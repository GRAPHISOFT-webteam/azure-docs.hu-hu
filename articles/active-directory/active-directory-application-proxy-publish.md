<properties
    pageTitle="使用 Azure AD 應用程式 Proxy 發行應用程式 | Microsoft Azure"
    description="涵蓋如何使用 Azure AD 應用程式 Proxy 發佈內部部署應用程式。"
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/02/2015"
    ms.author="kgremban"/>


# 使用 Azure AD 應用程式 Proxy 發佈應用程式

> [AZURE.NOTE] 應用程式 Proxy 是您升級至 Premium 或 Basic 版本的 Azure Active Directory 時，才可以使用的功能。 如需詳細資訊，請參閱 [Azure Active Directory 版本](active-directory-editions.md)。

啟用 Microsoft Azure AD 應用程式 Proxy 之後，您就可以發佈應用程式，以供您私人網路外部的使用者存取。

本文會逐步引導您在您要從網路外部啟用安全遠端存取的區域網路上，發佈所執行的應用程式。

> [AZURE.NOTE] 若要確認連接器正常執行，您所發行的第一個應用程式應該從私人網路以確保使用者可以存取網際網路，從發行實際應用程式之前可存取的網站。


## 使用精靈發佈應用程式

1. 開啟您選擇的瀏覽器並瀏覽至 Azure 傳統入口網站。
2. 在 Azure 傳統入口網站的左窗格中，按一下 [Active Directory] 索引標籤。
3. 按一下您啟用應用程式 Proxy 所在的目錄，以及您要發佈應用程式的目錄 (例如，Wingtip Toys)。
4. 按一下 [ **應用程式** 索引標籤，然後再按一下 **新增** 在畫面底部的按鈕
    ![新增應用程式](./media/active-directory-application-proxy-publish/aad_appproxy_selectdirectory.png)
5. 您要在項目? 對話方塊中，按一下 [ **發佈應用程式可以存取您的網路外**。
    ![將可從網路外部的新應用程式](./media/active-directory-application-proxy-publish/aad_appproxy_addapp.png)

6. 依照畫面上指示，提供下列有關您的應用程式的資訊：
    ![應用程式屬性](./media/active-directory-application-proxy-publish/aad_appproxy_appproperties.png)

**設定** | **詳細資料**
---|---
外部 URL | 這是用來從私人網路外部存取應用程式的雲端服務的 URL。 URL 會根據您所提供的名稱與後置詞 msappproxy.net 自動產生。
預先驗證方法 | <p>設定您想要使用的應用程式的預先驗證方法的型別:</p><p>a. Microsoft Azure Active Directory (Microsoft Azure AD) – 當使用者嘗試存取應用程式時，應用程式 Proxy 會將使用者重新導向至 Azure AD 將會驗證使用者，並確保使用者具有目錄和應用程式的必要權限登入。</p> <p>b. 通過 – 不執行預先驗證。</p>
外部 URL 通訊協定 | <p>根據預設，應用程式會使用 HTTPS 通訊協定發佈。 此服務會自動重新導向輸入含 http 之 URL 的使用者。</p><p>若要為內部應用程式啟用 HTTP，您必須將預先驗證方法設定為 [通過]，然後您就可以將外部 URL 通訊協定從 HTTPS 變更為 HTTP。 請注意，使用 HTTP 發佈應用程式可能會產生您的應用程式和使用者的安全性問題。</p><p>您可以插入自訂網域，而不是使用預設後置詞 msappproxy.net。 如需詳細資訊，請參閱 [使用自訂網域](active-directory-application-proxy-custom-domains.md)。</p>
內部 URL | <p>這是應用程式 Proxy 連接器在內部存取應用程式所使用的內部 URL。 這應該是從您私人網路內部存取應用程式所使用的已發佈應用程式的 URL。 這是有效的 URL，不含空格或符號。</p> <p>您可以指定後端伺服器上要發佈的特定路徑，而伺服器的其餘部分則不發佈。 例如，這可讓您發佈位於相同的 SharePoint 伺服器，但具有不同的名稱和存取規則的不同網站。</p> <p>路徑指定於內部 URL 欄位中，而且將可在外部 URL 中看到。 內部路徑和外部路徑必須相同。</p>

若要完成精靈，請按一下畫面底部的核取記號。 應用程式現在已在 Azure AD 中定義。


## 將使用者和群組指派給應用程式

1. 若是已預先驗證的應用程式，您必須指派將可存取應用程式的使用者和群組。 <p>若是通過的應用程式，則所有使用者都可以存取。 不過，若要讓使用者查看其應用程式清單中的應用程式，您必需將應用程式指派給該使用者。
2. 完成 [新增應用程式] 精靈之後，[應用程式 Proxy 快速啟動] 頁面隨即顯示。 若要指派使用者，請按一下 [ **指派使用者**。
    ![應用程式 Proxy 快速啟動畫面](./media/active-directory-application-proxy-publish/quickstart.png)
3. 選取每個使用者或群組，您想要指派給此應用程式和按一下 **指派**。

> [AZURE.NOTE] 整合式 Windows 驗證的應用程式，您可以從內部部署 Active Directory 指派的使用者和群組同步處理。 您無法為使用 Azure Active Directory 應用程式 Proxy 發佈的應用程式，指派使用 Microsoft 帳戶和來賓登入的使用者。 請確定您指派的使用者使用與您要發佈的應用程式屬於相同網域的認證登入。


## 進階組態

1. 您可以修改已發佈的應用程式，或從 [設定] 頁面設定進階選項，例如，單一登入內部部屬應用程式。
    ![進階組態](./media/active-directory-application-proxy-publish/advancedconfig.png)

2. 選取應用程式，然後按一下 **設定**。 可以使用下列選項：

**設定** | **詳細資料**
---|---
名稱 | 提供您的應用程式的描述性名稱
外部 URL | 這是用來從私人網路外部存取應用程式的雲端服務的 URL。 URL 會根據您所提供的名稱與後置詞 msappproxy.net 自動產生。
預先驗證方法 | <p>設定您想要使用的應用程式的預先驗證方法的型別:</p><p>a. Microsoft Azure Active Directory (Microsoft Azure AD) – 當使用者嘗試存取應用程式時，應用程式 Proxy 會將使用者重新導向至 Azure AD 將會驗證使用者，並確保使用者具有目錄和應用程式的必要權限登入。</p> <p>b. 通過 – 不執行預先驗證。</p>
外部 URL 通訊協定 | <p>根據預設，應用程式會使用 HTTPS 通訊協定發佈。 此服務會自動重新導向輸入含 http 之 URL 的使用者。</p><p>若要為內部應用程式啟用 HTTP，您必須將預先驗證方法設定為 [通過]，然後您就可以將外部 URL 通訊協定從 HTTPS 變更為 HTTP。 請注意，使用 HTTP 發佈應用程式可能會產生您的應用程式和使用者的安全性問題。</p><p>您可以插入自訂網域，而不是使用預設後置詞 msappproxy.net。 如需詳細資訊，請參閱 [使用自訂網域](active-directory-application-proxy-custom-domains.md)。</p>
內部 URL | <p>這是應用程式 Proxy 連接器在內部存取應用程式所使用的內部 URL。 這應該是從您私人網路內部存取應用程式所使用的已發佈應用程式的 URL。 這是有效的 URL，不含空格或符號。</p> <p>您可以指定後端伺服器上要發佈的特定路徑，而伺服器的其餘部分則不發佈。 例如，這可讓您發佈位於相同的 SharePoint 伺服器，但具有不同的名稱和存取規則的不同網站。</p> <p>路徑指定於內部 URL 欄位中，而且將可在外部 URL 中看到。 內部路徑和外部路徑必須相同。</p>
轉譯標頭中的 URL | 應用程式 (例如某些 SharePoint 組態) 的 HTTP 主機標頭未在轉譯中，將此設為 **否**。 這樣會同時為要求和回應標頭停用標頭轉譯。
內部驗證方法 | <p>如果您使用應用程式 Proxy 進行預先驗證，您可以設定內部驗證方法，讓使用者可以從單一登入 (SSO) 到此應用程式受益。</p><p> 選取 **整合式 Windows 驗證 (IWA)** 如果您的應用程式使用 IWA，而且您可以設定 Kerberos 限制委派 (KCD) 為此應用程式啟用 SSO。 使用 IWA 的應用程式必須設為使用 KCD，否則「應用程式 Proxy」將無法發佈這些應用程式。</p><p>選取 **無** 如果您的應用程式不使用 IWA。</p><p>如需詳細資訊，請參閱 [單一登入應用程式 proxy](active-directory-application-proxy-sso-using-kcd.md)。</p>
內部應用程式 SPN | <p>這是內部應用程式的服務主體名稱 (SPN)，如內部部屬應用程式 Proxy 中所設定。 應用程式 Proxy 連接器會使用 SPN 擷取使用 Kerberos 限制委派 (KCD) 的應用程式的 Kerberos 權杖。</p><p>如需詳細資訊，請參閱 [啟用單一登入](active-directory-application-proxy-sso-using-kcd.md)。</p>

使用 Azure Active Directory 應用程式 Proxy 發佈應用程式之後，這些應用程式會出現在 Azure AD 中的應用程式清單內，您可以在那裡進行管理。

如果您在發佈應用程式之後停用應用程式 Proxy 服務，則不會刪除應用程式，但將不再能夠從您的私人網路外部存取。

若要檢視應用程式，並確定可以存取該應用程式，按兩下應用程式的名稱。 如果已停用應用程式 Proxy 服務，而且無法使用應用程式，則會在畫面頂端出現一個警告訊息。

若要刪除應用程式，在清單中選取的應用程式，然後按一下 [ **刪除**。

## 另請參閱
應用程式 Proxy 還有其他更多用途：

- [啟用應用程式 Proxy](active-directory-application-proxy-enable.md)
- [使用您自己的網域名稱發行應用程式](active-directory-application-proxy-custom-domains.md)
- [啟用單一登入](active-directory-application-proxy-sso-using-kcd.md)
- [啟用條件式存取](active-directory-application-proxy-conditional-access.md)
- [使用宣告感知應用程式](active-directory-application-proxy-claims-aware-apps.md)
- [使用應用程式 Proxy 疑難排解您遇到的問題](active-directory-application-proxy-troubleshoot.md)

## 深入了解應用程式 Proxy
- [看看我們在這裡的線上說明](active-directory-application-proxy-enable.md)
- [查閱應用程式 Proxy 部落格](http://blogs.technet.com/b/applicationproxyblog/)
- [觀看我們在 Channel 9 上的影片！](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## 其他資源

* [關於 Kerberos 限制委派](http://technet.microsoft.com/library/cc995228.aspx)

