<properties 
   pageTitle="何謂 StorSimple Snapshot Manager？| Microsoft Azure"
   description="描述 StorSimple Snapshot Manager、其架構及其功能。"
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
   ms.date="10/12/2015"
   ms.author="v-sharos" />


# 什麼是 StorSimple Snapshot Manager？

## 概觀

StorSimple Snapshot Manager 是 Microsoft Management Console (MMC) 嵌入式管理單元，可簡化資料保護和備份管理 Microsoft Azure StorSimple 環境中。 使用 StorSimple Snapshot Manager 時，您可以將資料中心和雲端中的 Microsoft Azure StorSimple 資料當作單一整合式儲存體解決方案來管理，因而簡化備份程序並降低成本。

本概觀簡介 StorSimple Snapshot Manager、描述其功能，並說明其在 Microsoft Azure StorSimple 的角色。

如需整個 Microsoft Azure StorSimple 系統，包括 StorSimple 裝置、 StorSimple Manager 服務、 StorSimple Snapshot Manager，以及 StorSimple Adapter for SharePoint 的概觀，請參閱 [StorSimple 8000 系列: 混合式雲端儲存體解決方案](storsimple-overview.md)。

## StorSimple Snapshot Manager 用途和架構

StorSimple Snapshot Manager 提供中央管理主控台，可用來建立本機和雲端資料的一致時間點備份複本。 例如，您可以使用主控台：

- 設定、備份及刪除磁碟區。
- 設定磁碟區群組，以確保是應用程式一致的備份資料。
- 管理備份原則，以便在預先決定的排程上備份資料。
- 建立可以儲存在雲端，並用於災害復原的資料複本。

使用 StorSimple Snapshot Manager 時，您可以掛接磁碟區，然後將其設定為磁碟區群組，通常依應用程式區分。 StorSimple Snapshot Manager 會使用這些磁碟區群組，來產生應用程式一致的備份副本。 (當所有相關的檔案和資料庫已同步處理，並代表應用程式在特定時間點的真實狀態時，即存在應用程式一致性。)

StorSimple Snapshot Manager 備份採用增量快照的形式，即僅擷取自從上次備份之後的變更。 因此，備份所需的儲存體更少並可以快速建立和還原。 StorSimple Snapshot Manager 會使用 Windows 磁碟區陰影複製服務 (VSS)，來確保快照擷取應用程式一致的資料。 (如需詳細資訊，請移至〈與 Windows 磁碟區陰影複製服務整合〉一節。) 使用 StorSimple Snapshot Manager 時，您可以建立備份排程或視需要進行立即備份。 如果您需要從備份還原資料，StorSimple Snapshot Manager 可讓您從本機或雲端快照的目錄中進行選取。 Azure StorSimple 只會在需要時還原所需資料，這可防止在還原作業期間延遲資料可用性。)

![StorSimple Snapshot Manager 架構](./media/storsimple-what-is-snapshot-manager/HCS_SSM_Overview.png)

**圖 1：StorSimple Snapshot Manager 架構**

## 支援多個磁碟區類型

您可以使用 StorSimple Snapshot Manager，來設定和備份下列類型的磁碟區：

- **基本磁碟區** – 基本磁碟區是基本磁碟上的單一磁碟分割。

- **簡單磁碟區** – 簡單磁碟區是動態磁碟區，其中包含來自單一動態磁碟的磁碟空間。 簡單磁碟區由磁碟上的單一區域或同一個磁碟上連結在一起的多個區域所組成。 (簡單磁碟區只能在動態磁碟上建立。) 簡單磁碟區不具有容錯功能。

- **動態磁碟區** – 動態磁碟區是動態磁碟上建立的磁碟區。 動態磁碟會使用資料庫，來追蹤在電腦中動態磁碟的磁碟區相關資訊。

- **含鏡像的動態磁碟區** – 含鏡像的動態磁碟區是根據 RAID 1 架構建置的磁碟區。 使用 RAID 1 時，相同的資料會寫入兩個或多個磁碟，因而產生鏡像集。 然後，任何包含所要求資料的磁碟都可以處理讀取要求。

- **叢集共用磁碟區** – 使用叢集共用磁碟區 (CSV) 時，容錯移轉叢集中的多個節點可以同時讀取或寫入至相同磁碟。 可以快速地從一個節點容錯移轉到另一個節點，而不需要變更磁碟機擁有權，或掛接、卸載和移除磁碟區。

>[AZURE.IMPORTANT] 請勿在相同的快照中混用 CSV 和非 CSV。 不支援在快照中混用 CSV 和非 CSV。 

您可以使用 StorSimple Snapshot Manager，來還原整個磁碟區群組，或複製個別磁碟區及復原個別檔案。

- [磁碟區和磁碟區群組](#volumes-and-volume-groups)
- [備份類型和備份原則](#backup-types-and-backup-policies)

如需 StorSimple Snapshot Manager 功能，以及如何使用它們的詳細資訊，請參閱 [StorSimple Snapshot Manager 使用者介面](storsimple-use-snapshot-manager.md)。

## 磁碟區和磁碟區群組

使用 StorSimple Snapshot Manager 時，您可以建立磁碟區，然後將其設定為磁碟區群組。

StorSimple Snapshot Manager 會使用磁碟區群組，來建立應用程式一致的備份副本。 當所有相關的檔案和資料庫已同步處理，並代表應用程式在特定時間點的真實狀態時，即存在應用程式一致性。 磁碟區群組 (也稱為*一致性群組*) 為構成備份或還原作業的基礎。

磁碟區群組與磁碟區容器不同。 磁碟區容器包含一個或多個共用雲端儲存體帳戶和其他屬性 (例如加密和頻寬耗用量) 的磁碟區。 單一磁碟區容器可以包含最多 256 個精簡佈建型 StorSimple 磁碟區。 如需有關磁碟區容器的詳細資訊，請移至 [管理磁碟區容器](storsimple-manage-volume-containers.md)。 磁碟區群組是您為了協助備份作業而設定的磁碟區集合。 如果您選取兩個屬於不同磁碟區容器的磁碟區，並置於單一磁碟區群組中，然後建立該磁碟區群組的備份原則，則系統會使用適當的儲存體帳戶，將每個磁碟區備份在適當的磁碟區容器中。
>[AZURE.NOTE] 磁碟區群組中的所有磁碟區必須來自單一雲端服務提供者。

## 與 Windows 磁碟區陰影複製服務整合

StorSimple Snapshot Manager 會使用 Windows 磁碟區陰影複製服務 (VSS)，來擷取應用程式一致的資料。 VSS 可藉由與 VSS 感知應用程式通訊，來協調建立增量快照，以促進應用程式一致性。 VSS 確保取得快照時應用程式會暫時停用或靜止。

VSS 的 StorSimple Snapshot Manager 實作會使用 SQL Server 和一般 NTFS 磁碟區。 程序如下：

1. 要求者通常是資料管理和保護解決方案 (例如 StorSimple Snapshot Manager) 或備份應用程式，其會叫用 VSS，並要求從目標應用程式中的寫入器軟體收集資訊。

2. VSS 會連絡寫入器元件來擷取資料的說明。 寫入器會傳回要備份之資料的說明。

3. VSS 會指示寫入器準備應用程式進行備份。 寫入器會完成開啟的交易、更新交易記錄檔等等來準備資料進行備份，然後通知 VSS。

4. VSS 會指示寫入器暫時停止應用程式的資料存放區，並確定在建立陰影複製時沒有資料寫入至磁碟區。 這個步驟可確保資料一致性，而且所需時間不超過 60 秒。

5. VSS 會指示提供者建立陰影複製。 以軟體或硬體為基礎的提供者可管理目前執行中的磁碟區，並依需求建立陰影複製。 提供者會建立陰影複製，並在完成時通知 VSS。

6. VSS 會連絡寫入器以通知應用程式可繼續 I/O，同時也可確認在建立陰影複製期間已順利暫停 I/O。

7. 如果複製成功，VSS 會將複本的位置傳回給要求者。

8. 如果在建立陰影複製時寫入資料，則備份會不一致。 VSS 會刪除陰影複製，並通知要求者。 要求者可以自動重複備份程序，或通知系統管理員稍後再重試。

請參閱下圖。

![VSS 程序](./media/storsimple-what-is-snapshot-manager/HCS_SSM_VSS_process.png)

**圖 2：Windows 磁碟區陰影複製服務程序**

## 備份類型和備份原則

使用 StorSimple Snapshot Manager 時，您可以備份資料，並將其儲存在本機和雲端中。 您可以使用 StorSimple Snapshot Manager 來立即備份資料，或可以使用備份原則，建立排程自動進行備份。 備份原則也可讓您指定將保留多少個快照。

### 備份類型

您可以使用 StorSimple Snapshot Manager，建立下列類型的備份：

- **本機快照** – 本機快照是儲存在 StorSimple 裝置之磁碟區資料的時間點副本。 一般而言，可以快速建立和還原這種類型的備份。 您可以如同使用本機備份複本一般使用本機快照。

- **雲端快照** – 雲端快照是儲存在雲端之磁碟區資料的時間點複本。 雲端快照相當於在不同的異地儲存體系統上複寫的快照。 雲端快照在災害復原案例中特別有用。

### 依需求和排程的備份

使用 StorSimple Snapshot Manager 時，您可以起始要立即建立的一次性備份，或可以使用備份原則來排程週期性備份作業。

備份原則是一組自動化規則，您可以用來排程一般備份。 備份原則可讓您定義頻率和參數，用於取得特定磁碟區群組的快照。 您可以使用原則，同時對本機和雲端快照指定開始和到期日期、時間、頻率，以及保留需求。 在定義原則之後會立即套用。

必要時，您可以使用 StorSimple Snapshot Manager，設定或重新設定備份原則。

您可以對每個您建立的備份原則設定下列資訊：

- **名稱** – 所選備份原則的唯一名稱。

- **類型** – 備份原則的類型，可以是本機快照或雲端快照。

- **磁碟區群組** – 所選備份原則被指派到的磁碟區群組。

- **保留** – 要保留的備份副本數目。 如果核取 [全部]**** 方塊，則會保留所有的備份副本，直到超出每個磁碟區的備份副本數目上限，此時原則將失敗，並產生錯誤訊息。 或者，您可以指定要保留的備份數 (介於 1 到 64 之間)。

- **日期** – 備份原則的建立日期。

如需設定備份原則的資訊，請移至 [使用 StorSimple Snapshot Manager 來建立和管理備份原則](storsimple-snapshot-manager-manage-backup-policies.md)。

### 監視和管理備份工作

您可以使用 StorSimple Snapshot Manager，監視和管理即將開始、已排程和已完成的備份作業。 此外，StorSimple Snapshot Manager 提供最多 64 個已完成備份的目錄。 您可以使用目錄來尋找及還原磁碟區或個別檔案。

如需監視備份工作的相關資訊，請移至 [使用 StorSimple Snapshot Manager 來檢視和管理備份作業](storsimple-snapshot-manager-manage-backup-jobs.md)。


## 後續步驟

- 深入了解 [使用 StorSimple Snapshot Manager 來管理您的 StorSimple 解決方案](storsimple-snapshot-manager-admin.md)。

- 下載 [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220)。




