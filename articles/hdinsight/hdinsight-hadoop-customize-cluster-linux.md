<properties
    pageTitle="使用指令碼動作來自訂 HDInsight 叢集 | Microsoft Azure"
    description="學習如何使用指令碼動作在以 Linux 為基礎的 HDInsight 叢集上新增自訂元件。 指令碼動作是在叢集建立期間執行的 Bash 指令碼，可用來自訂叢集組態，或新增其他服務和公用程式，如 Hue、Solr 或 R。"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="paulettm"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/20/2015"
    ms.author="larryfr"/>

# 使用指令碼動作來自訂 HDInsight 叢集| Azure (Linux)

HDInsight 提供組態選項，稱為 **指令碼動作** ，會叫用自訂指令碼，以定義要在建立程序，在叢集上執行自訂。 這些指令碼可用來在叢集上安裝額外的軟體或變更叢集上的應用程式組態。

> [AZURE.NOTE] 這篇文章中的資訊是以 Linux 為基礎的 HDInsight 叢集的特定項目。 本文旨在說明 windows 叢集的版本，請參閱 [使用 (Windows) 的指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster.md)

## 叢集建立程序中的指令碼動作

只有正在建立叢集時，才會使用指令碼動作。 下圖說明在建立程序期間執行指令碼動作的時間：

![HDInsight 叢集自訂和叢集建立期間的階段][img-hdi-cluster-states]

當設定 HDInsight 時，已執行指令碼。 在此階段，指令碼會以平行方式在叢集中所有指定的節點上執行，在節點上會以根權限執行。

> [AZURE.NOTE] 因為您有根權限時的指令碼是在叢集節點上執行，您可以執行作業，例如停止和啟動服務，包括 Hadoop 相關服務。 如果您停止服務，您必須在指令碼完成執行之前，確定 Ambari 服務及其他 Hadoop 相關服務已啟動並且正在執行。 這些服務必須在叢集建立時，成功地確定叢集的健康情況和狀態。

每個叢集可接受多個指令碼動作，這些指令碼會依其指定順序被叫用。 指令碼可在前端節點、背景工作角色節點或同時在兩者執行。

> [AZURE.IMPORTANT] 指令碼動作必須在 60 分鐘內完成，否則它們會逾時。 在節點佈建期間，會同時執行指令碼與其他安裝和設定程序。 與在您開發環境中的執行時間相較，爭用 CPU 時間和網路頻寬等資源可能會導致指令碼需要較長的時間才能完成。
> 
> 若要讓執行指令碼所花費的時間降到最低，請避免從原始程式碼下載和編譯應用程式之類的工作。 您應預先編譯相關應用程式，並將二進位檔儲存在 Azure Blob 儲存體中，這樣可讓其能夠快速地下載到叢集。


## 範例指令碼動作指令碼

從 Azure 入口網站、Azure PowerShell 或 HDInsight .NET SDK，可以使用指令碼動作指令碼。 本文說明如何從入口網站使用指令碼動作。 若要了解如何使用 PowerShell 和.NET SDK 以使用指令碼動作，請查看下表所列的範例。

HDInsight 提供數個指令碼在 HDInsight 叢集上安裝下列元件：

名稱 | 指令碼
----- | -----
**安裝色調** | https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/install-hue-uber-v01.sh。 請參閱 [色調在 HDInsight 上的叢集安裝和使用](hdinsight-hadoop-hue-linux.md)。
**安裝 Spark** | https://hdiconfigactions.blob.core.windows.net/linuxsparkconfigactionv02/spark-installer-v02.sh。 請參閱 [HDInsight 上的 Spark 叢集的安裝和使用](hdinsight-hadoop-spark-install-linux.md)。
**安裝 R** | https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh。 請參閱 [安裝和使用 R HDInsight 叢集上的](hdinsight-hadoop-r-scripts-linux.md)。
**安裝 Solr** | https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh。 請參閱 [安裝和使用 HDInsight 叢集上 Solr](hdinsight-hadoop-solr-install-linux.md)。
**安裝 Giraph** | https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh。 請參閱 [Giraph 的 HDInsight 上的叢集安裝和使用](hdinsight-hadoop-giraph-install-linux.md)。

## 從 Azure 入口網站使用指令碼動作

1. 開始建立叢集述 [建立 Hadoop 叢集的 HDInsight](hdinsight-provision-clusters.md#portal)。

2. 下 __選擇性組態__, ，如 **指令碼動作** 刀鋒視窗中，按一下 [ **加入指令碼動作** 提供有關指令碼動作，詳細資料，如下所示 ︰

    ![使用指令碼動作以自訂叢集](./media/hdinsight-hadoop-customize-cluster-linux/HDI.CreateCluster.8.png)

  	| 屬性 | 值 |
  	| -------- | ----- |
  	| 名稱 | 指定指令碼動作的名稱。 |
  	| 指令碼 URI | 對自訂叢集所叫用的指令碼指定 URI。 |
  	| Head/Worker | 指定的節點 (**Head**, ，**工作者**, ，或 **ZooKeeper**) 上執行自訂指令碼。 |
  	| 參數 | 如果指令碼要求，請指定參數。 |

    請按 ENTER 加入一個以上的指令碼動作，以在叢集上安裝多個元件。

3. 按一下 [ **選取** 儲存指令碼動作組態，然後繼續建立叢集。

## 從 Azure 資源管理員範本使用指令碼動作

在本節中，我們使用 Azure 資源管理員 (ARM) 範本來建立 HDInsight 叢集，並且也使用指令碼動作在叢集上安裝自訂元件 (在此範例中為 R)。 本節提供範例 ARM 範本以使用指令碼動作建立叢集。

### 開始之前

* 如需設定工作站以執行 HDInsight Powershell cmdlet 的資訊，請參閱 [安裝和設定 Azure PowerShell](../powershell-install-configure.md)。
* 如需如何建立 ARM 範本的指示，請參閱 [撰寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)。
* 如果您沒有先前已使用 Azure PowerShell 與資源管理員，請參閱 [使用 Azure PowerShell 與 Azure 資源管理員](../powershell-azure-resource-manager.md)。

### 使用指令碼動作來建立叢集

1. 將下列範本複製到您的電腦上的位置。 此範本會在叢集中的前端節點和背景工作角色節點上安裝 R。 您也可以確認 JSON 範本是否有效。 貼上您的範本內容讀入 [JSONLint](http://jsonlint.com/), ，線上的 JSON 驗證工具。

            {
            "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "clusterLocation": {
                    "type": "string",
                    "defaultValue": "West US",
                    "allowedValues": [ "West US" ]
                },
                "clusterName": {
                    "type": "string"
                },
                "clusterUserName": {
                    "type": "string",
                    "defaultValue": "admin"
                },
                "clusterUserPassword": {
                    "type": "securestring"
                },
                "sshUserName": {
                    "type": "string",
                    "defaultValue": "username"
                },
                "sshPassword": {
                    "type": "securestring"
                },
                "clusterStorageAccountName": {
                    "type": "string"
                },
                "clusterStorageAccountResourceGroup": {
                    "type": "string"
                },
                "clusterStorageType": {
                    "type": "string",
                    "defaultValue": "Standard_LRS",
                    "allowedValues": [
                        "Standard_LRS",
                        "Standard_GRS",
                        "Standard_ZRS"
                    ]
                },
                "clusterStorageAccountContainer": {
                    "type": "string"
                },
                "clusterHeadNodeCount": {
                    "type": "int",
                    "defaultValue": 1
                },
                "clusterWorkerNodeCount": {
                    "type": "int",
                    "defaultValue": 2
                }
            },
            "variables": {
            },
            "resources": [
                {
                    "name": "[parameters('clusterStorageAccountName')]",
                    "type": "Microsoft.Storage/storageAccounts",
                    "location": "[parameters('clusterLocation')]",
                    "apiVersion": "2015-05-01-preview",
                    "dependsOn": [ ],
                    "tags": { },
                    "properties": {
                        "accountType": "[parameters('clusterStorageType')]"
                    }
                },
                {
                    "name": "[parameters('clusterName')]",
                    "type": "Microsoft.HDInsight/clusters",
                    "location": "[parameters('clusterLocation')]",
                    "apiVersion": "2015-03-01-preview",
                    "dependsOn": [
                        "[concat('Microsoft.Storage/storageAccounts/', parameters('clusterStorageAccountName'))]"
                    ],
                    "tags": { },
                    "properties": {
                        "clusterVersion": "3.2",
                        "osType": "Linux",
                        "clusterDefinition": {
                            "kind": "hadoop",
                            "configurations": {
                                "gateway": {
                                    "restAuthCredential.isEnabled": true,
                                    "restAuthCredential.username": "[parameters('clusterUserName')]",
                                    "restAuthCredential.password": "[parameters('clusterUserPassword')]"
                                }
                            }
                        },
                        "storageProfile": {
                            "storageaccounts": [
                                {
                                    "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                                    "isDefault": true,
                                    "container": "[parameters('clusterStorageAccountContainer')]",
                                    "key": "[listKeys(resourceId(parameters('clusterStorageAccountResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).key1]"
                                }
                            ]
                        },
                        "computeProfile": {
                            "roles": [
                                {
                                    "name": "headnode",
                                    "targetInstanceCount": "[parameters('clusterHeadNodeCount')]",
                                    "hardwareProfile": {
                                        "vmSize": "Large"
                                    },
                                    "osProfile": {
                                        "linuxOperatingSystemProfile": {
                                            "username": "[parameters('sshUserName')]",
                                            "password": "[parameters('sshPassword')]"
                                        }
                                    },
                                    "scriptActions": [
                                        {
                                            "name": "installR",
                                            "uri": "https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh",
                                            "parameters": ""
                                        }
                                    ]
                                },
                                {
                                    "name": "workernode",
                                    "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                                    "hardwareProfile": {
                                        "vmSize": "Large"
                                    },
                                    "osProfile": {
                                        "linuxOperatingSystemProfile": {
                                            "username": "[parameters('sshUserName')]",
                                            "password": "[parameters('sshPassword')]"
                                        }
                                    },
                                    "scriptActions": [
                                        {
                                            "name": "installR",
                                            "uri": "https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh",
                                            "parameters": ""
                                        }
                                    ]
                                }
                            ]
                        }
                    }
                }
            ],
            "outputs": {
                "cluster":{
                    "type" : "object",
                    "value" : "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                }
            }
        }

2. 啟動 Azure PowerShell 並且登入您的 Azure 帳戶。 提供您的認證之後，命令會傳回您的帳戶的相關資訊。

        Add-AzureRmAccount

        Id                             Type       ...
        --                             ----
        someone@example.com            User       ...

3. 如果您有多個訂用帳戶，請提供您想要用於部署的訂用帳戶識別碼。

        Select-AzureRmSubscription -SubscriptionID <YourSubscriptionId>

    > [AZURE.NOTE] 您可以使用 `Get-AzureRmSubscription` 來取得您的帳戶，其中包含每個訂用帳戶 Id 相關聯的所有訂閱的清單。

5. 如果您沒有現有資源群組，請建立新的資源群組。 提供您的解決方案所需的資源群組名稱和位置。 隨即傳回新資源群組的摘要。

        New-AzureRmResourceGroup -Name myresourcegroup -Location "West US"

        ResourceGroupName : myresourcegroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup


6. 若要建立新的部署資源群組，請執行 **新增 AzureRmResourceGroupDeployment** 命令，並提供必要的參數。 參數會包含您部署的名稱、資源群組的名稱、您建立之範本的路徑或 URL。 如果您的範本需要任何參數，您也必須傳遞這些參數。 在此案例中，用來在叢集上安裝 R 的指令碼動作不需要任何參數。


        New-AzureRmResourceGroupDeployment -Name mydeployment -ResourceGroupName myresourcegroup -TemplateFile <PathOrLinkToTemplate>


    You will be prompted to provide values for the parameters defined in the template.

7. 部署資源群組之後，您會看到部署的摘要。

          DeploymentName    : mydeployment
          ResourceGroupName : myresourcegroup
          ProvisioningState : Succeeded
          Timestamp         : 8/17/2015 7:00:27 PM
          Mode              : Incremental
          ...

8. 如果您的部署失敗，您可以使用下列 Cmdlet 來取得失敗的相關資訊。

        Get-AzureRmResourceGroupDeployment -ResourceGroupName myresourcegroup -ProvisioningState Failed

## 從 Azure PowerShell 使用指令碼動作

在本節中，我們使用 [新增 AzureRmHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx) 指令程式可使用指令碼動作以自訂叢集所叫用指令碼。 在繼續之前，請確認您已安裝和設定 Azure PowerShell。 如需設定工作站以執行 HDInsight PowerShell cmdlet 的資訊，請參閱 [安裝和設定 Azure PowerShell](../powershell-install-configure.md)。

執行下列步驟：

1. 開啟 Azure PowerShell 主控台，並宣告下列變數：

        # PROVIDE VALUES FOR THESE VARIABLES
        $subscriptionId = "<SubscriptionId>"        # ID of the Azure subscription
        $clusterName = "<HDInsightClusterName>"         # HDInsight cluster name
        $storageAccountName = "<StorageAccountName>"    # Azure storage account that hosts the default container
        $storageAccountKey = "<StorageAccountKey>"      # Key for the storage account
        $containerName = $clusterName
        $location = "<MicrosoftDataCenter>"             # Location of the HDInsight cluster. It must be in the same data center as the storage account.
        $clusterNodes = <ClusterSizeInNumbers>          # The number of nodes in the HDInsight cluster.
        $resourceGroupName = "<ResourceGroupName>"      # The resource group that the HDInsight cluster will be created in

2. 指定組態值 (例如叢集中的節點) 和要使用的預設儲存體。

        # SPECIFY THE CONFIGURATION OPTIONS
        Select-AzureRmSubscription -SubscriptionId $subscriptionId
        $config = New-AzureRmHDInsightClusterConfig
        $config.DefaultStorageAccountName="$storageAccountName.blob.core.windows.net"
        $config.DefaultStorageAccountKey=$storageAccountKey

3. 使用 **新增 AzureRmHDInsightScriptAction** cmdlet 來叫用指令碼。 下列範例使用會在叢集上安裝 R 的指令碼：

        # INVOKE THE SCRIPT USING THE SCRIPT ACTION FOR HEADNODE AND WORKERNODE
        $config = Add-AzureRmHDInsightScriptAction -Config $config -Name "Install R"  -NodeType HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh
        $config = Add-AzureRmHDInsightScriptAction -Config $config -Name "Install R"  -NodeType WorkerNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh

     **新增 AzureRmHDInsightScriptAction** cmdlet 可接受下列參數 ︰

  	| 參數 | 定義 |
  	| --------- | ---------- |
  	| 設定 | 要在其中新增指令碼動作資訊的組態物件。 |
  	| 名稱 | 指令碼動作的名稱。 |
  	| NodeType | 指定執行自訂指定碼的節點。 有效值為 **前端節點** （若要在前端節點上安裝）， **WorkerNode** （若要在所有資料節點上安裝），或 **ZookeeperNode** （到 zookeeper 節點上安裝）。 |
  	| 參數 | 指令碼所需的參數。 |
  	| Uri | 指定所執行之指令碼的 URI。 |

4. 為此叢集設定 admin/HTTPS 使用者：

        $httpCreds = get-credential
        
    系統顯示提示時，請輸入 'admin' 做為名稱，並提供密碼。

5. 設定 SSH 認證：

        $sshCreds = get-credential
    
    出現提示時，請輸入 SSH 使用者名稱和密碼。 如果您想要使用認證 (而非密碼) 來保護 SSH 帳戶，請使用空白密碼並將 `$sshPublicKey` 設為您想使用之憑證公開金鑰的內容。 例如：
    
        $sshPublicKey = Get-Content .\path\to\public.key -Raw
    
4. 最後，建立叢集：
        
        New-AzureRmHDInsightCluster -config $config -clustername $clusterName -DefaultStorageContainer $containerName -Location $location -ResourceGroupName $resourceGroupName -ClusterSizeInNodes $clusterNodes -HttpCredential $httpCreds -SshCredential $sshCreds -OSType Linux
    
    如果您是使用公開金鑰來保護 SSH 帳戶，您也必須指定 `-SshPublicKey $sshPublicKey` 做為參數。

建立叢集可能需要幾分鐘的時間。

## 從 HDInsight .NET SDK 使用指令碼動作

HDInsight .NET SDK 提供用戶端程式庫，讓您輕鬆地從 .NET 應用程式使用 HDInsight。 下列步驟示範如何使用指令碼從 HDInsight .NET SDK 自訂叢集。

> [AZURE.IMPORTANT] 您必須先建立自我簽署的憑證，將它安裝在您的工作站，然後將它上傳至您的 Azure 訂閱。 如需指示，請參閱 [建立自我簽署的憑證](http://go.microsoft.com/fwlink/?LinkId=511138)。


### 建立 Visual Studio 專案


1. 在 Visual Studio 建立 C# 主控台應用程式。
2. 從 Nuget **Package Manager Console**, ，執行下列命令 ︰

        Install-Package Microsoft.Azure.Common.Authentication -pre
        Install-Package Microsoft.Azure.Management.HDInsight -Pre

    這些命令會將 .NET 程式庫及其參考新增至目前的 Visual Studio 專案。

3. 開啟 **Program.cs**, ，並新增下列 using 陳述式 ︰

        using System;
        using System.Security;
        using Microsoft.Azure;
        using Microsoft.Azure.Common.Authentication;
        using Microsoft.Azure.Common.Authentication.Factories;
        using Microsoft.Azure.Common.Authentication.Models;
        using Microsoft.Azure.Management.HDInsight;
        using Microsoft.Azure.Management.HDInsight.Models;

4. 使用下列程式碼取代類別中的程式碼：

        private static HDInsightManagementClient _hdiManagementClient;

        private static Guid SubscriptionId = new Guid("<AZURE SUBSCRIPTION ID>");
        private const string ResourceGroupName = "<AZURE RESOURCEGROUP NAME>";

        private const string NewClusterName = "<HDINSIGHT CLUSTER NAME>";
        private const int NewClusterNumNodes = <NUMBER OF NODES>;
        private const string NewClusterLocation = "<LOCATION>";  // Must match the Azure Storage account location
        private const string NewClusterVersion = "3.2";
        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
        private const OSType NewClusterOSType = OSType.Linux;

        private const string ExistingStorageName = "<STORAGE ACCOUNT NAME>.blob.core.windows.net";
        private const string ExistingStorageKey = "<STORAGE ACCOUNT KEY>";
        private const string ExistingContainer = "<DEFAULT CONTAINER NAME>"; 

        private const string NewClusterUsername = "admin";
        private const string NewClusterPassword = "<HTTP USER PASSWORD>";

        private const string NewClusterSshUserName = "sshuser";
        private const string NewClusterSshPublicKey = @"---- BEGIN SSH2 PUBLIC KEY ----
            Comment: ""rsa-key-20150731""
            AAAAB3NzaC1yc2EAAAABJQAAAQEA4QiCRLqT7fnmUA5OhYWZNlZo6lLaY1c+IRsp
            gmPCsJVGQLu6O1wqcxRqiKk7keYq8bP5s30v6bIljsLZYTnyReNUa5LtFw7eauGr
            yVt3Pve6ejfWELhbVpi0iq8uJNFA9VvRkz8IP1JmjC5jsdnJhzQZtgkIrdn3w0e6
            WVfu15kKyY8YAiynVbdV51EB0SZaSLdMZkZQ81xi4DDtCZD7qvdtWEFwLa+EHdkd
            pzO36Mtev5XvseLQqzXzZ6aVBdlXoppGHXkoGHAMNOtEWRXpAUtEccjpATsaZhQR
            zZdZlzHduhM10ofS4YOYBADt9JohporbQVHM5w6qUhIgyiPo7w==
            ---- END SSH2 PUBLIC KEY ----"; //replace the public key with your own

        private static void Main(string[] args)
        {
            var tokenCreds = GetTokenCloudCredentials();
            var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);

            _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);

            CreateCluster();
        }

        public static SubscriptionCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
        {
            var authFactory = new AuthenticationFactory();

            var account = new AzureAccount { Type = AzureAccount.AccountType.User };

            if (username != null && password != null)
                account.Id = username;

            var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];

            var accessToken =
                authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto)
                    .AccessToken;

            return new TokenCloudCredentials(accessToken);
        }

        public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
        {
            return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
        }


        private static void CreateCluster()
        {
            var parameters = new ClusterCreateParameters
            {
                ClusterSizeInNodes = NewClusterNumNodes,
                Location = NewClusterLocation,
                ClusterType = NewClusterType,
                OSType = NewClusterOSType,
                Version = NewClusterVersion,

                DefaultStorageAccountName = ExistingStorageName,
                DefaultStorageAccountKey = ExistingStorageKey,
                DefaultStorageContainer = ExistingContainer,

                UserName = NewClusterUsername,
                Password = NewClusterPassword,
                SshUserName = NewClusterSshUserName,
                SshPublicKey = NewClusterSshPublicKey
            };

            ScriptAction rScriptAction = new ScriptAction("Install R",
                new Uri("https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh"), "");

            parameters.ScriptActions.Add(ClusterNodeType.HeadNode,new System.Collections.Generic.List<ScriptAction> { rScriptAction});
            parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { rScriptAction });

            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
        }
        
6. 取代類別成員值。

7. 按下 **F5** 執行應用程式。 主控台視窗會開啟並顯示應用程式的狀態。 系統也會提示您輸入 Azure 帳戶認證。 建立 HDInsight 叢集可能需要幾分鐘的時間。


## 疑難排解

您可以使用 Ambari Web UI 來檢視在叢集建立期間指令碼記錄的資訊。 不過，如果叢集建立因為指令碼錯誤而失敗，與該叢集相關聯的預設儲存體帳戶中也會有記錄檔。 本節提供關於如何使用這兩個選項擷取記錄檔的資訊。

### 使用 Ambari Web UI

1. 在瀏覽器中瀏覽 https://CLUSTERNAME.azurehdinsight.net。 將 CLUSTERNAME 取代為 HDInsight 叢集的名稱。

    出現提示時，輸入管理帳戶名稱 (admin) 和叢集的密碼。 您可能必須在 Web 表單中重新輸入系統管理員認證。

2. 從頁面頂端列中，選取 __ops__ 項目。 這會顯示透過 Ambari 在叢集上執行的目前和先前作業的清單。

    ![Ambari Web UI 列與選取的 ops](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. 找出具有項目 __run\_customscriptaction__ 中 __作業__ 資料行。 這些項目是在執行指令碼動作時建立的。

    ![作業的螢幕擷取畫面](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

    選取此項目，並且向下鑽研連結以檢視在叢集上執行指令碼時，產生的 STDOUT 和 STDERR 輸出。

### 從預設的儲存體帳戶存取記錄檔

如果叢集建立因為指令碼動作中的錯誤而失敗，仍可從與該叢集相關聯的預設儲存體帳戶直接存取指令碼動作記錄檔。

* 儲存體記錄檔位於 `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`。 

    ![作業的螢幕擷取畫面](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

    在其下，記錄檔會個別針對前端節點、背景工作節點和 Zookeeper 節點進行組織。 部分範例如下：
    * **前端節點** - `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`
    * **背景工作節點** - `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`
    * **Zookeeper 節點** - `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* 對應主機的所有 stdout 和 stderr 都會上傳至儲存體帳戶。 有一個 **輸出 \*.txt** 和 **錯誤 \*.txt** 針對每個指令碼動作。 output-*.txt 檔案包含在主機上執行之指令碼的 URI 相關資訊。 例如

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* 您有可能重複建立具有相同名稱的指令碼動作叢集。 在這種情況下，您可以根據 DATE 資料夾名稱來區分相關的記錄檔。 例如，在不同的日期為叢集 (mycluster) 建立的資料夾結構將是：
    * `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04`
    * `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* 如果您在同一天建立具有相同名稱的指令碼動作叢集，您可以使用唯一的前置詞來識別相關的記錄檔。

* 如果您是在那一天結束時建立叢集，則記錄檔可能會橫跨兩天。 在這種情況下，您會看到相同的叢集有兩個不同的日期資料夾。

* 將記錄檔上傳至預設容器可能需要 5 分鐘，特別是大型叢集。 因此，如果您想要存取記錄檔，則不應在指令碼動作失敗時立即刪除叢集。


## 支援在 HDInsight 叢集上使用開放原始碼軟體

Microsoft Azure HDInsight 服務是彈性的平台，可讓您使用以 Hadoop 形成之開放原始碼技術的生態系統，在雲端中建置巨量資料應用程式。 Microsoft Azure 會提供對開放原始碼技術支援的一般層級中所述 **支援範圍** 區段 [Azure 支援常見問題集網站](http://azure.microsoft.com/support/faq/)。 HDInsight 服務對於某些元件提供額外層級的支援，如下所述。

HDInsight 服務中有兩種類型的開放原始碼元件可用：

- **內建元件** -這些元件預先安裝在 HDInsight 叢集上，並且提供叢集的核心功能。 例如，YARN ResourceManager、Hive 查詢語言 (HiveQL) 及 Mahout 程式庫都屬於這個類別。 叢集元件的完整清單可於 [HDInsight 所提供 Hadoop 叢集版本的新功能](hdinsight-component-versioning.md)中取得。

- **自訂元件** -您為叢集中，使用者可以安裝或使用您的工作負載在社群中可用或是您建立的任何元件。

> [AZURE.WARNING] 隨附於 HDInsight 叢集的元件受到完整支援，並以隔離並解決這些元件的相關問題協助 Microsoft 支援服務。
>
> 自訂元件則獲得商務上合理的支援，協助您進一步疑難排解問題。 如此可能會進而解決問題，或要求您利用可用管道，以找出開放原始碼技術，從中了解該技術的深度專業知識。 例如，有許多社群網站可以使用，像是 ︰ [適用於 HDInsight 的 MSDN 論壇](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), ，[http://stackoverflow.com](http://stackoverflow.com)。 Apache 專案也有專案網站 [http://apache.org](http://apache.org), ，例如 ︰ [Hadoop](http://hadoop.apache.org/), ，[Spark](http://spark.apache.org/)。

HDInsight 服務提供數種方式以使用自訂元件。 無論元件如何使用或如何安裝在叢集上，都適用相同層級的支援。 以下是自訂元件可用於 HDInsight 叢集之最常見方式的清單：

1. 工作提交 - Hadoop 或其他類型的工作，執行或使用可以提交給叢集的自訂元件。

2. 叢集自訂 - 在叢集建立期間，您可以指定額外設定和將會安裝在叢集節點上的自訂元件。

3. 範例 - 對於熱門自訂元件，Microsoft 和其他提供者可能會提供如何在 HDInsight 叢集上使用這些元件的範例。 提供這些範例，但是沒有支援。

## 後續步驟

請參閱下列項目，以取得建立和使用指令碼以自訂叢集時的資訊和範例：

- [開發 HDInsight 的指令碼動作指令碼](hdinsight-hadoop-script-actions-linux.md)
- [在 HDInsight 叢集上安裝及使用 Spark](hdinsight-hadoop-spark-install-linux.md)
- [在 HDInsight 叢集上安裝及使用 R](hdinsight-hadoop-r-scripts-linux.md)
- [在 HDInsight 叢集上安裝及使用 Solr](hdinsight-hadoop-solr-install-linux.md)
- [在 HDInsight 叢集上安裝及使用 Giraph](hdinsight-hadoop-giraph-install-linux.md)



[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Stages during cluster creation"


