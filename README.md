# UFA 2012-2025: Multi-Season Analysis

Statistical analysis of the Ultimate Frisbee Association (UFA, formerly AUDL) covering all 13 seasons played between 2012 and 2025. Player game-stats data from the [WatchUFA API](https://watchufa.com), aggregated into ~9,000 player-season observations.

> Dataset: 13 seasons (2012-2025, excluding 2020 which was canceled due to COVID), ~3,500 unique players, 55 team tags.

## Key Findings

### 1. League overview
![League overview](figs/01_overview.png)

**New York Empire is the only clear dynasty in league history**, with 3 titles (2019, 2022, 2023). Bay Area Spiders (San Jose → Oakland) has 2. No other franchise has more than 1.

### 2. Players with most titles
![Titles per player](figs/02_titles_per_player.png)

**Beau Kittredge holds the all-time record: 5 titles**, won across 4 different franchises (San Jose, Dallas, San Francisco, New York). Jeff Babbitt has 4.

### 3. Career GOATs (era-adjusted)
![Career GOATs](figs/03_career_goats.png)

Players ranked by **cumulative season z-score** rather than raw totals — this controls for league inflation across eras (8 teams in 2012 vs. 24 in 2025) and rewards consistent dominance over peers, not just longevity.

- **Offensive GOAT:** Cameron Brock (cumulative z-score = 34.1, 964 career scores)
- **Defensive GOAT:** Jeff Babbitt (cumulative z-score = 31.9, 195 career blocks)
- **Longevity:** Cameron Brock and Kevin Quinlan (13/13 seasons — the only players present in every season of league history)

Note that Babbitt overtakes Ryan Drost (206 career blocks) once we adjust for era. Drost has more raw blocks, but Babbitt's per-season dominance was more extreme. Both are reasonable answers to "who is the defensive GOAT?" — they reflect different definitions.

### 4. Statistical profile of champions
![Champion profile](figs/04_champion_profile.png)

Across the 13 tested seasons, UFA champions show a consistent pattern:

- **High offensive volume** (z = +1.32, 92% of champions above league average)
- **More blocks** (z = +0.81, 69%)
- **Higher completion %** (z = +0.70, 85%)
- **Fewer throwaways** (z = -0.84, only 15% above average → 85% below)
- **Fewer drops** (z = -0.84, 15%)

The recipe is not "either volume or discipline" — it's **both at the same time**.

### 5. Talent concentration
![Elite depth](figs/05_elite_depth.png)

Champion teams are **not more concentrated** than non-champions (top-2 share, Gini show no statistical difference). But they have **~5 players in the league's top 10%**, vs. ~2.6 for non-champions. **The difference is absolute depth**, not internal roster distribution.

The Dallas Roughnecks 2016 is the most "perfect" team in league history, with 9 top-10% players — no one else ever had that many.

### 6. Year N → Year N+1 prediction
![Prediction](figs/06_year_over_year.png)

Individual stats persist moderately: r = 0.71 (R² = 0.51) for scores/game between consecutive years. **Top 5% players regress on average 9 percentile points the following year** — they remain good, but rarely repeat their peak.

### 7. Regular season vs. playoff
![Regular vs Playoff](figs/07_regular_vs_playoff.png)

**Only 46% of UFA champions were #1 in the regular season.** All 13 were in the league's top-3 (by official win-loss record), but the regular-season-to-playoff relationship collapsed in the modern era:

- **2012-2018 era:** 5 of 7 champions were #1 in the regular season (71%)
- **2019-2025 era:** 1 of 6 champions was #1 in the regular season (17%)

The league became more balanced and more subject to short-format playoff variance.

### 8. Game evolution
![Temporal evolution](figs/08_league_evolution.png)

Long-term trends (dashed line = linear fit):

- **Goals/game declining:** lower offensive volume, more defensive games
- **Blocks/game declining:** offense becoming more efficient
- **Throwaways/game falling dramatically:** historic drop in 2019
- **Completion % rising:** league became more careful with possession

The simultaneous drop in goals and throwaways in 2019 suggests a rule change or league-wide shift in style.

### 9. GOAT vs elite-tier players (radar)
![GOAT radar](figs/09_goat_radar.png)

Two side-by-side radars compare the all-time leaders to the league's 75th percentile (P75) on five career dimensions: Scores, Assists, Goals, Blocks, Longevity. All axes are normalized to percentile rank within qualified careers (≥30 games), so the P75 baseline sits at 75 on every axis by construction.

- **Cameron Brock (Offensive GOAT):** dominates every axis — including Blocks. He doesn't just outscore his peers; he is also a top-quartile defender. The radar is "boringly perfect" on purpose.
- **Ryan Drost (Defensive GOAT by raw volume):** clear contrast. Blocks pegs at 100; Assists barely clears P75. Pure cutter+defender profile, no handler responsibilities.

Note the radar uses **raw career totals** (Drost wins blocks volume), while figure 3's ranking uses **cumulative z-score** (Babbitt wins peak-adjusted dominance). Both are defensible answers to "defensive GOAT" depending on whether you weight career volume or seasonal peaks.

## How to run

```bash
pip install -r requirements.txt
python ufa_dashboard.py
```

Output: 9 PNGs in `figs/` (150 DPI, ready for README).

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

### Era-adjusted career rankings (cumulative z-score)

For the GOAT rankings in figure 3, I sum each player's per-season z-score (within their season) across their career. This adjusts for the fact that scoring 100 goals in 2014 (12 teams, ~340 league players) is harder than scoring 100 in 2025 (24 teams, ~750 players). Players with shorter peak-laden careers (Ryan Osgar, Pawel Janas) climb the rankings; pure-longevity players slide down.

### Heterogeneous schema

The API expanded its columns over the years. Metrics like `yardsThrown` and `hucksCompleted` only exist from 2021 onward. Analyses depending on these variables are restricted to 2021+. The analyses in this repo only use variables available throughout the entire period (scores, goals, assists, blocks, throwaways, drops, completion%).

### Champion regular-season standings

Validated via WatchUFA Power Rankings + Ultiworld + Wikipedia, year by year. **I did not use a plus/minus proxy** (which biases against low-volume teams like Boston 2025).

### Honest limitations

1. **n=13 champions is small.** Patterns exist but are not deterministic.
2. **Cross-era comparisons (2012 with 8 teams vs. 2025 with 24) require care.** Z-scores help but are not a complete fix — league size affects standard-deviation interpretation.
3. **Plus/minus has a strong teammate effect.** A mediocre player on a good team gets inflated; a star on a bad team gets deflated. No RAPM-style adjustment is available.
4. **Defense is underrepresented in the data.** Blocks capture only a fraction of defensive impact (tight marking, force, positioning are invisible).
5. **The predictive model is naive.** Year N → N+1 scores with R² = 0.51. More features (team changes, age, injuries) would improve it, but they aren't in the dataset.
6. **Radar uses qualified-careers (≥30 games) as the population.** This means "P75" in the radar is the 75th percentile of players who actually had a career — not of every player who ever appeared in the dataset. Fair for ranking elite-vs-elite, but it's not "P75 of the entire league."

## Sources

- [WatchUFA API](https://www.backend.ufastats.com/web-v1/player-stats) — raw statistics
- [Wikipedia: Ultimate Frisbee Association](https://en.wikipedia.org/wiki/Ultimate_Frisbee_Association) — champions and format
- [WatchUFA Power Rankings](https://watchufa.com/league/power-rankings) — win-loss records per season
- [Ultiworld](https://ultiworld.com) — context and cross-validation
