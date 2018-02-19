---
title: Create Video Subtitles Using Azure Logic Apps and Cognitive Services
layout: post
modified: 
categories: blog
excerpt: 
tags:
- logicapps
- cogsvcs
- captioning
date: '2017-12-19'
image:
  feature: 
share: true
comments: true
published: false
---

Azure makes it easy to build complex workflows quickly and seamlessly. One example could be to generate multilanguage subtitle (or caption files) for videos that you create. Using Event Grid to fire storage events, Logic Apps to orchestrate the flow and cognitive services to generate the transcribed audio it is easy to create a media workflow.

The overall architecture will look like this:
