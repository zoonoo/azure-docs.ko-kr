---
title: 미리 보기 - AKS(Azure Kubernetes) 클러스터에 스팟 노드 풀 추가
description: AKS(Azure Kubernetes) 클러스터에 스팟 노드 풀을 추가하는 방법에 대해 알아봅니다.
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 02/25/2020
ms.author: zarhoads
ms.openlocfilehash: 466ad7c88547b6676ba0ae263b74d14059322f1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77622050"
---
# <a name="preview---add-a-spot-node-pool-to-an-azure-kubernetes-service-aks-cluster"></a>미리 보기 - AKS(Azure Kubernetes) 클러스터에 스팟 노드 풀 추가

스팟 노드 풀은 스팟 가상 [시스템 규모 집합으로][vmss-spot]백업되는 노드 풀입니다. AKS 클러스터가 있는 노드에 스팟 VM을 사용하면 Azure에서 사용되지 않는 용량을 크게 절약할 수 있습니다. 사용 가능한 미사용 용량의 양은 노드 크기, 지역 및 시간대를 비롯한 여러 요인에 따라 달라집니다.

스팟 노드 풀을 배포할 때 Azure는 사용 가능한 용량이 있는 경우 스팟 노드를 할당합니다. 그러나 스팟 노드에 대한 SLA는 없습니다. 스팟 노드 풀을 백업하는 스팟 스케일 집합은 단일 오류 도메인에 배포되며 고가용성 보장을 제공하지 않습니다. Azure에서 용량을 다시 필요로 할 때 언제든지 Azure 인프라는 스팟 노드를 제거합니다.

스팟 노드는 중단, 조기 종료 또는 제거를 처리할 수 있는 워크로드에 적합합니다. 예를 들어 일괄 처리 작업, 개발 및 테스트 환경 및 대규모 컴퓨팅 워크로드와 같은 워크로드는 스팟 노드 풀에서 예약할 수 있는 좋은 후보일 수 있습니다.

이 문서에서는 기존 AKS(Azure Kubernetes Service) 클러스터에 보조 스팟 노드 풀을 추가합니다.

이 문서에서는 Kubernetes 및 Azure 로드 밸러서 개념에 대한 기본적인 이해를 가정합니다. 자세한 내용은 [AKS(Azure Kubernetes Service)의 Kubernetes 핵심 개념][kubernetes-concepts]을 참조하세요.

이 기능은 현재 미리 보기로 제공됩니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="before-you-begin"></a>시작하기 전에

스팟 노드 풀을 사용하는 클러스터를 만들 때 해당 클러스터는 노드 풀 및 *표준* SKU 로드 밸런서에 대해가상 시스템 규모 집합도 사용해야 합니다. 또한 스팟 노드 풀을 사용하려면 클러스터를 만든 후 추가 노드 풀을 추가해야 합니다. 추가 노드 풀 추가는 이후 단계에서 다루지만 먼저 미리 보기 기능을 사용하도록 설정해야 합니다.

> [!IMPORTANT]
> AKS 미리보기 기능은 셀프 서비스, 옵트인입니다. 커뮤니티에서 피드백과 버그를 수집하기 위해 제공됩니다. 미리 보기에서 이러한 기능은 프로덕션 용으로 사용할 수 없습니다. 공개 미리보기의 기능은 '최선의 노력'에 해당합니다. AKS 기술 지원 팀의 지원은 태평양 표준 시간대(태평양 표준 시간대) 동안에만 제공됩니다. 자세한 내용은 다음 지원 문서를 참조하십시오.
>
> * [AKS 지원 정책][aks-support-policies]
> * [Azure 지원 FAQ][aks-faq]

### <a name="register-spotpoolpreview-preview-feature"></a>스팟풀 미리보기 미리 보기 기능 등록

스팟 노드 풀을 사용하는 AKS 클러스터를 만들려면 구독에서 *spotpoolpreview* 기능 플래그를 사용하도록 설정해야 합니다. 이 기능은 클러스터를 구성할 때 최신 서비스 향상 기능을 제공합니다.

> [!CAUTION]
> 구독에 기능을 등록할 때 해당 기능을 현재 등록 취소할 수 없습니다. 일부 미리 보기 기능을 활성화하면 구독에서 만든 모든 AKS 클러스터에 기본값을 사용할 수 있습니다. 프로덕션 구독에서 미리 보기 기능을 사용하도록 설정하지 마십시오. 별도의 구독을 사용하여 미리 보기 기능을 테스트하고 피드백을 수집합니다.

다음 예제와 같이 az [피쳐 레지스터][az-feature-register] 명령을 사용하여 *spotpoolpreview 피쳐* 플래그를 등록합니다.

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

스팟 노드 풀을 사용하는 AKS 클러스터를 만들려면 *aks 미리 보기* CLI 확장 버전 0.4.32 이상이 필요합니다. az 확장 [추가][az-extension-add] 명령을 사용하여 *aks 미리 보기* Azure CLI 확장을 설치한 다음 az [확장 업데이트][az-extension-update] 명령을 사용하여 사용 가능한 업데이트를 확인합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview
 
# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>제한 사항

스팟 노드 풀을 사용하여 AKS 클러스터를 만들고 관리할 때 다음과 같은 제한 사항이 적용됩니다.

* 스팟 노드 풀은 클러스터의 기본 노드 풀이 될 수 없습니다. 스팟 노드 풀은 보조 풀에만 사용할 수 있습니다.
* 스팟 노드 풀은 코돈 및 드레인을 보장할 수 없으므로 스팟 노드 풀을 업그레이드할 수 없습니다. 기존 스팟 노드 풀을 Kubernetes 버전 업그레이드와 같은 작업을 수행할 새 스팟 노드 풀로 바꿔야 합니다. 스팟 노드 풀을 대체하려면 다른 버전의 Kubernetes로 새 스팟 노드 풀을 만들고 상태가 *준비될*때까지 기다린 다음 이전 노드 풀을 제거합니다.
* 제어 평면과 노드 풀을 동시에 업그레이드할 수 없습니다. 제어 평면과 나머지 노드 풀을 동시에 업그레이드하려면 별도로 업그레이드하거나 스팟 노드 풀을 제거해야 합니다.
* 스팟 노드 풀은 가상 시스템 배율 집합을 사용해야 합니다.
* 생성 후 스케일셋우선순위 또는 스팟맥스프라이스를 변경할 수 없습니다.
* 스팟맥스프라이스를 설정할 때 값은 -1이어야 하며 소수점 이하 의 소수점 이하 최대 5자리가 있는 양수 값이어야 합니다.
* 스팟 노드 풀에는 *레이블 kubernetes.azure.com/scalesetpriority:spot,* 오염 *kubernetes.azure.com/scalesetpriority=spot:NoSchedule*및 시스템 포드에 안티 선호도가 있습니다.
* 스팟 노드 풀에서 워크로드를 예약하려면 [해당 내약감을][spot-toleration] 추가해야 합니다.

## <a name="add-a-spot-node-pool-to-an-aks-cluster"></a>AKS 클러스터에 스팟 노드 풀 추가

여러 노드 풀이 활성화된 기존 클러스터에 스팟 노드 풀을 추가해야 합니다. 여러 노드 풀이 있는 AKS 클러스터를 만드는 방법에 대한 자세한 내용은 [여기에서][use-multiple-node-pools]확인할 수 있습니다.

[az aks 노드풀 추가를][az-aks-nodepool-add]사용하여 노드 풀을 만듭니다.
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

기본적으로 여러 노드 풀이 있는 클러스터를 만들 때 AKS 클러스터에서 *일반* 우선 *순위를* 가진 노드 풀을 만듭니다. 위의 명령은 *스팟*을 *우선적으로* 사용하여 기존 AKS 클러스터에 보조 노드 풀을 추가합니다. *스팟의* *우선 순위는* 노드 풀을 스팟 노드 풀로 만듭니다. *제거 정책* 매개 변수는 위의 예제에서 *삭제로* 설정됩니다.이 기본값입니다. [제거 정책을][eviction-policy] *Delete로*설정하면 노드 풀의 기본 배율 집합에 있는 노드가 제거될 때 삭제됩니다. 제거 정책을 *할당 하기로*설정할 수도 있습니다. 제거 정책을 *Deallocate로*설정하면 기본 축척 집합의 노드가 제거 시 할당 중지된 상태로 설정됩니다. 할당 할당 이 중지된 상태의 노드는 계산 할당량에 대해 계산 할당량에 대해 계산되며 클러스터 확장 또는 업그레이드에 문제가 발생할 수 있습니다. *우선 순위* 및 *제거 정책* 값은 노드 풀을 만드는 동안에만 설정할 수 있습니다. 이러한 값은 나중에 업데이트할 수 없습니다.

또한 이 명령을 사용하면 [스팟][cluster-autoscaler]노드 풀에 사용하는 것이 좋습니다. 클러스터에서 실행되는 워크로드에 따라 클러스터 자동 크기 조정기는 노드 풀의 노드 수를 확장하고 축소합니다. 스팟 노드 풀의 경우 클러스터 자동 크기 조정기는 추가 노드가 여전히 필요한 경우 제거 후 노드 수를 확장합니다. 노드 풀에 있을 수 있는 최대 노드 수를 변경하는 경우 `maxCount` 클러스터 자동 크기 조정기와 연결된 값도 조정해야 합니다. 클러스터 자동 크기 조정기를 사용하지 않는 경우 제거 시 스팟 풀은 결국 0으로 감소하고 추가 스팟 노드를 수신하기 위해 수동 작업이 필요합니다.

> [!Important]
> 일괄 처리 작업 및 테스트 환경과 같은 중단을 처리할 수 있는 스팟 노드 풀에서만 워크로드를 예약합니다. 스팟 노드 풀에서 노드 제거를 처리할 수 있는 워크로드만 스팟 노드 풀에서 예약되도록 하기 위해 스팟 노드 풀에 [오염 및 내포를][taints-tolerations] 설정하는 것이 좋습니다. 예를 들어 위의 명령 ny 기본값은 *kubernetes.azure.com/scalesetpriority=spot:NoSchedule* 오염을 추가하므로 해당 내약이 있는 포드만 이 노드에서 예약됩니다.

## <a name="verify-the-spot-node-pool"></a>스팟 노드 풀 확인

노드 풀이 스팟 노드 풀로 추가되었는지 확인하려면 다음을 수행합니다.

```azurecli
az aks nodepool show --resource-group myResourceGroup --cluster-name myAKSCluster --name spotnodepool
```

*축척 확인우선 순위는* *스팟입니다.*

스팟 노드에서 실행되도록 포드를 예약하려면 스팟 노드에 적용된 오염에 해당하는 내포를 추가합니다. 다음 예제에서는 이전 단계에서 사용된 *kubernetes.azure.com/scalesetpriority=spot:NoSchedule* 오염에 해당하는 내포를 정의하는 yaml 파일의 일부를 보여 주습니다.

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

이 내포가 있는 포드가 배포되면 Kubernetes는 오염이 적용된 노드에서 포드를 성공적으로 예약할 수 있습니다.

## <a name="max-price-for-a-spot-pool"></a>스팟 풀의 최대 가격
스팟 인스턴스의 가격은 지역 및 SKU에 따라 [가변적입니다.][pricing-spot] 자세한 내용은 [Linux][pricing-linux] 및 [Windows][pricing-windows]가격 책정을 참조하십시오.

가변 가격을 사용하면 최대 소수점 이하 장소를 사용하여 최대 가격(USD)으로 설정할 수 있습니다. 예를 들어 *값 0.98765는* 시간당 0.98765 USD의 최대 가격입니다. 최대 가격을 *-1로*설정하면 가격에 따라 인스턴스가 제거되지 않습니다. 인스턴스의 가격은 현재 스팟 가격 또는 표준 인스턴스의 가격 중 더 적은 가격이며, 사용 가능한 용량과 할당량이 있는 한 더 적습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 AKS 클러스터에 스팟 노드 풀을 추가하는 방법을 배웠습니다. 노드 풀에서 포드를 제어하는 방법에 대한 자세한 내용은 [AKS의 고급 스케줄러 기능에 대한 모범 사례를][operator-best-practices-advanced-scheduler]참조하십시오.

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