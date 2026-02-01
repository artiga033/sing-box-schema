# Sing-Box JSON Schema

## Project Overview

This project translates sing-box configuration documentation (Markdown files in `sing-box/docs/configuration/`) into JSON Schema files (in `schema/`). Sing-box is a universal proxy platform.

## File Hierarchy Mapping

Schemas are stored in the `schema/` directory. sing-box is clone under the `sing-box/` directory with `git clone https://github.com/SagerNet/sing-box`, if it does not already exist. The sing-box documentation files are located in `sing-box/docs/configuration/`. The schema files mirror the documentation structure.

**Key mapping rules:**
- `index.md` files map to the parent directory name (e.g., `dns/index.md` → `dns.schema.json`). Unlike the markdown, they do not reside in the subdiectory, instead, they are a sibling to a directory with the same name.
- Nested documentation maps to nested schema directories (e.g.`dns/fakeip.md` -> `dns/fakeip.schema.json`, `dns/server/index.md` -> `dns/server.schema.json`)
- Reusable types go in `schema/types/`. They does not correspond to any specific documentation file. However, they are been used frequently in other schema files so extracting them out for better reusability and maintainability.

For LLM Coding Agents, you may `ls` or `tree` the folder structure of `schema/` and `sing-box/docs/configuration/` to understand the file hierarchy mapping, before starting any task.

## Schema Patterns

### Discriminated Unions (for type-based polymorphism)
Use `allOf` with base properties + `oneOf` for type-specific schemas:
```json
{
  "allOf": [
    { "properties": { "type": { "enum": ["local", "hosts", ...] } } },
    { "oneOf": [
      { "$ref": "./server/local.schema.json" },
      { "$ref": "./server/hosts.schema.json" }
    ]}
  ]
}
```

### Type-Specific Schemas
Constrain the `type` field with `const` and compose with shared schemas:
```json
{
  "allOf": [
    { "properties": { "type": { "const": "local" } } },
    { "$ref": "../../shared/dial.schema.json" }
  ]
}
```

### String-or-Array Type
Sing-box allows single strings where arrays are expected. Use `types/string-or-array.schema.json`:
```json
{ "$ref": "../types/string-or-array.schema.json" }
```

### References
Use relative paths for `$ref`: `"$ref": "./dns.schema.json"` or `"$ref": "../../shared/dial.schema.json"`

### Special Notes
sing-box evolves rapidly. Some fields may be deprecated, but with a new field of the same name with some preconditions. Like as of singbox 1.11.0, `route.rule.outbound` is deprecated in favor of `route.rule.action` with precondition `action: "route"`. In such cases, we need to use "if" - "then" to mark the deprecated field, so that it appears as the deprecated one when rule.action is not "route", while as the new one when rule.action is "route" or does not exist.

Example:
```
{
  "$comment":"rule.schema.json",
  "if": {
    "properties": {
      "action": {
        "not": { "const": "route" }
      }
    }
  },
  "then": {
    "properties": {
      "outbound": {
        "deprecated": true,
        "description": "Deprecated in sing-box 1.11.0. Moved to Rule Action."
      }
    }
  }
}
```

```
{
  "$comment":"rule_action.schema.json",
  "if": {
      "properties": {
          "action": {
              "type": "string"
          }
      },
      "required": [
          "action"
      ]
  },
  "then": {
      "properties": {
          "action": {
              "const": "route"
          }
      }
  }
}
```

## Markdown Conventions

When reading documentation to create schemas:
- `!!! quote ""` blocks: prefix content with `>` in descriptions
- `!!! question` blocks: prefix with 🆕 emoji
- Version markers (`!!! quote "Changes in sing-box X.Y.Z"`) indicate feature availability
- Deprecation notices (`!!! failure "Deprecated in sing-box X.Y.Z"`) should set `"deprecated": true`

