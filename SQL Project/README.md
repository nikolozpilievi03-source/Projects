# 📊 Tech Layoffs — SQL Data Cleaning & Exploratory Analysis

A two-phase SQL project using a real-world dataset of global tech industry layoffs. The project covers end-to-end data preparation followed by structured exploratory analysis to surface meaningful trends.

---

## 🗂️ Project Structure

| File | Description |
|------|-------------|
| `Layoffs.sql` | Data cleaning pipeline — deduplication, standardization, null handling |
| `Exploring_DATA_Layoffs.sql` | Exploratory data analysis — aggregations, ranking, rolling totals |

---

## 🧹 Phase 1 — Data Cleaning (`Layoffs.sql`)

Raw data is rarely analysis-ready. This script transforms the source `layoffs` table through four systematic steps:

**1. Remove Duplicates**
- Created a staging table (`layoffs_staging`) to preserve the original data
- Used `ROW_NUMBER() OVER (PARTITION BY ...)` to identify exact duplicate rows
- Built a second staging table (`layoffs_staging2`) with a `row_num` column to safely delete duplicates

**2. Standardize Data**
- Trimmed whitespace from `company` names
- Unified inconsistent `industry` labels (e.g., `Crypto Currency`, `CryptoCurrency` → `Crypto`)
- Cleaned trailing punctuation from `country` values (e.g., `United States.` → `United States`)
- Converted `date` from string format to proper `DATE` type using `STR_TO_DATE()`

**3. Handle Nulls & Blanks**
- Set empty `industry` strings to `NULL` for consistency
- Self-joined the table to backfill missing `industry` values where another row for the same company had a known value
- Removed rows where both `total_laid_off` and `percentage_laid_off` were `NULL` (no analytical value)

**4. Remove Helper Columns**
- Dropped the `row_num` column after deduplication was complete

---

## 🔍 Phase 2 — Exploratory Data Analysis (`Exploring_DATA_Layoffs.sql`)

With clean data in hand, this script answers key questions about the layoff landscape:

**Scale & Extremes**
- Maximum single-event layoffs and full-company shutdowns (`percentage_laid_off = 1`)
- Companies that shut down entirely, ordered by funds raised — revealing how even well-funded startups folded

**Aggregations by Dimension**
- Total layoffs by **company**, **industry**, **country**, and **funding stage**
- Year-over-year breakdown to identify the hardest-hit periods

**Time Series Analysis**
- Monthly layoff totals across the full date range
- **Rolling cumulative total** using `SUM() OVER (ORDER BY month)` to visualize how layoffs accumulated over time

**Company Rankings by Year**
- Used a CTE + `DENSE_RANK() OVER (PARTITION BY year)` to rank the top 5 companies by layoffs for each calendar year

---

## 🛠️ Tools & Concepts Used

- **MySQL** — Window functions, CTEs, joins, date functions, string functions
- `ROW_NUMBER()`, `DENSE_RANK()` — Deduplication and ranking
- `STR_TO_DATE()`, `SUBSTR()` — Date parsing and manipulation
- `TRIM()`, `LIKE` — String cleaning and pattern matching
- Rolling aggregations with `SUM() OVER (ORDER BY ...)`

---

## 💡 Key Findings

- The dataset spans from **2020 to early 2023**, capturing the pandemic hiring boom and subsequent correction
- **Consumer**, **Retail**, and **Transportation** were among the hardest-hit industries
- The **United States** accounted for the largest share of layoffs by far
- Several companies that raised **hundreds of millions** in funding still shut down entirely
- Layoffs spiked sharply in **late 2022 and early 2023** as the tech correction accelerated

---

## 📁 Dataset

The dataset is based on publicly tracked tech layoff data (2020–2023), covering company, location, industry, total laid off, percentage laid off, funding stage, country, and funds raised.
