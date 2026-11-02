# Real-Time Elder Fraud Manipulation Tactic Detector - Run & Demo Guide

This guide provides step-by-step instructions to set up the environment, run the backend API, start the React frontend dashboard, and execute the automated test suites.

---

## 1. Prerequisites
- **Python 3.10+** (64-bit)
- **Node.js 18+** & npm
- **Git**

---

## 2. Setting Up Backend Environment

Open PowerShell and navigate to the backend folder:
```powershell
cd "c:\Users\duddu\Downloads\ELDERY LIVE TATIC DETECTOR\elder-fraud-detector\backend"
python -m venv venv
.\venv\Scripts\activate
pip install --upgrade pip
pip install -r requirements.txt
pip install "httpx==0.27.2"
```

---

## 3. Running Automated Unit Tests (46 Tests)

Run the full integration and unit test suite:
```powershell
cd "c:\Users\duddu\Downloads\ELDERY LIVE TATIC DETECTOR\elder-fraud-detector\backend"
.\venv\Scripts\activate
pytest tests/ -v
```

All 46 tests across:
- `test_phase7.py` (API health, endpoints, models)
- `test_risk_engine.py` (Cumulative score, threshold breaches)
- `test_temporal_engine.py` (Causal ordering, sequence scoring)
- `test_websocket.py` (Real-time duplex streaming)
will run and pass.

---

## 4. Starting the Real-Time Backend API Server

In a terminal, run:
```powershell
cd "c:\Users\duddu\Downloads\ELDERY LIVE TATIC DETECTOR\elder-fraud-detector\backend"
.\venv\Scripts\activate
uvicorn app.main:app --reload --host 127.0.0.1 --port 8000
```
- **Backend API URL**: `http://127.0.0.1:8000`
- **Swagger Interactive Docs**: `http://127.0.0.1:8000/docs`
- **WebSocket Endpoint**: `ws://127.0.0.1:8000/api/stream/{session_id}`

---

## 5. Setting Up and Running the React Frontend

Open a second PowerShell terminal:
```powershell
cd "c:\Users\duddu\Downloads\ELDERY LIVE TATIC DETECTOR\elder-fraud-detector\frontend"
npm install
npm run dev
```
- **Frontend Dashboard URL**: `http://localhost:5173`

---

## 6. Running the Live Demo Call Simulation

Once both backend and frontend are running:
1. Open your browser to `http://localhost:5173`.
2. Navigate to the **Live Call Monitor** page.
3. Click **"Start Live Demo Call"**.
4. Observe real-time turn ingestion:
   - Turn 1: *“Hello, I'm calling from your bank's fraud department.”* -> **Authority (0.85)**
   - Turn 3: *“Suspicious activity detected, your account is compromised.”* -> **Emotional Destabilization (0.75)**
   - Turn 5: *“You need to resolve this within ten minutes or lose everything.”* -> **Urgency (0.80)** -> Risk enters **Guarded**.
   - Turn 7: *“Don't call your daughter, keep this strictly between us.”* -> **Isolation (0.90)** -> Risk elevates to **High**.
   - Turn 9: *“Stay on the line while I guide the transfer.”* -> **Channel Control (0.88)** -> Risk breaches **Critical (Score > 0.85)**.
5. An immediate **Critical Elder Fraud Intervention Alert** is triggered with evidence IDs and intervention recommendations.

