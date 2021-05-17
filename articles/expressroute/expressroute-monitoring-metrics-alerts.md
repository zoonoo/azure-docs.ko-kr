---
title: 'Azure ExpressRoute: 모니터링, 메트릭 및 경고'
description: Azure의 모든 메트릭, 경고, 진단 로그에 대한 원스톱 상점과도 같은 Azure Monitor를 사용하여 Azure ExpressRoute 모니터링, 메트릭 및 경고에 대해 알아봅니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/11/2020
ms.author: duau
ms.openlocfilehash: 7a5da35da35b2f447256bc742681ccd7a7d403da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99091569"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>ExpressRoute 모니터링, 메트릭 및 경고

이 문서는 Azure Monitor를 사용하여 ExpressRoute 모니터링, 메트릭 및 경고를 이해하는 데 도움이 됩니다. Azure Monitor에서는 Azure 전체의 모든 메트릭, 경고, 진단 로그를 확인할 수 있습니다.
 
>[!NOTE]
>**클래식 메트릭** 을 사용하면 좋지 않습니다.
>

## <a name="expressroute-metrics"></a>ExpressRoute 메트릭

**메트릭** 을 보려면 *Azure Monitor* 페이지로 이동하여 *메트릭* 을 클릭합니다. **ExpressRoute** 메트릭을 보려면 리소스 종류 *ExpressRoute 회로* 를 기준으로 필터링합니다. **Global Reach** 메트릭을 보려면 리소스 종류 *ExpressRoute 회로* 를 기준으로 필터링하고 Global Reach를 사용하도록 설정한 ExpressRoute 회로 리소스를 선택합니다. **ExpressRoute Direct** 메트릭을 보려면 *ExpressRoute 포트* 를 기준으로 리소스 종류를 필터링합니다. 

메트릭을 선택하면 기본 집계가 적용됩니다. 필요에 따라 분할을 적용하여 차원이 다른 메트릭을 표시할 수 있습니다.

### <a name="available-metrics"></a>사용 가능한 메트릭

|**메트릭**|**범주**|**차원**|**기능**|
| --- | --- | --- | --- |
|ARP 가용성|가용성|<ui><li>피어(기본/보조 ExpressRoute 라우터)</ui></li><ui><li> 피어링 유형(프라이빗/퍼블릭/Microsoft)</ui></li>|ExpressRoute|
|Bgp 가용성|가용성|<ui><li> 피어(기본/보조 ExpressRoute 라우터)</ui></li><ui><li> 피어링 유형</ui></li>|ExpressRoute|
|BitsInPerSecond|트래픽|<ui><li> 피어링 유형(ExpressRoute)</ui></li><ui><li>링크(ExpressRoute Direct)</ui></li>|<li>ExpressRoute</li><li>ExpressRoute Direct</li><ui><li>ExpressRoute 게이트웨이 연결</ui></li>|
|BitsOutPerSecond|트래픽| <ui><li>피어링 유형(ExpressRoute)</ui></li><ui><li> 링크(ExpressRoute Direct) |<ui><li>ExpressRoute<ui><li>ExpressRoute Direct</ui></li><ui><li>ExpressRoute 게이트웨이 연결</ui></li>|
|CPU 사용률|성능| <ui><li>인스턴스</ui></li>|ExpressRoute 가상 네트워크 게이트웨이|
|초당 패킷|성능| <ui><li>인스턴스</ui></li>|ExpressRoute 가상 네트워크 게이트웨이|
|피어에 보급된 경로 수 |가용성| <ui><li>인스턴스</ui></li>|ExpressRoute 가상 네트워크 게이트웨이|
|피어에서 학습된 경로 수 |가용성| <ui><li>인스턴스</ui></li>|ExpressRoute 가상 네트워크 게이트웨이|
|경로 변경 빈도 |가용성| <ui><li>인스턴스</ui></li>|ExpressRoute 가상 네트워크 게이트웨이|
|가상 네트워크의 VM 수 |가용성| 해당 없음 |ExpressRoute 가상 네트워크 게이트웨이|
|GlobalReachBitsInPerSecond|트래픽|<ui><li>피어링 회로 Skey(서비스 키)</ui></li>|Global Reach|
|GlobalReachBitsOutPerSecond|트래픽|<ui><li>피어링 회로 Skey(서비스 키)</ui></li>|Global Reach|
|AdminState|물리적 연결|링크|ExpressRoute Direct|
|LineProtocol|물리적 연결|링크|ExpressRoute Direct|
|RxLightLevel|물리적 연결|<ui><li>링크</ui></li><ui><li>레인</ui></li>|ExpressRoute Direct|
|TxLightLevel|물리적 연결|<ui><li>링크</ui></li><ui><li>레인</ui></li>|ExpressRoute Direct|
>[!NOTE]
>*GlobalGlobalReachBitsInPerSecond* 및 *GlobalGlobalReachBitsOutPerSecond* 사용은 하나 이상의 Global Reach 연결이 설정된 경우에만 표시됩니다.
>

## <a name="circuits-metrics"></a>회로 메트릭

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>비트 입력 및 출력 - 모든 피어링에 대한 메트릭

지정된 ExpressRoute 회로의 모든 피어링에 대한 메트릭을 볼 수 있습니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg" alt-text="회로 메트릭":::

### <a name="bits-in-and-out---metrics-per-peering"></a>비트 입력 및 출력 - 피어링당 메트릭

프라이빗, 공용 및 Microsoft 피어링에 대한 메트릭을 비트/초 단위로 볼 수 있습니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg" alt-text="피어링당 메트릭":::

### <a name="bgp-availability---split-by-peer"></a>BGP 가용성 - 피어로 분할  

피어링 및 피어(기본 및 보조 ExpressRoute 라우터)에서 BGP의 실시간에 가까운 가용성을 확인할 수 있습니다. 이 대시보드는 프라이빗 피어링에 대해 가동 상태인 기본 BGP 세션과 프라이빗 피어링에 대해 다운 상태인 두 번째 BGP 세션을 보여 줍니다. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg" alt-text="피어당 BGP 가용성":::

### <a name="arp-availability---split-by-peering"></a>ARP 가용성 - 피어링당 분할  

피어링 및 피어(기본 및 보조 ExpressRoute 라우터)에서 [ARP](./expressroute-troubleshooting-arp-resource-manager.md)의 실시간에 가까운 가용성을 확인할 수 있습니다. 이 대시보드는 두 피어에서는 가동 상태이지만 피어링 전반에서 Microsoft 피어링에 대해 완전히 다운 상태인 프라이빗 피어링 ARP 세션을 보여 줍니다. 두 피어에서 기본 집계(평균)를 활용했습니다.  

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg" alt-text="피어당 ARP 가용성":::

## <a name="expressroute-direct-metrics"></a>ExpressRoute Direct 메트릭

### <a name="admin-state---split-by-link"></a>관리 상태 - 링크당 분할

ExpressRoute Direct 포트 쌍의 각 링크에 대한 관리 상태를 볼 수 있습니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg" alt-text="ER Direct 관리 상태":::

### <a name="bits-in-per-second---split-by-link"></a>초당 비트 입력 수 - 링크당 분할

ExpressRoute Direct 포트 쌍의 두 링크에서 초당 비트 입력 수를 볼 수 있습니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg" alt-text="ER Direct 초당 비트 입력 수":::

### <a name="bits-out-per-second---split-by-link"></a>초당 비트 출력 수 - 링크당 분할

ExpressRoute Direct 포트 쌍의 두 링크에서 초당 비트 출력 수를 볼 수도 있습니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg" alt-text="ER Direct 초당 비트 출력 수":::

### <a name="line-protocol---split-by-link"></a>라인 프로토콜 - 링크당 분할

ExpressRoute Direct 포트 쌍의 각 링크에서 라인 프로토콜을 볼 수 있습니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg" alt-text="ER Direct 라인 프로토콜":::

### <a name="rx-light-level---split-by-link"></a>Rx 라이트 수준 - 링크당 분할

각 포트에 대해 Rx 라이트 수준(ExpressRoute Direct 포트가 **수신** 하는 라이트 수준)을 볼 수 있습니다. 정상 Rx 라이트 수준은 일반적으로 -10~0dBm 사이입니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg" alt-text="ER Direct 라인 Rx 라이트 수준":::

### <a name="tx-light-level---split-by-link"></a>Tx 라이트 수준 - 링크당 분할

각 포트에 대해 Tx 라이트 수준(ExpressRoute Direct 포트가 **전송** 하는 라이트 수준)을 볼 수 있습니다. 정상 Tx 라이트 수준은 일반적으로 -10~0dBm 사이입니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg" alt-text="ER Direct 라인 Tx 라이트 수준":::

## <a name="expressroute-virtual-network-gateway-metrics"></a>ExpressRoute 가상 네트워크 게이트웨이 메트릭

### <a name="cpu-utilization---split-instance"></a>CPU 사용률 - 분할 인스턴스

게이트웨이 인스턴스의 CPU 사용률을 볼 수 있습니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/cpu-split.jpg" alt-text="CPU 분할":::

### <a name="packets-per-second---split-by-instance"></a>초당 패킷 - 인스턴스당 분할

게이트웨이를 트래버스하는 초당 패킷 수를 볼 수 있습니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/pps-split.jpg" alt-text="초당 패킷 - 분할":::

### <a name="count-of-routes-advertised-to-peer---split-by-instance"></a>피어에 보급된 경로 수 - 인스턴스당 분할

ExpressRoute 회로에 보급된 경로 수를 볼 수 있습니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-advertised-to-peer.png" alt-text="피어에 보급된 경로 수":::

### <a name="count-of-routes-learned-from-peer---split-by-instance"></a>피어에서 학습된 경로 수 - 인스턴스당 분할

ExpressRoute 회로에서 수신한 경로 수를 볼 수 있습니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-learned-from-peer.png" alt-text="피어에서 학습된 경로 수":::

### <a name="frequency-of-routes-change---split-by-instance"></a>경로 변경 빈도 - 인스턴스당 분할

게이트웨이에서 경로가 변경되는 빈도를 볼 수 있습니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/frequency-of-routes-changed.png" alt-text="경로 변경 빈도":::

### <a name="number-of-vms-in-the-virtual-network"></a>가상 네트워크의 VM 수

가상 네트워크의 가상 머신 수를 볼 수 있습니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/number-of-virtual-machines-virtual-network.png" alt-text="가상 네트워크의 가상 머신 수":::

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>ExpressRoute 게이트웨이 연결(비트/초)

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erconnections.jpg" alt-text="게이트웨이 연결":::

## <a name="alerts-for-expressroute-gateway-connections"></a>ExpressRoute 게이트웨이 연결에 대한 경고

1. 경고를 구성하려면 **Azure Monitor** 로 이동하여 **경고** 를 선택합니다.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg" alt-text="경고":::
2. **+대상 선택** 을 클릭하고 ExpressRoute 게이트웨이 연결 리소스를 선택합니다.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg" alt-text="대상":::
3. 경고 세부 정보를 정의합니다.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg" alt-text="작업 그룹":::
4. 작업 그룹을 정의하고 추가합니다.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/actiongroup.png" alt-text="작업 그룹 추가":::

## <a name="alerts-based-on-each-peering"></a>각 피어링을 기반으로 한 경고

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg" alt-text="각 피어링":::

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>회로에서 활동 로그에 대한 경고 구성

**경고 조건** 에서 신호 유형으로 **활동 로그** 를 선택하고 신호를 선택할 수 있습니다.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg" alt-text="활동 로그"::::

## <a name="additional-metrics-in-log-analytics"></a>Log Analytics의 추가 메트릭

ExpressRoute 회로 리소스로 이동하고 *로그* 탭을 선택하여 ExpressRoute 메트릭을 볼 수도 있습니다. 쿼리하는 모든 메트릭에 대해 아래 열이 출력에 포함됩니다.

|**열**|**형식**|**설명**|
| --- | --- | --- |
|TimeGrain|문자열|PT1M(메트릭 값은 1분마다 푸시됨)|
|개수|real|일반적으로 2와 같음(각 MSEE는 1분마다 단일 메트릭 값을 푸시함)|
|최소|real|두 MSEE에 의해 푸시되는 두 메트릭 값의 최소값|
|최대|real|두 MSEE에 의해 푸시되는 두 메트릭 값의 최대값|
|평균|real|(최소 + 최대)/2와 같음|
|합계|real|두 MSEE의 두 메트릭 값 합계(쿼리된 메트릭에 대해 초점을 맞출 주 값)|
  
## <a name="next-steps"></a>다음 단계

ExpressRoute 연결을 구성합니다.
  
* [회로 만들기 및 수정](expressroute-howto-circuit-arm.md)
* [피어링 구성 만들기 및 수정](expressroute-howto-routing-arm.md)
* [VNet을 ExpressRoute 회로에 연결](expressroute-howto-linkvnet-arm.md)
