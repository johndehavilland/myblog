---
layout: post
title: "Cleaning up IIS Virtual Directories with PowerShell"
modified:
categories: blog
excerpt:
tags: ['IIS', powershell']
image:
  feature:
date: 2013-09-21T15:39:55-04:00
---

Sometimes, especially if you are working with a large amount of web applications or different versions of the same application, you find IIS gets cluttered with all the different web applications. It is quite tedious to go through and delete different ones when you want to clean up. I created this simple powershell script to allow you to remove web applications from a specified website within IIS from the command line.

{% highlight powershell %}
import-module WebAdministration
$existingWebApplications = Get-WebApplication
$website = Read-Host "What web site do you want to remove applications from?";
$webAppToRemove = Read-Host "What application do you want to remove (wildcard(*)?";

if($webAppToRemove){

    foreach($existingWebApp in $existingWebApplications){
        $webappName = $existingWebApp.Attributes[0].Value;
        if($webAppName -Like "/" + $webAppToRemove){
            Write-Host "Removing " + $webAppName
            if($website){
                Remove-WebApplication -Name $webAppName -Site $website
            }
            else{
                Remove-WebApplication -Name $webAppName -Site "Default Web Site"   
            }

        }
    }
}
else{
    Write-Host "Provide an application name";
}
{% endhighlight %}

Over all this is pretty useful to be able to just run to clean up IIS.