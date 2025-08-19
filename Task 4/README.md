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
*(check the worksheet here)*

**Actions Taken:**
- **Headers:** Renamed via mapping table; removed survey question text from first row.
- **Country:** Standardized spelling/casing.
- **Education:** Mapped to categories (Bachelor's, Master's, Doctoral, Professional, No education/degree, College).
- **Years Coding:** Normalized into ordered bands.
- **Career Stage:** Derived from age ranges → Junior, Mid, Senior, Expert.
- **Gender:** Standardized to "Male" / "Female" for binary analysis; original kept for reference.
- **Job Title:** Trimmed spaces, corrected casing, validated against role list.
- **Duration:** Converted seconds to rounded minutes; flagged extreme outliers.

### Sheet 2 — Questions 7–15
*(check the worksheet here)*

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

## 📊 EDA (to be filled after analysis)

Here we will describe how we perform EDA for this sheet:
- Explore both selected (1) and not selected (0) counts.
- Use pivot tables to summarise by question and compare answers.
- Highlight top categories and unusual patterns.

## 📈 Analyzing the Insights (to be filled after EDA)

Once EDA is complete, this section will capture:
- Key trends in Q7–Q15
- Notable differences between years
- High‑frequency vs low‑frequency answer options
