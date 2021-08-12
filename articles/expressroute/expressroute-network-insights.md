---
title: Network Insights를 사용하는 Azure ExpressRoute 인사이트
description: Network Insights를 사용하는 Azure ExpressRoute 인사이트에 관해 알아봅니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 04/05/2021
ms.author: duau
ms.openlocfilehash: a5e01a4fc84f852b43e5a76114bebe145da10793
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443592"
---
# <a name="azure-expressroute-insights-using-network-insights"></a>Network Insights를 사용하는 Azure ExpressRoute 인사이트

이 문서에서는 Network Insights를 통해 ExpressRoute 메트릭 및 구성을 모두 한곳에서 보는 방법을 설명합니다. Network Insights를 통해 추가 설정을 완료하지 않아도 중요한 ExpressRoute 정보를 포함하는 토폴로지 맵 및 성능 상태 대시보드를 볼 수 있습니다.

:::image type="content" source="./media/expressroute-network-insights/network-monitor-page.png" alt-text="네트워크 모니터 방문 페이지의 스크린샷" lightbox="./media/expressroute-network-insights/monitor-landing-page-expanded.png":::

## <a name="visualize-functional-dependencies"></a>기능 종속성 시각화

1. *Azure Monitor* 페이지로 이동한 다음, ‘네트워크’를 선택합니다.

    :::image type="content" source="./media/expressroute-network-insights/monitor-page.png" alt-text="모니터 방문 페이지의 스크린샷":::

1. ‘ExpressRoute 회로’ 카드를 선택합니다. 

1. 그런 다음, 보려는 회로에 대해 토폴로지 단추를 선택합니다.

   :::image type="content" source="./media/expressroute-network-insights/monitor-landing-page.png" alt-text="ExpressRoute 모니터 방문 페이지의 스크린샷" lightbox="./media/expressroute-network-insights/monitor-landing-page-expanded.png"::: 

1. 기능 종속성 보기에서는 ExpressRoute 설정의 분명한 정의를 제공하여 다양한 ExpressRoute 구성 요소(피어링, 연결, 게이트웨이) 간 관계를 간략하게 설명합니다.

    :::image type="content" source="./media/expressroute-network-insights/topology-view.png" alt-text="Network Insights에 대한 토폴로지 보기의 스크린샷" lightbox="./media/expressroute-network-insights/topology-view-expanded.png":::

1. 토폴로지 맵에서 구성 요소를 가리켜서 구성 정보를 표시합니다. 예를 들어, ExpressRoute 피어링 구성 요소를 가리켜서 회로 대역폭 및 Global Reach 사용 여부와 같은 세부 정보를 표시합니다.

    :::image type="content" source="./media/expressroute-network-insights/topology-hovered.png" alt-text="토폴로지 보기 리소스를 가리키는 동작의 스크린샷" lightbox="./media/expressroute-network-insights/topology-hovered-expanded.png":::

## <a name="view-a-detailed-and-pre-loaded-metrics-dashboard"></a>자세한 미리 로드된 메트릭 대시보드 보기

기능 종속성 보기를 사용하여 ExpressRoute 설정의 토폴로지를 검토한 후 **자세한 메트릭 보기** 를 선택하여 자세한 메트릭 보기로 이동하고 회로의 성능을 파악합니다. 이 보기는 연결된 리소스의 구성된 목록 및 중요한 ExpressRoute 메트릭의 풍부한 대시보드를 제공합니다.

**연결된 리소스** 섹션에는 해당 리소스 페이지로 이동하기 위해 선택할 수 있는 연결된 ExpressRoute 게이트웨이 및 구성된 피어링이 나열됩니다.

:::image type="content" source="./media/expressroute-network-insights/linked-resources.png" alt-text="모니터 페이지에 있는 연결된 리소스의 스크린샷":::


**ExpressRoute 메트릭** 섹션에는 **가용성**, **처리량**, **패킷 삭제**, **게이트웨이 메트릭** 범주에 있는 중요한 회로 메트릭 차트가 포함됩니다.

### <a name="availability"></a>가용성

‘가용성’ 탭은 ARP와 BGP 가용성을 추적하여 회로의 데이터를 전체 및 개별 연결(기본과 보조)로 그림으로 나타냅니다. 

:::image type="content" source="./media/expressroute-network-insights/arp-bgp-availability.png" alt-text="가용성 메트릭 그래프의 스크린샷" lightbox="./media/expressroute-network-insights/arp-bgp-availability-expanded.png":::

### <a name="throughput"></a>처리량

마찬가지로 ‘처리량’ 탭은 회로에 대한 수신 및 송신 트래픽의 총 처리량(비트/초)을 그림으로 나타냅니다. 각 구성된 피어링 유형 및 개별 연결의 처리량을 볼 수도 있습니다.

:::image type="content" source="./media/expressroute-network-insights/throughput.png" alt-text="처리량 메트릭 그래프의 스크린샷" lightbox="./media/expressroute-network-insights/throughput-expanded.png":::

### <a name="packet-drops"></a>패킷 삭제

‘패킷 삭제’ 탭은 회로를 통한 수신 및 송신 트래픽의 삭제된 비트/초를 그림으로 나타냅니다. 이 탭은 정기적으로 회로 대역폭을 필요로 하거나 초과하는 경우 발생할 수 있는 성능 문제를 모니터링하는 쉬운 방법을 제공합니다.

:::image type="content" source="./media/expressroute-network-insights/dropped-packets.png" alt-text="삭제된 패킷 그래프의 스크린샷" lightbox="./media/expressroute-network-insights/dropped-packets-expanded.png":::

### <a name="gateway-metrics"></a>게이트웨이 메트릭

마지막으로, 게이트웨이 메트릭 탭은 연결된 리소스 섹션에서 선택한 ExpressRoute 게이트웨이의 주요 메트릭 차트로 채워집니다. 특정 가상 네트워크에 대한 연결을 모니터링해야 하는 경우 이 탭을 사용합니다.

:::image type="content" source="./media/expressroute-network-insights/gateway-metrics.png" alt-text="게이트웨이 처리량 및 CPU 메트릭의 스크린샷" lightbox="./media/expressroute-network-insights/gateway-metrics-expanded.png":::

## <a name="next-steps"></a>다음 단계

ExpressRoute 연결을 구성합니다.
  
* [Azure ExpressRoute](expressroute-introduction.md), [Network Insights](../azure-monitor/insights/network-insights-overview.md) 및 [Network Watcher](../network-watcher/network-watcher-monitoring-overview.md)에 관해 자세히 알아봅니다.
* [회로 만들기 및 수정](expressroute-howto-circuit-arm.md)
* [피어링 구성 만들기 및 수정](expressroute-howto-routing-arm.md)
* [VNet을 ExpressRoute 회로에 연결](expressroute-howto-linkvnet-arm.md)
* [메트릭 사용자 지정](expressroute-monitoring-metrics-alerts.md) 및 [연결 모니터](../network-watcher/connection-monitor-overview.md) 만들기
