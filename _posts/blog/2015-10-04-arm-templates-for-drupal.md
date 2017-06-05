---
layout: post
title: "Using Azure Resource Manager templates for deploying Drupal 7"
modified:
categories: blog
excerpt:
tags: ['Azure', 'ARM', 'Drupal', 'Azure Resource Manager']
image:
  feature:
date: 2015-10-04T23:26:56-05:00
share: true,
comments: true
---

This post is about automating deployment into Azure for Drupal 7. This will focus on leveraging [Azure Resource Manager](https://azure.microsoft.com/en-us/documentation/articles/resource-group-overview/) (ARM) and ARM templates to define the infrastructure as well as wire up the continous deployment processes. 

The environment will consist of the following:

1. Drupal 7 deployed to an Azure Web App
2. Azure SQL Database

Our pipeline will be as follows:

1. Code is committed to a repository - in this case bitbucket
2. A script is kicked off that spins up the environment (as described above).
3. The code is deployed.
4. The application is configured post deployment.

For the repository I am going to leverage [bitbucket](http://www.bitbucket.org) with a git repository setup in it. This will be where I will push my code.

Next, I need to create a template that defines the environment. This will be a JSON template which will be passed into the Azure Resource Manager to provision the resources. There is also a parameters file that accompanies the template allowing for parameterization of various elements.

The template can be seen in full [here](https://github.com/johndehavilland/drupalonazure/blob/master/drupal-basic.json).

Some things to point out:

* The Web App requires an App Service Plan (this defines the compute resources that back it). In my template I have that defined and make the Web App depend on it being created. By leveraging the **dependsOn** property you can create dependencies in your template:

{% highlight javascript %}
dependsOn: [
    "[resourceId('Microsoft.Web/serverFarms', parameters('hostingPlanName'))]"
]
{% endhighlight %}

* For the Azure SQL resource, a sub-resource is defined with the name **AllowAllWindowsAzureIps** which will open up the firewall to all Azure resources. This will allow the web app to communicate to the database through the firewall. For more secure environments you could opt for grabbing the specific IP address of the web app and using that instead.


* I have added the database connection string to the web app resource (as a sub resource). This will add the connection string details of the database to the web app to allow me to use it for things like automated backups.


* I have connected the web app to my bitbucket repository using the **sourcecontrols** subresource. This will automatically connect using my credentials and after the web app has been provisioned it will pull down any commits. Subsequent commits to the repo will also get automatically deployed. In order for this to work you have to set **IsManualIntegration** to false.

{%highlight javascript %}
{
    "apiVersion": "2015-04-01",
    "name": "web",
    "type": "sourcecontrols",
    "dependsOn": [
        "[resourceId('Microsoft.Web/Sites', parameters('siteName'))]"
    ],
    "properties": {
        "RepoUrl": "<repo-url>",
        "branch": "master",
        "IsManualIntegration": false
    }
}
{% endhighlight %}


* I have added a section to setup some site parameters to hold my database information as part of the web app. This will allow them to be referenced later as environment variables in my settings.php. The values will be pulled from the created database during provisioning.

{%highlight javascript %}
{
    "apiVersion": "2015-06-01",
    "name": "appsettings",
    "type": "config",
    "dependsOn": [
        "[resourceId('Microsoft.Web/Sites', parameters('siteName'))]"
    ],
    "properties": {
        "db_name": "[parameters('databaseName')]",
        "db_host": "[reference(concat('Microsoft.Sql/servers/', parameters('serverName'))).fullyQualifiedDomainName]",
        "db_user": "[concat(parameters('administratorLogin'), '@', parameters('serverName'))]",
        "db_pass": "[parameters('administratorLoginPassword')]"
    }
}
{% endhighlight %}

At this point my json template describing the environment is set. To test that it works I can run the following command (using the [Azure CLI](https://azure.microsoft.com/en-us/documentation/articles/xplat-cli-azure-resource-manager/)):

    azure group create "<name-of-resource-group>" "<azure-region>" -f drupal-deploy.json -d "<name-of-deployment>" -e params.json

To check the status of the deployment you can see the logs in the portal or via the command line through the following command:

    azure group log show <name-of-resource-group>
    
After deployment you should now see the resource group in the Azure portal. You should be able to browse to the site and it should launch and you should see the standard Azure Web App welcome page. 

Now that the environment is deployed and hooked up to my repository I need to change some commands so that when the latest bits are grabbed from the repository, Drupal 7 is setup and installed.

To customize steps after grabbing the latest commit you need to create a **.deployment** file and check that into your repo. My .deployment file is as follows:

>[config]
>
>command = site-creation.bat

This essentially instructs [Kudu](https://github.com/projectkudu/kudu) (the engine behind deployments for Azure Web apps) to call a batch file called **site-creation.bat**. This is a custom batch file that I created and added to the repo.

My batch file does the following steps:

1. Creates a directory for **drush**. [Drush](http://www.drush.org/en/master/) is command line shell for Drupal.
2. Downloads drush for Windows using curl
3. Unzips the downloaded zip into the drush directory.
4. Copies over a custom drush.bat to allow drush to use the php version my web app is using. Otherwise it uses the version it ships with which causes issues later.
5. Calls drush make using a custom makefile that is also part of my repo. Drush make allows you to pass in a make file telling drush which version of drupal to deploy and which modules to install. In my makefile I use Drupal 7 and install 2 additional modules: Sql Server module and Azure Storage Module. 
6. The output of drush make is copied to site\wwwroot as this is the entry point for the public site hosting.
7. The custom settings file is copied to the default sites directory. This will allow the database to be correctly found on the install.
8. There is an additional copy required for the sql server module.
9. Finally I call drush site-install to go ahead and install drupal. The install step configures the database and sets up some site variables. 

Checking these into my repository will trigger the web app to pick up the new commit and begin executing my site-creation.bat. Upon completion I have a fully functioning Drupal 7 site using Azure SQL Server. I can then go and deploy to an entirely new resource group to have it provision the same environment, deploy Drupal 7 and then install it for me all with a simple command - **azure group create**.

The full templates and batch files can be found [here](https://github.com/johndehavilland/drupalonazure).
