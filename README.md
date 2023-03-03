# EC_IT143_W9.2_sp500-constituent_usm.sql
/*****************************************************************************************************************
NAME:    EC_IT143_W9.2_sp500-constituent_usm.sql
PURPOSE: To create question frpm dataset and answer it---

MODIFICATION LOG:
Ver      Date        Author        Description
-----   ----------   -----------   -------------------------------------------------------------------------------
1.0     03/03/2023   Uduak Mbaba       1. Built this script for EC IT143


RUNTIME: 
Xm Xs

NOTES: 

 
******************************************************************************************************************/

-- Q1: What is the average number of years that companies remain in the S&P 500 before being removed, and how does this vary by industry?
-- A1: Let's query the dataset for answer----

          SELECT Industry, AVG(DATEDIFF(`Date added`, `Date removed`)) AS `Avg Years in S&P 500`
          FROM sp500_constituent
          WHERE `Date removed` IS NOT NULL
          GROUP BY Industry
          ORDER BY `Avg Years in S&P 500` DESC;

--- Create an Ad Hoc SQL query

          SELECT Industry, AVG(DATEDIFF(`Date added`, `Date removed`)) AS `Avg Years in S&P 500`
          FROM sp500_constituent
          WHERE `Date removed` IS NOT NULL
          GROUP BY Industry
          ORDER BY `Avg Years in S&P 500` DESC;

--- Turn the Ad Hoc SQL query into a View

          CREATE VIEW avg_years_in_sp500 AS
          SELECT Industry, AVG(DATEDIFF(`Date added`, `Date removed`)) AS `Avg Years in S&P 500`
          FROM sp500_constituent
          WHERE `Date removed` IS NOT NULL
          GROUP BY Industry
          ORDER BY `Avg Years in S&P 500` DESC;

--- Turn the View into a Table

          CREATE TABLE avg_years_in_sp500 (
            Industry VARCHAR(255) NOT NULL,
            `Avg Years in S&P 500` FLOAT NOT NULL,
            CONSTRAINT pk_avg_years_in_sp500 PRIMARY KEY (Industry)
          );

          INSERT INTO avg_years_in_sp500 (Industry, `Avg Years in S&P 500`)
          SELECT Industry, AVG(DATEDIFF(`Date added`, `Date removed`)) AS `Avg Years in S&P 500`
          FROM sp500_constituent
          WHERE `Date removed` IS NOT NULL
          GROUP BY Industry
          ORDER BY `Avg Years in S&P 500` DESC;

--- Load the Table from the View using an Ad Hoc SQL script

          INSERT INTO avg_years_in_sp500 (Industry, `Avg Years in S&P 500`)
          SELECT Industry, `Avg Years in S&P 500`
          FROM avg_years_in_sp500;

--- Turn the Ad Hoc SQL script in a Stored Procedure

          CREATE PROCEDURE get_avg_years_in_sp500()
          BEGIN
            SELECT Industry, AVG(DATEDIFF(`Date added`, `Date removed`)) AS `Avg Years in S&P 500`
            FROM sp500_constituent
            WHERE `Date removed` IS NOT NULL
            GROUP BY Industry
            ORDER BY `Avg Years in S&P 500` DESC;
          END;

--- Call the Stored Procedure

          CALL get_avg_years_in_sp500();
          
          
          
 /*****************************************************************************************************************
NAME:    EC_IT143_W9.2_sp500-constituent_usm.sql
PURPOSE: To create question frpm dataset and answer it---

MODIFICATION LOG:
Ver      Date        Author        Description
-----   ----------   -----------   -------------------------------------------------------------------------------
1.0     03/03/2023   Uduak Mbaba       1. Built this script for EC IT143


RUNTIME: 
Xm Xs

NOTES: 
This is where I talk about what this script is, why I built it, and other stuff...
 
******************************************************************************************************************/

-- Q2: What is the average number of companies that have been added to the S&P 500 each year since its inception, and how has this trend changed over time?
-- A2: Let's query the dataset for answer----

        SELECT YEAR(`Date added`) AS `Year Added`, COUNT(*) AS `Num Companies Added`,
               AVG(COUNT(*)) OVER (ORDER BY `Year Added` ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS `Avg Companies Added`
        FROM sp500_constituent
        GROUP BY `Year Added`
        ORDER BY `Year Added` ASC;
        
--- Create an Ad Hoc SQL query

          SELECT YEAR(`Date added`) AS `Year Added`, COUNT(*) AS `Num Companies Added`,
                 AVG(COUNT(*)) OVER (ORDER BY `Year Added` ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS `Avg Companies Added`
          FROM sp500_constituent
          GROUP BY `Year Added`
          ORDER BY `Year Added` ASC;

--- Turn the Ad Hoc SQL query into a View

          CREATE VIEW sp500_companies_added AS
          SELECT YEAR(`Date added`) AS `Year Added`, COUNT(*) AS `Num Companies Added`,
                 AVG(COUNT(*)) OVER (ORDER BY `Year Added` ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS `Avg Companies Added`
          FROM sp500_constituent
          GROUP BY `Year Added`
          ORDER BY `Year Added` ASC;

--- Turn the View into a Table

          CREATE TABLE sp500_companies_added (
            `Year Added` INT NOT NULL,
            `Num Companies Added` INT NOT NULL,
            `Avg Companies Added` FLOAT NOT NULL,
            CONSTRAINT pk_sp500_companies_added PRIMARY KEY (`Year Added`)
          );

          INSERT INTO sp500_companies_added (`Year Added`, `Num Companies Added`, `Avg Companies Added`)
          SELECT `Year Added`, `Num Companies Added`, `Avg Companies Added`
          FROM sp500_companies_added;

--- Load the Table from the View using an Ad Hoc SQL script

          INSERT INTO sp500_companies_added (`Year Added`, `Num Companies Added`, `Avg Companies Added`)
          SELECT `Year Added`, `Num Companies Added`, `Avg Companies Added`
          FROM sp500_companies_added;

--- Turn the Ad Hoc SQL script in a Stored Procedure

          CREATE PROCEDURE get_sp500_companies_added()
          BEGIN
            SELECT `Year Added`, `Num Companies Added`, `Avg Companies Added`
            FROM sp500_companies_added;
          END;

--- Call the Stored Procedure

          CALL get_sp500_companies_added();




/*****************************************************************************************************************
NAME:    EC_IT143_W9.2_sp500-constituent_usm.sql
PURPOSE: To create question frpm dataset and answer it---

MODIFICATION LOG:
Ver      Date        Author        Description
-----   ----------   -----------   -------------------------------------------------------------------------------
1.0     03/03/2023   Uduak Mbaba       1. Built this script for EC IT143


RUNTIME: 
Xm Xs

NOTES: 
This is where I talk about what this script is, why I built it, and other stuff...
 
******************************************************************************************************************/

-- Q3: Which companies have remained in the S&P 500 for the longest consecutive period of time, and what factors might account for their sustained success?
-- A3: Let's query the dataset for answer----

          SELECT Name, `Date added`, `Date removed`, 
                 DATEDIFF(MIN(`Date added`) OVER (PARTITION BY Ticker), `Date added`) + 1 AS `Consecutive Years in S&P 500`
          FROM sp500_constituent
          WHERE `Date removed` IS NULL
          ORDER BY `Consecutive Years in S&P 500` DESC
          LIMIT 10;

--- Create an Ad Hoc SQL query

          SELECT Name, `Date added`, `Date removed`, 
                 DATEDIFF(MIN(`Date added`) OVER (PARTITION BY Ticker), `Date added`) + 1 AS `Consecutive Years in S&P 500`
          FROM sp500_constituent
          WHERE `Date removed` IS NULL
          ORDER BY `Consecutive Years in S&P 500` DESC
          LIMIT 10;

--- Turn the Ad Hoc SQL query into a View

          CREATE VIEW top_ten_consecutive_years AS
          SELECT Name, `Date added`, `Date removed`, 
                 DATEDIFF(MIN(`Date added`) OVER (PARTITION BY Ticker), `Date added`) + 1 AS `Consecutive Years in S&P 500`
          FROM sp500_constituent
          WHERE `Date removed` IS NULL
          ORDER BY `Consecutive Years in S&P 500` DESC
          LIMIT 10;

--- Turn the View into a Table

          CREATE TABLE top_ten_consecutive_years_tbl (
              Name VARCHAR(255),
              `Date added` DATE,
              `Date removed` DATE,
              `Consecutive Years in S&P 500` INT,
              PRIMARY KEY (`Name`, `Date added`)
          );

--- Load the Table from the View using an Ad Hoc SQL script

          INSERT INTO top_ten_consecutive_years_tbl
          SELECT *
          FROM top_ten_consecutive_years;

--- Turn the Ad Hoc SQL script in a Stored Procedure

          DELIMITER //
          CREATE PROCEDURE load_top_ten_consecutive_years_tbl()
          BEGIN
              CREATE VIEW top_ten_consecutive_years AS
              SELECT Name, `Date added`, `Date removed`, 
                     DATEDIFF(MIN(`Date added`) OVER (PARTITION BY Ticker), `Date added`) + 1 AS `Consecutive Years in S&P 500`
              FROM sp500_constituent
              WHERE `Date removed` IS NULL
              ORDER BY `Consecutive Years in S&P 500` DESC
              LIMIT 10;

              CREATE TABLE top_ten_consecutive_years_tbl (
                  Name VARCHAR(255),
                  `Date added` DATE,
                  `Date removed` DATE,
                  `Consecutive Years in S&P 500` INT,
                  PRIMARY KEY (`Name`, `Date added`)
              );
    
                INSERT INTO top_ten_consecutive_years_tbl
                SELECT *
                FROM top_ten_consecutive_years;
            END //
            DELIMITER ;

--- Call the Stored Procedure

            CALL load_top_ten_consecutive_years_tbl();




/*****************************************************************************************************************
NAME:    EC_IT143_W9.2_sp500-constituent_usm.sql
PURPOSE: To create question frpm dataset and answer it---
MODIFICATION LOG:
Ver      Date        Author        Description
-----   ----------   -----------   -------------------------------------------------------------------------------
1.0     03/03/2023   Uduak Mbaba       1. Built this script for EC IT143


RUNTIME: 
Xm Xs

NOTES: 
This is where I talk about what this script is, why I built it, and other stuff...
 
******************************************************************************************************************/

-- Q4: What is the total number of companies added to the S&P 500 each year, and what is the average number of companies added per year? Which year had the highest and ---lowest number of companies added to the S&P 500?
-- A4: Let's query the dataset for answer----

          SELECT YEAR(`Date added`) AS `Year Added`, COUNT(*) AS `Num Companies Added`,
                 AVG(COUNT(*)) OVER (ORDER BY `Year Added` ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS `Avg Companies Added`
          FROM sp500_constituent
          GROUP BY `Year Added`
          ORDER BY `Year Added` ASC;

--- Create an Ad Hoc SQL query

          SELECT YEAR(`Date added`) AS `Year Added`, COUNT(*) AS `Num Companies Added`,
                 AVG(COUNT(*)) OVER (ORDER BY `Year Added` ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS `Avg Companies Added`
          FROM sp500_constituent
          GROUP BY `Year Added`
          ORDER BY `Year Added` ASC;

--- Turn the Ad Hoc SQL query into a View

          CREATE VIEW sp500_companies_added_per_year AS
          SELECT YEAR(`Date added`) AS `Year Added`, COUNT(*) AS `Num Companies Added`,
                 AVG(COUNT(*)) OVER (ORDER BY `Year Added` ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS `Avg Companies Added`
          FROM sp500_constituent
          GROUP BY `Year Added`
          ORDER BY `Year Added` ASC;

--- Turn the View into a Table

            CREATE TABLE sp500_companies_added_per_year_table (
              `Year Added` INT PRIMARY KEY,
              `Num Companies Added` INT,
              `Avg Companies Added` FLOAT
            );

            ALTER TABLE sp500_companies_added_per_year_table
            ADD CONSTRAINT sp500_companies_added_per_year_table_year_added_fk
            FOREIGN KEY (`Year Added`)
            REFERENCES sp500_companies_added_per_year(`Year Added`);

            INSERT INTO sp500_companies_added_per_year_table
            SELECT * FROM sp500_companies_added_per_year;

--- Load the Table from the View using an Ad Hoc SQL script

            INSERT INTO sp500_companies_added_per_year_table
            SELECT * FROM sp500_companies_added_per_year;

--- Turn the Ad Hoc SQL script in a Stored Procedure

            CREATE PROCEDURE load_sp500_companies_added_per_year_table()
            BEGIN
              INSERT INTO sp500_companies_added_per_year_table
              SELECT * FROM sp500_companies_added_per_year;
            END;

--- Call the Stored Procedure

            CALL load_sp500_companies_added_per_year_table();

