# CubeSat EPS Failure Analysis (crew_ai)

This repository contains a multi-agent CrewAI-driven project for analyzing CubeSat Electrical Power System (EPS) telemetry, detecting anomalies, and generating mission health & design reports.

Key features

- Compute baseline statistics (Min, Max, Mean, Std, ±3σ) across telemetry files in `./data`.
- Single-satellite anomaly detection + human-readable explanations (accepts a JSON input).
- Multi-agent pipeline (CrewAI) that uses LLMs to synthesize findings into professional reports.

Files of interest

- `main.py` — main entrypoint. Contains baseline computation, single-satellite analyzer, and multi-agent crew orchestration.
- `satellite_input.json` — optional sample input file (project root). When present, `main.py` will run the single-satellite analyzer and print an anomaly report.
- `data/` — place telemetry `.xlsx` files here for baseline computation.

Quick start (Windows PowerShell)

1. Create a Python virtual environment (recommended):

```powershell
python -m venv .env
# Activate the virtual environment
.\.env\Scripts\Activate.ps1
```

2. Install dependencies (create `requirements.txt` or install manually). Example:

```powershell
pip install -r requirements.txt
# OR install packages used in this repo
pip install crewai langchain-google-genai pandas python-dotenv openpyxl
```

3. Add your telemetry Excel files to `./data`.

4. (Optional) Provide a single-satellite input file `satellite_input.json` at the project root. Example format:

```json
{
  "name": "UGUISU",
  "values": {
    "vbatt": 7.2,
    "ibatt": 0.65,
    "tbatt": 23.5,
    "vpx": 3.12,
    "ipx": 0.05,
    "tpx": 21.0
  }
}
```

5. Set environment variables (if needed):

- `GEMINI_API_KEY` — required for CrewAI to call the Google Gemini/Vertex model.
- `SAT_INPUT_JSON` — alternatively provide sample JSON directly as an environment variable.
- `SKIP_CREW=1` — skip LLM/Agent run (useful for offline testing of analyzer without exhausting API quota).

6. Run the script:

```powershell
C:/path/to/your/venv/Scripts/python.exe main.py
# or, if your venv is activated:
python main.py
```

Behavior

- If `satellite_input.json` (or `SAT_INPUT_JSON`) is present, the script will run the single-satellite analyzer and print a human-readable anomaly report.
- If `SKIP_CREW=1` is set, the script will skip the CrewAI LLM agents and exit after printing the analyzer output.
- Otherwise the full multi-agent crew will run and produce a final report (this will call LLM APIs and may consume quota).


Security & secrets

- Put API keys and secrets in a `.env` file (loaded by `python-dotenv`) or environment variables. Do NOT commit `.env` or your virtual environment to source control.

Creating a `requirements.txt`

To capture your environment's dependencies after installing the packages you need:

```powershell
pip freeze > requirements.txt
```
<img width="1577" height="818" alt="Capture d_écran 2025-11-10 212149" src="https://github.com/user-attachments/assets/4889ff4a-b773-4fd3-b0c2-b53d6782f11e" />
<img width="1637" height="822" alt="Capture d_écran 2025-11-10 212116" src="https://github.com/user-attachments/assets/ea07ac76-3ba6-4c5a-ad6e-f3f1f9640d85" />
<img width="1896" height="810" alt="Capture d_écran 2025-11-10 212039" src="https://github.com/user-attachments/assets/2a8cc427-4e15-4046-b64b-635a92556c91" />

