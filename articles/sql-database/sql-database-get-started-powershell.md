---
title: "Azure PowerShell: Önálló SQL-adatbázis létrehozása | Microsoft Docs"
description: "Ismerje meg, hogyan hozhat létre SQL Database logikai kiszolgálót, kiszolgálószintű tűzfalszabályokat és adatbázisokat az Azure Portal használatával."
keywords: "oktatóanyag az SQL Database használatához, SQL-adatbázis létrehozása"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: hero-article
ms.date: 03/13/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: fe527f7de573b87fbc644cb6d71ae13816bc284b
ms.lasthandoff: 03/15/2017

---

# <a name="create-a-single-azure-sql-database-using-powershell"></a>Önálló Azure SQL-adatbázis létrehozása a PowerShell használatával

A PowerShell használatával Azure-erőforrások hozhatók létre és kezelhetők a parancssorból vagy szkriptekben. Ez az útmutató azt ismerteti, hogyan helyezhet üzembe Azure SQL-adatbázist a PowerShell használatával egy SQL Database logikai kiszolgálón található Azure-erőforráscsoportban.

A kezdés előtt ellenőrizze, hogy a PowerShell legújabb verziója van-e telepítve, és az Azure CLI telepítve van-e. Részletes információk: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azureps-cmdlets-docs). 

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésbe az [Add-AzureRmAccount](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.profile/v2.5.0/add-azurermaccount) paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

```powershell
Add-AzureRmAccount
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot a [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy `westeurope` nevű erőforráscsoportot a `myResourceGroup` helyen.

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "westeurope"
```
## <a name="create-a-logical-server"></a>Hozzon létre egy logikai kiszolgálót

Hozzon létre egy SQL Database logikai kiszolgálót a [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserver) paranccsal. A következő példában létrehozunk egy véletlenszerűen elnevezett kiszolgálót az erőforráscsoportban egy `ServerAdmin` nevű és `ChangeYourAdminPassword1` jelszavú rendszergazdai bejelentkezéssel. Igény szerint cserélje le ezeket az előre meghatározott értékeket.

```powershell
$servername = "server-$(Get-Random)"
New-AzureRmSqlServer -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -Location "westeurope" `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList "ServerAdmin", $(ConvertTo-SecureString -String "ChangeYourAdminPassword1" -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Konfiguráljon egy kiszolgálói tűzfalszabályt

Hozzon létre egy kiszolgálószintű SQL Database-tűzfalszabályt a [New-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserverfirewallrule) paranccsal. A kiszolgálószintű tűzfalszabályok lehetővé teszik, hogy külső alkalmazások, például az SQL Server Management Studio vagy az SQLCMD segédprogram az SQL Database szolgáltatás tűzfalán keresztül csatlakozzon egy SQL-adatbázishoz. A következő példában létrehozunk egy tűzfalszabályt egy előre meghatározott címtartományhoz, amely ebben a példában az IP-címek teljes lehetséges tartományát lefedi. Cserélje le ezeket az előre meghatározott értékeket a külső IP-címére vagy IP-címtartományára. 

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "255.255.255.255"
```

## <a name="create-a-blank-database"></a>Hozzon létre egy üres adatbázist

Hozzon létre egy üres, S0 teljesítményszintű SQL-adatbázist a kiszolgálón a [New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqldatabase) paranccsal. Az alábbi példa egy `mySampleDatabase` nevű adatbázist hoz létre. Igény szerint cserélje le ezt az előre meghatározott értéket.

```powershell
New-AzureRmSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName "MySampleDatabase" `
    -RequestedServiceObjectiveName "S0"
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az összes, a rövid útmutató által létrehozott erőforrás eltávolításához futtassa a következő parancsot:

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>Következő lépések

- Az SQL Server Management Studióval történő csatlakozáshoz és lekérdezéshez lásd [az SSMS segítségével történő csatlakozással és lekérdezéssel](sql-database-connect-query-ssms.md) foglalkozó témakört.
- Ha a Visual Studióval szeretne kapcsolódni, lásd a [Visual Studióval végzett csatlakozásról és lekérdezésről](sql-database-connect-query.md) szóló témakört.
* Az SQL Database műszaki áttekintését az [SQL Database szolgáltatást ismertető](sql-database-technical-overview.md) témakörben találja.

