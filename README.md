# LMS-Project-3---HR-Analytics-ETL
# Data Engineering Project: Enrollee Data Processing

## Project Overview

This project demonstrates a data engineering workflow to extract, clean, transform, and load data from various sources into a data warehouse. The goal is to consolidate information about potential course enrollees, their demographics, education, work experience, training hours, and city development index to enable further analysis.

## Data Sources

The project utilizes HR analytics data from the following sources (all the data is retrived from URL or automatically downloaded files when running the script):

- **Employed Data:** Loaded from a Google Sheet, containing basic enrollee information like ID, full name, city, and gender.
- **Enrollies' Education Data:** Loaded from an Excel file, containing enrollee ID, enrolled university, education level, and major discipline.
- **Enrollies' Work Experience Data:** Downloaded from a Google Drive link as a CSV file, containing enrollee ID, relevant experience, years of experience, company size, company type, and last new job information.
- **Training Hours Data:** Imported from a MySQL database table, containing enrollee ID and training hours.
- **City Development Index Data:** Scraped from an HTML table, containing city and its development index.
- **Employment Data:** Imported from a MySQL database table, containing enrollee ID and employment status.

## Steps and Decisions

The project follows these steps:

### 1. Data Extraction

Data is extracted from different sources using appropriate libraries and methods:

- **Google Sheet:** `pandas.read_excel` is used to read data directly from the shared Google Sheet URL.
- **Excel File:** `pandas.read_excel` is used to read data from the provided Excel file URL.
- **CSV File:** The `requests` library is used to download the CSV file from the Google Drive link, and then `pandas.read_csv` is used to read the downloaded file. This approach was chosen to handle the direct download from the provided URL.
- **MySQL Database:** `sqlalchemy` and `pymysql` are used to establish a connection to the MySQL database, and `pandas.read_sql_table` is used to read data from the specified tables.
- **HTML Table:** `pandas.read_html` is used to extract tables directly from the provided HTML page.

### 2. Data Cleaning

Each DataFrame is cleaned to handle missing values, incorrect data types, and inconsistent formats.

- **Enrollies Table:**
    - The `full_name` and `city` columns were converted to `string` dtype for better handling of textual data.
    - Missing values in the `gender` column were filled with 'other' and the column was converted to `category` dtype to represent the limited number of discrete gender options efficiently.
- **Enrollies' Education Data:**
    - `enrolled_university`, `major_discipline`, and `education_level` columns were converted to `string` dtype initially.
    - Missing values in `enrolled_university`, `major_discipline`, and `education_level` were filled with 'unknown'. `major_discipline` and `education_level` were then converted to `category` dtype as they represent categorical data.
- **Enrollies' Work Experience:**
    - All object type columns (`relevent_experience`, `experience`, `company_type`, `company_size`, `last_new_job`) were converted to `string` dtype for consistency and flexibility in handling various textual entries.
    - Missing values in `experience`, `company_type`, `company_size`, and `last_new_job` were filled. For `company_type` and `company_size`, the mode (most frequent value) was used to fill missing entries, assuming the most common category is a reasonable default. `company_type` was then converted to `category`. 'unknown' was used to fill missing values in `experience` and `last_new_job`.
- **City Development Index:**
    - The column names were renamed for clarity and consistency (`City` to `city` and `City Development Index` to `city_development_index`).
    - Both columns were converted to `string` dtype to ensure consistent data types before merging.
- **Training Hours Data:** No cleaning was required as the data was already in the correct format and had no missing values.
- **Employment Data:** No cleaning was required as the data was already in the correct format and had no missing values.

### 3. Data Merging

The cleaned DataFrames are merged into a single comprehensive DataFrame (`df`) based on the `enrollee_id` or `city` column using `left` joins. This ensures that all enrollee records are kept while bringing in related information from other tables.

### 4. Data Loading

The merged and individual cleaned DataFrames are loaded into a data warehouse hosted on a MySQL database.

- An SQLAlchemy engine is created to connect to the data warehouse database.
- The `to_sql` method is used to write each DataFrame to a corresponding table in the data warehouse. `if_exists='replace'` is used to overwrite the table if it already exists, which is suitable for this ETL process. `index=False` is used to prevent writing the DataFrame index as a column in the database table.

This structured approach ensures that data is extracted, cleaned, and loaded efficiently and effectively for further analysis and reporting.
