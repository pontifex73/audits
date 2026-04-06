# Basin - composability break in rate capping

## Core idea

The pump accepts an external Well Function abstraction, but part of its rate-capping logic falls back to a hardcoded reserve ratio formula.

## What the system assumes

Basin is built to support composable Wells, where different Well Functions can define reserve relationships through their own logic.

That means pump logic should consistently rely on the provided Well Function when reasoning about rates.

## Where the divergence happens

`MultiFlowPump._capRates` uses the provided Well Function to compute reserve rates, but later checks capped reserves with an inline ratio calculation instead of using the Well Function abstraction again.

This makes the logic depend on a specific rate model, even though the surrounding design is meant to support arbitrary Well Function implementations.

## Impact

Rate capping can behave incorrectly for Well Functions whose logic is not equivalent to the hardcoded reserve ratio formula.

That breaks the intended composability of the system and can lead to incorrect protocol behavior in non-default integrations.

## Why this case matters

This is a protocol design consistency issue.

The abstraction boundary exists, but the implementation does not fully respect it.

As a result, composability is supported at the interface level, but partially broken in execution.
