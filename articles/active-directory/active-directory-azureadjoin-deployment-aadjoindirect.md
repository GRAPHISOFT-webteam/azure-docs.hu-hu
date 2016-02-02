<properties 
    pageTitle="適用於 Azure AD Join 的使用案例和部署考量| Microsoft Azure" 
    description="說明系統管理員如何為其使用者 (員工、學生、其他使用者) 設定 Azure AD Join。其中也會討論使用 Azure AD Join 時出現的各種真實案例。" 
    services="active-directory" 
    documentationCenter="" 
    authors="femila" 
    manager="stevenpo" 
    editor=""
    tags="azure-classic-portal"/>

<tags 
    ms.service="active-directory" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/19/2015" 
    ms.author="femila"/>


# 適用於 Azure AD Join 的使用案例和部署考量

## 適用於 Azure AD Join 的使用案例

案例 1：主要位於雲端的企業
--------------

如果您目前是在雲端為貴公司操作和管理身分識別，或者即將轉向雲端，則可從 Azure AD Join 獲益。 您可以使用在 Azure AD 中建立的帳戶來登入 Windows 10。 透過 [第一次執行體驗 (FRX) 程序]((active-directory-azureadjoin-user-frx.md)) 或加入 Azure AD 透過 [設定體驗](active-directory-azureadjoin-user-upgrade.md), ，您的使用者可以將其電腦加入 Azure AD。 您的使用者現在可以在瀏覽器或 Office 應用程式中，享有其雲端資源 (例如 Office 365) 的 SSO 存取權。

案例 2: 教育機構
----------

教育機構通常有兩種使用者類型: 教職員和學生。 教職成員會被視為組織中較長期的成員，因此需要為他們建立內部部署帳戶。 但學生是組織中期限較短的成員，因而可在 Azure AD 中進行管理，如此一來，就能將目錄範圍推送至雲端，而不是內部部署。 這些學生現在可以使用其 Azure AD 帳戶登入 Windows，並在 Office 應用程式中取得 Office 365 資源的存取權。

案例 3：零售業
--------

零售業擁有季節性工作者和長期員工。 您可以將較長期的全職員工建立為內部部署帳戶，而他們通常會使用已加入網域的電腦。 但季節性工作者是組織中期限較短的成員，因而需要在可更輕易四處移動使用者授權的地方加以管理。 在具有 Office 365 授權的雲端中建立這些使用者，讓這些使用者能夠因為使用 Azure AD 帳戶登入 Windows 和 Office 應用程式而獲益，同時在他們離開公司之後對於他們的授權保有更多機動性。

案例 4：其他案例
---------

以外上面所討論的案例，就能輕易以讓使用者加入其裝置加入 Azure AD 因為簡化的加入經驗，在 Azure AD 中的裝置管理自動執行 MDM 註冊和單一登入 Azure AD 和內部資源。


## 適用於 Azure AD Join 的部署考量

讓使用者可將公司擁有的裝置直接加入 Azure AD
--------------------------

企業可以為合作夥伴公司和組織提供僅限雲端的帳戶。 這些合作夥伴之後就能輕鬆存取並單一登入至其公司 App 和資源。 此案例適用於主要使用其裝置來存取雲端資源的使用者，例如 Office 365 或 SaaS App，而這類資源會依賴 Azure AD 進行驗證。

### 必要條件

**在企業層級 (系統管理員)**

*   Azure 訂用帳戶搭配 Azure Active Directory

**在使用者層級**

*   Windows 10 (Professional 和 Enterprise SKU)

### 系統管理員工作

* [設定裝置註冊](active-directory-azureadjoin-setup.md)

### 使用者工作

* [設定新的 Windows 10 裝置安裝期間使用 Azure AD](active-directory-azureadjoin-user-frx.md)
* [設定 Azure AD 與 Windows 10 裝置設定](active-directory-azureadjoin-user-upgrade.md)
* [個人 Windows 10 裝置加入至您的組織](active-directory-azureadjoin-personal-device.md)



## 在組織中針對 Windows 10 啟用 BYOD

您可以設定使用者和員工，使用其個人的 Windows 裝置來存取公司應用程式和資源。 您的使用者可藉由將其 Azure AD 帳戶 (工作帳戶) 新增到個人的 Windows 裝置，以安全且相容的方式來存取公司資源。

### 必要條件

**在企業層級 (系統管理員)**

*   Azure AD 訂用帳戶

**在使用者層級**

*   Windows 10 (Professional 和 Enterprise SKU)


### 系統管理員工作

* [設定裝置註冊](active-directory-azureadjoin-setup.md)

### 使用者工作

* [個人 Windows 10 裝置加入至您的組織](active-directory-azureadjoin-personal-device.md)


## 其他資訊

* [企業的 Windows 10: 工作中使用裝置的方式](active-directory-azureadjoin-windows10-devices-overview.md)
* [擴充 Windows 10 裝置透過 Azure Active Directory Join 的雲端功能](active-directory-azureadjoin-user-upgrade.md)
* [不需要密碼透過 Microsoft Passport 驗證身分識別](active-directory-azureadjoin-passport.md)
* [了解使用案例的 Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [已加入網域的裝置連接到 Azure AD Windows 10 的使用體驗](active-directory-azureadjoin-devices-group-policy.md)
* [設定 Azure AD Join](active-directory-azureadjoin-setup.md)





