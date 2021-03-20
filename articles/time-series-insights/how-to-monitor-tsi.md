---
title: 모니터링 Time Series Insights | Microsoft Docs
description: 가용성, 성능 및 작업에 대 한 Time Series Insights를 모니터링 합니다.
author: deepakpalled
ms.author: lyhughes
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2020
ms.custom: lyrana
ms.openlocfilehash: a46ddeddfcefcd4d6e7f87747fe36cfc6ec82e35
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101737565"
---
# <a name="monitoring-time-series-insights"></a>모니터링 Time Series Insights

Azure 리소스를 사용하는 중요한 애플리케이션 및 비즈니스 프로세스가 있는 경우 이와 같은 리소스의 가용성, 성능 및 작업을 모니터링할 수 있습니다. 이 문서에서는 Time Series Insights에서 생성 되는 모니터링 데이터 및 Azure Monitor의 기능을 사용 하 여이 데이터를 분석 하 고 경고 하는 방법에 대해 설명 합니다.

## <a name="monitor-overview"></a>모니터링 개요

각 Time Series Insights 환경에 대 한 Azure Portal의 **개요** 페이지에는 받은 메시지 수와 저장 된 바이트 수와 같은 리소스 사용에 대 한 간략 한 보기가 포함 되어 있습니다. 이 정보는 유용 하지만이 창에서 적은 양의 모니터링 데이터를 사용할 수 있습니다. 이러한 데이터 중 일부는 자동으로 수집 되며, 리소스를 만드는 즉시 분석에 사용할 수 있습니다. 약간의 구성을 통해 추가적인 데이터 수집 형식을 사용할 수 있습니다.

## <a name="what-is-azure-monitor"></a>Azure Monitor 정의

Time Series Insights는 Azure에서 전체 stack 모니터링 서비스인 [Azure Monitor](../azure-monitor/overview.md)를 사용 하 여 모니터링 데이터를 만들며,이를 통해 다른 클라우드 및 온-프레미스의 리소스 외에도 azure 리소스를 모니터링할 수 있는 완전 한 기능 집합을 제공 합니다.

다음 개념을 설명하는 [Azure Monitor를 사용하여 Azure 리소스 모니터링 문서](../azure-monitor/essentials/monitor-azure-resource.md)로 시작하세요.

- Azure Monitor란?
- 모니터링과 관련된 비용
- Azure에서 수집된 데이터 모니터링
- 데이터 수집 구성
- 모니터링 데이터를 분석하고 경고하는 Azure의 표준 도구

다음 섹션에서는 Azure Time Series Insights에 대해 수집 된 특정 데이터를 설명 하 여이 문서를 작성 합니다. 또한이 섹션에서는 Azure tools를 사용 하 여 데이터 수집을 구성 하 고이 데이터를 분석 하는 예제를 제공 합니다.

> [!TIP]
> Azure Monitor와 관련 된 비용을 이해 하려면 [사용량 및 예상 비용](../azure-monitor//usage-estimated-costs.md)을 참조 하세요. 데이터가 Azure Monitor에 표시 되는 데 걸리는 시간을 이해 하려면 [로그 데이터 수집 시간](../azure-monitor/logs/data-ingestion-time.md)을 참조 하세요.

## <a name="monitoring-data-from-azure-time-series-insights"></a>Azure Time Series Insights에서 데이터 모니터링

Azure Time Series Insights는 [azure 리소스의 데이터 모니터링](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)에 설명 된 다른 azure 리소스와 동일한 종류의 모니터링 데이터를 수집 합니다. 

수집할 수 있는 로그 및 메트릭에 대 한 자세한 내용은 [Azure Time Series Insights 모니터링 데이터 참조](how-to-monitor-tsi-reference.md) 를 참조 하세요.

## <a name="collection-and-routing"></a>수집 및 라우팅

플랫폼 메트릭은 자동으로 수집 되 고 저장 되지만 진단 설정을 사용 하 여 다른 위치로 라우팅될 수 있습니다.

리소스 로그는 진단 설정을 만들고 하나 이상의 위치로 라우팅할 때까지 수집 및 저장되지 않습니다.
Azure Portal, CLI 또는 PowerShell을 사용한 진단 설정 만들기의 자세한 프로세스는 [Azure에서 플랫폼 로그 및 메트릭을 수집하는 진단 설정 만들기](../azure-monitor/essentials/diagnostic-settings.md)를 참조하세요. 진단 설정을 만들 때 수집할 로그 범주를 지정합니다.

Azure Time Series Insights에 대 한 다음 범주에서 로그를 수집할 수 있습니다.

   | 범주 | 설명 |
   |---|---|
   | 수신  | 수신 범주는 수신 파이프라인에서 발생 하는 오류를 추적 합니다. 이 범주에는 이벤트를 받을 때 발생 하는 오류 (예: 이벤트 원본에 연결 하지 못한 경우) 및 이벤트 처리 (예: 이벤트 페이로드를 구문 분석할 때 발생 하는 오류)가 포함 됩니다. |

## <a name="analyzing-metrics"></a>메트릭 분석

Azure Monitor 메뉴에서 메트릭을 열어 다른 Azure 서비스의 메트릭과 함께 Azure Time Series Insights에 대 한 메트릭을 분석할 수 있습니다. 이 도구 사용에 대한 자세한 내용은 [Azure 메트릭 탐색기 시작](../azure-monitor/essentials/metrics-getting-started.md)을 참조하세요.

수집 되는 플랫폼 메트릭의 목록은 [모니터링 Azure Time Series Insights 데이터 참조](how-to-monitor-tsi-reference.md#metrics) 를 참조 하세요.

이 예에서는 Azure Time Series Insights 환경으로 모든 이벤트 원본에서 받은 바이트 수를 보여 줍니다.

수신 **수신 바이트** [ ![ Azure 시계열 수신 수신 바이트](media/how-to-monitor-tsi/ingress-received-bytes.png)](media/how-to-monitor-tsi/ingress-received-bytes.png#lightbox) 수

이 예에서는 Azure Time Series Insights 환경에서 성공적으로 처리 되 고 쿼리에 사용할 수 있는 바이트 수를 보여 줍니다.

**수신 저장 바이트** [ ![ Azure 시계열 수신 저장 된 바이트](media/how-to-monitor-tsi/ingress-stored-bytes.png)](media/how-to-monitor-tsi/ingress-stored-bytes.png#lightbox)

## <a name="analyzing-logs"></a>로그 분석
리소스 로그는 스토리지 계정의 BLOB으로, 이벤트 데이터로 또는 로그 분석 쿼리를 통해 액세스할 수 있습니다.

Azure Monitor 로그의 데이터는 각 테이블에 고유한 속성 집합이 있는 테이블에 저장됩니다.

Azure Monitor의 모든 리소스 로그에는 동일한 필드와 그 뒤에 오는 서비스별 필드가 있습니다. 공용 스키마는 [Azure Monitor 리소스 로그 스키마](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema)에 설명 되어 있습니다. Azure Time Series Insights에 대해 수집 되는 리소스 로그의 유형 목록은 [Azure Time Series Insights 모니터링 데이터 참조](how-to-monitor-tsi-reference.md#resource-logs)를 참조 하세요.

Azure Time Series Insights은 다음 테이블에 데이터를 저장 합니다.

| 테이블 | 설명 |
|:---|:---|
| TSIIngress | 수신 범주의 데이터를 저장 하는 테이블입니다. 수신 범주는 수신 파이프라인에서 발생 하는 오류를 추적 합니다. 이 범주에는 이벤트를 받을 때 발생 하는 오류 (예: 이벤트 원본에 연결 하지 못한 경우) 및 이벤트 처리 (예: 이벤트 페이로드를 구문 분석할 때 발생 하는 오류)가 포함 됩니다.

Azure Monitor 로그에 데이터를 라우팅하려면 Log Analytics 작업 영역에 리소스 로그 또는 플랫폼 메트릭을 전송 하는 진단 설정을 만들어야 합니다. 자세히 알아보려면 [수집 및 라우팅](../iot-hub/monitor-iot-hub.md#collection-and-routing)을 참조 하세요.

## <a name="sample-queries"></a>샘플 쿼리

Azure Time Series Insights 환경을 모니터링 하는 데 사용할 수 있는 쿼리는 다음과 같습니다.

+ 지난 5 일 동안 발생 한 이벤트 원본 연결 실패에 대 한 세부 정보를 가져옵니다.

    ```Kusto
   TSIIngress
   | where OperationName == "Microsoft.TimeSeriesInsights/environments/eventsources/ingress/connect"
   | where _ResourceId contains "<your environment name, event source name, or the full event source resource URL>"
   | where TimeGenerated > ago(5d)

    ```
+ 지난 5 일 동안 받은 잘못 된 메시지에 대 한 세부 정보 가져오기:

    ```Kusto
   TSIIngress
   | where OperationName == "Microsoft.TimeSeriesInsights/environments/eventsources/ingress/deserialize"
   | where _ResourceId contains "<your environment name, event source name, or the full event source resource URL>"
   | where TimeGenerated > ago(5d)

    ```

## <a name="alerts"></a>경고

Azure Monitor 경고는 모니터링 데이터에서 중요한 조건이 발견될 때 사용자에게 사전에 알립니다. 이를 통해 고객이 알기 전에 시스템 문제를 식별하고 해결할 수 있습니다. [메트릭](../azure-monitor/alerts/alerts-metric-overview.md), [로그](../azure-monitor/alerts/alerts-unified-log.md) 및 [활동 로그](../azure-monitor/alerts/activity-log-alerts.md)에서 경고를 설정할 수 있습니다. 서로 다른 유형의 경고에는 장점과 단점이 있습니다.

플랫폼 메트릭을 기반으로 경고 규칙을 만들 때 개수 단위로 수집 되는 Time Series Insights 플랫폼 메트릭의 경우 일부 집계를 사용 하지 않거나 사용할 수 없는 경우도 있습니다.

## <a name="next-steps"></a>다음 단계

* Azure Time Series Insights에서 만든 로그 및 메트릭에 대 한 참조는 [Azure Time Series Insights 모니터링 데이터 참조](how-to-monitor-tsi-reference.md) 를 참조 하세요.
* Azure 리소스 모니터링에 대한 자세한 내용은 [Azure Monitor를 사용하여 Azure 리소스 모니터링](../azure-monitor/essentials/monitor-azure-resource.md)을 참조하세요.