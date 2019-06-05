---
title: 모니터링, 메트릭 및 경고 - Azure ExpressRoute | Microsoft Docs
description: 이 페이지에서는 ExpressRoute 모니터링에 대한 정보를 제공합니다.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: d78c110f3317f4dd9f16cbe243aeca437e9890a1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60364710"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>ExpressRoute 모니터링, 메트릭 및 경고

이 문서는 Azure Monitor를 사용하여 ExpressRoute 모니터링, 메트릭 및 경고를 이해하는 데 도움이 됩니다. Azure Monitor에서는 Azure 전체의 모든 메트릭, 경고, 진단 로그를 확인할 수 있습니다.
 
>[!NOTE]
>**클래식 메트릭**을 사용하면 좋지 않습니다.
>

## <a name="circuit-metrics"></a>회로 메트릭

**메트릭**으로 이동하려면 모니터링할 회로에 대한 ExpressRoute 페이지를 클릭합니다. **모니터링**에서 **메트릭**을 볼 수 있습니다. BitsInPerSecond 또는 BitsOutPerSecond 및 집계를 선택합니다. 경우에 따라 피어링당 메트릭을 표시하는 분할을 적용할 수도 있습니다.

![회로 메트릭](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

## <a name="metrics-per-peering"></a>피어링당 메트릭

프라이빗, 공용 및 Microsoft 피어링에 대한 메트릭을 비트/초 단위로 볼 수 있습니다.

![피어링당 메트릭](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

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
