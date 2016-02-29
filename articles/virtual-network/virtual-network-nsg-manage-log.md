<properties 
   pageTitle="監視 NSG 的作業、事件和計數器 | Microsoft Azure"
   description="了解如何啟用 NSG 的計數器、事件和作業記錄"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />

#網路安全性群組 (NSG) 的記錄檔分析

您可以在 Azure 中使用不同類型的記錄檔來管理和疑難排解 NSG。 透過入口網站可以存取其中一些記錄檔，而從 Azure Blob 儲存體可以擷取所有記錄檔並且在不同的工具中進行檢視，例如 Excel 和 PowerBI。 您可以從下列清單進一步了解不同類型的記錄檔。

- **稽核記錄檔:** 您可以使用 [Azure 稽核記錄檔](insights-debugging-with-events.md) (之前稱為操作記錄檔) 若要檢視所有作業提交給您的 Azure 訂閱和其狀態。 預設會啟用稽核記錄檔，並可在 Azure Preview 入口網站中進行檢視。
- **事件記錄檔:** 您可以使用此記錄檔，以檢視哪些 NSG 規則會套用至 Vm 和 MAC 位址為基礎的執行個體角色。 每隔 60 秒會收集一次這些規則的狀態。 
- **計數器記錄檔:** 您可以使用此記錄檔來檢視每個 NSG 規則套用到拒絕或允許流量的次數。

>[AZURE.WARNING] 記錄檔只可供部署在資源管理員部署模型中的資源。 您無法將記錄檔使用於傳統部署模型中的資源。 進一步了解的兩個模型，參考 [瞭解資源管理員部署和傳統部署](resource-manager-deployment-model.md) 文件。

##啟用記錄
每個資源管理員資源都會隨時自動啟用稽核記錄。 您需要啟用事件和計數器記錄，才能開始收集可透過這些記錄檔取得的資料。 若要啟用記錄，請遵循下列步驟。 

1.  登入 [Azure 預覽入口網站](http://portal.azure.com)。 如果您還沒有現有的網路安全性群組， [建立 NSG](virtual-networks-create-nsg-arm-ps.md) 在繼續之前。 

2.  在預覽入口網站中，按一下 [ **瀏覽** >> **網路安全性群組**。

    ![Preview 入口網站 - 網路安全性群組](./media/virtual-network-nsg-manage-log/portal-enable1.png)

3. 選取現有的網路安全性群組。

    ![Preview 入口網站 - 網路安全性群組設定](./media/virtual-network-nsg-manage-log/portal-enable2.png)

4. 在 **設定** 刀鋒視窗中，按一下 [ **診斷**, ，然後在 **診斷** 窗格中下, 一步] **狀態**, ，按一下 [ **上**
5. 在 **設定** 刀鋒視窗中，按一下 [ **儲存體帳戶**, ，然後選取現有的儲存體帳戶，或建立一個新。  

>[AZURE.INFORMATION] 稽核記錄檔不需要個別的儲存體帳戶。 將儲存體用於事件和規則記錄將會產生服務費用。

6. 在下拉式清單正下方 **儲存體帳戶**, ，選取是否要記錄的事件、 計數器，或兩者，然後按一下 [ **儲存**。

    ![Preview 入口網站 - 診斷記錄檔](./media/virtual-network-nsg-manage-log/portal-enable3.png)

## 稽核記錄檔
此記錄檔 (之前稱為「作業記錄檔」) 預設是由 Azure 產生。  記錄檔會在 Azure 的 [事件記錄檔] 存放區中保留 90 天。 深入了解這些記錄檔讀取 [檢視事件和稽核記錄檔](insights-debugging-with-events.md) 文件。

## 計數器記錄檔
如果您已如上所述對每一個 NSG 進行啟用，才會產生此記錄檔。 資料會儲存在您啟用記錄時所指定的儲存體帳戶中。 套用至資源的每個規則會以 JSON 格式記錄，如下所示。

    {
        "time": "2015-09-11T23:14:22.6940000Z",
        "systemId": "e22a0996-e5a7-4952-8e28-4357a6e8f0c5",
        "category": "NetworkSecurityGroupRuleCounter",
        "resourceId": "/SUBSCRIPTIONS/D763EE4A-9131-455F-8C5E-876035455EC4/RESOURCEGROUPS/INSIGHTOBONRP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/NSGINSIGHTOBONRP",
        "operationName": "NetworkSecurityGroupCounters",
        "properties": {
            "vnetResourceGuid":"{DD0074B1-4CB3-49FA-BF10-8719DFBA3568}",
            "subnetPrefix":"10.0.0.0/24",
            "macAddress":"001517D9C43C",
            "ruleName":"DenyAllOutBound",
            "direction":"Out",
            "type":"block",
            "matchedConnections":0
            }
    }

## 事件記錄檔
如果您已如上所述對每一個 NSG 進行啟用，才會產生此記錄檔。 資料會儲存在您啟用記錄時所指定的儲存體帳戶中。 會記錄下列資料：

    {
        "time": "2015-09-11T23:05:22.6860000Z",
        "systemId": "e22a0996-e5a7-4952-8e28-4357a6e8f0c5",
        "category": "NetworkSecurityGroupEvent",
        "resourceId": "/SUBSCRIPTIONS/D763EE4A-9131-455F-8C5E-876035455EC4/RESOURCEGROUPS/INSIGHTOBONRP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/NSGINSIGHTOBONRP",
        "operationName": "NetworkSecurityGroupEvents",
        "properties": {
            "vnetResourceGuid":"{DD0074B1-4CB3-49FA-BF10-8719DFBA3568}",
            "subnetPrefix":"10.0.0.0/24",
            "macAddress":"001517D9C43C",
            "ruleName":"AllowVnetOutBound",
            "direction":"Out",
            "priority":65000,
            "type":"allow",
            "conditions":{
                "destinationPortRange":"0-65535",
                "sourcePortRange":"0-65535",
                "destinationIP":"10.0.0.0/8,172.16.0.0/12,169.254.0.0/16,192.168.0.0/16,168.63.129.16/32",
                "sourceIP":"10.0.0.0/8,172.16.0.0/12,169.254.0.0/16,192.168.0.0/16,168.63.129.16/32"
            }
        }
    }

##檢視和分析稽核記錄檔
您可以使用下列任何方法，檢視和分析稽核記錄檔資料：

- **Azure 工具:** 透過 Azure PowerShell、 Azure 命令列介面 (CLI)、 Azure REST API 或 Azure 預覽入口網站的稽核記錄檔中擷取資訊。  每個方法的逐步指示所述 [稽核作業與資源管理員](resource-group-audit.md) 文件。
- **Power BI:** 如果還沒有 [Power BI](https://powerbi.microsoft.com/pricing) 帳戶，您可以免費試用。 使用 [Azure 稽核記錄檔內容組件的 Power BI](https://support.powerbi.com/knowledgebase/articles/742695) 可以分析您的資料，您可以使用做為預先設定的儀表板-，或自訂。

##檢視和分析計數器和事件記錄檔 
您需要連接到儲存體帳戶並擷取事件和計數器記錄檔的 JSON 記錄項目。 下載 JSON 檔案後，您可以將它們轉換成 CSV 並在 Excel、PowerBI 或任何其他資料視覺化工具中檢視。

>[AZURE.TIP] 如果您熟悉 Visual Studio 和變更值的常數和變數在 C# 中的基本概念，您可以使用 [記錄轉換程式工具](https://github.com/Azure-Samples/networking-dotnet-log-converter) 可以從 Github。

##其他資源

- [以視覺化方式檢視您的 Azure 稽核記錄檔與 Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) 部落格文章。
- [檢視及分析 Power BI 與多個 Azure 稽核記錄檔](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) 部落格文章。

