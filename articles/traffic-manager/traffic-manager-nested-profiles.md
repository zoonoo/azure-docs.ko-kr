---
title: "중첩 Traffic Manager 프로필 | Microsoft Docs"
description: "이 문서에서는 Azure 트래픽 관리자의 ‘중첩 프로필’ 기능에 대해 설명합니다."
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: f1b112c4-a3b1-496e-90eb-41e235a49609
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 8827793d771a2982a3dccb5d5d1674af0cd472ce
ms.openlocfilehash: b5c49b585be10a02e959a70385d31e474e21ce01

---

# <a name="nested-traffic-manager-profiles"></a>중첩 트래픽 관리자 프로필

Traffic Manager에는 Traffic Manager가 각 최종 사용자의 트래픽을 수신할 끝점을 선택하는 방법을 제어할 수 있는 다양한 트래픽 라우팅 방법이 포함되어 있습니다. 자세한 내용은 [트래픽 관리자 트래픽 라우팅 방법](traffic-manager-routing-methods.md)을 참조하세요.

각 트래픽 관리자 프로필은 단일 트래픽 라우팅 방법을 지정합니다. 그러나 단일 Traffic Manager 프로필에서 제공하는 것보다 더 정교한 트래픽 라우팅을 요구하는 시나리오가 있습니다. 둘 이상의 트래픽 라우팅 메서드의 장점을 결합하기 위해 Traffic Manager 프로필을 중첩할 수 있습니다. 중첩된 프로필을 사용하여 더 크고 복잡한 응용 프로그램 배포를 지원하기 위해 기본 Traffic Manager 동작을 재정의할 수 있습니다.

다음 예제에서는 다양한 시나리오에서 중첩 Traffic Manager 프로필을 사용하는 방법을 보여 줍니다.

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>예제 1: '성능' 및 '가중' 트래픽 라우팅 결합

응용 프로그램이 미국 서부, 유럽 서부 및 동아시아 등의 Azure 지역에 배포되었다고 가정합니다. Traffic Manager의 '성능' 트래픽 라우팅 방법을 사용하여 사용자에게 가장 가까운 지역으로 트래픽을 분산합니다.

![단일 트래픽 관리자 프로필][1]

이제 광범위하게 롤아웃하기 전에 서비스 업데이트를 테스트하려고 한다고 가정합니다. '가중' 트래픽 라우팅 메서드를 사용하여 트래픽의 일부를 테스트 배포로 보내려고 합니다. 유럽 서부에서 기존 프로덕션 배포와 함께 테스트 배포를 설정합니다.

단일 프로필에서 '가중' 및 '성능' 트래픽 라우팅을 결합할 수 없습니다. 이 시나리오를 지원하려면 두 개의 유럽 서부 끝점과 '가중' 트래픽 라우팅 메서드를 사용하여 Traffic Manager 프로필을 만들어야 합니다. 다음으로 이 '하위' 프로필을 '상위' 프로필에 끝점으로 추가합니다. 상위 프로필에서는 성능 트래픽 라우팅 메서드를 계속 사용하고 다른 전세계 배포를 끝점으로 포함합니다.

아래 다이어그램은 이 예제를 보여 줍니다.

![중첩 트래픽 관리자 프로필][2]

이 구성에서 상위 프로필을 통해 전송된 트래픽은 트래픽을 지역에 정상적으로 분산합니다. 유럽 서부 내에서 중첩 프로필은 할당된 가중치에 따라 프로덕션 및 테스트 끝점에 트래픽을 분산시킵니다.

상위 프로필에서 '성능' 트래픽 라우팅 메서드를 사용하는 경우 각 끝점은 할당된 위치여야 합니다. 위치는 끝점을 구성할 때 할당됩니다. 배포에 가장 가까운 Azure 지역을 선택합니다. Azure 지역은 인터넷 대기 시간 테이블에서 지원되는 위치 값입니다. 자세한 내용은 [Traffic Manager '성능' 트래픽 라우팅 메서드](traffic-manager-routing-methods.md#performance-traffic-routing-method)를 참조하세요.

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>예제 2: 중첩 프로필의 끝점 모니터링

트래픽 관리자는 적극적으로 각 서비스 끝점의 상태를 모니터링합니다. 끝점 상태가 정상이 아닌 경우 Traffic Manager는 서비스의 가용성을 유지하기 위해 사용자를 대체 끝점으로 보냅니다. 이 끝점 모니터링 및 장애 조치(Failover) 동작은 모든 트래픽 라우팅 방법에 적용됩니다. 자세한 내용은 [트래픽 관리자 끝점 모니터링](traffic-manager-monitoring.md)을 참조하세요. 끝점 모니터링은 중첩 프로필에 대해 다르게 작동합니다. 상위 프로필에서는 중첩 프로필을 사용하여 하위 프로필에 대한 상태 검사를 직접 수행하지 않습니다. 대신, 하위 프로필 끝점의 상태는 하위 프로필의 전반적인 상태를 계산하는 데 사용됩니다. 이 상태 정보는 중첩 프로필 계층 구조로 전파됩니다. 상위 프로필은 이 집계된 상태를 사용하여 하위 프로필에 트래픽을 보낼지 결정합니다. 중첩 프로필의 상태 모니터링에 대한 자세한 내용은이 문서의 [FAQ](#faq) 섹션을 참조하세요.

이전 예제로 돌아가서 유럽 서부의 프로덕션 배포가 실패하는 경우를 가정해보겠습니다. 기본적으로 '하위' 프로필은 테스트 배포로 모든 트래픽을 전달합니다. 테스트 배포도 실패하면 상위 프로필은 모든 하위 끝점이 정상적이지 않기 때문에 하위 프로필이 트래픽을 수신해야 하는지 결정합니다. 그런 다음 상위 프로필은 다른 지역에 트래픽을 분산시킵니다.

![중첩 프로필 장애 조치(기본 동작)][3]

이 정렬에 만족할 수 있습니다. 또는 유럽 서부의 모든 트래픽이 제한된 하위 집합 트래픽이 아닌 테스트 배포가 된다는 점에 고민할 수 있습니다. 테스트 배포의 상태에 관계없이 유럽 서부에서 프로덕션 배포에 실패하는 경우 다른 지역에 장애 조치하려고 합니다. 이 장애 조치를 사용하려면 하위 프로필을 상위 프로필에서 끝점으로 구성하는 경우 'MinChildEndpoints' 매개 변수를 지정할 수 있습니다. 매개 변수는 하위 프로필에서 사용할 수 있는 끝점의 최소 수를 결정합니다. 기본값은 '1'입니다. 이 시나리오에서는 MinChildEndpoints 값을 2로 설정합니다. 이 임계값보다 낮은 경우 상위 프로필은 전체 하위 프로필을 사용할 수 없도록 하고 다른 끝점으로 트래픽을 전송합니다.

다음 그림은 이 구성을 보여 줍니다.

!['MinChildEndpoints' = 2인 중첩 프로필 장애 조치][4]

> [!NOTE]
> '우선 순위' 트래픽 라우팅 메서드는 단일 끝점에 모든 트래픽을 배포합니다. 따라서 하위 프로필의 경우 MinChildEndpoints '1'이 아닌 값을 설정합니다.

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>예제 3: '성능' 트래픽 라우팅에서 우선 순위가 지정된 장애 조치(Failover) 지역

'성능' 트래픽 라우팅 방법의 기본 동작은 다음으로 가장 가까운 끝점이 오버로드되면서 연속 장애가 발생하지 않게 방지하도록 디자인되었습니다. 끝점이 실패하면 해당 끝점에 전달된 모든 트래픽은 모든 지역의 다른 끝점에 균등하게 분산됩니다.

![기본 장애 조치를 사용하는 '성능' 트래픽 라우팅][5]

그러나 유럽 서부 트래픽을 미국 서부로 장애 조치(Failover)하고 끝점을 둘 다 사용할 수 없게 되는 경우에만 다른 지역으로 전달한다고 가정합니다. '우선 순위' 트래픽 라우팅 메서드와 함께 하위 프로필을 사용하여 이 솔루션을 만들 수 있습니다.

![기본 설정 장애 조치를 사용하는 '성능' 트래픽 라우팅][6]

유럽 서부 끝점은 미국 서부 끝점보다 우선 순위가 높으므로 끝점이 둘 다 온라인 상태인 경우 모든 트래픽은 유럽 서부 끝점으로 전송됩니다. 유럽 서부에 장애가 발생하면 해당 트래픽은 미국 서부로 전달됩니다. 중첩 프로필을 사용하여 유럽 서부 및 미국 서부가 모두 실패하는 경우 트래픽은 동아시아로 전달됩니다.

모든 지역에 대해 이 패턴을 반복할 수 있습니다. 상위 프로필에 있는 세 개의 끝점을 세 개의 하위 프로필로 대체하면 각각 우선 순위가 지정된 장애 조치 순서를 제공합니다.

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>예제 4: 동일한 지역의 여러 끝점 간에 '성능' 트래픽 라우팅 제어

특정 지역에 둘 이상의 끝점이 있는 프로필에서 '성능' 트래픽 라우팅 방법을 사용한다고 가정해보겠습니다. 기본적으로 해당 지역으로 전달된 트래픽은 해당 지역에서 사용 가능한 모든 끝점에서 균등하게 배포됩니다.

![지역 내 트래픽 분산의'성능' 트래픽 라우팅(기본 동작)][7]

유럽 서부에 있는 여러 끝점을 추가하는 대신 해당 끝점을 별도 하위 프로필에서 묶습니다. 하위 프로필은 유럽 서부에서 유일한 끝점으로 상위 프로플에 추가됩니다. 하위 프로필에 대한 설정은 해당 지역 내에서 우선 순위 기반 또는 가중 트래픽 라우팅 등을 사용하도록 설정하여 유럽 서부에서 트래픽 배포를 제어할 수 있습니다.

![사용자 지정 지역 내 트래픽 분산을 사용하는 '성능' 트래픽 라우팅][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>예제 5: 끝점 기준 모니터링 설정

Traffic Manager를 사용하여 기존 온-프레미스 웹 사이트에서 Azure에서 호스트되는 새로운 클라우드 기반 버전으로 원활하게 트래픽을 마이그레이션하고 있다고 가정하겠습니다. 기존 사이트의 경우 홈페이지 URI를 사용하여 사이트 상태를 모니터링하려고 합니다. 하지만 새로운 클라우드 기반 버전의 경우에는 추가 검사를 포함하는 사용자 지정 모니터링 페이지(경로 '/monitor.aspx')를 구현합니다.

![트래픽 관리자 끝점 모니터링(기본 동작)][9]

Traffic Manager 프로필에서 모니터링 설정은 단일 프로필 내의 모든 끝점에 적용됩니다. 중첩 프로필로 사이트별로 여러 하위 프로필을 사용하여 다른 모니터링 설정을 정의할 수 있습니다.

![끝점 기준 설정을 사용하는 트래픽 관리자 끝점 모니터링][10]

## <a name="faq"></a>FAQ

### <a name="how-do-i-configure-nested-profiles"></a>중첩 프로필을 구성하려면 어떻게 해야 하나요?

중첩 Traffic Manager 프로필은 Azure Resource Manager 및 클래식 Azure REST API, Azure PowerShell cmdlet, 플랫폼 간 Azure CLI 명령을 사용하여 구성할 수 있습니다. 새 Azure Portal을 통해서도 지원됩니다. 클래식 포털을 통해서는 지원되지 않습니다.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>트래픽 관리자가 지원하는 중첩 계층 수는 얼마나 되나요?

프로필을 10개 수준 깊이까지 중첩할 수 있습니다. '루프'는 허용되지 않습니다.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>동일한 트래픽 관리자 프로필에서 중첩 자식 프로필과 다른 끝점 유형을 혼합할 수 있나요?

예. 프로필 내에서 서로 다른 유형의 끝점을 결합하는 방법에는 제한이 없습니다.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>중첩 프로필에는 요금 청구 모델이 어떻게 적용되나요?

중첩 프로필을 사용한다고 해서 가격이 더 부과되지는 않습니다.

Traffic Manager 요금 청구는 끝점 상태 검사 및 수백만 개의 DNS 쿼리로 구성됩니다.

* 끝점 상태 검사: 부모 프로필에서 끝점으로 구성된 자식 프로필에 대해서는 요금이 부과되지 않습니다. 하위 프로필에서 끝점 모니터링은 일반적인 방식으로 청구됩니다.
* DNS 쿼리: 각 쿼리는 한 번만 계산됩니다. 하위 프로필의 끝점을 반환하는 상위 프로필에 대한 쿼리는 상위 프로필에 대해서만 계산됩니다.

자세한 내용은 [Traffic Manager 가격 책정 페이지](https://azure.microsoft.com/pricing/details/traffic-manager/)를 참조하세요.

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>중첩 프로필이 성능에 영향을 미치나요?

아니요. 중첩 프로필을 사용해도 성능에 미치는 영향은 없습니다.

Traffic Manager 이름 서버는 각 DNS 쿼리를 처리하는 경우 프로필 계층 구조를 내부적으로 통과합니다. 상위 프로필에 대한 DNS 쿼리는 하위 프로필의 끝점에 대한 DNS 응답을 받을 수 있습니다. 단일 CNAME 레코드는 단일 프로필이나 중첩 프로필을 사용하는 경우 사용합니다. 계층 구조에서 각 프로필에 대한 CNAME 레코드를 만들 필요가 없습니다.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Traffic Manager에서 상위 프로필의 중첩 끝점 상태를 계산하려면 어떻게 하나요?

상위 프로필에서는 하위 프로필에 대한 상태 검사를 직접 수행하지 않습니다. 대신, 하위 프로필 끝점의 상태는 하위 프로필의 전반적인 상태를 계산하는 데 사용됩니다. 이 정보는 중첩 프로필 계층 구조로 전파되어 중첩 끝점의 상태를 확인합니다. 상위 프로필은 이 집계된 상태를 사용하여 하위 프로필에 트래픽을 보낼 수 있을지 결정합니다.

다음 테이블에서는 중첩 끝점에 대한 Traffic Manager의 상태 검사 동작에 대해 설명합니다.

| 자식 프로필 모니터 상태 | 부모 끝점 모니터 상태 | 참고 사항 |
| --- | --- | --- |
| Disabled. 하위 프로필을 사용하지 않도록 설정했습니다. |중지됨 |부모 끝점 상태는 Stopped이며 Disabled가 아닙니다. Disabled 상태는 부모 프로필에서 끝점을 사용할 수 없도록 설정했음을 표시하도록 예약되어 있습니다. |
| Degraded. 하나 이상의 자식 프로필 끝점이 Degraded 상태입니다. |Online: 자식 프로필의 Online 끝점 수가 MinChildEndpoints 값 이상입니다.<BR>CheckingEndpoint: 자식 프로필의 Online 및 CheckingEndpoint 끝점 수 합계가 MinChildEndpoints 값 이상입니다.<BR>Degraded: 그렇지 않은 경우 |트래픽이 CheckingEndpoint 상태의 끝점으로 라우팅됩니다. MinChildEndpoints를 너무 높게 설정하는 경우 끝점의 성능이 항상 저하됩니다. |
| Online. 하나 이상의 하위 프로필 끝점이 온라인 상태입니다. 성능이 저하된 끝점이 없습니다. |위 내용을 참조하세요. | |
| CheckingEndpoints. 하나 이상의 하위 프로필 끝점이 'CheckingEndpoint'입니다. '온라인' 또는 '성능이 저하'된 끝점이 없습니다. |위와 동일합니다. | |
| Inactive. 모든 하위 프로필 끝점이 사용 안함 또는 중지됨이거나 끝점이 없는 프로필입니다. |중지됨 | |

## <a name="next-steps"></a>다음 단계

 [트래픽 관리자 작동 방식](traffic-manager-how-traffic-manager-works.md)

 [트래픽 관리자 프로필을 만드는](traffic-manager-manage-profiles.md)

<!--Image references-->
[1]: ./media/traffic-manager-nested-profiles/figure-1.png
[2]: ./media/traffic-manager-nested-profiles/figure-2.png
[3]: ./media/traffic-manager-nested-profiles/figure-3.png
[4]: ./media/traffic-manager-nested-profiles/figure-4.png
[5]: ./media/traffic-manager-nested-profiles/figure-5.png
[6]: ./media/traffic-manager-nested-profiles/figure-6.png
[7]: ./media/traffic-manager-nested-profiles/figure-7.png
[8]: ./media/traffic-manager-nested-profiles/figure-8.png
[9]: ./media/traffic-manager-nested-profiles/figure-9.png
[10]: ./media/traffic-manager-nested-profiles/figure-10.png



<!--HONumber=Nov16_HO5-->


