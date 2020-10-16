---
title: Azure Monitor 로그 레코드의 표준 열 | Microsoft Docs
description: Azure Monitor 로그의 여러 데이터 형식에 공통적인 열에 대해 설명 합니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/09/2020
ms.openlocfilehash: 695b0b0ac06e63912ca0a471be3d96c148458c29
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92104243"
---
# <a name="standard-columns-in-azure-monitor-logs"></a>Azure Monitor 로그의 표준 열
Azure Monitor 로그의 데이터는 [Log Analytics 작업 영역 또는 Application Insights 응용 프로그램에](./data-platform-logs.md)각각 고유한 열 집합이 있는 특정 데이터 형식의 레코드 집합으로 저장 됩니다. 많은 데이터 형식에는 여러 형식에서 공통 되는 표준 열이 있습니다. 이 문서에서는 이러한 열에 대해 설명 하 고 쿼리에서 이러한 열을 사용 하는 방법에 대 한 예제를 제공 합니다.

Application Insights의 작업 영역 기반 응용 프로그램은 Log Analytics 작업 영역에 데이터를 저장 하 고 작업 영역의 다른 테이블과 동일한 표준 열을 사용 합니다. 클래식 응용 프로그램은 데이터를 별도로 저장 하 고이 문서에 지정 된 것과 다른 표준 열을 포함 합니다.

> [!NOTE]
> 표준 열 중 일부는 Log Analytics의 스키마 뷰나 intellisense에 표시 되지 않으며, 출력에서 열을 명시적으로 지정 하지 않는 한 쿼리 결과에 표시 되지 않습니다.

## <a name="timegenerated-and-timestamp"></a>TimeGenerated 및 timestamp
**Timegenerated** (Log Analytics 작업 영역) 및 **timestamp** (Application Insights 응용 프로그램) 열에는 데이터 원본에서 레코드를 만든 날짜와 시간이 포함 됩니다. 자세한 내용은 [Azure Monitor의 로그 데이터 수집 시간](data-ingestion-time.md) 을 참조 하세요.

**Timegenerated** 및 **timestamp** 는 시간별로 필터링 하거나 요약 하는 데 사용할 공용 열을 제공 합니다. Azure Portal에서 뷰나 대시보드의 시간 범위를 선택 하면 TimeGenerated 또는 timestamp를 사용 하 여 결과를 필터링 합니다. 

### <a name="examples"></a>예

다음 쿼리는 이전 주의 각 날짜에 생성된 오류 이벤트 수를 반환합니다.

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

다음 쿼리는 지난 주의 각 날짜에 대해 생성 된 예외 수를 반환 합니다.

```Kusto
exceptions
| where timestamp between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by timestamp asc 
```

## <a name="_timereceived"></a>\_TimeReceived
** \_ TimeReceived** 열에는 Azure 클라우드의 Azure Monitor 수집 지점에서 레코드를 받은 날짜와 시간이 포함 됩니다. 이는 데이터 원본과 클라우드 간의 대기 시간 문제를 식별 하는 데 유용할 수 있습니다. 예를 들어 에이전트에서 데이터를 전송 하는 동안 지연이 발생 하는 네트워킹 문제가 발생 합니다. 자세한 내용은 [Azure Monitor의 로그 데이터 수집 시간](data-ingestion-time.md) 을 참조 하세요.

다음 쿼리는 에이전트의 이벤트 레코드에 대 한 시간당 평균 대기 시간을 제공 합니다. 여기에는 에이전트에서 클라우드로의 시간 및 로그 쿼리에 사용할 수 있는 레코드의 총 시간이 포함 됩니다.

```Kusto
Event
| where TimeGenerated > ago(1d) 
| project TimeGenerated, TimeReceived = _TimeReceived, IngestionTime = ingestion_time() 
| extend AgentLatency = toreal(datetime_diff('Millisecond',TimeReceived,TimeGenerated)) / 1000
| extend TotalLatency = toreal(datetime_diff('Millisecond',IngestionTime,TimeGenerated)) / 1000
| summarize avg(AgentLatency), avg(TotalLatency) by bin(TimeGenerated,1hr)
``` 

## <a name="type-and-itemtype"></a>유형 및 itemType
**유형** (Log Analytics 작업 영역) 및 **itemType** (Application Insights 응용 프로그램) 열은 레코드가 검색 된 테이블의 이름을 포함 합니다 .이 테이블은 레코드 형식으로 생각할 수도 있습니다. 이 열은 연산자를 사용 하는 것과 같이 여러 테이블의 레코드를 결합 하 여 여러 형식의 레코드를 구분 하는 쿼리에 유용 `search` 합니다. 경우에 따라 **Type** 대신 **$table**을 사용할 수도 있습니다.

### <a name="examples"></a>예
다음 쿼리는 지난 시간 동안 수집된 레코드 수를 유형별로 반환합니다.

```Kusto
search * 
| where TimeGenerated > ago(1h)
| summarize count() by Type

```
## <a name="_itemid"></a>\_ItemId
** \_ ItemId** 열에는 레코드에 대 한 고유 식별자가 포함 됩니다.


## <a name="_resourceid"></a>\_ResourceId
** \_ ResourceId** 열에는 레코드가 연결 된 리소스에 대 한 고유 식별자가 포함 됩니다. 이를 통해 특정 리소스의 레코드만 쿼리 범위를 표시 하거나 관련 데이터를 여러 테이블에 조인 하는 데 사용할 표준 열을 제공 합니다.

Azure 리소스의 경우 **_ResourceId** 값은 [Azure 리소스 ID URL](../../azure-resource-manager/templates/template-functions-resource.md)입니다. 열은 현재 Azure 리소스로 제한 되지만 Azure 외부의 리소스 (예: 온-프레미스 컴퓨터)로 확장 됩니다.

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

다음 쿼리는 **_ResourceId** 를 구문 분석 하 고 Azure 구독 당 청구 된 데이터 볼륨을 집계 합니다.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last 
```

여러 데이터 형식을 검색할 경우 비용이 많이 들기 때문에 이러한 `union withsource = tt *` 쿼리는 자주 사용하지 않도록 합니다.

## <a name="_isbillable"></a>\_IsBillable
** \_ Isbillable** 가능 열은 수집 데이터에 대 한 청구 가능 여부를 지정 합니다. ** \_ Isbillable** 가능이 포함 된 데이터 `false` 는 무료로 수집 되며 Azure 계정에 청구 되지 않습니다.

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
** \_ BilledSize** 열에는 Azure 계정에 청구 되는 데이터의 크기 (바이트)를 지정 합니다 ( ** \_ isbillable** 가능 true 인 경우).


### <a name="examples"></a>예
컴퓨터별 수집 청구 가능 이벤트의 크기를 보려면 `_BilledSize` 바이트 단위로 크기를 제공 하는 열을 사용 합니다.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

구독 당 수집 청구 가능 이벤트의 크기를 확인 하려면 다음 쿼리를 사용 합니다.

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId | sort by Bytes nulls last 
```

리소스 그룹당 수집 청구 가능 이벤트의 크기를 확인 하려면 다음 쿼리를 사용 합니다.

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

특정 컴퓨터에서 청구 가능한 데이터 형식의 수를 확인 하려면 다음 쿼리를 사용 합니다.

```Kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last 
```

## <a name="next-steps"></a>다음 단계

- [Azure Monitor 로그 데이터](../log-query/log-query-overview.md)가 저장되는 방법에 대해 자세히 알아보세요.
- [로그 쿼리 작성](../log-query/get-started-queries.md) 단원을 계속 진행하세요.
- [로그 쿼리에서 테이블 조인](../log-query/joins.md) 단원을 계속 진행하세요.