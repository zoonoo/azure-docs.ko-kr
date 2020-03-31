---
title: Azure 응용 프로그램 게이트웨이 인그리스 컨트롤러란 무엇입니까?
description: 이 문서에서는 응용 프로그램 게이트웨이 받는 컨트롤러에 대 한 소개를 제공 합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: d6a63b6276c07b1fe6487b97f5c7fc255b6d3411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335811"
---
# <a name="what-is-application-gateway-ingress-controller"></a>응용 프로그램 게이트웨이 인그리스 컨트롤러란 무엇입니까?
응용 프로그램 게이트웨이 침투 컨트롤러(AGIC)는 Kubernetes 응용 프로그램으로, [Azure Kubernetes 서비스(AKS)](https://azure.microsoft.com/services/kubernetes-service/) 고객이 Azure의 기본 [응용 프로그램 게이트웨이](https://azure.microsoft.com/services/application-gateway/) L7 로드 밸러커를 활용하여 클라우드 소프트웨어를 인터넷에 노출할 수 있습니다. AGIC는 호스팅되는 Kubernetes 클러스터를 모니터링하고 선택한 서비스가 인터넷에 노출되도록 응용 프로그램 게이트웨이를 지속적으로 업데이트합니다.

Ingress 컨트롤러는 고객의 AKS에서 자체 포드에서 실행됩니다. AGIC는 Kubernetes 리소스의 하위 집합에서 변경 내용을 모니터링합니다. AKS 클러스터의 상태는 응용 프로그램 게이트웨이 특정 구성으로 변환되고 [ARM(Azure 리소스 관리자)에](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)적용됩니다.

## <a name="benefits-of-application-gateway-ingress-controller"></a>애플리케이션 게이트웨이 인그레스 컨트롤러의 이점
AGIC를 사용하면 단일 애플리케이션 게이트웨이 인그레스 컨트롤러를 사용하여 여러 AKS 클러스터를 제어할 수 있습니다. 또한 AGIC는 AKS 클러스터 앞에 다른 로드 밸런서/공용 IP가 필요하지 않으며 요청이 AKS 클러스터에 도달하기 전에 데이터 경로에서 여러 홉을 방지할 수 있습니다. 응용 프로그램 게이트웨이는 개인 IP를 사용하여 포드와 직접 대화하며 NodePort 또는 KubeProxy 서비스가 필요하지 않습니다. 이렇게 하면 배포에 더 나은 성능이 향상됩니다.

Ingress 컨트롤러는 Standard_v2 및 WAF_v2 SKU에 의해 독점적으로 지원되며, 이는 또한 자동 크기 조정 혜택을 제공합니다. 응용 프로그램 게이트웨이는 AKS 클러스터의 리소스를 소모하지 않고도 트래픽 로드의 증가 또는 감소에 대응하여 대응하고 그에 따라 확장할 수 있습니다.

또한 AGIC 외에도 응용 프로그램 게이트웨이를 사용하면 TLS 정책 및 WAF(웹 응용 프로그램 방화벽) 기능을 제공하여 AKS 클러스터를 보호하는 데도 도움이 됩니다.

![Azure 응용 프로그램 게이트웨이 + AKS](./media/application-gateway-ingress-controller-overview/architecture.png)

AGIC는 Kubernetes 침투 [리소스와](https://kubernetes.io/docs/user-guide/ingress/)서비스 및 배포/포드를 통해 구성됩니다. Azure의 기본 응용 프로그램 게이트웨이 L7 로드 밸러서를 활용하는 다양한 기능을 제공합니다. 몇 가지를 예로 들자면 다음과 같습니다.
  - URL 라우팅
  - 쿠키 기반 선호도
  - TLS 종단
  - 종단 간 TLS
  - 공용, 비공개 및 하이브리드 웹 사이트 지원
  - 통합 웹 응용 프로그램 방화벽

AGIC는 여러 네임스페이스를 처리할 수 있으며 금지 대상을 가지고 있으며, 이는 AGIC가 다른 기존 백엔드에 영향을 주지 않고 AKS 클러스터에 맞게 응용 프로그램 게이트웨이를 구성할 수 있다는 것을 의미합니다. 

## <a name="next-steps"></a>다음 단계

- [**그린필드 배포**](ingress-controller-install-new.md): 빈 슬레이트 인프라에 AGIC, AKS 및 애플리케이션 게이트웨이 설치에 대한 지침입니다.
- [**브라운필드 배포**](ingress-controller-install-existing.md): 기존 AKS 및 애플리케이션 게이트웨이에 AGIC를 설치합니다.

