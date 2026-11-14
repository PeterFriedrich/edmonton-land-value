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
| 2026-11-14 | **First case is the Metro Line LRT; Valley Line SE is second.** Metro Line's before and after years sit inside the clean 2012–2023 window, and two Edmonton studies give a result to compare against. Valley Line SE follows: construction-phase effect from the clean window, opening effect from the current roll as a single flagged point until later rolls exist. Parcels abutting or across from an LRT right of way are flagged, because the City codes a negative LRT influence there by rule. The Metro Line opening date (2015-09-06) is reported, not yet checked against a primary source. Rejected: Valley Line SE first on the current roll (opening effect rests on one point joined across an account-number mismatch); waiting for the 2027 roll (nothing to show until then); a non-LRT first case (harder identification or no Edmonton prior result). `[unverifiable]`: no code exists yet. | `docs/SCOPE_research.md` §2, §3 defects, §4.1, §6.2 |
| 2026-11-14 | **Estimate on account-level points; display as H3 hexagons.** Estimation follows the same account across years, with detached houses and condos modelled separately. The map shows H3 hexagons (resolution 9) with neighbourhoods at low zoom. Rejected: neighbourhood-only display (a neighbourhood is far larger than a station ring, and names and boundaries change); estimating on hex-year aggregates (composition change inside a hex reads as value change, and distance to a station becomes coarse). `[unverifiable]`: no code exists yet. | `docs/SCOPE_research.md` §3 defects, §6.3 |
| 2026-11-14 | **v1 makes no downtown attribution.** Downtown accounts stay in the panel and on the map as observed values, but are excluded from LRT treatment and control groups and get no estimated contribution. A downtown study that treats downtown as one unit (synthetic control) is a later backlog item. The Metro Line's MacEwan station sits at the downtown edge, so the build plan must state the rule for its ring. Rejected: synthetic control in v1 (a second method with few comparable neighbourhoods and about four pre-years, roughly doubling v1); a descriptive downtown chart in v1 (kept out to hold v1 to one result). `[unverifiable]`: no code exists yet. | `docs/SCOPE_research.md` §4.4, §6.4 |
| 2026-11-14 | **The 2024 zoning change and its reversal are a later study, not v1.** It is gated on a usable post-2024 roll and on the land layer, because the design compares land-heavy lots with building-heavy lots and that ratio needs a land estimate. The reversal half waits for the 2028 roll, the first one valued after the 2026-10-08 order. The Edmonton and Calgary repeal dates are checked against primary sources before any date is coded, because the sources conflict. Rejected: the upzoning half in v1 (one post point, a proxy for the ratio, and no untreated comparison group); dropping the study (it is the one case where a policy was switched on and then off). `[unverifiable]`: no code exists yet. | `docs/SCOPE_research.md` §5, §6.5 |
