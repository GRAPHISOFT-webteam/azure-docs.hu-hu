<properties
    pageTitle="使用混合式連線將 Azure 行動應用程式連線到內部部署 SQL Server | Microsoft Azure"
    description="了解如何使用混合式連線從 App Service Mobile App 連線至內部佈署 SQL Server"
    services="app-service\mobile"
    documentationCenter=""
    authors="ggailey777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.date="10/23/2015"
    ms.author="glenga"/>



# 使用混合式連線從 Mobile Apps 連線到內部部署 SQL Server

貴公司在轉移到雲端環境時，可能無法立即就將所有資產移轉至 Azure。 混合式連線可讓 Azure 應用程式服務中的 Mobile Apps 功能安全地連線到內部部署的資產。 因此，您可以藉由混合式連線讓行動用戶端使用 Azure 存取內部部署資料。 支援的資產包括任何可在靜態 TCP 連接埠上執行的資源，例如 Microsoft SQL Server、MySQL、HTTP Web API 和大部分的自訂 Web 服務。 混合式連線採用共用存取簽章 (SAS) 授權，以確保從行動服務和內部部署混合式連線管理員連往混合式連線的連線安全無虞。 如需詳細資訊，請參閱 [混合式連線概觀](../integration-hybrid-connection-overview.md)。

在此教學課程中，您將學習如何修改行動應用程式的 .NET 後端，而非以您服務佈建的預設 Azure SQL Database，以使用本機內部部署的 SQL Server 資料庫。

[AZURE.INCLUDE [app-service-mobile-to-web-and-api](../../includes/app-service-mobile-to-web-and-api.md)]

## 必要條件

本教學課程要求您具備下列項目：

- **現有的行動應用程式後端** <br/>遵循 [快速入門教學課程](app-service-mobile-windows-store-dotnet-get-started.md) 建立和下載新的.NET 後端行動應用程式從 [Azure 入口網站]。

[AZURE.INCLUDE [hybrid-connections-prerequisites](../../includes/hybrid-connections-prerequisites.md)]

## 在內部部署中安裝 SQL Server Express、啟用 TCP/IP 及建立 SQL Server 資料庫

[AZURE.INCLUDE [hybrid-connections-create-on-premises-database](../../includes/hybrid-connections-create-on-premises-database.md)]

## 建立混合式連線

您必須為行動應用程式後端的程式碼部分 (也就是 Web 應用程式) 建立新的混合連線和 BizTalk 服務。

1. 在 [Azure 入口網站]，瀏覽至您的行動應用程式並按一下 **網路** 設定中。

    ![瀏覽到 Web 應用程式](./media/app-service-mobile-dotnet-backend-hybrid-connections-get-started/mobile-app-link-to-web-app-backend.png)

2. 按一下 [混合式連線]**** 區段中的 [設定混合式連線端點]****。

    ![混合式連線](./media/app-service-mobile-dotnet-backend-hybrid-connections-get-started/start-hybrid-connection.png)

2. 在 [混合式連線] 刀鋒視窗上，按一下 [**新增**] > [**新增混合式連線**]。

3. 在 **建立混合式連線分頁]**, ，提供 **名稱** 和 **主機名稱** ，混合式連線並將 **連接埠** 至 `1433年`。

    ![建立混合式連線](./media/app-service-mobile-dotnet-backend-hybrid-connections-get-started/create-hybrid-connection.png)

4. 按一下 [**Biz Talk 服務**] 並輸入 BizTalk 服務的名稱，然後按一下 [**確定**] 兩次。

    本教學課程使用 **mobile1**。 您必須提供新的 BizTalk 服務的唯一名稱。

    程序完成後，[**通知**] 區域會閃爍綠色 [**成功**]，而且 [**混合式連線**] 刀鋒視窗會顯示新的混合式連線，且狀態為 [**未連線**]。

    ![One hybrid connection created](./media/app-service-mobile-dotnet-backend-hybrid-connections-get-started/hybrid-connection-created.png)

此時，您已經完成雲端混合式連線基礎結構的重要部分。 接下來，您會建立對應的內部部署片段。

## 安裝內部部署混合式連線管理員以完成連線

[AZURE.INCLUDE [app-service-hybrid-connections-manager-install](../../includes/app-service-hybrid-connections-manager-install.md)]

## 設定行動應用程式後端專案以連線到 SQL Server 資料庫

在此步驟中，您將定義內部部署資料庫的連接字串，並修改行動應用程式後端以使用這個連線。

1. 在 Visual Studio 2013 中，開啟定義行動應用程式後端的專案。

    若要了解如何下載.NET 後端專案，請參閱 [快速入門教學課程](app-service-mobile-windows-store-dotnet-get-started.md)。

2. 在 [方案總管] 中開啟 Web.config 檔案，找出 **connectionStrings** 區段，加入類似下面內容的新 SqlClient 項目，此項目指向內部部署 SQL Server 資料庫：

        <add name="OnPremisesDBConnection"
         connectionString="Data Source=OnPremisesServer,1433;
         Initial Catalog=OnPremisesDB;
         User ID=HybridConnectionLogin;
         Password=<**secure_password**>;
         MultipleActiveResultSets=True"
         providerName="System.Data.SqlClient" />

    請務必取代 `< * * secure_password * * >` 為您建立的密碼與這個字串中  *HybridConnectionLogin*。

3. 在 Visual Studio 中按一下 [儲存]****，以儲存 Web.config 檔案。
    > [AZURE.NOTE]在本機電腦上執行時，會使用此連線設定。 在 Azure 中執行時，則會以入口網站中所定義的連線設定覆寫這個設定。

4. 展開 **Models** 資料夾，然後開啟以 *Context.cs* 結尾的資料模型檔案。

6. 修改 **DbContext** 執行個體建構函式將值傳遞 `OnPremisesDBConnection` 基底 **DbContext** 建構函式，類似下列的程式碼片段:

        public class hybridService1Context : DbContext
        {
            public hybridService1Context()
                : base("OnPremisesDBConnection")
            {
            }
        }

    現在，服務將會使用新的 SQL Server 資料庫連線。

## 更新 Azure 以使用內部部署連接字串

接下來，您必須為這個新的連接字串新增應用程式設定，以便能夠從 Azure 使用。

1. 回到 [Azure 入口網站] 在您的行動應用程式的 web 應用程式後端程式碼中，按一下 [ **所有設定**, ，然後 **應用程式設定**。

3. 中 **Web 應用程式設定** 刀鋒視窗中，向下捲動至 **連接字串** 並加入新 **SQL Server** 名為連接字串 `OnPremisesDBConnection` 之類的值 `Server = OnPremisesServer，1433; Database = OnPremisesDB;使用者識別碼 = HybridConnectionsLogin;密碼 = < * * secure_password * * >`。

    取代 `< * * secure_password * * >` 取代為您的內部部署資料庫的安全密碼。

    ![Connection string for on-premises database](./media/app-service-mobile-dotnet-backend-hybrid-connections-get-started/set-sql-server-database-connection.png)

2. 按下 [**儲存**]，以儲存您剛剛建立的混合式連線和連接字串。

## 在 Azure 中發佈和測試行動應用程式後端

最後，您必須將行動應用程式後端發佈至 Azure，並確認它有使用混合式連線將資料儲存在內部部署資料庫內。

3. 在 Visual Studio 中，以滑鼠右鍵按一下專案，按一下 [**發佈**]，然後在 [**發佈 Web**] 中按一下 [**Microsoft Azure 網站**]。

    除了使用 Visual Studio，您也可以 [使用 Git 來發行您的後端](mobile-services-dotnet-backend-store-code-source-control.md)。

2. 使用 Azure 認證進行登入，並從 [**選取現有網站**] 中選取您的服務。

    Visual Studio 便會直接從 Azure 下載您的發佈設定。

3. 最後，按一下 [**發佈**]。

    發佈完成後，就會重新啟動服務並顯示後端啟動頁面。

4. 使用連接到行動應用程式的用戶端應用程式，叫用會產生資料庫變更的某些作業。

5. 在 SQL Server Management Studio 中，連線到 SQL Server 執行個體，開啟物件總管，然後依序展開 [**OnPremisesDB**] 資料庫和 [**表格**]。

6. 以滑鼠右鍵按一下 **hybridService1.TodoItems** 資料表，然後選擇 [Select Top 1000 Rows]**** 以檢視結果。

    請注意，您的行動應用程式後端會使用混合式連線，將用戶端應用程式中產生的變更儲存到內部部署資料庫。

## 另請參閱

+ [混合式連線網站](../../services/biztalk-services/)
+ [混合式連線概觀](../integration-hybrid-connection-overview.md)
+ [BizTalk 服務: 儀表板、 監視器、 調整、 設定和混合式連線索引標籤](../biztalk-dashboard-monitor-scale-tabs.md)
+ [如何對.NET 後端行動服務進行資料模型變更](../mobile-services-dotnet-backend-how-to-use-code-first-migrations.md)






[azure portal]: https://portal.azure.com/ 
[azure classic portal]: http://go.microsoft.com/fwlink/p/?linkid=213885 
[get started with mobile services]: ../mobile-services-windows-store-dotnet-get-started.md 

