# Decisions Index

Append-only. **One ROW per locked decision** — when, what, why (including what
was rejected), and a pointer to where the argument lives in full. When a decision
locks, add a row; when one is superseded, strike it (`~~...~~`) or mark it
`SUPERSEDED <date>` in place and add the successor — don't delete history.

**What a row owes you:**

1. ⚠️ **EVERY ROW CARRIES A POINTER TO A DOC** — not only to code. Code moves;
   the argument has to live somewhere prose can hold it.
   `scripts/check_doc_citations.py` checks that every pointer resolves.
2. **The row is a self-contained summary** and may paraphrase the argument.
3. **The pointer is the authority.** When a row and its target disagree, the
   target wins and the row gets fixed.
4. **A row names the test that protects it**, or carries `[unverifiable]`.
   `scripts/check_decisions_log.py` enforces this on the merge gate (and that a
   superseded row is marked where it stands).

| When | Decision | Full reasoning |
|------|----------|----------------|
| 2026-11-14 | **v1 outcome is total assessed value, not an estimated land value.** Effects are estimated on `assessed_value` over 2012–2023 plus the current roll and labelled "in assessed value". The land layer is a later stage with its own plan. Rejected: land layer first (every effect would rest on our own unvalidated land model, with no prior result to compare against); both in parallel (double the v1 build and a second set of assumptions). `[unverifiable]`: no code exists yet. | `docs/SCOPE_research.md` §4, §6.1 |
