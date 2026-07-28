# AGENTS.md — Neutral Instructions for Any Coding / Research Agent

This file is the single entry point for autonomous or assisted agents (Grok, ChatGPT, Gemini, Claude, local models, custom swarms).

## Mission

Enhance and operate the RuView / TimeView radio-sensing system:

1. High-temporal-resolution **4D materiality + motion reconstruction** from commodity WiFi CSI (ESP32 + ambient).
2. **Adaptive multi-sensor** support with **intrinsic auto-synchronization** (no explicit shared clocks).
3. Prefer physics / wave / temporal-operator driven methods (timemachines operators) over supervised volumetric labels.
4. Keep performance in the **constant-time family** wherever possible.

## Mandatory Reading (in order of relevance)

- `docs/TIMEVIEW_4D_ENHANCEMENT.md` — core 4D vision
- `docs/MULTI_SENSOR_INTRINSIC_SYNC.md` — multi-sensor topologies + intrinsic sync
- `docs/PERFORMANCE_AUDIT_CONSTANT_TIME.md` — complexity targets
- `docs/PLATFORM_AGNOSTIC.md` — neutrality rules
- `python/timeview/` — implementation surface

## Hard Constraints

- All synchronization of sensors must be intrinsic / implicit (spectral, WaveGraph consensus, Dual-time, STL, multi-agent temporal fields).
- Adaptive mode must work with 1..N sensors of mixed topologies (ambient, multi-worn, multi-person, self-connecting).
- Do not introduce model-vendor lock-in into core libraries.
- Prefer fixed-K, fixed-mode, DEQ, parallel-scan designs.
- Subsequent edits stay on `main`.

## Preferred Workflow

1. Load the relevant seed documents into context.
2. Propose or implement against the abstract interfaces in `python/timeview/multi_sensor.py` (and future modules).
3. Keep changes testable offline (synthetic CSI, unit tests).
4. Update the living docs when architecture decisions change.
5. Benchmark scaling vs sensor count and sequence length; flag any regression out of the constant-time family.

## Tooling Notes

- Firmware multi-node mesh already exists (`rv_mesh`, `swarm_bridge`, ESP-NOW, timesync). Build on it; do not reinvent discovery.
- Proprietary temporal operators live in the linked `timemachines` library; import or mirror patterns, do not assume private weights are present.
- JSON schemas and pure Python are the interchange format with any agent.

When in doubt, prefer physics consistency + intrinsic alignment over brittle explicit clock protocols.
