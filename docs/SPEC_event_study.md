# SPEC: LRT event studies (Metro Line, Valley Line SE)

Written 2026-11-14 (S3). **Status: a plan awaiting the owner's approval. No code exists and nothing here has been estimated.** The one part already locked is that the first-case order is conditional on the gates in §7 (`docs/DECISIONS.md`, second 2026-11-14 first-case row). The gate thresholds and every choice listed in §11 are proposals.

**Read this before** planning the linker, the events table or the event-study module, and before quoting any LRT effect.

Tags as in `docs/SCOPE_research.md`: *[measured]* was queried or read in this session; *[reported]* comes from a report or news and was not checked against a primary source; *[inferred]* is this session's reasoning, untested.

## 1. Why the design is fixed before the data is seen

The expected effect is small (low single-digit percent, `docs/SCOPE_research.md` §2), the outcome is the City's valuation model and not the market, and there are many defensible ring widths, control groups and sample filters. Choosing among them after seeing estimates would let the choice produce the result. So this spec fixes the model, the rings, the comparison group and the pass/fail checks first. Anything changed after the first estimate is run is reported as a change, with the before and after.

## 2. What is estimated

For each line, each property group and each distance ring: the change in log assessed value of accounts near a station, relative to comparison accounts, for every assessment year, measured from the last year before the event. Every figure is labelled "in assessed value".

| Study | Event | What the number means |
|---|---|---|
| Metro Line, opening | Service began 2015-09-06 | The opening effect **net of anticipation**. Not the total effect of the line: see §3. |
| Valley Line SE, construction | Groundbreaking 2016-04-22 *[reported]* | The effect of the construction period, 2017–2023 rolls. |
| Valley Line SE, opening | Opened 2023-11-04 | One flagged point from the current roll. Never a headline. No gate applies. |

## 3. Timing

Assessment year Y values the market at 1 July of Y−1 (`docs/SCOPE_research.md` §4.2). An event affects the first roll whose valuation date falls after it.

| Event | Date | First affected roll | Status |
|---|---|---|---|
| Metro Line, original opening target | April 2014 | (expectations, 2014 roll onward) | *[reported]*, two news reports |
| City Centre Airport closes | 2013-11-30 | 2015 | *[reported]*, news and the City's Blatchford page |
| Metro Line opens, interim service limited to 25 km/h between MacEwan and NAIT | 2015-09-06 | **2017** | *[reported]*, two news reports; no City source read |
| Valley Line SE groundbreaking | 2016-04-22 | **2017** | *[reported]* |
| Valley Line SE opens | 2023-11-04 | 2025 (inside the incomplete 2024–25 years) | *[measured]* in S1 from news |

Consequences:

- **Metro Line.** Before years are the 2012–2016 rolls; the reference year is 2016; after years are 2017–2023. The panel's first roll (2012, valued July 2011) is already after the line was announced and, going by the April 2014 target, under construction. The construction-start date was not found. So the "before" years hold anticipation, and the study cannot measure the line's total effect. Qiu & Tong used 2006–08 sales as their "before" *[reported]*, which this panel cannot reach.
- **Valley Line SE.** Before years are 2012–2016; reference 2016; construction years 2017–2023. Route approval and funding dates are not yet collected and may fall inside the before years; the events table must carry them.
- **Allinott's timing differs.** Allinott used 2016 as the Metro Line treatment year and 2017 for Valley Line SE construction *[measured: paper text]*. By the valuation-date rule the 2016 roll predates the Metro Line opening, so our Metro Line treatment year is one roll later than Allinott's. The replication arm (§9) uses Allinott's year; the main model uses ours.

## 4. Sample

- **Accounts:** `mill_class_1 = RESIDENTIAL`, two groups estimated separately (`docs/DECISIONS.md`, unit row): detached-like and condo. The dataset has no property-type field. The classification rule (suite, zoning, lot size) is set in the linker plan from the profiled data, not here. The counts below use "no suite" and "suite" as a stand-in.
- **Panel:** accounts linked across all of 2012–2023 by the linker. Accounts that are missing in some year, linked by position instead of account number, or show a change of `year_built` are **kept and flagged**, and each estimate is reported with and without them. Nothing is dropped silently; the count excluded from each estimate is part of the output.
- **Downtown:** accounts in the DOWNTOWN neighbourhood are in neither the treated nor the comparison group (`docs/DECISIONS.md`, downtown row).
- **Downtown-edge stations:** an account whose nearest station on the line is a downtown-edge station is in neither group. For the Metro Line that station is MacEwan: 2,612 of the 3,588 residential accounts within 800 m of it were in DOWNTOWN in the 2015 roll *[measured]*. For Valley Line SE the stations are Quarters, Churchill and 102 Street.

### First counts (2015 roll, residential, straight-line distance, measured 2026-11-14 through the Socrata API)

| Line, downtown-edge stations excluded | Ring | No suite | Suite | Neighbourhoods with 50 or more no-suite accounts |
|---|---|---|---|---|
| Metro Line (Kingsway, NAIT) | 0–400 m | 185 | 233 | 1 |
| Metro Line (Kingsway, NAIT) | 0–800 m | 1,015 | 786 | 4 |
| Valley Line SE (Mill Woods to Muttart, 9 stops) | 0–400 m | 3,120 | 1,001 | 14 |
| Valley Line SE (Mill Woods to Muttart, 9 stops) | 0–800 m | 9,093 | 2,677 | 19 |

Limits of these counts: they are one roll, not the linked panel; the NAIT position is approximate (53.5657, −113.5043) because `fhxi-cnhe` has no NAIT entry; "no suite" is not yet "detached".

## 5. Treated rings and the comparison group

Distance is straight-line from the account point to the nearest station on the line (excluding downtown-edge stations).

| Ring | Role |
|---|---|
| 0–400 m | Treated, inner |
| 400–800 m | Treated, outer |
| 800–1,600 m | Not in the comparison group. Estimated as its own "spillover" ring, because Qiu & Tong report effects beyond the treated zone. |
| Comparison | Residential accounts of the same group, 1.6–5 km from the line's stations, and more than 1.6 km from any LRT station that was open or under construction during 2012–2023. Not downtown. |

Comparison accounts are weighted so that their mix of 2012 value band and year-built band matches the treated ring's. Matching is on levels in 2012 only, never on the trend, so the before-years stay a fair test.

**Accounts beside the tracks.** The City applies a negative "LRT influence" by rule to parcels abutting or across from an LRT right of way. Accounts within a set distance of the track geometry (`8r95-rjy4`) are flagged. The distance comes from the profile, because account points are parcel centroids that move (median 58 m, `docs/SCOPE_research.md` §3). **The headline estimate excludes flagged accounts; they are reported as their own group**, labelled as a coded adjustment. When the City starts applying the influence (construction or opening) is not known.

## 6. Model and uncertainty

- **Model.** For one line and one property group: log assessed value on account effects, year effects, and ring × year terms, with the 2016 roll as the reference. Each line is estimated on its own. Each has a single event date, so the bias that affects pooled designs with many start dates does not arise, and no staggered-adoption estimator is needed.
- **Uncertainty comes from placebo stations, not from standard errors.** *[inferred]* The City's model has no station-distance term beyond the abutting rule, so an LRT effect can only reach an assessment through neighbourhood-level factors. Accounts in one neighbourhood then move together, and the Metro Line has four treated neighbourhoods. Account-level or clustered standard errors would be far too narrow. Instead: place pretend stations at random in mature neighbourhoods more than 1.6 km from any real station, run the same model for each, and take the middle 95% of those estimates as the "placebo band". A real estimate is reported with its position in that distribution.
- **Dollar figures.** Area totals only: the ring's estimate times the ring's reference-year value, with the placebo band carried through. No per-parcel dollars (`docs/SCOPE.md`).

## 7. Gates

Each gate is computed from the linked panel **before the after-year estimates for the treated rings are looked at**. G1, G3 and G5 use no treated after-year outcome at all; G2 and G4 use before-years and the airport comparison only.

| Gate | Question | Proposed pass rule | Applies to |
|---|---|---|---|
| G1 Precision | Can the design see an effect of the expected size? | The placebo band for the 0–800 m ring is no wider than ±3 percentage points. | Both lines, each property group |
| G2 Before-years | Were treated and comparison accounts moving together before the event? | Every before-year estimate (2012–2015) lies inside the placebo band. | Both lines |
| G3 Granularity | Do assessments vary within a neighbourhood enough to show distance? | Reported, not pass/fail: the share of the variation in yearly log change that lies within neighbourhood × group × year. Below 20%, no within-neighbourhood distance gradient is published and the 0–400 m and 400–800 m rings are merged. | Both lines |
| G4 Airport | Can the Metro Line opening be told apart from the airport closure? | Accounts 0–1,600 m from the former airport boundary and more than 1,600 m from a Metro Line station show no shift at the 2015 or 2016 roll outside the placebo band. | Metro Line only |
| G5 Composition | Is the treated ring mostly the same accounts throughout? | Flagged accounts (§4) are no more than 15% of the treated ring. Above that, the estimate is published only with the flag shown. | Both lines |

**What follows from the gates:**

- Metro Line passes G1, G2 and G4: it is the first case, as decided.
- Metro Line fails any of G1, G2, G4: **Valley Line SE construction becomes the first result.** The Metro Line is published as "not estimable from assessments", with the gate results as the explanation. That is a finding, not a gap.
- Valley Line SE fails G1 or G2 as well: stop and return to the owner. No estimate is published.

**Honest expectation:** on the first counts the Metro Line is likely to fail G1 or G4. Four treated neighbourhoods give a wide placebo band, and the airport closed beside both stations two rolls before the first after-year. The ±3 point threshold was chosen from the published effect range (about −2% to +7%), not from these counts, but the owner should set it knowing which way it is likely to cut.

## 8. Known confounds (not gated)

- **Metro Line:** the airport closure and the Blatchford redevelopment; the reduced-speed interim service and its traffic delays at crossings; Capital Line stations already near McCauley; NAIT station moved about 200 m on 2024-01-20 (outside the window).
- **Valley Line SE:** redevelopment near Bonnie Doon and Holyrood; the 2020 pandemic shift, which may differ between mature and suburban areas (the 2012 matching addresses level, not this); construction disruption and expected access arrive together and cannot be separated.
- **Both:** assessments pool several years of sales, so an effect phases in over rolls and is biased toward zero. A flat first after-year is not evidence of no effect.

## 9. Comparison with the published studies

- **Allinott replication arm.** Rerun Allinott's own design on our panel: treated within 1 km; comparison accounts beyond 6.5 km (Valley Line SE) or 7.6 km (Metro Line) from any open or planned station; pooled model without account effects; 2012–2019; treatment years 2017 and 2016; the 25th, 50th and 75th percentiles *[measured: paper text]*. Purpose: if we cannot land near Allinott's figures with Allinott's design on the same source data, suspect our panel before our model.
- **Qiu & Tong.** Sign and rough size only. Their data are sales with a 2006–08 "before" *[reported]*; ours cannot match that.

## 10. What this spec does not decide

The module layout and output schema of the event-study code (a plan of its own, once the panel exists); the linker's rules; which estimation package; how the result is drawn on the map.

## 11. The owner's decisions

Each has a recommendation; none is locked.

1. **G1 threshold:** ±3 percentage points (recommended), or another width.
2. **Rings:** 0–400 and 400–800 m treated, 800–1,600 m spillover (recommended), or Allinott's single 1 km ring as the main design.
3. **Comparison group:** 1.6–5 km, matched on 2012 value and year built (recommended); or Allinott's far-city group; or future Valley Line West station areas up to the 2020 roll.
4. **Accounts beside the tracks:** excluded from the headline and reported separately (recommended), or included with a flag.
5. **MacEwan rule:** nearest-station exclusion as in §4 (recommended), or keep the Central McDougall part of its ring as treated.
6. **Failing-Metro outcome:** publish it as "not estimable" with the diagnostics (recommended), or leave it off the map.

## 12. Order of work

Unchanged: downloader, profile and linker plan, linker, events table. Then the gates run on the linked panel, the owner sees the gate results, and only then does the event-study module get its plan.
