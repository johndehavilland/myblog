---
layout: post
title: "Update to the Hacker News App"
modified:
categories: blog
excerpt:
tags: ['javascript', 'hacker-news', 'windows8', 'winjs']
image:
  feature:
date: 2014-01-19T15:39:55-04:00
---

I have finally finished some updates on my first foray into Windows 8 apps. Last summer I uploaded, to the Windows Store, version 1 of my [Hacker News App](http://apps.microsoft.com/windows/en-us/app/hacker-news-app/d26f7f82-0c9d-46fa-aecf-f02f817a0729) which, using the bigrss feed from Hacker News, displayed the top stories from the popular tech news aggregator. It was pretty basic - it showed comments for each story and allowed the viewer to view the original story. For release 2 I added a couple of new features that I think will make it more useful as well as made a couple of bug fixes and UX improvements.

Highlights include:

1. Offline Stories

This one I feel will be quite useful - at least for me. Often I see several interesting stories and want to read them but I find the best places to read the articles are places where I don't have internet connectivity. This feature goes a little way to address that. You can select a story and save it for offline viewing. This stores it in the application's database for later viewing. It is fairly crude at the moment but seems to do the job.

2. Added newest stories and Ask HN stories options

I added the ability to view the newest submitted stories to HN as well as the ability to filter to the latest Ask HN stories. I personally just browse the front page of HN but others may find this useful.

3. Improved UX experience

I modified the app this time to follow the MS guidelines a little more closely. I am still struggling somewhat with the UX styling options, especially getting it right for the various different views - it is no easy task. I made some improvements this time but I feel some of it needs a little more work.

4. I added a couple of app wide settings.

These settings will allow the user to choose the default view for the home page (newest, frontpage, Ask HN) as well as the default link behavior (show comments, show article, show both).
Overall, this release will be more feature rich and will hopefully provide an improved user experience.

It is available in the [Windows Store](http://apps.microsoft.com/windows/en-us/app/hacker-news-app/d26f7f82-0c9d-46fa-aecf-f02f817a0729).