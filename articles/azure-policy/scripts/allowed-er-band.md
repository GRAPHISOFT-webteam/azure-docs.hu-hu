---
title: "Az Azure házirend json-mintát - Express Route sávszélesség engedélyezett |} Microsoft Docs"
description: "A json minta házirend feltétele, hogy kifejezett útvonalakat megadott sávszélesség használata."
services: azure-policy
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 
ms.service: azure-policy
ms.devlang: 
ms.topic: sample
ms.tgt_pltfrm: 
ms.workload: 
ms.date: 10/30/2017
ms.author: banders
ms.custom: mvc
ms.openlocfilehash: 985c7ae3a79a59e6fe20720fd8a2fe86a4f36905
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2017
---
# <a name="allowed-express-route-bandwidth"></a>Engedélyezett Express Route sávszélesség

A házirend feltétele, hogy kifejezett útvonalakat megadott sávszélesség használata. Megadja az Express Route megadható termékváltozatok tömbjét.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Minta sablon

[!code-json[main](../../../policy-templates/samples/Network/express-route-bandwidthInMbps/azurepolicy.json "Allowed Express Route bandwidth")]

A sablon használatával telepíthető a [Azure-portálon](#deploy-with-the-portal), a [PowerShell](#deploy-with-powershell) vagy a [Azure CLI](#deploy-with-azure-cli).

## <a name="deploy-with-the-portal"></a>Üzembe helyezés a portállal

[![Üzembe helyezés az Azure-ban](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FNetwork%2Fexpress-route-bandwidthInMbps%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>Üzembe helyezés a PowerShell-lel

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

```powershell
$definition = New-AzureRmPolicyDefinition -Name "express-route-bandwidthInMbps" -DisplayName "Allowed Express Route bandwidth" -description "This policy enables you to specify a set of express route bandwidths that your organization can deploy." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/express-route-bandwidthInMbps/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/express-route-bandwidthInMbps/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzureRMPolicyAssignment -Name <assignmentname> -Scope <scope>  -listOfBandwidthinMbps <Allowed Bandwidth> -PolicyDefinition $definition
$assignment
```

### <a name="clean-up-powershell-deployment"></a>PowerShell központi telepítés tisztítása

A következő parancsot az erőforráscsoport, virtuális gép és az összes kapcsolódó erőforrások eltávolítása.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="deploy-with-azure-cli"></a>Üzembe helyezés az Azure parancssori felületén keresztül

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy definition create --name 'express-route-bandwidthInMbps' --display-name 'Allowed Express Route bandwidth' --description 'This policy enables you to specify a set of express route bandwidths that your organization can deploy.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/express-route-bandwidthInMbps/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/express-route-bandwidthInMbps/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "express-route-bandwidthInMbps"
```

### <a name="clean-up-azure-cli-deployment"></a>Az Azure parancssori felület központi telepítés tisztítása

A következő parancsot az erőforráscsoport, virtuális gép és az összes kapcsolódó erőforrások eltávolítása.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Következő lépések

- További Azure csoportházirend-sablon minták erővel [sablonok Azure házirend](../json-samples.md).
