---
title: AKS(Azure Kubernetes Service) 클러스터 크기 조정
description: AKS(Azure Kubernetes Service) 클러스터의 노드 수를 조정하는 방법을 알아봅니다.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfoulds
ms.openlocfilehash: de3f8613c93715aecf7e9e066a8ad1d82e4379e3
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475137"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>AKS(Azure Kubernetes Service) 클러스터의 노드 수 조정

애플리케이션의 리소스 요구량이 변경되면 AKS 클러스터의 크기를 수동으로 조정하여 실행되는 노드 수를 변경할 수 있습니다. 클러스터를 축소하면 실행 중인 애플리케이션의 중단을 최소화하기 위해 노드 수가 적절하게 [제한 및 감소][kubernetes-drain]됩니다. AKS 노드는 표시 될 때까지 대기 강화 하는 경우 `Ready` pod에 예약 된 전에 Kubernetes 클러스터에서.

## <a name="scale-the-cluster-nodes"></a>클러스터 노드 크기 조정

먼저 가져옵니다 합니다 *이름* 사용 하 여 노드 풀을 [az aks 표시] [ az-aks-show] 명령 합니다. 다음 예제에서는 라는 클러스터에 대 한 노드 풀 이름을 가져옵니다 *myAKSCluster* 에 *myResourceGroup* 리소스 그룹:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

다음 예제 출력에는 *name*이 *nodepool1*로 표시되어 있습니다.

```console
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles

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

사용 된 [az aks 확장] [ az-aks-scale] 클러스터 노드를 확장 하는 명령입니다. 다음 예제에서는 *myAKSCluster*라는 클러스터를 단일 노드로 크기 조정합니다. 위 명령에서 *nodepool1*과 같은 실제 *--nodepool-name*을 입력합니다.

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

이 문서에서는 수동으로 노드 수를 늘리거나 AKS 클러스터를 확장 합니다. 사용할 수도 있습니다는 [autoscaler 클러스터] [ cluster-autoscaler] (현재는 AKS에서 미리 보기)을 자동으로 클러스터를 확장 합니다.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
