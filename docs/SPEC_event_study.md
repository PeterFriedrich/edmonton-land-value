# SPEC: LRT event studies (Metro Line, Valley Line SE)

Written 2026-11-14 (S3); §3A added 2026-11-15 after reading the City's methodology guides. **Status: the design choices in §11 were decided by the owner on 2026-11-16 and are locked in `docs/DECISIONS.md`. No code exists and nothing here has been estimated.** Dates were re-checked against primary sources the same day; the sourced list is `docs/SPEC_events_table.md` §4.

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
| Metro Line, original opening target | April 2014 | (expectations, 2014 roll onward) | *[reported]*, two news reports; the City's page says "spring 2014" |
| City Centre Airport closes | 2013-11-30 | 2015 | *[measured]* 2026-11-16: council motion of 2013-09-18 setting the date |
| Airport Protection Overlay removed (Bylaw 16859) | 2014-06-24 | 2015 | *[measured]* 2026-11-16: council minutes |
| Valley Line SE funding complete (Alberta commits up to $600 million) | 2014-03-11 | 2015 | *[reported]*, news published that day; the provincial release was not found |
| Metro Line opens, interim service limited to 25 km/h between MacEwan and NAIT | 2015-09-06 | **2017** | *[measured]* 2026-11-16: the City's Metro Line page |
| Metro Line runs at designed speed through intersections (restrictions return November 2017) | 2017-02-19 | 2018 | *[measured]* 2026-11-16: the City's Metro Line page |
| Valley Line SE groundbreaking | 2016-04-22 | **2017** | *[measured]* 2026-11-16: Government of Canada release of that date |
| Valley Line SE opens | 2023-11-04 | 2025 (inside the incomplete 2024–25 years) | *[measured]* 2026-11-16: the City's Valley Line Southeast page |

Consequences:

- **Metro Line.** Before years are the 2012–2016 rolls; the reference year is 2016; after years are 2017–2023. The panel's first roll (2012, valued July 2011) is already after the line was announced and, going by the April 2014 target, under construction. The City's page dates the start of right-of-way work to "2010 to 2011" and the provincial grant to 2011-04-05, both before the 2012 roll's valuation date. So every "before" year holds anticipation, and the study cannot measure the line's total effect. Qiu & Tong used 2006–08 sales as their "before" *[reported]*, which this panel cannot reach.
- **Valley Line SE.** Before years are 2012–2016; reference 2016; construction years 2017–2023. Funding was complete on 2014-03-11 (first affected roll 2015), and the City bought land along the route from 2012. So the 2015 and 2016 rolls are after the line became certain. They stay in the model, labelled "after funding", and gate G2 is judged on 2012–2014 for this line (§7; decided 2026-11-16).
- **Allinott's timing differs.** Allinott used 2016 as the Metro Line treatment year and 2017 for Valley Line SE construction *[measured: paper text]*. By the valuation-date rule the 2016 roll predates the Metro Line opening, so our Metro Line treatment year is one roll later than Allinott's. The replication arm (§9) uses Allinott's year; the main model uses ours.

## 3A. How the City's model produces the outcome

Read 2026-11-15 from the City's guides: 2026 Residential Improved, Land, Cost, and the three condo guides (Highrise, Lowrise, Townhome); 2025 Residential Improved; 2023 Residential Land; the 2026 neighbourhood study-areas index. All *[measured]* unless tagged. **Guides for the 2012–2022 rolls were not found** (not on the City's site; the web archive did not answer), so applying these facts to earlier rolls is an assumption.

| Fact | Where | What it means for the design |
|---|---|---|
| Location enters a house's value through three variables only: market area (nine, one valuation model each), neighbourhood, and study area. | 2026 Improved, "Location" | There is no distance-to-station term. An LRT effect reaches an assessment only if nearby sales move the neighbourhood's or study area's coefficient. This was an inference in the first version of this spec; it is now read from the guide for 2023–2026. |
| The LRT influence applies to a property that abuts an LRT right of way or is across a road or lane from it. It appears in the Improved and Land guides (2023, 2025, 2026). **The three condo guides have no LRT or transit term.** | 2026 Improved and Land, "Negative site influences"; condo guides searched | The beside-the-tracks flag (§5) matters for houses and land only. |
| Condo location enters through neighbourhood and "neighbourhood subgroupings". Highrise (six storeys or more), lowrise and townhome condos each have their own guide and models. | Condo guides, "Variables" | The condo group is three valuation models, and the data has no storeys field. The linker profile must say whether the three can be told apart. |
| A study area is a group of properties inside a neighbourhood "that show different market trends from the rest of the neighbourhood". | 2026 Improved, "Study area" | This is the one way the City can value the station side of a neighbourhood differently. In the 2026 index, the treated neighbourhoods with a study area are King Edward Park (3239, "King Edward Park East"), Strathcona and Prince Rupert. The maps were not inspected, so whether any follows an LRT line is not known. |
| **Each roll is built from five years of sales**, time-adjusted to the valuation date: July 2020 to June 2025 for the 2026 roll, July 2019 to June 2024 for 2025, July 2017 to June 2022 for 2023 (Land). | Each guide, p. 7 | See "The ramp" below. |
| Time adjustment factors are set per market area, with a separate River Valley submarket that includes Bonnie Doon and Cloverdale. | 2026 Improved, appendix | Comparison accounts must share the treated accounts' market area and submarket (§5). |
| Coefficients are not published (the regulation exempts them). | 2026 Improved, citing MRAT s.33(3) | How much weight recent sales get cannot be checked. |

### The ramp

A local price change after an event is absent from older sales, and the time adjustment, set for the whole market area, does not add it. So the share of a roll's five-year sales window that falls after the event caps how much of the effect that roll can show. *[inferred]* from the window dates, assuming a five-year window in every roll and equal weight on every sale; both are unverified.

| Roll | Sales window | Share after Metro Line opening (2015-09-06) | Share after Valley Line SE groundbreaking (2016-04-22) |
|---|---|---|---|
| 2017 | Jul 2011 – Jun 2016 | 16% | 4% |
| 2018 | Jul 2012 – Jun 2017 | 36% | 24% |
| 2019 | Jul 2013 – Jun 2018 | 56% | 44% |
| 2020 | Jul 2014 – Jun 2019 | 76% | 64% |
| 2021 | Jul 2015 – Jun 2020 | 96% | 84% |
| 2022, 2023 | Jul 2016 onward | 100% | 100% |

What follows:

- **The headline figure is the average of the 2022 and 2023 rolls**, the only ones whose sales window lies wholly after the event. Earlier after-years are shown as the path, not as the effect.
- **The expected path is a ramp over five rolls.** A jump at the first after-year is not what a market effect would look like here; it would point to a coded change or to accounts entering and leaving the sample.
- **The before-years are blurred the same way.** The 2012 roll's window reaches back to July 2006, before the Metro Line was under construction. Anticipation would therefore also ramp in through the before-years, so a drifting before-period can mean anticipation as well as a poor comparison group. G2 cannot tell the two apart.
- **Short windows understate.** A "year of" or "year after" comparison, as in two of Allinott's three samples, can show at most 16–36% of a Metro Line opening effect and 4–24% of a Valley Line SE construction effect. With Allinott's Metro Line treatment year of 2016 the share is zero.

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
| Comparison | Residential accounts of the same group, 1.6–5 km from the line's stations, and more than 1.6 km from any LRT station that was open or under construction during 2012–2023. Not downtown. **In the same assessment market area and submarket as the treated accounts they are compared with** (§3A), so both sides are valued by the same model with the same time adjustment. |

Comparison accounts are weighted so that their mix of 2012 value band and year-built band matches the treated ring's. Matching is on levels in 2012 only, never on the trend, so the before-years stay a fair test.

Market-area boundaries are published only as map images in the guides' appendix; no open dataset was found. Until neighbourhood-to-market-area membership is built, the same-market-area rule cannot be applied.

**Accounts beside the tracks.** The City applies a negative "LRT influence" by rule to parcels abutting or across from an LRT right of way. Accounts within a set distance of the track geometry (`8r95-rjy4`) are flagged. The distance comes from the profile, because account points are parcel centroids that move (median 58 m, `docs/SCOPE_research.md` §3). **The headline estimate excludes flagged accounts; they are reported as their own group**, labelled as a coded adjustment. This applies to houses and land only; the condo guides have no LRT term (§3A). When the City starts applying the influence (construction or opening) is not known.

## 6. Model and uncertainty

- **Model.** For one line and one property group: log assessed value on account effects, year effects, and ring × year terms, with the 2016 roll as the reference. The headline is the average of the 2022 and 2023 terms (§3A). Each line is estimated on its own. Each has a single event date, so the bias that affects pooled designs with many start dates does not arise, and no staggered-adoption estimator is needed.
- **Uncertainty comes from placebo stations, not from standard errors.** The City's model has no station-distance term beyond the abutting rule (§3A, read from the 2023–2026 guides), so an LRT effect can only reach an assessment through neighbourhood or study-area coefficients. Accounts in one neighbourhood then move together, and the Metro Line has four treated neighbourhoods. Account-level or clustered standard errors would be far too narrow. Instead: place pretend stations at random in mature neighbourhoods more than 1.6 km from any real station, run the same model for each, and take the middle 95% of those estimates as the "placebo band". A real estimate is reported with its position in that distribution.
- **Dollar figures.** Area totals only: the ring's estimate times the ring's reference-year value, with the placebo band carried through. No per-parcel dollars (`docs/SCOPE.md`).

## 7. Gates

Each gate is computed from the linked panel **before the after-year estimates for the treated rings are looked at**. G1, G3 and G5 use no treated after-year outcome at all; G2 and G4 use before-years and the airport comparison only.

| Gate | Question | Proposed pass rule | Applies to |
|---|---|---|---|
| G1 Precision | Can the design see an effect of the expected size? | The placebo band for the 0–800 m ring is no wider than ±3 percentage points. | Both lines, each property group |
| G2 Before-years | Were treated and comparison accounts moving together before the event? | Every before-year estimate lies inside the placebo band: 2012–2015 for the Metro Line, 2012–2014 for Valley Line SE. A Valley Line SE estimate for 2015 outside the band is reported as possible anticipation of the funded line, not as a failed gate. A failure can mean anticipation or a poor comparison group (§3A); either way the estimate is not published as an effect of the event. | Both lines |
| G3 Granularity | Do assessments vary within a neighbourhood enough to show distance? | Reported, not pass/fail: the share of the variation in yearly log change that lies within neighbourhood × group × year. Below 20%, no within-neighbourhood distance gradient is published and the 0–400 m and 400–800 m rings are merged. Given §3A, a low share is the expected result. | Both lines |
| G4 Airport | Can the Metro Line opening be told apart from the airport closure? | Accounts 0–1,600 m from the former airport boundary and more than 1,600 m from a Metro Line station show no shift at the 2015 or 2016 roll outside the placebo band. | Metro Line only |
| G5 Composition | Is the treated ring mostly the same accounts throughout? | Flagged accounts (§4) are no more than 15% of the treated ring. Above that, the estimate is published only with the flag shown. | Both lines |

**What follows from the gates:**

- Metro Line passes G1, G2 and G4: it is the first case, as decided.
- Metro Line fails any of G1, G2, G4: **Valley Line SE construction becomes the first result.** The Metro Line is published as "not estimable from assessments", with the gate results as the explanation. That is a finding, not a gap.
- Valley Line SE fails G1 or G2 as well: stop and return to the owner. No estimate is published.

**Honest expectation:** on the first counts the Metro Line is likely to fail G1 or G4. Four treated neighbourhoods give a wide placebo band, and the airport closed beside both stations two rolls before the first after-year. The ±3 point threshold was chosen from the published effect range (about −2% to +7%), not from these counts, but the owner should set it knowing which way it is likely to cut.

## 8. Known confounds (not gated)

- **Metro Line:** the airport closure, the Blatchford redevelopment decision (2014-06-10) and the removal of the Airport Protection Overlay (2014-06-24), all first touching the 2015 roll; the service itself, which ran restricted from opening, at designed speed only from February to November 2017, then restricted again until after November 2019, and every 15 minutes instead of the designed 10, so every Metro Line result is labelled "opening with restricted service"; traffic delays at crossings; Capital Line stations already near McCauley; NAIT station moved about 200 m on 2024-01-20 (outside the window).
- **Valley Line SE:** redevelopment near Bonnie Doon and Holyrood; the 2020 pandemic shift, which may differ between mature and suburban areas (the 2012 matching addresses level, not this); construction disruption and expected access arrive together and cannot be separated.
- **Both:** each roll pools five years of sales, so an effect ramps in over five rolls (§3A). A flat first after-year is not evidence of no effect. The pandemic years sit inside the 2022 and 2023 windows.

## 9. Comparison with the published studies

- **Allinott replication arm.** Rerun Allinott's own design on our panel: treated within 1 km; comparison accounts beyond 6.5 km (Valley Line SE) or 7.6 km (Metro Line) from any open or planned station; pooled model without account effects; 2012–2019; treatment years 2017 and 2016; the 25th, 50th and 75th percentiles *[measured: paper text]*. Purpose: if we cannot land near Allinott's figures with Allinott's design on the same source data, suspect our panel before our model. Allinott's short-window figures are expected to be small for the reason in §3A, so they are a check on the panel, not a benchmark for the headline.
- **Qiu & Tong.** Sign and rough size only. Their data are sales with a 2006–08 "before" *[reported]*; ours cannot match that.

## 10. What this spec does not decide

The module layout and output schema of the event-study code (a plan of its own, once the panel exists); the linker's rules; which estimation package; how the result is drawn on the map.

## 11. The owner's decisions

**Decided 2026-11-16: the owner chose the recommended option in all eight.** Each is a row in `docs/DECISIONS.md`. The alternatives are kept below as the record of what was turned down.

1. **G1 threshold:** ±3 percentage points (recommended), or another width.
2. **Rings:** 0–400 and 400–800 m treated, 800–1,600 m spillover (recommended), or Allinott's single 1 km ring as the main design.
3. **Comparison group:** 1.6–5 km, matched on 2012 value and year built (recommended); or Allinott's far-city group; or future Valley Line West station areas up to the 2020 roll.
4. **Accounts beside the tracks:** excluded from the headline and reported separately (recommended), or included with a flag.
5. **MacEwan rule:** nearest-station exclusion as in §4 (recommended), or keep the Central McDougall part of its ring as treated.
6. **Failing-Metro outcome:** publish it as "not estimable" with the diagnostics (recommended), or leave it off the map.
7. **Headline rolls:** the average of 2022 and 2023 (recommended, §3A), or the average of every after-year.
8. **Same market area for comparison accounts** (recommended, §3A). It needs the market-area membership built from the guide's maps first; without it, the fallback is the comparison group as first written.

## 12. Order of work

Unchanged: downloader, profile and linker plan, linker, events table. Then the gates run on the linked panel, the owner sees the gate results, and only then does the event-study module get its plan.
