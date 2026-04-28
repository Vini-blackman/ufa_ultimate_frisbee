# UFA 2012-2025: Multi-Season Analysis

Statistical analysis of the Ultimate Frisbee Association (UFA, formerly AUDL) covering all 13 seasons played between 2012 and 2025. Player game-stats data from the [WatchUFA API](https://watchufa.com), aggregated into ~9,000 player-season observations.

> Dataset: 13 seasons (2012-2025, excluding 2020 which was canceled due to COVID), ~3,500 unique players, 55 team tags.

## Key Findings

### 1. League overview
![League overview](figs/01_visao_geral.png)

**New York Empire is the only clear dynasty in league history**, with 3 titles (2019, 2022, 2023). Bay Area Spiders (San Jose → Oakland) has 2. No other franchise has more than 1.

### 2. Players with most titles
![Titles per player](figs/02_titulos_por_jogador.png)

**Beau Kittredge holds the all-time record: 5 titles**, won across 4 different franchises (San Jose, Dallas, San Francisco, New York). Jeff Babbitt has 4.

### 3. Career GOATs
![Career GOATs](figs/03_goats_carreira.png)

- **Offensive:** Cameron Brock (964 career scores)
- **Defensive:** Ryan Drost (206 career blocks)
- **Longevity:** Cameron Brock and Kevin Quinlan (13/13 seasons — the only players present in every season of league history)

### 4. Statistical profile of champions
![Champion profile](figs/04_perfil_campeoes.png)

Across the 13 tested seasons, UFA champions show a consistent pattern:

- **High offensive volume** (z = +1.32, 92% of champions above league average)
- **More blocks** (z = +0.81, 69%)
- **Higher completion %** (z = +0.70, 85%)
- **Fewer throwaways** (z = -0.84, only 15% above average → 85% below)
- **Fewer drops** (z = -0.84, 15%)

The recipe is not "either volume or discipline" — it's **both at the same time**.

### 5. Talent concentration
![Elite depth](figs/05_depth_elite.png)

Champion teams are **not more concentrated** than non-champions (top-2 share, Gini show no statistical difference). But they have **~5 players in the league's top 10%**, vs. ~2.6 for non-champions. **The difference is absolute depth**, not internal roster distribution.

The Dallas Roughnecks 2016 is the most "perfect" team in league history, with 9 top-10% players — no one else ever had that many.

### 6. Year N → Year N+1 prediction
![Prediction](figs/06_predicao_n_n1.png)

Individual stats persist moderately: r = 0.71 (R² = 0.51) for scores/game between consecutive years. **Top 5% players regress on average 9 percentile points the following year** — they remain good, but rarely repeat their peak.

### 7. Regular season vs. playoff
![Regular vs Playoff](figs/07_regular_vs_playoff.png)

**Only 46% of UFA champions were #1 in the regular season.** All 13 were in the league's top-3 (by official win-loss record), but the regular-season-to-playoff relationship collapsed in the modern era:

- **2012-2018 era:** 5 of 7 champions were #1 in the regular season (71%)
- **2019-2025 era:** 1 of 6 champions was #1 in the regular season (17%)

The league became more balanced and more subject to short-format playoff variance.

### 8. Game evolution
![Temporal evolution](figs/08_evolucao_liga.png)

Long-term trends (dashed line = linear fit):

- **Goals/game declining:** lower offensive volume, more defensive games
- **Blocks/game declining:** offense becoming more efficient
- **Throwaways/game falling dramatically:** historic drop in 2019
- **Completion % rising:** league became more careful with possession

The simultaneous drop in goals and throwaways in 2019 suggests a rule change or league-wide shift in style.

## How to run

```bash
pip install -r requirements.txt
python ufa_dashboard.py
```

Output: 8 PNGs in `figs/` (150 DPI, ready for README).

## Project structure

```
ufa-analysis/
├── ufa_dashboard.py                  # single script generating all figures
├── ufa_player_stats_all_years.csv    # dataset (13 seasons)
├── requirements.txt
├── figs/                             # outputs
└── README.md
```

## Methodological decisions and limitations

### Composite "Impact Score" metric

For analyses that need to balance offense and defense, I use:

```
Impact = scores + 1.5×blocks + 2×callahans − 1×throwaways − 0.5×drops − 0.5×stalls
```

multiplied by a completion% penalty (1.0 if ≥90%, scaling below). Weight justification: a callahan is a rare and devastating event (3×), blocks are about half as rare (1.5×), and throwaways cost roughly the equivalent of a lost score (1×).

### Heterogeneous schema

The API expanded its columns over the years. Metrics like `yardsThrown` and `hucksCompleted` only exist from 2021 onward. Analyses depending on these variables are restricted to 2021+. The analyses in this repo only use variables available throughout the entire period (scores, goals, assists, blocks, throwaways, drops, completion%).

### Champion regular-season standings

Validated via WatchUFA Power Rankings + Ultiworld + Wikipedia, year by year. **I did not use a plus/minus proxy** (which biases against low-volume teams like Boston 2025).

### Honest limitations

1. **n=13 champions is small.** Patterns exist but are not deterministic.
2. **Cross-era comparisons (2012 with 8 teams vs. 2025 with 24) require care.** Z-scores are not fully comparable across vastly different league sizes.
3. **Plus/minus has a strong teammate effect.** A mediocre player on a good team gets inflated; a star on a bad team gets deflated. No RAPM-style adjustment is available.
4. **Defense is underrepresented in the data.** Blocks capture only a fraction of defensive impact (tight marking, force, positioning are invisible).
5. **The predictive model is naive.** Year N → N+1 scores with R² = 0.51. More features (team changes, age, injuries) would improve it, but they aren't in the dataset.

## Sources

- [WatchUFA API](https://www.backend.ufastats.com/web-v1/player-stats) — raw statistics
- [Wikipedia: Ultimate Frisbee Association](https://en.wikipedia.org/wiki/Ultimate_Frisbee_Association) — champions and format
- [WatchUFA Power Rankings](https://watchufa.com/league/power-rankings) — win-loss records per season
- [Ultiworld](https://ultiworld.com) — context and cross-validation
