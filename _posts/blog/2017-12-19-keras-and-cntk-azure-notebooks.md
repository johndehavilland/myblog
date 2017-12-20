---
title: CNTK and Keras in Azure Notebooks
layout: post
modified: 
categories: blog
excerpt: 
tags:
- cntk
- keras
- jupyter
date: '2017-12-19'
image:
  feature: 
share: true,
comments: true
---

Being doing some work with Keras recently, which is a fantastic platform for making it simple to build out neural networks. Even better, it abstracts the underlying framework and allows you to use the one of your choice such as Tensorflow or CNTK without having to change your Keras code. With a simple config change you can switch the backend.

I also love using Azure Notebooks for quick and seamless prototyping and getting started. Keras is supported here and, by default, seems to use Tensorflow as the backend. Now, I wanted to see if I could switch that to CNTK - turns out it is pretty simple. Assuming you are using a python notebook you can run this before importing Keras:

{% highlight python %}
from keras import backend as K
import os
from importlib import reload

def set_keras_backend(backend):

    if K.backend() != backend:
        os.environ['KERAS_BACKEND'] = backend
        reload(K)
        assert K.backend() == backend

set_keras_backend("cntk")
{% endhighlight %}

![Keras and CNTK in Azure Notebooks]({{ site.images }}/azure-note-cntk.gif)