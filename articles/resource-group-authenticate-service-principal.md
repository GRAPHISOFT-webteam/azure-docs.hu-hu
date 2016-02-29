<properties
   pageTitle="使用 Azure 資源管理員驗證服務主體"
   description="描述如何透過角色存取控制授與服務主體的存取權，並驗證服務主體。 顯示如何使用 PowerShell 和 Azure CLI 執行這些工作。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="11/18/2015"
   ms.author="tomfitz"/>

# 使用 Azure 資源管理員驗證服務主體

本主題顯示如何允許服務主體 (例如自動化程序、應用程式或服務) 存取您訂用帳戶中的其他資源。 使用 Azure 資源管理員，您可以使用角色存取控制將允許的動作授與服務主體，並驗證該服務主體。 本主題顯示如何使用 PowerShell 和 Azure CLI 將角色指派給服務主體，並驗證服務主體。

其示範如何使用使用者名稱與密碼或憑證進行驗證。

您可以使用 Mac、Linux 和 Windows 適用的 Azure PowerShell 或 Azure CLI。 如果您未安裝 Azure PowerShell，請參閱 [如何安裝和設定 Azure PowerShell](./powershell-install-configure.md)。 如果您未安裝 Azure CLI，請參閱 [安裝和設定 Azure CLI](xplat-cli-install.md)。 使用入口網站執行這些步驟的相關資訊，請參閱 [建立 Active Directory 應用程式和服務主體使用入口網站](resource-group-create-service-principal-portal.md)

## 概念
1. Azure Active Directory (AAD) - 雲端的身分識別與存取管理服務。 如需詳細資訊，請參閱 [什麼是 Azure active Directory](active-directory/active-directory-whatis.md)
2. 服務主體 - 必須存取其他資源之目錄中應用程式的執行個體。
3. AD 應用程式 - 向 AAD 識別應用程式的目錄記錄。 如需詳細資訊，請參閱 [基本概念的 Azure AD 中驗證](https://msdn.microsoft.com/library/azure/874839d9-6de6-43aa-9a5c-613b0c93247e#BKMK_Auth)。

## 使用密碼驗證服務主體 - PowerShell

在這一節中，您將執行相關步驟來建立 Azure Active Directory 應用程式的服務主體、指派角色給服務主體，並藉由提供應用程式識別碼和密碼驗證為服務主體。

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]

1. 登入您的帳戶。

        PS C:\> Login-AzureRmAccount

1. 建立新的 AAD 應用程式執行 **新增 AzureRmADApplication** 命令。 提供應用程式的顯示名稱、描述應用程式之頁面的 URI (未確認連結)、識別應用程式的 URI，以及應用程式身分識別的密碼。

        PS C:\> $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password "<Your_Password>"

     檢查新的應用程式物件。  **ApplicationId** 屬性用於建立服務主體、 角色指派以及取得 JWT 權杖。

        PS C:\> $azureAdApplication

        Type                    : Application
        ApplicationId           : a41acfda-d588-47c9-8166-d659a335a865
        ApplicationObjectId     : a26aaa48-bd52-4a7f-b29f-1bebf74c91e3
        AvailableToOtherTenants : False
        AppPermissions          : {{
                            "claimValue": "user_impersonation",
                            "description": "Allow the application to access <Your Application Display Name> on behalf of the signed-in user.",
                            "directAccessGrantTypes": [],
                            "displayName": "Access <Your Application Display Name>",
                            "impersonationAccessGrantTypes": [
                              {
                                "impersonated": "User",
                                "impersonator": "Application"
                              }
                            ],
                            "isDisabled": false,
                            "origin": "Application",
                            "permissionId": "b866ef28-9abb-4698-8c8f-eb4328533831",
                            "resourceScopeType": "Personal",
                            "userConsentDescription": "Allow the application to access <Your Application Display Name> on your behalf.",
                            "userConsentDisplayName": "Access <Your Application Display Name>",
                            "lang": null
                          }}


2. 建立應用程式的服務主體。

        PS C:\> New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

     您現在已在目錄中建立服務主體，但未將任何權限或範圍指派給服務。 您必須明確地授與服務主體權限，才能在某個範圍執行作業。

3. 授與服務主體對您訂用帳戶的權限。 在此範例中，您會將讀取訂用帳戶中所有資源的權限授與服務主體。 如 **ServicePrincipalName** 參數，會提供 **ApplicationId** 或 **IdentifierUris** 建立應用程式時使用。 如需有關以角色為基礎的存取控制的詳細資訊，請參閱 [管理和稽核資源存取權](resource-group-rbac.md)

        PS C:\> New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

4. 擷取用來建立角色指派的訂用帳戶。 此訂閱將會稍後用來取得 **TenantId** 所在租用戶的服務主體的角色指派中。

        PS C:\> $subscription = Get-AzureRmSubscription

     如果您將在訂用帳戶目前選取的訂閱中建立角色指派，您可以指定 **SubscriptoinId** 或 **SubscriptionName** 參數來擷取不同的訂閱。

5. 若要登入為服務主體透過 PowerShell，請建立新 **PSCredential** 物件，其中包含您的認證執行 **Get-credential** 命令。

        PS C:\> $creds = Get-Credential

     系統會提示您輸入認證。

     ![][1]

     使用的使用者名稱， **ApplicationId** 或 **IdentifierUris** 建立應用程式時使用。 針對密碼，使用您在建立帳戶時所指定的密碼。

     使用您輸入做為輸入的認證 **登入 AzureRmAccount** 指令程式，以服務主體登入:

        PS C:\> Login-AzureRmAccount -Credential $creds -ServicePrincipal -Tenant $subscription.TenantId
        Environment           : AzureCloud
        Account               : {guid}
        Tenant                : {guid}
        Subscription          : {guid}
        CurrentStorageAccount :

     您現在應該驗證為您所建立 AAD 應用程式的服務主體。

6. 若要從應用程式進行驗證，請包含下列 .NET 程式碼。 擷取權杖之後，您便可以存取訂用帳戶中的資源。

        public static string GetAccessToken()
        {
          var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantId or tenant name}");  
          var credential = new ClientCredential(clientId: "{application id}", clientSecret: "{application password}");
          var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", clientCredential:credential);

          if (result == null) {
            throw new InvalidOperationException("Failed to obtain the JWT token");
          }

          string token = result.AccessToken;

          return token;
        }



## 使用憑證驗證服務主體 - PowerShell

在本節中，您將執行步驟來建立服務主體的 Azure Active Directory 應用程式，將角色指派給服務主體，以服務主體的驗證 
提供憑證。 本主題假設您已簽發憑證。

其顯示兩種方法來使用憑證 - 金鑰認證和金鑰值。 您可以使用其中一種方法。

首先，您必須在 PowerShell 中設定一些值，以便稍後建立應用程式時使用。

1. 登入您的帳戶。

        PS C:\> Login-AzureRmAccount

1. 針對這兩種方法，從您的憑證建立 X509Certificate 物件並擷取金鑰值。 使用憑證的路徑以及該憑證的密碼。

        $cert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate -ArgumentList @("C:\certificates\examplecert.pfx", "yourpassword")
        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

2. 如果使用金鑰認證，請建立金鑰認證物件並將其值設為上一個步驟中的 `$keyValue`。

        $currentDate = Get-Date
        $endDate = $currentDate.AddYears(1)
        $keyId = [guid]::NewGuid()
        $keyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
        $keyCredential.StartDate = $currentDate
        $keyCredential.EndDate= $endDate
        $keyCredential.KeyId = $keyId
        $keyCredential.Type = "AsymmetricX509Cert"
        $keyCredential.Usage = "Verify"
        $keyCredential.Value = $keyValue

3. 在目錄中建立應用程式。 第一個命令會顯示如何使用金鑰值。

        $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert       
        
    或者，使用第二個範例來指派金鑰認證。

         $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyCredentials $keyCredential

    檢查新的應用程式物件。  **ApplicationId** 屬性用於建立服務主體、 角色指派以及取得 JWT 權杖。

        PS C:\> $azureAdApplication

        Type                    : Application
        ApplicationId           : 76fa8d97-f07e-4b9a-b871-a57a7acd777a
        ApplicationObjectId     : c36b7b57-a949-4401-b381-18a5210aff10
        AvailableToOtherTenants : False
        AppPermissions          : {{
                            "claimValue": "user_impersonation",
                            "description": "Allow the application to access <Your Application Display Name> on behalf of the signed-in
                          user.",
                            "directAccessGrantTypes": [],
                            "displayName": "Access <Your Application Display Name>",
                            "impersonationAccessGrantTypes": [
                              {
                                "impersonated": "User",
                                "impersonator": "Application"
                              }
                            ],
                            "isDisabled": false,
                            "origin": "Application",
                            "permissionId": "9f13c6c6-35ba-43d6-b8b3-6a87aa641388",
                            "resourceScopeType": "Personal",
                            "userConsentDescription": "Allow the application to access <Your Application Display Name> on your behalf.",
                            "userConsentDisplayName": "Access <Your Application Display Name>",
                            "lang": null
                          }}

4. 建立應用程式的服務主體。

        PS C:\> New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    您現在已在目錄中建立服務主體，但未將任何權限或範圍指派給服務。 您必須明確地授與服務主體權限，才能在某個範圍執行作業。

5. 授與服務主體對您訂用帳戶的權限。 在此範例中，您會將讀取訂用帳戶中所有資源的權限授與服務主體。 如 **ServicePrincipalName** 參數，會提供 **ApplicationId** 或 **IdentifierUris** 建立應用程式時使用。 如需有關以角色為基礎的存取控制的詳細資訊，請參閱 [管理和稽核資源存取權](resource-group-rbac.md)

        PS C:\> New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

6. 若要從應用程式進行驗證，請包含下列 .NET 程式碼。 擷取用戶端之後，您可以存取訂用帳戶中的資源。

        string clientId = "<Application ID for your AAD app>"; 
        var subscriptionId = "<Your Azure SubscriptionId>"; 
        string tenant = "<Tenant id or tenant name>"; 

        var authContext = new AuthenticationContext(string.Format("https://login.windows.net/{0}", tenant)); 

        X509Certificate2 cert = null; 
        X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser); 
        string certName = "examplecert"; 
        try 
        { 
            store.Open(OpenFlags.ReadOnly); 
            var certCollection = store.Certificates; 
            var certs = certCollection.Find(X509FindType.FindBySubjectName, certName, false); 
            cert = certs[0]; 
        } 
        finally 
        { 
            store.Close(); 
        }        

        var certCred = new ClientAssertionCertificate(clientId, cert); 
        var token = authContext.AcquireToken("https://management.core.windows.net/", certCred); 
        var creds = new TokenCloudCredentials(subscriptionId, token.AccessToken); 
        var client = new ResourceManagementClient(creds); 
        

## 使用密碼驗證服務主體 - Azure CLI

您將從建立服務主體開始。 若要這麼做，我們必須在目錄中建立應用程式。 本節將逐步引導如何在目錄中建立新的應用程式。

1. 切換至 Azure 資源管理員模式，並登入您的帳戶。

        azure config mode arm
        azure login

2. 建立新的 AAD 應用程式執行 **azure ad 應用程式建立** 命令。 提供應用程式的顯示名稱、描述應用程式之頁面的 URI (未確認連結)、識別應用程式的 URI，以及應用程式身分識別的密碼。

        azure ad app create --name "<Your Application Display Name>" --home-page "<https://YourApplicationHomePage>" --identifier-uris "<https://YouApplicationUri>" --password <Your_Password>
        
    傳回 Azure AD 應用程式。 需要有 ApplicationId 屬性，才能建立服務主體、角色指派以及取得 JWT 權杖。 

        info:    Executing command ad app create
        + Creating application exampleapp                                                
        data:    Application Id:          b57dd71d-036c-4840-865e-23b71d8098ec
        data:    Application Object Id:   d5c519e2-6149-447e-b323-88d2c4ea27de
        data:    Application Permissions:  
        data:                             claimValue:  user_impersonation
        data:                             description:  Allow the application to access exampleapp on behalf of the signed-in user.
        ...
        info:    ad app create command OK

3. 建立應用程式的服務主體。 提供在上一個步驟中傳回的應用程式識別碼。

        azure ad sp create b57dd71d-036c-4840-865e-23b71d8098ec
        
    傳回新的服務主體。 授與權限時，需要物件識別碼。
    
        info:    Executing command ad sp create
        + Creating service principal for application b57dd71d-036c-4840-865e-23b71d8098ec
        data:    Object Id:               47193a0a-63e4-46bd-9bee-6a9f6f9c03cb
        data:    Display Name:            exampleapp
        ...
        info:    ad sp create command OK

    您現在已在目錄中建立服務主體，但未將任何權限或範圍指派給服務。 您必須明確地授與服務主體權限，才能在某個範圍執行作業。

4. 授與服務主體對您訂用帳戶的權限。 在此範例中，您會將讀取訂用帳戶中所有資源的權限授與服務主體。 如 **ServicePrincipalName** 參數，會提供 **ApplicationId** 或 **IdentifierUris** 建立應用程式時使用。 如需有關以角色為基礎的存取控制的詳細資訊，請參閱 [管理和稽核資源存取權](resource-group-rbac.md)

        azure role assignment create --objectId 47193a0a-63e4-46bd-9bee-6a9f6f9c03cb -o Reader -c /subscriptions/{subscriptionId}/

5. 判斷 **TenantId** 所在租用戶的服務主體的角色指派所列出的帳戶，並尋找 **TenantId** 在輸出中的屬性。

        azure account list --json

6. 使用服務主體做為您的身分識別來進行登入。 使用的使用者名稱， **ApplicationId** 建立應用程式時使用。 針對密碼，使用您在建立帳戶時所指定的密碼。

        azure login -u "<ApplicationId>" -p "<password>" --service-principal --tenant "<TenantId>"

    您現在應該驗證為您所建立 AAD 應用程式的服務主體。

## 使用憑證驗證服務主體 - PowerShell - Azure CLI

在本節中，您要執行的步驟是為 Azure Active Directory 應用程式建立可使用憑證進行驗證的服務主體。 
本主題假設您已發出憑證，並可 [OpenSSL](http://www.openssl.org/) 安裝。

1. 建立 **.pem** 檔案中使用:

        openssl.exe pkcs12 -in examplecert.pfx -out examplecert.pem -nodes

2. 開啟 **.pem** 檔案，然後複製憑證資料。

3. 建立新的 AAD 應用程式執行 **azure ad 應用程式建立** 命令，並提供您在上一個步驟，做為索引鍵值中複製的憑證資料。

        azure ad app create -n "<your application name>" --home-page "<https://YourApplicationHomePage>" -i "<https://YouApplicationUri>" --key-value <certificate data>

## 後續步驟
  
- 角色型存取控制的概觀，請參閱 [管理和稽核資源存取權](resource-group-rbac.md)  
- 若要了解如何使用入口網站與服務主體，請參閱 [建立新的 Azure 服務主體，使用 Azure 入口網站](./resource-group-create-service-principal-portal.md)  
- 如需實作 Azure 資源管理員安全性的指引，請參閱 [Azure 資源管理員的安全性考量](best-practices-resource-manager-security.md)


<!-- Images. -->
[1]: ./media/resource-group-authenticate-service-principal/arm-get-credential.png

