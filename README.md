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

| PartNumber | FOB_VendorA_Active | Most_Recent_Date | 2024-03-15_FOB | 2024-07-26_FOB | 2024-08-29_FOB | 2024-11-18_FOB | 2025-01-05_FOB |
|------------|---------------|------------------|----------------|----------------|----------------|----------------|----------------|
| P1001      | 121.34        | 2025-01-05       | 120.19         | 119.58         | NaN            | NaN            | 121.34         |
| P1002      | 210.89        | 2025-01-05       | 210.45         | NaN            | 212.36         | 209.78         | 210.89         |
| P1003      | 177.12        | 2024-08-29       | 175.32         | 176.89         | 177.12         | NaN            | NaN            |

> **Note:** This table is a representative sample and does not include all records from the actual dataset. Part numbers and pricing information have been modified for confidentiality.

**3.** After generating the Vendor FOB tables, which include the Active FOB price along with historical quote data, I will optimize purchasing decisions based on the pricing information provided by vendors. This approach will ensure more strategic and cost-effective sourcing decisions.

FOB (Free On Board) represents the cost of goods up to the point where they are loaded onto a shipping vessel at the origin port. It covers manufacturing costs, transportation to the port, export customs clearance, and loading fees. Once the goods are on board, responsibility and costs shift to the buyer, who must then manage freight charges, insurance, import duties, taxes, and inland transportation. While FOB pricing offers an initial snapshot of product costs, it does not account for the significant expenses incurred in shipping and importing the goods to their final destination.

In contrast, LDP (Landed Duty Paid) encompasses all costs required to deliver goods to the buyer’s location. This includes shipping, insurance, import duties, customs fees, and inland transportation. For purchasing decisions, LDP is a more critical factor, as it reflects the true total cost of acquiring goods—capturing all hidden and variable expenses. This comprehensive view is essential for accurate pricing strategies and profit margin calculations. While FOB prices may initially seem lower, they can overlook substantial costs that arise later, leading to potential financial surprises. Therefore, considering LDP costs ensures more accurate financial planning, better forecasting, and smarter decision-making in global sourcing.

LDP costs can vary significantly depending on the country of import. Different countries have unique import regulations, tariff rates, customs procedures, and tax policies, all of which directly impact the final landed cost. For instance, countries with high import duties, stricter customs regulations, or additional local taxes will naturally result in higher LDP costs. Additionally, factors like currency fluctuations, fuel surcharges, and varying transportation infrastructure can influence shipping and inland transportation expenses. Understanding these country-specific variables is essential for accurate cost estimation and strategic purchasing. By factoring in these differences, I can better assess total costs and negotiate more effectively with vendors, ensuring a more efficient and profitable sourcing process.

The table below presents an example of an imported SKU, illustrating how the LDP (Landed Duty Paid) cost can significantly impact purchasing decisions. While Vendor A initially offers the lowest FOB (Free On Board) cost, they ultimately present the highest LDP cost due to higher associated expenses such as import duties, freight, insurance, and inland transportation. This demonstrates how focusing solely on FOB pricing can be misleading, as it fails to account for the full range of costs required to bring the product to its final destination.

In this specific case, the SKU will be procured from Vendor C, who, despite quoting the second-best FOB cost, offers the lowest LDP cost. This lower LDP is attributed to more favorable logistics costs, reduced import duties, and efficient transportation charges, which collectively result in a more cost-effective solution for the company.

This example highlights the importance of evaluating LDP costs rather than relying solely on FOB pricing. By considering the complete cost structure—including all expenses involved in shipping, customs, and delivery—better purchasing decisions can be made. Ultimately, choosing Vendor C ensures that the company minimizes its overall landed costs, thereby enhancing profitability and supply chain efficiency.

| **Cost Component**   | **VendorA <br> (Country A)** | **VendorB <br>(Country B)** | **VendorC <br> (Country C)** |
|----------------------|--------------|--------------|--------------|
| FOB Cost         | $90          | $100         | $95          |
| Freight Cost     | $30          | $20          | $18          |
| Insurance        | $6           | $5           | $4           |
| Import Duty      | $40          | $25          | $30          |
| Customs Fees     | $8           | $6           | $4           |
| VAT/Taxes        | $20          | $15          | $12          |
| Inland Transport | $15          | $12          | $11          |
| **Total LDP Cost**   | **$209**     | **$183**     | **$175**     |

Due to the complex and fluctuating nature of LDP (Landed Duty Paid) costs—such as variations in freight charges, import duties, customs fees, and taxes—a dedicated **"LDP Factor"** is assigned to each vendor within SQL. The data of LDP factors are stored in `LDP_Factors` table. Changes in LDP factors are informed by purchasing department whenever a change of importing cost is recognized. This approach enables easier, faster, and real-time manipulation of cost data, ensuring more efficient analysis and decision-making.

In this specific sample case, the LDP factor—which reflects the ratio of LDP cost to FOB cost—varies across vendors. Vendor A has the highest LDP factor at approximately 2.32, indicating higher additional costs beyond the initial FOB price. Vendor B and Vendor C have lower LDP factors of 1.83 and 1.84 respectively, suggesting more favorable landed costs. These factors provide valuable insights for evaluating and optimizing purchasing decisions based on total landed costs. Here is a sample of the `LDP_Factors` table, illustrating the data structure:

| **Vendor** | **LDP Factor** |
|------------|----------------|
| Vendor A |   2.32         |
| Vendor B |   1.83         |
| Vendor C |   1.84         |

> **Note:** This table is a representative sample and does not include all records from the actual dataset.Information have been modified for confidentiality.

The following query retrieves all unique part numbers from the Vendor_A_FOB, Vendor_B_FOB, and Vendor_C_FOB tables. It calculates the LDP costs by applying the corresponding factors from the LDP_Factors table. The query then compares the LDP costs across vendors, identifies the minimum LDP cost for each part number, and displays the vendor offering the lowest cost.
To enhance efficiency and simplify future updates, I recommend creating a stored procedure that creates a `Vendor_LDP` table for this process. This will allow for easy re-execution of the query as new data becomes available, ensuring consistency and reducing manual effort.

```sql
SELECT
    COALESCE(a.PartNumber, b.PartNumber, c.PartNumber) AS PartNumber,

    -- Original FOB Prices
    a.FOB_VendorA_Active,
    b.FOB_VendorB_Active,
    c.FOB_VendorC_Active,

    -- LDP Factors
    fa.LDP_Factor AS LDP_Factor_VendorA,
    fb.LDP_Factor AS LDP_Factor_VendorB,
    fc.LDP_Factor AS LDP_Factor_VendorC,

    -- LDP Costs
    a.FOB_VendorA_Active * fa.LDP_Factor AS LDP_VendorA,
    b.FOB_VendorB_Active * fb.LDP_Factor AS LDP_VendorB,
    c.FOB_VendorC_Active * fc.LDP_Factor AS LDP_VendorC,

    -- Minimum LDP Cost
    LEAST(
        a.FOB_VendorA_Active * fa.LDP_Factor,
        b.FOB_VendorB_Active * fb.LDP_Factor,
        c.FOB_VendorC_Active * fc.LDP_Factor
    ) AS Min_LDP_Cost,

    -- Vendor with Minimum LDP Cost
    CASE 
        WHEN (a.FOB_VendorA_Active * fa.LDP_Factor) = LEAST(
            a.FOB_VendorA_Active * fa.LDP_Factor,
            b.FOB_VendorB_Active * fb.LDP_Factor,
            c.FOB_VendorC_Active * fc.LDP_Factor
        ) THEN 'Vendor A'
        WHEN (b.FOB_VendorB_Active * fb.LDP_Factor) = LEAST(
            a.FOB_VendorA_Active * fa.LDP_Factor,
            b.FOB_VendorB_Active * fb.LDP_Factor,
            c.FOB_VendorC_Active * fc.LDP_Factor
        ) THEN 'Vendor B'
        WHEN (c.FOB_VendorC_Active * fc.LDP_Factor) = LEAST(
            a.FOB_VendorA_Active * fa.LDP_Factor,
            b.FOB_VendorB_Active * fb.LDP_Factor,
            c.FOB_VendorC_Active * fc.LDP_Factor
        ) THEN 'Vendor C'
        ELSE NULL
    END AS Min_LDP_Vendor

FROM 
    master.dbo.Vendor_A_FOB AS a
FULL OUTER JOIN 
    master.dbo.Vendor_B_FOB AS b ON a.PartNumber = b.PartNumber
FULL OUTER JOIN 
    master.dbo.Vendor_C_FOB AS c ON COALESCE(a.PartNumber, b.PartNumber) = c.PartNumber

-- Joining with LDP Factors
LEFT JOIN master.dbo.LDP_factors AS fa ON fa.Vendor = 'Vendor A'
LEFT JOIN master.dbo.LDP_factors AS fb ON fb.Vendor = 'Vendor B'
LEFT JOIN master.dbo.LDP_factors AS fc ON fc.Vendor = 'Vendor C';
```
The output of the query shows that Vendor C offers the lowest LDP cost for P1001, Vendor B for P1002, and Vendor A for P1003. These variations result from differences in FOB prices and LDP factors. By selecting vendors based on the minimum LDP cost for each SKU, purchasing decisions can be optimized for greater cost efficiency.

| PartNumber | Min_LDP_Cost | Min_LDP_Vendor | LDP_VendorA | LDP_VendorB | LDP_VendorC | FOB_VendorA_Active | FOB_VendorB_Active | FOB_VendorC_Active | LDP_Factor_VendorA | LDP_Factor_VendorB | LDP_Factor_VendorC |
|------------|--------------|----------------|-------------|-------------|-------------|--------------------|--------------------|--------------------|--------------------|--------------------|--------------------|
| P1001      | 281.03       | Vendor C       | 281.51      | 282.87      | 281.03      | 121.34             | 154.03             | 152.73             | 2.32               | 1.83               | 1.84               |
| P1002      | 439.84       | Vendor B       | 489.27      | 439.84      | 488.72      | 210.89             | 240.35             | 265.61             | 2.32               | 1.83               | 1.84               |
| P1003      | 410.91       | Vendor A       | 410.91      | 412.43      | 411.24      | 177.12             | 225.37             | 223.28             | 2.32               | 1.83               | 1.84               |

> **Note:** This table is a representative sample and does not include all records from the actual dataset.Information have been modified for confidentiality.
---

### Results & Findings
---

In a **big data environment**, optimizing purchasing decisions requires managing large datasets, analyzing historical trends, and ensuring real-time responsiveness. The developed algorithm is designed to handle these complexities by dynamically calculating costs, comparing historical trends, and automating decision-making processes.

---

#### **Algorithm for Big Data Purchasing Optimization**

1. **Comprehensive Data Aggregation**:  
   The process begins by aggregating extensive historical and current **FOB (Free On Board)** pricing data from all vendor tables. Historical quote data is processed to extract both the **latest active FOB cost** and past prices, ensuring comprehensive visibility into price trends for each part number.

2. **Historical Price Comparison**:  
   The algorithm captures and analyzes historical FOB prices for each part number across different time periods. This allows for:
   - **Trend Analysis**: Identifying patterns in price fluctuations over time.  
   - **Vendor Consistency**: Assessing which vendors offer stable pricing versus those with volatile cost changes.  
   - **Strategic Forecasting**: Enabling predictive insights for future procurement strategies based on historical price movements.  

   This historical context ensures that purchasing decisions are informed not only by current prices but also by trends that may impact long-term cost management.

3. **Dynamic and Scalable LDP Cost Calculation**:  
   For each part number, the algorithm calculates the **LDP (Landed Duty Paid) cost** by multiplying the active FOB price by the corresponding **LDP factor**. These factors, reflecting additional costs like freight, insurance, import duties, and inland transportation, are maintained in the `LDP_Factors` table. The dynamic linkage ensures that any changes to LDP factors are automatically reflected in real-time cost calculations.

4. **Cross-Vendor and Historical Cost Comparison**:  
   The algorithm compares LDP costs across all vendors for each part number, identifying the **minimum LDP cost** and the corresponding vendor. Additionally, it contrasts current LDP costs with historical trends to identify:
   - **Cost Anomalies**: Detecting significant deviations from historical averages.  
   - **Vendor Reliability**: Highlighting vendors that consistently maintain competitive costs over time.  
   - **Strategic Opportunities**: Recognizing when historical lows or highs suggest negotiation opportunities.  

5. **Optimized Result Structuring**:  
   The final dataset is structured for **big data consumption**, including:
   - **Part Numbers** for unique identification.  
   - **Historical and Active FOB Prices** for trend analysis.  
   - **LDP Factors** dynamically linked to current data.  
   - **Calculated LDP Costs** for each vendor.  
   - The **Minimum LDP Cost** and corresponding **vendor**, ensuring clear purchasing guidance.

6. **Automated Process for Scalability**:  
   To manage the scale and complexity of big data, it is recommended to implement a **stored procedure**. This automated process enables:
   - Continuous updates as new pricing data is received.  
   - Instant recalculation of historical and active LDP costs.  
   - Consistent, repeatable analyses without manual intervention.  
   - Real-time insights to support agile and informed purchasing decisions.

---

#### Why This Approach is Optimized for Big Data and Historical Analysis

- **Scalability**: The algorithm is designed to process large datasets efficiently, ensuring consistent performance as data volumes grow.  
- **Historical Context**: Analyzing historical price trends provides deeper insights into vendor behavior, pricing patterns, and future cost forecasting.  
- **Dynamic Cost Management**: Real-time updates to LDP factors ensure that cost calculations are always accurate and reflect current market conditions.  
- **Automated Decision-Making**: Stored procedures streamline data processing, minimize manual intervention, and allow for rapid decision-making.  
- **Strategic Flexibility**: By comparing current and historical trends, purchasing strategies can be adjusted proactively to maximize cost savings.

---

#### **Strategic Value of Historical Price Comparison in Big Data**

- **Trend Identification**: Understanding how prices have fluctuated over time enables better negotiation strategies and vendor evaluations.  
- **Cost Stability Analysis**: Vendors with consistent pricing histories can be prioritized to reduce long-term procurement risks.  
- **Forecasting and Planning**: Historical data provides valuable insights for forecasting future price movements, aiding in budgeting and strategic planning.  
- **Negotiation Leverage**: Recognizing when current prices deviate from historical norms creates opportunities for cost negotiations.  

---

This **big data-optimized approach**, enhanced by historical price comparisons, ensures that purchasing decisions are **data-driven, strategically informed, and cost-effective**. It empowers organizations to manage large datasets efficiently while maintaining the flexibility to adapt to changing market conditions and historical trends.

### Next Steps
---

To further enhance the purchasing optimization process and ensure long-term scalability, the following strategic next steps are recommended:

1. **Automate LDP Factor Updates**:  
   Establish a process for automatically updating the `LDP_Factors` table based on real-time changes in shipping rates, customs fees, and other related costs. This will ensure continuous accuracy in LDP cost calculations.

2. **Implement Regular Data Refresh Schedules**:  
   Set up automated schedules to refresh vendor quote data, ensuring the system always reflects the most recent FOB prices and historical trends.

3. **Utilize MS Access for Exploratory Analysis**:  
   Leverage **MS Access** as a flexible and accessible tool for exploratory data analysis and validation. It can be used to perform ad-hoc queries, analyze specific vendor pricing trends, and provide quick data insights. MS Access also offers an interface for non-technical stakeholders to view summarized data, facilitating easier collaboration and faster decision-making.

4. **Develop Dynamic Reporting Dashboards**:  
   Create interactive dashboards using tools like **Power BI** or **Tableau** for real-time visualization of price trends, LDP cost comparisons, and vendor performance. This will improve decision-making efficiency and stakeholder engagement.

5. **Integrate Predictive Analytics**:  
   Leverage machine learning models to forecast future pricing trends based on historical data and market indicators. This will aid in proactive budgeting and negotiation strategies.

6. **Enhance Stored Procedure Scalability**:  
   Continuously monitor and optimize the stored procedures for performance improvements as data volumes increase, ensuring they remain efficient in processing large datasets.

7. **Vendor Performance Monitoring**:  
   Establish KPIs to track vendor pricing consistency, lead times, and delivery performance. This will support better long-term supplier relationship management.

8. **Conduct Periodic Cost Reviews**:  
   Schedule periodic reviews of LDP costs, comparing them against market benchmarks to ensure procurement strategies remain competitive.

9. **Expand Data Sources**:  
   Explore additional data sources such as market indexes, logistics rates, and global trade statistics to enhance the accuracy and depth of cost analyses.

10. **Strengthen Data Governance**:  
   Implement data validation and governance protocols to ensure data integrity, reduce discrepancies, and maintain a high level of data accuracy over time.

11. **Training and Knowledge Sharing**:  
    Provide training for the procurement and data analysis teams to ensure they are proficient in using the tools, understanding the analytics, and interpreting results for strategic decision-making.

---

By following these next steps, the purchasing optimization process will become more automated, data-driven, and scalable—ensuring cost-effective decisions while maintaining flexibility in adapting to market dynamics and future growth. Leveraging tools like **MS Access** for exploratory analysis will enhance collaboration, facilitate faster insights, and provide greater accessibility for non-technical teams.
