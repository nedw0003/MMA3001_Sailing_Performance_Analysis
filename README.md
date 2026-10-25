# MMA3001_Sailing_Performance_Analysis
# ⛵ Sailing Performance Analysis Pipeline

This repository contains a Python-based telemetry analysis pipeline that processes GPS and IMU logs from yacht races to extract tactical sailing metrics, detect maneuvers, and analyze crew performance using a flight-grade analysis dashboard.

---

## 📋 Features & Methodology

### 1. Robust Data Loading & Time Normalization
- Parses high-frequency race telemetry datasets (`.csv`).
- Normalizes chronological coordinate logs, switching UTC timestamps to local Melbourne time (`Australia/Melbourne`).

### 2. Apparent Wind Vector Algebra
Calculates **Apparent Wind Speed (AWS)** and **Apparent Wind Angle (AWA)** relative to the boat's moving bow using vector trigonometry:
- Standardizes True Wind Direction (TWD) and Course Over Ground (COG) to coordinate vectors (North positive Y, East positive X).
- Resolves sailboat velocity vectors ($V_b$) and True Wind vectors ($V_t$).
- Calculates Apparent Wind vectors: $\vec{V_a} = \vec{V_t} - \vec{V_b}$.
- Computes final relative AWA normalized between $-180^\circ$ and $180^\circ$.

### 3. Machine-Optimized True Wind Direction (TWD)
Rather than relying on static or inaccurate anemometer readings, this pipeline dynamically solves for TWD using a **Circular Mean Upwind Tack Midpoint** method:
- Segregates steady close-hauled upwind points into starboard (positive TWA) and port (negative TWA) datasets.
- Calculates average heading components using circular trigonometry to bypass $0^\circ/360^\circ$ boundary discontinuities.
- Computes the true wind axis bisecting the two headings.

### 4. Velocity Made Good (VMG)
Computes **Velocity Made Good to True Wind (VMG_TW)** to assess true upwind and downwind progress:
$$\text{VMG}_{\text{TW}} = \text{SOG} \times \cos(\text{TWA}_{\text{optimized}})$$

### 5. Advanced Maneuver Heuristics (Tacks, Gybes & Mark Roundings)
- **Transition-Based Tack Detection**: Tracks transitions between stable starboard close-hauled states ($30^\circ$ to $60^\circ$ TWA) and port close-hauled states ($-30^\circ$ to $-60^\circ$ TWA) within a physical $15$-second window.
- **Downwind Gybe Detection**: Detects stern-to-wind crossings passing through the $180^\circ$ relative heading threshold using a $20^\circ$ boundary tolerance.
- **Mark Rounding Identification**: Classifies when the sailing regime shifts systematically between Upwind and Downwind.
- **Deduplication**: Filters out redundant triggers using a rolling $10$-second cooldown window.

### 6. Interactive Flight & Performance Analysis Dashboard
An interactive `ipywidgets` dashboard featuring:
- **Full Course Plot**: GPS track maps dynamically colored by sailing regime (Upwind, Reaching, Downwind) with visual markers highlighting the exact location of selected maneuvers, custom distance scales, and polar TWD wind direction dials.
- **Telemetry Profile Panels**: Plots Speed Over Ground (SOG) and VMG before, during, and after maneuver execution.
- **Performance Scorecard**: Automatically evaluates crew transition efficiency, documenting **Speed Before Execution**, **Maneuver Duration**, **Speed Recovery Time**, and net **VMG Lost**.

---

## 📂 Project Structure

```directory
MMA3001_Project_Sailing/
├── data/
│   ├── Ora 8-15-2026 Race 1.csv
│   ├── Ora 8-15-2026 Race 2.csv
│   ├── Ora 8-15-2026 Race 3.csv
│   └── Ora 8-15-2026 Race 4.csv
├── Sailing_Analysis_Pipeline.ipynb
└── README.md
```

---

## 🛠️ Requirements & Installation

Install the required Python dependencies:
```bash
pip install numpy pandas matplotlib seaborn geopy ipywidgets
```

---

## 🚀 How to Run the Dashboard

1. Open `Sailing_Analysis_Pipeline.ipynb` in Google Colab or Jupyter.
2. Execute the data initialization, math processing, and maneuver detection cells.
3. Run the interactive UI cell. Use the dropdown menus to switch between races and evaluate individual tacks or gybes on-demand.
