# Fernie Avalanche Dashboard

**Real-time avalanche hazard monitoring for the Morrissey Ridge sub-study area**  
Coal Creek to Morrissey Ridge · Lizard Range, BC · 72.43 km²

SAIT Capstone W26 · CIRUS Lab

---

## Live Dashboards

> **⚠ GitHub Pages setup required — see [Enable GitHub Pages](#enable-github-pages) below**

| Dashboard | URL | Purpose |
|---|---|---|
| Live Conditions | `https://john10452.github.io/fernie-avalanche-dashboard/dashboard.html` | Current station readings, snowpack indicators, danger level |
| Scenario Demo | `https://john10452.github.io/fernie-avalanche-dashboard/dashboard_presentation.html` | Synthetic scenario playback for stakeholder presentations |

---

## Files in This Repository

| File | Updated | Description |
|---|---|---|
| `dashboard.html` | Hourly | Live conditions dashboard — reads `dashboard_data.json` |
| `dashboard_presentation.html` | On demand | Scenario playback dashboard with Play/Pause controls |
| `dashboard_data.json` | **Hourly** (automated) | Current snowpack analysis output — pushed by the collection machine |
| `scenario_A_events.json` | On demand | Scenario A: Storm Slab (Jan 15, 36h storm cycle) |
| `scenario_B_events.json` | On demand | Scenario B: Wind Slab (Feb 3, sustained NW wind) |
| `scenario_C_events.json` | On demand | Scenario C: Wet Avalanche (Feb 22-23, 2025 atmospheric river event) |
| `scenario_D_events.json` | On demand | Scenario D: Persistent Slab (Mar 8, 60h cold/clear + loading) |

---

## How It Works

```
Collection machine (C:\avalanche_weather\)
  │
  ├── collect_weather.py      H:00  ─── AvCan 130/77, Holfuy 1940, OWM grid
  ├── snowpack_analysis.py    H:05  ─── computes 5 indicators + danger level
  ├── upload_to_github.py     H:12  ─── pushes dashboard_data.json to this repo
  │
  └── weather_data.db               ─── stays on the machine, never uploaded

GitHub Pages (this repo)
  │
  ├── dashboard_data.json           ─── ~5 KB, updated each hour
  └── dashboard.html                ─── fetches dashboard_data.json every 5 min

User's browser
  └── opens dashboard URL           ─── sees current conditions
```

**`weather_data.db` never leaves the collection machine.** Only the 5 KB JSON summary is uploaded here. The full database (station history, OWM grid, raw API responses) stays local.

---

## Enable GitHub Pages

> **Do this once — takes 2 minutes.**

1. In this repository, click **Settings**
2. Click **Pages** in the left sidebar
3. Under **Source**: select **Deploy from a branch**
4. Branch: **main** · Folder: **/ (root)**
5. Click **Save**
6. Wait 1–2 minutes, then open:  
   `https://john10452.github.io/fernie-avalanche-dashboard/dashboard.html`

---

## Data Sources

| Station | Elevation | Type | Variables |
|---|---|---|---|
| AvCan 130 — Morrissey High | 1998 m | Physical | Temperature, wind, snow depth, humidity |
| AvCan 77 — Morrissey Low | 960 m | Physical | Temperature, wind, snow depth |
| Holfuy 1940 — Morrissey Ridge | 2130 m | Physical (ridge) | Wind speed, direction, gust, temperature, pressure |
| OWM 25-point grid | Area-wide | Model | Precipitation type/rate, area temperature |

Lapse rate is computed from the 1038 m elevation difference between AvCan 130 and AvCan 77.

---

## Snowpack Indicators

Five indicators computed each hour following CAA OGRS 4th ed. (2022):

| Indicator | Source | Critical threshold |
|---|---|---|
| New snow 24h | AvCan 130 snow height | ≥ 30 cm (CAA critical loading) |
| Wind slab score (0–4) | Holfuy 1940 wind | Speed ≥ 30 km/h + gust ≥ 40 km/h |
| Wet avalanche score (0–5) | AvCan temp + OWM rain | T ≥ 0°C at 1998 m or rain ≥ 3 mm |
| Faceting gradient (°C/m) | Lapse rate × multiplier | ≥ 10°C/m (Schweizer et al. 2003) |
| Persistent WL score (0–5) | Cold obs count + loading | ≥ 5h T < -10°C + 20 cm loading |

---

## Presentation Scenarios

Four synthetic scenarios for off-season stakeholder presentations:

| Scenario | Event | Duration | Based on |
|---|---|---|---|
| A — Storm Slab | Rapid loading, 48 cm/36h | 36 hours | Typical Fernie Pacific front |
| B — Wind Slab | Sustained NW 70 km/h | 30 hours | Typical ridge high |
| C — Wet Avalanche | Rain-on-snow, +8°C in 4h | 42 hours | **Real Feb 22–23, 2025 event** |
| D — Persistent Slab | 12-day cold/clear + 32 cm | 60 hours | Typical continental pattern |

---

## Professional Disclaimer

This system supports — it does not replace — certified avalanche professional assessment.  
All control decisions require field verification, snowpit data, and professional judgment.  
Outputs are decision-support tools calibrated to the Lizard Range / Fernie climate.

---

## References

- Canadian Avalanche Association. (2022). *Observation Guidelines and Recording Standards* (4th ed.).
- Schweizer, J., Jamieson, J. B., & Schneebeli, M. (2003). Snow avalanche formation. *Reviews of Geophysics, 41*(4). https://doi.org/10.1029/2002RG000123
- Statham, G. et al. (2018). A conceptual model of avalanche hazard. *Natural Hazards, 90*(2), 663–691.
- Holfuy API: https://holfuy.com · Avalanche Canada: https://avalanche.ca
