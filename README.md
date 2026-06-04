# Intersystem Noise Coupling
### A Stochastic Framework for Cross-Organ Physiological Synchronization



## Overview

The human body runs multiple physiological systems in parallel (cardiac, locomotor, endocrine, neural) and medicine typically studies them in isolation. 
Biological signals are not smooth. Heart rate varies beat to beat. Movement intensity fluctuates. These fluctuations are not pure noise, they carry structure. 
This project models that structure using **stochastic differential equations**, specifically the **coupled Ornstein-Uhlenbeck (OU) process**, to quantify how the probability distributions of fluctuations in one system influence the probability distributions in another.
The core hypothesis being **healthy systems maintain characteristic coupling signatures that break down, strengthen, or reorganize in disease states.**


## Research Question

*Are the random fluctuations across these systems mathematically correlated?*
*Is intersystem physiological coupling a measurable and diagnostically relevant biomarker of health state?*



## Methodology Overview

### 1. Signal Extraction
- **Cardiac signal:** RR intervals derived from ECG (time between consecutive heartbeats in ms)
- **Locomotor signal:** Accelerometer magnitude (√x² + y² + z²) representing overall movement intensity
- Both signals resampled to a uniform 4 Hz grid for cross-system comparison

### 2. Frequency Domain Analysis
- Power spectral density of HRV to extract LF (0.04–0.15 Hz) and HF (0.15–0.40 Hz) bands
- LF/HF ratio as sympathovagal balance index

### 3. Cross-Organ Coherence
- Spectral coherence between cardiac and locomotor signals
- Identifies frequency-specific synchronization between systems

### 4. Coupled OU Model Fitting
The bivariate coupled OU system is defined as:

```
dX = θ_x(−X)dt + κ_xy·Y·dt + σ_x·dW_x    (cardiac)
dY = θ_y(−Y)dt + κ_yx·X·dt + σ_y·dW_y    (locomotor)
```

Where:
- `κ_xy` — coupling strength: locomotion → cardiac
- `κ_yx` — coupling strength: cardiac → locomotion
- `σ_x`, `σ_y` — intrinsic noise levels per system
- `θ_x`, `θ_y` — mean reversion rates

Parameters are estimated per subject using least-squares regression on the discretized derivatives.

### 5. Transfer Entropy
Model-free, information-theoretic measure of directional coupling. Quantifies how much knowing one system's past reduces uncertainty about the other's future, without assuming linearity.

### 6. Windowed Coupling Analysis
Sliding-window OU fitting to capture temporal dynamics of coupling, whether coupling is stable or fluctuates across a recording session.


## Datasets

### MMASH (Multilevel Monitoring of Activity and Sleep in Healthy People)
- **N = 20** healthy, active adults
- 24-hour continuous recordings
- Signals used: ECG-derived RR intervals, wrist accelerometer
- Role: **Healthy baseline** — establishes what decoupled, healthy systems look like

### MIMIC-IV *(upcoming)*
- ICU patient waveform data
- Signals to be used: ECG-derived RR intervals, respiratory waveform
- Role: **Disease comparison** — tests whether coupling signatures differ in critically ill patients


## Repository Structure

```
intersystem-noise-coupling/
│
├── cross_organ_sync_mmash.ipynb   # Main analysis notebook
│
├── results/
│   ├── population_summary.png        # Panels A–E: full population overview
│   ├── ou_phase_space_real.png       # Phase space: coupled vs dissociated
│   ├── coupling_analysis.png         # Coupling strength across subjects
│   ├── windowed_coupling.png         # Temporal dynamics of coupling
│   ├── permutation_test.png          # Statistical validation
│   ├── outlier_deep_dive.png         # Subject-level anomaly analysis
│   ├── ou_coupling_params.csv        # Fitted OU parameters per subject
│   ├── transfer_entropy.csv          # TE values per subject
│   ├── windowed_coupling_stats.csv   # Windowed coupling statistics
│   └── mmash_final_features.csv      # Full feature set, ready for comparison
│
├── README.md
└── LICENSE
```


## Key Findings So Far (MMASH - Healthy Baseline)

| Metric | Value | Interpretation |
|--------|-------|----------------|
| Mean LF/HF ratio | 1.62 | Slight sympathetic dominance — normal for active people |
| Mean LF coherence | ~0.000 | Cardiac and locomotor systems largely independent |
| Mean κ_xy (loco→card) | −0.00551 | Movement mildly suppresses cardiac variability |
| Mean κ_yx (card→loco) | −0.00076 | Cardiac barely influences movement |
| κ_xy vs LF/HF correlation | r = 0.436, p = 0.054 | Trending: more coupling = more sympathetic activation |
| σ_cardiac range | 0.13 – 0.80 | High individual variability in cardiac noise |
| σ_locomotor range | 0.31 – 0.39 | Locomotor noise is consistent across people |

In healthy people, cardiac and locomotor systems are largely decoupled. The tiny coupling that exists is negative: movement slightly quiets cardiac variability, consistent with vagal withdrawal during physical activity. One subject shows anomalously positive coupling and is a consistent outlier across all metrics.


## Requirements

```
python >= 3.9
numpy
pandas
scipy
matplotlib
jupyter
```

Install with:
```bash
pip install numpy pandas scipy matplotlib jupyter
```
