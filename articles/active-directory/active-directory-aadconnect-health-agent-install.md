<properties
    pageTitle="Azure AD Connect Health 代理程式安裝 | Microsoft Azure"
    description="這是 Azure AD Connect Health 頁面，其中說明 AD FS 與同步處理的代理程式安裝。"
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/15/2015"
    ms.author="billmath"/>






# Azure AD Connect Health 代理程式安裝 

本文件將逐步引導您安裝和設定適用於 AD FS 的 Azure AD Connect Health 代理程式與同步處理。

>[AZURE.NOTE]請記住，您看到您的 Azure AD Connect Health 執行個體的任何 AD FS 資料之前，您必須在目標伺服器上安裝 Azure AD Connect Health 代理程式。  請務必完成需求 [這裡](active-directory-aadconnect-health.md#requirements) 之前安裝代理程式。  您可以下載代理程式 [這裡](http://go.microsoft.com/fwlink/?LinkID=518973)。


## 安裝 Azure AD Connect Health AD fs 的代理程式
若要啟動代理程式安裝，請按兩下您所下載的 .exe 檔案。 在第一個畫面上，按一下 [安裝]。

![驗證 Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install1.png)

一旦安裝完成之後，按一下 [立即設定]。

![驗證 Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install2.png)

這將會啟動命令提示字元，然後再啟動將執行 Register-AzureADConnectHealthADFSAgent 的特定 PowerShell。 系統將提示您登入 Azure。 繼續進行並登入。

![驗證 Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install3.png)


登入後，PowerShell 將會繼續。 一旦完成之後，您就可以關閉 PowerShell，且設定已完成。

此時，應該會自動啟動服務，且代理程式現在將會監視並收集資料。  請注意，如果您還未符合前幾節所述的所有必要條件，您將會在 PowerShell 視窗中看到警告。 請務必完成需求 [這裡](active-directory-aadconnect-health.md#requirements) 之前安裝代理程式。 下列螢幕擷取畫面是這些錯誤的其中一個範例。

![驗證 Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install4.png)

若要確認已安裝代理程式，請開啟服務，並尋找下列項目。 如果您已完成設定，這些服務應該正在執行。 否則，他們將不會啟動，直到設定完成為止。

- Azure AD Connect Health AD FS 診斷服務
- Azure AD Connect Health AD FS Insights 服務
- Azure AD Connect Health AD FS 監視服務

![驗證 Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install5.png)


### Windows Server 2008 R2 伺服器上的代理程式安裝

若是 Windows Server 2008 R2 伺服器，請執行以下操作：

1. 請確定伺服器在 Service Pack 1 或更高版本上執行。
1. 關閉 IE ESC 以安裝代理程式：
1. 在安裝 AD Health 代理程式之前，先在每部伺服器上安裝 Windows PowerShell 4.0。  安裝 Windows PowerShell 4.0：
 - 安裝 [Microsoft.NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779) 使用下列連結下載離線安裝程式。
 - 安裝 PowerShell ISE (從 Windows 功能)
 - 安裝 [Windows Management Framework 4.0。](https://www.microsoft.com/download/details.aspx?id=40855)
 - 在伺服器上安裝 Internet Explorer 10 或更新版本。 這是 Health 服務所必需，才能使用 Azure 系統管理員認證進行驗證。
1. 如需在 Windows Server 2008 R2 上安裝 Windows PowerShell 4.0 的詳細資訊，請參閱 wiki 文章 [這裡](http://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx)。

### 啟用 AD FS 的稽核

若要讓使用情況分析功能收集並分析資料，Azure AD Connect Health 代理程式需要 AD FS 稽核記錄檔中的資訊。 預設不會啟用這些記錄檔。 這只適用於 AD FS 同盟伺服器。 您不需要在 AD FS Proxy 伺服器或 Web 應用程式 Proxy 伺服器上啟用稽核。 使用下列程序啟用 AD FS 稽核，並找出 AD FS 稽核記錄檔。

#### 啟用 AD FS 2.0 的稽核

1. 按一下 [ **啟動**, ，指向 [ **程式**, ，指向 [ **系統管理工具**, ，然後按一下 [ **本機安全性原則**。
2. 瀏覽至 **Security Settings\Local Policies\User Rights Management** 資料夾，然後按兩下 [產生安全性稽核。
3. 在 **本機安全性設定** 索引標籤上，確認已列出 AD FS 2.0 服務帳戶。 如果不存在，按一下 [ **新增使用者或群組** 並將其加入清單，然後按一下 **確定**。
4. 使用提高的權限開啟命令提示字元，並執行下列命令以啟用稽核。<code>auditpol.exe /set /subcategory:"Application Generated"/failure /success:enable</code>
5. 關閉 [本機安全性原則]，然後開啟 [管理嵌入式管理單元]。  若要開啟 [管理] 嵌入式管理單元，請按一下 [ **啟動**, ，指向 [ **程式**, ，指向 [ **系統管理工具**, ，然後按一下 [AD FS 2.0 管理。
6. 在 [動作] 窗格中，按一下 [編輯同盟服務屬性]。
7. 在 **Federation Service 屬性** 對話方塊中，按一下 [ **事件** ] 索引標籤。
8. 選取 **成功稽核** 和 **失敗稽核** 核取方塊。
9. 按一下 [ **確定**。

#### 在 Windows Server 2012 R2 上啟用 AD FS 的稽核

1. 開啟 **本機安全性原則** 開啟 **伺服器管理員** 在 [開始] 畫面中或桌面上工作列中的伺服器管理員，然後按一下 [ **工具/本機安全性原則**。
2. 瀏覽至 **安全性本機原則 \ 使用者權限指派** 資料夾，然後再按兩下 **產生安全性稽核**。
3. 在 **本機安全性設定** 索引標籤上，確認已列出 AD FS 服務帳戶。 如果不存在，按一下 [ **新增使用者或群組** 並將其加入清單，然後按一下 **確定**。
4. 使用提高的權限開啟命令提示字元，然後執行下列命令以啟用稽核: <code>auditpol.exe /set /subcategory:"Application Generated"/failure /success:enable。</code>
5. 關閉 **本機安全性原則**, ，然後開啟 **AD FS 管理** 嵌入式管理單元 (在 [伺服器管理員] 中，按一下工具]，然後選取 [AD FS 管理)。
6. 在 [動作] 窗格中，按一下 [ **編輯同盟服務屬性**。
7. 在 [Federation Service 屬性] 對話方塊中，按一下 [ **事件** ] 索引標籤。
8. 選取 **成功稽核] 和 [失敗稽核** 核取方塊，然後按一下 [ **確定**。






#### 找出 AD FS 稽核記錄檔


1. 開啟 **事件檢視器**。
2. 移至 [Windows 記錄檔，然後選取 **安全性**。
3. 在右側，按一下 [ **篩選目前的記錄檔**。
4. 在 [事件來源] 下選取 **AD FS 稽核**。

![AD FS 稽核記錄](./media/active-directory-aadconnect-health-requirements/adfsaudit.png)

> [AZURE.WARNING] 如果您有已停用 AD FS 稽核 Azure AD Connect Health 代理程式的群組原則將無法收集資訊。 請確定您沒有可能即將停用稽核的群組原則。

[//]: # (Start of Agent Proxy Configuration Section)

## 安裝適用於同步處理的 Azure AD Connect Health 代理程式
適用於同步處理的 Azure AD Connect Health 代理程式會自動安裝在 Azure AD Connect 的最新組建中。  如果要使用適用於同步處理的 Azure AD Connect，您必須下載最新版的 Azure AD Connect 並安裝它。  您可以下載最新版本 [這裡](http://www.microsoft.com/download/details.aspx?id=47594)。

若要確認已安裝代理程式，請開啟服務，並尋找下列項目。 如果您已完成設定，這些服務應該正在執行。 否則，他們將不會啟動，直到設定完成為止。

- Azure AD Connect Health AadSync Insights 服務
- Azure AD Connect Health AadSync Monitoring 服務
 
![驗證適用於同步處理的 Azure AD Connect Health](./media/active-directory-aadconnect-health-sync/services.png)

>[Azure.NOTE] 請記住，使用 Azure AD Connect Health 需要 Azure AD Premium。  如果您沒有 Azure AD Premium，將無法完成 Azure 入口網站中的組態。  如需詳細資訊，請參閱需求 [這裡](active-directory-aadconnect-health.md#requirements)。 




## 設定 Azure AD Connect Health 代理程式使用 HTTP Proxy
您可以設定 Azure AD Connect Health 代理程式使用 HTTP Proxy。

>[AZURE.NOTE]
- 使用"Netsh WinHttp set ProxyServerAddress"將無法運作時的代理程式使用 System.Net 提出 web 要求，而不是 Microsoft Windows HTTP 服務。
- 設定的 Http Proxy 位址將用來傳遞已加密的 Https 訊息中。
- 不支援已驗證的 proxy (使用 HTTPBasic)。

### 變更健康情況代理程式 Proxy 組態
您有下列選項來設定 Azure AD Connect Health 代理程式使用 HTTP Proxy。

>[AZURE.NOTE] 您必須重新啟動所有的 Azure AD Connect Health 代理程式服務，以更新的 proxy 設定。 執行下列命令:<br>
    重新啟動服務 AdHealth *

#### 匯入現有的 Proxy 設定

##### 從 Internet Explorer 匯入
您可以匯入您的 Internet Explorer HTTP Proxy 設定，並將它們用於 Azure AD Connect Health 代理程式，方法是在執行健康情況代理程式的每部伺服器上執行下列 PowerShell 命令。

    Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings

##### 從 WinHTTP 匯入
您可以在執行健康情況代理程式的每部伺服器上執行下列 PowerShell 命令，以匯入您的 WinHTTP Proxy 設定。

    Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp

#### 以手動方式指定 Proxy 位址
您可以在執行健康情況代理程式的每部伺服器上執行下列 PowerShell 命令，以手動指定 Proxy 伺服器。

    Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port

範例: *組 AzureAdConnectHealthProxySettings HttpsProxyAddress myproxyserver:443*

- 「位址」可以是可解析的 DNS 伺服器名稱或 IPv4 位址
- 「連接埠」可以省略。 如果省略，則會選擇 443 做為預設連接埠。

#### 清除現有的 Proxy 設定
您可以執行下列命令來清除現有的 Proxy 設定。

    Set-AzureAdConnectHealthProxySettings -NoProxy


### 讀取目前的 Proxy 設定
您可以使用下列命令來讀取的目前設定的 Proxy 設定。

    Get-AzureAdConnectHealthProxySettings


[//]: # (End of Agent Proxy Configuration Section)


## 相關連結

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Azure AD Connect Health 操作](active-directory-aadconnect-health-operations.md)
* [使用 Azure AD Connect Health 搭配 AD FS](active-directory-aadconnect-health-adfs.md)
* [使用適用於同步處理的 Azure AD Connect Health](active-directory-aadconnect-health-sync.md)
* [Azure AD Connect Health 常見問題集](active-directory-aadconnect-health-faq.md)

