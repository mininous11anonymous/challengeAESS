# Anomaly Detection Metrics – Cheat Sheet

## Quick Reference

### 1. Core Algorithm: Z-Score Method

```
z = (x_sample - μ_baseline) / σ_baseline

where:
  x_sample = observed value
  μ_baseline = mean from historical data
  σ_baseline = standard deviation from historical data
```

**Threshold:** $|z| \geq 3$ → Anomaly detected (99.7% confidence)

### 2. Baseline Computation

For each EPS parameter across all satellites:

- Compute: `mean`, `std`, `min`, `max`
- Calculate: `3σ_low = mean - 3*std`, `3σ_high = mean + 3*std`

### 3. Anomaly Classification

| Z-Score Range | Interpretation     | Confidence |
| ------------- | ------------------ | ---------- |
| -3 < z < +3   | Normal             | 99.7%      |
| z ≤ -3        | Far below baseline | 99.7%      |
| z ≥ +3        | Far above baseline | 99.7%      |

### 4. Domain-Specific Heuristics

| Parameter           | Anomaly Type      | Likely Root Cause                           |
| ------------------- | ----------------- | ------------------------------------------- |
| **Voltage (V)**     | Too low (z ≤ -3)  | Panel fault, open circuit, shading          |
|                     | Too high (z ≥ +3) | Charging anomaly, measurement error         |
| **Current (A)**     | Too high (z ≥ +3) | Short circuit, increased load               |
|                     | Too low (z ≤ -3)  | Open circuit, degraded panel, disconnection |
| **Temperature (T)** | Too hot (z ≥ +3)  | Thermal runaway, poor cooling               |
|                     | Too cold (z ≤ -3) | Sensor miscalibration, cold environment     |

### 5. Performance Metrics

| Metric                            | Value                             |
| --------------------------------- | --------------------------------- |
| Baseline Computation Time         | ~300 ms (4 satellites)            |
| Single-Sample Analysis Time       | ~75 ms (no LLM)                   |
| Multi-Agent Crew Time             | 2–10 min (with LLM)               |
| **Anomaly Detection Sensitivity** | 99.7% (for normal distribution)   |
| **Specificity**                   | 99.7% (false positive rate: 0.3%) |

### 6. Data Snapshot

- **Satellites:** NEPALISAT, RAAVANA, TSURU, UGUISU
- **Records per Satellite:** 1,080 rows
- **Parameters Tracked:** 37 columns (V, I, T from panels & battery)
- **Total Data Points:** 4 × 1,080 × 37 ≈ 160K measurements

### 7. Execution Workflow

```
INPUT: satellite_input.json or SAT_INPUT_JSON
  ↓
[1] Load & parse sample values
  ↓
[2] compute_baselines('./data')  [~300 ms]
  ↓
[3] For each parameter:
    - Fetch baseline (mean, std)
    - Calculate z-score
    - Check if |z| ≥ 3
    - Generate domain explanation
  ↓
[4] Output: Markdown anomaly report
  ↓
[5] (Optional) Run multi-agent crew for interpretation
```

### 8. Key Functions

```python
# Compute baselines from all telemetry files
compute_baselines(folder_path: str) -> dict

# Detect & explain anomalies for a single sample
detect_and_explain_anomalies(
    sample_values: dict,
    baselines: dict = None,
    compare: str = 'global'
) -> str

# Data loading tool (for agents)
data_loading_tool(folder_path: str) -> str
```

### 9. Environment Variables

```bash
GEMINI_API_KEY=...           # Google Gemini API key (required for crew)
SAT_INPUT_JSON=...           # Alternative to satellite_input.json
SKIP_CREW=1                  # Skip LLM run; analyzer only
```

### 10. Example Anomaly Detection

**Baseline (Global):**

- `vbatt`: mean=7.2V, std=0.15V, range=[6.75V, 7.65V]
- `ipx`: mean=0.2A, std=0.03A, range=[0.11A, 0.29A]

**Sample (UGUISU):**

- `vbatt` = 5.5V → z = (5.5 - 7.2) / 0.15 = -11.33 → **ANOMALY** ✓
- `ipx` = 0.05A → z = (0.05 - 0.2) / 0.03 = -5.0 → **ANOMALY** ✓

**Diagnosis:** Low voltage + low current = **Panel degradation or disconnection**

---

