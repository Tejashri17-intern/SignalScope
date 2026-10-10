# Maharashtra Mobile Network Quality (2022-2026)

## Business question
Where in Maharashtra is mobile network quality weakest, how has it changed
since 2022, and how big is the gap between major cities and the rest of
the region?

## Data
- Source: Ookla Open Data (mobile, tile-level), 2022 Q1 to 2026 Q3
- Region: approximate bounding box around Maharashtra
- Tools: Python (DuckDB, pandas), SQL, Power BI

## Method
1. Loaded all quarters directly from Ookla's public S3 bucket with DuckDB,
   filtered to the Maharashtra bounding box.
2. Cleaned the data:
   - kept tiles with at least 5 tests and 2 devices (tiles with 1-2 tests
     reflect individual phones, not the area)
   - kept download speed between 0.1 and 1000 Mbps and latency between
     1 and 2000 ms
   - rows: [RAW_ROWS] raw, [CLEAN_ROWS] after cleaning
3. Tagged tiles to four city zones (Mumbai Metro, Pune, Nagpur, Nashik)
   using approximate bounding boxes; everything else is "Other".
4. All averages are weighted by number of tests, so a tile with 500 tests
   counts more than a tile with 5.
5. Built a three-page Power BI dashboard (overview, gap, tile-level map).

## Key findings
1. **Large jump in 2022 Q4.** Mumbai Metro's average download speed went
   from about 27 Mbps (2022 Q3) to about 150 Mbps (2022 Q4). The timing is
   consistent with the 5G rollout in India, but the dataset has no 5G
   label, so this does not prove 5G as the cause.
2. **Gradual decline since 2023.** Using only tiles present in every year
   from 2022 to 2025 (same-tile check, to rule out a changing tile mix),
   Mumbai Metro fell from 181 to 145 Mbps between 2023 and 2025, and Pune
   from 177 to 136 Mbps. Possible reasons (network load, changing user
   mix) are hypotheses and are not tested here.
3. **Speed gap closed, latency gap remains.** In 2026 (Q1-Q3), major
   cities averaged 135.6 Mbps against 136.8 Mbps for the rest of the
   region, but latency was 29.3 ms against 39.4 ms.
4. **Slow zones in Pune (2026).** Among tiles with 30+ tests, the slowest
   ran at about 20-25 Mbps. One tile ([AREA_NAME]) combined 21 Mbps with
   about 140 ms latency over 173 tests, which makes it a strong candidate
   for a network review.

## Limitations
- The bounding box is a rectangle, so some tiles from neighbouring states
  fall into "Other". "Rest of region" does not mean rural Maharashtra.
- The tests >= 5 filter removes low-usage tiles, so the speed of
  low-density areas is probably overstated.
- Tests per quarter roughly doubled in 2022 Q4, so the profile of people
  running tests may have changed along with the network.
- 2026 covers only Q1 to Q3.
- City zones are approximate boxes, not official boundaries.
- The Power BI map visual was unavailable, so tiles are plotted as a
  longitude/latitude scatter chart.

## Next steps
- Tighten the upper speed limit to remove outlier tiles
- Use a separate test threshold for low-density areas
- Replace the city boxes with real boundary shapefiles
- Add operator-level data if a source becomes available

## Repository contents
- `notebook.ipynb`: data loading, cleaning and SQL queries
- `*.csv`: aggregated tables used by the dashboard
- `dashboard/`: Power BI file and screenshots
