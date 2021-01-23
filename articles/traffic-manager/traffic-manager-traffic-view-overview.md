---
title: Azure Traffic Manager의 트래픽 보기
description: 이 소개에서는 Traffic manager 트래픽 보기의 작동 방식에 대해 알아봅니다.
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 01/22/2021
ms.author: duau
ms.custom: ''
ms.openlocfilehash: 66376655c61903761d93ea228c6d72fa05734353
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743052"
---
# <a name="traffic-manager-traffic-view"></a>Traffic Manager 트래픽 보기

Traffic Manager는 DNS (도메인 이름 시스템) 수준 라우팅을 제공 합니다. 이 서비스를 통해 최종 사용자는 선택한 라우팅 방법에 따라 정상 끝점으로 이동할 수 있습니다. 트래픽 보기를 통해 Traffic Manager에는 사용자 기반(DNS 확인자 세분성 수준) 및 해당 트래픽 패턴 보기가 제공됩니다. 트래픽 보기를 사용하도록 설정하면 실행 가능한 정보를 제공할 수 있게 처리됩니다. 

트래픽 보기를 사용하여 다음을 수행할 수 있습니다.
- 사용자 기반이 있는 위치를 이해합니다(로컬 DNS 확인자 수준 세분성까지).
- 이 지역에서 생성된 트래픽 볼륨을 확인합니다(Azure Traffic Manager가 처리하는 DNS 쿼리로 관찰).
- 이러한 사용자가 경험한 대표적 대기 시간 정보를 파악합니다.
- 이러한 각 사용자 기반에서 엔드포인트가 있는 Azure 지역까지 구체적인 트래픽 패턴을 심층 탐구합니다. 

예를 들어 트래픽 보기를 사용 하 여 트래픽이 많은 지역을 파악 하지만 대기 시간이 길어질 수 있습니다. 그런 다음이 정보를 사용 하 여 새 Azure 지역에 대 한 공간 확장을 계획 합니다. 이렇게 하면 사용자가 대기 시간을 줄일 수 있습니다.

## <a name="how-traffic-view-works"></a>트래픽 보기 작동 방식

트래픽 보기는 지난 7 일간 프로필에 대해 받은 들어오는 쿼리를 확인 하는 방식으로 작동 합니다. 들어오는 쿼리 정보에서 트래픽 보기 사용자의 위치를 나타내는 데 사용 되는 DNS 확인 자의 원본 IP를 추출 합니다. 이 정보는 DNS 확인자 수준에서 함께 그룹화 되어 사용자 기반 지역을 만듭니다. Traffic Manager는 IP 주소의 지리적 정보를 유지 관리 합니다. 그런 다음 쿼리가 라우팅되는 Azure 지역을 확인 하 고 해당 지역의 사용자에 대 한 트래픽 흐름 맵을 생성 합니다. Traffic Manager
 
다음 단계에서 Traffic Manager는 네트워크 인텔리전스 대기 시간 테이블을 사용 하 여 사용자 기본 지역을 Azure 지역 매핑과 연관 시킵니다. 이 테이블은 Azure 지역에 연결할 때 해당 지역의 사용자가 경험 하는 평균 대기 시간을 이해 하기 위해 다른 최종 사용자 네트워크에 대해 유지 관리 됩니다. 이러한 모든 계산은 사용자에 게 표시 되기 전에 로컬 DNS 확인자 IP 수준에 결합 됩니다. 다양한 방법으로 정보를 사용할 수 있습니다.

트래픽 보기 데이터 업데이트의 빈도는 여러 내부 서비스 변수에 따라 달라 집니다. 그러나 데이터는 24 시간 마다 한 번씩 업데이트 됩니다.

>[!NOTE]
>트래픽 보기에 설명된 대기 시간은 최종 사용자와 연결된 Azure 지역 간의 대표적인 대기 시간이며 DNS 조회 대기 시간이 아닙니다. 트래픽 보기에서는 로컬 DNS 해결 프로그램과 쿼리가 라우팅된 Azure 지역 간의 대기 시간을 측정하기 위해 최선을 다합니다. 사용할 수 있는 데이터가 충분치 않은 경우 반환되는 대기 시간은 null이 됩니다. 

## <a name="visual-overview"></a>시각적 개요

Traffic Manager 페이지의 **트래픽 보기** 섹션으로 이동 하면 트래픽 보기 정보 오버레이가 포함 된 지리적 지도가 표시 됩니다. 맵에는 Traffic Manager 프로필의 사용자 기반 및 엔드포인트에 대한 정보가 제공됩니다.

![Traffic Manager 트래픽 보기 지리적 보기][1]

### <a name="user-base-information"></a>사용자 기반 정보

위치 정보를 사용할 수 있는 로컬 DNS 해결 프로그램의 경우 맵에 표시 됩니다. DNS 확인자의 색상은 Traffic Manager 쿼리에 해당 DNS 확인자를 사용하는 최종 사용자에게 발생하는 평균 대기 시간을 나타냅니다.

맵에서 DNS 확인자 위치를 가리키면 다음이 표시됩니다.
- DNS 확인자의 IP 주소
- Traffic Manager에서 표시하는 DNS 쿼리 트래픽 볼륨
- DNS 확인자의 트래픽에서 라우트된 엔드포인트, 엔드포인트와 DNS 확인자 사이의 선으로 표시됨 
- 해당 위치에서 엔드포인트까지 평균 대기 시간, 이 둘을 연결하는 선의 색상으로 표시됨

### <a name="endpoint-information"></a>엔드포인트 정보

끝점이 위치한 Azure 지역은 지도에 파란색 점으로 표시 됩니다. 끝점이 외부에 있고 Azure 지역에 매핑되지 않은 경우 맵의 맨 위에 표시 됩니다. 모든 끝점을 선택 하 여 트래픽이 해당 끝점에 전달 된 위치에서 다른 위치 (사용 되는 DNS 확인자 기반)를 확인 합니다. 연결은 끝점과 DNS 확인자 위치 사이에 선으로 표시 됩니다. 해당 쌍 간의 대표 대기 시간에 따라 색이 지정 됩니다. 끝점의 이름과 해당 끝점을 실행 하는 Azure 지역을 확인할 수 있습니다. 이 Traffic Manager 프로필에 의해 전달 되는 총 요청 볼륨도 표시 됩니다.


## <a name="tabular-listing-and-raw-data-download"></a>테이블 형식 목록 및 원시 데이터 다운로드

Azure Portal에서 테이블 형식으로 트래픽 보기 데이터를 볼 수 있습니다. 다음을 표시 하는 각 DNS 확인자 IP/끝점 쌍에 대 한 항목이 있습니다.

* DNS 확인 자의 IP 주소입니다.
* 이름입니다.
* 끝점이 있는 Azure 지역의 지리적 위치입니다 (사용 가능한 경우).
* 해당 끝점에 대 한 DNS 확인자와 연결 된 요청 볼륨입니다.
* 해당 DNS를 사용 하는 최종 사용자와 관련 된 대표 대기 시간입니다 (사용 가능한 경우). 

또한 CSV 파일로 트래픽 보기 데이터를 다운로드하여 원하는 분석 워크플로의 일부로 사용할 수 있습니다.

## <a name="billing"></a>결제

트래픽 보기 사용 하는 경우 제공 되는 정보를 만드는 데 사용 되는 데이터 요소 수를 기준으로 요금이 청구 됩니다. 현재 사용되는 유일한 데이터 요소 형식은 Traffic Manager 프로필에 대해 수신된 쿼리입니다. 가격 책정에 대한 자세한 내용은 [Traffic Manager 가격 책정 페이지](https://azure.microsoft.com/pricing/details/traffic-manager/)를 참조하세요.

## <a name="faqs"></a>FAQ

* [트래픽 보기의 기능은 무엇인가요?](./traffic-manager-faqs.md#what-does-traffic-view-do)

* [트래픽 보기를 사용하는 장점은 무엇인가요?](./traffic-manager-faqs.md#how-can-i-benefit-from-using-traffic-view)

* [트래픽 보기와 Azure Monitor를 통해 사용할 수 있는 Traffic Manager 메트릭의 차이는 무엇인가요?](./traffic-manager-faqs.md#how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor)

* [트래픽 보기에서 EDNS 클라이언트 서브넷 정보를 사용하나요?](./traffic-manager-faqs.md#does-traffic-view-use-edns-client-subnet-information)

* [트래픽 보기는 며칠 동안의 데이터를 사용하나요?](./traffic-manager-faqs.md#how-many-days-of-data-does-traffic-view-use)

* [트래픽 보기는 외부 엔드포인트를 어떻게 처리하나요?](./traffic-manager-faqs.md#how-does-traffic-view-handle-external-endpoints)

* [내 구독의 각 프로필에 트래픽 보기를 사용하도록 설정해야 하나요?](./traffic-manager-faqs.md#do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription)

* [트래픽 보기를 해제하려면 어떻게 할까요?](./traffic-manager-faqs.md#how-can-i-turn-off-traffic-view)

* [트래픽 보기 요금 청구는 어떻게 작동하나요?](./traffic-manager-faqs.md#how-does-traffic-view-billing-work)

## <a name="next-steps"></a>다음 단계

- [Traffic Manager 작동 방식](traffic-manager-overview.md)
- Traffic Manager가 지원하는 [트래픽 라우팅 방법](traffic-manager-routing-methods.md) 에 대해 자세히 알아봅니다.
- [Traffic Manager 프로필을 만드는](./quickstart-create-traffic-manager-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-traffic-view-overview/trafficview.png