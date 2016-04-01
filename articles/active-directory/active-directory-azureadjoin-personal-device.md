

<properties 
    pageTitle="將個人裝置加入至您的組織 | Microsoft Azure" 
    description="說明使用者如何將他們的個人 Windows 10 電腦註冊至其公司網路，並提供 BYOD 案例的部署步驟。" 
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

# 將個人裝置加入至您的組織

將 Windows 10 裝置加入至您的組織
--------------------------------------------------------------------------------------------
1.  從 **開始** 功能表上，選取 **設定**。 
2.  選取 **帳戶**, ，然後按一下 [ **您的帳戶**。
3.  按一下 [ **新增工作或學校帳戶**, ，然後輸入您的組織帳戶。
4.  接著您將會進入組織的登入頁面。 輸入您的使用者名稱和密碼，然後按一下 **確定**。
5.  接著系統會提示您 Multi-Factor Authentication 挑戰。 這可由 IT 人員進行設定。
6.  接著 Azure AD 會檢查這個使用者/裝置是否需要行動裝置管理 (MDM) 註冊。 
7.  接著 Windows 會在 Azure AD 的組織目錄中登錄裝置並在 MDM 中進行註冊。
8.  完成此動作後，如果您是受管理的使用者，Windows 會總結安裝程序並透過自動登入來引導使用者至桌面。
9.  如果您是同盟的使用者，您將會進入 Windows 登入畫面且必須輸入您的認證。

## 其他資訊
* [適合企業使用的 Windows 10：使用裝置的工作方式](active-directory-azureadjoin-windows10-devices-overview.md)
* [透過 Azure Active Directory Join 擴充 Windows 10 裝置的雲端功能](active-directory-azureadjoin-user-upgrade.md)
* [透過 Microsoft Passport 不需要密碼就能驗證身分識別](active-directory-azureadjoin-passport.md)
* [了解適用於 Azure AD Join 的使用案例](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [將已加入網域的裝置連接到 Azure AD 以體驗 Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [設定 Azure AD Join](active-directory-azureadjoin-setup.md)


