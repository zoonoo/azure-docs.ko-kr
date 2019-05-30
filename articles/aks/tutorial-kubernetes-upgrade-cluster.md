---
title: Azure의 Kubernetes 자습서 - 클러스터 업그레이드
description: 이 AKS(Azure Kubernetes Service) 자습서에서는 기존 AKS 클러스터를 최신 Kubernetes 버전으로 업그레이드하는 방법을 알아봅니다.
services: container-service
author: tylermsft
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: twhitney
ms.custom: mvc
ms.openlocfilehash: 09e3fab67486b4677c5848d81fb39a68e511b6ac
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66305221"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>자습서: AKS(Azure Kubernetes Service)에서 Kubernetes 업그레이드

애플리케이션 및 클러스터 수명 주기의 일부로, Kubernetes 최신 버전으로 업그레이드하여 새 기능을 사용할 수 있습니다. Azure CLI를 사용하여 AKS(Azure Kubernetes Service) 클러스터를 업그레이드할 수 있습니다.

총 7부 중 7부인 이 자습서에서는 Kubernetes 클러스터가 업그레이드됩니다. 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 현재 및 사용 가능한 Kubernetes 버전 확인
> * Kubernetes 노드 업그레이드
> * 성공적인 업그레이드의 유효성 검사

## <a name="before-you-begin"></a>시작하기 전에

이전 자습서에서 애플리케이션은 컨테이너 이미지로 패키징되었습니다. 이 이미지는 Azure Container Registry로 업로드되었고, AKS 클러스터를 만들었습니다. 그런 다음, 애플리케이션은 AKS 클러스터에 배포되었습니다. 이러한 단계를 아직 수행하지 않았으나 수행하려는 경우 [자습서 1 - 컨테이너 이미지 만들기][aks-tutorial-prepare-app]로 시작합니다.

이 자습서의 작업을 수행하려면 Azure CLI 버전 2.0.53 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

## <a name="get-available-cluster-versions"></a>사용 가능한 클러스터 버전 가져오기

클러스터를 업그레이드하려면 먼저 [az aks get-upgrades][] 명령을 사용하여 업그레이드할 수 있는 Kubernetes 릴리스를 확인합니다.

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

다음 예제에서는 현재 버전이 *1.9.11*이고 *업그레이드* 열 아래에 사용 가능한 버전이 표시됩니다.

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  --------------
default  myResourceGroup  1.9.11           1.9.11             1.10.8, 1.10.9
```

## <a name="upgrade-a-cluster"></a>클러스터 업그레이드

실행 중인 애플리케이션의 중단을 최소화하기 위해 AKS 노드는 신중하게 차단되고 드레이닝됩니다. 이 프로세스에서 다음 단계가 수행됩니다.

1. Kubernetes 스케줄러는 업그레이드될 노드에서 추가 Pod가 예약되지 않도록 합니다.
1. 노드에서 실행 중인 Pod가 클러스터의 다른 노드에서 예약됩니다.
1. 최신 Kubernetes 구성 요소를 실행하는 노드가 만들어집니다.
1. 새 노드가 준비되고 클러스터에 조인될 때 Kubernetes 스케줄러에서 Pod를 실행하기 시작합니다.
1. 이전 노드가 삭제되고, 클러스터의 다음 노드가 차단 및 드레이닝 프로세스를 시작합니다.

[az aks upgrade][] 명령을 사용하여 AKS 클러스터를 업그레이드합니다. 다음 예제에서는 클러스터를 Kubernetes 버전 *1.10.9*로 업데이트합니다.

> [!NOTE]
> 부 버전을 한 번에 하나씩 업그레이드할 수 있습니다. 예를 들어 *1.9.11*에서 *1.10.9*로 업그레이드할 수 있지만, *1.9.6*에서 *1.11.x*로 바로 업그레이드할 수는 없습니다. *1.9.11*에서 *1.11.x*로 업그레이드하려면 먼저 *1.9.11*에서 *1.10.x*로 업그레이드한 다음, *1.10.x*에서 *1.11.x*로 업그레이드합니다.

```azurecli
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.10.9
```

압축된 다음 예제 출력에서는 *kubernetesVersion* 현재 보고서 *1.10.9*를 표시합니다.

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
  "kubernetesVersion": "1.10.9",
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

다음 예제 출력은 AKS 클러스터가 *KubernetesVersion 1.10.9*를 실행하는 것을 보여줍니다.

```
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.10.9               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

## <a name="delete-the-cluster"></a>클러스터 삭제

이 자습서는 시리즈의 마지막 부분이므로 AKS 클러스터를 삭제하는 것이 좋습니다. Kubernetes 노드가 Azure VM(가상 머신)에서 실행되므로 클러스터를 사용하지 않더라도 요금이 계속 부과됩니다. [az group delete][az-group-delete] 명령을 사용하여 리소스 그룹, 컨테이너 서비스 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> 클러스터를 삭제할 때, AKS 클러스터에 사용되는 Azure Active Directory 서비스 주체는 제거되지 않습니다. 서비스 주체를 제거하는 방법에 대한 단계는 [AKS 서비스 주체 고려 사항 및 삭제][sp-delete]를 참조하세요.

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
[az-group-delete]: /cli/azure/group#az-group-delete
[sp-delete]: kubernetes-service-principal.md#additional-considerations
