---
title: How To Build a Bot with Azure
layout: post
modified: 
categories: blog
excerpt: 
tags:
- Azure
- Bots
- NLP
image:
  feature: 
date: '2017-04-13 01:00:00 -0400'
share: true,
comments: true
---

I have created a simple, proof of concept bot that leverages the following Azure services:
*   Azure Bot Service
*   Microsoft Cognitive Service – Text Analytics
*   Microsoft Cognitive Services – LUIS
*   Azure Search
*   Azure Functions

This bot allows a user to ask questions about Azure and responds with targeted documents that should help the user get the answers they need. 

Every month, a set of Azure functions pulls the latest set of Azure documentation down from the [Azure Docs](https://github.com/Microsoft/azure-docs) public github repo. It extracts out all the documents and processes through those, landing them in an Azure Search index.

The bot is built using the [bot framework](https://dev.botframework.com/) and Microsoft Cognitive Service [LUIS](https://www.luis.ai/) (Language Understanding Intelligent Service). LUIS has been trained to recognize certain phrases to determine the intent. If the intent is detected as a *search* intent then, with the training, it tries to extract out the search entity from the message. The bot framework sends the user’s message to LUIS and LUIS extracts the detected entity. The bot service then calls Azure Search with that extracted entity and constructs a message back to the user in the chat window.

# What it looks like

![Image of layout](https://github.com/johndehavilland/azurehelperbot/raw/master/layout.png)

# How to use

The bot currently has been trained on the following sorts of phrases:

*   Tell me about x
*   What is x
*   How do I do x
*   What are my x limits

Full details can be found on my [github repo](https://github.com/johndehavilland/azurehelperbot).