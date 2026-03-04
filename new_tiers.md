---
layout: default
title: Adding a New Tier
nav_order: 5
---

# Adding a New Tier

This guide walks through adding a new tier to the burglary system. The dynamic tier system now automatically discovers tiers at startup.

---

## Overview

Adding a new tier requires **3 steps**:

1. Create the **house config** file (`houses/tierX.lua`)
2. Create the **interior config** file (`interiors/tierX.lua`)
3. Add **locale strings** for the menu

---

## Step 1: Create the House Config

Create `houses/tier5.lua` (or whichever tier number you're adding). This file defines all exterior/job configuration for the tier.

### Required Config Keys

Every tier **must** define these keys. Replace `5` with your tier number throughout.

```lua
----------------------------------------------------------------
-- Tier 5 Core Configuration
----------------------------------------------------------------

-- The chance of receiving a T5 house (0.0 to 1.0).
-- Set to 0.0 if using LevelScaling, which dynamically adjusts the chance based on player level.
Config.T5_HouseChance = 0.0

-- The scaling factor applied to boost the T5 house chance (0.0 to 1.0).
-- Only used when Config.LevelScaling = true in config.lua.
-- Uses power curve formula: lvl^1.5 * scaling
Config.T5_LevelScaling = 0.4

-- Minimum required level to request T5 jobs.
-- Only used when Config.RequireLevelForTier = true in config.lua.
Config.T5_RequiredLevel = 12

-- Percentage of reputation removed on job failure/cancel {min%, max%}.
Config.T5_RepRemovalPercent = { 10, 14 }

-- Required items to start the job.
Config.T5_RequiredItem = {
    require_all = true,         -- Whether ALL items are needed, or just one.
    "advancedlockpick",
    "securityphone",
}

-- Job duration in minutes.
Config.T5_JobTime = 40

-- Job offer queue delay in minutes {min, max}.
Config.T5_QueueTime = { 18, 30 }

-- Minimum online police required to start this tier.
Config.T5_MinPolice = 6

-- Cooldown in seconds between jobs of this tier.
Config.T5_JobCooldownSeconds = 300

-- Number of jobs before cooldown activates.
Config.T5_JobCooldownThreshold = 2

-- Break-in minigame configuration.
Config.T5_BreakInMinigame = {
    minigame = "circle",    -- Options: "circle", "square", "lockpick"
    circle = {
        amount = 6,
        speed = 5,
        custom = { size = 25, multi = 1.6 }
    },
    square = {
        time = 800,
        amount = 6,
        difficulty = "hard"
    },
}
```

### House Definitions

Define at least one house location:

```lua
Config.T5_Houses = {
    [1] = {
        -- Door coordinates (where the player enters).
        door_coords = vector3(123.45, -456.78, 29.50),

        -- Direction the door faces.
        heading = 180.0,

        -- Whether the house starts unlocked (should always be false).
        unlocked = false,

        -- Whether the house is currently occupied (should always be false).
        busy = false,

        -- Force a specific interior ID (optional, leave commented for random).
        -- interior = 1,

        -- Required items to enter this specific house (overrides tier-level items).
        required_item = {
            require_all = true,
            "advancedlockpick",
        },

        -------------------------------------------------------
        -- OPTIONAL: Exterior Features
        -- Include any combination of these to add exterior gameplay.
        -- Features are detected by presence.
        -------------------------------------------------------

        -- Security keypad outside the door (like T4).
        -- security_props = {
        --     [1] = {
        --         model = "ch_prop_ch_cctv_cam_02a",
        --         coords = vector3(123.0, -456.0, 30.0),
        --         rotation = vector3(0, 0, 180.0),
        --     },
        -- },

        -- CCTV cameras (triggers lockdown if player detected).
        -- security_cameras = {
        --     [1] = {
        --         model = "prop_cctv_cam_07a",
        --         coords = vector3(124.0, -457.0, 33.0),
        --         rotation = vector3(-25.0, 0.0, 90.0),
        --         monitor_coords = vector3(123.5, -456.5, 30.0),
        --         monitor_radius = 15.0,
        --     },
        -- },

        -- Armed guards (patrol outside the house).
        -- security_guards = {
        --     [1] = {
        --         model = GetHashKey('mp_m_freemode_01'),
        --         coords = vector4(122.0, -455.0, 29.5, 90.0),
        --         weapon = GetHashKey('WEAPON_PISTOL'),
        --         attachments = {},
        --         health = 250,
        --         style = "light_m",
        --         variation = "male",
        --         anim = { "amb@world_human_guard_stand@male@idle_a", "idle_c" },
        --         agro = true,
        --     },
        -- },

        -- Electric/power boxes (smashable with axe, controls lockdown power).
        -- electric_props = {
        --     [1] = {
        --         model = "prop_elecbox_09",
        --         coords = vector3(121.0, -454.0, 29.5),
        --         rotation = vector3(0, 0, 0),
        --     },
        -- },
    },
}
```

### Feature Flags

These flags control tier specific behavior. Add them after the houses table:

```lua
----------------------------------------------------------------
-- Tier 5 Feature Flags
----------------------------------------------------------------

-- Show "it's gotta be around here somewhere" hint near the house.
Config.T5_ShowNearHint = true

-- Play xsound alarm inside the house (requires Config.OptionalSound = true).
Config.T5_UseCustomAlarm = true

-- Use the USB insertion cutscene when disabling interior security panels.
Config.T5_UseUSBScene = true

-- Allow police to breach into houses of this tier.
Config.T5_AllowCopBreach = false

-- Use the Scrambler resource for break-in minigame (requires scrambler resource).
Config.T5_UseScrambler = true
```

### Optional: Guard Configuration

If your tier uses `security_guards`, you may want custom guard clothing and face features.

```lua
-- Minimum group members to start this tier (optional, omit for solo).
Config.T5_RequiredMemberCount = 4

-- Item needed to smash power/electric boxes (optional).
Config.T5_AxePrepItem = "heistaxe"

-- Chance of guards dropping a security keycard (0.0 to 1.0, optional).
Config.T5_SecurityCardChance = 0.55

-- Chance of getting a hint to loot guards (0.0 to 1.0, optional).
Config.T5_LootGuardHintChance = 0.3

-- Chance of losing the keycard on failed minigame (0.0 to 1.0, optional).
Config.T5_RemoveKeycardChance = 0.6

-- Override freemode guard model with a specific ped model (optional).
-- If set, guards will use this model instead of mp_m_freemode_01 with custom clothing.
-- Config.T5_OverrideGuardModel = "S_M_Y_BlackOps_01"

-- Guard clothing styles (optional, required if guards use freemode models).
-- Config.T5_GuardClothing = { ... }  -- See tier4.lua for full example

-- Guard face presets (optional, required if guards use freemode models).
-- Config.T5_GuardFaceFeatures = { ... }  -- See tier4.lua for full example
```

---

## Step 2: Create the Interior Config

Create `interiors/tier5.lua`. This defines the interior layout, loot zones, props, and safe configuration.

```lua
-- Configuration for T5 house interiors
Config.T5_Interiors = {
    [1] = {
        -- Teleport destination inside the interior.
        ipl_coords = vector3(123.0, -456.0, -100.0),

        -- Extra decoration props (optional).
        extra_props = {},

        -- Remove default interior objects (optional).
        extra_removals = {},

        -- Interior security alarm panels (optional).
        security_props = {
            -- [1] = {
            --     model = "hei_prop_hei_securitypanel",
            --     coords = vector3(125.0, -458.0, -99.5),
            --     rotation = vector3(0, 0, 90.0),
            --     required_tools = { "screwdriver" },
            --     anti_tamper = false,
            --     disabled = false,
            --     beeps = { current = 0, max = 8, speed = 1000 },
            -- },
        },

        -- Searchable zones (drawers, closets, shelves).
        search_zones = {
            [1] = {
                coords = vector3(124.0, -457.0, -99.5),
                heading = 180.0,
                display_name = "Cabinet",
                searched = false,
                busy = false,
                chance = { 0.0, 1 },
            },
        },

        -- Pickup props (spawned objects the player can grab).
        pickup_props = {
            [1] = {
                model = "prop_laptop_01a",
                coords = vector3(125.0, -456.0, -99.2),
                rotation = vector3(0, 0, 45.0),
                display_name = "Laptop",
                item_name = "laptop",
                item_amount = 1,
                searched = false,
                busy = false,
                chance = { 0.0, 1 },
            },
        },

        -- Pickup zones (interact with existing interior objects).
        pickup_zones = {},

        -- Interior doors that can be cracked/lockpicked.
        interior_doors = {},

        -- Homeowner peds inside the house.
        homeowner_peds = {},

        -- Safe props.
        safe_props = {},
    },
}

-- Safe minigame configuration for this tier.
Config.T5_SafeMinigame = {
    minigame = "circle",
    circle = {
        amount = 4,
        speed = 5,
        custom = { size = 25, multi = 1.5 }
    },
    square = {
        time = 800,
        amount = 5,
        difficulty = "hard"
    },
}

```

> **Tip:** Look at the existing `interiors/tier1.lua` through `interiors/tier4.lua` for complete examples of interior configurations with all available options.

---

## Step 3: Add Locale Strings

Add menu title and description entries to `locales/locale_en.json`:

```json
{
    "t5_job_title": "Extreme Risk",
    "t5_job_desc": "Fortified compounds. Only the most experienced crews need apply."
}
```

These are used by the bossman menu to display the tier's job listing. The keys follow the pattern `tX_job_title` and `tX_job_desc`.

### Optional Locale Strings

If you use mail/phone notifications for job start messages, you may also add:

```json
{
    "t5_job_start_message": "We've found a heavily fortified target. The location is marked on your GPS.",
    "t5_warning_message": "Expect extreme resistance. Armed response teams are on patrol.",
    "t5_setup_message": "Intel suggests a high-value cache inside. Coordinate with your team before engaging."
}
```

> **Note:** These are only needed if your `NotifyJobStarted` and related functions reference tier specific locale keys. The default implementation constructs messages using generic keys with the tier number.

---

## Step 4 (Optional): Add RepLimits and RepPenalties

If you want custom reputation values for the new tier, add entries to the tables in `shared/config.lua`:

```lua
Config.RepLimits = {
    breaking_in = {
        [1] = { 50, 100 },
        [2] = { 100, 150 },
        [3] = { 150, 250 },
        [4] = { 250, 350 },
        [5] = { 350, 500 },   -- Add entry for T5
    },
    -- ... repeat for each action type
}

Config.RepPenalties = {
    breakin_failed = {
        [1] = { 1, 2 },
        [2] = { 1, 2 },
        [3] = { 1, 2 },
        [4] = { 1, 2 },
        [5] = { 1, 3 },       -- Add entry for T5
    },
    -- ... repeat for each penalty type
}
```

**If you skip this step**, the system automatically inherits values from the highest defined tier (T4) for all new tiers.

---

## Verification

After adding all files and restarting the resource:

1. Check the server console for the auto-discovery message:
   ```
   [burglary] Discovered 5 tier(s) from config.
   ```
2. Open the bossman menu — the new tier should appear in the job list.
3. Test that the tier respects its `RequiredLevel` and `MinPolice` settings.

---

## Config Key Reference

### Required Keys (must be defined for every tier)

| Key | Type | Description |
|-----|------|-------------|
| `TX_Houses` | `table` | House location definitions (at least one entry) |
| `TX_Interiors` | `table` | Interior layout definitions (at least one entry) |
| `TX_HouseChance` | `number` | Base chance of receiving this tier (0.0–1.0) |
| `TX_RequiredLevel` | `number` | Minimum player level required |
| `TX_RepRemovalPercent` | `table` | `{min%, max%}` rep penalty on failure |
| `TX_RequiredItem` | `table` | Items needed to start the job |
| `TX_JobTime` | `number` | Job duration in minutes |
| `TX_QueueTime` | `table` | `{min, max}` queue delay in minutes |
| `TX_MinPolice` | `number` | Minimum online police count |
| `TX_JobCooldownSeconds` | `number` | Cooldown between jobs in seconds |
| `TX_JobCooldownThreshold` | `number` | Jobs before cooldown activates |
| `TX_BreakInMinigame` | `table` | Break-in minigame configuration |
| `TX_Rewards` | `table` | Loot/reward table by room type |
| `TX_SafeMinigame` | `table` | Safe cracking minigame configuration |

### Feature Flags (optional, have sensible defaults)

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| `TX_ShowNearHint` | `boolean` | `false` | Show proximity hint near the house |
| `TX_UseCustomAlarm` | `boolean` | `tier > 1` | Play xsound alarm inside |
| `TX_UseUSBScene` | `boolean` | `false` | USB insertion animation for security |
| `TX_AllowCopBreach` | `boolean` | `true` | Allow police breach events |
| `TX_UseScrambler` | `boolean` | `false` | Use Scrambler minigame resource |
| `TX_LevelScaling` | `number` | `0.0` | Level based chance scaling factor |
| `TX_RequiredMemberCount` | `number` | Minimum group size to start |

### Guard/Security Keys (optional, only needed if house has security_guards)

| Key | Type | Description |
|-----|------|-------------|
| `TX_AxePrepItem` | `string` | Item name for smashing power boxes |
| `TX_SecurityCardChance` | `number` | Guard keycard drop chance (0.0–1.0) |
| `TX_LootGuardHintChance` | `number` | Hint about looting guards chance (0.0–1.0) |
| `TX_RemoveKeycardChance` | `number` | Keycard removal on fail chance (0.0–1.0) |
| `TX_OverrideGuardModel` | `string` | Override freemode model for guards |
| `TX_GuardClothing` | `table` | Guard clothing style definitions |
| `TX_GuardFaceFeatures` | `table` | Guard face/hair variation presets |

---

## How It Works Under the Hood

The dynamic system uses string key construction to access config values:

```lua
-- Instead of static lookup tables like:
local tables = { [1] = Config.T1_Houses, [2] = Config.T2_Houses, ... }

-- The system now does:
local houses = Config["T" .. tier .. "_Houses"]
```

The `GetTierConfig(tier, key, default)` helper wraps this pattern with nil-safety:

```lua
-- Returns Config.T3_ShowNearHint, or false if not defined
local showHint = GetTierConfig(3, 'ShowNearHint', false)
```

Features like guards, cameras, electric props, and security keypads are activated by **presence in the house config** rather than tier number:

```lua
-- Old: if houseData.tier == 4 then spawnGuards() end
-- New: if houseData.house.security_guards then spawnGuards() end
```

This means any tier can have guards, cameras, or any combination of exterior features simply by defining them in the house table.
