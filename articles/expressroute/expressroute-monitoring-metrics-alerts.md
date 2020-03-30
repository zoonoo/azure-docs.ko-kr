---
title: 'Azure 익스프레스라우팅: 모니터링, 메트릭 및 경고'
description: 이 페이지에서는 ExpressRoute 모니터링에 대한 정보를 제공합니다.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: cherylmc
ms.openlocfilehash: 268a7e7c94285d3c4fdcb0c5fb91b685c09b58c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436907"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>ExpressRoute 모니터링, 메트릭 및 경고

이 문서는 Azure Monitor를 사용하여 ExpressRoute 모니터링, 메트릭 및 경고를 이해하는 데 도움이 됩니다. Azure Monitor에서는 Azure 전체의 모든 메트릭, 경고, 진단 로그를 확인할 수 있습니다.
 
>[!NOTE]
>**클래식 메트릭**을 사용하면 좋지 않습니다.
>

## <a name="expressroute-metrics"></a>익스프레스루트 메트릭

**메트릭을**보려면 *Azure 모니터* 페이지로 이동하여 *메트릭을 클릭합니다.* **ExpressRoute** 메트릭을 보려면 리소스 유형 *ExpressRoute 회로별로 필터링합니다.* 전역 **도달** 메트릭을 보려면 리소스 유형 *ExpressRoute 회로별로* 필터링하고 전역 도달이 활성화된 ExpressRoute 회로 리소스를 선택합니다. **ExpressRoute 직접** 메트릭을 보려면 *ExpressRoute 포트별로*리소스 유형을 필터링합니다. 

메트릭을 선택하면 기본 집계가 적용됩니다. 선택적으로 다른 차원의 메트릭을 표시하는 분할을 적용할 수 있습니다.

### <a name="available-metrics"></a>사용 가능한 메트릭
|**메트릭**|**범주**|**치수**|**특징**|
| --- | --- | --- | --- |
|ARP 가용성|가용성|<ui><li>피어(기본/보조 익스프레스루트 라우터)</ui></li><ui><li> 피어링 유형(개인/공용/마이크로소프트)</ui></li>|ExpressRoute|
|Bgp 가용성|가용성|<ui><li> 피어(기본/보조 익스프레스루트 라우터)</ui></li><ui><li> 피어링 유형</ui></li>|ExpressRoute|
|BitsInPerSecond|트래픽|<ui><li> 피어링 유형(익스프레스루트)</ui></li><ui><li>링크(익스프레스루트 다이렉트)</ui></li>| <li> ExpressRoute</li><li>ExpressRoute Direct|
|BitsOutPerSecond|트래픽| <ui><li>피어링 유형(익스프레스루트)</ui></li><ui><li> 링크(익스프레스루트 다이렉트) | <ui><li>익스프레스루트<ui><li>ExpressRoute Direct</ui></li> |
|글로벌 리치비트인퍼초|트래픽|<ui><li>피어회로스키(서비스 키)</ui></li>|Global Reach|
|글로벌 리치비트아웃퍼초|트래픽|<ui><li>피어회로스키(서비스 키)</ui></li>|Global Reach|
|관리 상태|물리적 연결|링크|ExpressRoute Direct|
|라인 프로토콜|물리적 연결|링크|ExpressRoute Direct|
|Rx라이트 레벨|물리적 연결|<ui><li>링크</ui></li><ui><li>레인</ui></li>|ExpressRoute Direct|
|Tx라이트레벨|물리적 연결|<ui><li>링크</ui></li><ui><li>레인</ui></li>|ExpressRoute Direct|
>[!NOTE]
>*글로벌 글로벌 리치비트인퍼초와* *글로벌글로벌리치비트아웃퍼초를* 사용하면 하나 이상의 글로벌 리치 연결이 설정된 경우에만 표시됩니다.
>

## <a name="circuits-metrics"></a>회로 메트릭

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>비트 인/아웃 - 모든 피어링에 대한 메트릭

지정된 ExpressRoute 회로에서 모든 피어링에서 메트릭을 볼 수 있습니다.

![회로 메트릭](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

### <a name="bits-in-and-out---metrics-per-peering"></a>비트 인/아웃 - 피어링당 메트릭

프라이빗, 공용 및 Microsoft 피어링에 대한 메트릭을 비트/초 단위로 볼 수 있습니다.

![피어링당 메트릭](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

### <a name="bgp-availability---split-by-peer"></a>BGP 가용성 - 피어별 분할  

피어링 및 피어(기본 및 보조 ExpressRoute 라우터)에서 BGP의 거의 실시간 가용성을 볼 수 있습니다. 이 대시보드는 개인 피어링을 위한 기본 BGP 세션과 개인 피어링을 위한 두 번째 BGP 세션을 표시합니다. 

![피어당 BGP 가용성](./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg) 

### <a name="arp-availability---split-by-peering"></a>ARP 가용성 - 피어링으로 분할  

피어링 및 피어(기본 및 보조 ExpressRoute 라우터)에서 [ARP의](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager) 거의 실시간 가용성을 볼 수 있습니다. 이 대시보드는 두 피어 모두에서 개인 피어링 ARP 세션을 보여 주지만 피어링 간에 Microsoft 피어링을 완료합니다. 기본 집계(평균)는 두 피어 모두에서 사용되었습니다.  

![피어당 ARP 가용성](./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg) 

## <a name="expressroute-direct-metrics"></a>익스프레스루트 직접 메트릭

### <a name="admin-state---split-by-link"></a>관리 상태 - 링크별로 분할
ExpressRoute 직접 포트 쌍의 각 링크에 대한 관리 상태를 볼 수 있습니다.

![er 직접 관리자 상태](./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg)

### <a name="bits-in-per-second---split-by-link"></a>초당 비트 - 링크로 분할
ExpressRoute 직접 포트 쌍의 두 링크에서 초당 비트를 볼 수 있습니다. 

![초당 er 직접 비트](./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg)

### <a name="bits-out-per-second---split-by-link"></a>초당 비트 - 링크로 분할
ExpressRoute Direct 포트 쌍의 두 링크에서 초당 비트를 볼 수도 있습니다. 

![초당 직접 비트 아웃 er](./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg)

### <a name="line-protocol---split-by-link"></a>라인 프로토콜 - 링크로 분할
ExpressRoute 직접 포트 쌍의 각 링크에서 라인 프로토콜을 볼 수 있습니다.

![er 직통 프로토콜](./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg)

### <a name="rx-light-level---split-by-link"></a>Rx 라이트 레벨 - 링크로 분할
각 포트에 대해 Rx 라이트 레벨(ExpressRoute Direct 포트가 **수신하는**라이트 레벨)을 볼 수 있습니다. 건강한 Rx 광 수준은 일반적으로 -10 ~ 0 dBm 범위 내에 속합니다.

![er 직선 Rx 라이트 레벨](./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg)

### <a name="tx-light-level---split-by-link"></a>Tx 라이트 레벨 - 링크로 분할
각 포트에 대해 Tx 라이트 레벨(ExpressRoute Direct 포트가 **전송하는**라이트 레벨)을 볼 수 있습니다. 건강한 Tx 광 수준은 일반적으로 -10 ~ 0 dBm 범위 내에 속합니다.

![er 직선 Rx 라이트 레벨](./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg)

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>ExpressRoute 게이트웨이 연결(비트/초)

![게이트웨이 연결](./media/expressroute-monitoring-metrics-alerts/erconnections.jpg )

## <a name="alerts-for-expressroute-gateway-connections"></a>ExpressRoute 게이트웨이 연결에 대한 경고

1. 경고를 구성하려면 **Azure Monitor**로 이동하여 **경고**를 클릭합니다.

   ![경고](./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg)

2. **+대상 선택**을 클릭하고 ExpressRoute 게이트웨이 연결 리소스를 선택합니다.

   ![대상]( ./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg)
3. 경고 세부 정보를 정의합니다.

   ![작업 그룹](./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg)

4. 작업 그룹을 정의하고 추가합니다.

   ![작업 그룹 추가](./media/expressroute-monitoring-metrics-alerts/actiongroup.png)

## <a name="alerts-based-on-each-peering"></a>각 피어링을 기반으로 한 경고

 ![대상](./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg)

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>회로에서 활동 로그에 대한 경고 구성

**경고 조건**에서 신호 유형으로 **활동 로그**를 선택하고 신호를 선택할 수 있습니다.

  ![다른 대상](./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg)
  
## <a name="next-steps"></a>다음 단계

ExpressRoute 연결을 구성합니다.
  
  * [회로 만들기 및 수정](expressroute-howto-circuit-arm.md)
  * [피어링 구성 만들기 및 수정](expressroute-howto-routing-arm.md)
  * [VNet을 ExpressRoute 회로에 연결](expressroute-howto-linkvnet-arm.md)
