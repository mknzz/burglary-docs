---
layout: default
title: Framework Functions and Troubleshooting
parent: sk-runs
nav_order: 4
---

# Framework functions and troubleshooting

## Public client functions

The public framework layer is designed so you can adapt sk-runs to your own stack without rewriting the main job flow.

Main documented client helpers:

- `HasItem(items, amount)`
- `Notify(message, type, duration)`
- `AddInteraction(coords, options, id)`
- `RemoveInteraction(id)`
- `ProgressBar(label, duration, useWhileMoving, canCancel, disableControls, callback)`
- `CircleSkillbar(minigameData, callback)`
- `GetPoliceCount()`
- `HasEnoughPolice()`
- `IsVehiclePlayerOwned(vehicle)`
- `SendPoliceAlert(alertType, coords, tier)`
- `TriggerPoliceDispatch(alertType, coords)`

## Public server functions

Main documented server helpers:

- `AddItem(src, itemName, amount, info)`
- `RemoveItem(src, itemName, amount)`
- `SendMail(src, subject, sender, message, button)`
- vehicle ownership callback for `running:server:IsVehicleOwned`

## Troubleshooting

### Job not starting

Check:

- minimum police requirement
- start-cost settings
- active cooldowns
- framework or inventory setup mismatches

### Carry props not showing

Check:

- `Config.CarryProps`
- `Config.AlwaysCarry`
- `Config.DisableCarry`
- valid model and animation names

### Trunk attachment issues

Check:

- vehicle entry in `Config.TrunkPositions`
- prop model match
- bone and offset values

### Rewards or mail not working

Check:

- reward tables are enabled
- phone integration is enabled and the correct optional resource is selected
- only one phone path is active at a time

### Police alerts not firing

Check:

- `Config.PoliceAlert.Enabled`
- configured dispatch system name
- alert chances above zero
- the dispatch resource is actually started

{: .note }
For deeper customization, the documented source-of-truth files are `client/framework_public.lua` and `server/framework_public.lua` in the sk-runs resource.