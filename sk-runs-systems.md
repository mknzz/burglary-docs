---
layout: default
title: Rewards, Systems, and Progression
parent: sk-runs
nav_order: 3
---

# Rewards, systems, and progression

## Reward structure

sk-runs supports two reward phases:

- per-delivery rewards
- job-completion rewards

Each phase can award:

- cash
- reputation
- normal item rolls
- rare item rolls

Tier-specific rewards can also be enabled through the tier config.

## Carry system

Players can visually carry configured props while handling package items.

Features:

- auto attach when item is picked up
- auto remove when item is dropped
- optional movement restrictions
- weapon suppression while carrying
- network sync for NPC-held props

Carry props are configured in `Config.CarryProps` with model, bone, position, rotation, and animation settings.

## Trunk attachment system

Vehicle drop-offs can attach package props to vehicle trunks. Position lookup order is:

1. vehicle + prop
2. vehicle + default
3. default vehicle + prop
4. global default

This allows precise support for vehicles like `speedo` and `fq2` while retaining a safe fallback.

## Cooldowns and police pressure

Cooldowns can be configured globally and per tier.

Important controls:

- `Config.JobCooldown.Enabled`
- `Config.JobCooldown.PerPlayer`
- `Config.JobCooldown.Global`
- `Config.JobCooldown.CooldownSeconds`
- per-tier `JobCooldownSeconds`
- per-tier `JobCooldownThreshold`

Police alerts support per-scenario base chances, tier multipliers, random alert delays, and multiple dispatch integrations.

## Dirty money cleaning

Marked bills or other configured dirty-money items can be cleaned during deliveries and job completion phases.

Important controls:

- `Enabled`
- `ChancePerDelivery`
- `ChanceOnCompletion`
- `ExchangeRate`
- `MaxCleanAmount`
- `DirtyMoneyItems`

## Reputation and leveling

Current progression model:

- configurable `RepPerLevel`
- configurable `MaxLevel`
- level-based tier chance scaling for future tiers
- reputation from deliveries, completion, and tier reward tables

This is the main gate used to unlock better jobs and future tier chances.