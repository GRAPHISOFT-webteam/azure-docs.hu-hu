<properties
    pageTitle="Python Web 和背景工作角色與 Python Tools 2.2 for Visual Studio | Microsoft Azure"
    description="使用 Python Tools for Visual Studio 建立 Azure 雲端服務的概觀，包括 Web 角色和背景工作角色。"
    services=""
    documentationCenter="python"
    authors="huguesv"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="hero-article"
    ms.date="08/30/2015"
    ms.author="huvalo"/>





# Python Web 和背景工作角色與 Python Tools 2.2 for Visual Studio

這篇文章提供使用 Python web 和背景工作角色的概觀 [Python Tools for Visual Studio []][]。

## 必要條件

 - Visual Studio 2013 或 2015
 - [Python 工具 2.2 的 Visual Studio []][] (PTVS)
 - [Azure SDK Tools for VS 2013 []][] 或 [Azure SDK Tools for VS 2015 []][]
 - [Python 2.7 32-bit[]][] or [Python 3.4 32-bit[]][]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## 什麼是 Python Web 和背景工作角色？

Azure 提供三種計算模型來執行應用程式: [Azure App Service ][execution model-web sites], ，[Azure 虛擬機器 ][execution model-vms], ，和 [Azure 雲端服務 ][execution model-cloud services]。 這三種模型都支援 Python。 雲端服務 (包含 Web 和背景工作角色) 可提供*平台即服務 (PaaS)*。 在雲端服務中，Web 角色應用程式會提供專用的 Internet Information Services (IIS) Web 伺服器，用以代管前端 Web 應用程式，而背景工作角色則可執行獨立於使用者互動或輸入以外的非同步、長時間執行或持續性工作。

如需詳細資訊，請參閱 [何謂雲端服務]。
> [AZURE.NOTE] *尋求建置簡單的網站？*
如果您的案例只需要簡單的網站前端，請考慮使用 Azure App Service 中的輕量型 Web Apps 功能。 隨著網站擴大，以及需求改變，您可以很輕易地升級到雲端服務。 請參閱 <a href="/develop/python/">Python 開發人員中心</a> 開發 Azure 應用程式服務的 Web 應用程式功能的文章。
<br />


## 建立專案

在 Visual Studio 中，您可以在 [Python]**** 下選取 [新增專案]**** 對話方塊中的 [Azure 雲端服務]****。

![New Project Dialog](./media/cloud-services-python-ptvs/new-project-cloud-service.png)

在 [Azure 雲端服務] 精靈中，您可以建立新的 Web 和背景工作角色。

![Azure Cloud Service Dialog](./media/cloud-services-python-ptvs/new-service-wizard.png)

背景工作角色範本隨附未定案程式碼，可連接至 Azure 儲存體帳戶或 Azure 服務匯流排。

![Cloud Service Solution](./media/cloud-services-python-ptvs/worker.png)

您可以隨時將 Web 或背景工作角色加入至現有的雲端服務。 您可以選擇加入方案中現有的專案，或建立新專案。

![Add Role Command](./media/cloud-services-python-ptvs/add-new-or-existing-role.png)

雲端服務可以包含以不同語言實作的角色。 例如，您可以有使用 Django 實作的 Python Web 角色，以及 Python 或 C# 背景工作角色。 您可以使用服務匯流排佇列或儲存體佇列，輕鬆地與角色進行通訊。

## 在本機執行

如果您將雲端服務專案設為啟始專案並按下 F5，雲端服務會在本機 Azure 模擬器中執行。

雖然 PTVS 支援在模擬器中啟動，但無法使用偵錯 (例如，中斷點)。

若要對 Web 和背景工作角色進行偵錯，您可以將角色專案設為啟始專案，然後偵錯。 您也可以設定多個啟始專案。 以滑鼠右鍵按一下方案，然後選取 [設定啟始專案]****。

![Solution Startup Project Properties](./media/cloud-services-python-ptvs/startup.png)

## 發佈至 Azure

若要發佈，請以滑鼠右鍵按一下方案中的雲端服務專案，然後選取 [發佈]****。

![Microsoft Azure Publish Sign In](./media/cloud-services-python-ptvs/publish-sign-in.png)

在設定頁面上，選取您要發佈到其中的雲端服務。

![Microsoft Azure Publish Settings](./media/cloud-services-python-ptvs/publish-settings.png)

如果沒有雲端服務可用，您可以建立新的雲端服務。

![Create Cloud Service Dialog](./media/cloud-services-python-ptvs/publish-create-cloud-service.png)

對機器啟用遠端桌面連線也有助於進行失敗偵錯。

![Remote Desktop Configuration Dialog](./media/cloud-services-python-ptvs/publish-remote-desktop-configuration.png)

組態設定完成之後，按一下 [發行]****。

輸出視窗中會顯示一些進度，接著會出現 [Microsoft Azure 活動記錄] 視窗。

![Microsoft Azure Activity Log Window](./media/cloud-services-python-ptvs/publish-activity-log.png)

部署需要幾分鐘才會完成，然後，Web 及/或背景工作角色就會在 Azure 上運作！

## 後續步驟

如需在 Python Tools for Visual Studio 中使用 Web 和背景工作角色的相關詳細資訊，請參閱 PTVS 文件：

- [雲端服務專案]][]

如需有關從 Web 和背景工作角色使用 Azure 服務的詳細資訊，例如使用 Azure 儲存體或服務匯流排，請參閱下列文章。

- [Blob 服務]][]
- [資料表服務]][]
- [佇列服務]][]
- [服務匯流排佇列]][]
- [服務匯流排主題]][]







[what is a cloud service?]: /manage/services/cloud-services/what-is-a-cloud-service/ 
[execution model-web sites]: fundamentals-application-models.md#WebSites 
[execution model-vms]: fundamentals-application-models.md#VMachine 
[execution model-cloud services]: fundamentals-application-models.md#CloudServices 
[python developer center]: /develop/python/ 
[blob service]: storage-python-how-to-use-blob-storage.md 
[queue service]: storage-python-how-to-use-queue-storage.md 
[table service]: storage-python-how-to-use-table-storage.md 
[service bus queues]: service-bus-python-how-to-use-queues.md 
[service bus topics]: service-bus-python-how-to-use-topics-subscriptions.md 
[python tools for visual studio]: http://aka.ms/ptvs 
[python tools for visual studio documentation]: http://aka.ms/ptvsdocs 
[cloud service projects]: http://go.microsoft.com/fwlink/?LinkId=624028 
[python tools 2.2 for visual studio]: http://go.microsoft.com/fwlink/?LinkID=624025 
[azure sdk tools for vs 2013]: http://go.microsoft.com/fwlink/?LinkId=323510 
[azure sdk tools for vs 2015]: http://go.microsoft.com/fwlink/?LinkId=518003 
[python 2.7 32-bit]: http://go.microsoft.com/fwlink/?LinkId=517190 
[python 3.4 32-bit]: http://go.microsoft.com/fwlink/?LinkId=517191 

