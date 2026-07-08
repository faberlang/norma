# Factory documentation (norma)

Open factory tracks for the public **Norma** source library (`src/**/*.fab`).

Relocated from private Radix on 2026-07-08.

## Layout (current)

```text
norma/
  src/                 public norma:* modules
  docs/factory/        this control plane
  scripta/             source checks
# siblings
  ../faber             public CLI
  ../radix             private compiler / host docs
  ../faber-runtime     Rust runtime carriers (not Norma source)
```

```bash
./scripta/check-source
```

Each `goal.md` owns its **Status** line.
