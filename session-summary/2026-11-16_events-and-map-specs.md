# 2026-11-16: events table, map display and design decisions handoff (S4)

## 0. Session Metadata
- **Model:** Fable 5.2 (`claude-fable-5-2`), with no model switch during the session.
- **Effort:** `medium` (checked with `echo "$CLAUDE_EFFORT"`). The S3 specs were written at `high`; this session's two specs and twelve decision rows were not, so give them a closer read before building on them.
- **Date / session #:** 2026-11-16, S4. S3 is `session-summary/2026-11-15_planning-specs.md`.

## 1. Goal
- **Project:** estimate how much city investments and major events contributed to Edmonton assessed values, and show it on an interactive map.
- **This session:** the two planning items that need no data (the events table, what the map shows), and taking the owner through the eight choices in `docs/SPEC_event_study.md` §11.

## 2. Current State
### ✅ Completed
PR #8 is merged, and its last commit (`ab69773`) is confirmed on `origin/master` with `git merge-base --is-ancestor`.
- **`docs/SPEC_events_table.md`** (new, approved): the rule from an event date to the first affected roll, the layout of the hand-kept `data/events.csv`, and every Metro Line, Valley Line SE and City Centre Airport date found, each with its source.
- **`docs/SPEC_map_display.md`** (new): hexagons show observed change compared with the city; estimates are drawn on rings in three states (outside the placebo band, inside it, not estimable). The two-layer design is approved; its §8 choices 2–4 are still proposals.
- **`docs/DECISIONS.md`:** twelve rows dated 2026-11-16. The owner chose the recommended option every time.
  - The eight §11 choices: G1 at ±3 points; rings 0–400, 400–800 and an 800–1,600 m spillover ring; comparison group 1.6–5 km, matched on 2012 value and year built; accounts beside the tracks out of the headline; nearest-station exclusion for downtown-edge stations; a failing line published as "not estimable"; headline is the average of the 2022 and 2023 rolls; comparison accounts in the same market area.
  - Four new ones: Valley Line SE gate G2 judged on 2012–2014 only; every Metro Line result named "opening with restricted service"; the events file layout; the map's two layers.
- **`docs/SPEC_event_study.md`:** status line, timing table (now cites primary sources), the G2 rule, the Metro Line confounds and the §11 heading were updated to match.
- **`data/DATA.md`:** entries for the council-minutes dataset `7zht-i9ve` and the City project pages.
- `TODO.md`, `docs/TODO_archive.md` and the `CLAUDE.md` Key Files list were updated in the same PR.

What the date research established:
- **Metro Line.** Opened 2015-09-06 with trains limited to 25 km/h between MacEwan and NAIT (City page). Right-of-way work began "2010 to 2011" and the provincial grant came 2011-04-05, so every before-year in the panel is after funding and construction start. Restrictions were lifted 2017-02-19, put back in November 2017, and still in place in November 2019.
- **Valley Line SE.** Groundbreaking 2016-04-22 (federal release); opened 2023-11-04 (City page). Funding was complete on 2014-03-11, which first touches the 2015 roll, and council approved land buying along the route on 2011-11-25, for purchase over 2012–2014.
- **Airport.** Council set the closure for 2013-11-30 by a motion of 2013-09-18. The Blatchford implementation strategy (2014-06-10) and the removal of the Airport Protection Overlay (Bylaw 16859, 2014-06-24) follow. All three first touch the 2015 roll, so gate G4 cannot tell them apart.

### ⚠️ In Progress
- Nothing half-done. No code exists and no data is downloaded.
- **Open proposals for the owner:** `docs/SPEC_map_display.md` §8, choices 2–4 (in-band rings drawn neutral, a 10-account minimum for a hexagon to show a value, an inline SVG chart). None blocks any coding task.

### ❌ Blocking Issues
- None.

## 3. Technical Learnings
- **Council minutes are an open dataset:** `7zht-i9ve`, 75,969 rows, meetings 2011-06-01 to 2025-09-19. Query with `$q=<text>` plus `$where=meeting_date between '…' and '…'`. A motion often appears two or three times (as moved, as amended, as the final "Action:" text). Nothing before June 2011.
- **The catalogue search lives in a sibling repo:** `cd ~/edmonton-open-data-landscape && .venv/bin/python -m src.search "query one" "query two" -k 8`. It takes several queries in one run and answers in seconds (the vectors for snapshot 2026-11-13 are cached). It was on the unmerged branch `feat/catalogue-search` on 2026-11-16. Insight Community survey datasets crowd the results; filter them out.
- **edmonton.ca pages answer `curl` and fail the web-fetch tool.** Use `curl -sL --cacert <certifi bundle> -A "<browser User-Agent>"`. The web-fetch agent got HTTP 502 on every edmonton.ca URL. For canada.ca it is the reverse: `curl` from this box returned code 000 and the web-fetch agent worked.
- **City pages that hold dated history:** `https://www.edmonton.ca/projects_plans/transit/north-lrt-project-history` (Metro Line, back to 2005) and `https://www.edmonton.ca/projects_plans/transit/valley-line-southeast`.
- **The Valley Line booklet is unreadable here:** `https://www.edmonton.ca/sites/default/files/public-files/documents/valley_line_lrt_booklet.pdf` (13.8 MB, 48 pages) is page images with no text layer, and the Read tool cannot render PDFs on this box because `pdftoppm` is not installed.
- **One conflict between City sources:** the Valley Line page says council approved the public-private partnership "in August 2012"; the minutes show the motion carried 2012-05-02. The spec uses the minutes. Both give the 2013 roll.
- **The roll rule:** an event on or before 30 June of year T first affects roll T+1; from 1 July onward, T+2. The first roll whose whole sales window is after the event is five rolls later. The first-affected-roll columns in `docs/SPEC_events_table.md` §4 were worked by hand and are not yet checked by a test.
- **Tiles, as reported by the `edmonton-civic-map` session (not re-run here):** `tippecanoe==2.72.0` from PyPI ships an ARM binary; they build by hand on this box and commit the `.pmtiles` with a manifest; they use PMTiles only for a 384,000-polygon layer and plain GeoJSON for boundary layers. Details and paths are in `docs/SPEC_map_display.md` §7.
- **Not verified:** the 2014-03-11 provincial commitment (news only) and the 2016-02-11 financial close (company releases found by search, not opened); the hexagon count (about 7,400 at most) and the 1 MB GeoJSON threshold in the map spec are estimates; no published map of an event-study result was examined for convention.
- **Disk:** 8.9 GB free on 2026-11-16.

## 4. Next Steps (prioritized)
1. **Press Sync in the claude.ai Project** if one is set up for this repo (`docs/BRIEF.md` does not exist yet, so it may not be). PR #8 changed:
   - `docs/SPEC_events_table.md`
   - `docs/SPEC_map_display.md`
   - `docs/SPEC_event_study.md`
   - `data/DATA.md`
2. **Coding session (cheaper model): build `src/download_assessments.py`.** Plan approved; first item under "v1" in `TODO.md`. Run `df -h /` first.
3. **Coding session: write `data/events.csv`** from `docs/SPEC_events_table.md` §4, with the date-to-roll rule, its test against the five worked cases in §2, and the row guards in §3, each broken on purpose once. Needs no assessment data. Then point `docs/SPEC_event_study.md` §3 at the file.
4. **Coding session: run the profile**, following `docs/SPEC_panel_profile.md`.
5. **Find the dates still missing a primary source** (listed in the `TODO.md` events-table item): the day of the March 2013 federal announcement, the provincial release for 2014-03-11, financial close, the pre-June-2011 approvals, when Metro Line restrictions ended, and which neighbourhoods the Airport Protection Overlay covered.
6. **Build neighbourhood-to-market-area membership** from the maps in `2026_RES_Improved.pdf`. The same-market-area rule is now locked and is blocked on this.
7. **Planning (Fable), after the profile report exists:** the linker plan (`docs/SPEC_panel_profile.md` §6).
8. **Ask the owner** the three open choices in `docs/SPEC_map_display.md` §8 when the map build is planned.

## 5. Restoration Procedure
- `cd ~/edmonton-land-value && git config core.hooksPath .githooks && gh issue list --state open` (no open issues on 2026-11-16).
- Branch fresh from `origin/master`; `docs/events-table` is merged and dead.
- `.venv` holds only `pytest` and `copier`. Tests: `.venv/bin/python -m pytest tests -q` (124 passed, 1 skipped). Guards: `for c in scripts/check_*.py; do .venv/bin/python "$c"; done` (the doc-citation guard prints 33 quoted-title warnings and exits 0).
- For HTTPS use certifi's bundle: `/home/opc/edmonton-tax-viz/.venv/lib/python3.12/site-packages/certifi/cacert.pem`. `pypdf` is in the same venv.
