---
title: "Fürtszolgáltatás SAP (A) SCS példányához, a Windows feladatátvevő fürt fájlmegosztást használja az Azure-on |} Microsoft Docs"
description: "Fürtszolgáltatás SAP (A) SCS példány fájlmegosztást használja a Windows feladatátvevő fürtön"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 94d725cfb072091e57c96d3b2aca7b2e73657eef
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2017
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[kb4025334]:https://support.microsoft.com/help/4025334/windows-10-update-kb4025334

[dv2-series]:https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dv2-series
[ds-series]:https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#ds-series

[sap-installation-guides]:http://service.sap.com/instguides

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-volumes-s2d-choosing-filesystem]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-filesystem
[choosing-the-size-of-volumes-s2d]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-size-of-volumes
[deploy-sofs-s2d-in-azure]:https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment
[s2d-in-win-2016]:https://docs.microsoft.com/en-us/windows-server/storage/storage-spaces/storage-spaces-direct-overview
[deep-dive-volumes-in-s2d]:https://blogs.technet.microsoft.com/filecab/2016/08/29/deep-dive-volumes-in-spaces-direct/

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP multi-SID high-availability configuration)

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png


[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


# <a name="clustering-sap-ascs-instance-on-windows-failover-cluster-using-file-share-on-azure"></a>Fürtszolgáltatás SAP (A) SCS példány fájlmegosztást használja az Azure-on Windows feladatátvevő fürtön

> ![Windows][Logo_Windows] Windows
>

Windows Server feladatátvételi fürtszolgáltatási az alapja az egy magas rendelkezésre állású SAP ASC/SCS telepítése és a Windows DBMS.

A feladatátvevő fürt egy 1 + n különálló kiszolgálók csoportja (csomópontok), amelyek együttműködése az alkalmazások és szolgáltatások rendelkezésre állásának javítása. Ha egy csomópont meghibásodik, Windows Server feladatátvételi fürtszolgáltatási számítja ki a biztosításához az alkalmazások és szolgáltatások kifogástalan fürt megőrzésével előforduló hibák száma. Választhat a különböző kvórummódok feladatátvételi fürtszolgáltatás eléréséhez.

## <a name="prerequisite"></a>Előfeltétel
Ügyeljen arra, hogy tekintse át ezeket a dokumentumokat, mielőtt kezdve ez a dokumentum:

* [Az Azure virtuális gépek magas rendelkezésre állású architektúra és SAP NetWeaver forgatókönyvei][sap-high-availability-architecture-scenarios]

> [!IMPORTANT]
>A fájlmegosztás SAP (A) SCS példányok fürtszolgáltatás támogatott **SAP NetWeaver 7.40 (vagy újabb)** termékek, a **SAP Kernel 7.49 (vagy újabb)**.
>


## <a name="windows-server-failover-clustering-in-azure"></a>Windows Server feladatátvételi fürtszolgáltatás az Azure-ban

Operációs rendszer nélküli vagy saját felhőben történő alkalmazáshoz képest, Azure virtuális gépek konfigurálása a Windows Server feladatátvételi fürtszolgáltatás további lépéseket igényel. A fürtöt hozza létre, ha több IP-címek és az SAP ASC/SCS példány virtuális állomásnevek be kell.

### <a name="name-resolution-in-azure-and-cluster-virtual-host-name"></a>Névfeloldás a Azure és a fürt virtuális gazdagép neve

Az Azure felhőalapú platform nem teszi lehetővé a virtuális IP-címek, például csak fix IP-címek konfigurálásához. Hozzon létre egy virtuális IP-címet a felhőben fürterőforrás elérni egy másik megoldásra van szüksége. Azure rendelkezik egy **belső terheléselosztó** az Azure terheléselosztó szolgáltatásban. A belső terheléselosztó rendelkező ügyfelek elérni a fürt keresztül a virtuális IP-címet. Kell telepítenie a belső terheléselosztó, amely tartalmazza a fürt csomópontjai az erőforráscsoportban. Ezt követően konfigurálja a belső terheléselosztó portok továbbítási szabályok a mintavételi minden szükséges portot. Az ügyfelek kapcsolódhatnak az virtuális állomás név. A DNS-kiszolgáló oldja fel a fürt IP-címét, és a belső terheléselosztó leírók port továbbítja a fürt aktív csomópontja.

![1. ábra: A Windows Server feladatátvételi fürtszolgáltatás konfigurációs az Azure-ban egy megosztott lemez nélkül][sap-ha-guide-figure-1001]

_**1. ábra:** Windows Server feladatátvételi fürtszolgáltatás konfigurációs az Azure-ban egy megosztott lemez nélkül_

## <a name="sap-ascs-ha-with-file-share"></a>(A) SCS magas rendelkezésre ÁLLÁSÚ fájlmegosztás az SAP

SAP fejlesztett új megközelítést és a megosztott lemezeket, SAP (A) SCS példány Windows feladatátvevő fürtre a fürt másodlagos.

Itt használható **SMB-fájlmegosztás** lehetősége van arra, hogy telepíteni **SAP globális állomás fájlok**.

> [!NOTE]
>SMB-fájlmegosztás SAP (A) SCS példányok fürtözéshez megosztott lemezt a fürt egy további lehetőség.  
>

Mi az az ebbe az architektúrába a következő:

* **SAP globális állomás fájlok elkülönülnek SAP központi szolgáltatások (a saját fájl struktúra és az üzenet és a sorba helyezni folyamatok)**
* **SAP központi szolgáltatások futtathatók SAP (A) SCS példány**
* SAP (A) SCS-példány fürtözött-e és elérhető használatával virtuális állomásnév **< (A) SCSVirtualHostName >**
* SAP globális fájlok kerülnek, SMB-fájlmegosztás, és hozzáférnek a <SAPGLOBALHost> állomásnév \\\\&lt;SAPGLOBALHost&gt;\sapmnt\\&lt;SID&gt;\SYS\...
* Az SAP (A) SCS-példány telepítve van a helyi lemez mindkét fürtcsomóponton
* A **< (A) SCSVirtualHostName >** hálózati neve eltér  **&lt;SAPGLOBALHost&gt;**

![2. ábra: Az új SAP (A) SCS magas rendelkezésre ÁLLÁSÚ architektúra az SMB-fájlmegosztásra][sap-ha-guide-figure-8004]

_**2. ábra:** új SAP (A) SCS magas rendelkezésre ÁLLÁSÚ architektúra az SMB-fájlmegosztásra_

SMB-fájlmegosztásra vonatkozó Előfeltételek:

* SMB 3.0-s (vagy magasabb) protokoll
* Az Active Directory (AD) hozzáférés-vezérlési lista (ACL) meg **AD felhasználói csoportokat** és **számítógép objektum számítógép$**
* Fájlmegosztás magas rendelkezésre ÁLLÁSÚ engedélyezve kell lennie:
    * Fájlok tárolására használt lemezek nem lehet a hibaérzékeny pontok kialakulását
    * Győződjön meg arról, hogy a kiszolgálók vagy virtuális gépek állásidő nem okoz a fájlmegosztás állásidő

Most a **SAP &lt;SID&gt;**  fürtszerepkör nem tartalmazó, fürt megosztott lemezek vagy általános fájlmegosztás fürterőforrás.


![3. ábra: < SID > SAP szerepkör fürterőforrások fájlmegosztás használata esetén][sap-ha-guide-figure-8005]

_**3. ábra:** **SAP &lt;SID&gt;**  szerepkör fürterőforrások fájlmegosztás használata esetén_


## <a name="scale-out-file-share-sofs-with-storage-spaces-direct-s2d-on-azure-as-sapmnt-file-share"></a>Kibővített fájlmegosztáson (SOFS) az Azure-on (S2D) tárolóhelyek – közvetlen, SAPMNT fájlmegosztás

Kibővíthető Fájlkiszolgáló is használhatja, és SAP globális állomás fájlok védelméhez, valamint magas rendelkezésre állású SAPMNT fájl megosztás szolgáltatását.

![4. ábra: SOFS fájlmegosztás SAP globális állomás fájlok védelmére szolgál][sap-ha-guide-figure-8006]

_**4. ábra:** SAP globális állomás fájlokat védő SOFS fájlmegosztás_

> [!IMPORTANT]
>Kibővíthető Fájlkiszolgáló fájlmegosztás teljes mértékben támogatott Microsoft Azure felhőbe is a helyszíni környezetben.
>

**Kibővíthető Fájlkiszolgáló** magas rendelkezésre állású és méretezhető vízszintesen SAPMNT fájlmegosztás kínál.

**Közvetlen tárolóhelyek (S2D)**, vettük **megosztott lemez** sofs-sel lehetővé teszi, hogy magas rendelkezésre állású és méretezhető tárolást biztosít helyi tárhellyel rendelkező kiszolgálók az összeállítása. Ezért használt sofs-sel, például SAP GLOBALHOST fájlok megosztott tárolót, nem a hibaérzékeny pontot (SPOF).

> [!IMPORTANT]
>Ha azt tervezi, a telepítő katasztrófa utáni helyreállításra, SOFS ajánlott megoldás magas rendelkezésre állású fájlmegosztás az Azure-ban.
>

### <a name="sap-prerequisites-for-sofs-in-azure"></a>Az Azure-ban SOFS SAP előfeltételei

A kibővíthető Fájlkiszolgáló van szüksége:

* Fürtcsomópontok közül legalább kettő az sofs-sel

* Minden csomópont legalább két helyi lemezzel kell rendelkeznie.

* A teljesítmény okból kell használnia **rugalmassági tükrözés**:
    * **2 – kétutas** tükrözést a kibővíthető Fájlkiszolgáló két fürtcsomópontra
    * **3 irányú** tükrözést a kibővíthető Fájlkiszolgáló fürt csomópontjainak három (vagy több)


* Az **javasoljuk, hogy 3 (vagy több fürt) csomópontokat az SOFS a 3 irányú tükrözés**.
A telepítő további méretezhetőséget és további tárolási rugalmasság, mint a kibővíthető Fájlkiszolgáló telepítése 2 fürtcsomópontok és a 2 – kétutas tükrözés kínál.

* Használjon **prémium szintű lemez**

* Az **ajánlott** használandó **Azure által kezelt lemezeken**

* Az **ajánlott** a új formátum kötetek **Resilient File System (ReFS)**
    * [SAP Megjegyzés 1869038 - SAP támogatja a ReFs fájlrendszer] [1869038]
    * Lásd: [kiválasztása a fájlrendszer] [ planning-volumes-s2d-choosing-filesystem] fejezet tervezése kötetei közvetlen tárolóhelyek.
    * Telepítse a [MS **KB4025334** összegző frissítés][kb4025334].


* Használhat **DS sorozatnak** vagy **DSv2-sorozat** Azure Virtuálisgép-méretek

* Ahhoz, hogy a virtuális gép hálózati teljesítmény közvetlen tárolóhelyek lemez szinkronizáláshoz szükséges többek jó, kell használnia, amely rendelkezik legalább egy Virtuálisgép-típussá **"magas" hálózati sávszélességet**.
További részletekért lásd: [DSv2-sorozat] [ dv2-series] és [DS sorozatnak] [ ds-series] megadását.

* Az **ajánlott** , hogy és **néhány tartalékkapacitás lefoglalva a tárolókészletben**. Kötetek kijavításához "helyben" meghajtó meghibásodik, a biztonsági adatok és a teljesítmény javítása után területet biztosít.

 További részletekért lásd: [kötetek méretének kiválasztása][choosing-the-size-of-volumes-s2d]


* Kibővíthető Fájlkiszolgáló Azure virtuális gépek kell telepíteni **saját Azure rendelkezésre állási csoport**

* Nincs szükség konfigurálása az Azure belső terheléselosztó SOFS fájlmegosztás hálózat neve, pl. <SAPGlobalHostName>, mert a elkészült a < (A) SCSVirtualHostname > SAP (A) SCS-példány vagy az adatbázis-kezelő. Kibővíthető Fájlkiszolgáló van kiterjesztése terhelés összes fürtcsomópont, így <SAPGlobalHostName> által használt helyi IP-címe fürt összes csomópontján.


> [!IMPORTANT]
>Nem lehet módosítani a SAPMNT fájlmegosztás, amely SAPGLOBALHOST mutat. SAP sapmnt nem támogatja egy másik megosztási nevet.
>[Megjegyzés 2492395 SAP - a megosztás neve sapmnt módosíthatók?][2492395]

### <a name="configuring-sap-ascs-instances-and-sofs-in-two-clusters"></a>(A) SCS konfigurálása SAP példányok és a kibővíthető Fájlkiszolgáló fürtök a

Lehetősége van egy fürtben, a saját SAP SAP (A) SCS példányok telepítése <SID> fürtszerepkört. A kibővíthető Fájlkiszolgáló fájlmegosztást a fürt egy másik szerepkört egy másik fürtben lett konfigurálva.

> [!IMPORTANT]
>Ebben az esetben az SAP (A) SCS példány úgy van konfigurálva, UNC elérési úton keresztül SAP GLOBALHost eléréséhez \\\\&lt;SAPGLOBALHost&gt;\sapmnt\\&lt;SID&gt;\SYS\...
>

![5. ábra: SAP (A) SCS-példány és a kibővíthető Fájlkiszolgáló fürtök telepítése][sap-ha-guide-figure-8007]

_**5. ábra:** SAP (A) SCS-példány és a kibővíthető Fájlkiszolgáló fürtök telepítése_

> [!IMPORTANT]
>Azure felhőben, az SAP, és a kibővíthető Fájlkiszolgáló fájl megosztások kell telepíteni a saját Azure rendelkezésre állási csoport használt a fürtökön biztosításához elosztva a fürt virtuális gépek elhelyezését az alapul szolgáló Azure-infrastruktúra.
>

## <a name="generic-file-share-with-sios-as-cluster-shared-disks"></a>Általános fájlmegosztás SIOS, a fürt megosztott lemezeket


> [!IMPORTANT]
>Kibővíthető Fájlkiszolgáló ajánlott magas rendelkezésre állású fájlmegosztás-megoldást.
>
>Azonban ha azt tervezi, továbbá hogy **vész-helyreállítási** a magas rendelkezésre állású fájlmegosztás kell használni általános fájlmegosztás és SISO technológia a fürt megosztott lemezeket.
>

Általános fájlmegosztás lehetősége, hogy magas rendelkezésre állású fájlmegosztás eléréséhez.

Itt mivel a fürt megosztott lemez 3. fél SIOS megoldás használhatja.

## <a name="next-steps"></a>Következő lépések

* [Az SAP magas rendelkezésre ÁLLÁSÚ Windows feladatátvevő fürt és a fájlmegosztás SAP (A) SCS-példány használata az Azure infrastruktúra előkészítése][sap-high-availability-infrastructure-wsfc-file-share]

* [SAP NetWeaver magas rendelkezésre ÁLLÁSÚ telepítés a Windows feladatátvevő fürt és a fájlmegosztás SCS példány SAP (A)][sap-high-availability-installation-wsfc-shared-disk]

* [Egy két csomópontos közvetlen tárolóhelyek kibővített fájlkiszolgáló telepítéséhez UPD tárolás az Azure-ban][deploy-sofs-s2d-in-azure]

* [Tárolóhelyek – közvetlen a Windows Server 2016][s2d-in-win-2016]

* [Részletes bemutatója: Kötetek a tárolóhelyek közvetlen][deep-dive-volumes-in-s2d]
