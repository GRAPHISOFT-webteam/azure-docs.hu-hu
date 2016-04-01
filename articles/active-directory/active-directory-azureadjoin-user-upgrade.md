<properties 
    pageTitle="從 [設定] 使用 Azure AD 設定 Windows 10 裝置 | Microsoft Azure" 
    description="說明使用者如何透過 [設定] 功能表加入 Azure AD。" 
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

# 從 [設定] 使用 Azure AD 設定 Windows 10 裝置
如果您已在使用 Windows 7 或 8，而且您的電腦升級至 Windows 10，您可以透過 [設定] 功能表加入至 Azure AD。

從 [設定] 功能表加入至 Azure AD
-----------------------------------------------------------------------------------------------

1. 從 [開始] 功能表中，按一下 [設定] 常用鍵。
2. 從 [設定]-> [**系統**]-> [**有關**]-> [**加入 Azure AD**
<center>
![](./media/active-directory-azureadjoin/active-directory-azureadjoin-settings.png) </center>

3. 按一下 [ **繼續** Azure AD Join 訊息視窗上。
<center>
![](./media/active-directory-azureadjoin/active-directory-azureadjoin-message.png) </center>
4. 提供您的登入認證。 此登入體驗將會包含完成驗證所需的所有步驟。 如果您是同盟租用戶的一部分，您的系統管理員會提供由組織所主控的同盟體驗。
<center>
![](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png) </center>
5. 如果您的組織已設定加入至 Azure AD 的 Multi-Factor Authentication，您必須提供第二個因素才能繼續進行。
6. 按一下 [ **接受** 上 * * 允許此裝置來管理 * *] 畫面。
7. 您應該會看到此訊息「您的裝置現在已加入至您在 Azure AD 中的組織」。


## 其他資訊
* [了解適用於 Azure AD Join 的使用案例](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [將已加入網域的裝置連接到 Azure AD 以體驗 Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [設定 Azure AD Join](active-directory-azureadjoin-setup.md)
* [透過 Microsoft Passport 不需要密碼就能驗證身分識別](active-directory-azureadjoin-passport.md)
* [了解適用於 Azure AD Join 的使用案例](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [將已加入網域的裝置連接到 Azure AD 以體驗 Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [設定 Azure AD Join](active-directory-azureadjoin-setup.md)



