<properties
    pageTitle="3 伺服器 SharePoint 伺服陣列 ARM 範本 | Microsoft Azure"
    description="逐步講解三部伺服器之 SharePoint 伺服器陣列的 Azure 資源管理員範本。"
    services="virtual-machines"
    documentationCenter=""
    authors="JoeDavies-MSFT"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"                                                                             ms.tgt_pltfrm="vm-windows-sharepoint"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2015"
    ms.author="josephd"/>

# 三部伺服器的 SharePoint 伺服器陣列資源管理員範本

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] 傳統部署模型。 您無法以傳統部署模型建立此資源。

本主題逐步講解三部伺服器的 SharePoint 伺服器陣列的 azuredeploy.json 範本檔案結構。 您可以從瀏覽器中看到此範本的內容 [這裡](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json)。

或者，若要檢查 azuredeploy.json 檔案的本機副本，請指定本機資料夾作為儲存該檔案的位置，然後建立該資料夾 (例如，C:\Azure\Templates\SharePointFarm)。 填入資料夾名稱，然後在本機電腦的 Azure PowerShell 命令提示字元上執行這些命令。

    $folderName="<folder name, such as C:\Azure\Templates\SharePointFarm>"
    $webclient = New-Object System.Net.WebClient
    $url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json"
    $filePath = $folderName + "\azuredeploy.json"
    $webclient.DownloadFile($url,$filePath)

在您選擇的文字編輯器或工具中開啟 azuredeploy.json 範本。 以下將說明範本檔案的結構和每個區段的用途。

## "parameters" 區段

 **「 參數 」** 區段指定用於資料輸入範本的參數。 您必須在執行範本時提供該資料。 您最多可以定義 50 個參數。 以下是 Azure 位置的參數範例：

    "deploymentLocation": {
        "type": "string",
        "allowedValues": [
            "West US",
            "East US",
            "West Europe",
            "East Asia",
            "Southeast Asia"
        ],
        "metadata": {
            "Description": "The region to deploy the resources into"
        },
        "defaultValue":"West Europe"
    },

## "variables" 區段

 **"Variables"** 區段會指定變數和，範本會使用其值。 變數值可以明確地設定或從參數值衍生。 和參數相反，您在執行範本時不需要提供變數值。 您最多可以定義 100 個變數。 這裡有一些範例：

    "LBFE": "LBFE",
    "LBBE": "LBBE",
    "RDPNAT": "RDP",
    "spWebNAT": "spWeb",
    "adSubnetName": "adSubnet",
    "sqlSubnetName": "sqlSubnet",
    "spSubnetName": "spSubnet",
    "adNicName": "adNic",
    "sqlNicName": "sqlNic",
    "spNicName": "spNic",

## "resources" 區段

 **「 資源 」** 區段會指定部署的資源群組中的 SharePoint 伺服器陣列資源所需的資訊。 您最多可定義 250 個資源，但僅能為資源相依性定義 5 個層級深度。

本節包含下列小節：

### Microsoft.Storage/storageAccounts  

本章節會建立伺服器陣列中的所有 VHD 和磁碟資源的新儲存體帳戶。 以下是儲存體帳戶的 JSON 程式碼：

    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[parameters('newStorageAccountName')]",
      "apiVersion": "2015-05-01-preview",
      "location": "[parameters('deploymentLocation')]",
      "properties": {
        "accountType": "[parameters('storageAccountType')]"
      }
    },

### Microsoft.Network/publicIPAddresses

這些區段會建立一組公用 IP 位址，有了這些網址之後，就可以透過網際網路連接每一個虛擬機器。 下列是一個範例：

    {
        "apiVersion": "2015-05-01-preview",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[variables('adpublicIPAddressName')]",
        "location": "[parameters('deploymentLocation')]",
        "properties": {
            "publicIPAllocationMethod": "[parameters('publicIPAddressType')]",
            "dnsSettings": {
                "domainNameLabel": "[parameters('adDNSPrefix')]"
            }
        }
    },

### Microsoft.Compute/availabilitySets

這些區段會建立三個可用性集合，每一個部署層各一個：

- Active Directory 網域控制站
- SQL Server 叢集
- SharePoint 伺服器

下列是一個範例：

    {
        "type": "Microsoft.Compute/availabilitySets",
        "name": "[variables('spAvailabilitySetName')]",
        "apiVersion": "2015-05-01-preview",
        "location": "[parameters('deploymentLocation')]"
    },

### Microsoft.Network/virtualNetworks

這些區段會建立一個雲端專用的虛擬網路，其中含有三個子網路 (每個部署層各一個)，而虛擬機器就是放在這個虛擬網路中。 以下是 JSON 程式碼：

    {
        "name": "[parameters('virtualNetworkName')]",
        "type": "Microsoft.Network/virtualNetworks",
        "location": "[parameters('deploymentLocation')]",
        "apiVersion": "2015-05-01-preview",
        "properties": {
            "addressSpace": {
            "addressPrefixes": [
                "[parameters('virtualNetworkAddressRange')]"
            ]
            },
            "subnets": "[variables('subnets')]"
        }
    },


### Microsoft.Network/loadBalancers

這些區段會為每一個虛擬機器建立負載平衡器執行個體，以便為網際網路的輸入流量，提供網路位址轉譯 (NAT) 和流量篩選功能。 針對每一個負載平衡器，設定前端、後端以及傳入 NAT 規則。 例如，每一個虛擬機器都有專屬的遠端桌面流量規則，而 SharePoint 伺服器的規則就是，可允許網際網路的輸入 Web 流量 (TCP 連接埠 80)。 以下是 SharePoint 伺服器的範例：


    {
        "apiVersion": "2015-05-01-preview",
        "name": "[variables('spLBName')]",
        "type": "Microsoft.Network/loadBalancers",
        "location": "[parameters('deploymentLocation')]",
        "dependsOn": [
            "[resourceId('Microsoft.Network/publicIPAddresses',variables('sppublicIPAddressName'))]"
        ],
        "properties": {
            "frontendIPConfigurations": [
                {
                    "name": "[variables('LBFE')]",
                    "properties": {
                        "publicIPAddress": {
                            "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('sppublicIPAddressName'))]"
                        }
                    }
                }
            ],
            "backendAddressPools": [
                {
                    "name": "[variables('LBBE')]"
                }
            ],
            "inboundNatRules": [
                {
                    "name": "[variables('RDPNAT')]",
                    "properties": {
                        "frontendIPConfiguration": {
                            "id": "[variables('splbFEConfigID')]"
                        },
                        "protocol": "tcp",
                        "frontendPort": "[parameters('RDPPort')]",
                        "backendPort": 3389,
                        "enableFloatingIP": false
                    }
                },
                {
                    "name": "[variables('spWebNAT')]",
                    "properties": {
                        "frontendIPConfiguration": {
                            "id": "[variables('splbFEConfigID')]"
                        },
                        "protocol": "tcp",
                        "frontendPort": 80,
                        "backendPort": 80,
                        "enableFloatingIP": false
                    }
                }
            ]
        }
    },

### Microsoft.Network/networkInterfaces

這些區段會為每個虛擬機器建立一個網路介面，並設定網域控制站的靜態 IP 位址。 以下是網域控制站的網路介面範例：

    {
        "name": "[variables('adNicName')]",
        "type": "Microsoft.Network/networkInterfaces",
        "location": "[parameters('deploymentLocation')]",
        "dependsOn": [
            "[parameters('virtualNetworkName')]",
            "[concat('Microsoft.Network/loadBalancers/',variables('adlbName'))]"
        ],
        "apiVersion": "2015-05-01-preview",
        "properties": {
            "ipConfigurations": [
                {
                    "name": "ipconfig1",
                    "properties": {
                        "privateIPAllocationMethod": "Static",
                        "privateIPAddress": "[parameters('adNicIPAddress')]",
                        "subnet": {
                            "id": "[variables('adSubnetRef')]"
                        },
                        "loadBalancerBackendAddressPools": [
                            {
                                "id": "[variables('adBEAddressPoolID')]"
                            }
                        ],
                        "loadBalancerInboundNatRules": [
                            {
                                "id": "[variables('adRDPNATRuleID')]"
                            }
                        ]
                    }
                }
            ]
        }
    },


### Microsoft.Compute/virtualMachines

這些區段會在部署中建立及設定三個虛擬機器。

第一個區段會建立並設定網域控制站，其中：

- 指定儲存體帳戶、可用性集合、網路介面和負載平衡器執行個體。
- 新增額外的磁碟。
- 執行 PowerShell 指令碼以設定網域控制站。

以下是 JSON 程式碼：

        {
            "apiVersion": "2015-05-01-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[parameters('adVMName')]",
            "location": "[parameters('deploymentLocation')]",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts',parameters('newStorageAccountName'))]",
                "[resourceId('Microsoft.Network/networkInterfaces',variables('adNicName'))]",
                "[resourceId('Microsoft.Compute/availabilitySets', variables('adAvailabilitySetName'))]",
                "[resourceId('Microsoft.Network/loadBalancers',variables('adlbName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('adVMSize')]"
                },
                "availabilitySet": {
                    "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('adAvailabilitySetName'))]"
                },
                "osProfile": {
                    "computername": "[parameters('adVMName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "imageReference": {
                        "publisher": "[parameters('adImagePublisher')]",
                        "offer": "[parameters('adImageOffer')]",
                        "sku": "[parameters('adImageSKU')]",
                        "version": "latest"
                    },
                    "osDisk": {
                        "name": "osdisk",
                        "vhd": {
                            "uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmContainerName'),'/',parameters('adVMName'),'-osdisk.vhd')]"
                        },
                        "caching": "ReadWrite",
                        "createOption": "FromImage"
                    },
                    "dataDisks": [
                        {
                            "vhd": {
                                "uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmContainerName'),'/', variables('adDataDisk'),'-1.vhd')]"
                            },
                            "name": "[concat(parameters('adVMName'),'-data-disk1')]",
                            "caching": "None",
                            "createOption": "empty",
                            "diskSizeGB": "[variables('adDataDiskSize')]",
                            "lun": 0
                        }
                    ]
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('adNicName'))]"
                        }
                    ]
                }
            },
            "resources": [
                {
                    "type": "Microsoft.Compute/virtualMachines/extensions",
                    "name": "[concat(parameters('adVMName'),'/InstallDomainController')]",
                    "apiVersion": "2015-05-01-preview",
                    "location": "[parameters('deploymentLocation')]",
                    "dependsOn": [
                        "[resourceId('Microsoft.Compute/virtualMachines', parameters('adVMName'))]"
                    ],
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "1.7",
                        "settings": {
                            "ModulesUrl": "[variables('adModulesURL')]",
                            "ConfigurationFunction": "[variables('adConfigurationFunction')]",
                            "Properties": {
                                "DomainName": "[parameters('domainName')]",
                                "AdminCreds": {
                                    "UserName": "[parameters('adminUserName')]",
                                    "Password": "PrivateSettingsRef:AdminPassword"
                                }
                            }
                        },
                        "protectedSettings": {
                            "Items": {
                                "AdminPassword": "[parameters('adminPassword')]"
                            }
                        }
                    }
                }
            ]
        },

從網域控制站的其他區段 **"name":"UpdateVNetDNS"** 使用靜態 IP 位址的網域控制站的虛擬網路的 DNS 伺服器。

下一個 **"type":"Microsoft.compute/virtualmachines"** 區段會建立在部署中的 SQL Server 虛擬機器和:

- 指定儲存體帳戶、可用性集合、負載平衡器、虛擬網路及網路介面。
- 新增額外的磁碟。

其他 **"Microsoft.Compute/virtualMachines/extensions"** 區段會呼叫 PowerShell 指令碼來設定 SQL Server。

下一個 **"type":"Microsoft.compute/virtualmachines"** 區段會在部署中，指定儲存體帳戶、 可用性設定組、 負載平衡器、 虛擬網路和網路介面來建立 SharePoint 虛擬機器。 額外 **"Microsoft.Compute/virtualMachines/extensions"** 區段會呼叫 PowerShell 指令碼來設定 SharePoint 伺服器陣列。

請注意子區段整體組織 **「 資源 」** JSON 檔案的區段:

1.  建立 Azure 基礎結構的元素，我們需要此元素來支援多個虛擬機器 (儲存體帳戶、公用 IP 位址、可用性集合、虛擬網路、網路介面、負載平衡器執行個體)。
2.  建立網域控制站虛擬機器，此虛擬機器會使用先前建立的 Azure 基礎結構的一般和特定元素、新增資料磁碟，並執行 PowerShell 指令碼。 此外，更新虛擬網路來使用網域控制站的靜態 IP 位址。
3.  建立 SQL Server 虛擬機器，此虛擬機器會使用先前為網域控制器建立的 Azure 基礎結構的一般和特定元素、新增資料磁碟，並執行 PowerShell 指令碼以設定 SQL Server。
4.  建立 SharePoint Server 虛擬機器，此虛擬機器會使用先前建立的 Azure 基礎結構的一般和特定元素，並執行 PowerShell 指令碼以設定 SharePoint 伺服器陣列。

在 Azure 中使用您自己的 JSON 範本建立多層式基礎結構時，請按照以下的步驟執行：

1.  建立部署所需的 Azure 基礎結構的一般 (儲存體帳戶、虛擬網路)、層相關 (可用性集合)，以及虛擬機器相關 (公用 IP 位址、可用性集合、網路介面、負載平衡器執行個體) 元素。
2.  至於應用程式中的每一層 (例如驗證、資料庫、Web)，使用一般 (儲存體帳戶、 虛擬網路)、 特定層 (可用性集合) 和虛擬機器特定公用 IP 位址、 網路介面 (負載平衡器執行個體) 元素，在該層中建立以及設定伺服器。

如需詳細資訊，請參閱 [Azure 資源管理員範本語言](../resource-group-authoring-templates.md)。

## 其他資源

[Azure 運算、 網路和存放裝置提供者 Azure 資源管理員](virtual-machines-azurerm-versus-azuresm.md)
[Azure 資源管理員概觀](../resource-group-overview.md)

[編寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)

[虛擬機器文件](http://azure.microsoft.com/documentation/services/virtual-machines/)

