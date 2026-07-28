# Platform-Agnostic Design for RuView / TimeView

This repository is intentionally **model- and platform-agnostic**. It can be driven by Grok, ChatGPT, Gemini, Claude, local LLMs, pure code agents, or human developers without any special casing.

## Principles

1. **Stable interfaces over model-specific prompts**  
   All core logic lives behind pure Python (and optional Rust) classes and functions. Agents call tools or import modules; they do not need vendor-specific APIs.

2. **Neutral documentation & seeds**  
   Inspirational documents (`TIMEVIEW_4D_ENHANCEMENT.md`, `MULTI_SENSOR_INTRINSIC_SYNC.md`, `PERFORMANCE_AUDIT_CONSTANT_TIME.md`, this file) are written so any capable model can include them in a system prompt.

3. **JSON / schema contracts**  
   Sensor streams, coordinator configs, aligned temporal fields, and 4D volumes use explicit schemas (see below and future `schemas/`). Agents exchange data via these contracts.

4. **Reproducible environment**  
   `pyproject.toml`, Docker, and lockfiles make the runtime independent of the chat platform.

5. **No hidden Grok / OpenAI / Google dependencies**  
   Optional agent tooling may exist, but the sensing, temporal operators, and reconstruction pipeline run standalone.

## Recommended Agent Prompt Skeleton (copy-paste into any model)

```
You are working on the RuView / TimeView radio-sensing codebase.

Core goals:
- Capture high-temporal-resolution 4D (spatiotemporal) materiality + human motion from commodity WiFi / ESP32 CSI.
- Prefer unsupervised / physics-informed reconstruction (SyntheticWaveField, WaveGraph, Dual-time, TemporalLogic) over supervised 3D ground-truth.
- Support adaptive multi-sensor topologies with *intrinsic* auto-synchronization (spectral phase, WaveGraph consensus, Dual-time, STL predicates, multi-agent temporal fields). No explicit master clocks.
- Keep operators in the constant-time family (fixed-K Chebyshev, fixed-mode spectral projection, DEQ fixed-iters, parallel scans).

Key documents to load when relevant:
- docs/TIMEVIEW_4D_ENHANCEMENT.md
- docs/MULTI_SENSOR_INTRINSIC_SYNC.md
- docs/PERFORMANCE_AUDIT_CONSTANT_TIME.md
- docs/PLATFORM_AGNOSTIC.md

Prefer pure Python / Rust interfaces under python/timeview/ and the existing mesh firmware. When proposing code, keep it platform-neutral and testable offline.
```

## Abstract Interfaces (stable contract)

See `python/timeview/multi_sensor.py` for the initial skeleton:

- `SensorNode` — identity, role, stream handle, topology tags (worn / ambient / person_id)
- `AutoSyncCoordinator` — discovery, dynamic SensorGraph, intrinsic alignment, adaptive fusion
- `AdaptiveFusionLayer` — produces the joint temporal field X(t, sensors) ready for 4D reconstruction

These can be implemented against real ESP32 mesh streams, simulated data, or any other radio source.

## JSON Schema Stubs (evolve under schemas/)

```json
{
  "SensorStream": {
    "sensor_id": "string",
    "person_id": "string | null",
    "role": "anchor | observer | fusion_relay | coordinator | unknown",
    "topology": "ambient | worn | mobile",
    "csi": {"t": "array", "amp": "array", "phase": "array", "subcarriers": "array"},
    "meta": {}
  },
  "CoordinatorConfig": {
    "mode": "adaptive",
    "max_sensors": "int | null",
    "fixed_modes": 64,
    "wave_K": 12,
    "enable_dual_time": true,
    "enable_stl_alignment": true
  }
}
```

## Usage with Different Platforms

| Platform | How to use |
|---|---|
| Grok / xAI | Include the prompt skeleton + relevant docs; call tools or generate code against the interfaces |
| ChatGPT / OpenAI | Same; use function-calling / tools against the Python APIs or JSON schemas |
| Gemini | Same; upload docs or paste seeds |
| Claude / Anthropic | Same |
| Local / open models | Same; the repo has no proprietary runtime requirements beyond optional CUDA for the heavy operators |
| Pure CI / scripts | `pytest`, firmware builds, and benchmarks run without any LLM |

## What "Platform Agnostic" Does *Not* Mean

- It does not mean the algorithms are watered down. Physics-informed inverse scattering, WaveGraph, Dual-time, and constant-time operators remain first-class.
- It does not prohibit model-specific *agent* tooling in separate directories; it only requires the core sensing + temporal stack to stay neutral.

---

Keep this document updated when new public interfaces are added.
