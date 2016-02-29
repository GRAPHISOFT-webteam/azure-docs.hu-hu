<properties
   pageTitle="Azure 資料目錄版本資訊"
   description="Azure 資料目錄 2015 年 8 月 28 日公開預覽版本的版本資訊。"
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="11/20/2015"
   ms.author="maroche"/>

# Azure 資料目錄版本資訊

## Azure 資料目錄 2015 年 11 月 20 日版本的注意事項

### 在 Power BI Desktop 中開啟資料來源

當使用 「 開啟中 Power BI Desktop 」 選項，從 **Azure 資料目錄** 入口網站，使用者可能會遇到的其中一個 Power BI Desktop 應用程式中的兩個問題:

- 會顯示標題為「無法開啟文件」的對話方塊
- Power BI Desktop 應用程式可開啟，但檔案是空的

每一種情況下，問題可以解決下載並安裝最新版的 Power BI Desktop 從 [PowerBI.com](https://powerbi.com)。

## Azure 資料目錄 2015 年 11 月 13 日版本的注意事項

### 註冊並連接至 Teradata

連接到 Teradata 資料來源時，使用者必須已安裝正確的 Teradata ODBC 驅動程式，符合所使用軟體的位元 (32 位元或 64 位元)。

此 ADC 發行日期為止，最新 [for windows (版本 15.10) 的 Teradata ODBC 驅動程式](http://downloads.teradata.com/download/connectivity/odbc-driver/windows) 相容與 Office 2013，但不是能搭配 Office 2016。

## Azure 資料目錄 2015 年 11 月 6 日版本的注意事項

### 使用者在啟動資料來源註冊工具時可能會遇到錯誤

當您啟動時 **Azure 資料目錄** 從資料來源註冊工具 **Azure 資料目錄** 入口網站，先前已安裝舊版工具的使用者可能會收到此訊息: 「 無法啟動應用程式。 請連絡應用程式廠商」。

使用者會收到這個訊息應該使用 Windows 控制台中移除現有的 「 Azure 資料目錄 」 應用程式，然後重新啟動從工具 **Azure 資料目錄** 入口網站。

## Azure 資料目錄 2015 年 7 月 13 日版本的注意事項

### 註冊並連接至 Oracle 資料庫

連接到 Oracle 資料庫資料來源時，使用者必須已安裝正確的 Oracle 驅動程式，符合所使用軟體的位元 (32 位元或 64 位元)。

-   在執行 32 位元 Windows 的電腦上註冊 Oracle 資料來源時，將使用 32 位元 Oracle 驅動程式
-   在執行 64 位元 Windows 的電腦上註冊 Oracle 資料來源時，將使用 64 位元 Oracle 驅動程式
-   在執行 32 位元版本 Microsoft Office 的電腦上使用 Excel 連接到 Oracle 資料來源時 (包括在 64 位元 Windows 上)，將使用 32 位元 Oracle 驅動程式
-   在執行 64 位元版本 Microsoft Office 的電腦上使用 Excel 連接到 Oracle 資料來源時，將使用 64 位元 Oracle 驅動程式

### 註冊及連接到 SQL Server Reporting Services

Azure 資料目錄初始預覽版本中對於 SQL Server Reporting Services (SSRS) 資料來源的支援，僅限於原生模式伺服器。 未來版本將加入在 SharePoint 模式中支援 SSRS。

### 在 Excel 中開啟資料資產

從 Microsoft Excel 中開啟資料資產時 **Azure 資料目錄** 入口網站，使用者可能會看到與 **Microsoft Excel 安全性注意事項** 對話方塊。 這是標準的預期的行為，使用者可以選取 **啟用** 以繼續。

如需詳細資訊，請參閱 [啟用或停用連結和檔案的可疑網站之安全性警告](https://support.office.com/en-us/article/Enable-or-disable-security-alerts-about-links-and-files-from-suspicious-websites-A1AC6AE9-5C4A-4EB3-B3F8-143336039BBE)。

### 預覽中遺漏 BLOB 和 UDT 資料行

當您註冊的資料表和檢視包含二進位大型物件 (BLOB) 和使用者自訂資料型別 (UDT) 資料行，且選擇要包含資料資產的預覽時，預覽中將不會包含這些資料行。

### Proxy 與原則設定及資料來源註冊

使用者可能會遇到一種情況，他們可以登入 Azure 資料目錄入口網站，但在嘗試登入資料來源註冊工具時，卻遇到錯誤訊息，導致無法登入。

此問題行為有兩個可能的原因：

**原因 1: Active Directory Federation Services 設定**
資料來源註冊工具使用「表單驗證」，以根據 Active Directory 驗證使用者登入。 Active Directory 系統管理員必須在全域驗證原則中啟用表單驗證，登入才會成功。

在某些情況下，只有當使用者是在公司網路上，或只有當使用者從公司網路外部連接時，才會發生此錯誤行為。 全域驗證原則允許分別為內部網路和外部網路連線啟用驗證方法。 如果使用者連線的網路未啟用表單驗證，可能會發生登入錯誤。

如需詳細資訊，請參閱 [設定驗證原則](https://technet.microsoft.com/en-us/library/dn486781.aspx)。

**原因 2: 網路 proxy 設定**
如果公司網路使用 proxy 伺服器，註冊工具可能無法透過 proxy 連線到 Azure Active Directory。 使用者可以編輯註冊工具的組態檔，將此區段加入至檔案，以確保註冊工具能夠連線：


      <system.net>
        <defaultProxy useDefaultCredentials="true" enabled="true">
          <proxy usesystemdefault="True"
                          proxyaddress="http://<your corporate network proxy url>"
                          bypassonlocal="True"/>
        </defaultProxy>
      </system.net>


若要找出 RegistrationTool.exe.config 檔案，請啟動註冊工具，然後開啟 Windows 工作管理員公用程式。 在工作管理員的 [詳細資料] 索引標籤，以滑鼠右鍵按一下 RegistrationTool.exe，再從快顯功能表中選擇 [開啟檔案位置]。

