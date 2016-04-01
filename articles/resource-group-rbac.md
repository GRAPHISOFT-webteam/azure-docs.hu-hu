<properties
   pageTitle="管理資源的存取"
   description="使用角色型存取控制 (RBAC) 來管理部署至 Azure 之資源的使用者權限。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="AzurePortal"
   ms.workload="na"
   ms.date="11/28/2015"
   ms.author="tomfitz"/>

# 管理資源的存取

使用 Azure 資源管理員時，您可以確定您組織中的使用者有適當的權限來管理或存取資源。 資源管理員會利用角色型存取控制 (RBAC)，以便您可以很容地對個別資源或資源群組套用安全性原則。 例如，您可以授與使用者為訂用帳戶中特定虛擬機器的存取權，或讓使用者能夠管理訂用帳戶中的所有網站，但不包括其他資源。

本主題著重在您用來指派角色和權限的命令。 如需角色型存取控制的概觀，請參閱 [Microsoft Azure 入口網站中的角色型存取控制](active-directory/role-based-access-control-configure.md)。

## 概念

以下是幾個角色型存取控制的相關重要概念供您了解：

1. 主體 - 被授與權限的實體，例如 Azure Active Directory 使用者、安全性群組或應用程式。
2. 角色 - 一組允許和排除的動作。
3. 動作的動作執行對資源 （例如。 讀取、 建立）。 
4. 範圍 - 角色套用的層級，例如訂用帳戶、資源群組或資源。
5. 角色指派 - 將主體與角色建立關聯並設定範圍的程序。

您可以取得一份支援 **動作** 使用 Azure 跨平台 (Xplat-cli) CLI 工具或 Azure PowerShell 模組。

Azure 跨平台 CLI 工具，使用下列命令來列出 **所有** 的所有資源提供者的動作。

    azure provider operations show --operationSearchString '*';

如果您使用 Azure 資源管理員 PowerShell 模組 (AzureRm)，使用下列命令來列出 **所有** 動作，如 **所有** 資源提供者。

    Get-AzureRmProviderOperation -OperationSearchString *;

## 角色範例
若要了解 RBAC 的概念，我們來看一些常見的角色定義範例：

| 角色    | 允許的動作 |
| ------- | ----------------- |
| 讀取者  | * / 讀取 （讀取任何項目） |
| 擁有者   | * (讀取/寫入任何項目) |

若要指派 **讀取器** 角色，才能 **使用者 A** 名為資源群組的 **ExampleGroup**, ，和 **擁有者** 角色 **使用者 B** 整個訂閱中，您可以指派下列 ︰

| 角色    | 主體   | 範圍 |
| --------|-------------|---------- |
| 讀取者  | User A      | /subscriptions/{subscriptionId}/resourceGroups/examplegroup |
| 擁有者   | User B      | /subscriptions/{subscriptionId} |

## 範例案例

本主題中，您會看到如何針對 透過 Azure PowerShell、適用於 Mac、Linux 及 Windows 的 Azure CLI，以及 REST API，執行下列常見案例。

1. 檢視訂用帳戶中可用的角色，以及允許那些角色執行的動作。
2. 授與「讀取者」權限給整個訂用帳戶的群組成員。
3. 授與「參與者」權限給應用程式，以允許應用程式管理資源群組內的資源。
4. 授與特定網站的「擁有者」權限給單一使用者。
5. 列出資源群組的稽核記錄檔。


## 如何使用 PowerShell 管理存取權

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]


### 檢視可用的角色
若要檢視所有可用的角色執行訂閱 **Get AzureRmRoleDefinition** 命令。

    PS C:\> Get-AzureRmRoleDefinition
    
    Name             : API Management Service Contributor
    Id               : /subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{guid}
    IsCustom         : False
    Description      : Lets you manage API Management services, but not access to them.
    Actions          : {Microsoft.ApiManagement/Services/*, Microsoft.Authorization/*/read,
                       Microsoft.Resources/subscriptions/resourceGroups/read,
                       Microsoft.Resources/subscriptions/resourceGroups/resources/read...}
    NotActions       : {}
    AssignableScopes : {/}

    Name             : Application Insights Component Contributor
    Id               : /subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{guid}
    IsCustom         : False
    Description      : Lets you manage Application Insights components, but not access to them.
    Actions          : {Microsoft.Insights/components/*, Microsoft.Insights/webtests/*, Microsoft.Authorization/*/read,
                       Microsoft.Resources/subscriptions/resourceGroups/read...}
    NotActions       : {}
    AssignableScopes : {/}
    ...

### 授與「讀取者」權限給訂用帳戶的群組。
1. 檢閱 **讀取器** 執行時，提供角色名稱的角色定義 **Get AzureRmRoleDefinition** 命令。 檢查允許的動作是否是您要指派的動作。

        PS C:\> Get-AzureRmRoleDefinition Reader
   
        Name             : Reader
        Id               : /subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{guid}
        IsCustom         : False
        Description      : Lets you view everything, but not make any changes.
        Actions          : {*/read}
        NotActions       : {}
        AssignableScopes : {/}

2. 執行以取得必要的安全性群組 **Get AzureRmADGroup** 命令。 提供訂用帳戶中群組的實際名稱。 ExampleAuditorGroup 會顯示在下面。

        PS C:\> $group = Get-AzureRmAdGroup -SearchString ExampleAuditorGroup

3. 建立稽核員安全性群組的角色指派。 當命令完成時，會傳回新的角色指派。 

        PS C:\> New-AzureRmRoleAssignment -ObjectId $group.Id -Scope /subscriptions/{subscriptionId}/ -RoleDefinitionName Reader


###授與資源群組的「參與者」角色給應用程式。
1. 檢閱 **參與者** 執行時，提供角色名稱的角色定義 **Get AzureRmRoleDefinition** 命令。 檢查允許的動作是否是您要指派的動作。

        PS C:\> Get-AzureRmRoleDefinition Contributor

2. 取得所執行的服務主體物件識別碼 **Get AzureRmADServicePrincipal** 命令並提供您的訂閱中的應用程式名稱。 ExampleApplication 會顯示在下面。

        PS C:\> $service = Get-AzureRmADServicePrincipal -SearchString ExampleApplicationName

3. 建立服務主體的角色指派執行 **新增 AzureRmRoleAssignment** 命令。

        PS C:\> New-AzureRmRoleAssignment -ObjectId $service.Id -ResourceGroupName ExampleGroupName -RoleDefinitionName Contributor

設定 Azure Active Directory 應用程式和服務主體的更詳細說明，請參閱 [驗證服務主體與 Azure 資源管理員](resource-group-authenticate-service-principal.md)。

###授與資源的「擁有者」權限給使用者。
1. 檢閱 **擁有者** 執行時，提供角色名稱的角色定義 **Get AzureRmRoleDefinition** 命令。 檢查允許的動作是否是您要指派的動作。

        PS C:\> Get-AzureRmRoleDefinition Owner

2. 建立使用者的角色指派。

        PS C:\> New-AzureRmRoleAssignment -UserPrincipalName "someone@example.com" -ResourceGroupName {groupName} -ResourceType "Microsoft.Web/sites" -ResourceName "mysite" -RoleDefinitionName Owner


###列出資源群組的稽核記錄檔。
若要取得資源群組的稽核記錄檔，請執行 **Get AzureRmLog** 命令 (或 **Get-azureresourcegrouplog** PowerShell 1.0 預覽之前的版本)。

      PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroupName

## 如何使用適用於 Mac、Linux 和 Windows 的 Azure CLI

如果您尚未 Azure CLI for Mac，Linux 和 Windows 安裝或已設定您組織的帳戶來使用 Azure CLI，請參閱 [安裝和設定 Azure CLI](xplat-cli-install.md)。

1. 使用您的認證登入您的 Azure 帳戶。 此命令會傳回您登入的結果。

        azure login

        ...
        info:    login command OK

2. 如果您有多個訂用帳戶，請提供您想要用於部署的訂用帳戶識別碼。

        azure account set <YourSubscriptionNameOrId>

3. 切換至 Azure 資源管理員模組。 您會收到新模式的確認。

        azure config mode arm
        
        info:     New mode is arm

### 檢視可用的角色
檢視您訂用帳戶的所有可用角色。 它會傳回一份角色定義清單。

    azure role list

### 授與「讀取者」權限給訂用帳戶的群組。
1. 取得角色定義 **讀取器** 角色。 檢查允許的動作是否是您要指派的動作。

        azure role show Reader
        
        info:    Executing command role show
        + Getting role definitions
        data:    Name    Actions  NotActions
        data:    ------  -------  ----------
        data:    Reader  */read
        info:    role show command OK

2. 根據名稱搜尋群組，以取得必要的安全性群組及其 objectId。 就會如以下範例顯示 ExampleAuditorGroup。

        azure ad group show --search ExampleAuditorGroup
        
        info:    Executing command ad group show
        + Getting group list
        data:    Display Name:      ExampleAuditorGroup
        data:    ObjectId:          1c272299-9729-462a-8d52-7efe5ece0c5c
        data:    Security Enabled:  true
        data:    Mail Enabled:
        data:
        info:    ad group show command OK

3. 建立安全性群組的角色指派。

        azure role assignment create --objectId {group-object-id} -o Reader -c /subscriptions/{subscriptionId}/
        
        info:    Executing command role assignment create
        + Getting role definition id
        + Creating role assignment
        info:    role assignment create command OK


### 授與資源群組的「參與者」角色給應用程式。
1. 取得角色定義 **參與者** 角色。 檢查允許的動作是否是您要指派的動作。

        azure role show Contributor

2. 取得應用程式的 ObjectId。 提供要擷取之應用程式的名稱。

        azure ad sp show --search ExampleApplicationName

2. 建立應用程式的角色指派。

        azure role assignment create --ObjectId {service-principal-object-id} -o Contributor -c /subscriptions/{subscriptionId}/


###授與特定網站的「擁有者」權限給使用者。
1. 取得角色定義 **擁有者** 角色。 檢查允許的動作是否是您要指派的動作。

        azure role show Owner

2. 建立使用者的角色指派。

        azure role assignment create --mail "someone@example.com" -o Owner -c /subscriptions/{subscriptionId}/resourceGroups/{groupName}/providers/Microsoft.Web/sites/{mySiteName}


###列出資源群組的稽核記錄檔。
若要取得資源群組的稽核記錄檔，請執行 **azure 群組記錄檔顯示** 命令，並提供所需的資源群組的名稱。

         azure group log show ExampleGroupName


## 如何使用 REST API
若要透過 Azure 資源管理員 REST API 管理角色型存取控制，您必須在傳送要求時設定一般的標頭和參數 (包括驗證權杖) 。 如需資訊 [一般參數和標頭](https://msdn.microsoft.com/library/azure/dn906885.aspx)。
   
若要探索支援的 API 版本，請執行：

      GET https://management.azure.com/providers/Microsoft.Authorization?api-version=2015-01-01

您可以在本主題使用 `2014-10-01-preview` 版本。

###建立角色指派
取得可用的角色定義。

    GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions?api-version=2014-10-01-preview

建立角色指派。

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version=2014-10-01-preview
    {
      "properties": {
          "roleDefinitionId": "/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
          "principalId": "{principal-object-id}",
          "scope": "/subscriptions/{subscription-id}"
       }
    }


###刪除角色指派

      Delete  https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version=2014-10-01-preview


## 後續步驟

- 若要了解角色型存取控制的相關資訊，請參閱 [Microsoft Azure 入口網站中的角色型存取控制](role-based-access-control-configure.md)。
- 若要深入了解使用服務主體來管理您的訂閱中的應用程式的存取，請參閱 [驗證服務主體透過 Azure 資源管理員](resource-group-authenticate-service-principal.md) 和 [建立新的 Azure 服務主體，使用 Azure 傳統入口網站](../resource-group-create-service-principal-portal.md)。
- 若要深入了解稽核您的組織中的作業，請參閱 [稽核作業與資源管理員](resource-group-audit.md)。
- 您可以使用自訂原則，在訂用帳戶內套用限制和慣例。 如需詳細資訊，請參閱 [的使用原則來管理資源，並控制存取](resource-manager-policy.md)。

 


