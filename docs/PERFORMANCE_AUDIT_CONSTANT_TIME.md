# Performance Audit & Constant-Time Family Drive for TimeView

**Goal:** Audit all temporal/wave operators used in the 4D reconstruction pipeline and convert as many as possible into the *constant-time family* — i.e., sequential depth / iteration count independent of sequence length T, graph size N, or horizon (fixed K, fixed iterations, spectral collapse, DEQ fixed-point, hierarchical fixed macro-steps).

This enables predictable high temporal resolution on edge hardware and saturates the proprietary timemachines library.

## Current Status (from LIBRARY_RESULTS_AND_BENCHMARKS + code)

### Already in / near Constant-Time Family
| Operator | Complexity (seq depth) | Notes |
|----------|------------------------|-------|
| MaskedSTLUntil / TemporalLogicProcessor | O(1) sequential (tensorized windows + reductions) | 1000–1600× vs true recurrent loop; max-diff 0 |
| WaveGraphPropagator (fixed K) | O(1) layers (K fixed, e.g. 6–20) | Chebyshev spectral waves + learnable damping; multi-hop in one layer |
| Parallel LIF membrane (no-reset) | O(1) via scan / FFT-style | 20–116× vs sequential; hard-reset still sequential |
| Dual-time / Anti-time / Dual-timewave fusion | O(1) | Fixed 2-pass + elementwise agreement/contradiction |
| Dual-radial | O(1) effective span (ceil(T/2) shells) | Boundary packing |
| Dynamic reweighting / Sheaf sculpting / Fixed-readout | Near O(1) (4– few iters) | 800–1300× wall vs full AdamW |
| FFTTemporalMixer (after transform) | Parallel O(log T) | Asymptotic win; frequency bins can be truncated to fixed |
| HybridMultiScaleWaveGraph | Fixed macro/micro steps | HierarchicalMultiScaleWaveEngine with fixed steps |

### Remaining Sequential / Scaling Gaps
- Hard-reset LIF exact dynamics (sequential by nature of reset).
- Long-horizon continuous PolyWave / SyntheticWaveField evolution if macro steps scale with T or required accuracy.
- Iterative graph message-passing baselines (already replaced by WaveGraph).
- Full end-to-end AdamW on large volumes (mitigated by fixed-readout / thermodynamic surrogates).
- Large-N exact WaveGraph dense path (O(N²) memory); sparse is better but still linear in E.

## Conversion Targets (shoot for constant-time)

1. **DEQ / Fixed-Point Solvers** (`deq_dctc.py` already present)
   - Collapse continuous wave evolution or thermodynamic trajectories into a fixed number of solver iterations independent of horizon.
   - Use for SyntheticWaveField / PolyWave inverse steps → constant-time per reconstruction query after setup.

2. **Spectral Collapse / Fixed-Mode Projection**
   - Early project long CSI T into fixed number of frequency / temporal basis coefficients (e.g. 32–128 modes).
   - All subsequent operators act on fixed-size tensors → true O(1) sequential.

3. **Fixed Hierarchical Scales**
   - Enforce hard-coded macro steps (e.g. 4–8) and micro steps independent of input length in MultiScaleTemporalWaveEngine.
   - Already partially present; make the default and document.

4. **Approximate Constant-Time Graph Waves**
   - For large volumes: fixed-size sketches, random features, or sampling of edges so effective cost independent of N after setup.
   - Or pre-compute low-rank Laplacian factors.

5. **Thermodynamic / GSTL Surrogates with Fixed Annealing Schedule**
   - Fixed temperature / free-energy steps for materiality hypothesis ranking.

6. **CSI 4D Specific**
   - Fixed voxel resolution + fixed temporal basis size → entire forward/inverse readout constant-time after graph construction.
   - Wearable trajectory projected via dual-time with fixed buffer.

## Action Plan (on main)

- [ ] Add constant-time flags / modes to key modules in the integration layer (`python/timeview/`).
- [ ] Prototype DEQ wrapper around SyntheticWaveField for inverse scattering.
- [ ] Benchmark fixed-mode spectral projection vs full T for reconstruction fidelity vs latency.
- [ ] Document in this file and TIMEVIEW_4D_ENHANCEMENT.md the constant-time subset that should be preferred for edge / high-rate streams.
- [ ] Where sequential is unavoidable (exact hard-reset, high-precision continuous), keep as optional high-accuracy path and route default to constant-time family.

## Expected Outcome

Majority of the live 4D reconstruction and synthetic-time projection path becomes **constant sequential depth**, enabling:
- Predictable real-time performance at max ESP32 CSI rates.
- Easy edge quantization / compilation.
- Higher effective temporal resolution via denser synthetic projections without proportional compute growth.

*Living audit document. Update after each conversion experiment.*
