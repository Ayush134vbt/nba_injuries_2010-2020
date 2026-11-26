## Data cleaning log

### Stage 1: Inspection
- Raw data: 27,105 data rows (27,106 including header), 5 columns: Date, Team, Acquired, Relinquished, Notes.
- Rows with a name in Acquired: 9,542. Rows with a name in Relinquished: 17,560. Their sum (27,102) is 3 short of the row count; explained in Stage 2.
- Date range: 3 Oct 2010 to 6 Oct 2020. Dates are stored as real Excel dates (27,105 rows have a date, matching the row count) and display in d/m/yy format.
- Teams: 32 unique values in the raw Team column (30 current franchises plus the old name "Bobcats" and one stray "Bullets" row). After standardisation, `Team Clean` has 30 (see below).
- Missing values: Date has no blanks. Team had 2 blanks (see Decisions).

### Stage 2: Separating injuries from returns
- Added a Row Type column in `clean_data` (`raw_data` left untouched), based on whether Acquired and Relinquished are filled:
  - Injury: 17,559 rows (Relinquished only)
  - Return: 9,541 rows (Acquired only)
  - Both: 1 row
  - Neither: 4 rows
  - Total: 27,105 (reconciles to the raw data)
- Reconciliation: Return + Both = 9,542 (matches the Acquired count); Injury + Both = 17,560 (matches the Relinquished count); Neither - Both = 3, which accounts for the gap in Stage 1.

### Decisions
- Both (1 row): John Collins, 23/03/18. Name appeared in both columns, but Notes said "returned to lineup", so it is a return. Excluded from the injury analysis. This shows the Acquired/Relinquished rule is a shortcut, and the Notes text is the real signal.
- Neither (4 rows): no player named in either column (one "placed on IL", two "activated from IL", one COVID-19 entry). Excluded because they can't be attributed to a player or an injury type.
- Total excluded at this step: 5 of 27,105 rows (about 0.02%), so the effect on results is negligible.
- Blank Team (2 injury rows): rows 13009 (2/09/15, Glen Davis) and 13011 (3/09/15, Landry Fields). Team was [FILL IN: filled from ESPN team history as of that date, or set to "Unknown"]. Edited in `clean_data` only.

### Team name standardisation
- Team names changed during 2010-2020 (New Orleans Hornets → Pelicans in 2013; Charlotte Bobcats → Hornets in 2014), so the same franchise appears under different names. The Team column is kept as recorded; a new column `Team Clean` holds standardised names, using today's franchise names.
- Rule applied in `Team Clean`:
  - Bobcats → Hornets (the Charlotte franchise).
  - Hornets dated before 1 Jul 2013 → Pelicans (the New Orleans franchise). Hornets rows dated on or after that stay as Hornets (Charlotte).
  - All other teams unchanged.
- The "Hornets" label in the raw data covered two franchises: 259 rows before the cutoff (New Orleans) and 460 on or after it (Charlotte).
- Verification: `Team Clean` has 30 unique values.
  - Hornets: 829 rows = 369 (Bobcats) + 460 (Hornets on or after 1 Jul 2013). ✓
  - Pelicans: 835 rows = 576 (Pelicans) + 259 (Hornets before 1 Jul 2013). ✓
- Unexpected team name: "Bullets" (1 row, [FILL IN: date]). [FILL IN: treated as Wizards / excluded / left as is, and why]

### Limitations
- A row is a report, not necessarily a new injury; one injury can appear in several rows (e.g. injury, then "placed on IL").
- Some Injury rows describe illness (e.g. flu), not injury. [FILL IN: how you handle this in Stage 3]
- Team is not used to define a unique injury event, because of the blank values.
- The cutoff date for the Hornets/Pelicans split is based on the franchise rename dates, not on a field in the data, so rows near the cutoff could be misassigned.
- Unexpected team name: "Bullets" (1 row, 5/11/17). The Washington Bullets became the Wizards in 1997, so this is a data-entry error. Changed to "Wizards" in `clean_data` (`raw_data` untouched). [Basis: Notes / player / assumption from the name]