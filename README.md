# FIFA World Cup 2026 — Player Performance Analysis

Exploratory analysis of the FIFA World Cup 2026 player performance dataset:
54,600 player-match records across 1,248 players and 48 teams. Loads the
data into SQLite, builds a player-level summary table, and works through
age, market value, top performers, and correlations between match
performance metrics.

## Contents

`FIFA_WC2026_Analysis.ipynb` — single notebook, run top to bottom.

| Section | What it covers |
|---|---|
| 1. Load data | Downloads the dataset via `kagglehub`, with a local-file fallback |
| 2. Store in SQLite | Loads the raw data into a local `.db` for querying |
| 3. Player-level summary | Aggregates match rows down to one row per player |
| 4. Age vs. rating | Match-level vs. player-level regression, side by side |
| 5. Market value vs. rating | Log-scaled value against average rating |
| 6. Top performers | Top 10 by Player-of-the-Match awards and by goals |
| 7. Age distribution | Histogram and boxplot by position |
| 8. Correlation heatmap | Match-level correlations across ~20 performance metrics |

## Setup

```bash
pip install pandas numpy matplotlib seaborn kagglehub openpyxl
```

Run the notebook cells in order. If `kagglehub` can't reach Kaggle (no
network, no credentials), the load step falls back to a local file —
place a copy of the source spreadsheet as `FIFA_World_Cup_2026_Player_Data.xlsx`
in the same directory as the notebook.

## A note on the data

The raw file is **match-level**, not player-level — each player has one
row per match (~44 rows/player on average). A few columns
(`total_goals_tournament`, `total_assists_tournament`,
`player_of_match_awards`) are **cumulative running totals** rather than a
single final value.

That matters for anything meant to answer a per-player question:

- **Section 3** builds a `players_summary` table (one row per player) by
  taking `MAX()` of the cumulative totals and `AVG()` of per-match rating,
  and every player-level chart downstream (age distribution, top
  performers) uses that table instead of the raw rows.
- **Section 4** deliberately keeps both versions side by side — fitting
  the same regression on raw match rows vs. on `players_summary` gives
  visibly different slopes, since match-level rows implicitly weight
  players by how many matches they played. Worth knowing before trusting
  any single-number trend line on this dataset.
- **Section 8** (correlation heatmap) intentionally stays at match level,
  since per-match stats vs. per-match rating is a match-granularity
  question by nature.

