# Project Overview – Quick Visual Summary

## 📊 What This Project Does

```
INPUT (CubeSat telemetry)
         ↓
    [ANALYZE]  ← Compute z-scores against historical baselines
         ↓
    [DETECT]   ← Flag values with |z| ≥ 3 (99.7% confidence)
         ↓
    [EXPLAIN]  ← Provide domain-specific root cause hints
         ↓
OUTPUT (Anomaly report + optional LLM interpretation)
```

---

## 🎯 Core Metrics Used

### 1. **Z-Score Calculation**

$$z = \frac{x_{\text{measured}} - \mu_{\text{baseline}}}{\sigma_{\text{baseline}}}$$

- **Measures:** How many standard deviations away from the mean
- **Threshold:** $|z| \geq 3$ → Anomaly (99.7% confidence)
- **Type:** Statistical, distribution-free comparative metric

### 2. **3-Sigma Bounds (Confidence Intervals)**

$$[\mu - 3\sigma, \mu + 3\sigma]$$

- **Bounds 99.7% of normal data**
- **Any value outside = potential anomaly**
- **Used for:** Quick threshold checks without computing z-score each time

### 3. **Descriptive Statistics (Baselines)**

- **Mean ($\mu$):** Central value
- **Std Dev ($\sigma$):** Data spread/variability
- **Min/Max:** Range of observed values
- **Count:** Number of samples (1,080 per satellite)

---

## 📈 Anomaly Detection Flow (Visual)

```
BASELINE (from historical data):
┌─────────────────────────────────────────────┐
│ vbatt: μ=7.2V, σ=0.15V                     │
│ Range: [6.75V, 7.65V]  (±3σ bounds)        │
└─────────────────────────────────────────────┘
           ▲
           │
    ┌──────┴──────────┬──────────────┬──────────────┐
    │                 │              │              │
   6.75V           7.2V           7.65V          8.0V
  (Low)           (Mean)           (High)        (Out!)
    │               │                │              │
    └─────────────────────────────────┘              │
         99.7% of normal                    Anomaly: z > 3
         data falls here
```

**Sample Measurement:** `vbatt = 5.5V`

- $z = \frac{5.5 - 7.2}{0.15} = -11.33$ → **ANOMALY** (far below baseline)
- Interpretation: "Voltage far below baseline → Panel fault, open circuit, etc."

---

## 🛰️ Data Snapshot

| Aspect                   | Value                             |
| ------------------------ | --------------------------------- |
| **Satellites**           | NEPALISAT, RAAVANA, TSURU, UGUISU |
| **Records/Satellite**    | 1,080                             |
| **Total Measurements**   | ~160,000                          |
| **Parameters**           | 37 (mixed V, I, T readings)       |
| **Baseline Computation** | ~300 ms                           |
| **Sample Analysis**      | ~75 ms (no LLM)                   |

---

## 🔍 Example Anomaly Scenarios

### Scenario 1: Panel Failure

```
Baseline: vpx (panel voltage) = 3.5V ± 0.24V
Sample:   vpx = 3.1V
z-score:  (3.1 - 3.5) / 0.08 = -5.0

Result: ANOMALY ✓
Hint: "Voltage far below baseline → Panel fault, open circuit, shading"
```

### Scenario 2: Current Spike (Short Circuit)

```
Baseline: ipx (panel current) = 0.2A ± 0.09A
Sample:   ipx = 0.5A
z-score:  (0.5 - 0.2) / 0.03 = 10.0

Result: ANOMALY ✓
Hint: "Current much higher → Short circuit, increased load"
```

### Scenario 3: Thermal Event

```
Baseline: tbatt (battery temp) = 25°C ± 3°C
Sample:   tbatt = 45°C
z-score:  (45 - 25) / 1 = 20.0

Result: ANOMALY ✓
Hint: "Temperature unusually high → Thermal runaway, poor cooling"
```

### Scenario 4: Normal Reading

```
Baseline: vbatt = 7.2V ± 0.45V
Sample:   vbatt = 7.0V
z-score:  (7.0 - 7.2) / 0.15 = -1.33

Result: NORMAL ✓ (|z| < 3)
```

---

## 🤖 Multi-Agent AI Pipeline (Optional LLM)

```
Agent 1: Data Engineer
  └─ Computes baselines & statistical summaries
     └─ Task 1 Output: "Baseline metrics for all parameters"

        ▼

Agent 2: Anomaly Detector
  └─ Analyzes outliers & compares failure vs. nominal satellites
     └─ Task 2 Output: "Degradation signatures & failure patterns"

        ▼

Agent 3: Senior Analyst
  └─ Synthesizes findings into professional report
     └─ Task 3 Output: "EPS Design Reference Report"
        - Executive Summary
        - Root Cause Diagnosis
        - Design Recommendations
```

**Time:** 2–10 minutes (includes LLM API calls)

---

## 📁 Key Files Overview

| File                    | Purpose         | Key Function(s)                                         |
| ----------------------- | --------------- | ------------------------------------------------------- |
| `main.py`               | Main logic      | `compute_baselines()`, `detect_and_explain_anomalies()` |
| `satellite_input.json`  | Sample input    | Test data for anomaly detection                         |
| `PROJECT_REPORT.md`     | Detailed docs   | Full technical specification                            |
| `METRICS_CHEATSHEET.md` | Quick reference | Algorithm summary & examples                            |
| `ARCHITECTURE.md`       | System design   | Diagrams, data flow, deployment                         |
| `README.md`             | Setup guide     | Installation & quick start                              |
| `data/`                 | Telemetry       | 4 Excel files (1,080 rows × 37 cols each)               |

---

## ⚡ Quick Execution Guide

### Analyzer Only (No LLM)

```powershell
# Set input
$env:SKIP_CREW = "1"

# Run
python main.py

# Output: Anomaly report in terminal (fast, ~100ms)
```

### Full Multi-Agent (With LLM)

```powershell
# Set API key
$env:GEMINI_API_KEY = "your-key-here"

# Run (no SKIP_CREW set)
python main.py

# Output: Anomaly report + Professional EPS report (slow, 2–10 min)
```

---

## 📊 Metrics Performance Table

| Metric               | Value                               | Notes                                                 |
| -------------------- | ----------------------------------- | ----------------------------------------------------- |
| **Sensitivity**      | 99.7%                               | Detects deviations ≥3σ (if normally distributed)      |
| **Specificity**      | 99.7%                               | Only 0.3% false positives (if normally distributed)   |
| **Processing Speed** | 50–100 ms                           | Z-score + explanation (analyzer only)                 |
| **Scalability**      | 4 satellites, 1K rows/sat           | Scales linearly; can handle 10K+ satellites           |
| **Accuracy**         | Depends on data distribution        | Assumes normal distribution; may fail for skewed data |
| **Cost**             | Free (analyzer) + API charges (LLM) | Google Gemini API charges per token                   |

---

## 🎯 Anomaly Interpretation by Domain

### Voltage Anomalies

| Direction             | Cause                                       | Impact                       |
| --------------------- | ------------------------------------------- | ---------------------------- |
| **z ≤ -3** (too low)  | Panel fault, open circuit, shading          | Reduced power generation     |
| **z ≥ +3** (too high) | Charging circuit anomaly, measurement error | Potential battery overcharge |

### Current Anomalies

| Direction             | Cause                                       | Impact                   |
| --------------------- | ------------------------------------------- | ------------------------ |
| **z ≤ -3** (too low)  | Degraded panel, disconnection, open circuit | Insufficient power draw  |
| **z ≥ +3** (too high) | Short circuit, increased load, overload     | Risk of component damage |

### Temperature Anomalies

| Direction             | Cause                           | Impact                            |
| --------------------- | ------------------------------- | --------------------------------- |
| **z ≤ -3** (too cold) | Sensor miscal, cold environment | Potential sensor error            |
| **z ≥ +3** (too hot)  | Thermal runaway, poor cooling   | Battery degradation, failure risk |

---

## 🔧 Configuration

### Environment Variables

```bash
GEMINI_API_KEY=...           # Google Gemini API key
SAT_INPUT_JSON="{...}"       # Alternative input (JSON string)
SKIP_CREW=1                  # Skip LLM run
```

### Input File (`satellite_input.json`)

```json
{
  "name": "SATELLITE_NAME",
  "values": {
    "vbatt": 7.2,
    "ibatt": 0.1,
    "vpx": 3.5,
    "ipx": 0.2,
    ...
  }
}
```

---
