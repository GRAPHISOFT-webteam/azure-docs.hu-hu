<properties
    pageTitle="啟用 Azure AD 應用程式 Proxy | Microsoft Azure"
    description="涵蓋如何使用 Azure AD 應用程式 Proxy 啟動並執行。"
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="StevenPo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/02/2015"
    ms.author="kgremban"/>


# 啟用 Azure AD 應用程式 Proxy

> [AZURE.NOTE] 應用程式 Proxy 是您升級至 Premium 或 Basic 版本的 Azure Active Directory 時才能使用的功能。 如需詳細資訊，請參閱 [Azure Active Directory 版本](active-directory-editions.md)。

Microsoft Azure Active Directory 應用程式 Proxy 可讓您在私人網路內發行應用程式 (例如 SharePoint 網站、Outlook Web Access 和 IIS 應用程式)，並提供網路外部使用者的安全存取。 員工可以從家裡使用自己的裝置登入您的應用程式，並透過這個雲端 Proxy 進行驗證。

應用程式 Proxy 的運作方式是透過在網路內部安裝一個稱為「連接器」的精簡型 Windows 服務。 連接器會維護您網路中到 Proxy 服務的輸出連接。 當使用者存取已發行的應用程式，Proxy 會使用此連接提供對應用程式的存取。

本文章將引導您在 Azure AD 中為您的雲端目錄啟用 Microsoft Azure AD 應用程式 Proxy、將應用程式 Proxy 連接器安裝在您的私人網路上，並使用您的 Microsoft Azure AD 租用戶訂用帳戶註冊連接器。

## 應用程式 Proxy 先決條件

您可以啟用並使用應用程式 Proxy 服務之前，必須具備：

- Microsoft Azure AD [基本或進階訂用帳戶](active-directory-editions.md) 和 Azure AD 目錄的全域管理員。
- 執行 Windows Server 2012 R2 或 Windows 8.1 或更高版本的伺服器，您可以在該伺服器上安裝應用程式 Proxy 連接器。 伺服器必須能夠將 HTTPS 要求傳送至雲端的應用程式 Proxy 服務，而且必須有您想要發佈的應用程式的 HTTPS 連線。
- 如果防火牆置於路徑中，請確定已開啟防火牆，以允許源自連接器到應用程式 Proxy 的 HTTPS (TCP) 要求。 連接器會將這些連接埠與屬於高層級網域的子網域一起使用：msappproxy.net。 請務必開啟下列**所有**的連接埠以**輸出**流量：

 連接埠號碼| 說明
--- | ---
 80| 為安全性驗證啟用輸出 HTTP 流量。
 443| 針對 Azure AD 啟用使用者驗證 (只有在連接器註冊程序才需要)
 10100 - 10120| 啟用傳送回 Proxy 的 LOB HTTP 回應
 9352、5671| 為連入要求啟用連接器到 Azure 服務之間的通訊。
 9350| 針對連入要求選擇性地啟用較佳的效能。
 8080| 啟用連接器開機順序以及連接器自動更新
 9090| 啟用連接器註冊 (只有在連接器註冊程序才需要)
 9091| 啟用連接器信任憑證自動更新

如果您的防火牆根據原始使用者強制執行流量，請針對來自當做網路服務執行的 Windows 服務的流量，開放這些連接埠。 此外，請務必為 NT Authority\System 啟用連接埠 8080。


## 步驟 1：在 Azure AD 中啟用應用程式 Proxy

1. 在 Azure 傳統入口網站中，以系統管理員身分登入。
2. 移至 Active Directory，並選取您要啟用應用程式 Proxy 所在的目錄。
3. 按一下 [設定]****，向下捲動至 [應用程式 Proxy]，並將 [啟用此目錄的應用程式 Proxy 服務] 切換為 [已啟用]****。

    ![啟用應用程式 Proxy](./media/active-directory-application-proxy-enable/app_proxy_enable.png) <p>
4. 按一下畫面底部的 [立即下載]****。 這會將您帶到下載頁面。 閱讀並接受授權條款，然後按一下 [下載]****，儲存應用程式 Proxy 連接器的 Windows Installer 檔案 (.exe)。

## 步驟 2：安裝並註冊連接器

1. 執行 `AADApplicationProxyConnectorInstaller.exe` 您準備好在伺服器上 (請參閱前述的應用程式 Proxy 必要條件)。
2. 依照精靈中的指示進行安裝。
3. 在安裝期間，系統將提示您使用您 Azure AD 租用戶的應用程式 Proxy 註冊連接器。
<p>-提供 Azure AD 全域系統管理員認證。
<p>-請務必註冊連接器的系統管理員是在相同的目錄，其中您啟用應用程式 Proxy 服務，例如如果租用戶網域為 contoso.com，則系統管理員應該是 admin@contoso.com 或在該網域上的其他別名。此外，您要是 Azure AD 租用戶的全域管理員。您的全域管理員租用戶可能與您的 Microsoft Azure 認證不同。
<p>-若 IE 增強式安全性設定設為 **上** 在伺服器上安裝 Azure AD 連接器，可能會封鎖註冊畫面。如果發生這種情況，請依照錯誤訊息中的指示允許存取。請確定已停用 [Internet Explorer 增強式安全性]。
<p>-如果連接器註冊不成功，請參閱 [疑難排解應用程式 Proxy](active-directory-application-proxy-troubleshoot.md)。

4. 安裝完成後，兩個新的服務會新增到您的伺服器中，如下所示。 這些是允許連線的連接器服務，以及可定期檢查連接器新版本，並在需要時更新連接器的自動更新服務。 按一下 [安裝] 視窗中的 [完成] 以完成安裝
    ![應用程式 Proxy 連接器服務!](./media/active-directory-application-proxy-enable/app_proxy_services.png) <p>
5. 您現在已經準備好使用應用程式 Proxy 發佈應用程式。

為了實現高可用性，您必須至少部署一個額外的連接器。 若要部署額外的連接器，重複以上的步驟 2 和 3。 每個連接器都必須分別進行註冊。

如果您想要解除安裝連接器，請解除安裝連接器服務和更新程式服務，然後務必重新啟動電腦，才能完全移除服務。


## 另請參閱

應用程式 Proxy 還有其他更多用途：

- [使用應用程式 Proxy 發行應用程式](active-directory-application-proxy-publish.md)
- [使用您自己的網域名稱發佈應用程式](active-directory-application-proxy-custom-domains.md)
- [啟用單一登入](active-directory-application-proxy-sso-using-kcd.md)
- [啟用條件式存取](active-directory-application-proxy-conditional-access.md)
- [使用宣告感知應用程式](active-directory-application-proxy-claims-aware-apps.md)
- [您無法使用應用程式 Proxy 的問題進行疑難排解](active-directory-application-proxy-troubleshoot.md)

## 深入了解應用程式 Proxy

- [看看這裡我們的線上說明](active-directory-application-proxy-enable.md)
- [應用程式 Proxy 部落格](http://blogs.technet.com/b/applicationproxyblog/)
- [觀看我們在 Channel 9 上的影片!](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## 其他資源

* [以組織身分登入 azure](sign-up-organization.md)
* [Azure 身分識別](fundamentals-identity.md)
* [使用應用程式 Proxy 發行應用程式](active-directory-application-proxy-publish.md)





