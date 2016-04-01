<properties
    pageTitle="WebApi 專案發生什麼事 （Visual Studio Azure Active Directory 連線服務） |Microsoft Azure "
    description="描述在 MVC 專案使用 Visual Studio 服務連接到 Azure AD 的 WebApi 會發生什麼事 ="active-directory"
    services="active-directory"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor="tglee"/>

<tags
    ms.service="active-directory"
    ms.workload="web"
    ms.tgt_pltfrm="vs-what-happened"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/03/2015"
    ms.author="tarcher"/>

# 我的 WebApi 專案發生什麼狀況 (Visual Studio Azure Active Directory 已連接服務)

> [AZURE.SELECTOR]
> - [開始使用](vs-active-directory-webapi-getting-started.md)
> - [發生什麼情形](vs-active-directory-webapi-what-happened.md)

##已加入參考

###NuGet 封裝參考

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

###.NET 參考

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

##程式碼變更

###程式碼檔案加入至專案

驗證啟動類別， **App_Start/Startup.Auth.cs** 已加入至專案，內含 Azure AD 驗證的啟動邏輯。

###啟動程式碼已加入至專案

如果您的專案中已有啟動類別 **組態** 方法已更新為包含呼叫 `ConfigureAuth(app)`。 否則已將啟動類別加入至專案。


###app.config 或 web.config 檔案有新的組態值。

已加入下列組態項目。
```
    `<appSettings>
            <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
            <add key="ida:Tenant" value="Your selected Azure AD Tenant" />
            <add key="ida:Audience" value="The App ID Uri from the wizard" />
    </appSettings>`
```

###已建立 Azure AD 應用程式

已在您於精靈中選取的目錄中建立 Azure AD 應用程式。

[深入了解 Azure Active Directory](http://azure.microsoft.com/services/active-directory/)

##如果我核取 *停用個別使用者帳戶驗證*, ，我的專案已進行哪些其他的變更？
NuGet 封裝參考會被移除，檔案也會移除並加以備份。 根據您的專案狀態，您可能必須手動移除其他參考或檔案，或修改為適當的程式碼。

###移除的 NuGet 封裝參考 (如果存在)

- `Microsoft.AspNet.Identity.Core`
- `Microsoft.AspNet.Identity.EntityFramework`
- `Microsoft.AspNet.Identity.Owin`

###備份和移除的程式碼檔案 (如果存在)

下列每個檔案都會備份，並且從專案中移除。 備份檔案位於專案目錄根目錄的「備份」資料夾。

- `App_Start\IdentityConfig.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Providers\ApplicationOAuthProvider.cs`

###備份的程式碼檔案 (如果存在)

下列每個檔案會在取代之前備份。 備份檔案位於專案目錄根目錄的「備份」資料夾。

- `Startup.cs`
- `App_Start\Startup.Auth.cs`

##如果我核取 *讀取目錄資料*, ，我的專案已進行哪些其他的變更？

###app.config 或 web.config 已進行其他變更

已加入下列其他組態項目。

```
    `<appSettings>
        <add key="ida:Password" value="Your Azure AD App's new password" />
    </appSettings>`
```

###Azure Active Directory 應用程式已更新
Azure Active Directory 應用程式已更新為包含 *讀取目錄資料* 使用權限和其他的登錄機碼已建立其再做使用 *ida: Password* 中 `web.config` 檔案。

[深入了解 Azure Active Directory](http://azure.microsoft.com/services/active-directory/)


