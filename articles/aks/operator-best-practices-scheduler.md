---
title: 운영자 모범 사례 - AKS(Azure Kubernetes Service)의 기본 스케줄러 기능
description: AKS(Azure Kubernetes Service)의 리소스 할당량 및 Pod 중단 예산과 같은 기본 스케줄러 기능 사용에 대한 클러스터 운영자 모범 사례 알아보기
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 034b7e18d3114804f846d77aa4feb001492c8883
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110467067"
---
# <a name="best-practices-for-basic-scheduler-features-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)의 기본 스케줄러 기능 모범 사례

AKS(Azure Kubernetes Service)에서 클러스터를 관리할 경우 종종 팀 및 워크로드를 격리해야 합니다. Kubernetes 스케줄러는 컴퓨팅 리소스의 배포를 제어하거나 유지 관리 이벤트의 영향을 제한할 수 있는 기능을 제공합니다.

이 모범 사례 문서에서는 클러스터 운영자의 기본 Kubernetes 예약 기능을 중점적으로 설명합니다. 이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 리소스 할당량을 사용하여 팀 또는 워크로드에 고정된 리소스 양 제공
> * Pod 중단 예산을 사용하여 예약된 유지 관리의 영향 제한

## <a name="enforce-resource-quotas"></a>리소스 할당량 적용

> **모범 사례 지침** 
> 
> 네임스페이스 수준에서 리소스 할당량을 계획하고 적용합니다. Pod가 리소스 요청 및 한도를 정의하지 않는 경우 배포를 거부합니다. 리소스 사용량을 모니터링하고 필요에 따라 할당량을 조정합니다.

리소스 요청 및 한도는 Pod 사양에 배치됩니다. 한도는 배포 시 Kubernetes 스케줄러에서 클러스터의 사용 가능한 노드를 찾기 위해 사용됩니다. 한도 및 요청은 개별 Pod 수준에서 작동합니다. 이러한 값을 정의하는 방법에 대한 자세한 내용은 [Pod 리소스 요청 및 한도 정의][resource-limits]를 참조하세요.

개발 팀 또는 프로젝트에서 리소스를 예약하고 제한하는 방법을 제공하려면 ‘리소스 할당량’을 사용해야 합니다. 이러한 할당량은 네임스페이스에 정의되며 다음 기준으로 할당량을 설정하는 데 사용할 수 있습니다.

* CPU 및 메모리 또는 GPU와 같은 **컴퓨팅 리소스**.
* 지정된 스토리지 클래스에 대한 총 볼륨 수 또는 디스크 공간의 크기를 포함하는 **스토리지 리소스** 입니다.
* 최대 비밀, 서비스 또는 작업 수와 같은 **개체 개수** 를 만들 수 있습니다.

Kubernetes는 리소스를 초과 커밋하지 않습니다. 누적 리소스 요청 합계가 할당된 할당량을 통과하면 모든 추가 배포에 실패합니다.

리소스 할당량을 정의하는 경우 네임스페이스에 만들어진 모든 Pod는 해당 Pod 사양의 한도 또는 요청을 제공해야 합니다. 이러한 값이 제공되지 않으면 배포를 거부할 수 있습니다. 대신 [네임스페이스의 기본 요청 및 한도를 구성][configure-default-quotas]할 수 있습니다.

다음 예제에서는 *dev-app-team-quotas.yaml* 이라는 YAML 매니페스트가 엄격한 한도로 총 CPU *10* 개, 메모리 *20Gi* 및 Pod *10* 개를 설정합니다.

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: dev-app-team
spec:
  hard:
    cpu: "10"
    memory: 20Gi
    pods: "10"
```

*dev-apps* 와 같은 네임스페이스를 지정하여 이 리소스 할당량을 적용할 수 있습니다.

```console
kubectl apply -f dev-app-team-quotas.yaml --namespace dev-apps
```

애플리케이션 개발자 및 소유자와 협력하여 요구 사항을 이해하고 적절한 리소스 할당량을 적용하세요.

사용 가능한 리소스 개체, 범위 및 우선 순위에 대한 자세한 내용은 [Kubernetes의 리소스 할당량][k8s-resource-quotas]을 참조하세요.

## <a name="plan-for-availability-using-pod-disruption-budgets"></a>Pod 중단 예산을 사용하여 가용성 계획

> **모범 사례 지침** 
>
> 애플리케이션의 가용성을 유지하려면 PDB(Pod 중단 예산)를 정의하여 클러스터에서 최소 수의 Pod를 사용할 수 있는지 확인합니다.

Pod를 제거하는 두 개의 중단 이벤트가 있습니다.

### <a name="involuntary-disruptions"></a>비자발적 중단

‘비자발적 중단’은 클러스터 운영자 또는 애플리케이션 소유자의 일반적인 제어를 벗어나는 이벤트입니다. 포함:
* 물리적 컴퓨터의 하드웨어 오류
* 커널 패닉
* 노드 VM 삭제

비자발적 중단은 다음과 같은 방법으로 완화할 수 있습니다.
* 배포에서 여러 Pod 복제본을 사용하여 완화할 수 있습니다. 
* 또한 AKS 클러스터에서 여러 노드를 실행하여 완화할 수 있습니다. 

### <a name="voluntary-disruptions"></a>자발적 중단

‘자발적 중단’은 클러스터 운영자 또는 애플리케이션 소유자가 요청한 이벤트입니다. 포함:
* 클러스터 업그레이드
* 업데이트된 배포 템플릿
* Pod를 실수로 삭제

Kubernetes는 자발적 중단에 대한 *PDB(Pod 중단 예산)* 를 제공하므로 자발적 중단 이벤트가 발생하는 경우 배포 또는 복제본 집합의 대응 방법을 계획할 수 있습니다. PDB(Pod 중단 예산)를 사용하여 클러스터 운영자는 사용 가능한 최소 또는 사용 불가능한 최대 리소스 수를 정의할 수 있습니다. 

클러스터를 업그레이드하거나 배포 템플릿을 업데이트하는 경우 Kubernetes 스케줄러는 자발적 중단 이벤트를 계속하는 것을 허용하기 전에 다른 노드의 추가 Pod 일정을 예약합니다. 스케줄러는 정의된 Pod 수가 클러스터의 다른 노드에서 성공적으로 예약될 때까지 노드를 다시 부팅하기 위해 대기합니다.

NGINX를 실행하는 5개의 Pod가 있는 복제본 세트의 예를 살펴보겠습니다. 복제본 세트의 Pod에는 레이블 `app: nginx-frontend`가 할당되었습니다. 자발적 중단 이벤트(예: 클러스터 업그레이드) 중에 최소 3개의 Pod가 계속 실행되게 하려고 합니다. *PodDisruptionBudget* 개체의 다음 YAML 매니페스트는 다음 요구 사항을 정의합니다.

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   minAvailable: 3
   selector:
    matchLabels:
      app: nginx-frontend
```

또한 복제본 세트의 Pod 수 강화를 자동으로 보완할 수 있는 백분율(예: *60%*)을 정의할 수도 있습니다.

복제본 세트에서 사용 불가능한 인스턴스의 최대 수를 정의할 수 있습니다. 또한 최대 사용 불가능한 Pod의 백분율을 정의할 수 있습니다. 다음 Pod 중단 예산 YAML 매니페스트는 복제본 세트에서 Pod를 2개 이하로 사용하도록 정의합니다.

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   maxUnavailable: 2
   selector:
    matchLabels:
      app: nginx-frontend
```

Pod 중단 예산이 정의되면 다른 Kubernetes 개체와 같이 AKS 클러스터에서 이를 만듭니다.

```console
kubectl apply -f nginx-pdb.yaml
```

애플리케이션 개발자 및 소유자와 협력하여 요구 사항을 이해하고 적절한 Pod 중단 예산을 적용하세요.

Pod 중단 예산 사용에 대한 자세한 내용은 [애플리케이션의 중단 예산 지정][k8s-pdbs]을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 기본 Kubernetes 스케줄러 기능을 중점적으로 설명했습니다. AKS의 클러스터 작업에 대한 자세한 내용은 다음 모범 사례를 참조하세요.

* [다중 테넌트 지원 및 클러스터 격리][aks-best-practices-cluster-isolation]
* [고급 Kubernetes 스케줄러 기능][aks-best-practices-advanced-scheduler]
* [인증 및 권한 부여][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-resource-quotas]: https://kubernetes.io/docs/concepts/policy/resource-quotas/
[configure-default-quotas]: https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/
[k8s-pdbs]: https://kubernetes.io/docs/tasks/run-application/configure-pdb/

<!-- INTERNAL LINKS -->
[resource-limits]: developer-best-practices-resource-management.md#define-pod-resource-requests-and-limits
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
