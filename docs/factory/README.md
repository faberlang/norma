# Factory documentation (norma)

Open factory tracks for the public **Norma** source library (`src/**/*.fab`).
The `src/` tree is also the cista package interface root and must remain
`.fab`-only.

Relocated from private Radix on 2026-07-08.

## Active campaigns

- [Native Faber Crypta](native-faber-crypta/CAMPAIGN.md) — Stages 1 and 2 are
  goal-check READY and lowered. Stage 1 is ready for Radix factory execution.

## Ready goals

- [Native Faber SHA-2](native-sha2/goal.md) — delivery
  [saved](native-sha2/delivery.md); factory execution waits for acceptance of
  the complete unsigned modular-word family.

The Native Faber SHA-2 goal supersedes the former SHA-256-only receipts plan.

Recent status/audit packets:

- `host-gateway-claim-gate-audit.md` - Host Gateway/Norma public-claim gate
  audit and P1 defer recommendation.
- `provider-source-claim-matrix.md` - Source-library/provider claim matrix with
  allowed claims, blocked claims, promotion triggers, and validation commands.
- `nondum-mori-deferral-residual.md` - Exact repeatable inventory for deferred
  `mori "norma:... deferred pending Stage ..."` stubs and the Radix SEM017
  hard gate for free-function `@ nondum` cleanup.

## Layout (current)

```text
norma/
  src/                 public norma:* .fab modules and package interface root
  exempla/             non-interface examples/tests
  docs/factory/        this control plane and planning notes
  scripta/             source checks
# siblings
  ../faber             public CLI
  ../radix             private compiler / host docs
  ../faber-runtime     Rust runtime carriers (not Norma source)
```

```bash
./scripta/check-source
./scripta/audit-deferred-mori
```

Each `goal.md` owns its **Status** line.
