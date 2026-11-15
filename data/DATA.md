# Data Sources

Reference for raw input files. Update this file when you discover column name
quirks, encoding issues, or anything unexpected. Do not rely on memory — write it
down here. A defect in the *publisher's* data goes in `docs/DATA_ISSUES.md`,
with a status saying whether they have been told.

**Download completeness:** an API that pages or caps (`$limit` on Socrata, for
instance) truncates *silently* — it returns exactly that many rows with no
error. Every downloader verifies its row count against the live server count
and fails hard on a mismatch.

**Vintage:** record, per source, the dataset id, the retrieval timestamp and
the publisher's own last-updated stamp. A guard must measure the DATA (row
counts, max date in the file), never a metadata string that can go stale
while the guard stays green.

## Sources

Nothing is downloaded yet (2026-11-14). The entries below come from Socrata metadata and API queries made while scoping. Fill in **Retrieved** when the first downloader runs. Background: `docs/SCOPE_research.md` §3.

### Property Assessment Data (Historical)
- **Publisher / URL:** City of Edmonton, https://data.edmonton.ca/d/qi6a-xuwt
- **Dataset id:** `qi6a-xuwt`
- **Retrieved:** not yet. **Publisher last-updated:** 2026-01-12 (catalogue stamp)
- **Rows / columns:** 2012–2025, 337,298 (2012) to 431,706 (2025) rows per year (a `group by assessment_year` query, 2026-11-14). 21 fields: `account_number, assessment_year, suite, house_number, street_name, legal_description, latitude, longitude, point_location, neighbourhood_name, year_built, garage, zoning, lot_size, assessed_value, mill_class_1..3, tax_class_pct_1..3`
- **CRS (if spatial):** WGS84 lat/lon points (one point per account)
- **Quirks:**
  - `assessed_value` is the **total**; there is no land/improvement split.
  - 🛑 **2024 and 2025 are incomplete:** whole buildings are missing. See `docs/DATA_ISSUES.md`.
  - API: the default page size is 1,000 and the server caps each request at 50k rows, so page and verify against the server count.
  - Identifiers churn (accounts, neighbourhood names, coordinates), and `lot_size` sometimes holds ownership shares instead of m². These are reported by `edmonton-tax-viz` (its `data/DATA.md` and `docs/DATA_ISSUES.md` §3) and not yet re-measured here.
  - Assessment year Y values the market at July 1 of Y−1 (City methodology).
  - **Measured 2026-11-15 by API count queries** (2012, 2015 and 2023 only):
    - `account_number` (text) is unique within a year.
    - `assessed_value` is never null; about 1,000 rows a year are zero.
    - Missing coordinates: 381 (2012), 326 (2015), 9,472 (2023). The jump is unexplained.
    - `legal_description` has at least two shapes: `Plan: … Block: … Lot: …` for a lot and `Plan: … Unit: …` for a condo unit.
    - `zoning` is never null. `year_built` is null on about 20,000 rows a year. `tax_class_pct_1..3` are text.
    - What the full profile must measure is in `docs/SPEC_panel_profile.md`.

### Property Assessment Data (Current Calendar Year)
- **Dataset id:** `q7d6-ambg`. 439,825 rows on 2026-11-14. Has `ward` and `tax_class`. No `year_built`, `zoning` or `lot_size`: those are in `dkk9-cj3x`. Account numbers can differ from `qi6a-xuwt` for the same parcel (reported by `edmonton-tax-viz`).

### Property Information (Current Calendar Year)
- **Dataset id:** `dkk9-cj3x`. Fields include `zoning, lot_size, total_gross_area, year_built, neighbourhood_id, ward`, lat/lon. Current year only. This is the only open floor-area source, needed for a residual land estimate.

### Capital Projects
- **Dataset ids:** `552h-hjwj` (attributes), `xw7u-7mr6` (points), `nmn5-5yzc` (lines). Fields: `asset_type, project_name, current_phase, approved_budget_m, forecasted_budget, approved_profile_schedule, forecasted_completion_date, ward, neighbourhood`, lat/lon. Despite its name, `approved_budget_m` holds dollars (e.g. 5923095). **Not checked:** whether completed projects stay in the dataset.

### LRT
- **Dataset ids:** stations `fhxi-cnhe`, routes `rpjw-4jft`, network `p4jk-ys2y`, tracks `8r95-rjy4`. Geometry only, with no opening dates. Dates go in a hand-kept event table.
- **`fhxi-cnhe` is platform-level and has no NAIT entry** (measured 2026-11-14): 54 rows, two per station for most, 28 distinct names. The Metro Line's NAIT terminus is absent under any name, so its position must come from the track geometry or a hand-kept point. First treated-account counts by line are in `docs/SPEC_event_study.md` §4.

### Permits
- **Dataset ids:** Development Permits `2ccn-pwtu` (2019+ variant `q4gd-6q9r`); General Building Permits `24uj-dj8v` (has `construction_value, floor_area, units_added`, lat/lon).

### Land parcels
- Only point datasets are open (`dm3i-bp8w` assessment parcels, `774r-kpz5` legal, `9tyx-zfd4` title, `78hg-w763` holding). No open parcel polygons.

### Assessment methodology (documents, not data)
- `https://www.edmonton.ca/sites/default/files/public-files/2026_RES_Land.pdf` and `2026_RES_Improved.pdf`, read 2026-11-14.
  - Valuation date is July 1 of the prior year.
  - The land model uses 8 market areas plus study areas, with sales from 2020-07-01 to 2025-06-30, time-adjusted.
  - **More guides, read 2026-11-15** (same folder): `2026_RES_HighriseCondo.pdf`, `2026_RES_LowriseCondo.pdf`, `2026_RES_TownhomeCondo.pdf`, `2026_RES_Cost.pdf`, `2025_RES_Improved.pdf`, `2023_RES_Land.pdf`, and the study-area maps `2026_StudyAreas_Neighbourhood.pdf` (20 MB). The index page is `https://www.edmonton.ca/residential_neighbourhoods/property_tax_assessment/reference-materials`. The Improved guides for 2016–2024 returned 404 under the same naming.
  - Residential Improved covers properties with one to three dwelling units. Its location variables are market area (nine, one model each), neighbourhood and study area. No distance-to-station variable exists in any guide read.
  - Every guide read uses a five-year sales window ending 30 June before the valuation date. Time adjustment factors are per market area, with a River Valley submarket.
  - The condo guides have no LRT or transit term. What this means for the studies is in `docs/SPEC_event_study.md` §3A.
  - Market-area boundaries are map images in the guide appendix. No open dataset of them was found.
  - Site influences are applied by rule. Positive: golf course, greenbelt, lake, noise barrier, park, ravine, river valley. Negative: includes **LRT influence** (abutting or across from an LRT right of way). See `docs/SCOPE_research.md` §4.
