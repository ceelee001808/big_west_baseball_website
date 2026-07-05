# Diamond Index — JUCO → Big West Readiness Board

An interactive scouting dashboard that ranks junior college (JUCO) hitters by how closely their batting profile resembles players who actually transferred to Big West Division I programs. Built as a single, dependency-free `index.html` ... no build step, no server, works offline.


<img width="1507" height="692" alt="DIAMOND_INDEX_1" src="https://github.com/user-attachments/assets/7f9cba02-5acf-46af-b41e-c0b4c195fbb0" />

<img width="989" height="417" alt="DIAMOND_INDEX_2" src="https://github.com/user-attachments/assets/da5b173d-9fb0-41a9-809f-ce0e15f76a05" />


## What it does

A Random Forest classifier was trained on **27,012 JUCO hitter-seasons** (2020–2025, min. 75 PA) against **199 confirmed Big West transfers**. The app presents the model's top 250 prospects on a sortable leaderboard, with a full scouting card for each player.

| Model detail | Value |
|---|---|
| Algorithm | `RandomForestClassifier` (scikit-learn) |
| Trees / depth / class weight | 200 / 10 / `balanced_subsample` |
| Features | K%, BB%, Secondary AVG, ISO, SLG, OBP, AVG |
| Training pool | 27,012 hitter-seasons, PA ≥ 75 |
| Positive class | 199 confirmed Big West transfers (0.74%) |
| Split | 80/20 stratified |
| AUC-ROC | 0.653 |

The **readiness score** is the raw fraction of the 200 trees that vote "transfer" — 138 yes votes → 69. Tiers: **Elite ≥ 70** · **Strong 55–69** · **Developing 40–54** · **Below threshold < 40**.

## Features

- **Sortable leaderboard** — 250 prospects, sortable by any column (readiness, OBP, SLG, K%, HR, year, name), with live search by player, JUCO program, or destination school
- **Filters** — season (2020–2025), commitment status (confirmed Big West / uncommitted), and tier
- **Confirmed destinations** — players who actually transferred show the school they landed at (e.g., → UCSB, → Cal Poly, → UC Davis), joined from the Big West transfer records
- **Scouting card** — readiness score with tree-vote count, board rank, tier, and full batting line
- **Skill diamond** — a four-axis, field-shaped radar of composite percentiles: Contact, Power, Discipline, Speed
- **True cohort percentiles** — every stat is placed against precomputed quantile grids from all 27,012 hitters, not just a mean comparison (K% correctly inverted — lower is better)
- **Head-to-head compare** — pin any player, select a second, and get a per-stat percentile showdown with winners highlighted
- **Copy scout line** — one click produces a paste-ready text summary for notes, texts, or reports
- **Model card** — plain-language explanation of the vote mechanism, feature importances, what AUC 0.653 means, and what the score cannot see
- **Responsive** — collapses gracefully to tablet and phone widths; respects reduced-motion preferences

## Getting started

```bash
git clone https://github.com/ceelee001808/baseball_bigwest_player_eval_website.git
cd baseball_bigwest_player_eval_website
open index.html   # or double-click, or enable GitHub Pages
```

Everything — markup, styles, the top-250 dataset, percentile grids, and logic — is embedded in one file (~112 KB). No dependencies, no network calls beyond Google Fonts.

## How the data was built

1. **Label** — JUCO hitters are cross-referenced by cleaned name against the Big West transfer list; matches become the positive class (`is_transfer = 1`)
2. **Filter** — seasons under 75 PA are dropped to reduce small-sample noise
3. **Train** — Random Forest on the 7 batting features with `balanced_subsample` to handle the 0.74% positive rate
4. **Score** — every qualified hitter gets a readiness score (`predict_proba`); the top 250 are embedded in the app along with their destination school (for confirmed transfers) and 101-point quantile grids per feature for client-side percentile lookups

## The 7 model features

| Feature | What it measures | Direction | Importance |
|---|---|---|---|
| K% | Strikeout rate | Lower is better | 16.0% |
| SLG | Slugging percentage | Higher | 15.3% |
| Sec AVG | Extra bases + walks + steals per AB | Higher | 15.0% |
| BB% | Walk rate | Higher | 14.1% |
| AVG | Batting average | Higher | 14.0% |
| ISO | Isolated power (SLG − AVG) | Higher | 13.0% |
| OBP | On-base percentage | Higher | 12.6% |

Importances cluster tightly (12.6–16.0%), so the model rewards complete offensive profiles rather than one standout tool.

## Read it honestly

The model only sees the batting line. It cannot account for:

- Defense, arm strength, speed tools beyond stolen bases, makeup, or academics
- Whether a player wanted to transfer or was eligible to
- Transfers outside the Big West — a JUCO star now at an SEC school reads as "uncommitted"
- Pitchers — hitters only

**AUC-ROC 0.653** means: given one random confirmed transfer and one random non-transfer, the model ranks the transfer higher 65.3% of the time (coin flip = 50%). The gap from perfect is everything the stat line doesn't capture — recruiting relationships, coaching connections, and eligibility.

## Project structure

```
.
├── index.html   # entire app: UI, embedded top-250 data, percentile grids, logic
└── README.md
```

The training pipeline (data cleaning, labeling, model fitting, export) lives in separate Python scripts using pandas and scikit-learn; the app itself is pure HTML/CSS/JS.

## Roadmap ideas

- Expand the board beyond the top 250 (full 27k pool with lazy loading)
- Pitcher model once pitching data is available
- Year-over-year player progression view for multi-season JUCO careers
- D2/D3 → Big West transfer model using the companion datasets

## License

No license file is currently included. Add one (e.g., MIT) if you intend for others to reuse this project.
