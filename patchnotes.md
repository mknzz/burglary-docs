---
layout: default
title: Patch Notes
nav_order: 6
---

# Patch Notes for sk-burglary

Latest patch notes for both escrow and full source versions.

## Update 3.6.0

**Reputation System Overhaul & Carry System Rewrite**

### Complete Reputation Security Overhaul

**Server Side Validation**
- All reputation calculations and modifications now happen server-side.
- New validation system with `Config.RepLimits` table defining min/max rep ranges for each action type and tier.
- Added `burglary:server:AddRepForGroup` event - Server validates rep amounts and distributes to all group members.
- Added `burglary:server:RemoveRepForGroup` event - Server calculates penalties and applies to group members.
- Modified `burglary:client:AddValidatedRep` - Now receives server `newTotal` value instead of calculating locally.
- Modified `burglary:client:RemoveValidatedRep` - Now receives server `newTotal` value.
- Removed vulnerable `burglary:server:UpdateRep` event that accepted client-provided rep values.

**Server Side Rep Tracking**
- Added `playerRepCache` to maintain authoritative reputation state on the server.
- Modified `loadPlayerRep()` - Now caches rep server-side and sends values to clients.
- Modified `updatePlayerRep()` - Server directly updates database after validation.
- Client `currentRep` is now display only, fully synced with server's value.

**Group Reputation Distribution**
- All group members inside the house now receive reputation rewards automatically.
- Updated `AddRepForReason(reason, tier, customRepRange)` function in `funcs.lua`.

**Rep Limits**
- Moved `repRewards` from `funcs.lua` to `Config.RepLimits` in `config.lua`.
- Added `Config.MaxCustomRepAllowed` (default: 2,000) to cap custom task rep values.
- Full validation for custom rep ranges - only `complete_task` can use custom values.
- Rep limit values scaled down to match new `RepPerLevel` thresholds (e.g. `breaking_in` T1: 50-100, T4: 250-350).
- Task rewards (`Config.TasksList`) rebalanced to match (e.g. Kill A Jugg: 1,000-1,500 rep).

### Power Curve Level Scaling System

**Improved Progression Formula**
- Changed from linear (`lvl * scaling`) to a power curve (`lvl^1.5 / maxLevel^1.5`) for accelerating progression.
- Modified `GetTierChance(lvl)` in `main.lua` - Now uses power curve scaling factor.
- Configurable probability normalization via `Config.MaxTierChance` (default: 0.85) caps total T2+T3+T4 chances to reserve a minimum for T1.
- More significant tier chance improvements at higher levels.

**Rebalanced Reputation Thresholds**

Updated `Config.RepPerLevel` for accelerating difficulty curve (~206,500 total rep, ~100-120 runs to max):
- Level 1→2: 2,000 rep
- Level 2→3: 3,500 rep
- Level 3→4: 6,000 rep
- Level 4→5: 9,000 rep
- Level 5→6: 13,000 rep
- Level 6→7: 18,000 rep
- Level 7→8: 25,000 rep
- Level 8→9: 33,000 rep
- Level 9→10: 42,000 rep
- Level 10→Max: 55,000 rep (display only)

### Tier 4 Job Selection Fixed
- Fixed `getRandomTier(lvl)` function to properly include tier 4 in random job selection.
- Modified selection logic in `main.lua` to check tiers in descending order: T4 → T3 → T2 → T1.
- Tier 4 now properly scales with level progression as intended.

### Rebalanced Tier Chances & Scaling

Updated tier configuration files with new base chances and power curve scaling factors. T3 and T4 now have 0% base chance, earned purely through level progression.

**Added `Config.RequireLevelForTier`** (default: `true`)
- When enabled, tiers are hard gated behind their `RequiredLevel` setting even when `LevelScaling` is enabled.
- A level 1 player cannot roll T3/T4 by chance. Set `false` for more probability based selection.
- Modified `getRandomTier(lvl)` in `main.lua` to enforce tier gates.

**Tier Distribution by Level** (with `RequireLevelForTier = true`):

Level 1-3 (New Players):
- T1: 100% (T2/T3/T4 locked)

Level 4 (Unlocks T2):
- T1: ~87% | T2: ~13%

Level 7 (Unlocks T3):
- T1: ~60% | T2: ~23% | T3: ~18%

Level 9 (Unlocks T4):
- T1: ~15% | T2: ~30% | T3: ~25% | T4: ~30%

Level 10 (Max Level):
- T1: ~15% | T2: ~30% | T3: ~26% | T4: ~30%

**Updated Configuration:**
- **Tier 1**: Base chance 1.0 (100%), no scaling - Always available as fallback
- **Tier 2**: Base chance 0.05 (5%), scaling factor 0.30 (was 0.03) - RequiredLevel: 4
- **Tier 3**: Base chance 0.0 (0%), scaling factor 0.30 (was 0.04) - RequiredLevel: 7
- **Tier 4**: Base chance 0.0 (0%), scaling factor 0.35 (was 0.047) - RequiredLevel: 9

Normalization (`Config.MaxTierChance`, default 0.85) activates at level 9+ when all tiers are unlocked, ensuring T1 always has at least 15% chance.

### Reputation Penalty

**Action Based Penalties**
- New `Config.RepPenalties` table in `config.lua` defines per reason, per tier penalty ranges as `{min%, max%}`.
- Penalties are applied as a percentage of the player's current rep, matching the existing `RepRemovalPercent` format.
- Each penalty reason can be individually tuned or disabled by setting both values to 0.

**Penalty Triggers:**
- `breakin_failed` - Failed the front door break-in minigame (default: 1-2% all tiers)
- `alarm_triggered` - Interior security alarm triggered for the first time via noise, gunshot, or anti-tamper (default: 1-4% depending on tier). Only fires once per burglary.
- `safe_failed` - Failed the safe cracking minigame (default: 1-3% depending on tier).
- `player_downed` - Player killed inside the house, triggering exterior teleport (default: 3-5% all tiers)
- `lockdown_triggered` - T4 CCTV lockdown activated (default: 4-6% T4 only, 0% T1-T3)

**Server Handling**
- Updated `burglary:server:RemoveRepForGroup` to accept an optional `reason` parameter.
- When a reason is provided and exists in `Config.RepPenalties`, uses the reason specific penalty range.
- Falls back to `T*_RepRemovalPercent` (job cancellation penalty) when no reason is given.
- Ranges set to `{0, 0}` are skipped server-side.

### Carry System Rewrite

**Complete System Overhaul**
- Rewrote the entire carry prop system with improved state management, reliability and performance.
- New `carryState` table structure for cleaner state tracking.
- Added `isAutoUpdate` and `isAttaching` flags to prevent race conditions and duplicate attachments.

**Per Item Animation Support**
- Each carry prop can now have its own custom animation.
- Added `animDict` and `anim` fields to `Config.CarryProps` entries.
- Default animations: Box carry for normal items, jerrycan for tools.

**Universal Auto Carry Monitor**
- Removed inventory specific event listeners in favor of universal polling system.
- Auto carry now works with all inventory systems (`qb-inventory`, `ox_inventory`, `qs-inventory`, `jaksam-inventory`).
- Polling based detection every 500ms using existing `PlyHasItem()` function.
- Fixed carry props spawning on the floor/in the air when joining a server with a carry item in inventory. Added `playerReady` flag driven by `QBCore:Client:OnPlayerLoaded` to delay prop attachment, inventory monitoring, and animation monitoring until the player ped is fully loaded in.

**Improved Weapon Handling**
- Auto holster weapons when picking up carry items instead of blocking the action.
- Weapons are automatically holstered with proper wait for completion.
- Carry prop re-attaches after holstering if item still in inventory.
- Notification shown when weapon is holstered while carrying.

### Ped Spawning & Target Fix

**Bossman & Sellman Target Fix**
- Fixed bossman and sellman targets not appearing on first join after a fresh server start.
- Ped creation now waits for `QBCore:Client:OnPlayerLoaded` before requesting peds from the server, ensuring the target system (qb-target/ox_target) is fully initialized.
- On resource restart, peds are created immediately via `LocalPlayer.state.isLoggedIn` check since the player is already loaded.

### Code Changes

**Removed Functions**
- Removed `AddRep(amount)` function - Replaced with server validated `AddRepForReason()`.
- Removed `GetRepPenalty(tier)` function - Penalty calculation now handled server-side via `Config.RepPenalties`.
- Removed legacy task rep logic from `GiveTaskRewards()`.
- Removed unused `validateRepAmount()` function from `main.lua`.
- Removed `repRewards` table from `funcs.lua` - Data moved to `Config.RepLimits`.
- Removed inventory specific event listeners from carry system.
- Removed unused `tryAttachToPlayer` function from carry system.

**Modified Functions**
- Modified `RemoveRep(tier)` → `RemoveRep(tier, reason)` - Now accepts an optional reason string to select a penalty range from `Config.RepPenalties`.
- Modified `triggerSecurityAlarms()` - Added `wasAlreadyDetected` check so the alarm penalty only triggers once per burglary.
- Modified `UnlockAndEnter(success)` - Calls `RemoveRep` with `'breakin_failed'` on minigame failure.
- Modified `unlockSafe()` callback - Calls `RemoveRep` with `'safe_failed'` when safe cracking fails (key unlock unaffected).
- Modified `TeleportOnDeath()` - Calls `RemoveRep` with `'player_downed'` before teleporting.
- Modified `LockdownExterior()` - Calls `RemoveRep` with `'lockdown_triggered'` when CCTV lockdown activates.
- Modified `burglary:server:RemoveRepForGroup` - Accepts optional `reason` param, looks up `Config.RepPenalties[reason][tier]` or falls back to `T*_RepRemovalPercent`.

**Files Modified**
- `server/main.lua` - Server-side validation, group distribution, rep tracking, tier validation, reason-based penalty lookup
- `client/main.lua` - Client validation handlers, scaling formula, tier selection, penalty hooks at failure points
- `client/funcs.lua` - New `AddRepForReason()` helper function
- `client/carry.lua` - Complete rewrite with per item animations and universal auto carry
- `client/peds.lua` - Added ready check for bossman/sellman target setup
- `shared/config.lua` - Added `Config.RepLimits`, `Config.MaxCustomRepAllowed`, `Config.RequireLevelForTier`, `Config.MaxTierChance`, `Config.RepPenalties`, updated rep thresholds & task rewards
- `houses/tier1.lua` - Added T1 chance config (1.0, no scaling)
- `houses/tier2.lua` - Updated T2 chance (0.05) & scaling (0.30)
- `houses/tier3.lua` - Updated T3 chance (0.0) & scaling (0.30)
- `houses/tier4.lua` - Updated T4 chance (0.0) & scaling (0.35)

### Migration Notes

**Configuration Migration**
- Rep limits are now configurable in `config.lua` via `Config.RepLimits`.
- Carry props can now include `animDict` and `anim` fields for custom animations.

**Database Migration**
- No schema changes required as the `boss_reputation` and `boss_task` tables remain the same.
- **Recommended**: Reset or delete all player reputation data due to the significantly rebalanced `RepPerLevel` thresholds. Existing rep values were earned under the old curve and will place players at incorrect levels.
  - Option A: Drop the rep table, it'll be auto created when the resource starts.
  - Option B: Reset all rep to 0 or delete all entries.
  - Option C: Leave as is, but expect players to be at higher levels than intended.

**Recommended**
- Review `Config.RepLimits` and adjust rep ranges if needed for your server economy.
- Review `Config.RepPerLevel` thresholds for desired progression speed.
- Fine tune tier scaling factors if you prefer different tier distribution.
- Adjust `Config.MaxTierChance` to control minimum T1 chance (default 0.85 = 15% T1 minimum).
- Set `Config.RequireLevelForTier = false` if you prefer pure probability based tier selection without level gates. For `Config.LevelScaling = true` only.
- Review `Config.RepPenalties` and adjust penalty ranges per reason and tier. Set `{0, 0}` to disable any specific penalty.

## Update 3.5.3

**General Fixes & Improvements**

- Fixed not being able to pickup/target a lot of weapon/attachment objects in the T4 interior when using `ox_target`. Added sphere zone creation for props that `ox_target` cannot detect with standard entity zones.
  - Configurable list of models requiring sphere zones via `Config.OxUntargetableModels` in `shared/config.lua`.
  - New functions:
    - `calculateSphereRadius(model)` in `client/main.lua` - Calculates optimal sphere radius based on model dimensions.
    - `requiresSphereZone(model)` in `client/main.lua` - Checks if a model needs sphere zone.
    - `SphereZoneTarget()` in `client/funcs.lua` - Creates an `ox_target` sphere zone.
  - Modified `setPickupPropTarget()` in `client/main.lua` to automatically choose between entity zones and sphere zones.
  - Modified `spawnPickupProps()` in `client/main.lua` to track which props use sphere zones.
  - Modified `removeAllPickupProps()` in `client/main.lua` to properly clean up sphere zones.
  - Modified `RemovePickupProp()` in `client/main.lua` to properly clean up individual sphere zones.

- Fixed CCTV monitoring breaking when network ownership changes. Modified `TeleportToLocation()` to transfer CCTV entity ownership and retrigger monitoring on enter/exit.
  - New functions/events:
    - `ensureCameraMonitoring()` in `client/main.lua`
    - `burglary:EnsureCameraMonitoring` in `client/main.lua` and `server/main.lua`
    - `burglary:ResumeCameraMonitoring` in `client/main.lua` and `server/main.lua`
→ Modified `TeleportToLocation()` in `client/main.lua`.

- Fixed some guard appearance reverting to default when entering and exiting the interior. Modified `TeleportToLocation()` to force guard appearance reconfiguration on exit.
  - New event: `burglary:ForceGuardReconfig` in `client/main.lua` and `server/main.lua`
→ Modified `TeleportToLocation()` in `client/main.lua`.

- Fixed players getting stuck in the wrong routing bucket when exiting the T4 interior. Removed `SetEntityBucketTemp` usage and function completely, reverting to the previous trusty simple bucket setting.
  - Modified `SetPlayerBucket` & `ResetPlayerBucket` events in `server/main.lua`.
  - Removed `SetEntityBucket` & `ResetEntityBucket` events in `server/main.lua`.
→ Modified `TeleportToLocation()` in `client/main.lua`.

- Reverted T4 interior medical bag search location coordinates.
→ Modified `search_zones` coordinates in `interiors/tier4.lua` → `Config.T4_Interiors[1]`.

## Update 3.5.2

**Jaksam Inventory Support**
- Added full support for `jaksam-inventory`.
- Modified `PlyHasItem()` and `PlyCanCarryItem()` functions in `client/funcs.lua`.
- Added `onItemAdded` and `onItemRemoved` hooks in `client/carry.lua`.
- Updated `canAddItem()`, `AddItem()`, `RemoveItem()`, and `VerifyItemInfo()` in `server/items.lua`.
- Modified `GetItemsList()` in `shared/init.lua`.
- Added to `Config.OptionalResources` in `shared/config.lua`.

**Enhanced Item Group Support**
- Players can now use alternative items to satisfy requirements.
- Primarily affects T2 houses and T2/T3 interiors: `lockpick` or `advancedlockpick` + `screwdriver`.
- Items are specified in nested table format: `{ "lockpick", "advancedlockpick" }`.
- Modified `PlyHasItem()` function in `client/funcs.lua`.
- Updated all `required_items` tables in `houses/tier2.lua` → `Config.T2_Houses` and `Config.T2_RequiredItem`.
- Updated `locked_doors/required_items` in `interiors/tier2.lua` → `Config.T2_Interiors[1]` Mid Apartment ID 1.
- Updated `locked_doors/required_items` in `interiors/tier3.lua` → `Config.T3_Interiors[1]` High End ID 1.

**Configurable Security Card Removal**
- Added `Config.T4_RemoveKeycardChance` (0.0 to 1.0) in `houses/tier4.lua`.
- Modified `unlockDoorWithSecurity()` in `client/main.lua`.

**General Fixes, Changes & Improvements**
- Fixed guard loot hints going to the wrong player. Loot hints were sent to the peds network owner instead of the killer.
→ Modified guard death event handler in `client/main.lua` and added `LootGuardHint` event in `server/main.lua`.

- Fixed juggernaut boss kill task not triggering. Task event was sent to the peds network owner instead of the killer.
→ Modified guard death event handler in `client/main.lua` and added `JuggBossCheck` event in `server/main.lua`.

- Fixed screwdriver requirement validation. Any prop in hand + screwdriver in inventory would satisfy the check. Now specifically checks if carrying the required item.
→ Added `IsCarryingItem(itemName)` function in `client/carry.lua` and modified `TryBreakin()` and `CheckForScrewdriver()` functions in `client/main.lua`.

- Fixed weapon pickup props with `ox_inventory`. Broken in update 3.5.0, weapon names weren't being converted before can carry inventory checks. Now applies `Config.ReplaceQbItemNames` conversion before `PlyCanCarryItem()`.
→ Modified `pickupAProp()` in `client/main.lua`.

- Adjusted item removal on minigame failures.
→ Modified `setDoorTarget()` (interior door cracking), `unlockDoorWithSecurity()` (T4 security keypad), `unlockSafe()` (safe cracking), and `UnlockAndEnter()` (house break-in) in `client/main.lua`.

- Fixed animation not ending after failed interior door cracking. Player remained stuck in lockpicking animation. Added proper animation cleanup on failure.
→ Modified `setDoorTarget()` in `client/main.lua`.

- Improved search zone notification timing. Moved "No Rewards" notification to after the progress bar completes.
→ Modified `searchAZone()` in `client/main.lua`.

- Added `qbcore` → `ox_inventory` item name conversion handling for search zones. Add items to convert in `Config.ReplaceQbItemNames`.
→ Modified `searchAZone()` in `client/main.lua`.

- Fixed incorrect error message params in `PlyCanCarryItem`. Error messages were passing type to the message param and vice versa. Item name wasn't being displayed correctly in "Item not found" errors.
→ Modified `PlyCanCarryItem()` in `client/funcs.lua`.

- Improved T4 medical bag search zone targeting. Adjusted vector3 coordinates for medical bag search zones to be more accessible when using `ox_target`.
→ Modified `search_zones` coordinates in `interiors/tier4.lua` → `Config.T4_Interiors[1]`.

- Improved main house cleanup and reset function. Refactored reset functions into smaller, reusable helper functions:
  - Added `resetPlayerState()` - Cleans up player-specific data (hints, detection status, etc.)
  - Added `resetInteriorEntities()` - Clears interior entity tracking table
  - Added `cleanupInteriorState()` - Handles all interior resets (search zones, peds, security, doors, safes)
  - Added `cleanupExteriorState()` - Handles all exterior cleanup (props, peds, alarms)
  - Added `resetHouseServerState()` - Manages states when no players inside
  - Improved `ResetHouse()` function organization with clearer flow and better separation.
→ Modified `ResetHouse()` and added new helper functions in `client/main.lua`.

- Reverted Halloween Madness changes. Removed all Halloween specific code and configurations.
→ Removed everything related from `client/main.lua` and events from `server/main.lua`. Restored original functions in `client/funcs.lua` and settings in `shared/config.lua`. All interior configuration files reverted to original.

## Update 3.5.1

**General Fixes & Improvements**
- Fixed job timeout system not ending jobs. Jobs would fail to auto end after the correct duration if a previous job had been manually cancelled previously. CancelJobTimeout() now properly cleans up the timeout callback and monitoring thread.
- **Interior door cracking actually fixed**
- Added `EnterHouseAlert(tier, coords, success)` to `funcs.lua` - Triggered when entering or attempting to break into houses, including tier information, coordinates, and break-in success status for better police response integration.

**Halloween Madness**
Added three new daily tasks:
- **Face Your Fears**: Encounter a shadow figure during any T1-T4 job
- **Toxic Immunity**: Complete a T4 job without taking any biohazard damage
- **Halloween Madness**: Experience the full Halloween chaos: survive a shadow encounter, complete a T4 job without biohazard damage, and tame a chimp. Progress persists across multiple jobs until all three conditions are met.

## 🎃 Halloween Madness Update 3.5.0

☣️ **Biohazard Contamination (Tier 4)**
- **Toxic interior environment**: Filled with dense green gas clouds using particle effects.
- **Protection system**: A gas mask item provides full immunity. Masks can be found around the interior or looted from peds.
- **Survival mechanics**: Gradual health damage, warning notifications at low health, screen effects (timecycle modifier, motion blur), and death consequences.
- Fully configurable tick rate, damage amounts, protection items, and particle behavior.

🐒 **Chimp Taming (Tier 3 - Franklins DJ)**
- **Befriend hostile chimps**: Using the banana item.
- **Companion behavior**: Tamed chimps follow players around and automatically spawn outside when exiting. Tamed chimps are friendly and won't attack players.
- **Monkey Business**: Complete the new Monkey Business task and earn the Golden Monkey reward by successfully taming a chimp.

😱 **Shadow Jumpscare**
- **Random jump scare**: Mysterious shadow figures spawn around players at configurable intervals.
- **Jumpscare effects**: Screen flash, camera shake, optional sound (xsound), temporary movement debuff.
- One scare per interior visit.

🧟 **Zombie Mode**
- **Master toggle** via `Config.HalloweenMadness.zombie_peds` - Enable/disable T4 exterior guard/boss zombie models.

🎭 **Hazmat Workers & Gas Masks**
- **Hazmat worker homeowners**: For T4 contaminated interiors with automatic gas mask prop attachment.

🌩️ **Halloween Weather & Time**
- **Atmospheric effects**: Halloween weather (orange/foggy) with locked time at 23:00 PM.
- Per interior toggle via `halloween_weather = true` in interior configs.

🔊 **Halloween Ambience**
- **Eerie ambient soundscapes** via xsound, configurable volume/distance.
- Per interior toggle via `halloween_ambience = true` with automatic lifecycle management.

📝 **New Translations**
Added to `locale_en.json`:
- Biohazard warnings, death messages, and protection status notifications
- Chimp taming interactions (success, failure, already tamed, missing item)

🏚️ **New Halloween Interior Themes**
Each tier now has its own Halloween theme:

- **T1**: Zombies
- **T2**: Murdered Family / Slasher
- **T3** (High-End): Aliens & UFOs / Government
- **T3** (Franklin's): Halloween Party / Chimp & Zombie Pug
- **T4**: Biohazard

**Includes major updates to all [interiors] config tables (`extra_props` and `homeowner_peds`).**

**General Fixes & Changes**
- Adjusted client-side overweight handling, added `PlyCanCarryItem` to `client/funcs.lua`, replacing the old ItemAdded and CannotAddItem event method.
- Added `Config.MaxInventoryWeight`, **this should match the max weight from your `qb-inventory` config (required for `PlyCanCarryItem`).** Not needed when using `ox_inventory`.
- Moved the `GetItemsList` function to `shared/init.lua`.
- Fixed interior door cracking unlocking the door even after failing the minigame.
- Added `Config.NotifyRep` to enable or disable reputation gain notifications.
- Added `Config.DoorHintDistance` controls distance for "It's gotta be around here somewhere" hint.
- Use `Config.ReplaceQbItemNames` to convert qb item names to ox when looting peds for better compatibility.
- Added `banana` and `gasmask` item to the provided `items_qb.lua` and `items_ox.lua` burglary files.

## Update 3.4.9

**General Fixes & Improvements**

- Prevent `ox_target` warnings from search/pickup zones, safes, and interior door cracking (should be all of them :D).

- Improved scene handling and fixed an issue where players could sometimes get stuck in the security panel scene with no minigame showing.

- Removed Franklins interior sofa fix stream (already included in bob74_ipl) - also fixes occasional crashes when restarting the resource.

- Added rep progress bar to the `ox_lib` context menu.

- Improved `AddItem` and `AddCashReward` security. All server-side item and reward events now validate incoming data structures, config, types, and coordinates to prevent exploits and unauthorized event triggers.
→ Introduced configurable `INTERACT_RANGE` constant in `items.lua`.

- Fixed "your hands are full" issue when using `ox_inventory`.

- Fixed the "Bonk A Local" task.

- Reverted Tier 4 lockdown alarm back to the original.

- Changed Tier 4 `securitycard` item label to "Access card" and updated its description.
→ `items_ox.lua` & `items_qb.lua`.

- Updated `securityphone` item description.
→ `items_ox.lua` & `items_qb.lua`.

- Fixed lockdown/alarm not resetting correctly for all group members.

- Removed the entire traitor/setup process and related code. To obtain the required `securitycard`, you must now loot dead guards - each has a chance to drop it based on `Config.T4_SecurityCardChance` in `houses/tier4.lua`.

- Notify players about looting guards for the required item when killed, based on `Config.T4_LootGuardHintChance` in `houses/tier4.lua`.

- Adjusted guard ped target handling to hopefully prevent `qb-target` CheckEntity errors.

- Prevent the exterior from loading multiple times when two or more group members enter the area simultaneously.

## Update 3.4.8

**General Fixes & Improvements**

- Fixed tool carry prop not being deleted when the item is removed from the inventory. Adjusted `tryAttachToPlayer` function in `client/carry.lua`.

- Fixed `task_complete_notify` message not displaying correctly in `locale_en.json`.

- Ensure proper particle dict cleanup in the `CreateParticle` function. Updated in `client/funcs.lua`.

- Prevent jumping while carrying props. Modified `createAndAttach` function in `client/carry.lua`.

- Fixed incorrect electric box ID being used when disabling the electric box, even when the correct target was hit, it would return and notify incorrect ID, caused by a mix up between raw ID and target ID. Updated `playAxeSmashScene` function in `client/main.lua`.

- Fixed task menu error when an item couldn't be found. Although the case was handled, a typo in the error print caused a separate unhandled error. Fixed in `GetTaskDescription` function in `client/main.lua`.

- Moved guard ped clothing style/IDs and face feature configs from `client/main.lua` to `houses/tier4.lua`. Now configured via `Config.T4_GuardFaceFeatures` and `Config.T4_GuardClothing`.

- Updated cop breaching logic, cops can now only breach if the house is currently active/busy. Adjusted `setCopHouse` function and related thread in `client/main.lua`.

- Fixed an issue where randomly assigned interiors (when no interior ID is set in the house config) weren't syncing correctly for cops. Not an issue by default, it only occurred if the `interior = 1` setting was removed to allow random interior ID selection. Adjusted `setCopHouse` function in `client/main.lua`, and `SetForCoppas` event in `client/main.lua` and `server/main.lua`.

- Fixed a bug where the house unlocked state was sometimes not reset properly. Updated `ResetHouse` function in `client/main.lua`.

Mass cleanup up and refactored various functions and events, removed unused comments, improved variable naming, and reorganised or relocated several events for better structure.
- Most client files were modified for cleanup (excluding `groups.lua` and `menu.lua`).
- `server/main.lua` was also updated.
- Adjust `Config.JobCooldown` in `shared/config.lua`.
- No changes were made to `[houses]`, `[interiors]`, `init.lua`, or `items.lua`.

Attempt better version control, 3.3.6 probably should've been 3.4, but 3.3 probably 4.0... Fack
→ Bumped version to 3.4.8

## Update 3.3.7 - Hotfix

**General Fixes & Improvements**

- Fixed an issue where cash props could be repeatedly picked up without being removed or marked as searched. This was caused by recent changes to how being overweight is handled during searches and pickups (introduced in update 3.3.6). Modified `pickupAProp` function in `main.lua`.

- Fixed after using a screwdriver to crack interior doors, attempting to pick up an item would incorrectly show a "hands are full" notification, even though the screwdriver prop had already auto removed. Modified `setDoorTarget` function in `main.lua`.

- Adjusted the coordinates of the additional main door in the T3 Franklin interior to fix a visual flicker (black flash) when viewing the door from certain angles. Modified front door coordinates in `T3_Interiors` → `extra_props` for interior ID 2.

- Added `Config.DisableSellman` to fully disable sell peds in `config.lua`.

- Added a `fix` option to the lb-phone setting in `Config.OptionalResources`. If you're using lb-phone and experiencing issues with accepting or receiving phone mail, try enabling this option. It assumes that lb-phone is passing its own mailId as param1 and the actual mail data as param2 to the AcceptJob function. Modified `AcceptJob` function in `main.lua` and `config.lua`.

## Update 3.3.6

**Bossman & Sellman**
- `sellman` peds can now be created with **passive bodyguards** via the main config.  
- **Spawn props** alongside `sellman` and `bossman` peds via the main config.  
- **Tier-specific required items** to request a job are now configurable per house in `[houses]`, and can be toggled via `Config.RequireRequestItem` / `Config.RemoveRequestedItem`.
- Improved **`sellman` price variation** - thresholds and multipliers added in the main config `Config.SupplyAndDemand`.  

**Inventory & UI Support**
- **Added support for `qs-inventory`.**  
- **Added support for `yphone`.**  
- Players **cannot sprint while carrying props**.  
- Weight check using `CanAddItem` for all search/pickup/looting actions is now actually handled and players are notified if their inventory is full.

**Loot & Boss Mechanics**
- The Tier 4 setup ped is now a **traitor guard** - bribeable or fightable (only if you refuse to pay extra) to access loot/info. Configure in `houses/tier4.lua → Config.T4_JobPrep`.
- The **heist axe** has been moved from the setup ped and is now attached to the boss guard's back - **lootable by any group member** when dead.  
- **Boss guard now carries an LMG.**  
- **All guards** are now lootable by any group member. (Fully setup but it does require configuring in `houses/tier4.lua → Config.T4_Houses → security_guards`.)  
- Configurable **tool removal chance** during break-ins and interior door cracking via `Config.ChanceOfItemRemoval` in the main config.  

**Security & Alarm**
- Interior security keypads are now **always targetable**.  
- **New indoor burglar alarm** using `xsound` for Tiers 2 & 3 (`Config.OptionalSound`) - fallback to the classic beep if disabled.  
- Updated **exterior alarm sound URL** to a permanent `xsound` link https://www.myinstants.com/media/sounds/securityalarm.mp3.  
- Fixed issues with **lockdown state** and **boss guard reset** behaviour.
- Electric box logic now **selects a random available box** to disable lockdowns, rather than matching.  
- **CCTV is now usable at any tier**, and alerts police at Tiers 1–3.

**Targeting & Interaction**
- Switched to `AddTargetEntity` for networked peds - fixed `qb-target` errors and not being able to click the target when looting dead peds.  
- Added `EntityTarget` and `RemoveEntityTarget` functions in `funcs.lua` to support clean removal for both `qb-target` and `ox_target`.  
- **House blips now include tier prefixes** (e.g., "T1 Job") with updated colors.  
- **Adjusted Tier 2 TV interaction zone** for better `ox_target` accessibility in `interior/tier2.lua → Config.T2_Interiors → pickup_zones`.  
- Improved **door enter/exit logic** overall - prevents `ox_target` duplicate zones and warning spam.  
- Ensured an animation is passed to `ox_lib` progress bars to **prevent moving while interacting** (searching, looting, etc.).
- Added a group wide busy state for search/pickup locations to prevent multiple members from interacting with the same target simultaneously.

**Group & Job Management**
- Job request mails now include **unique IDs** and **expiry timers** (`Config.MailExpiryTime`) - prevents accepting expired jobs.  
- Improved **group cleanup logic** when the leader/member disconnects, exits, or crashes.  
- Added **persistent, tier-based job cooldowns** (until server restart) via `[houses]` → `Config.T*_JobCooldownSeconds` and `Config.T*_JobCooldownThreshold`. Global cooldown settings in the main config `Config.JobCooldown`.
- Added **tier-based reputation loss on failure** via `[houses]` → `Config.T*_RepRemovalPercent`.  
- T4 setup busy state now **resets at job end**.  
- **Near house notifications** are now only shown for Tiers 2 & 3.   
- Fixed cop breach zone handling logic and prevent `house.tier nil` error.

**Animation & Props**
- **Carry animation** is now properly stopped on drop for a smoother transition instead of ClearPedTasks.  
- Players can **stow/hide tools while moving** without interrupting animations and movement.  
- Prevent **repeating animations** when picking up prop loot inside interiors (adjusted anim flags).  
- **Floating props** are now cleaned up properly after entering/exiting interiors.  
- Carry animation and prop are **cancelled if player becomes armed** (item is not removed).

**Ped & AI Behavior**
- Guards use **selected randomized ped parents, hair styles, face paint**, and other variation attributes.  
- Interior peds now **check for ownership transfer** and **retarget other players** correctly.  
  - No longer respawn when the owner leaves.  
  - Now despawn **only when the interior is empty**.  
- Prevent `CPed::SetVariation` palette ID warning.  
- Fixed **guard targeting, attributes, and relationship group resets** on owner change.  
  - Reapplies appearance features properly.  
- Improved cleanup of guards/setup peds using the **CPed pool** for force deletion.

**Config & Initialization**
- Refactored **resource initialization** in `init.lua` and both resource tables in the main config.  
- Added **missing translations** from escrowed files.  
- Moved `SendLBMail` event to a non-escrowed file: `server/items.lua`.
- On client start, triggers `police:server:UpdateCurrentCops` to hopefully force update the cop count.  

## Update 3.3.5

**Item Name Compatibility**  
- When `ox_inventory` is enabled, the script now auto-converts item names during prop pickups.  
- Added a mapping table to `config.lua` → `Config.ReplaceQbItemNames` to handle item name translations.  
- Added all items used in the T4 interior – you will now receive the correct item when using `ox_inventory`.  

**Job Menu Improvements**  
- Added an option to `config.lua` → `Config.ShowAllJobTiers` to enable or disable showing all job tiers in the job menu.  
- Job menu items are now disabled if the required level is not met and the blank menu item has been fixed. If Config.ShowAllJobTiers is disabled it will just add the unlocked tier to the job menu when the required level is met.   

**Security Minigames**
- Added **Var** and **Thermite** minigames from `ps-ui`. These can now be used in `SecurityMinigame` tables for both interior and exterior security panels.  
- Fixed an issue where the **security panel phone hacking scene** would replay and cause the player to get stuck in the hack loop after certain minigames (`circle`/`mhacking`).  

**General Fixes & Adjustments**  
- Prevented `ox_target` warnings for setup peds and prop pickups.  
- **Coffee in T3 now actually looks like coffee ☕**  
- Adjusted two models in T3 interiors that were previously inaccessible with `ox_target`.  
- Rearranged T3 **safe contents** to improve targeting and interaction.  
- **Replaced the cash stack** inside safes with a better model for `ox_target` functionality.  
- **Removed firearms from T2.**
- **T1 Motel:** Actually fixed the **interior exit** for `ox_target`, correctly adjusting `ipl_coords`.  
- **T3 High-End Interior:** Adjusted **extra main door coordinates** to prevent door glitching.    

**Interior & Loot Changes**  
- **T4 Interior:**  
  - Replaced **4 weapon models** with `ox_target` compatible ones.  
  - Added **7 new lootable items**.  
  - Added **2 brand-new items** to loot.  
- **T3 Interiors:**  
  - Replaced **4 weapon models** with `ox_target` compatible ones.  
  - Added **1 new weapon** to loot.  
- **Seasonal Cleanup:**  
  - Removed **Xmas tree** from T3 Franklin's interior.  
  - Removed **milk & cookies** from the T4 interior.  

**Group Password & Menu**  
- You can now **create/join groups with a password** (only using `sk-menu`).  
- **Revamped `sk-menu` UI** – added an option to include a textbox in menu items.

---

## **Update 3.3.4**

### **General Fixes & Improvements**  
- **Auto Resource Detection**: Added `Config.AutoResourceSetup` to automatically detect and enable required resources. Minigames are now detected based on availability.  
- **Exterior Entities Handling**: Introduced the `exteriorEntities` table to manage all exterior objects and peds, replacing `interiorEntities` and `guardEntities` for outside entities. Also refactored T4 security panel and guard handling.  
- **Guard Behavior Fixes**:  
  - Guards will no longer use vehicles to reach a target.  
  - Fixed guards not returning to patrolling or standing when they lose their target.  
  - Temporarily replaced knife/melee guards with shotguns.  

### **Carry Object Improvements**  
- Moved the carry objects table from `carry.lua` to `config.lua`.  
- Fixed an issue where props would not consistently appear when using `ox_inventory`, only showing when the carry prop was used.  
- A new thread now checks the player's inventory for the carry item when using `ox_inventory`.  

### **Sell Menu Fixes & Buyer Categories**  
- Fixed the sell menu not refreshing when all items expired or ran out.  
- Split buyers into **Tech, Collectibles, and Misc** categories, each with a unique ped. A random ped is chosen from each category on `onServerResourceStart`.  
- Added all burglary items to `Config.BuyersList` and included additional comments for configuring the buyer system. Adjust rarity, price, and other settings as needed.  

### **Targeting & Interaction Fixes**  
- **T4 Security Keypad Targeting**: Fixed an issue where targeting the T4 door security keypad with `ox_inventory` was broken due to incorrect item metadata handling.  

### **Miscellaneous Updates**  
- **Sell Menu Item Images**: Updated the `sk-menu` repository to include images for all burglary items in the sell menu.  
- **Required Cop Count Handling**: Now defined in **tiered house configs** when `Config.LevelScaling = false`.  
- **Alarm Sound Update**:  
  - Updated the link for the alarm sound MP3.  
  - Planning to replace it with YT and add a **fallback option** (likely a GTA soundtrack) in the next update.  
- **Bug Fixes & Typo Corrections**:  
  - Fixed a typo in the item image for `'figure2'` in both item.lua lists.

---

## **Update 3.3.3**

### **General Fixes & Improvements**  
- **Blacksreen Loop**: Fixed blackscreen loop when entering an interior caused by missing object or ped models. Added `IsModelValid()` and `IsModelInCdimage()` checks to prevent blackscreen loops by skipping invalid models and printing a message with the model name if `Config.PrintStuff` is enabled.
- **ox_target Security Zone**: Adjusted ox_target box zone size/pos for security keypad props.
- **ox_target T1 Exit Zone**: Fixed ox_target exit interior box zone for the T1 motel, broken in update 3.3.2 due to adjustments to `ipl_coords`.

---

## **Update 3.3.2**

### **General Fixes & Improvements**  
- **sk-menu group/member menu**: Fixed an issue where creating or joining a group using sk-menu would not display the group in the menu when ox_lib was enabled. This was caused by group data being set for the ox_lib menu instead of sk-menu, as the addGroupToMenu and addMemberToMenu functions prioritized ox_lib in `client\menu.lua`.
- **Create main interior door**: Added an additional main door for the motel T1 interior and both T3 interiors using the `extra_props` table in the interior config. This ensures there's no escape from the interior when the default interior door object is missing or fails to be retrieved, moved, or frozen. No more T1 motel void!
- **Adjusted Config.BuyersList**: Since ox_inventory does not include the `markedbills` item by default, which the `dirty_cash = true` setting in the `Config.BuyersList` relies on, dirty_cash has been set to false by default. If you want to use this feature, simply update the `dirty_item_name` to match the name of your dirty cash item in your ox_inventory item list and set dirty_cash to true.
- Added Ammo Crate and  as useable item in `server\items.lua`
- Removed Christmas trees from T2/T3

---

## **Update 3.3.1**

### **General Fixes & Improvements**  
- **ox_target Spam**: Attempted to fix the spam issue when trying to remove zones.  
- **Debugging Disabled**: Disabled some debugging elements for cleaner logs.  
- **Entity Spawn Logging**: Added print statements when attempting to spawn entities (`Config.PrintStuff`), helping identify missing models, typically due to different game versions.  
- **Error Handling:**  
  - Print error message if minigame/menu resources are missing.  
  - Fixed `ox_target` security error.  
  - Fixed `ox_lib` menu error when accessing the fence menu.  
  - Fixed `sk-menu` nil error if not present on the server.
- **Group Change Reverted**: Reverted a group change to resolve group display issues.  
- **Guard Spawning**: Slowed down guard spawning to reduce MS load.  

### **Documentation**  
- **Minigame Setup**: Updated documentation for setting up or changing minigames [Visit Minigame Documentation](https://mknzz.github.io/burglary-docs/tier_config.html#setting-up-a-new-minigame).
- **README.md**: Updated for clarity.
- **Ox Items List**: Updated to work with 3.3 `ox_items.lua`.

### **Compatability**  
- **QBox**: Fully tested and working with [QBox txAdminRecipe](https://github.com/Qbox-project/txAdminRecipe/blob/main/qbox.yaml).  
  - Ensure `ox_lib`, `ox_target`, and `ox_inventory` are enabled (`true`) in `main config.lua`.  
  - Set `qbx:enableBridge "true"` in your QBox server config to enable the QB-Core bridge for `qbx_core` (this should be true by default).

---

## **Update 3.3** - Our biggest yet!

**New Tier 4 Heist: Weapons Cache Robbery**  
- Players can now undertake a Tier 4 heist to rob a weapons cache, with rewards including weapons, attachments, materials, and more.  
- **Key Features:**  
  - Meet the boss's contact to get job details, tools, and the location of the heist.  
  - Interact with guards, avoid CCTV detection, and crack the security keypad.  
  - Introduced the *securitycard*, used alongside the *securityphone* to bypass keypads.  
    - The *securitycard* has a unique ID that must match the assigned security panel.  
    - Obtain the *securitycard* and location details from the setup ped.  
  - **CCTV detection triggers a lockdown:**  
    - A boss guard may spawn during the lockdown.  
    - Keypad access is temporarily disabled until the lockdown is resolved.  
    - Disable the lockdown by locating and smashing the correct power box using the *heistaxe*.
    - CCTV cameras can be disabled by shooting them. If a silencer is not used, a lockdown will be triggered.

**Reworked Reputation System**  
- XP required per level is now configurable via `Config.XPPerLevel` in `config.lua`.  
- Starting reputation is now set to 0 (previously 10,000).
- Revised reputation rewards based on actions and tiers:  
  - `AddRepForReason` in `func.lua` is now called from `main.lua` for actions like breaking in or bypassing security.  
  - Rewards can be configured in the `repRewards` table and `AddRepForReason` function in `funcs.lua`.  

**Outdoor Security Features**  
- Added guards, keypads, alarms, powerbox and CCTV for enhanced security.  
- Initially implemented for Tier 4 but fully adaptable for other tiers.

**New Menu**  
- Added compatibility with *sk-menu*.  
  - Recommended for auto-refreshing and smooth transitions.  
  - [Visit sk-menu GitHub](https://github.com/mknzz/sk-menu).

**New Selling Mechanic**  
- Added a fence menu for selling items.  
  - Items are randomly selected from the `Config.BuyersList` table in `config.lua` at resource start.  
  - Prices vary based on supply, demand, and rarity.  
  - Items expire after a set time or when a specified quantity is sold.  
- Optimized for *sk-menu* with auto-refreshing functionality.  

**SetupInterior Function Refactor**  
- Improved reliability by running spawning functions sequentially within a single thread.  
- Reduces blackscreen loading time and ensures safe props are created before pickup props.  

**Interior Overhaul**  
- Over 250+ new objects added across interiors, including 35 unique items for more immersive environments.  

### **Miscellaneous Improvements and Fixes**  
- Smoother animation and scene entry when interacting with a security keypad; the player ped now correctly moves towards and faces the security panel.  
- Enhanced processes for selling items and receiving marked money.  
- Disabled collision and damage for the keypad prop (`ch_prop_fingerprint_scanner_01d`).  
- Improved carry code and handling for multiple items in player inventory.  
- Added group status checks to prevent joining or leaving while busy.  
- Prevented `nil` errors from group functions.  
- Added a Cayo Perico elevator hacking scene for Tier 4 security.  
- Adjusted player ped's vertical offset during the USB hacking scene to prevent elevation issues.  
- Cleaned up and organized functions in `client/main.lua`:  
  - Improved comments and organized house and player data variables.  
- Ensured carriable items are accounted for when sold to the fence.  
- Utilized `AsNoLongerNeeded` natives to significantly reduce streaming memory usage.  
- Added `qb-inventory` to the `DefaultResources` table for better export handling.  
- Moved queue and job times to tiered `[houses]` configurations, allowing different times per tier.  
- Improved the transition from closed to open safes for the client by using a networked safe opening scene.  
- Tier 4 interior peds now have a chance to drop their current weapon when looted.  
- Improved item descriptions and updated prompts/translations.  
- Players now receive mail if no job is available.  
- Addressed potential issues with interior loading when skipping pickup props requiring a safe:  
  - The `spawnPickupProps` function now exits efficiently if no safe is present, skipping to the next pickup.  
  - Added the `DoesAnySafeExist` function and improved checks using the `interiorEntities` table.
- Updated target handling to use `ox_target's` addBoxZone export directly, if available, instead of `qb-target`.
- Updated inventory handling to use `ox_inventory's` addItem export directly, if available, instead of `qb-inventory`.
- Resolved an issue where the "Sneaky Beaky" task could complete after canceling a job without performing any actions.
- Fixed an issue where carry props were not being removed properly when using the updated `qb-inventory`.

---
