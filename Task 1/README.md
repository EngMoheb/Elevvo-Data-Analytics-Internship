# Sales Performance Dashboard (Excel)

**Superstore (Kaggle) → Clean, analyze, and deliver an interactive Excel dashboard**

---

## Story (quick)

Imagine you're handed thousands of messy sales transactions and asked to turn them into a single, reliable story executives can act on. That's the mission we completed: clean the Superstore data, enrich it with helper fields, analyze revenue and volume trends, and deliver an Excel dashboard that answers the core questions:

> *Where are we making money? When do sales spike? How fast is revenue moving month-to-month and year-to-year?*

---

## Table of Contents

- [Problem & Dataset Story](#problem--dataset-story)  
- [Data Cleaning Journey](#data-cleaning-journey)  
- [Helper Columns — why we added them](#helper-columns--why-we-added-them)  
- [Exploratory Data Analysis (EDA)](#exploratory-data-analysis-eda)  
- [Dashboard Creation](#dashboard-creation)  
- [Key Business Insights](#key-business-insights)  
- [Project Files & Links](#project-files--links)  
- [Reproduce (quick steps)](#reproduce-quick-steps)  
- [Lessons Learned](#lessons-learned)  
- [Contact](#contact)

---

## Problem & Dataset Story

**Task brief:**  
[![Task brief](Task%201/Task%20Details.png)](Task%201/Task%20Details.png)

**Business question:** Summarize sales performance and identify short-term (MoM) and long-term (YoY) trends to help plan promotions and manage inventory.

**Dataset:** [Superstore Sales dataset](https://www.kaggle.com/datasets/rohitsahoo/sales-forecasting) from Kaggle

[![Raw data preview](Task%201/RawData.png)](Task%201/RawData.png)

**Raw file used:** [RawData.xlsx](Task%201/RawData.xlsx)

The raw data captured orders across regions, categories and years. It required cleaning and enrichment before we could rely on trends and growth metrics.

---

## Data Cleaning Journey

> Clean data = trustworthy analysis. Cleaning was the foundation, not an afterthought.

**Preview (cleaned snapshot):**  
[![Cleaned data snapshot](Task%201/DataCleaned.png)](Task%201/DataCleaned.png)

**Cleaned dataset saved as:** [CleanedData.xlsx](Task%201/CleanedData.xlsx)

### Transformation steps (strict & to the point)

1. **Ingest**  
   - Imported `Task 1/RawData.xlsx` into Excel as an official **Table** named `tblSales`.

2. **Type & value validation**  
   - Formatted `Sales` as *Currency* and flagged negative/zero entries.  
   - Ensured `Order Date` and `Ship Date` are true Excel dates (converted text→date where needed).

3. **Text normalization**  
   - Applied `=PROPER(TRIM(...))` to Region, Category, Product and Customer names to standardize labels.

4. **ID & postal checks**  
   - Implemented simple formula checks to validate formats of `Order ID`, `Customer ID`, `Product ID`.  
   - Replaced blank postal codes with `Unknown`.

5. **Missing / anomalous values**  
   - Flagged critical gaps and reviewed them; corrected or excluded rows as appropriate.

6. **Save**  
   - Final cleaned table stored as [CleanedData.xlsx](Task%201/CleanedData.xlsx).

---

## Helper Columns — why we added them

We added helper columns to enhance our EDA capabilities and ensure reliable pivot table operations. These columns transform the raw data into analysis-ready format and enable sophisticated time-based calculations that would otherwise be complex or error-prone.

### Why these helper columns are essential for our EDA:

- **Units Sold** — Sets each transaction line to `1` for reliable item counting in pivot tables, enabling accurate volume analysis alongside revenue trends.

- **OrderDate_Real** — Converts all date entries to proper Excel date serials, ensuring date functions (YEAR, MONTH, TEXT) work correctly for time-based analysis.

- **Order Year** — Extracts integer years (2015, 2016, etc.) for quick year-over-year comparisons and annual trend analysis in pivot tables.

- **Order Month** — Creates readable month labels ("Jan", "Feb", etc.) for seasonal analysis and dashboard display.

- **Month Num** — Provides numeric months (1–12) to enable proper chronological sorting in pivot tables (preventing alphabetical month ordering).

- **Year–Month** — Combines year and month into a single timeline code (`yyyy-mm`) for seamless time series analysis and month-over-month calculations.

These helper columns are the foundation that makes our EDA robust, repeatable, and scalable for new data or different analysis requirements.

---

## Exploratory Data Analysis (EDA)

**EDA workbook:** [EDA.xlsx](Task%201/EDA.xlsx) — sheets: `pt_Trend`, `pt_MoM`, `pt_YoY`, `Dashboard_Excel`.

**Monthly revenue preview:**  
[![Monthly Revenue Trend](Task%201/MonthlyRevenueTrend.png)](Task%201/MonthlyRevenueTrend.png)

### What we ran & the insights we found

1. **Monthly time series**  
   - **What we did:** Pivot with `Year–Month` rows and `Sum(Sales)` + `Sum(Units Sold)` values.  
   - **Insight:** Clear seasonality — strong Q4 peaks and consistent Q1 troughs. Revenue grows toward 2018.

2. **Month-over-Month (MoM)**  
   - **What we did:** `Show Values As → % Difference From (previous)` on `Year–Month`.  
   - **Insight:** Average MoM ≈ **+44.1%**, but volatility is significant; narrow months with low bases (e.g., Feb 2015) produce extreme % swings (Mar 2015 ≈ **+1,121.4%**).

3. **Year-over-Year (YoY)**  
   - **What we did:** Pivot by `Order Year` and `% Difference From (previous)`.  
   - **Insight:** Recovery after 2016 dip and healthy YoY growth in 2017–2018 (2017 ≈ +30.6%, 2018 ≈ +20.3%).

4. **Regional performance**  
   - **What we did:** Pivot by Region → `Sum(Sales)` (connected to slicers).  
   - **Insight:** A few regions account for most revenue; seasonality patterns vary by region — useful for targeted campaigns.

5. **Category & product analysis**  
   - **What we did:** Category × Year–Month pivots and Top N product rankings.  
   - **Insight:** Specific sub-categories (e.g., Furniture, Technology) drive peak season sales—ideal for promotional planning.

6. **Anomalies and data quality checks**  
   - **What we did:** Flagged months with extreme MoM values for investigation.  
   - **Insight:** Large anomalies likely correspond to promotions, data issues, or batch returns; these require operational follow-up.

> For hands-on exploration open the interactive pivots in [EDA.xlsx](Task%201/EDA.xlsx).

---

## Dashboard Creation

**Final dashboard (screenshot):**  
[![Final Dashboard](Task%201/Final_Dashboard.png)](Task%201/Final_Dashboard.png)

Open [EDA.xlsx](Task%201/EDA.xlsx) → sheet `Dashboard_Excel` to interact with the live dashboard.

### Dashboard components

- **Summary pivot:** Total Revenue & Units Sold by `Year–Month`.  
- **Revenue Over Time:** Line chart (time series).  
- **Units Sold Over Time:** Line chart (secondary axis available).  
- **MoM KPI cards:** Avg / Max / Min MoM% (cells linked to the MoM pivot range).  
- **YoY pivot:** Year totals with YoY% column for strategic view.  
- **Slicers:** Region and Category, connected across pivots for synchronized filtering.  
- **Annotations:** short text blocks calling out key business findings.

**Design notes:** emphasis on clarity — consistent fonts, color-coded status (green/red), and dynamic KPI cards that respond to filters.

---

## Key Business Insights

- **Peak Revenue:** **Nov 2018** — **$118,000** (+20.3% YoY).  
- **Lowest Revenue:** **Feb 2015** — **$4,500** (−75.1% MoM).  
- **Average MoM Growth:** **+44.1%** — high, but noisy.  
- **Biggest MoM Spike:** **+1,121.4%** (Mar 2015) — flagged for review.  
- **Seasonality:** Q4 strongest; Q1 weakest — plan inventory & promotions accordingly.  
- **Units drive revenue:** Volume trends align with revenue; focus promotions on high-margin categories.

**Top recommendation:** Prepare inventory for Q4 and run targeted promotions in Q1 to reduce the trough.

---

## Project Files & Links

All Task 1 files are in the Task 1 folder:

- **Raw data:** [RawData.xlsx](Task%201/RawData.xlsx) — original dataset.  
- **Raw preview:** [RawData.png](Task%201/RawData.png) — snapshot.  
- **Cleaned data:** [CleanedData.xlsx](Task%201/CleanedData.xlsx) — cleaned table `tblSales`.  
- **Cleaned snapshot:** [DataCleaned.png](Task%201/DataCleaned.png).  
- **EDA & Dashboard workbook:** [EDA.xlsx](Task%201/EDA.xlsx).  
- **Final dashboard image:** [Final_Dashboard.png](Task%201/Final_Dashboard.png).  
- **Task brief image:** [Task Details.png](Task%201/Task%20Details.png).  
- **Kaggle source:** [Superstore Sales dataset](https://www.kaggle.com/datasets/rohitsahoo/sales-forecasting)

> Ensure filenames are present in the repo with exactly these names (case-sensitive on some systems).

---

## Reproduce (quick steps)

1. Open [RawData.xlsx](Task%201/RawData.xlsx) and convert the data to an Excel Table named **`tblSales`**.  
2. Apply the cleaning steps (normalize text, convert dates) and create helper columns described above. Save as [CleanedData.xlsx](Task%201/CleanedData.xlsx).  
3. Open [EDA.xlsx](Task%201/EDA.xlsx). If pivots are disconnected, update their data source to `Task 1/CleanedData.xlsx` (PivotTable Tools → Change Data Source).  
4. Refresh All (Data → Refresh All). Validate slicer behavior and KPI card updates.  
5. Re-export dashboard image if needed: `Task 1/Final_Dashboard.png`.

---

## Lessons Learned

- **Data quality is essential.** Fixing dates and IDs up front prevents many downstream issues.  
- **Excel is a powerful BI tool.** Tables + helper columns + pivot + slicers rapidly produce interactive dashboards.  
- **Use MoM & YoY together.** MoM for momentum; YoY for seasonally-adjusted growth.  
- **Keep dashboards simple.** Focused KPIs and annotated charts communicate fastest.

---

## Contact

**Lead Analyst:** Eng_Ahmed_Moheb  
**LinkedIn:** [Ahmed Moheb](https://www.linkedin.com/in/ahmed-moheb-09b37135a/)
