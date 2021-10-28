---
layout: post
title: "Using Azure Batch to unzip large number of files"
modified:
categories: blog
excerpt:
tags: ['Azure', 'AzureBatch']
image:
  feature:
date: 2016-04-23T00:00:00-05:00
share: true,
comments: true
---

If you ever have a need to unzip a large number of files that are sitting in Azure Storage then one option is to use Azure Batch. In this post I will show how easy it is to create an application that leverages Azure Batch to unzip files sitting in Azure Storage and place the extracted files back into Azure Storage. A full working solution is available on my github repository [here](https://github.com/johndehavilland/UnzipperOnAzureBatch).
<!--more-->
[Azure Batch](https://azure.microsoft.com/en-us/documentation/services/batch/) enables you to run large-scale parallel and high performance computing (HPC) applications efficiently in the cloud. It is a PaaS offering, which means the service will manage the cluster of compute for you and all you have to worry about is creating your logic and submitting to the batch service for execution across the nodes. 

In the scenario I will be describing how to create a program that will perform the following steps:

1. Create a pool of compute nodes within Azure Batch.
2. Create a job within Azure Batch to contain all my tasks.
3. Iterate over all files in a targeted Azure storage container.
4. Create unzip tasks for each file.
5. Add those tasks to the job.
6. Execute the job on the created  pool.
7. Monitor execution.
8. Clean up resources on completion.

First, you will need to create a [Storage Account](https://azure.microsoft.com/en-us/documentation/articles/storage-create-storage-account/) and an [Azure Batch Service](https://azure.microsoft.com/en-us/documentation/articles/batch-account-create-portal/). Once those are created you are ready to start creating your solution. You may already have an existing storage account with your zip files in - if so you can use that.

In Visual Studio, create a console application project. This will be what we execute to run our extraction process as well as creating the Azure Batch pool, job and tasks. 

###Unzipper Class
Create a class for the extraction logic called **UnzipperTask**. This will be what is executed for each task. The input will be simply the input uri for the zipped file. Create a method within in this class called TaskMain

{% highlight csharp %}
public static void TaskMain(string[] args)
{% endhighlight %}

This method expects to be passed four arguments: unzipper.exe --Task &lt;blobpath&gt; &lt;storageAccountName&gt; &lt;storageAccountKey&gt;. *Blobpath* is the URI to the zipped blob. Grab the arguments and create a client to the storage account and to the blob. Below shows how to create a reference to the blob.

{% highlight csharp %}
var storageCred = new StorageCredentials(storageAccountName, storageAccountKey);
CloudBlockBlob blob = new CloudBlockBlob(new Uri(blobName), storageCred);
{% endhighlight %}

Create a reference to the output container as well - this is where all extracted files will be placed.

Using the Stream function, download the blob as a stream, use the ZipArchive class to extract it and then upload it to the output container.

{% highlight csharp %}
using (Stream memoryStream = new MemoryStream())
{

    blob.DownloadToStream(memoryStream);
    memoryStream.Position = 0; //Reset the stream

    ZipArchive archive = new ZipArchive(memoryStream);
    
    foreach (ZipArchiveEntry entry in archive.Entries)
    {
        CloudBlockBlob blockBlob = outputContainer.GetBlockBlobReference(entry.Name);

        blockBlob.UploadFromStream(entry.Open());
    }
}
{% endhighlight %}

###Job Class
Now create a class for creating the Batch pool and jobs called **Job**. Add 2 constants to hold reference to any dlls and exes that need to be uploaded (this will make it easier to reference later on). In this case, just the program itself and the storage dll.

{% highlight csharp %}
private const string UnzipperExeName = "unzipper.exe";
private const string StorageClientDllName = "Microsoft.WindowsAzure.Storage.dll";
{% endhighlight %}

Create a method called Main(). First create a storage client to a staging storage account (this will be where the dlls are uploaded to). This can be the same storage account the zip files are in if you want. Next create a batch client to reference your Azure Batch Service.

{% highlight csharp %}
using (BatchClient client = BatchClient.Open(new BatchSharedKeyCredentials(unzipperSettings.BatchServiceUrl, unzipperSettings.BatchAccountName, unzipperSettings.BatchAccountKey)))
{% endhighlight %}

Now create a method called CreatePool and within this create a pool on the Azure Batch Service.

{% highlight csharp %}
CloudPool pool = client.PoolOperations.CreatePool(
                poolId: unzipperSettings.PoolId,
                targetDedicated: unzipperSettings.PoolNodeCount,
                virtualMachineSize: unzipperSettings.MachineSize,
                cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));
            pool.MaxTasksPerComputeNode = unzipperSettings.MaxTasksPerNode;
{% endhighlight %}

Here you define the compute settings within the pool. This includes how many target nodes, what the machine sku size of each node is, how many tasks per node and what OS family to use on the nodes. It is worth noting that when you submit the tasks to the pool they will be executed on each node until the max tasks per node is reached and then across each node until all the nodes are in use.

You then need to commit this pool to the batch service to create it.

{% highlight csharp %}
pool.Commit();
{% endhighlight %}

Next, create a method called CreateJob. This will create a job to which you will assign tasks. Within this method add the following code to create the job.

{% highlight csharp %}
CloudJob unboundJob = client.JobOperations.CreateJob();
unboundJob.Id = unzipperSettings.JobId;
unboundJob.PoolInformation = new PoolInformation() { PoolId = unzipperSettings.PoolId };

// Commit Job to create it in the service
unboundJob.Commit();
{% endhighlight %}

Next, create a method called CreateTasks. This will create tasks for each zipped file. First step is to add a reference to the dll and exe you need to execute for each each task as FileToStage objects.

{% highlight csharp %}
FileToStage unzipperExe = new FileToStage(UnzipperExeName, stagingStorageAccount);
FileToStage storageDll = new FileToStage(StorageClientDllName, stagingStorageAccount);
{% endhighlight %}

Now, get a list of all zipped files within the specified container.

{% highlight csharp %}
var container = client.GetContainerReference(unzipperSettings.Container);
var list = container.ListBlobs();
{% endhighlight %}

For each zipped file create a task.

{% highlight csharp %}
CloudTask task = new CloudTask("task_no_" + i, String.Format("{0} --Task {1} {2} {3}",
                    UnzipperExeName,
                    zipFile.Uri,
                    unzipperSettings.StorageAccountName,
                    unzipperSettings.StorageAccountKey));


task.FilesToStage = new List<IFileStagingProvider>
                            {
                                unzipperExe,
                                storageDll
                            };
{% endhighlight %}

Add these tasks to a list and return that list from the method. The CloudTask object takes a task id and a command line. The command line here will be the unzipper.exe along with the --Task parameter and a couple of additonal parameters such as the input blob uri.

Next, create a AddTasksToJob method. In this method you will add all the tasks to the job.

{% highlight csharp %}
client.JobOperations.AddTask(unzipperSettings.JobId, tasksToRun, fileStagingArtifacts: fsArtifactBag);
{% endhighlight %}

In order to monitor the status of your tasks as the execute, create a method called MonitorProgress. This method will wait on all tasks being completed (or a timeout being reached). It is worth noting that if the program ends at this stage unexpectedly and does not execute the clean up logic then the tasks will continue processing.

{% highlight csharp %}
IPagedEnumerable<CloudTask> ourTasks = job.ListTasks(new ODATADetailLevel(selectClause: "id"));
client.Utilities.CreateTaskStateMonitor().WaitAll(ourTasks, TaskState.Completed, TimeSpan.FromMinutes(120));
{% endhighlight %}

Finally, when all the tasks are complete you can clean up the pool and job and any resources.  Create a method called Cleanup and add clean up operations based on settings flags. Since the pool holds the compute nodes, if you plan to reuse again very quickly afterwards you may not want to delete the pool each time. This is especially typically when doing development and testing as the pool creation can take several minutes.

{% highlight csharp %}
//Delete the pool that we created
if (unzipperSettings.ShouldDeletePool)
{
    Console.WriteLine("Deleting pool: {0}", unzipperSettings.PoolId);
    client.PoolOperations.DeletePool(unzipperSettings.PoolId);
}

//Delete the job that we created
if (unzipperSettings.ShouldDeleteJob)
{
    Console.WriteLine("Deleting job: {0}", unzipperSettings.JobId);
    client.JobOperations.DeleteJob(unzipperSettings.JobId);
}

//Delete the containers we created
if (unzipperSettings.ShouldDeleteContainer)
{
    DeleteContainers(unzipperSettings, stagingContainer);
}
{% endhighlight %}

Call all these methods in sequence from your Job.Main method.

{% highlight csharp %}
CloudPool pool = CreatePool(unzipperSettings, client);

try
{
    CreateJob(unzipperSettings, client);

    List<CloudTask> tasksToRun = CreateTasks(unzipperSettings, stagingStorageAccount);

    AddTasksToJob(unzipperSettings, client, stagingContainer, tasksToRun);

    MonitorProgess(unzipperSettings, client);
}
finally
{
    Cleanup(unzipperSettings, client, stagingContainer);
}
{% endhighlight %}

Finally, update the Program.cs Main method to have the following logic.

{% highlight csharp %}
if (args != null && args.Length > 0 && args[0] == "--Task")
{
    UnzipperTask.TaskMain(args);
}
else
{
    Job.JobMain(args);
}
{% endhighlight %}

What this does is call the job creation process if the parameter **---Task** is not passed. When the Tasks are created, they call the same exe with the **---Task** parameter which triggers the extraction process.

###Summary
At this point you can build and run your project and it should execute. A full working solution is available on my github repository [here](https://github.com/johndehavilland/UnzipperOnAzureBatch) (just remember to change the Settings.settings file to point to your batch service and storage accounts before running). In addition, the [Batch Explorer tool](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer) is very useful for connecting to your Batch service account and seeing what is created and what is executing. More examples of Batch Service progams can be found [here](https://github.com/Azure/azure-batch-samples).