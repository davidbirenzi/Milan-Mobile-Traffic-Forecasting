# Milan Mobile Network Traffic — Time Series Forecasting

ML Techniques I formative assignment: memory-efficient loading of TIM Milan data, exploratory analysis, and one-step-ahead forecasts with **ARIMA(2,1,2)**, **LSTM**, and **GRU** on three grid cells.

**Author:** David Birenzi
GitHub repository: https://github.com/davidbirenzi/Mobile-Traffic-Forecasting-Milan
Demo video: https://youtu.be/r4L3qQvOO9k

---

## Repository layout

```
├── notebooks/              # Run in order (1 → 5)
│   ├── data_loading.ipynb
│   ├── exploratory_analysis.ipynb
│   ├── forecasting_arima.ipynb
│   ├── forecasting_lstm.ipynb
│   └── forecasting_gru.ipynb
├── data/processed/       # Generated outputs + minimum data to rerun forecasts
├── dataverse dataset/    # Raw TIM files (optional if Parquet already built)
├── figures/              # Plots from notebooks
├── report/               # report.md, report.html, figures, VIDEO_SCRIPT.md
├── scripts/              # Optional CLI helpers
└── requirements.txt
```

---

## Data

| Source | Link |
|--------|------|
| Telecommunications activity (Milan) | https://doi.org/10.7910/DVN/EGZHFV |
| Grid (Milan) | https://doi.org/10.7910/DVN/QJWLFU |

Place daily files `sms-call-internet-mi-*.txt` in **`dataverse dataset/`** (tab-separated; ~19 GB total).

**Minimum to rerun Tasks 2–3 without raw text:**

- `data/processed/milan_internet_traffic.parquet` — **not in Git** (~391 MB); generate with `notebooks/data_loading.ipynb` or keep a local copy after Task 1
- `data/processed/task2_focus_squares.csv` — included in this repo

To reproduce Task 1 from scratch, you need the full raw folder above.

---

## Setup (Windows / Linux / macOS)

**Requirements:** Python 3.10+ (tested on 3.14), ~8 GB free RAM for Parquet workflow, ~20 GB disk if using raw files.

```bash
cd "Time Series Forecasting"
python -m venv .venv
```

**Windows (PowerShell)**

```powershell
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
```

**Linux / macOS**

```bash
source .venv/bin/activate
pip install -r requirements.txt
```

**PyTorch (CPU only, if install fails):**

```bash
pip install torch --index-url https://download.pytorch.org/whl/cpu
pip install -r requirements.txt
```

---

## How to run

Open the project in VS Code or Jupyter, select the `.venv` kernel, then:

| Step | Notebook | Notes |
|------|----------|--------|
| 1 | `notebooks/data_loading.ipynb` | ~25–35 min first time; writes Parquet |
| 2 | `notebooks/exploratory_analysis.ipynb` | Needs Parquet; saves `figures/` |
| 3 | `notebooks/forecasting_arima.ipynb` | Test week: 16–22 Dec 2013 |
| 4 | `notebooks/forecasting_lstm.ipynb` | CPU training ~5–20 min per square |
| 5 | `notebooks/forecasting_gru.ipynb` | Same protocol as LSTM |

**Optional scripts** (from project root, venv active):

```bash
python scripts/run_gru.py              # GRU only → metrics + figures
python scripts/plot_failure_case.py   # Figure 16 for report
```

**Report PDF:** open `report/report.html` → Print → Save as PDF (enable background graphics). See `report/EXPORT_TO_PDF.md`.

---

## Forecasting protocol

- **Train:** 2013-11-01 → 2013-12-15  
- **Test:** 2013-12-16 → 2013-12-22 (held out)  
- **Squares:** 5161 (highest load), 4159, 4556  
- **Horizon:** one step (10 minutes), history up to \(t\) uses actual traffic  

---

## Key outputs

| Path | Content |
|------|---------|
| `data/processed/milan_internet_traffic.parquet` | Cleaned series (all squares) |
| `data/processed/*_metrics_by_area.csv` | MAE, MAPE, RMSE per model |
| `data/processed/*_predictions_square_*.csv` | Test-week actual vs predicted |
| `figures/task3_*` | Forecast and failure plots |
| `report/report.md` | Full written report |

---

## Models (short)

| Model | Role | Main settings |
|-------|------|----------------|
| ARIMA(2,1,2) | Statistical baseline | `statsmodels`; AIC order pick on 5161 |
| LSTM | Neural | `SEQ_LEN=144`, hidden 64, MinMaxScaler, early stopping |
| GRU | Neural | Same as LSTM; GRU cells |

---

## Submission bundle
 
1. **This repo** — notebooks + `data/processed/` (or document raw download)  
2. **Video** — script in `report/VIDEO_SCRIPT.md`; link in report References  

---

## References

G. Barlacchi et al., *Sci. Data* 2, 150055 (2015). https://doi.org/10.1038/sdata.2015.55
