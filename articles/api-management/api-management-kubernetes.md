---
title: Azure Kubernetes 서비스에 배포된 마이크로 서비스를 사용하여 Azure API 관리 사용 | 마이크로 소프트 문서
description: 이 문서에서는 AKS를 사용 하 여 API 관리를 배포 하는 옵션에 대해 설명 합니다.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/14/2019
ms.author: apimpm
ms.openlocfilehash: 1d6773b4daac256234c33bf50fb3736d585ac505
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75480997"
---
# <a name="use-azure-api-management-with-microservices-deployed-in-azure-kubernetes-service"></a>Azure Kubernetes 서비스에 배포된 마이크로 서비스를 사용하여 Azure API 관리 사용

마이크로 서비스는 API를 구축하는 데 적합합니다. [AKS(Azure Kubernetes Service)를](https://azure.microsoft.com/services/kubernetes-service/) 사용하면 클라우드에서 [마이크로 서비스 기반 아키텍처를](https://docs.microsoft.com/azure/architecture/guide/architecture-styles/microservices) 신속하게 배포하고 운영할 수 있습니다. 그런 다음 [Azure API 관리(API](https://aka.ms/apimrocks) 관리)를 활용하여 마이크로 서비스를 내부 및 외부 소비를 위한 API로 게시할 수 있습니다. 이 문서에서는 AKS를 사용 하 여 API 관리를 배포 하는 옵션에 대해 설명 합니다. Kubernetes, API 관리 및 Azure 네트워킹에 대한 기본 지식을 가정합니다. 

## <a name="background"></a>배경

마이크로 서비스를 사용량에 대한 API로 게시할 때 마이크로 서비스와 마이크로 서비스를 사용하는 클라이언트 간의 통신을 관리하기가 어려울 수 있습니다. 인증, 권한 부여, 제한, 캐싱, 변환 및 모니터링과 같은 많은 교차 절단 문제가 있습니다. 이러한 문제는 마이크로 서비스가 내부 또는 외부 클라이언트에 노출되는지 여부에 관계없이 유효합니다. 

[API 게이트웨이](https://docs.microsoft.com/dotnet/architecture/microservices/architect-microservice-container-applications/direct-client-to-microservice-communication-versus-the-api-gateway-pattern) 패턴은 이러한 문제를 해결합니다. API 게이트웨이는 마이크로 서비스의 정문 역할을 하고, 클라이언트를 마이크로 서비스에서 분리하고, 추가 보안 계층을 추가하고, 교차 절단 문제를 처리하는 부담을 제거하여 마이크로 서비스의 복잡성을 줄입니다. 

[Azure API 관리는](https://aka.ms/apimrocks) API 게이트웨이 요구 사항을 해결하는 턴키 솔루션입니다. 마이크로 서비스에 대한 일관되고 현대적인 게이트웨이를 신속하게 만들고 API로 게시할 수 있습니다. 전체 수명 주기 API 관리 솔루션으로 API 검색을 위한 셀프 서비스 개발자 포털, API 수명 주기 관리 및 API 분석을 비롯한 추가 기능도 제공합니다.

함께 사용하면 AKS와 API 관리팀이 마이크로 서비스 기반 API를 배포, 게시, 보안, 모니터링 및 관리하기 위한 플랫폼을 제공합니다. 이 문서에서는 API 관리와 함께 AKS를 배포하는 몇 가지 옵션을 다룹니다. 

## <a name="kubernetes-services-and-apis"></a>쿠베르네테스 서비스 및 API

Kubernetes 클러스터에서 컨테이너는 임시이며 수명 주기가 있는 [Pods에](https://kubernetes.io/docs/concepts/workloads/pods/pod/)배포됩니다. 작업자 노드가 죽으면 노드에서 실행되는 Pod가 손실됩니다. 따라서 Pod의 IP 주소는 언제든지 변경할 수 있습니다. 우리는 포드와 통신하는 데 의존 할 수 없습니다. 

이 문제를 해결하기 위해 Kubernetes는 [서비스의](https://kubernetes.io/docs/concepts/services-networking/service/)개념을 도입했습니다. Kubernetes 서비스는 포드의 논리 그룹을 정의하고 이러한 포드에 대한 외부 트래픽 노출, 부하 분산 및 서비스 검색을 가능하게 하는 추상화 계층입니다. 

API 관리를 통해 마이크로 서비스를 API로 게시할 준비가 되면 Kubernetes의 서비스를 API 관리의 API에 매핑하는 방법을 고려해야 합니다. 설정된 규칙이 없습니다. 비즈니스 기능 또는 도메인을 처음에 마이크로 서비스로 설계하고 분할하는 방법에 따라 다릅니다. 예를 들어 서비스 뒤에 있는 포드가 지정된 리소스(예: 고객)의 모든 작업을 담당하는 경우 서비스가 하나의 API에 매핑될 수 있습니다. 리소스에 대한 작업이 여러 마이크로 서비스(예: GetOrder, PlaceOrder)로 분할되는 경우 여러 서비스가 API 관리에서 하나의 단일 API로 논리적으로 집계될 수 있습니다(그림 1 참조). 

매핑도 진화할 수 있습니다. API Management는 마이크로 서비스 앞에 외관을 생성하므로 시간이 지남에 따라 마이크로 서비스를 리팩터링하고 크기를 조정할 수 있습니다. 

![API에 서비스 매핑](./media/api-management-aks/service-api-mapping.png)

## <a name="deploy-api-management-in-front-of-aks"></a>AKS 앞에 API 관리 배포

AKS 클러스터 앞에 API 관리를 배포하는 몇 가지 옵션이 있습니다. 

AKS 클러스터는 항상 VNet(가상 네트워크)에 배포되지만 API 관리 인스턴스를 VNet에 배포할 필요는 없습니다. API 관리가 클러스터 VNet 내에 상주하지 않는 경우 AKS 클러스터는 API 관리에 연결할 수 있도록 공용 끝점을 게시해야 합니다. 이 경우 API 관리와 AKS 간의 연결을 보호해야 합니다. 즉, API 관리를 통해서만 클러스터에 액세스할 수 있도록 해야 합니다. 의 옵션을 통해 이동하자. 

### <a name="option-1-expose-services-publicly"></a>옵션 1: 서비스를 공개적으로 노출

AKS 클러스터의 서비스는 NodePort, LoadBalancer 또는 ExternalName의 [서비스 유형을](https://docs.microsoft.com/azure/aks/concepts-network) 사용하여 공개적으로 노출될 수 있습니다. 이 경우 서비스는 공용 인터넷에서 직접 액세스할 수 있습니다. 클러스터 앞에 API Management를 배포한 후 마이크로 서비스에 인증을 적용하여 모든 인바운드 트래픽이 API 관리를 통과하도록 해야 합니다. 예를 들어 API Management는 클러스터에 대한 각 요청에 액세스 토큰을 포함할 수 있습니다. 각 마이크로 서비스는 요청을 처리하기 전에 토큰의 유효성을 검사해야 합니다. 


특히 마이크로 서비스에 인증 논리가 이미 구현되어 있는 경우 AKS 앞에 API 관리를 배포하는 것이 가장 쉬운 옵션일 수 있습니다. 

![서비스를 직접 게시](./media/api-management-aks/direct.png)

장점:
* 클러스터 VNet에 삽입할 필요가 없으므로 API 관리 측면에서 간편한 구성
* 서비스가 이미 공개적으로 노출되고 인증 논리가 마이크로 서비스에 이미 있는 경우 AKS 측에서 변경이 없습니다.

단점:
* 서비스 엔드포인트의 공개 가시성으로 인한 잠재적 보안 위험
* 인바운드 클러스터 트래픽에 대한 단일 진입점 없음
* 중복 인증 논리로 마이크로 서비스 복잡성

### <a name="option-2-install-an-ingress-controller"></a>옵션 2: 인그레스 컨트롤러 설치

옵션 1이 더 쉬울 수도 있지만 위에서 언급 한 주목할만한 단점이 있습니다. API 관리 인스턴스가 클러스터 VNet에 상주하지 않는 경우 뮤추얼 TLS 인증(mTLS)은 API 관리 인스턴스와 AKS 클러스터 간의 양방향으로 트래픽을 안전하게 신뢰할 수 있도록 하는 강력한 방법입니다. 

상호 TLS 인증은 기본적으로 API 관리에서 [지원되며](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates) [침투 컨트롤러를 설치하여](https://docs.microsoft.com/azure/aks/ingress-own-tls) Kubernetes에서 사용할 수 있습니다(그림 3). 결과적으로 인증은 마이크로 서비스를 단순화하는 Inress 컨트롤러에서 수행됩니다. 또한 인그레스에서 허용된 목록에 API 관리의 IP 주소를 추가하여 API 관리만 클러스터에 액세스할 수 있는지 확인할 수 있습니다.  

 
![인서스 컨트롤러를 통해 게시](./media/api-management-aks/ingress-controller.png)


장점:
* 클러스터 VNet에 주입할 필요가 없고 mTLS가 기본적으로 지원되므로 API 관리 측면에서 간편한 구성
* Inress 컨트롤러 계층에서 인바운드 클러스터 트래픽에 대한 보호를 중앙 집중화합니다.
* 공개적으로 보이는 클러스터 엔드포인트를 최소화하여 보안 위험 감소

단점:
* Ingress 컨트롤러를 설치, 구성 및 유지 관리하고 mTLS에 사용되는 인증서를 관리하기 위한 추가 작업으로 인해 클러스터 구성의 복잡성증가
* Ingress 컨트롤러 엔드포인트의 공개 가시성으로 인한 보안 위험


API Management를 통해 API를 게시하면 구독 키를 사용하여 이러한 API에 쉽고 안전하게 액세스할 수 있습니다. 게시된 API를 사용해야 하는 개발자는 이러한 API에 호출 시 HTTP 요청에 유효한 구독 키를 포함해야 합니다. 그렇지 않으면 API Management 게이트웨이에서 호출을 즉시 거부합니다. 그리고 백 엔드 서비스에 호출이 전달되지 않습니다.

API에 액세스하기 위해 구독 키를 얻으려면 구독이 필요합니다. 구독은 일반적으로 구독 키 쌍의 이름을 지정한 컨테이너입니다. 게시된 API를 사용해야 하는 개발자는 구독을 가져올 수 있습니다. API 게시자에게 승인을 받을 필요는 없습니다. 또한 API 게시자는 API 소비자를 위해 직접 구독을 만들 수도 있습니다.

### <a name="option-3-deploy-apim-inside-the-cluster-vnet"></a>옵션 3: 클러스터 VNet 내부에 APIM 배포

경우에 따라 규정 제약 이 있거나 엄격한 보안 요구 사항이 있는 고객은 공개적으로 노출된 끝점으로 인해 옵션 1과 2가 실행 가능한 솔루션이 아닐 수 있습니다. 다른 경우는 AKS 클러스터와 마이크로 서비스를 사용하는 응용 프로그램이 동일한 VNet 내에 있을 수 있으므로 모든 API 트래픽이 VNet 내에 남아 있으므로 클러스터를 공개적으로 노출할 이유가 없습니다. 이러한 시나리오의 경우 클러스터 VNet에 API 관리를 배포할 수 있습니다. [API 관리 프리미엄 계층은](https://aka.ms/apimpricing) VNet 배포를 지원합니다. 

[API 관리를 VNet에 배포하는](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet) 두 가지 모드가 있습니다. 

API 소비자가 클러스터 VNet에 거주하지 않는 경우 외부 모드(그림 4)를 사용해야 합니다. 이 모드에서는 API 관리 게이트웨이가 클러스터 VNet에 주입되지만 외부 로드 밸런서를 통해 공용 인터넷에서 액세스할 수 있습니다. 외부 클라이언트가 마이크로 서비스를 사용할 수 있도록 허용하면서 클러스터를 완전히 숨길 수 있습니다. 또한 NSG(네트워크 보안 그룹)와 같은 Azure 네트워킹 기능을 사용하여 네트워크 트래픽을 제한할 수 있습니다.

![외부 VNet 모드](./media/api-management-aks/vnet-external.png)

모든 API 소비자가 클러스터 VNet 내에 있는 경우 내부 모드(그림 5)를 사용할 수 있습니다. 이 모드에서는 API 관리 게이트웨이가 클러스터 VNET에 삽입되고 내부 로드 밸런서를 통해 이 VNet 내에서만 액세스할 수 있습니다. 공용 인터넷에서 API 관리 게이트웨이 또는 AKS 클러스터에 연결할 수 있는 방법은 없습니다. 

![내부 VNet 모드](./media/api-management-aks/vnet-internal.png)

 두 경우 모두 AKS 클러스터가 공개적으로 표시되지 않습니다. 옵션 2와 비교하여, 받는 컨트롤러가 필요하지 않을 수 있습니다. 시나리오 및 구성에 따라 API 관리와 마이크로 서비스 간에 인증이 필요할 수 있습니다. 예를 들어 서비스 메시를 채택하는 경우 항상 상호 TLS 인증이 필요합니다. 

장점:
* AKS 클러스터에 공용 끝점이 없기 때문에 가장 안전한 옵션
* 퍼블릭 엔드포인트가 없으므로 클러스터 구성 간소화
* 내부 모드를 사용하여 VNet 내부에서 API 관리 및 AKS를 모두 숨길 수 있는 기능
* NSG(네트워크 보안 그룹)와 같은 Azure 네트워킹 기능을 사용하여 네트워크 트래픽을 제어하는 기능

단점:
* VNet 내부에서 작동하도록 API 관리를 배포하고 구성하는 복잡성 증가

## <a name="next-steps"></a>다음 단계

* [AKS의 애플리케이션을 위한 네트워크 개념에](https://docs.microsoft.com/azure/aks/concepts-network) 대해 자세히 알아보기
* [가상 네트워크에서 API 관리를 사용하는 방법에](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet) 대해 자세히 알아보기





