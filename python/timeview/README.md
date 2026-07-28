# TimeView Python Surface

High-temporal-resolution 4D radio sensing + adaptive multi-sensor intrinsic sync.

## Modules

- `multi_sensor.py` — **Adaptive multi-sensor coordinator** with intrinsic auto-synchronization (no explicit clocks). Supports ambient, multi-worn, multi-person heterogeneous, and self-connecting topologies. See `docs/MULTI_SENSOR_INTRINSIC_SYNC.md`.

## Key Documents

- `docs/TIMEVIEW_4D_ENHANCEMENT.md` — 4D materiality + motion vision
- `docs/MULTI_SENSOR_INTRINSIC_SYNC.md` — multi-sensor topologies & intrinsic sync
- `docs/PERFORMANCE_AUDIT_CONSTANT_TIME.md` — constant-time family targets
- `docs/PLATFORM_AGNOSTIC.md` — model-neutral usage (Grok / ChatGPT / Gemini / Claude / local)
- `AGENTS.md` — entry point for any coding / research agent

## Quick Start (adaptive multi-sensor)

```python
from timeview.multi_sensor import (
    SensorNode, TopologyTag, SensorRole,
    make_adaptive_coordinator, AdaptiveFusionLayer,
)

coord = make_adaptive_coordinator(fixed_modes=64, wave_K=12)

# Register whatever is present — adaptive
coord.register_sensor(SensorNode(topology=TopologyTag.AMBIENT, role=SensorRole.ANCHOR))
coord.register_sensor(SensorNode(topology=TopologyTag.WORN, person_id="user_1"))
coord.register_sensor(SensorNode(topology=TopologyTag.WORN, person_id="user_1"))  # multi-worn
coord.register_sensor(SensorNode(topology=TopologyTag.WORN, person_id="user_2"))  # second person

fusion = AdaptiveFusionLayer(coord)
# aligned = fusion.forward(raw_streams)  # → joint temporal field for 4D reconstruction
```

All alignment is intrinsic (spectral / WaveGraph / Dual-time / STL / multi-agent). No master clock required.

## Platform Agnostic

This package has no dependency on any particular LLM vendor. Use it from Grok, ChatGPT, Gemini, Claude, local models, or pure scripts. See `docs/PLATFORM_AGNOSTIC.md` and `AGENTS.md`.
