# Mantis: an open farm diary

**Record once.** Mantis is a small, open-source farm diary. A farmer records a field operation for a parcel. Mantis runs a few transparent checks on the entry and shows the parcel's Sentinel-2 vegetation history (NDVI) next to it. The diary can be exported as CSV and reused.

This repository holds a **working prototype**, not a finished product. The demo runs on **real open parcel boundaries** from Spain (SIGPAC, Aragón) and a **fictional farm**: the diary entries, the organic status and the farm itself are examples.

> Status: private preview. To be released under EUPL-1.2.
> Talk: FOSSCOMM 2026, Sunday 1 Nov 2026, 11:00–11:30, Room 3A, University of West Attica (ΠΑΔΑ).

---

## What it does today

| Part | What works |
|---|---|
| Parcels | 9 real SIGPAC parcels near Borja (Zaragoza): 5 olive groves (OLIVAR), 4 vineyards (VIÑEDO), shown on a map |
| Diary | Record an operation (spraying, fertilising, harvest, photo, voice) on a parcel |
| Checks | Product rules for an organic parcel, e.g. herbicide or synthetic N fertiliser → **review needed**, with the regulation cited (Reg. (EU) 2021/1165). **The entry is still saved**; the warning stays attached to it |
| Satellite | NDVI per parcel, 36 ten-day periods of 2024. A simple rule checks whether the curve fits the declared crop (olive green in winter, vine dormant in winter) and suggests events (vine leaf-out, ground cover between rows) for the user to confirm or dismiss |
| Export | Diary → CSV (`date, parcel_id, crop, operation, entry, status, note, source`) |
| Languages | Greek / English |

## What it does **not** do

- It does **not** check legal compliance. The checks are a few readable rules for a demo scenario.
- NDVI is **context, not proof**. A curve cannot show that a spray or a fertiliser application happened.
- It does **not** submit anything to paying agencies, insurers or certifiers.
- No accounts and no server: the diary is stored only in the browser that uses it.

## Repository layout

```
mantis/
├── README.md
├── LICENSE                      EUPL-1.2
├── requirements.txt             Python packages for the notebook
├── index.html                   ready-to-open app (template + Borja data)
├── app/
│   └── mantis_template.html     the app, with __MANTIS_DATA__ placeholder
├── data/
│   └── mantis_data_borja.json   9 parcels: geometry + 36 NDVI values
└── notebooks/
    └── Mantis_Spain_SIGPAC_v02.ipynb
```

## Run it

**Just look at it:** open `index.html` in a browser (Chrome, Firefox). Nothing to install. Without internet, everything works except the satellite basemap under the map.

**Rebuild it from open data:** open `notebooks/Mantis_Spain_SIGPAC_v02.ipynb` in Google Colab and run the cells in order.

| Cell | Step |
|---|---|
| 1 | Install: geopandas, pyogrio, pystac-client, planetary-computer, stackstac, duckdb |
| 2–4b | Query the SIGPAC Aragón GeoParquet with DuckDB (4.1 M parcels), choose olive + vine parcels in Borja |
| 5–6 | Keep parcels ≥ 2,000 m², build a compact demo farm |
| 7–7b | Sentinel-2 L2A NDVI per parcel and ten-day period (cloud mask, 10 m inward buffer, gap interpolation) |
| 8–9 | Export `mantis_data_borja.json` |
| 10 | Put the data into the app template, write `index.html` and show it in the notebook |

Cell 7 takes several minutes.

## How the satellite rule works

- `winter` = mean NDVI of ten-day periods 1–9 (Jan–Mar), `summer` = periods 19–24 (Jul–Aug).
- **Olive** (evergreen): fits if `winter ≥ 0.35`.
- **Vine** (deciduous): fits if `winter < 0.30` and `summer ≥ winter + 0.05`. Leaf-out = first period after March where NDVI rises 0.05 above winter.
- **Ground cover** (both): first autumn period where NDVI rises 0.15 above summer.

The olive threshold is a demo choice. Parcel 981299938 sits at 0.349 and is flagged **check** on purpose: a threshold is a decision, and it can be wrong.

## Data and licences

| Source | Licence / terms |
|---|---|
| SIGPAC parcels, Aragón (Gobierno de Aragón / FEGA), via [fiboa on Source Cooperative](https://source.coop/fiboa/es-ar) | CC-BY-4.0 |
| Copernicus Sentinel-2 L2A, via Microsoft Planetary Computer STAC | Copernicus free, full and open data policy |
| Basemap tiles: Esri World Imagery | Esri terms of use; attribution shown on the map |
| [Leaflet](https://leafletjs.com) 1.9.4 (bundled in the app) | BSD-2-Clause, © Vladimir Agafonkin, CloudMade |

No competition, private or personal farm data is used.

## Origin

The idea for Mantis started at the EU Agri-Hackathon 2026. This repository contains only Mantis's own code and open data. It does not use hackathon datasets or code from other participants.

Code written by Petros L. Stratis with AI assistance (Claude).

## People

Code: Petros L. Stratis. FOSSCOMM 2026 talk:

- Petros L. Stratis, PhD candidate, ORCID [0009-0002-5954-1162](https://orcid.org/0009-0002-5954-1162)
- Dr. Vassilios Andronis, NTUA, ORCID [0000-0003-1699-6714](https://orcid.org/0000-0003-1699-6714)
- Emeritus Prof. Demetre Argialas, NTUA, ORCID [0000-0002-9757-3600](https://orcid.org/0000-0002-9757-3600)
 
Remote Sensing Laboratory, School of Rural, Surveying and Geoinformatics Engineering, National Technical University of Athens (NTUA)

## Acknowledgement

The PhD research behind this work was Supported by the Hellenic Foundation for Research and Innovation (HFRI) — 4th Call for HFRI PhD Fellowships, No. 011270

## Contributing

Most welcome: making uncertainty visible (measured vs. interpolated NDVI), tests for the checks, more crops, reproducibility.

## Licence

[EUPL-1.2](LICENSE)
