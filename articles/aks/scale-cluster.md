---
title: AKS(Azure Kubernetes Service) 클러스터 크기 조정
description: AKS(Azure Kubernetes Service) 클러스터의 노드 수를 조정하는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 09/16/2020
ms.openlocfilehash: d5686a74ffe138af51d2319c839a3a5c5887f992
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90902937"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>AKS(Azure Kubernetes Service) 클러스터의 노드 수 조정

애플리케이션의 리소스 요구량이 변경되면 AKS 클러스터의 크기를 수동으로 조정하여 실행되는 노드 수를 변경할 수 있습니다. 클러스터를 축소하면 실행 중인 애플리케이션의 중단을 최소화하기 위해 노드 수가 적절하게 [제한 및 감소][kubernetes-drain]됩니다. 수직 확장 하는 경우 AKS은 노드가 Kubernetes 클러스터에 표시 될 때까지 기다린 후 `Ready` pod 예약 됩니다.

## <a name="scale-the-cluster-nodes"></a>클러스터 노드 크기 조정

먼저 [az aks show][az-aks-show] 명령을 사용 하 여 노드 풀의 *이름을* 가져옵니다. 다음 예제에서는 *Myresourcegroup* 리소스 그룹에서 *myAKSCluster* 이라는 클러스터의 노드 풀 이름을 가져옵니다.

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

[Az aks scale][az-aks-scale] 명령을 사용 하 여 클러스터 노드의 크기를 조정 합니다. 다음 예제에서는 *myAKSCluster*라는 클러스터를 단일 노드로 크기 조정합니다. `--nodepool-name` *Nodepool1*와 같은 이전 명령에서 직접 제공 합니다.

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


## <a name="scale-user-node-pools-to-0"></a>`User`노드 풀 크기를 0으로 조정

`System`항상 노드를 실행 해야 하는 노드 풀과 달리 `User` 노드 풀을 사용 하면 0으로 확장할 수 있습니다. 시스템 및 사용자 노드 풀의 차이점에 대해 자세히 알아보려면 [시스템 및 사용자 노드 풀](use-system-pools.md)을 참조 하세요.

사용자 풀의 크기를 0으로 조정 하려면 위 명령 대신 [az aks nodepool scale][az-aks-nodepool-scale] 을 사용 하 `az aks scale` 고 노드 수로 0을 설정할 수 있습니다.


```azurecli-interactive
az aks nodepool scale --name <your node pool name> --cluster-name myAKSCluster --resource-group myResourceGroup  --node-count 0 
```

`User` `--min-count` [클러스터 Autoscaler](cluster-autoscaler.md) 의 매개 변수를 0으로 설정 하 여 노드 풀을 0 개 노드로 자동 크기 조정 할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 AKS 클러스터를 수동으로 확장 하 여 노드 수를 늘리거나 줄입니다. [클러스터 autoscaler][cluster-autoscaler] 를 사용 하 여 클러스터를 자동으로 확장할 수도 있습니다.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-scale&preserve-view=true