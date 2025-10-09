# PhonoPhylo — Phonetic-First Language Phylogeny

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](#license)
[![Python](https://img.shields.io/badge/python-3.10%2B-blue.svg)]()
[![Made with UMAP & HDBSCAN](https://img.shields.io/badge/UMAP-HDBSCAN-9cf.svg)]()

**PhonoPhylo** is a reproducible starter for *phonetic-first* language phylogeny:
- unify **PHOIBLE** (phoneme inventories), **WALS** (typology), **Glottolog** (tree & ids),
  **Lexibank/NorthEuraLex** (IPA wordlists) — with consistent keys (glottocode);
- normalize IPA via **CLTS/BIPA**, encode segments with **PanPhon**;
- build interpretable features (inventory flags, PanPhon histograms, CV patterns, prosody);
- cluster languages (UMAP → HDBSCAN) and validate vs. Glottolog families.

> Goal: a clean, extensible pipeline you can scale from 10 to 2,000+ languages.

---

## Highlights
- 🔑 **Keying by glottocode** with ISO639-3 fallbacks and conflict log
- 🔤 **IPA normalization** (CLTS/BIPA) + **PanPhon** 21-D articulatory features
- 🔊 Inventory flags: clicks, ejectives, implosives, retroflex/uvular/pharyngeal series, tone, nasal vowels
- 🧩 Feature blocks: `ph_*` (phonetics), `gr_*` (typology), `lx_*` (lexical distances), `prsd_*` (prosody)
- 🗺️ **UMAP** maps + **HDBSCAN** clusters; **ARI/NMI** against Glottolog families

---

## Data Sources (to integrate)
- **Glottolog** — canonical language catalogue & families (glottocode)
- **PHOIBLE** — phoneme inventories (CSV/CLDF)
- **WALS** — structural features (phonology/morphology/syntax)
- **Lexibank / NorthEuraLex** — IPA wordlists (CLDF); optional **ASJP** fallback
- **WikiPron / Epitran / Phonemizer** — when you need extra G2P coverage

> Licenses vary (mostly CC); see `docs/DATA_LICENSES.md`.

---

## Repo Structure
```
.
├─ src/
│  ├─ extract/           # loaders: glottolog, phoible, wals, lexibank, …
│  ├─ normalize/         # CLTS/BIPA cleanup, PanPhon mapping, validators
│  ├─ build_features/    # ph_*, gr_*, lx_*, prsd_* feature builders
│  ├─ modeling/          # UMAP/HDBSCAN, evaluation (ARI/NMI)
│  └─ utils/             # io, keys, logging
├─ data/
│  ├─ raw/               # downloaded as-is (not committed)
│  ├─ external/          # third-party after light prep
│  ├─ interim/           # normalized (parquet/csv)
│  └─ processed/         # features_lang.parquet, embeddings, plots
├─ notebooks/            # demos / EDA
├─ docs/
│  ├─ DATA_LICENSES.md
│  └─ SOURCES.md
├─ .gitignore
├─ requirements.txt
└─ README.md
```

---

## Quickstart

```bash
# create env
python -m venv .venv && . .venv/Scripts/activate  # on Windows
# or: . .venv/bin/activate                         # on Linux/Mac

pip install -r requirements.txt
```

Minimal run (toy pilot for 10 languages):
```bash
# 1) fetch Glottolog + PHOIBLE + WALS (paths configured in src/extract/config.yaml)
python -m src.extract.fetch_all

# 2) normalize IPA (CLTS/BIPA), map to PanPhon vectors
python -m src.normalize.ipa_to_panphon

# 3) build features (inventory flags, PanPhon histograms, CV patterns)
python -m src.build_features.make_phonetics

# 4) add typology (WALS), join by glottocode
python -m src.build_features.add_typology

# 5) embed + cluster + score
python -m src.modeling.umap_hdbscan
```

Artifacts:
- `data/processed/features_lang.parquet` — per-language feature table  
- `data/processed/embeddings_umap.parquet`  
- `data/processed/clusters_hdbscan.parquet`  
- `data/processed/plots/umap_map.png`

---

## Features (first batch)
- `ph_inventory_size`, `ph_vowel_count`, `ph_consonant_count`
- `ph_has_clicks`, `ph_has_ejectives`, `ph_has_implosives`
- `ph_has_retroflex`, `ph_has_uvular`, `ph_has_pharyngeal`
- `ph_tone_system` (none/register/contour)
- `ph_panphon_mean_*` (21 dims), `ph_panphon_hist_*`
- `ph_cv_entropy`, `ph_cv_profile_*`

Typology (subset from WALS) added as `gr_*` (one-hot/ordinal).

---

## Requirements
See `requirements.txt`, typical stack:
```
numpy
pandas
scikit-learn
umap-learn
hdbscan
panphon
pyclts
epitran
phonemizer
python-Levenshtein
pyyaml
matplotlib
seaborn
```

> Optional: `lexibank`, `cldfbench`, `geopandas` (for maps).

---

## Roadmap
- [ ] Core: robust GLN/ISO mapping & conflict log
- [ ] PHOIBLE → feature block `ph_*` (complete)
- [ ] WALS → `gr_*` + missingness policy
- [ ] Lexical distances (`lx_*`) from Lexibank/NorthEuraLex/ASJP
- [ ] Prosody features (`prsd_*`)
- [ ] Interactive map (UMAP + hover by glottocode)

---

## License
MIT (code). Data follow their original licenses — see `docs/DATA_LICENSES.md`.
