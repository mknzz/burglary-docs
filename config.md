---
layout: default
title: Main Config
nav_order: 3
---

# Configuring sk-burglary 3.6.0

## Finding the main config file

The main `config.lua` file can be located in the `shared` subfolder.

## Setting up default resources

If you have renamed your QB resources, you will need to adjust the `Config.DefaultResources` table accordingly. Ensure that the names in `Config.DefaultResources` match those on your server.

```
Config.DefaultResources = {
    -- These names must match the resource names on your server.
    core = { name = "qb-core", enabled = true },     -- Keep enabled for both QB and QBox. QBox's qbx_core provides 'qb-core' in its fxmanifest.lua.
    target = { name = "qb-target", enabled = true }, -- Change enabled to false if you're using another target resource.
    menu = { name = "qb-menu", enabled = true },     -- Change enabled to false if you're using another menu resource.
    skill = { name = "qb-skillbar", enabled = false },
    lock = { name = "qb-lockpick", enabled = false },
    minigames = { name = "qb-minigames", enabled = true },
    inventory = { name = "qb-inventory", enabled = true },
}
```

{: .note }
If you have not renamed your QB resources, you do not need to change the resource names shown in the block above.

## Setting up optional resources

```
Config.OptionalResources = {
    -- These names must match the resource names on your server.
    target = { name = "ox_target", enabled = false },                     -- Change enabled to true if you're using ox_target, make sure you set qb-target enabled to false. https://github.com/overextended/ox_target
    lib = { name = "ox_lib", enabled = false },                            -- Change enabled to true if you're using ox_lib. Set qb-menu enabled to false. https://github.com/overextended/ox_lib
    inventory = { name = "ox_inventory", enabled = false },               -- Change enabled to true if you're using ox_inventory. https://github.com/overextended/ox_inventory
    qsinventory = { name = "qs-inventory", enabled = false },             -- Change enabled to true if you're using quasar-inventory.
    jaksaminventory = { name = "jaksam-inventory", enabled = false },     -- Change enabled to true if you're using jaksam-inventory.
    mysql = { name = "oxmysql", enabled = false },                        -- Change enabled to true if you're using oxmysql. https://github.com/overextended/oxmysql
    ui = { name = "ps-ui", enabled = false },                             -- https://github.com/Project-Sloth/ps-ui
    phone = { name = "lb-phone", enabled = false, fix = false },          -- Change enabled to true if you're using lb-phone (3.1.6). Set fix to true if job accepting fails.
    yseries = { name = "yseries", enabled = false },                      -- Change enabled to true if you're using yseries phone.
    menu = { name = "sk-menu", enabled = false },                         -- Recommended | Set qb-menu enabled to false. https://github.com/mknzz/sk-menu
    safe = { name = "pd-safe", enabled = false },
}
```

To enable an optional resource, set the `enabled` variable to `true` for the corresponding resource in the `Config.OptionalResources` table. 

## ox_target
- Enable `ox_target` in `Config.OptionalResources`:
target = {name = "ox_target", enabled = true}
- Disable `qb-target` in `Config.RequiredResources`.

## ox_lib
- Enable `ox_lib` in `Config.OptionalResources`:
lib = {name = "ox_lib", enabled = true}
- Disable `qb-menu` in `Config.RequiredResources`.

## ox_inventory
- Enable `ox_inventory` in `Config.OptionalResources`:
inventory = {name = "ox_inventory", enabled = true}

## ps-ui
- Enable `ps-ui` in `Config.OptionalResources`:
ui = {name = "ps-ui", enabled = true}
- Usage: `circle` and `scrambler` minigame. For more information, check out Break In Minigame.

## pd-safe
- Enable `pd-safe` in `Config.OptionalResources`:
safe = {name = "pd-safe", enabled = true}
- Usage: `safescracker` minigame.

## Optional easy resource setup

If you'd prefer not to manually configure enabled resources in both tables or set up minigames, you can simply enable `Config.AutoResourceSetup` in the main `config.lua`. 

This option will automatically detect and enable the required/optional resources if they are running. Additionally, minigames will be automatically detected based on their availability.

## Setting up optional sound

You can optionally enable xsound by setting `Config.OptionalSound` to true. Currently, it is used to play the outdoor alarm during the T4 heist.

```
Config.OptionalSound = true
Config.OptionalSoundResource = "xsound"
```

## Setting up reputation

{: .important }
When the server starts, the `boss_reputation` table will be automatically imported. Please ensure that `Config.Levels` is enabled and **oxmysql** is installed.

The leveling system can be enabled or disabled. This can be done by setting the `Config.Levels` variable.

```
Config.Levels = true  -- Set to 'true' to enable, 'false' to disable
```

You can also customize the maximum level that can be achieved in the leveling system by adjusting the `Config.MaxLevel` variable.

```
Config.MaxLevel = 10  -- Set to your desired maximum level
```

If level scaling is enabled, the chance of getting a tier 2, tier 3, or tier 4 house increases based on the player's current level. This can be toggled with the `Config.LevelScaling` variable.

```
Config.LevelScaling = true  -- Set to 'true' to enable, 'false' to disable
```

## Maximum tier chance

When level scaling is enabled, the combined probability of T2+T3+T4 is capped by `Config.MaxTierChance`. The remaining chance is reserved for T1. For example, 0.85 means T1 will always have at least a 15% chance.

```
Config.MaxTierChance = 0.85  -- 0.0 to 1.0, default 0.85 (T1 gets at least 15%)
```

## Require level for tier

When `Config.RequireLevelForTier` is enabled, players must meet the tier's `RequiredLevel` before that tier can appear in random job selection — even when level scaling is active. This prevents low-level players from being randomly assigned high-tier houses they aren't ready for.

```
Config.RequireLevelForTier = true  -- Set to 'true' to gate tiers behind RequiredLevel, 'false' for pure probability
```

For example, with `RequireLevelForTier = true` and default `RequiredLevel` values:
- Level 1-3 players can only receive T1 houses
- Level 4+ players can receive T1 and T2 houses (`T2_RequiredLevel = 4`)
- Level 7+ players can receive T1, T2, and T3 houses (`T3_RequiredLevel = 7`)
- Level 9+ players can receive all tiers (`T4_RequiredLevel = 9`)

The `RequiredLevel` for each tier is set in the corresponding `houses/tierX.lua` file (e.g., `Config.T4_RequiredLevel = 9`).

If level scaling is disabled, you can select which tier house if you meet the required level.

## Door hint distance

You can configure the distance at which the "It's gotta be around here somewhere" hint appears for locked interior doors:

```
Config.DoorHintDistance = 7.5  -- Distance in game units
```

## Reputation per level

The amount of reputation required to reach each level can be configured using the `Config.RepPerLevel` table. These thresholds use an accelerating progression curve for balance.

```
Config.RepPerLevel = {
    [1] = 2000,       -- Level 1-2 (~6 T1 runs)
    [2] = 3500,       -- Level 2-3 (~8 runs)
    [3] = 6000,       -- Level 3-4 (~11 runs)
    [4] = 9000,       -- Level 4-5 (~12 runs)
    [5] = 13000,      -- Level 5-6 (~12 runs)
    [6] = 18000,      -- Level 6-7 (~13 runs)
    [7] = 25000,      -- Level 7-8 (~16 runs)
    [8] = 33000,      -- Level 8-9 (~20 runs)
    [9] = 42000,      -- Level 9-10 (~25 runs)
    [10] = 55000      -- Level 10-Max (display only)
}
```

This table defines the reputation gap needed to complete each level. For example, 2,000 reputation is required to go from level 1 to 2, and 3,500 to go from level 2 to 3. The cumulative total to max level is ~206,500 rep (~100-120 runs).

## Earning reputation

Reputation is earned by completing specific actions, with rewards varying based on the action and its difficulty. All reputation modifications are validated and applied server-side to prevent exploitation.

The `AddRepForReason` function in `funcs.lua` is triggered by `main.lua` when players successfully perform actions like breaking in or bypassing security. Reputation is distributed to all group members inside the house.

{: .note }
Reputation is also earned from daily tasks, which are loaded only when you check the list at the bossman.

## Reputation limits (Config.RepLimits)

Reputation reward ranges are now configured in the main `config.lua` via `Config.RepLimits`. These values are validated server-side — the server will reject any rep amounts outside the defined ranges.

```
Config.RepLimits = {
    breaking_in = {
        [1] = { 50, 100 },
        [2] = { 100, 150 },
        [3] = { 150, 250 },
        [4] = { 250, 350 }
    },
    bypass_security = {
        [1] = { 80, 120 },
        [2] = { 120, 180 },
        [3] = { 180, 280 },
        [4] = { 280, 400 }
    },
    complete_job = {
        [1] = { 100, 150 },
        [2] = { 150, 200 },
        [3] = { 200, 300 },
        [4] = { 300, 450 }
    },
    crack_safe = {
        [1] = { 70, 120 },
        [2] = { 120, 180 },
        [3] = { 180, 250 },
        [4] = { 250, 350 }
    },
    loot_ped = {
        [1] = { 30, 70 },
        [2] = { 50, 120 },
        [3] = { 80, 180 },
        [4] = { 150, 250 }
    },
    crack_door = {
        [1] = { 60, 110 },
        [2] = { 100, 160 },
        [3] = { 150, 220 },
        [4] = { 200, 300 }
    },
    stealth_complete = {
        [1] = { 80, 120 },
        [2] = { 120, 180 },
        [3] = { 180, 250 },
        [4] = { 250, 350 }
    },
    search_zone = {
        [1] = { 15, 30 },
        [2] = { 30, 50 },
        [3] = { 50, 80 },
        [4] = { 80, 120 }
    },
    pickup_item = {
        [1] = { 10, 25 },
        [2] = { 25, 45 },
        [3] = { 45, 70 },
        [4] = { 70, 100 }
    },
}
```

Each action type maps tier numbers (1-4) to a `{min, max}` rep range. A random value within that range is awarded when the action is completed.

You can also set the maximum allowed custom rep range for task rewards:

```
Config.MaxCustomRepAllowed = 2000
```

## Reputation penalties (Config.RepPenalties)

Reputation penalties are applied when the player fails specific actions during a burglary. Each penalty is defined as a `{min%, max%}` range per tier. A random percentage within the range is deducted from the player's current rep.

Set both values to `0` to disable a penalty for that tier. All penalties require `Config.Levels = true`.

```
Config.RepPenalties = {
    breakin_failed = {           -- Failed the front door break-in minigame
        [1] = { 1, 2 },
        [2] = { 1, 2 },
        [3] = { 1, 2 },
        [4] = { 1, 2 },
    },
    alarm_triggered = {          -- Interior security alarm triggered (noise, gunshot, anti-tamper)
        [1] = { 2, 4 },         -- Only fires once per burglary (first detection)
        [2] = { 2, 4 },
        [3] = { 2, 3 },
        [4] = { 1, 3 },
    },
    safe_failed = {              -- Failed the safe cracking minigame (not key unlock)
        [1] = { 1, 3 },
        [2] = { 1, 2 },
        [3] = { 1, 2 },
        [4] = { 1, 2 },
    },
    player_downed = {            -- Player killed inside the house
        [1] = { 3, 5 },
        [2] = { 3, 5 },
        [3] = { 3, 5 },
        [4] = { 3, 5 },
    },
    lockdown_triggered = {       -- T4 CCTV lockdown activated
        [1] = { 0, 0 },         -- N/A for T1-T3
        [2] = { 0, 0 },
        [3] = { 0, 0 },
        [4] = { 4, 6 },
    },
}
```

The server handler checks `Config.RepPenalties[reason][tier]` when a reason is provided. If no reason is given (e.g. job cancellation via `EndBurglary`), it falls back to `T*_RepRemovalPercent` from the tier config files.

## Reputation notifications

You can enable or disable notifications when reputation is gained:

```
Config.NotifyRep = true  -- Set to 'false' to hide rep gain notifications
```

## Break in time

Set the time period for robbing houses. You will not be able to start the break-in minigame if it is outside this time period.

You can set the earliest time at which a break-in can occur by adjusting the `Config.MinTime` variable. This value is based on a 24-hour clock.

```
Config.MinTime = 5  -- Set to your desired minimum time (in hours)
```

You can set the latest time at which a break-in can occur by adjusting the `Config.MaxTime` variable. This value is also based on a 24-hour clock.

```
Config.MaxTime = 23  -- Set to your desired maximum time (in hours)
```

In this example, the maximum time is set to 23, which means that if the time is 11PM or later, you can initiate a break-in.

To allow house robbing at any time, modify the `NotWithinBreakInHours` function in funcs.lua as follows:

```
function NotWithinBreakInHours()
    return false  -- Disables the time check, allowing house robbing at any time
end
```

## Queue time

The time is measured in minutes and the actual queue time is randomly determined based on the minimum and maximum times you set.

{: .note }
Moved queue time to tiered [houses] configurations in update 3.3, allowing different times per tier.

```
Config.QueueTime = { 5, 25 }  -- Set to your desired min and max queue times
```

## Job expire time

Set the time limit for a player to complete a job. The time is measured in minutes.

{: .note }
Moved job time to tiered [houses] configurations in update 3.3, allowing different times per tier.

```
Config.JobTime = 12  -- Set to your desired job time duration (in minutes)
```

If a player leaves the house after the job time has expired, the job will automatically end.

## Break in item

You can enable or disable the removal of break in items when the minigame is failed by setting the `Config.RemoveBreakinItem` variable

To adjust the chance of item removal on failure, modify the `Config.ChanceOfItemRemoval` value.

```
Config.ChanceOfItemRemoval = 0.9 -- 90% chance of removing the item if the break in is failed (also for cracking interior doors)
```

## Job cooldown

You can enable or disable global cooldown settings using the `Config.JobCooldown` table. This includes the PerPlayer, Global, and CooldownSeconds options.

To adjust cooldown times and thresholds for each tier, edit the corresponding values in the tiered [houses] configurations. For more information, visit the [cooldown settings section of the documentation](https://mknzz.github.io/burglary-docs/tier_config.html#changing-job-cooldown-settings).

## Phone mail or notify

Players can receive job offers via email or direct notification, depending on the `Config.SendMail` variable.

```
Config.SendMail = true  -- 'true' for email, 'false' for direct notification
```

If it’s false, players will receive a direct notification and the job will start automatically.

The `SendMail` function in `funcs.lua` sends an email to the player’s QB phone. If you’re using a different phone, you’ll need to modify this function, you can also enable `lb-phone` in `Config.OptionalResources`.

```
-- Function to send QB phone mail
function SendMail(subject, sender, message, buttonEvent, tier)
    local button = nil
    if buttonEvent and tier then
        button = {
            enabled = true,
            buttonEvent = buttonEvent,
            buttonData = tier
        }
    end

    -- For qs-smartphone, change 'qb-phone' in the event name below to 'qs-smartphone'
    TriggerServerEvent('qb-phone:server:sendNewMail', {
        sender = sender,
        subject = subject,
        message = message,
        button = button
    })
end
```

## Required job request item

You can control whether an item is required to request a job based on the job's tier by setting:

`Config.RequireRequestItem` — Enable or disable the requirement for an item when requesting a job.

`Config.RemoveRequestedItem` — Enable or disable removing the item after a successful job request.

To configure which items are required for each tier, edit the relevant settings in the tiered [houses] configurations. For more information, visit the [tiered request item settings section of the documentation](mknzz.github.io/burglary-docs/tier_config.html#changing-required-job-request-item).

## Daily tasks

Daily tasks can be enabled or disabled, and the number of tasks assigned can be configured:

```
Config.DailyTasks = true   -- Enable or disable daily tasks
Config.MinTasks = 5        -- Minimum number of tasks assigned
Config.MaxTasks = 8        -- Maximum number of tasks assigned
```

{: .important }
When the server starts, the `boss_task` table will be automatically imported if `Config.DailyTasks` is true. Requires **oxmysql**.

You can configure the available tasks in `Config.TasksList`. Each task has the following fields:

| Field | Description |
|---|---|
| `id` | Unique task identifier |
| `task` | Display name |
| `desc` | Description (supports `%s` for tier/item placeholders) |
| `icon` | Font Awesome icon class |
| `tier` | Single tier number or `{min, max}` range |
| `items` | Item(s) required or involved (string or table) |
| `weapon` | Weapon(s) involved (for combat tasks) |
| `rewards` | `{ rep = {min, max}, items = {"item1", "item2"} }` |
| `hide_items` | Whether to hide reward items in the task description |

## Carry system

The carry system automatically attaches props to players when carry items are in their inventory.

```
Config.AlwaysCarry = true    -- Enable constant inventory checks for carrying props
Config.DisableCarry = false  -- Completely disable carrying props
```

Carry props are configured in `Config.CarryProps`. Each entry defines the model, bone attachment, position, rotation, and whether the item is a tool:

```
Config.CarryProps = {
    ["bigtv"] = {
        model = "prop_tv_flat_02b",
        bone = 60309,
        position = vector3(0.075, 0.19, 0.295),
        rotation = vector3(223.0, 110.0, 0.0)
    },
    ["housescrewdriver"] = {
        model = "prop_tool_screwdvr02",
        bone = 6286,
        position = vector3(0.07, 0.07, -0.003),
        rotation = vector3(235.0, 72.1, 180.0),
        tool = true  -- Tool items use a different animation and don't restrict movement
    },
    -- You can add custom animations per item:
    ["example"] = {
        model = "prop_example",
        bone = 60309,
        position = vector3(0.0, 0.0, 0.0),
        rotation = vector3(0.0, 0.0, 0.0),
        animDict = "custom_anim_dict",  -- Optional: custom animation dictionary
        anim = "custom_anim"            -- Optional: custom animation name
    },
}
```

Items with `tool = true` use the jerrycan carry animation by default and don't apply movement restrictions. Regular items use the box carry animation and slow the player down.

## Item name mapping (ox_inventory)

When using `ox_inventory`, QB item names need to be mapped to their ox equivalents for prop pickups and search zones. Configure this via `Config.ReplaceQbItemNames`:

```
Config.ReplaceQbItemNames = {
    -- Attachments
    ["smallscope_attachment"] = "at_scope_small",
    ["medscope_attachment"] = "at_scope_medium",
    ["comp_attachment"] = "at_compensator",
    ["suppressor_attachment"] = "at_suppressor_light",
    ["grip_attachment"] = "at_grip",
    ["drum_attachment"] = "at_clip_drum_rifle",
    ["weapontint_3"] = "at_skin_camo",
    ["weapontint_7"] = "at_skin_metal",
    ["weapontint_2"] = "at_skin_luxe",

    -- Weapons
    ["weapon_combatmg"] = "WEAPON_COMBATMG",
    ["weapon_nightstick"] = "WEAPON_NIGHTSTICK",
    ["weapon_battleaxe"] = "WEAPON_BATTLEAXE",
    -- Add more mappings as needed for your server's item names

    -- Ammo
    ["pistol_ammo"] = "ammo-9",
    ["rifle_ammo"] = "ammo-rifle",
    ["shotgun_ammo"] = "ammo-shotgun",
}
```

{: .note }
This mapping is used during prop pickups (`pickupAProp`), search zones (`searchAZone`), and ped looting. If a QB item name exists as a key in this table, it will be converted to the corresponding ox name before adding to the player's inventory.

## Untargetable models (ox_target)

Some pickup prop models cannot be targeted by `ox_target` using standard entity zones. These models are configured to use sphere zones instead:

```
Config.OxUntargetableModels = {
    'w_ar_carbinerifle',
    'w_sb_microsmg',
    'w_ar_assaultrifle',
    -- Add more model names as needed
}
```

If you add new pickup props to interiors and find they can't be targeted with `ox_target`, add the model name to this list.

## Inventory weight

When using `qb-inventory` or `qs-inventory`, set the maximum inventory weight to match your inventory config:

```
Config.MaxInventoryWeight = 120000  -- Should match the max weight from your inventory config
```

{: .note }
Not needed when using `ox_inventory` — weight checks are handled automatically via its exports.

## Sellman configuration

You can fully disable the sell peds:

```
Config.DisableSellman = false  -- Set to true to disable all sellman peds
```

## Misc options

```
Config.DrawText = false        -- Enable or disable drawing text for certain actions
Config.TeleportOnDeath = true  -- Teleport to exterior door when health reaches 0
Config.Logs = false            -- Enable or disable webhook logging via qb-log
Config.PrintStuff = false      -- Enable or disable debug prints
```

## Dispatch alerts

Please navigate to the `funcs.lua` file and find the `RobberyAlert(coords)` function. We're utilizing `ps-dispatch` to send alerts to the police.

```
-- Send a dispatch alert using ps-dispatch
function RobberyAlert(coords)
    local street1, street2 = GetStreetNameAtCoord(coords.x, coords.y, coords.z, Citizen.ResultAsInteger(),
        Citizen.ResultAsInteger())
    local street1name = GetStreetNameFromHashKey(street1)
    local street2name = GetStreetNameFromHashKey(street2)
    local street = street1name..street2name
    local dispatchData = {
        message = 'Suspicious Individual',
        dispatchCode = '10-90',
        description = 'Possible B&E in progress, ' .. street,
        radius = 0,
        sprite = 40,
        color = 2,
        scale = 1.0,
        length = 3,
        coords = coords -- Houserobbery door coords
    }

    exports['ps-dispatch']:CustomAlert(dispatchData)
end
```

## Dispatch blips

If the dispatch blips for houserobberies are not appearing at the correct location, it’s likely due to the use of player coords when sending a dispatch alert.

To fix this issue, you should use the coords provided by the `RobberyAlert(coords)` function.

{: .note }
Alternatively, you can use the following export to get the correct house coords.
`exports['sk-burglary']:GetCurrentDoorCoords()` -- If no door coords are available, player coords will be returned

## Dispatch shooting blips

If the dispatch blips for shooting or fighting are not appearing at the correct location, it’s likely due to the use of player coords when sending a dispatch alert.

To fix the `Shooting()` and `Fight()` functions in `ps-dispatch`, please navigate to the `alerts.lua` file in the `client` folder, you will need to make the following changes.

Replace the entire `Shooting()` function (line 26) with this:

```
local function Shooting()
    local coords = GetEntityCoords(cache.ped)
    local houseCoords = exports['sk-burglary']:GetCurrentDoorCoords() -- Houserobbery door coords
    local dispatchData = {
        message = locale('shooting'),
        codeName = 'shooting',
        code = '10-11',
        icon = 'fas fa-gun',
        priority = 2,
        coords = houseCoords or coords, -- Houserobbery door coords
        street = GetStreetAndZone(coords),
        gender = GetPlayerGender(),
        weapon = GetWeaponName(),
        jobs = { 'leo' }
    }

    TriggerServerEvent('ps-dispatch:server:notify', dispatchData)
end
```

Same again, replace the entire `Fight()` function (line 117) with this:

```
local function Fight()
    local coords = GetEntityCoords(cache.ped)
    local houseCoords = exports['sk-burglary']:GetCurrentDoorCoords() -- Houserobbery door coords
    local dispatchData = {
        message = locale('melee'),
        codeName = 'fight',
        code = '10-10',
        icon = 'fas fa-hand-fist',
        priority = 2,
        coords = houseCoords or coords, -- Houserobbery door coords
        gender = GetPlayerGender(),
        street = GetStreetAndZone(coords),
        jobs = { 'leo' }
    }

    TriggerServerEvent('ps-dispatch:server:notify', dispatchData)
end
```