# Recurrent Quantum Feature Maps for Reservoir Computing

[![arXiv](https://img.shields.io/badge/arXiv-2604.03469-b31b1b.svg)](https://arxiv.org/abs/2604.03469)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Python 3.9+](https://img.shields.io/badge/python-3.9+-blue.svg)](https://www.python.org/)
[![Qiskit](https://img.shields.io/badge/Qiskit-IBM%20Quantum-6929C4)](https://qiskit.org/)

Official code repository for the paper:

> **Recurrent Quantum Feature Maps for Reservoir Computing**  
> Utkarsh Singh, Aaron Z. Goldberg, Christoph Simon, Khabat Heshami  
> *arXiv:2604.03469 [quant-ph]*, April 2026  
> [https://arxiv.org/abs/2604.03469](https://arxiv.org/abs/2604.03469)

---

## Overview

Reservoir computing is a fast training paradigm for temporal tasks where only a linear readout is trained on top of a fixed dynamical system. This work introduces a **quantum reservoir** built from **recurrent quantum feature maps**: at each timestep, a fixed quantum circuit encodes the current input *and* a classical feedback signal from the previous reservoir output, producing a 512-dimensional state vector (9 qubits) used for prediction.

Key contributions:
- Proposes the **CP feature map** as the quantum encoding for reservoir computing
- Outperforms classical baselines (ESN, MLP) and ZZFeatureMap-based QRC on Mackey-Glass time-series prediction
- Analyzes **memory capacity** and its relationship to entanglement
- Conducts a systematic **noise robustness study** across 1Q/2Q depolarizing, readout, and relaxation (T1) noise channels

---

## Repository Structure

```
.
├── backend.py              # Qiskit backend configuration
├── circuits.py             # Quantum circuit definitions (CPMap, ZZFeatureMap)
├── datasets.py             # Mackey-Glass time-series data generation
├── ESN.py                  # Echo State Network baseline
├── properties.py           # Circuit/reservoir property calculations
├── reservoirs.py           # Core quantum reservoir class
├── utility.py / utils.py   # Shared helper functions
│
├── CPQRC/                  # Jupyter notebooks reproducing all paper results
│   ├── CPRC_MG_Noisy.ipynb                          # Noisy simulation
│   ├── Ent_vs_MC.ipynb                              # Entanglement vs memory capacity
│   ├── Memory_capcity_ESN_different_parameters.ipynb
│   ├── data_10000/
│   │   ├── ph_parallel.py                           # Parallel hyperparameter search
│   │   ├── rmse_Vs_alpha.ipynb
│   │   └── rmse_Vs_limit.ipynb
│   └── Paper_results/                               # Final paper figures & notebooks
│       ├── classical_models_perfomance.ipynb        # Baseline comparisons
│       ├── CPRC_ESP.ipynb                           # Echo state property
│       ├── CPRC_MC_Vs_WS.ipynb                      # Memory capacity vs window size
│       ├── CPRC_MSE_Vs_Tau_classical.ipynb          # MSE vs tau
│       ├── CPRC_performance_tau17_ws_10.ipynb
│       ├── CPRC_performance_tau17_ws_20.ipynb
│       ├── CPRC_performance_tau17_ws_20_noisy2.ipynb
│       ├── CPRC_performance_tau17_ws_20_ph_30.ipynb
│       └── figures/                                 # All paper figures (PDF/PNG)
│
└── noise_study/            # Scriptable end-to-end noise sweep pipeline
    ├── run_noise_sweep.py  # Main entry point: run full noise sweep
    ├── run_one.py          # Run a single noise configuration
    ├── run_all.py          # Run all configurations sequentially
    ├── aggregate_results.py# Aggregate per-run results into summary
    ├── make_plots.py       # Generate noise sensitivity figures
    ├── job_run.sh          # SLURM batch job script (HPC cluster)
    ├── configs/            # YAML configuration files
    │   ├── noise_sweep_config.yaml
    │   ├── study_config.yaml
    │   └── paths.yaml
    ├── src/                # Noise study source modules
    │   ├── noise_models.py
    │   ├── parametric_noise_models.py
    │   ├── reservoir_adapter.py
    │   ├── runner.py
    │   ├── data.py
    │   ├── metrics.py
    │   ├── io_utils.py
    │   └── plot_utils.py
    ├── tests/              # Unit and integration tests
    └── rerun/              # Configs and results for rerun experiments
```

---

## Installation

```bash
git clone https://github.com/utkarshh-singh/Recurrent_Quantum_Featue_Maps_for_Reservoir_Computing.git
cd Recurrent_Quantum_Featue_Maps_for_Reservoir_Computing

pip install -r noise_study/requirements.txt
```

---

## Usage

### 1. Paper Results — Jupyter Notebooks

All main paper results (performance, memory capacity, entanglement, hyperparameter sweeps) are reproduced via notebooks in `CPQRC/Paper_results/`. Open and run them in order:

```bash
jupyter notebook CPQRC/Paper_results/
```

Key notebooks:

| Notebook | What it reproduces |
|---|---|
| `CPRC_performance_tau17_ws_20.ipynb` | Main forecasting result (τ=17) |
| `CPRC_MSE_Vs_Tau_classical.ipynb` | MSE vs τ, CPMap vs ZZMap vs baselines |
| `CPRC_MC_Vs_WS.ipynb` | Memory capacity vs window size |
| `Ent_vs_MC.ipynb` | Entanglement vs memory capacity |
| `CPRC_ESP.ipynb` | Echo state property verification |
| `classical_models_perfomance.ipynb` | ESN and MLP baseline results |

> **Data:** The Mackey-Glass time series is generated synthetically via `datasets.py` — no external download needed.

---

### 2. Noise Study — Python Scripts

The `noise_study/` folder is a self-contained pipeline for systematically sweeping noise channels and strengths.

**Noise channels studied:**

| Channel | Parameter range |
|---|---|
| 1-qubit depolarizing | p = 0.0001 → 0.05 |
| 2-qubit depolarizing | p = 0.0001 → 0.05 |
| Combined (1Q + 2Q) | p = 0.0001 → 0.05 |
| Readout error | p = 0.0001 → 0.05 |
| Relaxation (T1) | T1 = 10µs → 500µs |

**Run the full sweep:**

```bash
cd noise_study
python run_noise_sweep.py --config configs/noise_sweep_config.yaml
```

**Run a single noise configuration:**

```bash
python run_one.py --noise_type 1Q_depol --param 0.01
```

**Aggregate results and plot:**

```bash
python aggregate_results.py
python make_plots.py
```

**On an HPC cluster (SLURM):**

```bash
sbatch job_run.sh
```

Results are saved per-run under `runs_noise_sweep/<noise_type_param>/` as `metrics.json`, `predictions.csv`, and `status.json`.

---

## Citation

```bibtex
@article{singh2026recurrent,
  title   = {Recurrent Quantum Feature Maps for Reservoir Computing},
  author  = {Singh, Utkarsh and Goldberg, Aaron Z. and Simon, Christoph and Heshami, Khabat},
  journal = {arXiv preprint arXiv:2604.03469},
  year    = {2026},
  url     = {https://arxiv.org/abs/2604.03469}
}
```

---

## License

MIT License. See [LICENSE](LICENSE) for details.

---

## Contact

**Utkarsh Singh** — University of Calgary  
Questions about the code: open a [GitHub Issue](https://github.com/utkarshh-singh/Recurrent_Quantum_Featue_Maps_for_Reservoir_Computing/issues).  
Questions about the paper: see the arXiv contact information.
