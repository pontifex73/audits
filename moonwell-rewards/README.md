# Moonwell - liquidator rewards inconsistency

## Core idea

A balance migration path bypasses the normal transfer accounting and lets the liquidator accrue rewards against a balance that did not exist during the earlier period.

## What the system assumes

Supplier rewards should be updated before a user’s balance change becomes effective for future accrual.

## Where the divergence happens

`MErc20DelegateFixer.fixUser` transfers mTokens from the liquidated account to the liquidator by directly modifying balances.

This bypasses the standard transfer path, where the protocol first updates supplier rewards for both sides of the transfer.

As a result, the liquidator’s reward index is left stale while their balance increases.

## Impact

The liquidator can receive more rewards than intended.

In this case, that can also create downstream governance effects, since the liquidator account may gain unexpected reward-backed voting power.

## Why this case matters

This is not just a rewards bug.

It is a protocol mechanics issue where:
- balance state changes correctly
- reward accounting does not move with it

The inconsistency comes from a special execution path that bypasses the logic relied on by the protocol’s reward model.
