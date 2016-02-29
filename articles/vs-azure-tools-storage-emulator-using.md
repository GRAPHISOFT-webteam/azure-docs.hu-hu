<properties 
   pageTitle="在 Visual Studio 中設定和使用儲存體模擬器 | Microsoft Azure"
   description="在 Visual Studio 中設定和使用儲存體模擬器"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="storage"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/24/2015"
   ms.author="tarcher" />

# 在 Visual Studio 中設定和使用儲存體模擬器

Azure SDK 開發環境包含儲存體模擬器，這是一個公用程式，可在本機開發電腦上模擬 Azure 提供的 blob、佇列和資料表服務。 如果您要建置雲端服務來採用 Azure 儲存體服務，或撰寫任何外部應用程式來呼叫儲存體服務，您可以在本機利用儲存體模擬器來測試您的程式碼。 Azure Tools for Microsoft Visual Studio 已將儲存體模擬器的管理整合到 Visual Studio 中。 Azure Tools 會在第一次使用儲存體模擬器資料庫時初始化、當您從 Visual Studio 執行或偵錯程式碼時啟動儲存體模擬器服務，並以唯讀方式透過 [Azure 儲存體總管] 存取儲存體模擬器資料。

如需詳細資訊的儲存體模擬器，包括系統需求和自訂組態指示，請參閱 [使用 Azure 儲存體模擬器進行開發和測試](./storage/storage-use-emulator/)。

>[AZURE.NOTE] 有一些功能在儲存體模擬器模擬和 Azure 儲存體服務之間的差異。 請參閱 [差異之間儲存體模擬器和 Azure 儲存體服務](./storage/storage-use-emulator) Azure SDK 的文件中的特定差異的詳細資訊。

## 設定儲存體模擬器的連接字串

若要在角色內從程式碼存取儲存體模擬器，您可以設定連接字串來指向儲存體模擬器，稍後再變更為指向 Azure 儲存體帳戶。 連接字串是您的角色在執行階段可讀取來連接到儲存體帳戶的組態設定。 如需如何建立連接字串的詳細資訊，請參閱 [設定 Azure 應用程式](https://msdn.microsoft.com/library/azure/2da5d6ce-f74d-45a9-bf6b-b3a60c5ef74e#BK_SettingsPage)。

>[AZURE.NOTE] 您可以使用傳回的參考儲存體模擬器帳戶從您的程式碼 **DevelopmentStorageAccount** 屬性。 如果您想要從程式碼存取儲存體模擬器，這種方法能正常運作，但如果您計劃將應用程式發佈至 Azure，則必須建立連接字串來存取 Azure 儲存體帳戶，並在發佈前修改程式碼以使用該連接字串。 如果您經常在儲存體模擬器帳戶和 Azure 儲存體帳戶之間切換，連接字串可以簡化這個程序。

## 初始化及執行儲存體模擬器

您可以指定當您在 Visual Studio 中執行或偵錯服務時，讓 Visual Studio 自動啟動儲存體模擬器。 在 [方案總管] 中，開啟捷徑功能表您 **Azure** 專案，並選擇 **屬性**。 在 **開發** 索引標籤的 **啟動 Azure 儲存體模擬器** 清單中，選擇 **True** (如果它沒有設為該值)。

第一次從 Visual Studio 執行或偵錯服務時，儲存體模擬器會啟動初始化程序。 此程序會保留本機連接埠給儲存體模擬器，並建立儲存體模擬器資料庫。 完成後，除非刪除儲存體模擬器資料庫，否則此程序不必再次執行。

>[AZURE.NOTE] 從 2012 年 6 月版本的 Azure tools 開始，儲存體模擬器執行時，依預設，在 SQL Express。 在舊版的 Azure Tools 中，儲存體模擬器會針對 SQL Express 2005 或 2008 的預設執行個體執行，必須先安裝此軟體才能安裝 Azure SDK。 您也可以針對 SQL Express 具名執行個體或是 Microsoft SQL Server 具名或預設執行個體來執行儲存體模擬器。 如果您需要設定儲存體模擬器的預設執行個體以外的執行個體上執行，請參閱 [使用 Azure 儲存體模擬器進行開發和測試](./storage/storage-use-emulator/)。

儲存體模擬器提供使用者介面，可讓您檢視本機儲存體服務的狀態，並啟動、停止和重新設定它們。 一旦啟動儲存體模擬器服務，您可以用滑鼠右鍵按一下 Windows 工作列的 Microsoft Azure 模擬器通知區域圖示，以顯示使用者介面或是啟動或停止服務。

## 在 [伺服器總管] 中檢視儲存體模擬器資料

[伺服器總管] 中的 [Azure 儲存體] 節點可讓您檢視儲存體帳戶中的資料，以及變更 blob 和資料表資料的設定，包括儲存體模擬器。 請參閱 [瀏覽和管理使用伺服器總管] 中的儲存體資源](https://msdn.microsoft.com/library/azure/ff683677.aspx) 如需詳細資訊。

