<properties
   pageTitle="Azure AD Connect：從 Windows Azure AD 同步作業工具 (DirSync) 升級 | Microsoft Azure"
   description="了解如何從 DirSync 升級至 Azure AD Connect。  本文說明如何將目前 Windows Azure AD 同步作業工具 (DirSync) 升級至 Azure AD Connect 的步驟。"
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="12/16/2015"
   ms.author="shoatman;billmath"/>

# 將 Windows Azure Active Directory 同步作業 (DirSync) 升級至 Azure AD Connect

下列文件將協助您將現有的 DirSync 安裝升級至 Azure AD Connect。

## 相關文件
如果您沒有讀取文件上 [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md), 下, 表提供相關主題連結。 在開始從 DirSync 升級之前，您需要閱讀以粗體顯示的前兩個主題。

| 主題 |  |
| --------- | --------- |
| **下載 Azure AD Connect** | [下載 Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771) |
| **硬體和必要條件** | [Azure AD Connect：硬體和必要條件](active-directory-aadconnect-prerequisites.md) |
| **用於安裝的帳戶** | [Azure AD Connect 帳戶與權限的詳細資訊](active-directory-aadconnect-accounts-permissions.md) |

## 從 DirSync 升級
根據您目前的 DirSync 部署，有不同的升級選項。 如果預期的升級時間少於 3 小時，則我們建議執行就地升級。 如果預期的升級時間超過 3 小時，則我們建議在另一部伺服器上進行平行部署。 如果您有超過 5 萬個物件，預估會花費超過 3 個小時來進行升級。

| 案例 | |
| ---- | ---- |
| [就地升級](#in-place-upgrade)  | 如果升級預期少於 3 小時，則為慣用選項。 |
| [平行部署](#parallel-deployment) | 如果升級預期超過 3 小時，則為慣用選項。 |

>[AZURE.NOTE] 當您計劃從 DirSync 升級至 Azure AD Connect 時，不會解除安裝 DirSync 自行在升級之前。 Azure AD Connect 會讀取和移轉來自 DirSync 的組態，並且在檢查伺服器之後解除安裝。

**就地升級**

精靈會顯示完成升級的預期時間。  根據假設，估計需要 3 小時以完成具有 5 萬個物件 (使用者、連絡人和群組) 的資料庫升級。  Azure AD Connect 將會分析目前的 DirSync 設定，如果您的資料庫中的物件數目少於 5 萬個，則會建議就地升級。  如果您決定要繼續，在升級期間會自動套用目前的設定，您的伺服器將自動繼續執行作用中的同步處理。

如果您想要執行組態移轉及執行平行部署，您可以覆寫就地升級建議。 例如，您可能會利用這個機會來重新整理硬體和作業系統。 請參閱 [平行部署](#parallel-deployment) 區段，如需詳細資訊。

**平行部署**

如果您有超過 5 萬個物件，則建議使用平行部署。 這可避免您的使用者經歷任何作業延遲。 Azure AD Connect 安裝將嘗試估計升級的停機；但是，如果您過去曾升級 DirSync，則您自己的經驗可能就是最佳指南。

### 要升級的支援 DirSync 組態
下列組態變更受到 DirSync 支援，將會升級：

- 網域和 OU 篩選
- 替代 ID (UPN)
- 密碼同步和 Exchange 混合式設定
- 您的樹系/網域與 Azure AD 設定
- 根據使用者屬性進行篩選

下列變更無法升級。 如果您進行了任何這些變更，將會封鎖升級。

![已封鎖升級](./media/active-directory-aadconnect-dirsync-upgrade-get-started/analysisblocked.png)

在這些情況下建議您將新的 Azure AD Connect 伺服器中安裝 [預備模式](active-directory-aadconnectsync-operations.md#staging-mode) ，並確認新的 Azure AD Connect 設定與舊的目錄同步。 重新套用任何變更，使用自訂的組態中所述 [Azure AD Connect 同步處理自訂組態](active-directory-aadconnectsync-whatis.md)。

- 不支援的 DirSync 變更，例如移除屬性和使用自訂延伸模組 DLL

DirSync 用於服務帳戶的密碼無法擷取且不會移轉。 這些密碼將在升級期間重設。

### 從 DirSync 升級至 Azure AD Connect 的高階步驟

1. 歡迎使用 Azure AD Connect
2. 目前 DirSync 組態的分析
3. 收集 Azure AD 全域管理員密碼
4. 收集企業系統管理員帳戶的認證 (僅在 Azure AD Connect 安裝期間使用)
5. Azure AD Connect 安裝
    * 解除安裝 DirSync
    * 安裝 Azure AD Connect
    * 選擇性地開始同步處理

發生下列情況時，需要其他步驟：

* 您目前正在使用完整 SQL Server - 本機或遠端
* 您擁有超過 5 萬個物件的規模可進行同步處理

## 就地升級

1. 啟動 Azure AD Connect 安裝程式 (MSI)。
2. 檢閱並同意授權條款和隱私權注意事項。
![歡迎使用 Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/Welcome.png)
3. 按一下 [下一步，開始分析現有的 DirSync 安裝。
![分析現有目錄同步作業安裝](./media/active-directory-aadconnect-dirsync-upgrade-get-started/Analyze.png)
4. 分析完成時，我們將提出如何繼續的建議。  
    - 如果您使用 SQL Server Express，並以小於 50000 個物件，會顯示下列畫面 ︰
![分析已完成，準備好從 DirSync 升級](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisReady.png)
    - 如果您的 DirSync 使用完整的 SQL Server 將會改看到此頁面 ︰
![分析已完成，準備好從 DirSync 升級](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisReadyFullSQL.png)<BR/>
會顯示有關 DirSync 所使用之現有 SQL Server 資料庫伺服器資訊。 如果需要，請進行適當的調整。 按一下 [ **下一步** 繼續進行安裝。
    - 如果您有超過 50000 個物件，您會看到這個畫面，改為 ︰
![分析已完成，準備好從 DirSync 升級](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)<BR/>
若要繼續進行就地升級時，按一下此訊息旁的核取方塊 ︰ **繼續在此電腦上升級目錄同步。**
若要執行 [平行部署](#parallel-deployment) 改為將匯出 DirSync 組態設定，並移動到新的伺服器。
5. 提供您目前用來連線到 Azure AD 之帳戶的密碼。 必須是 DirSync 目前使用的帳戶。
![輸入您的 Azure AD 認證](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ConnectToAzureAD.png)
6. 提供 Active Directory 的企業系統管理員帳戶。
![輸入您的 ADDS 認證](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ConnectToADDS.png)
7. 您現在已經可以進行設定。  當您按一下 **升級**, 、 將解除安裝 DirSync 和 Azure AD Connect 將會設定並開始同步處理。
![準備好設定]](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ReadyToConfigure.png)


## 平行部署

### 匯出 DirSync 組態
**平行部署超過 5 萬個物件**

如果您有超過 5 萬個物件，則 Azure AD Connect 安裝會建議進行平行部署。

將會顯示與下面類似的畫面：

![分析完成](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)

如果您想要繼續進行平行部署，則需要執行下列步驟：

- 按一下 [ **匯出設定** ] 按鈕。  在不同的伺服器上安裝 Azure AD Connect 時，會匯入這些設定，以將目前 DirSync 中的任何設定移轉到新的 Azure AD Connect 安裝。

順利匯出設定之後，即可結束 DirSync 伺服器上的 Azure AD Connect 精靈。 繼續進行下一個步驟 [不同的伺服器上安裝 Azure AD Connect](#installation-of-azure-ad-connect-on-separate-server)

**平行部署少於 5 萬個物件**

如果您具有少於 5 萬個物件，但是仍然想要執行平行部署，則執行下列動作：

1. 執行 Azure AD Connect Installer (MSI)。
2. 當您看到 **歡迎使用 Azure AD Connect** 畫面上，按一下"X"在右上角的視窗以結束安裝精靈。
3. 開啟命令提示字元。
4. 從 Azure AD Connect 的安裝位置 (預設值：C:\Program Files\Microsoft Azure Active Directory Connect)，執行下列命令：
    `AzureADConnect.exe /ForceExport`。
5. 按一下 [ **匯出設定** ] 按鈕。  在不同的伺服器上安裝 Azure AD Connect 時，會匯入這些設定，以將目前 DirSync 中的任何設定移轉到新的 Azure AD Connect 安裝。

![分析完成](./media/active-directory-aadconnect-dirsync-upgrade-get-started/forceexport.png)

順利匯出設定之後，即可結束 DirSync 伺服器上的 Azure AD Connect 精靈。 繼續進行下一個步驟 [不同的伺服器上安裝 Azure AD Connect](#installation-of-azure-ad-connect-on-separate-server)

### 在不同的伺服器上安裝 Azure AD Connect

在新的伺服器上安裝 Azure AD Connect 時，假設您想要執行 Azure AD Connect 的全新安裝。 因為您想要使用 DirSync 組態，所以有一些額外步驟：

1. 執行 Azure AD Connect Installer (MSI)。
2. 當您看到 **歡迎使用 Azure AD Connect** 畫面上，按一下"X"在右上角的視窗以結束安裝精靈。
3. 開啟命令提示字元。
4. 從 Azure AD Connect 的安裝位置 (預設值：C:\Program Files\Microsoft Azure Active Directory Connect)，執行下列命令：
    `AzureADConnect.exe /migrate`。
    Azure AD Connect 安裝精靈會啟動，並會顯示下列畫面 ︰
![輸入您的 Azure AD 認證](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ImportSettings.png)
5. 選取從 DirSync 安裝所匯出的設定檔案。
6. 設定任何進階選項，包括：
    - Azure AD Connect 的自訂安裝位置。
    - 現有 SQL Server 執行個體 (預設值：Azure AD Connect 會安裝 SQL Server 2012 Express)。 請勿使用相同的資料庫執行個體做為您的 DirSync 伺服器。
    - 用來連接到 SQL Server 的服務帳戶 (如果您的 SQL Server 資料庫位於遠端，則這個帳戶必須是網域服務帳戶)。
在此畫面上，您可以查看這些選項 ︰
![輸入您的 Azure AD 認證](./media/active-directory-aadconnect-dirsync-upgrade-get-started/advancedsettings.png)
7. 按一下 [ **下一步**。
8. 在 **準備好設定** 頁面上，保留 **組態完成時，立即開始同步處理程序** 檢查。 伺服器將處於 [預備模式](active-directory-aadconnectsync-operations.md#staging-mode) 如此變更將不會匯出至 Azure AD 在此階段。
9. 按一下 [ **安裝**。

>[AZURE.NOTE] Windows Server Active Directory 與 Azure Active Directory 之間的同步處理會開始，但會匯出到 Azure AD 的任何變更。  一次只能有一個作用中的同步處理工具匯出變更。 這稱為 [預備模式](active-directory-aadconnectsync-operations.md#staging-mode)。

### 確認 Azure AD Connect 已準備好開始同步處理

若要確認 Azure AD Connect 已準備好要接管從 DirSync，您必須開啟  **同步處理服務管理員** 群組中 **Azure AD Connect** 從 [開始] 功能表。

應用程式中，您將需要檢視 **作業** ] 索引標籤。 在此索引標籤上，您想要確認已完成下列作業：

- 在 AD 連接器上匯入
- 在 Azure AD 連接器上匯入
- AD 連接器上的完整同步處理
- Azure AD 連接器上的完整同步處理

![匯入和同步處理完成](./media/active-directory-aadconnect-dirsync-upgrade-get-started/importsynccompleted.png)

檢閱這些作業的結果，並確定沒有任何錯誤。

如果您想要查看，並查看哪些變更即將匯出到 Azure AD，然後了解如何驗證在組態 [預備模式](active-directory-aadconnectsync-operations.md#staging-mode)。 進行必要的組態變更，直到看不到非預期的任何項目。

這 4 項作業完成之後，沒有任何錯誤，您對即將匯出的變更感到滿意，即準備好解除安裝 DirSync，並啟用 Azure AD Connect 同步處理。 完成以下兩個步驟來完成移轉。

### 解除安裝 DirSync (舊伺服器)

- 從 **程式和功能** 找出 **Windows Azure Active Directory 同步作業工具**
- 解除安裝 **Windows Azure Active Directory 同步作業工具**
- 請注意，可能需要 15 分鐘的時間才能完成解除安裝。

解除安裝 DirSync 之後，沒有任何作用中的伺服器會匯出至 Azure AD。 您在內部部署 Active Directory 中的任何變更繼續同步至 Azure AD 之前，必須完成下一個步驟。

### 啟用 Azure AD Connect (新伺服器)
安裝之後，重新開啟 Azure AD Connect 時，您可以進行其他設定變更。 啟動 **Azure AD Connect** 從 [開始] 功能表或桌面上的捷徑。 請確定您未嘗試重新執行安裝 MSI。

您應該會看見下列內容：

![其他工作](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AdditionalTasks.png)

- 選取 **設定預備模式**。
- 取消選取會關閉預備 **啟用預備模式** 核取方塊。

![輸入您的 Azure AD 認證](./media/active-directory-aadconnect-dirsync-upgrade-get-started/configurestaging.png)

- 按一下 [ **下一步** 按鈕
- 在 [確認] 頁面上按一下 **安裝** ] 按鈕。

Azure AD Connect 現在是您的作用中伺服器。

## 後續步驟
既然您已安裝 Azure AD Connect 可以 [驗證安裝，並指派授權](active-directory-aadconnect-whats-next.md)。

深入了解 [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。


