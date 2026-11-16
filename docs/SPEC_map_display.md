# SPEC: what the map shows

Written 2026-11-16 (S4). **Status: the two-layer design in §3 was approved by the owner on 2026-11-16 (`docs/DECISIONS.md`); choices 2–4 in §8 are still proposals. Nothing is built and no page has been drawn.** It settles what a reader sees for each kind of result. The build plan for the page (files, build step, cache stamping) is separate and comes when there is a result to show.

**Read this before** planning the map build, writing any output schema the map will read, or describing a result in page copy.

## 1. What is already decided, and what this adds

From `docs/DECISIONS.md`: estimation is on accounts and display is H3 hexagons at resolution 9, with neighbourhoods at low zoom; the stack is MapLibre GL JS with its own layers, PMTiles, Vite and plain JavaScript on GitHub Pages; downtown appears as observed values with no attribution. From `docs/SCOPE.md`: no dollar figure for a single parcel; macro shocks appear as one city-wide trend.

This spec adds the answer to one question those rows leave open: an estimate exists for a ring around a line's stations, not for a hexagon, so what does a hexagon show?

## 2. The problem with colouring hexagons by the estimate

A study produces few numbers: for each line and property group, one path of yearly estimates per ring (inner, outer, spillover), each with a placebo band. A resolution-9 hexagon covers about 0.1 km², so a station's rings hold dozens of hexagons. Giving each hexagon its ring's number would draw a dozen estimates as several hundred coloured cells, and a reader would take the cells for separate measurements. It is also close to the per-parcel figure that the scope rules out.

## 3. Two layers that never share a colour scale

**Layer 1, observed (hexagons).** Each hexagon shows what happened to the accounts inside it, with no model: the median change in assessed value of its linked accounts from the reference roll to the selected roll, minus the same figure for the whole city. A legend line says "Observed change compared with the city. Not an estimate of any cause." Controls: property group, and a roll slider.

- A hexagon with fewer than 10 linked accounts of the selected group shows no value and says why on hover. The threshold is a proposal.
- Downtown hexagons appear here like any other, which is what the downtown decision asks for.
- Neighbourhood polygons replace hexagons at low zoom, with the same measure.

**Layer 2, estimated (rings).** The rings are drawn as their real shapes: the union of the 0–400 m, 400–800 m and 800–1,600 m bands around a line's stations, with the comparison area outlined. A ring is one object with one state. Hexagons are not recoloured by it; the ring sits on top of them as an outline and a pattern, so the observed values stay visible underneath.

| State of a ring | When | How it is drawn | Words shown |
|---|---|---|---|
| **Effect outside the placebo band** | The headline estimate is beyond the middle 95% of placebo estimates. | Solid outline, coloured by sign, with the figure as a label. | "+2.1% in assessed value (placebo range −1.8% to +1.9%)". The example numbers are invented. |
| **Inside the placebo band** | The estimate is within the band. | Solid neutral outline, no colour. | "No effect distinguishable from chance: +0.6% (placebo range −1.8% to +1.9%)". |
| **Not estimable** | The line failed a gate (`docs/SPEC_event_study.md` §7). | Dashed outline with a hatch pattern. | "Not estimable from assessments", then the failed gate in one sentence. |
| **Not studied** | Downtown and the downtown-edge stations. | No ring. A marker on the station. | "Downtown: observed values only", with a link to the reason. |

- The three drawn states differ by line style and pattern as well as colour, so they survive greyscale and colour-blind viewing.
- The Valley Line SE opening estimate, a single flagged point from the current roll, never sets a ring's state. It appears only in the chart (§4), as a hollow marker with its caveat.
- Accounts beside the tracks, reported as their own group, are a row in the panel and not a map layer.

**Why "inside the band" gets no colour.** A pale tint would still read as "a small effect". The honest statement is that the design cannot tell this number from zero, and a neutral outline says that.

## 4. The panel: where the estimate is read

Selecting a ring opens a panel. On a phone it is a sheet over the lower half of the map.

1. **The event-study chart.** Rolls 2012–2023 across; percent change relative to the reference roll up. The ring's estimate for each roll is a point; the placebo band is a shaded strip. Vertical marks at the first affected roll of each dated event for that line, read from the events table (`docs/SPEC_events_table.md`): funding complete, construction start, opening. The 2022 and 2023 points are marked as the headline (`docs/SPEC_event_study.md` §3A).
2. **One sentence under the chart** that says how to read the ramp: an effect can only appear gradually, because each roll uses five years of sales.
3. **The area total**, with its range: the ring's estimate times the ring's reference-year value. No per-parcel figure.
4. **Counts:** accounts in the ring, in the comparison group, flagged, and excluded, so nothing is dropped out of sight.
5. **Gate results** for the line, pass or fail, each with its measured value.
6. **"In assessed value"** in the panel title, every time.

## 5. The city-wide trend

It is not a map layer. It is a small chart that is always visible beside the legend: the city-wide change in assessed value by roll, for the selected property group, captioned "City-wide change. Not attributed to any cause." Layer 1 is measured against it, so the caption also explains what "compared with the city" means on the hexagons.

## 6. Conventions this follows, and what was not checked

- **Event-study chart.** Points per period, a band, a marked reference period: the standard figure in the difference-in-differences literature the project already cites (`docs/SCOPE_research.md` §2).
- **Estimates on treatment areas, raw values on fine cells.** This is this session's design. No published map of an event-study result was examined, so whether comparable products do the same is not verified.
- **Greyed or hatched "no data" areas** are a mapping convention; using a hatch for "the method failed here" extends it and was not tested with a reader.
- **Not measured:** how the panel and the two legends fit at 390 px. This box renders text 15–20% too wide (`CLAUDE.md`, box quirks), so the fit is checked on a real phone when a page exists.

## 7. Files and tiles

Reported by the `edmonton-civic-map` session on 2026-11-16, not re-run here:

- Its vector tiles are built by hand on this box with `tippecanoe` 2.72.0 from the PyPI wheel (it ships an ARM binary), from newline-delimited GeoJSON. The `.pmtiles` files are committed, each with a manifest of source, tool versions, command and bytes per zoom. Its CI only builds and deploys the site.
- It uses PMTiles only for its 384,000-polygon building layer (28 MB). Its boundary layers ship as plain GeoJSON, 62–220 kB each, simplified so shared edges stay shared.
- GitHub Pages serves byte ranges correctly to browsers, but a `curl --compressed` check makes a PMTiles file look broken.
- It has never tiled a hexagon grid and has no measurement of gaps at tile edges.

What follows for this project, as a proposal:

- **Hexagons: measure before choosing.** Edmonton is about 780 km², so the grid is at most about 7,400 hexagons, each with 24 values (12 rolls, two groups). Write it as GeoJSON, gzip it, and ship GeoJSON if it is under about 1 MB; build PMTiles the civic-map way only above that. The 1 MB line and the hexagon count are estimates.
- **Rings, comparison areas, stations and tracks:** GeoJSON. They are a few dozen shapes.
- **Results** (estimates, bands, gate results, counts): one JSON file per line, read by the panel. Its schema is set in the event-study module's plan, which must name this spec's four states.
- **The chart:** drawn as inline SVG by a short function. It is twelve points and a band, and a chart library would be the largest script on the page. What the sibling sites use was not checked.

## 8. The owner's decisions

1. **Rings over observed hexagons** (§3). Decided 2026-11-16. Turned down: hexagons coloured by their ring's estimate.
2. **Inside the band is drawn neutral** (recommended), or tinted by its sign.
3. **A hexagon needs 10 linked accounts to show a value** (recommended), or another number.
4. **Inline SVG chart** (recommended), or a chart library.
