# NBA-Usage-Rate-Win-Analysis

*Python · Pandas · SQLite · Statsmodels · Scikit-learn · Seaborn · Matplotlib*

---

## Problem

NBA analytics discourse frequently conflates correlation with causation — high-usage star players are often credited with driving team success, but it's unclear whether usage itself contributes to winning or is simply a byproduct of playing on a good team. This project investigates whether star player usage rate (USG%) independently predicts team winning percentage, or whether the relationship is confounded by overall team quality. The analysis applies formal regression modeling and confounding controls to answer two questions:

1. **Does a star player's usage rate independently predict team winning percentage?**
2. **Is the observed correlation between usage and winning causal, or driven by team quality (net rating)?**

---

## Dataset

**Source:** NBA Dataset: Box Scores and Stats (1947 – Today) from Kaggle, supplemented with net ratings from Basketball Reference.

| Attribute | Detail |
|---|---|
| Records | 582+ player-seasons, 30 teams, 82 games each |
| Time span | Used 2025–2026 NBA regular season |
| Key features | Minutes, points, FGA, FTA, turnovers, team wins, net rating |
| Target variable | Team winning percentage (continuous) |
| Notable challenge | Deriving usage rate required merging player and team-level data across game records |

---

## Approach

### 1. SQL Integration & Data Aggregation
Rather than relying solely on Pandas, I used SQLite to demonstrate relational database proficiency. Wrote CTEs to calculate team winning percentages, filter qualifying players, and rank them by usage rate creating a reproducible, auditable pipeline.

### 2. Usage Rate Calculation
Applied the standard Basketball Reference usage rate formula:
USG% = 100 * ((FGA + 0.44 * FTA + TOV) * (Team Minutes / 5)) / (Minutes * (Team FGA + 0.44 * Team FTA + Team TOV))
Merged player game-level data with team-level statistics to obtain team totals for FGA, FTA, turnovers, and total minutes across each game, then averaged across the season.

### 3. Feature Engineering & Star Player Identification
Identified each team's "star player" as the highest-usage qualified player:

- At least 20 games played
- Average of at least 25 minutes per game

This produced 30 star players (one per team) for cross-team comparison.

### 4.Initial Correlation Analysis

Examined the bivariate relationship between star usage rate and team win percentage. Found a Pearson correlation of 0.408, a moderate positive relationship suggesting that higher-usage stars tend to play on better teams.

### 5. Confounding Assessment
Recognized that team quality (net rating) could confound the usage–winning relationship: better teams have better players who naturally command higher usage, and better teams also win more games. Conducted a multiple regression predicting win percentage from both usage rate and net rating.

### 5. Regression Modeling
Built an OLS regression model using Statsmodels:

- Predictors: Star usage rate, net rating
- Outcome: Team win percentage
- Evaluation: Coefficient significance, R-squared, partial regression plots
---

## Results

| Metric | Value |
|---|---|
| Pearson correlation (USG% vs Win%) | 0.408 |
| Model R-squared | 0.942 |
| Net Rating coefficient | 2.5504 (p < 0.001) |
| Usage Rate coefficient | 0.4241 (p = 0.089, not significant) |
| Star usage rate range | 25.53% (Alex Sarr) – 38.43% (Luka Doncic) |
| Team win% range | 20.73% (Wizards) – 78.05% (Thunder) |

---

## Key Insights & Learnings

- **Correlation ≠ Causation.** The initial positive correlation between usage rate and winning is confounded by team quality. After controlling for net rating, star usage rate no longer independently predicts winning (p = 0.089, above the 0.05 threshold). This is a textbook example of a spurious relationship driven by a lurking variable.

- **Team quality dominates.** Net rating alone explains 94.2% of the variance in winning percentage, with a highly significant coefficient (p < 0.001). Team performance is the dominant driver of success.

- **Star players benefit from good teams.** High usage rates among star players are more likely a result of being on good teams with effective systems and quality teammates, rather than being a driver of success. Good teams make players look good, not the other way around.

- **SQL adds reproducibility.** Building the pipeline in SQLite rather than Pandas alone made the analysis auditable and reusable.

- **Statistical significance matters.** The difference between p = 0.089 and p < 0.001 is the difference between a spurious correlation and a genuine driver. Without formal hypothesis testing, the confounding would have gone undetected.

---

## Business Impact

| Stakeholder | Value Delivered |
|---|---|
| NBA front offices | Evidence that team quality drives winning; informs roster construction strategy |
| Coaching staffs | Data-backed rationale for balanced offensive systems over star-heavy isolation play |
| Sports analytics teams | Reproducible SQL + Python template for confounding analysis and causal inference in sports data |
| Sports media & fans | Clear statistical debunking of the "high-usage star wins games" narrative |

This analysis demonstrates how formal statistical methods can separate genuine causal drivers from spurious correlations which is a skill directly applicable to player evaluation, lineup optimization, and front-office decision-making.

---

## Skills & Tech Stack

| Category | Tools |
|---|---|
| Data wrangling | Pandas, NumPy |
| Database | SQLite (CTEs, window functions, aggregations) |
| Statistical testing | OLS regression, multiple regression, partial regression plots |
| Feature engineering | Usage rate formula, per-game averaging, player-team aggregation |
| Modeling | Statsmodels (OLS) |
| Visualization | Matplotlib, Seaborn |
| Language | Python 3 |

## Interactive Dashboard
I built a Tableau dashboard that can be viewed for non-technical understanding. The dashboard explores the relationships between usage and winning as well as the top 10 players by usage rate.
https://public.tableau.com/app/profile/nathan.nguyen8587/viz/NBAUSG-WIN/Dashboard1
___

> **Note:** This project was completed as part of a formal data science curriculum. All analysis uses publicly available data.
