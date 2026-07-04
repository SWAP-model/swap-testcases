# swap-testcases

Regression **test-case inputs** for the [SWAP](https://github.com/SWAP-model/SWAP)
soil–water–atmosphere–plant model. This repository holds only the *inputs*
(and observed field data) for each scenario. The **expected outputs**
(`*_expected_gfortran.json` fixtures) and the regression runner live in the
SWAP repository — a fixture is an assertion about a specific engine build, not a
property of the case, so it travels with the engine.

## Why a separate repository

The cases are a stable data dependency shared by SWAP's regression suite (and,
in future, by pyswap and the coupling tooling). Keeping them out of the engine
repo keeps the engine tree lean and lets the data be versioned and pinned on its
own cadence. This mirrors how MODFLOW 6 keeps its test models in dedicated
`modflow6-testmodels` repositories.

Consumers **pin a release tag** of this repo (never a bare commit — an earlier
SWAP submodule broke when its pinned commit was garbage-collected upstream).
Tags on releases are permanent; that is the contract.

## Layout

```
cases/<name>/
    legacy/     legacy ASCII inputs — the swap420gf 4.2.0 oracle reads these
    toml/       modern TOML + CSV companion inputs — the modernized engine reads these
    observed/   measured field data shipped with the SWAP 4.2.0 release (where available)
archive/
    macroporeflow/   retired case (macropore physics removed per SWAP ADR 0040),
                     kept for a future macropore feature arc
docs/
    CASE_RECONSTRUCTION.md   how the reconstructed cases were rebuilt from legacy ASCII
```

Each case carries both a `legacy/` and a `toml/` tree so the same scenario can
be driven through the 4.2.0 reference build and the modernized build and
compared byte-for-byte.

## Provenance

- `hupselbrook`, `grassgrowth`, `oxygenstress`, `salinitystress`, `surfacewater`
  originate from the six cases shipped with the public **SWAP 4.2.0** release.
  Their modern TOML inputs were reconstructed from the legacy ASCII inputs (meteo
  `.met` → CSV, prescribed-GWL tables → `gwl_file` CSV) and validated
  byte-identical against the 4.2.0 gfortran oracle. See
  `docs/CASE_RECONSTRUCTION.md`.
- The `sw*` cases (`swrd2`, `swharv1`, `swcompensate1/2`, `swinter2/3`, `swcf3`,
  `swcf3_maize`, `swsalinity1`, `swoxygen2`, `swdrought2`), `snow`,
  `soilhysteresis`, `winter`, `winterhysteresis` are single-switch coverage
  cases derived from `hupselbrook`, each toggling one SWAP option.
- `observed/` data for grassgrowth, salinitystress, and surfacewater comes from
  the original SWAP 4.2.0 release distribution.

## Using the cases with SWAP

SWAP's regression runner discovers this repo via `--cases-path` /
`SWAP_TESTCASES_PATH`, defaulting to a sibling `../swap-testcases/cases`
checkout. Clone it next to your SWAP checkout:

```
~/code/SWAP
~/code/swap-testcases      # cases/ resolved automatically
```

## License

GPL-2.0, matching the SWAP model. See `LICENSE`.
