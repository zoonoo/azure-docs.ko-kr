---
title: AKS (Azure Kubernetes Service) 노드 이미지 업그레이드
description: AKS 클러스터 노드 및 노드 풀에서 이미지를 업그레이드 하는 방법에 대해 알아봅니다.
ms.service: container-service
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: jpalma
ms.openlocfilehash: 83d7d48922806334e2b49494fe0ef1d15e1a7a6a
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531482"
---
# <a name="azure-kubernetes-service-aks-node-image-upgrade"></a>AKS (Azure Kubernetes Service) 노드 이미지 업그레이드

AKS는 노드의 이미지를 업그레이드할 수 있도록 지원 하므로 최신 OS 및 런타임 업데이트를 최신 상태로 유지 합니다. AKS는 최신 업데이트를 사용 하 여 일주일에 하나의 새 이미지를 제공 하므로 Linux 또는 Windows 패치를 비롯 하 여 최신 기능을 위해 노드의 이미지를 정기적으로 업그레이드 하는 것이 유용 합니다. 이 문서에서는 AKS 클러스터 노드 이미지를 업그레이드 하는 방법 및 Kubernetes 버전을 업그레이드 하지 않고 노드 풀 이미지를 업데이트 하는 방법을 보여 줍니다.

AKS에서 제공 하는 최신 이미지에 대 한 자세한 내용은 [AKS 릴리스 정보](https://github.com/Azure/AKS/releases)를 참조 하세요.

클러스터에 대 한 Kubernetes 버전을 업그레이드 하는 방법에 대 한 자세한 내용은 [AKS 클러스터 업그레이드][upgrade-cluster]를 참조 하세요.

> [!NOTE]
> AKS 클러스터는 노드에 대 한 가상 머신 확장 집합을 사용 해야 합니다.

## <a name="check-if-your-node-pool-is-on-the-latest-node-image"></a>노드 풀이 최신 노드 이미지에 있는지 확인 합니다.

다음 명령을 사용 하 여 노드 풀에 사용할 수 있는 최신 노드 이미지 버전을 확인할 수 있습니다. 

```azurecli
az aks nodepool get-upgrades \
    --nodepool-name mynodepool \
    --cluster-name myAKSCluster \
    --resource-group myResourceGroup
```

출력에서 `latestNodeImageVersion` 아래 예제와 같이 확인할 수 있습니다.

```output
{
  "id": "/subscriptions/XXXX-XXX-XXX-XXX-XXXXX/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/nodepool1/upgradeProfiles/default",
  "kubernetesVersion": "1.17.11",
  "latestNodeImageVersion": "AKSUbuntu-1604-2020.10.28",
  "name": "default",
  "osType": "Linux",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.ContainerService/managedClusters/agentPools/upgradeProfiles",
  "upgrades": null
}
```

따라서 `nodepool1` 사용 가능한 최신 노드 이미지는입니다 `AKSUbuntu-1604-2020.10.28` . 이제를 실행 하 여 노드 풀에서 사용 중인 현재 노드 이미지 버전과 비교할 수 있습니다.

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --query nodeImageVersion
```

출력의 예는 다음과 같습니다.

```output
"AKSUbuntu-1604-2020.10.08"
```

따라서이 예제에서는 현재 `AKSUbuntu-1604-2020.10.08` 이미지 버전에서 최신 버전으로 업그레이드할 수 있습니다 `AKSUbuntu-1604-2020.10.28` . 

## <a name="upgrade-all-nodes-in-all-node-pools"></a>모든 노드 풀의 모든 노드 업그레이드

노드 이미지 업그레이드는를 사용 하 여 수행 됩니다 `az aks upgrade` . 노드 이미지를 업그레이드 하려면 다음 명령을 사용 합니다.

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-image-only
```

업그레이드 하는 동안 다음 명령을 사용 하 여 노드 이미지의 상태를 확인 `kubectl` 하 고 현재 노드 이미지 정보를 필터링 합니다.

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

업그레이드가 완료 되 면를 사용 `az aks show` 하 여 업데이트 된 노드 풀 세부 정보를 가져옵니다. 현재 노드 이미지는 속성에 표시 됩니다 `nodeImageVersion` .

```azurecli
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster
```

## <a name="upgrade-a-specific-node-pool"></a>특정 노드 풀 업그레이드

노드 풀에서 이미지를 업그레이드 하는 것은 클러스터에서 이미지를 업그레이드 하는 것과 비슷합니다.

Kubernetes 클러스터 업그레이드를 수행 하지 않고 노드 풀의 OS 이미지를 업데이트 하려면 `--node-image-only` 다음 예제에서 옵션을 사용 합니다.

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-image-only
```

업그레이드 하는 동안 다음 명령을 사용 하 여 노드 이미지의 상태를 확인 `kubectl` 하 고 현재 노드 이미지 정보를 필터링 합니다.

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

업그레이드가 완료 되 면를 사용 `az aks nodepool show` 하 여 업데이트 된 노드 풀 세부 정보를 가져옵니다. 현재 노드 이미지는 속성에 표시 됩니다 `nodeImageVersion` .

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="upgrade-node-images-with-node-surge"></a>노드 서 지를 사용 하 여 노드 이미지 업그레이드

노드 이미지 업그레이드 프로세스의 속도를 높이려면 사용자 지정 가능한 노드 서 수 값을 사용 하 여 노드 이미지를 업그레이드할 수 있습니다. 기본적으로 AKS는 하나의 추가 노드를 사용 하 여 업그레이드를 구성 합니다.

업그레이드 속도를 높이려면 값을 사용 `--max-surge` 하 여 업그레이드에 사용할 노드 수를 구성 합니다. 이렇게 하면 더 빨리 완료할 수 있습니다. 다양 한 설정의 장단점에 대해 자세히 알아보려면 `--max-surge` [노드 서 수 업그레이드 사용자 지정][max-surge]을 참조 하세요.

다음 명령은 노드 이미지 업그레이드를 수행 하기 위한 최대 서 지 값을 설정 합니다.

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --max-surge 33% \
    --node-image-only \
    --no-wait
```

업그레이드 하는 동안 다음 명령을 사용 하 여 노드 이미지의 상태를 확인 `kubectl` 하 고 현재 노드 이미지 정보를 필터링 합니다.

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

`az aks nodepool show`업데이트 된 노드 풀 세부 정보를 가져오려면를 사용 합니다. 현재 노드 이미지는 속성에 표시 됩니다 `nodeImageVersion` .

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="next-steps"></a>다음 단계

- 최신 노드 이미지에 대 한 자세한 내용은 [AKS 릴리스 정보](https://github.com/Azure/AKS/releases) 를 참조 하세요.
- [AKS 클러스터 업그레이드][upgrade-cluster]를 사용 하 여 Kubernetes 버전을 업그레이드 하는 방법을 알아봅니다.
- [GitHub 작업을 사용 하 여 클러스터 및 노드 풀 업그레이드 자동 적용][github-schedule]
- 여러 노드 풀 및 [여러 노드 풀 만들기 및 관리][use-multiple-node-pools]를 사용 하 여 노드 풀을 업그레이드 하는 방법에 대해 자세히 알아보세요.

<!-- LINKS - internal -->
[upgrade-cluster]: upgrade-cluster.md
[github-schedule]: node-upgrade-github-actions.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
