# On-Premises to Azure Migration

This repository contains a comprehensive guide for migrating data from on-premises infrastructure to Azure, covering various scenarios and best practices. Whether you are migrating databases, virtual machines, or other data sources, this guide aims to provide you with step-by-step instructions, tips, and considerations to ensure a successful data migration to Azure.


## Introduction 

Welcome to the Azure Data Migration Guide! This guide aims to assist you in migrating your data from on-premises infrastructure to Azure. By leveraging Azure's powerful services, you can seamlessly migrate your databases, virtual machines, and file storage to the cloud.

Azure provides a range of services to facilitate data migration, including Azure Database Migration Service, Azure Migrate, Azure File Sync, and more. Each section of this guide will focus on a specific migration scenario, providing detailed steps and code examples to help you through the migration process.

## Prerequisites

Before you begin the migration process, ensure that you have the following prerequisites in place:

Azure Subscription: You need an active Azure subscription to create the necessary resources in Azure.

Network Connectivity: Establish a secure network connection between your on-premises environment and Azure, such as a VPN or ExpressRoute.

Access Credentials: Gather the necessary credentials for accessing your on-premises data sources and the target Azure resources.

Security Considerations: Review and implement security measures, such as firewall rules, network access controls, and encryption, to protect your data during migration.

## Database Migration

This section focuses on migrating databases to Azure. We will cover three common database platforms used in on-premises environments: SQL Server, MySQL, and Oracle. For each platform, we will discuss pre-migration considerations, assessment tools, migration methods, and provide step-by-step instructions with code examples.

### SQL Server Migration

**Pre-migration Considerations**

Before migrating your SQL Server database to Azure, consider the following:

Compatibility: Ensure that your SQL Server version is supported in Azure SQL Database or Azure SQL Managed Instance.

Sizing and Capacity: Determine the appropriate sizing and capacity requirements for your target Azure SQL deployment.

Security and Compliance: Understand Azure SQL security features and compliance requirements for your application.

**Assessment and Discovery Tools**

Azure provides tools to assess and discover your SQL Server databases for migration:

Azure Migrate: Assess on-premises SQL Server databases for migration to Azure SQL Database.

Data Migration Assistant (DMA): Evaluate your databases for compatibility issues before migrating to Azure.
Database Migration Methods

There are multiple migration methods available for SQL Server databases:

Azure Database Migration Service (DMS): A fully managed service that simplifies database migration to Azure SQL Database or Azure SQL Managed Instance.

Azure Site Recovery: Replicate and migrate SQL Server databases to Azure VMs running SQL Server or Azure SQL Database

SQL Server Backup and Restore: Take a backup of your on-premises SQL Server database and restore it to Azure SQL Database.

Azure Data Sync: Synchronize data between on-premises SQL Server and Azure SQL Database.

**Step-by-Step Guide for SQL Server Migration to Azure**

Follow the steps below to migrate your SQL Server database to Azure:

- Provision an Azure SQL Database or Azure SQL Managed Instance based on your requirements.
- Install and configure the necessary assessment and discovery tools, such as Azure Migrate and Data Migration Assistant (DMA).
- Assess your SQL Server database using the assessment tools and address any compatibility issues identified.
- Set up Azure Database Migration Service (DMS) if you choose to use it for migration.
- Create a migration project in Azure DMS and configure the source and target databases.
- Perform a test migration to validate the migration process and resolve any issues.
- Start the final migration by running the migration task in Azure DMS.
- Monitor the migration progress and perform necessary post-migration validations.
- Update your application connection strings or configurations to point to the new Azure SQL Database.
- Decommission the on-premises SQL Server database once the migration is successful.

Here's an example of a PowerShell script to create an Azure SQL Database and migrate a SQL Server database using Azure DMS:


```powershell
# Set variables
$resourceGroupName = "myResourceGroup"
$location = "eastus"
$sourceSqlServerName = "myOnPremSqlServer"
$sourceDatabaseName = "myOnPremDatabase"
$targetSqlServerName = "myAzureSqlServer"
$targetDatabaseName = "myAzureDatabase"
$dmsProjectName = "myDmsProject"

# Create an Azure SQL Database
New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $targetSqlServerName -DatabaseName $targetDatabaseName -Edition "Standard" -RequestedServiceObjectiveName "S2"

# Create a DMS project
$projectId = (New-AzDmsProject -ResourceGroupName $resourceGroupName -ServiceName $targetSqlServerName -ProjectName $dmsProjectName).ProjectId

# Create a DMS task
$taskId = (New-AzDmsTask -ResourceGroupName $resourceGroupName -ServiceName $targetSqlServerName -ProjectName $dmsProjectName -TaskName "MigrateDatabase" -SourceConnection "Data Source=$sourceSqlServerName;Initial Catalog=$sourceDatabaseName;Integrated Security=True;" -TargetConnection "Data Source=$targetSqlServerName.database.windows.net;Initial Catalog=$targetDatabaseName;Integrated Security=False;User ID=myUsername;Password=myPassword;Encrypt=True;TrustServerCertificate=False;Authentication=SqlPassword").TaskId

# Start the DMS task
Start-AzDmsTask -ResourceGroupName $resourceGroupName -ServiceName $targetSqlServerName -ProjectName $dmsProjectName -TaskId $taskId
```

Make sure to update the variables with your specific values before running the script.

## MySQL Migration

Pre-migration Considerations
Before migrating your MySQL database to Azure, consider the following:

Compatibility: Check the compatibility between your MySQL version and Azure Database for MySQL.
Sizing and Capacity: Determine the appropriate sizing and capacity requirements for your target Azure Database for MySQL deployment.
Security and Compliance: Understand Azure Database for MySQL security features and compliance requirements for your application.

Assessment and Discovery Tools

For assessing and discovering your MySQL databases, you can use the following tools:

Azure Migrate: Assess on-premises MySQL databases for migration to Azure Database for MySQL.
Database Migration Methods
There are several methods available for migrating MySQL databases to Azure:

Azure Database Migration Service (DMS): Use DMS to perform an online migration of your MySQL database to Azure Database for MySQL.

Azure Database for MySQL Migration Wizard: Use the migration wizard to migrate your MySQL database schema and data to Azure Database for MySQL.

Database Dump and Restore: Dump your MySQL database and restore it to Azure Database for MySQL.


Step-by-Step Guide for MySQL Migration to Azure

Follow these steps to migrate your MySQL database to Azure Database for MySQL:

- Provision an Azure Database for MySQL based on your requirements.
- Install and configure the necessary assessment and discovery tools, such as Azure Migrate.
- Assess your MySQL database using Azure Migrate and address any compatibility issues identified.
- Set up Azure Database Migration Service (DMS) if you choose to use it for migration.
- Create a migration project in Azure DMS and configure the source and target databases.
- Perform a test migration to validate the migration process and resolve any issues.
- Start the final migration by running the migration task in Azure DMS.
- Monitor the migration progress and perform necessary post-migration validations.
- Update your application connection strings or configurations to point to the new Azure Database for MySQL.
- Decommission the on-premises MySQL database once the migration is successful.

Here's an example of a Python script to migrate a MySQL database to Azure Database for MySQL using the Azure MySQL Migration Wizard:


```python
import mysql.connector
import sys
import azmysqlmigration

# Connection details for the source MySQL database
source_host = "myOnPremHost"
source_user = "myOnPremUser"
source_password = "myOnPremPassword"
source_database = "myOnPremDatabase"

# Connection details for the target Azure Database for MySQL
target_host = "myAzureHost.mysql.database.azure.com"
target_user = "myAzureUser@myAzureHost"
target_password = "myAzurePassword"
target_database = "myAzureDatabase"

# Connect to the source MySQL database
source_conn = mysql.connector.connect(
  host=source_host,
  user=source_user,
  password=source_password,
  database=source_database
)

# Connect to the target Azure Database for MySQL
target_conn = mysql.connector.connect(
  host=target_host,
  user=target_user,
  password=target_password,
  database=target_database
)

# Perform the database migration
azmysqlmigration.migrate(
  source_conn,
  target_conn,
  table_migrate_option=azmysqlmigration.TableMigrateOption.SchemaAndData,
  object_migrate_option=azmysqlmigration.ObjectMigrateOption.AllObjects,
  operation_progress_callback=azmysqlmigration.print_progress_callback
)
```

****azmysqlmigration.migrate:**** This is a function from the azmysqlmigration package that performs the actual migration process.

**source_conn:** This variable represents the connection object to the source MySQL database. It should be a valid connection established using the appropriate credentials (host, user, password, database).

**target_conn:** This variable represents the connection object to the target Azure Database for MySQL. It should be a valid connection established using the appropriate credentials (host, user, password, database).

**table_migrate_option:** This parameter specifies the table migration option. In this example, azmysqlmigration.TableMigrateOption.SchemaAndData is used, which means both the table schema and data will be migrated.

**object_migrate_option:** This parameter specifies the object migration option. In this example, azmysqlmigration.ObjectMigrateOption.AllObjects is used, which means all database objects (tables, views, procedures, etc.) will be migrated.

**operation_progress_callback:** This parameter specifies a callback function to track the progress of the migration operation. In this example, **azmysqlmigration.print_progress_callback** is used, which is a predefined function provided by the azmysqlmigration package to print the progress information during the migration.

By providing the appropriate source and target connections, migration options, and progress callback function, the azmysqlmigration.migrate function will initiate the migration process, migrate the specified tables and objects, and provide progress updates.

Make sure you have installed the azmysqlmigration package and imported it correctly before running this code. Also, ensure that you have the necessary permissions and connectivity to both the source MySQL database and the target Azure Database for MySQL.


## Oracle Migration

Pre-migration Considerations

Before migrating your Oracle database to Azure, consider the following:

- Compatibility: Check the compatibility between your Oracle version and Azure Database for Oracle.
- Sizing and Capacity: Determine the appropriate sizing and capacity requirements for your target Azure Database for Oracle deployment.
- Security and Compliance: Understand Azure Database for Oracle security features and compliance requirements for your application.

Assessment and Discovery Tools
For assessing and discovering your Oracle databases, you can use the following tools:

Azure Migrate: Assess on-premises Oracle databases for migration to Azure Database for Oracle.
Oracle SQL Developer: Use SQL Developer to analyze your Oracle database schema and identify any potential migration issues.

Database Migration Methods
There are multiple methods available for migrating Oracle databases to Azure:

Azure Database Migration Service (DMS): Use DMS to perform an online migration of your Oracle database to Azure Database for Oracle.

Oracle Data Pump: Export the data from your Oracle database using Data Pump and import it into Azure Database for Oracle.

SQL*Loader: Use SQL*Loader to load data from Oracle export files into Azure Database for Oracle.


**Step-by-Step Guide for Oracle Migration to Azure**

Follow these steps to migrate your Oracle database to Azure Database for Oracle:

- Provision an Azure Database for Oracle based on your requirements.
- Install and configure the necessary assessment and discovery tools, such as Azure Migrate and Oracle SQL Developer.
- Assess your Oracle database using Azure Migrate and analyze it using Oracle SQL Developer to address any compatibility issues identified.
- Set up Azure Database Migration Service (DMS) if you choose to use it for migration.
- Create a migration project in Azure DMS and configure the source and target databases.
- Perform a test migration to validate the migration process and resolve any issues.
- Start the final migration by running the migration task in Azure DMS.
- Monitor the migration progress and perform necessary post-migration validations.
- Update your application connection strings or configurations to point to the new Azure Database for Oracle.
- Decommission the on-premises Oracle database once the migration is successful.


Here's an example of a SQL*Loader control file to load data from an Oracle export file into Azure Database for Oracle:

```sql
OPTIONS (SKIP=1)  -- Skip the header row in the CSV file

LOAD DATA
INFILE 'data.csv'  -- Replace 'data.csv' with the actual filename and path of your CSV file
APPEND
INTO TABLE my_table
FIELDS TERMINATED BY ','  -- Specify the delimiter used in the CSV file
TRAILING NULLCOLS
(
  column1,
  column2,
  column3,
  column4,
  column5
)
```


In this example, the control file assumes that your CSV file has a header row, and it skips that row using the OPTIONS (SKIP=1) directive. Adjust the skip value according to your file's structure.

Ensure that the data.csv file path and name match your actual CSV file's location.

Replace my_table with the target table name in your Azure Database for Oracle, and specify the column names and their corresponding positions in the CSV file. Adjust the number of columns and their data types as per your table schema.

Additionally, you can customize the control file by adding additional directives such as TRUNCATE to clear the target table before loading data or specifying data formats using the DATE_FORMAT or NULLIF options.

Make sure to review the Oracle documentation for SQL*Loader for more information and options to tailor the control file to your specific migration requirements.
