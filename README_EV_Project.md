# Electric Vehicle Population Analysis (SQL Server + Power BI)

> **285,822 EV records analyzed end to end:** cleaned and queried in SQL Server, then turned into an interactive 4-page Power BI report with DAX measures.

**Headline findings**

| Question | Answer |
|---|---|
| How many EVs are in the dataset? | **285,822** vehicles across **255 counties** |
| Who leads the market? | **Tesla: 117,392 vehicles (41.1%)**, about 1.8x the next four brands combined |
| How concentrated is the market? | The top 5 brands (Tesla, Chevrolet, Nissan, Ford, Kia) hold **64.1%** of all vehicles |
| Battery-electric or plug-in hybrid? | **BEV 229,876 (80.4%)** vs **PHEV 55,946 (19.6%)** |
| Which city has the most EVs? | **Seattle** |

---

## Project overview

An academic project that explores the public *Electric Vehicle Population Data* dataset to answer basic market questions: how big is the EV fleet, which brands and models lead, how do vehicle types and ranges compare, and where are the vehicles located.

**Author:** Omar Abdallatif Mohamed (data gathering, SQL Server work, cleaning, DAX measures, and Power BI dashboard)

**Tools:** SQL Server (T-SQL), Power BI (Power Query, DAX), Data.gov dataset

## Workflow

1. Brainstorming and defining the questions
2. Gathering the data from Data.gov
3. Storing the dataset in SQL Server
4. Exploring the data with SQL
5. Cleaning and transforming the data
6. Loading the SQL data into Power BI
7. Building DAX measures
8. Designing the report pages

## Data source

- **Dataset:** Electric Vehicle Population Data
- **Publisher:** Data.gov
- **Size:** 285,822 rows

## Data cleaning

| Problem | Fix |
|---|---|
| Missing values in `City`, `Make`, `Model_Year` | Detected with `WHERE ... IS NULL`, filled with `'Unknown'` using `UPDATE` + `ISNULL` so visuals keep every record |
| Inconsistent text (capitalization and spaces in `Make`) | Standardized with `UPPER(TRIM(Make))` in SQL and *Capitalize Each Word* in Power Query |
| Coordinates stored as one text field | Split into **Latitude** and **Longitude** so the map visual works |
| Unused columns slowing the report | Removed to reduce file size and refresh time |
| Wrong data types | Set correct types for dates, numbers, and text |

## SQL analysis

The full script is in [`sql/queries.sql`](sql/queries.sql).

| Analysis | Technique |
|---|---|
| Total volume of data | `COUNT(*)` |
| Top 5 brands | `GROUP BY`, `ORDER BY ... DESC`, `TOP 5` |
| Unique brands | `DISTINCT` |
| Average range per model year (technology trend) | `AVG()`, `GROUP BY` |
| Brand deep dive (Tesla) | `WHERE` |
| High-range cars (> 300 miles) and short-range cars (0 to 50 miles) | `WHERE`, `AND` |
| BEV vs PHEV split | `GROUP BY` |
| Brands starting with a letter | `LIKE 'B%'` |
| Missing-data detection and repair | `IS NULL`, `UPDATE`, `ISNULL` |

**Query results**

![Total vehicles](images/sql-total-vehicles.png)
![Top 5 brands](images/sql-top-5-brands.png)
![BEV vs PHEV](images/sql-bev-vs-phev.png)

## Power BI report

The report has four navigable pages:

| Page | Content |
|---|---|
| **Home** | KPI cards: Total Vehicles (286K), Total Counties (255), Top City (Seattle), Avg Range (38.15), Tesla Count (117K) |
| **EV statistics** | Average range by model, model ranking, state slicer |
| **Tesla** | Tesla count by model with state and city slicers |
| **Map** | Geographic distribution of vehicles with state and city slicers |

**DAX measures:** Total Vehicles, Total Counties, Top City, Avg Range, Highest Range, Tesla Count, plus "most popular" and "most range" model lookups. All values update instantly when a filter is applied.

![Dashboard home page](images/dashboard-home.png)
![Average range by model](images/dashboard-avg-range-by-model.png)

> The `.pbix` file is not included in this repository. Screenshots above come from the project presentation.

## Key insights

1. **Tesla dominates:** 117,392 vehicles, 41.1% of the fleet.
2. **The market is concentrated:** five brands account for 64.1% of vehicles.
3. **Battery-electric is the norm:** four out of five vehicles are BEVs.
4. **Range varies widely by model:** the top model averages 230 miles, while most of the models shown on the EV statistics page average under 110.

## Limitations and next steps

- **Average range (38.15) is understated.** Many records, especially recent model years, show an `Electric_Range` of 0 (for example, model years 2024 to 2026 average 0 to 7), which looks like unreported data rather than true zero range. Next step: exclude zeros from range averages and report how many were excluded.
- **Model popularity measure.** Replace the current model ranking with a `COUNT` of vehicles by model, which is the correct popularity metric.
- **Possible extensions:** adoption trend by model year, county-level market share, Python (Pandas) validation of the SQL results.

## How to reproduce

1. Download *Electric Vehicle Population Data* from Data.gov.
2. Import the CSV into SQL Server as `[dbo].[ele vech]`.
3. Run [`sql/queries.sql`](sql/queries.sql).
4. Connect Power BI to the SQL Server table and rebuild the measures listed above.
