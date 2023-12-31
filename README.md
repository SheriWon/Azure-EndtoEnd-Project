
# Azure-DataPipeline





## Overview

This project is building an end-to-end solution by ingesting the tables from the on-premise SQL Server database using Azure Data Factory and then storing the data in Azure Data Lake. Then Azure Databricks is used to transform the RAW data to the cleanest form of data and then we use Azure Synapse Analytics to load the clean data and finally use Microsoft Power BI to integrate with Azure Azure Synapse Analytics to build an interactive dashboard in Power BI. Moreover, we are using Azure Active Directory (AAD) and Azure Key Vault for monitoring and governance. 

## Table of Contents

[1. Data Pipeline Diagram](#1-data-pipeline-diagram)

1. Data Ingestion	

3. Data Transformation

1. Data Loading 

1. Pipeline Teasting

1. Requirements

## 1. Data Pipeline Diagram
![](https://github.com/SheriWon/Azure-EndtoEnd-Project/blob/main/diagram/End%20to%20End%20Azure%20Data%20architecture.JPG)

## 2. Data Ingestion
Using ADF pipeline extract ten tables from the On-permises SQL Server Database to Azure Data Lake Storage.

* Creating all tables schema to split each table name as "SchemaName" and "TableName", eg. "SalesLT" and "Address". 

![DataSource](https://github.com/SheriWon/Azure-EndtoEnd-Project/blob/main/diagram/data%20source.png)

![TableSchema](https://github.com/SheriWon/Azure-EndtoEnd-Project/blob/main/diagram/alltableSchema.png)


``` 
SELECT
s.name AS SchemaName,
t.name AS TableName
FROM sys.tables t
INNER JOIN sys.schemas s
ON t.schema_id = s.schema_id
WHERE s.name = 'SalesLT'; 
```
* Then, looking up all tables and iterating all tables by 

```
@activity('Look for all tables').output.value
 ```

## 3. Data Transformation
### 3.1 There are two cleaning purposes: 

1. Changing data type of the "ModifiedDate" column to the correct format "yyyy-MM-dd". 
1. Transforming each header to a clear format, such as changing 'AddressID' to 'Address_ID', to assist analysts in efficiently building the dashboard."

### Methods

* Creating three containers in Data Lake Storage and classifying transformations into three layers: Bronze, Silver, and Gold. Each layer contains progressively higher-quality data to meet the requirements for business reporting.

    * [Bronze](https://github.com/SheriWon/DataTransformation-Databricks)
    * [Sliver](https://github.com/SheriWon/DataTransformation-Databricks/blob/main/bronze%20to%20silver.py)
    * [Gold](https://github.com/SheriWon/DataTransformation-Databricks/blob/main/silver%20to%20gold.py)


### ETL pipeline 
![](https://github.com/SheriWon/Azure-EndtoEnd-Project/blob/main/diagram/DataExcration-Transformation.png)
## Data Loading
In Azure Synapse Analytics, the process involves loading meticulously cleansed data from the Gold layer, the highest quality data tier, into an Azure SQL Serverless database named 'gold_db'. To optimize accessibility and usability, a stored procedure is being developed. This procedure incorporates dynamic parameters, enabling the automatic creation of views for all tables present in the Gold container within the serverless SQL database. This dynamic approach ensures that any changes or updates in the underlying data source trigger an automatic refresh of these views, maintaining their relevance and accuracy for subsequent analyses or reporting purposes. [Click for more details](https://github.com/SheriWon/DataLoading-AzureSynapse)

![](https://github.com/SheriWon/Azure-EndtoEnd-Project/blob/main/diagram/dataloading.png)

## Pipeline Testing

The primary data source resides in an on-premise SQL Server database. Whenever new rows are added to any tables within this database, Azure Data Factory establishes a connection to the on-premise SQL Server database. It then extracts all the data, including the new rows, and stores this information in the Bronze layer container within Data Lake Storage. Following this initial stage, Databricks retrieves the accumulated data from the Bronze layer, transitioning it through two levels of transformation before placing it into the Gold layer.

The transformed data residing in the Gold layer is subsequently loaded into the database within Azure Synapse Analytics. Microsoft Power BI is seamlessly integrated with Azure Synapse Analytics to access the most up-to-date data available in the database.

To automate these processes, an Azure Data Factory pipeline trigger is set up. This trigger operates automatically upon execution, initiating the entire sequence of steps. Additionally, a schedule trigger is created to run the pipeline at specified intervals automatically, ensuring regular updates and maintenance of the data pipeline.[Click for more details](https://github.com/SheriWon/Azure-EndtoEnd-Project/tree/main/trigger)
## Requirements

* Programming lanaguage 
    - SQL
    - PySpark
* Azure tools
    - Azure Data Factory
    - Azure Data Lake Storage Gen2
    - Azure Databricks
    - Azure Synapse Analytics
    -  Azure Key vault
    - Azure Active Directory


* [Databricks Configeration](https://github.com/SheriWon/DataTransformation-Databricks/blob/main/storagemount.py)



