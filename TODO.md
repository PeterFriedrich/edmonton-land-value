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
- [ ] **Profile the raw panel and write the linker plan.** Measure account churn, coordinate shifts, splits and condo conversions, and how the current roll joins the history across renumbered accounts. Propose the rules for `src/build_panel.py` (position tolerance, link-method flag, unmatched report) to the owner. Done when: the owner has approved or changed the plan.
- [ ] **Panel linker, `src/build_panel.py`.** Blocked on the plan above.
- [ ] **Events table.** Hand-kept: event, geometry, announce / construction start / opening dates, first affected assessment year, source. Start with the Metro Line and Valley Line SE. Check each date against a primary source; the Metro Line opening date is still only reported.
- [ ] **Metro Line event study.** New module, so plan first. The plan must state: ring distances, the rule for MacEwan station at the downtown edge, the flag for parcels abutting an LRT right of way, detached and condo models, and the comparison with the two published Edmonton studies. Add `docs/ASSUMPTIONS.md` rows with the first estimate.
- [ ] **Valley Line SE study.** Construction-phase effect from 2012–2023; opening effect from the current roll as one flagged point.
- [ ] **Map v1.** Plan first. Stack is locked (`docs/DECISIONS.md`); open points are how tiles get built (tippecanoe isn't on the Oracle box's PATH, ask the `edmonton-civic-map` session) and the chart library. Read `docs/WEB_CACHE_BUSTING.md` first.

### Later (each needs its own plan)
- [ ] **Owner decides on sales data** once a Metro Line result exists.
- [ ] **Land layer.** Estimated land value per m².
- [ ] **Downtown study.** Downtown as one unit (synthetic control).
- [ ] **Zoning 2024 change and reversal.** Gated on the land layer and a usable post-2024 roll; the reversal half on the 2028 roll. Check the Edmonton and Calgary repeal dates against primary sources first.

## Done

Closed items moved out of `## Open work` live in **`docs/TODO_archive.md`** — one line each below, reasoning there.

- [x] **Foundational planning.** · `docs/TODO_archive.md`
- [x] **Answer the `docs/CHANGE_PIPELINE.md` "This project's fit" table.** · `docs/TODO_archive.md`
