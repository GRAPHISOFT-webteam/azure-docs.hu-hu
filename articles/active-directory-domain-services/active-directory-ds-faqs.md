<properties
    pageTitle="Azure Active Directory 網域服務預覽：常見問題集 | Microsoft Azure"
    description="關於「Azure Active Directory 網域服務」的常見問題集"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="udayh"
    editor="inhenk"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/16/2015"
    ms.author="maheshu"/>

# Azure Active Directory 網域服務預覽：常見問題集

此頁面會回答有關 Azure Active Directory 網域服務預覽的常見問題。 請隨時回來查看最新消息。

### 疑難排解指南
請參閱我們 [疑難排解指南 》](active-directory-ds-troubleshooting.md) 的設定或管理 Azure AD 網域服務時所遇到的常見問題解決方案。


### 組態

#### 我可以針對單一 Azure AD 目錄建立多個網域嗎？
編號 您只能建立單一網域服務的單一 Azure AD 網域服務的 Azure AD 目錄。  

#### 我可以在訂用帳戶內的多個虛擬網路中使用 Azure AD 網域服務嗎？
服務本身並不直接支援這種情況。 Azure AD 網域服務一次只能在一個虛擬網路上使用。 不過，您可以在多個虛擬網路之間設定連線，以便將 Azure AD 網域服務公開至其他虛擬網路。 這篇文章說明如何 [在 Azure 虛擬網路連接](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)。

#### 我是否可以使用 PowerShell 來啟用「Azure AD 網域服務」？
目前不提供「Azure AD 網域服務」的 PowerShell/自動化部署。

#### 「Azure AD 網域服務」是否可以在新的 Azure 入口網站中使用？
編號 Azure AD 網域服務可以設定只在舊版 Azure 管理入口網站 (也就是 https://manage.windowsazure.com)。 我們預計未來擴充支援新的 Microsoft Azure 管理入口網站 (也就是 https://portal.azure.com)。


### 管理和作業

#### 我已經啟用 Azure AD 網域服務。 我應該使用哪一個使用者帳戶來將電腦加入此網域？
已新增至系統管理群組的使用者帳戶 (也就是「AAD DC 系統管理員」) 就能夠將電腦加入網域。 此外，此群組中的使用者會被授與已加入網域之電腦的遠端桌面存取權限。

#### 我可以針對 Azure AD 網域服務所提供的網域取得網域系統管理員權限嗎？
編號 由於這是受管理的服務，您將不會提供系統管理員權限在網域上。 這表示網域內將不提供「網域系統管理員」以及「企業系統管理員」權限。 Azure AD 目錄內現有的網域系統管理員或企業系統管理員群組也不會被授與該網域的網域/企業系統管理員權限。

#### 我可以在 Azure AD 網域服務所提供的網域上，使用 LDAP 或其他 AD 系統管理工具來修改群組成員資格嗎？
編號 服務的 Azure AD 網域服務的網域上，無法修改群組成員資格。 這同樣適用於使用者屬性。 但是，您可能會在 Azure AD 中或內部部署網域上變更群組成員資格或使用者屬性。 這類變更將會自動同步處理到 Azure AD 網域服務。


### 計費與可用性

#### 這是付費服務嗎？
此服務在公開預覽持續期間，會以特別降低的價格來提供。 一旦服務公開上市 (GA) 之後，將會以完整價格進行計費。 如需詳細資訊，請參閱定價頁面。

#### 是否可以免費試用服務？
此服務隨附於 Azure 的免費試用版。 您可以註冊 [免費試用一個月的 Azure](https://azure.microsoft.com/pricing/free-trial/)。

#### 我可以從 Enterprise Mobility Suite (EMS) 中取得 Azure AD 網域服務嗎？
否，Azure AD 網域服務是隨用隨付的 Azure 服務，並不是 EMS 的一部分。 Azure AD 網域服務適用於所有 Azure AD 的 SKU  (也就是「免費」、「基本」和「高階」)，並根據使用方式進行每小時計費。

#### 哪些 Azure 區域提供此服務？
請參閱我們 [區域頁面](active-directory-ds-regions.md) 若要查看 Azure 區域位置都可使用 Azure AD 網域服務清單。

#### 「Azure AD 網域服務」將於何時正式上市？
我們目前無法分享服務何時將公開上市的時間表。


