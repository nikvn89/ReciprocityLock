# ReciprocityLock

> **Honest limitation.** ReciprocityLock evaluates exactly one declared bilateral right in submitted text: whether that right is granted to both declared roles under the same conditions. It does not assess fairness, legal enforceability, commercial balance, real-world identity, or whether an off-chain agreement was actually terminated. `EXITED` is only the irreversible state of this contract.

## What it does

ReciprocityLock is the Intelligent Contract behind PactMirror.

A creator declares:

- Party A and Party B
- one role label for each party
- exactly one bilateral right

The creator then submits natural-language terms. GenLayer validators return only:

```text
RIGHT_MIRRORED
RIGHT_NOT_MIRRORED
```

`RIGHT_MIRRORED` means the declared right is actually granted to both roles under the same conditions.

`RIGHT_NOT_MIRRORED` covers cases where:

- one role lacks the declared right,
- both roles lack the declared right,
- one role has broader or easier conditions,
- or one role has an additional independent route to use the declared right.

## Why GenLayer

The contract must interpret semantic equivalence under a role swap, not just keywords or sentence structure.

For example:

```text
Each side is free to end this agreement on thirty days written notice.
```

was observed on StudioNet as:

```text
RIGHT_MIRRORED
```

while:

```text
The Provider may terminate on thirty days notice; the Customer may terminate the hosting module alone.
```

was observed as:

```text
RIGHT_NOT_MIRRORED
```

The semantic output is intentionally narrow. AI decides only the verdict. The contract determines all state transitions and authorization.

## Deterministic consequence

```text
DRAFT
  |
  | RIGHT_NOT_MIRRORED
  v
DRAFT

DRAFT
  |
  | RIGHT_MIRRORED
  v
ACTIVE
  |
  | exercise_right by Party A or Party B
  v
EXITED
```

Once a mirrored term becomes active, no further term may be submitted.

`exercise_right` is deterministic and one-shot. Either declared party may call it. The first successful call records `exited_by` and permanently moves the pact to `EXITED`.

## Multi-tenant

Any wallet may create its own pact.

There is:

```text
no global admin
no deployer privilege
no clock
no token
no external web source
```

The pact ID includes the creator address, so different wallets can independently use the same pact name.

## Authorization

```text
create_pact      any wallet
submit_term      Party A / creator only
exercise_right   Party A OR Party B only
```

## Anti-replay / grinding controls

- exact term replay is rejected by content-addressed `term_id`
- maximum 5 term attempts per pact
- every converged attempt remains in the attempt log
- once `RIGHT_MIRRORED` activates a term, further submission is closed

## StudioNet deployment

```text
Contract:
0x478942A99631cB3357f4480210AF9c5a9bc8c3C2

Explorer:
https://explorer-studio.genlayer.com/address/0x478942A99631cB3357f4480210AF9c5a9bc8c3C2
```

## Runtime result

The kill-concept pair and the normal 5-transaction flow were executed successfully on StudioNet. See `TESTING.md`.
