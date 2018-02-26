---
title: Demystifying R options available from Microsoft 
layout: post
modified: 
categories: blog
excerpt: 
tags:
- r server
- microsoft machine learning server
- microsoftr
date: '2018-02-25'
image:
  feature: 
share: true
comments: true
published: false
---

The R landscape at Microsoft can be a bit confusing. So I want to try to lay it out simply here. First, Microsoft R Server has been rebranded to [Microsoft Machine Learning Server](https://blogs.technet.microsoft.com/machinelearning/2017/09/25/introducing-microsoft-machine-learning-server-9-2-release/). At the time of writing, ML Server 9.2 was available.

So, what are the different ways to use R from Microsoft?

## [Microsoft R Open](https://mran.microsoft.com/open)
* This is the enhanced, open source, distribution of R from Microsoft. 
* It is based on, and extends, the R language. It contains the R language, compatible with all R packages, scripts and applications that work with the underlying version of R. 
* Contains a set of specialized packages to enhance the R experience, including multi-threaded math libraries and enhanced performance optimizations.

## [Microsoft R Open in Azure ML](https://docs.microsoft.com/en-us/azure/machine-learning/studio-module-reference/execute-r-script)
* You can execute R scripts as part of Azure Machine Learning Studio experiments. 
* This supports Microsoft R Open and CRAN. 
* Note that this is currently a couple of versions behind the latest R releases - supporting CRAN 3.1.0

## [RevoScaleR](https://docs.microsoft.com/en-us/machine-learning-server/r-reference/revoscaler/revoscaler)
* Ships as part of Microsoft Machine Learning Server and Microsoft R Client.
* A collection of portable, scalable, and distributable R functions for importing, transforming, and analyzing data at scale. 
* Can run it locally or remotely (for scale out etc.)
* Remote context could be: Machine Learning Server, Spark, Hadoop, SQL Server

## [MicrosoftML](https://docs.microsoft.com/en-us/machine-learning-server/r/concept-what-is-the-microsoftml-package)
* R pacakge that adds state-of-the-art data transforms, machine learning algorithms, and pre-trained models to R and Python functionality
* Installed as part Machine Learning Server, Microsoft R Client and SQL Server Machine Learning Services.
* Works in tandem with RevoScaleR.

## [Microsoft R Client](https://docs.microsoft.com/en-us/machine-learning-server/r-client/what-is-microsoft-r-client)
* This is a free data science tool built on top of Microsoft R Open.
* Allows you to work with data locally and then offload to a remote compute context for more power.
* You can use the RevoScaleR packages as part of this.
* Its aim is to enable local development and exploration.

## [Microsoft Machine Learning Server](https://docs.microsoft.com/en-us/machine-learning-server/what-is-machine-learning-server)
* Standalone and installed on a computer not running SQL Server.
* Enterprise data analysis at scale - providing high performance and enterprise robustness.
* Supports R and Python.
* Secure environment for deploying and operationalizing machine learning models.
* Makes it easy to deploy your models as a web service.
* Ability to scale out using either Spark, Hadoop, SQL Server, or multiple nodes of ML Server.
* Microsoft Machine Learning Server stand-alone for Linux or Windows is licensed core-for-core as SQL Server 2017.
* All customers who have purchased Software Assurance for SQL Server Enterprise Edition are entitled to use 5 nodes of Microsoft Machine Learning Server for Hadoop/Spark for each core of SQL Server 2017 Enterprise Edition under SA. In addition, we are removing the core limit per-node; customers can have unlimited cores per node of Machine Learning Server for Hadoop/Spark.

## [Microsoft SQL Server 2017 Machine Learning Services](https://docs.microsoft.com/en-us/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services)
* Builds on R support in SQL Server 2016
* Integrating Machine Learning Services in the database - includes R and Python support.
* Can perform far better than conventional R because you can use server resources and RevoScaleR for scale out.
* This is built into the database engine (vs. stand alone as described above)
* Execute R scripts via sp_execute_external_script 
* Supports in-database package management
* Supports native scoring via T-SQL PREDICT function - can predict without needing to load R environment.

## [PowerBI and R](https://docs.microsoft.com/en-us/power-bi/service-r-visuals)
* The Power BI service supports viewing and interacting with visuals created with R scripts. 
* Note that in the service not all of the R packages are supported. 
* R visuals that are created in Power BI Desktop, and then published to the Power BI service, for the most part behave like any other visual in the Power BI service; you can interact, filter, slice, and pin them to a dashboard, or share them with others. 

## [Azure Databricks](https://docs.azuredatabricks.net/spark/latest/sparkr/overview.html)
* Can create notebooks and workflows using R or SparkR
* Support of CRAN packages.
* Leverage SparkR to take advantage of Spark (scale out etc.) for R jobs.

## [R with HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/r-server/r-server-get-started)
* HDInsight includes an option to spin up a Machine Learning Server (previously called R Server) to integrate with your HDI cluster.
* Execute R scripts with Spark/Hadoop compute context to distribute job across cluster.
* Use the ScaleR functions from RevoScaleR package to ensure R functions run across cluster.


