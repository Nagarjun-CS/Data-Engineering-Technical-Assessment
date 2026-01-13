# Data-Engineering-Technical-Assessment

## Overview

This pipeline ingests and standardizes healthcare eligibility data from multiple partner files (CSV or pipe-delimited .txt), each with their own schema and date format. The process is configuration-driven using partner-specific JSON files and handles:

- Column renaming and type standardization
 
- Phone/email formatting

- Date of birth parsing with error handling

- Invalid record logging (e.g., malformed DOBs)

- Merging all cleaned datasets into a unified DataFrame

Each data record is tagged with its partner_code, and any rows with invalid or missing dob values are saved separately per partner for follow-up.
