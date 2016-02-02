<properties 
    pageTitle="Azure 上使用 Python Tools 2.2 for Visual Studio 的 Bottle 和 Azure 資料表儲存體" 
    description="了解如何使用 Python Tools for Visual Studio 建立 Bottle 應用程式，藉此將資料儲存在 Azure 資料表儲存體中，並將 Web 應用程式部署到 Azure App Service Web Apps。" 
    services="app-service\web" 
    documentationCenter="python" 
    authors="huguesv" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="11/18/2015"
    ms.author="huvalo"/>



# Azure 上使用 Python Tools 2.2 for Visual Studio 的 Bottle 和 Azure 資料表儲存體

在本教學課程中，我們將建立簡單的民調 web 應用程式使用其中一個 PTVS 範例範本來使用 [Python Tools for Visual Studio]。 本教學課程也會提供做為 [視訊](https://www.youtube.com/watch?v=GJXDGaEPy94)。

民調 Web 應用程式會為其儲存機制定義一個抽象概念，讓您輕鬆地切換不同類型的儲存機制 (記憶體內部、Azure 資料表儲存體、MongoDB)。

我們將學習如何建立 Azure 儲存體帳戶、 如何設定 web 應用程式使用 Azure 資料表儲存體，以及如何將 web 應用程式發佈 [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714)s。

使用 MongoDB、 Azure 資料表儲存體、 MySQL 和 SQL Database 服務搭配使用 Bottle、 Flask 和 Django web 架構的 PTVS，請參閱 [Python 開發人員中心] 如需開發的 Azure 應用程式服務 Web 應用程式的文件。 雖然本文著重於應用程式服務，開發 [Azure 雲端服務] 時的步驟很類似。

## 必要條件

 - Visual Studio 2013 或 2015
 - [Python 工具 2.2 for Visual Studio]
 - [Python Tools for Visual Studio 範例 VSIX 2.2]
 - [Azure SDK Tools for VS 2013] 或 [VS 2015 的 Azure SDK Tools]
 - [Python 2.7 32 位元] 或 [Python 3.4 32 位元]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]
>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751), ，您可以立即建立短期入門 web 應用程式的應用程式服務中。 不需要信用卡；沒有承諾。

## 建立專案

在這一節中，我們將使用範例範本建立 Visual Studio 專案。 我們將建立虛擬環境並安裝必要的套件。 然後將使用預設記憶體內部存放庫，在本機執行應用程式。

1.  在 Visual Studio 中，選取 [檔案]****、[新增專案]****。

1.  在 [Python]****、[範例]**** 之下可取得 PTVS 範例 VSIX 中的專案範本。 選取 [Polls Bottle Web Project]****，然後按一下 [確定] 以建立專案。

    ![New Project Dialog](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleNewProject.png)

1.  系統會提示您安裝外部套件。 選取 [安裝到虛擬環境]****。

    ![外部套件對話方塊](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleExternalPackages.png)

1.  選取 [Python 2.7]**** 或 [Python 3.4]**** 作為基礎解譯器。

    ![新增虛擬環境對話方塊](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonAddVirtualEnv.png)

1.  按 `F5` 確認應用程式可運作。 根據預設，應用程式會使用記憶體內部儲存機制，而不需要進行任何設定。 當 Web 伺服器停止時，所有資料都會遺失。

1.  按一下 [Create Sample Polls]****，然後按一下某項民調並投票。

    ![Web Browser](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleInMemoryBrowser.png)

## 建立 Azure 儲存體帳戶

若要使用儲存體作業，您需要 Azure 儲存體帳戶。 您可以依照下列步驟來建立儲存體帳戶。

1.  登入 [Azure 入口網站](https://portal.azure.com/)。

2. 按一下入口網站左上方的 [新增]**** 圖示，然後按一下 [資料+儲存體]**** > [儲存體帳戶]****。 按一下 [ **建立** ] 按鈕，然後為儲存體帳戶指定唯一名稱，並建立新 [資源群組](../resource-group-overview.md) 它。


 建立儲存體帳戶後，[通知]**** 按鈕便會閃爍綠色 [成功]****，儲存體帳戶的刀鋒視窗會開啟，顯示它屬於您所建立的新資源群組。


5. 按一下儲存體帳戶刀鋒視窗中的 [設定]**** 部分。 記下帳戶名稱和金鑰。

    我們在下一節中將需要此資訊來設定您的專案。

## 設定專案

在這一節中，我們會將應用程式設定為使用我們剛才建立的儲存體帳戶。 然後會在本機執行此應用程式。

1.  在 Visual Studio 的 [方案總管] 中，在您的專案節點上按一下滑鼠右鍵，然後選取 [屬性]****。 按一下 [偵錯]**** 索引標籤。

    ![專案偵錯設定](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleAzureTableStorageProjectDebugSettings.png)

1.  在 [偵錯伺服器命令]****、[環境]**** 中設定應用程式所需的環境變數值。

        REPOSITORY_NAME=azuretablestorage
        STORAGE_NAME=<storage account name>
        STORAGE_KEY=<primary access key>

    此舉會在您 [開始偵錯]**** 時設定環境變數。 如果您想要在 [啟動但不偵錯]**** 時設定變數，請在 [執行伺服器命令]**** 下設定相同的值。

    此外，您也可以使用 Windows 控制台定義環境變數。 如果想要避免將認證儲存在原始碼 / 專案檔案中，這是比較好的選項。 請注意，您必須重新啟動 Visual Studio，新的環境值才可用於應用程式。

1.  實作 Azure 資料表儲存體儲存機制的程式碼位於 **models/azuretablestorage.py**。 如需有關如何從 Python 使用表格服務，請參閱 [文件]。

1.  利用 `F5` 執行應用程式。 使用 [Create Sample Polls]**** 建立的民調以及投票所提交的資料將會在 Azure 資料表儲存體中序列化。
    > [AZURE.NOTE] Python 2.7 虛擬環境可能會在 Visual Studio 中造成例外狀況中斷。 按下 `F5` 繼續載入 web 專案。 

1.  瀏覽至 [關於]**** 頁面，確認應用程式是使用「Azure 資料表儲存體」****儲存機制。

    ![Web Browser](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleAzureTableStorageAbout.png)

## 探索 Azure 資料表儲存體

使用 Visual Studio 中的 [伺服器總管] 即可輕易檢視和編輯儲存體資料表。 在這一節中，我們將使用 [伺服器總管] 來檢視民調應用程式資料表的內容。
> [AZURE.NOTE] 這需要 Microsoft Azure Tools 安裝，它可以做為 [Azure SDK for.NET] 的一部分。

1.  開啟 [伺服器總管]****。 依序展開 [Azure]****、[儲存體]****、您的儲存體帳戶和 [資料表]****。


1.  按兩下 [民調]**** 或 [選擇]**** 資料表可在文件視窗中檢視目錄，以及新增/移除/編輯實體。


## 將 Web 應用程式發佈至 Azure App Service

Azure .NET SDK 提供簡單的方法將 Web 應用程式部署至 Azure App Service。

1.  在 [方案總管]**** 中，以滑鼠右鍵按一下專案節點並選取 [發佈]****。


1.  按一下 [Microsoft Azure Web Apps]****。

1.  按一下 [新增]**** 以建立新的 Web 應用程式。

1.  填寫下列欄位，然後按一下 [建立]****。
-   **Web 應用程式名稱**
-   **App Service 計劃**
-   **資源群組**
-   **區域**
-   讓「資料庫伺服器」****維持設定為「沒有資料庫」****


1.  接受所有其他預設值並按一下 [發佈]****。

1.  您的 Web 瀏覽器將會自動開啟到已發佈的 Web 應用程式。 如果您瀏覽至 [關於] 頁面，您會看到它使用 [記憶體內部]**** 儲存機制，而非 [Azure 資料表儲存體]**** 儲存機制。

    這是因為 Azure App Service 中未設定 Web Apps 執行個體的環境變數，所以使用在 **settings.py** 中指定的預設值。

## 設定 Web Apps 執行個體

在本節中，我們將設定 Web Apps 執行個體的環境變數。

1.  在 [Azure 入口網站] 中，依序按一下 [瀏覽]**** > [Web Apps]**** > Web 應用程式名稱，開啟 Web 應用程式的刀鋒視窗。

1.  在您的 Web 應用程式刀鋒視窗中，按一下 [所有設定]**** > [應用程式設定]****。


1.  向下捲動至 **應用程式設定** 區段，並設定的值 **REPOSITORY\_NAME**, ，**STORAGE\_NAME** 和 **STORAGE\_KEY** 中所述 **設定專案** 一節。


1. 依序按一下 [儲存]****、[重新啟動]**** 及 [瀏覽]****。


1.  透過使用「Azure 資料表儲存體」****儲存機制，應該會看到 Web 應用程式如預期般運作。

    恭喜！

    ![Web Browser](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleAzureBrowser.png)

## 後續步驟

請遵循下列連結以深入了解 Python Tools for Visual Studio、Bottle 和 Azure 資料表儲存體。

- [Python Tools for Visual Studio 文件]
  - [Web 專案]
  - [雲端服務專案]
  - [遠端偵錯 Microsoft Azure]
- [Bottle 文件]
- [Azure 儲存體]
- [Azure SDK for Python]
- [如何使用 Python 的資料表儲存體服務]

## 變更的項目

* 如需變更從應用程式服務的網站的指南，請參閱: [Azure App Service，及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)





[python developer center]: /develop/python/ 
[azure cloud services]: ../cloud-services-python-ptvs.md 
[documentation]: ../storage-python-how-to-use-table-storage.md 
[how to use the table storage service from python]: ../storage-python-how-to-use-table-storage.md 
[azure portal]: https://portal.azure.com 
[azure sdk for .net]: http://azure.microsoft.com/downloads/ 
[python tools for visual studio]: http://aka.ms/ptvs 
[python tools 2.2 for visual studio]: http://go.microsoft.com/fwlink/?LinkId=624025 
[python tools 2.2 for visual studio samples vsix]: http://go.microsoft.com/fwlink/?LinkId=624025 
[azure sdk tools for vs 2013]: http://go.microsoft.com/fwlink/?LinkId=323510 
[azure sdk tools for vs 2015]: http://go.microsoft.com/fwlink/?LinkId=518003 
[python 2.7 32-bit]: http://go.microsoft.com/fwlink/?LinkId=517190 
[python 3.4 32-bit]: http://go.microsoft.com/fwlink/?LinkId=517191 
[python tools for visual studio documentation]: http://aka.ms/ptvsdocs 
[bottle documentation]: http://bottlepy.org/docs/dev/index.html 
[remote debugging on microsoft azure]: http://go.microsoft.com/fwlink/?LinkId=624026 
[web projects]: http://go.microsoft.com/fwlink/?LinkId=624027 
[cloud service projects]: http://go.microsoft.com/fwlink/?LinkId=624028 
[azure storage]: http://azure.microsoft.com/documentation/services/storage/ 
[azure sdk for python]: https://github.com/Azure/azure-sdk-for-python 

