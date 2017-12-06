---
title: "Az Azure-on Kubernetes Helm a tároló üzembe helyezése"
description: "A Helm csomagolás eszköz segítségével Kubernetes gazdagépfürtökön AKS a tároló üzembe helyezése"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 7065ceaf87f0cb5ebf46c53c71c6df4b069b2deb
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2017
---
# <a name="use-helm-with-azure-container-service-aks"></a>Helm használata az Azure Tárolószolgáltatás (AKS)

[Helm](https://github.com/kubernetes/helm/) nyílt forráskódú csomagolás eszköz, amely segít telepíteni, és Kubernetes alkalmazások életciklusának kezelését. Például a Linux-csomag kezelői hasonló *APT* és *Yum*, Helm Kubernetes diagramok, amelyek a csomagok előre konfigurált Kubernetes erőforrások kezelésére szolgál.

Ez a dokumentum lépéseit konfigurálásával és használatával Helm AKS Kubernetes fürtben.

## <a name="before-you-begin"></a>Előkészületek

A dokumentumban foglalt lépések feltételezik, hogy korábban már létrehozott egy AKS-fürtöt, és kiépített egy kubectl-kapcsolatot a fürttel. Ha ezeket még létre kell hoznia olvassa el az [AKS gyors útmutatóját](./kubernetes-walkthrough.md).

## <a name="install-helm-cli"></a>Helm parancssori felület telepítése

A Helm CLI ügyfél, amely a fejlesztői rendszeren fut, és lehetővé teszi indítása, leállítása és kezelheti az alkalmazásokat az Helm diagramok.

Ha Azure CloudShell használata esetén a Helm CLI már telepítve van. A Mac használja a Helm CLI telepítendő `brew`. A telepítési beállítások lásd [telepítése Helm](https://github.com/kubernetes/helm/blob/master/docs/install.md).

```console
brew install kubernetes-helm
```

Kimenet:

```
==> Downloading https://homebrew.bintray.com/bottles/kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
######################################################################## 100.0%
==> Pouring kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
==> Caveats
Bash completion has been installed to:
  /usr/local/etc/bash_completion.d
==> Summary
🍺  /usr/local/Cellar/kubernetes-helm/2.6.2: 50 files, 132.4MB
```

## <a name="configure-helm"></a>Helm konfigurálása

A [helm init](https://docs.helm.sh/helm/#helm-init) parancs segítségével Helm összetevőinek telepítése Kubernetes fürtben, és az ügyféloldali konfigurációkat. Helm előre telepítve van a AKS fürtök, így csak az ügyféloldali konfigurációra nincs szükség. A következő parancsot a Helm ügyfél konfigurálásához.

```azurecli-interactive
helm init --client-only
```

Kimenet:

```
$HELM_HOME has been configured at /Users/neilpeterson/.helm.
Not installing Tiller due to 'client-only' flag having been set
Happy Helming!
```

## <a name="find-helm-charts"></a>Helm diagramok keresése

Helm Kubernetes fürtbe alkalmazások központi telepítéséhez jól használható. Keresse meg a korábban létrehozott Helm diagramok, használja a [helm keresési](https://docs.helm.sh/helm/#helm-search) parancsot.

```azurecli-interactive
helm search
```

A kimeneti láthatóhoz hasonló, azonban a legtöbb több diagramokat.

```
NAME                            VERSION DESCRIPTION
stable/acs-engine-autoscaler    2.0.0   Scales worker nodes within agent pools
stable/artifactory              6.1.0   Universal Repository Manager supporting all maj...
stable/aws-cluster-autoscaler   0.3.1   Scales worker nodes within autoscaling groups.
stable/buildkite                0.2.0   Agent for Buildkite
stable/centrifugo               2.0.0   Centrifugo is a real-time messaging server.
stable/chaoskube                0.5.0   Chaoskube periodically kills random pods in you...
stable/chronograf               0.3.0   Open-source web application written in Go and R...
stable/cluster-autoscaler       0.2.0   Scales worker nodes within autoscaling groups.
stable/cockroachdb              0.5.0   CockroachDB is a scalable, survivable, strongly...
stable/concourse                0.7.0   Concourse is a simple and scalable CI system.
stable/consul                   0.4.1   Highly available and distributed service discov...
stable/coredns                  0.5.0   CoreDNS is a DNS server that chains middleware ...
stable/coscale                  0.2.0   CoScale Agent
stable/dask-distributed         2.0.0   Distributed computation in Python
stable/datadog                  0.8.0   DataDog Agent
...
```

Frissítse a diagramok listáját, használja a [helm tárház frissítési](https://docs.helm.sh/helm/#helm-repo-update) parancsot.

```azurecli-interactive
helm repo update
```

Kimenet:

```
Hang tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

## <a name="run-helm-charts"></a>Helm diagramok futtatása

Egy NGINX érkező tartományvezérlő telepítéséhez használja a [helm telepítés](https://docs.helm.sh/helm/#helm-install) parancsot.

```azurecli-interactive
helm install stable/nginx-ingress
```

A kimenet az alábbihoz hasonló, de tartoznak a további utasításokat a Kubernetes központi telepítés használatával.

```
NAME:   tufted-ocelot
LAST DEPLOYED: Thu Oct  5 00:48:04 2017
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                                    DATA  AGE
tufted-ocelot-nginx-ingress-controller  1     5s

==> v1/Service
NAME                                         CLUSTER-IP   EXTERNAL-IP  PORT(S)                     AGE
tufted-ocelot-nginx-ingress-controller       10.0.140.10  <pending>    80:30486/TCP,443:31358/TCP  5s
tufted-ocelot-nginx-ingress-default-backend  10.0.34.132  <none>       80/TCP                      5s

==> v1beta1/Deployment
NAME                                         DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
tufted-ocelot-nginx-ingress-controller       1        1        1           0          5s
tufted-ocelot-nginx-ingress-default-backend  1        1        1           1          5s
...
```

További tájékoztatást az NGINX érkező vezérlőhöz Kubernetes, lásd: [NGINX érkező vezérlő](https://github.com/kubernetes/ingress/tree/master/controllers/nginx).

## <a name="list-helm-charts"></a>Lista Helm diagramok

A fürtön telepítve diagramok listájának megtekintéséhez használja a [helm lista](https://docs.helm.sh/helm/#helm-list) parancsot.

```azurecli-interactive
helm list
```

Kimenet:

```
NAME            REVISION    UPDATED                     STATUS      CHART               NAMESPACE
bilging-ant     1           Thu Oct  5 00:11:11 2017    DEPLOYED    nginx-ingress-0.8.7 default
```

## <a name="next-steps"></a>Következő lépések

Kubernetes diagramok kezelésével kapcsolatos további információkért a Helm dokumentációjában talál.

> [!div class="nextstepaction"]
> [Helm dokumentáció](https://github.com/kubernetes/helm/blob/master/docs/index.md)