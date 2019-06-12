---
title: Using Apache Drill to Access Parquet Files in PowerBI
layout: post
modified: 
categories: blog
excerpt: 
tags:
- PowerBI
- parquet
- Apache Drill
- Azure Data Lake Store
- ADLS
date: '2019-06-10'
image:
  feature: 
share: true
comments: true
published: true
---

When you are working with data, especially larger data sets, you will come across parquet files. [Parquet](https://parquet.apache.org/) is a binary columnar storage format which is efficient for several large data use cases both in terms of compression and speed.

If you have built out your Data Lake on Azure (using either Azure Storage or Azure Data Lake Store) you may want to be able to connect and work with your parquet files in PowerBI. Since parquet requires a compute layer there are a few options to achieve this. A fairly simple and easy way to get started is use Apache Drill. Apache Drill is an open-source software framework that supports data-intensive distributed applications for interactive analysis of large-scale datasets. If you want to know why you would use Apache Drill check out more details [here](https://drill.apache.org/why/).

An easy way to get started with Apache Drill is via a container. I created a [github repo](https://github.com/johndehavilland/apachedrillazuredatalake) where you can leverage the full Dockerfile and setup to get started connecting Drill to Azure Data Lake Store. 

Below I describe some of the details of how to set it up if you were starting from scratch.

First you need to setup Apache Drill to talk with Azure Data Lake Store. This requires the following things to be setup:

1. You need to [download and install](https://drill.apache.org/docs/installing-the-driver-on-linux/#step-1:-download-the-drill-odbc-driver) the MapR Drill ODBC client driver. 

2. Setup the ini file paths as environment variables

3. Create an Azure AD service principle and get the app id and client secret. You will need to ensure this service principle has the right access rights to your data lake store files and folders.

3. Update the core-site.xml with the app id and key for accessing the data lake store files:

```xml
<property>
     <name>dfs.adls.oauth2.access.token.provider.type</name>
     <value>ClientCredential</value>
</property>
<property>
     <name>dfs.adls.oauth2.refresh.url</name>
     <value>https://login.microsoftonline.com/[TENANT_ID]/oauth2/token</value>
</property>
<property>
     <name>dfs.adls.oauth2.client.id</name>
     <value>[APPLICATION ID]</value>
</property>
<property>
     <name>dfs.adls.oauth2.credential</name>
     <value>[APPLICATION KEY]</value>
</property> 
<property>
     <name>fs.adl.impl</name>
     <value>org.apache.hadoop.fs.adl.AdlFileSystem</value>
</property>
<property>
     <name>fs.AbstractFileSystem.adl.impl</name>
     <value>org.apache.hadoop.fs.adl.Adl</value>
</property>
```

4. Update the storage-plugins-override.conf file to include a reference to the Azure Data Lake Store. This will allow you to use it as a storage layer.

![Storage plugin in Apache Drill]({{ site.images }}/drill_storage_plugin.png)
{:.post-image}
*Storage plugin in Apache Drill*
{:.image-caption}

```json
"storage":{
    datalake : {
    type : "file",
    connection : "adl://jdhdatalake.azuredatalakestore.net/",
    config : null,
    workspaces : {
      tmp : {
        location : "/tmp",
        writable : true,
        defaultInputFormat : null,
        allowAccessOutsideWorkspace : false
      },
      root : {
        location : "/",
        writable : false,
        defaultInputFormat : null,
        allowAccessOutsideWorkspace : false
      }
    },
    formats : {
      "psv" : {
        type : "text",
        extensions : [ "tbl" ],
        delimiter : "|"
      },
      "csv" : {
        type : "text",
        extensions : [ "csv" ],
        extractHeader : true,
        delimiter : ","
      },
      "tsv" : {
        type : "text",
        extensions : [ "tsv" ],
        delimiter : "\t"
      },
      "parquet" : {
        "type" : "parquet"
      },
      "json" : {
        type : "json",
        extensions : [ "json" ]
      }
    },
    enabled : true
   }
  }
```

5. Add the following three jars to jars/3rdparty:
    * hadoop-azure-2.7.3.jar
    * azure-data-lake-store-sdk-2.1.5.jar
    * hadoop-azure-datalake-3.0.0-alpha3.jar

There are newer versions of these but this worked for me.

Now start Apache Drill(either embedded or distributed). You should be able to browse to the UI (typically on port 8047). Here, under storage you should see the Data Lake Store storage plugin enabled. You can also run a test query and check it is working:

```
SHOW FILES FROM `datalake`;
```

```
SELECT * FROM datalake.`parquet/userdata1.parquet`
```

Finally, to connect to this via PowerBI, first make sure to install locally the MapR ODBC driver which you can get from [here](http://package.mapr.com/tools/MapR-ODBC/MapR_Drill/).

Then open PowerBI Desktop and choose ODBC.

Under ODBC, leave the top dropdown alone and under advanced enter the following:

`driver={MapR Drill ODBC Driver};connectiontype=Direct;host=localhost;port=31010;authenticationtype=No Authentication`

Make sure to choose *No Credentials* and, if all is good you should be able to view your data.