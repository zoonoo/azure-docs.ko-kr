---
title: Azure Service Bus 모니터링
description: Azure Monitor를 사용하여 Azure Service Bus의 메트릭에 대한 경고를 보고 분석하고 만드는 방법을 알아봅니다.
ms.topic: conceptual
ms.custom: subject-monitoring
ms.date: 05/18/2021
ms.openlocfilehash: 90930a5f76fd1aaccd7968febdf7dbdbd684ab28
ms.sourcegitcommit: a9f131fb59ac8dc2f7b5774de7aae9279d960d74
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110192134"
---
# <a name="monitor-azure-service-bus"></a>Azure Service Bus 모니터
Azure 리소스를 사용하는 중요한 애플리케이션 및 비즈니스 프로세스가 있는 경우 이와 같은 리소스의 가용성, 성능 및 작업을 모니터링할 수 있습니다. 이 문서에서는 Azure Service Bus에서 생성되는 모니터링 데이터와 Azure Monitor 기능을 사용하여 이 데이터에 대해 분석하고 경고하는 방법을 설명합니다.

## <a name="what-is-azure-monitor"></a>Azure Monitor란?
Azure Service Bus는 Azure의 전체 스택 모니터링 서비스인 [Azure Monitor](../azure-monitor/overview.md)를 사용하여 모니터링 데이터를 만듭니다. Azure Monitor는 Azure 리소스를 모니터링하기 위한 완전한 기능 세트를 제공합니다. 또한 다른 클라우드 및 온-프레미스의 리소스도 모니터링할 수 있습니다.

다음 개념을 설명하는 [Azure Monitor를 사용하여 Azure 리소스 모니터링 문서](../azure-monitor/essentials/monitor-azure-resource.md)로 시작하세요.

- Azure Monitor란?
- 모니터링과 관련된 비용
- Azure에서 수집된 데이터 모니터링
- 데이터 수집 구성
- 모니터링 데이터를 분석하고 경고하는 Azure의 표준 도구

이 문서의 다음 섹션에서는 Azure Service Bus에 대해 수집하는 데이터를 설명합니다. 또한 이 섹션에서는 Azure 도구를 사용하여 데이터 수집을 구성하고 이 데이터를 분석하는 예제를 제공합니다.

> [!TIP]
> Azure Monitor와 관련된 비용을 이해하려면 [사용량 및 예상 비용](../azure-monitor//usage-estimated-costs.md)을 참조하세요. 데이터가 Azure Monitor에 표시되는 데 걸리는 시간을 이해하려면 [로그 데이터 수집 시간](../azure-monitor/logs/data-ingestion-time.md)을 참조하세요.

## <a name="monitoring-data-from-azure-service-bus"></a>Azure Service Bus 데이터 모니터링
Azure Service Bus는 [Azure 리소스의 모니터링 데이터](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)에 설명된 다른 Azure 리소스와 동일한 종류의 모니터링 데이터를 수집합니다. 

Azure Service Bus에서 만든 로그 및 메트릭에 대한 자세한 내용은 [Azure Service BusB 모니터링 데이터 참조](monitor-service-bus-reference.md)를 참조하세요.

## <a name="collection-and-routing"></a>수집 및 라우팅
플랫폼 메트릭 및 활동 로그는 자동으로 수집되고 저장되지만 진단 설정을 사용하여 다른 위치로 라우팅할 수 있습니다.  

리소스 로그는 진단 설정을 만들고 하나 이상의 위치로 라우팅할 때까지 수집 및 저장되지 않습니다.

Azure Portal, CLI 또는 PowerShell을 사용한 진단 설정 만들기의 자세한 프로세스는 [Azure에서 플랫폼 로그 및 메트릭을 수집하는 진단 설정 만들기](../azure-monitor/essentials/diagnostic-settings.md)를 참조하세요. 진단 설정을 만들 때 수집할 로그 범주를 지정합니다. Azure Service Bus의 범주는 [Azure Service Bus 모니터링 데이터 참조](monitor-service-bus-reference.md#resource-logs)에 나열되어 있습니다.

### <a name="azure-storage"></a>Azure Storage 
진단 로깅 정보는 **insights-logs-operationlogs** 및 **insights-metrics-pt1m** 이라는 컨테이너에 저장됩니다.

작업 로그의 샘플 URL: `https://<Azure Storage account>.blob.core.windows.net/insights-logs-operationallogs/resourceId=/SUBSCRIPTIONS/<Azure subscription ID>/RESOURCEGROUPS/<Resource group name>/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/<Namespace name>/y=<YEAR>/m=<MONTH-NUMBER>/d=<DAY-NUMBER>/h=<HOUR>/m=<MINUTE>/PT1H.json`. 메트릭 로그의 URL은 비슷합니다. 

### <a name="azure-event-hubs"></a>Azure Event Hubs
진단 로깅 정보는 **insights-logs-operationlogs** 및 **insights-metrics-pt1m** 이라는 이름의 이벤트 허브에 저장됩니다. 사용자 고유의 이벤트 허브를 선택할 수도 있습니다. 

### <a name="log-analytics"></a>Log Analytics 
진단 로깅 정보는 **AzureDiagnostics** 및 **AzureMetrics** 라는 테이블에 저장됩니다. 

### <a name="sample-operational-log-output-formatted"></a>샘플 작업 로그 출력(형식 지정)

```json
{
    "Environment": "PROD",
    "Region": "East US",
    "ScaleUnit": "PROD-BL2-002",
    "ActivityId": "a097a88a-33e5-4c9c-9c64-20f506ec1375",
    "EventName": "Retrieve Namespace",
    "resourceId": "/SUBSCRIPTIONS/<Azure subscription ID>/RESOURCEGROUPS/SPSBUS0213RG/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SPSBUS0213NS",
    "SubscriptionId": "<Azure subscription ID>",
    "EventTimeString": "5/18/2021 3:25:55 AM +00:00",
    "EventProperties": "{\"SubscriptionId\":\"<Azure subscription ID>\",\"Namespace\":\"spsbus0213ns\",\"Via\":\"https://spsbus0213ns.servicebus.windows.net/$Resources/topics?api-version=2017-04&$skip=0&$top=100\",\"TrackingId\":\"a097a88a-33e5-4c9c-9c64-20f506ec1375_M8CH3_M8CH3_G8\"}",
    "Status": "Succeeded",
    "Caller": "rpfrontdoor",
    "category": "OperationalLogs"
}
```

### <a name="sample-metric-log-output-formatted"></a>샘플 메트릭 로그 출력(형식 지정)

```json
{
    "count": 1,
    "total": 4,
    "minimum": 4,
    "maximum": 4,
    "average": 4,
    "resourceId": "/SUBSCRIPTIONS/<Azure subscription ID>/RESOURCEGROUPS/SPSBUS0213RG/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SPSBUS0213NS",
    "time": "2021-05-18T03:27:00.0000000Z",
    "metricName": "IncomingMessages",
    "timeGrain": "PT1M"
}
```

> [!IMPORTANT]
> 이러한 설정을 사용하려면 추가 Azure 서비스(스토리지 계정, 이벤트 허브 또는 Log Analytics)가 필요하므로 비용이 늘어날 수 있습니다. 예상 비용을 계산하려면 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator)를 방문하세요.

> [!NOTE]
> 진단 설정에서 메트릭을 사용하도록 설정하면 현재는 스토리지 계정, 이벤트 허브 또는 Log Analytics로 전송되는 정보에 차원 정보가 포함되지 않습니다.

수집할 수 있는 메트릭 및 로그에 대해서는 다음 섹션에서 설명합니다.

## <a name="analyzing-metrics"></a>메트릭 분석
Service Bus 네임스페이스의 홈페이지에 있는 **Azure Monitor** 섹션에서 **측정 항목** 을 선택하여 다른 Azure 서비스의 측정 항목과 함께 Azure Service Bus에 대한 측정 항목을 분석할 수 있습니다. 이 도구 사용에 대한 자세한 내용은 [Azure 메트릭 탐색기 시작](../azure-monitor/essentials/metrics-getting-started.md)을 참조하세요. 수집되는 플랫폼 메트릭의 목록은 [Azure Service Bus 모니터링 데이터 참조 메트릭](monitor-service-bus-reference.md#metrics)을 참조하세요.

![Service Bus 네임스페이스가 선택된 메트릭 탐색기](./media/monitor-service-bus/metrics.png)

[Azure Monitor에서 지원되는 모든 리소스 메트릭](../azure-monitor/essentials/metrics-supported.md) 목록을 참조하세요.

> [!TIP]
> Azure Monitor 메트릭 데이터는 90일 동안 사용할 수 있습니다. 그러나 차트를 만드는 경우 30일 동안만 시각화할 수 있습니다. 예를 들어 90일 시각화하려는 경우 90일 기간 중 30일에 해당하는 차트 3개로 분할해야 합니다.

### <a name="filtering-and-splitting"></a>필터링 및 분할
차원을 지원하는 메트릭의 경우 차원 값으로 필터를 적용할 수 있습니다. 예를 들어, `EntityName`이 큐 또는 주제의 이름으로 설정된 필터를 추가합니다. 또한 차원별로 메트릭을 분할하여 메트릭의 세그먼트가 서로 어떻게 비교되는지 시각화할 수 있습니다. 필터링 및 분할에 대한 자세한 내용은 [Azure Monitor의 고급 기능](../azure-monitor/essentials/metrics-charts.md)을 참조하세요.

## <a name="analyzing-logs"></a>로그 분석
Azure Monitor Log Analytics를 사용하려면 진단 구성을 만들고 __Log Analytics에 정보 보내기__ 를 사용하도록 설정해야 합니다. 자세한 내용은 [컬렉션 및 라우팅](#collection-and-routing) 섹션을 참조하세요. Azure Monitor 로그의 데이터는 각 테이블에 고유한 속성 집합이 있는 테이블에 저장됩니다. Azure Service Bus는 **AzureDiagnostics** 및 **AzureMetrics** 테이블에 데이터를 저장합니다.

> [!IMPORTANT]
> Azure Service Bus 메뉴에서 **로그** 를 선택하면 쿼리 범위가 현재 작업 영역으로 설정된 Log Analytics가 열립니다. 즉, 로그 쿼리에는 해당 리소스의 데이터만 포함됩니다. 다른 Azure 서비스의 데이터 또는 다른 데이터베이스의 데이터를 포함하는 쿼리를 실행하려면 **Azure Monitor** 메뉴에서 **로그** 를 선택합니다. 자세한 내용은 [Azure Monitor Log Analytics의 로그 쿼리 범위 및 시간 범위](../azure-monitor/logs/scope.md)를 참조하세요.

로그 및 측정 항목에 대한 자세한 참조는 [Azure Service Bus 모니터링 데이터 참조](monitor-service-bus-reference.md)를 참조하세요.

### <a name="sample-kusto-queries"></a>샘플 Kusto 쿼리

> [!IMPORTANT]
> Azure Service Bus 메뉴에서 **로그** 를 선택하면 현재 Azure Service Bus 네임스페이스로 설정된 쿼리 범위와 함께 Log Analytics가 열립니다. 즉, 로그 쿼리에는 해당 리소스의 데이터만 포함됩니다. 다른 작업 영역의 데이터 또는 다른 Azure 서비스의 데이터를 포함하는 쿼리를 실행하려면 **Azure Monitor** 메뉴에서 **로그** 를 선택합니다. 자세한 내용은 [Azure Monitor Log Analytics의 로그 쿼리 범위 및 시간 범위](../azure-monitor/logs/scope.md)를 참조하세요.

다음은 Azure Service Bus 리소스를 모니터링하는 데 사용할 수 있는 샘플 쿼리입니다. 

+ 지난 7일 동안의 관리 작업 내용을 가져옵니다. 

    ```Kusto
    AzureDiagnostics
    | where TimeGenerated > ago(7d)
    | where ResourceProvider =="MICROSOFT.SERVICEBUS"
    | where Category == "OperationalLogs"
    | summarize count() by EventName_s, _ResourceId
    ```

+ "키를 찾을 수 없음" 오류가 발생한 키 자격 증명 모음에 대한 액세스 시도를 가져옵니다.

    ```Kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.SERVICEBUS" 
    | where Category == "Error" and OperationName == "wrapkey"
    | project Message, _ResourceId
    ```

+ 지난 7일 동안의 오류 발생

    ```Kusto
    AzureDiagnostics
    | where TimeGenerated > ago(7d)
    | where ResourceProvider =="MICROSOFT.SERVICEBUS"
    | where Category == "Error" 
    | summarize count() by EventName_s, _ResourceId
    ```

+ 키를 사용하지 않도록 설정하거나 복원하기 위해 키 자격 증명 모음으로 수행되는 작업을 확인합니다.

    ```Kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.SERVICEBUS"
    | where (Category == "info" and (OperationName == "disable" or OperationName == "restore"))
    | project Message, _ResourceId
    ```

+ 자동 삭제된 모든 항목 가져오기

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.SERVICEBUS"
    | where Category == "OperationalLogs"
    | where EventName_s startswith "AutoDelete"
    | summarize count() by EventName_s, _ResourceId    
    ```
    
## <a name="alerts"></a>경고
Service Bus 네임스페이스의 홈페이지에 있는 **Azure Monitor** 섹션에서 **경고** 를 선택하여 Azure Service Bus에 대한 경고에 액세스할 수 있습니다. 경고 만들기에 대한 자세한 내용은 [Azure Monitor를 사용하여 메트릭 경고 만들기, 보기 및 관리](../azure-monitor/alerts/alerts-metric.md)를 참조하세요.


## <a name="next-steps"></a>다음 단계

- 로그 및 메트릭에 대한 참조는 [Azure Service Bus 모니터링 데이터 참조](monitor-service-bus-reference.md)를 참조하세요.
- Azure 리소스 모니터링에 대한 자세한 내용은 [Azure Monitor를 사용하여 Azure 리소스 모니터링](../azure-monitor/essentials/monitor-azure-resource.md)을 참조하세요.
