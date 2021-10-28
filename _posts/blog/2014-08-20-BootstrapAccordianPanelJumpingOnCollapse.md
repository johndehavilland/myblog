---
layout: post
title: "Panel Jumping On Collapse"
modified:
categories: blog
excerpt:
tags: ['user-experience', 'bootstrap']
image:
  feature:
date: 2014-08-20T15:39:55-04:00
---

I added a bootstrap panel to a webpage with collapsing sections. The requirement was for each panel to remain open until it was specifically closed. This worked fine, however, I noticed that on closing the panel there would appear to be a jump effect which did not look nice at all.
<!--more-->
This can be seen here in this [fiddle](http://jsfiddle.net/johndehavilland/247x1rbv/).

Turns out the reason for this issue is because of the use of the `a` tag for the panel headings. When collapsing it automatically jumps to the top of the link, which causes the jumping effect you see. This occurs when expanding the panel causes the page to scroll.

To correct this simply use a `span` tag instead as demonstrated in this [fiddle](http://jsfiddle.net/johndehavilland/oszb3y1t/1/).
