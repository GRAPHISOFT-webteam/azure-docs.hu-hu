<properties
    pageTitle="使用 Azure AD 應用程式 Proxy 發佈之應用程式的條件式存取"
    description="說明如何針對您使用 Azure AD 應用程式 Proxy 發佈可供遠端存取的應用程式設定條件式存取。"
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
    ms.date="10/12/2015"
    ms.author="kgremban"/>

# 使用條件式存取
> [AZURE.NOTE] 應用程式 Proxy 是您升級至 Premium 或 Basic 版本的 Azure Active Directory 時，才可以使用的功能。 如需詳細資訊，請參閱 [Azure Active Directory 版本](active-directory-editions.md)。

您現在可以啟用存取規則，以授與條件式存取給使用者和群組，以便存取使用應用程式 Proxy 發佈的應用程式。 這可讓您：

- 要求各應用程式的多重要素驗證
- 只在使用者不在公司時要求多重要素驗證
- 在使用者不在公司時封鎖使用者存取應用程式

這些規則可以套用至所有使用者和群組，或只套用至特定使用者和群組。
根據預設，規則會套用至所有可存取應用程式的使用者。 不過，也可以將規則限制為指定之安全性群組的使用者成員。  

當使用者存取使用 OAuth 2.0、OpenID Connect、SAML 或 WS-同盟的同盟應用程式時，就會評估存取規則。 此外，當 OAuth 2.0 和 OpenID Connect 以及重新整理權杖用來取得存取權杖時，也會評估存取規則。

## 條件式存取的先決條件

- Azure Active Directory Premium 的訂用帳戶
- 同盟或受管理的 Azure Active Directory 租用戶
- 同盟的租用戶需要啟用 Multi-Factor Authentication (MFA)

![](./media/active-directory-application-proxy-conditional-access/application-proxy-conditional-access.png)

## 設定每個應用程式的 Multi-Factor Authentication
1. 在 Azure 管理入口網站中，以系統管理員身分登入。
2. 移至 Active Directory，並選取您要啟用應用程式 Proxy 所在的目錄。
3. 按一下 [ **應用程式** 向下捲動至 **存取規則** 一節。 只有使用應用程式 Proxy 發佈並使用同盟驗證的應用程式，才會顯示 [存取規則] 區段。
4. 選取 [啟用規則 **啟用存取規則** 到 **上**。
5. 指定將套用規則的使用者和群組。 使用 **加入群組** 按鈕來選取要套用存取規則的一個或多個群組。 此對話方塊也可用來移除選取的群組。  若將規則選取為套用至群組，則只會對屬於其中一個指定安全性群組的使用者強制執行存取規則。 <br> 若要明確地排除規則的安全性群組，請檢查 **除了**  ，並指定一個或多個群組。 [除外] 清單中群組的使用者成員不需要執行 Multi-Factor Authentication。 <br>如果使用者已設定為使用每個使用者的多重要素驗證功能，則此設定會優先於應用程式的多重要素驗證規則。 這表示已設定每個使用者的 Multi-Factor Authentication 的使用者都必須執行 Multi-Factor Authentication，即使他們已從應用程式的 Multi-Factor Authentication 規則中免除。 [深入了解多重要素驗證與每個使用者設定](../multi-factor-authentication/multi-factor-authentication.md)。
6. 選取您要設定的存取規則：
    - **需要多重要素驗證**: 套用存取規則的使用者所必須完成多因素驗證，才能存取規則套用至應用程式。
    - **需要多因素驗證，不在工作時**: 使用者嘗試存取應用程式來自受信任的 IP 位址不需要執行多因素驗證。 可以在 [Multi-Factor Authentication 設定] 頁面上設定受信任的 IP 位址範圍。
    - **封鎖不在工作時存取**: 使用者嘗試存取公司網路外部的應用程式不能存取應用程式。


## 設定同盟服務的 MFA
對於同盟的租用戶，Multi-Factor Authentication (MFA) 可能由 Azure Active Directory 或內部部署 AD FS 伺服器執行。 根據預設，MFA 會發生在 Azure Active Directory 所裝載的任何頁面上。 若要設定內部部署 MFA，請執行 Windows PowerShell 並使用 –SupportsMFA 屬性來設定 Azure AD 模組。
下列範例示範如何使用啟用內部部署 MFA [Set-msoldomainfederationsettings 指令程式](https://msdn.microsoft.com/library/azure/dn194088.aspx) contoso.com 租用戶上: `Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true `
除了設定這個旗標，同盟租用戶 AD FS 執行個體必須設為執行多因素驗證。 依照指示 [部署 Microsoft Azure 多重要素驗證內部](../multi-factor-authentication/multi-factor-authentication-get-started-server.md)。
## 另請參閱
應用程式 Proxy 還有其他更多用途：

- [使用應用程式 Proxy 發行應用程式](active-directory-application-proxy-publish.md)
- [使用您自己的網域名稱發行應用程式](active-directory-application-proxy-custom-domains.md)
- [啟用單一登入](active-directory-application-proxy-sso-using-kcd.md)
- [使用宣告感知應用程式](active-directory-application-proxy-claims-aware-apps.md)
- [使用應用程式 Proxy 疑難排解您遇到的問題](active-directory-application-proxy-troubleshoot.md)

## 深入了解應用程式 Proxy
- [看看我們在這裡的線上說明](active-directory-application-proxy-enable.md)
- [查閱應用程式 Proxy 部落格](http://blogs.technet.com/b/applicationproxyblog/)
- [觀看我們在 Channel 9 上的影片！](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## 其他資源

* [以組織方式註冊 Azure](sign-up-organization.md)
* [Azure 身分識別](fundamentals-identity.md)

