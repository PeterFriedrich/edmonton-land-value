# TODO

The source of truth for what's left. Read first every session; update in place.

**Format contract** (`tools/todo_archive.py` depends on it): top-level items are
`- [ ]` / `- [x]` lines directly under `## Open work`; `###` sub-headings may
group them; closed items are moved to `docs/TODO_archive.md` by the tool, which
leaves a one-line stub under `## Done`. An open item can be stale — reproduce the
symptom and re-measure the stated cause before acting on it.

## Open work

### v1: the Metro Line study (in order)
- [ ] **Assessment downloader, `src/download_assessments.py`** (plan approved 2026-11-14, see `docs/DECISIONS.md`). Pages `qi6a-xuwt` one assessment year at a time into `data/raw/` as one parquet file per year, plus the current roll `q7d6-ambg`. Fails if a year's row count differs from the server's count. Writes a manifest (dataset, year, rows, server count, fetch time). Keeps 2024–25, flagged incomplete. Use certifi's CA bundle; run `df -h /` first. Done when: all years are on disk, the count guard has been broken on purpose and gone red, and `data/DATA.md` records what was found.
- [ ] **Build neighbourhood-to-market-area membership** (nine assessment market areas plus the River Valley submarket). The boundaries are map images in the appendix of `2026_RES_Improved.pdf`; no open dataset was found. Needed for the same-market-area comparison rule in `docs/SPEC_event_study.md` §5. Also look again for the 2012–2022 methodology guides, which §3A could not find. Done when: a hand-kept table with its source page exists, or the owner drops the rule.
- [ ] **Profile the raw panel**, following `docs/SPEC_panel_profile.md` (blocks A–K, the two rules, the self-tests). A coding task; the spec says what to measure and what the report looks like. Done when: the report exists, its self-tests pass, and new facts are in `data/DATA.md`.
- [ ] **Write the linker plan** from the profile report, deciding the items in `docs/SPEC_panel_profile.md` §6 (link-key order, position tolerance, splits and rebuilds, property-group rule, fixed position, current-roll join, 2024–25). Done when: the owner has approved or changed the plan.
- [ ] **Panel linker, `src/build_panel.py`.** Blocked on the plan above.
- [ ] **Events table, `data/events.csv`** (plan approved 2026-11-16, `docs/SPEC_events_table.md`). A coding task that needs no assessment data: write the file from the spec's §4 rows, the date-to-roll rule with its test, and the row guards in §3, each broken on purpose once. Then point `docs/SPEC_event_study.md` §3 at the file. Still to find, each from a primary source: the day of the March 2013 federal announcement; the provincial release for 2014-03-11; financial close (2016-02-11 rests on company releases); the Valley Line SE corridor and concept-plan approvals and the phased airport-closure decision (all before June 2011, where the council-minutes dataset begins); when Metro Line speed restrictions ended for good; which neighbourhoods the Airport Protection Overlay covered. The City's Valley Line booklet (August 2016, page images) likely holds the early history and needs a PDF renderer this box lacks.
- [ ] **Run the gates in `docs/SPEC_event_study.md` §7 on the linked panel** (precision, before-years, granularity, airport, composition), before any after-year estimate is looked at. Done when: the owner has seen the gate results and the first case is confirmed or switched.
- [ ] **Event-study module, first case.** New module, so plan first; the design is `docs/SPEC_event_study.md`, and the first case is whichever line the gates leave standing. Includes the Allinott replication arm. Add `docs/ASSUMPTIONS.md` rows with the first estimate.
- [ ] **Second LRT case.** The other line, or the Metro Line's "not estimable" write-up if it failed. Valley Line SE's opening effect is one flagged point from the current roll.
- [ ] **Map v1.** Plan first. What the reader sees is `docs/SPEC_map_display.md` (the two-layer design is approved; its §8 choices 2–4 are open). Stack is locked (`docs/DECISIONS.md`). How `edmonton-civic-map` builds tiles is recorded in that spec's §7: measure the hexagon GeoJSON's gzipped size before choosing PMTiles. Read `docs/WEB_CACHE_BUSTING.md` first.

### Later (each needs its own plan)
- [ ] **Owner decides on sales data** once a Metro Line result exists.
- [ ] **Land layer.** Estimated land value per m².
- [ ] **Downtown study.** Downtown as one unit (synthetic control).
- [ ] **Zoning 2024 change and reversal.** Gated on the land layer and a usable post-2024 roll; the reversal half on the 2028 roll. Check the Edmonton and Calgary repeal dates against primary sources first.

## Done

Closed items moved out of `## Open work` live in **`docs/TODO_archive.md`** — one line each below, reasoning there.

- [x] **Foundational planning.** · `docs/TODO_archive.md`
- [x] **Answer the `docs/CHANGE_PIPELINE.md` "This project's fit" table.** · `docs/TODO_archive.md`
- [x] **Owner reviews `docs/SPEC_event_study.md`.** All eight §11 choices answered 2026-11-16 and recorded in `docs/DECISIONS.md`. · `docs/TODO_archive.md`
