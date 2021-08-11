---
title: 스케줄러 기능에 대한 모범 사례
titleSuffix: Azure Kubernetes Service
description: AKS(Azure Kubernetes Service)의 taint/toleration, 노드 선택기 및 선호도나 노드 간 선호도 및 선호도 방지와 같은 고급 스케줄러 기능 사용에 대한 클러스터 운영자 모범 사례 알아보기
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 971916c3fc903ff5d69db2e0f82fd884acf807b3
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831585"
---
# <a name="best-practices-for-advanced-scheduler-features-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)의 고급 스케줄러 기능 모범 사례

AKS(Azure Kubernetes Service)에서 클러스터를 관리할 경우 종종 팀 및 워크로드를 격리해야 합니다. Kubernetes 스케줄러가 제공하는 고급 기능을 통해 다음을 제어할 수 있습니다.
* 특정 노드에서 예약할 수 있는 Pod
* 다중 Pod 애플리케이션을 클러스터 전체에 적절하게 배포하는 방법 

이 모범 사례 문서에서는 클러스터 운영자의 고급 Kubernetes 예약 기능을 중점적으로 설명합니다. 이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * taint 및 toleration을 사용하여 노드에서 예약할 수 있는 Pod를 제한
> * 노드 선택기 또는 노드 선호도를 사용하여 특정 노드에서 실행할 Pod에 우선권 부여
> * Pod 간 선호도 또는 선호도 방지를 사용하여 Pod 분리 또는 그룹화

## <a name="provide-dedicated-nodes-using-taints-and-tolerations"></a>taint 및 toleration을 사용하여 전용 노드 제공

> **모범 사례 지침:** 
>
> 수신 컨트롤러와 같은 리소스 집약적 애플리케이션의 액세스를 특정 노드로 제한합니다. 노드 리소스가 필요한 워크로드에 노드 리소스를 계속 사용하도록 하고, 노드에서 다른 워크로드의 예약을 허용하지 않습니다.

AKS 클러스터를 만들 경우 GPU 지원 또는 많은 강력한 CPU를 사용하여 노드를 배포할 수 있습니다. 이러한 노드는 ML(기계 학습) 또는 AI(인공 지능)와 같은 대규모 데이터 처리 워크로드에 사용할 수 있습니다. 

이러한 노드 리소스 하드웨어는 일반적으로 배포하는 데 비용이 많이 들기 때문에 이러한 노드에서 예약할 수 있는 워크로드를 제한하세요. 대신 클러스터의 일부 노드를 수신 서비스 실행에만 사용하고 다른 워크로드는 차단하는 것이 좋습니다.

여러 노드 풀을 사용하여 다양한 노드에 대한 이러한 지원을 제공합니다. AKS 클러스터는 하나 이상의 노드 풀을 제공합니다.

Kubernetes 스케줄러는 taint 및 toleration을 사용하여 노드에서 실행할 수 있는 워크로드를 제한합니다.

* 노드에 **taint** 를 적용하여 특정 포드만 예약할 수 있음을 나타냅니다.
* 그런 다음, Pod에 **toleration** 을 적용하여 노드의 taint를 허용(*tolerate*)하도록 합니다.

Pod를 AKS 클러스터에 배포하는 경우 Kubernetes는 taint가 toleration과 맞춰 조정되는 노드에서만 Pod를 예약합니다. 예를 들어, GPU 지원이 있는 노드의 AKS 클러스터에 노드 풀을 추가했다고 가정합니다. 이름(예: *gpu*)을 정의한 후 예약의 값을 정의합니다. 이 값을 *NoSchedule* 로 설정하면 Kubernetes 스케줄러가 노드에서 정의되지 않은 toleration을 사용하여 Pod를 예약할 수 없습니다.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name taintnp \
    --node-taints sku=gpu:NoSchedule \
    --no-wait
```

taint가 노드 풀의 노드에 적용되면 노드에 대한 예약을 허용하는 Pod 사양에 toleration을 정의합니다. 다음 예제는 `sku: gpu` 및 `effect: NoSchedule`을 정의하여 이전 단계에서 노드 풀에 적용된 taint를 허용합니다.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

`kubectl apply -f gpu-toleration.yaml`을 사용하여 이 Pod가 배포되면 Kubernetes는 taint가 적용된 노드에서 Pod를 예약할 수 있습니다. 이 논리적 격리를 사용하면 클러스터 내의 리소스에 대한 액세스를 제어할 수 있습니다.

Taint를 적용할 경우 애플리케이션 개발자 및 소유자와 협력하여 해당 배포에서 필요한 toleration을 정의하도록 할 수 있습니다.

AKS에서 여러 노드 풀을 사용하는 방법에 대한 자세한 내용은 [AKS에서 클러스터에 대한 여러 노드 풀 만들기 및 관리][use-multiple-node-pools]를 참조하세요.

### <a name="behavior-of-taints-and-tolerations-in-aks"></a>AKS의 taint 및 toleration 동작

AKS에서 노드 풀을 업그레이드하는 경우 taint 및 toleration은 새 노드에 적용될 때 설정된 패턴을 따릅니다.

#### <a name="default-clusters-that-use-vm-scale-sets"></a>VM 확장 집합을 사용하는 기본 클러스터
AKS API에서 [nodepool을 taint][taint-node-pool]하여 새로 스케일 아웃된 노드가 API 지정 노드 taint를 받도록 할 수 있습니다.

다음을 가정해 보겠습니다.
1. 노드가 두 개(*node1* 및 *node2*)인 클러스터로 시작합니다. 
1. 노드 풀을 업그레이드합니다.
1. 노드 두 개(*node3* 및 *node4*)가 추가로 생성됩니다. 
1. taint는 각각 전달됩니다.
1. 원래 ‘node1’ 및 ‘node2’가 삭제됩니다.

#### <a name="clusters-without-vm-scale-set-support"></a>VM 확장 집합이 지원되지 않는 클러스터

이번에는 다음과 같이 가정합니다.
1. 노드가 두 개(*node1* 및 *node2*)인 클러스터가 있습니다. 
1. 그런 다음, 노드 풀을 업그레이드합니다.
1. 추가 노드인 *node3* 이 생성됩니다.
1. *node1* 의 taint가 *node3* 에 적용됩니다.
1. *node1* 이 삭제됩니다.
1. 새 *node1* 이 생성되어 원래 *node1* 을 대체합니다.
1. *node2* taint가 새 *node1* 에 적용됩니다. 
1. *node2* 가 삭제됩니다.

기본적으로 *node1* 은 *node3* 이 되고, *node2* 는 새 *node1* 이 됩니다.

AKS에서 노드 풀을 스케일링하는 경우 taint 및 toleration은 의도적으로 전달되지 않습니다.

## <a name="control-pod-scheduling-using-node-selectors-and-affinity"></a>노드 선택기 및 선호도를 사용하여 Pod 예약 제어

> **모범 사례 지침** 
> 
> 노드 선택기, 노드 선호도 또는 Pod 간 선호도를 사용하여 노드에서 Pod 예약을 제어합니다. 이러한 설정을 통해 Kubernetes 스케줄러는 노드의 하드웨어 등을 기준으로 워크로드를 논리적으로 격리할 수 있습니다.

taint 및 toleration은 하드 컷오프를 통해 리소스를 논리적으로 격리합니다. Pod가 노드의 taint를 허용하지 않으면 노드에서 예약되지 않습니다.

또는 노드 선택기를 사용할 수 있습니다. 예를 들어 로컬로 연결된 SSD 스토리지 또는 대용량 메모리를 표시하기 위해 노드에 레이블을 지정한 후 Pod 사양에 노드 선택기를 정의합니다. Kubernetes는 일치하는 노드에서 해당 Pod를 예약합니다.

Toleration과 달리, 일치하는 노드 선택기가 없는 Pod는 레이블 지정된 노드에서 계속 예약할 수 있습니다. 이 동작을 사용하면 노드의 사용되지 않는 리소스를 이용할 수 있지만, 일치하는 노드 선택기를 정의하는 Pod에 우선 순위를 지정합니다.

대용량 메모리를 사용하는 노드의 예를 살펴보겠습니다. 이러한 노드는 대용량 메모리를 요청하는 포드의 우선 순위를 지정합니다. 리소스가 유휴 상태로 있지 않도록 하기 위해 다른 Pod의 실행을 허용할 수도 있습니다. 다음 예제 명령은 레이블이 *hardware=highmem* 인 노드 풀을 *myResourceGroup* 의 *myAKSCluster* 에 추가합니다. 해당 노드 풀의 모든 노드에는 이 레이블이 있습니다.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name labelnp \
    --node-count 1 \
    --labels hardware=highmem \
    --no-wait
```

Pod 사양은 `nodeSelector` 속성을 추가하여 노드에 설정된 레이블과 일치하는 노드 선택기를 정의합니다.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  nodeSelector:
      hardware: highmem
```

이러한 스케줄러 옵션을 사용할 경우 애플리케이션 개발자 및 소유자와 협력하여 Pod 사양을 올바르게 정의하도록 할 수 있습니다.

노드 선택기 사용에 대한 자세한 내용은 [노드에 Pod 할당][k8s-node-selector]을 참조하세요.

### <a name="node-affinity"></a>노드 선호도

노드 선택기는 지정된 노드에 Pod를 할당하기 위한 기본 솔루션입니다. *노드 선호도* 는 더 많은 유연성을 제공하여 포드를 노드와 매칭할 수 없을 경우 발생하는 상황을 정의할 수 있습니다. 다음과 같습니다. 
* Kubernetes 스케줄러가 Pod를 레이블이 지정된 호스트와 일치시키도록 ‘요구’할 수 있습니다. 또는,
* 또는 일치를 ‘선호’할 수 있지만 일치 항목이 없는 경우 다른 호스트에서 Pod을 예약할 수 있습니다.

다음 예제는 노드 선호도를 *requiredDuringSchedulingIgnoredDuringExecution* 으로 설정합니다. 이 선호도를 사용하려면 일치하는 레이블을 가진 노드를 사용하기 위한 Kubernetes 일정이 필요합니다. 사용 가능한 노드가 없는 경우 Pod는 예약이 계속될 때까지 기다려야 합니다. 다른 노드에서 Pod를 예약하도록 허용하려면 값을 ***preferred** DuringSchedulingIgnoreDuringExecution*으로 설정하면 됩니다.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: hardware
            operator: In
            values: highmem
```

설정의 *IgnoredDuringExecution* 부분은 노드 레이블이 변경될 경우 Pod가 노드에서 제거되면 안 된다는 것을 나타냅니다. Kubernetes 스케줄러는 노드에 이미 예약된 Pod가 아니라, 예약 중인 새 Pod에만 업데이트된 노드 레이블을 사용합니다.

자세한 내용은 [선호도 및 선호도 방지][k8s-affinity]를 참조하세요.

### <a name="inter-pod-affinity-and-anti-affinity"></a>Pod 간 선호도 및 선호도 방지

Kubernetes 스케줄러가 워크로드를 논리적으로 격리하는 한 가지 마지막 접근 방식은 Pod 간 선호도 또는 선호도 방지를 사용하는 것입니다. 이러한 설정은 일치하는 기존 Pod가 있는 노드에서 Pod가 예약되어서는 안 되거나 예약되어야 함을 정의합니다.  기본적으로 Kubernetes 스케줄러는 전체 노드의 복제본 세트에서 여러 Pod를 예약하려고 시도합니다. 이 동작에 관한 보다 구체적인 규칙을 정의할 수 있습니다.

예를 들어 Azure Cache for Redis를 사용하는 웹 애플리케이션이 있습니다. 
1. Pod 선호도 방지 규칙을 사용하여 Kubernetes 스케줄러가 전체 노드에 복제본을 배포하도록 요청합니다. 
1. 선호도 규칙을 사용하여 각 웹앱 구성 요소가 해당 캐시와 동일한 호스트에서 예약되도록 합니다. 

전체 노드에 대한 Pod 배포는 다음 예제와 같이 표시됩니다.

| **노드 1** | **노드 2** | **노드 3** |
|------------|------------|------------|
| webapp-1   | webapp-2   | webapp-3   |
| cache-1    | cache-2    | cache-3    |

Pod 간 선호도 및 선호도 방지는 노드 선택기 또는 노드 선호도보다 더 복잡한 배포를 제공합니다. 배포를 통해 리소스를 논리적으로 격리하고 Kubernetes가 노드에서 포드를 예약하는 방식을 제어합니다. 

Azure Cache for Redis 예제와 함께 이 웹 애플리케이션의 전체 예제를 보려면 [동일한 노드에서 Pod 공동 배치][k8s-pod-affinity]를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 고급 Kubernetes 스케줄러 기능을 중점적으로 설명했습니다. AKS의 클러스터 작업에 대한 자세한 내용은 다음 모범 사례를 참조하세요.

* [다중 테넌트 지원 및 클러스터 격리][aks-best-practices-scheduler]
* [기본 Kubernetes 스케줄러 기능][aks-best-practices-scheduler]
* [인증 및 권한 부여][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-taints-tolerations]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[k8s-node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[k8s-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity
[k8s-pod-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#always-co-located-in-the-same-node

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-identity]: operator-best-practices-identity.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[taint-node-pool]: use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool
