<properties
    pageTitle="使用 Azure 資源管理員範本建立具有監視和診斷的 Windows 虛擬機器 | Microsoft Azure"
    description="使用 Azure 資源管理員範本以建立具有 Azure 診斷延伸模組的新的 Windows 虛擬機器。"
    services="virtual-machines"
    documentationCenter=""
    authors="sbtron"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/15/2015"
    ms.author="saurabh"/>

# 使用 Azure 資源管理員範本建立具有監視和診斷的 Windows 虛擬機器

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] 傳統部署模型。 

Azure 診斷延伸模組提供以 Windows 為基礎的 Azure 虛擬機器上的監視和診斷功能。 您可以將延伸模組納入為 Azure 資源管理員範本的一部分，在虛擬機器上啟用這些功能。 請參閱 [與 VM 延伸模組撰寫 Azure 資源管理員範本](virtual-machines-extensions-authoring-templates.md) 如需有關包括任何延伸模組做為虛擬機器範本的一部分。 本文描述如何將 Azure 診斷延伸模組新增至 Windows 虛擬機器範本。  
  

## 將 Azure 診斷延伸模組新增至 VM 資源定義 

若要在 Windows 虛擬機器上啟用診斷延伸模組，您需要新增延伸模組做為資源管理員範本中的 VM 資源。

簡單資源管理員的基礎的虛擬機器加入至延伸模組組態 *資源* 虛擬機器的陣列: 

    "resources": [
                {
                    "name": "Microsoft.Insights.VMDiagnosticsSettings",
                    "type": "extensions",
                    "location": "[resourceGroup().location]",
                    "apiVersion": "2015-06-15",
                    "dependsOn": [
                        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
                    ],
                    "tags": {
                        "displayName": "AzureDiagnostics"
                    },
                    "properties": {
                        "publisher": "Microsoft.Azure.Diagnostics",
                        "type": "IaaSDiagnostics",
                        "typeHandlerVersion": "1.5",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
                            "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
                        },
                        "protectedSettings": {
                            "storageAccountName": "[parameters('existingdiagnosticsStorageAccountName')]",
                            "storageAccountKey": "[listkeys(variables('accountid'), '2015-05-01-preview').key1]",
                            "storageAccountEndPoint": "https://core.windows.net"
                        }
                    }
                }
            ]


另一個常見的慣例是在範本的根資源節點新增延伸模組組態，而不是在虛擬機器的資源節點底下定義。 您必須使用此方法時明確指定延伸模組，並使用的虛擬機器之間的階層式關聯性 *名稱* 和 *類型* 值。 例如： 
  
    "name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
    "type": "Microsoft.Compute/virtualMachines/extensions",

延伸模組一律與虛擬機器相關聯，您可以直接在虛擬機器的資源節點底下定義，或是在基底層級定義並且使用階層式命名慣例，將其與虛擬機器產生關聯。

針對虛擬機器擴充集合中指定的延伸模組組態 *extensionProfile* 屬性 *VirtualMachineProfile*。
   
 *發行者* 屬性的值與 **Microsoft.Azure.Diagnostics** 和 *類型* 屬性的值與 **IaaSDiagnostics** 唯一識別 Azure 診斷擴充功能。

值 *名稱* 屬性可用來參考的資源群組中的擴充功能。 將它設定專為 **Microsoft.Insights.VMDiagnosticsSettings** 可讓使用者輕易識別確保，監視圖表上顯示出正確 Azure 傳統入口網站中 Azure 傳統入口網站。

 *TypeHandlerVersion* 指定您想要使用延伸模組的版本。 設定 *autoUpgradeMinorVersion* 次要版本 **true** 可確保您獲得最新的次要版本所提供的延伸模組。 強烈建議您一律將 *autoUpgradeMinorVersion* 永遠是 **true** ，讓您自動取得最新可用的診斷延伸模組使用的所有新功能和 bug 修正。 

 *設定* 項目包含可設定及回讀取 (有時稱為公用組態) 的延伸模組的延伸模組的組態屬性。  *搜尋 [xmlcfg* 屬性包含以 xml 為基礎的診斷記錄檔中，組態等診斷代理程式所收集的效能計數器。 請參閱 [診斷組態結構描述](https://msdn.microsoft.com/library/azure/dn782207.aspx) 如需有關 xml 結構描述本身。 常見的作法是將實際的 xml 組態儲存為 Azure 資源管理員範本中的變數，然後再進行串連和 base64 編碼，這些設定的值 *搜尋 [xmlcfg*。 請參閱 〈 [診斷組態變數](#diagnostics-configuration-variables) 以進一步了解如何在變數中儲存的 xml。  *StorageAccount* 屬性會指定資料傳輸到哪一個診斷的儲存體帳戶名稱。 
 
中的屬性 *protectedSettings* (有時稱為為私用組態) 可以設定，但無法讀取之後設定。 唯寫的本質 *protectedSettings* 很適合存放機密，像是儲存體帳戶金鑰的診斷資料寫入的位置。    

## 將診斷儲存體帳戶指定為參數 

診斷延伸模組 json 上述程式碼片段會假設兩個參數 *existingdiagnosticsStorageAccountName* 和
*existingdiagnosticsStorageAccountName* 指定診斷儲存體帳戶的診斷資料儲存的位置。 將診斷儲存體帳戶指定為參數可讓您輕鬆地跨不同環境變更診斷儲存體帳戶，例如，您可能想要使用不同診斷儲存體帳戶進行測試，並且使用另外一個進行生產部署。  

        "existingdiagnosticsStorageAccountName": {
            "type": "string",
            "metadata": {
        "description": "The name of an existing storage account to which diagnostics data will be transfered."
            }        
        },
        "existingdiagnosticsStorageResourceGroup": {
            "type": "string",
            "metadata": {
        "description": "The resource group for the storage account specified in existingdiagnosticsStorageAccountName"
            }
        }

最佳做法是在不同於虛擬機器資源群組的其他資源群組中指定診斷儲存體帳戶。 資源群組可以視為是具有自己的存留期的部署單位，可以部署虛擬機器以及在新組態更新時重新部署，但是您可能想要跨這些虛擬機器部署繼續在相同的儲存體帳戶中儲存診斷資料。 在不同的資源中擁有儲存體帳戶可讓儲存體帳戶接受來自各種虛擬機器部署的資料，方便疑難排解各種版本之間的問題。

>[AZURE.NOTE] 如果您從 Visual Studio 建立 windows 虛擬機器範本可能使用相同的儲存體帳戶在上傳虛擬機器 VHD 設定預設儲存體帳戶。 這是為了簡化 VM 的初始設定。 您應該重構範本以使用可以當做參數傳入的不同儲存體帳戶。 

## 診斷組態變數
 
診斷延伸模組 json 上述程式碼片段會定義 *accountid* 變數來簡化取得診斷儲存體的儲存體帳戶金鑰:   
    
    "accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"


 *搜尋 [xmlcfg* 診斷延伸模組屬性定義使用串連在一起的多個變數。 這些變數值的格式為 xml，因此必須在設定 json 變數時正確逸出。

以下說明診斷組態 xml，它會收集標準系統層級效能計數器以及一些 Windows 事件記錄檔和診斷基礎結構記錄檔。 已正確逸出和格式化，因此組態可以直接貼到您的範本的變數區段。 請參閱 [診斷組態結構描述](https://msdn.microsoft.com/library/azure/dn782207.aspx) 更人性化可讀取的組態 xml 範例。
    
        "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
        "wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
        "wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
        "wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
        "wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
        "wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"

上述組態中的度量定義 xml 節點是重要的組態項目，因為它會定義稍早在 xml 中的效能計數器的定義方式 *PerformanceCounter* 節點會彙總，並儲存。 

> [AZURE.IMPORTANT] 這些度量中的磁碟機的監視圖表與警示 Azure 入口網站。   **度量** 節點 *resourceID* 和 **MetricAggregation** 必須包含在您的 VM 的診斷組態中，如果您想要查看監視資料，Azure 入口網站中的 VM。 

以下是度量定義的 xml 的範本： 

        <Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
            <MetricAggregation scheduledTransferPeriod="PT1H"/>
            <MetricAggregation scheduledTransferPeriod="PT1M"/>
        </Metrics>

 *ResourceID* 屬性可唯一識別您的訂閱中的虛擬機器。 請確定使用 subscription() 和 resourceGroup() 函式，這樣範本就會根據訂用帳戶和您要部署的資源群組自動更新這些值。

如果您要在迴圈中建立多個虛擬機器，則您必須填入 *resourceID* 正確區分每個個別的 VM copyindex () 函式的值。  *搜尋 [xmlCfg* 值可以更新為支援此功能，如下所示:  

    "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 

MetricAggregation 值 *PT1H* 和 *PT1M* 表示彙總時數分鐘和彙總時間超過一小時。

## 儲存體中的 WADMetrics 資料表

上述的度量組態將會在您的診斷儲存體帳戶中產生具有下列命名慣例的資料表：

- **WADMetrics** : 標準的前置詞的所有 WADMetrics 資料表
- **PT1H** 或 **PT1M** : 表示此資料表包含超過 1 小時或 1 分鐘的彙總資料
- **P10D** : 表示資料表會包含從資料表開始收集資料的 10 天的資料
- **V2S** : 字串常數
- **yyyymmdd** : 資料表開始收集資料的日期

範例: *WADMetricsPT1HP10DV2S20151108* 將包含超過一小時 10 天，開始於 2015 年 11 月 11 日彙總的度量資料    

每個 WADMetrics 資料表將會包含下列資料行：

- **PartitionKey**: 根據建構 partitionkey *resourceID* 來唯一識別 VM 資源的值。 針對例如: 002Fsubscriptions:<subscriptionID>: 002FresourceGroups:002F<ResourceGroupName>: 002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>  
- **RowKey** : 遵循格式 <Descending time tick>:<Performance Counter Name>。 遞減的時間刻度計算是最大時間刻度減去開始彙總期間時間。 例如 如果取樣期間已於 2015 年 11 月 10 日啟動，而且也很 00:00Hrs UTC，然後計算: DateTime.MaxValue.Ticks-(新 DateTime(2015,11,10,0,0,0,DateTimeKind.Utc)。刻度)。 針對記憶體可用位元組效能計數器，資料列索引鍵如下所示：2519551871999999999__:005CMemory:005CAvailable:0020Bytes
- **CounterName** : 效能計數器的名稱。 這會比對 *counterSpecifier* xml 組態中定義。
- **最大** : 彙總期間的效能計數器的最大值。
- **最小** : 彙總期間的效能計數器的最小值。
- **總** : 彙總期間報告的效能計數器的所有值的總和。
- **計數** : 報告效能計數器的值總數。
- **平均** : 彙總期間的效能計數器的平均值 (總計/計數) 值。


## 後續步驟

- 完整的範例範本與診斷擴充功能的 Windows 虛擬機器，請參閱 [201-vm-監視-診斷-擴充功能](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)   
- 資源管理員範本使用部署 [PowerShell](virtual-machines-deploy-rmtemplates-powershell.md) 或 [Azure 命令列](virtual-machines-deploy-rmtemplates-powershell.md)
- 深入了解 [編寫 Azure 資源管理員範本](resource-group-authoring-templates.md)








