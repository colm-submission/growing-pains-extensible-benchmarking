# Growing Pains: Extensible and Efficient LLM Benchmarking Via Fixed Parameter Calibration


Code for the paper Growing Pains: Extensible and Efficient LLM Benchmarking Via Fixed Parameter Calibration


## Repository layout

```
config/
  constants.py              sweep grids, dataset lists, anchor methods
  data_sources.py           data source configuration builders
  data_source_config.json
  experiment_presets.yaml

src/
  chain_experiment.py       entry point — runs one chain calibration experiment
  calibration.py            base frame calibration, fixed-anchor calibration, anchor selection
  data_loading.py           dataset loading and grouping
  evaluation.py             MAE and Spearman rho evaluation, baselines
  io.py                     shared I/O helpers
  irt/                      IRT engine: MIRT 2PL training, anchor selection, theta estimation

scripts/
  run_experiments.py        orchestrator for anchor count and reference model sweeps

data/
  input/                    benchmark response files
  output/                   experiment results written here

tests/
  demo_test.py              fast synthetic IRT test + optional real-data test
```

## Setup

```bash
uv venv .venv --python 3.11 && source .venv/bin/activate
uv pip install -r requirements.txt
uv pip install -e .
```

## Data

Benchmark response files (`lb.pickle`, `mmlu_fields.pickle`) are included in `data/input/`. The item-level response data is from Polo et al. (2024b), "tinyBenchmarks: evaluating LLMs with fewer examples" (ICML 2024).

## Getting Started

```bash
# Verify the IRT engine works on synthetic data (~5 seconds)
python tests/demo_test.py --fast

# Verify on real data (requires data/input/ to be populated)
python tests/demo_test.py --full

# Run a single chain calibration experiment
python src/chain_experiment.py --help
```

## Reproducing Experiments

List available experiments:

```bash
python scripts/run_experiments.py --list
```

Run an experiment (preview first, then execute):

```bash
python scripts/run_experiments.py --experiment lb_anchor_sweep --dry-run
python scripts/run_experiments.py --experiment lb_anchor_sweep
```

Available experiments: `lb_anchor_sweep`, `lb_refmodel_sweep`, `mmlu_anchor_sweep`, `mmlu_refmodel_sweep`.

Results are written to `data/output/`. On a SLURM cluster, add `--dispatch sbatch` to submit one job per run.

