---
title: "Virtuális hálózatok csatlakoztatása a virtuális hálózati társviszony - Azure parancssori Felülettel |} Microsoft Docs"
description: "Útmutató: virtuális hálózatok csatlakoztatása a virtuális hálózati társviszony-létesítés."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/06/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: df56f2e3e13f80e7ce2c2b6c9cffeac3d03776e5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-cli"></a>Virtuális hálózatok csatlakoztatása a virtuális hálózati társviszony-létesítést az Azure parancssori felület használatával

Kapcsolódás virtuális hálózatok egymástól a virtuális hálózati társviszony-létesítés. Virtuális hálózatok vannak társviszonyban, ha mindkét virtuális hálózat erőforrásainak képesek kommunikálnak egymással, ugyanahhoz késés és a sávszélesség, mintha az erőforrásokat ugyanabban a virtuális hálózatban. Ez a cikk ismerteti, létrehozása és a társviszony-létesítés két virtuális hálózatok. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Két virtuális hálózatok létrehozása
> * Virtuális hálózatok közötti társviszony létrehozása
> * Tesztelje a társviszony-létesítés

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a gyorsútmutatóhoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). 

## <a name="create-virtual-networks"></a>Virtuális hálózatok létrehozása

Virtuális hálózat létrehozása előtt hozzon létre egy erőforráscsoportot a virtuális hálózat, és ez a cikk létrehozott összes többi erőforrása van. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Hozzon létre egy virtuális hálózatot az [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) paranccsal. Az alábbi példa létrehoz egy virtuális hálózatot nevű *myVirtualNetwork1* a címelőtaggal rendelkező *10.0.0.0/16*.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.0.0.0/24
```

Hozzon létre egy virtuális hálózatot nevű *myVirtualNetwork2* a címelőtaggal rendelkező *10.1.0.0/16*. A címelőtag nem fedi át a címelőtagot a *myVirtualNetwork1* virtuális hálózat. Virtuális hálózatok átfedő címelőtagok nem partnert.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --address-prefixes 10.1.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.1.0.0/24
```

## <a name="peer-virtual-networks"></a>Társ virtuális hálózatok

Társviszony létrehozása történik, virtuális hálózat azonosítója, ezért be kell szereznie az egyes virtuális hálózati Azonosítóját közötti [az hálózati vnet show](/cli/azure/network/vnet#az_network_vnet_show) és az azonosító tárolható egy változóban.

```azurecli-interactive
# Get the id for myVirtualNetwork1.
vNet1Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork1 \
  --query id --out tsv)

# Get the id for myVirtualNetwork2.
vNet2Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork2 \
  --query id \
  --out tsv)
```

Hozzon létre a társviszony *myVirtualNetwork1* való *myVirtualNetwork2* rendelkező [létrehozása az hálózati vnetben társviszony-létesítés](/cli/azure/network/vnet/peering#az_network_vnet_peering_create). Ha a `--allow-vnet-access` paraméter nincs megadva, a társviszony-létesítés létrejött, de nem érkeztek áramolhasson rajta.

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --remote-vnet-id $vNet2Id \
  --allow-vnet-access
```

Adott vissza a korábbi parancs futtatása után a kimenetben megjelenik az **peeringState** van *kezdeményezett*. A társviszony-létesítési marad a *kezdeményezett* állapot, amíg nem hoz létre a társviszony *myVirtualNetwork2* való *myVirtualNetwork1*. Hozzon létre a társviszony *myVirtualNetwork2* való *myVirtualNetwork1*. 

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork2-myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork2 \
  --remote-vnet-id $vNet1Id \
  --allow-vnet-access
```

Adott vissza a korábbi parancs futtatása után a kimenetben megjelenik az **peeringState** van *csatlakoztatva*. Azure társviszony-létesítési állapota megváltozik a *myVirtualNetwork1-myVirtualNetwork2* társviszony-létesítési való *csatlakoztatva*. Ellenőrizze, hogy a társviszony-létesítési állapota a *myVirtualNetwork1-myVirtualNetwork2* társviszony-létesítés változott *csatlakoztatva* rendelkező [az hálózati vnetben társviszony-létesítési megjelenítése](/cli/azure/network/vnet/peering#az_network_vnet_peering_show).

```azurecli-interactive
az network vnet peering show \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --query peeringState
```

Amíg a többi virtuális hálózatán lévő erőforrásokat az egyik nem tud kommunikálni a virtuális hálózati erőforrásokhoz a **peeringState** mindkét virtuális hálózatok esetében: *csatlakoztatva*. 

Társviszony virtuális hálózatok között, de nem tranzitív. Igen, például ha is egyenrangú *myVirtualNetwork2* való *myVirtualNetwork3*, kell létrehoznia a virtuális hálózatok közötti társviszony további *myVirtualNetwork2* és *myVirtualNetwork3*. Annak ellenére, hogy *myVirtualNetwork1* nincsenek társviszonyban, a *myVirtualNetwork2*, erőforrások *myVirtualNetwork1* csak hozzáférjen az erőforrásokhoz  *myVirtualNetwork3* Ha *myVirtualNetwork1* lett is társítottak, a *myVirtualNetwork3*. 

Társviszony-létesítés előtt éles virtuális hálózatok, javasoljuk, hogy alaposan feltérképezése a [társviszony-létesítési áttekintése](virtual-network-peering-overview.md), [kezelése a társviszony-létesítés](virtual-network-manage-peering.md), és [virtuális hálózati korlátok ](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Bár ez a cikk bemutatja, hogy társviszony-létesítés között két virtuális hálózat ugyanabban az előfizetésben és helyen, a virtuális hálózatok is partnert is [különböző régiókban](#register) és [különböző Azure-előfizetések](create-peering-different-subscriptions.md#cli). Is létrehozhat [küllős hálózati kialakításokat](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) a társviszony-létesítés.

## <a name="test-peering"></a>Tesztelje a társviszony-létesítés

A társviszony-létesítés különböző virtuális hálózatokon lévő virtuális gépek közötti hálózati kommunikációhoz teszteléséhez virtuális gép telepítése minden egyes alhálózathoz, és majd a virtuális gépek közötti kommunikációra. 

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

A virtuális gép létrehozása [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create). Az alábbi példa létrehoz egy virtuális gépet, nevű *myVm1* a a *myVirtualNetwork1* virtuális hálózat. Ha SSH-kulcsok még nem léteznek a kulcs alapértelmezett helye, a parancs létrehozza azokat. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást. A `--no-wait` beállítás hoz létre a virtuális gép a háttérben, így továbbra is a következő lépéssel.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork1 \
  --subnet Subnet1 \
  --generate-ssh-keys \
  --no-wait
```

Azure automatikusan hozzárendeli az 10.0.0.4 privát IP-címként a virtuális gép, mert 10.0.0.4 az első elérhető IP-cím *Alhalozat_1* a *myVirtualNetwork1*. 

A virtuális gép létrehozása a *myVirtualNetwork2* virtuális hálózat.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork2 \
  --subnet Subnet1 \
  --generate-ssh-keys
```

A virtuális gép létrehozásához néhány percet vesz igénybe. A virtuális gép létrehozása után az Azure parancssori felület információkat jeleníti meg az alábbi példához hasonló: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.1.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

A példa kimenetben megjelenik az **privateipaddress tulajdonságot** van *10.1.0.4*. Azure DHCP automatikusan hozzárendelni 10.1.0.4 a virtuális géphez, mert az első elérhető címe *Alhalozat_1* a *myVirtualNetwork2*. Vegye figyelembe a **publicIpAddress**. Ez a cím a virtuális gép egy későbbi lépésben az internetről való eléréséhez használt.

### <a name="test-virtual-machine-communication"></a>Teszt virtuális gép kommunikáció

Az SSH-munkamenetet létrehozni, az alábbi parancs segítségével a *myVm2* virtuális gépet. Cserélje le `<publicIpAddress>` a virtuális gép a nyilvános IP-címmel. Az előző példában a nyilvános IP-cím van *13.90.242.231*.

```bash 
ssh <publicIpAddress>
```

Pingelje meg a virtuális gép *myVirtualNetwork1*.

```bash 
ping 10.0.0.4 -c 4
```

Négy választ kap. Ha a virtuális gép neve pingelése (*myVm1*), az IP-cím helyett pingelés sikertelen, mert *myVm1* egy ismeretlen állomás neve. Azure alapértelmezett névfeloldás működik, az azonos virtuális hálózatban lévő virtuális gépek között, de nem a különböző virtuális hálózatokon lévő virtuális gépek között. Virtuális hálózatok közötti névfeloldás, kell [a saját DNS-kiszolgáló telepítése](virtual-networks-name-resolution-for-vms-and-role-instances.md) , vagy használjon [titkos tartományok Azure DNS](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Zárja be az SSH-munkamenetet a *myVm2* virtuális gépet. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség, [az csoport törlése](/cli/azure/group#az_group_delete) használatával távolítsa el az erőforráscsoport és a benne található erőforrásokat.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

**<a name="register"></a>A globális virtuális hálózati társviszony-létesítési Preview regisztrálása**

Az azonos régiókban lévő virtuális hálózatok közötti társviszony kialakítása általánosan elérhető. Virtuális hálózatok különböző régiókban jelenleg előzetes verzióban érhetők társviszony. Lásd: [virtuális hálózati frissítések](https://azure.microsoft.com/updates/?product=virtual-network) az elérhető régiók. Virtuális hálózatok egyenrangú régiók között, először regisztrálnia kell az előzetes (belül minden partnert kívánt virtuális hálózat szerepel az előfizetés) az alábbi lépések végrehajtásával:

1. Az előzetes rögzítése a következő parancsok beírásával:

  ```azurecli-interactive
  az feature register --name AllowGlobalVnetPeering --namespace Microsoft.Network
  az provider register --name Microsoft.Network
  ```

2. Győződjön meg arról, hogy be vannak jegyezve a az előzetes a következő parancs beírásával:

  ```azurecli-interactive
  az feature show --name AllowGlobalVnetPeering --namespace Microsoft.Network
  ```

  Virtuális hálózatok előtt különböző régiókban egyenrangú megkezdése a **RegistrationState** kimeneti kapja az előző parancs bevitele után **regisztrált** mindkét előfizetésekhez társviszony-létesítés sikertelen .

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, két hálózat kapcsolódás a virtuális hálózati társviszony-létesítés. Is [saját számítógép csatlakoztatása egy virtuális hálózati](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) egy VPN-en keresztül és a virtuális hálózatot, vagy nincsenek társviszonyban, virtuális hálózatok erőforrások.

Továbbra is a virtuális hálózati cikkekben ismertetett feladatok végrehajtásához újrafelhasználható parancsfájlok parancsfájl-példák.

> [!div class="nextstepaction"]
> [Virtuális hálózati parancsfájl minták](../networking/cli-samples.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
