<properties 
   pageTitle="使用 Azure PowerShell 管理 Azure 資料湖分析 | Azure" 
   description="了解如何管理資料湖分析工作、資料來源、使用者。 " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="12/01/2015"
   ms.author="jgao"/>

# 使用 Azure PowerShell 管理 Azure 資料湖分析

[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

了解如何使用 Azure PowerShell 管理 Azure 資料湖分析帳戶、資料來源、使用者和工作。 若要使用其他工具查看管理主題，請按一下上方選取的索引標籤。

**必要條件**

開始進行本教學課程之前，您必須具備下列條件：

- **Azure 訂用帳戶**。 [取得 Azure 免費試用]，請參閱 https://azure.microsoft.com/en-us/pricing/free-trial/)。


<!-- ################################ -->
<!-- ################################ -->


##安裝 Azure PowerShell 1.0 或更新版本

首先，您必須先解除安裝 0.9x 版本。

檢查已安裝的 PowerShell 版本：

    Get-Module *azure*
    
若要解除安裝較舊的版本，請在控制台中執行 [程式和功能]。 

共有兩個安裝 Azure PowerShell 的主要選項。 

- [PowerShell 組件庫](https://www.powershellgallery.com/)。 從提高權限的 PowerShell ISE 或提高權限的 Windows PowerShell 主控台執行下列命令：

        # Install the Azure Resource Manager modules from PowerShell Gallery
        Install-Module AzureRM
        Install-AzureRM
        
        # Install the Azure Service Management module from PowerShell Gallery
        Install-Module Azure
        
        # Import AzureRM modules for the given version manifest in the AzureRM module
        Import-AzureRM
        
        # Import Azure Service Management module
        Import-Module Azure

    如需詳細資訊，請參閱 [PowerShell 組件庫](https://www.powershellgallery.com/)。

- [Microsoft Web Platform Installer (WebPI)](http://aka.ms/webpi-azps)。 如果您已安裝 Azure PowerShell 0.9.x，系統將提示您解除安裝 0.9.x。 如果您是從 PowerShell 資源庫安裝 Azure PowerShell 模組，必須在安裝安裝程式之前先移除模組，以確保 Azure PowerShell 環境保持一致。 如需指示，請參閱 [透過 WebPI 安裝 Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/)。

WebPI 每個月都會更新。 PowerShell 資源庫將持續更新。 若您想要透過 PowerShell 資源庫安裝，則此為取得最新最優異 Azure PowerShell 的首要管道。

**若要列出 cmdlet**:

    Get-Command *Azure*DataLakeAnalytics*

**若要連接至 Azure，請使用下列 cmdlet**:

    Login-AzureRmAccount
    Get-AzureRmSubscription  # for finding the Azure Subscription ID
    Set-AzureRmContext -SubscriptionID <Azure Subscription ID>
    
## 管理帳戶

您必須擁有資料湖分析帳戶，才能執行任何資料湖分析工作。 不同於 Azure HDInsight 中，不必付費分析帳戶時 
執行工作。  您只需支付執行工作時的費用。  如需詳細資訊，請參閱 
[Azure 資料湖分析概觀](data-lake-analytics-overview.md)。  

###建立帳戶

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeStoreName = "<DataLakeAccountName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $location = "<Microsoft Data Center>"
    
    Write-Host "Create a resource group ..." -ForegroundColor Green
    New-AzureRmResourceGroup `
        -Name  $resourceGroupName `
        -Location $location
    
    Write-Host "Create a Data Lake account ..."  -ForegroundColor Green
    New-AzureRmDataLakeStoreAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $dataLakeStoreName `
        -Location $location 
    
    Write-Host "Create a Data Lake Analytics account ..."  -ForegroundColor Green
    New-AzureRmDataLakeAnalyticsAccount `
        -Name $dataLakeAnalyticsAccountName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -DefaultDataLake $dataLakeStoreName
    
    Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
    Get-AzureRmDataLakeAnalyticsAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $dataLakeAnalyticsAccountName  

您也可以使用 Azure 資源群組範本。 用來建立資料湖分析帳戶和相依的資料湖存放區帳戶的範本位於 [附錄 A](#appendix-a)。 將範本儲存成 .json 範本的檔案，然後使用下列 PowerShell 指令碼呼叫該範本：


    $AzureSubscriptionID = "<Your Azure Subscription ID>"
    
    $ResourceGroupName = "<New Azure Resource Group Name>"
    $Location = "EAST US 2"
    $DefaultDataLakeStoreAccountName = "<New Data Lake Store Account Name>"
    $DataLakeAnalyticsAccountName = "<New Data Lake Analytics Account Name>"
    
    $DeploymentName = "MyDataLakeAnalyticsDeployment"
    $ARMTemplateFile = "E:\Tutorials\ADL\ARMTemplate\azuredeploy.json"  # update the Json template path 
    
    Login-AzureRmAccount
    
    Select-AzureRmSubscription -SubscriptionId $AzureSubscriptionID
    
    # Create the resource group
    New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
    
    # Create the Data Lake Analytics account with the default Data Lake Store account.
    $parameters = @{"adlAnalyticsName"=$DataLakeAnalyticsAccountName; "adlStoreName"=$DefaultDataLakeStoreAccountName}
    New-AzureRmResourceGroupDeployment -Name $DeploymentName -ResourceGroupName $ResourceGroupName -TemplateFile $ARMTemplateFile -TemplateParameterObject $parameters 

 
###清單帳戶

列出目前訂用帳戶內的資料湖分析帳戶

    Get-AzureRmDataLakeAnalyticsAccount
    
輸出如下：

    Id         : /subscriptions/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/resourceGroups/learn1021rg/providers/Microsoft.DataLakeAnalytics/accounts/learn1021adla
    Location   : eastus2
    Name       : learn1021adla
    Properties : Microsoft.Azure.Management.DataLake.Analytics.Models.DataLakeAnalyticsAccountProperties
    Tags       : {}
    Type       : Microsoft.DataLakeAnalytics/accounts

列出特定資源群組內的資料湖分析帳戶

    Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName

取得特定資料湖分析帳戶的詳細資料

    Get-AzureRmDataLakeAnalyticsAccount -Name $adlAnalyticsAccountName

測試特定資料湖分析帳戶的存在

    Test-AzureRmDataLakeAnalyticsAccount -Name $adlAnalyticsAccountName

此指令程式會傳回 **True** 或 **False**。

###刪除資料湖分析帳戶

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    
    Remove-AzureRmDataLakeAnalyticsAccount -Name $dataLakeAnalyticsAccountName 

刪除分析帳戶不會刪除相依的資料湖儲存體帳戶。 下列範例會刪除資料湖分析帳戶和預設的資料湖存放區帳戶

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DefaultDataLakeAccount

    Remove-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName 
    Remove-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName

<!-- ################################ -->
<!-- ################################ -->
## 管理帳戶資料來源

資料湖分析目前支援下列資料來源：

- [Azure 資料湖儲存體](data-lake-storage-overview.md)
- [Azure 儲存體](storage-introduction.md)

當您建立分析帳戶時，您必須指定要設為預設的 Azure 資料湖儲存體帳戶 
子網域名稱。 預設的資料湖存放區帳戶是用來儲存工作中繼資料與工作稽核記錄。 當您有了 
建立分析帳戶，您可以加入其他資料湖儲存體帳戶及/或 Azure 儲存體帳戶。 

### 尋找預設的資料湖存放區帳戶

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DefaultDataLakeAccount


### 新增其他的 Azure Blob 儲存體帳戶

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $AzureStorageAccountName = "<AzureStorageAccountName>"
    $AzureStorageAccountKey = "<AzureStorageAccountKey>"
    
    Add-AzureRmDataLakeAnalyticsDataSource -ResourceGroupName $resourceGroupName -AccountName $dataLakeAnalyticName -AzureBlob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey

### 新增其他的資料湖存放區帳戶

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $AzureDataLakeName = "<DataLakeStoreName>"
    
    Add-AzureRmDataLakeAnalyticsDataSource -ResourceGroupName $resourceGroupName -AccountName $dataLakeAnalyticName -DataLake $AzureDataLakeName 

### 列出資料來源：

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"

    (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DataLakeStoreAccounts
    (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.StorageAccounts
    


<!-- ################################ -->
<!-- ################################ -->
## 管理工作

您必須擁有資料湖分析帳戶，才能建立工作。  如需詳細資訊，請參閱 [管理資料湖分析帳戶](#manage-data-lake-analytics-accounts)。

### 列出工作

    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    
    Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName
    
    Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName -State Running, Queued
    #States: Accepted, Compiling, Ended, New, Paused, Queued, Running, Scheduling, Starting
    
    Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName -Result Cancelled
    #Results: Cancelled, Failed, None, Successed 
    
    Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName -Name <Job Name>
    Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName -Submitter <Job submitter>

    # List all jobs submitted on January 1 (local time)
    Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName `
        -SubmittedAfter "2015/01/01"
        -SubmittedBefore "2015/01/02"   

    # List all jobs that succeeded on January 1 after 2 pm (UTC time)
    Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName `
        -State Ended
        -Result Succeeded
        -SubmittedAfter "2015/01/01 2:00 PM -0"
        -SubmittedBefore "2015/01/02 -0"

    # List all jobs submitted in the past hour
    Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName `
        -SubmittedAfter (Get-Date).AddHours(-1)

### 取得工作詳細資料

    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName -JobID <Job ID>
    
### 提交工作

    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"

    #Pass script via path
    Submit-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName `
        -Name $jobName `
        -ScriptPath $scriptPath

    #Pass script contents
    Submit-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName `
        -Name $jobName `
        -Script $scriptContents

> [AZURE.NOTE] 工作的預設優先順序為 1000，和工作平行處理原則的預設程度為 1。


### 取消工作

    Stop-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName `
        -JobID $jobID


## 管理目錄項目

U-SQL 目錄是用來建構資料和程式碼，讓 U-SQL 指令碼可以共用它們。 目錄可以讓 Azure 資料湖中的資料具有可能的最高效能。 如需詳細資訊，請參閱 [使用 U SQL 目錄](data-lake-analytics-use-u-sql-catalog.md)。

###列出目錄項目

    #List databases
    Get-AzureRmDataLakeAnalyticsCatalogItem `
        -AccountName $adlAnalyticsAccountName `
        -ItemType Database
    
    
    
    #List tables
    Get-AzureRmDataLakeAnalyticsCatalogItem `
        -AccountName $adlAnalyticsAccountName `
        -ItemType Table `
        -Path "master.dbo"

###取得目錄項目詳細資料 

    #Get a database
    Get-AzureRmDataLakeAnalyticsCatalogItem `
        -AccountName $adlAnalyticsAccountName `
        -ItemType Database `
        -Path "master"
    
    #Get a table
    Get-AzureRmDataLakeAnalyticsCatalogItem `
        -AccountName $adlAnalyticsAccountName `
        -ItemType Table `
        -Path "master.dbo.mytable"

###測試類別目錄項目的存在

    Test-AzureRmDataLakeAnalyticsCatalogItem  `
        -AccountName $adlAnalyticsAccountName `
        -ItemType Database `
        -Path "master"

###建立目錄密碼
    新 AzureRmDataLakeAnalyticsCatalogSecret  `
            -AccountName $adlAnalyticsAccountName `
            -DatabaseName 「 主要 」 '
            -密碼 (Get-credential-UserName"username"-"輸入的密碼 」 的訊息)

### 修改目錄密碼
    Set-AzureRmDataLakeAnalyticsCatalogSecret  `
            -AccountName $adlAnalyticsAccountName `
            -DatabaseName "master" `
            -Secret (Get-Credential -UserName "username" -Message "Enter the password")



###刪除目錄密碼
    移除 AzureRmDataLakeAnalyticsCatalogSecret  `
            -AccountName $adlAnalyticsAccountName `
            -DatabaseName 「 主要 」


## 使用 Azure 資源管理員群組

應用程式通常由許多元件，例如 web 應用程式、 資料庫、 資料庫伺服器、 存放裝置組成
和第 3 個合作對象的服務。 Azure 資源管理員 (ARM) 可讓您能夠使用您的應用程式中的資源 
為群組，稱為 「 Azure 資源群組。 您可以部署、 更新、 監視或刪除所有的 
您的應用程式在單一、 協調的作業中的資源。 您的部署，且可使用範本 
範本可以用於不同的環境，例如測試、 預備和生產環境。 您可以釐清計費 
為您的組織檢視整個群組的彙總成本。 如需詳細資訊，請參閱 [Azure
資源管理員概觀](resource-group-overview.md)。 

資料湖分析服務可包含下列元件：

- Azure 資料湖分析帳戶
- 必要的預設 Azure 資料湖儲存體帳戶
- 其他 Azure 資料湖儲存體帳戶
- 其他 Azure 儲存體帳戶

您可以在某個 ARM 群組下建立上述所有元件，這樣更容易管理。

![Azure 資料湖分析帳戶與儲存體](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

資料湖分析帳戶和相依儲存體帳戶必須位於同一個 Azure 資料中心。
但 ARM 群組可位在不同的資料中心內。  

##另請參閱 

- [Microsoft Azure 資料湖分析概觀](data-lake-analytics-overview.md)
- [使用 Azure 入口網站開始使用資料湖分析](data-lake-analytics-get-started-portal.md)
- [使用 Azure 入口網站管理 Azure 資料湖分析](data-lake-analytics-use-portal.md)
- [使用 Azure 入口網站監視和疑難排解 Azure 資料湖分析作業](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

##附錄 A - 資料湖分析 ARM 範本

下列 ARM 範本可用於部署資料湖分析帳戶及其相依資料湖存放區帳戶。  另存成 json 檔案，然後使用 PowerShell 指令碼呼叫該範本。 如需詳細資訊，請參閱
[將應用程式使用 Azure 資源管理員範本部署](resource-group-template-deploy.md#deploy-with-powershell) 和 [撰寫 Azure 資源管理員範本](resource-group-authoring-templates.md)。

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "adlAnalyticsName": {
                "type": "string",
                "metadata": {
                    "description": "The name of the Data Lake Analytics account to create."
                }
            },
            "adlStoreName": {
                "type": "string",
                "metadata": {
                    "description": "The name of the Data Lake Store account to create."
                }
            }
        },
        "resources": [{
            "name": "[parameters('adlAnalyticsName')]",
            "type": "Microsoft.DataLakeAnalytics/accounts",
            "location": "East US 2",
            "apiVersion": "2015-10-01-preview",
            "dependsOn": ["[concat('Microsoft.DataLakeStore/accounts/',parameters('adlStoreName'))]"],
            "tags": {
                
            },
            "properties": {
                "defaultDataLakeAccount": "[parameters('adlStoreName')]"
                }
            }
        },
        {
            "name": "[parameters('adlName')]",
            "type": "Microsoft.DataLakeStore/accounts",
            "location": "East US 2",
            "apiVersion": "2015-10-01-preview",
            "dependsOn": [],
            "tags": {
                
            }
        }],
        "outputs": {
            "adlAnalyticsAccount": {
                "type": "object",
                "value": "[reference(resourceId('Microsoft.DataLakeAnalytics/accounts',parameters('adlAnalyticsName')))]"
            },
            "adlStoreAccount": {
                "type": "object",
                "value": "[reference(resourceId('Microsoft.DataLakeStore/accounts',parameters('adlStoreName')))]"
            }
        }
    }
