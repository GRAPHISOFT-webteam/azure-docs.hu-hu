## 準備驗證資源管理員要求

您必須驗證您使用的資源執行的所有作業 [Azure 資源管理員][lnk-authenticate-arm] 與 Azure Active Directory (AD)。 最簡單的設定方式是使用 PowerShell 或 Azure CLI。

如果您尚未安裝 [Azure PowerShell 1.0][lnk-powershell-install], ，即可使用下列 PowerShell 命令。 您必須以系統管理員身分執行 PowerShell。

```
# Install the Azure Resource Manager modules from PowerShell Gallery
Install-Module AzureRM
Install-AzureRM

# Install the Azure Service Management module from PowerShell Gallery
Install-Module Azure
```

下列步驟示範如何使用 PowerShell 設定 AD 應用程式的密碼驗證。 您可以在標準 PowerShell 工作階段中執行這些命令。

1. 使用下列命令來登入您的 Azure 訂用帳戶：

    ```
    Login-AzureRmAccount
    ```

2. 請記下的您 **TenantId** 和 **SubscriptionId**。 稍後您將需要這些資訊。

3. 使用下列命令並取代預留位置，以建立新的 Azure Active Directory 應用程式：

    - **{顯示名稱}:** 的顯示名稱，您的應用程式，例如 **MySampleApp**
    - **{首頁 URL}:** 您的應用程式，例如在首頁的 URL **http://mysampleapp/home**。 此 URL 不需要指向實際的應用程式。
    - **{應用程式識別碼}:** 的唯一識別碼，例如 **http://mysampleapp**。 此 URL 不需要指向實際的應用程式。
    - **{Password}:** 要用來驗證您的應用程式使用的密碼。

    ```
    New-AzureRmADApplication -DisplayName {Display name} -HomePage {Home page URL} IdentifierUris {Application identifier} -Password {Password}
    ```
    
4. 請記下的 **ApplicationId** 您所建立的應用程式。 稍後您將會需要此資訊。

5. 建立新的服務主體使用下列命令，取代 **{MyApplicationId}** 與 **ApplicationId** 上一個步驟:

    ```
    New-AzureRmADServicePrincipal -ApplicationId {MyApplicationId}
    ```
    
6. 安裝程式的角色指派，使用下列命令，取代 **{MyApplicationId}** 與您 **ApplicationId**。

    ```
    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```
    
您已建立可從自訂 C# 應用程式驗證的 Azure AD 應用程式。 在本教學課程後續的內容當中，您將需要以下各值：

- TenantId
- SubscriptionId
- ApplicationId
- 密碼

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: https://azure.microsoft.com/en-us/blog/azps-1-0-pre/

