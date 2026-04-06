# Steadefi - mintFee timing issue

## Core idea

Withdrawal pricing uses `totalSupply` before delayed fee minting is applied.

## What the system assumes

A withdrawal should be priced against a vault state that already reflects accrued management fees.

## Where the divergence happens

`GMXWithdraw.withdraw` computes the user’s share ratio from the current `totalSupply`, and only afterwards calls `mintFee()`.

Because `mintFee()` mints vault shares to the treasury, `totalSupply` increases after the withdrawal ratio has already been fixed.

## Impact

The withdrawing user can receive more assets than they should.

That value is effectively taken from:
- other share holders
- protocol fee recipients

The longer `mintFee()` has not been called, the larger the mispricing.

## Why this case matters

This is a protocol mechanics issue caused by stale state in a sensitive accounting path.

The bug is not in the fee formula itself, but in the mismatch between:
- when fees are economically due
- when the implementation updates supply
