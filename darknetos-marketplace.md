---
layout: default
title: Marketplace and Delivery
parent: darknetos
nav_order: 3
---

# Erebus Market and delivery flow

## Core purchase lifecycle

The marketplace is server-authoritative. Purchases and order transitions are validated on the server, with money, inventory, and database changes happening in the `server/sv_*.lua` modules.

The normal order flow is:

```text
escrowed -> deposited -> completed
```

Additional terminal states include:

- `refunded`
- `expired`
- `cancelled`
- `seized`

## Listing flow

1. Seller creates a listing.
2. The listing is validated against fee, item, and reputation rules.
3. Buyer purchases the listing and funds are escrowed.
4. A drop location is selected.
5. Seller receives the deposit deadline.
6. Buyer receives pickup instructions after a successful deposit and any configured delay.

## Dead-drop locations

Dead-drops are defined in `Config.Delivery.locations` and selected server-side. Current repo defaults use a random strategy, enforce one active order per location, and avoid immediate vendor repeats.

## Direct transfer vs stash mode

### Direct transfer

Used when:

- `useInventoryStash = false`
- the active inventory is not `ox_inventory`
- listing-time item removal makes stash deposit unnecessary
- claim-time item granting is disabled

### Stash mode

Used when:

- `Config.Delivery.useInventoryStash = true`
- the detected inventory is `ox_inventory`

Vendor flow:

1. Interact with the dead-drop prop.
2. `prepareDepositStash` validates the order and registers `skryptzos_drop_<orderId>`.
3. The stash opens empty.
4. Seller places the ordered items inside.
5. `finalizeDeposit` validates the stash contents and moves the order to `deposited`.

Buyer flow:

1. Interact with the dead-drop prop.
2. `preparePickupStash` registers the stash and loads the ordered items.
3. Buyer removes the full contents.
4. `finalizePickup` confirms the stash is empty and completes the order.

## Offline timers and delayed pickup

If the vendor or buyer is offline at the relevant phase, deadlines are paused by storing `NULL` deadlines. When the player returns, `ActivateDeferredOrders` applies a fresh timer and sends the matching client event.

The current config also delays buyer coordinates for five minutes after deposit by default.

## Ratings and payouts

- sellers gain reputation on successful sales
- buyers can submit one rating per order
- offline seller payout and offline buyer refund are processed in timer loops
- `seller_paid` and `buyer_refunded` guard against duplicate processing

{: .note }
The current repo also supports police dead-drop seizure targets through the law enforcement flow when drop interception is enabled.