---
title: Azure Kubernetes 서비스(AKS)에서 가용성 영역 사용
description: AKS(Azure Kubernetes Service)의 가용성 영역에 노드를 분산하는 클러스터를 만드는 방법에 대해 알아봅니다.
services: container-service
ms.custom: fasttrack-edit
ms.topic: article
ms.date: 06/24/2019
ms.openlocfilehash: 5693d9e90de9ba68e7b76e0f2bd5b75141dbda71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596813"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>가용성 영역을 사용하는 AKS(Azure Kubernetes) 클러스터 만들기

AKS(Azure Kubernetes Service) 클러스터는 기본 Azure 계산 인프라의 논리 섹션에 노드 및 저장소와 같은 리소스를 배포합니다. 이 배포 모델은 노드가 단일 Azure 데이터 센터에서 별도의 업데이트 및 장애 도메인에서 실행되는지 확인합니다. 이 기본 동작으로 배포된 AKS 클러스터는 하드웨어 오류 또는 계획된 유지 관리 이벤트로부터 보호하기 위해 높은 수준의 가용성을 제공합니다.

응용 프로그램에 더 높은 수준의 가용성을 제공하기 위해 AKS 클러스터를 가용성 영역에 분산할 수 있습니다. 이러한 영역은 지정된 영역 내에서 물리적으로 분리된 데이터 센터입니다. 클러스터 구성 요소가 여러 영역에 분산되면 AKS 클러스터는 이러한 영역 중 하나에서 오류를 견딜 수 있습니다. 하나의 전체 데이터 센터에 문제가 있더라도 응용 프로그램 및 관리 작업을 계속 사용할 수 있습니다.

이 문서에서는 AKS 클러스터를 만들고 가용성 영역에 노드 구성 요소를 배포하는 방법을 보여 주며 이 문서에서는

## <a name="before-you-begin"></a>시작하기 전에

Azure CLI 버전 2.0.76 이상 설치 및 구성이 필요합니다.  `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우  [Azure CLI 설치][install-azure-cli]를 참조하세요.

## <a name="limitations-and-region-availability"></a>제한 사항 및 지역 가용성

AKS 클러스터는 현재 다음 리전의 가용성 영역을 사용하여 만들 수 있습니다.

* 미국 중부
* 미국 동부 2
* 미국 동부
* 프랑스 중부
* 일본 동부
* 북유럽
* 동남아시아
* 영국 남부
* 서유럽
* 미국 서부 2

가용성 영역을 사용하여 AKS 클러스터를 만들 때 다음과 같은 제한 사항이 적용됩니다.

* 클러스터를 만들 때만 가용성 영역을 활성화할 수 있습니다.
* 클러스터를 만든 후에는 가용성 영역 설정을 업데이트할 수 없습니다. 또한 가용성 영역을 사용하도록 기존 비가용성 영역 클러스터를 업데이트할 수 없습니다.
* AKS 클러스터가 생성된 후에는 가용성 영역을 비활성화할 수 없습니다.
* 선택한 노드 크기(VM SKU)는 모든 가용성 영역에서 사용할 수 있어야 합니다.
* 가용성 영역이 활성화된 클러스터는 영역 간에 배포하기 위해 Azure 표준 로드 밸런서를 사용해야 합니다.
* 표준 로드 밸워서를 배포하려면 Kubernetes 버전 1.13.5 이상을 사용해야 합니다.

가용성 영역을 사용하는 AKS 클러스터는 로드 밸런서 유형의 기본값인 Azure 로드 밸런서 *표준* SKU를 사용해야 합니다. 이 로드 밸런서 유형은 클러스터 생성 시에만 정의할 수 있습니다. 자세한 정보 및 표준 로드 밸러블러의 제한 사항은 [Azure 로드 밸러블러 표준 SKU 제한을][standard-lb-limitations]참조하십시오.

### <a name="azure-disks-limitations"></a>Azure 디스크 제한 사항

Azure 관리 디스크를 사용하는 볼륨은 현재 구역 리소스가 아닙니다. 원래 영역과 다른 영역에서 다시 예약된 포드는 이전 디스크를 다시 연결할 수 없습니다. 구역 문제에서 발생할 수 있는 영구 저장소가 필요하지 않은 상태 비저장 워크로드를 실행하는 것이 좋습니다.

상태 저장 워크로드를 실행해야 하는 경우 포드 사양의 오염 및 내포를 사용하여 Kubernetes 스케줄러에 디스크와 동일한 영역에 포드를 만들라고 지시합니다. 또는 영역 간에 예약된 대로 포드에 연결할 수 있는 Azure Files와 같은 네트워크 기반 저장소를 사용합니다.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>AKS 클러스터의 가용성 영역 개요

가용성 영역은 데이터 센터 오류로부터 응용 프로그램과 데이터를 보호하는 고가용성 제품입니다. 영역은 Azure 지역 내의 고유한 물리적 위치입니다. 각 영역은 독립된 전원, 냉각 및 네트워킹을 갖춘 하나 이상의 데이터 센터로 구성됩니다. 복원력을 보장하려면 활성화된 모든 지역에서 최소한 세 개의 별도 영역이 필요합니다. 한 지역 내에서 가용성 영역을 물리적으로 구분하면 애플리케이션 및 데이터를 데이터 센터 오류로부터 보호할 수 있습니다. 영역 중복 서비스는 단일 오류 지점으로부터 보호하기 위해 가용성 영역 간에 응용 프로그램과 데이터를 복제합니다.

자세한 내용은 [Azure의 가용성 영역을 참조하세요.][az-overview]

가용성 영역을 사용하여 배포되는 AKS 클러스터는 단일 리전 내의 여러 영역에 노드를 배포할 수 있습니다. 예를 들어 *미국 동부 2* 리전의 클러스터는 *미국 동부 2의*세 가용성 영역 모두에 노드를 만들 수 있습니다. 이러한 AKS 클러스터 리소스 배포는 특정 영역의 실패에 탄력적일 때 클러스터 가용성을 향상시킵니다.

![가용성 영역 전반에 걸친 AKS 노드 분포](media/availability-zones/aks-availability-zones.png)

영역 중단에서 노드를 수동으로 재조정하거나 클러스터 자동 크기 조정기에서 다시 조정할 수 있습니다. 단일 영역을 사용할 수 없게 되면 응용 프로그램이 계속 실행됩니다.

## <a name="create-an-aks-cluster-across-availability-zones"></a>가용성 영역 간에 AKS 클러스터 만들기

[az aks create][az-aks-create] 명령을 사용하여 클러스터를 `--zones` 만들 때 매개 변수는 에이전트 노드가 배포되는 영역을 정의합니다. 클러스터에 대한 AKS 제어 평면 구성 요소도 클러스터 생성 시 `--zones` 매개 변수를 정의할 때 사용 가능한 가장 높은 구성의 영역에 분산됩니다.

AKS 클러스터를 만들 때 기본 에이전트 풀에 대한 영역을 정의하지 않으면 클러스터의 AKS 제어 평면 구성 요소는 가용성 영역을 사용하지 않습니다. [az aks nodepool을][az-aks-nodepool-add] 사용하여 추가 노드 풀을 `--zones` 추가하고 해당 새 노드에 대해 지정할 수 있지만 제어 평면 구성 요소는 가용성 영역 인식 없이 유지됩니다. 노드 풀 또는 AKS 제어 평면 구성 요소가 배포되면 영역 인식을 변경할 수 없습니다.

다음 예제에서는 *myResourceGroup이라는*리소스 그룹에 *myAKSCluster라는* AKS 클러스터를 만듭니다. 영역 *1의*에이전트 1개, *2개중*1개, *3개*중 1개 등 총 *3개의* 노드가 만들어집니다. AKS 제어 평면 구성 요소는 클러스터 생성 프로세스의 일부로 정의되므로 사용 가능한 가장 높은 구성의 영역에 분산됩니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --generate-ssh-keys \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --node-count 3 \
    --zones 1 2 3
```

AKS 클러스터를 만드는 데 몇 분이 걸립니다.

## <a name="verify-node-distribution-across-zones"></a>영역 간 노드 분포 확인

클러스터가 준비되면 확장 집합에 에이전트 노드를 나열하여 배포할 가용성 영역을 확인합니다.

먼저 [az aks get-credentials][az-aks-get-credentials] 명령을 사용하여 AKS 클러스터 자격 증명을 가져옵니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

다음으로 [kubectl 설명][kubectl-describe] 명령을 사용하여 클러스터의 노드를 나열합니다. 다음 예제와 같이 *failure-domain.beta.kubernetes.io/zone* 값을 필터링합니다.

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

다음 예제 출력은 첫 번째 가용성 영역에 대한 *eastus2-1* 및 두 번째 가용성 영역에 대한 *eastus2-2와* 같이 지정된 영역 및 가용성 영역에 분산된 세 개의 노드를 보여 주며, 두 번째 가용성 영역에 대해 다음과 같이 표시됩니다.

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

에이전트 풀에 노드를 추가하면 Azure 플랫폼은 지정된 가용성 영역에 기본 VM을 자동으로 배포합니다.

최신 Kubernetes 버전(1.17.0 이상)에서 AKS는 더 이상 사용되지 `topology.kubernetes.io/zone` 않는 추가 레이블 외에 `failure-domain.beta.kubernetes.io/zone`최신 레이블을 사용하고 있습니다.

## <a name="verify-pod-distribution-across-zones"></a>영역 간 포드 분포 확인

[잘 알려진 레이블, 주석 및 Taints에][kubectl-well_known_labels]설명된 대로 Kubernetes는 레이블을 `failure-domain.beta.kubernetes.io/zone` 사용하여 사용 가능한 여러 영역에 걸쳐 복제 컨트롤러 또는 서비스에서 포드를 자동으로 배포합니다. 이를 테스트하기 위해 클러스터를 3개에서 5개 노드로 확장하여 올바른 포드 확산을 확인할 수 있습니다.

```azurecli-interactive
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5
```

몇 분 후에 배율 조정 작업이 `kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"` 완료되면 명령은 이 샘플과 유사한 출력을 제공해야 합니다.

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
Name:       aks-nodepool1-28993262-vmss000003
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000004
            failure-domain.beta.kubernetes.io/zone=eastus2-2
```

보시다시피 이제 영역 1과 2에 두 개의 노드가 추가되었습니다. 세 개의 복제본으로 구성된 응용 프로그램을 배포할 수 있습니다. NGINX를 예로 들겠습니다.

```console
kubectl run nginx --image=nginx --replicas=3
```

포드가 실행되는 노드를 확인하면 세 가지 가용성 영역에 해당하는 포드에서 포드가 실행되고 있음을 알 수 있습니다. 예를 들어 명령을 `kubectl describe pod | grep -e "^Name:" -e "^Node:"` 사용하면 다음과 유사한 출력을 얻을 수 있습니다.

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

이전 출력에서 볼 수 있듯이 첫 번째 포드는 가용성 영역에 `eastus2-1`있는 노드 0에서 실행됩니다. 두 번째 포드는 에 해당하는 노드 `eastus2-3`2에서 실행되고 세 번째 `eastus2-2`포드는 노드 4에서 에 해당합니다. 추가 구성 없이 Kubernetes는 세 가지 가용성 영역에 걸쳐 포드를 올바르게 확산하고 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 가용성 영역을 사용하는 AKS 클러스터를 만드는 방법을 자세히 설명합니다. 가용성이 높은 클러스터에 대한 자세한 고려 사항은 [AKS의 비즈니스 연속성 및 재해 복구에 대한 모범 사례를][best-practices-bc-dr]참조하십시오.

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-overview]: ../availability-zones/az-overview.md
[best-practices-bc-dr]: operator-best-practices-multi-region.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[standard-lb-limitations]: load-balancer-standard.md#limitations
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-well_known_labels]: https://kubernetes.io/docs/reference/kubernetes-api/labels-annotations-taints/
