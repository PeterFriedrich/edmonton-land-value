# 2026-11-14: scoping research handoff (S1)

## 0. Session Metadata
- **Model:** Opus 5.5 (`claude-opus-5-5`), with no model switch during the session.
- **Effort:** `medium` (checked with `$CLAUDE_EFFORT`).
- **Date / session #:** 2026-11-14, S1, the repo's first working session.

## 1. Goal
- **Project:** estimate how much city investments and the major events over time contributed to Edmonton land and assessed values, and show it on an interactive map (`CLAUDE.md` §Project).
- **This session:**
  - Research prior art, what Edmonton needs, and whether a map is feasible (the owner's first task, relayed by `server`).
  - Check the owner's Claude web report against local evidence.
  - Write everything into repo docs so the next session (the owner plans to use Fable) can do the **foundational planning**.

## 2. Current State
### ✅ Completed
- **Out-of-repo research** in `/home/opc/research/edmonton-land-value/`, three files:
  - `prior_art_and_feasibility_2026-11-14.md`: this session's report, plus an addendum from `edmonton-tax-viz` on the assessment data.
  - `prior_art_reply_2026-11-14.md`: the owner's Claude web report, saved verbatim.
  - `prior_art_reply_verification_2026-11-14.md`: a claim-by-claim check of that report.
- **Repo docs** (PR described in §4 step 0):
  - `docs/SCOPE_research.md` (new). **The one file the planning session must read**: prior art, datasets, defects, circularity, candidate events, and the §6 decisions list.
  - `data/DATA.md`: source entries from metadata (nothing downloaded yet).
  - `docs/DATA_ISSUES.md`: one row on the 2024/2025 gap in `qi6a-xuwt`, NOT SENT. The draft report belongs to `edmonton-tax-viz`.
  - `TODO.md`: the planning item, plus the template's "fit table" obligation.
  - `CLAUDE.md` Key Files: a pointer to `docs/SCOPE_research.md`.
- **Owner decisions so far: none.** `docs/DECISIONS.md` and `docs/SCOPE.md` are deliberately untouched, because nothing was locked.

### ⚠️ In Progress
- No code. No data downloaded.

### ❌ Blocking Issues
- None. The design is constrained by data defects (below), not blocked.

## 3. Technical Learnings
- **`qi6a-xuwt` historical assessments:**
  - Covers 2012–2025 with total value only.
  - 🛑 **2024 and 2025 are missing whole buildings.** Re-checked: Stantec Tower, 10310 102 St NW, has 315 accounts in 2021–23 and 0 in 2024–25. The usable window is **2012–2023 plus the current roll** `q7d6-ambg`.
- **The City's 2026 methodology codes site influences by rule**, including a *negative* "LRT influence" for abutting or across-the-road parcels. A DiD on assessments partly measures that rule. The owner's Claude web report missed this.
- **Sibling resources:**
  - `edmonton-tax-viz` has no parcel panel and no account crosswalk.
  - Its identifier-churn notes are in its `data/DATA.md` and `docs/DATA_ISSUES.md` §3.
  - Its name-correction map is `NAME_CORRECTIONS` in `~/edmonton-tax-viz/src/load_assessment.py`.
- **Socrata access:** paging is required (a 50k-row cap per request).
- **Box quirks:**
  - Use certifi's CA bundle for HTTPS (stale system bundle).
  - `pdftotext` is missing; `pypdf` works in `~/edmonton-tax-viz/.venv`.
  - This repo has no `.venv` of its own yet.
- **Public repo:** keep data-access and outreach plans (who to ask for sales data) in the research folder, not in repo docs (global `CLAUDE.md` rule).

## 4. Next Steps (prioritized)
0. If the PR for this session isn't merged, the owner merges it. It changes `data/DATA.md`, which is a synced file, but `docs/BRIEF.md` doesn't exist yet, so there's no claude.ai Project to sync.
1. **Planning session:**
   - Read `docs/SCOPE_research.md` in full. On the Oracle box, also read the three research files it names; the verification file is the short one.
   - Take the owner through its §6 decisions, one at a time:
     1. outcome (assessed value or a land layer);
     2. first case (LRT; how to get a Valley Line SE post-opening point);
     3. unit of analysis;
     4. downtown;
     5. the zoning-reversal study;
     6. sales validation;
     7. map stack;
     8. out-of-scope list;
     9. the panel-build module plan.
2. Record what the owner locks in `docs/DECISIONS.md`. Each row needs a test or `[unverifiable]` (enforced by `scripts/check_decisions_log.py`). Put rejected ideas in `docs/SCOPE.md`.
3. Answer the `docs/CHANGE_PIPELINE.md` "This project's fit" table.
4. Replace the planning item in `TODO.md` with the build backlog. The first build item is probably the panel downloader, which is a new module, so it needs a plan first.

## 5. Restoration Procedure
- `cd ~/edmonton-land-value && git config core.hooksPath .githooks && gh issue list --state open`
- Create a venv when code starts: `python3.12 -m venv .venv && .venv/bin/pip install -r requirements.txt` (check `requirements.txt` first).
- Before any big download, run `df -h /`. It showed about 9 GB free on 2026-11-14.

---

## Update: end of session (/handoff, 2026-11-14)
- **Model / effort:** Opus 5.5 (`claude-opus-5-5`), `medium` (re-checked with `$CLAUDE_EFFORT`). Same as above.
- **PR #1** (`docs/scoping-research`): OPEN, CI `test` **pass**. The owner merges it, because it changes `data/DATA.md`, a synced file, so the session can't self-merge. After merge, confirm it landed with `git merge-base --is-ancestor <merge sha> origin/master`. `docs/BRIEF.md` doesn't exist yet, so there's no claude.ai Project sync to do.
- **Message to `server`:** asked it to add a row for `/home/opc/research/edmonton-land-value/` to `/home/opc/research/README.md`. No reply as of this handoff. Check whether the row is there; that file is `server`'s to edit, not ours.
- **Gotcha seen this session:** a verification chain of `pytest … | tail -1 && git commit` does **not** stop on a pytest failure, because the pipe's exit status is `tail`'s. Run pytest with `.venv/bin/python` (this repo **does** have a `.venv`; §3 above is wrong to say it has none) and chain on pytest's own status: `.venv/bin/python -m pytest tests -q && git commit …`.
- **Next steps:** unchanged from §4. The first action for the planning session (Fable) is to read `docs/SCOPE_research.md` and go through its §6 decisions with the owner.

---

# 2026-11-14: foundational planning handoff (S2)

## 0. Session Metadata
- **Model:** Fable 5.2 (`claude-fable-5-2`), with no model switch during the session.
- **Effort:** `medium` (checked with `echo "$CLAUDE_EFFORT"`).
- **Date / session #:** 2026-11-14, S2. S1 is the section above; where the two disagree, this section is newer.

## 1. Goal
- **Project:** estimate how much city investments and major events contributed to Edmonton assessed values, and show it on an interactive map.
- **This session:** take the owner through the nine planning decisions in `docs/SCOPE_research.md` §6, record them, answer the fit table, and replace the planning item with a build backlog.

## 2. Current State
### ✅ Completed
- **PR #1 (S1) is merged**: merge commit `74a6d61` on `origin/master`. `server` added the `research/README.md` row S1 asked for.
- **PR #2** (`docs/foundational-planning`, https://github.com/PeterFriedrich/edmonton-land-value/pull/2): MERGED by the owner mid-session, merge commit `d2105cc`, confirmed on `origin/master` with `git merge-base --is-ancestor`. Docs only, no synced file.
- **All nine decisions answered by the owner**, each picked in session (the owner took the recommended option every time):
  1. **Outcome:** total assessed value for v1 (2012–2023 plus the current roll), labelled "in assessed value". The land layer is a later stage.
  2. **First case:** Metro Line LRT, then Valley Line SE (construction effect from the clean window; opening effect from the current roll as one flagged point). Parcels abutting an LRT right of way are flagged.
  3. **Unit:** estimate on account-level points, detached and condo modelled separately; display as H3 hexagons (resolution 9), neighbourhoods at low zoom.
  4. **Downtown:** no attribution in v1. Downtown accounts stay in the panel and on the map but are excluded from LRT treatment and control groups.
  5. **Zoning change and reversal:** a later study, gated on the land layer and a usable post-2024 roll; the reversal half waits for the 2028 roll.
  6. **Sales data:** deferred. The owner decides after a Metro Line result exists.
  7. **Map stack:** follows `edmonton-civic-map` (MapLibre GL JS with its own layers only, PMTiles, Vite with plain JavaScript, static on GitHub Pages, no outside requests).
  8. **Out of scope:** all five items (SHAP as causal, per-parcel dollars, causal macro attribution, separating downtown projects, "investment ROI").
  9. **Panel build:** two modules. Only `src/download_assessments.py` is approved. `src/build_panel.py` gets its own plan after the downloaded data is profiled.
- **Files changed by PR #2:**
  - `docs/DECISIONS.md`: eight rows (decisions 1–7 and 9), all `[unverifiable]` because no code exists.
  - `docs/SCOPE.md`: the five out-of-scope bullets (decision 8 lives here, not in a decisions row).
  - `docs/CHANGE_PIPELINE.md` §"This project's fit": answered, 19 `adopted`, 11 `later`, none `no`.
  - `TODO.md`: the two planning items closed and archived to `docs/TODO_archive.md`; the build backlog replaces them.

### ⚠️ In Progress
- This handoff is on its own branch, `docs/handoff-s2` (PR #3), because PR #2 merged before the handoff was committed. Nothing else is half-done. No code, no data downloaded.

### ❌ Blocking Issues
- None.

## 3. Technical Learnings
- **`tippecanoe`, `pmtiles` and `ogr2ogr` are not on the Oracle box's PATH** (checked with `which`). How `edmonton-civic-map` builds its tiles was not looked into; ask that session before the map build.
- **`master` requires the `test` check**, admins not enforced (checked with `gh api …/branches/master/protection`).
- **The Metro Line opening date (2015-09-06) is still only reported**, not checked against a primary source.
- **Sibling stack rows:** the civic-map stack decision is its `docs/DECISIONS.md` row of 2026-10-13; `edmonton-tax-viz` uses frameworkless MapLibre plus deck.gl.
- **Guards:** `scripts/check_fit_table.py` and `scripts/check_assumptions.py` exist alongside the decisions and citation guards. Run all of them with `for c in scripts/check_*.py; do .venv/bin/python "$c"; done`. `check_doc_citations.py` prints about 32 warnings for template files that don't exist yet and still exits 0.
- **TODO format:** mark an item `- [x]`, then `.venv/bin/python tools/todo_archive.py` moves it to `docs/TODO_archive.md` and leaves a `## Done` line.
- **Public repo:** decision rows and the backlog name no prospective data holders. Keep any outreach plan in `/home/opc/research/edmonton-land-value/`.
- **The owner merged PR #2 within minutes of CI going green**, between the PR check and the handoff commit. The commit chain's own `gh pr view … = OPEN` test stopped it; keep that test in the chain.
- **Disk:** 9.3 GB free on 2026-11-14.

## 4. Next Steps (prioritized)
1. **Check PR #3** (the handoff, `gh pr view 3 --json state,mergeCommit`). If it is unmerged, it is markdown only with no synced file. Then branch fresh from `origin/master`.
2. **Build the assessment downloader, `src/download_assessments.py`** (plan approved; first item in `TODO.md`):
   - Page `qi6a-xuwt` one assessment year at a time (50k-row cap per request, stable `$order`) into `data/raw/` as one parquet file per year, plus the current roll `q7d6-ambg`.
   - Fail if a year's row count differs from the server's `count(*)`. Write a manifest (dataset, year, rows, server count, fetch time).
   - Keep 2024–25, flagged incomplete. Never drop them.
   - Use certifi's CA bundle. Add the dependencies to `requirements.txt` and, if CI needs them, `requirements-ci.txt` (`scripts/check_requirements.py` keeps the two in step).
   - Break the row-count guard on purpose and confirm it goes red. Then update the decision 9 row to cite the test, and record findings in `data/DATA.md` (a synced file, so that PR needs the owner's merge).
3. **Profile the raw panel and write the linker plan** for the owner: account churn, coordinate shifts, splits and condo conversions, and how the current roll joins the history across renumbered accounts.
4. The rest of the order is in `TODO.md`: linker, events table, Metro Line study (plan first), Valley Line SE, map v1.

## 5. Restoration Procedure
- `cd ~/edmonton-land-value && git config core.hooksPath .githooks && gh issue list --state open` (no open issues on 2026-11-14).
- `.venv` exists; it holds only `pytest` and `copier` so far. Tests: `.venv/bin/python -m pytest tests -q` (124 passed, 1 skipped).
- Before the download, run `df -h /`.
