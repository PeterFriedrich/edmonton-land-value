# SPEC: the events table

Written 2026-11-16 (S4). **Status: approved by the owner on 2026-11-16** (the file layout and the three choices in §7; rows in `docs/DECISIONS.md`). No file or code exists yet; §8 is the work a coding session does next.

**Read this before** coding an event date anywhere, quoting when an event "started", or changing the timing table in `docs/SPEC_event_study.md` §3.

Tags: *[primary]* means read on 2026-11-16 in a record made by the body that took the action (council minutes, a City project page, a government news release). *[secondary]* means a news report or a company release. *[not found]* means searched for and not located.

## 1. What the table is for

One hand-kept file that holds every dated fact a study relies on: what happened, when, how sure the date is, where it was read, and which assessment roll it first touches. The event-study module reads its event date from here and nowhere else, so a date is corrected in one place.

It holds dates only. Station positions, ring distances and sample rules stay in `docs/SPEC_event_study.md`.

## 2. The rule from a date to a roll

Assessment year Y values the market at 1 July of Y−1, from sales in the five years ending 30 June of Y−1 (`docs/SPEC_event_study.md` §3A).

| Quantity | Rule | Example: 2015-09-06 |
|---|---|---|
| **First affected roll** | The first roll whose valuation date is after the event. An event on or before 30 June of year T gives T+1; from 1 July of year T onward it gives T+2. | 2017 |
| **First fully affected roll** | The first roll whose whole five-year sales window is after the event: five rolls later. | 2022 |
| **Share of a roll's window after the event** | Days of the window after the event, divided by the window's days. | 16% for the 2017 roll |

- An event dated exactly 1 July counts as after the valuation date (T+2). No row in §4 sits on that boundary.
- The rule is computed, never typed. The file stores the date; code derives the rolls. A test checks the rule against five worked cases from §4 (2015-09-06 → 2017, 2016-04-22 → 2017, 2013-11-30 → 2015, 2023-11-04 → 2025, 2014-06-24 → 2015), and goes red if the boundary month is moved.
- **The five-year window is read from the 2023–2026 guides only.** Applying it to earlier rolls is the assumption already recorded in `docs/SPEC_event_study.md` §3A.

**Dates known only to a month or a year.** Each row stores the earliest and the latest day the event could have happened. The rule runs on both. If both give the same roll, the row is usable. If they differ, the row is marked "roll ambiguous" and cannot be a study's event date until a better source is found. In §4 this happens once (Metro Line construction start, "2010 to 2011"), and it does not matter there because every possible roll is before the panel begins.

## 3. Columns (proposed)

| Column | Meaning |
|---|---|
| `event_id` | Short stable name, e.g. `metro_open_restricted`. |
| `subject` | `metro_line`, `valley_line_se`, `city_centre_airport`. One line or site per value. |
| `kind` | `plan_approval`, `funding`, `procurement`, `construction_start`, `opening`, `service_change`, `closure`, `regulation`. |
| `role` | How a study uses the row: `event` (a study's time zero), `anticipation` (news that could move prices before the event), `confound` (something else happening nearby), `context`. |
| `date_earliest`, `date_latest` | The same day when the date is exact. |
| `what` | One sentence, in the source's own terms. |
| `source` | URL, or dataset id plus the meeting date and item title. |
| `source_tier` | `primary` or `secondary`. |
| `read_on` | The day the source was read. |
| `note` | Conflicts between sources, and what the date does not establish. |

Not stored: the derived rolls (§2) and geometry. A row names its `subject`; the station or site geometry for that subject is defined once in the event-study code.

**File:** `data/events.csv`, committed, edited by hand. Rejected: a table inside a markdown doc (code cannot read it without parsing prose); YAML (the repo has no YAML reader and every row has the same fields).

**Guards, written with the file:** every row has a source and a `read_on`; `date_earliest` ≤ `date_latest`; `event_id` is unique; a row with `role = event` is `primary` and not roll-ambiguous. Each is broken on purpose once.

## 4. The rows found on 2026-11-16

"Council minutes" means the City's open dataset `7zht-i9ve` (Council and Committee Meetings, June 2011 to September 2025), queried by API. "City Metro Line page" is `https://www.edmonton.ca/projects_plans/transit/north-lrt-project-history`.

### Metro Line (Churchill to NAIT)

| Date | What | First affected roll | Source |
|---|---|---|---|
| November 2008 | Council approves the concept plan. | 2010 | City Metro Line page *[primary]* |
| 2010 to 2011 | "Right of way preparations began (roadwork, building removals, utility relocations, Kingsway road reconstruction, track slab construction)." | 2011–2013, ambiguous | City Metro Line page *[primary]* |
| 2011-04-05 | Alberta grants $497 million from GreenTRIP. | 2012 | City Metro Line page *[primary]* |
| January 2012 | Tunnelling under way "since January 2012". | 2013 | Government of Canada release, 2012-11-16 *[primary]* |
| 2012-11-16 | Tunnel breaks through to Churchill Station; the release says trains "will start running in 2014". | 2014 | Same release *[primary]* |
| Spring 2014 | Construction complete "in time for the planned spring 2014 opening"; the signalling system was not ready. | 2015 | City Metro Line page *[primary]* |
| 2014-06-29 | Kingsway/Royal Alex Transit Centre opens for buses. | 2015 | City Metro Line page *[primary]* |
| **2015-09-06** | **Opens to the public with operating restrictions:** trains every 15 minutes, no faster than 25 km/h between MacEwan and NAIT. | **2017** | City Metro Line page *[primary]* |
| 2016-06-19 | Speed raised between crossings; still 25 km/h over road crossings. | 2017 | City Metro Line page *[primary]* |
| **2017-02-19** | **Last speed restriction lifted:** up to 50 km/h through intersections. | **2018** | City Metro Line page *[primary]* |
| November 2017 | Speed restrictions put back after two trains ended up facing each other on one track. | 2019 | City Metro Line page *[primary]* |
| 2019-04-04 | City issues the signalling contractor a notice of termination. | 2020 | City Metro Line page *[primary]* |
| November 2019 | A replacement fixed-block signalling system starts being installed. The page gives no date for when restrictions ended for good. | 2021 | City Metro Line page *[primary]* |
| 2024-01-20 | NAIT/Blatchford Market station replaces the temporary NAIT station (outside the study window). | 2025 | City Metro Line page *[primary]* |

What this changes:

- **The opening date now has a City source.** It was resting on two news reports.
- **Every before-year is after funding and after construction began.** The panel's first roll (2012, valued July 2011) follows the April 2011 grant and the start of right-of-way work. `docs/SPEC_event_study.md` §3 said this by inference; it is now read from the City's page.
- **The opening was not one event.** Service ran restricted from September 2015, at designed speed only from February to November 2017, then restricted again until some date after November 2019. The line also still ran every 15 minutes, not the designed 10. A study of "the opening" is a study of a service that was degraded for most of the after-years. This belongs in the confounds list of the event-study spec and in the page copy of any Metro Line result.
- **Not found:** the date restrictions ended for good; the date of the April 2014 opening target in a City record (the City page says "spring 2014"); the federal funding announcement.

### Valley Line Southeast (Mill Woods to 102 Street)

| Date | What | First affected roll | Source |
|---|---|---|---|
| 2011-06-01 | Council approves $39 million for preliminary engineering of the Southeast to West line. | 2012 | Council minutes *[primary]* |
| 2011-11-25 | Council approves up to $102 million for land along the southeast route, bought "in thirds in each of 2012 2013 and 2014". | 2013 | Council minutes *[primary]* |
| 2012-05-02 | Council decides the Southeast to Downtown line will be a public-private partnership and applies for federal money. | 2013 | Council minutes *[primary]* |
| 2012-06-12 | Committee recommends amendments to the Southeast LRT Concept Plan. | 2013 | Council minutes *[primary]* |
| March 2013 | Canada announces up to $250 million from the P3 Canada Fund. The archived release prints no day. | 2014 | Government of Canada release *[primary]* |
| 2013-09-18 | Council approves the Stage 1 environmental assessment and site location study. | 2015 | Council minutes *[primary]* |
| 2013-12-11 | Council states the City's $800 million "is in place" and asks the other governments to commit. | 2015 | Council minutes *[primary]* |
| **2014-03-11** | **Alberta commits up to $600 million**, which completes the funding. | **2015** | CTV News, published that day *[secondary]*; the provincial release was *[not found]* |
| 2014-04-15 | Council approves the revised capital profile and starts procurement (request for qualifications, then proposals). | 2015 | Council minutes *[primary]* |
| 2015-10-20 | Council approves the funding plan. | 2017 | Council minutes *[primary]* |
| 2016-02-11 | The builder's consortium reaches financial close. | 2017 | Company releases found by search, not opened *[secondary]* |
| **2016-04-22** | **Groundbreaking.** The release gives a planned opening of December 2020. | **2017** | Government of Canada release *[primary]* |
| **2023-11-04** | **Opens for passenger service.** | **2025** | City Valley Line Southeast page *[primary]* |

What this changes:

- **The groundbreaking date now has a primary source.**
- **Funding was complete two rolls before the reference year.** The province's commitment (March 2014) first touches the 2015 roll, and the City was buying land along the route from 2012. So the 2015 and 2016 before-years are after the line became certain, and the 2013 and 2014 before-years are after the City began buying land. The 2012 roll predates the land buying and every funding commitment, but not the engineering money. See §7, choice A.
- **One conflict between City sources.** The City's Valley Line page says council approved the partnership model "in August 2012"; the council minutes show the motion carried on 2012-05-02. The minutes are the record of the vote, so the table uses May. Both dates give the 2013 roll.
- **Not found:** the original corridor and concept-plan approvals (before June 2011, where the minutes dataset begins); the day of the March 2013 federal announcement; a primary source for the provincial commitment and for financial close. The City's Valley Line booklet (August 2016, 48 pages) probably holds the early history, but it is page images with no text and this box has no tool to render it.

### City Centre Airport

| Date | What | First affected roll | Source |
|---|---|---|---|
| 2013-09-18 | Council asks the airports authority to take the steps for "full closure … at 11:59 p.m. November 30 2013". | 2015 | Council minutes *[primary]* |
| **2013-11-30** | **Airport closes.** | **2015** | The same motion *[primary]*. It records the date council set; no record of the closure happening was read. |
| 2014-06-10 | Council approves the Blatchford redevelopment implementation strategy. | 2015 | Council minutes *[primary]* |
| **2014-06-24** | **Bylaw 16859 passes third reading:** the Airport Protection Overlay is removed from the Zoning Bylaw. | **2015** | Council minutes *[primary]* |

What this changes:

- **Closure, the redevelopment decision and the overlay removal all first touch the 2015 roll.** The overlay passed six days before the 2015 roll's valuation date. Gate G4 looks for a shift at the 2015 or 2016 roll, so it covers all three, and it cannot tell them apart.
- **The overlay was not only local.** It limited building heights under the flight paths, including downtown. Its removal is a city-centre event as well as an airport-area one. Downtown is already outside the study (`docs/DECISIONS.md`, downtown row), but comparison accounts under the former flight paths are not. The overlay's map was not read, so which neighbourhoods it covered is unknown.
- **Not found:** the earlier council decision to close the airport in phases (before June 2011), which is when the closure became expected.

## 5. Where the dates came from

- **Council minutes, `7zht-i9ve`.** Found with the `edmonton-open-data-landscape` catalogue search. It holds motions with their text, date and result for four council terms, June 2011 to September 2025, about 76,000 rows. Full-text search works through the API's `$q`. It is the primary record for anything council voted on in that period. A later dataset series covers the 2025–2029 term.
- **The City's Metro Line page** carries a dated history back to 2005. It answered a browser-like request from this box and returned an error to the web-fetch tool.
- **The open-data catalogue holds no dataset of LRT opening or construction dates,** no assessment market-area boundaries and no study-area boundaries (searched 2026-11-16).

## 6. What the table does not hold yet

Rogers Place, the Bus Network Redesign, Valley Line West, the levy areas and the 2024 zoning change (`docs/SCOPE_research.md` §5) get rows when their study is planned. No date for them was checked in this session.

## 7. The owner's decisions

**Decided 2026-11-16: the recommended option in A, B and C.**

A. **Valley Line SE: which before-years count as clean.** The spec's gate G2 tests the 2012–2015 before-years against the placebo band. Funding was complete in March 2014 (first roll 2015) and land buying began in 2012.
   - **Recommended:** keep groundbreaking as the event and 2016 as the reference year. Label the 2015 and 2016 rolls "after funding" in every chart. G2 passes or fails on 2012–2014 only, and a 2015 estimate outside the band is reported as possible anticipation, not as a failed comparison.
   - Or: leave G2 as written, and accept that a real anticipation effect would fail the gate.
   - Or: make the March 2014 commitment the event (reference year 2014, three before-years). This measures "the line became certain" and folds construction into the after-period.

B. **Metro Line: say "opening with restricted service" everywhere,** and add the service history to the confounds (recommended). The alternative is a second estimate dated from full-speed service (February 2017), which has nine months of clean service before restrictions returned and is not worth a design of its own.

C. **The file is `data/events.csv` with the columns in §3** (recommended), or another layout.

## 8. Order of work

After approval: write the file from §4, the roll rule and its test, and the guards in §3. Then replace the timing table in `docs/SPEC_event_study.md` §3 with a pointer to the file. None of this waits on the downloader.
