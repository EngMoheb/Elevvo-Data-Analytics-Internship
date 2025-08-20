#  Chinook SQL to Insights

## 📖 The Backstory

Chinook — a digital music store — had years of sales data stored in a SQLite database. Our mission? Migrate it to PostgreSQL, clean it, and make it analysis-ready to answer key business questions:

- **Top Performers** → Which tracks generate the most revenue
- **Regional Patterns** → Where our customers are located  
- **Trends Over Time** → How sales evolve month-to-month
- Advanced EDA Queries for exploring the data 

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


## 🏗️ Building the Foundation

We designed our schema to reflect the relationships in a real music business:

- **Artists** create **Albums**
- **Albums** contain **Tracks**
- **Tracks** have **Genres** and **MediaTypes**
- **Customers** place **Invoices**
- **Invoices** contain **InvoiceLines**
- **Employees** support customers and manage sales

## 🚛 The Great Migration: CSV Import

With the schema ready, we bulk-imported the CSVs into PostgreSQL using `COPY`, handling date formats with `SET datestyle = 'DMY'`. This step brought all historical Chinook data into our new relational structure.

## 🧹Cleaning House: Preparing for EDA

Before analysis, we ensured the data was consistent and trustworthy:

- ✅ **Valid dates** → Removed any invoice dates far in the future or before the year 2000
- ✅ **Standardized text** → Country names cleaned and variants unified (USA, United States, U.S.A. → USA)
- ✅ **Referential integrity** → Checked for orphaned tracks, missing invoices, or broken foreign keys
- ✅ **Business rule checks** → Flagged unit prices or quantities ≤ 0

We also created a data changes audit table to track every transformation for transparency and reproducibility.

## 🔍 The Discovery Phase: Analysis Queries

### Query 1 — Top-Selling Tracks

```sql
WITH track_sales AS (
    SELECT 
        il."TrackId",
        SUM(il."Quantity" * il."UnitPrice") AS total_revenue,
        SUM(il."Quantity") AS units_sold
    FROM "InvoiceLine" il
    GROUP BY il."TrackId"
)
SELECT 
    RANK() OVER (ORDER BY ts.total_revenue DESC) AS revenue_rank,
    ts."TrackId",
    t."Name" AS track_name,
    a."Title" AS album_title,
    ar."Name" AS artist_name,
    ts.total_revenue,
    ts.units_sold
FROM track_sales ts
JOIN "Track" t ON ts."TrackId" = t."TrackId"
LEFT JOIN "Album" a ON t."AlbumId" = a."AlbumId"
LEFT JOIN "Artist" ar ON a."ArtistId" = ar."ArtistId"
ORDER BY ts.total_revenue DESC, ts.units_sold DESC
LIMIT 10;
```

**Result Sample:**

| Rank | Track Name | Album | Artist | Revenue | Units |
|------|------------|--------|---------|---------|-------|
| 1 | How to Stop an Exploding Man | Heroes, Season 1 | Heroes | 3.98 | 2 |
| 1 | The Woman King | Battlestar Galactica S3 | Battlestar Galactica | 3.98 | 2 |
| 1 | Walkabout | Lost, Season 1 | Lost | 3.98 | 2 |

### Query 2 — Revenue by Country

```sql
WITH revenue_by_country AS (
    SELECT 
        TRIM("Country") AS country,
        SUM(il."Quantity" * il."UnitPrice") AS total_revenue
    FROM "Invoice" inv
    JOIN "InvoiceLine" il ON inv."InvoiceId" = il."InvoiceId"
    JOIN "Customer" c ON inv."CustomerId" = c."CustomerId"
    GROUP BY TRIM("Country")
)
SELECT 
    country,
    total_revenue,
    ROUND(total_revenue * 100.0 / SUM(total_revenue) OVER (), 2) AS pct_of_total
FROM revenue_by_country
ORDER BY total_revenue DESC;
```

**Result Sample:**

| Country | Revenue | % of Total |
|---------|---------|------------|
| USA | 523.06 | 22.46 |
| Canada | 303.96 | 13.05 |
| France | 195.10 | 8.38 |
| Brazil | 190.10 | 8.16 |

### Query 3 — Monthly Revenue Trends

```sql
WITH monthly_sales AS (
    SELECT 
        TO_CHAR(inv."InvoiceDate", 'YYYY-MM') AS month,
        SUM(il."Quantity" * il."UnitPrice") AS revenue
    FROM "Invoice" inv
    JOIN "InvoiceLine" il ON inv."InvoiceId" = il."InvoiceId"
    GROUP BY 1
)
SELECT 
    month,
    revenue,
    SUM(revenue) OVER (ORDER BY month) AS running_total
FROM monthly_sales
ORDER BY month;
```

**Result Sample:**

| Month | Revenue | Running Total |
|-------|---------|---------------|
| 2009-01 | 37.62 | 37.62 |
| 2009-02 | 37.62 | 75.24 |
| 2009-03 | 37.62 | 112.86 |
| ... | ... | ... |
| 2010-01 | 52.62 | (higher) |


## 📊 Key Insights

- Track sales are highly distributed with multiple ties for top revenue
- USA and Canada dominate revenue generation (35%+ combined)
- Monthly sales show consistent growth patterns over time
- Data quality improvements were essential for accurate analysis

## 🛠️ Technologies Used

- **PostgreSQL** - Primary database system
- **SQLite** - Source database format  
- **VS Code** - Development environment
- **DB Browser for SQLite** - Data export tool

---

*This project demonstrates end-to-end database migration, data cleaning, and business intelligence workflows using real-world music industry data.*
