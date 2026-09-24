# ReciprocityLock v1.1 — Runtime Test Plan

Expected procedures only; observed results belong in `TESTING.md`.

## Consent flow

1. Party A creates a pact: expect `PENDING`, `accepted = false`.
2. Submit before acceptance: expect rollback `Party B must accept the pact first`.
3. Unrelated wallet accepts: expect rollback `Only Party B may accept the pact`.
4. Party B accepts: expect `DRAFT`, `accepted = true`, `attempt_count = 0`.
5. Party B repeats acceptance: expect rollback `Pact has already been accepted`.

## M1–M5 semantic suite

```text
role_a_label = Controller
role_b_label = Processor
right_label  = withdrawal from the arrangement on sixty days notice
```

| ID | Text | Expected |
|---|---|---|
| M1 | The Controller may withdraw from this arrangement on sixty days notice, and the Processor is free to do the same. | `RIGHT_MIRRORED` |
| M2 | The Controller may withdraw from this arrangement on sixty days notice, and the Processor may do the same with the Controller's prior written consent. | `RIGHT_NOT_MIRRORED` |
| M3 | The Controller may withdraw from this arrangement on sixty days notice, and the Processor may do the same; the Processor must also keep an access log for six years. | `RIGHT_MIRRORED` |
| M4 | Neither the Controller nor the Processor may withdraw from this arrangement before the fixed term ends. | `RIGHT_NOT_MIRRORED` |
| M5 | The Processor may withdraw from this arrangement on sixty days notice, and the Controller has the same right or may withdraw at once on any audit failure. | `RIGHT_NOT_MIRRORED` |

Use three accepted pacts: M2/M4/M5 on pact 1; M1 on pact 2; M3 on pact 3. Record every actual verdict and transaction hash without altering the suite to fit the output.

## Additional runtime checks

- exercise while DRAFT must roll back;
- Party B exercises after M1 or M3 and `exited_by` must match Party B;
- malformed semantic output must roll back without consuming an attempt;
- spacing-only variants must map to the same term ID;
- run a signed normalized term longer than 151 characters and record full text plus transaction hash.
