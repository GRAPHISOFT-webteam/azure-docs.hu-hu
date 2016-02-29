<properties 
   pageTitle="管理您的 StorSimple 備份類別目錄 | Microsoft Azure"
   description="說明如何使用 StorSimple Manager 服務的 [備份類別目錄] 頁面列出、選取和刪除磁碟區的備份組。"
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="09/14/2015"
   ms.author="v-sharos" />

# 使用 StorSimple Manager 服務管理備份類別目錄

## 概觀

StorSimple Manager 服務 **備份類別目錄** 頁面會顯示產生手動或自動備份時所建立的所有備份組。 您可以使用此頁面來列出備份原則或磁碟區的所有備份、選取或刪除備份，或是使用備份來還原或複製磁碟區。

本教學課程說明如何列出、選取和刪除備份組。 若要了解如何從備份還原您的裝置，請前往 [從備份組還原裝置](storsimple-restore-from-backup-set.md)。 若要了解如何複製磁碟區，請前往 [複製 StorSimple 磁碟區](storsimple-clone-volume.md)。

![備份類別目錄](./media/storsimple-manage-backup-catalog/HCS_BackupCatalog.png) 

 **備份類別目錄** 頁面提供查詢，以縮小備份組選取範圍。 您可以篩選根據下列參數擷取的備份組：

- **裝置** – 建立備份組所在的裝置。

- **備份原則或磁碟區** – 備份原則或與此備份組相關聯的磁碟區。

- **From 和 To** – 建立備份組的日期和時間範圍。

接著會根據下列屬性，將篩選的備份組列表顯示：

- **名稱** – 備份原則或備份組相關聯的磁碟區的名稱。

- **大小** – 備份組的實際大小。

- **建立** 上 – 建立備份時的日期和時間。 

- **型別** – 備份組可以是本機快照或雲端快照。 本機快照是本機儲存於裝置上的所有磁碟區資料備份，而雲端快照是指位於雲端的磁碟區資料備份。 本機快照可提供更快速的存取，而雲端快照是選擇來進行資料復原。

- **起始者** – 由排程或由使用者手動備份也可以自動初始化。 您可以使用備份原則排程備份。 或者，您可以使用 **進行備份** 選項製作互動式備份。

## 列出磁碟區的備份組
 
完成下列步驟，以列出磁碟區的所有備份。

#### 若要列出備份組

1. 在 StorSimple Manager 服務頁面上，按一下 [ **備份類別目錄** ] 索引標籤。

2. 如下方所示，篩選選取項目：

    1. 選取適當的裝置。

    2. 在下拉式清單中，選擇要檢視對應備份的磁碟區。

    3. 指定時間範圍。

    4. 按一下核取圖示 ![核取圖示](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) 若要執行此查詢。
 
    與選取的磁碟區相關的備份應會顯示在備份組清單中。

## 選取備份組

完成下列步驟，選取磁碟區的備份組或備份原則。

#### 若要選取備份組

1. 在 StorSimple Manager 服務頁面上，按一下 [ **備份類別目錄** ] 索引標籤。

2. 如下方所示，篩選選取項目：

    1. 選取適當的裝置。

    2. 在下拉式清單中，針對要選取的備份選擇磁碟區或備份原則。

    3. 指定時間範圍。

    4. 按一下核取圖示 ![核取圖示](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) 若要執行此查詢。

    與選取的磁碟區或備份原則相關聯的備份應該會出現在備份組清單中。

3. 選取並展開備份組。  **還原，並刪除** 選項會顯示在頁面底部。 您可以在選取的備份組上執行以上任一動作。

## 刪除備份組

不再需要保留與備份相關的資料時，請將備份刪除。 執行下列步驟以刪除備份組。

#### 若要刪除備份組

1. 在 StorSimple Manager 服務頁面上，按一下 [ **備份類別目錄] 索引標籤**。

2. 如下方所示，篩選選取項目：

    1. 選取適當的裝置。

    2. 在下拉式清單中，針對要選取的備份選擇磁碟區或備份原則。

    3. 指定時間範圍。

    4. 按一下核取圖示 ![核取圖示](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) 若要執行此查詢。

    與選取的磁碟區或備份原則相關聯的備份應該會出現在備份組清單中。

3. 選取並展開備份組。  **還原，並刪除** 選項會顯示在頁面底部。 按一下 [ **刪除**。

4. 刪除進行中和順利完成時，您會收到通知。 刪除完成之後，重新整理此頁面上的查詢。 已刪除的備份組將不會再顯示於備份組清單中。

## 後續步驟

- 了解如何 [使用備份類別目錄將裝置還原備份集](storsimple-restore-from-backup-set.md)。

- 了解如何 [使用 StorSimple Manager 服務來管理 StorSimple 裝置](storsimple-manager-service-administration.md)。
