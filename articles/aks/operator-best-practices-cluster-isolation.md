---
title: 클러스터 격리에 대한 모범 사례
titleSuffix: Azure Kubernetes Service
description: AKS(Azure Kubernetes Services)의 클러스터 격리에 대한 클러스터 운영자 모범 사례 알아보기
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 3b6295c1f1ae424c77c32876e32609a7d492e07d
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110461642"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Services)의 클러스터 격리 모범 사례

AKS(Azure Kubernetes Service)에서 클러스터를 관리할 경우 종종 팀 및 워크로드를 격리해야 합니다. AKS는 다중 테넌트 클러스터를 실행하고 리소스를 격리하는 방법의 유연성을 제공합니다. Kubernetes에 대한 투자를 최대화하려면 먼저 AKS 다중 테넌트 및 격리 기능을 이해하고 구현합니다.

이 모범 사례 문서는 클러스터 운영을 위한 격리를 중점적으로 설명합니다. 이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 다중 테넌트 클러스터 및 리소스 격리 계획
> * AKS 클러스터에서 논리적 또는 물리적 격리 사용

## <a name="design-clusters-for-multi-tenancy"></a>다중 테넌트용 클러스터 설계

Kubernetes를 사용하면 팀과 워크로드를 동일한 클러스터에 논리적으로 격리할 수 있습니다. 목표는 각 팀이 필요로 하는 리소스로 범위가 지정된 권한을 최소한의 수로 제공하는 것입니다. Kubernetes [네임스페이스][k8s-namespaces]는 논리적 격리 경계를 만듭니다. 격리 및 다중 테넌트에 대한 추가 Kubernetes 기능 및 고려 사항에는 다음 영역이 포함됩니다:

### <a name="scheduling"></a>예약

*일정* 은 리소스 할당량 및 Pod 중단 예산과 같은 기본 기능을 사용합니다. 이러한 기능에 대한 자세한 내용은 [AKS의 기본 스케줄러 기능 모범 사례][aks-best-practices-scheduler]를 참조하세요.

고급 스케줄러 기능은 다음과 같습니다.
* 테인트 및 톨러레이션
* 노드 선택기
* 노드 및 Pod 선호도 또는 선호도 방지. 

이러한 기능에 대한 자세한 내용은 [AKS의 고급 스케줄러 기능 모범 사례][aks-best-practices-advanced-scheduler]를 참조하세요.

### <a name="networking"></a>네트워킹

*네트워킹* 은 네트워크 정책을 사용하여 Pod 내외부의 트래픽 흐름을 제어합니다.

### <a name="authentication-and-authorization"></a>인증 및 권한 부여

*인증 및 권한 부여* 는 다음을 사용합니다.
* RBAC(역할 기반 액세스 제어)
* Azure AD(Active Directory) 통합
* Pod ID
* Azure Key Vault의 비밀 

이러한 기능에 대한 자세한 내용은 [AKS의 인증 및 권한 부여 모범 사례][aks-best-practices-identity]를 참조하세요.

### <a name="containers"></a>컨테이너
*컨테이너* 에는 다음이 포함됩니다.
* AKS용 Azure Policy 추가 기능으로 Pod 보안을 적용합니다.
* Pod 보안 컨텍스트 사용.
* 이미지와 런타임에서 취약성을 모두 검사합니다. 
* App Armor 또는 Seccomp(보안 컴퓨팅)를 사용하여 컨테이너 액세스를 기본 노드로 제한합니다.

## <a name="logically-isolate-clusters"></a>논리적으로 클러스터 격리

> **모범 사례 지침**
>
> *논리적 격리* 를 사용하여 팀과 프로젝트를 분리합니다. 팀 또는 애플리케이션에 배포하는 물리적 AKS 클러스터 수를 최소화합니다.

논리적 격리를 사용하면 여러 워크로드, 팀 또는 환경에 단일 AKS 클러스터를 사용할 수 있습니다. Kubernetes [네임스페이스][k8s-namespaces]는 워크로드 및 리소스의 논리적 격리 경계를 형성합니다.

![AKS에서 Kubernetes 클러스터의 논리적 격리](media/operator-best-practices-cluster-isolation/logical-isolation.png)

클러스터의 논리적 분리는 일반적으로 물리적으로 분리하는 것보다 더 높은 Pod 밀도를 제공하며, 클러스터에서 유휴 상태인 컴퓨팅 용량이 적습니다. Kubernetes 클러스터 자동 크기 조정기와 결합된 경우에는 요구에 맞게 노드 수를 강화 또는 규모 축소할 수 있습니다. 이 자동 크기 조정의 모범 사례 접근 방식을 사용하면 필요한 만큼만 노드를 실행하여 비용을 최소화할 수 있습니다.

현재 Kubernetes 환경은 악의적인 다중 테넌트 사용에 대해 완전히 안전하지 않습니다. 다중 테넌트 환경에서 여러 테넌트는 공통의 공유 인프라에서 작업합니다. 모든 테넌트를 신뢰할 수 없는 경우 테넌트가 다른 테넌트의 보안 및 서비스에 영향을 미치지 않도록 추가 계획이 필요합니다.

노드에 대한 Kubernetes RBAC와 같은 추가 보안 기능은 악용을 효율적으로 차단합니다. 악의적인 다중 테넌트 작업을 실행할 때 진정한 보안이 적용되도록 하려면 하이퍼바이저만 신뢰해야 합니다. Kubernetes의 보안 도메인은 개별 노드가 아닌 전체 클러스터가 됩니다. 

이러한 유형의 악의적인 다중 테넌트 워크로드의 경우 물리적으로 격리된 클러스터를 사용해야 합니다.

## <a name="physically-isolate-clusters"></a>물리적으로 클러스터 격리

> **모범 사례 지침** 
>
> 개별 팀 또는 애플리케이션 배포에 대한 물리적 격리 사용을 최소화합니다. 대신에 이전 섹션에서 설명한 대로 ‘논리적’ 격리를 사용합니다.

AKS 클러스터를 물리적으로 분리하는 것은 클러스터 격리에 대한 일반적인 접근 방식입니다. 이 격리 모델에서는 팀 또는 워크로드에 자체 AKS 클러스터가 할당됩니다. 물리적 격리는 워크로드 또는 팀을 격리하는 가장 쉬운 방법처럼 보일 수 있지만 관리 및 재무 오버헤드를 추가합니다. 이제 이러한 여러 클러스터를 유지 관리하고 개별적으로 액세스를 제공하고 사용 권한을 할당해야 합니다. 또한 각 개별 노드에 대해 요금이 청구됩니다.

![AKS에서 개별 Kubernetes 클러스터의 물리적 격리](media/operator-best-practices-cluster-isolation/physical-isolation.png)

물리적으로 분리된 클러스터는 일반적으로 Pod 밀도가 낮습니다. 각 팀 또는 워크로드에는 고유한 AKS 클러스터가 있으므로 클러스터는 대개 컴퓨팅 리소스를 사용하여 초과 프로비저닝됩니다. 일반적으로 해당 노드에는 적은 Pod이 예약됩니다. 요청되지 않은 노드 용량은 다른 팀에서 개발한 애플리케이션 또는 서비스에 사용할 수 없습니다. 이러한 초과 리소스로 인해 물리적으로 분리된 클러스터에서 추가 비용이 발생합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 클러스터 격리를 중점적으로 설명했습니다. AKS의 클러스터 작업에 대한 자세한 내용은 다음 모범 사례를 참조하세요.

* [기본 Kubernetes 스케줄러 기능][aks-best-practices-scheduler]
* [고급 Kubernetes 스케줄러 기능][aks-best-practices-advanced-scheduler]
* [인증 및 권한 부여][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[k8s-namespaces]: concepts-clusters-workloads.md#namespaces
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
