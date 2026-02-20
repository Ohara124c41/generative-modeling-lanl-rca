# Generative AI Applications (P5)

## Project Description
This project implements a full generative AI workflow for LANL cybersecurity telemetry using a Transformer-based sequence model in PyTorch. The notebook loads LANL-derived network event data, preprocesses and tokenizes structured event sequences, trains and compares model ablations, generates synthetic event narratives, maps them to RCA-style hypotheses, and runs governance-oriented diagnostics (NIST CSF mapping and optional AIF360 screening). The goal is a reproducible, traceable generative component for a broader DevSecAIOps/local-cloud architecture.

## Project Repository
- https://github.com/Ohara124c41/generative-modeling-lanl-rca

## What I Built
- `generative_model.ipynb`: end-to-end generative modeling + RCA notebook
- `requirements.txt`: project-scoped reproducibility dependencies
- `Generative_AI_Analysis_Report_draft.pdf`: report aligned to notebook outputs

## Dataset
- Name: LANL 2017 network event telemetry (+ optional WLS context subset)
- Source: https://csr.lanl.gov/data/2017/
- Additional LANL references: https://csr.lanl.gov/data/auth/ and https://csr.lanl.gov/data/cyber1/

## Data Access Setup
The notebook is designed to run from processed cache first, with optional raw fallback.

Preferred processed files:
- `data/processed/2017/p5_lanl2017_network_events_subset.csv.gz`
- `data/processed/2017/p5_wls_context_subset.csv.gz`

Colab/Drive alternatives also supported by path resolution logic:
- `/content/drive/MyDrive/data/processed/2017/...`
- `/content/drive/MyDrive/data processed 2017/...`

If processed cache is not available, raw fallback expects LANL netflow files under:
- `data/raw/2017/netflow_day-*.csv`

## Key Run Snapshot
From the current notebook run:
- Loaded event cache: `300,000` rows (`raw_load_mode=processed_cache`, day `2`)
- Loaded WLS context lines: `50,000`
- Modeling rows used: `50,000`
- Total tokens: `600,000` (`12.0` tokens/event)
- Vocabulary size: `10,635`
- Final selected model: `low_lr_1e4_ext10`
- Best validation loss: `~1.641` (perplexity `~5.160`)
- Generated samples: `8`
- Exported RCA artifacts: `8` rows (`CSV` + `JSONL`)

## Experimental Comparison Summary
Main experiments:
- `baseline` (`d_model=128`, `n_layers=2`, `dropout=0.2`, `lr=1e-3`, 10 epochs)
- `dropout_0p1`
- `wider_d192_l4`
- `low_lr_1e4`
- `low_lr_1e4_ext10` (extended follow-up)

Observed behavior:
- Baseline overfit after early epochs (validation loss worsened over time).
- Low learning rate variants gave the strongest validation behavior.
- `low_lr_1e4_ext10` produced the best recorded validation loss.

## Generated Output Quality and RCA Layer
- Generated sequences are structurally coherent but still moderately repetitive.
- Distinct-2 values are in a moderate range; repetition remains non-trivial.
- RCA layer outputs confidence-scored hypotheses and interpretable component scores.
- Graph views (NetworkX) are included for interaction and RCA linkage inspection.

## Bias and Risk Diagnostics
- NIST CSF-oriented checks are included as run-level governance signals.
- Optional AIF360 screening runs on protocol-proxy grouping and reports disparity metrics.
- In this run, screening indicates notable proxy-group disparity, so outputs should be treated as analyst-support artifacts, not autonomous decisions.

## How To Run
1. Create and activate a virtual environment:
```bash
python3 -m venv .venv
source .venv/bin/activate
```
2. Install dependencies:
```bash
pip install -r requirements.txt
```
3. Launch Jupyter:
```bash
jupyter notebook
```
4. Open and run:
`generative_model.ipynb`

## Reproducibility Notes
- Dependency bootstrap is included in-notebook.
- Seeds and config are fixed in setup cells.
- Processed-cache workflow avoids repeated multi-GB raw ingestion.
- Model checkpoints are persisted under processed-model directories.
- Refresh reproducibility lock before submission:
```bash
pip freeze > requirements.txt
```
