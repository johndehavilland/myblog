---
layout: post
title: "Migrating from AWS to Azure using Azure Site Recovery"
modified:
categories: blog
excerpt:
tags: ['Azure', 'AWS', 'Azure Site Recovery', 'ASR', 'migration']
image:
  feature:
date: 2015-06-02T23:26:56-05:00
share: true,
comments: true
---

With Azure Site Recovery you can migrate VMs from Physical Servers to Azure. This opens up a variety of useful use cases such as 

* Migrating instances from AWS to Azure
* Having a Azure as a failover site for AWS instances.

This guide will walk through how simple it is to use ASR to migrate an AWS VM to Azure.

1\. First, you will need to sign up (or have access to) an Azure Subscription.

2\. Create an Azure Site Recovery vault in the Azure Management Portal.

![Create Azure Site Recovery Vault]({{ site.images }}/asw-asr/asr_vault_create.png)

3\. Now you will need to setup a VNET for holding the migration resources. To do that click the new button and choose Network Services --> Virtual Network --> Quick Create

![Create a new VNET]({{ site.images }}/asw-asr/asr_dropdown.png)

4\. Click into the newly created vault in the portal and you should see a set of steps.

![Azure Site recovery start page]({{ site.images }}/asw-asr/asr_steps.png)

5\. From the *Setup Recovery* drop down choose *Between an on-premise site with Vmware/physical servers and Azure (NEW)*. If you don't see this option then you will need to sign up for the preview and wait for it to be enabled.

![Azure Site Recovery dropdown]({{ site.images }}/asw-asr/asr_dropdown.png)

6\. Now you can easily follow the steps that are helpfully outlined on this quick start page. First step is to prepare the target resources in Azure. 

a. **Deploy a Configuration Server.** This will create a standard A3 (4 cores, 7GB memory) VM in the vnet you created eariler.

![Azure Site Recovery Config Server]({{ site.images }}/asw-asr/asr_configsrv.png)

b. Download the registration key to your local computer. This will download a .vaultcredentials file. 

c. Once the config server VM has been deployed go ahead and connect to it. You can do this by navigating to Virtual Machines in the portal and finding the Config Server that was deployed.

![Azure Site Recovery Config Server VM]({{ site.images }}/asw-asr/asr_cfgsrvvm.png)

d. RDP into the VM, ignore the dialog and copy over the downloaded .vaultcredentials file you downloaded from the portal in step b.

e. When you RDP into to the Configuration server VM you will see this dialog:

![Azure Site Recovery Config Server VM Wizard]({{ site.images }}/asw-asr/asr_cfgsrv_wxd.png)

Follow the steps. This will install MySql on to the Config Server. During the process you will setup the passwords for various MySql users and you will have to provide the Vault Registration key (which you downloaded from the portal and copied to this VM).

 ![Azure Site Recovery Config Server VM Vault Credentials]({{ site.images }}/asw-asr/asr_cfgsrv_vc.png)

 It will then go ahead and download MySql and install it. The process takes no more than 5 minutes.

 At the end a box will pop up that gives you the password for the agent. **It is important to note this down**

![Azure Site Recovery Config Server VM Connection password]({{ site.images }}/asw-asr/asr_cfgsrv_connpwd.png)
     
7\. You should now see the configuration server appear under the *Servers* tab in the ASR vault.

![Azure Site Recovery Config Server in vault]({{ site.images }}/asw-asr/cfgsrv_vault.png)
    
8\. Create and deploy the Master Target Server. Go back to the quick start page on the ASR Vault and click *Deploy Master Target Server*.

9\. When the Master Target VM is created, go to Virtual Machines tab in the portal, select the newly created master target server and RDP in to complete the process. When you connect you will be prompted for some connection information. Enter the internal ip address for the Configuration Server (created in step 6a) and use the passphrase that you saved from earlier. (step 6e).

![Host Agent Config]({{ site.images }}/asw-asr/host_agent_config.png)

10\. In the portal, go to the *Servers* page of the Vault and press Refresh. Then drill down into the Configuration server and check to see if the Master Target server now appears. If it does you are on the right track.

![Host Agent Config]({{ site.images }}/asw-asr/mstr_trgt.png)

11\. At this point we are ready to start protecting machines in a source environment - in this case AWS. In my test AWS environment I created a simple t1.micro Windows machine. This is the machine I plan to migrate over to Azure. The first step is, in the AWS environment, we need to create a process server in the same network that the machines we want to migrate are in. This process server will need to be somewhat beefy - I chose an m3.large. 

[This article](http://azure.microsoft.com/blog/2015/01/22/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/) provides some guidance on process server sizing.
When you have created such a machine you now need to install the process server on it. Note that you need at least 8GB free on the disk.

a. RDP into the created process server and download the Process Server installers from the ASR Vault home page.

b.  Extract these two programs:

    *   Microsoft-ASR_CX_TP_8.2.0.0_Windows.exe
    *   Microsoft-ASR_CX_8.2.0.0_Windows.exe

c. Run Microsoft-ASR_CX_TP_8.2.0.0_Windows.exe which installs some third party tools.

d. Run Microsoft-ASR_CX_8.2.0.0_Windows.exe. On server mode choose "Process server".

![ASR Process Server]({{ site.images }}/asw-asr/asr_process.png)

e. You will then need to enter the Configuration Server details. Enter the public ip address of the config server and public https port (unless you have a VPN connection setup between environments - in which case you can use the internal ip address). You can find this information from the VM dashboard in the Azure Management portal. You will also need to enter the password you captured earlier.

![ASR Process Server Config]({{ site.images }}/asw-asr/asr_process_cfg.png)

f. Validate that the Process Server is correctly registered with the Configuration Server by going back to the Azure Management Portal and looking under Vault --> Configuration Server --> Server Details

![ASR Process Server]({{ site.images }}/asw-asr/asr_processsrv.png)

12\. Create the protection group within the Azure Site Recovery vault.

![ASR Protection Group]({{ site.images }}/asw-asr/asr_prot.png)

a. First create the group, indicating from and target.

![ASR Protection Group]({{ site.images }}/asw-asr/asr_prot_sett.png)

b. Then set up the replication settings.

**Multi VM consistency**: If you turn this on it creates shared application-consistent recovery points across the machines in the protection group. This setting is most relevant when all of the machines in the protection group are running the same workload. All machines will be recovered to the same data point. Only available for Windows servers.

**RPO threshold**: Alerts will be generated when the continuous data protection replication RPO exceeds the configured RPO threshold value.

**Recovery point retention**: Specifies the retention window. Protected machines can be recovered to any point within this window.

**Application-consistent snapshot frequency**: Specifies how frequently recovery points containing application-consistent snapshots will be created.

![ASR Protection Group Replication Settings]({{ site.images }}/asw-asr/asr_prot_rep.png) 
 
13\. Once that is created you will need to install the mobility service onto your source machines from your process server. This can be done manually or automatically. In order to be done automatically there are a list of things that need to be in place - see [here](https://azure.microsoft.com/en-us/documentation/articles/site-recovery-vmware-to-azure/#step-14-manually-install-the-mobility-service-on-source-machines)

14\. Add the target machine to a protection group.

 ![ASR Add Physical Machine]({{ site.images }}/asw-asr/asr_add_phys.png) 
 
15\. Then the protection job will start to initialize the protection

 ![ASR Protection Job]({{ site.images }}/asw-asr/asr_prot_job.png) 
 
16\. After protection has been enabled, data will be synced and replicated. This initially may take several hours depending on the speed of the connection and the size/# of machines being protected.

 ![ASR Protection Synchronizing]({{ site.images }}/asw-asr/asr_prot_sync.png) 
 
17\. After a machine has a **Protected** status you can configure its failover properties. In the protection group details select the machine and open the Configure tab.

 ![ASR Machine Spec]({{ site.images }}/asw-asr/asr_prot_mch.png)
 
18\. Add a recovery plan. 

 ![ASR Recovery Plan]({{ site.images }}/asw-asr/asr_rec.png)

19\. Add the virtual machines into the Recovery plan.
 
 ![ASR Recovery Plan Machines]({{ site.images }}/asw-asr/asr_rec_mch.png)
 
20\. Now you are ready to failover. From the bottom bar choose **Failover**

 ![ASR Failover]({{ site.images }}/asw-asr/asr_fail.png)

21\. Confirm the failover.

 ![ASR Confirm Failover]({{ site.images }}/asw-asr/asr_confirm.png)
 
22\. When the failover is complete you should see all the steps with a green check.

![ASR Failover Complete]({{ site.images }}/asw-asr/asr_fail_complete.png)

At this point if you go to the VM tab in Azure you should see your migrated VM now up and running. You may need to add a public endpoint to allow you the ability to RDP in.

This completes the migratation. You can find out more details about Azure Site Recovery [here](http://azure.microsoft.com/en-us/services/site-recovery)