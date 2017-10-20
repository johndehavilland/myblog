---
title: Azure Machine Learning Workbench
layout: post
modified: 
categories: blog
excerpt: 
tags:
- Azure
- AzMLWorkbench
- MachineLearning
date: '2017-09-26'
image:
  feature: 
share: true,
comments: true
---

Azure just released a set of updates to Azure Machine Learning. Check out all the details [here](https://azure.microsoft.com/en-us/blog/diving-deep-into-what-s-new-with-azure-machine-learning/).

There were three new launches:

the AML Workbench, a cross-platform client for AI-powered data wrangling and experiment management,
the AML Experimentation service to help data scientists increase their rate of experimentation with big data and GPUs, and
the AML Model Management service to host, version, manage and monitor machine learning models.

The Azure Machine Learning workbench is pretty neat. A desktop app that creating and managing your whole end to end data science life cycle. It works with Visual Studio Code and it even runs on macOS.

Its easy to get going with it. You can simply provision a new experimentation service under your Azure subscription (for free) and from there you can download the application.

![Download workbench]({{ site.images }}/azureml-download.png)

Within the workbench you manage things via projects. Within a project you have your data sources, code, models and other files. There are a variety of ways to deploy and run your code. You can run it locally, or locally via docker images which is pretty cool or run it remotely on Azure via Spark for scale out.

![Execution options]({{ site.images }}/azureml-dropdown.gif)

The data preparation options within the tool are fantastic. You can ingest data from a variety of sources. Once connected you have a plethora of options for data prep.

![Data prep options]({{ site.images }}/azureml-data.gif)

One really cool aspect is the data preparation features built on advanced research in program synthesis and data cleansing. Here it is in action, you can create complex examples and it intelligently applies those across the data set. This will rapdily reduce time data scientists spend wrangling data.

![Data prep options]({{ site.images }}/azureml-prep.gif)

There are also some nice visualizations built into the data prep aspects of the tool

![Data visualizations]({{ site.images }}/azureml-visualizations.png)

Once your code and data is ready you can execute it and monitor job progress. On completion you can drill into the job itself and get information on how well it performed. 

![Run information]({{ site.images }}/azureml-runs.png)

You can even compare performance across several runs very easily in the tool

![Comparing runs]({{ site.images }}/azureml-compareruns.png)

Once you have a model you are happy with you can then explore options for deploying it. 

This just scratches the surface of this powerful new set of capabilities but definitely an exciting time for machine learning. For an indepth tutorial [check here](https://docs.microsoft.com/en-us/azure/machine-learning/preview/tutorial-classifying-iris-part-1).



