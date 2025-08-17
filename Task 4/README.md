# 🗂️ Data Cleaning & Preparation Project – Kaggle Data Science & ML Survey

## 📖 Project Story – Before the Cleaning Began

### 1. The Beginning

This journey started with a deceptively simple-looking dataset — the Kaggle Data Science & ML Survey (covering 2017–2021). At first glance, it looked like a goldmine of insights waiting to be unlocked. But as soon as we peeked under the hood, we realized we weren't just mining for insights — we'd need to do serious excavation before we could even start exploring.

**The mission was clear:**

Take this massive, messy, wide survey dataset and make it clean, consistent, and analysis-ready — while documenting every step so anyone could reproduce or audit the process later.

### 2. Meet the Data

- **Source:** Kaggle Data Science & ML Survey
- **Original Rows:** 101,846 unique entries (after removing 4,456 duplicates)
- **Original Columns:** ~300 (exact expected count before cleaning: 293)
- **Format:** Multi-choice survey → one column per option

**Challenges spotted immediately:**

- Many blank or all-null columns causing Excel/Power Query to choke
- Inconsistent column naming (mixed casing, cryptic codes like `Q1_Part_1`)
- Performance slowdowns due to extreme dataset width
- "None" / "Other" answer columns scattered throughout
- Missing survey questions Q28–Q30 in original data

### 3. The First Strategic Moves

Rather than try to clean all ~300 columns at once, we decided to split the dataset into smaller, logical files to make the work lighter and more systematic. We also wanted to group similar structures together — especially "paired" questions — so we could apply consistent transformations without fear of breaking unrelated data.

### 4. The Data Separation Plan

We ended up with 5 smaller files, each representing a theme or block of questions:

| File # | Contents |
|--------|----------|
| 1 | **Demographics & General Q** → Demographics + Questions 1-6 |
| 2 | **Questions 7-15** |
| 3 | **Questions 16-26** |
| 4 | **Current vs Future (Paired structure)** → Q27, Q32, Q34, Q36, Q37, Q38 — two parts each ("Current Use" and "Future Interest"), with Q28–Q30 missing from source |
| 5 | **Questions 31, 33, 35, 39, 40, 41, 42** |

**Why split like this?**

✅ Faster handling in Power Query & Excel  
✅ Easier to apply consistent cleaning rules within each block  
✅ Special handling for "paired" questions without affecting the rest

### 5. Special Handling for Paired Questions

The **Current vs Future** set (File 4) is special because each question appears in two parts, with identical answer options:

- **Part 1:** Tools/tech currently used regularly
- **Part 2:** Tools/tech the respondent wants to learn in the next 2 years

These will be kept in the same sheet, but cleaned with mirrored naming conventions (e.g., `_Now` / `_Future` suffixes). This will make later Power BI visualizations simple and accurate.

### 6. Pre-Cleaning Agreements

Before touching any values, we defined the core rules we'll follow in the cleaning phase:

- **Use a mapping table for renaming** → no manual header edits, all documented
- **Standardize data types** → match field purpose (Text, Number, Date)
- **Handle "None" & "Other"** → consistent prefixes or drop if redundant
- **Tackle missing data strategically** → distinguish between true skips and accidental blanks
- **Document everything** in a change log with the file name as the header
- **Validate final merged file** → must have exactly 293 columns after recombining

### 7. Validation Check Plan – Column Count

**Before cleaning, we will:**

1. Load original downloaded dataset → confirm column count = 293
2. If count differs, investigate before proceeding (check export completeness, hidden columns, or mismatched source)

### 8. What Happens Next

With the strategy locked, we're ready to:

1. **Clean each of the 5 files separately** using the agreed rules
2. **Keep a living change log** so the documentation tells the full story from raw → clean
3. **Merge all files back** into one master dataset
4. **Run validation** to ensure structure matches the original (but cleaner)

The next commit in this project will mark the start of the **Cleaning Phase** — where we tame the chaos and shape this into a dataset ready for powerful exploratory analysis.

---

> 💡 **Note:** This section is "before cleaning" — the story will be expanded with detailed cleaning logs and transformations as each file is processed.

## Project Structure

```
📁 kaggle-survey-data-cleaning/
├── 📄 README.md                    # This documentation
├── 📁 raw-data/                    # Original dataset files
├── 📁 split-files/                 # 5 separated file groups
│   ├── file1_demographics_q1-6.csv
│   ├── file2_q7-15.csv
│   ├── file3_q16-26.csv
│   ├── file4_current-future-paired.csv
│   └── file5_remaining-questions.csv
├── 📁 cleaned-data/                # Final clean datasets
├── 📁 documentation/               # Cleaning logs & mapping tables
└── 📁 validation/                  # Data quality checks
```


## Contributing

This project follows a systematic, documented approach to data cleaning. If you'd like to contribute:

- Follow the established naming conventions
- Document all changes in the appropriate log files
- Ensure validation checks pass before submitting changes

---

**Status:** 🚧 Pre-Cleaning Phase Complete | Next: Data Cleaning Implementation
