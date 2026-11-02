# Phase 7 — Causality Audit

## Objective
The fundamental flaw of the Phase 2 dataset generation was bidirectional context (future leakage). Phase 7 must mathematically and programmatically guarantee that prediction at Turn $t$ relies *exclusively* on Turns $\le t$.

## Verification Design
1. **Incremental Architecture**: The `TemporalRiskEngine` processes data exactly one turn at a time. It cannot access future turns because they literally have not arrived over the WebSocket yet.
2. **LSTM Hidden State Isolation**: The LSTM relies entirely on $h_{t-1}$ to contextualize the past.
3. **Automated Causality Testing**: The test suite (`test_causal_behavior`) evaluates identical prefixes with and without future turns appended, asserting that the risk output at step $t$ is exactly the same to 6 decimal places regardless of what follows.

## Results
- `test_no_future_leakage`: **PASSED**
- `test_prefix_consistency`: **PASSED**
- `test_session_isolation`: **PASSED** (proving hidden states do not cross-contaminate parallel sessions).

## Conclusion
The real-time streaming pipeline strictly preserves causality. Future leakage is eradicated.
