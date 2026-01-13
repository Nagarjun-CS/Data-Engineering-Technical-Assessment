# Data-Engineering-Technical-Assessment

## Overview

This pipeline ingests and standardizes healthcare eligibility data from multiple partner files (CSV or pipe-delimited .txt), each with their own schema and date format. The process is configuration-driven using partner-specific JSON files and handles:

- Column renaming and type standardization
 
- Phone/email formatting

- Date of birth parsing with error handling

- Invalid record logging (e.g., malformed DOBs)

- Merging all cleaned datasets into a unified DataFrame

Each data record is tagged with its partner_code, and any rows with invalid or missing dob values are saved separately per partner for follow-up.

## How to Run the Pipeline

1. **Upload Input Files**
   - Raw partner data files (CSV or delimited TXT) should be uploaded to:
     ```
     /Volumes/data_catalog/source/assessment_volume/Healthcare_patners/
     ```
   - Partner configuration files should be uploaded to:
     ```
     /Volumes/data_catalog/source/assessment_volume/Config/
     ```
 2. **Run the Notebook**
   - Execute the main Databricks notebook.
   - The pipeline will:
     - Load each partner’s configuration
     - Read the raw file using the configured delimiter
     - Apply schema standardization and data cleaning
     - Remove invalid records from the final dataset
     - Export invalid DOB records per partner

3. **Outputs**
   - **Final cleaned dataset** is available as a Spark DataFrame (`final_df`)
   - **Invalid DOB records** are written to:
     ```
     /Volumes/data_catalog/source/assessment_volume/output/invalid_dobs/<partner_code>/
     ```

