---
title: Azure Traffic Manager - 트래픽 라우팅 방법
description: 이 문서는 Traffic Manager에서 사용 하는 다양 한 트래픽 라우팅 방법을 이해 하는 데 도움이 됩니다.
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/21/2021
ms.author: duau
ms.openlocfilehash: eeebded128f636ecba2e4e0dab1bc2f0632aaa6a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98730975"
---
# <a name="traffic-manager-routing-methods"></a>Traffic Manager 라우팅 방법

Azure Traffic Manager는 다양한 서비스 엔드포인트에 네트워크 트래픽을 라우팅하는 방법을 결정하는 6가지 트래픽 라우팅 방법을 지원합니다. 모든 프로필에 대해, Traffic Manager는 관련된 트래픽 라우팅 메서드를 수신한 각 DNS 쿼리에 적용합니다. 트래픽 라우팅 메서드는 DNS 응답에서 반환된 엔드포인트를 결정합니다.

Traffic Manager에서 다음과 같은 트래픽 라우팅 방법을 사용할 수 있습니다.

* **[우선 순위](#priority-traffic-routing-method):** 모든 트래픽에 대해 기본 서비스 끝점을 사용할 때 **우선 순위** 라우팅을 선택 합니다. 주 또는 백업 끝점 중 하나를 사용할 수 없는 경우 여러 백업 끝점을 제공할 수 있습니다.
* **가 중: [](#weighted)** 가중치에 따라 일련의 끝점에 트래픽을 분산 하려는 경우 **가중치가** 적용 된 라우팅을 선택 합니다. 모든 끝점에서 균등 하 게 분산 하려면 가중치를 동일 하 게 설정 합니다.
* **[성능](#performance):** 끝점이 서로 다른 지리적 위치에 있고 최종 사용자가 가장 낮은 네트워크 대기 시간에 "가장 가까운" 끝점을 사용 하도록 하려는 경우 **성능** 라우팅을 선택 합니다.
* **[지리적](#geographic):** DNS 쿼리가 지리적으로 발생 하는 위치에 따라 특정 끝점 (Azure, 외부 또는 중첩)으로 사용자를 안내할 **지리적** 라우팅을 선택 합니다. 이 라우팅 방법을 사용 하면 데이터 주권 요구 사항, 콘텐츠 지역화 & 사용자 환경, 다른 지역의 트래픽 측정 등의 시나리오를 준수할 수 있습니다.
* **[다중값](#multivalue):** 엔드포인트로 IPv4/IPv6 주소만 사용할 수 있는 Traffic Manager 프로필의 경우 **다중값** 을 선택합니다. 이 프로필에 대해 쿼리가 수신되면 정상 상태의 모든 엔드포인트가 반환됩니다.
* **[서브넷](#subnet):** **서브넷** 트래픽 라우팅 메서드를 선택 하 여 최종 사용자 IP 주소 범위 집합을 특정 끝점에 매핑합니다. 요청이 수신되면 해당 요청의 원본 IP 주소에 대해 매핑될 엔드포인트가 반환됩니다. 


모든 Traffic Manager 프로필에는 상태 모니터링 및 끝점의 자동 장애 조치 (failover)가 있습니다. 자세한 내용은 [Traffic Manager 엔드포인트 모니터링](traffic-manager-monitoring.md)을 참조하세요. Traffic Manager 프로필 내에서 한 번에 하나의 트래픽 라우팅 메서드만 구성할 수 있습니다. 언제든지 프로필에 대해 다른 트래픽 라우팅 방법을 선택할 수 있습니다. 가동 중지 시간 없이 1 분 내에 변경 내용이 적용 됩니다. 중첩 된 Traffic Manager 프로필을 사용 하 여 트래픽 라우팅 방법을 결합할 수 있습니다. 중첩 프로필을 사용 하면 더 크고 복잡 한 응용 프로그램의 요구를 충족 하는 정교한 트래픽 라우팅 구성을 사용할 수 있습니다. 자세한 내용은 [중첩 Traffic Manager 프로필](traffic-manager-nested-profiles.md)을 참조하세요.

## <a name="priority-traffic-routing-method"></a>우선 순위 트래픽 라우팅 방법

조직에서 서비스에 대 한 안정성을 제공 하려는 경우가 종종 있습니다. 이렇게 하려면 주 복제본이 다운 된 경우에 하나 이상의 백업 서비스를 배포 합니다. '우선 순위' 트래픽 라우팅 메서드를 사용하여 Azure 고객은 이러한 장애 조치(Failover) 패턴을 쉽게 구현할 수 있습니다.

![Azure Traffic Manager '우선 순위' 트래픽 라우팅 메서드](media/traffic-manager-routing-methods/priority.png)

Traffic Manager 프로필은 우선순위로 정렬된 서비스 엔드포인트 목록을 포함합니다. 기본적으로 Traffic Manager는 모든 트래픽을 기본(가장 높은 우선 순위) 엔드포인트로 전송합니다. 기본 엔드포인트를 사용할 수 없는 경우 Traffic Manager는 두 번째 엔드포인트에 트래픽을 라우팅합니다. 기본 끝점과 보조 끝점을 사용할 수 없는 경우 트래픽은 세 번째 끝점으로 이동 하는 방식으로 진행 됩니다. 엔드포인트의 가용성은 구성된 상태(사용 또는 사용 안 함) 및 지속적인 엔드포인트 모니터링을 기반으로 합니다.

### <a name="configuring-endpoints"></a>엔드포인트 구성

Azure Resource Manager에서 각 엔드포인트에 대해 '우선 순위' 속성을 사용하여 엔드포인트 우선 순위를 명시적으로 구성합니다. 이 속성은 1에서 1000 사이의 값입니다. 값이 낮을수록 높은 우선 순위를 나타냅니다. 끝점은 우선 순위 값을 공유할 수 없습니다. 이 속성을 설정하는 작업은 선택 사항입니다. 생략하면 엔드포인트 순서에 따른 기본 우선 순위를 사용합니다.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>가중 트래픽 라우팅 방법
'가중' 트래픽 라우팅 메서드를 사용하면 균등하게 트래픽을 분산하거나 미리 정의된 가중치를 사용할 수 있습니다.

![Azure Traffic Manager '가중' 트래픽 라우팅 메서드](media/traffic-manager-routing-methods/weighted.png)

가중 트래픽 라우팅 메서드에서는 Traffic Manager 프로필 구성에서 각 엔드포인트에 가중치를 할당합니다. 가중치는 1에서 1000 사이의 정수입니다. 이 매개 변수는 선택 사항입니다. 생략되면 Traffic Manager는 '1'이라는 기본 가중치를 사용합니다. 가중치가 높을수록 우선 순위가 높아집니다.

Traffic Manager는 수신한 각 DNS 쿼리에 대해 사용 가능한 엔드포인트를 임의로 선택합니다. 엔드포인트를 선택하는 확률은 사용 가능한 모든 엔드포인트에 할당된 가중치를 기반으로 합니다. 모든 엔드포인트 결과에서 동일한 가중치를 사용하면 균등하게 트래픽이 분포됩니다. 특정 엔드포인트에 더 높은(또는 더 낮은) 가중치를 적용하면 해당 엔드포인트가 DNS 응답에서 더(또는 덜) 자주 반환됩니다.

가중 메서드를 사용하면 다음과 같은 몇 가지 유용한 시나리오를 사용할 수 있습니다.

* 점진적 응용 프로그램 업그레이드: 새 끝점으로 라우팅할 트래픽의 비율을 지정 하 고 시간 경과에 따른 트래픽을 100%로 점진적으로 증가 시킵니다.
* Azure에 애플리케이션 마이그레이션: Azure 엔드포인트 및 외부 엔드포인트로 프로필을 만듭니다. 새 엔드포인트를 선호하도록 엔드포인트의 가중치를 조정합니다.
* 추가 용량을 위한 클라우드 버스트: 온-프레미스 배포를 Traffic Manager 프로필 뒤에 배치 하 여 클라우드로 신속 하 게 확장할 수 있습니다. 클라우드에 추가 용량이 필요한 경우 엔드포인트를 더 추가하거나 사용하도록 설정하고 각 엔드포인트로 전송되는 트래픽 양을 지정할 수 있습니다.

Azure Portal, Azure PowerShell, CLI 또는 REST Api를 사용 하 여 가중치를 구성할 수 있습니다.

클라이언트에서 DNS 응답을 캐시 한다는 점을 명심 해야 합니다. 또한 클라이언트에서 DNS 이름을 확인 하는 데 사용 하는 재귀 DNS 서버에 의해 캐시 됩니다. 이 캐싱은 가중치가 적용 된 트래픽 분포에 영향을 미칠 수 있습니다. 클라이언트 및 재귀 DNS 서버의 수가 큰 경우 트래픽 분산은 예상대로 작동합니다. 그러나 클라이언트 또는 재귀 DNS 서버의 수가 적을 경우 캐싱은 트래픽 배포를 상당히 왜곡시킬 수 있습니다.

일반 사용 사례는 다음과 같습니다.

* 개발 및 테스트 환경
* 애플리케이션 간 통신
* 일반적인 재귀 DNS 인프라를 공유하는 좁은 사용자 기반을 목표로 하는 애플리케이션(예: 프록시를 통해 연결하는 회사의 직원)

이러한 DNS 캐싱 효과는 Azure Traffic Manager만이 아니라 모든 DNS 기반 트래픽 라우팅 시스템에 공통적으로 적용됩니다. 경우에 따라 DNS 캐시를 명시적으로 지우면 문제가 해결될 수도 있습니다. 작동 하지 않는 경우 대체 트래픽 라우팅 방법이 더 적합할 수 있습니다.

## <a name="performance-traffic-routing-method"></a><a name = "performance"></a>성능 트래픽 라우팅 방법

전 세계적으로 둘 이상의 위치에 끝점을 배포 하면 응용 프로그램의 응답성을 향상 시킬 수 있습니다. ' 성능 ' 트래픽 라우팅 방법을 사용 하면 트래픽을 ' 가장 가까운 ' 위치로 라우팅할 수 있습니다.

![Azure Traffic Manager '성능' 트래픽 라우팅 메서드](media/traffic-manager-routing-methods/performance.png)

' 가장 가까운 ' 끝점은 지리적 거리를 기준으로 가장 가까운 것은 아닙니다. 대신, '성능' 트래픽 라우팅 메서드는 네트워크 대기 시간을 측정하여 가장 가까운 엔드포인트를 결정합니다. Traffic Manager에는 IP 주소 범위와 각 Azure 데이터 센터 간의 왕복 시간을 추적하는 인터넷 대기 시간 테이블이 있습니다.

Traffic Manager는 인터넷 대기 시간 테이블에서 들어오는 DNS 요청의 원본 IP 주소를 찾습니다. 그런 다음, Azure 데이터 센터에서 해당 IP 주소 범위에 대 한 대기 시간이 가장 짧은 사용 가능한 끝점을 선택 Traffic Manager 합니다. 그런 다음 Traffic Manager는 DNS 응답에서 해당 끝점을 반환 합니다.

[Traffic Manager 작동 방식](traffic-manager-how-it-works.md)에 설명 된 대로 Traffic Manager는 클라이언트에서 직접 DNS 쿼리를 받지 않습니다. 대신 DNS 쿼리는 클라이언트에서 사용 하도록 구성 된 재귀 DNS 서비스에서 제공 됩니다. 따라서 ' 가장 가까운 ' 끝점을 결정 하는 데 사용 되는 IP 주소는 클라이언트의 IP 주소가 아니라 재귀 DNS 서비스의 IP 주소입니다. 이 IP 주소는 클라이언트에 적합 한 프록시입니다.


Traffic Manager는 인터넷 대기 시간 테이블을 정기적으로 업데이트하여 전세계 인터넷 및 새 Azure 지역에서 변경 내용을 고려합니다. 그러나 애플리케이션 성능은 인터넷을 통한 부하에서 실시간 변형에 따라 달라 집니다. 성능 트래픽 라우팅은 지정 된 서비스 끝점에 대 한 부하를 모니터링 하지 않습니다. 끝점을 사용할 수 없게 되 면 Traffic Manager DNS 쿼리 응답에 포함 되지 않습니다.


주의할 사항:

* 프로필이 동일한 Azure 지역에서 여러 엔드포인트를 포함하는 경우 Traffic Manager는 트래픽을 해당 지역에서 사용할 수 있는 엔드포인트에 균등하게 분산합니다. 지역 내에서 다른 트래픽 분산을 원하는 경우 [중첩 Traffic Manager 프로필](traffic-manager-nested-profiles.md)을 사용할 수 있습니다.
* 가장 가까운 Azure 지역에서 활성화된 모든 엔드포인트의 성능이 저하되면 Traffic Manager가 다음으로 가장 가까운 Azure 지역의 엔드포인트로 트래픽을 이동합니다. 기본 장애 조치 순서를 정의하려는 경우 [중첩된 Traffic Manager 프로필](traffic-manager-nested-profiles.md)을 사용합니다.
* 외부 엔드포인트 또는 중첩 엔드포인트에서 성능 트래픽 라우팅 방법을 사용하는 경우 해당 엔드포인트의 위치를 지정해야 합니다. 배포에 가장 가까운 Azure 지역을 선택합니다. 이러한 위치는 인터넷 대기 시간 테이블에서 지원되는 값입니다.
* 엔드포인트를 선택하는 알고리즘은 결정적입니다. 동일한 클라이언트에서 반복되는 DNS 쿼리는 동일한 엔드포인트로 전달됩니다. 일반적으로 클라이언트는 이동할 때 다른 재귀 DNS 서버를 사용합니다. 클라이언트는 다른 엔드포인트로 라우팅될 수 있습니다. 라우팅은 인터넷 대기 시간 테이블의 업데이트에 의해서도 영향을 받을 수 있습니다. 따라서 성능 트래픽 라우팅 메서드는 클라이언트가 항상 동일한 끝점으로 라우팅되는 것을 보장 하지 않습니다.
* 인터넷 대기 시간 표가 변경될 경우 일부 클라이언트가 다른 엔드포인트로 보내진다는 것을 알 수 있습니다. 이 라우팅 변경은 현재 대기 시간 데이터에 따라 더 정확 합니다. 인터넷이 계속해서 진화함에 따라, 이러한 업데이트는 성능 트래픽 라우팅의 정확성을 유지하는 데 필수적입니다.

## <a name="geographic-traffic-routing-method"></a><a name = "geographic"></a>지리적 트래픽 라우팅 방법

사용자가 해당 DNS 쿼리가 시작 된 지리적 위치에 따라 특정 끝점 (Azure, External 또는 Nested)에 전달 되도록 Traffic Manager 프로필은 지리적 라우팅 방법을 사용 하도록 구성할 수 있습니다. 이 라우팅 방법을 사용 하면 데이터 주권 조건을 준수 하 고, 사용자 환경 & 콘텐츠를 지역화 하 고, 다른 지역의 트래픽을 측정 하는 데 사용할 수 있습니다.
프로필이 지리적 라우팅에 구성된 경우 해당 프로필과 연결된 각 엔드포인트에는 거기에 할당된 지리적 지역이 있어야 합니다. 지리적 지역은 다음과 같은 수준으로 세분화될 수 있습니다. 
- 세계–모든 지역
- 지역 그룹화 - 예: 아프리카, 중동, 오스트레일리아/태평양 등 
- 국가/지역 - 예: 아일랜드, 페루, 홍콩 특별 행정구 등 
- 시/도 - 예: 미국-캘리포니아, 오스트레일리아-퀸즐랜드, 캐나다-앨버타 등(참고: 이 세분성 수준은 오스트레일리아, 캐나다 및 미국의 주/지방에서만 지원됨).

지역 또는 지역 집합이 끝점에 할당 되 면 해당 지역의 모든 요청은 해당 끝점 으로만 라우팅됩니다. Traffic Manager는 DNS 쿼리의 원본 IP 주소를 사용 하 여 사용자가 쿼리 하는 위치의 지역을 결정 합니다. 일반적으로 사용자에 대 한 쿼리를 수행 하는 로컬 DNS 확인 자의 IP 주소입니다.  

![Azure Traffic Manager '지리적' 트래픽 라우팅 메서드](./media/traffic-manager-routing-methods/geographic.png)

Traffic Manager는 DNS 쿼리의 원본 IP 주소를 읽고 해당 지역에서 원본으로 사용할 지역을 결정 합니다. 그런 다음이 지리적 지역이 매핑된 끝점이 있는지 확인 합니다. 이 조회는 가장 낮은 세분성 수준 (지원 되는 경우 시/도, 국가/지역 수준)에서 시작 되 고 최고 수준 ( **세계**)까지 모든 방향으로 이동 합니다. 이 순회를 사용 하 여 발견 된 첫 번째 일치 항목이 쿼리 응답에서 반환할 끝점으로 선택 됩니다. 중첩 형식 엔드포인트와 일치하는 경우 해당 라우팅 방법을 기준으로 자식 프로필 내의 엔드포인트가 반환됩니다. 다음 사항을 이러한 동작에 사용할 수 있습니다.

- 지리적 지역은 라우팅 형식이 지리적 라우팅일 경우 Traffic Manager 프로필에서 엔드포인트에만 매핑될 수 있습니다. 이러한 제한을 통해 사용자의 라우팅이 결정적이 고 고객이 명확한 지리적 경계가 필요한 시나리오를 사용할 수 있습니다.
- 사용자 지역이 두 개의 다른 끝점의 지리적 매핑 아래에 나열 되는 경우 Traffic Manager 세분성이 가장 낮은 끝점을 선택 합니다. Traffic Manager는 해당 지역에서 다른 끝점으로의 라우팅 요청을 고려 하지 않습니다. 예를 들어 Endpoint1과 Endpoint2라는 두 개의 엔드포인트가 있는 지리적 라우팅 형식 프로필을 고려해 보세요. 엔드포인트1은 아일랜드에서 트래픽을 수신하도록 구성되고 엔드포인트2는 유럽에서 트래픽을 수신하도록 구성되어 있습니다. 요청이 아일랜드에서 시작 되 면 항상 Endpoint1로 라우팅됩니다.
- 지역은 하나의 끝점에만 매핑될 수 있으므로 Traffic Manager는 끝점이 정상 인지 여부에 관계 없이 응답을 반환 합니다.

    >[!IMPORTANT]
    >지리적 라우팅 방법을 사용하는 고객은 각각에 두 개 이상의 엔드포인트가 포함된 자식 프로필이 있는 중첩 형식 엔드포인트와 연결하는 것이 좋습니다.
- 일치하는 엔드포인트가 있고 해당 엔드포인트가 **중지된** 상태이면 Traffic Manager는 NODATA 응답을 반환합니다. 이 경우 지리적 지역 계층 구조의 상위 수준에서 더 이상 조회가 수행되지 않습니다. 이 동작은 하위 프로필이 **중지됨** 또는 **비활성화** 상태인 경우 중첩 엔드포인트 유형에 적용됩니다.
- **사용 안 함** 상태로 표시되는 엔드포인트는 지역 일치 프로세스에 포함되지 않습니다. 이 동작은 엔드포인트가 **비활성화** 상태인 경우 중첩 엔드포인트 유형에도 적용됩니다.
- 쿼리가 해당 프로필에 매핑이 없는 지리적 지역에서 수행된 경우 Traffic Manager는 NODATA 응답을 반환합니다. 따라서 하나의 끝점으로 지리적 라우팅을 사용 하는 것이 좋습니다. 영역 지역이 할당 된 자식 프로필 내에서 두 개 이상의 끝점으로 **중첩 된 형식** 으로 가장 이상적입니다. 또한이 구성은 지역에 매핑되지 않는 모든 IP 주소가 처리 되도록 합니다.

[Traffic Manager 작동 방식](traffic-manager-how-it-works.md)에 설명 된 대로 Traffic Manager는 클라이언트에서 직접 DNS 쿼리를 받지 않습니다. DNS 쿼리는 클라이언트에서 사용 하도록 구성 된 재귀 DNS 서비스에서 제공 됩니다. 따라서 지역을 결정 하는 데 사용 되는 IP 주소는 클라이언트의 IP 주소가 아니라 재귀 DNS 서비스의 IP 주소입니다. 이 IP 주소는 클라이언트에 적합 한 프록시입니다.

### <a name="faqs"></a>FAQ

* [지리적 라우팅이 유용한 사용 사례에는 어떤 것이 있습니까?](./traffic-manager-faqs.md#what-are-some-use-cases-where-geographic-routing-is-useful)

* [성능 라우팅 방법 또는 지리적 라우팅 방법을 사용해야 하는지를 결정하려면 어떻게 할까요?](./traffic-manager-faqs.md#how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method)

* [지리적 라우팅에 대해 Traffic Manager에서 지원되는 지역은 어디입니까?](./traffic-manager-faqs.md#what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing)

* [Traffic Manager는 어떻게 사용자가 쿼리하는 위치를 결정합니까?](./traffic-manager-faqs.md#how-does-traffic-manager-determine-where-a-user-is-querying-from)

* [Traffic Manager가 모든 경우에 사용자의 정확한 지리적 위치를 올바르게 결정한다고 보장할 수 있나요?](./traffic-manager-faqs.md#is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case)

* [끝점을 지리적 라우팅에 대해 구성 된 것과 동일한 지역에 물리적으로 배치 해야 하나요?](./traffic-manager-faqs.md#does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing)

* [지리적 라우팅을 수행 하도록 구성 되지 않은 프로필의 끝점에 지리적 지역을 할당할 수 있나요?](./traffic-manager-faqs.md#can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing)

* [기존 프로필의 라우팅 방법을 지리적으로 변경하려고 시도하면 오류가 발생하는 이유는 무엇인가요?](./traffic-manager-faqs.md#why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic)

* [고객에게 지리적 라우팅이 활성화된 프로필의 엔드포인트보다 중첩 프로필을 만드는 것을 권장하는 이유는 무엇입니까?](./traffic-manager-faqs.md#why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled)

* [이 라우팅 형식을 지원하는 API 버전에 제한이 있습니까?](./traffic-manager-faqs.md#are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type)

## <a name="multivalue-traffic-routing-method"></a><a name = "multivalue"></a>다중값 트래픽 라우팅 방법
**다중값** 트래픽 라우팅 방법을 사용하면 단일 DNS 쿼리 응답에 정상 상태의 여러 엔드포인트를 가져올 수 있습니다. 이 구성을 통해 호출자는 반환 된 끝점이 응답 하지 않는 경우에 다른 끝점을 사용 하 여 클라이언트 쪽 재시도를 수행할 수 있습니다. 이 패턴은 서비스의 가용성을 향상시키고 새 DNS 쿼리와 관련된 대기 시간을 줄여 정상 상태의 엔드포인트를 얻을 수 있습니다. 다중값 라우팅 방법은 ‘외부’ 유형의 모든 엔드포인트가 IPv4 또는 IPv6 주소로 지정된 경우에만 작동합니다. 쿼리가 이 프로필에 대해 수신되면 정상 상태의 모든 엔드포인트가 반환되고, 구성 가능한 최대 반환 수 제한이 적용됩니다.

### <a name="faqs"></a>FAQ

* [다중값 라우팅이 유용한 사용 사례에는 어떤 것이 있습니까?](./traffic-manager-faqs.md#what-are-some-use-cases-where-multivalue-routing-is-useful)

* [다중값 라우팅이 사용될 때 얼마나 많은 엔드포인트가 반환되나요?](./traffic-manager-faqs.md#how-many-endpoints-are-returned-when-multivalue-routing-is-used)

* [다중값 라우팅이 사용될 때 동일한 엔드포인트 집합을 얻을 수 있나요?](./traffic-manager-faqs.md#will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used)

## <a name="subnet-traffic-routing-method"></a><a name = "subnet"></a>서브넷 트래픽 라우팅 방법
**서브넷** 트래픽 라우팅 방법을 사용 하면 최종 사용자 IP 주소 범위 집합을 프로필의 특정 끝점에 매핑할 수 있습니다. Traffic Manager 해당 프로필에 대 한 DNS 쿼리를 수신 하면 해당 요청의 원본 IP 주소를 검사 합니다. 그러면 매핑되는 끝점을 결정 하 고 쿼리 응답에서 해당 끝점을 반환 합니다. 대부분의 경우 원본 IP 주소는 호출자가 사용 하는 DNS 확인자입니다.

끝점에 매핑될 IP 주소는 CIDR 범위 (예: 1.2.3.0/24) 또는 주소 범위 (예: 1.2.3.4-5.6.7.8)로 지정할 수 있습니다. 끝점과 연결 된 IP 범위는 해당 프로필 내에서 고유 해야 합니다. 주소 범위는 동일한 프로필에 있는 다른 끝점의 IP 주소 집합과 겹칠 수 없습니다.
주소 범위가 없는 엔드포인트를 정의하는 경우 해당 엔드포인트는 대체(fallback)용으로 사용되어 남아 있는 서브넷에서 트래픽을 가져옵니다. 대체(fallback) 엔드포인트가 포함되지 않으면 Traffic Manager는 정의되지 않은 모든 범위에 대해 NODATA 응답을 보냅니다. 가능한 모든 IP 범위를 끝점에서 지정 하려면 대체 (fallback) 끝점을 정의 하는 것이 좋습니다.

특정 IP 공간에서 연결하는 사용자에게 다른 환경을 제공하기 위해 서브넷 라우팅을 사용할 수 있습니다. 예를 들어 회사 사무실의 모든 요청을 다른 끝점으로 라우팅할 수 있습니다. 이 라우팅 방법은 특히 앱의 내부 전용 버전을 테스트 하려는 경우에 유용 합니다. 또 다른 시나리오는 특정 ISP에서 연결하는 사용자에게 다른 환경을 제공하려는 경우입니다(예: 지정된 ISP의 사용자 차단).

### <a name="faqs"></a>FAQ

* [서브넷 라우팅이 유용한 사용 사례에는 어떤 것이 있습니까?](./traffic-manager-faqs.md#what-are-some-use-cases-where-subnet-routing-is-useful)

* [Traffic Manager는 최종 사용자의 IP 주소를 어떻게 알 수 있나요?](./traffic-manager-faqs.md#how-does-traffic-manager-know-the-ip-address-of-the-end-user)

* [서브넷 라우팅을 사용하는 경우 IP 주소를 어떻게 지정할 수 있나요?](./traffic-manager-faqs.md#how-can-i-specify-ip-addresses-when-using-subnet-routing)

* [서브넷 라우팅을 사용하는 경우 대체(fallback) 엔드포인트를 어떻게 지정할 수 있나요?](./traffic-manager-faqs.md#how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing)

* [서브넷 라우팅 형식 프로필에서 엔드포인트가 비활성화된 경우에는 어떻게 되나요?](./traffic-manager-faqs.md#what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile)


## <a name="next-steps"></a>다음 단계

[Traffic Manager 끝점 모니터링](traffic-manager-monitoring.md) 을 사용 하 여 고가용성 응용 프로그램을 개발 하는 방법을 알아봅니다.