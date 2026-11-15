# SPEC: profiling the raw assessment panel

Written 2026-11-15 (S3). **Status: a plan awaiting the owner's approval.** It lists what a profile run must measure so the linker plan (`src/build_panel.py`) can be written from numbers, not guesses. No profile has been run.

**Read this before** writing or running the profile, and before writing the linker plan.

The profile is a read-only script over the downloader's parquet files. It writes one report and nothing else: no panel, no links, no data contract. Suggested home: `src/profile_panel.py`, report to `output/profile/` (ignored by git). Which library reads the parquet is the coding session's choice; the box has about 16 GB of memory free, so work a year or a pair of years at a time.

## 1. Two rules the profile must keep

1. **It measures no effect.** It must not compare how treated and comparison accounts changed in value after an event. `docs/SPEC_event_study.md` §1 fixes the design before estimates are seen, and a profile that tabulated ring-by-year value changes would break that. Counts by ring are fine; value changes by ring are not.
2. **Every table reconciles.** For each pair of years, continuing plus vanished equals last year's total, and continuing plus appeared equals this year's total. Each table states its year scope and its row total. A count that does not reconcile is a failure, not a footnote.

## 2. What is already known

Measured 2026-11-14/15 through the API unless tagged. The profile reproduces the first five rows as a self-test (§5).

| Fact | Value |
|---|---|
| Rows per year | 337,298 (2012), 367,386 (2015), 425,728 (2023) |
| `account_number` is unique within a year | distinct count equals row count in 2012, 2015 and 2023 |
| `assessed_value` is never null; about 1,000 rows per year are zero | 1,058 / 992 / 988 |
| Missing coordinates | 381 (2012), 326 (2015), **9,472 (2023)** |
| 10310 102 St NW | 315 accounts in 2021–23, none in 2024–25 (S1) |
| `legal_description` has at least two shapes | `Plan: 4592AD  Block: 28  Lot: 18` for a lot; `Plan: 7821593  Unit: 8` for a condo unit |
| `suite` is filled on 66,215 / 73,672 / 83,522 rows | 2012 / 2015 / 2023 |
| `year_built` is null on about 20,000 rows a year; among residential rows with a suite, almost never | 132 / 31 / 27 |
| `zoning` is never null; the 2023 roll still uses the pre-2024 codes | top codes RF1, RA7, RSL |
| `tax_class_pct_1..3` are text, not numbers | field metadata |
| Yearly account loss 0.15–0.91%; coordinates re-centroided by a median of 58 m, up to 559 m | *[reported by `edmonton-tax-viz`, not re-measured]* |
| 2,448 accounts are in 2023 and the current roll but missing from 2025 | *[reported by `edmonton-tax-viz`]* |
| The City values houses (one to three units), highrise, lowrise and townhome condos with separate models; condo buildings also hold accessory units (parking, storage) as their own accounts | `docs/SPEC_event_study.md` §3A |

## 3. The measurements

Each block names the question, what to measure, and which linker or study decision it feeds. "By group" means by `mill_class_1` and, for residential, by the candidate property groups of block F. "In the rings" means for accounts within 800 m of the non-downtown stations of each line and for the comparison zone, as defined in `docs/SPEC_event_study.md` §4–§5 (use the 2015 coordinates and approximate station points; this is a count, not the final assignment).

### A. Is each year's file sound?
- Rows, distinct accounts, and nulls, zeros and negative values for every field, per year.
- The type and shape of `account_number` (length, leading zeros, non-digits) per year.
- Who the 9,472 accounts with no coordinates in 2023 are: by group, by neighbourhood, whether they had coordinates in 2022, and the same count for every year.
- Coordinates outside the city's bounding box.
- **Feeds:** which fields a link key can rely on; whether position can be used in every year.

### B. How long do accounts last?
- For each consecutive pair of years: continuing, vanished, appeared, by group.
- Accounts with a gap (present, absent, present again), by the years of the gap. The 2024–25 defect should appear here; anything else is new.
- Share of 2012 residential accounts present in every year to 2023, city-wide and in the rings.
- **Feeds:** how much of the panel links by account number alone; the size of the problem the other link keys must solve.

### C. Is a continuing account the same property?
For accounts present in both years of a pair, the share whose field changed, per year pair and by group:
- coordinates: the distribution of the shift in metres (percentiles, and shares over 5, 10, 25, 50, 100 m). Separate the years where most accounts moved (a re-centroiding) from single moves;
- `legal_description`, address (`house_number`, `street_name`, `suite`), `neighbourhood_name` (a rename moves every account in a neighbourhood at once; a single change is something else), `zoning`, `mill_class_1`;
- `year_built` (a rebuild), `lot_size` (over 5%), `garage`;
- the yearly log change in `assessed_value`: its distribution, and the tails beyond ±0.5, measured against the median for the same neighbourhood, group and year;
- which of these changes happen together (a new `year_built` with a value jump is a redevelopment; a new legal description with a smaller lot is a subdivision).
- **Feeds:** the flags the panel carries (rebuilt, re-zoned, lot changed), which the event study reports with and without (§4 of its spec); whether an account number can ever be reused for a different property.

### D. What happened to accounts that vanished or appeared?
For each account last seen in year t−1, look for successors among accounts first seen in year t, using three independent keys: the parsed legal description (block E), the address, and position.
- Classify each case: one-to-one (a renumbering), one-to-many (a subdivision or a condo conversion; compare the sum of successor values), many-to-one (a consolidation), no successor (a demolition, a removal, or a defect).
- Counts of each class per year, by group, and in the rings.
- **The position tolerance.** Take the one-to-one cases confirmed by legal description or address as the truth. For position-only matching at 5, 10, 25, 50 and 100 m, report how many true links it finds and how many false ones it makes.
- The share of accounts that share an exact coordinate with another account, by group. Units in one building share a point, so position cannot tell them apart.
- Twenty example account numbers per class, for reading by eye.
- **Feeds:** the order of link keys, the position tolerance, and the rule for splits and merges (the working assumption is that they are flagged and not linked, because the property changed).

### E. How good are the other keys?
- `legal_description`: share that parses into plan/block/lot, plan/unit, or neither, per year; whether the parsed key is unique within a year; whether it is stable for continuing accounts.
- Address: uniqueness within a year, stability for continuing accounts, and spelling variants of one street (one address is known to appear under three spellings).
- **Feeds:** whether legal description or address comes second in the link order.

### F. Which property group is each residential account?
The data has no property-type field. Cross-tabulate, per year, the candidates:
- legal description says `Unit` or not; `suite` filled or not; `year_built` null or not; `zoning`; `garage`; `tax_class_pct_1` under 100;
- units per condo plan and per coordinate (a tower, a low building and a row of townhomes differ in these);
- for `Unit` accounts, the value distribution and the share under $50,000 (parking and storage units), and whether a `Unit` account has its own coordinate and a house-sized lot (a bare-land condo is a house);
- residential rows with no `year_built` and no suite (vacant land, valued by the Land guide).
- Counts of each candidate class per year and in the rings.
- **Feeds:** the classification rule for the detached-like and condo groups, and whether highrise, lowrise and townhome condos can be told apart. Whatever cannot be classified is reported as its own class, not folded into another.

### G. What does `lot_size` hold?
- By group: shares under 1, 5 and 50 m²; groups of accounts at one coordinate whose values sum to about 1; year-to-year stability.
- **Feeds:** whether `lot_size` can be a matching variable or a flag for the detached-like group only.

### H. How does the current roll join the history?
- `q7d6-ambg` against `dkk9-cj3x`: whether their account numbers match one to one.
- The current roll against the 2023 and 2025 historical years: overlap by account number; for the rest, matches by legal description (from `dkk9-cj3x`) and by address.
- The same counts in the rings.
- **Feeds:** how the Valley Line SE opening point is joined, and how many ring accounts it loses.

### I. How bad are 2024 and 2025 where it matters?
- Reproduce the count of accounts in 2023 and the current roll but missing from 2025, and from 2024.
- How many are in the rings and in the comparison zones of each line.
- **Feeds:** whether 2024–25 are unusable everywhere or only where buildings are missing. The loss is building-shaped, so the rings may be nearly untouched; if so, the owner can decide whether those rolls are usable there.

### J. Where is each account?
- For accounts in or near the rings: how many would fall in a different ring depending on which year's coordinates are used.
- **Feeds:** the rule for one fixed position per linked account.

### K. Is there enough to study?
- Per line, property group and ring (and the comparison zone): accounts in 2012; accounts present every year to 2023 by account number alone; how many more each further link key would add; the share carrying each flag from block C; the number of neighbourhoods with 50 or more such accounts.
- Counts only. No values (rule 1).
- **Feeds:** gates G1 and G5 of the event-study spec, before the linker is built.

## 4. The report

One markdown file with a fixed table per block, in the order above, each headed by its question. Large tables go beside it as CSV. It ends with a list of anything the profile found that this spec did not ask about. New facts about the data also go in `data/DATA.md`, and a defect in the City's data goes in `docs/DATA_ISSUES.md`.

## 5. Self-tests

The profile stops with an error if any fails:
- each year's row count equals the downloader's manifest;
- the first five rows of the §2 table are reproduced exactly;
- every table reconciles (rule 2).

It reports, without stopping, whether it reproduces the two figures reported by `edmonton-tax-viz` (the 2,448 missing accounts and the 58 m median shift). A mismatch there is a finding for the owner.

## 6. What the linker plan will decide from it

| Linker decision | Blocks |
|---|---|
| Order of link keys: account number, then legal description or address, then position | B, D, E |
| Position tolerance, and where position is not allowed (shared coordinates) | A, D |
| Splits, merges and rebuilds: linked, or flagged and left unlinked | C, D |
| Property-group rule, and what stays unclassified | F, G |
| One fixed position per linked account | C, J |
| How the current roll attaches | H |
| Whether 2024–25 are kept out everywhere | I |
| The panel's columns, including a link-method flag and an unmatched report | all |

## 7. Not in this spec

The linking rules themselves; the panel's schema; any estimate.
