# Data Cleaning SQL Project

## Introduction

In this project, I focus on the critical process of data cleaning, a vital step in ensuring the integrity and usability of datasets. Using SQL, I explore a layoffs dataset that contains various inconsistencies, missing values, and duplicate entries. This project aims to demonstrate effective techniques for cleaning data to prepare it for analysis.

## Objectives

The main objectives of this project are to:
- Identify and handle missing values
- Remove duplicates
- Standardize data formats
- Validate data integrity

### Tools Used
SQL: Utilized SQL for querying and manipulating the dataset, performing data cleaning tasks, and ensuring data integrity.

Database Management System: Employed a relational database management system (MySQL) for data storage and management.
 
## Data Cleaning Process

The data cleaning process involves several SQL queries to transform the dataset into a usable format. Below are some key steps taken during the cleaning process:

### 1. Identifying Missing Values

We first identify missing values in the dataset to understand the extent of data loss.

```sql
# importing the data
CREATE TABLE world_layoffs.layoffs_staging 
LIKE world_layoffs.layoffs;

INSERT layoffs_staging 
SELECT * FROM world_layoffs.layoffs;

# identifying duplicates
WITH duplicate_cte AS 
(
SELECT *, 
ROW_NUMBER() OVER(
PARTITION BY company, location, industry, total_Laid_off, percentage_laid_off, `date`, stage, 
country, funds_raised_millions) AS row_num
FROM layoffs_staging
)
SELECT * 
FROM duplicate_cte
WHERE row_num > 1;

# Putting duplicates in a table
CREATE TABLE `layoffs_staging2` (
	`company` text, 
    `location` text, 
    `industry` text, 
    `total_laid_off` int DEFAULT NULL, 
    `percentage_laid_off` text, 
    `date` text, 
    `stage` text, 
    `country` text, 
    `funds_raised_millions` int DEFAULT NULL, 
	`row_num` INT
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;

# deleting those duplicates
DELETE
FROM layoffs_staging2
WHERE row_num > 1;
```

### 2. Updating null and empty values
```sql
UPDATE layoffs_staging2 t1
JOIN layoffs_staging t2
	ON t1.company = t2.company
SET t1.industry = t2.industry
WHERE t1.industry IS NULL
AND t2.industry IS NOT NULL;
```

### 3. Removing columns and rows
```sql
DELETE
FROM layoffs_staging2
WHERE total_laid_off IS NULL
AND percentage_laid_off is NULL;

ALTER TABLE layoffs_staging2
DROP COLUMN row_num;
```
## What I Learned
Throughout this data cleaning project, I gained valuable insights and skills that are essential for any data-driven work:

Importance of Data Quality: I learned that clean, high-quality data is crucial for accurate analysis. Issues like missing values and duplicates can lead to misleading results, underscoring the necessity of thorough data cleaning.

SQL Proficiency: Working with SQL deepened my understanding of database management and query writing. I became more comfortable with complex queries, including those for identifying and handling missing data, as well as manipulating and transforming datasets.

Critical Thinking: The process of validating data integrity required critical thinking and attention to detail. I learned to question data anomalies and ensure that the cleaned dataset would support reliable conclusions.

Preparation for Analysis: I recognized that data cleaning is a foundational step that prepares datasets for further analysis. A well-cleaned dataset can significantly impact the insights derived from it, enabling more informed decision-making.

Best Practices in Data Cleaning: I developed a set of best practices for data cleaning, including thorough documentation of cleaning processes and maintaining backups of original datasets. This is vital for transparency and reproducibility in data projects.

## Results
After applying the above cleaning techniques, the dataset is now free of duplicates and missing values, with standardized formats, making it ready for further analysis.

## Conclusion
Data cleaning is a fundamental step in the data analysis process. This project showcases how SQL can be effectively used to prepare datasets by handling common issues such as missing values, duplicates, and format inconsistencies. The cleaned dataset can now serve as a reliable foundation for meaningful analysis and insights.

