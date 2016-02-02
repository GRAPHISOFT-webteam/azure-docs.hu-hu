<properties
    pageTitle="如何使用 .NET 的 Blob 儲存體 | Microsoft Azure"
    description="了解如 Azure Blob 儲存體以及如何建立容器與上傳、下載、列出及刪除 Blob 內容。"
    services="storage"
    documentationCenter=".net"
    authors="tamram"
    manager="carmonm"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article" 
    ms.date="12/01/2015"
    ms.author="tamram"/>



# 如何使用 .NET 的 Blob 儲存體

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## 概觀

本指南將示範如何使用
執行一般案例。 這些範例均以 C# 撰寫，並
使用 Azure Storage Client Library for .NET。 儲存體用戶端程式庫是一個 SDK，可簡化與 Blob 儲存體 REST API 的互動。 本指南所涵蓋的案例包括**上傳**、**列出**、**下載**及**刪除** Blob，完成所需時間約一小時。 如果您想要觀看入門影片，請參閱 [介紹到 Azure 儲存體在五分鐘內](https://azure.microsoft.com/documentation/videos/azure-storage-5-minute-overview/) 或者，您可以讀取 [在五分鐘內開始使用 Azure 儲存體](storage-getting-started-guide.md)。

[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-configure-connection-string-include](../../includes/storage-configure-connection-string-include.md)]

## 以程式設計方式存取 Blob 儲存體

[AZURE.INCLUDE [storage-dotnet-obtain-assembly](../../includes/storage-dotnet-obtain-assembly.md)]

### 命名空間宣告

將下列命名空間宣告加入至任何 C\# 檔案的頂端
(您想要在該檔案中以程式設計方式存取 Azure 儲存體)：

    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Blob;

確定已參照 `Microsoft.WindowsAzure.Storage.dll` 組件。

[AZURE.INCLUDE [storage-dotnet-retrieve-conn-string](../../includes/storage-dotnet-retrieve-conn-string.md)]

A **CloudBlobClient** 類型可讓您擷取物件代表
Blob 儲存體服務內所儲存之容器和 Blob 的物件。 Auch die Eigenschaften
下列程式碼會建立 **CloudBlobClient** 物件使用的儲存體
以上擷取的儲存體帳戶物件：

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

## 建立容器

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

此範例說明如何建立尚不存在的容器：

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Retrieve a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    
    // Create the container if it doesn't already exist.
    container.CreateIfNotExists();

根據預設，新容器屬私人性質，您必須指定
儲存體存取金鑰才能從此
。 若要讓所有人都能使用容器中的檔案，
您可以使用下列程式碼將容器設定為公用
容器：

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess =
        BlobContainerPublicAccessType.Blob });

網際網路上的任何人都可以看到公用容器中的 Blob，但
要有適當的存取金鑰，才能修改或刪除這些 Blob。

## 將 Blob 上傳至容器

Azure Blob 儲存體支援區塊 Blob 和頁面 Blob。 在大多數情況下，建議使用區塊 Blob 的類型。

若要將檔案上傳至區塊 Blob，請取得容器參照，並使用
該參照來取得區塊 Blob 參照。 擁有 Blob 參照後，即可
藉由呼叫它的資料流 **UploadFromStream** 方法。 此操作會建立 Blob (如果其並不存在)
或覆寫 Blob (如果其已存在)。

下列範例顯示如何將 Blob 上傳到容器，並假設已建立該容器。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    
    // Retrieve reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");
    
    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## 列出容器中的 Blob

若要列出容器中的 Blob，請先取得容器參照。 您
然後可以使用容器的 **ListBlobs** 方法來擷取 blob 和/或目錄
目錄。 若要針對
傳回 **IListBlobItem**, ，您必須進行轉換， **CloudBlockBlob**,，
**CloudPageBlob**, ，或 **CloudBlobDirectory** 物件。 如果不清楚類型，可使用
類型檢查來決定要將其轉換至何種類型。 下列程式碼會
示範如何擷取和輸出
`相片` 容器:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("photos");
    
    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, false))
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;
    
            Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);
    
        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob pageBlob = (CloudPageBlob)item;
    
            Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);
    
        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory directory = (CloudBlobDirectory)item;
    
            Console.WriteLine("Directory: {0}", directory.Uri);
        }
    }

如上所述，為 blob 命名時，您可以在其名稱中包含路徑資訊。 這會建立虛擬目錄結構，讓您能夠組織及周遊，就像使用傳統檔案系統一樣。 請注意，目錄結構僅限虛擬目錄結構 - Blog 儲存體中唯一可用的資源為容器和 blob。 但是，儲存體用戶端程式庫提供 **CloudBlobDirectory** 物件來參照虛擬目錄，以及簡化透過此方式組織之 blob 的使用程序。

例如，假設名為 `photos` 的容器中有下列一組區塊 Blob：

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

當您在 'photos' 容器上呼叫 **ListBlobs** (如上面範例所示) 時，會傳回階層式清單。 它包含 **CloudBlobDirectory** 和 **CloudBlockBlob** 物件，分別代表容器中的目錄與 blob。 輸出結果看起來像這樣：

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

您可以選擇性地設定 **UseFlatBlobListing** 參數 **ListBlobs** 方法
**true**. 在此案例中，容器中的每個 blob 都是以 **CloudBlockBlob** 物件的方式傳回： 呼叫 **ListBlobs** 會傳回簡單列表，看起來像這樣：

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

而且結果看起來像這樣：

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

## 下載 Blob

若要下載 Blob，請先擷取 Blob 參照，然後呼叫 **DownloadToStream** 方法。 下列
範例會使用 **DownloadToStream** 方法將 blob
內容移轉到您可以永久儲存到本機檔案的串流物件。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    
    // Retrieve reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");
    
    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

您也可以使用 **DownloadToStream** 方法，將 Blob 的內容當成文字字串下載。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    
    // Retrieve reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");
    
    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## 刪除 Blob

若要刪除 Blob，請先取得 Blob 參考，然後在該參數上呼叫
**刪除** 方法。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    
    // Retrieve reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");
    
    // Delete the blob.
    blockBlob.Delete();

## 以非同步方式分頁列出 Blob

如果您要列出大量的 Blob，或是要控制在單一列出作業中傳回的結果數，您可以在結果頁面中列出 Blob。 此範例說明如何以非同步方式分頁傳回結果，使執行不會因為等待大型結果集傳回而中斷。

此範例說明一般 Blob 列出方式，但您也可以執行階層式清單，方法是將 **ListBlobsSegmentedAsync** 方法的 `useFlatBlobListing` 參數設為 `false`。

因為範例方法會呼叫非同步方法，所以前面必須加上 `async` 關鍵字，而且其必須傳回 **Task** 物件。 為 **ListBlobsSegmentedAsync** 方法指定的 await 關鍵字會擱置範例方法的執行，直到列出工作完成為止。

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        //List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");
    
        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;
    
        //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        //When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
            //or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();
    
            //Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
        while (continuationToken != null);
    }

## 寫入附加 Blob

附加 Blob 是一種全新的 Blob 類型，並在 Azure Storage Client Library for .NET 5.x 版中推出。 附加 Blob 已針對附加作業 (例如紀錄) 最佳化。 如同區塊 Blob，附加 Blob 亦由區塊組成，但當您將新區塊加入附加 Blob 時，它一律會附加到 Blob 結尾。 您無法更新或刪除附加 Blob 中的現有區塊。 附加 Blob 的區塊識別碼不會公開顯示，因為該識別碼適用於區塊 Blob。

附加 Blob 中的每個區塊大小都不同，最大為 4 MB，而附加 Blob 可包含高達 50,000 個區塊。 因此，附加 Blob 的大小上限稍高於 195 GB (4 MB X 50,000 個區塊)。

下列範例中建立了新的附加 Blob，並附加一些資料，以模擬簡單的記錄作業。

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    //Create service client for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    //Get a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("my-append-blobs");
    
    //Create the container if it does not already exist. 
    container.CreateIfNotExists();
    
    //Get a reference to an append blob.
    CloudAppendBlob appendBlob = container.GetAppendBlobReference("append-blob.log");
    
    //Create the append blob. Note that if the blob already exists, the CreateOrReplace() method will overwrite it.
    //You can check whether the blob exists to avoid overwriting it by using CloudAppendBlob.Exists().
    appendBlob.CreateOrReplace();
    
    int numBlocks = 10;
    
    //Generate an array of random bytes.
    Random rnd = new Random();
    byte[] bytes = new byte[numBlocks];
    rnd.NextBytes(bytes);
    
    //Simulate a logging operation by writing text data and byte data to the end of the append blob.
    for (int i = 0; i < numBlocks; i++)
    {
        appendBlob.AppendText(String.Format("Timestamp: {0:u} \tLog Entry: {1}{2}",
            DateTime.UtcNow, bytes[i], Environment.NewLine));
    }
    
    //Read the append blob to the console window.
    Console.WriteLine(appendBlob.DownloadText());

請參閱 [了解區塊 Blob、 分頁 Blob 及附加 Blob](https://msdn.microsoft.com/library/azure/ee691964.aspx) 如需有關 blob 的三種類型之間的差異。

## 後續步驟

既然您已了解 Blob 儲存體的基礎概念之後，請參考下列連結
若要深入了解。

### Blob 儲存體參考文件

- [For.NET 參考資料的儲存體用戶端程式庫](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
- [REST API 參考](http://msdn.microsoft.com/library/azure/dd179355)

### 其他功能指南

- [快速入門適用於.NET 的資料表儲存體](storage-dotnet-how-to-use-tables.md)
- [快速入門適用於.NET 的佇列儲存體](storage-dotnet-how-to-use-queues.md)
- [快速入門適用於.NET 的檔案儲存體](storage-dotnet-how-to-use-files.md)
- [使用 AzCopy 命令列公用程式傳輸資料](storage-use-azcopy)
- [使用 SQL 資料庫來儲存關聯式資料](../sql-database/articles/sql-database-dotnet-how-to-use.md)
- [如何使用 WebJobs SDK 使用 Azure blob 儲存體](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)


[blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png 
[blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png 
[blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png 
[blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png 
[blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png 
[azure storage team blog]: http://blogs.msdn.com/b/windowsazurestorage/ 
[configuring connection strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx 
[.net client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409 
[rest api reference]: http://msdn.microsoft.com/library/azure/dd179355 

