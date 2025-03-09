# Purchasing Optimization

### Table of Contents

-[Project Overview](#project-overview)

-[Data Sources](#data-sources)

-[Tools](#tools)

-[Data Cleaning & Preparation](#data-cleaning--preparation)

-[Analysis](#analysis)

-[Results & Findings](#results--findings)

-[Next Steps](#next-steps)

### Project Overview
---
Given the numerous active vendor channels and rapidly fluctuating automotive part prices, it is essential to continuously monitor the latest pricing trends to maintain competitiveness. An SQL-based approach will be implemented to efficiently track, analyze, and update pricing data in real time. This solution will provide detailed analyses of pricing history for specific vendors, along with comprehensive price comparisons across all active vendors to optimize purchasing decisions. Additionally, we utilize MS Access to distribute insights, perform exploratory analyses, and effectively manage daily data requirements. Vendor names, part numbers, and pricing information have been anonymized to maintain confidentiality and protect proprietary data.

### Data Sources
---
**1.** Vendor A Quotes: The files "Vendor_A_Quote_03152024.csv", "Vendor_A_Quote_07262024.csv", "Vendor_A_Quote_11182024.csv", "Vendor_A_Quote_08292024.csv", "Vendor_A_Quote_01052025.csv", "Vendor_A_Quote_02212025.csv", and "Vendor_A_Quote_03052025.csv" contain columns for `Part#`, `VendorA_FOB`, and `Date`

**2.** Vendor B Quotes: The files "Vendor_B_Quote_04172024.csv", "Vendor_B_Quote_06192024.csv", "Vendor_B_Quote_11222024.csv", "Vendor_B_Quote_09102024.csv", "Vendor_B_Quote_01252025.csv", "Vendor_B_Quote_02282025.csv", and "Vendor_B_Quote_03062025.csv" contain columns for `Part#`, `VendorB_FOB`, and `Date`.

**3.** Vendor C Quotes: The files "Vendor_C_Quote_05242024.csv", "Vendor_C_Quote_07312024.csv", "Vendor_C_Quote_01142025.csv", "Vendor_C_Quote_02272025.csv", and "Vendor_C_Quote_03032025.csv" contain columns for `Part#`, `VendorC_FOB`, and `Date`.

**4.** Vendor D Quotes: The files "Vendor_D_Quote_03282024.csv", "Vendor_D_Quote_06122024.csv", "Vendor_D_Quote_10292024.csv", "Vendor_D_Quote_05182024.csv", "Vendor_D_Quote_07242024.csv", "Vendor_D_Quote_09262024.csv", "Vendor_D_Quote_01152025.csv", "Vendor_D_Quote_02282025.csv", and "Vendor_D_Quote_03062025.csv" contain columns for `Part#`, `VendorD_FOB`, and `Date`.

### Tools
---
- **MS SQL Server**: For data transformation and analysis.
- **MS Access**: To distribute insights, perform exploratory analyses.
- **MS Excel**: Pre-clean quote data

### Data Cleaning & Preparation
---
**1.** Each vendor quote is provided in Excel files with varying formats, layouts, and styles. Before proceeding, the relevant data—Part#, FOB cost, and Date—should be extracted using Excel and saved as CSV files for consistency and ease of processing. If the quote is coming from an EDI or an ERP system, the same applies—data should be extracted in a structured format and saved as CSVs for seamless analysis and integration.

**2.**  All the CSV files mentioned above need to be imported into SSMS. I have provided the import script for Vendor A as an example. Each quote from each vendor should be imported into the database in the same manner. The provided code imports the tables into a database called "Master".

```sql
USE Master;

CREATE TABLE Vendor_A_Quote_03152024 (
    PartNumber NVARCHAR(50),
    VendorA_FOB DECIMAL(10,2),
    Date DATE
);
BULK INSERT Vendor_A_Quote_03152024
FROM 'C:\CSV_Imports\Vendor_A_Quote_03152024.csv'
WITH (FORMAT = 'CSV', FIRSTROW = 2, FIELDTERMINATOR = ',', ROWTERMINATOR = '\n');

CREATE TABLE Vendor_A_Quote_07262024 (
    PartNumber NVARCHAR(50),
    VendorA_FOB DECIMAL(10,2),
    Date DATE
);
BULK INSERT Vendor_A_Quote_07262024
FROM 'C:\CSV_Imports\Vendor_A_Quote_07262024.csv'
WITH (FORMAT = 'CSV', FIRSTROW = 2, FIELDTERMINATOR = ',', ROWTERMINATOR = '\n');

CREATE TABLE Vendor_A_Quote_11182024 (
    PartNumber NVARCHAR(50),
    VendorA_FOB DECIMAL(10,2),
    Date DATE
);
BULK INSERT Vendor_A_Quote_11182024
FROM 'C:\CSV_Imports\Vendor_A_Quote_11182024.csv'
WITH (FORMAT = 'CSV', FIRSTROW = 2, FIELDTERMINATOR = ',', ROWTERMINATOR = '\n');

CREATE TABLE Vendor_A_Quote_08292024 (
    PartNumber NVARCHAR(50),
    VendorA_FOB DECIMAL(10,2),
    Date DATE
);
BULK INSERT Vendor_A_Quote_08292024
FROM 'C:\CSV_Imports\Vendor_A_Quote_08292024.csv'
WITH (FORMAT = 'CSV', FIRSTROW = 2, FIELDTERMINATOR = ',', ROWTERMINATOR = '\n');

CREATE TABLE Vendor_A_Quote_01052025 (
    PartNumber NVARCHAR(50),
    VendorA_FOB DECIMAL(10,2),
    Date DATE
);
BULK INSERT Vendor_A_Quote_01052025
FROM 'C:\CSV_Imports\Vendor_A_Quote_01052025.csv'
WITH (FORMAT = 'CSV', FIRSTROW = 2, FIELDTERMINATOR = ',', ROWTERMINATOR = '\n');

CREATE TABLE Vendor_A_Quote_02212025 (
    PartNumber NVARCHAR(50),
    VendorA_FOB DECIMAL(10,2),
    Date DATE
);
BULK INSERT Vendor_A_Quote_02212025
FROM 'C:\CSV_Imports\Vendor_A_Quote_02212025.csv'
WITH (FORMAT = 'CSV', FIRSTROW = 2, FIELDTERMINATOR = ',', ROWTERMINATOR = '\n');

CREATE TABLE Vendor_A_Quote_03052025 (
    PartNumber NVARCHAR(50),
    VendorA_FOB DECIMAL(10,2),
    Date DATE
);
BULK INSERT Vendor_A_Quote_03052025
FROM 'C:\CSV_Imports\Vendor_A_Quote_03052025.csv'
WITH (FORMAT = 'CSV', FIRSTROW = 2, FIELDTERMINATOR = ',', ROWTERMINATOR = '\n');
```

**3.**  After uploading all quote files from vendors, a comprehensive master quote table should be created for each vendor by systematically appending all received quotes. This process consolidates quotes from different dates into a single dataset, enabling seamless analysis, comparison, and informed decision-making. A master quotes table should be created for each vendor using the same approach.Future quotes can be incorporated into the master table using the INSERT INTO function in SQL, ensuring the dataset remains up to date for ongoing analysis and decision-making.

Below is the SQL code to append all quotes from different dates for Vendor A, creating the Vendor_A_Quotes_Master table:

```sql
USE Master;

CREATE TABLE Vendor_A_Quotes_Master (
    PartNumber NVARCHAR(50),
    VendorA_FOB DECIMAL(10,2),
    QuoteDate DATE,
    SourceTable NVARCHAR(50)
);

INSERT INTO Vendor_A_Quotes_Master (PartNumber, VendorA_FOB, QuoteDate, SourceTable)
SELECT PartNumber, VendorA_FOB, Date, 'Vendor_A_Quote_03152024' FROM Vendor_A_Quote_03152024
UNION ALL
SELECT PartNumber, VendorA_FOB, Date, 'Vendor_A_Quote_07262024' FROM Vendor_A_Quote_07262024
UNION ALL
SELECT PartNumber, VendorA_FOB, Date, 'Vendor_A_Quote_11182024' FROM Vendor_A_Quote_11182024
UNION ALL
SELECT PartNumber, VendorA_FOB, Date, 'Vendor_A_Quote_08292024' FROM Vendor_A_Quote_08292024
UNION ALL
SELECT PartNumber, VendorA_FOB, Date, 'Vendor_A_Quote_01052025' FROM Vendor_A_Quote_01052025
UNION ALL
SELECT PartNumber, VendorA_FOB, Date, 'Vendor_A_Quote_02212025' FROM Vendor_A_Quote_02212025
UNION ALL
SELECT PartNumber, VendorA_FOB, Date, 'Vendor_A_Quote_03052025' FROM Vendor_A_Quote_03052025;
```

The table below provides an example of the intended structure of the Vendor_A_Quotes_Master table. It consolidates all quote data from Vendor A over multiple dates.

| PartNumber | VendorA_FOB |  Date  |
|------------|------------|------------|
| P1001      | 120.19     | 2024-03-15 |
| P1001      | 119.58     | 2024-07-26 |
| P1001      | 121.34     | 2025-01-05 |
| P1002      | 210.45     | 2024-03-15 |
| P1002      | 209.78     | 2024-11-18 |
| P1002      | 212.36     | 2024-08-29 |
| P1002      | 210.89     | 2025-01-05 |
| P1003      | 175.32     | 2024-03-15 |
| P1003      | 176.89     | 2024-07-26 |
| P1003      | 177.12     | 2024-08-29 |
| P1003      | 175.98     | 2025-01-05 |

> **Note:** This table is a representative sample and does not include all records from the actual dataset. Part numbers and pricing information have been modified for confidentiality.

### Analysis

**1.** After consolidating all quotes for each vendor, the dataset needs further refinement to enhance visibility and improve price tracking. To achieve this, additional columns should be introduced to display dates more clearly, allowing for easier analysis of price changes over time. Additionally, two key columns should be created: `Active FOB`, which reflects the most relevant price, and `Most Recent Quote Date`, which captures the latest available quote for each part. These adjustments will ensure better data organization and facilitate more informed decision-making."










