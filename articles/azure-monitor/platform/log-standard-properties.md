---
title: Azure Monitor 로그 레코드의 표준 속성 | Microsoft Docs
description: Azure Monitor 로그에서 여러 데이터 형식에 공통적인 속성에 대해 설명합니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/18/2019
ms.openlocfilehash: 252ddeb372744986df0b8ba9b742d0462a4e8202
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274477"
---
# <a name="standard-properties-in-azure-monitor-logs"></a>Azure 모니터 로그의 표준 속성
Azure Monitor Logs의 데이터는 고유한 속성 집합이 있는 특정 데이터 형식이 있는 [Log Analytics 작업 영역 또는 응용 프로그램 인사이트 응용 프로그램에 레코드 집합으로 저장됩니다.](../log-query/logs-structure.md) 많은 데이터 형식에는 여러 형식에 공통적인 표준 속성이 있습니다. 이 문서에서는 이러한 속성에 대해 설명하고 쿼리에 속성을 사용하는 방법의 예를 제공합니다.

> [!NOTE]
> 일부 표준 속성은 Log Analytics의 스키마 보기 또는 intellisense에 표시되지 않으며 출력에 속성을 명시적으로 지정하지 않는 한 쿼리 결과에 표시되지 않습니다.

## <a name="timegenerated-and-timestamp"></a>시간 생성 및 타임스탬프
**TimeGenerated(로그** 분석 작업 영역) 및 **타임스탬프(애플리케이션** 인사이트 응용 프로그램) 속성에는 데이터 원본에서 레코드를 만든 날짜와 시간이 포함됩니다. 자세한 내용은 [Azure 모니터의 데이터 수집 시간을](data-ingestion-time.md) 참조하십시오.

**Timegenerated** 및 **타임스탬프는** 시간별로 필터링하거나 요약하는 데 사용할 공통 속성을 제공합니다. Azure 포털에서 보기 또는 대시보드에 대한 시간 범위를 선택하면 TimeGenerated 또는 타임스탬프를 사용하여 결과를 필터링합니다. 

### <a name="examples"></a>예

다음 쿼리는 이전 주의 각 날짜에 생성된 오류 이벤트 수를 반환합니다.

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

다음 쿼리는 이전 주에 매일 생성된 예외 수를 반환합니다.

```Kusto
exceptions
| where timestamp between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by timestamp asc 
```

## <a name="_timereceived"></a>\_시간 수신
TimeReceived 속성에는 Azure 클라우드의 Azure Monitor 인시스템 인션 지점에서 레코드를 받은 날짜와 시간이 포함됩니다. ** \_** 이는 데이터 원본과 클라우드 간의 대기 시간 문제를 식별하는 데 유용할 수 있습니다. 예를 들어 에이전트에서 데이터를 전송하는 데 지연을 일으키는 네트워킹 문제가 있습니다. 자세한 내용은 [Azure 모니터의 데이터 수집 시간을](data-ingestion-time.md) 참조하십시오.

다음 쿼리는 에이전트의 이벤트 레코드에 대해 시간별 평균 대기 시간을 제공합니다. 여기에는 에이전트에서 클라우드까지의 시간 및 로그 쿼리에 사용할 수 있는 레코드의 총 시간이 포함됩니다.

```Kusto
Event
| where TimeGenerated > ago(1d) 
| project TimeGenerated, TimeReceived = _TimeReceived, IngestionTime = ingestion_time() 
| extend AgentLatency = toreal(datetime_diff('Millisecond',TimeReceived,TimeGenerated)) / 1000
| extend TotalLatency = toreal(datetime_diff('Millisecond',IngestionTime,TimeGenerated)) / 1000
| summarize avg(AgentLatency), avg(TotalLatency) by bin(TimeGenerated,1hr)
``` 

## <a name="type-and-itemtype"></a>유형 및 항목유형
**유형(로그** 분석 작업 영역) 및 **itemType(응용** 프로그램 인사이트 응용 프로그램 응용 프로그램) 속성은 레코드가 검색된 테이블의 이름을 레코드 유형으로 생각할 수 있습니다. 이 속성은 `search` 연산자를 사용하여 여러 유형의 레코드를 구분하는 쿼리처럼 여러 테이블의 레코드를 결합하는 쿼리에 유용합니다. 경우에 따라 **Type** 대신 **$table**을 사용할 수도 있습니다.

### <a name="examples"></a>예
다음 쿼리는 지난 시간 동안 수집된 레코드 수를 유형별로 반환합니다.

```Kusto
search * 
| where TimeGenerated > ago(1h)
| summarize count() by Type

```
## <a name="_itemid"></a>\_Itemid
** \_ItemId** 속성에는 레코드에 대한 고유 식별자가 있습니다.


## <a name="_resourceid"></a>\_ResourceId
** \_ResourceId** 속성에는 레코드가 연결된 리소스에 대한 고유 식별자가 있습니다. 쿼리 범위를 특정 리소스의 레코드로만 제한하거나 여러 테이블의 관련 데이터를 조인하는 데 사용할 표준 속성을 제공합니다.

Azure 리소스의 경우 **_ResourceId** 값은 [Azure 리소스 ID URL](../../azure-resource-manager/templates/template-functions-resource.md)입니다. 이 속성은 현재 Azure 리소스로 제한되지만 온-프레미스 컴퓨터와 같은 Azure 외부 리소스로 확장될 예정입니다.

> [!NOTE]
> 일부 데이터 형식은 Azure 리소스 ID 또는 적어도 그 일부(예: 구독 ID)를 포함하는 필드를 이미 갖고 있습니다. 이러한 필드는 이전 버전과의 호환성을 위해 유지되지만, 보다 일관적인 _ResourceId를 사용하여 교차 상관 관계를 수행하는 것이 좋습니다.

### <a name="examples"></a>예
다음 쿼리는 각 컴퓨터에 대한 성능 및 이벤트 데이터를 조인합니다. 여기서는 ID가 _101_이고 프로세서 활용률이 50%를 초과하는 이벤트가 모두 표시됩니다.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

다음 쿼리는 _AzureActivity_ 레코드를 _SecurityEvent_ 레코드와 조인합니다. 이 컴퓨터에 로그인한 사용자가 포함된 활동 작업이 모두 표시됩니다.

```Kusto
AzureActivity 
| where  
    OperationName in ("Restart Virtual Machine", "Create or Update Virtual Machine", "Delete Virtual Machine")  
    and ActivityStatus == "Succeeded"  
| join kind= leftouter (    
   SecurityEvent 
   | where EventID == 4624  
   | summarize LoggedOnAccounts = makeset(Account) by _ResourceId 
) on _ResourceId  
```

다음 쿼리는 **_ResourceId** 구문 분석하고 Azure 구독당 청구된 데이터 볼륨을 집계합니다.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last 
```

여러 데이터 형식을 검색할 경우 비용이 많이 들기 때문에 이러한 `union withsource = tt *` 쿼리는 자주 사용하지 않도록 합니다.

## <a name="_isbillable"></a>\_IsBillable
** \_IsBillable** 속성은 수집된 데이터가 청구 가능한지 여부를 지정합니다. ** \_False와 동일한 IsBillable** 데이터는 무료로 수집되며 Azure 계정에 청구되지 않습니다. _false_

### <a name="examples"></a>예
비용이 청구되는 데이터 형식을 전송하는 컴퓨터 목록을 가져오려면 다음 쿼리를 사용합니다.

> [!NOTE]
> 다른 데이터 형식의 데이터 간을 검색할 경우 비용이 많이 들기 때문에 `union withsource = tt *`를 사용하는 쿼리는 자주 사용하지 않도록 합니다. 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

비용이 청구되는 데이터 형식을 전송하는 컴퓨터의 시간당 수를 반환하도록 이 쿼리를 다음과 같이 확장할 수 있습니다.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="_billedsize"></a>\_BilledSize
** \_BilledSize** 속성은 ** \_IsBillable이** true인 경우 Azure 계정에 청구되는 데이터 바이트의 크기를 지정합니다.


### <a name="examples"></a>예
컴퓨터당 수집된 청구 가능한 이벤트의 크기를 보려면 크기(바이트)를 제공하는 `_BilledSize` 속성을 사용합니다.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

구독당 청구 가능한 이벤트의 크기를 확인하려면 다음 쿼리를 사용합니다.

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId | sort by Bytes nulls last 
```

리소스 그룹당 청구 가능한 이벤트의 크기를 확인하려면 다음 쿼리를 사용합니다.

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/resourcegroups/" ResourceGroupName "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId, ResourceGroupName | sort by Bytes nulls last 

```


컴퓨터당 수집된 이벤트 수를 보려면 다음 쿼리를 사용합니다.

```Kusto
union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last
```

컴퓨터당 수집된 청구 가능한 이벤트 수를 보려면 다음 쿼리를 사용합니다. 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last
```

특정 컴퓨터에서 청구 가능한 데이터 형식의 수를 확인하려면 다음 쿼리를 사용합니다.

```Kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last 
```

## <a name="next-steps"></a>다음 단계

- [Azure Monitor 로그 데이터](../log-query/log-query-overview.md)가 저장되는 방법에 대해 자세히 알아보세요.
- [로그 쿼리 작성](../../azure-monitor/log-query/get-started-queries.md) 단원을 계속 진행하세요.
- [로그 쿼리에서 테이블 조인](../../azure-monitor/log-query/joins.md) 단원을 계속 진행하세요.
