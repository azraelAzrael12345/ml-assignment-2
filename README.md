# Decision Trees, Bagging, Boosting, and Robustness

This project studies decision-tree learning on the **Olivetti faces dataset**, with a focus on:

- Decision-tree model selection and tree depth.
- Manual bagging and ensemble comparisons.
- SAMME boosting and training-history analysis.
- Robustness to image masking.
- Bias–variance analysis and prediction-level case studies.
- Reproducible experiments, validation checks, figures, and a LaTeX/PDF report.

The repository contains a run with seed `26509` and a run under `runs/m26ecm509/`. Before submitting or generating the final run, check the assignment instructions and update the student/repository metadata in `configs/default.json`.

## Project structure

The following tree reflects the uploaded Windows folder listing. Generated files and large environment/cache directories are summarized rather than expanded.

```text
ml-asssignment-2/
├── .gitignore
├── pytest.ini
├── README.md
├── requirements-lock.txt
├── configs/
│   ├── default.json
│   └── resolved.json
├── data/
│   ├── olivetti.npz
│   ├── resamples.npz
│   ├── split.npz
│   └── split_metadata.json
├── figures/
│   └── 06_boosting_high_weight_face.pdf
├── logs/
│   ├── experiments.log
│   ├── reproducibility_check.json
│   └── tests.log
├── models/
│   └── selected.joblib
├── results/
│   ├── predictions.npz
│   └── summary.json
├── runs/
│   └── m26ecm509/
│       ├── configs/
│       │   └── resolved.json
│       ├── data/
│       │   ├── olivetti.npz
│       │   ├── resamples.npz
│       │   ├── split.npz
│       │   └── split_metadata.json
│       ├── figures/
│       │   └── 06_boosting_high_weight_face.pdf
│       ├── logs/
│       │   └── experiments.log
│       ├── models/
│       │   └── selected.joblib
│       ├── report/
│       │   ├── cvpr.sty
│       │   ├── report.log
│       │   ├── report.pdf
│       │   ├── report.tex
│       │   └── figures/
│       │       ├── 02_bagging_diagnostics_compact.pdf
│       │       ├── 03_boosting_history_compact.pdf
│       │       ├── 05_bagging_face_cases_compact.pdf
│       │       └── 07_masking_divergence_compact.pdf
│       └── results/
│           ├── bagging_search.json
│           ├── biasvariance.json
│           ├── boosting_history.json
│           ├── boosting_search.json
│           ├── cases.json
│           ├── depth_study.json
│           ├── final_table.json
│           ├── matched_comparison.json
│           ├── predictions.npz
│           ├── refit_accuracy_records.json
│           ├── robustness.json
│           ├── summary.json
│           ├── toy_trace.json
│           ├── tree_search.json
│           └── refits/
├── scripts/
│   ├── check_reproducibility.py
│   ├── enrich_results.py
│   ├── make_figures.py
│   ├── make_report.py
│   ├── run_all.py
│   ├── run_experiments.py
│   └── validate_results.py
├── task99/
│   ├── __init__.py
│   ├── data.py
│   ├── experiments.py
│   ├── metrics.py
│   └── models.py
└── tests/
    ├── test_data_metrics.py
    └── test_models.py
```

> `.venv/`, `.pytest_cache/`, `__pycache__/`, and compiled Python files are local environment/cache artifacts and are not part of the source-code structure. Do not commit `.venv/`.

## Requirements

- Windows 10/11
- Python 3.12 is the version specified by the reference project README. The uploaded local environment appears to use Python 3.13, so if dependency installation or reproducibility checks fail, use Python 3.12 unless the assignment explicitly permits another version.
- Git
- Tectonic LaTeX engine to rebuild the PDF report
- Packages listed in `requirements-lock.txt`

## Setup on Windows (PowerShell)

Open the project folder in VS Code, then choose **Terminal → New Terminal**. Confirm the terminal is at the repository root (the folder containing `requirements-lock.txt`).

```powershell
# Check Python
python --version

# Create the virtual environment (run once)
python -m venv .venv

# If PowerShell blocks activation scripts, allow them for this terminal session only
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass

# Activate the virtual environment
.\.venv\Scripts\Activate.ps1

# Upgrade pip and install the locked dependencies
python -m pip install --upgrade pip
python -m pip install -r requirements-lock.txt
```

If `python` is not recognized, try `py -3.12` in place of `python` when creating the environment:

```powershell
py -3.12 -m venv .venv
```

In each new PowerShell terminal, activate the existing environment again:

```powershell
.\.venv\Scripts\Activate.ps1
```

If activation is blocked, run the `Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass` command in that terminal first. Alternatively, use Command Prompt with:

```cmd
.venv\Scripts\activate.bat
```

## Configuration before running

Open `configs/default.json` and check the assignment-specific fields. The reference README names these fields:

- `student_name`
- `roll_number`
- `repository_url`
- `final_commit_hash`

After the final commit is created, get its hash with:

```powershell
git rev-parse HEAD
```

Update `final_commit_hash` in the configuration if the assignment requires it, and rerun the relevant pipeline/validation steps if the configuration is part of the submitted result.

## Run the project

Run commands from the repository root with the virtual environment activated.

### 1. Reproduce the included provisional run

```powershell
python scripts/run_all.py --jobs 4
```

If the pipeline cannot find Tectonic automatically, first check:

```powershell
tectonic --version
```

If Tectonic is available only inside the active virtual environment, check:

```powershell
.\.venv\Scripts\tectonic.exe --version
```

Then pass its full path using the `--latex` option:

```powershell
python scripts/run_all.py --jobs 4 --latex ".\.venv\Scripts\tectonic.exe"
```

The exact `--latex` argument expected by the script may depend on how `scripts/run_all.py` handles the option; if this command is rejected, inspect `python scripts/run_all.py --help` and follow the displayed argument format.

### 2. Run with your actual roll number

Replace `YOUR_COMPLETE_ROLL_NUMBER` with the exact roll number required by the assignment. This command writes to a separate output directory so the existing run is preserved.

```powershell
python scripts/run_all.py --root runs/actual_roll --roll-number YOUR_COMPLETE_ROLL_NUMBER --jobs 4
python scripts/validate_results.py --root runs/actual_roll
```

If needed, add the `--latex` option with the Tectonic executable path as described above.

### 3. Rebuild figures and report from saved results

```powershell
python scripts/run_all.py --skip-experiments
```

This skips the experiment stage and rebuilds downstream outputs from available saved results. Use it only when compatible results already exist.

### 4. Run tests and validation checks separately

```powershell
python -m pytest -q
python scripts/validate_results.py
python scripts/check_reproducibility.py
```

## Report output

The uploaded folder structure contains the generated report for the existing run at:

```text
runs/m26ecm509/report/report.pdf
runs/m26ecm509/report/report.tex
```

To compile the TeX source manually from the report directory:

```powershell
Set-Location runs/m26ecm509/report
tectonic --keep-logs report.tex
Set-Location ../../..
```

If `tectonic` is not on PATH but exists in the active virtual environment, use:

```powershell
..\..\..\.venv\Scripts\tectonic.exe --keep-logs report.tex
```

The manual compile command assumes `cvpr.sty` and the report's figure files remain in the expected relative locations. Keep `cvpr.sty` alongside `report.tex`.

## Main files and directories

| Path | Purpose |
|---|---|
| `task99/` | Core data, model, experiment, and metric implementation |
| `scripts/run_all.py` | Main end-to-end pipeline |
| `scripts/run_experiments.py` | Experiment execution |
| `scripts/make_figures.py` | Figure generation |
| `scripts/make_report.py` | Report generation |
| `scripts/validate_results.py` | Checks saved results |
| `scripts/check_reproducibility.py` | Reproducibility checks |
| `configs/default.json` | Default run and submission metadata |
| `configs/resolved.json` | Resolved configuration for the included run |
| `data/` | Olivetti data and saved split/resampling artifacts |
| `results/` | Summary and prediction outputs at the project root |
| `runs/m26ecm509/` | Detailed artifacts for the existing run |
| `runs/m26ecm509/report/` | LaTeX source, CVPR style file, PDF, and report figures |
| `tests/` | Automated tests |
| `requirements-lock.txt` | Locked Python dependencies |
| `logs/` | Experiment, test, and reproducibility logs |

## Git hygiene

Before committing, make sure the virtual environment and private/local artifacts are ignored. A minimal `.gitignore` should include:

```gitignore
.venv/
__pycache__/
*.py[cod]
.pytest_cache/
.env
```

Do not commit credentials, private datasets, or machine-specific absolute paths. If your course requires the generated results or PDF, follow the submission instructions and include only the required artifacts.

## Reproducibility notes


- Keep the configuration, code, data split, logs, and final commit hash consistent for the final submission.
- The report build requires Tectonic. If a PDF cannot be built, preserve the generated `.tex` source and resolve the missing compiler/style/figure dependency before submitting.
