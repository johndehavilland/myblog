---
title: Live Video Broadcast and Mixing in Azure with Teams
layout: post
modified: 
categories: blog
excerpt: 
tags:
- Broadcasting
- Azure
- Virtual Control Room
date: '2020-11-15'
image:
  feature: 
share: true
comments: true
published: true
---

In media broadcasting, with a continued focus on moving production to the cloud, the idea of virtual production control rooms are taking root. A production control room is the place were incoming feeds are combined together and the outgoing program is created. Virtualizing this brings several key benefits:

* Ability to **scale up and down** as needed for live events - no need for expensive investment in hardware up front
* Enabling a **remote workforce** - important for geographically dispersed production
* **Increased productivity** since you can now potentially produce multiple events per day without the need to go from venue to venue.

I want to demonstrate how easy it is to setup a simple virtual production control room in Azure. We will keep it simple, using [vMix](https://www.vmix.com/) as our mixer and [Microsoft Teams](https://www.microsoft.com/en-us/microsoft-365/microsoft-teams/group-chat-software) for capturing video feeds locally and sharing video via [NDI](https://ndi.tv/) to the master control in Azure. The setup is simple to demonstrate the capabilities but can easily be adjusted to accomodate more traditional broadcasting workflows.

![virtual control room architecture]({{ site.images }}/vpcr-arch.png)
{:.post-image}
*Virtual Production Control Room Architecture*
{:.image-caption}

The core components in Azure will be a virtual network and a GPU-based Virtual Machine. In addition, you will want to set up a private network connection to your location where you will be interviewing via Teams - this will allow a secure way to capture the Teams video feeds via NDI.  

We will use [Teradici](https://www.teradici.com/) to remotely access the vMix platform. Teradici provides a low latency remote desktop protocol (PCoIP) which is perfect for video scenarios like mixing that require minimal latency to ensure great user experience and the ability for editors to handle programming for live scenarios. 

Finally, we want to be able to capture high-quality individual video streams from 2 or more participants from a Teams call. Imagine a live interview being conducted as a scenario where this applicable. In order to do this, we will be using NDI within Teams to broadcast the streams to our mixer in Azure. 

NDI is a protocol, developed by NewTek that enable video-compatible products to communicate, deliver, and receive high-definition video over a computer network in a high-quality, low-latency manner that is frame-accurate and suitable for switching in a live production environment. 

The steps to create this in Azure is as follows: 
1. Create a [NV series](https://docs.microsoft.com/en-us/azure/virtual-machines/nv-series) machine with Windows 2019 – ideally a Standard_NV12s_v3. 
2. Install on this VM the folllowing: 
* [Newtek NDI Tools](https://ndi.tv/tools/) 
* [vMix](https://www.vmix.com/) 
* [Teradici Graphics Agent](https://docs.teradici.com/find/subscription/cloud-access-plus/product/cloud-access-software/component/graphics-agent-for-windows/2.15.0)
3. Now you will need setup a private connection from the Azure VNet to your local site where Teams will be running. This could be an ExpressRoute. For this scenario, you can simply set up a Point to Site VPN by following [this guide](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal). This will create a private channel to enable capturing NDI streams in vMix running in your virtual production control room. 
4. You will need to enable the following ports in Azure to allow NDI to work: 5960-65535, 5353 and the following ports for Teradici: 4172, 60443
5. You need to enable NDI in Teams. Once it is enabled you can then start your interview call in Teams and turn on NDI Broadcast. This will start to broadcast the video streams using NDI.

![NDI in teams]({{ site.images }}/vpcr-ndi-teams.png)
{:.post-image}
*Enable NDI in Teams*
{:.image-caption}

6. You can now connect to your Azure VM using the Teradici PCoIP client.
7. Once connected, open vMix and add input as NDI – you should see the Teams video streams available to add.

![NDI in vMix]({{ site.images }}/vpcr-ndi-vmix.png)
{:.post-image}
*Capture NDI in vMix*
{:.image-caption}

8. At this point you can now mix these together in vMix and create a live output to send downstream.

![Mixing in vMix]({{ site.images }}/vpcr-ndi-mix.png)
{:.post-image}
*Mixing NDI in vMix*
{:.image-caption}

As you can see, this is a fairly easy scenario to get setup in Azure and you can start enabling your teams right away to take advantage of these capabilities. 