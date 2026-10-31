# Environment evidence

Original and current environments are different.

| Scope | Confirmed record |
|---|---|
| Formal training | Saved per-run `environment.json`: Python 3.10.20; NumPy 2.2.6; pandas 2.3.3; scikit-learn 1.7.2; PyTorch 2.5.1+cu121; CUDA 12.1; NVIDIA A800-SXM4-80GB. |
| Generator replay environment | Existing `provenance/generator_historical_regeneration_audit.json` in R1-1: Python 3.13.12, NumPy 2.5.0, pandas 3.0.3. This replay record is not a complete contemporaneous lockfile for every historical run. |
| Historical A / TranAD | Per-run config, profile and available historical records retained. Complete original dependency lock/source binding and all stdout records were not preserved. Do not fill missing values from the current environment. TranAD is the local full-window-target adaptation. |
| R3 mechanism training | Available exact environment record is retained in `experiments/R3_2_shuffled_context_mechanism/audit/training_environment_exact.txt`; consult it rather than treating it as all historical environments. |

The root requirements.txt is an unpinned project dependency list, not a reproducibility lockfile. Frozen implementation snapshots and per-run environment records take precedence. No dependency installation or accelerator test was performed during packaging. GPU UUIDs and private interpreter paths are redacted in public copies. Hardware capacity does not establish original driver version or deterministic behavior on other devices.

Current packaging environment (metadata inspection only):

```json
{
  "python": "3.13.12",
  "platform": "Linux x86_64",
  "packages": {
    "numpy": "2.5.0",
    "pandas": "3.0.3",
    "torch": "NOT INSTALLED in packaging interpreter",
    "scikit-learn": "NOT INSTALLED in packaging interpreter"
  }
}
```

The static archive tools use Python standard library only; the build audit also used NumPy for safe non-object array metadata inspection. They do not require loading the training framework.
