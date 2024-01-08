---
layout: default
title: Main config
nav_order: 3
---

# Configuring sk-burglary (3.1)

Open the main config file (config.lua) and make sure the required resource names are the same as the resources on your server

```
-- If you have renamed your QBCore object replace "QBCore" with the name of your core
Config.CoreObjectName = "QBCore"

-- exports[Config.CoreResourceName]:GetCoreObject()
Config.CoreResourceName = "qb-core"

-- exports[TargetResourceName]:AddZone
Config.TargetResourceName = "qb-target"

-- exports[Config.MenuResourceName]:openMenu
Config.MenuResourceName = "qb-menu"

-- TriggerEvent(Config.LockpickResourceName..':client:openLockpick')
Config.LockpickResourceName = "qb-lockpick"
```

{: .note }
If you have not renamed your server resources, you do not need to change the resource names shown in the code block above.

## Changing the minigame

You can change the minigame for breaking in, disabling the security and cracking safes in the main config.lua.

```
-- Set the skill check minigame for lockpicking a door
Config.DoorSkillcheck = "lockpick"

-- Set the skill check minigame for disabling the security
Config.SecuritySkillcheck = "square"

-- Set the skill check minigame for cracking the safe
Config.SafeSkillcheck = "square"
```

### Minigames for breaking in
> - [qb-lock](https://github.com/Nathan-FiveM/qb-lock) ```Config.DoorSkillcheck = "circle"```
>
> - [ps-ui](https://github.com/Project-Sloth/ps-ui) ```Config.DoorSkillcheck = "ps-circle"```
>
> - [qb-skillbar](https://github.com/qbcore-framework/qb-skillbar) ```Config.DoorSkillcheck = "square"```
>
> - [qb-lockpick](https://github.com/qbcore-framework/qb-lockpick) ```Config.DoorSkillcheck = "lockpick"``` 

### Minigames for disabling the security
> - [qb-lock](https://github.com/Nathan-FiveM/qb-lock) ```Config.SecuritySkillcheck = "circle"```
>
> - [ps-ui](https://github.com/Project-Sloth/ps-ui) ```Config.SecuritySkillcheck = "ps-circle"```
>
> - [ps-ui](https://github.com/Project-Sloth/ps-ui) ```Config.SecuritySkillcheck = "ps-scrambler"```
>
> - [qb-skillbar](https://github.com/qbcore-framework/qb-skillbar) ```Config.SecuritySkillcheck = "square"```

### Minigames for cracking the safe
> - [qb-lock](https://github.com/Nathan-FiveM/qb-lock) ```Config.SafeSkillcheck = "circle"```
>
> - [ps-ui](https://github.com/Project-Sloth/ps-ui) ```Config.SafeSkillcheck = "ps-circle"```
>
> - [qb-skillbar](https://github.com/qbcore-framework/qb-skillbar) ```Config.SafeSkillcheck = "square"```

## Adding a minigame

The events for these minigames can be found cl_public.lua. You can modify these events or add a new event for your own minigame.

```
--- Breaking into the house
--  Config.DoorSkillcheck = "square"
RegisterNetEvent("lockpicks:UseLockpickSquare", function() - Line 572

--- Disabling the security panel
--  Config.SecuritySkillcheck = "sqaure"
RegisterNetEvent("lockpicks:HackSecuritySquare", function(id, spawnId) - Line 775

--- Cracking safe
--  Config.SafeSkillcheck = "square"
RegisterNetEvent("lockpicks:LockpickSafeSquare", function(id) - Line 978
```

The minigame events shown above are handled by these functions in cl_public.lua.

```
--- Trigger the break in minigame
function BreakIn() - Line 1083

--- Trigger the disable security minigame
function DisableSecurity(id, spawnId) - Line 1104

--- Trigger the crack safe minigame
function CrackSafe(id) - Line 1118
```

## Breaking in time

You can change the time period for robbing houses in the main config. You will not be able to start the break in minigame if it is too early.

```
-- Example: if the time is 5AM or later you cannot break in
Config.MinTime = 5

-- Example: if the time is 11PM or later you can break in
Config.MaxTime = 23
```

You can completely remove the time check from the minigame events in cl_public.lua. Look for the events shown in the adding a minigame section [above](https://mknzz.github.io/burglary-docs/config.html#adding-a-minigame).

```
-- Remove this to remove the time check
local hours = GetClockHours()
if hours >= Config.MinTime and hours < Config.MaxTime then
    QbNotify(Config.Prompts["time"], "error")
    return
end
```

## Queue and finish time

You can set how long it takes to receive a job offer (seconds) in the main config

```
-- Set how long it takes for the player to receive a job (seconds)
Config.JobWaitTime = { 285, 345 } -- [1] = min seconds, [2] = max seconds
```

You can set how long the player has to complete the job (seconds)

```
-- Set how long the player has to complete the job after entry (seconds)
Config.JobTime = { 325, 385 } -- [1] = min seconds, [2] = max seconds
```

If you leave the house after it has expired the job will end

## Using phone mail

If enabled you will receive job offers and information via the phone mail app.

```
-- If true the player will be emailed a job offer using qb-phone, the offer must be accepted
-- If false the player will be notified via QB.Notify or the notify resource you are using
Config.QbPhoneMail = true
```

You can change the phone events used for sending mail in cl_public.lua.

```
--- Send job offer via mail qb-phone
--- @param tier integer
function SendJobMail(tier)
    TriggerServerEvent("qb-phone:server:sendNewMail", {
        sender = "Bossman",
        subject = "T"..tier.." ".."job offer",
        message = Config.Prompts["requested3"],
        button = 
        {                    
            enabled = true,
            buttonEvent = "burglary:client:AcceptHouse",
            buttonData = tier
        }
    })
end

--- Send job info via mail qb-phone
--- @param message string
--- @param subject string
function SendMail(message, subject)
    TriggerServerEvent("qb-phone:server:sendNewMail", {
        sender = "Bossman",
        subject = subject,
        message = message,
        button = {}
    })
end
```

## Using a different notify

You can disable the QB notifications in the main config

```
-- If true QB.Notify notifications will be used
-- If false a different notify resource can be used, you can add your own notify trigger in cl_public.lua
Config.QbNotify = false
```

Go to cl_public.lua and find the QbNotify function

```
--- Handle notifications
--- @param message string
--- @param type string
function QbNotify(message, type)
    if Config.QbNotify then
        Core.Functions.Notify(message, type)
    else
        --- Add your own notify trigger here, you must set Config.QbNotify = false in config.lua
        --- Example: using okokNotify

        --local time = GetClockHours()
        --exports['okokNotify']:Alert("Bossman", message, time, type)
    end
end
```

Add your own notify trigger/export to the else statement

## Adding or changing dispatch alerts

You can change the dispatch event or edit the alert function in cl_public.lua

```
local copsAlerted = false
local function AlertCoppers(housePos)
    if not copsAlerted then
        local street1, street2 = GetStreetNameAtCoord(housePos.x, housePos.y, housePos.z, Citizen.ResultAsInteger(), Citizen.ResultAsInteger())
        local street1name = GetStreetNameFromHashKey(street1)
        local street2name = GetStreetNameFromHashKey(street2)
        local gender = Core.Functions.GetPlayerData().charinfo.gender == 1 and "Female" or "Male"
        
        --- You must use housePos instead of player coords 
        
        ------- You can remove this export and add your own dispatch export/event -------
        --[[exports["ps-dispatch"]:CustomAlert({
            coords = housePos,
            message = "Possible B&E in progress",
            dispatchCode = "10-90",
            description = "Possible B&E in progress, "..gender.." ".."suspect",
            radius = 0,
            sprite = 40,
            color = 2,
            scale = 1.0,
            length = 3,
        })]]--
        ------- You can remove this export and add your own dispatch export/event -------

        copsAlerted = true
    end
end
```

For cd_dispatch users just replace the default AlertCoppers function with this one

```
local function AlertCoppers(housePos)
    if not copsAlerted then
        local street1, street2 = GetStreetNameAtCoord(housePos.x, housePos.y, housePos.z, Citizen.ResultAsInteger(),
            Citizen.ResultAsInteger())
        local street1name = GetStreetNameFromHashKey(street1)
        local street2name = GetStreetNameFromHashKey(street2)
        local gender = Core.Functions.GetPlayerData().charinfo.gender == 1 and "female" or "male"
        local dispatchMsg = string.format("Possible B&E in progress, %s suspect, at %s", gender, street1name..street2name)

        ------- You can remove this export and add your own dispatch export/event -------
        local data = exports['cd_dispatch']:GetPlayerInfo()
        TriggerServerEvent('cd_dispatch:AddNotification', {
            job_table = {'police', }, 
            coords = housePos,
            title = '10-90',
            message = dispatchMsg,
            flash = 0,
            unique_id = data.unique_id,
            sound = 1,
            blip = {
                sprite = 40, 
                scale = 1.2, 
                colour = 3,
                flashes = false, 
                text = '911 - House Robbery',
                time = 5,
                radius = 0,
            }
        })
        ------- You can remove this export and add your own dispatch export/event -------

        copsAlerted = true
    end
end
```

### Fixing ps-dispatch blips

If dispatch blips are broken when using ps-dispatch you will have to make some quick changes

In cl_public.lua locate the AlertCoppers(housePos) function line 5

Replace the entire AlertCoppers function with this, or you can add '''coords = housePos''' to the alert data yourself
```
--- Alerting the coppers
local copsAlerted = false
local function AlertCoppers(housePos)
    if not copsAlerted then
        local street1, street2 = GetStreetNameAtCoord(housePos.x, housePos.y, housePos.z, Citizen.ResultAsInteger(),
            Citizen.ResultAsInteger())
        local street1name = GetStreetNameFromHashKey(street1)
        local street2name = GetStreetNameFromHashKey(street2)
        local gender = Core.Functions.GetPlayerData().charinfo.gender == 1 and "Female" or "Male"

        ------- You can remove this export and add your own dispatch export/event -------
        exports["ps-dispatch"]:CustomAlert({
            message = "Possible B&E in progress",
            dispatchCode = "10-90",
            description = "Possible B&E in progress, " .. gender .. " " .. "suspect",
            radius = 0,
            sprite = 40,
            color = 2,
            scale = 1.0,
            length = 3,
            coords = housePos -- Set the coords to the house door
        })
        ------- You can remove this export and add your own dispatch export/event -------

        copsAlerted = true
    end
end
```

### Fixing ps-dispatch shooting blips

If blips are also broken when shooting/fighting you will have to make a few more changes

In ps-dispatch -> [client] -> alerts.lua look for ```local function Shooting()``` line 26

Replace the entire Shooting() function with this
```
local function Shooting()
    local coords = GetEntityCoords(cache.ped)

    local house = exports['qb-burglary']:GetTier()
    local inside = exports['qb-burglary']:GetInsideStatus()
    local houseCoords
    if house and inside then
        local currentId = exports['qb-burglary']:GetCurrentHouse()
        houseCoords = house[currentId]['location']
    end

    local dispatchData = {
        message = locale('shooting'),
        codeName = 'shooting',
        code = '10-11',
        icon = 'fas fa-gun',
        priority = 2,
        coords = houseCoords or coords, -- Set the robbery door coords
        street = GetStreetAndZone(coords),
        gender = GetPlayerGender(),
        weapon = GetWeaponName(),
        jobs = { 'leo' }
    }

    TriggerServerEvent('ps-dispatch:server:notify', dispatchData)
end
```

### Fixing ps-dispatch fighting blips

Do the exact same for ```local function Fight()``` line 117

Replace the entire Fight() function with this
```
local function Fight()
    local coords = GetEntityCoords(cache.ped)

    local house = exports['qb-burglary']:GetTier()
    local inside = exports['qb-burglary']:GetInsideStatus()
    local houseCoords
    if house and inside then
        local currentId = exports['qb-burglary']:GetCurrentHouse()
        houseCoords = house[currentId]['location']
    end

    local dispatchData = {
        message = locale('melee'),
        codeName = 'fight',
        code = '10-10',
        icon = 'fas fa-hand-fist',
        priority = 2,
        coords = houseCoords or coords, -- Set the robbery door coords
        gender = GetPlayerGender(),
        street = GetStreetAndZone(coords),
        jobs = { 'leo' }
    }

    TriggerServerEvent('ps-dispatch:server:notify', dispatchData)
end
```