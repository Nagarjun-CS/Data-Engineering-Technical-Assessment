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

1. **Upload Input Files** (This location can be changed according to the requirement. For the assessment I have used databricks volumes to store the data. If data is stored in the different location change the path in the code as well. Data path and config are stored under the variable data_dir and config_dir respectively).
   
   - Raw partner data files (CSV or delimited TXT) should be uploaded to:
     ```
     /Volumes/data_catalog/source/assessment_volume/Healthcare_patners/
     ```
   - Partner configuration files should be uploaded to:
     ```
     /Volumes/data_catalog/source/assessment_volume/Config/
     ```
 3. **Run the Notebook**
   - Execute the main Databricks notebook.
   - The pipeline will:
     - Load each partner’s configuration
     - Read the raw file using the configured delimiter
     - Apply schema standardization and data cleaning
     - Remove invalid records from the final dataset
     - Export invalid DOB records per partner

3. **Outputs** (This location can be changed according to the requirement. For the assessment I have used databricks volumes to store the invalid DOB records. If you want to stored the invalid records in the different location change the path in the code as well. Output path is stored under the variable output_path).
   
   - **Final cleaned dataset** is available as a Spark DataFrame (`merged_df`)
   - **Invalid DOB records** are written to:
     ```
     /Volumes/data_catalog/source/assessment_volume/output/invalid_jobs/
     ```

## How to Add a New Partner

To onboard a new data partner, follow these steps:

1. **Prepare the Partner’s Raw Data File**
     - File can be of any format supported by Spark (e.g., .csv, .txt, .pipe, etc.).
     - The file name must begin with the partner_name (e.g., acme.txt, acme.csv, or acme.pipe if partner_name is acme).
     - Place the file inside:
       ```
       /Volumes/data_catalog/source/assessment_volume/Healthcare_partners/
       ```

2. **Create the Partner Configuration JSON**
     - The file name must begin with the partner_name. (e.g. partner_name.json )
     - Save a JSON file with the following structure inside:
        ```
        /Volumes/data_catalog/source/assessment_volume/Config/
       ```
     **Sample Config (partner_name.json)**

    ```json
        {
      "partner_code": "acme",
      "delimiter": "|",
      "date_format": "MM/dd/yyyy",
      "column_mapping": {
        "MBI": "external_id",
        "FNAME": "first_name",
        "LNAME": "last_name",
        "DOB": "dob",
        "EMAIL": "email",
        "PHONE": "phone"
      }
    }
    
**Notes:**

- **partner_code:** must match the start of the data file name (case-sensitive).
- **delimiter:** delimiter used in the data file (e.g., ,, |, $ etc).
- **date_format:** format of the date of birth (DOB) field.
- **column_mapping:** map original columns to standardized schema.

**3. Run the Notebook**

   - The pipeline automatically detects and processes all config files.
   - For each config, it finds the corresponding raw file using the partner_code.
   - The file is ingested, standardized, cleaned, and merged.
   - Rows with malformed or missing DOBs are logged and exported separately.

**4. Verify the Outputs**

    - Cleaned, valid records appear in the unified merged_df.
    - Invalid DOB records are exported per partner under:
     ```
    /Volumes/data_catalog/source/assessment_volume/output/invalid_dobs/{partner_code}/
     ```
