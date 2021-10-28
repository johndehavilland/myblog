---
layout: post
title: "Azure Resource Manager template apiVersion"
modified:
categories: blog
excerpt:
tags: ['Azure', 'ARM', 'apiversion', 'Azure Resource Manager']
image:
  feature:
date: 2015-09-10T23:26:56-05:00
share: true,
comments: true
---
If you have been working with Azure Resoure Manager templates then you will have come across the need for apiVersion property on all resources. You will also have noticed that this is not consistent between resources.
<!--more-->
Essentially, not all resources are supported by the same api version. In order to determine what is supported where you can check on the [ARM schema github repository](https://github.com/Azure/azure-resource-manager-schemas).

