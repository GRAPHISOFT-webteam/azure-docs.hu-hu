---
title: "Magánhálózati IP-címek konfigurálása virtuális gépek - Azure CLI 1.0 |} Microsoft Docs"
description: "Útmutató az Azure parancssori felület (CLI) 1.0 használó virtuális gépek magánhálózati IP-címek konfigurálásához."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 40b03a1a-ea00-454c-b716-7574cea49ac0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9179319095c31d5eb454860e173ffa7c65fc9f73
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-cli-10"></a>Az Azure CLI 1.0 használata virtuális gépek magánhálózati IP-címek konfigurálása


## <a name="cli-versions-to-complete-the-task"></a>A feladat befejezéséhez használható CLI-verziók 

A következő CLI-verziók egyikével elvégezheti a feladatot: 

- [Az Azure CLI 1.0](#how-to-specify-a-static-private-ip-address-when-creating-a-vm) – a parancssori felületen a klasszikus és resource management üzembe helyezési modellel (a cikk)
- [Az Azure CLI 2.0](virtual-networks-static-private-ip-arm-cli.md) -erőforrás felügyeleti telepítési modell a következő generációs parancssori felület 

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Ez a cikk a Resource Manager-alapú üzemi modellt ismerteti. Emellett [statikus magánhálózati IP-cím, a klasszikus üzembe helyezési modellel kezelése](virtual-networks-static-private-ip-classic-cli.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Az alábbi minta Azure parancssori felület parancsait már létrehozott egy egyszerű környezetben várható. Ha szeretné a parancsokat a jelen dokumentum megjelenített, először leírt tesztkörnyezet felépítéséhez [hozhat létre egy vnetet](virtual-networks-create-vnet-arm-cli.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>A statikus magánhálózati IP-cím megadása a virtuális gép létrehozásakor
Nevű virtuális gép létrehozása *DNS01* a a *előtér* egy vnet nevű alhálózat *TestVNet* statikus magánhálózati IP-címe a *192.168.1.101*, kövesse az alábbi lépéseket:

1. Ha még sosem használta az Azure CLI-t, akkor tekintse meg [Install and Configure the Azure CLI](../cli-install-nodejs.md) (Az Azure CLI telepítése és konfigurálása) részt, és kövesse az utasításokat addig a pontig, ahol ki kell választania az Azure-fiókot és -előfizetést.
2. Az **azure config mode** parancs futtatásával váltson az Erőforrás-kezelő módra, a lent látható módon.
   
        azure config mode arm
   
    Várt kimenet:
   
        info:    New mode is arm
3. Futtassa a **létrehozása azure-hálózat nyilvános ip-** egy nyilvános IP-címet a virtuális gép létrehozásához. A kimenet után látható lista ismerteti a használt paramétereket.
   
        azure network public-ip create -g TestRG -n TestPIP -l centralus
   
    Várt kimenet:
   
        info:    Executing command network public-ip create
        + Looking up the public ip "TestPIP"
        + Creating public ip address "TestPIP"
        + Looking up the public ip "TestPIP"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP
        data:    Name                            : TestPIP
        data:    Type                            : Microsoft.Network/publicIPAddresses
        data:    Location                        : centralus
        data:    Provisioning state              : Succeeded
        data:    Allocation method               : Dynamic
        data:    Idle timeout                    : 4
        info:    network public-ip create command OK
   
   * **-g (vagy --resource-group)**. A nyilvános IP-cím létrejön az erőforráscsoport neve.
   * **-n (vagy --name)**. A nyilvános IP-cím neve.
   * **-l (vagy --location)**. Azure-régió, ahol a nyilvános IP-cím létrejön. A mi esetünkben *centralus*.
4. Futtassa a **azure-hálózat hálózati adapter létrehozása** parancs futtatásával hozzon létre egy hálózati Adaptert statikus magánhálózati IP-címe. A kimenet után látható lista ismerteti a használt paramétereket.
   
        azure network nic create -g TestRG -n TestNIC -l centralus -a 192.168.1.101 -m TestVNet -k FrontEnd
   
    Várt kimenet:
   
        + Looking up the network interface "TestNIC"
        + Looking up the subnet "FrontEnd"
        + Creating network interface "TestNIC"
        + Looking up the network interface "TestNIC"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
        data:    Name                            : TestNIC
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : centralus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 192.168.1.101
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:
        info:    network nic create command OK
   
   * **-a (vagy--magánfelhő-ip-cím)**. Statikus magánhálózati IP-cím, az a hálózati adaptert.
   * **-m (vagy--vnet-alhálózatneve)**. A VNet neve, ahol a hálózati adapter létrejön.
   * **-k (vagy--alhálózati-név)**. Az alhálózat, ahol létrejön-e a hálózati adapter neve.
5. Futtassa a **azure virtuális gép létrehozása** parancsot a nyilvános IP- és a hálózati adapter a fenti létrehozott virtuális gép létrehozásához. A kimenet után látható lista ismerteti a használt paramétereket.
   
        azure vm create -g TestRG -n DNS01 -l centralus -y Windows -f TestNIC -i TestPIP -F TestVNet -j FrontEnd -o vnetstorage -q bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 -u adminuser -p AdminP@ssw0rd
   
    Várt kimenet:
   
        info:    Executing command vm create
        + Looking up the VM "DNS01"
        info:    Using the VM Size "Standard_A1"
        warn:    The image "bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2" will be used for VM
        info:    The [OS, Data] Disk or image configuration requires storage account
        + Looking up the storage account vnetstorage
        + Looking up the NIC "TestNIC"
        info:    Found an existing NIC "TestNIC"
        info:    Found an IP configuration with virtual network subnet id "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd" in the NIC "TestNIC"
        info:    Found public ip parameters, trying to setup PublicIP profile
        + Looking up the public ip "TestPIP"
        info:    Found an existing PublicIP "TestPIP"
        info:    Configuring identified NIC IP configuration with PublicIP "TestPIP"
        + Updating NIC "TestNIC"
        + Looking up the NIC "TestNIC"
        + Creating VM "DNS01"
        info:    vm create command OK
   
   * **-y (vagy--operációsrendszer-típust)**. Operációs rendszer a virtuális géphez, vagy típusú *Windows* vagy *Linux*.
   * **-f (vagy--nic-név)**. A hálózati Adaptert a virtuális gép nevét fogja használni.
   * **-i (vagy--nyilvános-ip-név)**. A nyilvános IP-címet a virtuális gép nevét fogja használni.
   * **-F (vagy--vnet-name)**. A VNet neve, ahol a virtuális gép létrejön.
   * **-j (vagy--vnet-alhálózat-név)**. Ahol a virtuális gép létrejön az alhálózat neve.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Hogyan lehet lekérni a statikus magánhálózati IP-címadatok a virtuális gépek
A statikus magánhálózati IP-címadatok a fenti parancsfájl létrehozza a virtuális gép megtekintéséhez futtassa a következő Azure CLI parancsot, és tekintse meg az értékeit *privát IP-foglalási-metódus* és *magánhálózati IP-cím*:

    azure vm show -g TestRG -n DNS01

Várt kimenet:

    info:    Executing command vm show
    + Looking up the VM "DNS01"
    + Looking up the NIC "TestNIC"
    + Looking up the public ip "TestPIP
    data:    Id                              :/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01
    data:    ProvisioningState               :Succeeded
    data:    Name                            :DNS01
    data:    Location                        :centralus
    data:    Type                            :Microsoft.Compute/virtualMachines
    data:
    data:    Hardware Profile:
    data:      Size                          :Standard_A1
    data:
    data:    Storage Profile:
    data:      Source image:
    data:        Id                          :/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/services/images/bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
    data:
    data:      OS Disk:
    data:        OSType                      :Windows
    data:        Name                        :cli08d7bd987a0112a8-os-1441774961355
    data:        Caching                     :ReadWrite
    data:        CreateOption                :FromImage
    data:        Vhd:
    data:          Uri                       :https://vnetstorage2.blob.core.windows.net/vhds/cli08d7bd987a0112a8-os-1441774961355vhd
    data:
    data:    OS Profile:
    data:      Computer Name                 :DNS01
    data:      User Name                     :adminuser
    data:      Windows Configuration:
    data:        Provision VM Agent          :true
    data:        Enable automatic updates    :true
    data:
    data:    Network Profile:
    data:      Network Interfaces:
    data:        Network Interface #1:
    data:          Id                        :/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
    data:          Primary                   :true
    data:          MAC Address               :00-0D-3A-90-1A-A8
    data:          Provisioning State        :Succeeded
    data:          Name                      :TestNIC
    data:          Location                  :centralus
    data:            Private IP alloc-method :Static
    data:            Private IP address      :192.168.1.101
    data:            Public IP address       :40.122.213.159
    info:    vm show command OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>A statikus magánhálózati IP-cím eltávolítása a virtuális gépek
Az Azure CLI az erőforrás-kezelő egy hálózati adapter nem távolítható el a statikus magánhálózati IP-cím. Kell hozzon létre egy új hálózati Adaptert, amely egy dinamikus IP-cím használ, távolítsa el a korábbi hálózati Adaptert a virtuális gép és az új hálózati majd hozzá a virtuális gép. A hálózati Adaptert a virtuális gép használt int eh a fenti parancsok módosításához kövesse az alábbi lépéseket.

1. Futtassa a **azure-hálózat hálózati adapter létrehozása** parancs futtatásával hozzon létre egy új hálózati Adaptert dinamikus IP-lefoglalást használatával. Figyelje meg, hogyan nem kell megadnia az IP-cím most.
   
        azure network nic create -g TestRG -n TestNIC2 -l centralus -m TestVNet -k FrontEnd
   
    Várt kimenet:
   
        info:    Executing command network nic create
        + Looking up the network interface "TestNIC2"
        + Looking up the subnet "FrontEnd"
        + Creating network interface "TestNIC2"
        + Looking up the network interface "TestNIC2"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2
        data:    Name                            : TestNIC2
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : centralus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 192.168.1.6
        data:      Private IP Allocation Method  : Dynamic
        data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:
        info:    network nic create command OK
2. Futtassa a **azure virtuálisgép-készlet** parancs futtatásával módosíthatja a virtuális gép által használt hálózati adapter.
   
        azure vm set -g TestRG -n DNS01 -N TestNIC2
   
    Várt kimenet:
   
        info:    Executing command vm set
        + Looking up the VM "DNS01"
        + Looking up the NIC "TestNIC2"
        + Updating VM "DNS01"
        info:    vm set command OK
3. Ha szeretett volna, futtassa a **azure-hálózat hálózati törlése** parancs törli a régi hálózati adaptert.
   
        azure network nic delete -g TestRG -n TestNIC --quiet
   
    Várt kimenet:
   
        info:    Executing command network nic delete
        + Looking up the network interface "TestNIC"
        + Deleting network interface "TestNIC"
        info:    network nic delete command OK

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>A statikus magánhálózati IP-cím hozzáadása egy meglévő virtuális Gépen
A fenti parancsfájl használatával létrehozott virtuális gép által használt hálózati adapterre hozzá statikus magánhálózati IP-cím hozzáadásához futtassa a következő parancsot:

    azure network nic set -g TestRG -n TestNIC2 -a 192.168.1.101

Várt kimenet:

    info:    Executing command network nic set
    + Looking up the network interface "TestNIC2"
    + Updating network interface "TestNIC2"
    + Looking up the network interface "TestNIC2"
    data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2
    data:    Name                            : TestNIC2
    data:    Type                            : Microsoft.Network/networkInterfaces
    data:    Location                        : centralus
    data:    Provisioning state              : Succeeded
    data:    MAC address                     : 00-0D-3A-90-29-25
    data:    Enable IP forwarding            : false
    data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01
    data:    IP configurations:
    data:      Name                          : NIC-config
    data:      Provisioning state            : Succeeded
    data:      Private IP address            : 192.168.1.101
    data:      Private IP Allocation Method  : Static
    data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
    data:
    info:    network nic set command OK

## <a name="next-steps"></a>Következő lépések
* További tudnivalók [foglalt nyilvános IP-cím](virtual-networks-reserved-public-ip.md) címek.
* További tudnivalók [példányszintű nyilvános IP (ILPIP)](virtual-networks-instance-level-public-ip.md) címek.
* Tekintse át a [fenntartott IP-REST API-k](https://msdn.microsoft.com/library/azure/dn722420.aspx).

