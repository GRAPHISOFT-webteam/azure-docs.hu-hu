<properties
   pageTitle="使用 Azure Active Directory 驗證連接到 SQL Database | Microsoft Azure"
   description="了解如何使用 Azure Active Directory 驗證連接到 SQL Database"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jeffreyg"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="10/08/2015"
   ms.author="rick.byham@microsoft.com"/>


# 使用 Azure Active Directory 驗證連接到 SQL Database

Azure Active Directory 驗證是 Azure Active Directory (Azure AD) 中使用身分識別連接到 Microsoft Azure SQL Database 的機制。 您可以使用 Azure Active Directory 驗證，在單一中央位置集中管理資料庫使用者和其他 Microsoft 服務的身分識別。 中央識別碼管理提供單一位置以管理 SQL Database 使用者並簡化權限管理。 包括以下優點：

- 它提供 SQL Server 驗證的替代方案。
- 協助停止跨資料庫伺服器使用過多的使用者身分識別。
- 允許在單一位置的密碼替換
- 客戶可以管理使用外部 (AAD) 群組的資料庫權限。
- 它可以藉由啟用整合式 Windows 驗證和 Azure Active Directory 支援的其他形式驗證來避免儲存密碼。
- Azure Active Directory 驗證會使用自主資料庫使用者，在資料庫層級驗證身分。

> [AZURE.IMPORTANT] Azure Active Directory 驗證是預覽功能，並受限於授權合約 (例如 Enterprise 合約、 Microsoft Azure 合約或 Microsoft Online 訂閱合約)，以及任何適用的預覽條款 [補充使用條款 Microsoft Azure 預覽的](http://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

設定步驟包括以下設定和使用 Azure Active Directory 驗證的程序。

1. 建立和填入 Azure Active Directory
2. 請確定您的資料庫是 Azure SQL Database V12
3. 選用：和目前與您的 Azure 訂用帳戶相關聯的 active directory 產生關聯並加以變更
4. 建立 Azure SQL Server 的 Azure Active Directory 系統管理員
5. 設定用戶端電腦
6. 在對應至 Azure AD 身分識別的資料庫中建立自主資料庫使用者
7. 使用 Azure AD 身分識別連接到您的資料庫


## 信認架構

下列高階圖表摘要說明搭配使用 Azure AD 驗證與 Azure SQL Database 的解決方案架構。 箭頭表示通訊路徑。

![aad 驗證圖表][1]

下圖表示允許用戶端藉由提交 Azure AD 所驗證且受資料庫信認之權杖以連接到資料庫的同盟、信任和主控關聯性。 請務必了解使用 Azure AD 驗證存取資料庫的必要條件是主控訂用帳戶要與 Azure Active Directory 相關聯。

![訂用帳戶關聯性][2]

## 系統管理員結構

使用 Azure AD 驗證時，SQL Database 伺服器會有兩個系統管理員帳戶，原始的 SQL Server 系統管理員和 Azure AD 系統管理員。 只有以 Azure AD 帳戶為基礎的系統管理員可以在使用者資料庫中建立第一個 Azure AD 自主資料庫使用者。 Azure AD 系統管理員登入可以是 Azure AD 使用者或 Azure AD 群組。 當系統管理員是群組帳戶時，它可以供任何群組成員使用，啟用 SQL Server 執行個體的多個 Azure AD 系統管理員。 以系統管理員的身分使用群組帳戶，可讓您集中新增和移除 Azure AD 中的群組成員，而不需要變更 SQL Database 中的使用者或權限，藉以增強管理性。 一律只能設定一個 Azure AD 系統管理員 (使用者或群組)。

![系統管理員結構][3]

## 權限

若要建立新的使用者，您必須具有資料庫中的 **ALTER ANY USER** 權限。 任何資料庫使用者皆可授與 **ALTER ANY USER** 權限。 **ALTER ANY USER** 權限的擁有者包括伺服器系統管理員帳戶、具備資料庫之 **CONTROL ON DATABASE** 或 **ALTER ON DATABASE** 權限的資料庫使用者，以及 **db_owner** 資料庫角色的成員。

若要在 Azure SQL Database 中建立自主資料庫使用者，您必須使用 Azure AD 身分識別連接到資料庫。 若要建立第一個自主資料庫使用者，您必須使用 Azure AD 系統管理員 (資料庫的擁有者) 連接到資料庫。 如以下步驟 4 和 5 所示。

## Azure AD 功能和限制

Azure SQL Server 中可佈建 Azure Active Directory 的下列成員:
- 原生的成員: 在受管理的網域或位於客戶網域中建立 Azure AD 中的成員。 如需詳細資訊，請參閱 [您自己的網域名稱新增至 Azure AD](active-directory-add-domain.md)。
- 同盟網域成員: 在 Azure AD 的同盟網域中建立的成員。 如需詳細資訊，請參閱 [Microsoft Azure 現在支援 Windows Server Active Directory 的同盟](http://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/)。
- 匯入的成員從其他 Azure Active Directory 是原生或同盟的網域成員。
- Active Directory 群組建立為安全性群組。

不支援 Microsoft 帳戶 (例如 outlook.com、hotmail.com、live.com) 或其他來賓帳戶 (例如 gmail.com、yahoo.com)。 如果您可以登入 [https://login.live.com](https://login.live.com) 使用帳戶及密碼，則您使用 Microsoft 帳戶不支援 Azure SQL database 的 Azure AD 驗證。

### 其他考量

- 若要增強管理性，建議您以系統管理員身分佈建專用的 Azure Active Directory 群組。
- 一個 Azure SQL Server 一律只能設定一個 Azure AD 系統管理員 (使用者或群組)。
- 只有 Azure Active Directory 系統管理員可以一開始就使用 Azure Active Directory 帳戶連接到 Azure SQL Server。 Active Directory 系統管理員可以設定後續的 Azure Active Directory 資料庫使用者。
- 建議將連接逾時設定為 30 秒。
- 不支援某些工具，例如 BI 和 Excel。
- Azure Active Directory 驗證只支援 **.NET Framework Data Provider for SqlServer** (最低版本 .NET Framework 4.6)。 因此，Management Studio (適用於 SQL Server 2016) 和資料層應用程式 (DAC 和 .bacpac) 可以連接，但 **sqlcmd.exe** 無法連接，因為 **sqlcmd** 使用 ODBC 提供者。
- 不支援雙因素驗證或其他形式的互動式驗證。


## 1.建立和填入 Azure Active Directory

建立 Azure Active directory 並利用使用者和群組填入。 其中包括：

- 建立初始網域 Azure AD 受管理的網域。
- 將內部部署 Active Directory 網域服務與 Azure Active Directory 建立同盟。

如需詳細資訊，請參閱 [您自己的網域名稱新增至 Azure AD](active-directory-add-domain.md), ，[Microsoft Azure 現在支援 Windows Server Active Directory 的同盟](http://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), ，[管理 Azure AD 目錄](https://msdn.microsoft.com/library/azure/hh967611.aspx), ，和 [使用 Windows PowerShell 管理 Azure AD](https://msdn.microsoft.com/library/azure/jj151815.aspx)。

## 2.請確定您的資料庫是 Azure SQL Database V12

最新的 SQL Database V12 支援 Azure Active Directory 驗證。 如需 SQL Database V12，以及了解它是否可在您的地區資訊，請參閱 [的最新的 SQL Database Update V12 新功能](sql-database-v12-whats-new.md)。

如果您有現有的資料庫，請確認它裝載在 SQL Database V12 藉由連接到資料庫 (例如使用 SQL Server Management Studio) 和執行 `選取 @@VERSION;` 至少為 SQL Database V12 中的資料庫的預期的輸出 **Microsoft SQL Azure (RTM)-12.0**。

如果您的資料庫未裝載 SQL Database V12 功能，請參閱 [規劃和準備升級至 SQL Database V12](sql-database-v12-plan-prepare-upgrade.md), ，，然後瀏覽 Azure 傳統入口網站，將資料庫移轉至 SQL Database V12。

或者，建立新的資料庫在 SQL Database V12 中所述的步驟 [建立您的第一個 Azure SQL database](sql-database-get-started.md)。 **秘訣**：選取新資料庫的訂用帳戶之前參閱下一個步驟。

## 3.選用：和目前與您的 Azure 訂用帳戶相關聯的 active directory 產生關聯並加以變更

若要將您的資料庫與貴組織的 Azure AD 目錄產生關聯，請讓目錄成為裝載資料庫之 Azure 訂用帳戶信任的目錄。 如需詳細資訊，請參閱 [如何在 Azure 訂閱與 Azure AD 的關聯](https://msdn.microsoft.com/library/azure/dn629581.aspx)。

**其他資訊：**每個 Azure 訂用帳戶都會與 Azure AD 執行個體有信任關係。 這表示它信任該目錄來驗證使用者、服務和裝置。 多個訂用帳戶可以信任相同的目錄，但是一個訂用帳戶只能信任一個目錄。 您可以看到您的訂閱底下所信任的目錄 **設定** ] 索引標籤上 [https://manage.windowsazure.com/](https://manage.windowsazure.com/)。 這個訂用帳戶與目錄之間存在的信任關係不同於訂用帳戶與所有其他 Azure 資源 (網站、資料庫等) 之間的關係，後者比較像是訂用帳戶的子資源。 如果訂用帳戶已過期，則也會停止存取與該訂用帳戶相關聯的其他資源。 但目錄會保留在 Azure 中，而且您可以將其他訂用帳戶與該目錄產生關聯，並繼續管理目錄使用者。 如需資源的詳細資訊，請參閱 [了解在 Azure 中的資源存取](https://msdn.microsoft.com/library/azure/dn584083.aspx)。

下列程序會提供如何變更特定訂用帳戶的相關聯目錄的逐步指示。

1. 連接到您 [Azure 傳統入口網站](https://manage.windowsazure.com/) 使用 Azure 訂用帳戶管理員。
2. 在左邊的橫幅中，選取 [設定]****。
3. 您的訂用帳戶會出現在 [設定] 畫面中。 如果未出現所需的訂用帳戶，請按一下頂端的 [訂用帳戶]****，拉下 [依目錄篩選]**** 方塊並選取包含您訂用帳戶的目錄，然後按一下 [套用]****。

    ![選取訂用帳戶][4]
4. 在 **設定** 區域中，按一下您的訂閱，然後再按一下  **編輯目錄** 頁面的底部。

    ![ad-settings-portal][5]
5. 在 [編輯目錄]**** 方塊中，選取與您的 SQL Server 相關聯的 Azure Active Directory，然後按一下箭號進行下一步。

    ![edit-directory-select][6]
6. 在 [確認目錄對應]**** 對話方塊中，確認「**將移除所有的共同管理員**」。

    ![edit-directory-confirm][7]
7. 按一下核取記號重新載入入口網站。

> [AZURE.NOTE] 當您變更目錄時，所有共同管理員、Azure AD 使用者和群組以及目錄備份資源使用者的存取權將會移除，他們將不再有此訂用帳戶或其資源的存取權。 只有具備服務系統管理員身分的您能夠根據新的目錄設定主體的存取權。 這項變更可能需要相當長的時間才會傳播到所有資源。 變更目錄也會變更 SQL Database 的 Azure AD 系統管理員，並且不允許任何現有 Azure AD 使用者的 SQL Database 存取。 Azure AD 系統管理員必須重設 (如下所述) 且必須建立新的 Azure AD 使用者。

## 4.建立 Azure SQL Server 的 Azure Active Directory 系統管理員

每個 Azure SQL Server 會從單一伺服器系統管理員帳戶，也就是從整個 Azure SQL Server 的系統管理員開始。 第二個伺服器系統管理員必須建立，也就是 Azure AD 帳戶。 這個主體會在 master 資料庫中建立為自主資料庫使用者。 身為系統管理員，伺服器系統管理員帳戶是每個使用者資料庫中的 **db_owner** 角色成員，並且會進入每個使用者資料庫做為 **dbo** 使用者。 如需伺服器系統管理員帳戶的詳細資訊，請參閱 [管理資料庫和 Azure SQL Database 中的登入](sql-database-manage-logins.md) 和 **登入和使用者** 區段 [Azure SQL Database 安全性方針和限制](sql-database-security-guidelines.md)。
> [AZURE.NOTE] 不以 Azure AD 帳戶 (包括 Azure SQL Server 系統管理員帳戶) 為基礎的使用者無法建立以 Azure AD 為基礎的使用者，因為他們沒有使用 Azure AD 驗證建議資料庫使用者的權限。

### 使用 Azure 傳統入口網站佈建 Azure SQL Server 的 Azure Active Directory 系統管理員

1. 在 [Azure 傳統入口網站](https://portal.azure.com/), ，按一下右上角的 [下拉式清單可能 Active Directory 的連線。 選擇正確的 Active Directory 做為預設 Azure AD。 此步驟利用 Azure SQL Database 連結與 Active Directory 相關聯的訂用帳戶，確定 Azure AD 和 SQL Server 使用相同的訂用帳戶。

    ![choose-ad][8]
2. 在左邊的橫幅中選取 [SQL 伺服器]****、選取您的 **SQL Server**，然後在 [SQL Server]**** 刀鋒視窗的頂端按一下 [設定]****。

    ![ad 設定][9]
3. 在 [設定]**** 刀鋒視窗中，按一下 [Active Directory 管理 (預覽)]****，並接受預覽子句。
4. 在 [Active Directory 管理 (預覽)]**** 刀鋒視窗中，按一下以檢閱，然後按一下 [確定]**** 接受預覽條款。
5. 在 [Active Directory 管理 (預覽)]**** 刀鋒視窗中，按一下 [Active Directory 管理]****，然後在頂端按一下 [設定管理員]****。
6. 在 [新增系統管理員]**** 刀鋒視窗中，搜尋使用者，選取使用者或群組成為系統管理員，然後按一下 [選取]****。 (Active Directory 管理刀鋒視窗會顯示您的 Active Directory 的所有成員與群組。 呈現灰色的使用者或群組無法選取，因為他們不受支援成為 Azure AD 系統管理員。 (請參閱以上 **Azure AD 功能和限制**中支援的系統管理員清單。) 以角色為基礎的存取控制 (RBAC) 只會套用至入口網站，並且不會傳播至 SQL Server。
7. 在頂端 **Active Directory 系統管理員** 刀鋒視窗中，按一下 [ **儲存**。 
    ![選擇系統管理員][10]

    變更系統管理員的程序可能需要幾分鐘的時間。 然後新的系統管理員會出現在 [Active Directory 管理]**** 方塊中。

> [AZURE.NOTE] 設定 Azure AD 系統管理員時，新的系統管理員名稱 (使用者或群組) 不可以已經存在於主要資料庫中做為 SQL Server 驗證登入。 如果存在，Azure AD 系統管理員設定將會失敗；其中會復原其建立並指出這樣的系統管理員 (名稱) 已經存在。 由於這類 SQL Server 驗證登入不是 Azure AD 的一部分，因此使用 Azure AD 驗證來連線到伺服器的一切努力都會失敗。

若要稍後移除系統管理員，請在 [Active Directory 系統管理員]**** 刀鋒視窗頂端，按一下 [移除系統管理員]****。

### 使用 PowerShell 佈建 Azure SQL Server 的 Azure AD 系統管理員

若要執行 PowerShell Cmdlet，Azure PowerShell 必須已安裝且正在執行中。 如需詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。

若要佈建 Azure AD 系統管理員，您必須執行下列 Azure PowerShell 命令：

- Add-AzureRmAccount
- Select-AzureRmSubscription


用來佈建和管理 Azure AD 系統管理員的 Cmdlet：

| Cmdlet 名稱| 說明|
|---------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| [設定 AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt603544.aspx)| 佈建 Azure SQL Server 的 Azure Active Directory 系統管理員。(必須來自目前的訂用帳戶。)|
| [移除 AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt619340.aspx)| 移除 Azure SQL Server 的 Azure Active Directory 系統管理員。|
| [Get AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt603737.aspx)| 傳回目前為 Azure SQL Server 設定的 Azure Active Directory 系統管理員的相關資訊。|

使用 PowerShell 命令取得說明每個這些命令，查看其他詳細資料，例如 `取得說明集 AzureRmSqlServerActiveDirectoryAdministrator`。

下列指令碼會佈建 Azure AD 系統管理員群組命名為 **DBA_Group** (物件識別碼 `40b79501-b343-44ed-9ce7-da4c8cc7353f`) 的 **demo_server** 名為資源群組中的伺服器 **群組-23**:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23" 
–ServerName "demo_server" -DisplayName "DBA_Group"
```

**DisplayName** 輸入參數可接受 Azure AD 顯示名稱或「使用者主體名稱」。 例如 `DisplayName ="John Smith"` 和 `DisplayName ="johns@contoso.com"`。 Azure AD 群組只支援 Azure AD 顯示名稱。
> [AZURE.NOTE] Azure PowerShell 命令 `組 AzureRmSqlServerActiveDirectoryAdministrator` 不會阻止您佈建 Azure AD 管理員不支援的使用者。 不支援的使用者可以佈建，但是不能連接到資料庫。 (請參閱以上 **Azure AD 功能和限制**中支援的系統管理員清單)。

下列範例使用選用的 **ObjectID**：

```
Set-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23" 
–ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [AZURE.NOTE] 當 **DisplayName** 並非唯一時，就需要 Azure AD **ObjectID**。 若要擷取 **ObjectID** 和 **DisplayName** 的值，請使用 Azure 傳統入口網站的 [Active Directory] 區段，然後檢視使用者或群組的屬性。

下列範例會傳回 Azure SQL Server 的目前 Azure AD 系統管理員的相關資訊：

```
Get-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23" –ServerName "demo_server" | Format-List
```

下列範例會移除 Azure AD 系統管理員:
```
Remove-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" –ServerName "demo_server"
```

## 5.設定用戶端電腦

在您的應用程式或使用者使用 Azure AD 身分識別連接到 Azure SQL Database 的所有用戶端電腦上，您必須安裝下列軟體：

- .NET framework 4.6 版或更新從 [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx)。
- SQL Server 的 azure Active Directory 驗證程式庫 (**ADALSQL。DLL**) 有多個語言版本 (x86 和 amd64) 從下載中心的 [Microsoft Active Directory Authentication Library 的 Microsoft SQL Server](http://www.microsoft.com/download/details.aspx?id=48742)。

### 工具

- 安裝 [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 或 [SQL Server Data Tools for Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) 符合.NET Framework 4.6 需求。
- SSMS 會安裝 x86 版的 **ADALSQL.DLL**。 (目前 SSMS 無法在安裝後提示您需要重新開機。 在未來的 CTP 中將修正此問題。)
- SSDT 會安裝 amd64 版的 **ADALSQL.DLL**。 SSDT 僅部分支援 Azure AD 驗證。
- 從最新的 Visual Studio [Visual Studio 下載](https://www.visualstudio.com/downloads/download-visual-studio-vs) 符合.NET Framework 4.6 需求，但不會安裝必要的 amd64 版本 **ADALSQL。DLL**.

## 6.在對應至 Azure AD 身分識別的資料庫中建立自主資料庫使用者

### 關於自主資料庫使用者

Azure Active Directory 驗證需要建立資料庫使用者做為自主資料庫使用者。 以 Azure AD 身分識別為基礎的自主資料庫使用者是在 master 資料庫中沒有登入的資料庫使用者，並且會在與資料庫相關聯的 Azure AD 目錄中對應至身分識別。 Azure AD 身分識別可以是個別的使用者帳戶或群組。 如需有關自主的資料庫使用者的詳細資訊，請參閱 [包含資料庫使用者進行您資料庫可攜式](https://msdn.microsoft.com/library/ff929188.aspx)。 資料庫使用者 (系統管理員所預期的) 無法使用入口網站和未傳播至 SQL Server 的 RBAC 角色建立。

### 使用 SQL Server Management Studio 連接到使用者資料庫

若要確認 Azure AD 系統管理員已正確設定，請使用 Azure AD 系統管理員帳戶連接到 **master** 資料庫。
若要佈建以 Azure AD 為基礎的自主資料庫使用者 (而非擁有資料庫的伺服器系統管理員)，請連接到具有資料庫存取權之 Azure AD 身分識別的資料庫。
> [AZURE.IMPORTANT] Azure Active Directory 驗證的支援可在 [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。

#### 使用 Active Directory 整合式驗證進行連接

如果您已使用您的 Azure Active Directory 認證從同盟網域登入 Windows，請使用這個方法。

1. 啟動 Management Studio，並在 [連接到 Database Engine]**** (或 [連接到伺服器]****) 對話方塊的 [驗證]**** 方塊中，選取 [Active Directory 整合式驗證]****。 不需要密碼或沒有密碼可輸入，因為現有的認證將會在連接時出現。
2. 按一下 [選項]**** 按鈕，然後在 [連接屬性]**** 頁面的 [連接到資料庫]**** 方塊中，輸入您想要連線的使用者資料庫名稱。

#### 使用 Active Directory 密碼驗證進行連接

使用 Azure AD 受管理網域連接到 Azure AD 主體名稱時，請使用這個方法。 您也可以將其用於沒有網域存取權的同盟帳戶，例如在遠端運作時。

如果您使用認證從未與 Azure 建立同盟的網域登入 Windows，或在使用 Azure AD 驗證時使用以初始或用戶端網域為基礎的 Azure AD，請使用這個方法。

1. 啟動 Management Studio，並在 [連接到 Database Engine]**** (或 [連接到伺服器]****) 對話方塊的 [驗證]**** 方塊中，選取 [Active Directory 密碼驗證]****。
2. 在 **使用者名稱** 方塊中，輸入您的 Azure Active Directory 使用者名稱格式 **username@domain.com**。 這必須是來自 Azure Active Directory 的帳戶或來自與 Azure Active Directory 建立同盟之網域的帳戶。
3. 在 [密碼]**** 方塊中，輸入您的 Azure Active Directory 帳戶或同盟網域帳戶的使用者密碼。
4. 按一下 [選項]**** 按鈕，然後在 [連接屬性]**** 頁面的 [連接到資料庫]**** 方塊中，輸入您想要連線的使用者資料庫名稱。


### 在使用者資料庫中建立 Azure AD 自主資料庫使用者

若要建立以 Azure AD 為基礎的自主資料庫使用者 (而非擁有資料庫的伺服器系統管理員)，請以至少具有 **ALTER ANY USER** 權限的使用者身分，使用 Azure AD 身分識別連線到資料庫 (如先前程序所述)。 然後使用下列的 Transact-SQL 語法：

    CREATE USER Azure_AD_principal_name 
    FROM EXTERNAL PROVIDER;

*Azure_AD_principal_name* 可以是 Azure AD 使用者的使用者主體名稱或 Azure AD 群組的顯示名稱。

**範例：**
若要建立自主的資料庫使用者代表 Azure AD 同盟，或受管理網域使用者:

    CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
    CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;

建立代表 Azure AD 或同盟網域群組的自主資料庫使用者：

    CREATE USER [Nurses] FROM EXTERNAL PROVIDER;

如需有關建立包含資料庫使用者根據 Azure Active Directory 身分識別，請參閱 [CREATE USER (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms173463.aspx)。

當您建立資料庫使用者時，該使用者會獲得 **CONNECT** 權限，而可以用 **PUBLIC** 角色的成員身分連線到該資料庫。 一開始提供給使用者的權限僅限於已授與 **PUBLIC** 角色的任何權限，或已授與其所屬任何 Windows 群組的任何權限。 一旦您佈建以 Azure AD 為基礎的自主資料庫使用者，您可以使用您授與權限給任何其他類型使用者的相同方式，授與該使用者額外的權限。 通常會授與權限給資料庫角色並新增使用者至角色。 如需詳細資訊，請參閱 [資料庫引擎的權限的基本概念](http://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx)。 如需特殊的 SQL 資料庫角色的詳細資訊，請參閱 [管理資料庫和 Azure SQL Database 中的登入](sql-database-manage-logins.md)。
匯入至管理網域的同盟網域使用者必須使用受管理的網域身分識別。
> [AZURE.NOTE] Azure AD 使用者會在資料庫中繼資料中標示為類型 E (EXTERNAL_USER)，而群組則標示為類型 X (EXTERNAL_GROUPS)。 如需詳細資訊，請參閱 [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx)。


## 7.使用 Azure Active Directory 身分識別連接到您的資料庫

Azure Active Directory 驗證支援下列方法，使用 Azure AD 身分識別連接至資料庫：

- 使用整合式 Windows 驗證
- 具備 Azure AD 主體名稱與密碼

### 7.1.使用整合式 (Windows) 驗證進行連接

若要使用整合式 Windows 驗證，網域的 Active Directory 必須與 Azure Active Directory 建立同盟，且連接到資料庫的用戶端應用程式 (或服務) 必須在使用者網域認證下的已加入網域電腦上執行。

若要使用整合式驗證以及 Azure AD 身分識別連接至資料庫，資料庫連接字串中的驗證關鍵字必須設定為 Active Directory 整合式。 下列 C# 程式碼範例會使用 ADO.NET。

    string ConnectionString = 
    @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated;";
    SqlConnection conn = new SqlConnection(ConnectionString);
    conn.Open();

附註的連接字串關鍵字 `整合式安全性 = True` 不支援連接到 Azure SQL Database。

### 7.2.使用 Azure AD 主體名稱與密碼進行連接

若要使用整合式驗證以及 Azure AD 身分識別連接至資料庫，驗證密碼必須設定為 Active Directory 密碼，且連接字串必須包含使用者識別碼/UID 及密碼/PWD 關鍵字和值。 下列 C# 程式碼範例會使用 ADO.NET。

    string ConnectionString =
      @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
    SqlConnection conn = new SqlConnection(ConnectionString);
    conn.Open();

特定程式碼的 Azure AD 驗證與相關範例請參閱 [SQL Server 安全性部落格](http://blogs.msdn.com/b/sqlsecurity/) MSDN 上。

## 另請參閱

[管理資料庫和 Azure SQL Database 中的登入](sql-database-manage-logins.md)

[自主的資料庫使用者](https://msdn.microsoft.com/library/ff929071.aspx)

[建立使用者 (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms173463.aspx)




[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png 
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png 
[3]: ./media/sql-database-aad-authentication/3admin-structure.png 
[4]: ./media/sql-database-aad-authentication/4select-subscription.png 
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png 
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png 
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png 
[8]: ./media/sql-database-aad-authentication/8choose-ad.png 
[9]: ./media/sql-database-aad-authentication/9ad-settings.png 
[10]: ./media/sql-database-aad-authentication/10choose-admin.png 

