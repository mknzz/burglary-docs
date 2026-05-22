---
layout: default
title: Configuration
parent: darknetos
nav_order: 2
---

# darknetos configuration reference

All main settings live in `config.lua`. The values below reflect the current repository defaults rather than older README values.

## General

| Key | Current default | Notes |
|---|---|---|
| `Config.Command` | `erebus` | Chat command to open the OS |
| `Config.PanicKey` | `F10` | Panic wipe keybind |
| `Config.OSName` | `skryptzOS` | Boot and terminal branding |
| `Config.MarketName` | `Erebus Market` | Market UI branding |
| `Config.Framework` | `auto` | Auto-detects QBCore, QBox, or ESX |
| `Config.MoneyType` | `crypto` | QBCore/QBox XRC balance source |
| `Config.Debug` | `true` | Prints debug information |
| `Config.HideHints` | `true` | Reduces onboarding hints in UI and terminal |

## Marketplace

| Key | Current default | Notes |
|---|---|---|
| `listingFeeXRC` | `0.5` | Listing creation fee |
| `registrationFeeXRC` | `50.0` | Market account registration fee |
| `listingExpiryHours` | `48` | Listing auto-expiry |
| `requireItem` | `true` | Player must hold the listed item |
| `removeItemOnList` | `false` | Item remains with seller until deposit |
| `giveItemOnClaim` | `true` | Buyer receives item on successful pickup |
| `requirePGP` | `true` | Registration requires PGP setup |
| `autoEnterPgp` | `false` | Manual public key registration by default |
| `pgpMode` | `hardcore` | Enables meaningful encryption consequences |
| `hardcoreAutoEncrypt` | `false` | Sender can be required to paste recipient pubkey |
| `hardcoreAutoDecrypt` | `false` | Recipient can be required to paste private key |
| `terminalKeysOnly` | `true` | Key export stays in terminal by default |
| `minAnonToTrade` | `85` | Minimum anonymity to buy or sell |

## Anonymity and threat model

| Key | Current default | Notes |
|---|---|---|
| `base` | `35` | Starting anonymity |
| `bridgeBonus` | `15` | Added when bridge is enabled |
| `firewallBonus` | `15` | Added when firewall is enabled |
| `vpnBonus` | `15` | Added when VPN is enabled |
| `proxyChainsBonus` | `10` | Per hop anonymity bonus |
| `maxProxyHops` | `2` | Maximum configured hops |
| `torVpnConflict` | `true` | Enables Onion + VPN penalty |
| `torVpnPenalty` | `5` | Penalty applied when both are active |
| `decayStorageThreshold` | `50` | Storage threshold for anonymity decay |
| `policeAlertThreshold` | `25` | Low anonymity alert threshold |

Threat level is enabled by default and maps the active risk factors into five bands: `SECURE`, `GUARDED`, `ELEVATED`, `HIGH`, and `CRITICAL`.

## Delivery

| Key | Current default | Notes |
|---|---|---|
| `vendorDepositWindow` | `300` | Vendor deposit deadline in seconds |
| `buyerPickupWindow` | `300` | Buyer pickup deadline in seconds |
| `interactionSystem` | `auto` | Detects `ox_target`, then `qb-target`, else TextUI |
| `interactionRadius` | `2.5` | Interaction radius |
| `progressBarDuration` | `3000` | Deposit/pickup progress duration |
| `useInventoryStash` | `true` | Enables ox_inventory stash mode |
| `stashSlots` | `5` | Stash slot count |
| `stashMaxWeight` | `50000` | Stash max weight |

Pickup-delay withholding is also enabled by default:

```lua
pickupDelay = {
    enabled = true,
    waitSeconds = 300,
    pendingMsg = 'Drop secured - pickup coordinates transmitting in ~%d min(s).',
}
```

## Reputation and rate limits

Current defaults:

- `Config.Reputation.saleGain = 1.0`
- `Config.Reputation.decayPerMin = 0.005`
- `Config.Reputation.failPenalty = 2.0`
- `Config.Reputation.cancelPenalty = 1.0`

Important cooldowns:

- purchases: `5000 ms`
- new listings: `3000 ms`
- `relay_scrub`: `60000 ms`
- market registration: `10000 ms`
- `pgp-keygen`: `10000 ms`
- terminal market stats and intercept queries: `10000 ms`

## Law enforcement and dispatch

Law enforcement tooling is enabled by default, requires the `police` job, and requires on-duty status.

Alert defaults currently use very permissive thresholds and chances for most events. The main alert types are:

- `drop_deposit`
- `drop_pickup`
- `purchase`
- `low_anonymity`
- `unencrypted_msg`

Dispatch auto-detection scans:

- `ps-dispatch`
- `cd_dispatch`
- `core_dispatch`
- `qs-dispatch`
- `rcore_dispatch`
- `linden_outlawalert`
- `origen_police`

## Challenges and boot sequence

The current challenge defaults are:

| Action | Type | Difficulty | Failure |
|---|---|---|---|
| Market unlock | `distorted` | `medium` | `retry` |
| Bridge | `wire` | `easy` | `cooldown` |
| Firewall | `memory` | `easy` | `cooldown` |
| VPN | `sequence` | `easy` | `retry` |
| Proxy chain | `maze` | `hard` | `cooldown` |
| Relay scrub | `math` | `hard` | `cooldown` |

Boot sequence defaults:

```lua
Config.Boot = {
    durationMs = 1000,
    label = 'Booting ' .. (Config.OSName or 'SKryptzOS') .. '...',
}
```