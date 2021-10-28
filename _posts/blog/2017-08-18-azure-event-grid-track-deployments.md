---
title: Using Azure Event Grid to Monitor Deployments
layout: post
modified: 
categories: blog
excerpt: 
tags:
- Azure
- AzEventGrid
- AzureEventGrid
date: '2017-08-18'
image:
  feature: 
share: true,
comments: true
---

Azure just released a cool new service call [Azure Event Grid](https://azure.microsoft.com/en-us/blog/introducing-azure-event-grid-an-event-service-for-modern-applications/). Azure Event Grid manages all routing of events from any source, to any destination, for any application. It essentially puts events as first class citizens in the ecosystem. Built with a serverless model, it allows you to wire up eventing to perform "shoulder taps" when events happen to trigger downstream processing.
<!--more-->
As an example, I setup a basic event grid to collect events on an Azure subscription. I applied a filter to just watch for deployment events on a specific resource group and, if one occured, I would push details of that to my slack channel.

Setup was really simple and took about 10 minutes. Using [logic apps](https://azure.microsoft.com/en-us/services/logic-apps/), I created a trigger based on event grids

![Image of event grid creation]({{ site.images }}/event-grid-create.gif)

I selected the subscription and chose *Microsoft.Resources.subscriptions* as the resource type. Next, I chose **advanced options** to create my filter. In order to do this, I peeked at the JSON to grab the subscription id and in the *Prefix Filter* field I entered the following:

    /subscriptions/<subscriptionid>/resourcegroups/ExampleGroup/providers/Microsoft.Resources/deployments

I added Slack as the action to take on the trigger being fired and connected it to my Slack channel. Here is a deployment in action with a notification occuring when the deployment is complete.

![Image of event grid in action]({{ site.images }}/event-grid-test.gif)

Very simply to use and quite powerful.
