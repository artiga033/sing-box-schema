# sing-box-schema

This repository contains JSON Schema files for [sing-box](https://github.com/SagerNet/sing-box).

This is an unofficial, community-maintained work. Check the [tracking issue in sing-box repo](https://github.com/SagerNet/sing-box/issues/425) if you are wishing for an officially provided schema.

## Disclaimer

Maintaining JSON schema for the complicated and rapidly evolving sing-box configuration is a challenging task. This repos is mostly LLM-generated with only human curation on obvious mistakes. It does not guarantee complete coverage or up-to-date status.

Schema from this repo is only for a more fluent editing experience in editors. They are **NOT** expected to be used for validation or other critical purposes which requires high accuracy.

## Quick Start

Each schema file corresponds to a sing-box config object.

As a start point, [`config.schema.json`](https://artiga033.github.io/sing-box-schema/config.schema.json) represents the full configuration file.

If you are writing your configuration in a split-file manner, you may check each seperate schema file to fit your need.

Specially, a [`rule-set-source.schema.json`](https://artiga033.github.io/sing-box-schema/rule-set/rule-set-source.schema.json) is specifically for writing [rule-set source files](https://sing-box.sagernet.org/configuration/rule-set/source-format/).
