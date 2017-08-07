---
title: Using Azure Custom Decision Service To Drive Recommendations
layout: post
modified: 
categories: blog
excerpt: 
tags:
- Azure
- DecisionService
- MachineLearning
- AI
date: '2017-07-26'
image:
  feature: 
share: true,
comments: true
---

I wanted to spice up this blog a little bit with some intelligence. If you notice, on the left (or at the top), there is now a list of recommended articles you might like. This is driven by machine learning algorithms on the backend powered by the [Azure Custom Decision Service](https://azure.microsoft.com/en-us/services/cognitive-services/custom-decision-service/). This service uses reinforcement learning to personalize the list of links based on your behavior. This means that other users reading the same article may see a different set of recommended articles. The service adapts the list of recommendations to maximize the overall engagement of users.

From the documentation it talks about being able to adjust and responding to emerging trends and adapt to showing relevant content quickly. The canonical use case would be in news-based sites that have high volumes of traffic and are constantly updating content. Here you can leverage the Custom Decision Service to drive news articles more tailored to individual users tastes and behaviors. 

While my blog does not really have a high volume of traffic and I am not really pushing out lots of content all the time it does show how easy it is to integrate some more advanced services very easily and enhance even simple experiences for users. 

All you need to do is go to the [Custom Decision Service site](https://ds.microsoft.com) and create an app. Then you hook it up to an RSS feed from your site. At this point you can integrate it into your site via some JavaScript which will allow you to send information back and surface up recommended links. You can [check out the code](https://github.com/johndehavilland/myblog/blob/master/_layouts/post.html#L72) I implemented for this site to get an idea.
