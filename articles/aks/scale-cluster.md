---
title: AKS(Azure Kubernetes Service) 클러스터 크기 조정
description: AKS(Azure Kubernetes Service) 클러스터의 노드 수를 조정하는 방법을 알아봅니다.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: 719f45aeeb5c7aa7e9b5e597ed461808c9d2b005
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73472582"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>AKS(Azure Kubernetes Service) 클러스터의 노드 수 조정

애플리케이션의 리소스 요구량이 변경되면 AKS 클러스터의 크기를 수동으로 조정하여 실행되는 노드 수를 변경할 수 있습니다. 규모를 축소 하면 실행 중인 응용 프로그램의 중단을 최소화 하기 위해 노드를 신중 하 게 [통제 하 고 방전][kubernetes-drain] 합니다. 확장 하는 경우 AKS는 Kubernetes 클러스터에 의해 노드가 `Ready` 표시 될 때까지 기다린 후 pod 예약 합니다.

## <a name="scale-the-cluster-nodes"></a>클러스터 노드 크기 조정

먼저 [az aks show][az-aks-show] 명령을 사용 하 여 노드 풀의 *이름을* 가져옵니다. 다음 예제에서는 *Myresourcegroup* 리소스 그룹에서 *myAKSCluster* 이라는 클러스터의 노드 풀 이름을 가져옵니다.

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

[Az aks scale][az-aks-scale] 명령을 사용 하 여 클러스터 노드의 크기를 조정 합니다. 다음 예제에서는 *myAKSCluster*라는 클러스터를 단일 노드로 크기 조정합니다. 위 명령에서 *nodepool1*과 같은 실제 *--nodepool-name*을 입력합니다.

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

이 문서에서는 AKS 클러스터를 수동으로 확장 하 여 노드 수를 늘리거나 줄입니다. [클러스터 autoscaler][cluster-autoscaler] 를 사용 하 여 클러스터를 자동으로 확장할 수도 있습니다.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
