---
layout: default
title: Jobs and Scenarios
parent: sk-runs
nav_order: 2
---

# Job flow and scenario configuration

## Current shipped flow

```text
Request -> Queue -> Assignment -> Steal/Find Vehicle (optional) -> Pickup -> Delivery -> Destroy Vehicle (optional) -> Completion
```

## Tier model

The current documentation describes Tier 1 Goods Running as the included implementation. Additional tiers can be added by creating new job files and registering them in the shared tier config.

Example Tier 1 defaults:

```lua
GoodsRunning.RequiredLevel = 1
GoodsRunning.JobTime = 5
GoodsRunning.QueueTime = { 0, 0 }
GoodsRunning.BaseRepReward = { 800, 1200 }
GoodsRunning.BaseCashReward = { 400, 800 }
GoodsRunning.JobCooldownSeconds = 300
GoodsRunning.JobCooldownThreshold = 3
GoodsRunning.DefaultDropCount = { 1, 1 }
```

## Pickup scenarios

### `meetPed`

Player meets an NPC contact to collect goods.

Important options:

- `requirePayment`
- `paymentAmount`
- `pickupItems`
- `stealVehicle`
- `provideVehicle`
- `vehicleModel`
- `multiplePedPickups`
- `interactDistance`
- `label`
- `icon`

## Delivery scenarios

### `meetPed`

Simple hand-off to a stationary contact.

Main controls:

- `requireVehicle`
- `duration`
- `busy`

### `meetVehicle`

Buyer vehicle arrives and the player loads packages into the trunk.

Main controls:

- `vehicleModel`
- `vehicleSpawnDistance`
- `waitTime`
- `approachSpeed`
- `bone`
- `interactDistance`

## Core job settings

Frequently adjusted global settings:

- `Config.MinPolice`
- `Config.UseTarget`
- `Config.PlayerArrivalRadius`
- `Config.DisposeVehicleToComplete`
- `Config.DeleteVehicleOnCancel`
- `Config.UseMail`
- `Config.CostToStart`
- `Config.StartCost`
- `Config.AlwaysCarry`
- `Config.DisableCarry`