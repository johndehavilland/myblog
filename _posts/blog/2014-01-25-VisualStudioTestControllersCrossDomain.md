---
layout: post
title: "Visual Studio Test Controllers Cross Domain"
modified:
categories: blog
excerpt:
tags: ['performance', 'visual-studio']
image:
  feature:
date: 2014-01-25T15:39:55-04:00
---


I have setup some test controllers and was struggling to hit them remotely from my local machine. The main issue was because my local machine was in one domain and the controllers were in another domain. After lots of fruitless googling I came eventually came across [this simple solution](http://stackoverflow.com/a/10511987/790131).

Basically, use the Windows Credential Manager to map the right username/password to the controller machine you want to connect to. It was as easy as that.