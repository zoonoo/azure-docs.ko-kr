---
title: Azure Kubernetes 서비스 (AKS) 시작 및 중지
description: AKS (Azure Kubernetes Service) 클러스터를 중지 하거나 시작 하는 방법에 대해 알아봅니다.
services: container-service
ms.topic: article
ms.date: 09/18/2020
author: palma21
ms.openlocfilehash: a743a6c30d5ce8bcaf275bf1a658f8343de4d4fb
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90937881"
---
# <a name="stop-and-start-an-azure-kubernetes-service-aks-cluster-preview"></a>AKS (Azure Kubernetes Service) 클러스터 (미리 보기)를 중지 하고 시작 합니다.

업무 시간 중에만 사용 되는 개발 클러스터와 같이 AKS 워크 로드를 지속적으로 실행할 필요가 없을 수도 있습니다. 이로 인해 Azure Kubernetes 서비스 (AKS) 클러스터가 유휴 상태일 수 있으며 시스템 구성 요소를 초과 하지 않는 시간이 발생 합니다. [모든 `User` 노드 풀을 0으로 확장](scale-cluster.md#scale-user-node-pools-to-0)하여 클러스터 공간을 줄일 수 있지만, 클러스터가 실행되는 동안에는 시스템 구성 요소를 실행 하는 데에도 [ `System` 풀이](use-system-pools.md) 필요 합니다. 이러한 기간 동안 비용을 더 최적화 하려면 클러스터를 완전히 해제(중지) 할 수 있습니다. 이 작업을 수행하면 제어 평면과 에이전트 노드가 모두 중지 되므로 모든 계산 비용을 절감할 수 있을 뿐만 아니라 다시 시작할 때에 저장된 모든 개체 및 클러스터 상태를 유지할 수 있습니다. 이를 통해 주말 이후부터 또는 일괄 처리 작업을 실행 하는 동안에만 클러스터를 실행 하는 위치를 선택할 수 있습니다.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작 [Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

### <a name="install-the-aks-preview-azure-cli"></a>Azure CLI 설치 `aks-preview` 

*Aks-preview* Azure CLI 확장 버전 0.4.64 이상이 필요 합니다. [Az extension add][az-extension-add] 명령을 사용하여 *aks-preview* Azure CLI 확장을 설치 합니다. 또는 [az extension update][az-extension-update] 명령을 사용하여 사용 가능한 업데이트를 설치 합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

> [!WARNING]
> 중지된 AKS 클러스터의 클러스터 상태는 최대 12 개월 동안 보존 됩니다. 클러스터가 12 개월 넘게 중지 된 경우 클러스터 상태를 복구할 수 없습니다. 자세한 내용은 [AKS 지원 정책](support-policies.md)을 참조 하세요.
> 중지된 AKS 클러스터를 시작 하거나 삭제할 수만 있습니다. 크기 조정 또는 업그레이드와 같은 작업을 수행 하려면 먼저 클러스터를 시작 합니다.


### <a name="register-the-startstoppreview-preview-feature"></a>`StartStopPreview`미리 보기 기능 등록

클러스터 시작/중지 기능을 사용 하려면 `StartStopPreview` 구독에서 기능 플래그를 사용 하도록 설정 해야 합니다.

`StartStopPreview`다음 예제와 같이 [az feature register][az-feature-register] 명령을 사용하여 기능 플래그를 등록 합니다.

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "StartStopPreview"
```

상태가 *Registered*로 표시되는 데 몇 분 정도 걸립니다. [Az feature list][az-feature-list] 명령을 사용하여 등록 상태를 확인 합니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/StartStopPreview')].{Name:name,State:properties.state}"
```

준비가 되면 [az provider register][az-provider-register] 명령을 사용하여 *ContainerService* 리소스 공급자의 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="stop-an-aks-cluster"></a>AKS 클러스터 중지

명령을 사용하여 `az aks stop` 실행 중인 AKS 클러스터의 노드 및 제어 평면을 중지할 수 있습니다. 다음 예제에서는 *myAKSCluster*라는 클러스터를 중지 합니다.

```azurecli-interactive
az aks stop --name myAKSCluster --resource-group myResourceGroup
```

[Az aks show] [az-aks-show] 명령을 사용하여 클러스터가 중지 된 시기를 확인하고 `powerState` 아래 출력에 표시 된 것 처럼 표시 되는지 확인할 수 있습니다 `Stopped` .

```json
{
[...]
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_westus2",
  "powerState":{
    "code":"Stopped"
  },
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
[...]
}
```

`provisioningState` `Stopping` 이 표시되면 클러스터가 아직 완전히 중지 되지 않았음을 의미 합니다.

> [!IMPORTANT]
> [Pod 중단 예산을](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) 사용하는 경우 드레이닝 프로세스를 완료 하는 데 시간이 더 오래 걸릴 수 있으므로 중지 작업에 시간이 더 오래 걸릴 수 있습니다.


## <a name="start-an-aks-cluster"></a>AKS 클러스터 시작

명령을 사용하여 `az aks start` 중지 된 AKS 클러스터의 노드 및 제어 평면을 시작할 수 있습니다. 클러스터가 이전 제어 평면 상태 및 에이전트 노드 수로 다시 시작 됩니다.  
다음 예제에서는 *myAKSCluster*라는 클러스터를 시작 합니다.

```azurecli-interactive
az aks start --name myAKSCluster --resource-group myResourceGroup
```

[Az aks show] [az-aks-show] 명령을 사용하여 클러스터가 시작 된 시기를 확인 하 고 `powerState` `Running` 아래 출력에 표시 된 것으로 확인 합니다.

```json
{
[...]
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_westus2",
  "powerState":{
    "code":"Running"
  },
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
[...]
}
```

`provisioningState` `Starting` 이 표시되면 클러스터가 아직 완전히 시작 되지 않았음을 의미 합니다.


## <a name="next-steps"></a>다음 단계

- 풀 크기를 0으로 조정 하는 방법에 대한 자세한 `User` 내용은 [ `User` 풀 크기 조정](scale-cluster.md#scale-user-node-pools-to-0)을 참조 하세요.
- 스폿 인스턴스를 사용하여 비용을 절감 하는 방법을 알아보려면 [AKS에 별색 노드 풀 추가](spot-node-pool.md)를 참조 하세요.
- AKS 지원 정책에 대한 자세한 내용은 [AKS 지원 정책](support-policies.md)을 참조 하세요.

<!-- LINKS - external -->

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli&preserve-view=true
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add&preserve-view=true
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update&preserve-view=true
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest#az-feature-register&preserve-view=true
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true
