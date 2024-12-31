---
layout: default
title: Patch Notes
nav_order: 6
---

# Patch Notes for sk-burglary

Latest patch notes for both escrow and full source versions.

## **Update 3.3.1 - Latest**

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
- - **Ox Items List**: Updated `ox_items.lua`.

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