---
layout: default
title: Install
parent: sk-runs
nav_order: 1
---

# Installing sk-runs

## Required dependencies

- QBCore or QBox
- one supported inventory: `qb-inventory`, `ox_inventory`, `qs-inventory`, or `jaksam-inventory`
- one interaction stack: `qb-target`, `ox_target`, or `ox_lib`
- one menu/UI path: `ox_lib`, `qb-menu`, or `sk-menu`

Optional integrations:

- `ox_lib`
- `ox_target`
- `lb-phone`
- `yseries`

## Installation steps

1. Place the resource in your server resources folder.
2. Add the provided package item to your framework or inventory item list.
3. Add the item image to your inventory image folder.
4. Configure resources and job settings in `shared/config.lua`.
5. Add `ensure sk-runs` to `server.cfg`.

## Resource auto-detection

You can enable:

```lua
Config.AutoResourceSetup = true
```

If you prefer manual setup, configure the `Config.DefaultResources` and `Config.OptionalResources` tables so only one option per category is enabled.

## QBox setup

For QBox:

1. Enable `ox_lib`, `ox_target`, and `ox_inventory`.
2. Keep the QB bridge available.
3. Set:

```cfg
setr qbx:enableBridge "true"
```

## First smoke test

After restart, confirm:

- the resource starts without framework detection errors
- job request UI opens
- package items can be awarded and removed correctly
- target or interaction points appear at pickup and delivery phases