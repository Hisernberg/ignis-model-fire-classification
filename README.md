# IGNIS Fire-Type Classification Model

Private mirror of the Hugging Face models [`Nabidnur/ignis-fire-classifier`](https://huggingface.co/Nabidnur/ignis-fire-classifier) and [`Nabidnur/ignis-fire-type-model`](https://huggingface.co/Nabidnur/ignis-fire-type-model).

## What it does
Classifies every active-fire detection into a fire type with a calibrated
probability, so the map popup can say *what kind of fire* this is — not just
how hot it is.

| Class | Typical signature |
|-------|-------------------|
| Vegetation fire (forest / savanna / cropland) | daytime VIIRS/MODIS, FRP 5–250 MW, moderate confidence |
| Industrial flare (gas/oil) | persistent night detections, near-zero diurnal variance |
| Volcanic thermal | multi-week persistence, extreme FRP spikes |
| Other / unknown | low confidence or mixed features |

## Inputs (per detection)
- `frp` — fire radiative power (MW)
- `conf` — detection confidence (%)
- `night` — day/night flag
- `dens25` — local detection density (points within 0.25°)
- diurnal persistence features from the 7-day FIRMS history

## Architecture & size
Gradient-boosted trees + calibrated softmax head over 12 handcrafted features.
**~18 KB** of weights (`src/lib/ignis/typeWeights.json` ships inside the app —
zero-dependency inference, no runtime download).

## Training data
FIRMS NRT detections cross-referenced with EONET labelled wildfire events and
known flare registers (VIIRS nightfire signatures), 2023–2026, global.

## Intended use
- inline "fire type" badge in the IGNIS hotspot popup
- offline-first: runs in-process on the server route (`/api/hotspot`)

## Limitations
- trained on 1 km / 375 m NRT detections; not validated below 375 m
- industrial vs volcanic confusion persists at low persistence windows

## License & release
Private during NASA Space Apps judging; will be re-released by the team under
Apache-2.0 after the competition. **This repository contains no credentials** —
all keys live in the private vault repo.
