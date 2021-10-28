---
title: A Lap Around Some Machine Learning Frameworks
layout: post
modified: 
categories: blog
excerpt: 
tags:
- machinelearning
- analytics
- deeplearning
date: '2017-10-26'
image:
  feature: 
share: true,
comments: true
---

Below I have highlighted some of the commonly used, open source, machine learning frameworks you will find in use today.
<!--more-->
## Tensorflow
  * Deep learning framework created by the Google Brain Team 
  * Started out as a proprietary ML system based on deep neural networks at Google
  * Can run on CPUs and GPUs.
  * Focuses on graph based computations - used in neural networks
  * Has Python support and a C api.
  * Check out [samples here](https://learningtensorflow.com/examples/)
  * Check it out [here](https://www.tensorflow.org/get_started/)
  
## Microsoft Cognitive Toolkit
  * Deep learning framework created by Microsoft Research
  * Formerly known as CNTK
  * Creates neural networks via directed graphs.
  * Works on CPU and GPU
  * Works with Python as well as C#, Java and C++
  * Significantly faster in some circumstances than other frameworks
  * Check out [examples here](https://docs.microsoft.com/en-us/cognitive-toolkit/Examples)
  * Check it out [here](https://docs.microsoft.com/en-us/cognitive-toolkit/getting-started)

## Theano
  * Python based numerical computational library
  * Developed primarily by the ML group at Montreal University
  * Major development will cease by end of year due to the evolving ecosystem and stronger players their own libraries.
  * Check it out [here](http://deeplearning.net/software/theano/)

## Torch
  * Created by Ronan Collobert, Koray Kavukcuoglu and Clement Farabet. 
  * Uses Lua as the scripting language
  * Focus is on GPU computations
  * Has neural network capabilities as well as support for popular optimization libraries 
  * Large set of samples and good community.
  * Google's DeepMind used Torch up until a year ago  when they transitioned to TensorFlow
  * Check it out [here]( http://torch.ch/)
  
## Caffe
  * Deep learning framework developed by UC Berkley
  * Models are created via configuration (vs. coding) making it potentially easier to create models;
  * It is very fast - example it can process 60m images per day on a single NVIDA K80 GPU
  * Extensible code and decent community
  * Check it out [here](http://caffe.berkeleyvision.org/)
  
## Caffe2
  * Built by Facebook - an extension to Caffe
  * Aims for ML in production especially on mobile devices as well as large scale deployments
  * Has these improvements over Caffe
    * first-class support for large-scale distributed training
    * mobile deployment
    * new hardware support (in addition to CPU and CUDA)
    * flexibility for future directions such as quantized computation
    * stress tested by the vast scale of Facebook applications
  * Check it out [here](https://caffe2.ai/)
  
## Keras
  * Created by François Chollet
  * Neural network library written in Python.
  * Can run on top of several different frameworks (e.g. Tensorflow, Microsoft CNTK)
  * Keras is more an abstraction layer over underlying frameworks making it very easy to create and configure a neural network regardless of the backend library.
  * Check it out [here](https://keras.io/)
  
## Apache Spark Mllib
  * Built on top of apache spark - an open source cluster-computing framework leveraging memory over disk i/o for far superior performance over frameworks like Hadoop
  * 2 packages - MLLib and ML
  * ML provides higher level api over dataframes but does not have all the algorithms that MLLib has
  * Can be as 9x fast as disk based Mahout
  * Includes many common machine learning algorithms
  * Check it out [here](https://spark.apache.org/mllib/)
  
## Apache Mahout
  * Mahout means Elephant Rider.
  * Uses Samsara, a vector math experimentation environment with R-like syntax which works at scale
  * Previously, Amazon used it to for recommendations
  * Sits on top of MapReduce and is fairly mature but constrained by disk i/o  -slow and not good with intensive jobs. Work is underway to move to Spark.
  * Focuses on collaborative filtering, clustering and classification
  * Check it out [here](http://mahout.apache.org/)
