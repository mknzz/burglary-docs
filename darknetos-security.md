---
layout: default
title: PGP, Surveillance, and Terminal
parent: darknetos
nav_order: 4
---

# PGP, surveillance, and terminal workflows

## PGP messaging modes

### Simple mode

Encryption is cosmetic. Messages can still be read normally.

### Hardcore mode

Encryption state matters:

- unencrypted messages can be marked insecure
- law enforcement can intercept plaintext when permitted
- encrypted messages require the matching private key path to read

Current defaults are the stricter manual path:

- `pgpMode = 'hardcore'`
- `hardcoreAutoEncrypt = false`
- `hardcoreAutoDecrypt = false`
- `terminalKeysOnly = true`

## Key management

Main terminal commands:

- `pgp-keygen`
- `pgp-keygen --force`
- `pgp-export-pub`
- `pgp-export-priv`
- `pgp`
- `pgp status`

When `autoEnterPgp = false`, generated public keys are not auto-registered into the market account. Players must still complete the registration flow.

## Law enforcement surveillance

The surveillance stack includes:

- terminal commands `intercept` and `intercept-read <id>`
- a desktop surveillance window for law enforcement players
- optional police notification on unencrypted messages
- configurable alert routing through supported dispatch resources

By default, interception is limited to unencrypted messages and requires the player to have an allowed job while on duty.

## Dead-drop interception

The current repo also supports police seizure of deposited drops. Officers can receive a target on exposed deposited drops when `Config.LawEnforcement.dropInterception.enabled = true`.

Key controls:

- anonymity threshold gating
- chance gating
- whether the buyer is refunded
- whether the buyer and seller are notified
- whether the officer receives contraband, a generic evidence item, or nothing

## Terminal surface

Core command groups:

### Identity and status

- `whoami`
- `erebus status`
- `wallet`
- `balance`
- `rep`
- `messages`
- `inbox`

### Network actions

- `onion --start`
- `route add bridge`
- `connect <host>.onion`
- `onion-locate`
- `relay_scrub`

### PGP and law enforcement

- `pgp-keygen`
- `pgp-export-pub`
- `pgp-export-priv`
- `intercept`
- `intercept-read <id>`

### Utility

- `help`
- `clear`
- `date`
- `uptime`
- `uname`
- `ifconfig`
- `ip`

{: .important }
The user-facing branding is Onion relay, but some internal variables and compatibility names still use `tor` in the codebase.