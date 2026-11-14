# DATA_ISSUES — defects in data we do not control, and what we've told the publisher

**What belongs here:** a defect in a source we consume — plus the evidence, what
it breaks on our side, and **whether anyone has been told**. One row per defect,
newest first.

**What does NOT belong here:**

| this file | where it goes instead |
|---|---|
| how a source is shaped, its columns, its quirks | `data/DATA.md` |
| our own build work and backlog | `TODO.md` |
| a locked decision + its reasoning | `docs/DECISIONS.md` |
| an audit we ran over our own pipeline | `docs/AUDIT_LEDGER.md` |

⚠️ **The point of the file is the last column.** Findings that never leave the
repo are the standing failure mode: a measured upstream defect can be archived
unsent as a sub-item of a closed parent and go invisible. Status must be one of
**NOT SENT · SENT (date) · ACKNOWLEDGED (date) · FIXED (date) · WONTFIX**, and
"the artifact exists" is **not** sent.

---

| Date found | Source / dataset | Defect | Evidence | What it breaks here | Status |
|------------|------------------|--------|----------|---------------------|--------|
| 2026-11-14 (first found by `edmonton-tax-viz`) | `qi6a-xuwt` Property Assessment Data (Historical) | 2024 and 2025 slices are missing 2,448 accounts that exist in earlier years and in the current roll, in whole buildings | Re-checked here: 10310 102 St NW has 315 accounts in 2021–2023 and 0 in 2024–2025 (`$group=assessment_year` query, 2026-11-14). Full evidence: https://peterfriedrich.github.io/edmonton-tax-viz/notebooks/historical-2024-gap.html | The historical panel is usable for 2012–2023 only; the Valley Line SE post-opening period is missing | NOT SENT (the draft report belongs to `edmonton-tax-viz`, `docs/DRAFT_bug_report_historical_dropout.md`) |
