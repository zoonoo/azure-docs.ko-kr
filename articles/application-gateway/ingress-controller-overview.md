---
title: Azure 애플리케이션 게이트웨이 수신 컨트롤러 란?
description: 이 문서에서는 Application Gateway 수신 컨트롤러의 용도를 소개 합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: c1bd41587e4f56fb0a7f3eb8285d301751f558d1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84668103"
---
# <a name="what-is-application-gateway-ingress-controller"></a>Application Gateway 수신 컨트롤러는 무엇입니까?
AGIC (Application Gateway 수신 컨트롤러)는 Kubernetes 응용 프로그램으로, azure [Kubernetes 서비스 (AKS)](https://azure.microsoft.com/services/kubernetes-service/) 고객이 azure의 기본 [Application Gateway](https://azure.microsoft.com/services/application-gateway/) L7 부하 분산 장치를 활용 하 여 클라우드 소프트웨어를 인터넷에 노출할 수 있도록 합니다. AGIC는 호스트 되는 Kubernetes 클러스터를 모니터링 하 고 Application Gateway를 지속적으로 업데이트 하 여 선택 된 서비스가 인터넷에 노출 되도록 합니다.

수신 컨트롤러는 고객의 AKS에서 자체 pod로 실행 됩니다. AGIC는 Kubernetes 리소스의 하위 집합을 모니터링 하 여 변경 내용을 모니터링 합니다. AKS 클러스터의 상태는 특정 구성 Application Gateway 변환 되 고 [ARM (Azure Resource Manager)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)에 적용 됩니다.

## <a name="benefits-of-application-gateway-ingress-controller"></a>Application Gateway 수신 컨트롤러의 이점
AGIC를 사용 하면 AKS 클러스터 앞에 다른 부하 분산 장치/공용 IP가 필요 하지 않으며 요청이 AKS 클러스터에 도달 하기 전에 데이터 경로에서 여러 홉을 피할 수 있습니다. Application Gateway는 해당 개인 IP를 직접 사용 하 여 pod에 대 한 통신을 수행 하며 NodePort 또는 KubeProxy services가 필요 하지 않습니다. 또한 배포에 더 나은 성능을 제공 합니다.

수신 컨트롤러는 Standard_v2 및 WAF_v2 Sku 에서만 지원 되며 자동 크기 조정 이점도 제공 합니다. AKS 클러스터의 리소스를 사용 하지 않고 트래픽 로드의 증가 또는 감소에 대응 하 여 적절 하 게 크기를 조정할 수 Application Gateway.

AGIC 외에도 Application Gateway를 사용 하면 TLS 정책 및 WAF (웹 응용 프로그램 방화벽) 기능을 제공 하 여 AKS 클러스터를 보호할 수 있습니다.

![Azure 애플리케이션 게이트웨이 + AKS](./media/application-gateway-ingress-controller-overview/architecture.png)

AGIC는 서비스 및 배포/Pod와 함께 Kubernetes [수신 리소스](https://kubernetes.io/docs/user-guide/ingress/)를 통해 구성 됩니다. Azure의 기본 Application Gateway L7 부하 분산 장치를 활용 하 여 다양 한 기능을 제공 합니다. 몇 가지를 예로 들자면 다음과 같습니다.
  - URL 라우팅
  - 쿠키 기반 선호도
  - TLS 종료
  - 종단 간 TLS
  - 공용, 개인 및 하이브리드 웹 사이트 지원
  - 통합 웹 응용 프로그램 방화벽

## <a name="difference-between-helm-deployment-and-aks-add-on"></a>투구 배포 및 AKS 추가 기능 간의 차이점
AKS 클러스터에 대해 AGIC를 배포 하는 방법에는 두 가지가 있습니다. 첫 번째 방법은 투구를 통한 것입니다. 두 번째는 AKS를 추가 기능으로 설정 하는 것입니다. AGIC를 AKS 추가 기능으로 배포 하는 경우의 주요 장점은 투구를 통해 배포 하는 것 보다 훨씬 간단 하다는 것입니다. 새 설치의 경우 Azure CLI에서 한 줄에 AGIC를 추가 기능으로 사용 하 여 새 Application Gateway 및 새 AKS 클러스터를 배포할 수 있습니다. 또한 추가 기능은 완전히 관리 되는 서비스 이며, 자동 업데이트 및 지원 증가와 같은 추가 이점을 제공 합니다. AKS는 투구를 통해 배포 된 AGIC를 지원 하지 않지만 AKS 추가 기능으로 배포 된 AGIC는 AKS에서 지원 됩니다. 

AGIC 추가 기능은 여전히 고객의 AKS 클러스터에 pod로 배포 되지만, 투구 배포 버전과 AGIC의 추가 기능 버전 간에는 몇 가지 차이점이 있습니다. 다음은 두 버전 간의 차이점 목록입니다. 
  - AKS 추가 기능에서 투구 배포 값을 수정할 수 없습니다.
    - `verbosityLevel`기본적으로 5로 설정 됩니다.
    - `usePrivateIp`기본적으로 false로 설정 됩니다. 이는 [사용-개인-ip 주석](ingress-controller-annotations.md#use-private-ip) 으로 덮어쓸 수 있습니다.
    - `shared`추가 기능에서 지원 되지 않음 
    - `reconcilePeriodSeconds`추가 기능에서 지원 되지 않음
    - `armAuth.type`추가 기능에서 지원 되지 않음
  - 투구를 통해 배포 된 AGIC는 ProhibitedTargets을 지원 합니다. 즉, AGIC는 기존 백 엔드에 영향을 주지 않고 AKS 클러스터에 대해 특별히 Application Gateway를 구성할 수 있습니다. AGIC 추가 기능에서 현재이 기능을 지원 하지 않습니다. 
  - AGIC 추가 기능이 관리 되는 서비스 이기 때문에 고객이 AGIC를 수동으로 업데이트 해야 하는 투구를 통해 배포 된 AGIC와 달리 고객은 최신 버전의 AGIC 추가 기능으로 자동 업데이트 됩니다. 

> [!NOTE]
> 배포의 AGIC AKS 추가 기능은 현재 미리 보기 상태입니다. 미리 보기로 제공 되는 기능에 프로덕션 워크 로드를 실행 하지 않는 것이 좋습니다. 사용해 보려는 경우에는를 사용 하 여 테스트 하도록 새 클러스터를 설정 하는 것이 좋습니다. 

다음 표에서는 현재 AGIC의 AKS 추가 기능 버전 및 지원 되는 시나리오를 정렬 합니다. 

### <a name="aks-add-on-agic-single-aks-cluster"></a>AKS 추가 기능 AGIC (단일 AKS 클러스터)
|                  |1 Application Gateway |2 + 응용 프로그램 게이트웨이 |
|------------------|---------|--------|
|**AGIC 1**|예, 지원 됩니다. |아니요, 백로그에 있습니다. |
|**2 + AGICs**|아니요, AGIC 1 개만 지원 됨/클러스터 |아니요, AGIC 1 개만 지원 됨/클러스터 |

### <a name="helm-deployed-agic-single-aks-cluster"></a>AGIC 배포 된 투구 (단일 AKS 클러스터)
|                  |1 Application Gateway |2 + 응용 프로그램 게이트웨이 |
|------------------|---------|--------|
|**AGIC 1**|예, 지원 됩니다. |아니요, 백로그에 있습니다. |
|**2 + AGICs**|Shared ProhibitedTarget 기능을 사용 하 고 별도의 네임 스페이스를 시청 해야 함 |예, 지원 됩니다. |

### <a name="helm-deployed-agic-2-aks-clusters"></a>AGIC 배포 된 투구 (2 개 이상의 AKS 클러스터)
|                  |1 Application Gateway |2 + 응용 프로그램 게이트웨이 |
|------------------|---------|--------|
|**AGIC 1**|N/A |해당 없음 |
|**2 + AGICs**|Shared ProhibitedTarget 기능을 사용 해야 합니다. |해당 없음 |

## <a name="next-steps"></a>다음 단계
- [**AKS 추가 기능 최적의 배포**](tutorial-ingress-controller-add-on-new.md): AGIC 추가 기능, AKS 및 Application Gateway를 설치 하는 방법에 대 한 지침은 빈 슬레이트 인프라에 있습니다.
- [**AKS 추가 기능 Brownfield 배포**](tutorial-ingress-controller-add-on-existing.md): 기존 Application Gateway를 사용 하 여 AKS 클러스터에 AGIC 추가 기능을 설치 합니다.
- [**투구 최적의 배포**](ingress-controller-install-new.md): 빈 슬레이트 인프라에서 투구, new AKS 클러스터 및 새로운 Application Gateway를 통해 AGIC를 설치 합니다.
- [**투구 Brownfield 배포**](ingress-controller-install-existing.md): 기존 AKS 클러스터에 대 한 AGIC를 통해, Application Gateway을 배포 합니다.

