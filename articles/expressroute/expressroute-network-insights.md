---
title: Network Insights를 사용한 Azure Express 경로 정보
description: Network Insights를 사용 하 여 Azure Express 경로 정보에 대해 알아봅니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/23/2021
ms.author: duau
ms.openlocfilehash: 7033ea6a1ba6d85f9aa15e14bb9577b2439c59a8
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105050566"
---
# <a name="azure-expressroute-insights-using-network-insights"></a>Network Insights를 사용한 Azure Express 경로 정보

이 문서에서는 Network Insights를 통해 Express 경로 메트릭 및 구성을 모두 한 곳에서 보는 방법을 설명 합니다. 네트워크 정보를 통해 추가 설정을 완료할 필요 없이 중요 한 Express 경로 정보를 포함 하는 토폴로지 맵 및 상태 대시보드를 볼 수 있습니다.

:::image type="content" source="./media/expressroute-network-insights/monitor-landing-page.png" alt-text="Express 경로 모니터 방문 페이지의 스크린샷" lightbox="./media/expressroute-network-insights/monitor-landing-page-expanded.png":::

## <a name="visualize-functional-dependencies"></a>기능 종속성 시각화

이 솔루션을 보려면 *Azure Monitor* 페이지로 이동 하 여 *네트워크* 를 선택한 후 *express 경로 회로* 카드를 선택 합니다. 그런 다음 보려는 회로에 대 한 토폴로지 단추를 선택 합니다.

함수 종속성 뷰에서는 Express 경로 설정에 대 한 명확한 그림을 제공 하 여 다양 한 Express 경로 구성 요소 (피어 링, 연결, 게이트웨이) 간의 관계를 간략하게 설명 합니다.

:::image type="content" source="./media/expressroute-network-insights/topology-view.png" alt-text="네트워크 insights에 대 한 토폴로지 보기의 스크린샷" lightbox="./media/expressroute-network-insights/topology-view-expanded.png":::

구성 정보를 보려면 토폴로지 맵에서 구성 요소를 마우스로 가리킵니다. 예를 들어, Express 경로 피어 링 구성 요소를 마우스로 가리켜서 회로 대역폭과 Global Reach 사용량 등의 세부 정보를 볼 수 있습니다.

:::image type="content" source="./media/expressroute-network-insights/topology-hovered.png" alt-text="토폴로지 보기 리소스 가리키기의 스크린샷" lightbox="./media/expressroute-network-insights/topology-hovered-expanded.png":::

## <a name="view-a-detailed-and-pre-loaded-metrics-dashboard"></a>상세 및 미리 로드 된 메트릭 대시보드 보기

함수 종속성 보기를 사용 하 여 Express 경로 설정의 토폴로지를 검토 한 후 **자세한 메트릭 보기를 선택 하** 여 회로의 성능을 파악 합니다. 이 보기는 연결 된 리소스의 구성 된 목록과 중요 Express 경로 메트릭에 대 한 풍부한 대시보드를 제공 합니다.

연결 **된 리소스** 섹션에는 연결 된 express 경로 게이트웨이 및 구성 된 피어 링이 나열 되며, 해당 리소스 페이지로 이동 하기 위해 선택할 수 있습니다.

:::image type="content" source="./media/expressroute-network-insights/linked-resources.png" alt-text="모니터 페이지에 있는 연결 된 리소스의 스크린샷":::


**Express 경로 메트릭** 섹션에는 **가용성**, **처리량**, **패킷 삭제** 및 **게이트웨이 메트릭의** 범주의 중요 한 회로 메트릭에 대 한 차트가 포함 되어 있습니다.

### <a name="availability"></a>가용성

*가용성* 탭은 ARP 및 BGP 가용성을 추적 하 여 회로에 대 한 데이터를 전체 및 개별 연결 (기본 및 보조)으로 플롯 합니다. 

:::image type="content" source="./media/expressroute-network-insights/arp-bgp-availability.png" alt-text="가용성 메트릭 그래프의 스크린샷" lightbox="./media/expressroute-network-insights/arp-bgp-availability-expanded.png":::

### <a name="throughput"></a>처리량

마찬가지로 *처리량* 탭은 회로에 대 한 총 수신 및 송신 트래픽 처리량을 비트/초로 플롯 합니다. 개별 연결 및 각 유형의 구성 된 피어 링에 대 한 처리량을 볼 수도 있습니다.

:::image type="content" source="./media/expressroute-network-insights/throughput.png" alt-text="처리량 메트릭 그래프의 스크린샷" lightbox="./media/expressroute-network-insights/throughput-expanded.png":::

### <a name="packet-drops"></a>패킷 삭제

*패킷 삭제* 탭은 회로를 통해 수신 및 송신 트래픽에 대 한 삭제 된 비트/초를 플롯 합니다. 이 탭은 회로 대역폭을 정기적으로 요구 하거나 초과 하는 경우 발생할 수 있는 성능 문제를 모니터링 하는 쉬운 방법을 제공 합니다.

:::image type="content" source="./media/expressroute-network-insights/dropped-packets.png" alt-text="삭제 된 패킷 그래프의 스크린샷" lightbox="./media/expressroute-network-insights/dropped-packets-expanded.png":::

### <a name="gateway-metrics"></a>게이트웨이 메트릭

마지막으로 게이트웨이 메트릭 탭은 선택한 Express 경로 게이트웨이 (연결 된 리소스 섹션에서)에 대 한 주요 메트릭 차트로 채워집니다. 특정 가상 네트워크에 대 한 연결을 모니터링 해야 하는 경우이 탭을 사용 합니다.

:::image type="content" source="./media/expressroute-network-insights/gateway-metrics.png" alt-text="게이트웨이 처리량 및 CPU 메트릭의 스크린샷" lightbox="./media/expressroute-network-insights/gateway-metrics-expanded.png":::

## <a name="next-steps"></a>다음 단계

ExpressRoute 연결을 구성합니다.
  
* [Azure express](expressroute-introduction.md)경로, [Network Insights](../azure-monitor/insights/network-insights-overview.md)및 [Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) 에 대해 자세히 알아보세요.
* [회로 만들기 및 수정](expressroute-howto-circuit-arm.md)
* [피어링 구성 만들기 및 수정](expressroute-howto-routing-arm.md)
* [VNet을 ExpressRoute 회로에 연결](expressroute-howto-linkvnet-arm.md)
* [메트릭 사용자 지정](expressroute-monitoring-metrics-alerts.md) 및 [연결 모니터](../network-watcher/connection-monitor-overview.md) 만들기
