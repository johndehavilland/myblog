---
title: Event Driven Movie Captioning in Azure
layout: post
modified: 
categories: blog
excerpt: 
tags:
- logicapps
- cog svcs
- captioning
date: '2018-02-01'
image:
  feature: 
share: true
comments: true
published: false
---

Azure makes it easy to build complex workflows quickly and seamlessly. One example could be to generate multi-language subtitle (or caption files) for videos that you create. Using Event Grid to fire storage events, Logic Apps to orchestrate the flow and cognitive services to generate the transcribed audio it is easy to create a media workflow.

The overall architecture will look like this:


