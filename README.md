# 🧹 BudgetWise Finance Data Cleaning (SQL Project)

## 📘 Overview
This project demonstrates end-to-end data cleaning in **MySQL** using only SQL queries.  
The dataset represents personal finance transactions (`financial_budgeting` table) that were messy — full of duplicates, inconsistent text formats, invalid dates, and incorrect amounts.

I built a reproducible SQL pipeline that transforms the raw table into a clean, analysis-ready dataset.

---

## ⚙️ Cleaning Process

### 1️⃣ Create Staging Tables
Work safely on a copy of the raw data.
```sql
CREATE TABLE financial_budgeting_staging LIKE financial_budgeting;
INSERT INTO financial_budgeting_staging SELECT * FROM financial_budgeting;

###2️⃣ Remove Duplicates

Use ROW_NUMBER() with window functions to find exact duplicates and keep only the first record.
```sql
ROW_NUMBER() OVER (
  PARTITION BY transaction_id, user_id, date, transaction_type, category, amount, payment_mode, location, notes
)
3️⃣ Standardize Text Columns

Fix inconsistent spellings and capitalization across:

Category (Rnt → Rent, Fod/Foods → Food, etc.)

Payment Mode (CRD → Card, BankTransfer → Bank Transfer)

Location / Notes (empty → N/A)

4️⃣ Clean & Convert Dates

Handle multiple date formats (dd-mm-yyyy, yyyy/mm/dd, etc.) using STR_TO_DATE, then convert the column to DATE type.

5️⃣ Clean Amounts

Convert negative amounts to positive using ABS()

Remove extreme outliers (amount >= 999999)

Ensure column type is numeric

6️⃣ Handle Missing Values

Drop rows missing critical data (like category or both category and location)

Replace blanks in text columns with N/A

Flag or delete null dates depending on analysis needs

7️⃣ Quality Checks

Run final validation queries:
```sql
SELECT COUNT(*) FROM financial_budgeting_staging2;
SELECT SUM(date IS NULL) AS null_dates FROM financial_budgeting_staging2;
SELECT DISTINCT category FROM financial_budgeting_staging2;

| Step         | Example Before   Example After
| ------------ | -------------- | --------------- | 
| Category     | `Fod`          | `Food`          |               
| Payment Mode | `BankTransfer` | `Bank Transfer` |               
| Date         | `'03/12/23'`   | `2023-03-12`    |               
| Amount       | `-450`         | `450`           |               
| Notes        | `''`           | `'N/A'`         |


#Tools & Concepts

SQL / MySQL 8

Window functions

CASE statements for categorical mapping

STR_TO_DATE for parsing multiple date formats

Data type conversion & constraints

Outlier handling

#Key Learnings

Built a full data-cleaning workflow using pure SQL.

Learned the importance of staging tables for safe transformations.

Practiced data standardization, type conversion, and validation.

Gained confidence working with window functions and conditional logic in SQL.
