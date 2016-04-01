<properties
    pageTitle="我的 MVC 專案 (Visual Studio Azure Active Directory 已連線服務) 發生什麼事 | Microsoft Azure "
    description="說明您使用 Visual Studio 已連線服務連線至 Azure AD 時，MVC 專案會有何狀況"
    services="active-directory"
    documentationCenter="na"
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

# 我的 MVC 專案 (Visual Studio Azure Active Directory 已連線服務) 發生什麼事？

> [AZURE.SELECTOR]
> - [開始使用](vs-active-directory-dotnet-getting-started.md)
> - [發生什麼情形](vs-active-directory-dotnet-what-happened.md)



## 已加入參考

### NuGet 封裝參考

- **Microsoft.IdentityModel.Protocol.Extensions**
- **Microsoft.Owin**
- **Microsoft.Owin.Host.SystemWeb**
- **Microsoft.Owin.Security**
- **Microsoft.Owin.Security.Cookies**
- **Microsoft.Owin.Security.OpenIdConnect**
- **Owin**
- **System.IdentityModel.Tokens.Jwt**

### .NET 參考

- **Microsoft.IdentityModel.Protocol.Extensions**
- **Microsoft.Owin**
- **Microsoft.Owin.Host.SystemWeb**
- **Microsoft.Owin.Security**
- **Microsoft.Owin.Security.Cookies**
- **Microsoft.Owin.Security.OpenIdConnect**
- **Owin**
- **System.IdentityModel**
- **System.IdentityModel.Tokens.Jwt**
- **System.Runtime.Serialization**

## 已加入程式碼

### 程式碼檔案加入至專案

驗證啟動類別， **App_Start/Startup.Auth.cs** 已加入至專案，內含 Azure AD 驗證的啟動邏輯。 此外，其中包含加入控制器類別 controllers/Accountcontroller.cs **signin （)** 和 **signout （)** 方法。 最後，部分檢視 **Views/Shared/_LoginPartial.cshtml** 起，內含 SignIn/signout 的動作連結。

### 啟動程式碼已加入至專案

如果您的專案中已有啟動類別 **組態** 方法已更新為包含呼叫 **Configureauth**。 否則已將啟動類別加入至專案。

### app.config 或 web.config 有新的組態值

已加入下列組態項目。


    <appSettings>
        <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="The selected Azure AD Domain" />
        <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
        <add key="ida:PostLogoutRedirectUri" value="Your project start page" />
    </appSettings>

### 建立 Azure Active Directory (AD) 應用程式
已在您於精靈中選取的目錄中建立 Azure AD 應用程式。

##如果我核取 *停用個別使用者帳戶驗證*, ，我的專案已進行哪些其他的變更？
NuGet 封裝參考會被移除，檔案也會移除並加以備份。 根據您的專案狀態，您可能必須手動移除其他參考或檔案，或修改為適當的程式碼。

### 移除的 NuGet 封裝參考 (如果存在)

- **Microsoft.AspNet.Identity.Core**
- **Microsoft.AspNet.Identity.EntityFramework**
- **Microsoft.AspNet.Identity.Owin**

### 備份和移除的程式碼檔案 (如果存在)

下列每個檔案都會備份，並且從專案中移除。 備份檔案位於專案目錄根目錄的「備份」資料夾。

- **App_Start\IdentityConfig.cs**
- **Controllers\ManageController.cs**
- **Models\IdentityModels.cs**
- **Models\ManageViewModels.cs**

### 備份的程式碼檔案 (如果存在)

下列每個檔案會在取代之前備份。 備份檔案位於專案目錄根目錄的「備份」資料夾。

- **Startup.cs**
- **App_Start\Startup.Auth.cs**
- **Controllers\AccountController.cs**
- **Views\Shared\_LoginPartial.cshtml**

## 如果我核取 *讀取目錄資料*, ，我的專案已進行哪些其他的變更？

已加入其他參考。

###其他 NuGet 封裝參考

- **EntityFramework**
- **Microsoft.Azure.ActiveDirectory.GraphClient**
- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.IdentityModel.Clients.ActiveDirectory**
- **System.Spatial**

###其他 .NET 參考

- **EntityFramework**
- **EntityFramework.SqlServer**
- **Microsoft.Azure.ActiveDirectory.GraphClient**
- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.IdentityModel.Clients.ActiveDirectory**
- **Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms**
- **System.Spatial**

###其他程式碼檔案已加入至專案

兩個檔案已加入以支援權杖快取 ︰ **Models\ADALTokenCache.cs** 和 **Models\ApplicationDbContext.cs**。  已加入其他控制器和檢視，以說明使用 Azure 圖形 API 存取使用者設定檔資訊。  這些檔案是 **Controllers\UserProfileController.cs** 和 **Views\UserProfile\Index.cshtml**。

###其他啟動程式碼已加入至專案

在 **startup.auth.cs** 檔案中，新 **OpenIdConnectAuthenticationNotifications** 物件已新增至 **通知** 成員 **OpenIdConnectAuthenticationOptions**。  這是為了啟用接收 OAuth 程式碼，並用它交換存取權杖。

###app.config 或 web.config 已進行其他變更

已加入下列其他組態項目。

    <appSettings>
        <add key="ida:ClientSecret" value="Your Azure AD App's new client secret" />
    </appSettings>

已加入下列組態區段和連接字串。

    <configSections>
        <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
    </configSections>
    <connectionStrings>
        <add name="DefaultConnection" connectionString="Data Source=(localdb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\aspnet-[AppName + Generated Id].mdf;Initial Catalog=aspnet-[AppName + Generated Id];Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>
    <entityFramework>
        <defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
          <parameters>
            <parameter value="mssqllocaldb" />
          </parameters>
        </defaultConnectionFactory>
        <providers>
          <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
        </providers>
    </entityFramework>


###Azure Active Directory 應用程式已更新
Azure Active Directory 應用程式已更新為包含 *讀取目錄資料* 使用權限和其他的登錄機碼已建立其再做使用 *ida: ClientSecret* 中 **web.config** 檔案。

[深入了解 Azure Active Directory](http://azure.microsoft.com/services/active-directory/)


