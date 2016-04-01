<properties 
    pageTitle="使用資源管理員來稽核作業 |Microsoft Azure" 
    description="在 [資源管理員] 中使用稽核記錄檔，以檢閱使用者動作和錯誤。 顯示 PowerShell、Azure CLI 和 REST。" 
    services="azure-resource-manager" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="azure-resource-manager" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/02/2015" 
    ms.author="tomfitz"/>

# 使用資源管理員來稽核作業

當您在部署期間或在方案的存留期間遇到問題，您需要了解發生什麼問題。 [資源管理員] 提供兩種方法讓您了解發生什麼情形以及其發生原因。 
您可以使用部署命令來擷取有關特定部署和作業的資訊。 或者，您可以使用稽核記錄檔來擷取部署和其他動作的相關資訊 
取得方案的存留期間。 本主題著重於稽核記錄檔。 

稽核記錄檔包含在您的資源上執行的所有動作。 所以，如果您組織中的使用者修改資源，您將能夠識別此動作、時間和使用者。

使用稽核記錄時，有兩個重要限制需謹記在心：

1. 稽核記錄只會保留 90 天。
2. 您只能查詢 15 天內的範圍。

您可以透過 Azure PowerShell、Azure CLI、REST API 或 Azure 入口網站，擷取稽核記錄檔中的資訊。

## PowerShell

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]

若要擷取的記錄項目，請執行 **Get AzureRmLog** 命令 (或 **Get-azureresourcegrouplog** PowerShell 版本早於 1.0)。 您可提供額外的參數來篩選項目清單。 

下列範例示範如何使用稽核記錄檔來研究在方案存留期間所採取的動作。 您可以看到此動作的發生時間以及提出要求者。 以日期格式指定開始和結束日期。

    PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00

或者，您可以使用日期函數來指定日期範圍，例如過去 15 天。

    PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-15)

視您指定的開始時間而定，先前的命令可以傳回該資源群組的一長串動作。 您可以提供搜尋準則，以篩選您所尋找的結果。 例如，如果您
會嘗試研究 web 應用程式已停止您可以執行下列命令，請參閱 someone@example.com 在執行停止動作。

    PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-15) | Where-Object OperationName -eq Microsoft.Web/sites/stop/action

    Authorization     :
                        Scope     : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
                        Action    : Microsoft.Web/sites/stop/action
                        Role      : Subscription Admin
                        Condition :
    Caller            : someone@example.com
    CorrelationId     : 84beae59-92aa-4662-a6fc-b6fecc0ff8da
    EventSource       : Administrative
    EventTimestamp    : 8/28/2015 4:08:18 PM
    OperationName     : Microsoft.Web/sites/stop/action
    ResourceGroupName : ExampleGroup
    ResourceId        : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
    Status            : Succeeded
    SubscriptionId    : xxxxx
    SubStatus         : OK

在下一個範例中，我們將只尋找在指定的開始時間後的失敗動作。 我們也會包含 **DetailedOutput** 參數，以查看錯誤訊息。

    PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-15) -Status Failed –DetailedOutput
    
如果這個命令會傳回太多的項目和屬性，您可以專注稽核藉由擷取 **屬性** 屬性。

    PS C:\> (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties

    Content
    -------
    {}
    {[statusCode, Conflict], [statusMessage, {"Code":"Conflict","Message":"Website with given name mysite already exists...
    {[statusCode, Conflict], [serviceRequestId, ], [statusMessage, {"Code":"Conflict","Message":"Website with given name...

而且，您可藉由查看狀態訊息進一步精簡結果。

    PS C:\> (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties[1].Content["statusMessage"] | ConvertFrom-Json

    Code       : Conflict
    Message    : Website with given name mysite already exists.
    Target     :
    Details    : {@{Message=Website with given name mysite already exists.}, @{Code=Conflict}, @{ErrorEntity=}}
    Innererror :


## Azure CLI

若要擷取的記錄項目，執行 **azure 群組記錄檔顯示** 命令。

    azure group log show ExampleGroup

您可以篩選使用 JSON 公用程式的結果，例如 [jq](http://stedolan.github.io/jq/download/)。 下列範例示範如何尋找涉及更新 Web 組態檔的作業。

    azure group log show ExampleGroup --json | jq ".[] | select(.operationName.localizedValue == \"Update web sites config\")"

您可以加入 **--上次-部署** 參數來限制傳回的項目只有作業的最後一個部署。

    azure group log show ExampleGroup --last-deployment

如果上次部署的作業清單太長，您可以只篩選失敗作業的結果。

    azure group log show tfCopyGroup --last-deployment --json | jq ".[] | select(.status.value == \"Failed\")"

                                   /Microsoft.Web/Sites/ExampleSite
    data:    SubscriptionId:       <guid>
    data:    EventTimestamp (UTC): Thu Aug 27 2015 13:03:27 GMT-0700 (Pacific Daylight Time)
    data:    OperationName:        Update website
    data:    OperationId:          cb772193-b52c-4134-9013-673afe6a5604
    data:    Status:               Failed
    data:    SubStatus:            Conflict (HTTP Status Code: 409)
    data:    Caller:               someone@example.com
    data:    CorrelationId:        a8c7a2b4-5678-4b1b-a50a-c17230427b1e
    data:    Description:
    data:    HttpRequest:          clientRequestId: <guid>
                                   clientIpAddress: 000.000.000.000
                                   method:          PUT

    data:    Level:                Error
    data:    ResourceGroup:        ExampleGroup
    data:    ResourceProvider:     Azure Web Sites
    data:    EventSource:          Administrative
    data:    Properties:           statusCode:       Conflict
                                   serviceRequestId:
                                   statusMessage:    {"Code":"Conflict","Message":"Website with given name
                                   ExampleSite already exists.","Target":null,"
                                   Details
                                   ":[{"Message":"Website with given name ExampleSite already exists."},
                                   {"Code":"Conflict"},
                                   {"ErrorEntity":{"Code":"Conflict","Message":"Website with given
                                   name ExampleSite already exists.","ExtendedCode
                                   ":"
                                   54001","MessageTemplate":"Website with given name {0} already exists.",
                                   "Parameters":["ExampleSite"],"
                                   InnerErrors":null}}],"Innererror":null}



## REST API

使用稽核記錄檔的 REST 作業屬於 [Insights REST API](https://msdn.microsoft.com/library/azure/dn931943.aspx)。 若要擷取稽核記錄檔事件，請參閱 [列出訂閱中的管理事件](https://msdn.microsoft.com/library/azure/dn931934.aspx)。

## 入口網站

您也可以透過入口網站檢視已記錄的作業。 只需選取稽核記錄檔刀鋒視窗。

![選取稽核記錄檔](./media/resource-group-audit/select-audit.png) 

並且，檢視最新的作業清單。

![顯示動作](./media/resource-group-audit/show-actions.png)

您可以選取任何作業，以取得更多詳細資訊。

## 後續步驟

- 若要深入了解設定安全性原則，請參閱 [管理資源的存取權](resource-group-rbac.md)。
- 若要深入了解授與存取權的服務主體，請參閱 [驗證服務主體與 Azure 資源管理員](resource-group-authenticate-service-principal.md)。
- 若要了解如何為所有使用者資源上的動作，請參閱 [鎖定的資源與 Azure 資源管理員](resource-group-lock-resources.md)。


