<properties 
    pageTitle="從通用 Windows 應用程式將影像上傳至 Azure Blob 儲存體 | Microsoft Azure" 
    description="了解如何使用 JavaScript 後端行動服務將影像上傳至 Azure Blob 儲存體，並從通用 Windows 應用程式存取影像。" 
    services="mobile-services,storage" 
    documentationCenter="windows" 
    authors="ggailey777" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="mobile-services" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-store" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="11/16/2015" 
    ms.author="glenga"/>

# 使用行動服務將影像上傳到 Azure Blob 儲存體

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../../includes/mobile-services-selector-upload-data-blob-storage.md)]

##概觀 

本主題說明如何使用 Azure 行動服務，讓您的應用程式能在 Azure 儲存體中上傳和存放使用者產生的影像。 行動服務會使用 SQL Database 儲存資料。 不過在 Azure Blob 儲存體服務中，二進位大型物件 (BLOB) 資料的儲存比較有效。 

您無法將資料安全上傳至 Blob 儲存體服務所需的認證，安全地散佈給用戶端應用程式。 您必須在您的行動服務中儲存這些認證，並將其用於產生共用存取簽章 (SAS)，以便上傳新的影像。 行動服務會安全地將 SAS (到期時間很短的認證，在此例中為 5 分鐘) 傳回給用戶端應用程式。 應用程式會使用此暫存認證來上傳影像。 在此範例中，來自 Blob 服務的下載是公開的。

在此教學課程中，您會在行動服務快速入門應用程式中新增功能，以便使用行動服務所產生的 SAS 來拍照並將影像上傳到 Azure。 

##必要條件

本教學課程需要下列各項：

+ Microsoft Visual Studio 2013 Update 3 或更新版本
+ [Azure 儲存體帳戶](../storage-create-storage-account.md)
+ 連接到電腦的相機或其他影像擷取裝置。

本教學課程會以行動服務快速入門為基礎。 在開始本教學課程之前，您必須先完成 [Get started with Mobile Services]。 

##在 Azure 傳統入口網站中更新已註冊的插入指令碼

[AZURE.INCLUDE [mobile-services-configure-blob-storage](../../includes/mobile-services-configure-blob-storage.md)]

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-upload-to-blob-storage](../../includes/mobile-services-windows-universal-dotnet-upload-to-blob-storage.md)]

##後續步驟

您現在已藉由整合行動服務與 Blob 服務，而能夠安全地上傳影像，請參閱其他後端服務和整合主題：

+ [在行動服務中排程後端工作]

    了解如何使用行動服務工作排程器功能，定義可依您定義的排程執行的伺服器指令碼。

+ [行動服務伺服器指令碼參考]

    參考使用伺服器指令碼來執行伺服器端工作以及與其他 Azure 元件和外部資源整合的主題。
 
+ [行動服務 .NET 作法概念性參考]

    深入了解如何搭配使用行動服務與 .NET
  
 
<!-- Anchors. -->
[Install the Storage Client library]: #install-storage-client
[Update the client app to capture images]: #add-select-images
[Update the insert script to generate an SAS]: #update-scripts
[Upload images to test the app]: #test
[Next Steps]:#next-steps

<!-- Images. -->

[2]: ./media/mobile-services-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png


<!-- URLs. -->
[Send email from Mobile Services with SendGrid]: store-sendgrid-mobile-services-send-email-scripts.md
[Schedule backend jobs in Mobile Services]: mobile-services-schedule-recurring-tasks.md
[Send push notifications to Windows Store apps using Service Bus from a .NET back-end]: http://go.microsoft.com/fwlink/?LinkId=277073&clcid=0x409
[Mobile Services server script reference]: mobile-services-how-to-use-server-scripts.md
[Get started with Mobile Services]: mobile-services-javascript-backend-windows-store-dotnet-get-started.md
[How To Create a Storage Account]: ../storage-create-storage-account.md
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866 
[Mobile Services .NET How-to Conceptual Reference]: mobile-services-windows-dotnet-how-to-use-client-library.md
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
 

