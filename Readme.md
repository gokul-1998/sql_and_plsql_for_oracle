# This repository give us a quick review on SQL fundamental topics
### The topics were taken from https://education.oracle.com/oracle-database-sql/pexam_1Z0-071

- ## Relational Database concepts
    - ### Explaining the theoretical and physical aspects of a relational database
        - A Relation Database Management system (RDBMS) is a database management system that is based on the relational model. 
        - It has the following major components:        
            - Table, 
            - Record/Tuple/Row, 
            - Field,
            - Column/Attribute. 
        - Examples of the most popular RDBMS are MYSQL, Oracle, IBM DB2, and Microsoft SQL Server database.
    - ### Relating clauses in SQL Select Statement to Components of an ERD
        - # refer various varieties of select statements
    - ### Explaining the relationship between a database and SQL
        - SQL is the language to communicate to database
- ## Retrieving Data using the SQL SELECT 
    - ### Using Column aliases
        - https://www.oracletutorial.com/oracle-basics/oracle-alias/
        - [replace this with more detailed md file]
    - ### Using The SQL SELECT statement
        - https://www.oracletutorial.com/oracle-basics/oracle-select/
    - ### Using concatenation operator, literal character strings, alternative quote operator, and the DISTINCT keyword
        - concatenation https://docs.oracle.com/cd/B19306_01/server.102/b14200/operators003.htm
        - [use the above page to download the sql book]
        - literals https://docs.oracle.com/cd/B19306_01/appdev.102/b14261/literal_declaration.htm#:~:text=A%20string%20literal%20is%20a,case%20sensitive%20within%20string%20literals.
        - alternative quote operator https://www.geeksforgeeks.org/sql-alternative-quote-operator/
        - distinct https://www.oracletutorial.com/oracle-basics/oracle-select-distinct/
    - ### Using Arithmetic expressions and NULL values in the SELECT statement
        - arithmetic expressions https://docs.oracle.com/cd/B19306_01/server.102/b14200/operators002.htm
        - null https://docs.oracle.com/cd/B19306_01/server.102/b14200/sql_elements005.htm#:~:text=If%20a%20column%20in%20a,value%20would%20not%20be%20meaningful.
- ## Restricting and Sorting Data
    - ### Rules of precedence for operators in an expression
        - https://docs.oracle.com/cd/E49933_01/server.770/es_eql/src/ceql_expr_precedence_rules.html
    - ### Limiting Rows Returned in a SQL Statement
        - https://www.oracletutorial.com/oracle-basics/oracle-fetch/
    - ### Using Substitution Variables
        - https://docs.oracle.com/cd/F49540_01/DOC/server.815/a66736/ch33.htm#:~:text=A%20substitution%20variable%20is%20a,rather%20than%20the%20variable%20itself.
    - ### DEFINE AND VERIFY     
        - https://www.youtube.com/watch?v=HNKcn8XHuKI
    - ### Sorting Data
        - https://www.oracletutorial.com/oracle-basics/oracle-order-by/
- ## Using Single-Row Functions to Customize Output
    - ### Manipulating strings with character functions in SQL SELECT and WHERE clauses
        - https://docs.oracle.com/middleware/1221/biee/BIVUG/GUID-BBA975C7-B2C5-4C94-A007-28775680F6A5.htm  
    - ### performing arithmetic with date data
        - https://docs.oracle.com/cd/E17952_01/mysql-5.7-en/date-and-time-functions.html
    - ### Manipulating numbers with the ROUND, TRUNC and MOD functions
        - https://docs.oracle.com/cd/B14156_01/doc/B13812/html/sqfunc.htm
    - ### date 
        - https://docs.oracle.com/cd/E17952_01/mysql-5.7-en/date-and-time-functions.html
- ## Using Conversion Functions and Conditional Expressions
    - ### Applying the NVL, NULLIF, and COALESCE functions to data
        - https://oracle-base.com/articles/misc/null-related-functions
    - ### Understanding implicit and explicit data type conversion
        - https://docs.oracle.com/cd/B19306_01/server.102/b14200/sql_elements002.htm
    - ### Using the TO_CHAR, TO_NUMBER, and TO_DATE conversion functions 
        - https://www.youtube.com/watch?v=MCjX_c0KziQ
    - ### nesting multiple functions
        - https://asktom.oracle.com/pls/apex/asktom.search?tag=nesting-of-functions
- ## Reporting Aggregated Data Using Group Functions
    - ### Restricting Group Results
        - https://docs.oracle.com/javadb/10.8.3.0/ref/rrefsqlj14854.html#:~:text=A%20HAVING%20clause%20restricts%20the,result%20as%20a%20single%20group.
    - ### Creating Groups of Data
        - https://docs.oracle.com/cd/E49933_01/server.770/es_eql/src/reql_aggregation_grouping_sets.html
    - ### Using Group Functions
        - https://docs.oracle.com/database/121/SQLRF/functions003.htm#:~:text=In%20a%20query%20containing%20a,result%20row%20for%20each%20group.
- ## Displaying Data from Multiple Tables
    - ### using self join 
        - https://www.oracletutorial.com/oracle-basics/oracle-self-join/
    - ### Using Various Types of Joins
        - https://docs.oracle.com/javadb/10.6.2.1/ref/rrefsqlj29840.html
    - ### Using Non equijoins
        - https://www.w3resource.com/oracle/joins/oracle-non-equijoins.php
    - ### Using OUTER joins
        - https://docs.oracle.com/javadb/10.6.2.1/ref/rrefsqlj18922.html
    - ### cartesian products
        - https://docs.oracle.com/javadb/10.8.3.0/ref/rrefsqljcrossjoin.html
- ## Using Subqueries to Solve Queries
    - ### single row subqueries
        - https://docs.oracle.com/cd/E17952_01/mysql-5.7-en/row-subqueries.html
    - ### Using Multiple Row Subqueries
        - http://www.dba-oracle.com/t_multiple_row_subqueries.htm
    - ### Update and delete rows using correlated subqueries
        - https://docs.oracle.com/cd/B12037_01/server.101/b10759/queries007.htm
- ## Using SET Operators
    - ### Matching the SELECT statements
        - https://docs.oracle.com/cd/B19306_01/server.102/b14200/operators005.htm
    - ### Using the ORDER BY clause in set operations
        - https://docs.oracle.com/cd/B19306_01/server.102/b14200/queries004.htm
    - ### intersect operator
        - https://docs.oracle.com/cd/B19306_01/server.102/b14200/queries004.htm
- ## Managing Tables using DML statements
    - ### Managing Database Transactions
        - https://docs.oracle.com/cd/E11882_01/server.112/e40540/part_txn.htm
    - ### Controlling transactions
        - https://docs.oracle.com/en/database/oracle/oracle-database/19/zzpre/defining-controlling-transactions.html
    - ### Perform Insert, Update and Delete operations
        - https://www.youtube.com/watch?v=d9hnGQoOHZE
    - ### Performing multi table Inserts
        - https://oracle-base.com/articles/9i/multitable-inserts
    - ### Performing Merge statements
        - https://docs.oracle.com/database/121/SQLRF/statements_9017.htm
- ## Managing Indexes Synonyms and Sequences
    - ### Managing Indexes
        - https://docs.oracle.com/cd/E11882_01/server.112/e25494/indexes.htm
    - ### Managing Synonyms
        - https://docs.oracle.com/database/apex-18.1/AEUTL/managing-synonyms.htm
    - ### Managing Sequences
        - https://docs.oracle.com/cd/E18283_01/server.112/e17120/views002.htm#:~:text=Application%20Clusters%20environment-,Altering%20Sequences,except%20the%20sequence%20starting%20number.
- ## Use DDL to manage tables and their relationships
    - ### Describing and Working with Tables
        - https://docs.oracle.com/cd/E18283_01/server.112/e16604/ch_twelve019.htm
    - ### Describing and Working with Columns and Data Types
        - https://livesql.oracle.com/apex/livesql/file/tutorial_FIMJZ2NPQ4AWTCE0B329BW3GX.html
    - ### Creating tables
        - https://docs.oracle.com/cd/B28359_01/server.111/b28310/tables003.htm
    - ### drop column
        - https://oracle-base.com/articles/8i/dropping-columns
    - ### truncate table
        - https://docs.oracle.com/database/121/SQLRF/statements_10007.htm
    - ### Creating and using Temporary Tables
        - https://docs.oracle.com/cd/E17952_01/mysql-8.0-en/create-temporary-table.html
    - ### Creating and using external tables
        - https://docs.oracle.com/cd/B19306_01/server.102/b14215/et_concepts.htm
    - ### managing constraints 
        - https://docs.oracle.com/cd/E18283_01/server.112/e17120/general005.htm
- ## Managing Views
    - https://docs.oracle.com/database/apex-5.1/AEUTL/managing-views.htm
- ## Controlling User Access
    - https://docs.oracle.com/cd/E19092-01/sol.entmgr41/816-1998/MYN_security_ctrl.html#:~:text=By%20controlling%20user%20access%2C%20you,responsibilities%20and%20other%20relevant%20criteria.
    - ### Differentiating system privileges from object privileges
        -  https://docs.oracle.com/cd/A58617_01/server.804/a58227/ch18.htm
    - ### Granting privileges on tables
        - https://docs.oracle.com/javadb/10.8.3.0/ref/rrefsqljgrant.html
    - ### grant vs roles
        - https://docs.oracle.com/cd/A97630_01/server.920/a96521/privs.htm#:~:text=A%20user%20privilege%20is%20a,together%20privileges%20or%20other%20roles.
- ## Managing Objects with Data Dictionary Views
    - https://docs.oracle.com/cd/E11882_01/server.112/e40540/datadict.htm
- ## Managing Data in Different Time Zones
    - ### Working with CURRENT_DATE, CURRENT_TIMESTAMP,and LOCALTIMESTAMP
        - https://docs.oracle.com/cd/B19306_01/server.102/b14200/functions079.htm
    - ### Working with INTERVAL data types
        - https://www.oracletutorial.com/oracle-basics/oracle-interval/#:~:text=In%20addition%2C%20It%20provides%20the,and%20seconds%20including%20fractional%20seconds.




















