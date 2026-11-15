# 2026-11-15: planning specs handoff (S3)

## 0. Session Metadata
- **Model:** Fable 5.2 (`claude-fable-5-2`), with no model switch during the session.
- **Effort:** `high` (checked with `echo "$CLAUDE_EFFORT"`).
- **Date / session #:** 2026-11-14 to 2026-11-15, S3. S1 and S2 are in `session-summary/2026-11-14_scoping-research.md`.

## 1. Goal
- **Project:** estimate how much city investments and major events contributed to Edmonton assessed values, and show it on an interactive map.
- **This session:** the owner wants Fable used for the hardest planning work, not coding. The session wrote the study design and the profiling protocol, and left the downloader build for a cheaper session.

## 2. Current State
### ✅ Completed
All three PRs are merged, and the last commit (`701103c`) is confirmed on `origin/master` with `git merge-base --is-ancestor`.
- **PR #4** (merge `665dc1e`): `docs/SPEC_event_study.md`, the LRT event-study design. `docs/DECISIONS.md`: the first-case row is marked `AMENDED 2026-11-14`, and a new row makes the Metro-Line-first order **conditional on feasibility gates** (the owner agreed in session).
- **PR #5** (merge `7d4ed5d`): §3A added to the spec from the City's methodology guides.
- **PR #6** (merge `b45e3d9`): `docs/SPEC_panel_profile.md`, the profiling protocol for the linker.
- `TODO.md`, `data/DATA.md` and the `CLAUDE.md` Key Files list were updated in the same PRs.

What the specs establish:
- **Treated-sample counts** (2015 roll, residential with no suite, within 800 m of non-downtown stations): Metro Line 1,015 accounts in 4 neighbourhoods; Valley Line SE 9,093 in 19. The Metro Line is expected to fail the gates, which would make Valley Line SE construction the first result.
- **Five gates** (spec §7): precision, before-years, granularity, airport, composition. They run on the linked panel before any after-year estimate is looked at.
- **The City's model has no distance-to-station term** (read from the 2023–2026 guides). Location enters through market area, neighbourhood and study area. The LRT influence covers houses and land abutting or across from the right of way; the condo guides have no LRT term.
- **Each roll pools five years of sales**, so an effect ramps in over five rolls. The headline is the average of the 2022 and 2023 rolls. Allinott's short-window figures can show only a fraction of an effect.
- **Uncertainty comes from placebo stations**, not standard errors.
- **The profile measures no effect** and every table must reconcile.

### ⚠️ In Progress
- Nothing half-done. No code exists and no data is downloaded.
- **Awaiting the owner:** the eight choices in `docs/SPEC_event_study.md` §11. They were merged as proposals, not answered. None blocks the downloader or the profile.
- Both specs say "a plan awaiting the owner's approval". The owner merged them but did not say "approved" in session; treat the profile spec as the working plan and ask before treating §11 as locked.

### ❌ Blocking Issues
- None.

## 3. Technical Learnings
- **Methodology guides** are at `https://www.edmonton.ca/sites/default/files/public-files/<name>.pdf` (the `/assets/PDF/` path 404s). Names that work: `2026_RES_Improved`, `2026_RES_Land`, `2026_RES_Cost`, `2026_RES_HighriseCondo`, `2026_RES_LowriseCondo`, `2026_RES_TownhomeCondo`, `2025_RES_Improved`, `2023_RES_Land`, `2026_StudyAreas_Neighbourhood` (20 MB). Improved guides for 2016–2024 return 404. The web archive's CDX API returned 503 when tried. Send a browser User-Agent.
- **Guide text extracts one word per line** with `pypdf` (in `~/edmonton-tax-viz/.venv`); collapse whitespace before searching. The appendix maps (market areas) are images with no text.
- **Not verified:** the ramp table assumes a five-year window in every roll and equal weight on every sale; guides for the 2012–2022 rolls were not found; the guides were read by text extraction and keyword search, not page by page; the study-area maps were not inspected (King Edward Park has an "East" study area).
- **`fhxi-cnhe` (LRT stations)** is platform-level (54 rows, 28 names) and has no NAIT entry. The counts used an approximate NAIT point (53.5657, −113.5043).
- **Socrata quirks:** `within_circle(point_location, lat, lon, metres)` works for ring counts; `count(distinct account_number)` works; `tax_class_pct_1` is text, so `< 100` is a type error; a bare `1=1` in `$where` returned 403.
- **Field facts** (2012, 2015, 2023 only): `account_number` is unique within a year; `assessed_value` is never null, about 1,000 zeros a year; missing coordinates jump from 381 and 326 to 9,472 in 2023 (unexplained); `legal_description` has a lot shape and a condo-unit shape.
- **Event dates:** Metro Line opened 2015-09-06 on interim 25 km/h service, original target April 2014 (two news reports, no City source). City Centre Airport closed 2013-11-30. Still missing: Metro Line construction start, Valley Line SE approval and funding dates, the airport overlay removal date.
- **Allinott's design** (paper text read): treated within 1 km, comparison beyond 6.5 km (Valley Line SE) or 7.6 km (Metro Line) from any open or planned station, pooled model 2012–2019, treatment years 2017 and 2016. By the valuation-date rule the 2016 roll predates the Metro Line opening.
- **Stacked PRs worked:** PR #6 was branched from PR #5's branch while #5 was open, and the owner merged both in order.
- **Chain pytest on its own exit status**, not through `| tail`: `.venv/bin/python -m pytest tests -q >/dev/null && …`.
- **Disk:** 9.3 GB free on 2026-11-15.

## 4. Next Steps (prioritized)
1. **Coding session (cheaper model): build `src/download_assessments.py`.** The plan is approved; the item is first under "v1" in `TODO.md`. Run `df -h /` first.
2. **Coding session: run the profile**, following `docs/SPEC_panel_profile.md` (blocks A–K, the two rules, the self-tests).
3. **Planning (Fable), no data needed, in this order:**
   - **The events table:** its columns, the rule from date to first affected roll, and a primary source for each date. The missing dates are listed in §3 above and in `docs/SPEC_event_study.md` §3.
   - **What the map shows:** how a ring estimate with a placebo band becomes hexagons, what "not estimable" looks like, and where the city-wide trend sits. Ask the `edmonton-civic-map` session how it builds tiles first (`tippecanoe`, `pmtiles` and `ogr2ogr` are not on this box's PATH).
4. **Take the owner through the eight choices** in `docs/SPEC_event_study.md` §11 and record each in `docs/DECISIONS.md`.
5. **Build neighbourhood-to-market-area membership** from the maps in `2026_RES_Improved.pdf` (a `TODO.md` item). The same-market-area comparison rule needs it.
6. **Planning (Fable), after the profile report exists:** the linker plan, deciding the items in `docs/SPEC_panel_profile.md` §6.

## 5. Restoration Procedure
- `cd ~/edmonton-land-value && git config core.hooksPath .githooks && gh issue list --state open` (no open issues on 2026-11-15).
- Branch fresh from `origin/master`; `docs/panel-profile`, `docs/assessment-mechanics` and `docs/event-study-spec` are merged and dead.
- `.venv` holds only `pytest` and `copier`. Tests: `.venv/bin/python -m pytest tests -q` (124 passed, 1 skipped). Guards: `for c in scripts/check_*.py; do .venv/bin/python "$c"; done`.
- For HTTPS use certifi's bundle: `/home/opc/edmonton-tax-viz/.venv/lib/python3.12/site-packages/certifi/cacert.pem`.
