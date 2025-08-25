# 🗂️ Data Cleaning & Insight Generation from Survey Data

## 📖 Project Story

### 1. The Beginning

We started with the Kaggle Data Science & ML Survey (2017–2021) — a huge, messy, wide dataset that looked like a goldmine but needed serious preparation before any insights could be extracted.

**Goal:** Make the dataset clean, consistent, and analysis‑ready — then perform EDA on each cleaned block to extract meaningful insights.

### 2. The Data

**Source:** Kaggle Data Science & ML Survey 2017–2021

**Original Rows:** 101,846 (after removing 4,456 duplicates)

**Original Columns:** ~293 before cleaning

**Format:**
- Multi‑choice → one column per option
- Single‑choice → one column per question

**Initial challenges:**
- Many blank/all‑null columns slowing Power Query & Excel
- Inconsistent naming (mixed casing, cryptic codes like Q1_Part_1)
- "None" / "Other" columns scattered in multiple formats
- Missing questions Q28–Q30 in source

### 3. Our Approach

1. Split the dataset into smaller files for speed and clarity.

2. Clean each file separately:
   - **Labeling** → for multi‑answer questions: Convert 0 to "Not Selected" and 1 to "Selected" for non‑technical stakeholders. Example: In Q7 (Programming Languages), 1 under "Lang: Python" = Selected, 0 = Not Selected.
   - **Mapping** → for single‑answer questions: map raw values to clear categories. Example: "United States of America" → "United States".
   - **Basic cleaning** → trim/remove extra spaces, fix casing, correct encoding issues.
   - **Data types** → set each field correctly (Text, Number, Date).

3. Export each cleaned file into an Excel workbook.

4. **EDA per sheet** — each cleaned sheet gets its own exploratory analysis to extract insights without slowing Excel by merging all data at once.

5. Document every change in simple, plain language so it's easy to follow and reproduce.

### 4. File Separation Plan

| File # | Contents |
|--------|----------|
| 1 | Demographics & General Questions (Q1–Q6) |
| 2 | Questions 7–15 |
| 3 | Questions 16–26 |
| 4 | Paired "Current vs Future" (Q27, Q32, Q34, Q36, Q37, Q38) — with Q28–Q30 missing |
| 5 | Q31, Q33, Q35, Q39–Q42 |

### 5. Special Handling for Paired Questions

In File 4:
- Each question has two versions — Current use and Future interest.
- Cleaned with mirrored naming (_Now / _Future) for clarity.

## 🚀 Cleaning Phase

### Sheet 1 — Demographic & General (Q1–Q6)

**Actions Taken:**
- **Headers:** Renamed via mapping table; removed survey question text from first row.
- **Country:** Standardized spelling/casing.
- **Education:** Mapped to categories (Bachelor's, Master's, Doctoral, Professional, No education/degree, College).
- **Years Coding:** Normalized into ordered bands.
- **Career Stage:** Derived from age ranges → Junior, Mid, Senior, Expert.
- **Gender:** Standardized to "Male" / "Female" for binary analysis; original kept for reference.
- **Job Title:** Trimmed spaces, corrected casing, validated against role list.
- **Duration:** Converted seconds to rounded minutes; flagged extreme outliers.

# 📊 EDA

## Key Analyses Conducted

1. **Top 10 Respondented Countries**  
   Ranked the countries with the highest number of survey participants, providing a clear view of the dataset's geographic concentration and primary respondent markets.

2. **Education Level Distribution Across All Jobs**  
   Measured the spread of qualifications (e.g., High School, Bachelor's, Master's, PhD) across the entire job spectrum, capturing the global talent pipeline's education makeup.

3. **Educational Backgrounds of Data Science Professionals**  
   Focused specifically on respondents in data‑science‑related roles to uncover the academic paths most common in this field, from undergraduate majors to postgraduate specializations.

4. **Top 5 Countries Leading Job Markets — Role Popularity by Country**  
   Identified the five most dominant job markets in the dataset, then broke down which roles are most prevalent in each, revealing pockets of specialization.

# 🔍 Analyze the Insights


- **Geographic Weighting**: The dataset is heavily influenced by a small set of countries that dominate the respondent pool. Strategic insights drawn from this survey will be most representative of these core markets.

- **Education Landscape**: The overall workforce skews towards higher education levels, with a strong representation of bachelor's and master's degree holders — but the proportion varies by country, hinting at differences in national education pipelines.

- **Data Science Talent Profile**: Data‑science professionals lean heavily toward advanced degrees, suggesting employers in this field may prioritize postgraduate qualifications or that such roles attract candidates with deeper academic backgrounds.

- **Market Specialization**: The leading five job markets reveal distinct role patterns — some are tech‑heavy, others show business or operations dominance — indicating that workforce development and recruitment strategies should be tailored to regional specializations.



--------------------

### Sheet 2 — Questions 7–15

**Summary:**
- **Headers:** Renamed via mapping table; survey text row removed.
- Contains single‑answer and multi‑answer questions.
- **Single‑answer columns (mapped):** [list columns here] — mapped raw responses into clean categories.
- **Multi‑answer columns (labeled):** [list columns here] — converted 0 to "Not Selected" and 1 to "Selected" for readability.

**Example labeling logic in Power Query:**

```powerquery
LabelMap = [
    #"Q7_Python" = "Lang: Python",
    #"Q7_SQL"    = "Lang: SQL"
],
Existing = List.Intersect({Record.FieldNames(LabelMap), Table.ColumnNames(PreviousStep)}),
Pairs = List.Transform(Existing, each {_, Record.Field(LabelMap, _)}),
Renamed = Table.RenameColumns(PreviousStep, Pairs, MissingField.Ignore)
```

**CPU Column Issue (Simplified):**
- In the raw data, the "CPU" column came in blank for all rows.
- We traced it to an import mismatch where the column header didn't match the expected name, so Power Query pulled it in as null.
- **Fix:** Created a new column that correctly pulled CPU values from the right source field, replacing nulls where applicable.

# Exploratory Data Analysis & Insights 

## Q8 – Preferred Programming Language to Learn First

### EDA:
- Python overwhelmingly leads with **78.6%** of respondents recommending it as the first language to learn.
- R (10.6%) and SQL (5.1%) trail far behind.

### Insights:
- Python's dominance suggests it is perceived as the most versatile and accessible entry point for programming, especially in data science and analytics.
- R's niche appeal aligns with statistical and academic use cases, while SQL's lower ranking reflects its role as a complementary rather than primary language.
- Training programs and beginner resources should prioritize Python, with optional tracks for R and SQL.

## Q9 – Distribution of Preferred IDEs

### EDA:
- **Jupyter** leads with 41.5k selections, followed by **VS Code** (27.1k) and **PyCharm** (23.9k).
- RStudio (21.6k) and Notepad++ (18.2k) form the mid‑tier.
- Other IDEs have smaller but notable user bases.

### Insights:
- Jupyter's lead reflects its integration with Python and suitability for exploratory, notebook‑based workflows.
- VS Code's strong showing indicates a growing preference for lightweight, extensible editors.
- Tooling support and documentation should focus on Jupyter and VS Code, with PyCharm as a key secondary environment.

## Q11 – Primary Computing Platforms

### EDA:
- Desktop (18.3k) and laptop (16.2k) dominate usage.
- Other platforms (workstations, "Platform" category, and miscellaneous) are far less common.

### Insights:
- Development and deployment strategies should assume desktop/laptop as the primary environment.
- Optimization for less common platforms may have limited ROI unless targeting specialized user groups.

## Q13 – Frequency of [Activity] Participation

### EDA:
- "Never" is the most common response (40k), followed by occasional participation (2–5 times: 6.5k; once: 6.2k).
- High‑frequency participation (>25 times) is rare.

### Insights:
- The activity has low penetration among respondents, suggesting either low awareness, low perceived value, or high barriers to entry.
- Engagement strategies should focus on converting "Never" respondents into occasional participants through awareness campaigns or low‑commitment entry points.

## Q15 – Frequency of Machine Learning Usage

### EDA:
- Most respondents use ML infrequently: <1 time (26.9k) or 1–2 times (16.8k).
- A significant group (6.0k) has never used ML.
- Heavy users (10+ times) are a small minority.

### Insights:
- ML adoption is still in early stages for the majority, with a long tail of occasional users.
- Training and tooling should target the "low‑frequency" segment to accelerate adoption.
- Advanced ML resources should be reserved for the smaller, high‑engagement group.

---

## Executive Summary – Section Q8–Q15

- **Python is the clear entry point** for programming, recommended by nearly 4 out of 5 respondents, with R and SQL far behind. This reinforces a Python‑first training and onboarding strategy.

- **Jupyter and VS Code dominate the IDE landscape**, reflecting a preference for flexible, lightweight, and Python‑friendly environments. PyCharm remains a strong third choice for more structured development.

- **Desktop and laptop usage is near‑universal**, simplifying environment standardization and reducing the need for platform‑specific adaptations.

- **Engagement gaps are evident** in both Q13 and Q15: large "Never" or "Low‑frequency" segments highlight untapped potential for participation in the surveyed activities and for machine learning adoption.

- **Adoption strategies should focus on conversion**, moving low‑engagement respondents into occasional or regular users through targeted awareness, accessible resources, and low‑barrier entry points.
