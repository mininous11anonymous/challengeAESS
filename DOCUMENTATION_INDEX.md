# 📚 Documentation Index & Quick Navigation

## 📋 Complete Documentation Set

This project includes comprehensive documentation covering all aspects of the CubeSat EPS anomaly detection system. Use this index to find what you need.

---

## 🎯 By Use Case

### "I want to understand what this project does"

→ Start here: **[QUICKSTART_VISUALS.md](QUICKSTART_VISUALS.md)**

- Visual diagrams
- Example scenarios
- Quick performance metrics
- ~5 min read

### "I want to know the exact algorithms & metrics"

→ Read: **[METRICS_CHEATSHEET.md](METRICS_CHEATSHEET.md)**

- Z-score formula
- 3-sigma threshold
- Domain heuristics table
- Performance metrics
- ~10 min read

### "I need complete technical details"

→ Read: **[PROJECT_REPORT.md](PROJECT_REPORT.md)**

- Full project structure
- Tech stack
- Algorithm specifications
- Data characteristics
- Limitations & future work
- ~30 min read

### "I'm deploying this or need architecture details"

→ Read: **[ARCHITECTURE.md](ARCHITECTURE.md)**

- System architecture diagram
- Data flow diagrams
- Implementation details (pseudocode)
- Integration points
- Testing strategy
- Deployment checklist
- ~40 min read

### "I just want to run it"

→ Start here: **[README.md](README.md)**

- Quick-start setup (5 min)
- Command examples
- Environment variables
- File locations
- ~15 min read

---

## 📄 Documentation Files at a Glance

| File                       | Size        | Purpose                                  | Read Time | Audience                  |
| -------------------------- | ----------- | ---------------------------------------- | --------- | ------------------------- |
| **QUICKSTART_VISUALS.md**  | 10.4 KB     | Visual overview, examples, quick metrics | 5 min     | Everyone                  |
| **README.md**              | 3.56 KB     | Setup, installation, quick start         | 15 min    | End users                 |
| **METRICS_CHEATSHEET.md**  | 4.54 KB     | Formulas, thresholds, quick reference    | 10 min    | Analysts, Data Scientists |
| **PROJECT_REPORT.md**      | 20.65 KB    | Complete technical specification         | 30 min    | Engineers, Architects     |
| **ARCHITECTURE.md**        | 21.7 KB     | System design, integration, deployment   | 40 min    | DevOps, Backend Engineers |
| **DOCUMENTATION_INDEX.md** | (this file) | Navigation guide                         | 5 min     | Everyone                  |

**Total Documentation:** ~60 KB (~90 minutes total read time)

---

## 🔍 Topic Quick-Finder

### Anomaly Detection

- **How it works:** QUICKSTART_VISUALS.md → "Anomaly Detection Flow"
- **Math formula:** METRICS_CHEATSHEET.md → "Core Algorithm: Z-Score Method"
- **Detailed algorithm:** PROJECT_REPORT.md → Section 3.2
- **Example:** ARCHITECTURE.md → Section 2 (Data Flow)

### Baseline Computation

- **Overview:** QUICKSTART_VISUALS.md → "3-Sigma Bounds"
- **Formula:** METRICS_CHEATSHEET.md → "Baseline Computation"
- **Full spec:** PROJECT_REPORT.md → Section 3.1
- **Code pseudocode:** ARCHITECTURE.md → Section 1.1

### Multi-Agent AI (CrewAI)

- **Overview:** QUICKSTART_VISUALS.md → "Multi-Agent AI Pipeline"
- **Agents & tasks:** PROJECT_REPORT.md → Section 3.3
- **Flow diagram:** ARCHITECTURE.md → System Architecture Diagram (middle section)

### Setup & Execution

- **Installation:** README.md → Quick Start
- **Running analyzer only:** QUICKSTART_VISUALS.md → "Quick Execution Guide"
- **Environment variables:** PROJECT_REPORT.md → Section 9
- **Full integration:** ARCHITECTURE.md → Section 3

### Testing

- **Strategy:** ARCHITECTURE.md → Section 4 (Testing Strategy)
- **Unit tests:** ARCHITECTURE.md → Testing Strategy → Unit Tests
- **Integration tests:** ARCHITECTURE.md → Testing Strategy → Integration Tests

### Deployment

- **Checklist:** ARCHITECTURE.md → Deployment Checklist
- **Performance optimization:** ARCHITECTURE.md → Performance Optimization Tips
- **Limitations:** PROJECT_REPORT.md → Section 10

### Metrics & Performance

- **Quick metrics:** METRICS_CHEATSHEET.md → Performance Metrics
- **Detailed performance:** PROJECT_REPORT.md → Section 8
- **Example calculations:** QUICKSTART_VISUALS.md → Example Anomaly Scenarios

---

## 🚀 Recommended Reading Order

### For First-Time Users

1. README.md (setup)
2. QUICKSTART_VISUALS.md (what it does)
3. METRICS_CHEATSHEET.md (quick reference)

### For Data Scientists/Analysts

1. METRICS_CHEATSHEET.md (formulas)
2. QUICKSTART_VISUALS.md (examples)
3. PROJECT_REPORT.md (full specs)

### For Software Engineers/DevOps

1. README.md (setup)
2. ARCHITECTURE.md (design)
3. PROJECT_REPORT.md (full specs)

### For Decision Makers/PMs

1. QUICKSTART_VISUALS.md (overview)
2. PROJECT_REPORT.md → Section 8 (Performance)
3. PROJECT_REPORT.md → Section 10 (Limitations & Future Work)

---

## 📊 Key Metrics Summary (From Across Docs)

| Aspect                          | Value                                 | Reference                                |
| ------------------------------- | ------------------------------------- | ---------------------------------------- |
| **Core Algorithm**              | Z-score (99.7% confidence)            | METRICS_CHEATSHEET.md                    |
| **Anomaly Threshold**           | \|z\| ≥ 3 (3-sigma)                   | PROJECT_REPORT.md, QUICKSTART_VISUALS.md |
| **Sensitivity**                 | 99.7%                                 | PROJECT_REPORT.md Section 8              |
| **Specificity**                 | 99.7%                                 | PROJECT_REPORT.md Section 8              |
| **Processing Time (Analyzer)**  | 50–100 ms                             | PROJECT_REPORT.md Section 8              |
| **Processing Time (Full Crew)** | 2–10 min                              | PROJECT_REPORT.md Section 8              |
| **Satellites**                  | 4 (NEPALISAT, RAAVANA, TSURU, UGUISU) | PROJECT_REPORT.md Section 1              |
| **Records per Satellite**       | 1,080                                 | PROJECT_REPORT.md Section 1              |
| **Total Measurements**          | ~160,000                              | QUICKSTART_VISUALS.md                    |
| **Parameters Tracked**          | 37 (V, I, T)                          | PROJECT_REPORT.md Section 1              |

---

## 🔗 Cross-References

### If you're reading PROJECT_REPORT.md and want more detail on:

- **Algorithms** → See METRICS_CHEATSHEET.md
- **Architecture** → See ARCHITECTURE.md
- **Examples** → See QUICKSTART_VISUALS.md
- **Setup** → See README.md

### If you're reading ARCHITECTURE.md and want:

- **Implementation code** → See main.py (in project root)
- **Quick reference** → See METRICS_CHEATSHEET.md
- **Full specs** → See PROJECT_REPORT.md
- **Visual examples** → See QUICKSTART_VISUALS.md

### If you're reading METRICS_CHEATSHEET.md and want:

- **More detail** → See PROJECT_REPORT.md Section 3
- **Visual examples** → See QUICKSTART_VISUALS.md
- **Code samples** → See ARCHITECTURE.md Section 1
- **Setup help** → See README.md

---

## 💾 Key Source Code References

| Topic                | File                   | Function(s)                          |
| -------------------- | ---------------------- | ------------------------------------ |
| Baseline Computation | `main.py`              | `compute_baselines()`                |
| Anomaly Detection    | `main.py`              | `detect_and_explain_anomalies()`     |
| Data Loading         | `main.py`              | `data_loading_tool()`                |
| Multi-Agent Pipeline | `main.py`              | `agent1`, `agent2`, `agent3`, `crew` |
| Sample Input         | `satellite_input.json` | (data file)                          |

---

## 📞 Getting Help

**Question** → **Where to Find Answer**

- _How do I run the analyzer?_ → README.md § Quick Start
- _What is z-score?_ → METRICS_CHEATSHEET.md § Core Algorithm
- _How are anomalies explained?_ → PROJECT_REPORT.md § Section 3.2
- _What's the overall architecture?_ → ARCHITECTURE.md § System Architecture Diagram
- _What are the performance metrics?_ → PROJECT_REPORT.md § Section 8
- _How do I deploy this?_ → ARCHITECTURE.md § Deployment Checklist
- _What are the limitations?_ → PROJECT_REPORT.md § Section 10
- _What are next steps?_ → PROJECT_REPORT.md § Section 10 OR ARCHITECTURE.md § Deployment Checklist

---

## 🎓 Learning Path by Role

### 👨‍💼 Project Manager

**Goal:** Understand what the project does and its capabilities

1. QUICKSTART_VISUALS.md (10 min)
2. PROJECT_REPORT.md § 8 Performance (5 min)
3. PROJECT_REPORT.md § 10 Limitations (5 min)

### 👨‍🔬 Data Scientist

**Goal:** Understand algorithms and metrics in detail

1. METRICS_CHEATSHEET.md (10 min)
2. PROJECT_REPORT.md § 3 Algorithms (20 min)
3. ARCHITECTURE.md § 2 Data Flow (10 min)

### 👨‍💻 Software Engineer

**Goal:** Understand how to integrate and extend

1. README.md (15 min)
2. ARCHITECTURE.md (40 min)
3. PROJECT_REPORT.md § 10 Future Work (10 min)

### 👨‍🔧 DevOps Engineer

**Goal:** Understand deployment, performance, and scaling

1. README.md (15 min)
2. ARCHITECTURE.md § Deployment Checklist (20 min)
3. ARCHITECTURE.md § Performance Optimization (10 min)

### 📊 Analyst / End User

**Goal:** Understand what the system outputs and how to interpret it

1. QUICKSTART_VISUALS.md § Example Anomaly Scenarios (10 min)
2. METRICS_CHEATSHEET.md § Domain-Specific Heuristics (5 min)
3. PROJECT_REPORT.md § 6 Sample Input & Output (10 min)

---

## 📈 Documentation Coverage

This documentation covers:

- ✅ **What:** Project purpose, goals, capabilities
- ✅ **How:** Algorithms, formulas, step-by-step workflows
- ✅ **Why:** Design decisions, metric choices, approach justification
- ✅ **Setup:** Installation, configuration, quick start
- ✅ **Examples:** Real-world scenarios, sample data, expected outputs
- ✅ **Performance:** Speed, accuracy, scalability metrics
- ✅ **Deployment:** Checklists, best practices, optimization tips
- ✅ **Testing:** Unit tests, integration tests, validation
- ✅ **Limitations:** Known issues, assumptions, constraints
- ✅ **Future:** Enhancement ideas, next steps, roadmap

**Not covered (but available in code):**

- Line-by-line code comments (see `main.py` for in-code documentation)
- API endpoint specifications (not yet exposed as REST API)
- Frontend integration guide (Angular dashboard not fully integrated)

---

## 🔄 How to Update Documentation

If you modify the project:

1. Update relevant section in PROJECT_REPORT.md
2. Update related formula/metric in METRICS_CHEATSHEET.md
3. Update example in QUICKSTART_VISUALS.md if needed
4. Update architecture if design changes in ARCHITECTURE.md
5. Update setup instructions in README.md if dependencies change

---

## ✨ Summary

You now have **5 comprehensive documentation files** covering:

- 📖 **Project specification** (PROJECT_REPORT.md)
- 📐 **Metrics & algorithms** (METRICS_CHEATSHEET.md)
- 🏗️ **System design** (ARCHITECTURE.md)
- 🚀 **Quick setup** (README.md)
- 🎨 **Visual guides** (QUICKSTART_VISUALS.md)
- 📚 **Navigation** (this file)

**Total:** ~90 minutes to read everything; ~15 minutes to get started.

---

**Last Updated:** November 10, 2025  
**Documentation Status:** ✅ Complete & Comprehensive
