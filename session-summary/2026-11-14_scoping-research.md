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
