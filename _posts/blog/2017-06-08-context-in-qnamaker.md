---
title: Creating Context with Microsoft QNAmaker
layout: post
modified: 
categories: blog
excerpt: 
tags:
- Azure
- Bots
- NLP
date: '2017-06-08'
image:
  feature: 
share: true,
comments: true
---

If you have not checked out [Microsoft's qnamaker](http://qnmaker.ai) then you should. "From FAQ to Bot in minutes" - quite literally. You can sign up, give it an FAQ to ingest, wait a minute or so for it to train and then you will be given a REST endpoint to which you can POST natural language questions. You can quickly incorporate this into a bot using the Azure Bot Framework or even incorporate it into other web applications. 
<!--more-->
Sometimes you want a bit more complexity in your responses though. Suppose a scenario where you may have a question that is the same but refers to two different products.

Let's take an example of 2 cars: red and green. Let's assume that you want to find out the MPG for each car. In the knowedge base you have two questions:

1. What is the mpg for the green car?
2. what is the mpg for the red car?

Now what if the user asks, *what is the mpg of the car?* Ideally, your bot should be able to answer that with a question: *Which color?* and prompt them with the options.

How could you achieve this using the QNAMaker? Well, you could create a third question:
  
>What is the mpg for the car?

and set the answer to this to be a question response, in JSON format for example:

```
{
  type: "question",
  choice:["red","green"],
  question:"what is the mpg for the _ car?"
}
```
Now when your program gets this response, it can parse it and initiate a prompt for answers (e.g. using the bot framework FormFlow). Once the user choose a response you can programatically substitue into the original question the additional piece and submit that back to the qnamaker as a new question. 

With this technique you can build a more sophisticated response in your applications and still leverage the power of the qnamaker.

Here as an example of it in action using the inbuilt tester.

![QNAMaker with JSON response]({{ site.images }}/faq-response.gif)

