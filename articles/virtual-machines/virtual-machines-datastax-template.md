<properties
    pageTitle="在 Ubuntu 上使用資源管理員範本的 DataStax | Microsoft Azure"
    description="了解如何使用 Azure PowerShell 或 Azure CLI 搭配資源管理員範本，在 Ubuntu VM 上輕鬆部署新的 DataStax 叢集"
    services="virtual-machines"
    documentationCenter=""
    authors="scoriani"
    manager="timlt"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines"
    ms.workload="multiple"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/29/2015"
    ms.author="scoriani"/>

# 在 Ubuntu 上使用 Resource Manager 範本的 DataStax

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] 傳統部署模型。


DataStax 是知名的業界領導者，他們根據 Apache Cassandra 來開發和提供各種解決方案，這是一種可提供商業支援且符合企業需求的 NoSQL 分散式資料庫技術，此技術廣受市場認可、敏捷、永不停擺，並可依照未來的各種公司規模需求進行調整。 DataStax 提供 Enterprise (DSE) 和 Community (DSC) 等版本。 它也提供記憶體內部運算、企業級安全性、快速且功能強大的整合式分析與企業搜尋之類的功能。

除了已經提供 Azure Marketplace 中，現在您可以輕鬆部署新的 DataStax 叢集，在 Ubuntu Vm 上使用資源管理員範本部署到 [PowerShell](../powershell-install-configure.md) 或 [Azure CLI](../xplat-cli-install.md)。

根據這個範本部署的新叢集會採用下圖中所述的拓撲，不過，您可以自訂本文中所述的範本，輕鬆實現其他拓撲：

![cluster-architecture](media/virtual-machines-datastax-template/cluster-architecture.png)

使用參數，您可以定義將在新 Apache Cassandra 叢集上部署的節點數目。 系統也會在同一個虛擬網路中獨立的 VM 上部署 Datastax 作業中心服務的執行個體，讓您能夠監視叢集和所有個別節點的狀態、新增/移除節點，以及執行所有與該叢集相關的管理工作。

部署完成之後，您可以使用設定的 DNS 位址，來存取 Datastax 作業中心 VM 執行個體。 OpsCenter VM 會啟用 SSH 連接埠 22 以及針對HTTPS 啟用連接埠 8443。 用於將包含作業中心的 DNS 位址 *dnsName* 和 *區域* 做為參數，產生的格式輸入 `{dnsName}.{region}.cloudapp.azure.com`。 如果您在建立部署 *dnsName* 參數設定為"datastax"，"West US"區域中，您無法存取在部署 DataStax 作業中心 VM `https://datastax.westus.cloudapp.azure.com:8443`。

> [AZURE.NOTE] 部署中使用的憑證是自我簽署的憑證，以建立瀏覽器發出警告。 您可以按照此程序 [DataStax](http://www.datastax.com/) 憑證換成您自己的 SSL 憑證的網站。

在深入了解與 Azure 資源管理員和將針對此次部署使用之範本的詳細資訊之前，請確定您已正確設定 Azure PowerShell 或 Azure CLI。

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../../includes/xplat-getting-set-up-arm.md)]

## 使用資源管理員範本建立 DataStax 類型的 Cassandra 叢集

請依照下列步驟，使用 Github 範本儲存機制中的資源管理員範本，根據 DataStax 建立 Apache Cassandra 叢集。 每個步驟都將包含適用於 Azure PowerShell 和 Azure CLI 的指引。

### 步驟 1-a：使用 Azure PowerShell 下載範本檔案

為 JSON 範本和其他相關聯的檔案建立本機資料夾 (例如，C:\Azure\Templates\DataStax)。

使用您本機資料夾的資料夾名稱來替代，並執行下列命令：

    $folderName="C:\Azure\Templates\DataStax"
    $webclient = New-Object System.Net.WebClient
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/azuredeploy.json"
    $filePath = $folderName + "\azuredeploy.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/azuredeploy-parameters.json"
    $filePath = $folderName + "\azuredeploy-parameters.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/dsenode.sh"
    $filePath = $folderName + "\dsenode.sh"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/ephemeral-nodes-resources.json"
    $filePath = $folderName + "\ephemeral-nodes-resources.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/metadata.json"
    $filePath = $folderName + "\metadata.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/opscenter-install-resources.json"
    $filePath = $folderName + "\opscenter-install-resources.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/opscenter-resources.json"
    $filePath = $folderName + "\opscenter-resources.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/opscenter.sh"
    $filePath = $folderName + "\opscenter.sh"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/shared-resources.json"
    $filePath = $folderName + "shared-resources.json"
    $webclient.DownloadFile($url,$filePath)

### 步驟 1-b：使用 Azure CLI 下載範本檔案

使用您選擇的 Git 用戶端，來複製整個範本儲存機制，例如：

    git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates

複製完成後，尋找 **datastax 上 ubuntu** C:\Azure\Templates 目錄中的資料夾。

### 步驟 2：(選用) 了解範本參數

部署非簡單式解決方案時 (例如 DataStax 類型的 DataStax Apache Cassandra 叢集)，您必須指定一組設定參數來處理一些必要的設定。 您可以在範本定義中宣告這些參數，這樣一來，就能在部署期間透過外部檔案或在命令列中指定值。

在 azuredeploy.json 檔案頂端的 "parameters" 區段中，您會發現設定 DataStax 叢集時，範本所需要的參數集。 以下範例來自這個範本的 azuredeploy.json 檔案的 "parameters" 區段：

    "parameters": {
        "region": {
            "type": "string",
            "defaultValue": "West US",
            "metadata": {
                "Description": "Location where resources will be provisioned"
            }
        },
        "storageAccountPrefix": {
            "type": "string",
            "defaultValue": "uniqueStorageAccountName",
            "metadata": {
                "Description": "Unique namespace for the storage account where the virtual machine's disks will be placed"
            }
        },
        "dnsName": {
            "type": "string",
            "metadata" : {
                "Description": "DNS subname for the operations center public IP"
            }
        },
        "virtualNetworkName": {
            "type": "string",
            "defaultValue": "myvnet",
            "metadata": {
                "Description": "Name of the virtual network provisioned for the cluster"
            }
        },
        "adminUsername": {
            "type": "string",
            "metadata": {
                "Description": "Administrator user name used when provisioning virtual machines"
            }
        },
        "adminPassword": {
            "type": "securestring",
            "metadata": {
                "Description": "Administrator password used when provisioning virtual machines"
            }
        },
        "opsCenterAdminPassword": {
            "type": "securestring",
            "metadata": {
                "Description": "Sets the operations center admin user password"
            }
        },
        "clusterVmSize": {
            "type": "string",
            "defaultValue": "Standard_D3",
            "allowedValues": [
                "Standard_D1",
                "Standard_D2",
                "Standard_D3",
                "Standard_D4",
                "Standard_D11",
                "Standard_D12",
                "Standard_D13",
                "Standard_D14"
            ],
            "metadata": {
                "Description": "The size of the virtual machines used when provisioning cluster nodes"
            }
        },
        "clusterNodeCount": {
            "type": "int",
            "metadata": {
                "Description": "The number of nodes provisioned in the cluster"
            }
        },
        "clusterName": {
            "type": "string",
            "metadata": {
                "Description": "The name of the cluster provisioned"
            }
        }
    }

每個參數都具有資料類型和允許值之類的詳細資料。 這允許驗證在互動模式 (例如 Azure PowerShell 或 Azure CLI)，及自我探索 UI (透過剖析必要參數清單及其描述並動態建置的 UI) 的範本執行期間所傳遞的參數。

### 步驟 3-a：透過 Azure PowerShell 使用範本部署 DataStax 叢集

藉由建立 JSON 檔案 (其中包含適用於所有參數的執行階段值)，來為您的部署準備參數檔案。 接著將此檔案當成單一實體傳遞給部署命令。 如果未包含參數檔案，Azure PowerShell 將使用範本中指定的任何預設值，然後提示您填寫剩餘的值。

以下是來自 azuredeploy-parameters.json 檔案的參數集範例：

    {
        "storageAccountPrefix": {
            "value": "scorianisa"
        },
        "dnsName": {
            "value": "scorianids"
        },
        "virtualNetworkName": {
            "value": "datastax"
        },
        "adminUsername": {
            "value": "scoriani"
        },
        "adminPassword": {
            "value": ""
        },
        "region": {
            "value": "West US"
        },
        "opsCenterAdminPassword": {
            "value": ""
        },
        "clusterVmSize": {
            "value": "Standard_D3"
        },
        "clusterNodeCount": {
            "value": 3
        },
        "clusterName": {
            "value": "cl1"
        }
    }

填寫 Azure 部署名稱、資源群組名稱、Azure 位置，以及儲存 JSON 部署檔案的資料夾。 然後執行以下命令：

    $deployName="<deployment name>"
    $RGName="<resource group name>"
    $locName="<Azure location, such as West US>"
    $folderName="<folder name, such as C:\Azure\Templates\DataStax>"
    $templateFile= $folderName + "\azuredeploy.json"
    $templateParameterFile= $folderName + "\azuredeploy-parameters.json"

    New-AzureResourceGroup –Name $RGName –Location $locName

    New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile

當您執行 **New-azureresourcegroupdeployment** 命令時，這將會從 JSON 參數檔案中，擷取參數值，然後開始執行範本。 定義以及使用不同環境 (測試、實際執行等) 所需的參數檔案，有利範本的重複使用以及簡化複雜的多重環境解決方案。

部署時，請記得需要建立新的 Azure 儲存體帳戶，所以您提供來做為儲存體帳戶參數的名稱必須是唯一且符合 Azure 儲存體帳戶的所有需求 (僅限小寫字母和數字)。

部署期間以及部署結束之後，您可以檢查所有佈建期間所進行的要求，包括任何發生的錯誤。  

若要這樣做，請移至 [Azure 入口網站](https://portal.azure.com) 並執行下列動作 ︰

- 按一下 [ **瀏覽** 在左側導覽列，然後向下的捲動並按一下 **資源群組**。  
- 按一下剛建立的資源群組，以顯示 [資源群組] 刀鋒視窗。  
- 按一下 [事件] 橫條圖中 **監視** 組件的 [資源群組] 刀鋒視窗中，您可以查看事件，為您的部署。
- 按一下個別事件，您可以進一步查看代表範本所做的每個作業之詳細資料。

測試之後，如果需要移除這個資源群組及其所有資源 (儲存體帳戶、虛擬機器和虛擬網路)，請使用這個單一命令：

    Remove-AzureResourceGroup –Name "<resource group name>" -Force

### 步驟 3-b：透過 Azure CLI 使用範本部署 DataStax 叢集

若要透過 Azure CLI 部署 DataStax 叢集，請先指定名稱和位置來建立資源群組：

    azure group create dsc "West US"

將此資源群組名稱、JSON 範本檔案的位置，以及參數檔案的位置 (如需詳細資訊，請參閱上方的 Azure PowerShell 章節) 傳遞給下列命令：

    azure group deployment create dsc -f .\azuredeploy.json -e .\azuredeploy-parameters.json

您可以使用下列命令來檢查個別資源部署的狀態：

    azure group deployment list dsc

## DataStax 範本結構和檔案組織的導覽

為了讓 Resource Manager 範本的設計更加完善且可重複使用，您必須在部署 DataStax 之類的複雜解決方案期間，考慮清楚如何安排一連串複雜但又彼此相關的工作。 除了透過相關延伸模組執行指令碼之外，還可以利用資源管理員範本連結和資源迴圈，這樣就能實作模組化方法，而實際上所有以複雜範本為基礎的部署都能重複使用此方法。

本節將帶領您逐步了解 DataStax 叢集的 azuredeploy.json 檔案結構。

### "parameters" 區段

azuredeploy.json 的 "parameters" 區段會指定此範本中所使用的可修改參數。 先前提及的 azuredeploy-parameters.json 檔案是在範本執行期間，用來將值傳遞到 azuredeploy.json 的 "parameters" 區段。

### "variables" 區段

"variables" 區段會指定一些變數，這些參數會全程用在這個範本中。 這包含數個欄位 (JSON 資料類型或片段)，而它們將在執行階段設定為常數或計算值。 以下是此 DataStax 範本的 "variables" 區段：

    "variables": {
    "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/datastax-on-ubuntu/",
    "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
    "clusterNodesTemplateUrl": "[concat(variables('templateBaseUrl'), 'ephemeral-nodes-resources.json')]",
    "opsCenterTemplateUrl": "[concat(variables('templateBaseUrl'), 'opscenter-resources.json')]",
    "opsCenterInstallTemplateUrl": "[concat(variables('templateBaseUrl'), 'opscenter-install-resources.json')]",
    "opsCenterVmSize": "Standard_A1",
    "networkSettings": {
        "virtualNetworkName": "[parameters('virtualNetworkName')]",
        "addressPrefix": "10.0.0.0/16",
        "subnet": {
            "dse": {
                "name": "dse",
                "prefix": "10.0.0.0/24",
                "vnet": "[parameters('virtualNetworkName')]"
            }
        },
        "statics": {
            "clusterRange": {
                "base": "10.0.0.",
                "start": 5
            },
            "opsCenter": "10.0.0.240"
        }
    },
    "osSettings": {
        "imageReference": {
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "14.04.2-LTS",
            "version": "latest"
        },
        "scripts": [
            "[concat(variables('templateBaseUrl'), 'dsenode.sh')]",
            "[concat(variables('templateBaseUrl'), 'opscenter.sh')]",
            "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
        ]
    },
    "sharedStorageAccountName": "[concat(parameters('storageAccountPrefix'),'cmn')]",
    "nodeList": "[concat(variables('networkSettings').statics.clusterRange.base, variables('networkSettings').statics.clusterRange.start, '-', parameters('clusterNodeCount'))]"
    },

深入研究這個範例之後，就會看到兩種不同的方法。 在第一個片段中， **osSettings** 變數是巢狀的 JSON 元素，其中包含四個索引鍵-值組 ︰

    "osSettings": {
          "imageReference": {
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "14.04.2-LTS",
            "version": "latest"
          },

     
在第二個片段中， **指令碼** 變數是 JSON 陣列，會在執行階段透過範本語言函式 (concat) 和另一個變數，再加上字串常數的值計算每個元素 ︰

          "scripts": [
            "[concat(variables('templateBaseUrl'), 'dsenode.sh')]",
            "[concat(variables('templateBaseUrl'), 'opscenter.sh')]",
            "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
          ]

### "resources" 區段

絕大多數的動作就是在 "resources" 區段進行的。 請仔細查看這個區段，您會立即找出兩個不同的案例。 第一個是定義為 `Microsoft.Resources/deployments` 類型的元素，基本上它會叫用主要檔案內的巢狀部署。 透過 **templateLink** 元素 （和相關的版本屬性），就可以指定連結的範本檔案，就會叫用做為輸入，傳遞一組參數，如本片段所示 ︰

    {
          "name": "shared",
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2015-01-01",
          "properties": {
            "mode": "Incremental",
            "templateLink": {
              "uri": "[variables('sharedTemplateUrl')]",
              "contentVersion": "1.0.0.0"
            },
            "parameters": {
              "region": {
                "value": "[parameters('region')]"
              },
              "networkSettings": {
                "value": "[variables('networkSettings')]"
              },
              "storageAccountName": {
                "value": "[variables('sharedStorageAccountName')]"
              }
            }
          }
        },

在第一個範例中，我們很清楚地知道此案例中的 azuredeploy.json 是用來做為一種協調流程機制，負責叫用一些其他範本檔案。 每個檔案會負責需要部署活動的一部分。

特別是，下列連結的範本將用於此部署：

-   **shared-resource.json**︰ 包含定義的整個部署中共用的所有資源。 例如，用來儲存 VM 的作業系統磁碟和虛擬網路的儲存體帳戶。
-   **opscenter-resources.json**︰ 部署 OpsCenter VM 和所有相關的資源，包括網路介面和公用 IP 位址。
-   **安裝-opscenter-resources.json**︰ 部署 OpsCenter VM 延伸模組 （適用於 Linux 的自訂指令碼），將會叫用特定的 bash 指令碼檔案 (opscenter.sh)，才能設定該 VM 內設定 OpsCenter 服務。
-   **暫時 azuredeploy.json**︰ 部署所有叢集節點 Vm 和連接的資源 (網路卡、 私人 Ip 等。)。 此範本也會部署 VM 擴充功能 (適用於 Linux 的自訂指令碼)，並叫用 bash 指令碼 (dsenode.sh)，在每一個節點上安裝 Apache Cassandra 程式碼。

讓我們深入了解最後一個範本的使用方式，因為從範本開發角度來看，這是最有趣的範本之一。 在此要強調一個重要的概念，那就是一個範本檔案可以重複部署某一種資源類型，而且每一個執行個體都可以為必要的設定指定唯一的值。 這個概念稱為 **資源迴圈**。

暫時 azuredeploy.json 會叫用時從主要 azuredeploy.json 檔案中，參數稱為 *nodeCount* 提供參數清單的一部分。 在子範本中， *nodeCount* （部署在叢集中的節點數目） 將會用於內部 **複製** 的每個資源都必須部署於多個複本，下列片段中反白顯示的項目。 針對不同的執行個體的部署資源，需要唯一值的所有設定 **copyindex （)** 函式可用來取得數值，指出目前的索引，在這個特定資源迴圈的建立。 在下列片段中，您可以在為 DataStax 叢集節點建立的多個 VM 上看見這個概念的運用：

               {
                  "apiVersion": "2015-05-01-preview",
                  "type": "Microsoft.Compute/virtualMachines",
                  "name": "[concat(parameters('namespace'), 'vm', copyindex())]",
                  "location": "[parameters('region')]",
                  "copy": {
                    "name": "[concat(parameters('namespace'), 'vmLoop')]",
                    "count": "[parameters('nodeCount')]"
                  },
                  "dependsOn": [
                    "[concat('Microsoft.Network/networkInterfaces/', parameters('namespace'), 'nic', copyindex())]",
                    "[concat('Microsoft.Compute/availabilitySets/', parameters('namespace'), 'set')]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]"
                  ],
                  "properties": {
                    "availabilitySet": {
                      "id": "[resourceId('Microsoft.Compute/availabilitySets', concat(parameters('namespace'), 'set'))]"
                    },
                    "hardwareProfile": {
                      "vmSize": "[parameters('vmSize')]"
                    },
                    "osProfile": {
                      "computername": "[concat(parameters('namespace'), 'vm', copyIndex())]",
                      "adminUsername": "[parameters('adminUsername')]",
                      "adminPassword": "[parameters('adminPassword')]"
                    },
                    "storageProfile": {
                      "imageReference": "[parameters('osSettings').imageReference]",
                      "osDisk": {
                        "name": "osdisk",
                        "vhd": {
                          "uri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net/vhds/', variables('vmName'), copyindex(), '-osdisk.vhd')]"
                        },
                        "caching": "ReadWrite",
                        "createOption": "FromImage"
                      },
                      "dataDisks": [
                        {
                          "name": "datadisk1",
                          "diskSizeGB": 1023,
                          "lun": 0,
                          "vhd": {
                            "Uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/','vhds/', variables('vmName'), copyindex(), 'DataDisk1.vhd')]"
                          },
                          "caching": "None",
                          "createOption": "Empty"
                        }
                      ]
                    },
                    "networkProfile": {
                      "networkInterfaces": [
                        {
                          "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('namespace'), 'nic', copyindex()))]"
                        }
                      ]
                    }
                  }
                },

建立資源的另一個重要概念是能夠指定相依性和資源之間的優先順序，您可以看到在 **dependsOn** JSON 陣列。 在這個特殊範本中，每一個節點也會連接 1 TB 磁碟 (請參閱＜dataDisks＞)，其可用來裝載 Apache Cassandra 執行個體的備份和快照。

當 dsenode.sh 指令碼檔執行時，會進行各種節點準備活動，其中之一就是格式化連結的磁碟。 這個指令碼的第一列會叫用另一個指令碼：

    bash vm-disk-utils-0.1.sh

Vm 磁碟-utils 0.1.sh 檔案屬於 **shared_scripts\ubuntu** azure 快速入門範本 GitHub 儲存機制中的資料夾和包含磁碟掛接、 格式設定及等量的非常實用的功能。 這些函式可以用於儲存機制中的所有範本。

另一個要探索的有趣片段是與 CustomScriptForLinux VM 延伸模組相關的片段。 這些片段都是以獨立資源類型進行安裝，而且每個叢集節點 (以及 OpsCenter 執行個體) 上都具有相依性。 它們運用的是針對虛擬機器所描述的相同資源迴圈機制：

    {
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "[concat(parameters('namespace'), 'vm', copyindex(), '/installdsenode')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[parameters('region')]",
    "copy": {
        "name": "[concat(parameters('namespace'), 'vmLoop')]",
        "count": "[parameters('nodeCount')]"
    },
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('namespace'), 'vm', copyindex())]",
        "[concat('Microsoft.Network/networkInterfaces/', parameters('namespace'), 'nic', copyindex())]"
    ],
    "properties": {
        "publisher": "Microsoft.OSTCExtensions",
        "type": "CustomScriptForLinux",
        "typeHandlerVersion": "1.2",
        "settings": {
            "fileUris": "[parameters('osSettings').scripts]",
            "commandToExecute": "bash dsenode.sh"
        }
    }
    }

讓您自己熟悉此部署內所含的其他檔案，就能了解如何利用 Azure 資源管理員範本，根據任何技術來組織和協調適用於多節點解決方案的複雜部署策略所需的所有詳細資料和最佳做法。 在此建議一種範本檔案建構方法，請自行決定是否採用，如下圖重點標示的部分：

![datastax-template-structure](media/virtual-machines-datastax-template/datastax-template-structure.png)

基本上，這種方法會建議：

-   將您的核心範本檔案定義成所有特定部署活動的協調中心，利用範本連結功能來叫用子範本的執行。
-   建立特定的範本檔案，將部署可在所有其他特定部署工作上共用的所有資源 (儲存體帳戶、虛擬網路組態等)。 如果不同的部署之間，其一般基礎結構的需求皆類似，就可以高度重複使用。
-   包含選用資源範本，適用於指定資源特定的場地需求。
-   針對資源群組中的成員相同時 (叢集中的節點等等)，可以建立會利用資源迴圈功能的特定範本，以便部署多個具有唯一屬性的執行個體。
-   所有的後續部署工作 (產品安裝、設定等) 都會利用指令碼部署擴充功能以及建立每一種技術獨有的指令碼。

如需詳細資訊，請參閱 [Azure 資源管理員範本語言](../resource-group-authoring-templates.md)。


