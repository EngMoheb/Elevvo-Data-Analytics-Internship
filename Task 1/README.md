# Task 1 — Sales Performance Dashboard (Excel)

**Superstore (Kaggle) → Clean, analyze, and deliver an interactive Excel dashboard**

---

## Story (quick)

Imagine you’re handed thousands of messy sales transactions and asked to turn them into a single, reliable story executives can act on. That’s the mission we completed: clean the Superstore data, enrich it with helper fields, analyze revenue and volume trends, and deliver an Excel dashboard that answers the core questions:

> *Where are we making money? When do sales spike? How fast is revenue moving month-to-month and year-to-year?*

---

## Table of Contents

- [Problem & Dataset Story](#problem--dataset-story)  
- [Data Cleaning Journey](#data-cleaning-journey)  
- [Helper Columns (formulas)](#helper-columns-formulas)  
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
<a href="Task%201/Task%20Details.png"><img src="Task%201/Task%20Details.png" alt="Task brief" width="360" /></a>

**Business question:** Summarize sales performance and identify short-term (MoM) and long-term (YoY) trends to help plan promotions and inventory.

**Dataset:** Superstore Sales dataset (Kaggle) — [Kaggle: Sales Forecasting](https://www.kaggle.com/datasets/rohitsahoo/sales-forecasting)

**Raw file used:** `[Task 1/RawData.xlsx](Task%201/RawData.xlsx)`

The raw data captured orders across regions, categories and years. It required cleaning and enrichment before we could trust trends and growth metrics.

---

## Data Cleaning Journey

> Clean data = trustworthy analysis. We treated cleaning as the foundation, not an afterthought.

**Preview (cleaned snapshot):**  
<a href="Task%201/DataCleaned.png"><img src="Task%201/DataCleaned.png" alt="Cleaned data snapshot" width="520" /></a>

**Cleaned dataset saved as:** `[Task 1/CleanedData.xlsx](Task%201/CleanedData.xlsx)`

### Transformation steps (strict & to the point)

1. **Ingest**  
   - Imported `Task 1/RawData.xlsx` into Excel as a Table named `tblSales`.

2. **Type & value validation**  
   - Formatted `Sales` as *Currency* and flagged negative/zero entries.  
   - Ensured `Order Date` & `Ship Date` are true Excel dates (converted text→date where needed).

3. **Text normalization**  
   - `=PROPER(TRIM(...))` applied to Region, Category, Product and Customer names.

4. **ID & postal checks**  
   - Validation formulas for `Order ID`, `Customer ID`, `Product ID`.  
   - Blank postal codes replaced with `Unknown`.

5. **Missing / anomalous values**  
   - Flagged critical gaps; corrected or excluded after review.

6. **Save**  
   - Final cleaned table: `[Task 1/CleanedData.xlsx](Task%201/CleanedData.xlsx)`.

---

## Helper Columns (formulas)

Add these columns to `tblSales` (structured-reference examples):

```excel
-- Units Sold (each row = one line item)
=1

-- OrderDate_Real (if your Order Date is text; adjust to your format)
=DATE(
  VALUE(RIGHT([@[Order Date]],4)),
  VALUE(MID([@[Order Date]],4,2)),
  VALUE(LEFT([@[Order Date]],2))
)

-- Order Year
=YEAR([@[OrderDate_Real]])

-- Order Month (label)
=TEXT([@[OrderDate_Real]],"mmm")

-- Month Num (for sorting)
=MONTH([@[OrderDate_Real]])

-- Year–Month (single axis)
=TEXT([@[OrderDate_Real]],"yyyy-mm")
