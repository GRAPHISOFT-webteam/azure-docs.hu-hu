<properties 
   pageTitle="StorSimple Snapshot Manager 及磁碟區 | Microsoft Azure"
   description="描述如何使用 StorSimple Snapshot Manager MMC 嵌入式管理單元來檢視和管理磁碟區及設定備份。"
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
   ms.date="12/02/2015"
   ms.author="v-sharos" />

# 使用 StorSimple Snapshot Manager 來檢視和管理磁碟區

## 概觀

您可以使用 StorSimple Snapshot Manager **磁碟區** 節點 (在 **範圍** 窗格) 選取的磁碟區，並檢視其相關資訊。 磁碟區會呈現為對應至主機所掛接磁碟區的磁碟機。  **磁碟區** 節點會顯示本機磁碟區和磁碟區類型所支援的 Azure StorSimple，包括透過使用 iSCSI 及裝置探索到的磁碟區。 

如需有關支援的磁碟區的詳細資訊，請移至 [支援多個磁碟區類型](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types)。

![在 [結果] 窗格中的磁碟區清單](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

 **磁碟區** 節點也可讓您重新掃描或刪除後 StorSimple Snapshot Manager 探索到的磁碟區。 

本教學課程說明如何掛載、初始化和格式化磁碟區，然後使用 StorSimple Snapshot Manager：

- 檢視磁碟區的相關資訊 
- 刪除磁碟區
- 重新掃描磁碟區 
- 設定基本磁碟區並將其備份
- 設定動態鏡像磁碟區並將其備份

>[AZURE.NOTE] 所有 **磁碟區** 節點動作也會提供 **動作** 窗格。
 
## 掛接磁碟區

請使用下列程序，來掛接、初始化和格式化 Azure StorSimple 磁碟區。 此程序使用 [磁碟管理]，這是一種系統公用程式，用於管理硬碟及其包含的磁碟區或磁碟分割。 如需磁碟管理的詳細資訊，請移至 [磁碟管理](https://technet.microsoft.com/library/cc770943.aspx) Microsoft TechNet 網站上。

#### 若要掛接磁碟區

1. 在您的主機電腦上，啟動 Microsoft iSCSI 啟動器。

2. 提供其中一個介面 IP 位址作為目標入口網站，或探索 IP 位址，然後連接至裝置。 在連接裝置之後，您的 Windows 系統將可存取磁碟區。 如需有關使用 Microsoft iSCSI 啟動器的詳細資訊，請移至一節 「 連接至 iSCSI 目標裝置 」 在 [安裝和設定 Microsoft iSCSI 啟動器] [1]。

3. 請使用下列任一個選項來啟動 [磁碟管理]：

    - 輸入在 Diskmgmt.msc **執行** 方塊。

    - 啟動 [伺服器管理員中，展開 **儲存體** 節點，然後再選取 **磁碟管理**。

    - 啟動 **系統管理工具**, ，依序展開 **電腦管理** 節點，然後再選取 **磁碟管理**。 

    >[AZURE.NOTE] 您必須使用系統管理員權限來執行磁碟管理。
 
4. 將磁碟區連線：

   1. 在 [磁碟管理，以滑鼠右鍵按一下任何磁碟區標示為 **離線**。

   2. 按一下 [ **重新啟動磁碟**。 磁碟應標示為 **線上** 之後重新啟動磁碟。

5. 初始化磁碟區：

   1. 以滑鼠右鍵按一下已探索到的磁碟區。

   2. 在功能表上，選取 **初始化磁碟**。

   3. 在 **初始化磁碟** ] 對話方塊中選取您要初始化的磁碟，然後按一下 [ **確定**。

6. 格式化簡單磁碟區：

   1. 以滑鼠右鍵按一下您想要格式化的磁碟區。

   2. 在功能表上，選取 **新增簡單磁碟區**。

   3. 使用 [新增簡單磁碟區] 精靈來格式化磁碟區：

      - 指定磁碟區大小。
      - 提供磁碟機代號。
      - 選取 NTFS 檔案系統。
      - 指定 64 KB 的配置單位大小。
      - 執行快速格式化。

7. 格式化多重磁碟分割磁碟區。 如需指示，請移至 〈 磁碟分割與磁碟區 」 一節 [實作磁碟管理](https://msdn.microsoft.com/library/dd163556.aspx)。

## 檢視您磁碟區的相關資訊

請使用下列程序，來檢視本機和 Azure StorSimple 磁碟區的相關資訊。

#### 若要檢視磁碟區資訊

1. 按一下桌面圖示，以啟動 StorSimple Snapshot Manager。 

2. 在 **範圍** ] 窗格中，按一下 [ **磁碟區** 節點。 本機與掛接磁碟區，包括所有的 Azure StorSimple 磁碟區清單會出現在 **結果** 窗格。 中的資料行 **結果** 窗格中進行設定。 (以滑鼠右鍵按一下 **磁碟區** 節點中，選取 **檢視**, ，然後選取 **新增/移除資料行**。)

    ![設定資料行](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)

    結果資料行 | 說明 
    :--------------|:-------------
    名稱           |  **名稱** 資料行包含指派給每個探索到的磁碟區的磁碟機代號。
    裝置         |  **裝置** 資料行包含裝置要連線到主機電腦的 IP 位址。
    裝置磁碟區名稱 |  **裝置磁碟區名稱** 資料行包含選取的磁碟區所屬之裝置磁碟區名稱。 這是 Azure 傳統入口網站中針對該特定磁碟區定義的磁碟區名稱。
    存取路徑   |  **存取路徑** 資料行顯示磁碟區的存取路徑。 這是可在主機電腦上存取磁碟區的磁碟機代號或掛接點。
 
## 刪除磁碟區

請使用下列程序，從 StorSimple Snapshot Manager 中刪除磁碟區。

>[AZURE.NOTE] 如果它是任何磁碟區群組的一部分，您無法刪除磁碟區。 (磁碟區若是磁碟區群組的成員，無法對其使用刪除選項。)您必須刪除整個磁碟區群組，才能刪除磁碟區。


#### 若要刪除磁碟區

1. 按一下桌面圖示，以啟動 StorSimple Snapshot Manager。

2. 在 **範圍** ] 窗格中，按一下 [ **磁碟區** 節點。 

3. 在 **結果** ] 窗格中，以滑鼠右鍵按一下您想要刪除的磁碟區。

4. 在功能表上，按一下 [ **刪除**。 

    ![刪除磁碟區](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 

5.  **刪除磁碟區** ] 對話方塊隨即出現。 型別 **確認** 中] 文字方塊中，然後按一下 **確定**。

6. 根據預設，StorSimple Snapshot Manager 會先備份磁碟區，再將其刪除。 此預防措施可讓您在意外刪除時避免資料損失。 StorSimple Snapshot Manager 會顯示 **自動快照** 備份磁碟區時的進度訊息。 

    ![自動快照集訊息](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## 重新掃描磁碟區

請使用下列程序，來重新掃描已連接至 StorSimple Snapshot Manager 的磁碟區。

#### 若要重新掃描磁碟區

1. 按一下桌面圖示，以啟動 StorSimple Snapshot Manager。

2. 在 **範圍** ] 窗格中，以滑鼠右鍵按一下 **磁碟區**, ，然後按一下 [ **重新掃描磁碟區**。

    ![重新掃描磁碟區](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
 
    此程序會同步處理磁碟區清單與 StorSimple Snapshot Manager。 任何變更 (例如新增的磁碟區或已刪除的磁碟區) 將會反映在結果中。

## 設定和備份基本磁碟區

請使用下列程序，來設定基本磁碟區的備份，然後立即啟動備份，或建立一個原則進行排程的備份。

### 必要條件

開始之前：

- 確定已正確設定 StorSimple 裝置和主機電腦。 如需詳細資訊，請移至 [部署在內部部署 StorSimple 裝置](storsimple-deployment-walkthrough.md)。

- 安裝和設定 StorSimple Snapshot Manager。 如需詳細資訊，請移至 [部署 StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md)。

#### 若要設定基本磁碟區的備份

1. 在 StorSimple 裝置上建立基本磁碟區。

2. 掛接、 初始化及格式化磁碟區中所述 [掛接磁碟區](#mount-volumes)。 

3. 按一下桌面上的 StorSimple Snapshot Manager 圖示。 隨即會出現 [StorSimple Snapshot Manager] 視窗。 

4. 在 **範圍** ] 窗格中，以滑鼠右鍵按一下 **磁碟區** 節點，然後再選取 **重新掃描磁碟區**。 掃描完成時，磁碟區清單應該會出現在 **結果** 窗格。 

5. 在 **結果** ] 窗格中，以滑鼠右鍵按一下磁碟區，然後 **建立磁碟區群組**。 

    ![建立磁碟區群組](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 

6. 在 **建立磁碟區群組** 對話方塊中，輸入磁碟區群組的名稱，指派給它，磁碟區，然後按一下 **確定**。

7. 在 **範圍** ] 窗格中，展開 **磁碟區群組** 節點。 新的磁碟區群組應該會出現在 **磁碟區群組** 節點。 

8. 以滑鼠右鍵按一下磁碟區群組名稱。

    - 若要啟動互動式 (隨) 備份作業，請按一下 [ **進行備份**。 

    - 若要排程自動備份，請按一下 [ **建立備份原則**。 在 **一般** 頁面上，從清單中選取磁碟區群組。 在 **排程** 頁面上，輸入排程詳細資料。 當您完成時，按一下 [ **確定**。 

9. 若要確認備份作業已啟動，請展開 **工作** 節點 **範圍** ] 窗格中，然後再按一下 **執行** 節點。 目前執行工作的清單會出現在 **結果** 窗格。 

## 設定和備份動態鏡像磁碟區

請完成下列步驟來設定動態鏡像磁碟區的備份：

- 步驟 1：使用 [磁碟管理] 來建立動態鏡像磁碟區。 

- 步驟 2：使用 StorSimple Snapshot Manager 來設定備份。

### 必要條件

開始之前：

- 確定已正確設定 StorSimple 裝置和主機電腦。 如需詳細資訊，請移至 [部署在內部部署 StorSimple 裝置](storsimple-deployment-walkthrough.md)。

- 安裝和設定 StorSimple Snapshot Manager。 如需詳細資訊，請移至 [部署 StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md)。

- 在 StorSimple 裝置上設定兩個磁碟區。 (在範例中，可用的磁碟區是 **磁碟 1** 和 **磁碟 2**。) 

### 步驟 1：使用 [磁碟管理] 來建立動態鏡像磁碟區

[磁碟管理] 是一種系統公用程式，用於管理硬碟及其包含的磁碟區或磁碟分割。 如需磁碟管理的詳細資訊，請移至 [磁碟管理](https://technet.microsoft.com/library/cc770943.aspx) Microsoft TechNet 網站上。

#### 若要建立動態鏡像磁碟區

1. 請使用下列任一個選項來啟動 [磁碟管理]： 

   - 開啟 **執行** 方塊中，輸入 **Diskmgmt.msc**, ，然後按 Enter。

   - 啟動 [伺服器管理員中，展開 **儲存體** 節點，然後再選取 **磁碟管理**。 

   - 啟動 **系統管理工具**, ，依序展開 **電腦管理** 節點，然後再選取 **磁碟管理**。 

2. 確定您在 StorSimple 裝置上有兩個可用的磁碟區。 (在此範例中，可用的磁碟區是 **磁碟 1** 和 **磁碟 2**。) 

3. 在磁碟管理] 視窗的下方窗格中，以滑鼠右鍵按一下 **磁碟 1** ，然後選取 **新增鏡像磁碟區**。 

    ![新鏡像磁碟區](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 

4. 在 **新增鏡像磁碟區** 精靈頁面上，按一下 [ **下一步**。

5. 在 **選取磁碟** 頁面上，選取 **磁碟 2** 中 **選取** ] 窗格中，按一下 [ **新增**, ，然後按一下 [ **下一步**。 

6. 在 **指派磁碟機代號或路徑** 頁面上，接受預設值，然後按 **下一步**。 

7. 在 **格式化磁碟區** 頁面上，於 **配置單位大小** 方塊中，選取 **64k**。 選取 **執行快速格式化** 核取方塊，，然後按一下 [ **下一步**。 

8. 在 **完成新增鏡像磁碟區** 頁面上，檢閱您的設定，然後按一下 **完成**。 

9. 訊息會出現，以指出基本磁碟將會轉換成動態磁碟。 按一下 [ **是**。

    ![動態磁碟轉換訊息](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 

10. 在磁碟管理中，確認磁碟區 1 與磁碟區 2 顯示為動態鏡像磁碟區。 (**動態** 應該會出現在 [狀態] 欄中，而容量列顏色應變成紅色，以指出鏡像磁碟區。) 

    ![磁碟管理的鏡像動態磁碟](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 
 
### 步驟 2：使用 StorSimple Snapshot Manager 來設定備份

請使用下列程序，來設定動態鏡像磁碟區，然後立即啟動備份，或建立一個原則進行排程的備份。

#### 若要設定動態鏡像磁碟區的備份

1. 按一下桌面上的 StorSimple Snapshot Manager 圖示。 隨即會出現 [StorSimple Snapshot Manager] 視窗。 

2. 在 **範圍** ] 窗格中，以滑鼠右鍵按一下 **磁碟區** 節點，然後選取 **重新掃描磁碟區**。 掃描完成時，磁碟區清單應該會出現在 **結果** 窗格。 動態鏡像磁碟區會列為單一磁碟區。 

3. 在 **結果** ] 窗格中，以滑鼠右鍵按一下動態鏡像磁碟區，以及 [ **建立磁碟區群組**。 

4. 在 **建立磁碟區群組** 對話方塊中，輸入磁碟區群組的名稱，將動態鏡像磁碟區指派給此群組，然後按一下 **確定**。 

5. 在 **範圍** ] 窗格中，展開 **磁碟區群組** 節點。 新的磁碟區群組應該會出現在  **磁碟區群組** 節點。 

6. 以滑鼠右鍵按一下磁碟區群組名稱。 

    - 若要啟動互動式 (隨) 備份作業，請按一下 [ **進行備份**。 

    - 若要排程自動備份，請按一下 [ **建立備份原則**。 在 **一般** 頁面上，從清單中選取磁碟區群組。 在 **排程** 頁面上，輸入排程詳細資料。 當您完成時，按一下 [ **確定**。 

7. 您可以在備份作業執行時加以監視。 在 **範圍** ] 窗格中，展開 **工作** 節點，然後再按一下 **執行**, ，工作詳細資料會出現在 **結果** 窗格。 當備份工作完成時，詳細資料會傳輸至 **過去 24** 小時作業清單。 

## 後續步驟

- 了解如何 [使用 StorSimple Snapshot Manager 來管理您的 StorSimple 解決方案](storsimple-snapshot-manager-admin.md)。
- 了解如何 [使用 StorSimple Snapshot Manager 來建立及管理磁碟區群組](storsimple-snapshot-manager-manage-volume-groups.md)。

<!--Reference links-->
[1]: https://msdn.microsoft.com/library/ee338480(v=ws.10).aspx
