<properties 
    pageTitle="如何使用 .NET 設定用於媒體服務開發的電腦" 
    description="了解將 Media Services SDK for .NET 用於媒體服務的必要條件。 同時了解如何建立 Visual Studio 應用程式。" 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="10/05/2015"
    ms.author="juliako"/>

#使用 .NET 進行媒體服務開發

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

本主題討論如何使用 .NET 開始開發媒體服務應用程式。

 **Azure 媒體服務.NET SDK** 程式庫可讓您對使用.NET 進行媒體服務進行程式設計。 若要讓使用.NET 進行開發更為簡單 **Azure 媒體服務.NET SDK 延伸** 程式庫。 此程式庫包含一組延伸方法和協助程式函數，以簡化 .NET 程式碼。 這兩個程式庫都是透過 **NuGet** 和 **GitHub**。


##必要條件

-   新的或現有 Azure 訂閱中的媒體服務帳戶。 請參閱主題 [如何建立媒體服務帳戶](media-services-create-account.md)。
-   作業系統：Windows 10、Windows 7、Windows 2008 R2 或 Windows 8。
-   .NET Framework 4.5。
-    Visual Studio 2015、Visual Studio 2013、Visual Studio 2012 或 Visual Studio 2010 SP1 (Professional、Premium、Ultimate 或 Express)。


##建立和設定 Visual Studio 專案

本節說明如何在 Visual Studio 中建立專案並設定來進行媒體服務開發。  在此案例中，專案是 C# Windows 主控台應用程式，但這裡顯示的設定步驟也適用於可以為媒體服務應用程式建立的其他專案類型 (例如，Windows Forms 應用程式或 ASP.NET Web 應用程式)。

本節說明如何使用 **NuGet** 新增 Media Services.NET SDK 和其他相依程式庫。

或者，您可以從 GitHub 取得最新的媒體服務.NET SDK 位元 ([github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) 和 [github.com /azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions))、 建置方案，並加入至用戶端專案的參考。 請注意，會下載並自動解壓縮所有必要相依性。

1. 在 Visual Studio 2013、Visual Studio 2012 或 Visual Studio 2010 SP1 中，建立新的 C# 主控台應用程式。 輸入 **名稱**, ，**位置**, ，和 **方案名稱**, ，然後按一下 [確定]。

2. 建置方案。

2. 使用 **NuGet** 來安裝和新增 **Azure 媒體服務.NET SDK 延伸**。 安裝這個封裝，也會安裝 **媒體服務.NET SDK** ，並將所有其他必要的相依性。
1. 確定您已安裝 NuGet 的最新版本。 如需詳細資訊和安裝指示，請參閱 [NuGet](http://nuget.codeplex.com/)。

2. 在 [方案總管] 中，於專案名稱上按一下滑鼠右鍵，然後選擇 [管理 NuGet 封裝]。

[管理 NuGet 封裝] 對話方塊隨即出現。

3. 在線上資源庫中，搜尋 [Azure MediaServices 延伸模組]，並選擇 [Azure Media Services .NET SDK 延伸模組]，然後按一下 [安裝] 按鈕。

修改的專案，並加入 Media Services.NET SDK 延伸模組、 Media Services.NET SDK 和其他相依組件參考。

4. 若要提升更乾淨的開發環境，請考慮啟用 [NuGet 封裝還原]。 如需詳細資訊，請參閱 [NuGet 封裝還原"](http://docs.nuget.org/consume/package-restore)。

3. 將參考加入 **System.configuration**組件。 此組件包含 System.Configuration。**ConfigurationManager** 類別，用來存取組態檔 (例如，App.config)。

若要使用 [管理參考] 對話方塊加入參考，請執行下列動作：

1. 在 [方案總管] 中，於專案名稱上按一下滑鼠右鍵。 然後，依序選取 [加入] 和 [參考]。

[管理參考] 對話方塊隨即出現。

2. 在 .NET Framework 組件下，尋找並選取 System.Configuration 組件。
3. 按下 [確定]。


4. 開啟 App.config 檔案 （如果尚未新增，則預設，檔案新增至您的專案），並新增 *appSettings* 區段加入檔案。 設定 Azure 媒體服務帳戶名稱和帳戶金鑰的值 (如下列範例所示)。

若要取得 **帳戶名稱** 和 **帳戶金鑰** 資訊，請開啟 **Azure 傳統入口網站**, ，選取媒體服務帳戶，然後按一下 [ **管理金鑰** ] 按鈕。


<configuration>
    ...
      <appSettings>
        <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
        <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
      </appSettings>
      
    </configuration>

5. 在 Program.cs 檔案的開頭，使用下列程式碼來覆寫現有的 using 陳述式。

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using System.Configuration;
        using System.Threading;
        using System.IO;
        using Microsoft.WindowsAzure.MediaServices.Client;

現在，您可以開始開發媒體服務應用程式。    



##媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


