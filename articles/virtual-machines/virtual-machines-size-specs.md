<properties
 pageTitle="虛擬機器大小 | Microsoft Azure"
 description="列出虛擬機器的不同大小及其容量。"
 services="virtual-machines"
 documentationCenter=""
 authors="cynthn"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>

<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="infrastructure-services"
 ms.date="12/11/2015"
 ms.author="cynthn"/>


# 虛擬機器的大小

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## 概觀

本文說明以虛擬機器為基礎之計算資源的可用大小和選項，而您可以使用這類資源來執行應用程式和工作負載。 同時也提供當您規劃使用這些資源時所需注意的部署考量。 如需各種大小的定價資訊，請參閱 [虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/)。

若要查看 Azure Vm 上的一般限制，請參閱 [Azure 訂用帳戶和服務限制、 配額和條件約束](../azure-subscription-service-limits.md)。

標準大小包含幾個系列：A、D、DS、G 與 GS。 這些大小的一些考量事項包括：

*   D 系列 VM 是為了執行要求更高計算能力和暫存磁碟效能的應用程式所設計。 D 系列 VM 提供更快的處理器、較高的記憶體與核心比率，以及適用於暫存磁碟的固態硬碟 (SSD)。 如需詳細資訊，請參閱公告部落格， [新 D 系列虛擬機器大小](http://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/)。

*   Dv2 系列是原始 D 系列的延續，擁有更強大的 CPU。 Dv2 系列 CPU 比 D 系列 CPU 快約 35%。 它以最新一代的 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) 處理器為基礎，搭配 Intel Turbo Boost Technology 2.0，最高可達 3.2 GHz。 Dv2 系列的記憶體和磁碟組態和 D 系列一樣。

    Dv2 系列的區域可用性會根據這個排程:
        10 月 ' 15: 美國東部 2、 美國中部、 美國北部中部、 美國西部
        11 月 ' 15: 美國東部、 北歐、 西歐
        1 月 ' 16: 美國中南部、 APAC 東部、 APAC 東南亞、 日本東部、 日本西部、
                澳洲東部、 澳大利亞東南部巴西南部

*   G 系列 VM 提供最大的大小和最佳效能，並且可在具有 Intel Xeon E5 V3 系列處理器的主機上執行。

*   DS 系列和 GS 系列 VM 可以使用進階儲存體，針對使用大量 I/O 的工作負載提供高效能、低延遲的儲存體。 這些 VM 會使用固態硬碟 (SSD) 來裝載虛擬機器的磁碟，還可提供本機 SSD 磁碟快取。 僅特定地區可用進階儲存體。 如需詳細資訊，請參閱 [高階儲存體: Azure 虛擬機器工作負載的高效能儲存體](../storage-premium-storage-preview-portal.md)。

虛擬機器的大小會影響定價。 大小也會影響虛擬機器的處理、記憶體和儲存體容量。 儲存體成本會分別根據儲存體帳戶中使用的頁面來計算。 如需詳細資訊，請參閱 [虛擬機器定價詳細資料](http://azure.microsoft.com/pricing/details/virtual-machines/) 和 [Azure 儲存體定價](http://azure.microsoft.com/pricing/details/storage/)。 如需 VMss 的儲存體的詳細資訊，請參閱 [有關磁碟和虛擬機器的 Vhd ](virtual-machines-disks-vhds.md)。

下列考量可協助您決定大小：


*   某些 Azure 資料中心內的實體主機可能不支援較大的虛擬機器大小，例如 A5 – A11\。如此一來，您可能會看到錯誤訊息 * * 無法設定虛擬機器 <machine name>** 或 **無法建立虛擬機器 <machine name>* * 調整現有的虛擬機器到新的大小、 在 2013 年 4 月 16 日; 之前建立的虛擬網路中建立新的虛擬機器或將新的虛擬機器加入現有雲端服務時。請參閱  [錯誤: 「 無法設定虛擬機器 」](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) 支援論壇，每個部署案例的因應措施。

*   A8/A10 和 A9/A11 虛擬機器大小具有相同實體性能。 A8 和 A9 虛擬機器執行個體包含額外的網路介面卡，其會連線到遠端直接記憶體存取 (RDMA) 網路，以利在虛擬機器之間進行快速通訊。 A8 和 A9 執行個體是專為執行期間，節點之間需要常數和低延遲通訊的高效能計算應用程式所設計，例如，使用訊息傳遞介面 (MPI) 的應用程式。 A10 和 A11 虛擬機器執行個體不包含額外的網路介面卡。 A10 和 A11 執行個體專為不需要常數和低度延遲節點的高效能計算應用程式設計，也就是所謂的參數式或窘迫平行應用程式。

*   Dv2 系列，D 系列、 G 系列和 DS/GS 對應項目將是理想的應用程式需要更快的 Cpu、 更好的本機磁碟的效能，或更高的記憶體需求。 它們為許多企業級應用程式提供了強大的組合。





## 大小資料表

下表顯示其所提供的大小和容量。
>[AZURE.NOTE] 儲存體容量是使用 1024^3 位元組當作 GB 的度量單位來表示。 這有時稱為 gibibyte 或基底 2 定義。 比較使用不同基底系統的大小時，請記住，基底 2 的大小可能會顯示為小於基底 10，但是對於任何特定的大小 (例如 1 GB)，基底 2 的系統可提供比基底 10 系統更大的容量，因為 1024^3 大於 1000^3。



## 標準層級：A 系列

在傳統部署模型中，部分 VM 大小會與 Powershell 和 CLI 中的稍有不同。

* Standard_A0 是「特小型」
* Standard_A1 是「小型」
* Standard_A2 是「中型」
* Standard_A3 是「大型」
* Standard_A4 是「特大型」

<br>

| 大小| CPU 核心| 記憶體| NIC (最大)| 最大磁碟大小| 最大資料磁碟 (每個 1023 GB)| 最大IOPS (每個磁碟 500)|
|---|---|---|---|---|---|---|
| Standard_A0\特小型| 1| 768 MB| 1| 暫存 = 20 GB| 1| 1x500|
| Standard_A1\小型| 1| 1.75 GB| 1| 暫存 = 70 GB| 2| 2x500|
| Standard_A2\中型| 2| 3.5 GB| 1| 暫存 = 135 GB| 4| 4x500|
| Standard_A3\大型| 4| 7 GB| 2| 暫存 = 285 GB| 8| 8x500|
| Standard_A4\特大型| 8| 14 GB| 4| 暫存 = 605 GB| 16| 16x500|
| Standard_A5| 2| 14 GB| 1| 暫存 = 135 GB| 4| 4X500|
| Standard_A6| 4| 28 GB| 2| 暫存 = 285 GB| 8| 8x500|
| Standard_A7| 8| 56 GB| 4| 暫存 = 605 GB| 16| 16x500|
| Standard_A8| 8| 56 GB| 2| 暫存 = 382 GB 附註: 資訊及考量，需使用此大小，請參閱 [關於 A8、 A9、 A10 和 A11 計算密集型執行個體](http://go.microsoft.com/fwlink/p/?linkid=328042)。| 16| 16x500|
| Standard_A9| 16| 112 GB| 4| 暫存 = 382 GB 附註: 資訊及考量，需使用此大小，請參閱 [關於 A8、 A9、 A10 和 A11 計算密集型執行個體](http://go.microsoft.com/fwlink/p/?linkid=328042)。| 16| 16x500|
| Standard_A10| 8| 56 GB| 2| 暫存 = 382 GB 附註: 資訊及考量，需使用此大小，請參閱 [關於 A8、 A9、 A10 和 A11 計算密集型執行個體](http://go.microsoft.com/fwlink/p/?linkid=328042)。| 16| 16x500|
| Standard_A11| 16| 112 GB| 4| 暫存 = 382 GB 附註: 資訊及考量，需使用此大小，請參閱 [關於 A8、 A9、 A10 和 A11 計算密集型執行個體](http://go.microsoft.com/fwlink/p/?linkid=328042)。| 16| 16x500|

## 標準層級：D 系列

| 大小| CPU 核心| 記憶體| NIC (最大)| 最大磁碟大小| 最大資料磁碟 (每個 1023 GB)| 最大IOPS (每個磁碟 500)|
|---|---|---|---|---|---|---|
| 標準_D1| 1| 3.5 GB| 1| 暫存 (SSD) =50 GB| 2| 2x500|
| 標準_D2| 2| 7 GB| 2| 暫存 (SSD) = 100 GB| 4| 4x500|
| 標準_D3| 4| 14 GB| 4| 暫存 (SSD) = 200 GB| 8| 8x500|
| 標準_D4| 8| 28 GB| 8| 暫存 (SSD) = 400 GB| 16| 16x500|
| 標準_D11| 2| 14 GB| 2| 暫存 (SSD) = 100 GB| 4| 4x500|
| 標準_D12| 4| 28 GB| 4| 暫存 (SSD) = 200 GB| 8| 8x500|
| 標準_D13| 8| 56 GB| 8| 暫存 (SSD) = 400 GB| 16| 16x500|
| 標準_D14| 16| 112 GB| 8| 暫存 (SSD) = 800 GB| 32| 32x500|

## 標準層級：Dv2 系列

| 大小| CPU 核心| 記憶體| NIC (最大)| 最大磁碟大小| 最大資料磁碟 (每個 1023 GB)| 最大IOPS (每個磁碟 500)|
|---|---|---|---|---|---|---|
| Standard_D1_v2| 1| 3.5 GB| 1| 暫存 (SSD) =50 GB| 2| 2x500|
| Standard_D2_v2| 2| 7 GB| 2| 暫存 (SSD) = 100 GB| 4| 4x500|
| Standard_D3_v2| 4| 14 GB| 4| 暫存 (SSD) = 200 GB| 8| 8x500|
| Standard_D4_v2| 8| 28 GB| 8| 暫存 (SSD) = 400 GB| 16| 16x500|
| Standard_D5_v2| 16| 56 GB| 8| 暫存 (SSD) = 800 GB| 32| 32x500|
| Standard_D11_v2| 2| 14 GB| 2| 暫存 (SSD) = 100 GB| 4| 4x500|
| Standard_D12_v2| 4| 28 GB| 4| 暫存 (SSD) = 200 GB| 8| 8x500|
| Standard_D13_v2| 8| 56 GB| 8| 暫存 (SSD) = 400 GB| 16| 16x500|
| Standard_D14_v2| 16| 112 GB| 8| 暫存 (SSD) = 800 GB| 32| 32x500|

## 標準層級：DS 系列*

| 大小| CPU 核心| 記憶體| NIC (最大)| 最大磁碟大小| 最大資料磁碟 (每個 1023 GB)| 快取大小 (GB)| 最大磁碟 IOPS & 頻寬|
|---|---|---|---|---|---|---|---|
| Standard_DS1| 1| 3.5| 1| 本機 SSD 磁碟 = 7 GB| 2| 43| 3200 秒 32 MB|
| Standard_DS2| 2| 7| 2| 本機 SSD 磁碟 = 14 GB| 4| 86| 6400 每秒 64 MB|
| Standard_DS3| 4| 14| 4| 本機 SSD 磁碟 = 28 GB| 8| 172| 12800 秒 128 MB|
| Standard_DS4| 8| 28| 8| 本機 SSD 磁碟 = 56 GB| 16| 344| 25600 每秒 256 MB|
| Standard_DS11| 2| 14| 2| 本機 SSD 磁碟 = 28 GB| 4| 72| 6400 每秒 64 MB|
| Standard_DS12| 4| 28| 4| 本機 SSD 磁碟 = 56 GB| 8| 144| 12800 秒 128 MB|
| Standard_DS13| 8| 56| 8| 本機 SSD 磁碟 = 112 GB| 16| 288| 25600 每秒 256 MB|
| Standard_DS14| 16| 112| 8| 本機 SSD 磁碟 = 224 GB| 32| 576| 50000 每秒 512 MB|

*磁碟大小會影響 DS 系列 VM 每秒 (IOPS) 輸入/輸出作業量最大值，和可以達到最大輸送量 (頻寬)。 如需詳細資訊，請參閱 [高階儲存體: Azure 虛擬機器工作負載的高效能儲存體](../storage-premium-storage-preview-portal.md)。

## 標準層級：G 系列

| 大小| CPU 核心| 記憶體| NIC (最大)| 最大磁碟大小| 最大資料磁碟 (每個 1023 GB)| 最大IOPS (每個磁碟 500)|
|---|---|---|---|---|---|---|
| Standard_G1| 2| 28 GB| 1| 本機 SSD 磁碟 = 384 GB| 4| 4 x 500|
| Standard_G2| 4| 56 GB| 2| 本機 SSD 磁碟 = 768 GB| 8| 8 x 500|
| Standard_G3| 8| 112 GB| 4| 本機 SSD 磁碟 = 1,536 GB| 16| 16 x 500|
| Standard_G4| 16| 224 GB| 8| 本機 SSD 磁碟 = 3,072 GB| 32| 32 x 500|
| Standard_G5| 32| 448 GB| 8| 本機 SSD 磁碟 = 6,144 GB| 64| 64 x 500|

## 標準層級：GS 系列

| 大小| CPU 核心| 記憶體| NIC (最大)| 最大磁碟大小| 最大資料磁碟 (每個 1023 GB)| 快取大小 (GB)| 最大磁碟 IOPS & 頻寬|
|---|---|---|---|---|---|---|---|
| Standard_GS1| 2| 28| 1| 本機 SSD 磁碟 = 56 GB| 4| 264| 5000 秒 125 MB|
| Standard_GS2| 4| 56| 2| 本機 SSD 磁碟 = 112 GB| 8| 528| 10000 秒 250 MB|
| Standard_GS3| 8| 112| 4| 本機 SSD 磁碟 = 224 GB| 16| 1056| 20000 每秒 500 MB|
| Standard_GS4| 16| 224| 8| 本機 SSD 磁碟 = 448 GB| 32| 2112| 40000 每秒 1000 MB|
| Standard_GS5| 32| 448| 8| 本機 SSD 磁碟 = 896 GB| 64| 4224| 80000 每秒 2000 MB|


### 另請參閱

[Azure 訂用帳戶和服務限制、 配額和條件約束](../azure-subscription-service-limits.md)

[關於 A8、 A9、 A10 和 A11 計算密集型執行個體](virtual-machines-a8-a9-a10-a11-specs.md)







