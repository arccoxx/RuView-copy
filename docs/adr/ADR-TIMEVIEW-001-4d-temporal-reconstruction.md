# ADR-TIMEVIEW-001: 4D Temporal Field Reconstruction via Timemachines Operators

## Status
Proposed (invention seed)

## Context
RuView provides excellent CSI multistatic sensing, self-supervised embeddings, and pose/vitals. Goal is unsupervised MRI-like materiality + movement reconstruction over the full 3D volume as a 4D stream, with maximal temporal resolution, using proprietary temporal computer algorithms.

## Decision
Adopt the TimeView architecture:
- Construct temporal fields from CSI using timemachines primitives.
- Represent volume as graph + continuous wave medium (SyntheticWaveField / PolyWave).
- Inverse via physics-informed consistency (no supervised volumetric GT).
- Dual-time / spectral / logic operators for high-res projections and event streams.
- Wearable ESP32 as mobile illuminator/probe for observability.

## Consequences
- Adds dependency on private timemachines (or selected public modules).
- Enables label-free volumetric sensing beyond current pose/vitals.
- Higher compute for inverse (edge for inference, Seed/GPU for reconstruction).
- New downstream tasks become possible (true material mapping, full 4D understanding).

## References
- docs/TIMEVIEW_4D_ENHANCEMENT.md
- arccoxx/timemachines README (temporal computer philosophy, SyntheticWaveField, WaveGraphNN, etc.)
- Existing RuView ADRs on multistatic, CSI, self-sup, RuVector.
