---
title: 근접 배치 그룹을 사용 하 여 AKS (Azure Kubernetes Service) 클러스터의 대기 시간 줄이기
description: 근접 배치 그룹을 사용 하 여 AKS 클러스터 워크 로드에 대 한 대기 시간을 줄이는 방법에 대해 알아봅니다.
services: container-service
manager: gwallace
ms.topic: article
ms.date: 10/19/2020
ms.openlocfilehash: fa81e293bc5e53a852bdb404f9e6d41c4297647b
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93349038"
---
# <a name="reduce-latency-with-proximity-placement-groups"></a>근접 배치 그룹을 사용 하 여 대기 시간 단축

> [!Note]
> AKS에서 근접 배치 그룹을 사용 하는 경우 공동 배치는 에이전트 노드에만 적용 됩니다. 노드-노드 및 pod 대기 시간에 해당 하는 호스트 된 pod가 개선 되었습니다. 공동 위치는 클러스터의 제어 평면 배치에 영향을 주지 않습니다.

Azure에서 응용 프로그램을 배포할 때 지역 또는 가용성 영역에 VM (가상 머신)을 분산 시키면 네트워크 대기 시간이 만들어지므로 응용 프로그램의 전반적인 성능에 영향을 줄 수 있습니다. 근접 배치 그룹은 Azure 계산 리소스가 물리적으로 서로 가까이 있는 상태를 확인 하는 데 사용 되는 논리적 그룹화입니다. 게임, 엔지니어링 시뮬레이션 및 HFT (높은 빈도 거래)와 같은 일부 응용 프로그램에는 짧은 대기 시간 및 신속 하 게 완료 되는 작업이 필요 합니다. 이러한 HPC (고성능 컴퓨팅) 시나리오의 경우 클러스터의 노드 풀에 대해 (ppg ( [근접 배치 그룹](../virtual-machines/linux/co-location.md#proximity-placement-groups) )을 사용 하는 것이 좋습니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 Azure CLI 버전 2.14 이상을 실행 해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

### <a name="limitations"></a>제한 사항

* 근접 배치 그룹은 최대 하나의 가용성 영역에 매핑할 수 있습니다.
* 노드 풀은 Virtual Machine Scale Sets를 사용 하 여 근접 배치 그룹을 연결 해야 합니다.
* 노드 풀은 노드 풀을 만들 때만 근접 배치 그룹을 연결할 수 있습니다.

## <a name="node-pools-and-proximity-placement-groups"></a>노드 풀 및 근접 배치 그룹

근접 배치 그룹을 사용 하 여 배포 하는 첫 번째 리소스는 특정 데이터 센터에 연결 됩니다. 동일한 근접 배치 그룹을 사용 하 여 배포 된 추가 리소스는 동일한 데이터 센터에 공동 배치 됩니다. 근접 배치 그룹을 사용 하는 모든 리소스를 중지 (할당 취소) 하거나 삭제 한 후에는 더 이상 연결 되지 않습니다.

* 많은 노드 풀을 단일 근접 배치 그룹에 연결할 수 있습니다.
* 노드 풀은 단일 근접 배치 그룹에만 연결할 수 있습니다.

### <a name="configure-proximity-placement-groups-with-availability-zones"></a>가용성 영역을 사용 하 여 근접 배치 그룹 구성

> [!NOTE]
> 근접 배치 그룹에는 최대 하나의 가용성 영역을 사용 하는 노드 풀이 필요 하지만, [99.9%의 기준 AZURE VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) 는 여전히 단일 영역의 vm에 적용 됩니다.

근접 배치 그룹은 노드 풀 개념 이며 각 개별 노드 풀과 연결 됩니다. PPG 리소스를 사용 하면 AKS 제어 평면 가용성에 영향을 주지 않습니다. 영역을 사용 하 여 클러스터를 디자인 하는 방법에 영향을 줄 수 있습니다. 클러스터가 여러 영역에 분산 되도록 하려면 다음 설계를 수행 하는 것이 좋습니다.

* 3 개 영역을 사용 하 고 근접 배치 그룹이 연결 되지 않은 첫 번째 시스템 풀을 사용 하 여 클러스터를 프로 비전 합니다. 이렇게 하면 시스템이 여러 영역에 걸쳐 분산 되는 전용 노드 풀에 pod 됩니다.
* 각 풀에 연결 된 고유한 영역 및 근접 배치 그룹을 사용 하 여 추가 사용자 노드 풀을 추가 합니다. 예는 영역 1 및 PPG1에 있는 nodepool1, 영역 2 및 PPG2의 nodepool2, nodepool3가 있는 영역 3의 PPG3입니다. 이렇게 하면 클러스터 수준에서 노드가 여러 영역에 분산 되며 개별 노드 풀은 지정 된 영역에서 전용 PPG 리소스로 공동 배치 됩니다.

## <a name="create-a-new-aks-cluster-with-a-proximity-placement-group"></a>근접 배치 그룹을 사용 하 여 새 AKS 클러스터 만들기

다음 예제에서는 [az group create][az-group-create] 명령을 사용 하 여 *Centralus* 지역에 *myresourcegroup* 이라는 리소스 그룹을 만듭니다. 그런 다음 *myAKSCluster* 라는 AKS 클러스터가 [az AKS create][az-aks-create] 명령을 사용 하 여 만들어집니다.

가속화 된 네트워킹을 통해 가상 컴퓨터의 네트워킹 성능이 크게 향상 됩니다. 이상적으로는 근접 배치 그룹을 가속 네트워킹과 함께 사용 하는 것이 좋습니다. 기본적으로 AKS는 [지원 되는 가상 컴퓨터 인스턴스에서](../virtual-network/create-vm-accelerated-networking-cli.md?toc=/azure/virtual-machines/linux/toc.json#limitations-and-constraints)가속화 된 네트워킹을 사용 합니다. 여기에는 두 개 이상의 vcpus가 포함 된 대부분의 Azure 가상 컴퓨터가 포함 됩니다.

첫 번째 시스템 노드 풀에 연결 된 근접 배치 그룹을 사용 하 여 새 AKS 클러스터를 만듭니다.

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```
다음 명령을 실행 하 고 반환 되는 ID를 저장 합니다.

```azurecli-interactive
# Create proximity placement group
az ppg create -n myPPG -g myResourceGroup -l centralus -t standard
```

명령은 예정 된 CLI 명령에 필요한 *id* 값을 포함 하는 출력을 생성 합니다.

```output
{
  "availabilitySets": null,
  "colocationStatus": null,
  "id": "/subscriptions/yourSubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Compute/proximityPlacementGroups/myPPG",
  "location": "centralus",
  "name": "myPPG",
  "proximityPlacementGroupType": "Standard",
  "resourceGroup": "myResourceGroup",
  "tags": {},
  "type": "Microsoft.Compute/proximityPlacementGroups",
  "virtualMachineScaleSets": null,
  "virtualMachines": null
}
```

아래 명령에서 *myPPGResourceID* 값에 대 한 근접 배치 그룹 리소스 ID를 사용 합니다.

```azurecli-interactive
# Create an AKS cluster that uses a proximity placement group for the initial system node pool only. The PPG has no effect on the cluster control plane.
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --ppg myPPGResourceID
```

## <a name="add-a-proximity-placement-group-to-an-existing-cluster"></a>기존 클러스터에 근접 배치 그룹 추가

새 노드 풀을 만들어 기존 클러스터에 근접 배치 그룹을 추가할 수 있습니다. 그런 다음 필요에 따라 기존 워크 로드를 새 노드 풀로 마이그레이션한 다음 원래 노드 풀을 삭제할 수 있습니다.

이전에 만든 것과 동일한 근접 배치 그룹을 사용 하 여 AKS 클러스터의 두 노드 풀에 있는 에이전트 노드가 물리적으로 동일한 데이터 센터에 위치 하는지 확인 합니다.

앞에서 만든 근접 배치 그룹의 리소스 ID를 사용 하 고 명령을 사용 하 여 새 노드 풀을 추가 합니다 [`az aks nodepool add`][az-aks-nodepool-add] .

```azurecli-interactive
# Add a new node pool that uses a proximity placement group, use a --node-count = 1 for testing
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 1 \
    --ppg myPPGResourceID
```

## <a name="clean-up"></a>정리

클러스터를 삭제 하려면 명령을 사용 하 여 [`az group delete`][az-group-delete] AKS 리소스 그룹을 삭제 합니다.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>다음 단계

* [근접 배치 그룹][proximity-placement-groups]에 대해 자세히 알아보세요.

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[proximity-placement-groups]: ../virtual-machines/linux/co-location.md#proximity-placement-groups
[az-aks-create]: /cli/azure/aks#az-aks-create
[system-pool]: ./use-system-pools.md
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
