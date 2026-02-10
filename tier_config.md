---
layout: default
title: Houses Config
nav_order: 4
---

# Configuring sk-burglary 3.6.0

## Finding the house config files

There are separate configuration files for each tier of houses. These files, `tier1.lua`, `tier2.lua`,`tier3.lua`, and `tier4.lua` are located in the `houses` subfolder. Each configuration file contains specific settings for the houses in its respective tier.

{: .note }
For the following docs, we’re using the `tier3.lua` house config file as an example. Configuration is exactly the same for `tier1.lua`, `tier2.lua` and `tier4.lua`.

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
> - **lockpick**: This option allows you to use the default `qb-lockpick` minigame. Ensure that this resource is present and **enabled** in the `Config.DefaultResources` table in the `config.lua` file.
>
> - **circle**: This option allows you to use either the `ps-ui` or `ox_lib` for the circle minigame. Ensure that one of these resources is present and **enabled** in the `Config.OptionalResources` table in the `config.lua` file.
>
> - **var**: Uses the `ps-ui` var minigame. Requires `ps-ui` to be present and **enabled** in `Config.OptionalResources`.
>
> - **thermite**: Uses the `ps-ui` thermite minigame. Requires `ps-ui` to be present and **enabled** in `Config.OptionalResources`.
>
> - **mhacking**: Mobile hacking minigame.
>
> - **scrambler**: Scrambler minigame from `ps-ui`.
>
> - **safecracker**: Uses `pd-safe`. Requires `pd-safe` to be present and **enabled** in `Config.OptionalResources`.

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

You can set the base chance of receiving a T3 house by changing the `Config.T3_HouseChance` variable.

```
Config.T3_HouseChance = 0.0 -- The value can range from 0.0 (no chance) to 1.0 (guaranteed chance).
``` 

You can set the scaling factor that boosts the chance of receiving a T3 house as the player levels up. For this to take effect, the `Config.LevelScaling` variable in `config.lua` must be set to **true**.

When level scaling is enabled, tier chances are calculated using a power curve formula:

`tierChance = baseChance + (scalingFactor * (level^1.5 / maxLevel^1.5))`

This means higher levels provide increasingly larger boosts to tier chances. The total of T2+T3+T4 chances is capped at 85%, reserving a minimum 15% for T1.

```
Config.T3_LevelScaling = 0.30 -- (0.0 to 1.0)
```

{: .note }
When `Config.RequireLevelForTier` is enabled in `config.lua`, tiers are also gated behind their `RequiredLevel`, a player below the required level will never receive that tier regardless of probability.

You can set the level required to request T3 house offers. When `Config.RequireLevelForTier` is enabled (default), this also gates the tier in random selection. When `Config.LevelScaling` is disabled, meeting the required level allows you to choose the tier directly.

```
Config.T3_RequiredLevel = 7 -- (1 - Config.MaxLevel)
```

## Changing required job request item

You can modify the items required to request a job from the bossman by editing the item names in the `Config.T3_RequiredItem` table

```
-- The required items for requesting a T3 house offer
Config.T3_RequiredItem = {
    require_all = true, -- Whether all required items are needed.
    "advancedlockpick",
    "housescrewdriver"
}
```

## Changing job cooldown settings

You can set the cooldown time for requesting a T3 house by changing the `Config.T3_JobCooldownSeconds` variable.

```
-- T3 Job cooldown time in seconds
Config.T3_JobCooldownSeconds = 240
```

To set the cooldown threshold (the number of jobs completed before the cooldown is applied), adjust the `Config.T3_JobCooldownThreshold` variable.

```
-- T3 Job cooldown threshold (number of jobs before cooldown applies)
Config.T3_JobCooldownThreshold = 3
```

## Changing rep loss amount

You can adjust the amount of reputation removed when a job is cancelled by modifying the `Config.T3_RepRemovalPercent` table.

```
-- The percentage of reputation removed when a T3 house is failed or cancelled.
Config.T3_RepRemovalPercent = { 8, 10 } -- 8% to 10% (min to max)
```