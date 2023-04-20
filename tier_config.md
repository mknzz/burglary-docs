---
layout: default
title: House configs
nav_order: 4
---

# Configuring qb-burglary

## qb-burglary\houses\tier1.lua\tier2.lua\tier3.lua

Open the tier 1-3 lua configs in the houses subfolder. If you want to edit the house config for T1 go to houses -> tier1.lua.

## Adding more houses to rob

Simply copy and paste one of the existing houses and replace the ["location"] and ["heading"]

```
-- We will be editing the tier1.lua config as an example, the layout exactly the same as tier2 & tier3
-- T1 house locations
Config.T1_Houses = {
    --[[****************************GROVE STREET***************************--]]
    [1] = { -- Make sure you change the ID when pasting.
        ["interior"] = 1, -- Set the interior ID for this house, T1 interior list: interiors/tier1.lua
        ["location"] = vector3(29.54, -1854.5, 24.07), -- Set the entry door location for this house
        ["heading"] = 225.12, -- Set the entry door heading for this house
        ["unlocked"] = false, -- Do not change
        ["authorised"] = false, -- Do not change
        ["poly"] = false, -- Do not change
    },
}
```

## Setting the required level to start a job

If you are using Config.Reputation you can set the required level for a certain tier in each one of the house configs.

``` 
-- We will be editing the tier1.lua config as an example, the layout exactly the same as tier2 & tier3
-- If Config.Reputation = true the player must reach this level to unlock T1 houses
Config.T1_RequiredLvl = 1
```

## Changing the difficulty for circle minigames

["circles"] is the amount of circles
["speed"] is the rotating speed in seconds (i think)

```
-- We will be editing the tier1.lua config as an example, the layout exactly the same as tier2 & tier3
-- Set the T1 circle / ps-circle minigame difficulty - https://github.com/Nathan-FiveM/qb-lock
Config.T1_Difficulty = {
    ["circles"] = { 3, 4 }, -- [1] = min, [2] = max
    ["speed"] = { 8, 9 } -- [1] = min, [2] = max
}
```

## Setting the required items for breaking in and cracking the safe

```
-- We will be editing the tier1.lua config as an example, the layout exactly the same as tier2 & tier3
-- Set the item used to break in to T1 houses
Config.T1_RequiredBreakIn = "lockpick"

-- Set the item used to crack in to T1 safes
Config.T1_RequiredSafeCracker = "lockpick"
```

## Setting the reward items for searching locations

A random rarity will be chosen based on ["chance"], once a rarity is chosen one random item from that rarity will be given.

["exp"] is how much XP the player will receive when searching locations, only if Config.Reputation = true
["item"] is the QB item name
["amount"] is the QB item amount min & max

```
-- We will be editing the tier1.lua config as an example, the layout exactly the same as tier2 & tier3
-- Set the reward items for T1 search locations
Config.T1_RewardItems = {
    ["common"] = { ["chance"] = 100, ["exp"] = { 55, 95 },
        [1] = {
            ["item"] = "twerks_candy",
            ["amount"] = { 1, 2 },
        },
        [2] = {
            ["item"] = "metalscrap",
            ["amount"] = { 1, 2 },
        },
        [3] = {
            ["item"] = "copper",
            ["amount"] = { 1, 2 },
        },
        [4] = {
            ["item"] = "bandage",
            ["amount"] = { 1, 2 },
        },
    },
    ["uncommon"] = { ["chance"] = 55, ["exp"] = { 95, 135 },
        [1] = {
            ["item"] = "joint",
            ["amount"] = { 1, 3 },
        },
        [2] = {
            ["item"] = "rolling_paper",
            ["amount"] = { 1, 3 },
        },
        [3] = {
            ["item"] = "firstaid",
            ["amount"] = { 1, 3 },
        }
    },
    ["rare"] = { ["chance"] = 35, ["exp"] = { 135, 175 },
        [1] = {
            ["item"] = "cokebaggy",
            ["amount"] = { 1, 4 },
        },
        [2] = {
            ["item"] = "xtcbaggy",
            ["amount"] = { 1, 4 },
        },
        [3] = {
            ["item"] = "crack_baggy",
            ["amount"] = { 1, 4 },
        },
    },
    ["noway"] = { ["chance"] = 15, ["exp"] = { 175, 215 },
        [1] = {
            ["item"] = "goldchain",
            ["amount"] = { 1, 5 },
        },
        [2] = {
            ["item"] = "10kgoldchain",
            ["amount"] = { 1, 5 },
        },
        [3] = {
            ["item"] = "fitbit",
            ["amount"] = { 1, 5 },
        },
        [4] = {
            ["item"] = "trojan_usb",
            ["amount"] = { 1, 5 },
        }
    },
}
```

## Setting the reward items for safes

This works exactly the same as the search location rewards table.

["exp"] is how much XP the player will receive when searching locations, only if Config.Reputation = true
["item"] is the QB item name
["amount"] is the QB item amount min & max

```
-- We will be editing the tier1.lua config as an example, the layout exactly the same as tier2 & tier3
-- Set the reward items for T1 safes
Config.T1_SafeRewardItems = {
    ["common"] = { ["chance"] = 100, ["exp"] = { 100, 150 },
        [1] = {
            ["item"] = "sandwich",
            ["amount"] = { 1, 2 },
        },
        [2] = {
            ["item"] = "metalscrap",
            ["amount"] = { 1, 2 },
        },
        [3] = {
            ["item"] = "copper",
            ["amount"] = { 1, 2 },
        },
        [4] = {
            ["item"] = "rubber",
            ["amount"] = { 1, 2 },
        },
    },
    ["uncommon"] = { ["chance"] = 55, ["exp"] = { 200, 250 },
        [1] = {
            ["item"] = "joint",
            ["amount"] = { 1, 3 },
        },
        [2] = {
            ["item"] = "rolling_paper",
            ["amount"] = { 1, 3 },
        },
        [3] = {
            ["item"] = "bandage",
            ["amount"] = { 1, 3 },
        }
    },
    ["rare"] = { ["chance"] = 35, ["exp"] = { 300, 350 },
        [1] = {
            ["item"] = "cokebaggy",
            ["amount"] = { 1, 4 },
        },
        [2] = {
            ["item"] = "xtcbaggy",
            ["amount"] = { 1, 4 },
        },
        [3] = {
            ["item"] = "crack_baggy",
            ["amount"] = { 1, 4 },
        },
    },
    ["noway"] = { ["chance"] = 15, ["exp"] = { 350, 400 },
        [1] = {
            ["item"] = "goldchain",
            ["amount"] = { 1, 5 },
        },
        [2] = {
            ["item"] = "10kgoldchain",
            ["amount"] = { 1, 5 },
        },
        [3] = {
            ["item"] = "fitbit",
            ["amount"] = { 1, 5 },
        },
        [4] = {
            ["item"] = "trojan_usb",
            ["amount"] = { 1, 5 },
        }
    },
}
```

## Setting the amount given from cash props

A random amount of cash will be given based on the Config.T1_RewardCashAmount.

Example: math.random(Config.T1_RewardCashAmount[1], Config.T1_RewardCashAmount[2])

```
-- We will be editing the tier1.lua config as an example, the layout exactly the same as tier2 & tier3
-- Set the amount of cash rewarded for T1 cash piles
Config.T1_RewardCashAmount = { 150, 250 } -- [1] = min, [2] = max
```