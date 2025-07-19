---
layout: default
title: Main Config
nav_order: 3
---

# Configuring sk-burglary 3.3.7

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
    target = { name = "ox_target", enabled = false },         -- Change enabled to true if you're using ox_target, make sure you set qb-target enabled to false. https://github.com/overextended/ox_target
    lib = { name = "ox_lib", enabled = false },               -- Change enabled to true if you're using ox_lib. Set qb-menu enabled to false. https://github.com/overextended/ox_lib
    inventory = { name = "ox_inventory", enabled = false },   -- Change enabled to true if you're using ox_inventory. https://github.com/overextended/ox_inventory
    qsinventory = { name = "qs-inventory", enabled = false }, -- Change enabled to true if you're using quasar-inventory.
    mysql = { name = "oxmysql", enabled = false },            -- Change enabled to true if you're using oxmysql. https://github.com/overextended/oxmysql
    ui = { name = "ps-ui", enabled = false },                 -- https://github.com/Project-Sloth/ps-ui
    phone = { name = "lb-phone", enabled = false },           -- Change enabled to true if you're using lb-phone (3.1.6).
    yseries = { name = "yseries", enabled = false },          -- Change enabled to true if you're using yseries phone.
    menu = { name = "sk-menu", enabled = false },             -- Recommended | Set qb-menu enabled to false. https://github.com/mknzz/sk-menu
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

If level scaling is enabled, you can adjust the chance of getting a tier 2 or tier 3 house based on the current level. This can be done by setting the `Config.LevelScaling` variable.

```
Config.LevelScaling = true  -- Set to 'true' to enable, 'false' to disable
```

If level scaling is disabled, you can select which tier house if you meet the required level.

To add rep, you can utilize the client-side `AddRep` function as follows:

```
AddRep({ 100, 200 }) -- This will add a random amount of rep between 100 and 200
```

## Reputation per level

The amount of reputation required to reach each level can be configured using the `Config.RepPerLevel` table.

```
Config.RepPerLevel = {
    [1] = 5000,
    [2] = 10000,
    [3] = 17500,
    [4] = 27500,
    [5] = 40000,
    [6] = 55000,
    [7] = 75000,
    [8] = 100000,
    [9] = 130000,
    [10] = 170000
}
```

This table defines the reputation needed to complete each level. For example, 5,000 reputation is required for level 1 and 10,000 for level 2.

## Earning reputation

Reputation is earned by completing specific actions, with rewards varying based on the action and its difficulty.

The `AddRepForReason` function in `func.lua` is triggered by `main.lua` when players successfully perform actions like breaking in or bypassing security.

{: .note }
Reputation is also earned from daily tasks, which are loaded only when you check the list at the bossman.

```
-- Reputation rewards based on different actions and tiers
local repRewards = {
    ['breaking_in'] = { [1] = { rep = { 500, 1000 } }, [2] = { rep = { 1000, 1500 } }, [3] = { rep = { 1500, 2500 } }, [4] = { rep = { 2500, 3500 } } },
    ['bypass_security'] = { [1] = { rep = { 800, 1200 } }, [2] = { rep = { 1200, 1800 } }, [3] = { rep = { 1800, 2800 } }, [4] = { rep = { 2800, 4000 } } },
    ['complete_job'] = { [1] = { rep = { 1000, 1500 } }, [2] = { rep = { 1500, 2000 } }, [3] = { rep = { 2000, 3000 } }, [4] = { rep = { 3000, 4500 } } },
    ['crack_safe'] = { [1] = { rep = { 700, 1200 } }, [2] = { rep = { 1200, 1800 } }, [3] = { rep = { 1800, 2500 } }, [4] = { rep = { 2500, 3500 } } },
    ['loot_ped'] = { [1] = { rep = { 300, 700 } }, [2] = { rep = { 500, 1200 } }, [3] = { rep = { 800, 1800 } }, [4] = { rep = { 1500, 2500 } } },
    ['crack_door'] = { [1] = { rep = { 600, 1100 } }, [2] = { rep = { 1000, 1600 } }, [3] = { rep = { 1500, 2200 } }, [4] = { rep = { 2000, 3000 } } },
}

-- Function to add reputation based on the reason and tier
function AddRepForReason(reason, tier)
    -- Check if the reason exists in the repRewards table
    if repRewards[reason] then
        -- Check if the tier exists for that reason
        local tierData = repRewards[reason][tier]
        if tierData then
            local repToAdd = tierData.rep
            AddRep(repToAdd)
        end
    end
end
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
Config.ChanceOfItemRemoval = 0.5 -- 50% chance of removing the item if the break in is failed (also for cracking interior doors)
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
