---
layout: default
title: Troubleshooting
nav_order: 5
---

# Troubleshooting sk-burglary 3.6.1

Please make sure you have followed [Installing](https://mknzz.github.io/burglary-docs/install.html) and [Main Config](https://mknzz.github.io/burglary-docs/config.html).

## The break in minigame is not working

If you're attempting to break into a door and nothing happens, follow these steps:

> - **Check the client console**: An error message should appear, indicating the missing minigame. However, in some cases, no error may be printed.
>
> - **Check the minigame option**: Ensure that the minigame variable in `Config.T3_BreakInMinigame` (for the tier where the issue occurred) is set to a valid option. The valid options are: 
>   - "circle"
>   - "square"
>   - "lockpick"
>
> - **Check the required minigame resources**: 
>   - For the "lockpick" or "circle" minigames, ensure the necessary resources are enabled in your `config.lua`:
>     - `qb-lockpick` or `qb-minigames` (for "lockpick")
>     - `ps-ui` or `ox_lib` (for "circle")
>

For more break in minigame information, please visit the [Houses Config](https://mknzz.github.io/burglary-docs/tier_config.html) section of our docs.

---

## Items not found / missing items

If you see errors in your server console like:

```
[burglary-opensrc] ^1Item not found in OX items list: twerks_candy
[burglary-opensrc] ^1Item not found in OX items list: snikkel_candy
```

This means the item exists in the burglary config but does not exist in your inventory system's item list. This is most common when using `ox_inventory`, as its default item list does not include all QBCore item names.

> **Add the items to your inventory**
>
> Add the missing items directly to your `ox_inventory` items list. Refer to the provided `items_ox.lua` file in the burglary resource root for all burglary related item definitions.
>
> **Or Map QBCore item names to OX item names**
>
> Use `Config.ReplaceQbItemNames` in `shared/config.lua` to map QBCore item names to their `ox_inventory` equivalents. When the script tries to give or check for an item, it will automatically convert the name before interacting with `ox_inventory`.
>
> ```lua
> Config.ReplaceQbItemNames = {
>     ["twerks_candy"] = "candy",           -- example mapping
>     ["snikkel_candy"] = "chocolate",      -- example mapping
>     ["rolex"] = "goldwatch",
> }
> ```
>
> Only items that have a different name in `ox_inventory` need to be added here. Items with identical names in both systems do not need a mapping.

**Where items are configured:**

Items appear across multiple config files. If an item is missing, check these locations:

- `shared/config.lua` — `Config.CarryProps`, `Config.BuyersList`, general item references
- `houses/tier1.lua` — `Config.T1_Houses` → `required_item` (break-in tools)
- `houses/tier2.lua` — `Config.T2_Houses` → `required_item`, `Config.T2_RequiredItem`
- `houses/tier3.lua` — `Config.T3_Houses` → `required_item`
- `houses/tier4.lua` — `Config.T4_Houses` → `required_item`, guard loot, setup items
- `interiors/tier1.lua` — `Config.T1_Interiors` → `pickup_props`, `search_zones`, `safes`
- `interiors/tier2.lua` — `Config.T2_Interiors` → `pickup_props`, `search_zones`, `locked_doors`, `safes`
- `interiors/tier3.lua` — `Config.T3_Interiors` → `pickup_props`, `search_zones`, `locked_doors`, `safes`
- `interiors/tier4.lua` — `Config.T4_Interiors` → `pickup_props`, `search_zones`, `safes`

You can replace any item name in these configs with an item that exists in your inventory system.

---