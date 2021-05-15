---
title: Azure Application Gateway 수신 컨트롤러란?
description: 이 문서에서는 Application Gateway 수신 컨트롤러에 대해 소개합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: 2564fd38056241fd48f58f5f6039bf64f92b6741
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101714411"
---
# <a name="what-is-application-gateway-ingress-controller"></a>Application Gateway 수신 컨트롤러란?
AGIC(Application Gateway 수신 컨트롤러)는 Kubernetes 애플리케이션으로, [AKS(Azure Kubernetes Service)](https://azure.microsoft.com/services/kubernetes-service/) 고객이 Azure의 기본 [Application Gateway](https://azure.microsoft.com/services/application-gateway/) L7 부하 분산 장치를 활용하여 클라우드 소프트웨어를 인터넷에 노출할 수 있도록 합니다. AGIC는 호스트되는 Kubernetes 클러스터를 모니터링하고 Application Gateway를 지속적으로 업데이트하여 선택된 서비스가 인터넷에 노출되도록 합니다.

수신 컨트롤러는 고객의 AKS에서 자체 pod로 실행됩니다. AGIC는 Kubernetes 리소스의 하위 집합에서 변경 내용을 모니터링합니다. AKS 클러스터의 상태는 Application Gateway 특정 구성으로 변환되고 [ARM(Azure Resource Manager)](../azure-resource-manager/management/overview.md)에 적용됩니다.

## <a name="benefits-of-application-gateway-ingress-controller"></a>Application Gateway 수신 컨트롤러의 이점
AGIC를 사용하면 AKS 클러스터 앞에 다른 부하 분산 장치/공용 IP가 필요하지 않으며 요청이 AKS 클러스터에 도달하기 전에 데이터 경로에서 여러 홉을 피할 수 있습니다. Application Gateway는 해당 개인 IP를 직접 사용하여 pod에 대한 통신을 수행하며 NodePort 또는 KubeProxy 서비스가 필요하지 않습니다. 또한 배포에 더 나은 성능을 제공합니다.

수신 컨트롤러는 Standard_v2 및 WAF_v2 SKU에서만 지원되며 자동 크기 조정 이점도 제공합니다. AKS 클러스터의 리소스를 사용하지 않고 트래픽 로드의 증가 또는 감소에 대응하여 적절하게 크기를 조정할 수 있습니다.

AGIC 외에도 Application Gateway를 사용하면 TLS 정책 및 WAF(웹 애플리케이션 방화벽) 기능을 제공하여 AKS 클러스터를 보호할 수도 있습니다.

![Azure Application Gateway + AKS](./media/application-gateway-ingress-controller-overview/architecture.png)

AGIC는 서비스 및 배포/Pod와 함께 Kubernetes [수신 리소스](https://kubernetes.io/docs/user-guide/ingress/)를 통해 구성됩니다. Azure의 기본 Application Gateway L7 부하 분산 장치를 활용하여 다양한 기능을 제공합니다. 몇 가지를 예로 들자면 다음과 같습니다.
  - URL 라우팅
  - 쿠키 기반 선호도
  - TLS 종료
  - 엔드투엔드 TLS
  - 공용, 개인 및 하이브리드 웹 사이트 지원
  - 통합 웹 애플리케이션 방화벽

## <a name="difference-between-helm-deployment-and-aks-add-on"></a>Helm 배포와 AKS 추가 기능 간의 차이점
AKS 클러스터에 대해 AGIC를 배포하는 방법에는 두 가지가 있습니다. 첫 번째 방법은 Helm을 통한 것이고 두 번째는 AKS를 추가 기능으로 설정하는 것입니다. AGIC를 AKS 추가 기능으로 배포하는 경우 주요 장점은 Helm을 통해 배포하는 것보다 훨씬 간단하다는 것입니다. 새 설치의 경우 Azure CLI에서 한 줄에 AGIC를 추가 기능으로 사용하여 새 Application Gateway 및 새 AKS 클러스터를 배포할 수 있습니다. 또한 추가 기능은 완전히 관리되는 서비스이며, 자동 업데이트 및 지원 증가와 같은 추가 이점을 제공합니다. AGIC(Helm 및 AKS 추가 기능)를 배포하는 두 가지 방법은 모두 Microsoft에서 완벽하게 지원됩니다. 또한 추가 기능을 사용하면 AKS를 첫 번째 클래스 추가 기능으로 사용하여 AKS와의 통합을 개선할 수 있습니다.

AGIC 추가 기능은 계속 고객의 AKS 클러스터에 pod로 배포되지만, Helm 배포 버전과 AGIC의 추가 기능 버전 간에는 몇 가지 차이점이 있습니다. 다음은 두 버전 간의 차이점 목록입니다. 
  - AKS 추가 기능에서는 Helm 배포 값을 수정할 수 없습니다.
    - `verbosityLevel`은 기본적으로 5로 설정됩니다.
    - `usePrivateIp`는 기본적으로 false로 설정됩니다. 이는 [use-private-ip annotation](ingress-controller-annotations.md#use-private-ip)으로 덮어쓸 수 있습니다.
    - `shared`는 추가 기능에서 지원되지 않습니다. 
    - `reconcilePeriodSeconds`는 추가 기능에서 지원되지 않습니다.
    - `armAuth.type`는 추가 기능에서 지원되지 않습니다.
  - Helm을 통해 배포된 AGIC는 ProhibitedTargets을 지원합니다. 즉, AGIC에서 기존 백 엔드에 영향을 주지 않고 AKS 클러스터에 대해 특별히 Application Gateway를 구성할 수 있습니다. AGIC 추가 기능은 현재 이 기능을 지원하지 않습니다. 
  - AGIC 추가 기능은 관리되는 서비스이기 때문에 고객이 AGIC를 수동으로 업데이트해야 하는 Helm을 통해 배포된 AGIC와 달리 고객은 최신 버전의 AGIC 추가 기능으로 자동으로 업데이트됩니다. 

> [!NOTE]
> 고객은 AKS 클러스터당 하나의 AGIC 추가 기능만 배포할 수 있으며, 각 AGIC 추가 기능에는 현재 Application Gateway 하나만 대상으로 지정할 수 있습니다. 클러스터당 두 개 이상의 AGIC가 필요한 배포 또는 Application Gateway 하나를 대상으로 하는 여러 AGIC가 필요한 배포의 경우 Helm을 통해 배포된 AGIC를 계속 사용하세요. 

## <a name="next-steps"></a>다음 단계
- [**AKS 추가 기능 그린필드 배포**](tutorial-ingress-controller-add-on-new.md): AGIC 추가 기능, AKS 및 Application Gateway를 빈 슬레이트 인프라에 설치하는 방법에 대한 지침입니다.
- [**AKS 추가 기능 브라운필드 배포**](tutorial-ingress-controller-add-on-existing.md): 기존 Application Gateway를 사용하여 AKS 클러스터에 AGIC 추가 기능을 설치합니다.
- [**Helm 그린필드 배포**](ingress-controller-install-new.md): Helm, 새로운 AKS 클러스터 및 새로운 Application Gateway를 통해 빈 슬레이트 인프라에 AGIC를 설치합니다.
- [**Helm 브라운필드 배포**](ingress-controller-install-existing.md): 기존 AKS 클러스터 및 Application Gateway의 Helm을 통해 AGIC를 배포합니다.