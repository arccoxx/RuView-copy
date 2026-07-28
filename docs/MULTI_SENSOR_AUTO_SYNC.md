# Multi-Sensor Adaptive Auto-Sync for TimeView / RuView

*Karpathy-style inspirational seed document — include in any agent/LLM prompt for flexible multi-sensor 4D radio sensing workflows.*

## Goal

Support **any number of sensors** that **auto-coordinate and intrinsically synchronize** without explicit master clocks, NTP, or manual pairing. Adaptive mode uses however many sensors are present in the environment. Cover every practical topology variation while feeding the unsupervised 4D materiality + movement reconstruction (MRI-like via physics-informed wave operators from timemachines).

**Intrinsic / Implicit Synchronization** is non-negotiable: synchronization emerges from the temporal field operators themselves (phase, multipath, spectral cross-correlation, dual-time consistency, WaveGraph consensus, STL event alignment) rather than external time protocols.

## Topology Variations Supported

1. **Ambient multi-sensor around a single user**  
   Fixed ESP32s / commodity APs as passive illuminators + one (or more) wearable probe(s). Classic multistatic passive radar.

2. **Multiple sensors worn on the same user**  
   Body-centric mesh (wrist, chest, ankle, etc.). Near-field diversity + self-calibration of body materiality. Improves observability for inverse scattering.

3. **Self-connecting environmental sensors**  
   - Fixed / external (wall, ceiling, furniture) that discover and join the mesh.  
   - Worn by the same or other users. Sensors announce via CSI signatures / RF beacons and are accepted via unsupervised clustering on temporal embeddings.

4. **Multi-person tracking with heterogeneous sensor counts**  
   Person A wears 1 ESP32, Person B wears 3, ambient 4 fixed nodes. System clusters streams into per-person trajectories + shared volume materiality field. Different people may have different numbers of worn sensors; adaptive assignment handles it.

5. **Mixed / dynamic**  
   Sensors appear/disappear, move between people, or change role (illuminator ↔ probe). Graph is dynamic; reconstruction continues with whatever subset is live.

## Core Architecture: Adaptive Multi-Sensor Coordinator

Inspired by multi-agent temporal models and temporal-computer principles in the proprietary timemachines library (Dual-time, WaveGraphPropagator, FFTTemporalMixer, TemporalLogicProcessor / STL, SyntheticWaveField, spectral collapse, DEQ fixed-point).

### SensorNode Abstraction
```text
SensorNode:
  id, role (illuminator | probe | hybrid), location_prior (optional),
  stream: CSI time × subcarriers × (amp, phase),
  temporal_field: X_i(t) constructed via FFT / LIF / spectral projection,
  join_signature: unsupervised embedding for discovery
```

### AutoSyncCoordinator (Adaptive, Intrinsic)
- **Discovery & Join**: New sensor appears → emit short CSI / beacon → compute temporal embedding (RuVector-style or spectral features) → unsupervised match or new cluster. No pairing codes.
- **Intrinsic Clock Alignment**:
  - Shared ambient illuminators produce common multipath signatures → cross-correlation / phase matching in FFT domain estimates relative offsets (no NTP).
  - Dual-time / dual-radial operators enforce causal + anti-causal consistency across streams.
  - WaveGraph on the sensor topology: nodes = sensors, edges = multipath / proximity; Chebyshev wave propagation + damping produces consensus fields that are phase-aligned by construction.
  - STL / TemporalLogic predicates: `eventually(align(A,B))`, `always(phase_diff < ε)`, event-based alignment on micro-motions or shared events.
- **Adaptive Graph Construction**: Dynamic SensorGraph whose size = current live sensors. Fixed-mode spectral collapse (project each stream to fixed M modes) keeps downstream cost in the constant-time family regardless of N_sensors.
- **Person / Body Assignment**: Unsupervised clustering of worn sensors into person-level groups using trajectory coherence + near-field materiality priors. Ambient sensors remain shared volume nodes.
- **Forward / Inverse**: All aligned streams feed the same SyntheticWaveField / physics residual loss for 4D volume reconstruction. More sensors = better observability, same unsupervised objective.

### Constant-Time Friendly Paths (from PERFORMANCE_AUDIT)
- Early spectral projection of every sensor stream to fixed M modes → O(1) sequential depth after setup.
- Fixed-K WaveGraph on sensor topology + volume voxels.
- DEQ / fixed-point for multi-stream consistency refinement.
- Dynamic reweighting / sheaf for fast adaptation when sensors join/leave.

## Implementation Roadmap (on main)

1. `python/timeview/sensor_node.py` — abstract SensorNode + CSI stream adapter.
2. `python/timeview/auto_sync.py` — AutoSyncCoordinator with intrinsic alignment (spectral cross-corr, DualTime fusion, WaveGraph consensus).
3. Dynamic SensorGraph + person clustering module.
4. Adaptive mode flag: `coordinator.adapt(sensors)` uses whatever is present; no fixed topology required.
5. Firmware / ESP32 side: lightweight beacon + high-rate CSI streaming that self-announces.
6. Evaluation: synthetic multi-sensor FDTD with known clock offsets; measure residual alignment error and reconstruction fidelity vs. number of sensors.

## Prompt Inclusion (Platform-Agnostic)

"You are extending TimeView / RuView with adaptive multi-sensor intrinsic auto-synchronization. Prefer discovery via temporal embeddings, clock alignment via spectral phase / DualTime / WaveGraph consensus / STL predicates (no explicit NTP), support ambient + multi-worn + multi-person heterogeneous topologies, keep operators in the constant-time family via fixed-mode projection. Reference docs/MULTI_SENSOR_AUTO_SYNC.md and docs/TIMEVIEW_4D_ENHANCEMENT.md."

This document is the living seed. Saturate with code, experiments, and ADRs on main.
