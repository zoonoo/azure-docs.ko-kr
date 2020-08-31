---
title: 미리 보기-Azure Kubernetes 서비스 (AKS) 클러스터에 별색 노드 풀 추가
description: Azure Kubernetes 서비스 (AKS) 클러스터에 별색 노드 풀을 추가 하는 방법에 대해 알아봅니다.
services: container-service
ms.service: container-service
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: dbb003c287a18810c2c14c4f2ea401fa55cca427
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87987293"
---
# <a name="preview---add-a-spot-node-pool-to-an-azure-kubernetes-service-aks-cluster"></a>미리 보기-Azure Kubernetes 서비스 (AKS) 클러스터에 별색 노드 풀 추가

별색 노드 풀은 [별색 가상 머신 확장 집합][vmss-spot]에 의해 지원 되는 노드 풀입니다. AKS 클러스터를 사용 하 여 노드에 스폿 Vm을 사용 하면 상당한 비용 절감으로 Azure에서 사용 되지 않는 용량을 활용할 수 있습니다. 사용 가능한 사용률 용량의 양은 노드 크기, 지역 및 시간을 포함 하 여 여러 요인에 따라 달라 집니다.

별색 노드 풀을 배포 하는 경우 Azure는 사용 가능한 용량이 있는 경우 별색 노드를 할당 합니다. 하지만 스폿 노드에 대 한 SLA는 없습니다. 별색 노드 풀을 지 원하는 스폿 확장 집합은 단일 장애 도메인에 배포 되며 고가용성을 보장 하지 않습니다. Azure에서 용량을 다시 필요로 하는 경우 언제 든 지 Azure 인프라에서 스폿 노드를 제거 합니다.

지점 노드는 중단, 조기 종료 또는 제거를 처리할 수 있는 워크 로드에 적합 합니다. 예를 들어 일괄 처리 작업, 개발 및 테스트 환경, 대량 계산 워크 로드 등의 작업은 별색 노드 풀에서 예약 하는 것이 좋습니다.

이 문서에서는 기존 Azure Kubernetes 서비스 (AKS) 클러스터에 보조 노드 풀을 추가 합니다.

이 문서에서는 Kubernetes 및 Azure Load Balancer 개념을 기본적으로 이해하고 있다고 가정합니다. 자세한 내용은 [AKS(Azure Kubernetes Service)의 Kubernetes 핵심 개념][kubernetes-concepts]을 참조하세요.

이 기능은 현재 미리 보기로 제공됩니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="before-you-begin"></a>시작하기 전에

클러스터를 만들어 별색 노드 풀을 사용할 경우 해당 클러스터는 노드 풀 및 *표준* SKU 부하 분산 장치에 대 한 Virtual Machine Scale Sets도 사용 해야 합니다. 또한 클러스터를 만든 후에는 별색 노드 풀을 사용 하기 위해 추가 노드 풀을 추가 해야 합니다. 추가 노드 풀 추가는 이후 단계에서 설명 하지만 먼저 미리 보기 기능을 사용 하도록 설정 해야 합니다.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="register-spotpoolpreview-preview-feature"></a>Spotpoolpreview 미리 보기 기능 등록

별색 노드 풀을 사용 하는 AKS 클러스터를 만들려면 구독에서 *spotpoolpreview* 기능 플래그를 사용 하도록 설정 해야 합니다. 이 기능은 클러스터를 구성할 때 제공 되는 최신 서비스 기능 집합을 제공 합니다.

다음 예제와 같이 [az feature register][az-feature-register] 명령을 사용 하 여 *spotpoolpreview* feature 플래그를 등록 합니다.

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "spotpoolpreview"
```

상태가 *Registered*로 표시되는 데 몇 분 정도 걸립니다. [az feature list][az-feature-list] 명령을 사용하여 등록 상태를 확인할 수 있습니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/spotpoolpreview')].{Name:name,State:properties.state}"
```

준비가 되면 [az provider register][az-provider-register] 명령을 사용하여 *Microsoft.ContainerService* 리소스 공급자 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 확장 설치

별색 노드 풀을 사용 하는 AKS 클러스터를 만들려면 *AKS-preview* CLI 확장 버전 0.4.32 이상이 필요 합니다. [az extension add][az-extension-add] 명령을 사용하여 *aks-preview* Azure CLI 확장을 설치한 후 [az extension update][az-extension-update] 명령을 사용하여 사용 가능한 업데이트를 확인합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview
 
# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>제한 사항

스폿 노드 풀로 AKS 클러스터를 만들고 관리 하는 경우 다음과 같은 제한 사항이 적용 됩니다.

* 별색 노드 풀은 클러스터의 기본 노드 풀 일 수 없습니다. 별색 노드 풀은 보조 풀에만 사용할 수 있습니다.
* 별색 노드 풀은 cordon 및 드레이닝을 보장할 수 없으므로 별색 노드 풀을 업그레이드할 수 없습니다. Kubernetes 버전 업그레이드와 같은 작업을 수행 하려면 기존 별색 노드 풀을 새 항목으로 바꾸어야 합니다. 별색 노드 풀을 교체 하려면 Kubernetes의 다른 버전을 사용 하 여 새 별색 노드 풀을 만들고, 상태가 *Ready*가 될 때까지 기다린 후 이전 노드 풀을 제거 합니다.
* 제어 평면과 노드 풀을 동시에 업그레이드할 수는 없습니다. 이러한 항목은 개별적으로 업그레이드 하거나, 제어 평면과 나머지 노드 풀을 동시에 업그레이드 하기 위해 별색 노드 풀을 제거 해야 합니다.
* 별색 노드 풀은 Virtual Machine Scale Sets를 사용 해야 합니다.
* ScaleSetPriority 또는 SpotMaxPrice를 만든 후에는 변경할 수 없습니다.
* SpotMaxPrice를 설정 하는 경우 값은-1 이거나 최대 5 개의 소수 자릿수를 포함 하는 양수 값 이어야 합니다.
* 별색 노드 풀에는 *kubernetes.azure.com/scalesetpriority:spot*, taint *kubernetes.azure.com/scalesetpriority=spot:NoSchedule*및 system pod에는 선호도가 포함 됩니다.
* 별색 노드 풀에서 작업을 예약 하려면 [해당 toleration][spot-toleration] 를 추가 해야 합니다.

## <a name="add-a-spot-node-pool-to-an-aks-cluster"></a>AKS 클러스터에 스폿 노드 풀 추가

여러 노드 풀을 사용할 수 있는 기존 클러스터에 별색 노드 풀을 추가 해야 합니다. 여러 노드 풀로 AKS 클러스터를 만드는 방법에 대 한 자세한 내용은 [여기][use-multiple-node-pools]에서 사용할 수 있습니다.

[Az aks nodepool add][az-aks-nodepool-add]를 사용 하 여 노드 풀을 만듭니다.
```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name spotnodepool \
    --priority Spot \
    --eviction-policy Delete \
    --spot-max-price -1 \
    --enable-cluster-autoscaler \
    --min-count 1 \
    --max-count 3 \
    --no-wait
```

기본적으로 여러 노드 풀을 사용 하 여 클러스터를 만들 때 AKS 클러스터에서 *우선 순위가* *Regular* 인 노드 풀을 만듭니다. 위의 명령은 *최우선 순위의* 기존 AKS 클러스터에 보조 노드 풀을 추가 *합니다.* *지점* *우선 순위* 는 노드 풀을 별색 노드 풀로 만듭니다. *제거 정책* 매개 변수는 위의 예제에서 기본값 인 *Delete* 로 설정 됩니다. [제거 정책을][eviction-policy] *삭제*로 설정 하면 노드 풀의 기본 확장 집합에 있는 노드가 제거 될 때 삭제 됩니다. 제거 정책을 *할당*취소로 설정할 수도 있습니다. 제거 정책을 *할당*취소로 설정 하면 제거 시 기본 확장 집합의 노드가 중지 된 할당 취소 상태로 설정 됩니다. 중지 됨-할당 취소 된 상태에 있는 노드는 계산 할당량에 대해 계산 되며, 클러스터 크기 조정 또는 업그레이드에 문제가 발생할 수 있습니다. *우선 순위* 및 *제거 정책* 값은 노드 풀을 만드는 동안에만 설정할 수 있습니다. 이러한 값은 나중에 업데이트할 수 없습니다.

또한이 명령은 [클러스터 autoscaler][cluster-autoscaler]사용 하도록 설정 합니다 .이는 별색 노드 풀에서 사용 하는 것이 좋습니다. 클러스터에서 실행 되는 작업에 따라 클러스터는 노드 풀의 노드 수를 확장 하 고 축소 autoscaler 합니다. 별색 노드 풀의 경우 클러스터 autoscaler 추가 노드가 여전히 필요한 경우 제거 후 노드 수를 확장 합니다. 노드 풀에 포함 될 수 있는 최대 노드 수를 변경 하는 경우 `maxCount` 클러스터 autoscaler 연결 된 값도 조정 해야 합니다. 클러스터 autoscaler를 사용 하지 않는 경우 제거 시 스폿 풀은 결국 0으로 줄어들고 추가 별색 노드를 수신 하기 위한 수동 작업이 필요 합니다.

> [!Important]
> 일괄 처리 작업 및 테스트 환경과 같은 중단을 처리할 수 있는 별색 노드 풀 에서만 작업을 예약 합니다. 노드 제거를 처리할 수 있는 작업만 별색 노드 풀에서 예약 되도록 별색 노드 풀에 [taints 및 tolerations][taints-tolerations] 를 설정 하는 것이 좋습니다. 예를 들어 위의 command taint는 *kubernetes.azure.com/scalesetpriority=spot:NoSchedule* 의 pod를 추가 하므로 해당 toleration가 있는만이 노드에 예약 됩니다.

## <a name="verify-the-spot-node-pool"></a>별색 노드 풀 확인

노드 풀이 별색 노드 풀로 추가 되었는지 확인 하려면 다음을 수행 합니다.

```azurecli
az aks nodepool show --resource-group myResourceGroup --cluster-name myAKSCluster --name spotnodepool
```

*ScaleSetPriority* *있는지 확인 합니다.*

Pod가 스폿 노드에서 실행 되도록 예약 하려면 스폿 노드에 적용 된 taint에 해당 하는 toleration를 추가 합니다. 다음 예제에서는 이전 단계에서 사용 된 *kubernetes.azure.com/scalesetpriority=spot:NoSchedule* taint에 해당 하는 toleration를 정의 하는 yaml 파일의 일부를 보여 줍니다.

```yaml
spec:
  containers:
  - name: spot-example
  tolerations:
  - key: "kubernetes.azure.com/scalesetpriority"
    operator: "Equal"
    value: "spot"
    effect: "NoSchedule"
   ...
```

이 toleration를 포함 하는 pod가 배포 되 면 Kubernetes가 taint가 적용 된 노드에서 pod를 예약할 수 있습니다.

## <a name="max-price-for-a-spot-pool"></a>스폿 풀의 최대 가격
[지점 인스턴스의 가격은][pricing-spot]지역 및 SKU를 기준으로 하는 변수입니다. 자세한 내용은 [Linux][pricing-linux] 및 [Windows][pricing-windows]가격 책정을 참조 하세요.

가변 가격 책정을 사용 하는 경우 최대 5 개의 소수 자릿수를 사용 하 여 미국 달러 (USD)로 최대 가격을 설정 하는 옵션을 사용할 수 있습니다. 예를 들어 값 *0.98765* 는 시간당 $0.98765 USD의 최대 가격이 됩니다. 최대 가격을 *-1*로 설정 하면 인스턴스는 가격에 따라 제거 되지 않습니다. 인스턴스의 가격은 사용 가능한 용량 및 할당량을 초과 하는 경우에 대비 하 여 현재 가격 또는 표준 인스턴스의 가격으로 사용 됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 AKS 클러스터에 스폿 노드 풀을 추가 하는 방법을 알아보았습니다. 노드 풀에서 pod을 제어 하는 방법에 대 한 자세한 내용은 [AKS의 advanced scheduler 기능 모범 사례][operator-best-practices-advanced-scheduler]를 참조 하세요.

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-deploy-create]: /cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-template-deploy]: ../azure-resource-manager/templates/deploy-cli.md#deployment-scope
[cluster-autoscaler]: cluster-autoscaler.md
[eviction-policy]: ../virtual-machine-scale-sets/use-spot.md#eviction-policy
[kubernetes-concepts]: concepts-clusters-workloads.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[pricing-linux]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/
[pricing-spot]: ../virtual-machine-scale-sets/use-spot.md#pricing
[pricing-windows]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/
[spot-toleration]: #verify-the-spot-node-pool
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[use-multiple-node-pools]: use-multiple-node-pools.md
[vmss-spot]: ../virtual-machine-scale-sets/use-spot.md