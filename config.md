---
layout: default
title: Main Config
nav_order: 3
---

# Configuring sk-burglary 3.2

## Finding the main config file

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

To enable an optional resource, set the `enabled` variable to `true` for the corresponding resource in the `Config.OptionalResources` table. 

## ox_target
- Enable `ox_target` in `Config.OptionalResources`:
[1] = {name = "ox_target", enabled = true}
- Disable `qb-target` in `Config.RequiredResources`.

## ox_lib
- Enable `ox_lib` in `Config.OptionalResources`:
[2] = {name = "ox_lib", enabled = true}
- Disable `qb-menu` in `Config.RequiredResources`.

## ox_inventory
- Enable `ox_inventory` in `Config.OptionalResources`:
[3] = {name = "ox_inventory", enabled = true}

## ps-ui
- Enable `ps-ui` in `Config.OptionalResources`:
[5] = {name = "ps-ui", enabled = true}
- Usage: `circle` and `scrambler` minigame. For more information, check out Break In Minigame.

## pd-safe
- Enable `pd-safe` in `Config.OptionalResources`:
[6] = {name = "pd-safe", enabled = true}
- Usage: `safescracker` minigame.

## Setting up reputation

{: .important }
When the server starts, the `boss_reputation` table will be automatically imported. Please ensure that `Config.Levels` is enabled and **oxmysql** is installed.

The leveling system can be enabled or disabled. This can be done by setting the Config.Levels variable.

```
Config.Levels = true  -- Set to 'true' to enable, 'false' to disable
```

You can also customize the maximum level that can be achieved in the leveling system by adjusting the Config.MaxLevel variable.

```
Config.MaxLevel = 10  -- Set to your desired maximum level
```

If level scaling is enabled, you can adjust the chance of getting a tier 2 or tier 3 house based on the current level. This can be done by setting the Config.LevelScaling variable.

```
Config.LevelScaling = true  -- Set to 'true' to enable, 'false' to disable
```

If level scaling is disabled, you can select which tier house if you meet the required level.

{: .note }
Rep is earned solely from daily tasks. In `main.lua`, search for `AddRep` to find commented lines showing previous ways to earn rep, such as breaking in and hacking.

To add rep, you can utilize the client-side `AddRep` function as follows:

```
AddRep({ 100, 200 }) -- This will add a random amount of rep between 100 and 200
```

## Break in time

Set the time period for robbing houses. You will not be able to start the break-in minigame if it is outside this time period.

You can set the earliest time at which a break-in can occur by adjusting the Config.MinTime variable. This value is based on a 24-hour clock.

```
Config.MinTime = 5  -- Set to your desired minimum time (in hours)
```

You can set the latest time at which a break-in can occur by adjusting the Config.MaxTime variable. This value is also based on a 24-hour clock.

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

```
Config.QueueTime = { 5, 25 }  -- Set to your desired min and max queue times
```

## Job expire time

Set the time limit for a player to complete a job. The time is measured in minutes.

You can set the duration of the job by adjusting the Config.JobTime variable.

```
Config.JobTime = 12  -- Set to your desired job time duration (in minutes)
```

If a player leaves the house after the job time has expired, the job will automatically end.

## Phone mail or notify

Players can receive job offers via email or direct notification, depending on the Config.SendMail variable.

```
Config.SendMail = true  -- 'true' for email, 'false' for direct notification
```

If it’s false, players will receive a direct notification and the job will start automatically.

The `SendMail` function in funcs.lua sends an email to the player’s QB phone. If you’re using a different phone system, you’ll need to modify this function.

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

    TriggerServerEvent('qb-phone:server:sendNewMail', {
        sender = sender,
        subject = subject,
        message = message,
        button = button
    })
end
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
