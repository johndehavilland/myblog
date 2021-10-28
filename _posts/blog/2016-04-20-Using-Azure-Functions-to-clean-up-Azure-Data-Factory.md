---
layout: post
title: "Using Azure Functions to clean up Azure Data Factory"
modified:
categories: blog
excerpt:
tags: ['Azure', 'AzureFunctions', 'AzureDataFactory', 'BigData']
image:
  feature:
date: 2016-04-20T00:00:00-05:00
share: true,
comments: true
---

When you build out a pipeline using [Azure Data Factory](https://azure.microsoft.com/en-us/services/data-factory/) you will have to associate it to a storage account. If, as part of your pipeline you are running certain jobs such as a HDInsight On Demand job it will, for each slice run, generate a container in the storage account for that run. This is great for debugging, but if you run a pipeline for any extensive period of time these job containers build up and you need a way to periodically purge them. Typically you will see the adf job container be called something like: *adf**name_of_factory**-**name_of_ondemand_service**-**timestamp***
<!--more-->
So one easy way to implement automated cleanup of these folder is to use [Azure Functions](https://azure.microsoft.com/en-us/services/functions/) to run a clean-up job on a schedule against that storage account to remove prior adf generated folders. This is easy enough to do. Create an Azure Function by going to *New* in the [Azure Portal](http://portal.azure.com) and typing *Function* in the search box. 

After you have created the Azure Function service, choose New Function and choose a TimedTrigger template (I chose the TimedTrigger - Node one). Give your function a meaningful name and enter a schedule. The schedule is based off cron expressions for scheduling - [this blog post](http://blog.amitapple.com/post/2015/06/scheduling-azure-webjobs/) does a good job of explaining how this works. Then press *create* and this will take you to the coding view.

Here is the code for my function:

<script src="https://gist.github.com/johndehavilland/2c75a1ba0a7375d2ee9da90991df626b.js"></script>

After you have entered the code, update the values to point to your storage account and container name and then click **Save**. You can then test it out by scrolling down and clicking **Run**. If all works then you are all set and Azure Functions will execute this function on your defined schedule.

