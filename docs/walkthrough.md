# Project Walkthrough (`walkthrough.md`)

This document provides a walkthrough of the Real-Time Elder Fraud Telephony Manipulation Tactic Detector system, explaining each screen, component, and operational lifecycle.

---

## 1. System Lifecycle Walkthrough

### Step 1: Telephony Audio Ingestion & Turn Segmentation
- Live telephony audio streams into `turn_segmenter.py` and `vad_service.py`.
- Voice Activity Detection (VAD) monitors speech pauses.
- Once a pause exceeds 650 ms, the audio segment is converted to text via `asr_provider.py`.

### Step 2: Privacy Filter & PII Stripping
- `privacy_service.py` intercepts the raw transcript string.
- Sensitive entities (Social Security Numbers, 16-digit credit cards, bank routing numbers, telephone numbers) are masked using SHA-256 salted hashes and replaced with `[REDACTED_FINANCIAL]`.

### Step 3: Turn-Level Tactic Prediction
- The clean turn transcript is passed to `model_service.py` (or `mock_classifier.py` for local demos).
- Five binary classifiers evaluate the presence of:
  1. **Authority** (bank fraud investigator, law enforcement officer)
  2. **Emotional Destabilization** (threat of arrest, legal forfeiture)
  3. **Urgency** (countdown timers, immediate wire demands)
  4. **Isolation** (prohibiting contact with children or bank tellers)
  5. **Channel Control** (demanding victim remain continuously on the call)

### Step 4: Temporal Escalation Modeling
- The tactic probability vector is appended to the session's causal sequence history.
- The Causal LSTM processes the accumulated sequence unidirectionally.
- When combinations like `Authority -> Fear -> Urgency -> Isolation` occur, the risk score spikes exponentially faster than when isolated tactics appear.

### Step 5: Frontend Alert Delivery
- The computed risk score and tactic breakdowns are streamed over WebSocket (`/api/stream/{session_id}`).
- The React dashboard updates dynamically in sub-second time.
- If risk $\ge 0.85$, a high-priority intervention dialog renders on the screen, detailing evidence phrases and recommended actions.

---

## 2. Frontend Interface Overview

1. **Dashboard Overview**: Summary of active sessions, total calls analyzed, high-risk detection rate, and average latency.
2. **Live Call Monitor**: Real-time duplex transcript stream with speaker tags (`Caller` vs. `Elderly Recipient`), live tactic badges, and a dynamic risk gauge.
3. **Historical Audit**: Searchable registry of prior call sessions, evidence trails, and PII-sanitized logs.
4. **Model Performance**: Visualizations of precision-recall curves, ROC curves, latency benchmarks, and ablation studies.
5. **Settings & Thresholds**: Configurable risk boundaries for Guarded ($0.20$), High ($0.50$), and Critical ($0.85$).

