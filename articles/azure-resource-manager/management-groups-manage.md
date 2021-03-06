---
title: "Hogyan kell módosítani, törléséhez vagy kezeléséhez a felügyeleti csoport – Azure |} Microsoft Docs"
description: "Megtudhatja, hogyan karbantartása, és frissítse a felügyeleti csoport hierarchiában."
author: rthorn17
manager: rithorn
editor: 
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/1/2018
ms.author: rithorn
ms.openlocfilehash: 33797ddcd2a6ff083c5fb4b2fa7ddb8f9d6bd76c
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2018
---
# <a name="manage-your-resources-with-management-groups"></a>Kezelheti az erőforrásokat a felügyeleti csoportok 
Felügyeleti csoportok elősegítő tárolók hozzáférési házirend és megfelelőség kezeléséhez több előfizetéssel. Módosítsa, törölje, és kezelje az ezekben a tárolókban, amelyek együtt hierarchiákkal rendelkeznek a következő [Azure házirend](../azure-policy/azure-policy-introduction.md) és [Azure szerepköralapú hozzáférés vezérlők (RBAC)](../active-directory/role-based-access-control-what-is.md). Felügyeleti csoportok kapcsolatos további információkért lásd: [rendezheti az erőforrásokat az Azure felügyeleti csoportok ](management-groups-overview.md).

A felügyeleti csoport funkciót egy nyilvános előzetes verziójában érhető el. Indíthatja a felügyeleti csoportok, jelentkezzen be a [Azure-portálon](https://portal.azure.com) vagy használhat [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview), [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/extension?view=azure-cli-latest#az_extension_list_available), vagy a [REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview) számára a felügyeleti csoportok kezelése.

Változtatásokat egy felügyeleti csoporthoz, a felügyeleti csoport egy tulajdonos vagy közreműködő szerepkörrel kell rendelkeznie. Milyen engedélyekkel Ön rendelkezik, válassza ki a felügyeleti csoportot, és válassza ki **IAM**. Az RBAC-szerepkörök kapcsolatos további információkért lásd: [kezelheti a hozzáférést és engedélyeket az RBAC](../active-directory/role-based-access-control-what-is.md).

## <a name="change-the-name-of-a-management-group"></a>A felügyeleti csoport nevének módosítása 
A portál, a PowerShell vagy az Azure CLI segítségével módosíthatja a felügyeleti csoport neve.

### <a name="change-the-name-in-the-portal"></a>Változtassa meg a nevét, a portálon

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com)
2. Válassza ki **minden szolgáltatás** > **felügyeleti csoportok**  
3. Válassza ki az átnevezni kívánt felügyeleti csoport. 
4. Válassza ki a **csoport átnevezése** az oldal tetején. 

    ![Csoport átnevezése](media/management-groups/detail_action_small.png)
5. A menü megnyitása után adja meg az új nevet szeretne megjeleníteni.

    ![Csoport átnevezése](media/management-groups/rename_context.png) 
4. Kattintson a **Mentés** gombra. 

### <a name="change-the-name-in-powershell"></a>A PowerShell nevének módosítása

Frissíteni a megjelenítési név használata **frissítés-AzureRmManagementGroup**. Például ha módosítani szeretné egy felügyeleti csoport neve "Contoso informatikai" a "Contoso csoport", akkor futtassa a következő parancsot: 

```azurepowershell-inetractive
C:\> Update-AzureRmManagementGroup -GroupName ContosoIt -DisplayName "Contoso Group"  
``` 

### <a name="change-the-name-in-azure-cli"></a>Módosíthatja a nevet az Azure parancssori felület

Azure CLI használata esetén használja a frissítés parancsot. 

```azure-cli
C:\> az account management-group update --group-name Contoso --display-name "Contoso Group" 
```

---
 
## <a name="delete-a-management-group"></a>Felügyeleti csoport törlése
A felügyeleti csoport törléséhez a következő követelményeinek teljesülnie kell:
1. Nincsenek olyan felügyeleti csoportokat vagy a felügyeleti csoportba tartozó előfizetések. 
    - A felügyeleti csoport kívül előfizetés áthelyezésével kapcsolatban lásd: [előfizetés áthelyezése egy másik managemnt csoport](#Move-subscriptions-in-the-hierarchy). 
    - Felügyeleti csoport áthelyezése egy másik felügyeleti csoportba, lásd: [helyezze át a felügyeleti csoportok a hierarchia](#Move-management-groups-in-the-hierarchy). 
2. Írási engedélyekkel rendelkezzen a felügyeleti csoport tulajdonosi vagy közreműködői szerepkört a felügyeleti csoportban. Milyen engedélyekkel Ön rendelkezik, válassza ki a felügyeleti csoportot, és válassza ki **IAM**. Az RBAC-szerepkörök további tudnivalókért lásd: [kezelheti a hozzáférést és engedélyeket az RBAC](../active-directory/role-based-access-control-what-is.md).  

### <a name="delete-in-the-portal"></a>A portál törlése

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com)
2. Válassza ki **minden szolgáltatás** > **felügyeleti csoportok**  
3. Válassza ki a törölni kívánt felügyeleti csoport. 
    
    ![Csoport törlése](media/management-groups/delete.png)
4. Válassza a **Törlés** elemet. 
    - Az ikon le van tiltva, ha az egér választó ikon fölé elsajátíthatja, hogy okát. 
5. Erősítse meg a felügyeleti csoport törölni kívánt megnyíló ablak van. 

    ![Csoport törlése](media/management-groups/delete_confirm.png) 
6. Válassza ki **Igen** 


### <a name="delete-in-powershell"></a>A PowerShell törlése

Használja a **Remove-AzureRmManagementGroup** belül PowerShell felügyeleti csoportok törlése paranccsal. 

```azurepowershell-interactive
Remove-AzureRmManagementGroup -GroupName Contoso
```

### <a name="delete-in-azure-cli"></a>Törlés az Azure CLI felületen
Azure parancssori felülettel a parancs az fiók felügyeleticsoport törlésének használja. 

```azure-cli
C:\> az account management-group delete --group-name Contoso
```
---

## <a name="view-management-groups"></a>Felügyeleti csoportok megtekintése
A felügyeleti csoport rendelkezik közvetlen vagy az örökölt RBAC szerepkör tekintheti meg.  

### <a name="view-in-the-portal"></a>Tekintse meg a portálon
1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com)
2. Válassza ki **minden szolgáltatás** > **felügyeleti csoportok** 
3. A felügyeleti csoport hierarchia lapon terhelések, amelyekben az összes csoport látható, hogy rendelkezik-e a hozzáférést. 
    ![Main](media/management-groups/main.png)
4. A részletekért önálló felügyeleti csoport kiválasztása  

### <a name="view-in-powershell"></a>Megtekintés a PowerShell
A Get-AzureRmManagementGroup paranccsal minden csoportjainak lekérdezésére.  

```azurepowershell-interactive
Get-AzureRmManagementGroup
```
Ha egyetlen felügyeleti csoport, kattintson a - GroupName paraméter

```azurepowershell-interactive
Get-AzureRmManagementGroup -GroupName Contoso
```

### <a name="view-in-azure-cli"></a>Az Azure CLI megtekintése
A lista paranccsal minden csoportjainak lekérdezésére.  

```azure-cli
az account management-group list
```
Egyetlen felügyeleti csoport információ megjelenítése paranccsal

```azurepowershell-interactive
az account management-group show --group-name Contoso
```
---

## <a name="move-subscriptions-in-the-hierarchy"></a>Helyezze át az előfizetések a hierarchiában
Hozzon létre egy felügyeleti csoportot akkor szeretné a előfizetések együtt. Csak a felügyeleti csoportok és előfizetések tehető a gyermekek másik felügyeleti csoportban. Olyan előfizetést, amelyet a felügyeleti csoport áthelyezése a felhasználói hozzáférés és a házirendek örökli a szülő felügyeleti csoport. 

Helyezze át az előfizetést, van néhány engedélyekkel kell rendelkeznie: 
- A gyermek előfizetés "Owner" szerepkört.
- Az új fölérendelt felügyeleti csoport "Owner" vagy "Közreműködői" szerepkör. 
- "Tulajdonos" vagy "Közreműködői" szerepkört a régi szülő felügyeleti csoportra vonatkozóan.
Milyen engedélyekkel Ön rendelkezik, válassza ki a felügyeleti csoportot, és válassza ki **IAM**. Az RBAC-szerepkörök további tudnivalókért lásd: [kezelheti a hozzáférést és engedélyeket az RBAC](../active-directory/role-based-access-control-what-is.md). 

### <a name="move-subscriptions-in-the-portal"></a>Helyezze át az előfizetések a portálon

**Egy meglévő előfizetés hozzáadása a felügyeleti csoport**
1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com)
2. Válassza ki **minden szolgáltatás** > **felügyeleti csoportok** 
3. Válassza ki a felügyeleti csoport szülőjeként tervezi.      
5. A lap tetején jelölje ki a **hozzáadása a meglévő**. 
6. Megnyitott menüben válasszon ki a **erőforrástípus** az elem, amely áthelyezni kívánt **előfizetés**.
7. A megfelelő azonosítójú listájában válassza ki az előfizetést 

    ![Children](media/management-groups/add_context_2.png)
8. Válassza ki a "Mentés"

**Előfizetés eltávolítása a felügyeleti csoport**
1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com)
2. Válassza ki **minden szolgáltatás** > **felügyeleti csoportok** 
3. Válassza ki a felügyeleti csoport, amely az aktuális szülő tervezi.  
4. Jelölje ki az előfizetés sor végén a három pont áthelyezni kívánt listájában.

    ![Áthelyezés](media/management-groups/move_small.png)
5. Válassza ki **áthelyezése**
6. A megnyíló menüben válassza a **szülő felügyeleti csoport**.

    ![Áthelyezés](media/management-groups/move_small_context.png)
7. Válassza ki **mentése**

### <a name="move-subscriptions-in-powershell"></a>Helyezze át az előfizetések a PowerShellben
PowerShell előfizetés áthelyezéséhez használja az Add-AzureRmManagementGroupSubscription parancsot.  

```azurepowershell-interactive
New-AzureRmManagementGroupSubscription -GroupName Contoso -SubscriptionId 12345678-1234-1234-1234-123456789012
```

A közötti kapcsolat eltávolítása és az előfizetés és a felügyeleti csoport használja a Remove-AzureRmManagementGroupSubscription parancsot.

```azurepowershell-interactive
Remove-AzureRmManagementGroupSubscription -GroupName Contoso -SubscriptionId 12345678-1234-1234-1234-123456789012
```

### <a name="move-subscriptions-in-azure-cli"></a>Helyezze át az előfizetések az Azure parancssori felület
Parancssori felület előfizetés áthelyezéséhez használja az add parancs. 

```azure-cli
C:\> az account management-group add --group-name Contoso --subscription 12345678-1234-1234-1234-123456789012
```

A felügyeleti csoportból az előfizetés eltávolításához használja az előfizetés remove parancsot.  

```azure-cli
C:\> az account management-group remove --group-name Contoso --subscription 12345678-1234-1234-1234-123456789012
```

---

## <a name="move-management-groups-in-the-hierarchy"></a>Helyezze át a felügyeleti csoportok a hierarchiában  
Ha a fölérendelt felügyeleti csoport, a gyermek erőforrások közé tartoznak a felügyeleti csoportok, előfizetések, erőforráscsoport-sablonok és erőforrások áthelyezése a szülő helyezi át.   

### <a name="move-management-groups-in-the-portal"></a>Helyezze át a felügyeleti csoportok a portálon

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com)
2. Válassza ki **minden szolgáltatás** > **felügyeleti csoportok** 
3. Válassza ki a felügyeleti csoport szülőjeként tervezi.      
5. A lap tetején jelölje ki a **hozzáadása a meglévő**.
6. Megnyitott menüben válasszon ki a **erőforrástípus** az elem, amely áthelyezni kívánt **felügyeleti csoport**.
7. Válassza ki a felügyeleti csoport helyes Azonosítóját és nevét.

    ![move](media/management-groups/add_context.png)
8. Válassza ki **mentése**

### <a name="move-management-groups-in-powershell"></a>Helyezze át a felügyeleti csoportok a PowerShellben
PowerShell frissítés-AzureRmManagementGroup paranccsal helyezze át a felügyeleti csoportot egy másik csoporthoz.  

```powershell
C:\> Update-AzureRmManagementGroup -GroupName Contoso  -ParentName ContosoIT
```  
### <a name="move-management-groups-in-azure-cli"></a>Helyezze át a felügyeleti csoportok az Azure parancssori felület
A frissítési paranccsal helyezze át a felügyeleti csoport az Azure parancssori felület. 

```azure-cli
C:/> az account management-group udpate --group-name Contoso --parent-id "Contoso Tenant" 
``` 

---

## <a name="next-steps"></a>További lépések 
Felügyeleti csoportok kapcsolatos további információkért lásd: 
- [Az erőforrások rendszerezéséhez az Azure felügyeleti csoportok ](management-groups-overview.md)
- [Azure-erőforrások rendszerezéséhez felügyeleti csoportok létrehozása](management-groups-create.md)
- [Az Azure Powershell modul telepítése](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [Tekintse át a REST API-specifikáció](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview)
- [Az Azure CLI-bővítményének telepítése](https://docs.microsoft.com/en-us/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)