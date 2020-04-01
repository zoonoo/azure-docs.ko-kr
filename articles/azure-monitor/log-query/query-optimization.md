---
title: Azure 모니터에서 로그 쿼리 최적화
description: Azure 모니터에서 로그 쿼리를 최적화하기 위한 모범 사례입니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/30/2019
ms.openlocfilehash: 29d5213b8eecd94ed8c8ce565972c9f98872a362
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411425"
---
# <a name="optimize-log-queries-in-azure-monitor"></a>Azure 모니터에서 로그 쿼리 최적화
Azure 모니터 로그는 [Azure 데이터 탐색기(ADX)를](/azure/data-explorer/) 사용하여 로그 데이터를 저장하고 해당 데이터를 분석하기 위한 쿼리를 실행합니다. ADX 클러스터를 생성, 관리 및 유지 관리하고 로그 분석 워크로드에 맞게 최적화합니다. 쿼리를 실행하면 쿼리가 최적화되고 작업 영역 데이터를 저장하는 적절한 ADX 클러스터로 라우팅됩니다. Azure 모니터 로그와 Azure 데이터 탐색기 모두 많은 자동 쿼리 최적화 메커니즘을 사용합니다. 자동 최적화는 상당한 향상을 제공하지만 쿼리 성능을 크게 향상시킬 수 있는 경우도 있습니다. 이 문서에서는 성능 고려 사항과 이를 해결하기 위한 몇 가지 기술에 대해 설명합니다.

대부분의 기술은 Azure 데이터 탐색기 및 Azure 모니터 로그에서 직접 실행되는 쿼리에 공통적으로 있지만 여기에 설명된 몇 가지 고유한 Azure Monitor Logs 고려 사항이 있습니다. Azure 데이터 탐색기 최적화 팁에 대한 자세한 내용은 [쿼리 모범 사례](/azure/kusto/query/best-practices)를 참조하십시오.

최적화된 쿼리는 다음과 같은 것입니다.

- 더 빠르게 실행하여 쿼리 실행의 전체 기간을 줄입니다.
- 제한되거나 거부될 확률이 더 작습니다.

대시보드, 경고, 논리 앱 및 Power BI와 같이 반복적이고 버스트한 사용에 사용되는 쿼리에 특히 주의해야 합니다. 이러한 경우 비효율적인 쿼리의 영향은 상당합니다.

## <a name="query-performance-pane"></a>쿼리 성능 창
Log Analytics에서 쿼리를 실행한 후 쿼리 결과 위의 아래쪽 화살표를 클릭하여 쿼리에 대한 여러 성능 지표의 결과를 보여 주는 쿼리 성능 창을 봅니다. 이러한 성과 지표는 각각 다음 섹션에 설명되어 있습니다.

![쿼리 성능 창](media/query-optimization/query-performance-pane.png)


## <a name="query-performance-indicators"></a>쿼리 성능 지표

실행되는 모든 쿼리에 대해 다음 쿼리 성능 표시기를 사용할 수 있습니다.

- [총 CPU](#total-cpu): 모든 계산 노드에서 쿼리를 처리하는 데 사용되는 전체 계산. 컴퓨팅, 구문 분석 및 데이터 가져오기에 사용되는 시간을 나타냅니다. 

- [처리된 쿼리에 사용되는 데이터](#data-used-for-processed-query): 쿼리를 처리하기 위해 액세스한 전체 데이터입니다. 대상 테이블의 크기, 사용된 시간 범위, 적용된 필터 및 참조된 열 수에 따라 영향을 받습니다.

- [처리된 쿼리의 시간 범위:](#time-span-of-the-processed-query)쿼리를 처리하기 위해 액세스한 최신 데이터와 가장 오래된 데이터 간의 간격입니다. 쿼리에 대해 지정된 명시적 시간 범위의 영향을 받습니다.

- [처리된 데이터의 연령](#age-of-processed-data): 쿼리를 처리하기 위해 액세스한 현재와 가장 오래된 데이터 간의 간격입니다. 데이터 가져오기의 효율성에 큰 영향을 미칩니다.

- [작업 영역 수](#number-of-workspaces): 암시적 또는 명시적 선택으로 인해 쿼리 처리 중에 액세스한 작업 영역 수입니다.

- [지역 수](#number-of-regions): 작업 영역의 암시적 또는 명시적 선택으로 인해 쿼리 처리 중에 액세스한 지역 수입니다. 다중 영역 쿼리는 효율성이 훨씬 낮으며 성능 지표는 부분 적용 범위를 제공합니다.

- [병렬 처리](#parallelism): 시스템이 여러 노드에서 이 쿼리를 실행할 수 있었던 양을 나타냅니다. CPU 사용량이 많은 쿼리에만 해당됩니다. 특정 기능 및 연산자의 사용에 의해 영향을 받습니다.


## <a name="total-cpu"></a>총 CPU
모든 쿼리 처리 노드에서 이 쿼리를 처리하기 위해 투자된 실제 계산 CPU입니다. 대부분의 쿼리는 많은 수의 노드에서 실행되므로 일반적으로 쿼리가 실제로 실행하는 데 걸린 기간보다 훨씬 큽습니다. 

쿼리 처리 시간이 다음과 에 소요됩니다.
- 데이터 검색 - 이전 데이터를 검색하면 최근 데이터를 검색하는 것보다 더 많은 시간이 소비됩니다.
- 데이터 처리 – 데이터의 논리 및 평가. 

쿼리 처리 노드에 소요된 시간 이외에 Azure Monitor Logs에서 보내는 추가 시간이 있습니다: 사용자를 인증하고 이 데이터에 액세스하고, 데이터 저장소를 찾고, 쿼리를 구문 분석하고, 쿼리 처리 노드를 할당할 수 있는지 확인합니다. 이 시간은 쿼리 총 CPU 시간에 포함되지 않습니다.

### <a name="early-filtering-of-records-prior-of-using-high-cpu-functions"></a>높은 CPU 기능을 사용하기 전에 레코드조기 필터링

일부 쿼리 명령 및 함수는 CPU 사용량이 무겁습니다. JSON 및 XML을 구문 분석하거나 복잡한 정규식을 추출하는 명령의 경우 특히 그렇습니다. 이러한 구문 분석은 [parse_json()](/azure/kusto/query/parsejsonfunction) 또는 [parse_xml()](/azure/kusto/query/parse-xmlfunction) 함수를 통해 명시적으로 또는 동적 열을 참조할 때 암시적으로 발생할 수 있습니다.

이러한 함수는 처리 중인 행 수에 비례하여 CPU를 사용합니다. 가장 효율적인 최적화는 CPU 집약적 함수가 실행되기 전에 가능한 한 많은 레코드를 필터링할 수 있는 쿼리 초기에 조건을 추가하는 것입니다.

예를 들어 다음 쿼리는 정확히 동일한 결과를 생성하지만 두 번째 쿼리는 구문 분석 전에 [where](/azure/kusto/query/whereoperator) 조건으로 가장 효율적입니다.

```Kusto
//less efficient
SecurityEvent
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| summarize count() by FileHash, FilePath
| where FileHash != "" and FilePath !startswith "%SYSTEM32"  // Problem: irrelevant results are filtered after all processing and parsing is done
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| summarize count() by FileHash, FilePath
| where FileHash != "" // No need to filter out %SYSTEM32 here as it was removed before
```

### <a name="avoid-using-evaluated-where-clauses"></a>평가된 절을 사용하지 마십시오.

데이터 집합에 물리적으로 존재하는 열이 아닌 평가된 열의 [절이](/azure/kusto/query/whereoperator) 포함된 쿼리는 효율성을 잃게 됩니다. 평가된 열을 필터링하면 대규모 데이터 집합을 처리할 때 일부 시스템 최적화가 방지되지 않습니다.
예를 들어 다음 쿼리는 정확히 동일한 결과를 생성하지만 두 번째 쿼리는 [조건이](/azure/kusto/query/whereoperator) 기본 제공 열을 참조하므로 더 효율적입니다.

```Kusto
//less efficient
Heartbeat 
| extend IPRegion = iif(RemoteIPLongitude  < -94,"WestCoast","EastCoast")
| where IPRegion == "WestCoast"
| summarize count() by Computer
```
```Kusto
//more efficient
Heartbeat 
| where RemoteIPLongitude  < -94
| extend IPRegion = iif(RemoteIPLongitude  < -94,"WestCoast","EastCoast")
| summarize count() by Computer
```

### <a name="use-effective-aggregation-commands-and-dimmentions-in-summarize-and-join"></a>효과적인 집계 명령 및 요약 및 조인에서 멘션 사용

[max()](/azure/kusto/query/max-aggfunction)및 [sum()](/azure/kusto/query/sum-aggfunction)및 [count()](/azure/kusto/query/count-aggfunction)및 [avg()와](/azure/kusto/query/avg-aggfunction) 같은 일부 집계 명령은 논리로 인해 CPU 영향이 적지만 다른 명령은 더 복잡하며 효율적으로 실행할 수 있는 추론 및 추정을 포함합니다. 예를 들어 [dcount())는](/azure/kusto/query/dcount-aggfunction) HyperLogLog 알고리즘을 사용하여 실제로 각 값을 계산하지 않고 큰 데이터 집합의 고유한 개수에 대한 가까운 추정을 제공합니다. 백분위수 함수는 가장 가까운 순위 백분위수 알고리즘을 사용하여 유사한 근사치를 수행하고 있습니다. 몇 가지 명령에는 영향을 줄이기 위한 선택적 매개 변수가 포함되어 있습니다. 예를 들어 [makeset()](/azure/kusto/query/makeset-aggfunction) 함수에는 CPU와 메모리에 크게 영향을 미치는 최대 집합 크기를 정의하는 선택적 매개 변수가 있습니다.

[조인](/azure/kusto/query/joinoperator?pivots=azuremonitor) 및 [요약](/azure/kusto/query/summarizeoperator) 명령은 많은 데이터 집합을 처리할 때 CPU 사용률이 높을 수 있습니다. 복잡성은 요약 또는 조인 특성으로 `by` 사용 중인 열의 *카디널리티라고*하는 가능한 값의 수와 직접적으로 관련이 있습니다. 조인 및 요약에 대한 설명 및 최적화는 해당 문서 문서 및 최적화 팁을 참조하십시오.

예를 들어 다음 쿼리는 **CounterPath가** 항상 **CounterName** 및 **ObjectName에**일대일로 매핑되므로 정확히 동일한 결과를 생성합니다. 두 번째는 집계 차원이 작기 때문에 더 효율적입니다.

```Kusto
//less efficient
Perf
| summarize avg(CounterValue) 
by CounterName, CounterPath, ObjectName
```
```Kusto
//make the group expression more compact improve the performance
Perf
| summarize avg(CounterValue), any(CounterName), any(ObjectName) 
by CounterPath
```

CPU 사용량은 집중적인 컴퓨팅이 필요한 조건이나 확장된 열의 영향을 받을 수도 있습니다. 같은 모든 사소한 문자열 비교 같은 [동일 ==](/azure/kusto/query/datatypes-string-operators) 및 [시작으로](/azure/kusto/query/datatypes-string-operators) 고급 텍스트 일치는 더 많은 영향을 미칠 동안 거의 동일한 CPU 영향을 가합니다. 특히 [연산자가](/azure/kusto/query/datatypes-string-operators) 연산자가 [포함되어](/azure/kusto/query/datatypes-string-operators) 있는 것이 더 효율적입니다. 문자열 처리 기술로 인해 짧은 문자열보다 4자 보다 긴 문자열을 찾는 것이 더 효율적입니다.

예를 들어 다음 쿼리는 컴퓨터 이름 지정 정책에 따라 비슷한 결과를 생성하지만 두 번째 쿼리는 더 효율적입니다.

```Kusto
//less efficient – due to filter based on contains
Heartbeat
| where Computer contains "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//less efficient – due to filter based on extend
Heartbeat
| extend MyComputer = Computer
| where MyComputer startswith "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//more efficient
Heartbeat
| where Computer startswith "Production" 
| summarize count() by ComputerIP 
```

> [!NOTE]
> 이 표시기는 즉시 클러스터의 CPU만 표시합니다. 다중 지역 쿼리에서는 지역 중 하나만 나타냅니다. 다중 작업 영역 쿼리에서는 모든 작업 영역이 포함되지 않을 수 있습니다.

### <a name="avoid-full-xml-and-json-parsing-when-string-parsing-works"></a>문자열 구문 분석이 작동할 때 전체 XML 및 JSON 구문 분석 방지
XML 또는 JSON 개체의 전체 구문 분석은 높은 CPU 및 메모리 리소스를 소비할 수 있습니다. 대부분의 경우 하나 또는 두 개의 매개 변수만 필요하고 XML 또는 JSON 개체가 간단하면 [구문 분석 연산자](/azure/kusto/query/parseoperator) 또는 기타 텍스트 구문 분석 기술을 사용하여 문자열로 구문 분석하는 것이 더 [쉽습니다.](/azure/azure-monitor/log-query/parse-text) XML 또는 JSON 개체의 레코드 수가 증가함에 따라 성능 향상이 더 중요해질 것입니다. 레코드 수가 수천만 에 도달할 때 필수적입니다.

예를 들어 다음 쿼리는 전체 XML 구문 분석 작업을 수행하지 않고 위의 쿼리와 정확히 동일한 결과를 반환합니다. FilePath 요소와 같은 XML 파일 구조에 대한 몇 가지 가정을 FileHash 다음으로 해석하고 그 중 어느 것도 특성을 가지고 있지 않습니다. 

```Kusto
//even more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| parse EventData with * "<FilePath>" FilePath "</FilePath>" * "<FileHash>" FileHash "</FileHash>" *
| summarize count() by FileHash, FilePath
| where FileHash != "" // No need to filter out %SYSTEM32 here as it was removed before
```


## <a name="data-used-for-processed-query"></a>처리된 쿼리에 사용되는 데이터

쿼리 처리의 중요한 요소는 쿼리 처리에 검색되고 사용되는 데이터의 볼륨입니다. Azure Data Explorer는 다른 데이터 플랫폼에 비해 데이터 볼륨을 크게 줄이는 공격적인 최적화를 사용합니다. 그러나 쿼리에는 사용되는 데이터 볼륨에 영향을 미칠 수 있는 중요한 요소가 있습니다.

Azure 모니터 로그에서 **TimeGenerated** 열은 데이터를 인덱싱하는 방법으로 사용됩니다. **TimeGenerated** 값을 가능한 한 좁은 범위로 제한하면 처리해야 하는 데이터의 양을 크게 제한하여 쿼리 성능이 크게 향상됩니다.

### <a name="avoid-unnecessary-use-of-search-and-union-operators"></a>검색 및 노조 운영자의 불필요한 사용을 방지

프로세스인 데이터를 증가시키는 또 다른 요소는 많은 수의 테이블을 사용하는 것입니다. 이 작업은 `search *` 일반적으로 `union *` 명령이 사용될 때 발생합니다. 이러한 명령은 시스템이 작업 영역의 모든 테이블에서 데이터를 평가하고 검색하도록 강제합니다. 경우에 따라 작업 영역에 수백 개의 테이블이 있을 수 있습니다. "검색 *" 또는 특정 테이블에 범위를 지정하지 않고 검색을 사용하여 가능한 한 많이 피하십시오.

예를 들어 다음 쿼리는 정확히 동일한 결과를 생성하지만 마지막 쿼리는 지금까지 가장 효율적입니다.

```Kusto
// This version scans all tables though only Perf has this kind of data
search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This version scans all strings in Perf tables – much more efficient
Perf
| search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This is the most efficient version 
Perf 
| where CounterName == "% Processor Time"  
| summarize count(), avg(CounterValue)  by Computer
```

### <a name="add-early-filters-to-the-query"></a>쿼리에 초기 필터 추가

데이터 볼륨을 줄이는 또 다른 방법은 쿼리 초기에 [조건을](/azure/kusto/query/whereoperator) 사용하는 것입니다. Azure Data Explorer 플랫폼에는 특정 조건과 관련된 데이터가 포함된 파티션을 알 수 있는 캐시가 포함되어 있습니다. 예를 들어 쿼리에 `where EventID == 4624` 포함된 경우 일치하는 이벤트가 있는 파티션을 처리하는 노드에만 쿼리를 배포합니다.

다음 예제 쿼리는 정확히 동일한 결과를 생성하지만 두 번째 쿼리는 더 효율적입니다.

```Kusto
//less efficient
SecurityEvent
| summarize LoginSessions = dcount(LogonGuid) by Account
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 4624 //Logon GUID is relevant only for logon event
| summarize LoginSessions = dcount(LogonGuid) by Account
```

### <a name="reduce-the-number-of-columns-that-is-retrieved"></a>검색되는 열 수 감소

Azure Data Explorer는 열 데이터 저장소이므로 모든 열의 검색은 다른 열과 독립적입니다. 검색되는 열 수는 전체 데이터 볼륨에 직접적인 영향을 미칩니다. 결과를 [요약하거나](/azure/kusto/query/summarizeoperator) 특정 열을 [투영하는](/azure/kusto/query/projectoperator) 데 필요한 열만 출력에 포함해야 합니다. Azure Data Explorer에는 검색된 열 수를 줄이기 위한 몇 가지 최적화가 있습니다. 예를 들어 [요약](/azure/kusto/query/summarizeoperator) 명령에서 열이 참조되지 않은 경우 열이 필요하지 않다고 판단되면 열이 검색되지 않습니다.

예를 들어 두 번째 쿼리는 하나의 열이 아니라 세 개의 열을 가져와야 하므로 세 배 더 많은 데이터를 처리할 수 있습니다.

```Kusto
//Less columns --> Less data
SecurityEvent
| summarize count() by Computer  
```
```Kusto
//More columns --> More data
SecurityEvent
| summarize count(), dcount(EventID), avg(Level) by Computer  
```

## <a name="time-span-of-the-processed-query"></a>처리된 쿼리의 시간 범위

Azure 모니터 로그의 모든 로그는 **TimeGenerated** 열에 따라 분할됩니다. 액세스하는 파티션 수는 시간 범위와 직접 관련이 있습니다. 시간 범위를 줄이는 것이 신속한 쿼리 실행을 보장하는 가장 효율적인 방법입니다.

시간 범위는 [Azure 모니터 로그 분석의 로그 쿼리 범위 및 시간 범위에](scope.md#time-range)설명된 대로 로그 분석 화면의 시간 범위 선택기에서 설정할 수 있습니다. 선택한 시간 범위가 쿼리 메타데이터를 사용하여 백 엔드에 전달될 때 이 방법을 사용하는 것이 좋습니다. 

다른 방법은 쿼리에서 **TimeGenerated의** [where](/azure/kusto/query/whereoperator) 조건을 명시적으로 포함하는 것입니다. 쿼리가 다른 인터페이스에서 사용되는 경우에도 시간 범위가 고정되어 있음을 보장하기 때문에 이 메서드를 사용해야 합니다.
쿼리의 모든 부분에 **TimeGenerated** 필터가 있는지 확인해야 합니다. 쿼리에 다양한 테이블 또는 동일한 테이블에서 데이터를 가져오는 하위 쿼리가 있는 경우 각 쿼리에는 고유한 [위치](/azure/kusto/query/whereoperator) 조건을 포함해야 합니다.

### <a name="make-sure-all-sub-queries-have-timegenerated-filter"></a>모든 하위 쿼리에 시간 생성 필터가 있는지 확인합니다.

예를 들어 다음 쿼리에서는 **Perf** 테이블이 마지막 날에대해서만 검색되지만 **하트비트** 테이블은 최대 2년까지의 모든 기록을 검색합니다.

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize IPs = makeset(ComputerIP, 10) by  Computer
) on Computer
```

이러한 실수가 발생하는 일반적인 경우는 [arg_max()가](/azure/kusto/query/arg-max-aggfunction) 가장 최근의 발생을 찾는 데 사용되는 경우입니다. 다음은 그 예입니다.

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

내부 쿼리에 시간 필터를 추가하여 쉽게 수정할 수 있습니다.

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    | where TimeGenerated > ago(1d) //filter for this part
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

이 오류의 또 다른 예는 여러 테이블에 대한 [공용 구조조정](/azure/kusto/query/unionoperator?pivots=azuremonitor) 직후의 시간 범위 필터링을 수행할 때입니다. 공용 구조체를 수행할 때 각 하위 쿼리의 범위를 조정해야 합니다. [let](/azure/kusto/query/letstatement) 문을 사용하여 범위 지정 일관성을 보장할 수 있습니다.

예를 들어 다음 쿼리는 지난 1일뿐만 아니라 *하트비트* 및 *Perf* 테이블의 모든 데이터를 검사합니다.

```Kusto
Heartbeat 
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | summarize arg_min(TimeGenerated,*) by Computer) 
| where TimeGenerated > ago(1d)
| summarize min(TimeGenerated) by Computer
```

이 쿼리는 다음과 같이 수정해야 합니다.

```Kusto
let MinTime = ago(1d);
Heartbeat 
| where TimeGenerated > MinTime
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | where TimeGenerated > MinTime
    | summarize arg_min(TimeGenerated,*) by Computer) 
| summarize min(TimeGenerated) by Computer
```

### <a name="time-span-measurement-limitations"></a>시간 범위 측정 제한

측정은 항상 지정된 실제 시간보다 큽합니다. 예를 들어 쿼리의 필터가 7일인 경우 시스템에서 7.5 일 또는 8.1일을 검색할 수 있습니다. 시스템이 데이터를 가변 크기의 청크로 분할하기 때문입니다. 모든 관련 레코드를 검색하기 위해 몇 시간, 심지어 하루 이상을 커버할 수 있는 전체 파티션을 검사합니다.

시스템이 시간 범위를 정확하게 측정할 수 없는 경우가 몇 가지 있습니다. 이 문제는 쿼리의 범위가 하루 미만이거나 다중 작업 영역 쿼리에서 발생하는 대부분의 경우 발생합니다.


> [!IMPORTANT]
> 이 표시기는 즉시 클러스터에서 처리된 데이터만 표시합니다. 다중 지역 쿼리에서는 지역 중 하나만 나타냅니다. 다중 작업 영역 쿼리에서는 모든 작업 영역이 포함되지 않을 수 있습니다.

## <a name="age-of-processed-data"></a>처리된 데이터의 시간
Azure 데이터 탐색기는 메모리 내 저장소, 로컬 SSD 디스크 및 훨씬 느린 Azure Blob과 같은 여러 저장소 계층을 사용합니다. 데이터가 새로 워지면 대기 시간이 더 적은 더 성능이 뛰어난 계층에 저장되어 쿼리 기간과 CPU가 줄어듭니다. 데이터 자체 이외에 시스템에는 메타데이터에 대한 캐시도 있습니다. 데이터가 오래 될수록 메타데이터가 캐시에 있을 확률이 줄어듭니다.

일부 쿼리에서는 이전 데이터를 사용해야 하지만 이전 데이터를 실수로 사용하는 경우가 있습니다. 이 문제는 메타 데이터에 시간 범위를 제공하지 않고 쿼리가 실행되고 모든 테이블 참조에 **TimeGenerated** 열의 필터가 포함되지 않는 경우에 발생합니다. 이러한 경우 시스템은 해당 테이블에 저장된 모든 데이터를 검색합니다. 데이터 보존기간이 길면 긴 기간과 데이터 보존 기간만큼 오래된 데이터를 다룰 수 있습니다.

예를 들어 다음과 같은 경우일 수 있습니다.

- 제한되지 않는 하위 쿼리를 통해 Log Analytics에서 시간 범위를 설정하지 않습니다. 위 예제를 참조하세요.
- 시간 범위 선택적 매개 변수 없이 API를 사용합니다.
- Power BI 커넥터와 같은 시간 범위를 강제하지 않는 클라이언트를 사용합니다.

이 경우 관련이 있는 경우와 관련이 있는 자세한 섹션의 예제 및 메모를 참조하십시오.

## <a name="number-of-regions"></a>지역 수
여러 지역에서 단일 쿼리를 실행할 수 있는 몇 가지 상황이 있습니다.

- 여러 작업 영역이 명시적으로 나열되고 다른 지역에 있는 경우
- 리소스 범위 쿼리가 데이터를 가져오고 데이터가 다른 지역에 있는 여러 작업 영역에 저장되는 경우

지역 간 쿼리 실행을 위해서는 시스템이 쿼리 최종 결과보다 훨씬 큰 중간 데이터의 백 엔드 큰 청크를 직렬화하고 전송해야 합니다. 또한 최적화, 추론 및 캐시를 활용하는 시스템의 기능을 제한합니다.
이러한 모든 영역을 검사할 실제 이유가 없는 경우 더 적은 영역을 포괄하도록 범위를 조정해야 합니다. 리소스 범위가 최소화되었지만 여전히 많은 지역이 사용되는 경우 잘못된 구성으로 인해 발생할 수 있습니다. 예를 들어 감사 로그 및 진단 설정은 다른 지역의 다른 작업 영역으로 전송되거나 여러 진단 설정 구성이 있습니다. 

> [!IMPORTANT]
> 쿼리가 여러 지역에서 실행되면 CPU 및 데이터 측정이 정확하지 않으며 지역 중 하나에서만 측정값을 나타냅니다.

## <a name="number-of-workspaces"></a>작업 영역 수
작업 영역은 로그 데이터를 분리하고 관리하는 데 사용되는 논리적 컨테이너입니다. 백 엔드는 선택한 영역 내의 실제 클러스터에서 작업 영역 배치를 최적화합니다.

여러 작업 영역의 사용은 다음과 같은 결과로 발생할 수 있습니다. 

- 여러 작업 영역이 명시적으로 나열되는 위치입니다.
- 리소스 범위 쿼리가 데이터를 가져오고 데이터가 여러 작업 영역에 저장되는 경우
 
쿼리의 지역 간 및 클러스터 간 실행을 위해서는 시스템이 쿼리 최종 결과보다 훨씬 큰 중간 데이터의 백 엔드 큰 청크를 직렬화하고 전송해야 합니다. 또한 최적화, 추론 및 캐시 활용을 수행하는 시스템 기능을 제한합니다.

> [!IMPORTANT]
> 일부 다중 작업 영역 시나리오에서는 CPU 및 데이터 측정이 정확하지 않으며 몇 개의 작업 영역만 측정을 나타냅니다.

## <a name="parallelism"></a>병렬 처리
Azure Monitor Logs는 쿼리를 실행하기 위해 Azure Data Explorer의 큰 클러스터를 사용하고 있으며 이러한 클러스터는 규모에 따라 달라지므로 최대 수십 개의 계산 노드를 얻을 수 있습니다. 시스템은 작업 영역 배치 논리 및 용량에 따라 클러스터의 크기를 자동으로 조정합니다.

쿼리를 효율적으로 실행하기 위해 처리에 필요한 데이터를 기반으로 노드를 계산하기 위해 분할및 분산됩니다. 시스템이 이 작업을 효율적으로 수행할 수 없는 경우가 있습니다. 이로 인해 쿼리 기간이 길어지도 있습니다. 

병렬 처리를 줄일 수 있는 쿼리 동작은 다음과 같습니다.

- [직렬화 연산자,](/azure/kusto/query/serializeoperator) [next()](/azure/kusto/query/nextfunction), [prev()](/azure/kusto/query/prevfunction)및 [행](/azure/kusto/query/rowcumsumfunction) 함수와 같은 직렬화 및 창 함수의 사용입니다. 시계열 및 사용자 분석 기능은 이러한 경우 일부에서 사용할 수 있습니다. 비효율적인 직렬화는 다음 연산자가 쿼리의 끝에 사용되지 않는 경우에도 발생할 수 있습니다: [범위,](/azure/kusto/query/rangeoperator) [정렬,](/azure/kusto/query/sortoperator) [순서,](/azure/kusto/query/orderoperator) [상단,](/azure/kusto/query/topoperator) [상단 타자,](/azure/kusto/query/tophittersoperator) [getschema](/azure/kusto/query/getschemaoperator).
-    [dcount()](/azure/kusto/query/dcount-aggfunction) 집계 함수를 사용하면 시스템이 고유한 값의 중앙 복사본을 갖도록 합니다. 데이터 배율이 높은 경우 dcount 함수 선택적 매개 변수를 사용하여 정확도를 줄이는 것이 좋습니다.
-    대부분의 경우 [조인](/azure/kusto/query/joinoperator?pivots=azuremonitor) 연산자는 전체 병렬 처리를 낮춥습니다. 성능에 문제가 있는 경우 셔플 조인을 대안으로 검사합니다.
-    리소스 범위 쿼리에서 실행 전 RBAC 검사는 매우 많은 수의 RBAC 할당이 있는 경우에 남아 있을 수 있습니다. 이로 인해 더 긴 검사가 수행되어 병렬 처리가 낮아질 수 있습니다. 예를 들어 쿼리는 수천 개의 리소스가 있고 각 리소스에는 구독 또는 리소스 그룹이 아닌 리소스 수준에서 많은 역할 할당이 있는 구독에서 실행됩니다.
-    쿼리가 작은 데이터 청크를 처리하는 경우 시스템이 여러 계산 노드에 분산되지 않기 때문에 병렬처리가 낮아집니다.



## <a name="next-steps"></a>다음 단계

- [Kusto 쿼리 언어에 대한 참조 문서.](/azure/kusto/query/)
