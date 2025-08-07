---
layout: default
title: Patch Notes
nav_order: 6
---

# Patch Notes for sk-burglary

Latest patch notes for both escrow and full source versions.

# Update 3.4.8 - Latest

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
