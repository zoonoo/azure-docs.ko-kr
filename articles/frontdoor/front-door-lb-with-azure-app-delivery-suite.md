---
title: Azure Front Door - Azure 애플리케이션 전송 제품군을 통해 부하 분산 | Microsoft Docs
description: 이 문서에서는 Azure에서 애플리케이션 배달 제품군을 사용하여 부하 분산을 권장하는 방법을 알아봅니다.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2021
ms.author: duau
ms.openlocfilehash: 9910cadfcbb40bbb411c8d07bc73ad2205c085e5
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110070519"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>Azure 애플리케이션 배달 제품군을 통해 부하 분산

## <a name="introduction"></a>소개
Microsoft Azure는 네트워크 트래픽을 분산하고 부하를 분산하는 방법을 관리하는 다음과 같은 다양한 전역 및 지역 서비스를 제공합니다. 

* Application Gateway
* Front Door 
* Load Balancer  
* Traffic Manager

Azure의 많은 지역 및 영역 아키텍처와 함께 이러한 서비스를 함께 사용하면 강력하고 확장성 있는 고성능 애플리케이션을 빌드할 수 있습니다.

:::image type="content" source="./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png" alt-text="애플리케이션 배달 제품군":::
 
이러한 서비스는 두 가지 범주로 구분됩니다.
1. Traffic Manager 및 Front Door와 같은 **글로벌 부하 분산 서비스** 는 최종 사용자의 트래픽을 지역 백 엔드, 클라우드 및 하이브리드 온-프레미스 서비스에 분산합니다. 글로벌 부하 분산은 트래픽을 가장 가까운 서비스 백 엔드로 라우팅하고, 서비스 안정성 변화에 대응하여 사용자에게 상시 가용성과 고성능을 유지합니다. 
1. Load Balancers 및 Application Gateway와 같은 **지역 부하 분산 서비스** 는 VNET(가상 네트워크) 내의 VM(가상 머신) 또는 지역 내의 서비스 엔드포인트에 트래픽을 분산하는 기능을 제공합니다.

이러한 글로벌 서비스와 지역 서비스를 결합하면 애플리케이션은 최종 사용자로부터 IaaS, PaaS 또는 온-프레미스 서비스로 전송되는 안정적이고 안전한 엔드투엔드 트래픽의 이점을 누릴 수 있습니다. 다음 섹션에서는 이러한 각 서비스에 대해 설명합니다.

## <a name="global-load-balancing"></a>전역 부하 분산
**Traffic Manager** 는 전역 DNS 부하 분산을 제공 합니다. 들어오는 DNS 요청을 살펴보고 고객이 선택한 라우팅 정책에 따라 정상 백 엔드로 응답합니다. 라우팅 메서드의 옵션은 다음과 같습니다.
- **성능 라우팅** 은 최소 대기 시간으로 가장 가까운 백 엔드로 요청을 보냅니다.
- **우선 순위 라우팅** 은 다른 백 엔드를 백업으로 사용하여 모든 트래픽을 백 엔드로 보냅니다.
- 각 백 엔드에 할당된 가중치에 따라 트래픽을 배포하는 **가중 방식의 라운드 로빈 라우팅**
- **지리적 라우팅** 은 특정 지역에서 가져온 요청이 해당 지역에 매핑된 백 엔드에서 처리되도록 합니다. (예를 들어 스페인의 모든 요청은 프랑스 중앙 Azure 지역으로 전달되어야 합니다.)
- **서브넷 라우팅** 을 사용하면 IP 주소 범위를 백 엔드에 매핑할 수 있으므로 해당 IP에 대한 수신 요청이 특정 백 엔드로 전송됩니다. (예를 들어 회사 HQ의 IP 주소 범위에서 연결하는 모든 사용자는 일반 사용자와 다른 웹 콘텐츠를 가져와야 합니다.)

클라이언트에서 해당 백 엔드에 직접 연결합니다. Azure Traffic Manager는 백 엔드에서 비정상적인 경우를 감지한 다음, 클라이언트를 다른 정상적인 인스턴스로 리디렉션합니다. 서비스에 대한 자세한 내용은 [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) 설명서를 참조하세요.

**Azure Front Door** 는 글로벌 HTTP 부하 분산을 포함하여 DSA(동적 웹 사이트 가속)를 제공합니다.  지정된 호스트 이름, URL 경로 및 구성된 규칙에 대해 가장 가까운 서비스 백 엔드/지역으로 들어오는 HTTP 요청 경로를 확인합니다.  
Front Door는 Microsoft의 네트워크 에지에서 HTTP 요청을 종료하고, 적극적으로 검색하여 애플리케이션이나 인프라 상태 또는 대기 시간 변경을 감지합니다.  Front Door는 항상 트래픽을 가장 빠르고 사용 가능한(정상) 백 엔드로 라우팅합니다. 서비스에 대한 자세한 내용은 Front Door의 [라우팅 아키텍처](front-door-routing-architecture.md) 세부 정보 및 [트래픽 라우팅 방법](front-door-routing-methods.md)을 참조하세요.

## <a name="regional-load-balancing"></a>지역 부하 분산
Application Gateway는 ADC(Application Delivery Controller)를 서비스로 제공하여 애플리케이션에 다양한 계층 7 부하 분산 기능을 제공합니다. 따라서 고객은 애플리케이션 게이트웨이에 CPU 집약적인 TLS 종료를 오프로드하여 웹 팜 생산성을 최적화할 수 있습니다. Layer 7의 기타 추가 라우팅 기능으로 들어오는 트래픽의 라운드 로빈 배포, 쿠키 기반 세션 선호도, URL 패스 기반 라우팅 및 단일 Application Gateway 뒤에 여러 웹 사이트를 호스팅할 수 있는 기능 등을 포함합니다. Application Gateway는 인터넷 연결 엔드포인트, 내부 전용 엔드포인트 또는 둘의 조합으로 구성될 수 있습니다. Application Gateway는 완전히 Azure에서 관리되므로 확장성 및 고가용성을 제공합니다. 관리 효율성을 향상시키기 위한 풍부한 진단 및 로깅 기능을 제공합니다.

Load Balancer는 Azure SDN 스택의 필수적인 부분으로, 모든 UDP 및 TCP 프로토콜에 대해 대기 시간이 짧은 고성능 계층 4 부하 분산 서비스를 제공합니다. 백 엔드 풀에 인바운드 연결을 매핑하는 규칙을 정의하여 공용 또는 내부 부하 분산 엔드포인트를 구성할 수 있습니다. TCP 또는 HTTPS를 사용한 상태 프로빙 모니터링을 통해 서비스 가용성을 관리하는 데 도움이 됩니다.

## <a name="choosing-a-global-load-balancer"></a>글로벌 부하 분산 장치 선택
글로벌 부하 분산을 위해 Traffic Manager와 Azure Front Door 사이에 글로벌 부하 분산 장치를 선택하는 경우 두 서비스의 유사점과 차이점을 고려해야 합니다.   두 서비스 모두에서 제공하는 기능은 다음과 같습니다.
- **다중 지역 중복**: 한 지역이 가동 중단되면 애플리케이션 소유자의 개입 없이 트래픽이 가장 가까운 지역으로 원활하게 라우팅됩니다.
- **가장 가까운 지역 라우팅:** 트래픽이 가장 가까운 지역으로 자동으로 라우팅됩니다.

</br>다음 표에서는 Traffic Manager와 Azure Front Door의 차이점을 설명합니다.</br>

| Traffic Manager | Azure Front Door |
| --------------- | ------------------------ |
|**모든 프로토콜:** Traffic Manager는 DNS 계층에서 작동하므로 모든 유형의 네트워크 트래픽(HTTP, TCP, UDP 등)을 라우팅할 수 있습니다. | **HTTP 가속:** Front Door 트래픽이 Microsoft Edge 네트워크에서 프록시됩니다. HTTP/S 요청에는 TLS 협상 대기 시간을 줄이는 대기 시간 및 처리량 개선이 표시됩니다.|
|**온-프레미스 라우팅:** DNS 계층에서 라우팅을 사용하여 트래픽이 항상 지점 간에 이동합니다.  지점에서 온-프레미스 데이터 센터로 라우팅하면 직접 경로를 사용할 수 있으며, 심지어 Traffic Manager를 사용하여 사용자 고유의 네트워크에서도 사용할 수 있습니다. | **독립적인 확장성:** Front Door는 HTTP 요청과 함께 작동하기 때문에 다른 URL 경로에 대한 요청은 규칙과 각 애플리케이션 마이크로 서비스의 상태에 따라 다른 백 엔드 / 지역 서비스 풀(마이크로 서비스)로 라우팅될 수 있습니다.|
|**청구 형식:** DNS 기반 청구는 사용자에 따라 확장되고 서비스의 경우 더 많은 사용자로 확장되어 사용량이 늘어나지만 비용을 줄일 수 있습니다. |**인라인 보안:** Front Door는 속도 제한 및 IP ACL과 같은 규칙을 사용하여 트래픽이 애플리케이션에 도달하기 전에 백 엔드를 보호할 수 있습니다. 

</br>HTTP가 Front Door와 함께 작동하는 성능, 운용성 및 보안 이점 때문에 고객이 HTTP 워크로드에 Front Door를 사용하는 것이 좋습니다. Traffic Manager와 Front Door를 병렬로 사용하여 애플리케이션의 모든 트래픽을 처리할 수 있습니다. 

## <a name="building-with-azures-application-delivery-suite"></a>Azure 애플리케이션 배달 제품군을 통해 빌드 
가능하면 항상 가장 가까운 위치에서 사용자에게 트래픽을 전달할 수 있도록 모든 웹 사이트, API, 서비스를 지리적으로 중복하는 것이 좋습니다.  여러 부하 분산 서비스를 결합하면 지리적 및 영역 중복성을 빌드하여 안정성과 성능을 최대화할 수 있습니다.

다음 다이어그램에서는 이러한 모든 서비스의 조합을 사용하여 전역 웹 서비스를 빌드하는 예제 아키텍처를 설명합니다. 아키텍처는 Traffic Manager를 사용하여 트래픽을 파일 및 개체 전달을 위한 글로벌 백 엔드로 라우팅하고 Front Door를 사용하여 전역적으로 /store/* 패턴과 일치하는 URL 경로를 App Service로 마이그레이션한 서비스로 라우팅하고 다른 모든 요청은 지역 Application Gateway로 라우팅합니다.

IaaS 서비스의 각 지역에서 애플리케이션 개발자는 /images/* 패턴과 일치하는 모든 URL이 VM의 전용 풀에서 제공되도록 결정했습니다. 이 VM 풀은 웹 팜의 나머지 부분과 다릅니다.

또한 동적 콘텐츠를 제공하는 기본 VM 풀은 고가용성 클러스터에 호스트되는 백 엔드 데이터베이스와 통신해야 합니다. 전체 배포는 Azure Resource Manager를 통해 구성됩니다.

다음 다이어그램은 이 시나리오의 아키텍처를 보여 줍니다.

:::image type="content" source="./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png" alt-text="애플리케이션 배달 제품군에 대한 세부 아키텍처":::

> [!NOTE]
> 이 예제는 Azure에서 제공하는 부하 분산 서비스의 다양한 구성 중 하나에 불과합니다. Traffic Manager, Front Door, Application Gateway 및 Load Balancer는 부하 분산 요구 사항에 가장 적합하게 혼합하여 일치시킬 수 있습니다. 예를 들어, TLS/SSL 오프로드 또는 Layer 7 처리가 필요 없는 경우 부하 분산 장치 Application Gateway 대신에 Load Balancer를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는](quickstart-create-front-door.md) 방법을 알아봅니다.
- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.
