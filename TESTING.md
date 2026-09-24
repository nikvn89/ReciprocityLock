# ReciprocityLock v1.1 — Test Results

## Final status

| Gate | Result |
|---|---|
| Python source compilation | **PASS** |
| Contract invariant checks | **PASS — 12/12** |
| M1–M5 shortcut analysis | **PASS** |
| v1.1 StudioNet deployment | **PASS — GenVM SUCCESS, consensus Accepted** |
| v1.1 consent flow | **NOT RUN** |
| v1.1 M1–M5 semantic runtime | **NOT RUN** |
| v1.1 signed long-write runtime | **NOT RUN** |

The exact v1.1 contract was deployed at `0x8F84adB020C953a1415Cc4ac5eF2617Ec97DBb12`. Deploy transaction `0x0294f79384c1942f57d14a9299704c1d1c99a8773a040be2f642131deae39b4a` reached GenVM `SUCCESS` and consensus `Accepted`. The accepted `get_config` response reports version `1.1` and the `PENDING` state. The consent flow, M1–M5 runtime suite, and signed long write remain `NOT RUN`.

No calldata-size or gas-estimation result is presented as GenVM runtime evidence. A signed long write remains `NOT RUN` until the exact text and transaction hash are recorded on the v1.1 address.

Run:

```bash
python3 -m py_compile ReciprocityLock.py
python3 tests/verify_contract.py ReciprocityLock.py
python3 tests/pm_kill.py ReciprocityLock.py
```

Observed:

```text
contract SHA-256: f98b294afb5fc1122af2b7678fef9f8b25bcb4a481df48626d0ca5d41ec6e3d9
contract invariants: 12/12 PASS
single-token separators: none
single-bigram separators: none
maximum rubric-line Jaccard similarity: 0.111
```

Every future runtime PASS must include the v1.1 address and transaction hash.
