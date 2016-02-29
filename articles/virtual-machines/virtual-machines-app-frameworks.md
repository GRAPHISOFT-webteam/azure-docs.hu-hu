<properties
   pageTitle="應用程式架構 | Microsoft Azure"
   description="描述如何使用範本與 Azure 資源管理員，建立主流應用程式架構。 範例包括 LAMP 堆疊、SharePoint 以及 SQL Server。"
   services="virtual-machines"
   documentationCenter="virtual-machines"
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-resource-manager" />

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="infrastructure"
   ms.date="10/21/2015"
   ms.author="rasquill"/>

# 使用範本建立應用程式架構

請使用這個範本，快速設計出色的作品。 除了這些範本，您可以搜尋範本] (https://azure.microsoft.com/documentation/templates/)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] 傳統部署模型。


| 範本 | 說明 | 檢視範本 | 立即部署 |
|:---|:---|:---:|:---:|
| 部署 *n* Vm 快速 | 這是 Microsoft 建立的範本部署高達 *n* 新的 Vm (以及新虛擬網路和儲存體帳戶)。 | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/resource-loop-vms-vnet-aset/) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fresource-loop-vms-vnet-aset%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Active Directory 樹系和網域 | 這個範本會部署兩個新的 VM (以及一個新的虛擬網路、儲存體帳戶和負載平衡器) 並建立新的 Active Directory 樹系和網域。 會將每個 VM 建立成新網域的 DC，並放置在可用性集合。 每個 VM 也會加入具有負載平衡之公用 IP 位址的 RDP 端點。 | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/active-directory-new-domain-ha-2-dc) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Factive-directory-new-domain-ha-2-dc%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Apache 網頁伺服器 | 這個範本使用 Azure Linux CustomScript 延伸模組來部署 Apache 網頁伺服器。 這個範本會建立 Ubuntu VM、安裝 Apache2 以及建立一個簡單的 HTML 檔案。| [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/apache2-on-ubuntu-vm) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapache2-on-ubuntu-vm%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>
| Couchbase 叢集 | 這個範本會在 Ubuntu 虛擬機器上部署 Couchbase 叢集。 | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/couchbase-on-ubuntu) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fcouchbase-on-ubuntu%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| DataStax 叢集 | 這個範本會使用 Azure Linux CustomScript 延伸模組並在 Ubuntu VM 上安裝 DataStax 叢集。 [詳細的逐步解說。](virtual-machines-datastax-template.md)| [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/datastax-on-ubuntu) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fdatastax-on-ubuntu%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| DataStax Enterprise 叢集 | 這個範本會使用 Azure Linux CustomScript 擴充，在 Ubuntu VM 上安裝 DataStax Enterprise 叢集。 [詳細的逐步解說。](virtual-machines-datastax-enterprise-template.md)| [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/datastax-enterprise) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fdatastax-enterprise%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Django 應用程式 | 這個範本會使用 Azure Linux CustomScript 延伸模組，然後在 Ubuntu VM 上部署 Django 應用程式。 Django 應用程式也會無訊息安裝 Python 和 Apache。 | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/django-app) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fdjango-app%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Docker 容器 | 這個範本可讓您部署 Ubuntu VM 的 Docker (使用 Docker 延伸模組) 以及 3 個直接從 DockerHub 調度的 Docker 容器，然後藉由 Docker Compose 進行部署。 | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fdocker-simple-on-ubuntu%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Elasticsearch 叢集 | 這個範本會在 Ubuntu VM 上部署 Elasticsearch 叢集，然後使用範本連結功能來建立資料節點縮放單位。 | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/elasticsearch) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Felasticsearch%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Hortonworks HDP | 這個範本在 CentOS 虛擬機器上建立多伺服器的 Hortonworks HDP 2.2 Apache Hadoop 部署，並設定叢集中的 HDP 安裝。|  [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/hortonworks-on-centos) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fhortonworks-on-centos%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>|
| Jenkins 主要節點和從屬節點 | 這個範本會在 Ubuntu VM 上部署 Jenkins 主要節點，然後在兩個額外的 VM 上部署多個 Jenkins 從屬節點。 | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/jenkins-on-ubuntu) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fjenkins-on-ubuntu%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Kafka 叢集 | 這個範本會使用 Azure Linux CustomScript 延伸模組並在 Ubuntu VM 上部署 Kafka 叢集。 這個範本也會建立一個可公開使用且可以當做 "jumpbox" 的 VM ，讓您可以 SSH 連線到 Kafka 節點來診斷以及解決問題。| [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/kafka-on-ubuntu) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%kafka-on-ubuntu%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Ubuntu 上的 LAMP 堆疊 | 這個範本會使用 Azure Linux CustomScript 延伸模組來部署 LAMP 應用程式，方法是建立 Ubuntu VM、進行 MySQL、Apache 和 PHP 的無訊息安裝，然後建立簡單的 PHP 指令碼。 | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Flamp-app%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| MongoDB | 這個範本會使用 Linux CustomScript 延伸模組，然後在 Ubuntu 虛擬機器上部署 MongoDB。 [詳細的逐步解說。](virtual-machines-mongodb-template.md)| [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-ubuntu) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fmongodb-on-ubuntu%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Redis 叢集 | 這個範本會在 Ubuntu 虛擬機器上部署 Redis 叢集。 這個範本也會建立一個可公開使用且可以當做 "jumpbox" 的 VM ，讓您可以 SSH 連線到 Redis 節點來診斷以及解決問題。 [詳細的逐步解說。](virtual-machines-redis-template.md)| [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/redis-high-availability) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fredis-high-availability%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| SharePoint 伺服器陣列 | 此範本會建立三個新的 Azure VM，各有一個公用 IP 位址、負載平衡器和虛擬網路。 它會設定一個 VM 做為新樹系和網域的 Active Directory DC、一個已加入 SQL Server 網域的 VM，以及一個具有 SharePoint 伺服器陣列和網站的 VM。 所有 VM 都有對外公開的 RDP。 [詳細的逐步解說。](virtual-machines-rmtemplate-sharepoint-walkthrough.md) | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/sharepoint-three-vm) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsharepoint-three-vm%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Spark 叢集 | 這個範本會在 Ubuntu 虛擬機器上部署 Spark 叢集。 這個範本也會建立一個可公開使用且可以當做 "jumpbox" 的 VM ，讓您可以 SSH 連線到 Spark 節點來診斷以及解決問題。 [詳細的逐步解說。](virtual-machines-spark-template.md)| [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/spark-ubuntu-multidisks) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fspark-ubuntu-multidisks%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| 安裝 Tomcat 和 OpenJDK | 這個範本會在 Ubuntu VM 上安裝 OpenJDK 和 Tomcat。 | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/openjdk-tomcat-ubuntu-vm) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fopenjdk-tomcat-ubuntu-vm%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| WordPress | 此範本會在一個 Ubuntu VM 上部署完整的 LAMP 堆疊，然後安裝並初始化 WordPress。 | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/wordpress-single-vm-ubuntu) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fwordpress-single-vm-ubuntu%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| ZooKeeper 叢集 | 這個範本會在 Ubuntu VM 上建立一個由三個節點構成的 ZooKeeper 叢集。 | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/zookeeper-cluster-ubuntu-vm) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fzookeeper-cluster-ubuntu-vm%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |

## 後續步驟

探索可供您使用的所有範本上 [GitHub](https://github.com/Azure/azure-quickstart-templates)。

深入了解 [Azure 資源管理員](../resource-group-template-deploy.md)。

