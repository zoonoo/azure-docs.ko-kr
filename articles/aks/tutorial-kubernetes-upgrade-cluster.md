---
title: Azure의 Kubernetes 자습서 - 클러스터 업그레이드
description: 이 AKS(Azure Kubernetes Service) 자습서에서는 기존 AKS 클러스터를 최신 Kubernetes 버전으로 업그레이드하는 방법을 알아봅니다.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 11e082ae235706613b0a60b12bc2b27896953508
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2018
ms.locfileid: "41919576"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>자습서: AKS(Azure Kubernetes Service)에서 Kubernetes 업그레이드

응용 프로그램 및 클러스터 수명 주기의 일부로, Kubernetes 최신 버전으로 업그레이드하여 새 기능을 사용할 수 있습니다. Azure CLI를 사용하여 AKS(Azure Kubernetes Service) 클러스터를 업그레이드할 수 있습니다. 응용 프로그램을 실행하는 방해를 최소화하려면 업그레이드 프로세스 동안 Kubernetes 노드를 신중하게 [통제하고 드레이닝][kubernetes-drain]합니다.

총 7부 중 7부인 이 자습서에서는 Kubernetes 클러스터가 업그레이드됩니다. 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 현재 및 사용 가능한 Kubernetes 버전 확인
> * Kubernetes 노드 업그레이드
> * 성공적인 업그레이드의 유효성 검사

## <a name="before-you-begin"></a>시작하기 전에

이전 자습서에서는 응용 프로그램을 컨테이너 이미지에 패키지하고, 이 이미지를 Azure Container Registry에 업로드하고, Kubernetes 클러스터를 만들었습니다. 그런 다음 Kubernetes 클러스터에서 응용 프로그램을 실행했습니다. 이러한 단계를 아직 수행하지 않았으나 수행하려는 경우 [자습서 1 - 컨테이너 이미지 만들기][aks-tutorial-prepare-app]로 돌아갑니다.

이 자습서의 작업을 수행하려면 Azure CLI 버전 2.0.44 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

## <a name="get-available-cluster-versions"></a>사용 가능한 클러스터 버전 가져오기

클러스터를 업그레이드하려면 먼저 [az aks get-upgrades][] 명령을 사용하여 업그레이드할 수 있는 Kubernetes 릴리스를 확인합니다.

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

다음 예제에서는 현재 버전이 *1.9.6*이고 *업그레이드* 열 아래에 사용 가능한 업그레이드 버전이 표시됩니다.

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  ----------------------
default  myResourceGroup  1.9.9            1.9.9              1.10.3, 1.10.5, 1.10.6
```

## <a name="upgrade-a-cluster"></a>클러스터 업그레이드

[az aks upgrade][] 명령을 사용하여 AKS 클러스터를 업그레이드합니다. 다음 예제에서는 클러스터를 Kubernetes 버전 *1.10.6*으로 업데이트합니다.

> [!NOTE]
> 부 버전을 한 번에 하나씩 업그레이드할 수 있습니다. 예를 들어 *1.9.6*에서 *1.10.3*으로 업그레이드할 수 있지만, *1.9.6*에서 *1.11.x*로 바로 업그레이드할 수는 없습니다. *1.9.6*에서 *1.11.x*로 업그레이드하려면 먼저 *1.9.6*에서 *1.10.3*으로 업그레이드한 다음, *1.10.3*에서 *1.11.x*로 업그레이드합니다.

```azurecli
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.10.6
```

압축된 다음 예제 출력에서는 *kubernetesVersion* 현재 보고서 *1.10.6*을 표시합니다.

```json
{
  "agentPoolProfiles": [
    {
      "count": 3,
      "maxPods": 110,
      "name": "nodepool1",
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS1_v2",
    }
  ],
  "dnsPrefix": "myAKSClust-myResourceGroup-19da35",
  "enableRbac": false,
  "fqdn": "myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io",
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "kubernetesVersion": "1.10.6",
  "location": "eastus",
  "name": "myAKSCluster",
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-an-upgrade"></a>업그레이드 유효성 검사

다음과 같이 [az aks show][] 명령을 사용하여 업그레이드가 성공했는지 확인합니다.

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

다음 예제 출력은 AKS 클러스터가 *KubernetesVersion 1.10.6*을 실행하는 것을 보여줍니다.

```
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.10.6               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 AKS 클러스터에서 Kubernetes를 업그레이드했습니다. 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 현재 및 사용 가능한 Kubernetes 버전 확인
> * Kubernetes 노드 업그레이드
> * 성공적인 업그레이드의 유효성 검사

AKS에 대해 자세히 알아보려면 다음 링크를 참조하세요.

> [!div class="nextstepaction"]
> [AKS 개요][aks-intro]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-intro]: ./intro-kubernetes.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az aks show]: /cli/azure/aks#az-aks-show
[az aks get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az aks upgrade]: /cli/azure/aks#az-aks-upgrade
[azure-cli-install]: /cli/azure/install-azure-cli