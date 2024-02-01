---
layout: default
title: Main Configuration
nav_order: 3
---

# Configuring sk-burglary 3.1

The main `config.lua` file can be located in the `shared` subfolder.

## Setting up default resources

If you have renamed your QB resources, you will need to adjust the `Config.DefaultResources` table accordingly. Ensure that the names in `Config.DefaultResources` match those on your server.

```
Config.DefaultResources = {
    -- The names here must correspond to the resource names on your server.
    [1] = { name = "qb-core", enabled = true },
    [2] = { name = "qb-target", enabled = true },
    [3] = { name = "qb-menu", enabled = true },
    [4] = { name = "qb-skillbar", enabled = true },
    [5] = { name = "qb-lockpick", enabled = true },
}
```

{: .note }
If you have not renamed your QB resources, you do not need to change the resource names shown in the block above.

## Setting up optional resources

```
Config.OptionalResources = {
    -- These names must match the resource names on your server.
    [1] = { name = "ox_target", enabled = false },
    [2] = { name = "ox_lib", enabled = false },
    [3] = { name = "ox_inventory", enabled = false },
    [4] = { name = "oxmysql", enabled = false },
    [5] = { name = "ps-ui", enabled = false },
    [6] = { name = "pd-safe", enabled = false }
}
```

> - **ox_lib**: When enabled, this will be utilized for the circle minigame, notifications, progress bar, and context menu. qb-menu must be disabled in Config.RequiredResources.
>
> - **ox_target**: When enabled, this will be utilized for targetting zones and entities. qb-target must be disabled in Config.RequiredResources.
>
> - **ox_inventory**: When enabled, this will be utilized instead of qb-inventory. No changes to Config.RequiredResources needed.
>
> - **oxmysql** - [Github](https://github.com/overextended/oxmysql)
>
> - **ps-ui** - [Github](https://github.com/Project-Sloth/ps-ui)
>
> - **pd-safe** - [Github](https://github.com/VHall1/pd-safe)

## Breaking in time

## Queue and finish time