---
title: "Azure CLI-szkript – Fiókkulcsok lekérése az Azure Cosmos DB-hez | Microsoft Docs"
description: "Azure CLI-példaszkript – Fiókkulcsok lekérése az Azure Cosmos DB-hez"
services: cosmos-db
documentationcenter: cosmosdb
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: cosmosdb
ms.workload: database
ms.date: 06/02/2017
ms.author: mimig
ms.openlocfilehash: 4c9e0e157e0c9040f6d9aa73c7d708276d8570d3
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="get-account-keys-for-azure-cosmos-db-using-the-azure-cli"></a>Fiókkulcsok lekérése az Azure Cosmos DB-hez az Azure CLI használatával

Ez a példa lekéri bármilyen Azure Cosmos DB-fiók fiókkulcsait.  

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure parancssori felületének 2.0-s vagy annál újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/scale-cosmosdb-get-account-key/secure-cosmosdb-get-account-key.sh?highlight=22-25 "Get Azure Cosmos DB account keys")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az cosmosdb update](https://docs.microsoft.com/cli/azure/cosmosdb#az_cosmosdb_update) | Frissít egy Azure Cosmos DB-fiókot. |
| [az cosmosdb list-keys](https://docs.microsoft.com/cli/azure/cosmosdb#az_cosmosdb_list_keys) | Létrehoz egy logikai kiszolgálót, amelyen az SQL Database szolgáltatás fut. |
| [az group delete](https://docs.microsoft.com/cli/azure/group#az_group_delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure/overview).

További Azure Cosmos DB CLI-példaszkripteket az [Azure Cosmos DB CLI dokumentációjában](../cli-samples.md) találhat.
