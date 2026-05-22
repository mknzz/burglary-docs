---
layout: default
title: skryptzOS
nav_order: 3
has_children: true
---

1.0.0
{: .label .label-green }

# skryptzOS

skryptzOS simulates a darknet operating system with a built-in marketplace called Erebus Market. It uses a server authoritative design, a shared `FW.*` adapter for QBCore, QBox, and ESX, and a NUI desktop that hosts the market, terminal, messaging, and law enforcement tooling.

## What is included

- SKryptzOS desktop shell with boot sequence, windows, tray, and settings panel
- Erebus Market listings, purchases, vendor ratings, and reputation
- Dead-drop delivery flow with direct transfer or ox_inventory stash mode
- Anonymity systems for bridge, firewall, VPN, proxy chains, and threat level
- PGP messaging with simple and hardcore modes
- Law enforcement interception, surveillance app, and alert routing
- Terminal command surface for onboarding, network actions, status, and key export

## Start here

- [Install]({{ '/darknetos-install.html' | relative_url }})
- [Configuration]({{ '/darknetos-config.html' | relative_url }})
- [Marketplace and Delivery]({{ '/darknetos-marketplace.html' | relative_url }})
- [PGP, Surveillance, and Terminal]({{ '/darknetos-security.html' | relative_url }})

## Architecture

### Shared

- `config.lua`
- `shared/framework.lua`
- `shared/onion.lua`

### Client

- `client/main.lua` manages the `DNC` state table, NUI open/close, and keybinds
- `client/delivery.lua` handles dead-drop props, interactions, blips, and police seize flow
- `client/callbacks.lua` proxies all NUI callbacks to the server
- `client/anim.lua` handles laptop use animation

### Server

- `server/sv_state.lua` global state and callback registration
- `server/sv_utils.lua` helpers, anonymity math, and persistence
- `server/sv_crypto.lua` challenge + fake PGP helpers
- `server/sv_drops.lua` drop selection and onion discovery helpers
- `server/sv_player.lua` player lifecycle and identifier mapping
- `server/sv_item.lua` usable laptop item registration
- `server/sv_police.lua` law enforcement authorization and dispatch
- `server/sv_market.lua` marketplace, order lifecycle, stash flow, and ratings
- `server/sv_messaging.lua` inbox and interception callbacks
- `server/sv_terminal.lua` terminal command dispatch
- `server/sv_timers.lua` timeouts, decay loops, payouts, and cleanup