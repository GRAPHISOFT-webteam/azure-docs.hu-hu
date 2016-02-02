<properties 
    pageTitle="在 Azure App Service 中還原應用程式" 
    description="了解如何從備份還原您的應用程式。" 
    services="app-service" 
    documentationCenter="" 
    authors="cephalin" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/11/2015" 
    ms.author="cephalin"/>


# 在 Azure App Service 中還原應用程式

本文將說明如何還原您先前備份所使用的應用程式服務應用程式 [應用程式服務](app-service-value-prop-what-is) 備份功能。 如需詳細資訊，請參閱 [應用程式服務備份](web-sites-backup.md)。

App Service「還原」功能可讓您依需求，將應用程式及其連結的資料庫 (SQL Database 或 MySQL) 還原成先前的狀態，或是根據您其中一個原始的應用程式備份來建立新應用程式。 建立與最新版本平行執行的新應用程式，對進行 A/B 測試來說，會相當有用。

應用程式服務還原功能可在 **備份** 刀鋒視窗中的 [Azure 入口網站](http://portal.azure.com), ，僅適用於標準及高階定價層。 如需調整您的應用程式使用標準或高階層的相關資訊，請參閱 [Azure App Service 中調整應用程式](web-sites-scale.md)。 請注意，「進階」層所允許執行的每日備份數量比「標準」層多。

<a name="PreviousBackup"></a>
## 從先前製作的備份還原應用程式

1. 在「Azure 入口網站」中您應用程式的 [設定]**** 刀鋒視窗上，按一下 [備份]**** 以顯示 [備份]**** 刀鋒視窗。 然後，在命令列中按一下 [立即還原]****。

    ![選擇立即還原][chooserestorenow]

3. 在 [還原]**** 刀鋒視窗中，先選取備份來源。

    ![](./media/web-sites-restore/021ChooseSource.png)

    [應用程式備份]**** 選項會顯示所有由應用程式本身直接建立的備份，因為這些是應用程式唯一知道的備份。 您可以輕鬆地選取其中一個。 
    [儲存體]**** 選項可讓您從 [備份]**** 刀鋒視窗中設定的儲存體帳戶和容器中，選取實際的備份 ZIP 檔案。 如果有備份檔案中的任何其他應用程式 
    容器，則您可以選取這些一併還原。

4. 接著，在 [還原目的地]**** 中指定應用程式還原目的地。

    ![](./media/web-sites-restore/022ChooseDestination.png)
    >[AZURE.WARNING] 如果您選擇 [覆寫]****，則與您現有應用程式相關的所有資料都會被清除。 然後按 **確定**,，
    請確定它是完全您要做什麼。

    您可以選取 [現有應用程式]****，將應用程式備份還原到相同資源群組中的另一個應用程式。 使用此選項， 
    您應該已經建立另一個應用程式使用鏡像資料庫組態，定義一個資源群組中
    在應用程式備份。

5. 按一下 [確定]****。

<a name="StorageAccount"></a>
## 從儲存體帳戶下載或刪除備份

1. 從「Azure 入口網站」的主要 [瀏覽]**** 刀鋒視窗中，選取 [儲存體帳戶]****。

    隨即會出現您現有的儲存體帳戶清單。

2. 選取包含您想要下載或刪除之備份的儲存體帳戶。

    [儲存體]**** 刀鋒視窗隨即顯示。

3. 選取 [儲存體]**** 刀鋒視窗中的 [容器]**** 組件，以顯示 [容器]**** 刀鋒視窗。

    隨即會顯示容器清單。 這份清單也會顯示 URL 以及上次修改此容器的日期。

    ![檢視容器][viewcontainers]

4. 在清單中，選取容器以顯示刀鋒視窗，其中會顯示檔案名稱清單，以及每個檔案的大小。

5. 選取檔案之後，您可以選擇 [下載]**** 或 [刪除]**** 檔案。 請注意，有兩種主要的檔案類型：.zip 檔案和.xml 檔案。

<a name="OperationLogs"></a>
## 檢視稽核記錄

1. 若要查看有關應用程式還原作業成功或失敗的詳細資訊，請選取主要 [瀏覽]**** 刀鋒視窗的 [稽核記錄]**** 部分。

    [音訊記錄]**** 刀鋒視窗會顯示您的所有作業，以及層級、狀態、資源和時間詳細資料。

2. 捲動刀鋒視窗來尋找與您應用程式相關的作業。
3. 若要檢視有關作業的其他詳細資訊，請在清單中選取該作業。

[詳細資料] 刀鋒視窗將會顯示與該作業有關的可用資訊。
>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751), ，您可以立即建立短期入門 web 應用程式的應用程式服務中。 不需要信用卡；沒有承諾。

## 變更的項目

* 如需變更從應用程式服務的網站的指南，請參閱: [Azure App Service，及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)



[chooserestorenow]: ./media/web-sites-restore/02ChooseRestoreNow.png 
[viewcontainers]: ./media/web-sites-restore/03ViewContainers.png 
[storageaccountfile]: ./media/web-sites-restore/02StorageAccountFile.png 
[browsecloudstorage]: ./media/web-sites-restore/03BrowseCloudStorage.png 
[storageaccountfileselected]: ./media/web-sites-restore/04StorageAccountFileSelected.png 
[chooserestoresettings]: ./media/web-sites-restore/05ChooseRestoreSettings.png 
[choosedbserver]: ./media/web-sites-restore/06ChooseDBServer.png 
[restoretonewsqldb]: ./media/web-sites-restore/07RestoreToNewSQLDB.png 
[newsqldbconfig]: ./media/web-sites-restore/08NewSQLDBConfig.png 
[restoredcontosowebsite]: ./media/web-sites-restore/09RestoredContosoWebSite.png 
[dashboardoperationlogslink]: ./media/web-sites-restore/10DashboardOperationLogsLink.png 
[managementservicesoperationlogslist]: ./media/web-sites-restore/11ManagementServicesOperationLogsList.png 
[detailsbutton]: ./media/web-sites-restore/12DetailsButton.png 
[operationdetails]: ./media/web-sites-restore/13OperationDetails.png 

