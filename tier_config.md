---
layout: default
title: Houses Config
nav_order: 4
---

# Configuring sk-burglary 3.3.3

## Finding the house config files

There are separate configuration files for each tier of houses. These files, `tier1.lua`, `tier2.lua`, and `tier3.lua`, are located in the `houses` subfolder. Each configuration file contains specific settings for the houses in its respective tier.

{: .note }
For the following docs, we’re using the `tier3.lua` house config file as an example. Configuration is exactly the same for `tier1.lua` and `tier2.lua`.

## Adding more houses to rob

To add more T3 houses to rob, navigate to the `houses` subfolder and open the `tier3.lua` file. Within the `Config.T3_Houses` table, copy and paste an existing house entry and adjust the values as needed.

```
-- Configuration for T3 houses
Config.T3_Houses = {
    [1] = { -- Increment this when adding another house
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

You can change the break in minigame by changing the `minigame` variable in the `Config.T3_BreakInMinigame` table.

```
-- Choose a minigame and adjust difficulty for breaking into T3 houses.
Config.T3_BreakInMinigame = {
    minigame = "lockpick", -- Minigame options: "circle", "square", "lockpick"

    -- Settings for optional circle minigames.
    circle = {
        amount = 5, -- Number of circles to complete
        speed = 6, -- Speed of the moving pointer
        custom = {
            size = 27, -- Size of the hit zone
            multi = 1.5 -- Multiplier for speed
        }
    },

    -- Settings for default square minigame.
    square = {
        time = 875,
        amount = 5
    },
}
```

There are three minigame options available:

> - **square**: This is the default QB skillcheck minigame.
>
> - **lockpick**: This option allows you to use the default `qb-lockpick` minigame. Ensure that this resource is present and **enabled** in the `Config.RequiredResources` table in the `config.lua` file.
>
> - **circle**: This option allows you to use either the `ps-ui` or `ox_lib` for the circle minigame. Ensure that one of these resources is present and **enabled** in the `Config.OptionalResources` table in the `config.lua` file.

## Setting up a new minigame

Here's how you can set up and configure a new minigame by adding it into the minigame handler.

{: .note }
This is all exactly the same process for each tier, also the same for changing other minigames `Config.T3_SecurityMinigame` and `Config.T3_SafeMinigame`. It's just these are in the tiered [interior] configs.

**1. Define the minigame in config:**

Add a new entry in `Config.T3_BreakInMinigame` for your new minigame in the [houses] `tier3.lua` file.

```
Config.T3_BreakInMinigame = {
    -- Set your new minigame for T3 break in here "new_minigame"
    minigame = "new_minigame", -- Minigame options: "circle", "square", "lockpick"

    -- Settings for your new minigame "new_minigame"
    new_minigame = {
        parameter1 = value1, -- Could be speed or time etc
        parameter2 = value2,
        -- Add more parameters as needed
    },

    -- Settings for optional circle minigames.
    circle = {
        amount = 5,
        speed = 6,
        custom = {
            size = 27,
            multi = 1.5
        }
    },

    -- Settings for default square minigame.
    square = {
        time = 875,
        amount = 5,
        difficulty = "hard"
    },
}
```

**2. Minigame handler integration:**

The minigame handler functions are located in the `client/funcs.lua` file. Here you'll modify or add to the `StartAMinigame` function to include your new minigame.

```
function StartAMinigame(minigameData, anim, successCallback)
    if anim then
        LoadAnimDict(anim[1])
        TaskPlayAnim(PlayerPedId(), anim[1], anim[2], 1.0, 1.0, -1, 63, 0.0, false, false, false)
    end

    local minigameHandlers = {
        lockpick = DefaultLockpick,
        square = DefaultSkillbar,
        mhacking = DefaultMobileHack,
        scrambler = Scrambler,
        circle = CircleSkillbar,
        safecracker = SafeCracker,

        -- Add your new handler here "new_minigame"
        new_minigame = NewMinigameHandler,
    }

    local minigameType = minigameData.minigame
    local minigameHandler = minigameHandlers[minigameType]

    if minigameHandler then
        if minigameType == 'lockpick' then
            minigameHandler(successCallback)
        else
            minigameHandler(minigameData, successCallback)
        end
    end
end
```

**3. Adding the new minigame function:**

In `client/funcs.lua`, define `NewMinigameHandler` to manage the logic of your new minigame.

```
function NewMinigameHandler(minigameData, successCallback)
    -- Your minigame settings from the config
    local parameter1 = minigameData.new_minigame.parameter1
    local parameter2 = minigameData.new_minigame.parameter2
    -- Example: Use an external library or resource to run the minigame
    local success = exports['new_minigame_resource']:Start(parameter1, parameter2)
    successCallback(success)
end
```

## Changing level requirements

You can set the chance of receiving a T3 house by changing the `Config.T3_HouseChance` variable.

```
Config.T3_HouseChance = 0.17 -- The value can range from 0.0 (no chance) to 1.0 (guaranteed chance).
``` 

You can set the scaling factor that boosts the chance of receiving a T3 house. For this to take effect, the `Config.LevelScaling` variable in `config.lua` must be set to **true**.

```
Config.T3_LevelScaling = 0.027 -- (0.0 to 1.0)
```

You can set the level required to request T3 house offers. If you meet the required level, you will also be able to choose the tier of the house. For this to take effect, the `Config.LevelScaling` variable in `config.lua` must be set to **false**.

```
Config.T3_RequiredLevel = 7 -- (1 - Config.MaxLevel)
```
