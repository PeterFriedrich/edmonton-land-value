# SCOPE — what this project deliberately does not do

The one hand-kept input to the Claude web brief (`scripts/make_brief.py`, setup
in `docs/CLAUDE_WEB.md`). Everything else in the brief is generated from files
the repo already maintains; this is the list an outside reviewer can't derive —
ideas that were considered and turned down, so they stop coming back as
recommendations.

One bullet each: **the thing**, then why not, and a pointer if a decision row
or doc holds the argument. A turned-down idea that has a locked decision needs
no bullet here — the brief already carries `docs/DECISIONS.md`.

## Out of scope

- **Machine-learning feature attribution (e.g. SHAP) presented as causal**: a model fitted to assessments re-learns the assessor's formula and can't tell "built where values were rising" from "raised values". A descriptive view labelled as such is not ruled out. `docs/SCOPE_research.md` §2.
- **A dollar contribution for an individual parcel**: effects are estimated for groups of accounts, not single ones. Area totals with a range stay in scope. `docs/SCOPE_research.md` §6.8.
- **Causal attribution of macro shocks** (oil crash, COVID, migration): about 12 annual points can't identify them, so they appear as one city-wide trend. `docs/SCOPE_research.md` §4.6.
- **Separating the co-located downtown projects from each other**: the levy area, arena, LRT, rezoning and office shock overlap. `docs/SCOPE_research.md` §4.4, and the downtown row in `docs/DECISIONS.md`.
- **"Investment ROI"**: assessed value misses benefits such as travel time and holds no costs, so a return figure would read a partial benefit as the whole. `docs/SCOPE_research.md` §6.8.
