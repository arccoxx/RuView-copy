# Multi-Sensor Intrinsic Auto-Sync & Adaptive Coordination

*Inspirational seed (Karpathy-style) for TimeView / RuView. Include this document (or excerpts) in any agent / LLM prompt working on multi-radio sensing.*

## Goal

Support **arbitrary numbers and topologies** of CSI / WiFi / RF sensors with **intrinsic, implicit auto-synchronization**. No master clock, no explicit NTP, no central time server required for alignment. Adaptive mode discovers and incorporates whatever sensors appear in the environment.

Inspiration drawn from:
- timemachines multi-agent temporal model (strong many-stream latency/accuracy wins)
- Transducers for multi-agent coordination
- Dual-time / Dual-timewave / Dual-radial processors
- WaveGraphPropagator + learnable damping
- FFTTemporalMixer spectral phase matching
- TemporalLogicProcessor / MaskedSTLUntil path predicates
- Existing RuView firmware: `rv_mesh`, `swarm_bridge`, ESP-NOW / timesync, adaptive_controller

## Supported Topologies (all handled by the same adaptive path)

1. **Ambient fixed sensors around one or more users**  
   Classic multistatic passive radar (routers + ESP32s + any WiFi AP).

2. **Multiple sensors worn on the *same* user**  
   Body-relative topology prior (rigid or soft-body model). Near-field calibration from wearable diversity.

3. **Self-connecting environment sensors**  
   Fixed or mobile nodes that join via RF discovery / CSI signature / unsupervised embedding similarity. No pre-configuration.

4. **Multiple people, heterogeneous sensor counts**  
   0..K worn sensors per person + shared ambient. Track every person; assign streams to persons via clustering on trajectory + materiality signatures (RuVector-style embeddings).

5. **Arbitrary mixes** of the above. Sensors can be ESP32, commodity routers, phones, mmWave co-located, etc.

The coordinator is **adaptive**: it uses however many sensors are present. Adding or removing a node triggers dynamic SensorGraph update (or fixed-rank approximation to stay in the constant-time family).

## Intrinsic / Implicit Synchronization Mechanisms

All mechanisms avoid explicit shared clocks. Alignment emerges from the radio physics and temporal operators:

| Mechanism | How it aligns | Constant-time notes |
|---|---|---|
| **Spectral phase / cross-correlation** | FFTTemporalMixer on shared multipath or ambient illuminator signatures estimates relative clock offsets + phase in frequency domain | Fixed FFT size or fixed-mode projection → O(1)/O(log) after pad |
| **WaveGraph consensus** | Sensors (or spatial bins they illuminate) as nodes; WaveGraphPropagator + damping propagates fields so local observations auto-align via physics consistency / multipath agreement | Fixed-K Chebyshev; optional Nyström / fixed-rank for large N |
| **Dual-time / Dual-radial fusion** | Forward + anti-time (or boundary-shell) paths across sensors enforce causal + reverse consistency | Fixed 2× fusion, independent of further recursion |
| **TemporalLogic / STL predicates** | Soft event alignment: `eventually(motion_A ∧ motion_B)`, path predicates over volume changes without hard timestamps | Tensorized parallel windows → O(1) sequential steps |
| **Physics-informed residual** | SyntheticWaveField / inverse-scattering loss penalizes desynchronized multipath projections | DEQ / fixed-iter or fixed-readout adaptation |
| **Multi-agent temporal / Transducer** | Parallel latent streams of all sensors coordinated in one wide temporal field (timemachines multi-agent path) | Parallel GPU field evaluation |

Result: streams become aligned in a joint temporal field X(t, sensors) ready for 4D volume reconstruction.

## Adaptive Mode Details

- **Discovery**: Sensors advertise via periodic CSI beacon, side-channel, or pure RF fingerprint. Unsupervised join via embedding similarity or multipath signature clustering.
- **Dynamic SensorGraph**: Nodes = sensors or spatial bins; edges = possible multipath / free-space / body links. Laplacian (or fixed-rank approx) updated on join/leave. Cost stays controlled.
- **Person–Sensor assignment**: Cluster streams by trajectory embeddings + body priors. Support varying counts per person; re-assign on the fly.
- **Graceful degradation**: Falls back to single-sensor, ambient-only, or partial volume if sensors disappear.
- **Role awareness** (from existing `rv_mesh`): ANCHOR / OBSERVER / FUSION_RELAY / COORDINATOR can still be elected, but the *temporal alignment itself* remains intrinsic even if roles change.

## Integration with TimeView 4D Pipeline

```
N sensor CSI streams
        ↓
Adaptive discovery + SensorGraph construction
        ↓
Intrinsic alignment operators (spectral / WaveGraph / Dual-time / STL / multi-agent)
        ↓
Joint temporal field X(t, sensors)  [fixed-mode projection for constant-time]
        ↓
Volume graph + SyntheticWaveField inverse scattering (materiality + motion)
        ↓
4D (x,y,z,t) reconstruction + synthetic high-rate streams per person / per query point
        ↓
Downstream: pose, vitals, multi-person tracking, RuVector memory, Cognitum storage
```

All heavy operators stay in the constant-time family (see `docs/PERFORMANCE_AUDIT_CONSTANT_TIME.md`).

## Platform-Agnostic Design

See `docs/PLATFORM_AGNOSTIC.md` and `AGENTS.md`.

- Pure Python (and optional Rust) interfaces; no model-specific code.
- JSON schemas for SensorStream, CoordinatorConfig, aligned fields, 4D volumes.
- Neutral prompt seed that any LLM (Grok, ChatGPT, Gemini, Claude, local models) can include.
- Docker / pyproject for reproducible environments.
- Abstract classes so agents can implement or call tools against a stable contract.

## Concrete Next Steps

1. Skeleton already started in `python/timeview/multi_sensor.py` (SensorNode, AutoSyncCoordinator, AdaptiveFusion).
2. Wire real CSI streams from existing multi-link / mesh firmware into the coordinator.
3. Prototype spectral cross-correlation + WaveGraph consensus alignment on synthetic multi-sensor data.
4. ADR for intrinsic sync semantics and person-assignment clustering.
5. Benchmark: alignment residual vs explicit NTP baseline; scaling vs number of sensors (must stay flat after fixed-mode projection).
6. Update `docs/TIMEVIEW_4D_ENHANCEMENT.md` cross-links and PERFORMANCE_AUDIT for multi-sensor constant-time paths.

## Prompt Usage (any model)

```
You are enhancing RuView / TimeView for adaptive multi-sensor sensing with intrinsic auto-synchronization.
Prefer constructing joint temporal fields from any number of sensors, aligning them via spectral phase, WaveGraph consensus, Dual-time fusion, TemporalLogic predicates, and multi-agent temporal operators — never relying on explicit shared clocks.
Support ambient, multi-worn, multi-person heterogeneous, and self-connecting topologies.
Keep operators in the constant-time family (fixed-K, fixed-modes, DEQ, parallel scans).
Reference docs/MULTI_SENSOR_INTRINSIC_SYNC.md and docs/TIMEVIEW_4D_ENHANCEMENT.md.
```

---

*Living document. Saturate further with code, experiments, and ADRs on main.*
