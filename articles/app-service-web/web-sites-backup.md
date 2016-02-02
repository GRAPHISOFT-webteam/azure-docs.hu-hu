<properties 
    pageTitle="在 Azure App Service 中備份 Web 應用程式" 
    description="了解如何在 Azure App Service 中建立 Web 應用程式的備份" 
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
    ms.date="12/10/2015" 
    ms.author="cephalin"/>


# 在 Azure App Service 中備份 Web 應用程式

中的備份和還原功能 [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) 可讓您輕鬆建立 web 應用程式備份，手動或自動。 您可以將 Web 應用程式還原至先前的狀態，或根據原始應用程式的其中一個備份來建立新的 Web 應用程式。

如需從備份還原 Azure web 應用程式的詳細資訊，請參閱 [還原 web 應用程式](web-sites-restore.md)。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

<a name="whatsbackedup"></a>
## 備份什麼項目

Web Apps 可以備份下列資訊：

* Web 應用程式設定
* Web 應用程式檔案內容
* 任何連接到您應用程式的 Azure SQL 資料庫或 MySQL 資料庫 (您可以選擇要將哪些資料庫納入備份)

這項資訊會備份至您指定的 Azure 儲存體帳戶和容器。
> [AZURE.NOTE] 每個備份都是應用程式的完整離線複本，而不是增量更新。

<a name="requirements"></a>
## 需求和限制

* 若要使用「備份與還原」功能，App Service 方案必須屬於「標準」層或更高階層。 如需調整您的應用程式服務計劃，以使用較高的層次的詳細資訊，請參閱 [Azure App Service 中調整 web 應用程式](web-sites-scale.md)。 請注意，「進階」層所允許的每日備份數量比「標準」層多。

* 使用備份和還原功能時，Azure 儲存體帳戶和容器必須與您即將備份的 Web 應用程式隸屬於相同的訂用帳戶。 如果您還沒有儲存體帳戶，您可以建立一個依序按一下 **儲存體帳戶** 中 **備份** 刀鋒視窗中的 [Azure 入口網站](http://portal.azure.com), ，然後選擇 [ **儲存體帳戶** 和 **容器** 從 **目的地** 刀鋒視窗。 如需 Azure 儲存體帳戶的詳細資訊，請參閱 [連結](#moreaboutstorage) 這篇文章的結尾。

* 備份和還原功能支援最多 10GB 的網站和資料庫內容。 如果備份功能因承載超出此限制而無法繼續進行，系統將會指出錯誤。

<a name="manualbackup"></a>
## 建立手動備份

1. 在 Azure 入口網站中，從 Web Apps 刀鋒視窗中選擇您的 Web 應用程式。 這將會在新的刀鋒視窗中顯示您的 Web 應用程式詳細資料。
2. 在您應用程式的刀鋒視窗中選取 [設定]****，然後選取 [備份]****。 [備份]**** 刀鋒視窗隨即顯示。

    ![Backups page][choosebackupspage]

3. 在 [備份]**** 刀鋒視窗中，按一下 [儲存體: 未設定]**** 以設定儲存體帳戶。

    ![Choose storage account][choosestorageaccount]

4. 選取 [儲存體帳戶]**** 和 [容器]****，以選擇您的備份目的地。 此儲存體帳戶必須與您即將備份的 Web 應用程式隸屬於相同的訂用帳戶。 如果您希望的話，也可以在個別的刀鋒視窗中，建立新的儲存體帳戶或新的容器。 完成後，按一下 [選取]****。

    ![Choose storage account](./media/web-sites-backup/02ChooseStorageAccount1.png)

5. 在仍處於開啟狀態的 [設定備份設定]**** 刀鋒視窗中，按一下 [資料庫設定]****，然後選取您想要包含在備份中的資料庫 (SQL 資料庫或 MySQL)，然後按一下 [確定]****。

    ![Choose storage account](./media/web-sites-backup/03ConfigureDatabase.png)
    > [AZURE.NOTE]  資料庫若要出現在此清單中，其連接字串必須存在於入口網站之 [Web 應用程式設定]**** 刀鋒視窗的 [連接字串]**** 區段中。

6. 在 [設定備份設定]**** 刀鋒視窗中，按一下 [儲存]****。
6. 在 [**備份**] 刀鋒視窗中，選取 [**備份目的地**]。 您必須選擇現有的儲存體帳戶和容器。
7. 在 [備份]**** 刀鋒視窗的命令列中，按一下 [立即備份]****。

    ![BackUpNow button][backupnow]

    在備份過程中，您將會看見進度訊息。


您可以隨時進行手動備份。

<a name="automatedbackups"></a>
## 設定自動備份

1. 在 [備份]**** 刀鋒視窗中，按一下 [排程: 未設定]****。

    ![Choose storage account](./media/web-sites-backup/05ScheduleBackup.png)

1. 在 [備份排程設定]**** 刀鋒視窗中，將 [排定的備份]**** 設定為 [開啟]****，然後視需要設定備份排程並按一下 [確定]****。

    ![啟用自動備份][setautomatedbackupon]

4. 在仍處於開啟狀態的 [設定備份設定]**** 刀鋒視窗中，按一下 [儲存體設定]****，然後選取「儲存體帳戶」****和「容器」****來選擇您的備份目的地。 此儲存體帳戶必須與您即將備份的 Web 應用程式隸屬於相同的訂用帳戶。 如果您希望的話，也可以在個別的刀鋒視窗中，建立新的儲存體帳戶或新的容器。 完成後，按一下 [選取]****。

    ![Choose storage account](./media/web-sites-backup/02ChooseStorageAccount1.png)

5. 在 [設定備份設定]**** 刀鋒視窗中，按一下 [資料庫設定]****，然後選取您想要包含在備份中的資料庫 (SQL 資料庫或 MySQL)，然後按一下 [確定]****。

    ![Choose storage account](./media/web-sites-backup/03ConfigureDatabase.png)
    > [AZURE.NOTE]  資料庫若要出現在此清單中，其連接字串必須存在於入口網站之 [Web 應用程式設定]**** 刀鋒視窗的 [連接字串]**** 區段中。

6. 在 [設定備份設定]**** 刀鋒視窗中，按一下 [儲存]****。

<a name="notes"></a>
## 注意事項

* 請確定會在 Web 應用程式 [**設定**] 中的 [**Web 應用程式設定**] 刀鋒視窗上，適當地設定每個資料庫的連接字串，使備份和還原功能可納入您的資料庫。

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751), ，您可以立即建立短期入門 web 應用程式的應用程式服務中。 不需要信用卡；沒有承諾。

<a name="partialbackups"></a>
## 只備份您 Web 應用程式的一部分

您有時不想要備份 Web 應用程式上的所有項目。 以下是一些範例：

-   您 [設定每週備份](web-sites-backup.md#configure-automated-backups) 的 web 應用程式，其中包含絕對不會變更，例如舊部落格文章或影像的靜態內容。
-   您 Web 應用程式的內容超過 10 GB (亦即一次可以備份的最大數量)。
-   您不想要備份記錄檔。

部分備份可讓您選擇只針對想要備份的檔案進行備份。

### 從備份中排除檔案

若要從備份中排除檔案和資料夾，建立 `_backup.filter` 檔案中的 web 應用程式的 [wwwroot] 資料夾，並指定您想要在其中排除檔案和資料夾的清單。 若要存取這個簡單的方法是透過 [Kudu 主控台](https://github.com/projectkudu/kudu/wiki/Kudu-console)。

假設您有一個 Web 應用程式，其中包含過去幾年的記錄檔和靜態影像，且以後不再變更。 您已完整備份 Web 應用程式，包括舊的影像。 現在您想要每天備份 Web 應用程式，但不想付費儲存從未變更的記錄檔或靜態影像檔案。

![Logs 資料夾][logsfolder]
![Images 資料夾][imagesfolder]

下列步驟顯示如何從備份中排除這些檔案。

1. 移至 `http://{yourapp}.scm.azurewebsites.net/DebugConsole` 並找出您想要從備份中排除的資料夾。 在此範例中，您想要排除下列檔案和資料夾 (如該 UI 所示)：

        D:\home\site\wwwroot\Logs
        D:\home\LogFiles
        D:\home\site\wwwroot\Images\2013
        D:\home\site\wwwroot\Images\2014
        D:\home\site\wwwroot\Images\brand.png

    [AZURE。附註] 最後一行說明您可以排除個人檔案以及資料夾。

2. 建立檔案，稱為 `_backup.filter` 與上述清單放在檔案中，但移除 `D:\home`。 一行列出一個目錄或檔案。 因此，檔案的內容應該如下：

    \site\wwwroot\Logs
    \LogFiles
    \site\wwwroot\Images\2013
    \site\wwwroot\Images\2014
    \site\wwwroot\Images\brand.png

3. 上傳此檔案來 `D:\home\site\wwwroot\` 目錄的站台使用 [ftp](web-sites-deploy.md#ftp) 或任何其他方法。 如果您希望，您可以建立檔案直接在 `http://{yourapp}.scm.azurewebsites.net/DebugConsole` 和插入內容。

4. 執行備份相同的方式，通常會進行， [手動](#create-a-manual-backup) 或 [自動](#configure-automated-backups)。

現在，任何的檔案和資料夾中所指定 `_backup.filter` 從備份中排除。 在此範例中，將不再備份記錄檔和 2013年和 2014 年影像檔，以及 brand.png。
>[AZURE.NOTE] 相同的方式還原您網站的部分備份 [還原定期備份](web-sites-restore.md)。 還原程序將執行正確事項。
>
>還原完整備份時，網站上的所有內容都會取代為備份中的內容。 如果檔案在網站上，但不在備份裡，即會遭到刪除。 但是，還原部分備份時，位於其中一個黑名單目錄或任何黑名單檔案中的任何內容都會保持原狀。

<a name="aboutbackups"></a>

## 備份的儲存方式

建立 Web 應用程式的一或多個備份之後，這些備份就會顯示在您儲存體帳戶以及 Web 應用程式的 [容器]**** 刀鋒視窗中。 在儲存體帳戶中，每個備份都會有一個包含備份資料的 .zip 檔案，以及一個包含 .zip 檔案內容資訊清單的 .xml 檔案。 如果您要存取備份而不實際執行 Web 應用程式還原，則可以將這些檔案解壓縮並加以瀏覽。

Web 應用程式的資料庫備份則儲存在 .zip 檔案的根目錄中。 若是 SQL 資料庫，這會是 BACPAC 檔案 (無副檔名)，而且可以匯入。 若要建立新的 SQL 資料庫，根據 BACPAC 匯出，請參閱 [匯入 BACPAC 檔案以建立新的使用者資料庫](http://technet.microsoft.com/library/hh710052.aspx)。
> [AZURE.WARNING] 對 **websitebackups** 容器中的檔案進行任何變更，都可能導致備份失效，進而無法還原。

<a name="nextsteps"></a>
## 後續步驟

如需從備份還原 web 應用程式的詳細資訊，請參閱 [還原 web 應用程式在 Azure App Service 中的](web-sites-restore.md)。

若要開始使用 Azure，請參閱 [Microsoft Azure 免費試用](/pricing/free-trial/)。

## 變更的項目

* 如需變更從應用程式服務的網站的指南，請參閱: [Azure App Service，及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)



[choosebackupspage]: ./media/web-sites-backup/01ChooseBackupsPage.png 
[choosestorageaccount]: ./media/web-sites-backup/02ChooseStorageAccount.png 
[includeddatabases]: ./media/web-sites-backup/03IncludedDatabases.png 
[backupnow]: ./media/web-sites-backup/04BackUpNow.png 
[backupprogress]: ./media/web-sites-backup/05BackupProgress.png 
[setautomatedbackupon]: ./media/web-sites-backup/06SetAutomatedBackupOn.png 
[frequency]: ./media/web-sites-backup/07Frequency.png 
[startdate]: ./media/web-sites-backup/08StartDate.png 
[starttime]: ./media/web-sites-backup/09StartTime.png 
[saveicon]: ./media/web-sites-backup/10SaveIcon.png 
[imagesfolder]: ./media/web-sites-backup/11Images.png 
[logsfolder]: ./media/web-sites-backup/12Logs.png 
[ghostupgradewarning]: ./media/web-sites-backup/13GhostUpgradeWarning.png 

