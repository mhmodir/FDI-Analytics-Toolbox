<div align="center">

# FDI Analytics Toolbox

**Unsupervised Fault Detection and Identification for Industrial Sensor Data**

[![Python](https://img.shields.io/badge/Python-3.10%2B-blue?logo=python)](https://www.python.org/)
[![Dash](https://img.shields.io/badge/Dash-2.17-informational?logo=plotly)](https://dash.plotly.com/)
[![License](https://img.shields.io/badge/License-All%20Rights%20Reserved-red)](#license)
[![Azure](https://img.shields.io/badge/Deployed%20on-Azure%20App%20Service-0078D4?logo=microsoftazure)](https://fdi-analytics-app.azurewebsites.net)

A fully interactive, browser-based web application for diagnosing faults in industrial sensor streams — **no labeled fault data required**. Upload a CSV, walk through a six-stage pipeline, and get fault windows, root-cause contributions, and an AI-assisted interpretation — all without writing a single line of code.

[**🚀 Launch App**](https://fdi-analytics-app.azurewebsites.net) · [**📘 User Manual**](#documentation) · [**⚡ Quick-Start Guide**](#documentation) · [**🎬 Video Walkthrough**](#documentation)

![FDI Analytics Screenshot](assets/screenshot.png)

</div>

---

## Table of Contents

- [Overview](#overview)
- [Pipeline](#pipeline)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Usage](#usage)
- [Documentation](#documentation)
- [Project Structure](#project-structure)
- [License](#license)

---

## Overview

FDI Analytics is a **no-code condition monitoring toolbox** built for engineers and researchers working with industrial time-series data. It implements a complete Fault Detection and Identification (FDI) pipeline using only unsupervised machine learning — meaning you do not need historical fault labels, annotations, or a separate training dataset.

**Who is this for?**
- Process engineers monitoring plant equipment (pumps, compressors, heat exchangers, reactors)
- Researchers prototyping anomaly detection workflows on new sensor datasets
- Students learning signal processing and unsupervised ML in an applied setting

**Key principle:** the entire pipeline operates without labeled fault data. A short segment of normal operation is used as a baseline; everything that deviates from that baseline is scored and ranked.

> **Note:** This project is hosted as a live web application. Source code is not publicly distributed. See [License](#license).

---

## Pipeline

The app guides users through six sequential stages, each building on the previous:

```
┌─────────────┐    ┌──────────────────┐    ┌──────────────────────┐
│  1. Home    │ →  │ 2. Pre-processing │ →  │  3. Feature Extraction│
│ Data Upload │    │ Signal Conditioning│   │  Window-based features│
└─────────────┘    └──────────────────┘    └──────────────────────┘
                                                        ↓
┌─────────────┐    ┌──────────────────┐    ┌──────────────────────┐
│  6. Report  │ ←  │5. Identification  │ ←  │   4. Detection        │
│ Export & AI │    │ Root-Cause Ranking│    │ Anomaly Scoring       │
└─────────────┘    └──────────────────┘    └──────────────────────┘
```

| Stage | What happens |
|-------|-------------|
| **Home** | Upload CSV, inspect raw signals, define the normal-operation baseline window |
| **Pre-processing** | Apply up to 5 sequential signal conditioning steps per variable |
| **Feature Extraction** | Compute statistical/frequency-domain features or deep-learned embeddings per sliding window |
| **Detection** | Score each window with an unsupervised anomaly detector; flag windows above threshold |
| **Identification** | Rank variables and features by their contribution to each flagged anomaly |
| **Report** | View summary statistics, score distributions, contribution heatmaps; export CSV or HTML; query the AI assistant |

---

## Features

### Signal Pre-processing
Five configurable conditioning stages, each with multiple method options:

| Stage | Methods |
|-------|---------|
| Missing value handling | Drop rows · Mean imputation · Median imputation · Linear interpolation |
| Outlier removal | Z-score clipping · IQR clipping · Hampel filter |
| Detrending | Linear detrend · First-order differencing · Moving-average baseline removal |
| Noise filtering | Moving average · Median filter · Savitzky-Golay · Exponential smoothing |
| Normalization | Min-Max · Z-score standardization · Robust scaling |

### Feature Extraction
**Traditional (statistical & frequency-domain):**
Mean, Std, RMS, Variance, Skewness, Kurtosis, Peak-to-Peak, Crest Factor, Dominant Frequency (FFT), Spectral Energy (FFT)

**Deep learning embeddings** *(optional)*:
- Dense Autoencoder — compact nonlinear embedding
- LSTM Autoencoder — captures temporal dependencies
- 1D-CNN Autoencoder — captures local waveform shape

### Fault Detection
**Classical (unsupervised):**
- PCA-based (Hotelling's T²)
- Isolation Forest
- One-Class SVM
- Local Outlier Factor (LOF)

**Deep learning** *(optional)*:
- Autoencoder — reconstruction-error anomaly score
- Variational Autoencoder (VAE) — probabilistic reconstruction error
- Deep SVDD — one-class hypersphere distance

### Fault Identification
Root-cause contribution analysis to pinpoint which variables and features are driving each anomaly:
- Z-score deviation from normal baseline per feature
- Autoencoder residual — per-feature reconstruction error
- Gradient × Input Saliency *(deep learning only)*

### AI Assistant
A built-in LLM assistant with full awareness of the current pipeline state (dataset shape, preprocessing applied, detection results, top contributing features). Supports:

| Provider | Default model |
|----------|-------------|
| OpenAI | gpt-4o |
| Anthropic | claude-3-5-sonnet |
| Ollama (local) | llama3 |
| Custom OpenAI-compatible endpoint | configurable |

Pre-built quick prompts: interpret detection results · recommend preprocessing · compare methods · summarize findings · suggest next steps · optimize window parameters.

### UX
- Dark sidebar with a waterfall pipeline stepper showing progress at a glance
- Live progress bars and a **Cancel** button on all long-running jobs
- **Select All / Clear** on the variable selection dropdown
- Export results as CSV or a self-contained HTML report
- Works in any modern browser — no installation required for end users

---

## Tech Stack

| Layer | Library | Version |
|-------|---------|---------|
| Web framework | [Dash](https://dash.plotly.com/) | 2.17 |
| Plotting | [Plotly](https://plotly.com/python/) | 5.24 |
| Data | [Pandas](https://pandas.pydata.org/) · [NumPy](https://numpy.org/) | 2.2 · 1.26 |
| Signal processing | [SciPy](https://scipy.org/) | 1.13 |
| Classical ML | [scikit-learn](https://scikit-learn.org/) | 1.5 |
| Deep learning *(optional)* | [PyTorch](https://pytorch.org/) | 2.x (CPU) |
| Background callbacks | [diskcache](https://grantjenks.com/docs/diskcache/) via `dash[diskcache]` | — |
| Hosting | [Azure App Service](https://azure.microsoft.com/en-us/products/app-service) | B2 Linux |

---

## Usage

The app is accessible directly at **https://fdi-analytics-app.azurewebsites.net** — no installation or account required.

1. **Home** — Upload your CSV file. Inspect raw signal plots. Set the baseline window (the time range representing normal operation) using the range sliders.

2. **Pre-processing** — Enable and configure each conditioning step. Preview the effect on your signals before proceeding.

3. **Feature Extraction** — Choose variables, sliding window parameters (window size, step size, sampling frequency), and which features to compute. Run extraction.

4. **Detection** — Select a detection method and configure its hyperparameters. Run detection to get an anomaly score for every window.

5. **Identification** — View a ranked heatmap of variable and feature contributions. Understand which sensor and which aspect of its signal is most anomalous.

6. **Report** — Review summary statistics, score distribution, and contribution heatmap. Ask the AI assistant questions about your results. Export as CSV or HTML.

For a guided walkthrough, see the [Quick-Start Guide](#documentation) or [Video Walkthrough](#documentation).

---

## Documentation

| Document | Description |
|----------|-------------|
| 📘 [User Manual](docs/user_manual.pdf) | Full operator reference — all pages, controls, methods, and parameters explained |
| ⚡ [Quick-Start Guide](docs/quickstart.pdf) | Get from CSV to fault report in under 10 minutes |
| 🎬 [Video Walkthrough](docs/demo_video.mp4) | End-to-end case study on a real industrial dataset |

---

## Project Structure

```
fdi-analytics/
├── README.md
├── requirements.txt        # Python dependencies
├── assets/
│   └── screenshot.png
└── docs/
    ├── user_manual.pdf
    ├── quickstart.pdf
    └── demo_video.mp4
```

> The application source code (`app.py`) and stylesheet are proprietary and not included in this repository. The live app is hosted at [fdi-analytics-app.azurewebsites.net](https://fdi-analytics-app.azurewebsites.net).

---

## License

© 2026 Mohammad Modirrousta. All rights reserved.

This repository contains documentation and project resources only. The application source code is proprietary. No part of the source code may be reproduced, distributed, or used without explicit written permission from the author.

For collaboration or licensing enquiries, contact via [GitHub](https://github.com/mhmodir).

---

<div align="center">

Built with [Dash](https://dash.plotly.com/) · Hosted on [Azure App Service](https://azure.microsoft.com/en-us/products/app-service)

**[github.com/mhmodir](https://github.com/mhmodir)**

</div>
