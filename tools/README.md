# tools/

Case-authoring scripts. These generate the case *inputs* under `../cases/`;
they do not produce the expected-output fixtures — those are made in the SWAP
repo (`tests/regression/regen_reference.py`) and committed there, because a
fixture is an assertion about a specific engine build.

- `gen_switch_cases.py` — derive the crop-switch coverage cases from
  `cases/hupselbrook/` by toggling one crop-file option each.
- `gen_system_cases.py` — derive the system/hydrology-switch cases
  (hysteresis, snow, frost) from `cases/hupselbrook/`.
- `met_to_csv.py` — convert a legacy `.met` meteo file to the modern CSV form.

Run from anywhere; paths resolve relative to the repo root:

```
python tools/gen_switch_cases.py            # all switch cases
python tools/gen_system_cases.py winter     # one system case
```

Both generators are idempotent — re-running them against the committed cases
produces no diff. After (re)generating a case, produce its fixture in the SWAP
repo and register it in `tests/regression/test_output_regression.py`.
