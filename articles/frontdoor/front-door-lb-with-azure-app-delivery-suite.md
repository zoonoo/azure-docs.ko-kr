---
title: Azure의 응용 프로그램 제공 도구 모음을 사용 하 여 azure 전면 도어-부하 분산 | Microsoft Docs
description: 이 문서는 Azure에서 응용 프로그램 배달 도구 모음으로 부하 분산을 권장 하는 방법을 배우는 데 도움이 됩니다.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 50e047325ad17710794b1640715ab1938373fe85
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91542175"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>Azure 애플리케이션 배달 제품군을 통해 부하 분산

## <a name="introduction"></a>소개
Microsoft Azure는 네트워크 트래픽이 분산 되 고 부하가 분산 되는 방식을 관리 하는 다양 한 글로벌 및 지역 서비스를 제공 합니다. 

* Application Gateway
* Front Door 
* Load Balancer  
* Traffic Manager

Azure의 많은 지역 및 영역 아키텍처와 함께 이러한 서비스를 함께 사용 하면 강력 하 고 확장 가능 하며 고성능 응용 프로그램을 빌드할 수 있습니다.

:::image type="content" source="./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png" alt-text="애플리케이션 배달 제품군":::
 
이러한 서비스는 두 가지 범주로 구분됩니다.
1. Traffic Manager 및 전방 도어와 같은 **전역 부하 분산 서비스** 는 클라우드 및 하이브리드 온-프레미스 서비스에서 최종 사용자의 트래픽을 분산 합니다. 전역 부하 분산은 트래픽을 가장 가까운 서비스 백엔드로 라우팅하고, 서비스 안정성 변경에 반응 하 여 사용자에 게 항상 사용 가능한 고가용성 및 고성능을 유지 합니다. 
1. 부하 분산 장치 및 응용 프로그램 게이트웨이와 같은 **지역 부하 분산 서비스** 는 가상 네트워크 (vnet) 내의 가상 머신 (vm) 또는 지역 내의 서비스 끝점에 트래픽을 분산 하는 기능을 제공 합니다.

이러한 글로벌 및 지역 서비스를 결합 하는 경우 응용 프로그램은 최종 사용자에 게 IaaS, PaaS 또는 온-프레미스 서비스로 전송 되는 안정적이 고 안전한 종단 간 트래픽을 활용 합니다. 다음 섹션에서는 이러한 각 서비스에 대해 설명합니다.

## <a name="global-load-balancing"></a>전역 부하 분산
**Traffic Manager**는 전역 DNS 부하 분산을 제공 합니다. 들어오는 DNS 요청을 확인 하 고 고객이 선택한 라우팅 정책에 따라 정상 백엔드를 사용 하 여 응답 합니다. 라우팅 메서드의 옵션은 다음과 같습니다.
- * * 성능 라우팅은 최소 대기 시간을 사용 하 여 가장 가까운 백 엔드로 요청을 보냅니다.
- **우선 순위 라우팅은** 백 엔드에 모든 트래픽을 전달 하 고 다른 백 엔드를 백업으로 보냅니다.
- **가중치가 적용 된 라운드 로빈 라우팅은** 각 백 엔드에 할당 된 가중치에 따라 트래픽을 분산 합니다.
- **지리적 라우팅은** 특정 지리적 지역에서 발생 하는 요청이 해당 지역에 대해 매핑된 백 엔드에 의해 처리 되도록 합니다. 예를 들어 스페인의 모든 요청은 프랑스 중앙 Azure 지역으로 전달 되어야 합니다.
- **서브넷 라우팅을** 사용 하면 ip 주소 범위를 백 엔드에 매핑하여 해당 ip에 대 한 들어오는 요청이 특정 백 엔드로 전송 될 수 있습니다. 예를 들어 회사 사령부의 IP 주소 범위에서 연결 하는 모든 사용자는 일반 사용자와는 다른 웹 콘텐츠를 가져와야 합니다.

클라이언트에서 해당 백 엔드에 직접 연결합니다. Azure Traffic Manager는 백 엔드에서 비정상적인 경우를 감지한 다음, 클라이언트를 다른 정상적인 인스턴스로 리디렉션합니다. 서비스에 대한 자세한 내용은 [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) 설명서를 참조하세요.

**Azure 전면 도어** 는 글로벌 HTTP 부하 분산을 포함 하 여 DSA (동적 웹 사이트 가속)를 제공 합니다.  지정된 호스트 이름, URL 경로 및 구성된 규칙에 대해 가장 가까운 서비스 백 엔드/지역으로 들어오는 HTTP 요청 경로를 확인합니다.  
Front Door는 Microsoft의 네트워크 에지에서 HTTP 요청을 종료하고, 적극적으로 검색하여 애플리케이션이나 인프라 상태 또는 대기 시간 변경을 감지합니다.  Front Door는 항상 트래픽을 가장 빠르고 사용 가능한(정상) 백 엔드로 라우팅합니다. 서비스에 대한 자세한 내용은 Front Door의 [라우팅 아키텍처](front-door-routing-architecture.md) 세부 정보 및 [트래픽 라우팅 방법](front-door-routing-methods.md)을 참조하세요.

## <a name="regional-load-balancing"></a>지역 부하 분산
Application Gateway는 ADC(Application Delivery Controller)를 서비스로 제공하여 애플리케이션에 다양한 계층 7 부하 분산 기능을 제공합니다. 이를 통해 고객은 응용 프로그램 게이트웨이에 CPU 집약적 TLS 종료를 오프 로드 하 여 웹 팜 생산성을 최적화할 수 있습니다. 또 다른 추가 계층 7 라우팅 기능에는 들어오는 트래픽의 라운드 로빈 배포, 쿠키 기반 세션 선호도, URL 경로 기반 라우팅 및 단일 application gateway 뒤에 여러 웹 사이트를 호스트 하는 기능이 포함 되어 있습니다. Application Gateway는 인터넷 연결 끝점, 내부 전용 끝점 또는 둘의 조합으로 구성할 수 있습니다. Application Gateway은 완전히 Azure에서 관리 되므로 확장성 및 고가용성을 제공 합니다. 관리 효율성을 향상시키기 위한 풍부한 진단 및 로깅 기능을 제공합니다.

부하 분산 장치는 모든 UDP 및 TCP 프로토콜에 대 한 고성능의 대기 시간이 짧은 계층 4 부하 분산 서비스를 제공 하는 Azure SDN 스택의 필수적인 부분입니다. 백 엔드 풀에 인바운드 연결을 매핑하는 규칙을 정의 하 여 공용 또는 내부 부하 분산 끝점을 구성할 수 있습니다. TCP 또는 HTTPS를 사용 하 여 상태 검색 모니터링을 통해 서비스 가용성을 관리 하는 데 도움이 됩니다.

## <a name="choosing-a-global-load-balancer"></a>글로벌 부하 분산 장치 선택
글로벌 부하 분산을 위해 Traffic Manager와 Azure Front Door 사이에 글로벌 부하 분산 장치를 선택하는 경우 두 서비스의 유사점과 차이점을 고려해야 합니다.   두 서비스 모두에서 제공하는 기능은 다음과 같습니다.
- **다중 지역 중복:** 한 지역이 서비스에서 벗어나면 트래픽이 응용 프로그램 소유자의 개입 없이 가장 가까운 지역으로 원활 하 게 라우팅됩니다.
- **가장 가까운 지역 라우팅:** 트래픽이 가장 가까운 지역으로 자동으로 라우팅됩니다.

</br>다음 표에서는 Traffic Manager와 Azure Front 도어 간의 차이점을 설명 합니다.</br>

| Traffic Manager | Azure Front Door |
| --------------- | ------------------------ |
|**모든 프로토콜:** Traffic Manager DNS 계층에서 작동 하므로 모든 유형의 네트워크 트래픽을 라우팅할 수 있습니다. HTTP, TCP, UDP 등이 있습니다. | **HTTP 가속:** 전면 도어를 사용 하면 트래픽이 Microsoft 네트워크의에 지에서 프록시 됩니다. HTTP/S 요청에는 대기 시간 및 처리량 향상이 표시 되므로 TLS 협상의 대기 시간이 줄어듭니다.|
|**온-프레미스 라우팅:** DNS 계층에서 라우팅을 사용 하면 트래픽은 항상 지점에서 지점으로 이동 합니다.  지점에서 온-프레미스 데이터 센터로 라우팅하는 경우에는 직접 경로를 사용할 수 있습니다. Traffic Manager를 사용 하는 사용자 고유의 네트워크에도 해당 합니다. | **독립 확장성:** 프런트 도어가 HTTP 요청에서 작동 하므로 다른 URL 경로에 대 한 요청은 규칙 및 각 응용 프로그램 마이크로 서비스의 상태에 따라 서로 다른 백 엔드/지역 서비스 풀 (마이크로 서비스)으로 라우팅될 수 있습니다.|
|**청구 형식:** DNS 기반 청구는 사용자에 따라 확장되고 서비스의 경우 더 많은 사용자로 확장되어 사용량이 늘어나지만 비용을 줄일 수 있습니다. |**인라인 보안:** Front Door는 속도 제한 및 IP ACL과 같은 규칙을 사용하여 트래픽이 애플리케이션에 도달하기 전에 백 엔드를 보호할 수 있습니다. 

</br>HTTP가 Front 도어를 사용 하는 경우 성능, 운용성 및 보안상의 이점 때문에 고객이 HTTP 작업에 대해 앞 도어를 사용 하는 것이 좋습니다. Traffic Manager와 Front Door를 병렬로 사용하여 애플리케이션의 모든 트래픽을 처리할 수 있습니다. 

## <a name="building-with-azures-application-delivery-suite"></a>Azure 애플리케이션 배달 제품군을 통해 빌드 
가능 하면 항상 가장 가까운 위치에서 사용자에 게 트래픽을 제공할 수 있도록 모든 websites, Api, 서비스를 지리적으로 중복 하는 것이 좋습니다.  여러 부하 분산 서비스를 결합 하면 지리적 및 영역 중복성을 빌드하여 안정성과 성능을 최대화할 수 있습니다.

다음 다이어그램에서는 이러한 모든 서비스의 조합을 사용 하 여 전역 웹 서비스를 구축 하는 예제 아키텍처를 설명 합니다. 아키텍처는 파일 및 개체 배달을 위해 트래픽을 글로벌 백 엔드로 라우팅하는 Traffic Manager 사용 했습니다. Front 도어를 사용 하는 동안, App Service로 마이그레이션한 서비스에 대 한 패턴/a s/*와 일치 하는 URL 경로를 전역적으로 라우팅합니다. 마지막으로 지역 응용 프로그램 게이트웨이에 대 한 다른 모든 요청을 라우팅합니다.

IaaS 서비스의 각 지역에서 응용 프로그램 개발자는/images/* 패턴과 일치 하는 모든 Url이 Vm 전용 풀에서 제공 되도록 결정 했습니다. 이 Vm 풀은 웹 팜의 나머지 부분과 다릅니다.

또한 동적 콘텐츠를 제공하는 기본 VM 풀은 고가용성 클러스터에 호스트되는 백 엔드 데이터베이스와 통신해야 합니다. 전체 배포는 Azure Resource Manager를 통해 구성 됩니다.

다음 다이어그램은 이 시나리오의 아키텍처를 보여 줍니다.

:::image type="content" source="./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png" alt-text="애플리케이션 배달 제품군":::

> [!NOTE]
> 이 예제는 Azure에서 제공하는 부하 분산 서비스의 다양한 구성 중 하나에 불과합니다. Traffic Manager, Front Door, Application Gateway 및 Load Balancer는 부하 분산 요구 사항에 가장 적합하게 혼합하여 일치시킬 수 있습니다. 예를 들어 TLS/SSL 오프 로드 또는 계층 7 처리가 필요 하지 않은 경우 Application Gateway 대신 Load Balancer를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는](quickstart-create-front-door.md) 방법을 알아봅니다.
- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.
