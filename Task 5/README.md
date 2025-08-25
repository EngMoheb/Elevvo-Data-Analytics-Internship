# 🎵 Chinook Database Adventure: From Legacy SQLite to Business Insights

A step-by-step journey of transforming music sales data into a clean, queryable PostgreSQL database for analysis.

## 📖 The Backstory

Chinook — a digital music store — had years of sales data stored in a SQLite database. Our mission? Migrate it to PostgreSQL, clean it, and make it analysis-ready to answer three key business questions:

- **Top Performers** → Which tracks generate the most revenue
- **Regional Patterns** → Where our customers are located  
- **Trends Over Time** → How sales evolve month-to-month

The core migration process was straightforward: We opened the SQLite database in DB Browser, exported each table to CSV, uploaded those CSVs into VS Code, connected to a PostgreSQL database, created the tables, and imported the data.

## 🗃️ The Raw Materials

We started with a single `Chinook.sqlite` database containing 11 interrelated tables:

| Table/File | Description |
|------------|-------------|
| Artist | Musicians |
| Album | Collections of tracks |
| Genre | Rock, Jazz, Classical, etc. |
| MediaType | MP3, AAC, video formats |
| Track | Individual songs (products) |
| Employee | Sales team & management |
| Customer | Buyers worldwide |
| Invoice | Sales transactions |
| InvoiceLine | Items per sale |
| Playlist | Curated collections |
| PlaylistTrack | Playlist–track relationships |

## ⚙️ Setting the Stage: Environment Setup

### Prerequisites for Success:

- PostgreSQL (any recent version)
- pgAdmin or VS Code with PostgreSQL extension
- CSV files accessible to the PostgreSQL server
- Proper permissions for file operations

### Key Configuration Note:
If CSV files use DD-MM-YY date formats, set:
```sql
SET datestyle = 'DMY';
```
before importing to avoid date parsing errors.

## 🏗️ Building the Foundation: Database Schema

We designed our schema to reflect the relationships in a real music business:

- **Artists** create **Albums**
- **Albums** contain **Tracks**
- **Tracks** have **Genres** and **MediaTypes**
- **Customers** place **Invoices**
- **Invoices** contain **InvoiceLines**
- **Employees** support customers and manage sales

*(Full schema creation script is preserved for reproducibility.)*

## 🚛 The Great Migration: CSV Import

With the schema ready, we bulk-imported the CSVs into PostgreSQL using `COPY`, handling date formats with `SET datestyle = 'DMY'`. This step brought all historical Chinook data into our new relational structure.

## 🧹 Cleaning House: Preparing for EDA

Before analysis, we ensured the data was consistent and trustworthy:

 ```sql
/* 1) Clean Date/Time Anomalies
      - Any invoice dates outside the range 2000-01-01 to today 
        are considered invalid and set to NULL */
UPDATE "Invoice"
   SET "InvoiceDate" = NULL
 WHERE "InvoiceDate" < DATE '2000-01-01'
    OR "InvoiceDate" > CURRENT_DATE;

sql /* 2) Identify Orphaned Foreign-Key Records
      - Tracks without a linked album
      - InvoiceLines without a valid Track reference */
-- Tracks missing AlbumId
SELECT *
  FROM "Track" t
 WHERE t."AlbumId" IS NULL;

-- InvoiceLines referencing non‑existent Tracks
SELECT il.*
  FROM "InvoiceLine" il
  LEFT JOIN "Track" t ON il."TrackId" = t."TrackId"
 WHERE t."TrackId" IS NULL;

/* 3) Standardize Text Columns
      - Trim leading/trailing spaces
      - Capitalize each word in the Country field */
UPDATE "Customer"
   SET "Country" = INITCAP(TRIM("Country"));

/* 4) Validate Numeric Fields
      - Identify any UnitPrice ≤ 0 or Quantity ≤ 0 */
SELECT *
  FROM "InvoiceLine"
 WHERE "UnitPrice" <= 0
    OR "Quantity" <= 0;
```


- ✅ **Valid dates** → Removed any invoice dates far in the future or before the year 2000
- ✅ **Standardized text** → Country names cleaned and variants unified (USA, United States, U.S.A. → USA)
- ✅ **Referential integrity** → Checked for orphaned tracks, missing invoices, or broken foreign keys
- ✅ **Business rule checks** → Flagged unit prices or quantities ≤ 0

We also created a data changes audit table to track every transformation for transparency and reproducibility.

## 🔍 The Discovery Phase: Analysis Queries

With clean data in place, we wrote targeted SQL queries to answer the business questions.

### Query 1 — Table Inventory (Row Counts)

**Purpose:** First step in EDA — verify table sizes and ensure expected data presence.

```sql
-- Count rows per key table in the Chinook database
SELECT 'Artist'        AS table_name, COUNT(*) AS row_count FROM Artist
UNION ALL
SELECT 'Album',        COUNT(*) FROM Album
UNION ALL
SELECT 'Track',        COUNT(*) FROM Track
UNION ALL
SELECT 'Genre',        COUNT(*) FROM Genre
UNION ALL
SELECT 'MediaType',    COUNT(*) FROM MediaType
UNION ALL
SELECT 'Customer',     COUNT(*) FROM Customer
UNION ALL
SELECT 'Employee',     COUNT(*) FROM Employee
UNION ALL
SELECT 'Invoice',      COUNT(*) FROM Invoice
UNION ALL
SELECT 'InvoiceLine',  COUNT(*) FROM InvoiceLine
UNION ALL
SELECT 'Playlist',     COUNT(*) FROM Playlist
UNION ALL
SELECT 'PlaylistTrack',COUNT(*) FROM PlaylistTrack;
```

**Results:**

| table_name | row_count |
|------------|-----------|
| Artist | 275 |
| Album | 347 |
| Track | 3503 |
| Genre | 25 |
| MediaType | 5 |
| Customer | 59 |
| Employee | 8 |
| Invoice | 412 |
| InvoiceLine | 2240 |
| Playlist | 18 |
| PlaylistTrack | 8715 |

### Query 2 — Top-Selling Tracks

**Purpose:** Identify highest-revenue tracks with album & artist context.

```sql
WITH track_sales AS (
    SELECT
        il.TrackId,
        SUM(il.Quantity * il.UnitPrice) AS total_revenue, -- total $ per track
        SUM(il.Quantity)                AS units_sold     -- total units per track
    FROM InvoiceLine il
    GROUP BY il.TrackId
)
SELECT
    RANK() OVER (ORDER BY ts.total_revenue DESC) AS revenue_rank,  -- rank by revenue
    ts.TrackId,
    t.Name       AS track_name,
    a.Title      AS album_title,
    ar.Name      AS artist_name,
    total_revenue::money AS total_revenue,                         -- currency format
    ts.units_sold
FROM track_sales ts
JOIN Track  t  ON ts.TrackId = t.TrackId
JOIN Album  a  ON t.AlbumId  = a.AlbumId
JOIN Artist ar ON a.ArtistId = ar.ArtistId
ORDER BY ts.total_revenue DESC
LIMIT 10;
```

**Results:**

| revenue_rank | TrackId | track_name | album_title | artist_name | total_revenue | units_sold |
|--------------|---------|------------|-------------|-------------|---------------|------------|
| 1 | 3223 | How to Stop an Exploding Man | Heroes, Season 1 | Heroes | $3.98 | 2 |
| 1 | 2832 | The Woman King | Battlestar Galactica, S3 | Battlestar Galactica | $3.98 | 2 |
| 1 | 2868 | Walkabout | Lost, Season 1 | Lost | $3.98 | 2 |
| 1 | 2850 | The Fix | Heroes, Season 1 | Heroes | $3.98 | 2 |
| 1 | 3250 | Pilot | Aquaman | Aquaman | $3.98 | 2 |
| 1 | 3214 | Phyllis's Wedding | The Office, Season 3 | The Office | $3.98 | 2 |
| 1 | 3177 | Hot Girl | The Office, Season 1 | The Office | $3.98 | 2 |
| 1 | 3200 | Gay Witch Hunt | The Office, Season 3 | The Office | $3.98 | 2 |
| 9 | 2860 | Adrift | Lost, Season 2 | Lost | $1.99 | 1 |
| 9 | 2862 | The Glass Ballerina | Lost, Season 3 | Lost | $1.99 | 1 |

### Query 3 — Revenue by Region

**Purpose:** Compare sales contribution by country using billing address (finalized at sale).

```sql
WITH revenue_by_country AS (
    SELECT 
        INITCAP(TRIM(BillingCountry)) AS country,               -- clean + title case
        SUM(il.Quantity * il.UnitPrice) AS total_revenue
    FROM Invoice inv
    JOIN InvoiceLine il ON inv.InvoiceId = il.InvoiceId
    GROUP BY INITCAP(TRIM(BillingCountry))
)
SELECT 
    country,
    total_revenue::money AS total_revenue,                                      -- currency
    ROUND(total_revenue * 100.0 / SUM(total_revenue) OVER (), 2)::text || '%' AS pct_of_total -- percentage
FROM revenue_by_country
ORDER BY total_revenue DESC;
```

**Results:**

| country | total_revenue | pct_of_total |
|---------|---------------|--------------|
| Usa | $523.06 | 22.46% |
| Canada | $303.96 | 13.05% |
| France | $195.10 | 8.38% |
| Brazil | $190.10 | 8.16% |
| Germany | $156.48 | 6.72% |
| United Kingdom | $112.86 | 4.85% |
| Czech Republic | $90.24 | 3.88% |
| Portugal | $77.24 | 3.32% |
| India | $75.26 | 3.23% |
| Chile | $46.62 | 2.00% |
| … | … | … |

### Query 4 — Monthly Revenue & Running Total

**Purpose:** Show sales timeline with cumulative growth.

```sql
WITH monthly_sales AS (
    SELECT
        TO_CHAR(inv.InvoiceDate, 'YYYY-MM') AS month,              -- month label
        SUM(il.Quantity * il.UnitPrice)      AS revenue             -- total revenue per month
    FROM Invoice inv
    JOIN InvoiceLine il ON inv.InvoiceId = il.InvoiceId
    GROUP BY TO_CHAR(inv.InvoiceDate, 'YYYY-MM')
)
SELECT
    month,
    revenue::money AS revenue,                                     -- currency format
    SUM(revenue) OVER (ORDER BY month) ::money AS running_total     -- cumulative total in currency
FROM monthly_sales
ORDER BY month;
```

**Results:**

| month | revenue | running_total |
|-------|---------|---------------|
| 2009-01 | $35.64 | $35.64 |
| 2009-02 | $37.62 | $73.26 |
| 2009-03 | $37.62 | $110.88 |
| 2009-04 | $37.62 | $148.50 |
| 2009-05 | $37.62 | $186.12 |
| 2009-06 | $37.62 | $223.74 |
| 2009-07 | $37.62 | $261.36 |
| 2009-08 | $37.62 | $298.98 |
| 2009-09 | $37.62 | $336.60 |
| 2009-10 | $37.62 | $374.22 |
| … | … | … |
| 2013-12 | $38.62 | $2328.60 |

## 📊 Advanced EDA Queries

### Query 5 — Revenue by Country 

**Purpose:** Identify top-performing countries by total sales to guide market prioritization.

```sql
SELECT 
    CASE 
        WHEN c."Country" = 'Usa' THEN 'USA'               -- normalize country name
        ELSE c."Country"
    END AS country, 
    SUM(il."Quantity" * il."UnitPrice")::money AS total_revenue -- currency format
FROM "Invoice"       AS inv
JOIN "InvoiceLine"   AS il  ON inv."InvoiceId"   = il."InvoiceId"
JOIN "Customer"      AS c   ON inv."CustomerId"  = c."CustomerId"
GROUP BY country
ORDER BY total_revenue DESC
LIMIT 20;
```

**Results:**

| Country | total_revenue |
|---------|---------------|
| USA | $523.06 |
| Canada | $303.96 |
| France | $195.10 |
| Brazil | $190.10 |
| Germany | $156.48 |
| United Kingdom | $112.86 |
| Czech Republic | $90.24 |
| Portugal | $77.24 |
| India | $75.26 |
| Chile | $46.62 |
| Hungary | $45.62 |
| Ireland | $45.62 |
| Austria | $42.62 |
| Finland | $41.62 |
| Netherlands | $40.62 |
| Norway | $39.62 |
| Sweden | $38.62 |
| Argentina | $37.62 |
| Belgium | $37.62 |
| Poland | $37.62 |

### Query 6 — Top 10 Tracks by Revenue 

```sql
SELECT 
    il."TrackId",
    t."Name" AS track_name,
    SUM(il."Quantity" * il."UnitPrice")::money AS total_revenue, -- currency format
    SUM(il."Quantity")                AS units_sold
FROM "InvoiceLine" AS il
JOIN "Track"       AS t ON il."TrackId" = t."TrackId"
GROUP BY il."TrackId", t."Name"
ORDER BY total_revenue DESC
LIMIT 10;
```

**Results:**

| TrackId | track_name | total_revenue | units_sold |
|---------|------------|---------------|------------|
| 3250 | Pilot | $3.98 | 2 |
| 3200 | Gay Witch Hunt | $3.98 | 2 |
| 2850 | The Fix | $3.98 | 2 |
| 3223 | How to Stop an Exploding Man | $3.98 | 2 |
| 2868 | Walkabout | $3.98 | 2 |
| 3177 | Hot Girl | $3.98 | 2 |
| 3214 | Phyllis's Wedding | $3.98 | 2 |
| 2832 | The Woman King | $3.98 | 2 |
| 2844 | Better Halves | $1.99 | 1 |
| 3196 | Michael's Birthday | $1.99 | 1 |

### Query 7 — Monthly Revenue with MoM % Change

```sql
WITH monthly AS (
    SELECT 
        TO_CHAR(inv."InvoiceDate", 'YYYY-MM') AS month,
        SUM(il."Quantity" * il."UnitPrice")::money AS revenue
    FROM "Invoice"       AS inv
    JOIN "InvoiceLine"   AS il ON inv."InvoiceId" = il."InvoiceId"
    GROUP BY 1
)
SELECT 
    month,
    revenue,
    CASE 
        WHEN LAG(revenue) OVER (ORDER BY month) IS NULL THEN NULL
        ELSE ROUND(
            100.0 * (revenue::numeric - LAG(revenue) OVER (ORDER BY month)::numeric) /
            NULLIF(LAG(revenue) OVER (ORDER BY month)::numeric, 0), 
        2)::text || '%'                                     -- append % sign
    END AS pct_change
FROM monthly
ORDER BY month;
```

**Results (excerpt):**

| month | revenue | pct_change |
|-------|---------|------------|
| 2009-01 | $35.64 | |
| 2009-02 | $37.62 | 5.56% |
| 2009-03 | $37.62 | 0.00% |
| … | … | … |
| 2013-12 | $38.62 | -22.17% |

### Query 8 — Top Customers by Lifetime Value (LTV)

```sql
SELECT 
    c."CustomerId",
    c."FirstName",
    c."LastName",
    SUM(il."Quantity" * il."UnitPrice")::money AS lifetime_value, -- currency format
    COUNT(DISTINCT inv."InvoiceId") AS orders
FROM "Customer"     AS c
JOIN "Invoice"      AS inv ON c."CustomerId" = inv."CustomerId"
JOIN "InvoiceLine"  AS il  ON inv."InvoiceId" = il."InvoiceId"
GROUP BY c."CustomerId", c."FirstName", c."LastName"
ORDER BY lifetime_value DESC
LIMIT 50;
```

**Results (excerpt):**

| CustomerId | FirstName | LastName | lifetime_value | orders |
|------------|-----------|----------|----------------|--------|
| 6 | Helena | HolÃ½ | $49.62 | 7 |
| 26 | Richard | Cunningham | $47.62 | 7 |
| 57 | Luis | Rojas | $46.62 | 7 |
| … | … | … | … | … |

### Query 9 — Revenue by Genre

```sql
WITH genre_rev AS (
    SELECT 
        g."Name" AS genre,
        SUM(il."Quantity" * il."UnitPrice")::money AS revenue
    FROM "InvoiceLine" AS il
    JOIN "Invoice"     AS inv ON il."InvoiceId" = inv."InvoiceId"
    JOIN "Track"       AS t   ON il."TrackId"   = t."TrackId"
    JOIN "Genre"       AS g   ON t."GenreId"    = g."GenreId"
    GROUP BY g."Name"
)
SELECT 
    genre,
    revenue,
    ROUND(100.0 * revenue::numeric / SUM(revenue::numeric) OVER (), 2)::text || '%' AS pct_of_total
FROM genre_rev
ORDER BY revenue DESC;
```

**Results (top 5):**

| genre | revenue | pct_of_total |
|-------|---------|--------------|
| Rock | $826.65 | 35.50% |
| Latin | $382.14 | 16.41% |
| Metal | $261.36 | 11.22% |
| Alternative & Punk | $241.56 | 10.37% |
| TV Shows | $93.53 | 4.02% |

### Query 10 — Average Order Value (AOV) by Month

```sql
SELECT 
    TO_CHAR(inv."InvoiceDate",'YYYY-MM') AS month,
    ROUND(
        SUM(il."Quantity" * il."UnitPrice") / 
        NULLIF(COUNT(DISTINCT inv."InvoiceId"), 0),
        2
    ) AS "AOV"                                              -- renamed to AOV
FROM "Invoice"       AS inv
JOIN "InvoiceLine"   AS il ON inv."InvoiceId" = il."InvoiceId"
GROUP BY 1
ORDER BY 1;
```

**Results:**

| month | AOV |
|-------|-----|
| 2009-01 | 5.94 |
| 2009-02 | 5.37 |
| 2009-03 | 5.37 |
| … | … |
| 2013-12 | 5.52 |


## **Top Insight:**

* **Top Markets:** USA dominates sales, followed by Canada, France, Brazil, and Germany.
* **Revenue by Genre:** Rock, Latin, and Metal generate over 50% of total revenue.
* **Customer Loyalty:** A consistent group of repeat buyers sustains monthly sales.
* **Product Spread:** Sales come from many tracks with small, steady volumes rather than a few mega-hits.
* **Stable Revenue Trend:** Monthly income holds steady with occasional spikes from special releases.
* **Consistent Basket Size:** Average spend per invoice remains steady over time.

## 🛠️ Technologies Used

- **PostgreSQL** - Primary database system
- **SQLite** - Source database format  
- **VS Code** - Development environment
- **DB Browser for SQLite** - Data export tool


