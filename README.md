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
     /Volumes/data_catalog/source/assessment_volume/output/invalid_dobs/
     ```

## How to Add a New Partner

1. **Create a Configuration File**

   Add a new JSON file under:
    ```
   /Volumes/data_catalog/source/assessment_volume/Config/
    ```
    Example:
    ```json
    {
      "partner_code": "newpartner",
      "delimiter": ",",
      "date_format": "MM/dd/yyyy",
      "column_mapping": {
        "SubscriberID": "external_id",
        "FirstName": "first_name",
        "LastName": "last_name",
        "DOB": "dob",
        "EmailAddress": "email",
        "PhoneNumber": "phone"
      }
    }

 2. **Upload Partner Data File**
    Place the partner’s raw data file in:
    ```
   /Volumes/data_catalog/source/assessment_volume/Healthcare_patners/
    ```

 3. **Register the Partner in the Notebook**
    acme_data_path = "/Volumes/data_catalog/source/assessment_volume/Healthcare_partners/acme.txt"
