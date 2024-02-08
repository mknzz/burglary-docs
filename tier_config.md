---
layout: default
title: Houses Config
nav_order: 4
---

# Configuring sk-burglary 3.1

## Finding the house config files

There are separate configuration files for each tier of houses. These files, `tier1.lua`, `tier2.lua`, and `tier3.lua`, are located in the `houses` subfolder. Each configuration file contains specific settings for the houses in its respective tier, including **door coords**, **lock status**, **interior ID**, **active robbery status**, and **required items** for breaking in.

{: .note }
For the following docs, we’re using the `tier3.lua` house config file as an example.

## Adding more houses to rob

To add more Tier 3 (T3) houses to rob, navigate to the `houses` subfolder and open the `tier3.lua` file. Within the `Config.T3_Houses` table, copy and paste an existing house entry and adjust the values as needed.

```
-- Configuration for T3 houses
Config.T3_Houses = {
    [1] = { -- Make sure to increment this
        -- The door coordinates for the house.
        door_coords = vector3(-36.56, -570.73, 38.83),

        -- The heading or direction the door is facing.
        heading = 138.31,

        -- Whether the house is already unlocked.       
        unlocked = false,

        -- Set the interior for the house, if this is not set a random one will be chosen.
        interior = 1,

        -- Whether the house is currently being robbed by a player.
        busy = false,

        -- The required items for accessing this house.
        required_item = { require_all = true, -- Whether all required items are needed.
            "advancedlockpick",
            "housescrewdriver"
        }
    },
}
```

## Setting up break in minigame

## Changing level requirements