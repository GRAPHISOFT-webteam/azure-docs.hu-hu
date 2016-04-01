<properties
    pageTitle="如何使用拼音的 Blob 儲存體 | Microsoft Azure"
    description="了解如何使用 Azure Blob 服務來上傳、下載、列出及刪除 Blob 內容。 範例以 Ruby 撰寫。"
    services="storage"
    documentationCenter="ruby"
    authors="tfitzmac"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="09/01/2015"
    ms.author="tomfitz"/>


# 如何使用 Ruby 的 Blob 儲存體

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## 概觀

本指南將示範如何使用 Azure Blob 服務執行一般案例。 這些範例使用 Ruby API 撰寫。 涵蓋的案例包括 **上傳、 列出、 下載** 和 **刪除** blob。

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## 建立 Ruby 應用程式

建立 Ruby 應用程式。 如需指示，
請參閱 [Azure 上建立 Ruby 應用程式](/develop/ruby/tutorials/web-app-with-linux-vm/)。

## 設定您的應用程式以存取儲存體

若要使用 Azure 儲存體，您需要下載並使用 Ruby azure 封裝，這包含一組便利程式庫，能與儲存體 REST 服務通訊。

### 使用 RubyGems 來取得套件

1. 使用命令列介面，例如 **PowerShell** (Windows)、 **終端機** (Mac) 或 **Bash** (Unix)。

2. 在命令視窗中鍵入 "gem install azure" 以安裝 Gem 和相依性。

### 匯入封裝

使用您偏好的文字編輯器，將以下內容新增至您打算使用儲存體的 Ruby 檔案頂端：

    require "azure"

## 設定 Azure 儲存體連接

Azure 模組會讀取環境變數 **AZURE\_STORAGE\_ACCOUNT** 和 **AZURE\_STORAGE\_ACCESS_KEY**
以取得連接到 Azure 儲存體帳戶所需的資訊。 如果未設定這些環境變數，您必須指定帳戶資訊，才能使用 **Azure::Blob::BlobService** 為下列程式碼 ︰

    Azure.config.storage_account_name = "<your azure storage account>"
    Azure.config.storage_access_key = "<your azure storage access key>"


若要取得這些值，請執行下列動作：

1. 登入 [Azure 入口網站](portal.azure.com)。
2. 瀏覽到您要使用的儲存體帳戶。
3. 按一下 [ **管理金鑰** 底部的 [瀏覽] 窗格。
4. 在快顯對話方塊中，您將會看到儲存體帳戶名稱、主要存取金鑰和次要存取金鑰。 如需存取金鑰，您可以使用主要存取金鑰或次要存取金鑰。

## 建立容器

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

 **Azure::Blob::BlobService** 物件可讓您能使用容器及 blob。 若要建立容器，請使用 **create\_container （)** 方法。

下列程式碼範例會建立容器或列印錯誤訊息 (若有的話)。

    azure_blob_service = Azure::Blob::BlobService.new
    begin
      container = azure_blob_service.create_container("test-container")
    rescue
      puts $!
    end

如果您想讓容器中的檔案成為公用性質，可以設定容器的權限。

您可以直接修改 <strong>create\_container （)</strong> 呼叫以傳遞 **: public\_access\_level** 選項 ︰

    container = azure_blob_service.create_container("test-container",
      :public_access_level => "<public access level>")


有效值為 **: public\_access\_level** 選項 ︰

* **blob:** 指定容器和 blob 資料的完整公開讀取權限。 用戶端可以透過匿名要求列舉容器內的 Blob，但無法列舉儲存體帳戶內的容器。

* **容器 ︰** 指定 blob 的公用讀取權限。 您可以透過匿名要求讀取此容器內的 Blob 資料，但您無法使用容器資料。 用戶端無法透過匿名要求列舉容器內的 Blob。

或者，您可以修改容器的公用存取層級使用 **set\_container\_acl** 方法，以指定的公用存取等級。

下列程式碼範例將公用存取等級變更為 **容器**:

    azure_blob_service.set_container_acl('test-container', "container")

## 將 Blob 上傳至容器

若要將內容上傳至 blob，使用 **create\_block\_blob （)** 方法建立 blob，使用檔案或字串作為 blob 的內容。

下列程式碼上傳檔案 **test.png** 作為名為"image-blob"的容器中的新 blob。

    content = File.open("test.png", "rb") { |file| file.read }
    blob = azure_blob_service.create_block_blob(container.name,
      "image-blob", content)
    puts blob.name

## 列出容器中的 Blob

若要列出容器，請使用 **list_containers （)** 方法。
若要列出容器內的 blob，使用 **list\_blobs （)** 方法。

這會輸出該帳戶所有容器中所有 Blob 的 URL。

    containers = azure_blob_service.list_containers()
    containers.each do |container|
      blobs = azure_blob_service.list_blobs(container.name)
      blobs.each do |blob|
        puts blob.name
      end
    end

## 下載 Blob

若要下載 blob，請使用 **get\_blob （)** 方法來擷取內容。

下列程式碼範例示範如何使用 **get\_blob （)** 來下載"image-blob"的內容，並寫入本機檔案。

    blob, content = azure_blob_service.get_blob(container.name,"image-blob")
    File.open("download.png","wb") {|f| f.write(content)}

## 刪除 Blob
最後，若要刪除 blob，請使用 **delete\_blob （)** 方法。 下列程式碼範例示範如何刪除 Blob。

    azure_blob_service.delete_blob(container.name, "image-blob")

## 後續步驟

請遵循下列連結以深入了解更複雜的儲存體工作：

- [Azure 儲存體團隊部落格](http://blogs.msdn.com/b/windowsazurestorage/)
- [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) GitHub 上的儲存機制
- [使用 AzCopy 命令列公用程式傳輸資料](storage-use-azcopy)

