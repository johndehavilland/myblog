---
title: Using Azure Functions to extract named entities from news articles
layout: post
modified: 
categories: blog
excerpt: 
tags:
- Azure
- NamedEntityRecognition
- AzureFunctions
image:
  feature: 
date: '2016-08-14 01:00:00 -0400'
share: true,
comments: true
---

Recently a [custom visual](https://powerbi.microsoft.com/en-us/blog/new-power-bi-custom-visuals-for-browsing-and-analyzing-collections-of-text/) was released for Power BI that enabled browsing and analyzing collections of text. These visuals can provide a powerful set of tools for analysis.
<!--more-->
There are three visuals: 
* Facet Key
* Cluster Map
* Strippets Browser

In this post I want to just concentrate on the Facet Key and Strippets Browser visuals and talk about how you can use [Azure Functions](https://azure.microsoft.com/en-us/services/functions/) to simply and easily generate the data required to use them. 

The Facet Key visual allows you to quickly see a break down of top entities across your text corpus. An entity in this case would be a location, organization or person. The data for these can be generated via the process of Named Entity Recognition. There are various ways to go about achieving this. One option is to leverage the Stanford Named Entity Recognition Module. Another alternative is to use the Named Entity Recognition Module in Azure Machine Learning.

The Strippets Browser is a document reader that provides two complementary ways of sampling the contents of a collection of documents or a news stream. Text within the Strippets Browser is highlighted and that maps to the identified entities. The first time an entity is mentioned it is also accompanied by an icon in an outline strip next to the document scrollbar. 

Suppose you want to create a dashboard that displays visuals related to news stories about your company. On a daily basis you can retrieve the top 100 stories from google news for your specific company. The URL for that feed would be as follows:
[https://news.google.com/news/feeds?pz=1&cf=all&ned=us&hl=en&q=microsoft&output=rss&num=100](https://news.google.com/news/feeds?pz=1&cf=all&ned=us&hl=en&q=microsoft&output=rss&num=100)

You can create an Azure Function that once a day goes out and grabs the feed. Here is some example code that would take an RSS feed and parse out some key elements:

{% highlight csharp %}
XmlDocument xdoc = new XmlDocument();
xdoc.Load(url);
if (xdoc != null)
{
    XmlElement root = xdoc.DocumentElement;
    XmlNodeList xNodelst = root.SelectNodes("channel/item");

    foreach (XmlNode node in xNodelst)
    {
        string title = node.SelectSingleNode("title").InnerText;
        string link = node.SelectSingleNode("link").InnerText;
        string summary = RemoveHtmlTags(node.SelectSingleNode("description").InnerText);
        docs.Add(new string[] { Guid.NewGuid().ToString(), summary, title, link, url });
    }
}
{% endhighlight %}

Once you have that list of documents from the feed you can then store the feed data into a SQL database table. 

At this point, for each document extracted from the feed you need to identify entities in the summary. One way to do this is use the [Named Entity Recognizer module](http://nlp.stanford.edu/software/CRF-NER.shtml) from Stanford. While this is a Java based library, there is a [C# wrapper](https://sergey-tihon.github.io/Stanford.NLP.NET/StanfordNER.html) which makes it easy to use in an Azure Function. 

To use it in an Azure Function, simply create a project.json file as follows:

{% highlight json %}
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Newtonsoft.Json":"9.0.1",
        "Stanford.NLP.NER": "3.6.0.0",
        "IKVM":"8.1.5717.0"
      }
    }
   }
}
{% endhighlight %}

This will pull in the relevant wrappers from nuget. You will also need to download the module and grab the classifiers you want to use and upload those to the Azure Function. You will then need to upload the classifier(s) you plan to use as part of your function.

In your Azure Function code you can reference the NER classifier as follows where *MyAzureFunction* is the name of your Azure Function.

{% highlight csharp %}
var classifier = CRFClassifier.getClassifierNoExceptions(@"D:\home\site\wwwroot\MyAzureFunction\stanford-ner-2013-06-20\classifiers\english.all.3class.distsim.crf.ser.gz");

var output = classifier.classifyToString(text, "tabbedEntities", true);
var result = output.Split(new[] { '\r', '\n' });
return result; 
{% endhighlight %}

This would give you a list of entities in memory. You can then parse that list and insert the entities into the SQL Db mapping them to the corresponding article that you pulled from the feed earlier.

Then, within PowerBI, you can connect to the Azure SQL Database as your data source and, using the custom facet visual, you can map the entity type, entity value and article count to produce a facet visual of the key entities across all the articles.

