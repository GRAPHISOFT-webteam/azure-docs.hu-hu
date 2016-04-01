<properties
    pageTitle="Microsoft Azure 訂用帳戶及服務限制、配額與限制"
    description="提供通用的 Azure 訂用帳戶和服務限制、配額和條件約束的清單。 這包括如何增加限制和最大值的詳細資訊。"
    services=""
    documentationCenter=""
    authors="rothja"
    manager="jeffreyg"
    editor="monicar"/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/03/2015"
    ms.author="jroth"/>

# Azure 訂閱和服務限制、配額與限制

## 概觀

本文件說明一些最常見的 Microsoft Azure 限制。 請注意，這目前未涵蓋所有 Azure 服務。 這些限制將隨著時間擴展並更新以涵蓋更多平台。

> [AZURE.NOTE] 如果您想要將限制提升到上述 **預設限制**, ，您可以 [開啟線上客戶支援要求，不另外加收費用](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 無法將限制提升到高於 **上限** 下表中的值。 如果沒有任何 **上限** 資料行，則指定的資源並沒有可調整的限制。

## 限制和 Azure 資源管理員

現在您可以結合多個 Azure 中的資源到單一的 Azure 資源群組。 使用資源群組時的限制是，在全域時會使用 Azure 資源管理員在地區層級管理。 如需 Azure 資源群組的詳細資訊，請參閱 [使用資源群組來管理您的 Azure 資源](resource-group-portal.md)。

在以下的限制中，已加入了新資料表，以反映在使用 Azure 資源管理員時的限制方面的任何差異。 例如，沒有 **訂用帳戶限制** 資料表和 **訂用帳戶限制-Azure 資源管理員** 資料表。 當某個限制同時適用於這兩個案例時，只會顯示在第一個資料表中。 除非另有說明，限制在所有區域中全域適用。

> [AZURE.NOTE] 請務必強調 Azure 資源群組中資源的配額是每個區域可以存取您的訂閱，並不是每個訂閱，因為服務管理配額。 讓我們以核心配額為例。 如果您需要要求增加配額以支援核心，您必須決定您想要在哪些區域中使用多少個核心，然後提出 Azure 資源群組核心配額的特定要求，以取得您想要的數量和區域。 因此，如果您需要在西歐使用 30 個核心以在該處執行應用程式，您應該在西歐特別要求 30 個核心。 但是您在任何其他區域中的核心配額將不會增加 -- 僅西歐會有 30 個核心配額。
<!-- -->
因此，考慮決定每個區域中您的工作負載所需的 Azure 資源群組配額，並在要考慮部署的每個區域中要求該數量可能會有所幫助。 請參閱 [移難排解部署問題](resource-group-deploy-debug.md##authentication-subscription-role-and-quota-issues) 取得探索您目前的配額的特定區域的詳細說明。


## 特定服務的限制

- [Active Directory](#active-directory-limits)
- [API 管理](#api-management-limits)
- [App Service](#app-service-limits)
- [Application Insights](#application-insights-limits)
- [Azure Redis 快取](#azure-redis-cache-limits)
- [Azure RemoteApp](#azure-remoteapp-limits)
- [備份](#backup-limits)
- [批次](#batch-limits)
- [BizTalk 服務](#biztalk-services-limits)
- [CDN](#cdn-limits)
- [雲端服務](#cloud-services-limits)
- [Data Factory](#data-factory-limits)
- [DNS](#dns-limits)
- [DocumentDB](#documentdb-limits)
- [IoT 中心](#iot-hub-limits)
- [金鑰保存庫](#key-vault-limits)
- [媒體服務](#media-services-limits)
- [Mobile Engagement](#mobile-engagement-limits)
- [行動服務](#mobile-services-limits)
- [Multi-Factor Authentication](#multi-factor-authentication)
- [網路](#networking-limits)
- [通知中樞服務](#notification-hub-service-limits)
- [Operational Insights](#operational-insights-limits)
- [資源群組](#resource-group-limits)
- [排程器](#scheduler-limits)
- [搜尋](#search-limits)
- [服務匯流排](#service-bus-limits)
- [站台復原](#site-recovery-limits)
- [SQL Database](#sql-database-limits)
- [儲存體](#storage-limits)
- [StorSimple 系統](#storsimple-system-limits)
- [串流分析](#stream-analytics-limits)
- [訂閱](#subscription-limits)
- [流量管理員](#traffic-manager-limits)
- [虛擬機器](#virtual-machines-limits)


### 訂閱限制
#### 訂閱限制
[AZURE.INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### 訂用帳戶限制 - Azure 資源管理員

使用 Azure 資源管理員和 Azure 資源群組時，適用下列限制。 使用 Azure 資源管理員時未變更的限制不會在以下列出。 請參閱先前的資料表來瞭解這些限制。

[AZURE.INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]


### 資源群組限制

[AZURE.INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]


### 虛擬機器限制
#### 虛擬機器限制
[AZURE.INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]


#### 虛擬機器限制 - Azure 資源管理員

使用 Azure 資源管理員和 Azure 資源群組時，適用下列限制。 使用 Azure 資源管理員時未變更的限制不會在以下列出。 請參閱先前的資料表來瞭解這些限制。

[AZURE.INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]


### 網路限制

[AZURE.INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### 網路限制
[AZURE.INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### 流量管理員限制

[AZURE.INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### DNS 限制

[AZURE.INCLUDE [dns-limits](../includes/dns-limits.md)]

### 儲存體限制

如需有關儲存體帳戶限制的詳細資訊，請參閱 [Azure 儲存體延展性和效能目標](../articles/storage/storage-scalability-targets.md)。

#### 儲存體服務限制

[AZURE.INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

#### 虛擬機器磁碟限制 

[AZURE.INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

請參閱 [虛擬機器大小](../articles/virtual-machines/virtual-machines-size-specs.md) 如需詳細資訊。

**標準儲存體帳戶**

[AZURE.INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

**進階儲存體帳戶**

[AZURE.INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

#### 儲存體資源提供者限制

[AZURE.INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]


### 雲端服務限制

[AZURE.INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]


### 應用程式服務限制
下列應用程式服務限制包含 Web 應用程式、行動應用程式、API 應用程式和邏輯應用程式的限制。

[AZURE.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### 排程器限制

[AZURE.INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### Batch 限制

[AZURE.INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

###BizTalk 服務限制
下表顯示 Azure Biztalk 服務的限制。

[AZURE.INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]


### DocumentDB 限制

[AZURE.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]


### Mobile Engagement 限制

[AZURE.INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]


### 搜尋限制

定價層會決定容量及您搜尋服務的限制。

####標準層級

[AZURE.INCLUDE [azure-search-limits-standard](../includes/azure-search-limits-standard.md)]

####共用層級 (多租用戶服務的一部分，Azure 訂閱者可免費使用)

[AZURE.INCLUDE [azure-search-limits-free](../includes/azure-search-limits-free.md)]

若要深入了解金鑰、 複本磁碟分割組合、 要求、 回應以及如何達成高可用性的各種不同工作負載的限制，請參閱 [服務在 Azure 搜尋服務的限制](search/search-limits-quotas-capacity.md)。

### 媒體服務限制

[AZURE.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### CDN 限制

[AZURE.INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### 行動服務限制

[AZURE.INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### 通知中樞服務限制

[AZURE.INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]


### 服務匯流排限制

[AZURE.INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### IoT 中樞限制

[AZURE.INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### Data Factory 限制

[AZURE.INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]


### 串流分析限制

[AZURE.INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### Active Directory 限制

[AZURE.INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]


### Azure RemoteApp 限制

[AZURE.INCLUDE [azure-remoteapp-limits](../includes/azure-remoteapp-limits.md)]

### StorSimple 系統限制

[AZURE.INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]


### Operational Insights 限制

[AZURE.INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### 備份限制

[AZURE.INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### 網站復原限制

[AZURE.INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### Application Insights 限制

[AZURE.INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### API 管理限制

[AZURE.INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### Azure Redis 快取限制

[AZURE.INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### 金鑰保存庫限制

[AZURE.INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### Multi-Factor Authentication
[AZURE.INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### SQL Database 限制

如需 SQL Database 限制，請參閱 [SQL Database 資源限制](sql-database/sql-database-resource-limits.md)。

## 另請參閱

[了解 Azure 限制和增加](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Azure 的虛擬機器和雲端服務大小](http://msdn.microsoft.com/library/azure/dn197896.aspx)


