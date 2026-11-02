# Phase 7 — Real-Time Pipeline Architecture

## Overview
The Phase 7 pipeline transforms the frozen Phase 5/6 models (DistilBERT V2 and Causal LSTM V2) into a production-ready streaming service. It operates on a strict event-driven architecture designed to process speech incrementally.

## Components

1. **Audio / ASR Abstraction (`asr_provider.py`)**: 
   - Abstracts real-time audio streams into transcript chunks. 
   - Supports `MockASR` (for testing) and `ReplayASR` (for dataset benchmarking).
   
2. **Turn Segmenter (`turn_segmenter.py`)**:
   - Takes continuous ASR transcripts and chunks them into semantic "turns" based on speaker diarization and natural pauses.

3. **Temporal Risk Engine (`temporal_engine.py`)**:
   - Manages the execution flow.
   - For each finalized turn:
     - Passes text to the frozen **DistilBERT V2** classifier to extract 5 continuous tactic probabilities.
     - Passes the tactic vector and the session's current `hidden_state` to the frozen **Causal LSTM V2**.
     - Updates the session's overall `temporal_risk`.

4. **Explanation Engine (`explanation_engine.py`)**:
   - Converts numeric tactic probabilities and overall risk into human-readable, scientifically defensible explanations.
   - Generates alerts when the threshold ($\tau = 0.20$) is crossed.

5. **Session Manager (`session_manager.py`)**:
   - Strictly isolates LSTM hidden states between concurrent calls. 
   - Ensures memory leaks and state contamination do not occur.

6. **Websocket Handler (`websocket.py`)**:
   - Streams live transcripts, tactic updates, and risk events to the frontend dashboard.
