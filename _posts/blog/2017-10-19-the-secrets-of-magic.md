---
title: The Secrets of Magic or Using PROSE to transform JSON
layout: post
modified: 
categories: blog
excerpt: 
tags:
- PROSE
- MachineLearning
- DataWrangling
date: '2017-10-19'
image:
  feature: 
share: true,
comments: true
---

Sometimes you come across something that just blows you away. I had this experience recently with the [Program Synthesis by Example](https://microsoft.github.io/prose/) functionality in the newly release [Azure Machine Learning Workbench](https://www.johndehavilland.com/blog/2017/09/26/Azure-Machine-Learning.html). 

It takes data-wrangling to a whole new level - opening the door to some very cool opportunities. At it's most basic, PROSE automatically finds a program that will best convert a given input to a given output. If you want to convert a whole document then you would give an input sample from that and the expected output. PROSE would then synthesize a ranked set of programs that match the given input/output examples and chooses the highest ranked one to apply. This will then be applied across the entire input.

You will have seen this in action before, in Excel, via the flash fill mechanism. However, this mechanism was fraught and limited in scope. It relied to heavily on hand created rules and heuristics to find the program to convert input to output. Now though, using recent advances in deep learning, PROSE uses a data-driven approach to select the right algorithm to apply without any need for hand created rules. It is available as part of Azure Machine Learning Workbench and is also available as an  SDK that you can programatically leverage. Its pretty easy to use and, to demonstrate, that I put together a simple sample to transform JSON from one form to another using PROSE. The application is very simple and you can try it out below.

<h1><center><a href ="http://jsonparse.azurewebsites.net/">Click here to try it out</a></center></h1>

As an example you can use the following JSON, or use your own.

Input Example:

{% highlight javascript %}
{
  "datatype": "local",
  "data": [
    {
      "Name": "John",
      "status": "To Be Processed",
      "LastUpdatedDate": "2013-05-31 08:40:55.0"
    },
    {
      "Name": "Paul",
      "status": "To Be Processed",
      "LastUpdatedDate": "2013-06-02 16:03:00.0"
    }
  ]
}
{% endhighlight %}

Output Example

{% highlight javascript %}
[
  {
    "John" : "To Be Processed"
  },
  {
    "Paul" : "To Be Processed"
  }
]
{% endhighlight %}

Input Full:

{% highlight javascript %}
{
  "datatype": "local",
  "data": [
    {
      "Name": "John",
      "status": "To Be Processed",
      "LastUpdatedDate": "2013-05-31 08:40:55.0"
    },
    {
      "Name": "Paul",
      "status": "To Be Processed",
      "LastUpdatedDate": "2013-06-02 16:03:00.0"
    },
    {
      "Name": "Alice",
      "status": "Finished",
      "LastUpdatedDate": "2013-07-02 12:04:00.0"
    }
  ]
}
{% endhighlight %}

