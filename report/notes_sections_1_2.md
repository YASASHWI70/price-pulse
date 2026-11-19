# Notes for report sections 1 and 2

From: Yasashwi, for Priyanka
Based on: project log, 1_data.ipynb, README data dictionary

---

## Start here: the project in plain English

**What we're asking:** when prices shot up in 2021–2023 (the "cost-of-living crisis"), did prices in Ireland rise faster than in the rest of Europe? And which kinds of spending (energy, food, housing) caused it?

**What the data is:** official price statistics. Every month, each country checks the prices of thousands of everyday things (bread, electricity, rent, petrol…) and reports how much they've changed.

**What I did (Part 1):** downloaded two big official files, threw away everything we don't need, made the two files look the same, checked they agree, and joined them into one small, clean file: `data/clean_data.csv`. **That file is what you build the chart from.**

### Key terms

| Term | What it means in simple words |
|---|---|
| **HICP** | Harmonised Index of Consumer Prices. The EU's official way of measuring price rises. "Harmonised" means every EU country measures it the same way, so countries can be compared fairly. |
| **Inflation** | How fast prices are rising. |
| **Annual rate of change** | How much prices went up compared with the **same month one year earlier**, in %. Example: 10% in June 2022 means prices were 10% higher than in June 2021. **This is the number in our `value` column.** |
| **Index (2015 = 100)** | Another way of showing prices: 2015 prices are set to 100, so 120 means 20% higher than in 2015. We did **not** use this; we used the annual rate of change. |
| **COICOP** | The official list of spending categories, like a family tree. 01 = Food and drinks, 04 = Housing and home energy, 07 = Transport, and so on. Longer codes are more specific (011 = Food, 0111 = Bread). |
| **Category** | One type of spending. We kept 4: **All items** (everything together), **Energy**, **Food**, **Housing**. |
| **Euro area** | The group of 20 EU countries that use the euro. Its number is the average for those countries. We use it as "normal for Europe". |
| **EU 27** | The 27 countries in the European Union. We show the other 26 (plus Ireland) as background lines. |
| **CSO** | Central Statistics Office, Ireland's official statistics office. Source of the Ireland data. |
| **Eurostat** | The EU's statistics office. Source of the data for the other countries. |
| **Flag** | A small letter Eurostat adds to some numbers as a note, e.g. "u" = less reliable. |
| **Row / column** | A row is one line of the table (one country, one category, one month). A column is one field (e.g. month, country, value). |
| **Missing value** | An empty cell where a number should be. Our clean file has none. |

### The clean file, explained

Each row answers: *"In this month, in this country, how much had prices of this category risen compared with a year before?"*

Example row:

| month | geo | coicop_code | category | value |
|---|---|---|---|---|
| 2022-06-01 | Ireland | ZZZ3 | Energy | 54.1 |

Read it as: **"In June 2022, energy prices in Ireland were 54.1% higher than in June 2021."**

- **month:** the month (always shown as the 1st day of the month)
- **geo:** the country, or "Euro area"
- **coicop_code:** the official category code (00 All items, 01 Food, 04 Housing, ZZZ3 Energy)
- **category:** the short category name, ready for chart labels
- **value:** the % rise vs a year earlier (negative = prices fell)

The file has 9,408 rows: 28 places × 4 categories × 84 months (Jan 2019 – Dec 2025).

### How to open it in your notebook

```python
import pandas as pd
data = pd.read_csv("data/clean_data.csv",
                   parse_dates=["month"],
                   dtype={"coicop_code": str})
```

Why the extra bits: `parse_dates` makes Python treat `month` as real dates (so the chart puts them in order), and `dtype` keeps the codes as text (otherwise "01" would turn into the number 1).

### The story so far (what the numbers show)

- **Overall, Ireland did slightly better than Europe:** peak 9.6% vs 10.6%.
- **But Energy and Housing hit Ireland harder:** Energy peaked at 54.1% vs 44.1%; Housing at 31.8% vs 23.2%.
- **Food was gentler in Ireland:** 13.2% vs 17.5%.
- **By December 2025, Ireland's prices were still rising faster:** 2.7% vs 2.0%.

The rest of this file is the detail for report sections 1 and 2.

---

## Section 1: Dataset(s)

### Dataset 1: CSO CPM15
- **Title:** EU Harmonised Index of Consumer Prices (HICP) by commodity group (monthly)
- **Publisher:** Central Statistics Office (CSO), Ireland
- **Link:** https://data.cso.ie/table/CPM15
- **Listed on:** CSO High-Value Datasets page (makes us eligible for the CSO event)
- **Downloaded:** 27 Oct 2026, full dataset, CSV, "Codes" switched on
- **Size:** 33.9 MB, 244,800 rows × 8 columns
- **Contents:** 5 statistics × 360 months (Jan 1996 – Dec 2025) × 136 commodity groups
- **Base year:** 2015 = 100
- **Missing values:** 20,611 in the VALUE column (about 8%), mostly older years and smaller categories
- **Data quality note from CSO:** some sub-indices (Unprocessed food, Energy) for Nov 2023 – Jan 2024 were corrected on 7 Mar 2024

### Dataset 2: Eurostat prc_hicp_manr
- **Title:** HICP – monthly data (annual rate of change), ECOICOP ver.1
- **Publisher:** Eurostat
- **Code / DOI:** prc_hicp_manr / 10.2908/prc_hicp_manr
- **Last updated by Eurostat:** 6 Feb 2026
- **Downloaded:** 27 Oct 2026, full dataset, SDMX-CSV 1.0, uncompressed
- **Size:** 491.6 MB, 3,533,048 rows × 10 columns
- **Contents:** 45 countries/country groups × 348 months (Jan 1997 – Dec 2025) × 467 COICOP categories
- **Missing values:** 22,008
- **Flags on values:** d = 308,971 (definition differs), u = 17,603 (low reliability), b = 1,561 (break in series), du = 819, e = 184 (estimate)

### Why both datasets stop in December 2025
From January 2026, Eurostat changed to a new classification (COICOP 2018) and a new base year (2025 = 100), published in a new dataset (prc_hicp_minr). We used the older 2015-based series because it uses the same category codes as CPM15 and ends in the same month, so the two sources match.

### Big data aspect
- **Volume:** the Eurostat file is 491.6 MB with 3,533,048 rows, about 3.4× Excel's limit of 1,048,576 rows. It cannot be opened in a spreadsheet, so it was processed in Python (pandas), loading only the 5 useful columns to save memory.
- **Variety (main claim):** two organisations with different formats that had to be made to fit together:

| | CSO CPM15 | Eurostat prc_hicp_manr |
|---|---|---|
| Date format | 201901 (number) | 2019-01 (text) |
| Categories | Codes (01, 04, ZZZ3) | Names ("Energy") |
| All-items code | "-" | Name "All-items HICP" |
| Measures | 5 statistics | 1 (annual rate of change) |
| Countries | Ireland only, no country column | 45 places, incl. 3 versions of the euro area |
| Notes on values | None | Flag column (d, u, b, e) |

### Columns
Full column-by-column table is in README.md (Data dictionary) and data_dictionary.txt.

### Permission to use
Both are official open data, free to reuse with attribution. Cite the CSO and Eurostat copyright/reuse pages in the references.

---

## Section 2: Exploration, processing, cleaning, integration

### What exploring showed
- CSO has 136 categories, including 7 special groups with codes ZZZ1–ZZZ7 (ZZZ3 = Energy).
- Since 2019, only 672 of 11,424 CSO annual-rate values are missing, so recent data is almost complete.
- Eurostat includes non-EU countries (UK, US, Türkiye…) and several country groups, so it needed filtering.
- Ireland is in both datasets, which gave us a way to check that the sources agree.

### Choosing the subset (and why)
- **Measure:** annual rate of change (%), the only measure in Eurostat, so both sources compare like with like.
- **Categories:** All items, Energy, Food, Housing (water, electricity, gas and other fuels).
  - All items = the overall baseline.
  - Ireland rose *more* than the euro area on Energy and Housing, but *less* on Food, so the chart shows a contrast.
  - Transport left out: its spike is mostly petrol and diesel, which is already in Energy.
- **Places:** Ireland, euro area (20 countries) and the other 26 EU countries.
  - Shows where Ireland ranks, not just Ireland vs one average. Ireland's peak ranks 24th of 27 for All items and 26th for Food, but 11th for Energy and Housing.
  - The 20-country euro area was used because it has the same members in every month. The other euro area series change membership over time, so they aren't a fair comparison.
- **Dates:** Jan 2019 – Dec 2025. 2019 = before the crisis, 2022 = peak, 2025 = cool-down and the last month in both files.

### Cleaning the CSO data

| Step | Why | Rows after |
|---|---|---|
| Load, with the code column as text | Keeps leading zeros (01, 04) | 244,800 |
| Keep "Annual rate of change in HICP" only | Same measure as Eurostat | 48,960 |
| Keep codes -, 01, 04, ZZZ3 | The 4 chosen categories; codes are exact, names aren't | 1,440 |
| Keep Jan 2019 – Dec 2025 | Chosen date range | 336 |

Then:
- Dropped STATISTIC, Statistic Label, UNIT (same value in every row after filtering) and Month (duplicate of TLIST(M1)).
- Renamed columns to month, coicop_code, value (C01779V04002 meant nothing).
- Changed the All-items code from "-" to "00" (the official COICOP code; a dash looks like missing data).
- Converted month from 201901 (number) to a real date.
- Added short category names and a geo column = Ireland, so the table matches Eurostat's columns.
- **Result:** 336 rows × 5 columns, 0 missing, 84 rows per category.

### Cleaning the Eurostat data

| Step | Why | Rows after |
|---|---|---|
| Load 5 of 10 columns | Other 5 were constant or empty; saves memory on a 491.6 MB file | 3,533,048 |
| Keep the 4 categories (by name) | Same categories as CSO; Eurostat uses names | 56,353 |
| Keep 27 EU countries + euro area (20) | Comparison group | 38,413 |
| Keep Jan 2019 – Dec 2025 | Same date range as CSO | 9,408 |

Then:
- **Flags:** only 8 values left were flagged "u" (low reliability): Hungary and Portugal, Food, April–June 2020 and 2021, during COVID lockdowns. Kept them (official figures, only 8 of 9,408, not Ireland). Dropped the flag column.
- Renamed columns to match the CSO table.
- Converted month from 2019-01 (text) to a real date.
- Added the same short names and COICOP codes as the CSO table; renamed the euro area to "Euro area".
- **Result:** 9,408 rows × 5 columns, 0 missing, 28 places, 2,352 rows per category.

### Integration (merging) and the check
- Matched Ireland from CSO with Ireland from Eurostat on month, country, code and category: all 336 rows matched.
- **316 of 336 values are identical. 20 differ by exactly 0.1, all in Energy.**
- Likely reason: CSO's methodology note says its rates are calculated from index numbers rounded to one decimal place. Energy is a combined group, so rounding differences show up there. A 0.1 difference is too small to see on the chart, so the sources agree.
- Combined the two: **Ireland from the CSO** (official national source) and **the other 26 countries + euro area from Eurostat**. Ireland was removed from the Eurostat table first so it isn't counted twice.
- **Final table:** 9,408 rows × 5 columns (336 from CSO + 9,072 from Eurostat), 0 missing values, 0 duplicates, 28 places.

### Data reduction at a glance
3,533,048 + 244,800 raw rows → 9,408 clean rows

### Key numbers (peak annual rate, Ireland vs euro area)
| Category | Ireland | Euro area |
|---|---|---|
| All items | 9.6% (Jun 2022) | 10.6% (Oct 2022) |
| Energy | 54.1% (Jun 2022) | 44.1% |
| Housing | 31.8% | 23.2% |
| Food | 13.2% | 17.5% |
| All items, Dec 2025 | 2.7% | 2.0% |

### Output
- `data/clean_data.csv`, 9,408 rows × 5 columns: month, geo, coicop_code, category, value.
- Must be loaded with:
  `pd.read_csv("data/clean_data.csv", parse_dates=["month"], dtype={"coicop_code": str})`
  Without dtype, codes 00/01/04 turn into 0/1/4.

### Problems met (useful for the critique)
- Eurostat uses names and CSO uses codes, so a mapping had to be written for the 4 categories.
- Three versions of the euro area in Eurostat; had to choose the fixed 20-country one.
- Leading zeros in codes are lost unless loaded as text.
- Kernel restart caused a "data is not defined" error; fixed by running the notebook top to bottom (Run All), which also confirmed it is reproducible.
- The 2015-based series ends in Dec 2025, so 2026 data could not be included.
