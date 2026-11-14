# Scope research: what the 2026-11-14 research established, and what planning must decide

Written 2026-11-14, the day the repo was created. **Read this before the foundational planning session.** It distils three files that live **outside** the repo on the Oracle box, by design (`/home/opc/research/edmonton-land-value/`, not committed):

- `prior_art_and_feasibility_2026-11-14.md`: this session's report, with an addendum from the `edmonton-tax-viz` session on the assessment data.
- `prior_art_reply_2026-11-14.md`: a Claude web report the owner ran independently, saved verbatim. It is broader on prior art and modelling.
- `prior_art_reply_verification_2026-11-14.md`: a claim-by-claim check of that report (✅ / ⚠️ / ❓ / ❌).

**Nothing here is decided.** `docs/DECISIONS.md` has no rows yet, and the decisions in §6 are the owner's. Each fact carries a tag: *[measured]* means queried or read in this session; *[reported]* means it comes from a report or a sibling session and was not re-checked here.

## 1. The goal (owner's words, 2026-11-14)

> "Land value prediction, but more specifically, try and glean the contribution of different city investments (and also whatever events or changes we know matter most temporally) to the land value total … research into prior art, and what would be needed to do this for Edmonton. Ideally … some sort of interactive map."

Both reports reach the same framing. With free data, the defensible product is **attribution of change in *assessed* value**, shown as an "estimated contribution" with uncertainty. True *land*-value attribution needs a land layer we estimate ourselves. *Market* value needs sales data, which is not open.

## 2. Prior art, in brief

- **Method family.** Event studies and difference-in-differences (DiD) around dated investments, comparing parcels near a project with similar parcels farther away. For many projects with different start dates, use staggered-adoption estimators (Callaway–Sant'Anna, Sun–Abraham, Gardner two-stage), because plain two-way fixed effects is biased there. Spatial DiD covers spillover into the control group. Synthetic control suits a single big treated unit, such as downtown. Hedonic models and gradient-boosted models with SHAP are **descriptive only**: they re-learn the assessor's formula and can't separate "the LRT was built where values were rising" from "the LRT raised values". *[reported, both reports agree]*
- **Edmonton evidence is thin and mixed.**
  - Qiu & Tong (2021, *Economic Modelling* 99), spatial DiD: an Edmonton LRT line had *negative* effects on nearby detached houses, with negative spillover. *[abstract read; the CW report says Metro Line and 5,881 sales, not checked]*
  - Allinott (2022), a U of A MA project, not peer reviewed, using *assessed values*: Valley Line SE construction phase about +1–4%; Metro Line opening −1.8% to +6.7%, positive for higher-value homes and null or negative for lower-value ones; negative within about 50 m of a station. *[measured: text extracted and read]*
- **Priors for effect size.** Small (low single-digit %), spatially tight (about 400–800 m), possibly negative right next to stations or arenas, and different by property type. Effects often start at announcement, not opening. Upzoning raises land-intensive parcels and can lower capital-intensive ones (Auckland). *[reported]*
- **Tools.** OpenAVMKit (open-source Python mass appraisal, copyleft licence) is the nearest existing stack for estimating land value. Python packages `differences`, `csdid`, `pyfixest`, `pysyncon`, `doubleml`, `econml` and `lonboard` all exist on PyPI. *[measured: existence only, features not checked]*
- **Value-capture figures are forecasts, not causal estimates.** Examples: the Crossrail uplift, Calgary's Green Line, and Edmonton's own levy-area (CRL) increments. The Quarters CRL is projected to fall about $63.6M short of its costs. *[reported]*

## 3. Edmonton data

### What exists *[measured via the Socrata API unless tagged]*
| Need | Dataset | Notes |
|---|---|---|
| Parcel value panel | Property Assessment Data (Historical) `qi6a-xuwt` | Years 2012–2025; 337k (2012) to 432k (2025) accounts per year; 5,501,958 rows *[reported by tax-viz]*. 21 fields: account, year, address, legal description, lat/lon, neighbourhood, year_built, garage, zoning, lot_size, `assessed_value` (total only), mill_class/tax_class_pct 1–3. **No land/improvement split.** |
| Current roll | Property Assessment Data (Current) `q7d6-ambg` | 439,825 accounts on 2026-11-14 |
| Building attributes | Property Information (Current) `dkk9-cj3x` | Includes `total_gross_area` and `year_built`; current year only |
| Investments (current portfolio) | Capital Projects `552h-hjwj`, plus point/line geometry `xw7u-7mr6` / `nmn5-5yzc` | Asset type, budget, phase, forecast completion. **Not checked:** whether completed projects stay listed. Historical events will likely need a hand-kept table. |
| LRT | LRT Stations `fhxi-cnhe`, Routes `rpjw-4jft`, Tracks `8r95-rjy4` | Geometry only; no dates |
| Permits | Development Permits `2ccn-pwtu` (2019+ variant `q4gd-6q9r`); General Building Permits `24uj-dj8v` | Confound "land" signals with improvement changes |
| Parcels | Land Parcels datasets (`dm3i-bp8w`, `774r-kpz5`, …) | **Points only.** No open polygons (CW report says since 2021-11-01, date not checked) |
| Assessment method | City methodology PDFs `2026_RES_Land.pdf`, `2026_RES_Improved.pdf` | Read. See §4. |
| Sales prices | none open | Land Titles charges per title; parcel-level sales need a data agreement. Access options are in the out-of-repo research files. |

The sibling `edmonton-tax-viz` holds only a neighbourhood × year rollup of `qi6a-xuwt`, not the parcel panel, and no account crosswalk exists anywhere. *[reported by that session]*

### Data defects that shape the design
- 🛑 **The 2024 and 2025 historical slices are incomplete.** 2,448 accounts that exist in earlier years and in the current roll are missing, in whole buildings. Re-checked here: 10310 102 St NW (Stantec Tower) has 315 accounts in 2021–2023 and **0 in 2024–2025** *[measured]*. Not fixed upstream, and tax-viz has not reported it to the City *[reported]*. **The usable historical window is 2012–2023, plus the current roll.** The Valley Line SE post-opening period falls exactly in the gap.
- **Identifiers churn.** 0.15–0.91% of accounts vanish each year. The historical and current datasets disagree on some account numbers. Neighbourhoods get renamed (OLIVER → WÎHKWÊNTÔWIN, 12k parcels). Coordinates get re-centroided (median 58 m, up to 559 m). *[reported by tax-viz]*
- **`lot_size`** is in m², except where a record holds ownership shares instead; condo lot sizes are inconsistent. *[reported by tax-viz]*

## 4. Problems specific to measuring with assessments

1. **The outcome is the City's model, not the market.** The 2026 methodology applies **site influences by rule** *[measured: read in both PDFs]*:
   - Positive: golf course, greenbelt, lake, noise barrier, park, ravine, river valley.
   - Negative, including an **"LRT influence"** for properties abutting or across from an LRT right of way.
   - The land model has 8 market areas plus study areas and uses sales from 1 Jul 2020 to 30 Jun 2025.

   So a station-ring DiD on assessments partly recovers a coded adjustment, mostly at 0–50 m. **Static amenity premia can't be estimated from assessments.** Only *changes* around *new* investments are informative.
2. **Lag.** Assessment year Y values the market at July 1 of Y−1. Event dates shift accordingly.
3. **Smoothing.** Mass appraisal pools sales over 5 years and across study areas, which biases effects toward zero.
4. **Overlapping treatments downtown.** Levy area, arena, LRT, rezoning and the pandemic office shock overlap, so downtown can't be decomposed parcel by parcel.
5. **Land value must be estimated.** Options: (a) a land surface fitted to vacant residential land and smoothed; (b) a residual (total minus depreciated building cost, using `total_gross_area`, `year_built` and a construction-cost index); (c) hedonic location × lot-size land shares.
6. **Macro shocks** (2014–16 oil crash, 2020 COVID, migration surge) are absorbed by year fixed effects and shown as one city-wide bar. With about 12 annual points they can't be attributed causally.

## 5. Candidate events

Code each with announce / funding, construction start, opening, and first affected assessment year.

| Event | Dates | Status |
|---|---|---|
| Valley Line SE LRT | opened 2023-11-04; groundbreaking 2016-04-22 | opening ✅ measured; groundbreaking reported |
| Metro Line (NAIT) | opened 2015-09-06 | reported |
| Valley Line West | construction from 2021, completion 2028+ | reported. An anticipation study only. |
| Rogers Place / ICE District | opened 2016-09-08 | reported |
| Bus Network Redesign | 2021-04-25 | reported |
| Zoning Bylaw 20001 (city-wide upzoning) | effective 2024-01-01 | reported, well known |
| Provincial order to reverse the 2024 rezoning | 2026-10-08, three months to comply | news-confirmed. First affected roll is 2028, so it can't be observed yet. Calgary's repeal date conflicts between sources. |
| Quarters / Downtown CRLs, Blatchford, Walterdale Bridge | various | not checked |

## 6. Decisions the foundational planning has to make (the owner's)

1. **Outcome.** Assessed total value, or an estimated land layer first? Both reports say: start with assessed value and add the land layer later.
2. **First case.** Both reports point to LRT stations, since prior art exists to compare against. Reproduce Allinott's Valley Line SE / Metro Line result first as a sanity check. Given the 2024–25 gap, decide how to get a post-opening point for Valley Line SE: the current roll only, or wait for the 2027 roll.
3. **Unit of analysis.** Account-level points for estimation; H3 (res 8–9) or neighbourhood for display.
4. **Downtown.** Synthetic control as one unit, or leave it out of v1.
5. **Zoning 2024 → reversal** as a later study (improvement-to-land ratio as the axis).
6. **Sales-data validation.** Whether to pursue it, and how (options in the research files).
7. **Map stack.** Follow `edmonton-civic-map` (MapLibre GL + PMTiles, static hosting), or not. Both reports recommend it.
8. **Scope boundaries for `docs/SCOPE.md`.** Both reports recommend these as **out of scope**:
   - SHAP / ML feature attribution presented as causal;
   - per-parcel dollar contributions;
   - causal attribution of macro shocks;
   - separating co-located downtown projects;
   - "investment ROI".
9. **The panel build.** A module that pages `qi6a-xuwt` (50k-row server cap per request) into parquet, checks row counts against the server, and links accounts across years by account number plus position tolerance. That is a new module and data contract, so it needs a plan first (`CLAUDE.md`).

## 7. Disk and environment
About 9 GB free on 2026-11-14 (the disk is shared). Store the full panel as parquet, not CSV. Python: use the project `.venv` (system `python3` is 3.6).
