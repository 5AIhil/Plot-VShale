# Well Log Visualization & Shale Volume ($V_{shale}$) Estimation

![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)
![Python](https://img.shields.io/badge/Python-3.8%2B-blue.svg)
![Libraries](https://img.shields.io/badge/Libraries-NumPy%20%7C%20Pandas%20%7C%20Matplotlib-green.svg)

A comprehensive petrophysical analysis and well log visualization toolkit built in Python. This project provides automated statistical baseline estimation, multi-track geophysical log plotting (Gamma Ray, Density-Neutron Porosity overlay, and Sonic travel time), and shale volume ($V_{shale}$) calculations for formation evaluation and reservoir characterization.

---

## 📌 Project Overview

In petrophysical formation evaluation, well log visualization and lithology identification are essential steps for reservoir characterization. This repository contains end-to-end Python tools to:
- Load, clean, and preprocess digital well log records (`WellData.csv`).
- Calculate **clean sand** ($GR_{sand}$) and **pure shale** ($GR_{shale}$) baselines using non-parametric percentile statistics.
- Quantify **Shale Volume ($V_{shale}$)** across subsurface depths using linear and non-linear non-linear petrophysical models.
- Generate high-resolution, publication-ready **3-Track Well Log Plots** using `Matplotlib`, featuring shared depth axes, dual scale overlays, and baseline indicators.

---

## 📊 Well Log Data Suite (`WellData.csv`)

The dataset comprises **1,921 depth samples** recorded across a continuous well interval of **4,609.80 ft to 4,801.80 ft** (total thickness of **192.0 ft** at 0.1 ft sampling step).

### Log Curve Specifications & Statistical Summary

| Log Curve | Parameter | Units | Min | Max | Mean | Petrophysical Purpose |
| :--- | :--- | :--- | :---: | :---: | :---: | :--- |
| **DEPTH** | Measured Depth | $ft$ | 4609.80 | 4801.80 | - | Vertical positioning & interval mapping |
| **GR** | Gamma Ray | $API$ | 19.61 | 137.46 | 67.54 | Lithology identification & shale content estimation |
| **RHOB** | Bulk Density | $g/cc$ | 1.95 | 2.79 | 2.55 | Formation density & density porosity calculation |
| **NPHI** | Neutron Porosity | $frac$ | 0.0335 | 0.4174 | 0.1141 | Hydrogen index & liquid-filled porosity |
| **DT** | Sonic Travel Time | $\mu s/ft$ | 54.93 | 89.62 | 67.40 | Acoustic velocity & primary/secondary porosity |
| **DRHO** | Density Correction | $g/cc$ | -0.076 | 0.063 | -0.003 | Quality control for borehole roughness / mud cake |

---

## 🔬 Petrophysical Methodology & Formulas

### 1. Statistical Baseline Calibration
Rather than arbitrarily selecting sand and shale baselines, statistical percentiles over the Gamma Ray distribution are used to establish robust cutoff limits:
- **Clean Sand Baseline ($GR_{sand}$)**: $5^{th}$ percentile = **$25.59$ API**
- **Pure Shale Baseline ($GR_{shale}$)**: $95^{th}$ percentile = **$117.15$ API**

### 2. Shale Volume ($V_{shale}$) Estimation Models

#### Linear Gamma Ray Index ($I_{GR}$)
The fundamental linear index expresses the relative proportion of shale based on Gamma Ray readings:
$$I_{GR} = \frac{GR_{log} - GR_{sand}}{GR_{shale} - GR_{sand}}$$

#### Non-Linear Corrections
In consolidated or older formations, linear $I_{GR}$ can overestimate actual shale volume. The repository implements standard non-linear corrections:
- **Larionov (Tertiary / Young Rocks)**:
  $$V_{shale} = 0.33 \times \left(2^{2 \times I_{GR}} - 1\right)$$
- **Larionov (Pre-Tertiary / Older Rocks)**:
  $$V_{shale} = 0.35 \times \left(2^{1.5 \times I_{GR}} - 1\right)$$
- **Steiber Model**:
  $$V_{shale} = \frac{I_{GR}}{3 - 2 \times I_{GR}}$$

---

## 💡 Key Petrophysical Insights & Reservoir Interpretation

1. **Reservoir Sandstone Identification**:
   - **405 sampling intervals** (cumulative thickness $\approx 40.5$ ft) exhibit $GR \le 35.6$ API.
   - These clean sandstone zones display low gamma ray, low neutron porosity, and normal bulk density ($2.60 - 2.65$ g/cc), representing prime reservoir intervals with high effective porosity and permeability.

2. **Non-Reservoir Shale Formations**:
   - **290 sampling intervals** (cumulative thickness $\approx 29.0$ ft) exhibit $GR \ge 107.2$ API.
   - High gamma ray levels correlate with elevated clay mineral content and higher neutron porosity ($NPHI > 0.30$), indicating thick mudstone/shale seal beds.

3. **Average Shale Volume**:
   - Across the entire logged depth section, the mean linear shale volume is **$45.65\%$**, characterizing a heterogeneous interbedded sand-shale sequence (fluvial/deltaic depositional environment).

4. **Neutron-Density Porosity Crossover Analysis**:
   - Track 2 utilizes a twin-axis overlay where NPHI is plotted on an inverted scale ($0.45 \to -0.15$ frac) against RHOB ($1.90 \to 2.90$ g/cc).
   - **Gas / Light Hydrocarbon Crossover**: Intervals where the NPHI curve shifts to the left of the RHOB curve highlight potential gas-bearing zones or low-density fluid effects.
   - **Shale Crossover**: Intervals where NPHI shifts far right relative to RHOB confirm high-bound-water clay zones.

---

## 📉 Multi-Track Plot Visualization

The 3-track visualization layout provides an industry-standard well log display:
- **Track 1 (Gamma Ray)**: Displays the GR log (0 - 200 API) along with dashed lines for $GR_{sand}$ ($25.59$ API) and $GR_{shale}$ ($117.15$ API).
- **Track 2 (Neutron & Density)**: Dual-axis plot containing Neutron Porosity ($NPHI$, blue line) and Bulk Density ($RHOB$, red line).
- **Track 3 (Sonic Log)**: Acoustic travel time ($DT$, magenta line, $108 - 28\ \mu s/ft$).

<img width="1324" height="1391" alt="Well Log Visualization Plot" src="https://github.com/user-attachments/assets/5de4e42e-84b7-4567-a8e4-d5c1d56a2141" />

---

## 🛠️ Installation & Usage

### Prerequisites
Ensure Python 3.8+ is installed along with the necessary scientific packages:
```bash
pip install numpy pandas matplotlib
```

### Running the Notebook
Open and execute the Jupyter Notebook:
```bash
jupyter notebook "Displaying a Well Plot with MatPlotLib.ipynb"
```

### Python Code Snippet
```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

# 1. Load data
df = pd.read_csv("WellData.csv", header=0)
df = df.rename(columns=str.strip)

# 2. Statistical percentile baselines
GR_sand = round(np.percentile(df['GR'], 5), 2)   # 5th percentile
GR_shale = round(np.percentile(df['GR'], 95), 2) # 95th percentile

# 3. Calculate Linear Vshale
df['I_GR'] = (df['GR'] - GR_sand) / (GR_shale - GR_sand)
df['Vshale'] = df['I_GR'].clip(0, 1)

print(f"Clean Sand Baseline: {GR_sand} API")
print(f"Pure Shale Baseline: {GR_shale} API")
```

---

## 📂 Repository Structure

```
Plot & VShale/
│
├── Displaying a Well Plot with MatPlotLib.ipynb  # Interactive Jupyter Notebook for plotting & Vshale
├── WellData.csv                                  # Input well log dataset (DEPTH, GR, DT, RHOB, DRHO, NPHI)
├── README.md                                     # Detailed project documentation & petrophysical insights
└── LICENSE                                       # MIT License
```

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
