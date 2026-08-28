# ReciprocityLock — StudioNet Runtime Testing

Contract:

```text
0x478942A99631cB3357f4480210AF9c5a9bc8c3C2
```

Explorer:

```text
https://explorer-studio.genlayer.com/address/0x478942A99631cB3357f4480210AF9c5a9bc8c3C2
```

## Wallets used

```text
Party A / creator:
0x6276095FAEA15108740445ff277fdA8c304657F4

Party B:
0xE7241B8b44e3f8a0FcCdfF6f4b76380d152F2A61

Third-party test wallet:
0x43F4f5c0946108Dc41542c8aF51E0aA0C253E701
```

## Calldata probe

Observed locally against StudioNet RPC:

```text
SHORT term: 70 chars
serialized payload: 169 bytes
eth_estimateGas: OK

LONG term: 210 chars
serialized payload: 311 bytes
eth_estimateGas: OK
```

This crossed the historical 255-byte serialization boundary successfully for `eth_estimateGas`.

## Kill-concept test

### K1 — same semantic right, collective wording

Submitted:

```text
Each side is free to end this agreement on thirty days written notice.
```

Observed:

```text
RIGHT_MIRRORED
```

### K2 — role/scope asymmetry

Submitted:

```text
The Provider may terminate on thirty days notice; the Customer may terminate the hosting module alone.
```

Observed:

```text
RIGHT_NOT_MIRRORED
```

Result:

```text
K1 PASS
K2 PASS
```

The semantic primitive survived the required kill test.

## Normal five-transaction demo

Pact:

```text
name: Hosting pact
role A: Provider
role B: Customer
right: termination on thirty days written notice
```

Pact ID:

```text
a4adc85f39492f29d209b45b01d7e62f154532383d488a438b12de34f3e6560e
```

### TX1 — create pact

Observed:

```text
SUCCESS
state = DRAFT
```

### TX2 — asymmetric term

Submitted:

```text
Either party may terminate on thirty days written notice. The Provider may also terminate immediately at its own discretion.
```

Observed:

```text
RIGHT_NOT_MIRRORED
state remained DRAFT
attempt_count = 1
```

### TX3 — try exercising before mirrored activation

Called:

```text
exercise_right(pact_id)
```

Observed rollback:

```text
[rollback] No mirrored term is active
```

This proves the semantic verdict has a real deterministic consequence: the right is not available while the pact remains DRAFT.

### TX4 — mirrored term

Submitted:

```text
Either party may terminate on thirty days written notice. Neither party has any other right to terminate.
```

Observed:

```text
RIGHT_MIRRORED
state = ACTIVE
attempt_count = 2
```

Active term ID:

```text
53c61203fdd9e9b69cbc2d54225c7387f13362839a7a31ee7c59ec4d49f83244
```

### TX5 — exercise shared right

Called by Party A.

Observed final state:

```text
state = EXITED
exited = true
attempt_count = 2
exited_by = 0x6276095FAEA15108740445ff277fdA8c304657F4
```

Final authoritative `get_pact` state confirmed the pact was permanently exited.

## Runtime conclusion

Observed StudioNet evidence confirms:

```text
RIGHT_NOT_MIRRORED does not activate the shared right
RIGHT_MIRRORED activates the shared right
exercise_right before activation reverts
exercise_right after activation succeeds
EXITED is irreversible contract state
attempt_count tracks both accepted semantic attempts
```

No result above is marked PASS based only on expected behavior; all listed verdict/state results were observed during StudioNet testing.
