---
layout: post
title: "Sync Git master with fork"
modified:
categories: blog
excerpt:
tags: ['git', 'github', 'fork', 'master']
image:
  feature:
date: 2015-04-10T23:26:56-05:00
share: true,
comments: true
---

I recently had to fork a branch in github and then I had to figure out how to keep the changes from the master in sync with the fork branch I had made. Turns out it is really simple.

First you have to add a remote to point to the original master:

    git remote add upstream <repo-location>

then to keep it in sync just run the following commands:

    git fetch upstream
    git rebase upstream/master

And that is it.