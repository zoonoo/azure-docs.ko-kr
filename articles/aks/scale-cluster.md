---
title: AKS(Azure Kubernetes Service) 클러스터 크기 조정
description: AKS(Azure Kubernetes Service) 클러스터의 노드 수를 조정하는 방법을 알아봅니다.
services: container-service
author: iainfoulds
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: 55d7a00a0a8c0b655f06810f8bcea7126bb9167f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368420"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>AKS(Azure Kubernetes Service) 클러스터의 노드 수 조정

애플리케이션의 리소스 요구량이 변경되면 AKS 클러스터의 크기를 수동으로 조정하여 실행되는 노드 수를 변경할 수 있습니다. 클러스터를 축소하면 실행 중인 애플리케이션의 중단을 최소화하기 위해 노드 수가 적절하게 [제한 및 감소][kubernetes-drain]됩니다. 확장하면 AKS는 노드가 Kubernetes `Ready` 클러스터로 표시될 때까지 기다렸다가 포드가 예약됩니다.

## <a name="scale-the-cluster-nodes"></a>클러스터 노드 크기 조정

먼저 [az aks show][az-aks-show] 명령을 사용하여 노드 풀의 *이름을* 가져옵니다. 다음 예제에서는 *myResourceGroup* 리소스 그룹에서 *myAKSCluster라는* 클러스터의 노드 풀 이름을 가져옵니다.

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

다음 예제 출력에는 *name*이 *nodepool1*로 표시되어 있습니다.

```output
[
  {
    "count": 1,
    "maxPods": 110,
    "name": "nodepool1",
    "osDiskSizeGb": 30,
    "osType": "Linux",
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_DS2_v2"
  }
]
```

az [aks 배율][az-aks-scale] 명령을 사용하여 클러스터 노드의 크기를 조정합니다. 다음 예제에서는 *myAKSCluster*라는 클러스터를 단일 노드로 크기 조정합니다. 위 명령에서 *nodepool1*과 같은 실제 *--nodepool-name*을 입력합니다.

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

다음 예제 출력에는 *agentPoolProfiles* 섹션에 나와 있는 것처럼 클러스터의 크기가 노드 하나로 올바르게 조정되었음이 표시됩니다.

```json
{
  "aadProfile": null,
  "addonProfiles": null,
  "agentPoolProfiles": [
    {
      "count": 1,
      "maxPods": 110,
      "name": "nodepool1",
      "osDiskSizeGb": 30,
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS2_v2",
      "vnetSubnetId": null
    }
  ],
  [...]
}
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 노드 수를 늘리거나 줄이기 위해 AKS 클러스터를 수동으로 확장했습니다. [클러스터 자동 크기 조정기를][cluster-autoscaler] 사용하여 클러스터를 자동으로 확장할 수도 있습니다.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
