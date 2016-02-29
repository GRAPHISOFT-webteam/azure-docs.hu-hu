<properties
   pageTitle="Azure 備份 - 離線備份或使用 Azure 匯入/匯出服務進行初始植入 | Microsoft Azure"
   description="了解 Azure 備份如何讓您使用 Azure 匯入/匯出服務在網路上傳送資料。 此文章說明如何使用 Azure 匯入/匯出服務離線植入初始備份資料"
   services="backup"
   documentationCenter=""
   authors="aashishr"
   manager="shreeshd"
   editor=""/>
<tags
   ms.service="backup 」
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload= 「 儲存體的備份復原的 」
   ms.date="11/25/2015 」
   ms.author="aashishr 」。「 jimpark 」 / >

# 在 Azure 備份中離線備份工作流程

Azure 備份與 Azure 匯入/匯出服務密切整合，可讓您快速傳輸初始備份資料。 如果您的初始備份資料大小達到 TB，且需要透過高延遲和低頻寬網路傳輸時，您可以使用 Azure 匯入/匯出服務將初始備份複本送至 Azure 資料中心的一個或多個硬碟上。 本文章提供完成此工作流程所需步驟的概觀。

## 概觀

您可以使用 Azure 備份和 Azure 匯入/匯出，透過磁碟簡單且直接地將資料離線上傳至 Azure。 不需透過網路傳輸初始完整複本，備份資料寫入至 *預備位置*。 預備位置可以是直接連接的儲存體或網路共用。 完成初始複本時，使用 *Azure 匯入/匯出工具*, ，此資料會寫入最後送至 Azure 資料中心的 SATA 磁碟機。 根據初始備份的大小，可能需要一個或多個 SATA 磁碟機才能完成此作業。 Azure 匯入/匯出工具帳戶適用於三種案例。 將備份寫入至磁碟後，您可以將其送至最近的資料中心位置，以便上傳至 Azure。 接著 Azure 備份會將備份資料複製至備份保存庫，並排程增量備份。

## 先決條件

1. 請務必熟悉 Azure 匯入匯出工作流程會列出 [這裡](../storage-import-export-service.md)。
2. 起始工作流程之前，請確定已建立 Azure 備份保存庫、已下載保存庫認證、Azure 備份代理程式已安裝於您的 Windows Server/Windows 用戶端或 System Center Data Protection Manager (SCDPM) 伺服器，且電腦已註冊 Azure 備份保存庫。
3. 下載 Azure 發佈檔案設定，從 [這裡](https://manage.windowsazure.com/publishsettings) 您打算備份我們資料的電腦上。
4. 準備 *預備位置* 可能在網路共用或電腦上的其他磁碟機。 請確定預備位置有足夠的磁碟空間來保存您的初始複本。 例如：若您正在嘗試備份 500 GB 的檔案伺服器，請確定預備區域至少有 500 GB 的空間 (雖然使用量可能較少)。 預備區域是「暫時性儲存體」，因此在此工作流程期間會暫時使用。
5. 外部 SATA 磁碟機寫入器和外部的 3.5 英吋的 SATA 磁碟機。 只有 3.5 英吋的 SATA II/III 硬碟能夠用於匯入/匯出服務。 不支援大於 6 TB 的硬碟。 您可以將 SATA II/III 磁碟附加至大多數使用 SATA II/III USB 介面卡的外部電腦。 檢查 Azure 匯入/匯出文件以取得服務所支援的最新磁碟機組合。
6. 在 SATA 磁碟機寫入器所連接的電腦上啟用 BitLocker。
7. 下載 Azure 匯入/匯出工具，從 [這裡](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) SATA 磁碟機寫入器所連接的電腦。

## 工作流程
本章節提供的資訊是完成 **離線備份** 工作流程，以便您的資料可以傳遞至 Azure 資料中心，並上傳至 Azure 儲存體。 如果您有關於匯入服務或處理程序任何層面的問題，請參閱 [匯入服務概觀](../storage-import-export-service.md) 上述文件。

### 初始離線備份

1. 作為排程備份的一部分，您將會遇到下列畫面 (在 Windows Server、Windows 用戶端或 SCDPM 中)。

    ![匯入畫面](./media/backup-azure-backup-import-export/importscreen.png)

    Scdpm 中的畫面看起來如下。 <br/>
    ![DPM 匯入畫面](./media/backup-azure-backup-import-export/dpmoffline.png)

    其中：

    - **預備位置** -這是指初始備份複本寫入暫存位置。 這可能是網路共用或在本機電腦上。
    - **Azure 匯入工作名稱** -作為完成此工作流程的一部分，您必須建立 *匯入工作* Azure 入口網站 (文件的後續部分所涵蓋)。 提供您打算在 Azure 入口網站中稍後使用的輸入。
    - **Azure 發佈設定** -這是一個 XML 檔案包含您的訂用帳戶設定檔的相關資訊。 也包含與訂用帳戶相關聯的安全認證。 您可以從下載的檔案 [這裡](https://manage.windowsazure.com/publishsettings)。 提供發佈設定檔案的本機路徑。
    - **Azure 訂用帳戶 ID** -提供您打算初始化 Azure 匯入工作的 Azure 訂用帳戶識別碼。 如果您有多個 Azure 訂用帳戶，請使用與匯入工作相關聯的識別碼。
    - **Azure 儲存體帳戶** -輸入要與此匯入工作相關聯的 Azure 儲存體帳戶的名稱。
    - **Azure 儲存體容器** -輸入將匯入此工作資料的目的地儲存體 blob 的名稱。

2. 完成工作流程，並選取 **立即備份** 在 Azure 備份 mmc 中以初始化離線備份複本。 作為此步驟的一部分，初始備份會寫入預備區域。

    ![立即備份](./media/backup-azure-backup-import-export/backupnow.png)

    SCDPM 中的對應工作流程上的 [啟用 **保護群組** ，然後選擇 **建立復原點** 選項。 接著選擇 **線上保護** 選項。

    ![立即備份 DPM](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    當作業完成時， *。AIBBlob* 和 *。BaseBlob* 預備位置中建立檔案。

    ![輸出](./media/backup-azure-backup-import-export/opbackupnow.png)

### 準備 SATA 磁碟機

1. 下載 [Microsoft Azure 匯入/匯出工具](http://go.microsoft.com/fwlink/?linkid=301900&clcid=0x409) 至 *複製電腦*。 請確定預備位置 (在上一個步驟中) 可從您打算執行下一組命令的電腦進行存取。 必要時，複本電腦可以與來源電腦相同。

2. 解壓縮 *WAImportExport.zip* 檔案。 執行 *WAImportExport* 工具，可將格式 SATA 磁碟機、 將備份資料寫入 SATA 磁碟機，並予以加密。 執行下列命令之前請確定已在電腦上啟用 BitLocker。 <br/>

    *做為 PrepImport /j:.\WAImportExport.exe <*JournalFile*>.jrn /id: <*SessionId*> /sk: <*StorageAccountKey*> /BlobType:**PageBlob** /t: <*TargetDriveLetter*>/格式化 / 加密 /srcdir: <*預備位置*> /dstdir: <*DestinationBlobVirtualDirectory*> / *


| 參數 | 說明
|-------------|-------------|
| /j: <*JournalFile*>| 日誌檔案的路徑。 每個磁碟機必須只有一個日誌檔案。 請注意，日誌檔案不得位於目標磁碟機上。 日誌檔案的副檔名是 .jrn，且作為執行此命令的一部分而建立。|
|/id: <*SessionId*> | 工作階段識別碼會識別 *複製工作階段*。 其用來確保正確復原中斷的複製工作階段。 在複製工作階段中複製的檔案會儲存在以目標磁碟機上工作階段識別碼命名的目錄。|
| /sk: <*StorageAccountKey*> | 資料將要匯入儲存體帳戶的帳戶金鑰。 |
| /BlobType | 指定 **PageBlob**, ，此工作流程在指定 PageBlob 選項時，才會成功。 這不是預設選項，且應該在此命令中進行描述。 |
|/t: <*TargetDriveLetter*> | 目前複製工作階段中目標硬碟的磁碟機代號，不包含結尾的冒號。|
|/format | 當磁碟機需要進行格式化時請指定此參數；否則請省略。 此工具格式化磁碟機之前，會提示您從主控台進行確認。 若要隱藏確認，請指定 /silentmode 參數。|
|/encrypt | 當磁碟機尚未使用 BitLocker 加密，且需要透過工具加密時，請指定此參數。 若已使用 BitLocker 加密磁碟機，則省略此參數並指定 /bk 參數，以提供現有的 BitLocker 金鑰。 若您指定 /format 參數，則您也必須指定 /encrypt 參數。 |
|/srcdir: <*SourceDirectory*> | 包含檔案的來源目錄會複製至目標磁碟機。 目錄路徑必須是絕對路徑 (而非相對路徑)。|
|/dstdir: <*DestinationBlobVirtualDirectory*> | Microsoft Azure 儲存體帳戶中的目的地虛擬目錄路徑。 指定目的地虛擬目錄或 blob 時，請確定使用有效的容器名稱。 請記住容器名稱必須是小寫。|

  > [AZURE.NOTE] 擷取工作流程的整個資訊的 WAImportExport 資料夾中建立日誌檔案。 在 Azure 入口網站中建立匯入工作時，您將需要此檔案。

  ![PowerShell 輸出](./media/backup-azure-backup-import-export/psoutput.png)

### 在 Azure 入口網站中建立匯入工作
1. 瀏覽至您的儲存體帳戶中 [管理入口網站](https://manage.windowsazure.com/), ，然後按一下 [ **匯入/匯出** ，然後在 **建立匯入工作** 的工作窗格中。

    ![入口網站](./media/backup-azure-backup-import-export/azureportal.png)

2. 在精靈的步驟 1，指出您已備妥磁碟機並有可用的磁碟機日誌。 在精靈的步驟 2 中，提供負責處理此匯入工作的人員連絡資訊。
3. 在步驟 3 中，上傳在上一個章節中您所取得的磁碟機日誌檔案。
4. 在步驟 4 中，輸入匯入工作的描述性名稱。 請注意，您輸入的名稱只能包含小寫字母、數字、連字號和底線，必須以字母開頭，且不得包含空格。 當工作正在進行中和一旦完成後，您將使用您所選的名稱來進行追蹤。
5. 接著，從清單中選取資料中心區域。 資料中心區域將會指出您的包裹必須送達的資料中心和地址。

    ![DC](./media/backup-azure-backup-import-export/dc.png)

6. 在步驟 5 中，從清單中選取您的寄回貨運公司，並輸入貨運公司客戶編號。 當匯入工作完成時，Microsoft 會透過此廠商將磁碟機寄還給您。

7. 運送磁碟，並輸入追蹤號碼以便追蹤運送狀態。 一旦磁碟到達資料中心，其會複製到儲存體帳戶並更新狀態。

    ![完成狀態](./media/backup-azure-backup-import-export/complete.png)

### 完成工作流程
一旦初始備份資料可供您的儲存體帳戶使用之後，Azure 備份代理程式會將此帳戶的資料內容複製至多個承租的備份儲存體帳戶中。 在下一步的排程備份時間，Azure 備份代理程式會透過初始備份複本執行增量備份。

## 後續步驟
- Azure 匯入/匯出工作流程上的任何問題，請參閱此 [文章](../storage-import-export-service.md)。
- 離線備份 > 章節的 Azure 備份，請參閱 [常見問題集](backup-azure-backup-faq.md) 的任何工作流程的相關問題

