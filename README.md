## Data cleaning log

### Stage 1: Inspection
- Raw data: 27,105 data rows (27,106 including header), 5 columns: Date, Team, Acquired, Relinquished, Notes.
- Rows with a name in Acquired: 9,542. Rows with a name in Relinquished: 17,560.
- Date range: [FILL IN: MIN to MAX]
- Teams: [FILL IN: number of unique teams, and any naming variants found]
- Date check: [FILL IN: dates stored as real dates? any gaps?]

### Stage 2: Separating injuries from returns
- Added a Row Type column in `clean_data` (raw_data left untouched), based on whether Acquired and Relinquished are filled:
  - Injury: 17,559 rows (Relinquished only)
  - Return: 9,541 rows (Acquired only)
  - Both: 1 row
  - Neither: 4 rows
  - Total: 27,105 (reconciles to raw data)
- Reconciliation: Return + Both = 9,542 (matches the Acquired count); Injury + Both = 17,560 (matches the Relinquished count).

### Decisions
- Both (1 row): John Collins, 23/03/18. Name appeared in both columns, but Notes said "returned to lineup", so it is a return. Excluded from the injury analysis. This shows the Acquired/Relinquished rule is a shortcut, and the Notes text is the real signal.
- Neither (4 rows): no player named in either column (one "placed on IL", two "activated from IL", one COVID-19 entry). Excluded because they can't be attributed to a player or an injury type.
- Total excluded at this step: 5 of 27,105 rows (about 0.02%), so the effect on results is negligible.

### Limitations noted so far
- A row is a report, not necessarily a new injury; one injury can appear in several rows (e.g. injury, then "placed on IL").
- Some Injury rows describe illness (e.g. flu), not injury. [FILL IN: how you handle this in Stage 3]