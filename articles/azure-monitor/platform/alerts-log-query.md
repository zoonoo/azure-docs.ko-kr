---
title: 로그 경고 쿼리 최적화 | Microsoft Docs
description: 효율적인 경고 쿼리 작성에 대 한 권장 사항
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 02/19/2019
ms.subservice: alerts
ms.openlocfilehash: 7f03858b2427b2a2069ebe2c9d06425e7a741e2b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91294362"
---
# <a name="optimizing-log-alert-queries"></a>로그 경고 쿼리 최적화
이 문서에서는 최적의 성능을 얻기 위해 [로그 경고](alerts-unified-log.md) 쿼리를 작성 하 고 변환 하는 방법을 설명 합니다. 최적화 된 쿼리는 자주 실행 되는 경고의 대기 시간과 로드를 줄입니다.

## <a name="how-to-start-writing-an-alert-log-query"></a>경고 로그 쿼리 작성을 시작 하는 방법

경고 쿼리는 문제를 나타내는 [Log Analytics의 로그 데이터를 쿼리 하](alerts-log.md#create-a-log-alert-rule-with-the-azure-portal) 는 것부터 시작 합니다. [경고 쿼리 예제 항목](../log-query/saved-queries.md) 을 사용 하 여 검색할 수 있는 항목을 이해할 수 있습니다. 사용자가 [직접 쿼리를 작성](../log-query/get-started-portal.md)하는 작업을 시작할 수도 있습니다. 

### <a name="queries-that-indicate-the-issue-and-not-the-alert"></a>경고가 아닌 문제를 나타내는 쿼리

경고 흐름은 경고에 대 한 문제를 나타내는 결과를 변환 하기 위해 작성 되었습니다. 예를 들어 다음과 같은 쿼리의 경우

``` Kusto
SecurityEvent
| where EventID == 4624
```

사용자가 경고를 사용 하는 경우이 이벤트 유형이 발생 하면 경고 논리가 쿼리에 추가 됩니다 `count` . 실행 되는 쿼리는 다음과 같습니다.

``` Kusto
SecurityEvent
| where EventID == 4624
| count
```

쿼리에 경고 논리를 추가할 필요가 없으며이로 인해 문제가 발생할 수도 있습니다. 위의 예에서는 쿼리에를 포함 하는 경우 `count` 경고 서비스가를 수행 하므로 항상 값 1이 반환 됩니다 `count` `count` .

### <a name="avoid-limit-and-take-operators"></a>`limit`및 `take` 연산자 사용 안 합니다.

쿼리에서 및를 사용 하면 `limit` `take` 시간이 지남에 따라 결과가 일관 되지 않을 때 대기 시간 및 경고 로드를 늘릴 수 있습니다. 필요한 경우에만 사용 하는 것이 좋습니다.

## <a name="log-query-constraints"></a>로그 쿼리 제약 조건
[Azure Monitor의 로그 쿼리](../log-query/log-query-overview.md) 는 테이블, [`search`](/azure/kusto/query/searchoperator) 또는 연산자 중 하나로 시작 [`union`](/azure/kusto/query/unionoperator) 합니다.

로그 경고 규칙에 대 한 쿼리는 항상 테이블을 사용 하 여 명확한 범위를 정의 해야 합니다 .이를 통해 쿼리 성능과 결과의 관련성을 모두 향상 시킬 수 있습니다. 경고 규칙의 쿼리는 자주 실행 되므로 및를 사용 `search` `union` 하면 여러 테이블에서 검색 해야 하므로 경고에 대기 시간이 과도 하 게 추가 될 수 있습니다. 이러한 연산자는 또한 쿼리를 최적화 하는 경고 서비스의 기능을 줄여 줍니다.

또는 연산자를 사용 하는 로그 경고 규칙을 만들거나 수정 하는 것은 지원 되지 않으므로 `search` `union` 리소스 간 쿼리를 사용할 수 있습니다.

예를 들어 다음 경고 쿼리는 _securityevent_ 테이블로 범위가 지정 되며 특정 이벤트 ID를 검색 합니다. 쿼리가 처리 해야 하는 유일한 테이블입니다.

``` Kusto
SecurityEvent
| where EventID == 4624
```

상호 리소스 쿼리를 [사용 하](../log-query/cross-workspace-query.md) 는 로그 경고 규칙은 `union` 쿼리 범위를 특정 리소스로 제한 하는 형식을 사용 하기 때문에 이러한 변경의 영향을 받지 않습니다. 다음 예는 유효한 로그 경고 쿼리입니다.

```Kusto
union
app('Contoso-app1').requests,
app('Contoso-app2').requests,
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
> [리소스 간 쿼리](../log-query/cross-workspace-query.md) 는 새 [scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules)에서 지원 됩니다. 로그 경고를 만드는 데 [레거시 Log Analytics 경고 API](api-alerts.md) 를 계속 사용 하는 경우 [여기](alerts-log-api-switch.md)에서 전환 하는 방법을 배울 수 있습니다.

## <a name="examples"></a>예제
다음 예에서는 및를 사용 하는 로그 쿼리를 비롯 하 여 `search` `union` 경고 규칙에 사용 하기 위해 이러한 쿼리를 수정 하는 데 사용할 수 있는 단계를 제공 합니다.

### <a name="example-1"></a>예 1
을 사용 하 여 성능 정보를 검색 하는 다음 쿼리를 사용 하 여 로그 경고 규칙을 만들 수 있습니다 `search` . 

``` Kusto
search *
| where Type == 'Perf' and CounterName == '% Free Space'
| where CounterValue < 30
```

이 쿼리를 수정하려면 먼저 다음 쿼리를 사용해 속성이 속한 테이블을 식별합니다.

``` Kusto
search *
| where CounterName == '% Free Space'
| summarize by $table
```

이 쿼리의 결과에는 _CounterName_ 속성이 _Perf_ 테이블에 포함되어 있었음이 표시됩니다.

이 결과를 사용 하 여 경고 규칙에 사용할 다음 쿼리를 만들 수 있습니다.

``` Kusto
Perf
| where CounterName == '% Free Space'
| where CounterValue < 30
```

### <a name="example-2"></a>예제 2
을 사용 하 여 성능 정보를 검색 하는 다음 쿼리를 사용 하 여 로그 경고 규칙을 만들 수 있습니다 `search` . 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer
| where Avg_Memory_Usage between(90 .. 95)  
```

이 쿼리를 수정하려면 먼저 다음 쿼리를 사용해 속성이 속한 테이블을 식별합니다.

``` Kusto
search ObjectName=="Memory" and CounterName=="% Committed Bytes In Use"
| summarize by $table
```

이 쿼리의 결과에는 _ObjectName_ 및 _CounterName_ 속성이 _Perf_ 테이블에 포함되어 있었음이 표시됩니다.

이 결과를 사용 하 여 경고 규칙에 사용할 다음 쿼리를 만들 수 있습니다.

``` Kusto
Perf
| where ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"
| summarize Avg_Memory_Usage=avg(CounterValue) by Computer
| where Avg_Memory_Usage between(90 .. 95)
```

### <a name="example-3"></a>예제 3

및를 모두 사용 하 여 성능 정보를 검색 하는 다음 쿼리를 사용 하 여 로그 경고 규칙을 만들 수 있습니다 `search` `union` . 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")
| where Computer !in (
    union *
    | where CounterName == "% Processor Utility"
    | summarize by Computer)
| summarize Avg_Idle_Time = avg(CounterValue) by Computer
```

이 쿼리를 수정하려면 먼저 다음 쿼리를 사용해 쿼리의 첫 부분에 있는 속성이 속한 테이블을 식별합니다. 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")
| summarize by $table
```

이 쿼리의 결과에는 이러한 모든 속성이 _Perf_ 테이블에 포함되어 있었음이 표시됩니다.

이제 `withsource` 명령에서 `union`을 사용하여 각 행을 제공한 원본 테이블을 식별합니다.

``` Kusto
union withsource=table *
| where CounterName == "% Processor Utility"
| summarize by table
```

이 쿼리의 결과에는 이러한 속성 역시 _Perf_ 테이블에 포함되어 있었음이 표시됩니다.

이러한 결과를 사용 하 여 경고 규칙에 사용할 다음 쿼리를 만들 수 있습니다. 

``` Kusto
Perf
| where ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total"
| where Computer !in (
    (Perf
    | where CounterName == "% Processor Utility"
    | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer
``` 

### <a name="example-4"></a>예제 4
다음 쿼리를 사용 하 여 두 쿼리의 결과를 조인 하는 로그 경고 규칙을 만들려고 합니다 `search` .

```Kusto
search Type == 'SecurityEvent' and EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    search in (Heartbeat) OSType == 'Windows'
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h)
    | project Hour , Computer
) on Hour
```

쿼리를 수정하려면 먼저 다음 쿼리를 사용하여 조인 왼쪽의 속성이 포함된 테이블을 식별합니다. 

``` Kusto
search Type == 'SecurityEvent' and EventID == '4625'
| summarize by $table
```

결과에는 조인 왼쪽의 속성이 _SecurityEvent_ 테이블에 속한다는 내용이 표시됩니다. 

이제 다음 쿼리를 사용하여 조인 오른쪽의 속성이 포함된 테이블을 식별합니다. 
 
``` Kusto
search in (Heartbeat) OSType == 'Windows'
| summarize by $table
```
 
결과는 조인의 오른쪽에 있는 속성이 _하트 비트_ 테이블에 속해 있음을 나타냅니다.

이러한 결과를 사용 하 여 경고 규칙에 사용할 다음 쿼리를 만들 수 있습니다. 

``` Kusto
SecurityEvent
| where EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    Heartbeat
    | where OSType == 'Windows'
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h)
    | project Hour , Computer
) on Hour
```

## <a name="next-steps"></a>다음 단계
- Azure Monitor의 [로그 경고](alerts-log.md)에 대해 알아봅니다.
- [로그 쿼리](../log-query/log-query-overview.md)에 대해 알아봅니다.
