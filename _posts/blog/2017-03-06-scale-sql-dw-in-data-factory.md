---
title: How To Scale Azure SQL Data Warehouse in Azure Data Factory
layout: post
modified: 
categories: blog
excerpt: 
tags:
- AzureDataFactory
- SQLDW
- Scaling
image:
  feature: 
date: '2017-03-06 01:00:00 -0400'
share: true,
comments: true
---

You can use [Azure SQL Data Warehouse](https://azure.microsoft.com/en-us/services/sql-data-warehouse/) as part of your Azure Data Factory pipeline which is great, but you probably don't want to have the data warehouse running at the maximum [Data Warehouse Units](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-manage-compute-overview) (DWU) all the time, especially if the pipeline is not running on a frequent basis. I want to share with you some steps to enable scaling up and scaling down of SQL Data Warehouse right within your Data Factory pipeline.
<!--more-->
For scaling up you will need:

1. A linked service pointing to your SQL Data Warehouse master database - it has to be master so we can poll for success.
2. A dataset of type SQL Table referencing master database.
3. A dataset to act as fake output (e.g. Azure Blob output)
4. A pipeline with a copy activity that uses the sqlReaderQuery to modify the Service Objective and polls until it is done (because the scaling is async).

The activity looks like:
{% highlight json %}
{
	"type": "Copy",
	"typeProperties": {
	  "source": {
	    "type": "SqlSource",
	    "sqlReaderQuery": "ALTER DATABASE <name-of-your-dw> MODIFY (service_objective = 'DW300'); WHILE  ( SELECT TOP 1  state_desc  FROM sys.dm_operation_status  WHERE  1=1 AND resource_type_desc = 'Database' AND major_resource_id = '<name-of-your-dw>' AND operation = 'ALTER DATABASE' ORDER BY start_time DESC ) = 'IN_PROGRESS' BEGIN RAISERROR('Scale operation in progress',0,0) WITH NOWAIT; WAITFOR DELAY '00:00:05'; END; SELECT 1"
	  },
	  "sink": {
	    "type": "BlobSink",
	  }
	},
	"inputs": [
	  {
	    "name": "AzureSqlDbScaling"
	  }
	],
	"outputs": [
	  {
	    "name": "ScaleupOutput"
	  }
	],
	"policy": {
	  "timeout": "1.00:00:00",
	  "concurrency": 1,
	  "retry": 3
	},
	"scheduler": {
	  "frequency": "Day",
	  "interval": 1
	},
	"name": "ScaleUp"
}
{% endhighlight %}

For scaling down you need the following:

1. A linked service pointing to another db/dw on the same logical server (not master though).
2. A fake dataset for output.
2. A stored proc in this db/dw for scaling down.
3. A pipeline with a stored proc activity.

The stored proc could look like this: 
{% highlight sql %}
CREATE PROCEDURE spScaleDown
AS  
ALTER DATABASE <name-of-your-dw> MODIFY (service_objective = 'DW100');
GO 
{% endhighlight %}

The activity looks like:
{% highlight json %}
{
	"type": "SqlServerStoredProcedure",
	"typeProperties": {
	  "storedProcedureName": "spScaleDown",
	  "storedProcedureParameters": {}
	},
	"inputs": [
	  {
	    "name": "FinalDataset"
	  }
	],
	"outputs": [
	  {
	    "name": "FakeDataset"
	  }
	],
	"policy": {
	"timeout": "01:00:00",
	"concurrency": 1
}
{% endhighlight %}

This should allow you to now incorporate scaling up and scaling down a sql data warehouse within a data factory pipeline. You can access the full end to end pipeline json [here in github](https://github.com/johndehavilland/azuresqldwscaler).