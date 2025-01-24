# Mr.Health_project

Documentation of Data Ingestion and Consolidation Process

Introduction
This document describes the data ingestion and consolidation process for MR HEALTH, using Azure Data Factory, Azure Data Lake Storage, and Azure Databricks. The data is processed through different layers in Data Lake, using medallion architecture (bronze, silver, and gold layers) as described below.

 ![image](https://github.com/user-attachments/assets/c8dc75ca-7177-4d94-bf6c-469d108cb080)

Config
Contains the initial project configurations to ensure execution:

 ![image](https://github.com/user-attachments/assets/38bb784c-0fd8-4d7b-b364-087d17127089)

Bronze Layer
Stores raw data received directly from various sources, including CSV files and MR HEALTH database data. This layer serves as a source data repository, preserving the integrity of the original data.
Structure:
• state: State data
• order_item: Order items data
• country: Country data
• order: Orders data
• product: Products data
• unit: Units data

![image](https://github.com/user-attachments/assets/4fb9a7e5-9359-4a8d-90f7-e3f5486e9d27)

 
Silver Layer
The Silver layer processes and cleanses data from the Bronze layer. Here, data is transformed and normalized to facilitate analysis and further processing. This layer removes duplicate data, corrects errors, and transforms data into a more structured format.
Example of Silver Table Structure:
Table: Silver_Table
• Order_Id (INT)
• Order_Date (DateTime)
• Product_Id (INT)
• Quantity (INT)
• Total_Value (Decimal)
• Product_Name (String)
• Unit_Name (String)
• State_Name (String)
• Country_Name (String)

Structure:
• Silver_Table: Consolidates all data from various bronze tables into a unified table with detailed order information.

![image](https://github.com/user-attachments/assets/a06f6b8b-5516-4685-9f1c-339f91caee0e)

 
Gold Layer
The Gold layer contains transformed data ready for analysis.
Data in this layer is highly structured and optimized for analytical queries and report generation depending on business rules.
Example Structure:
• total_customer_sales: Total sales by customer
• total_orders_per_day: Total orders per day

![image](https://github.com/user-attachments/assets/a809667f-b2ef-473e-98ee-309f9637869f)

---
 
## Data Ingestion and Consolidation Process
Ingestion and Data Consolidation
Data ingestion is performed through Azure Data Factory, which copies data from various sources to Data Lake.

Ingestion Pipeline:
IngestCSVData: Pipeline that copies data from CSV files to Bronze layer
Transformation and Consolidation
Transformations are performed in Azure Databricks, where data from the Bronze layer is processed and moved to the Silver layer and subsequently to the Gold layer.

Transformation Notebooks:
Silver_Transform: Notebook that consolidates data from Bronze layer into Silver_Table
Gold_Venda_Total: Notebook that creates the total sales table
Gold_Venda_Diaria: Notebook that creates the daily orders total table

![image](https://github.com/user-attachments/assets/7254af6f-0f80-4f92-82da-9804b9450893)

 
Stored Data Description
Data Structure

Bronze:
Stores raw data in its original format
Example file: pedido.csv

Silver:
Stores cleaned and normalized data
Example table: Silver_Table

Gold:
Stores transformed data ready for analysis
Example tables: Gold_Venda_Total, Gold_Venda_Diaria

File and Table Structure
Each layer has a specific structure for organizing and storing data, as described above. Tables are stored in Delta format to support versioning and ACID transactions.

Implementation Activities:

### Data Ingestion

Linked Services Configuration:
  Created Linked Services in Azure Data Factory to connect to various data sources

Dataset Configuration:
  Created datasets in Azure Data Factory for each data source

Ingestion Pipeline Creation:
  Created pipelines to copy data from sources to Bronze layer

Data Transformation
  Transformation Notebook Creation:
    Developed notebooks in Databricks to transform data from Bronze to Silver layer

Silver and Gold Table Creation:
  Developed notebooks to create and populate tables in Silver and Gold layers

Data Consolidation
 
Notebook Execution:
  Configured notebook activities in Azure Data Factory to execute transformation and consolidation notebooks

Verification and Validation:
  Validated transformed data to ensure accuracy and consistency

Version Management
  Delta Lake Configuration:
    Configured Delta Lake to enable versioning and ACID transactions
    Use commands like DESCRIBE HISTORY and RESTORE to manage versions

 ![image](https://github.com/user-attachments/assets/263aeb7f-fc70-4eda-b9ed-3a7f9e7d6e6d)


### Automated Updates and Triggers Configuration

Configured activities in Azure Data Factory to automatically execute Databricks notebooks after data ingestion, using triggers in Azure Data Factory, setting them to automatically initiate transformation notebooks after ingestion pipelines completion. For example, we can use a trigger to start the Bronze to Silver layer transformation notebook immediately after the CSV data ingestion pipeline finishes. Depending on business rules, we can perform daily ingestion at midnight, however, it would always be one day outdated. For this, we can select, for example, to trigger for each updated ORDER, thus remaining updated without ingesting all data from all tables, only updating what really matters for the business.

### Conclusion

This document describes the data layer architecture, ingestion and consolidation process, and necessary activities to implement the data pipeline for MR HEALTH. The use of Azure Data Factory, Azure Data Lake Storage, and Azure Databricks ensures a robust and scalable process for data ingestion, transformation, and analysis.
