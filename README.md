# Data Cleaning Project — Financial Budgeting (SQL)

The goal of this script is to build a complete, step-by-step data cleaning pipeline that ensures accuracy and consistency in the financial_budgeting dataset.

# First Step Check & Remove duplicate data

# Code explanation

Checks the source table’s contents gives a baseline view before cleaning.

Select * from financial_budgeting;

Creates a staging table identical in structure to financial_budgeting.
This ensures the cleaning process doesn’t alter the original data.

CREATE TABLE financial_budgeting_staging
Like financial_budgeting;

Copies all rows from the original table into the staging area.

INSERT financial_budgeting_staging 
select * from financial_budgeting;

Builds a CTE (Common Table Expression) to mark duplicates.
It assigns a row number to each record within partitions defined by key columns — if two rows have identical values across all those columns, they share the same partition.

With duplicate_cte as(
Select *, 
ROW_NUMBER() over(partition by transaction_id,user_id,`date`,transaction_type,category,amount,
payment_mode,location,notes ) As duplicate_num
from financial_budgeting_staging)

Displays only the rows identified as duplicates.

select * from duplicate_cte
where duplicate_num > 1;

Creates another staging table to hold data along with the duplicate_num flag.

CREATE TABLE `financial_budgeting_staging2` (
  `transaction_id` text,
  `user_id` text,
  `date` text,
  `transaction_type` text,
  `category` text,
  `amount` int DEFAULT NULL,
  `payment_mode` text,
  `location` text,
  `notes` text,
  `duplicate_num` INT
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;


Loads all data from the first staging table into the second, tagging duplicates.

Insert INTO financial_budgeting_staging2
Select *, 
ROW_NUMBER() over(...) As duplicate_num
from financial_budgeting_staging;

Removes all rows beyond the first instance of each duplicate set — effectively deduplicating the dataset.


Delete from financial_budgeting_staging2
where duplicate_num > 1;

Shows the cleaned data for verification.

select * from financial_budgeting_staging2;


# Current scope

Table replication and staging setup

Duplicate detection and cleanup

⏳ Further cleaning (e.g., null handling, data type standardization, transformations) still to be done

Notes

The main table financial_budgeting remains untouched.

The process uses MySQL’s ROW_NUMBER() function, so version 8.0+ is required.

Once other cleaning tasks are added, this script will evolve into a full ETL workflow.
