---
title: AKS (Azure Kubernetes Service)에서 사용 하 여 가용성 영역
description: Azure Kubernetes Service (AKS)에서 가용성 영역에서 노드를 배포 하는 클러스터를 만드는 방법 알아보기
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 06/24/2019
ms.author: iainfou
ms.openlocfilehash: 0f99386aa9eeb75a990507e383c32412fb39eceb
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840681"
---
# <a name="preview---create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>미리 보기-가용성 영역을 사용 하는 Azure Kubernetes Service (AKS) 클러스터 만들기

Azure Kubernetes Service (AKS) 클러스터는 노드와 논리적 부분 기본 Azure 저장소에 대 한 계산 인프라와 같은 리소스를 배포 합니다. 이 배포 모델은 단일 Azure 데이터 센터에서 별도 업데이트 및 장애 도메인 간에 노드를 실행 합니다. 이 기본 동작을 사용 하 여 배포 된 AKS 클러스터 하드웨어 오류 로부터 보호 하는 가용성 수준을 높이기 또는 유지 관리 이벤트를 계획 합니다.

더 높은 수준의 응용 프로그램 가용성을 제공 하려면 가용성 영역에서 AKS 클러스터를 배포할 수 있습니다. 이러한 영역은 특정된 지역 내에서 물리적으로 별도 데이터 센터에 설명 합니다. 클러스터 구성 요소는 여러 영역에 분산 하는 경우에 AKS 클러스터는 해당 영역 중 하나에 실패를 허용할 수 있습니다. 응용 프로그램 및 관리 작업 계속 하나의 전체 데이터 센터 문제가 발생 하는 경우에 사용할 수 있습니다.

이 문서에서는 AKS 클러스터를 만들고 가용성 영역에서 노드 구성 요소를 배포 하는 방법을 보여 줍니다. 이 기능은 현재 미리 보기로 제공됩니다.

> [!IMPORTANT]
> AKS 미리 보기 기능은 셀프 서비스, 옵트인 합니다. 커뮤니티에서 의견 및 버그를 수집 하도록 제공 됩니다. 미리 보기에서이 기능이 없는 프로덕션 사용 해야 합니다. 공개 미리 보기에서 기능 '최상의' 지원에 속합니다. AKS 기술 지원 팀의 지원 업무 시간은 태평양 표준 시간대 (PST)만 제공 됩니다. 추가 정보는 다음과 같은 지원 문서를 참조 하세요.
>
> * [AKS 지원 정책][aks-support-policies]
> * [Azure 지원 FAQ][aks-faq]

## <a name="before-you-begin"></a>시작하기 전 주의 사항

이상이 설치 및 구성 수 또는 Azure CLI 버전 2.0.66 필요 합니다.  `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드를 참조 해야 하는 경우 [Azure CLI 설치][install-azure-cli]합니다.

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 확장 설치

가용성 영역을 사용 하는 AKS 클러스터를 만들려면, 해야 합니다 *aks 미리 보기* CLI 확장 버전 0.4.1 이상. 설치를 *aks 미리 보기* 사용 하 여 Azure CLI 확장을 [az 확장 추가][az-extension-add] command, then check for any available updates using the [az extension update][az-extension-update] 명령:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-feature-flags-for-your-subscription"></a>구독에 대 한 등록 기능 플래그

AKS 클러스터는 가용성 영역을 만들려면 먼저 구독에 일부 기능 플래그를 설정 합니다. 클러스터 배포 및 Kubernetes 노드 구성을 관리 하려면 가상 머신 확장을 사용 합니다. 합니다 *표준* 클러스터에 트래픽 라우팅하는 데 네트워크 구성 요소에 대 한 복원 력을 제공 하려면 Azure load balancer의 SKU가 필요 합니다. 등록 합니다 *AvailabilityZonePreview*, *AKSAzureStandardLoadBalancer*, 및 *VMSSPreview* 기능 플래그를 사용 하 여는 [az기능등록][az-feature-register] 다음 예와에서 같이 명령:

> [!CAUTION]
> 구독에서 기능을 등록 하면 수 없는 현재 등록을 취소 기능. 일부 미리 보기 기능을 사용 하도록 설정 하면 구독에서 만든 모든 AKS 클러스터에 대 한 기본값을 사용할 수 있습니다. 프로덕션 구독에서 미리 보기 기능을 사용 하지 마십시오. 별도 구독을 사용 하 여 미리 보기 기능을 테스트 및 피드백을 수집 합니다.

```azurecli-interactive
az feature register --name AvailabilityZonePreview --namespace Microsoft.ContainerService
az feature register --name AKSAzureStandardLoadBalancer --namespace Microsoft.ContainerService
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

상태가 *Registered*로 표시되는 데 몇 분 정도 걸립니다. 사용 하 여 등록 상태를 확인할 수 있습니다 합니다 [az 기능 목록][az-feature-list] 명령:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AvailabilityZonePreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSAzureStandardLoadBalancer')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

준비가 되 면 새로 고침의 등록 합니다 *Microsoft.ContainerService* 사용 하 여 리소스 공급자를 [az provider register][az-provider-register] 명령:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations-and-region-availability"></a>제한 사항 및 지역 가용성

현재 다음 지역에서 가용성 영역을 사용 하 여 AKS 클러스터를 만들 수 있습니다.

* 미국 동부 2
* 북유럽
* 동남아시아
* 서유럽
* 미국 서부 2

가용성 영역을 사용 하 여 AKS 클러스터를 만들 때 다음과 같은 제한이 있습니다.

* 클러스터를 만들 때에 가용성 영역을 사용할 수 있습니다.
* 클러스터를 만든 후에 가용성 영역 설정은 업데이트할 수 없습니다. 또한 가용성 영역을 사용 하기 위해 기존, 비 가용성 영역 클러스터를 업데이트할 수 없습니다.
* 생성 된 후에 AKS 클러스터에 대 한 가용성 영역을 비활성화할 수 없습니다.
* 모든 가용성 영역에서 선택한 노드 크기 (VM SKU)를 사용할 수 있어야 합니다.
* 클러스터 가용성 영역을 사용 하도록 설정 해야 하는 데 Azure 표준 Load Balancer의 배포 영역에서 합니다.
* Kubernetes 버전 1.13.5 사용 해야 표준 Load Balancer를 배포 하기 위해 이상.

가용성 영역을 사용 하는 AKS 클러스터에서 Azure load balancer를 사용 해야 합니다 *표준* SKU입니다. 기본값 *기본* Azure load balancer의 SKU 가용성 영역에서 배포를 지원 하지 않습니다. 자세한 내용 및 표준의 제한 사항에는 부하 분산 장치를 참조 하세요 [Azure load balancer 표준 SKU 미리 보기 제한 사항][standard-lb-limitations]합니다.

### <a name="azure-disks-limitations"></a>Azure 디스크 제한 사항

Azure를 사용 하는 볼륨 관리 디스크가 없는 현재 영역 리소스입니다. 원래 시간대에서 다른 영역에 다시 예약 하는 pod가 이전 디스크 다시 연결할 수 없습니다. 것이 좋습니다 영역에서 발생할 수 있는 영구 저장소에 발급 되지 않아도 되는 상태 비저장 워크 로드를 실행 합니다.

상태 저장 워크 로드를 실행 해야 하는 경우 taints 및 사용 tolerations에 pod 사양에서 pod에 디스크와 동일한 영역에 만들려는 Kubernetes 스케줄러를 합니다. 또는 영역 간에 예약 하는 대로 pod에 연결할 수 있는 Azure Files와 같은 네트워크 기반 저장소를 사용 합니다.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>AKS에 대 한 가용성 영역 개요 클러스터

가용성 영역은 데이터 센터 오류에서 애플리케이션 및 데이터를 보호하는 고가용성 기능입니다. 영역은 Azure 지역 내의 고유한 물리적 위치입니다. 각 영역은 독립된 전원, 냉각 및 네트워킹을 갖춘 하나 이상의 데이터 센터로 구성됩니다. 복원력을 보장하려면 활성화된 모든 지역에서 최소한 세 개의 별도 영역이 필요합니다. 지역 내에서 가용성 영역의 물리적 구분은 애플리케이션 및 데이터를 데이터 센터 오류로부터 보호할 수 있습니다. 영역 중복 서비스는 단일 지점 오류에서 보호하기 위해 가용성 영역에서 애플리케이션 및 데이터를 복제합니다.

자세한 내용은 [Azure에서 가용성 영역은 무엇입니까?][az-overview]합니다.

단일 지역 내 여러 영역에서 가용성 영역을 사용 하 여 배포 된 AKS 클러스터 노드를 배포할 수 있습니다. 예를 들어 클러스터에는 *미국 동부 2* 지역에서 모든 세 가용성 영역에서 노드를 만들 수 있습니다 *미국 동부 2*합니다. AKS 클러스터 리소스의이 배포판 특정 영역 실패에 탄력적 이므로 클러스터 가용성을 개선 합니다.

![가용성 영역에 걸쳐 AKS 노드 분포](media/availability-zones/aks-availability-zones.png)

영역 중단, 노드 수에 부하가 다시 분산 수동으로 또는 클러스터 autoscaler를 사용 합니다. 단일 영역을 사용할 수 없는 응용 프로그램 계속 실행 합니다.

## <a name="create-an-aks-cluster-across-availability-zones"></a>가용성 영역에서 AKS 클러스터 만들기

사용 하 여 클러스터를 만들 때 합니다 [az aks 만들기][az-aks-create] 명령인은 `--node-zones` 매개 변수 정의에 영역 에이전트 노드에 배포 됩니다. 클러스터의 AKS 제어 평면 구성 요소 또한에 분산 되어 사용 가능한 가장 높은 구성에 대 한 영역 지정 하는 클러스터를 만들 때의 `--node-zones` 매개 변수입니다.

AKS 클러스터를 만들 때 기본 에이전트 풀에 대 한 모든 영역을 정의 하지 않으면, 클러스터의 AKS 제어 평면 구성 요소 가용성 영역을 사용 하지 않습니다. 하지만 (현재 AKS에서 미리 보기)는에서 추가 노드 풀을 추가할 수 있습니다 사용 하는 [az aks nodepool 추가][az-aks-nodepool-add] 명령 및 지정 `--node-zones` 새 에이전트 노드를 제어 평면 구성 요소 상태를 유지 없이 가용성 영역 인식 합니다. 표준 시간대 인식 변경할 수 없습니다는 배포한 노드 풀 또는 AKS 제어 평면 구성 요소에 대 한 합니다.

다음 예제에서는 명명 된 AKS 클러스터를 만듭니다 *myAKSCluster* 이라는 리소스 그룹에서 *myResourceGroup*합니다. 총 *3* 노드가 만들어집니다-영역에서 에이전트를 하나 *1*하나씩에서 *2*, 및에서 하나 *3*합니다. AKS 제어 평면 구성 요소는 클러스터에 속하는 프로세스를 만들 때 정의 되므로도 사용할 수 있는 가장 높은 구성에서 영역 간에 분산 됩니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version 1.13.5 \
    --generate-ssh-keys \
    --enable-vmss \
    --load-balancer-sku standard \
    --node-count 3 \
    --node-zones 1 2 3
```

AKS 클러스터를 만드는 데 몇 분이 걸립니다.

## <a name="verify-node-distribution-across-zones"></a>영역에 걸쳐 노드 분포를 확인 하십시오.

클러스터 준비 되 면 어떤 가용성 영역에서 배포 되는 참조 하도록 규모 집합의 에이전트 노드를 나열 합니다.

먼저, 사용 하 여 AKS 클러스터 자격 증명을 가져옵니다 합니다 [az aks 자격 증명 가져오기][az-aks-get-credentials] 명령:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

다음을 사용 하 여 합니다 [kubectl 설명][kubectl-describe] 클러스터의 노드를 나열 하는 명령입니다. 필터링 된 *failure-domain.beta.kubernetes.io/zone* 다음 예제에서와 같이 값:

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

다음 예제 출력과 같은 지정 된 지역 및 가용성 영역에서 배포 노드 세 개를 보여 줍니다 *eastus2-1* 첫 번째 가용성 영역에 대 한 및 *eastus2 2* 두 번째 가용성 영역:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

에이전트 풀에 추가 노드를 추가 하면 Azure 플랫폼 기본 Vm 지정 된 가용성 영역에서 자동으로 배포 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 가용성 영역을 사용 하는 AKS 클러스터를 만드는 방법을 자세히 설명 합니다. 항상 사용 가능한 클러스터의 추가 고려 사항은 참조 하세요 [AKS에 비즈니스 연속성 및 재해 복구에 대 한 유용한][best-practices-bc-dr]합니다.

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
