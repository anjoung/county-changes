# Historical county changes, 1790–1990: the data

Richard L. Forstall's Population of States and Counties of the United States: 1790 to 1990 (U.S. Bureau of the Census, 1996) is an important research resource for anyone who works in economic history. Counties have changed over the course of history. New counties have emerged and some have been abolished. Forstall compiled every major change the Census Bureau recorded: counties formed, abolished, merged, split, or renamed. The trouble is that it is a book, and a tedious one, so researchers who use it usually code up the years they need and stop. I had an LLM read the whole thing and turn it into one machine-readable file, free to use under CC BY 4.0. As a check on accuracy, I compared it with the county changes that Martha Bailey and Andrew Goodman-Bacon coded by hand for their 2015 paper. For every one of their changes that comes from this book, this file reproduces their coding exactly.

License: CC BY 4.0 (https://creativecommons.org/licenses/by/4.0/). Cite as: Joung, Andrew (2026). Historical county changes, 1790–1990, derived from Forstall (1996).

Files: `county_changes.csv` reports every county-level event: the county and its FIPS code, what happened, the other county involved and its role, the census interval, the year where the book prints one, the population moved, and the book's page and words. One row per event. `counties.csv` reports every county: the censuses it appears in, its Notes cell, and its Part IV dates and FIPS code. One row per county. Each also as JSON with the same columns; `../datapackage.json` describes both with types. Every event row names the scan page and quotes the book, and the page `../county/{slug}.html` shows it in context with a link to the scan.

## county_changes.csv

| Column | Type | Meaning |
|---|---|---|
| `event_id` | string | The item in the read: p{scan page}-t{table}-r{row}-c{claim} for a Notes-column entry, p{page}-f{footnote}-c{claim} for a footnote. |
| `state` | string | State name, upper case. |
| `unit` | string | county, parish, borough, city or subdivision. |
| `county` | string | The unit's name as the book prints it in its table row, e.g. "Nuckolls (old)". |
| `county_fips` | string | FIPS code from the book's Part IV. A county abolished before 1930 carries the book's own code, the preceding county's code plus a decimal (38011.1), explained at the foot of every state page; blank where Part IV has no line. |
| `event_type` | string | birth, abolition, rename, boundary_change, reported_with or other. |
| `partner_county` | string | The other unit named in the event, as resolved to a table row where possible. |
| `partner_unit` | string | The partner's unit. |
| `partner_state` | string | The partner's state (a few partners are across a state line). |
| `partner_fips` | string | The partner's FIPS code, as for county_fips. |
| `partner_role` | string | origin, minor_origin (the book's semicolon: a very small contribution), destination, minor_destination, other_name, reporting_unit or counterpart; blank with no partner. |
| `census_before` | integer | The census before the event, in the state's own sequence (Oklahoma has 1907, Alaska 1929 and 1939). Equal to census_after when the event is dated to a census; blank when undated. |
| `census_after` | integer | The census after the event. |
| `event_year` | integer | The year, where the book prints one. |
| `when` | string | The book's dating phrase as printed: "after 1980", "1840-50", "December 7, 1990". |
| `population_moved` | integer | The population the book attaches to the event, where it gives one. |
| `pdf_page` | integer | The page of the scan. |
| `printed_page` | integer | The book's printed page. |
| `location` | string | "Notes column", "Note 5", or "Note C31" (a Virginia independent-city note). |
| `quote` | string | The book's words for this event, verbatim. |
| `confident` | boolean | Whether the reader was sure of the item; every doubt was then settled by the author's rulings, so all true. |
| `reason_code` | string | The kind of doubt the reader had, kept for the record: print, syntax, typing or placement. |
| `reason` | string | The reader's doubt in words. |
| `ruled` | string | The author's ruling that settled the doubt, quoting it. |
| `corrected` | boolean | Whether a hand correction was applied (none were). |
| `checked` | string | The date of the ruling. |

## counties.csv

| Column | Type | Meaning |
|---|---|---|
| `state` | string | State name, upper case. |
| `unit` | string | county, parish, borough, city or subdivision. |
| `name` | string | The unit's name as the book prints it. |
| `present` | string | The censuses at which the book gives the unit a population, space-separated. |
| `see_notes` | string | The note numbers the unit's Notes cell points to. |
| `pages` | string | The scan pages of the unit's table rows. |
| `sections` | string | The table section the unit sits under, where the state's table has sections (e.g. Inhabited Islands). |
| `n_rows` | integer | The number of table rows read for the unit (one per census table it appears in). |
| `notes` | string | The Notes cell, verbatim. |
| `first_census` | number | The first census in present. |
| `last_census` | number | The last census in present. |
| `gaps` | string | Censuses between first and last at which the unit has no population row. |
| `unit_key` | string | main, or city for Virginia's independent cities under their own Part IV heading. |
| `p4_first_census` | number | Part IV's first census for the unit. |
| `no_change_since` | number | Part IV's census since which there has been no significant change. |
| `fips` | string | Part IV's FIPS code, as for county_fips above (decimal codes for counties abolished before 1930). |
| `part4_id` | string | The Part IV line the unit was matched to. |

## How it was made, and what to watch for

The book was read page by page from the scan by claude-opus-5-5 through the Claude Agent SDK, one call per page; every item the reader doubted was flagged, and the author ruled on each kind of doubt. The assembly, the derived list of splits and merges and the checks against the book's own county counts are plain Python on the per-page reads. Events are dated to a census interval unless the book prints a year; this is census reporting, not legal history. About 55 names in the notes resolved to no table row. The book's Part IV skips ten current counties at page breaks, so they have no FIPS here. Alaska and Virginia follow the book's own conventions. The file ends where the book does, at the 1990 census. The Census Bureau's list of county changes after 1990 is used inside the project for its own county units and is not part of this file. The check against Bailey and Goodman-Bacon (2015) is at `../bgb.html`.
