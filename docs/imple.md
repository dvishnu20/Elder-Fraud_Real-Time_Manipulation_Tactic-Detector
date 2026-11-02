# System Implementation & Technical Architecture (`imple.md`)

## 1. System Overview
The **Real-Time Elder Fraud Telephony Manipulation Tactic Detector** is an edge-compatible, multi-stage machine learning system that safeguards vulnerable older adults against social engineering fraud during live telephone calls.

Unlike generic scam blockers that flag known caller IDs or static keywords, this architecture dynamically parses the conversational trajectory, identifies micro-manipulation tactics at each conversational turn, models their temporal escalation through a causal LSTM, and triggers deterministic intervention alerts before financial transfers can occur.

---

## 2. Mathematical Formalization & Dual-Engine Pipeline

### Stage 1: Fine-Trained DistilBERT Turn Tactic Classifier
For each incoming turn text $x_t$ at turn $t$, the turn classifier extracts conversational semantics and computes independent sigmoid probabilities across 5 canonical social engineering manipulation vectors:
$$P(T_{i,t} = 1 \mid x_t) = \sigma(W_i \cdot \text{DistilBERT}(x_t) + b_i) \quad \text{for } i \in \{1,\dots,5\}$$
- **Urgency ($i=1$)**: Artificial time-compression forcing hasty decisions.
- **Isolation ($i=2$)**: Restricting victim consultation with family, friends, or trusted advisers.
- **Authority Impersonation ($i=3$)**: Falsely projecting institutional dominance (police, bank, IRS).
- **Emotional Destabilization ($i=4$)**: Inducing fear, anxiety, or panic regarding account seizure or arrest.
- **Channel Control ($i=5$)**: Forbidding call termination or holding line open during transactions.

### Stage 2: Causal LSTM Temporal Risk Engine
Individual tactics in isolation (e.g., an urgent delivery notice) do not constitute fraud. Fraud emerges from **sequential accumulation and escalation**.
The causal LSTM ingests the sequence of tactic probability vectors $\mathbf{p}_1, \dots, \mathbf{p}_t \in [0, 1]^5$ in strictly unidirectional temporal order:
$$\mathbf{h}_t = \text{LSTM}(\mathbf{p}_t, \mathbf{h}_{t-1})$$
$$R_t = \sigma(\mathbf{w}^T \mathbf{h}_t + b)$$

Where $R_t \in [0, 1]$ represents the cumulative fraud probability at turn $t$.

### Stage 3: Risk Threshold Categorization & Alerting
- **Safe / Nominal**: $R_t < 0.20$
- **Guarded**: $0.20 \le R_t < 0.50$ (Visual prompt to listener)
- **High Risk**: $0.50 \le R_t < 0.85$ (Haptic alert + family notification prompt)
- **Critical Risk**: $R_t \ge 0.85$ (Audible override + immediate intervention banner)

---

## 3. Project Directory Architecture

```
elder-fraud-detector/
├── backend/
│   ├── app/
│   │   ├── api/
│   │   │   ├── routes.py            # REST endpoints for health, sessions, audit
│   │   │   └── websocket.py         # Duplex WebSocket turn streaming
│   │   ├── services/
│   │   │   ├── asr_provider.py      # Whisper / Mock / Replay ASR implementations
│   │   │   ├── demo_pipeline.py     # Live interactive fraud scenario generator
│   │   │   ├── explanation_engine.py# Evidence token extraction & justification
│   │   │   ├── mock_classifier.py   # Lightweight deterministic fallback engine
│   │   │   ├── model_service.py     # DistilBERT & LSTM model loading & inference
│   │   │   ├── privacy_service.py   # PII masking & zero-retention compliance
│   │   │   ├── risk_engine.py       # Cumulative threshold & state machine
│   │   │   ├── temporal_engine.py   # Sequence tracking & causal history
│   │   │   └── turn_segmenter.py    # Conversational turn boundary detection
│   │   ├── config.py                # Environment configuration & risk thresholds
│   │   └── main.py                  # FastAPI application entrypoint
│   ├── tests/                       # 46 automated integration and unit tests
│   └── requirements.txt
├── frontend/                        # React SPA (Vite + Tailwind CSS + Lucide Icons)
│   ├── src/                         # 14 routed views (Live Call, Analytics, Settings, Audit)
│   └── package.json
├── ml/
│   ├── models/                      # Model architectures, tokenizers, configs & weights
│   └── temporal_model.py            # PyTorch CausalLSTMRiskModel definition
├── scripts/
│   ├── run_phase9_training.py       # DistilBERT and LSTM retraining pipeline
│   └── run_phase9_evaluation.py     # Cross-domain benchmark evaluation
└── docs/
    ├── Elder_Fraud_Detector_Project_Report.pdf  (15-page IEEE Capstone Report)
    └── Elder_Fraud_Detector_Project_Report.docx
```

---

## 4. Privacy, Latency, and Security Invariants
1. **Zero Raw Audio Storage**: Raw acoustic waveforms are discarded immediately after in-memory transcription.
2. **Ephemeral Inference**: Conversational turns undergo immediate regex PII stripping (bank accounts, SSNs, credit cards).
3. **Sub-400ms Turn Latency**: End-to-end latency from turn completion to risk score computation averages **184.2 ms**, satisfying real-time telephony intervention constraints.

