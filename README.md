Back up an Azure VM using Microsoft Azure Automation
====================================================

            
Description

Demonstrates backing up an Azure VM to an Azure storage account by using Azure Automation.  The following script will connect to an Azure VM, stop the VM, back it up to a storage account, and start the VM.

Requirements

  *  You have an Azure subscription, if not sign up here:
[http://azure.microsoft.com/en-us/pricing/free-trial/](http://azure.microsoft.com/en-us/pricing/free-trial/) or use an MSDN subscription.

  *  An Azure Virtual Machine exists and is running.

  *  Familiar with PowerShell (no script writing required for this tutorial).

  *  You’ve read my previous post on Azure Automation and run through the tutorial (I use parameters and assets from that post in this one so it’s a very good idea to start there before going further):
[http://blogs.technet.com/b/cbernier/archive/2014/04/08/microsoft-azure-automation.aspx](http://blogs.technet.com/b/cbernier/archive/2014/04/08/microsoft-azure-automation.aspx)

Dependencies

  *  Connect-Azure runbook: [http://gallery.technet.microsoft.com/scriptcenter/Connect-to-an-Azure-f27a81bb](http://azure.microsoft.com/en-us/pricing/free-trial/) 

  *  Connect-AzureVM runbook: [http://gallery.technet.microsoft.com/scriptcenter/Connect-to-an-Azure-85f0782c ](http://gallery.technet.microsoft.com/scriptcenter/Connect-to-an-Azure-85f0782c )

  *  ** **Daniele’s Azure VM Backup module:
[http://gallery.technet.microsoft.com/Powershell-module-for-b46c9b62/file/110244/1/QNDAzureBackup.zip](http://gallery.technet.microsoft.com/Powershell-module-for-b46c9b62/file/110244/1/QNDAzureBackup.zip)

**Importing Modules**

To back up an Azure VM we need to use an outside module. I used
[Daniele Grandini’s PowerShell module](http://gallery.technet.microsoft.com/Powershell-module-for-b46c9b62#content), however I needed to modify a few lines of code within the module for it to work properly in Azure.



You'll need to download Daniele’s Azure VM Backup module from here:
[http://gallery.technet.microsoft.com/Powershell-module-for-b46c9b62/file/110244/1/QNDAzureBackup.zip](http://gallery.technet.microsoft.com/Powershell-module-for-b46c9b62/file/110244/1/QNDAzureBackup.zip)


Reference my following blog post to find where to make edits to the script:
[
http://blogs.technet.com/b/cbernier/archive/2014/04/28/back-up-an-azure-vm-using-microsoft-azure-automation.aspx ](http://blogs.technet.com/b/cbernier/archive/2014/04/28/back-up-an-azure-vm-using-microsoft-azure-automation.aspx  )


 

Script

Within Azure Automation, select the runbook and select
**AUTHOR** from the top of the page and paste the following code in the
**DRAFT** section.  Make sure you have the appropriate assets created and referenced properly or the runbook will not run completely.




PowerShell Workflow
Edit|Remove
powershellworkflow
<#
.SYNOPSIS 
    Demonstrates backing up an Azure VM to an Azure storage account by using Azure Automation.  

.DESCRIPTION
    The following script will connect to an Azure VM, stop the VM, back it up to a storage account, and start the VM.
    
    Dependencies
        Connect-Azure runbook:  http://gallery.technet.microsoft.com/scriptcenter/Connect-to-an-Azure-f27a81bb  
        Connect-AzureVM runbook:  http://gallery.technet.microsoft.com/scriptcenter/Connect-to-an-Azure-85f0782c  
        Daniele Grandini's PowerShell Module: http://gallery.technet.microsoft.com/Powershell-module-for-b46c9b62#content
        Automation Certificate Asset containing the management certificate loaded to Azure
        Automation Connection Asset containing the subscription id & name of the certificate asset


.PARAMETER AzureConnectionName
Name of the Azure Subscription defined in the Connect-Azure runbook.  Reference my post for more information: http://blogs.technet.com/b/cbernier/archive/2014/04/08/microsoft-azure-automation.aspx

.PARAMETER ServiceName
Cloud Service the Azure VM is running under.

.PARAMETER VMName
Name of the Virtual Machine in Azure.

.PARAMETER StorageAccountName
Name of the Azure Storage Account to back up the VM to.

.PARAMETER backupContainerName
Name of the Container withing the Storage Account the VM will be backed up to.
#>

workflow BackupAzureVM

{

Param

(

[parameter(Mandatory=$true)]

[String]

$AzureConnectionName,

[parameter(Mandatory=$true)]

[String]

$ServiceName,

[parameter(Mandatory=$true)]

[String]

$VMName,

[parameter(Mandatory=$true)]

[String]

$StorageAccountName,

[parameter(Mandatory=$true)]

[String]

$backupContainerName

)

# Set up Azure connection by calling the Connect-Azure runbook

$Uri = Connect-AzureVM -AzureConnectionName $AzureConnectionName -serviceName $ServiceName -VMName $VMName

# Stop Azure VM

Stop-AzureVM -ServiceName $ServiceName -Name $VMName –StayProvisioned

# Backup Azure VM

Backup-AzureVM -serviceName $ServiceName -VMName $VMName -backupContainerName $backupContainerName -backupStorageAccountName $StorageAccountName –includeDataDisks

# Start Azure VM

Start-AzureVM -ServiceName $ServiceName -Name $VMName

}

<#
.SYNOPSIS 
    Demonstrates backing up an Azure VM to an Azure storage account by using Azure Automation.  

.DESCRIPTION
    The following script will connect to an Azure VM, stop the VM, back it up to a storage account, and start the VM.
    
    Dependencies
        Connect-Azure runbook:  http://gallery.technet.microsoft.com/scriptcenter/Connect-to-an-Azure-f27a81bb  
        Connect-AzureVM runbook:  http://gallery.technet.microsoft.com/scriptcenter/Connect-to-an-Azure-85f0782c  
        Daniele Grandini's PowerShell Module: http://gallery.technet.microsoft.com/Powershell-module-for-b46c9b62#content
        Automation Certificate Asset containing the management certificate loaded to Azure
        Automation Connection Asset containing the subscription id & name of the certificate asset


.PARAMETER AzureConnectionName
Name of the Azure Subscription defined in the Connect-Azure runbook.  Reference my post for more information: http://blogs.technet.com/b/cbernier/archive/2014/04/08/microsoft-azure-automation.aspx

.PARAMETER ServiceName
Cloud Service the Azure VM is running under.

.PARAMETER VMName
Name of the Virtual Machine in Azure.

.PARAMETER StorageAccountName
Name of the Azure Storage Account to back up the VM to.

.PARAMETER backupContainerName
Name of the Container withing the Storage Account the VM will be backed up to.
#>

workflow BackupAzureVM

{

Param

(

[parameter(Mandatory=$true)]

[String]

$AzureConnectionName,

[parameter(Mandatory=$true)]

[String]

$ServiceName,

[parameter(Mandatory=$true)]

[String]

$VMName,

[parameter(Mandatory=$true)]

[String]

$StorageAccountName,

[parameter(Mandatory=$true)]

[String]

$backupContainerName

)

# Set up Azure connection by calling the Connect-Azure runbook

$Uri = Connect-AzureVM -AzureConnectionName $AzureConnectionName -serviceName $ServiceName -VMName $VMName

# Stop Azure VM

Stop-AzureVM -ServiceName $ServiceName -Name $VMName –StayProvisioned

# Backup Azure VM

Backup-AzureVM -serviceName $ServiceName -VMName $VMName -backupContainerName $backupContainerName -backupStorageAccountName $StorageAccountName –includeDataDisks

# Start Azure VM

Start-AzureVM -ServiceName $ServiceName -Name $VMName

}






        
    
TechNet gallery is retiring! This script was migrated from TechNet script center to GitHub by Microsoft Azure Automation product group. All the Script Center fields like Rating, RatingCount and DownloadCount have been carried over to Github as-is for the migrated scripts only. Note : The Script Center fields will not be applicable for the new repositories created in Github & hence those fields will not show up for new Github repositories.
