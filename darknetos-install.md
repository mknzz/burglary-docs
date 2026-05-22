---
layout: default
title: Install
parent: darknetos
nav_order: 1
---

# Installing darknetos

## Requirements

Required runtime dependencies:

- QBCore, QBox, or ESX
- `oxmysql`
- `ox_lib`
- One supported inventory: `ox_inventory`, `qb-inventory`, or `qs-inventory`

Optional integrations and auto-detected resources:

- `ox_target`
- `qb-target`
- dispatch resources such as `ps-dispatch`, `cd_dispatch`, `core_dispatch`, `qs-dispatch`, `rcore_dispatch`, `linden_outlawalert`, and `origen_police`

## Resource placement

Place the resource in your server resources folder:

```text
resources/[skryptz]/darknetos/
```

## Database

Import the included SQL:

```sql
source sql/tailsrot_mvp.sql
```

The script creates these tables:

- `tailsrot_players`
- `tailsrot_listings`
- `tailsrot_orders`
- `tailsrot_ratings`
- `tailsrot_messages`
- `tailsrot_market_state`

## Start order

darknetos should start after your framework and database resources.

```cfg
ensure qb-core
ensure oxmysql
ensure ox_lib
ensure qb-inventory
# ...
ensure darknetos
```

If you are using QBox, keep the QB bridge available and enable:

```cfg
setr qbx:enableBridge "true"
```

## Inventory notes

- `ox_inventory` unlocks stash-based dead-drop deposit and pickup when `Config.Delivery.useInventoryStash = true`
- non-ox inventory setups automatically fall back to direct item transfer
- the resource auto-detects inventory support through `shared/framework.lua`

## Laptop item

The resource includes `laptopitem.lua` and `server/sv_item.lua` support for a usable laptop item named `skryptzos_laptop`. Make sure the item exists in your framework or inventory item list if you intend to open the UI through the item instead of only via the command.

## First smoke test

After restart:

1. Run `/erebus` or use the configured laptop item.
2. Confirm the boot sequence opens the desktop.
3. Open Network Monitor, Erebus Market, and Terminal.
4. Watch the server console for the framework + inventory detection log from `shared/framework.lua`.