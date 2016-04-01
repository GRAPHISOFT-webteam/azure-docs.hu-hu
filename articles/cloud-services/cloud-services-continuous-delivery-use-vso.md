<properties
    pageTitle="在 Azure 中使用 Visual Studio Team Services 來連續傳遞 | Microsoft Azure"
    description="了解如何設定 Visual Studio Team Services 的 Team 專案，自動建置和部署至 Azure App Service 或雲端服務中的 Web 應用程式功能。"
    services="cloud-services"
    documentationCenter=".net"
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/02/2015"
    ms.author="tarcher"/>

# 使用 Visual Studio Team Services 連續傳遞至 Azure

您可以將 Visual Studio Team Services 小組專案設定為自動建置和部署至 Azure Web 應用程式或雲端服務。  (如需如何設定連續組建及部署系統使用 *內部* Team Foundation Server，請參閱 [Azure 中雲端服務的連續傳遞](cloud-services-dotnet-continuous-delivery.md)。)

本教學課程假設您已安裝 Visual Studio 2013 和 Azure SDK。 如果您還沒有 Visual Studio 2013，下載選擇 **免費開始** 連結 [www.visualstudio.com](http://www.visualstudio.com)。 安裝 Azure SDK，透過 [這裡](http://go.microsoft.com/fwlink/?LinkId=239540)。

> [AZURE.NOTE] 您需要有 Visual Studio Team Services 帳戶，才能完成本教學課程 ︰
> 您可以 [免費開啟 Visual Studio Team Services 帳戶](http://go.microsoft.com/fwlink/p/?LinkId=512979)。

若要使用 Visual Studio Team Services 將雲端服務設定為自動建立和部署至 Azure，請依照下列步驟進行。

## 步驟 1：建立 Team 專案

請依照下列指示 [這裡](http://go.microsoft.com/fwlink/?LinkId=512980) 來建立 team 專案，並將它連結至 Visual Studio。 本逐步解說假設您使用 Team Foundation 版本控制 (TFVC) 做為原始檔控制解決方案。 如果您想要使用 Git 進行版本控制，請參閱 [本逐步解說的 Git 版本](http://go.microsoft.com/fwlink/p/?LinkId=397358)。

## 步驟 2：將專案簽入原始檔控制

1. 在 Visual Studio 中，開啟您要部署的方案，或建立新方案。
您可以依照此逐步解說的步驟部署 Web 應用程式或雲端服務 (Azure 應用程式)。
如果要建立新方案，請建立新的 Azure 雲端服務專案，
或建立新的 ASP.NET MVC 專案。 請確定專案以 .NET Framework 4 或 4.5 為目標，如果是建立雲端服務專案，請加入 ASP.NET MVC Web 角色和背景工作角色，然後對 Web 角色選擇網際網路應用程式。 出現提示時，選擇 [ **網際網路應用程式**。
如果要建立 Web 應用程式，請選擇 ASP.NET Web 應用程式的專案範本，然後選擇 [MVC]。 請參閱 [Azure App Service 中建立 ASP.NET web 應用程式](../web-sites-dotnet-get-started.md)。

    > [AZURE.NOTE] Visual Studio Team Services 僅支援目前的 Visual Studio Web 應用程式的 CI 部署。 Web Site 專案超出範圍。

1. 開啟方案的內容功能表並選擇 [ **將方案加入至原始檔控制**。

    ![][5]

1. 接受或變更預設值，然後選擇 [ **確定** ] 按鈕。 完成此程序時，原始檔控制圖示會出現在 **方案總管] 中**。

    ![][6]

1. 開啟方案的捷徑功能表，然後選擇 **簽入**。

    ![][7]

1. 在 **暫止的變更** 區域 **Team Explorer**, ，輸入簽入註解，然後選擇 [ **簽入** ] 按鈕。

    ![][8]

    請注意簽入時用來包含或排除特定變更的選項。 如果您要排除的變更，選擇 **全部包含** 連結。

    ![][9]

## 步驟 3：將專案連接至 Azure

1. 您現有一個 VSTS 小組專案，且裡面有一些原始程式碼，可以準備將小組專案連線至 Azure。  在 [Azure 傳統入口網站](http://manage.windowsazure.com), ，選取 [雲端服務或 web 應用程式，或建立一個新的選擇 **+** 底部離開，且選擇圖示 **定域機組服務** 或 **Web 應用程式** 然後 **快速建立**。 選擇 **設定發行與 Visual Studio Team Services** 連結。

    ![][10]

1. 在精靈中，輸入您的 Visual Studio Team Services 帳戶名稱] 文字方塊中按一下 **立即授權** 連結。 可能會要求您登入。

    ![][11]

1. 在 **連線要求** 快顯對話方塊中，選擇 [ **接受** ，授權 Azure 在 VSTS 中設定您的 team 專案的按鈕。

    ![][12]

1. 授權成功時，將出現含有 Visual Studio Team Services 小組專案清單的下拉清單。 選擇您在先前步驟中，建立 team 專案的名稱，然後選擇精靈的核取記號按鈕。

    ![][13]

1. 連結專案之後，將出現一些指示，指出如何將變更簽入至 Visual Studio Team Services 小組專案。  下次簽入時，Visual Studio Team Services 就會建立專案並部署至 Azure。  現在就試藉由按一下 **從 Visual Studio 簽入** 連結，然後 **啟動 Visual Studio** 連結 (或同等權限 **Visual Studio** 入口網站畫面底部的按鈕)。

    ![][14]

## 步驟 4：觸發重建和重新部署專案

1. 在 Visual Studio **Team Explorer**, ，選擇 [ **原始檔控制總管** 連結。

    ![][15]

1. 瀏覽至方案檔並開啟。

    ![][16]

1. 在 **方案總管] 中**, ，開啟檔案並變更它。 例如，變更檔案 `_Layout.cshtml` MVC web 角色中的 Views\\Shared 資料夾底下。

    ![][17]

1. 編輯網站的標誌，然後選擇 [ **Ctrl + S** 儲存檔案。

    ![][18]

1. 在 **Team Explorer**, ，選擇 [ **暫止的變更** 連結。

    ![][19]

1. 輸入註解，然後選擇 **簽入** ] 按鈕。

    ![][20]

1. 選擇 **家用** ] 按鈕回到 **Team Explorer** 首頁。

    ![][21]

1. 選擇 **組建** 連結來檢視進行中的組建。

    ![][22]

    **Team Explorer** 顯示簽入已觸發組建。

    ![][23]

1. 按兩下進行中的組建名稱，檢視進行中組建的詳細記錄。

    ![][24]

1. 當組建進行時，查看您使用精靈將 TFS 連結至 Azure 時所建立的組建定義。  開啟組建定義的捷徑功能表，然後選擇 **編輯組建定義**。

    ![][25]

    在 **觸發程序** 索引標籤上，您會看到組建定義來建立每個簽入的設定預設。

    ![][26]

    在 **程序** 索引標籤上，您可以看到部署環境已設為雲端服務或 web 應用程式的名稱。 如果您使用的是 Web 應用程式，則顯示的屬性跟下面的屬性不同。

    ![][27]

1. 如果不想要使用預設值，請指定屬性的值。 Azure 發行屬性位於 **部署** 一節。

    下表顯示可用的屬性在 **部署** 區段 ︰

  	|屬性|預設值|
  	|---|---|
  	|允許未受信任的憑證|如果為 false，SSL 憑證必須經過根授權單位簽署。|
  	|允許升級|允許部署更新現有的部署而非建立新的部署。 保留 IP 位址。|
  	|不要刪除|如果為 true，則不要覆寫現有不相關的部署 (允許升級)。|
  	|部署設定的路徑|Web 應用程式的 .pubxml 檔的路徑，這是儲存機制之根資料夾的相對路徑。 雲端服務則會忽略。|
  	|SharePoint 部署環境|與服務名稱相同。|
  	|Azure 部署環境|Web 應用程式或雲端服務的名稱。|

1. 如果您使用多個服務組態 （.cscfg 檔），您可以指定所需的服務組態中 **組建、 進階、 MSBuild 引數** 設定。 例如，若要使用 ServiceConfiguration.Test.cscfg，請設定 MSBuild 引數的命令列選項 `/p:TargetProfile=Test`。

    ![][38]

    現在應該已順利完成您的組建。

    ![][28]

1. 如果您按兩下組建名稱，Visual Studio 會顯示 **組建摘要**, ，包括任何測試結果相關聯的單元測試專案。

    ![][29]

1. 在 [Azure 傳統入口網站](http://manage.windowsazure.com), ，您可以檢視相關聯的部署上 **部署** 索引標籤上，選取預備環境時。

    ![][30]

1.  瀏覽至網站的 URL。 Web 應用程式，只要按一下 **瀏覽** 命令列上的按鈕。 雲端服務中，選擇 [在 URL **快速概覽** 區段 **儀表板** 頁面顯示雲端服務的預備環境。 依預設，來自雲端服務連續整合的部署會發行至預備環境。 您可以變更此設定 **替代雲端服務環境** 屬性 **生產**。 此擷取畫面顯示網站 URL 在雲端服務儀表板頁面上的位置。

    ![][31]

    新的瀏覽器索引標籤會開啟來顯示您執行中的網站。

    ![][32]

    若為雲端服務，如果對專案進行其他變更，則會觸發更多組建，將累積多個部署。 最後一個會標示為「作用中」。

    ![][33]

## 步驟 5：重新部署舊版組建

此步驟適用於雲端服務，且為選用的。 在 Azure 傳統入口網站中，選擇 [先前的部署，然後按一下 **重新部署** 倒轉舊版簽入您的網站] 按鈕。  請注意，這會在 TFS 中觸發新的組建，並在部署歷程記錄中建立新的項目。

![][34]

## 步驟 6：變更生產部署

此步驟僅適用於雲端服務，不適用於 Web 應用程式。 準備好時，您可以選擇將升級預備環境到生產環境 **交換** Azure 傳統入口網站中的按鈕。 新部署的預備環境會升級至「生產」，而先前的生產環境 (若有的話) 會變成預備環境。 「作用中」部署可能與生產和預備環境不同，但最近組建的部署歷程記錄都一樣，與環境無關。

![][35]

## 步驟 7：執行單元測試

此步驟僅適用於 Web 應用程式，不適用於雲端服務。 若要為部署的品質把關，您可以執行單元測試；如果測試失敗，則可以停止部署。

1.  在 Visual Studio 中，加入單元測試專案。

    ![][39]

1.  將專案參考加入您要測試的專案。

    ![][40]

1.  加入一些單元測試。 若要開始使用，請嘗試一律會通過的虛擬測試。

        ```
        using System;
        using Microsoft.VisualStudio.TestTools.UnitTesting;

        namespace UnitTestProject1
        {
            [TestClass]
            public class UnitTest1
            {
                [TestMethod]
                [ExpectedException(typeof(NotImplementedException))]
                public void TestMethod1()
                {
                    throw new NotImplementedException();
                }
            }
        }
        ```

1.  編輯組建定義中，選擇 [ **程序** 索引標籤，然後展開 **測試** 節點。

1.  設定 **測試失敗時使組建失敗** 設為 True。 換句話說，除非通過測試，否則不會進行部署。

    ![][41]

1.  將新組建排入佇列。

    ![][42]

    ![][43]

1. 在建置進行時，檢查其進度。

    ![][44]

    ![][45]

1. 在建置完成時，檢查測試結果。

    ![][46]

    ![][47]

1.  嘗試建立將失敗的測試。 透過複製第一個測試、將其重新命名，並將 NotImplementedException 標記為預期的例外狀況的程式碼行加上註解，來加入新的測試。

        ```
        [TestMethod]
        //[ExpectedException(typeof(NotImplementedException))]
        public void TestMethod2()
        {
            throw new NotImplementedException();
        }
        ```

1. 簽入變更，以將新組建排入佇列。

    ![][48]

1. 檢視測試結果，以查看失敗的詳細資料。

    ![][49]

    ![][50]

## 後續步驟
如需有關 Visual Studio Team Services 中的單元測試的詳細資訊，請參閱 [在建置中執行單元測試](http://go.microsoft.com/fwlink/p/?LinkId=510474)。 如果您使用 Git，請參閱 [Git 中共用程式碼](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) 和 [Azure App Service 中使用 GIT 連續部署](../web-sites-publish-source-control.md)。  如需 Visual Studio Team Services 的詳細資訊，請參閱 [Visual Studio Team Services](http://go.microsoft.com/fwlink/?LinkId=253861)。

[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso/tfs1.png
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png

[5]: ./media/cloud-services-continuous-delivery-use-vso/tfs5.png
[6]: ./media/cloud-services-continuous-delivery-use-vso/tfs6.png
[7]: ./media/cloud-services-continuous-delivery-use-vso/tfs7.png
[8]: ./media/cloud-services-continuous-delivery-use-vso/tfs8.png
[9]: ./media/cloud-services-continuous-delivery-use-vso/tfs9.png
[10]: ./media/cloud-services-continuous-delivery-use-vso/tfs10.png
[11]: ./media/cloud-services-continuous-delivery-use-vso/tfs11.png
[12]: ./media/cloud-services-continuous-delivery-use-vso/tfs12.png
[13]: ./media/cloud-services-continuous-delivery-use-vso/tfs13.png
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso/tfs18.png
[19]: ./media/cloud-services-continuous-delivery-use-vso/tfs19.png
[20]: ./media/cloud-services-continuous-delivery-use-vso/tfs20.png
[21]: ./media/cloud-services-continuous-delivery-use-vso/tfs21.png
[22]: ./media/cloud-services-continuous-delivery-use-vso/tfs22.png
[23]: ./media/cloud-services-continuous-delivery-use-vso/tfs23.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso/tfs27.png
[28]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso/tfs37.PNG
[38]: ./media/cloud-services-continuous-delivery-use-vso/AdvancedMSBuildSettings.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso/AddUnitTestProject.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso/AddProjectReferences.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso/EditBuildDefinitionForTest.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso/QueueNewBuild.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso/QueueBuildDialog.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso/BuildInTeamExplorer.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso/BuildRequestInfo.PNG
[46]: ./media/cloud-services-continuous-delivery-use-vso/BuildSucceeded.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso/TestResultsPassed.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso/CheckInChangeToMakeTestsFail.PNG
[49]: ./media/cloud-services-continuous-delivery-use-vso/TestsFailed.PNG
[50]: ./media/cloud-services-continuous-delivery-use-vso/TestsResultsFailed.PNG


