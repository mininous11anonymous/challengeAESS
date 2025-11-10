# Architecture & Implementation Guide

## System Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         CubeSat EPS Analysis System                         │
└─────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│ INPUT SOURCES                                                               │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌──────────────────────┐      ┌──────────────────────┐                  │
│  │ satellite_input.json │      │ SAT_INPUT_JSON       │                  │
│  │ (single sample)      │  OR  │ (env variable)       │                  │
│  └──────┬───────────────┘      └──────┬───────────────┘                  │
│         │                              │                                  │
│         └──────────────┬───────────────┘                                  │
│                        │                                                  │
│                   {"vbatt": 5.5,                                          │
│                    "ibatt": -2.0,                                         │
│                    "vpx": 3.1, ...}                                       │
│                        │                                                  │
└────────────────────────┼──────────────────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│ DATA LOADING & BASELINE COMPUTATION                                         │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  data/                                                                     │
│  ├── NEPALISAT.xlsx  }                                                    │
│  ├── RAAVANA.xlsx    } ──► compute_baselines('./data') ──► Baselines    │
│  ├── TSURU.xlsx      }     (1,080 rows × 37 cols each)     Dict:         │
│  └── UGUISU.xlsx     }                                       {            │
│                                                              by_satellite │
│  Statistics Computed:                                        global       │
│  • Mean, Std, Min, Max                                      }             │
│  • 3-Sigma Low/High                                                      │
│                                                                             │
└────────────────────┬──────────────────────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│ ANOMALY DETECTION ENGINE                                                    │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  detect_and_explain_anomalies(sample_values, baselines, compare='global') │
│                                                                             │
│  For each parameter in sample_values:                                     │
│  ┌──────────────────────────────────────────────────────────────────────┐ │
│  │ 1. Fetch baseline stats (μ, σ)                                      │ │
│  │    z = (x_sample - μ) / σ                                          │ │
│  │                                                                      │ │
│  │ 2. Check threshold: |z| ≥ 3 ?                                      │ │
│  │    YES → Anomaly detected                                          │ │
│  │    NO  → Normal value                                              │ │
│  │                                                                      │ │
│  │ 3. Domain interpretation (if anomaly):                             │ │
│  │    • Voltage + z≤-3 → "Panel fault"                               │ │
│  │    • Current + z≥+3 → "Short circuit"                             │ │
│  │    • Temperature + z≥+3 → "Thermal runaway"                       │ │
│  │    ...                                                              │ │
│  └──────────────────────────────────────────────────────────────────────┘ │
│                                                                             │
│  Output: Markdown anomaly report with z-scores & explanations             │
│                                                                             │
└────────────────────┬──────────────────────────────────────────────────────┘
                     │
                     ▼
        ┌────────────────────────────┐
        │  Anomaly Report (Markdown) │
        │                            │
        │ - vbatt: z=-11.33 (ANOM)  │
        │ - ibatt: z=-26.50 (ANOM)  │
        │ - vpx: z=-5.00 (ANOM)     │
        │ - ipx: z=-5.00 (ANOM)     │
        │                            │
        │ Explanations:              │
        │ • Voltage low → panel fault│
        │ • Current low → open circ  │
        └────────────────────────────┘
                     │
                     ▼
        ┌────────────────────────────┐
        │  Skip Crew? (SKIP_CREW=1)  │
        │  YES ──► EXIT              │
        │  NO  ──► Continue...       │
        └────┬───────────────────────┘
             │
             NO
             │
             ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│ MULTI-AGENT INTERPRETATION PIPELINE (CrewAI + Gemini LLM)                  │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  PROCESS: Sequential (Task 1 → Task 2 → Task 3)                           │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐ │
│  │ AGENT 1: EPS Telemetry Data Engineer                               │ │
│  ├─────────────────────────────────────────────────────────────────────┤ │
│  │ Goal: Compute & structure baselines (Min/Max/Mean/±3σ)             │ │
│  │ Task 1: Process all telemetry, output baseline summary            │ │
│  │ Input: folder_path = "./data"                                     │ │
│  │ Output: Structured Markdown with baseline metrics                 │ │
│  └─────────────────────────────────────────────────────────────────────┘ │
│                            ▼                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐ │
│  │ AGENT 2: EPS Degradation & Anomaly Detector                        │ │
│  ├─────────────────────────────────────────────────────────────────────┤ │
│  │ Goal: Identify ±3σ anomalies & comparative degradation signatures  │ │
│  │ Task 2: Analyze Agent 1 output, list outliers & signatures        │ │
│  │ Input: Task 1 output + sample_anomaly_report (if provided)        │ │
│  │ Output: Outliers list + Degradation signature analysis            │ │
│  └─────────────────────────────────────────────────────────────────────┘ │
│                            ▼                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐ │
│  │ AGENT 3: Mission Control Senior EPS Designer & Analyst             │ │
│  ├─────────────────────────────────────────────────────────────────────┤ │
│  │ Goal: Generate professional EPS Design Reference Report            │ │
│  │ Task 3: Synthesize findings → Executive Summary + Diagnosis +      │ │
│  │         Design Recommendations                                     │ │
│  │ Input: Task 2 output                                               │ │
│  │ Output: Final professional report (Markdown)                       │ │
│  └─────────────────────────────────────────────────────────────────────┘ │
│                            ▼                                              │
│  ┌────────────────────────────────────────────────────────────────────┐  │
│  │ FINAL OUTPUT: EPS Design Reference Report                         │  │
│  │                                                                    │  │
│  │ 1. Executive Summary                                             │  │
│  │ 2. Root Cause Diagnosis (panel failures, thermal issues, etc.)  │  │
│  │ 3. Lessons Learned                                              │  │
│  │ 4. Design Recommendations for future 1U CubeSats                │  │
│  └────────────────────────────────────────────────────────────────────┘  │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Data Flow Summary

```
Telemetry Data (Excel)
  ↓
[Load & Parse] → Numeric columns only
  ↓
[Baseline Computation]
  • Per-file stats (mean, std, min, max)
  • Global aggregation across all satellites
  • 3-Sigma calculation (confidence bounds)
  ↓
[Baseline Dictionary]
  {
    'by_satellite': {file: {param: stats}},
    'global': {param: stats}
  }
  ↓
[Single-Sample Input]  +  [Baselines]
          ↓                    ↓
     [Z-Score Calc]  ←─────────┘
          ↓
     [|z| ≥ 3?]
     /        \
   YES       NO
    ↓         ↓
[Anomaly]  [Normal]
    ↓         ↓
[Domain     [Skip]
 Heuristic]
    ↓
[Report]
    ↓
[Display + Optional LLM Interpretation]
```

---

## Implementation Details

### 1. Baseline Computation Algorithm

```python
def compute_baselines(folder_path: str) -> dict:
    baselines = {
        'by_satellite': {},
        'global': {}
    }

    # Step 1: Per-file analysis
    for each_xlsx_file in folder_path:
        df = read_xlsx(file)
        per_file_stats = {}

        for column in df.columns:
            s = describe(df[column])
            mean = s['mean']
            std = s['std']

            # Compute 3-sigma bounds
            low = mean - 3 * std
            high = mean + 3 * std

            per_file_stats[column] = {
                'mean': mean,
                'std': std,
                'min': s['min'],
                'max': s['max'],
                '3sigma_low': low,
                '3sigma_high': high
            }

        baselines['by_satellite'][file] = per_file_stats

    # Step 2: Global aggregation
    all_data = concatenate_all_dfs()
    for column in all_data.columns:
        # Repeat per-file logic on concatenated data
        ...

    return baselines
```

### 2. Anomaly Detection Algorithm

```python
def detect_and_explain_anomalies(sample_values, baselines, compare='global'):
    baseline_dict = baselines[compare]
    anomalies = []
    report = []

    for param, value in sample_values.items():
        stats = baseline_dict.get(param)
        if not stats:
            report.append(f"{param}: No baseline available")
            continue

        # Z-score calculation
        mean = stats['mean']
        std = stats['std']
        z = (value - mean) / std

        # Threshold check
        is_anomaly = abs(z) >= 3

        report.append(f"{param}: z={z:.2f}, {'ANOMALY' if is_anomaly else 'normal'}")

        # Domain interpretation (if anomaly)
        if is_anomaly:
            explanation = interpret_anomaly(param, z)
            anomalies.append((param, value, mean, std, z, explanation))

    # Format report
    output = "\n".join(report)
    if anomalies:
        output += "\n\n### Anomalies Detected:\n"
        for param, value, mean, std, z, expl in anomalies:
            output += f"- {param}: value={value}, mean={mean}, z={z}\n"
            output += f"  Explanation: {expl}\n"

    return output
```

### 3. Domain Interpretation Heuristics

```python
def interpret_anomaly(param, z):
    # Voltage parameters (vpx, vpy, vbatt, etc.)
    if 'v' in param.lower() and 't' not in param.lower():
        if z < -3:
            return "Voltage far below baseline → Panel fault, open circuit, shading"
        if z > 3:
            return "Voltage unusually high → Charging anomaly, measurement error"

    # Current parameters (ipx, ipy, ibatt, etc.)
    if 'i' in param.lower():
        if z > 3:
            return "Current much higher → Short circuit, increased load"
        if z < -3:
            return "Current much lower → Open circuit, degraded panel, disconnection"

    # Temperature parameters (tpx, tpy, tbatt, etc.)
    if 't' in param.lower():
        if z > 3:
            return "Temperature unusually high → Thermal runaway, poor cooling"
        if z < -3:
            return "Temperature unusually low → Sensor miscalibration, cold environment"

    return f"Deviation by {z:.2f}σ from baseline → Investigate"
```

---

## Integration Points

### 1. With Telemetry System

- **Input:** Excel files in `data/` folder (1,080 rows × 37 columns each)
- **Frequency:** Batch analysis (no real-time streaming)
- **Update:** Regenerate baselines when new data is available

### 2. With LLM System (CrewAI + Gemini)

- **Trigger:** If `SKIP_CREW=0` (or not set), after anomaly analysis
- **API:** Google Gemini 2.0 Flash (via `langchain-google-genai`)
- **Rate Limit:** Be aware of API quota (expensive for production)
- **Workaround:** Cache results, batch multiple analyses

### 3. With Dashboard (Angular, optional)

- **Backend:** `main.py` could expose REST API (not implemented yet)
- **Frontend:** `dashboard/` contains Angular components (not integrated yet)
- **Recommendation:** Add FastAPI or Flask wrapper for HTTP interface

---

## Testing Strategy

### Unit Tests (Recommended)

```python
# test_baselines.py
def test_compute_baselines_returns_dict():
    result = compute_baselines('./data')
    assert isinstance(result, dict)
    assert 'by_satellite' in result
    assert 'global' in result

def test_baseline_stats_valid_ranges():
    result = compute_baselines('./data')
    for param, stats in result['global'].items():
        assert stats['min'] <= stats['mean'] <= stats['max']
        assert stats['std'] >= 0
        assert stats['3sigma_low'] < stats['3sigma_high']

# test_anomalies.py
def test_detect_anomalies_normal_values():
    sample = {'vbatt': 7.2, 'ibatt': 0.1}  # Normal values
    report = detect_and_explain_anomalies(sample, baselines)
    assert 'Anomalies Detected' not in report

def test_detect_anomalies_extreme_values():
    sample = {'vbatt': 1.0}  # Very low voltage (should trigger anomaly)
    report = detect_and_explain_anomalies(sample, baselines)
    assert 'Anomalies Detected' in report
    assert 'vbatt' in report

def test_z_score_calculation():
    # vbatt mean=7.2, std=0.15
    # sample=5.5 → z=(5.5-7.2)/0.15=-11.33
    z = (5.5 - 7.2) / 0.15
    assert abs(z - (-11.33)) < 0.01
    assert abs(z) >= 3  # Should be flagged as anomaly
```

### Integration Tests

```python
# test_integration.py
def test_end_to_end_analysis_with_json_input():
    # 1. Load satellite_input.json
    # 2. Compute baselines
    # 3. Run anomaly detection
    # 4. Verify report contains expected sections

def test_multi_agent_crew_execution():
    # Requires GEMINI_API_KEY set
    # 1. Run crew.kickoff()
    # 2. Verify output is non-empty
    # 3. Check for professional report structure
```

---

## Deployment Checklist

- [ ] Generate `requirements.txt` from current environment
- [ ] Create `setup.py` or `pyproject.toml` for pip install
- [ ] Add `.env.example` template for users
- [ ] Add unit tests to `tests/` folder
- [ ] Add GitHub Actions CI/CD (lint, test, build)
- [ ] Document API rate limits and cost estimates
- [ ] Add CLI wrapper (argparse) for easier invocation
- [ ] Integrate with dashboard (REST API)
- [ ] Set up monitoring/logging for production
- [ ] Add versioning and changelog

---

## Performance Optimization Tips

1. **Cache Baselines:** Compute once, reuse for multiple samples
2. **Batch LLM Calls:** Combine multiple anomaly reports into single LLM request
3. **Use Async I/O:** For parallel Excel file reading (use `openpyxl` with threading)
4. **Profile Code:** Use `cProfile` to identify bottlenecks
5. **Consider Parquet:** Replace Excel with Parquet for faster I/O (100x+ speedup)
6. **Stream Processing:** For real-time data, use Kafka or similar event stream

---

**Last Updated:** November 10, 2025
