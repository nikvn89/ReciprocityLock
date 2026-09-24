# ReciprocityLock v1.1

ReciprocityLock is the GenLayer Intelligent Contract behind PactMirror. It evaluates exactly one declared bilateral right: whether that right is granted to both declared roles under the same conditions.

> **Honest limitation.** ReciprocityLock does not assess overall fairness, legal enforceability, commercial balance, real-world identity, or whether an off-chain agreement was actually terminated. `EXITED` is only the irreversible state of this contract.

The mirrored right in V1 is shared and one-shot. This is suitable for termination/withdrawal rights and would mis-model independently repeatable rights such as audit or inspection rights.

The rubric is public through `get_rubric`, so semantic grinding is not a closed problem. A creator can aim at the rubric within the five-attempt cap. The deterrents are the small cap, permanent attempt history, and the fact that a successful mirrored term also arms the counterparty with the same one-shot right.

At an assumed independent model error rate of 10%, the probability of at least one erroneous mirrored result in five attempts is `1 - 0.9^5 ≈ 0.41`.

## State machine

```text
PENDING -- accept_pact by Party B --> DRAFT
DRAFT   -- RIGHT_NOT_MIRRORED     --> DRAFT
DRAFT   -- RIGHT_MIRRORED         --> ACTIVE
ACTIVE  -- exercise_right by A/B  --> EXITED
```

Party B is nominated by the creator but must submit a separate on-chain acceptance before any semantic term can be submitted. This proves address-level participation, not independent real-world identity; one person can still control both wallets.

## AI boundary

Validators may return only:

```text
RIGHT_MIRRORED
RIGHT_NOT_MIRRORED
```

Malformed JSON, a non-object response, a missing verdict, or an unknown label raises `Invalid semantic output`. The transaction rolls back, so `attempt_count` does not increase and no fabricated verdict is stored.

## Deterministic controls

- Party B must be nonzero and different from the creator.
- Only Party B can accept a pending pact.
- Only the creator can submit terms after acceptance.
- All Python whitespace runs collapse before term hashing.
- Exact normalized replay is rejected.
- At most five converged attempts are stored.
- A mirrored term closes further submission.
- Either declared party may exercise the active right once.
- Reserved prompt tokens are rejected, not sanitized.

## StudioNet v1.1 deployment

```text
Contract: 0x8F84adB020C953a1415Cc4ac5eF2617Ec97DBb12
Deploy tx: 0x0294f79384c1942f57d14a9299704c1d1c99a8773a040be2f642131deae39b4a
Explorer: https://explorer-studio.genlayer.com/address/0x8F84adB020C953a1415Cc4ac5eF2617Ec97DBb12
```

Observed: GenVM `SUCCESS`, consensus `Accepted`, and accepted `get_config` reports version `1.1` with the `PENDING` state.

Historical v1.0 address, not valid as v1.1 evidence:

```text
0x478942A99631cB3357f4480210AF9c5a9bc8c3C2
```

See `TEST_PLAN.md` for the post-deployment procedure and `TESTING.md` for observed results only.
