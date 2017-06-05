---
layout: post
title: "BREACH Attack"
modified:
categories: blog
excerpt:
tags: ['security']
image:
  feature:
date: 2014-07-26T15:39:55-04:00
---

Back in August 2013, at the Blackhat conference, security researchers Angelo Prado, Neal Harris and Yoel Gluck announced BREACH.

BREACH (Browser Reconnaissance and Exfiltration via Adaptive Compression of Hypertext) is an exploit against HTTPS when using HTTP compression. It is based on the earlier discovered compression exploit, CRIME.

The attack is relatively straightforward:

* End user is browsing over open wifi (or some other channel) where an attacker can evasedrop on all the user's network traffic.

* The attacker tricks the user into visiting a website of their choosing - perhaps even via man in the middle (MITM) and redirecting you.

* This website the user now visits will initiate a series of requests to the target web site using JavaScript.

* Since the attacker is monitoring the traffic they can see the length of the bytes being sent back.

Being able to see the length allows the attacker to attempt to figure out some secret on the website's page (e.g. a CSRF token). When a querystring parameter is appended to the URL then, chances are, it will also be part of the response body. With compression enabled if the query string you pass in matches something else in the response then that will compress better than if it did not match. With this knowledge a hacker can brute-force different combinations into the querystring parameter and compare the lengths of the response. The more it matches the better the compression will be and so the lowest length would mean that that querystring parameter value is the value you were seeking.

Using brute force making many requests the attacker can figure out and uncover certain secrets. Depending on the secret the attacker could potentially make requests to the target website pretending to be you or steal key pieces of data.

1. To mitigate against this there are some options (listed below in order of effectiveness):

2. Disabling HTTP compression

3. Separating secrets from user input

4. Randomizing secrets per request

5. Masking secrets (effectively randomizing by XORing with a random secret per request)

6. Protecting vulnerable pages with CSRF

7. Length hiding (by adding random number of bytes to the responses)

8. Rate-limiting the requests

More can be learnt from [breachattack.com](http://www.breachattack.com/) The team also released a basic tool to allow you to test your own website which can be found here - [https://github.com/nealharris/BREACH](https://github.com/nealharris/BREACH)