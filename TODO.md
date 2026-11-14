# TODO

The source of truth for what's left. Read first every session; update in place.

**Format contract** (`tools/todo_archive.py` depends on it): top-level items are
`- [ ]` / `- [x]` lines directly under `## Open work`; `###` sub-headings may
group them; closed items are moved to `docs/TODO_archive.md` by the tool, which
leaves a one-line stub under `## Done`. An open item can be stale — reproduce the
symptom and re-measure the stated cause before acting on it.

## Open work

- [ ] **Foundational planning.** Read `docs/SCOPE_research.md` (and, on the Oracle box, the three files in `/home/opc/research/edmonton-land-value/` it names). Take the owner through its §6 decisions. Write the locked ones into `docs/DECISIONS.md` and the rejected ideas into `docs/SCOPE.md`, then replace this item with the build backlog. Done when: §6 has an owner answer for each item, and `TODO.md` holds the first build items.
- [ ] **Answer the `docs/CHANGE_PIPELINE.md` "This project's fit" table.** Every row still reads `not evaluated`. Do it during the planning session (it's a template obligation at project creation).

## Done
