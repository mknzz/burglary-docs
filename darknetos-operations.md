---
layout: default
title: Operations and QA
parent: darknetos
nav_order: 5
---

# Operations and QA

## Recommended release checks

### Setup

- SQL imports cleanly
- resource starts without runtime errors
- framework and inventory detection log appears on startup

### Access and desktop

- `/erebus` or laptop item opens the OS
- boot animation completes and NUI focus is restored correctly on close
- Network Monitor, Market, Terminal, and any law enforcement windows open correctly
- settings panel opens, persists, and closes on outside click or `Escape`

### Network and anonymity

- bridge, firewall, VPN, and proxy chain actions respect challenge rules
- anonymity bonuses and penalties recalculate correctly
- threat level updates with the current player posture
- event log entries appear in network monitoring data

### Market and delivery

- listing creation, purchase, vendor deposit, buyer pickup, and seller payout complete cleanly
- stash mode works in `ox_inventory`
- fallback direct transfer still works when stash mode is unavailable
- offline deposit and pickup timer resumption behave correctly
- expiry, refund, and cancellation clean up stashes and props

### PGP and surveillance

- key generation and export work
- manual encrypt and manual decrypt flows work in hardcore mode
- unencrypted messages are flagged insecure
- law enforcement players can access surveillance tools while non-LE players are denied

### Terminal and rate limits

- common commands respond correctly
- unknown commands fail cleanly
- cooldowns apply to purchases, listings, relay scrub, registration, and intercept queries

## Operational notes

- the server files rely on load order and bare globals across `server_scripts`
- the client files rely on `DNC` being defined in `client/main.lua` before later files load
- the NUI JavaScript files rely on the `window.App` IIFE load order from `core.js` through `app.js`
- do not bypass the `FW.*` adapter with direct QBCore or ESX calls

## Current known doc caveats

- some older version labels in the repo are behind the live feature set
- the live config defaults differ from older README snapshots in several places
- local browser previews do not perfectly match in-game CEF sizing for the laptop shell