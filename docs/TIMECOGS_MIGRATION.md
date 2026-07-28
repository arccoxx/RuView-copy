# TimeCogs Migration — Zero Cognitum Dependency

This fork’s TimeView enhancement line no longer depends on Cognitum or Cognitum Seed.

## Replacement

| Former | Replacement |
|---|---|
| Cognitum Seed (appliance + RuVector host + RVF + cog runtime) | **[TimeCogs](https://github.com/arccoxx/timecogs)** — software-first, platform-agnostic temporal runtime |
| Cognitum marketplace surfaces (e.g. Musica VJ) | Not carried; brand bleed documented in TimeCogs `docs/COGNITUM_ANALYSIS.md` |
| Implicit “sensor language around vector DB” | Clarified: Seed was the attested edge host for sensors; TimeCogs is the equivalent runtime without hardware lock-in |

## Where to go

- Sensing + 4D reconstruction + intrinsic multi-sensor sync → `arccoxx/timeview`
- Temporal memory + attestation + cog host → `arccoxx/timecogs` (private)
- Temporal operators → `arccoxx/timemachines`

## RuVector note

RuVector remains an excellent external engine. Optional enhancement mapping (WaveGraph, Dual-time, fixed-mode spectral, SyntheticWaveField into its temporal crates) lives in TimeCogs `docs/RUVECTOR_ENHANCEMENT.md`. Default path does not require it.

Agents working on this fork should treat TimeCogs as the authoritative memory/attestation surface for all new TimeView work.
