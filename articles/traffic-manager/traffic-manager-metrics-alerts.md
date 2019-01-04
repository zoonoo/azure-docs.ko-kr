---
title: Azure Traffic Manager의 메트릭 및 경고 | Microsoft Docs
description: 이 문서에서는 Azure에서 Traffic Manager에 사용할 수 있는 메트릭을 설명합니다.
services: traffic-manager
documentationcenter: ''
author: KumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2018
ms.author: kumud
ms.openlocfilehash: bb7817b082da11de3071925d01a3402902410a6f
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53437972"
---
# <a name="traffic-manager-metrics-and-alerts"></a>Traffic Manager의 메트릭 및 경고

Traffic Manager는 여러 라우팅 방법과 엔드포인트 모니터링 옵션을 포함한 DNS 기반 부하 분산 기능을 제공합니다. 이 문서에서는 고객이 사용할 수 있는 메트릭 및 관련 경고에 대해 설명합니다. 

## <a name="metrics-available-in-traffic-manager"></a>Traffic Manager에서 사용할 수 있는 메트릭 

고객이 Traffic Manager의 사용 및 프로필에 속한 엔드포인트의 상태를 파악하기 위해 사용할 수 있는 해당 프로필에 따라 Traffic Manager에서 제공하는 메트릭은 다음과 같습니다.  

### <a name="queries-by-endpoint-returned"></a>반환된 엔드포인트별 쿼리
[이 메트릭](../azure-monitor/platform/metrics-supported.md)을 사용하여 지정한 기간 동안 Traffic Manager 프로필에서 처리된 쿼리 수를 확인합니다. 또한 Traffic Manager의 쿼리 응답에서 엔드포인트가 반환된 횟수를 파악하는 데 도움이 되도록 엔드포인트 수준 세분성에서 동일한 정보를 볼 수도 있습니다.

아래 예의 그림 1에서는 Traffic Manager 프로필에서 반환된 모든 쿼리 응답을 모든 표시하고 있습니다. 

  
![Traffic Manager 메트릭 - 모든 쿼리에 대한 집계 보기](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-queries-aggregate-view.png)

*그림 1: 모든 쿼리가 포함된 집계 보기*
  
그림 2에서는 동일한 정보가 표시되지만 엔드포인트별로 분할되어 있습니다. 결과적으로, 특정 엔드포인트가 반환된 쿼리 응답 볼륨을 확인할 수 있습니다.

![Traffic Manager 메트릭 - 엔드포인트별 쿼리 볼륨에 대한 분할 보기](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-query-volume-per-endpoint.png)

*그림 2: 반환된 엔드포인트별 쿼리 볼륨이 표시된 분할 보기*

## <a name="endpoint-status-by-endpoint"></a>엔드포인트별 엔드포인트 상태
[이 메트릭](../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles)을 사용하여 프로필의 엔드포인트에 대한 상태를 파악합니다. 다음 두 값을 사용합니다.
 - 엔드포인트가 작동 중인 경우 **1**을 사용합니다.
 - 엔드포인트가 중단된 경우 **0**을 사용합니다.

이 메트릭은 모든 메트릭의 상태를 나타내는 집계 값을 표시하거나(그림 3), 특정 엔드포인트에 대한 상태를 분할하여 표시할 수 있습니다(그림 4 참조). 전자의 경우, 집계 수준이 **평균**으로 선택되면 이 메트릭의 값은 모든 엔드포인트의 상태에 대한 산술 평균입니다. 예를 들어 프로필에 두 개의 엔드포인트가 있고 하나만 정상 상태인 경우, 이 메트릭의 값은 그림 3과 같이 **0.50**이 됩니다. 


![Traffic Manager 메트릭 - 엔드포인트 상태에 대한 복합 보기](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-composite-view.png)

*그림 3: 엔드포인트 상태 메트릭에 대한 복합 보기 - "평균" 집계 선택*


![Traffic Manager 메트릭 - 엔드포인트 상태에 대한 분할 보기](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-split-view.png)

*그림 4: 엔드포인트 상태 메트릭에 대한 분할 보기*

이러한 메트릭은 [Azure Monitor 서비스](../azure-monitor/platform/metrics-supported.md)의 포털, [REST API](https://docs.microsoft.com/rest/api/monitor/), [Azure CLI](https://docs.microsoft.com/cli/azure/monitor) 및 [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights)을 통하거나 Traffic Manager의 포털 환경에 있는 메트릭 섹션을 통해 사용할 수 있습니다.

## <a name="alerts-on-traffic-manager-metrics"></a>Traffic Manager 메트릭에 대한 경고
Azure Monitor는 Traffic Manager의 메트릭 처리 및 표시 외에도 고객이 이러한 메트릭과 연결된 경고를 구성하고 받을 수 있도록 합니다. 경고가 발생하기 위해 이러한 메트릭에서 충족해야 하는 조건, 이러한 조건을 모니터링해야 하는 빈도 및 사용자에게 경고를 보내는 방법을 선택할 수 있습니다. 자세한 내용은 [Azure Monitor 경고 설명서](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
- [Azure Monitor 서비스](../azure-monitor/platform/metrics-supported.md)에 대해 자세히 알아봅니다.
- [Azure Monitor를 사용하여 새 차트를 만드는 방법](../azure-monitor/platform/metrics-charts.md#create-a-new-chart)을 알아봅니다.
