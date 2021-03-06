---
title: "Azure Container Service-oktatóanyag – Fürt üzembe helyezése"
description: "Azure Container Service-oktatóanyag – Fürt üzembe helyezése"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 09/14/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 6ef789bc017e670566d25dd9d167698515e88349
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="deploy-a-kubernetes-cluster-in-azure-container-service"></a>Kubernetes-fürt üzembe helyezése az Azure Container Service-ben

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

A Kubernetes tárolóalapú alkalmazásokhoz kínál elosztott platformot. Az Azure Container Service használatával egyszerűen és gyorsan építhető ki egy üzemkész Kubernetes-fürt. Ebben az oktatóanyagban, amely egy hétrészes sorozat harmadik része, egy Azure Container Service-beli Kubernetes-fürtöt helyezünk üzembe. Ennek lépései az alábbiak:

> [!div class="checklist"]
> * Kubernetes ACS-fürt üzembe helyezése
> * A Kubernetes parancssori felület (kubectl) telepítése
> * A kubectl konfigurálása

Az ezt követő oktatóanyagokban üzembe helyezzük majd az Azure Vote alkalmazást a fürtön, méretezzük, frissítjük, majd konfiguráljuk az Operations Management Suite szolgáltatást a Kubernetes-fürt monitorozására.

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokban létrehoztunk egy tárolórendszerképet, és feltöltöttük egy Azure Container Registry-példányra. Ha ezeket a lépéseket még nem hajtotta végre, és szeretné követni az oktatóanyagot, lépjen vissza az [1. oktatóanyag – Tárolórendszerképek létrehozása](./container-service-tutorial-kubernetes-prepare-app.md) részhez.

## <a name="create-kubernetes-cluster"></a>Kubernetes-fürt létrehozása

Hozzon létre egy Kubernetes-fürtöt az Azure Container Service-ben az [az acs create](/cli/azure/acs#az_acs_create) paranccsal. 

A következő példában létrehozunk egy `myK8sCluster` nevű fürtöt egy `myResourceGroup` nevű erőforráscsoportban. Az erőforráscsoportot [az előző oktatóanyagban](./container-service-tutorial-kubernetes-prepare-acr.md) hoztuk létre.

```azurecli-interactive 
az acs create --orchestrator-type kubernetes --resource-group myResourceGroup --name myK8SCluster --generate-ssh-keys 
```

Egyes esetekben – például korlátozott próbaverziónál – az Azure-előfizetés korlátozott hozzáféréssel rendelkezik az Azure-erőforrásokhoz. Ha az üzembe helyezés az elérhető magok korlátozott száma miatt hiúsul meg, csökkentse az alapértelmezett ügynökök számát az `--agent-count 1` az [az acs create](/cli/azure/acs#az_acs_create) parancshoz történő hozzáadásával. 

Néhány perc múlva befejeződik az üzembe helyezés, és a rendszer visszaadja az ACS-beli üzembe helyezéssel kapcsolatos adatokat JSON formátumban.

## <a name="install-the-kubectl-cli"></a>A kubectl parancssori felület telepítése

Ahhoz, hogy csatlakozni tudjon a Kubernetes-fürthöz az ügyfélszámítógépről, használja a Kubernetes [kubectl](https://kubernetes.io/docs/user-guide/kubectl/) nevű parancssori ügyfelét. 

Ha az Azure CloudShellt használja, a kubectl már telepítve van. Ha helyileg szeretné telepíteni, használja az [az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli) parancsot.

Linux vagy macOS használata esetén előfordulhat, hogy sudo használatával kell futtatnia. Windows használata esetén győződjön meg róla, hogy a felület rendszergazdaként lett futtatva.

```azurecli-interactive 
az acs kubernetes install-cli 
```

Windows rendszeren az alapértelmezett telepítés telepítési hely a *c:\program files (x86)\kubectl.exe*. Előfordulhat, hogy ezt a fájlt hozzá kell adnia a Windows-útvonalhoz. 

## <a name="connect-with-kubectl"></a>Kapcsolódás a kubectl parancssori ügyfélhez

A kubectl a Kubernetes-fürthöz való csatlakozásra konfigurálásához futtassa az [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials) parancsot.

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group myResourceGroup --name myK8SCluster
```

A fürthöz való csatlakozás ellenőrzéséhez futtassa a [kubectl get nodes](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) parancsot.

```azurecli-interactive
kubectl get nodes
```

Kimenet:

```bash
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.6.2
k8s-agent-98dc3136-1    Ready                      5m        v1.6.2
k8s-agent-98dc3136-2    Ready                      5m        v1.6.2
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.6.2
```

Az oktatóanyag befejezésével rendelkezésére áll majd egy számítási feladatok végrehajtására kész ACS Kubernetes-fürt. Az ezt követő oktatóanyagokban egy többtárolós alkalmazást helyezünk üzembe a fürtön, majd elvégezzük annak horizontális skálázását, frissítését és monitorozását.

## <a name="next-steps"></a>További lépések

Az oktatóanyagban egy Azure Container Service-beli Kubernetes fürtöt helyezett üzembe. A következő lépéseket hajtotta végre:

> [!div class="checklist"]
> * Üzembe helyezett egy Kubernetes ACS-fürtöt
> * Telepítette a Kubernetes parancssori felületet (kubectl)
> * Konfigurálta a kubectl parancssori felületet

Folytassa a következő oktatóanyaggal, amely azt ismerteti, hogyan futtathatók alkalmazások a fürtön.

> [!div class="nextstepaction"]
> [Alkalmazások üzembe helyezése a Kubernetesben](./container-service-tutorial-kubernetes-deploy-application.md)
