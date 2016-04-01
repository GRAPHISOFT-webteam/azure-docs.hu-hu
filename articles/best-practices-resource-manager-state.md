<properties
    pageTitle="在「Azure 資源管理員」範本中處理狀態的最佳做法"
    description="示範透過「Azure 資源管理員」範本與連結的範本，使用複雜物件來共用狀態資料的建議做法"
    services="azure-resource-manager"
    documentationCenter=""
    authors="mmercuri"
    manager="georgem"
    editor="tysonn"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/10/2015"
    ms.author="mmercuri"/>

# Azure 資源管理員範本中的共用狀態

此主題示範在「Azure 資源管理員」範本內以及跨連結的範本，管理及共用狀態的最佳做法。 本主題所顯示的參數與變數為您可以定義的物件類型範例，方便您用來組織部署需求。 在這些範例中，您可以實作自己的物件與您環境適用的屬性值。

本主題是較大份白皮書的一部分。 若要閱讀完整的文件、 下載 [世界類別 ARM 範本的考量和已證實實務] (http://download.microsoft.com/download/8/E/1/8E1DBEFA-CECE-4DC9-A813-93520A5D7CFE/World 類別 ARM 範本-考量和證明 Practices.pdf)。


## 使用複雜物件來共用狀態

與其給予可提供總彈性和無數個變化的範本，其實常見的模式是提供可選取已知組態的能力 - 實際上，是諸如沙箱、小型、中型和大型等標準 T 恤尺寸。 T 恤尺寸的其他範例包括產品供應項目，例如社群版本或企業版本。 在其他情況下，這可能是某種技術的工作負載特定組態， 例如，對應減少或沒有 SQL。

有了複雜物件，您可以建立變數，其中包含有時也稱為「屬性包」的資料集合，並使用該資料在範本中驅動資源宣告。 這種方法可針對預先為客戶設定好的各種大小提供良好且已知的組態。 如果沒有已知組態，客戶就必須自行判斷叢集大小、納入平台資源限制，以及進行數學運算來識別儲存體帳戶所產生的資料分割和其他資源 (因叢集大小和資源限制而導致) 。 已知組態讓客戶能夠輕鬆選取正確的 T 恤尺寸，也就是指定的部署。 除了為客戶提供更好的經驗，少量的已知組態可讓您更輕鬆地提供支援，並協助您提供較高的密度等級。


下列範例將顯示如何定義包含複雜物件以代表資料集合的變數。 此集合定義的值，以供虛擬機器大小、 網路設定 
作業系統設定和可用性設定。

    "variables": {
      "tshirtSizeLarge": {
        "vmSize": "Standard_A4",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-16disk-resources.json')]",
        "vmCount": 3,
        "slaveCount": 2,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 2,
          "pool": "db",
          "map": [0,1,1],
          "jumpbox": 0
        }
      },
      "osSettings": {
        "scripts": [
          "[concat(variables('templateBaseUrl'), 'install_postgresql.sh')]",
          "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
        ],
        "imageReference": {
      "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "14.04.2-LTS",
          "version": "latest"
        }
      },
      "networkSettings": {
        "vnetName": "[parameters('virtualNetworkName')]",
        "addressPrefix": "10.0.0.0/16",
        "subnets": {
          "dmz": {
            "name": "dmz",
            "prefix": "10.0.0.0/24",
            "vnet": "[parameters('virtualNetworkName')]"
          },
          "data": {
            "name": "data",
            "prefix": "10.0.1.0/24",
            "vnet": "[parameters('virtualNetworkName')]"
          }
        }
      },
      "availabilitySetSettings": {
        "name": "pgsqlAvailabilitySet",
        "fdCount": 3,
        "udCount": 5
      }
    }

您稍後可以在範本中參考這些變數。 參考具名變數和其屬性的能力可簡化範本語法， 
並使其更容易了解內容。 下列範例使用如上所示的物件來設定值，以定義要部署的資源。 例如，請注意，VM 大小由所擷取的值設定 
如 `variables('tshirtSize').vmSize` 磁碟大小是擷取自，而值 `variables('tshirtSize').diskSize`。 此外，已設定之連結的範本的 URI 
值 `variables('tshirtSize').vmTemplate`。

    "name": "master-node",
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
    "dependsOn": [
        "[concat('Microsoft.Resources/deployments/', 'shared')]"
    ],
    "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[variables('tshirtSize').vmTemplate]",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "value": "[parameters('adminPassword')]"
          },
          "replicatorPassword": {
            "value": "[parameters('replicatorPassword')]"
          },
          "osSettings": {
        "value": "[variables('osSettings')]"
          },
          "subnet": {
            "value": "[variables('networkSettings').subnets.data]"
          },
          "commonSettings": {
            "value": {
              "region": "[parameters('region')]",
              "adminUsername": "[parameters('adminUsername')]",
              "namespace": "ms"
            }
          },
          "storageSettings": {
            "value":"[variables('tshirtSize').storage]"
          },
          "machineSettings": {
            "value": {
              "vmSize": "[variables('tshirtSize').vmSize]",
              "diskSize": "[variables('tshirtSize').diskSize]",
              "vmCount": 1,
              "availabilitySet": "[variables('availabilitySetSettings').name]"
            }
          },
          "masterIpAddress": {
            "value": "0"
          },
          "dbType": {
            "value": "MASTER"
          }
        }
      }
    }

## 將狀態傳遞到範本和與其連結的範本

您可以透過下列方式，將狀態資訊共用到範本和與其連結的範本：

- 在部署期間直接提供給主要範本的參數
- 主要範本和連結的範本共用的參數、靜態變數，和產生的變數

### 提供給主要範本的通用參數

下表列出在範本中的常用參數。

**傳遞到主要範本的常用參數**

名稱 | 值 | 說明
---- | ----- | -----------
location    | 來自 Azure 區域之條件約束清單的字串   | 將部署資源的位置。
storageAccountNamePrefix    | String    | 將放置 VM 磁碟之儲存體帳戶的唯一 DNS 名稱
domainName  | String    | 格式可公開存取的 jumpbox VM 網域名稱 ︰ **{domainName}。 {location}.cloudapp.com** ，例如 ︰ **mydomainname.westus.cloudapp.azure.com**
adminUsername   | String    | VM 的使用者名稱
adminPassword   | String    | VM 的密碼
tshirtSize  | 來自提供 T 恤大小之條件約束清單的字串   | 要佈建的具名縮放單位大小。 例如，"Small"、"Medium"、"Large"
virtualNetworkName  | String    | 取用者想使用的虛擬網路名稱。
enableJumpbox   | 來自條件約束清單的字串 (enabled/disabled) | 識別是否要為環境啟用 JumpBox 的參數。 值："enabled"、"disabled"

### 傳送到連結的範本之參數

當連線到連結的範本時，您將經常混合使用靜態變數與產生的變數。

#### 靜態變數

靜態變數通常用於提供基底值，例如在範本中全程使用的 URL，或用來組合動態變數值的值。

在下面的，範本摘要 *templateBaseUrl* GitHub 中指定範本的根位置。 下一行會建置新的變數 *sharedTemplateUrl* 的串連 
值 *templateBaseUrl* 已知共用的資源範本的名稱。 下面，複雜物件變數用來儲存 t 恤大小，其中 *templateBaseUrl* 是 
若要指定儲存在已知的組態範本位置串連 *vmTemplate* 屬性。

這個方法的好處是您可以輕鬆地移動、分岔，或使用範本做為新範本的基礎。 如果範本位置變更時，您只需要變更靜態變數 
在一個地方，主要的範本 — 這會傳遞到所有範本。

    "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
    "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
    "tshirtSizeSmall": {
      "vmSize": "Standard_A1",
      "diskSize": 1023,
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
      "vmCount": 2,
      "slaveCount": 1,
      "storage": {
        "name": "[parameters('storageAccountNamePrefix')]",
        "count": 1,
        "pool": "db",
        "map": [0,0],
        "jumpbox": 0
      }
    }

#### 產生的變數

除了靜態變數，有一些變數是動態產生的。 本節將說明一些產生的變數的常見類型。

##### tshirtSize

當呼叫主要範本時，您可以從固定數目的選項，其中通常包含例如值選取 t 恤尺寸 *小型*, ，*媒體*, ，和 *大*。

在主要範本中，這個選項顯示為參數，例如 *tshirtSize*:

    "tshirtSize": {
      "type": "string",
      "defaultValue": "Small",
      "allowedValues": [
        "Small",
        "Medium",
        "Large"
      ],
      "metadata": {
        "Description": "T-shirt size of the MongoDB deployment"
      }
    }

在主要範本內部，變數對應到每個大小。 例如，如果可用的大小是小型、 中型和大型，變數區段會包含變數 
名為 *tshirtSizeSmall*, ，*tshirtSizeMedium*, ，和 *tshirtSizeLarge*。

如以下範例所示，這些變數會定義特定 T 恤大小的屬性。 每個識別 VM 類型、 磁碟大小、 相關聯的縮放單位資源範本 
若要連結至執行個體數目、 儲存體帳戶詳細資料和 jumpbox 狀態。

儲存體帳戶名稱前置詞取自使用者提供的參數，且連結的範本是由範本的基底 URL 及特定縮放單位資源範本的檔案名稱串連。

    "tshirtSizeSmall": {
      "vmSize": "Standard_A1",
            "diskSize": 1023,
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
      "vmCount": 2,
      "storage": {
        "name": "[parameters('storageAccountNamePrefix')]",
        "count": 1,
        "pool": "db",
        "map": [0,0],
        "jumpbox": 0
      }
    },
    "tshirtSizeMedium": {
      "vmSize": "Standard_A3",
      "diskSize": 1023,
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-8disk-resources.json')]",
      "vmCount": 2,
      "storage": {
        "name": "[parameters('storageAccountNamePrefix')]",
        "count": 2,
        "pool": "db",
        "map": [0,1],
        "jumpbox": 0
      }
    },
    "tshirtSizeLarge": {
      "vmSize": "Standard_A4",
      "diskSize": 1023,
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-16disk-resources.json')]",
      "vmCount": 3,
      "storage": {
        "name": "[parameters('storageAccountNamePrefix')]",
        "count": 2,
        "pool": "db",
        "map": [0,1,1],
        "jumpbox": 0
      }
    }

 *TshirtSize* 變數出現在 [變數] 區段。 結尾的 t 恤大小您提供 (*小型*, ，*媒體*, ，*大*) 會與文字串連 *tshirtSize* 擷取 
該 t 恤大小的的相關聯的複雜物件變數 ︰

    "tshirtSize": "[variables(concat('tshirtSize', parameters('tshirtSize')))]",

此變數將傳遞到連結的縮放單位資源範本。

##### networkSettings

在容量、功能或端對端範圍的解決方案範本中，連結的範本通常會建立存在於網路上的資源。 其中一個簡單的方法是使用複雜物件來儲存 
網路設定，並將其傳遞到連結的範本。

通訊網路設定的範例如下所示。

    "networkSettings": {
      "vnetName": "[parameters('virtualNetworkName')]",
      "addressPrefix": "10.0.0.0/16",
      "subnets": {
        "dmz": {
          "name": "dmz",
          "prefix": "10.0.0.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        },
        "data": {
          "name": "data",
          "prefix": "10.0.1.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        }
      }
    }

##### availabilitySettings

連結的範本中所建立的資源通常會放置在可用性集合中。 在下列範例中，已指定可用性集合名稱，以及要使用的容錯網域和更新網域計數。

    "availabilitySetSettings": {
      "name": "pgsqlAvailabilitySet",
      "fdCount": 3,
      "udCount": 5
    }

如果您需要多個可用性集合 （例如，一個用於主要節點），另一個用於資料節點，您可以使用名稱做為前置詞，指定多個可用性設定組，或依照稍早所示的模型 
用於建立特定 t 恤大小的變數。

##### storageSettings

儲存體詳細資料通常會與連結的範本共用。 在下列範例中， *storageSettings* 物件提供有關儲存體帳戶和容器名稱的詳細資料。

    "storageSettings": {
        "vhdStorageAccountName": "[parameters('storageAccountName')]",
        "vhdContainerName": "[variables('vmStorageAccountContainerName')]",
        "destinationVhdsContainer": "[concat('https://', parameters('storageAccountName'), variables('vmStorageAccountDomain'), '/', variables('vmStorageAccountContainerName'), '/')]"
    }

##### osSettings

利用連結的範本，您可能需要傳遞作業系統設定到各種節點類型，並橫跨不同的已知組態類型。 複雜物件是簡單的方法來儲存及共用作業系統資訊，也會使 
它支援多個作業系統選擇部署更容易。

下列範例顯示的物件， *osSettings*:

    "osSettings": {
      "imageReference": {
        "publisher": "Canonical",
        "offer": "UbuntuServer",
        "sku": "14.04.2-LTS",
        "version": "latest"
      }
    }

##### machineSettings

產生的變數， *machineSettings* 是複雜物件，包含混合核心變數來建立新的 VM ︰ 系統管理員使用者名稱和密碼、 VM 名稱，以及作業中的前置詞 
系統映像參考，如下所示 ︰

    "machineSettings": {
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "machineNamePrefix": "mongodb-",
        "osImageReference": {
            "publisher": "[variables('osFamilySpec').imagePublisher]",
            "offer": "[variables('osFamilySpec').imageOffer]",
            "sku": "[variables('osFamilySpec').imageSKU]",
            "version": "latest"
        }
    },

請注意， *osImageReference* 擷取的值從 *osSettings* 主要範本中定義的變數。 這表示您可以輕鬆變更 vm 的作業系統 — 完全或基礎 
在範本取用者的喜好設定。

##### vmScripts

 *VmScripts* 物件包含有關在 VM 執行個體，包括外部和內部參考上下載及執行的指令碼的詳細資訊。 外部參考包含基礎結構。 
內部參考包含已安裝的軟體和組態。

您使用 *scriptsToDownload* 屬性列出要下載至 VM 的指令碼。

如以下範例所示，此物件也包含對不同動作類型之命令列引數的參考。 這些動作包括執行的預設安裝 
每個個別的節點、 所部署的所有節點之後, 執行的安裝和其他任何指令碼可能與指定的範本。

這個範例來自用於部署 MongoDB 的範本，需要有仲裁程式以提供高可用性。  *ArbiterNodeInstallCommand* 已加入至 *vmScripts* 以安裝仲裁程式。

您可以在變數區段中找到定義特定文字以搭配適當的值執行指令碼的變數。

    "vmScripts": {
        "scriptsToDownload": [
            "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
            "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
        ],
        "regularNodeInstallCommand": "[variables('installCommand')]",
        "lastNodeInstallCommand": "[concat(variables('installCommand'), ' -l')]",
        "arbiterNodeInstallCommand": "[concat(variables('installCommand'), ' -a')]"
    },


## 從範本傳回狀態

您不只可以將資料傳遞到範本中，也可以與發出呼叫的範本共用資料。 在 **輸出** 區段的連結的範本，您可以提供可使用的索引鍵/值組 
由來源範本。

下列範例顯示如何傳遞在連結的範本中產生的私人 IP 位址。

    "outputs": {
        "masterip": {
            "value": "[reference(concat(variables('nicName'),0)).ipConfigurations[0].privateIPAddress]",
            "type": "string"
         }
    }

在主要範本中，您可以透過下列語法使用該資料：

    "masterIpAddress": {
        "value": "[reference('master-node').outputs.masterip.value]"
    }

## 後續步驟
- [編寫 Azure 資源管理員範本](resource-group-authoring-templates.md)
- [Azure 資源管理員範本函數](resource-group-template-functions.md)



