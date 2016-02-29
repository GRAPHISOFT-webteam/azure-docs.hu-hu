<properties
    pageTitle="使用 Azure CLI 搭配 Azure 儲存體 | Microsoft Azure"
    description="了解如何搭配 Azure 儲存體使用 Azure 命令列介面 (Azure CLI) 來建立和管理儲存體帳戶，以及使用 Azure Blob 和檔案。"
    services="storage"
    documentationCenter="na"
    authors="tamram"
    manager="jdial"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="09/28/2015"
    ms.author="chungli;jiyang;yaxia;tamram"/>

# 使用 Azure CLI 搭配 Azure 儲存體

## 概觀

Azure CLI 提供您一組開放原始碼的跨平台命令集合，供您運用在 Azure 平台上。 它提供許多相同的功能中找到的 [Azure 入口網站](portal.azure.com) 以及豐富的資料存取功能。

在本指南中，我們將探討如何使用 [Azure 命令列介面 (Azure CLI)](../xplat-cli-install.md) 來執行各種與 Azure 儲存體的開發和管理工作。 建議您在使用本指南之前，先下載並安裝或升級至最新的 Azure CLI。

本指南假設您已了解 Azure 儲存體的基本概念。 本指南提供許多指令碼示範如何使用 Azure CLI 搭配 Azure 儲存體。 在執行每個指令碼之前，請務必先根據您的組態更新指令碼變數。

> [AZURE.NOTE] 本指南提供在 Azure 服務管理模式 (ASM) 中執行的 Azure CLI 命令和指令碼範例。 請參閱 [適用於 Mac、 Linux 和 Windows 搭配 Azure 資源管理使用 Azure CLI](../azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects) 的 Azure CLI 命令，以儲存在 Azure 資源管理 (ARM) 模式。

## 在 5 分鐘內開始使用 Azure 儲存體和 Azure CLI

本指南使用 Ubuntu 作為範例，但其他作業系統平台也同樣能夠執行。

**Azure 新手:** 取得 Microsoft Azure 訂閱和與該訂用帳戶相關聯的 Microsoft 帳戶。 如需 Azure 購買選項的詳細資訊，請參閱 [免費試用版](http://azure.microsoft.com/pricing/free-trial/), ，[購買選項](http://azure.microsoft.com/pricing/purchase-options/), ，和 [會員優惠](http://azure.microsoft.com/pricing/member-offers/) (適用於 MSDN、 Microsoft Partner Network、 BizSpark 和其他 Microsoft 程式的成員)。

請參閱 [管理帳戶、 訂閱和系統管理角色](https://msdn.microsoft.com/library/azure/hh531793.aspx) 如需有關 Azure 訂用帳戶。

**建立 Microsoft Azure 訂用帳戶和帳戶之後：**

1. 下載並安裝 Azure CLI 中所述的指示 [安裝 Azure CLI](../xplat-cli-install.md)。
2. 安裝好 Azure CLI 之後，您就能從命令列介面 (Bash、終端機、命令提示字元) 中使用 azure 命令存取 Azure CLI 命令。 輸入 `azure` 命令，您應該會看見下列輸出。

    ![Azure 命令輸出][Image1]

3. 在命令列介面中，輸入 `azure storage` 可列出所有 azure 儲存體命令，對 Azure CLI 提供的功能有初步印象。 您可以輸入命令名稱搭配 **-h** 參數 (例如， `azure storage share create -h`) 以查看命令語法的詳細資料。
4. 現在，我們將提供簡單的指令碼，顯示用來存取 Azure 儲存體的基本 Azure CLI 命令。 指令碼會先要求您為您的儲存體帳戶和金鑰設定兩個變數。 然後，指令碼將在這個新的儲存體帳戶中建立新容器，並將現有的映像檔案 (Blob) 上傳至該容器。 指令碼列出該容器中的所有 Blob 之後，會將映像檔案下載至本機電腦上的目的地目錄。

        #!/bin/bash
        # A simple Azure storage example

        export AZURE_STORAGE_ACCOUNT=<storage_account_name>
        export AZURE_STORAGE_ACCESS_KEY=<storage_account_key>

        export container_name=<container_name>
        export blob_name=<blob_name>
        export image_to_upload=<image_to_upload>
        export destination_folder=<destination_folder>

        echo "Creating the container..."
        azure storage container create $container_name

        echo "Uploading the image..."
        azure storage blob upload $image_to_upload $container_name $blob_name

        echo "Listing the blobs..."
        azure storage blob list $container_name

        echo "Downloading the image..."
        azure storage blob download $container_name $blob_name $destination_folder

        echo "Done"

5. 在您的本機電腦上，開啟您慣用的文字編輯器 (例如 vim)。 在文字編輯器中輸入上述指令碼。

6. 現在，您需要根據您的組態設定更新指令碼變數。

    - **< Storage_account_name >** 指令碼中使用的指定名稱或輸入新名稱儲存體帳戶。 **重要事項:** 的儲存體帳戶名稱必須是唯一在 Azure 中。 而且必須是小寫字母！

    - **< Storage_account_key >** 儲存體帳戶的存取金鑰。

    - **< Container_name >** 指令碼中使用的指定名稱或輸入新名稱為您的容器。

    - **< Image_to_upload >** 輸入路徑的圖片在本機電腦，例如:"~ / /images/helloworld.png"。

    - **< Destination_folder >** 輸入可儲存下載從 Azure 儲存體，例如檔案的本機目錄的路徑:"~/downloadImages"。

7. 您已更新必要變數 vim 中的之後，按下按鍵組合"Esc，:，wq! 」 若要儲存指令碼。

8. 若要執行這個指令碼，只要在 bash 主控台中輸入指令碼檔案名稱即可。 在這個指令碼執行之後，您應該會有一個本機目的地資料夾，包含下載的映像檔案。 以下螢幕擷取畫面顯示範例輸出︰

在指令碼執行之後，您應該有包含下載的映像檔案的本機目的資料夾。

## 使用 Azure CLI 管理儲存體帳戶

### 連線到您的 Azure 訂用帳戶

雖然大部分的儲存體命令在沒有 Azure 訂用帳戶的狀況下仍然能夠運作，但是我們建議您從 Azure CLI 連線到您的訂用帳戶。 若要設定 Azure CLI 以搭配您的訂閱，請依照下列中的步驟 [如何連接到您的 Azure 訂閱](../xplat-cli-install.md#how-to-connect-to-your-azure-subscription)。

### 建立新的儲存體帳戶

若要使用 Azure 儲存體，您將需要儲存體帳戶。 設定電腦以連接至您的訂用帳戶之後，您可以建立新的 Azure 儲存體帳戶。

        azure storage account create <account_name>

儲存體帳戶的名稱必須介於 3 到 24 個字元的長度，而且只能使用數字和小寫字母。

### 在環境變數中設定預設 Azure 儲存體帳戶

您可以在訂用帳戶中有多個儲存體帳戶。 您可以選擇其中一個儲存體帳戶並且在環境變數中進行設定，讓同一個工作階段中的所有儲存體命令都能使用。 這可讓您執行 Azure CLI 儲存體命令，而不需明確指定儲存體帳戶和金鑰。

        export AZURE_STORAGE_ACCOUNT=<account_name>
        export AZURE_STORAGE_ACCESS_KEY=<key>

另一種設定預設儲存體帳戶的方式是使用連接字串。 首先透過下列命令取得連接字串：

        azure storage account connectionstring show <account_name>

然後複製輸出連接字串，並將它設定為環境變數：

        export AZURE_STORAGE_CONNECTION_STRING=<connection_string>

## 建立和管理 Blob

Azure Blob 儲存體是一項儲存大量非結構化資料的服務 (例如文字或二進位資料)，全球任何地方都可透過 HTTP 或 HTTPS 來存取這些資料。 本節假設您已熟悉 Azure Blob 儲存體的概念。 如需詳細資訊，請參閱 [如何使用 Blob 儲存體.NET](storage-dotnet-how-to-use-blobs.md) 和 [Blob 服務概念](http://msdn.microsoft.com/library/azure/dd179376.aspx)。

### 建立容器

Azure 儲存體中的每個 Blob 必須位於一個容器中。 您可以使用 `azure storage container create` 命令建立私用容器：

        azure storage container create mycontainer

> [AZURE.NOTE] 有三個層級的匿名讀取權限: **關閉**, ，**Blob**, ，和 **容器**。 若要防止匿名存取 blob，權限] 參數設定為 **關閉**。 新容器預設為私人，且只能由帳戶擁有者存取。 若要允許匿名公開讀取權限 blob 資源，但不是會對容器中繼資料或容器中 blob 清單的權限將參數設定 **Blob**。 若要允許 blob 資源、 容器中繼資料的容器中 blob 清單的完整公用讀取權限，權限] 參數設定為 **容器**。 如需詳細資訊，請參閱 [管理 Azure 儲存體資源的存取](storage-manage-access-to-resources.md)。

### 將 Blob 上傳至容器

Azure Blob 儲存體支援區塊 Blob 和頁面 Blob。 如需詳細資訊，請參閱 [了解區塊 Blob 和分頁 Blob](http://msdn.microsoft.com/library/azure/ee691964.aspx)。

若要將 Blob 上傳至容器，您可以使用 `azure storage blob upload`。 根據預設，此命令會將本機檔案上傳至區塊 Blob。 若要指定 Blob 的類型，您可以使用 `--blobtype` 參數。

        azure storage blob upload '~/images/HelloWorld.png' mycontainer myBlockBlob

### 從容器下載 Blob

下列範例示範如何從容器下載 Blob。

        azure storage blob download mycontainer myBlockBlob '~/downloadImages/downloaded.png'

### 複製 Blob

您可以在儲存體帳戶內或在不同儲存體帳戶和區域之間，以非同步方式複製 Blob。

下列範例示範如何從一個儲存體帳戶複製 Blob 到另一個儲存體帳戶。 在此範例中，我們建立 Blob 為公開，可匿名存取的容器。

    azure storage container create mycontainer2 -a <accountName2> -k <accountKey2> -p Blob

    azure storage blob upload '~/Images/HelloWorld.png' mycontainer2 myBlockBlob2 -a <accountName2> -k <accountKey2>

    azure storage blob copy start 'https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2' mycontainer

此範例會執行非同步複製。 您可以執行 `azure storage blob copy show` 作業監視每個複製作業的狀態。

請注意，針對複製作業提供的來源 URL 必須是可公開存取，或包含 SAS (共用存取簽章) 權杖。

### 刪除 Blob

若要刪除 Blob，請使用下列命令：

        azure storage blob delete mycontainer myBlockBlob2

## 建立和管理檔案共用

Azure 檔案儲存體為使用標準 SMB 通訊協定的應用程式提供共用儲存體。 Microsoft Azure 虛擬機器和雲端服務，以及內部部署應用程式，可以透過掛接共用，共用檔案資料。 您可以透過 Azure CLI 管理檔案共用和檔案資料。 如需 Azure 檔案儲存體的詳細資訊，請參閱 [如何使用 Windows Azure 檔案儲存體](storage-dotnet-how-to-use-files) 或 [如何使用 Linux 的 Azure 檔案儲存體](storage-how-to-use-files-linux.md)。

### 建立檔案共用

Azure 檔案共用是 Azure 中的 SMB 檔案共用。 所有目錄和檔案都必須在檔案共用中建立。 帳戶可包含無限制數目的共用，而共用可儲存無限制數目的檔案，最多可達儲存體帳戶的容量限制。 下列範例會建立名為的檔案共用 **myshare**。

        azure storage share create myshare

### 建立目錄

目錄會提供 Azure 檔案共用選擇性的階層式結構。 下列範例會建立名為 **myDir** 檔案共用中。

        azure storage directory create myshare myDir

請注意，目錄路徑可包含多個層級， *例如*, ，**/ b**。 不過，您必須確定所有父目錄都存在。 例如，針對路徑 **/ b**, ，您必須先建立目錄 **** 第一次，然後再建立目錄 **b**。

### 上傳本機檔案至目錄

下列範例會從檔案上傳 **~/temp/samplefile.txt** 至 **myDir** 目錄。 編輯檔案路徑，以指向本機機器上的有效檔案：

        azure storage file upload '~/temp/samplefile.txt' myshare myDir

請注意，共用中的檔案大小可能高達 1 TB。

### 列出共用根或目錄中的檔案

您可以使用下列命令，列出共用根或目錄中的檔案和子目錄：

        azure storage file list myshare myDir

請注意，列出作業不一定會顯示目錄名稱。 如果省略，則命令會列出共用根目錄的內容。

### 複製檔案

從 Azure CLI 0.9.8 版開始，您可以將檔案複製到另一個檔案、將檔案複製到 Blob 或將 Blob 複製到檔案。 下列示範如何使用 CLI 命令執行這些複製作業。 將檔案複製到新的目錄：

    azure storage file copy start --source-share srcshare --source-path srcdir/hello.txt --dest-share destshare --dest-path destdir/hellocopy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
    
將 Blob 複製到檔案目錄：

    azure storage file copy start --source-container srcctn --source-blob hello2.txt --dest-share hello --dest-path hellodir/hello2copy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString

## 後續步驟

以下是有助於您深入了解 Azure 儲存體的一些相關文章和資源。

- [Azure 儲存體文件](http://azure.microsoft.com/documentation/services/storage/)
- [Azure 儲存體 REST API 參考](https://msdn.microsoft.com/library/azure/dd179355.aspx)


[Image1]: ./media/storage-azure-cli/azure_command.png
 

