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

Below is the SQL code to append all quotes from different dates for Vendor A, creating the `Vendor_A_Quotes_Master` table:

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

> **Note:** This table is a representative sample and does not include all records from the actual dataset. Part numbers and pricing information have been modified for confidentiality.

### Analysis

**1.** After consolidating all vendor quotes, the dataset requires further refinement to enhance visibility and improve price tracking. To achieve this, additional columns will be introduced to display dates more clearly, making it easier to analyze price fluctuations over time. Key enhancements include:  

- **`Vendor_A_FOB_Active`**, which reflects the most up-to-date and relevant price.  
- **`Most_Recent_Date`**, which captures the latest available quote for each part.  
- **Dynamically generated columns based on the unique dates** available in the `Vendor_A_Quotes_Master` table, ensuring a more flexible and adaptive structure for tracking price changes.  

These improvements will enhance data organization and facilitate more informed decision-making.  

To implement these transformations, the `Vendor_A_Quotes_Master` table will serve as the foundation for structuring and refining the data. The result will be the `Vendor_A_FOB` table, which provides a streamlined view of the most relevant pricing information for Vendor A.  

To automate this process, a stored procedure will be developed to extract, transform, and load (ETL) the necessary data from the `Vendor_A_Quotes_Master` table. This stored procedure will keep the `Vendor_A_FOB` table continuously updated with the latest pricing information, improving efficiency, data accuracy, and price tracking over time.

The following SQL code create stored procedure called `Vendor_A_FOB_Update` responsible for generating and maintaining the `Vendor_A_FOB` table:

> **Note:** Each stored procedure for each vendor should be created in the same manner

```sql
USE [Master]
GO

SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO

-- Creating a new stored procedure named Vendor_A_FOB_Update
CREATE PROCEDURE [dbo].[Vendor_A_FOB_Update]
AS
BEGIN
    -- Prevents extra result sets from interfering with SELECT statements
    SET NOCOUNT ON;

    -- Declaring variables to store dynamic SQL query
    DECLARE @columns NVARCHAR(MAX), @sql NVARCHAR(MAX);

    -- Dynamically generating column names for pivoting, based on unique dates in Vendor_A_Quotes_Master
    SELECT @columns = STRING_AGG(QUOTENAME(CONVERT(VARCHAR(10), Date, 120) + '_FOB'), ',')
    FROM (SELECT DISTINCT Date FROM Vendor_A_Quotes_Master) AS Dates;

    -- Constructing the dynamic SQL query
    SET @sql = '
    -- Check if the table Vendor_A_FOB already exists, and drop it if it does
    IF OBJECT_ID(''dbo.Vendor_A_FOB'', ''U'') IS NOT NULL
        DROP TABLE dbo.Vendor_A_FOB;

    -- Using a Common Table Expression (CTE) to assign row numbers for ranking
    WITH RankedData AS (
        SELECT 
            PartNumber,  -- The unique identifier for parts
            VendorA_FOB, -- The FOB (Freight On Board) cost from Vendor A
            Date,        -- Date of the FOB entry
            ROW_NUMBER() OVER (PARTITION BY PartNumber ORDER BY Date DESC) AS rn
            -- Assigns a row number for each PartNumber, ordering by Date in descending order
            -- The most recent date will have rn = 1
        FROM 
            Vendor_A_Quotes_Master
    )
    -- Creating the final result table Vendor_A_FOB
    SELECT 
        r.PartNumber,           -- The part number
        r.VendorA_FOB AS FOB_VendorA_Active, -- The latest FOB price from Vendor A
        r.Date AS Most_Recent_Date,          -- The most recent date for the FOB price
        ' + @columns + '                     -- Dynamically generated columns for pivoted FOB values
    INTO dbo.Vendor_A_FOB  -- Saving results into a new table
    FROM 
        RankedData r
    LEFT JOIN 
        (SELECT 
             PartNumber, 
             VendorA_FOB, 
             CONVERT(VARCHAR(10), Date, 120) + ''_FOB'' AS Date_FOB
             -- Creating a new column Date_FOB in format YYYY-MM-DD_FOB
         FROM Vendor_A_Quotes_Master) AS SourceTable
    PIVOT
        (MAX(VendorA_FOB) FOR Date_FOB IN (' + @columns + ')) AS PivotTable
        -- Pivoting FOB values based on dates, so each date becomes a separate column
    ON r.PartNumber = PivotTable.PartNumber
    WHERE 
        r.rn = 1  -- Ensures that we only take the most recent FOB price per PartNumber
    ORDER BY 
        r.PartNumber;  -- Sorting the results by PartNumber
    ';

    -- Executing the dynamically constructed SQL query
    EXEC sp_executesql @sql;
END;
GO
```
**2.** Executing above code will generate the stored procedure 'Vendor_A_FOB_Update', In SSMS, you can find stored procedures by expanding Databases > [Your Database] > Programmability > Stored Procedures in the Object Explorer, or by running SELECT name FROM sys.procedures; to list them. To create the Vendor_A_FOB table, execute the stored procedure using the following command:

```sql
EXECUTE Vendor_A_FOB_Update;
```
> **Note:** Each stored procedure should be executed for each vendor in the same manner

Upon execution, the Vendor_A_FOB table will be created in the database with the following structure:

| PartNumber | FOB_Heb_Active | Most_Recent_Date | 2024-03-15_FOB | 2024-07-26_FOB | 2024-08-29_FOB | 2024-11-18_FOB | 2025-01-05_FOB |
|------------|---------------|------------------|----------------|----------------|----------------|----------------|----------------|
| P1001      | 121.34        | 2025-01-05       | 120.19         | 119.58         | NaN            | NaN            | 121.34         |
| P1002      | 210.89        | 2025-01-05       | 210.45         | NaN            | 212.36         | 209.78         | 210.89         |
| P1003      | 177.12        | 2024-08-29       | 175.32         | 176.89         | 177.12         | NaN            | NaN            |

> **Note:** This table is a representative sample and does not include all records from the actual dataset. Part numbers and pricing information have been modified for confidentiality.

**3.** After generating the Vendor FOB tables, which include the Active FOB price along with historical quote data, I will optimize purchasing decisions based on the pricing information provided by vendors. This approach will ensure more strategic and cost-effective sourcing decisions.

FOB (Free On Board) represents the cost of goods up to the point where they are loaded onto a shipping vessel at the origin port. It covers manufacturing costs, transportation to the port, export customs clearance, and loading fees. Once the goods are on board, responsibility and costs shift to the buyer, who must then manage freight charges, insurance, import duties, taxes, and inland transportation. While FOB pricing offers an initial snapshot of product costs, it does not account for the significant expenses incurred in shipping and importing the goods to their final destination.

In contrast, LDP (Landed Duty Paid) encompasses all costs required to deliver goods to the buyer’s location. This includes shipping, insurance, import duties, customs fees, and inland transportation. For purchasing decisions, LDP is a more critical factor, as it reflects the true total cost of acquiring goods—capturing all hidden and variable expenses. This comprehensive view is essential for accurate pricing strategies and profit margin calculations. While FOB prices may initially seem lower, they can overlook substantial costs that arise later, leading to potential financial surprises. Therefore, considering LDP costs ensures more accurate financial planning, better forecasting, and smarter decision-making in global sourcing.

Moreover, LDP costs can vary significantly depending on the country of import. Different countries have unique import regulations, tariff rates, customs procedures, and tax policies, all of which directly impact the final landed cost. For instance, countries with high import duties, stricter customs regulations, or additional local taxes will naturally result in higher LDP costs. Additionally, factors like currency fluctuations, fuel surcharges, and varying transportation infrastructure can influence shipping and inland transportation expenses. Understanding these country-specific variables is essential for accurate cost estimation and strategic purchasing. By factoring in these differences, I can better assess total costs and negotiate more effectively with vendors, ensuring a more efficient and profitable sourcing process.























