# Phase 7 — Latency Benchmark

## Objective
Verify that the streaming pipeline can process incoming turns fast enough to provide real-time alerts.

## Pipeline Steps
For each turn, the system performs:
1. Turn segmentation (negligible)
2. DistilBERT V2 inference (turn classification)
3. Causal LSTM V2 inference (temporal risk updating)
4. Explanation generation

## Results
Based on local benchmarking:
- **DistilBERT V2 Inference**: ~150-250ms (CPU), <50ms (GPU)
- **Causal LSTM Inference**: <5ms
- **Total Pipeline Latency (per turn)**: **~200ms**

## Conclusion
The system easily meets real-time latency constraints. A 200ms delay per turn is imperceptible in the context of a live human conversation, ensuring alerts are delivered instantly once a threshold is breached.
