<properties 
   pageTitle="開始使用資料湖存放區 | Azure" 
   description="使用 Azure PowerShell 建立資料湖存放區帳戶，並執行基本作業" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="12/04/2015"
   ms.author="nitinme"/>

# 使用 Azure PowerShell 開始使用 Azure 資料湖分析存放區

> [AZURE.SELECTOR]
- [使用入口網站](data-lake-store-get-started-portal.md)
- [使用 PowerShell](data-lake-store-get-started-powershell.md)
- [使用 .NET SDK](data-lake-store-get-started-net-sdk.md)
- [使用 Azure CLI](data-lake-store-get-started-cli.md)
- [使用 Node.js](data-lake-store-manage-use-nodejs.md)

了解如何使用 Azure PowerShell 建立 Azure 資料湖存放區帳戶並執行基本作業，例如建立資料夾、上傳和下載資料檔案、刪除您的帳戶等等。如需詳細資料湖存放區的詳細資訊，請參閱 [資料湖市集概觀](data-lake-store-overview.md)。

## 必要條件

開始進行本教學課程之前，您必須具備下列條件：

- **Azure 訂用帳戶**。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
- **啟用您的 Azure 訂閱** 資料湖存放區公開預覽。 請參閱 [指示](data-lake-store-get-started-portal.md#signup)。


##安裝 Azure PowerShell 1.0 或更新版本

一開始，您必須解除安裝 Azure PowerShell 的 0.9x 版本。 若要檢查已安裝 PowerShell 的版本，請從 PowerShell 視窗執行下列命令：

    Get-Module *azure*
    
若要解除安裝較舊的版本，請執行 **程式和功能** 在控制台和移除早於 PowerShell 1.0 是否已安裝的版本。 

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

WebPI 每個月都會更新。 PowerShell 資源庫將持續更新。 如果您想要從 PowerShell 資源庫進行安裝，這會是取得最新和最優異的 Azure PowerShell 功能之首要管道。

## 建立 Azure 資料湖存放區帳戶

1. 從您的桌面上開啟新的 Azure PowerShell 視窗，輸入下列程式碼片段登入 Azure 帳戶、設定訂用帳戶，然後註冊資料湖存放區提供者。 系統提示您登入時，請使用其中一個訂用帳戶管理員/擁有者身分登入：

        # Log in to your Azure account
        Login-AzureRmAccount
        
        # List all the subscriptions associated to your account
        Get-AzureRmSubscription
        
        # Select a subscription 
        Set-AzureRmContext -SubscriptionId <subscription ID>
        
        # Register for Azure Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore" 


2. Azure 資料湖存放區帳戶與 Azure 資源群組相關聯。 從建立 Azure 資源群組開始。

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![建立 Azure 資源群組](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Create an Azure Resource Group")

2. 建立 Azure 資料湖存放區帳戶。 您指定的名稱必須只包含小寫字母和數字。

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![建立 Azure 資料湖存放區帳戶](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Create an Azure Data Lake Store account")

3. 確認已成功建立帳戶。

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    這個輸出應該 **True**。

## 在您的 Azure 資料湖存放區中建立目錄結構

您可以在您的 Azure 資料湖存放區帳戶下建立用於管理與儲存資料的目錄。 

1. 指定根目錄。

        $myrootdir = "/"

2. 建立新的目錄稱為 **mynewdirectory** 指定根目錄下。

        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory

3. 確認已成功建立新目錄。

        Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir

    應該會顯示類似下面的輸出畫面：

    ![確認目錄](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Verify Directory")


## 將資料上傳至 Azure 資料湖存放區

您可以在根層級直接將資料上傳至資料湖存放區，或上傳至您在帳戶內建立的目錄。 下列程式碼片段示範如何將某些範例資料上傳至目錄 (**mynewdirectory**) 您在上一節中建立。

如果您要尋找要上傳的一些範例資料，您可以取得 **政策救護車資料** 資料夾從 [Azure 資料湖 Git 儲存機制](https://github.com/MicrosoftBigData/AzureDataLake/tree/master/SQLIPSamples/SampleData/AmbulanceData)。 下載檔案，並將它儲存在您的電腦，例如 C:\sampledata\ 上的本機目錄。

    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## 重新命名、下載與刪除資料湖存放區中的資料

若要重新命名檔案，請使用下列命令：

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

若要下載檔案，請使用下列命令：

    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

若要刪除檔案，請使用下列命令：

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv 
    
出現提示時，輸入 **Y** 刪除的項目。 如果您要刪除多個檔案，可以提供所有的路徑並以逗號分隔。

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## 刪除 Azure 資料湖存放區帳戶

使用下列命令刪除資料湖存放區帳戶。

    Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

出現提示時，輸入 **Y** 刪除帳戶。


## 其他建立資料湖存放區帳戶的方法

- [使用入口網站開始使用資料湖存放區](data-lake-store-get-started-portal.md)
- [使用 .NET SDK 開始使用資料湖存放區](data-lake-store-get-started-net-sdk.md)
- [使用 Azure CLI 開始使用資料湖存放區](data-lake-store-get-started-cli.md)


## 後續步驟

- [保護資料湖存放區中的資料](data-lake-store-secure-data.md)
- [搭配 Data Lake 存放區使用 Azure Data Lake 分析](data-lake-analytics-get-started-portal.md)
- [搭配資料湖存放區使用 Azure HDInsight](data-lake-store-hdinsight-hadoop-use-portal.md)



