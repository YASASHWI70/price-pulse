API link for eustat dataset: https://ec.europa.eu/eurostat/api/dissemination/sdmx/3.0/data/dataflow/ESTAT/prc_hicp_manr/1.0?compress=true&format=csvdata&formatVersion=1.0&lang=en&labels=label_only


## Data dictionary

### CSO – CPM15 (EU HICP for Ireland by commodity group)
244,800 rows × 8 columns · 33.9 MB · Jan 1996 – Dec 2025 · Base 2015=100

| Column | Type | Meaning | Example | Kept? |
|---|---|---|---|---|
| `STATISTIC` | text | Code for the measure | `CPM15C01` | No – duplicates `Statistic Label` |
| `Statistic Label` | text | Measure in words (5 types: index, monthly change, annual change, two 12-month averages) | `EU HICP` | Yes – used to filter to annual rate of change |
| `TLIST(M1)` | integer | Month as YYYYMM | `199601` | Yes – converted to a date |
| `Month` | text | Month in words | `1996 January` | No – duplicates `TLIST(M1)` |
| `C01779V04002` | text | COICOP category code (`-` = all items) | `01`, `011` | Yes – renamed to `coicop_code` |
| `Commodity Group` | text | Category name with code in brackets | `Food (COICOP 01.1)` | Yes – used as chart labels |
| `UNIT` | text | Unit of the value | `Base 2015=100` | No – constant after filtering |
| `VALUE` | decimal | The measured value (20,611 missing) | `68.1` | Yes – the data |

### Eurostat – prc_hicp_manr (HICP monthly annual rate of change)
10 columns · 491.6 MB · 1997 – Dec 2025 · 45 geo entities · 467 COICOP categories · ECOICOP ver.1

| Column | Type | Meaning | Example | Kept? |
|---|---|---|---|---|
| `DATAFLOW` | text | Dataset name and version | `ESTAT:PRC_HICP_MANR(1.0)` | No – same in every row |
| `LAST UPDATE` | text | Last update by Eurostat | `06/02/26 23:00:00` | No – recorded here instead |
| `freq` | text | Frequency | `Monthly` | No – same in every row |
| `unit` | text | Unit of the value | `Annual rate of change` | No – same in every row |
| `coicop` | text | Category name (not a code) | `Administered prices` | Yes |
| `geo` | text | Country or country group | `Albania` | Yes |
| `TIME_PERIOD` | text | Month as YYYY-MM | `2019-12` | Yes – converted to a date |
| `OBS_VALUE` | decimal | % change vs same month a year earlier | `0.1` | Yes – the data |
| `OBS_FLAG` | text | Note on the value; mostly blank (`d` = definition differs) | `d` | Yes – checked, then dropped |
| `CONF_STATUS` | empty | Confidentiality status; completely empty | `NaN` | No – no data |

### Key terms
- **COICOP:** standard classification of consumer spending. Codes go from general to specific (`01` Food & drinks → `011` Food → `0111` Bread & cereals).
- **Index (2015=100):** price level compared with 2015. 120 = 20% higher than in 2015.
- **Annual rate of change:** % change compared with the same month one year earlier.

### Differences between the two sources
1. Date format: CSO `YYYYMM` (integer) vs Eurostat `YYYY-MM` (text)
2. Categories: CSO uses codes, Eurostat uses names
3. Measures: CSO has 5 statistics, Eurostat has only the annual rate of change


## Scope of the visualisation
- **Question:** Did prices in Ireland rise faster than in the rest of Europe, and which categories drove it?
- **Categories:** All items, Energy, Food and non-alcoholic beverages, Housing/water/electricity/gas
- **Places:** Ireland, euro area (20 countries), the other 26 EU countries
- **Period:** January 2019 – December 2025
- **Measure:** Annual rate of change (%)