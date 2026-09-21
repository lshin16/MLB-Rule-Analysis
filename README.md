# MLB 2023 Rule Change Impact Analysis

Did the 2023 rule changes — pitch clock, 18-inch bases, shift restrictions —
actually change how teams run the bases?

A three-person study for the Northwestern Sports Analytics Group. This
repository holds my section: stolen base attempts and success rates,
2015–2025. Josh analyzed extra-base advancement on batted balls and Mason
analyzed run expectancy across base-out states; their findings are in the
full write-up.

## Findings

**Attempts rose sharply, and the rules explain it.** Modeling league-average
stolen base attempts on year plus a pre/post indicator gives a rule-change
effect of 48.7 additional attempts per team-season (p < 0.001, adjusted
R² = 0.93).

**The record success rate does not belong to the rules.** 2023's 80.1%
success rate was the highest in the sample, but the pre/post indicator is
not significant (p = 0.13). The year term is (+0.59 percentage points per
season, p = 0.02) — success rates were already climbing before 2023. The
rules changed how often teams ran, not how well.

## Data

Team-level baserunning data from Baseball-Reference, 2015–2025 (`sbd.csv`,
279 rows). 2020 is excluded as a 60-game season; 2019 is not in the pull.
Analysis uses the League Average row for each season, so n = 9.

Note: Baseball-Reference's CS column exports as percent-formatted text
("4400%" = 44 caught stealing), which the cleaning step strips and rescales.

## Files

- `Rule_Change.Rmd` — analysis notebook (R, tidyverse)
- `Rule_Change.md` — knitted output with plots
- `sbd.csv` — source data
- `writeup/NSAG_MLB_Rule_Changes.pdf` — full team write-up, all three sections

## Method note

A pre/post level shift with a linear time term — the simplest interrupted
time series specification. It does not model a change in slope after the
intervention. With nine annual observations the design is descriptive rather
than a strong causal identification.
