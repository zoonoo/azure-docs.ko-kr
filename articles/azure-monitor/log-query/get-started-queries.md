---
title: Azure Monitor에서 로그 쿼리 시작 | Microsoft Docs
description: 이 문서에서는 Azure Monitor에서 로그 쿼리 작성을 시작하기 위한 자습서를 제공합니다.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: bwren
ms.openlocfilehash: b03109ee5cdb76247bf3be6fda97e0cf6e434f17
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296081"
---
# <a name="get-started-with-log-queries-in-azure-monitor"></a>Azure Monitor에서 로그 쿼리를 사용 하 여 시작


> [!NOTE]
> 완료 해야 [Azure Monitor Log Analytics를 사용 하 여 시작](get-started-portal.md) 이 자습서를 완료 하기 전에 합니다.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

이 자습서에서는 Azure Monitor에서 로그 쿼리를 작성 하는 것이 배웁니다. 다음을 수행하는 방법에 대해 알아봅니다.

- 쿼리 구조 이해
- 쿼리 결과 정렬
- 쿼리 결과 필터링
- 시간 범위 지정
- 결과에 포함할 필드 선택
- 사용자 지정 필드 정의 및 사용
- 결과 집계 및 그룹화

Azure portal에서 Log Analytics를 사용 하 여에 대 한 자습서를 참조 하세요 [Azure Monitor Log Analytics를 사용 하 여 시작](get-started-portal.md)합니다.<br>
Azure Monitor에서 로그 쿼리 대 한 자세한 내용은 참조 하세요 [Azure Monitor에서 쿼리 로그 개요](log-query-overview.md)합니다.

## <a name="writing-a-new-query"></a>새 쿼리 작성
쿼리는 테이블 이름 또는 *search* 명령을 사용하여 시작할 수 있습니다. 쿼리에 대한 명확한 범위를 정의하고 쿼리 성능 및 결과의 관련성을 개선하므로 테이블 이름을 사용하여 시작해야 합니다.

> [!NOTE]
> Azure Monitor에서 사용되는 Kusto 쿼리 언어는 대/소문자를 구분합니다. 언어 키워드는 일반적으로 소문자로 작성됩니다. 쿼리에서 테이블 또는 열 이름을 사용하는 경우 스키마 창에 표시된 대로 정확한 대/소문자를 사용해야 합니다.

### <a name="table-based-queries"></a>테이블 기반 쿼리
Azure Monitor는 테이블에 각각 여러 열로 구성된 로그 데이터를 구성합니다. 모든 테이블 및 열은 Analytics 포털에서 Log Analytics의 스키마 창에 표시됩니다. 관심 있는 테이블을 식별한 다음, 일부 데이터를 살펴봅니다.

```Kusto
SecurityEvent
| take 10
```

위에 표시된 쿼리는 특정 순서 없이 *SecurityEvent* 테이블에서 10개의 결과를 반환합니다. 이는 테이블을 살펴보고 해당 구조 및 콘텐츠를 이해하는 매우 일반적인 방법입니다. 빌드되는 방식을 살펴보겠습니다.

* 쿼리는 테이블 이름 *SecurityEvent*로 시작합니다. 이 일부는 쿼리의 범위를 정의합니다.
* 파이프(|) 문자는 명령을 분리하므로 다음 명령의 입력에서 첫 번째 명령의 출력입니다. 임의의 수의 파이프된 요소를 추가할 수 있습니다.
* 다음 파이프는 테이블에서 특정 개수의 임의 레코드를 반환하는 **take** 명령입니다.

`| take 10`을 추가하지 않고도 실제로 쿼리를 실행할 수 있습니다. 이는 여전히 유효할 수 있지만 최대 10,000개의 결과를 반환할 수 있습니다.

### <a name="search-queries"></a>검색 쿼리
검색 쿼리는 덜 구조적이며, 일반적으로 해당 열에 있는 특정 값을 포함하는 레코드를 찾기에 더 적합합니다.

```Kusto
search in (SecurityEvent) "Cryptographic"
| take 10
```

이 쿼리는 구문 "Cryptographic"을 포함하는 레코드에 대한 *SecurityEvent* 테이블을 검색합니다. 이러한 레코드 중 10개의 레코드가 반환되고 표시됩니다. `in (SecurityEvent)` 부분을 생략하고 `search "Cryptographic"`만을 실행하는 경우 검색은 *모든* 테이블을 살펴봅니다. 이는 시간이 더 걸리며 덜 효율적입니다.

> [!WARNING]
> 검색 쿼리는 더 많은 데이터를 처리할 수 있기 때문에 일반적으로 쿼리 테이블 기반 보다 느립니다. 

## <a name="sort-and-top"></a>정렬 및 위쪽
**take**가 몇 가지 레코드를 가져오는 데 유용하지만 특정 순서 없이 결과가 선택되고 표시됩니다. 정렬된 보기를 가져오려면 기본 열을 기준으로 **정렬**할 수 있습니다.

```Kusto
SecurityEvent   
| sort by TimeGenerated desc
```

그러나 너무 많은 결과를 반환할 수 있으며 약간의 시간이 소요될 수 있습니다. 위의 쿼리는 TimeGenerated 열을 기준으로 *전체* SecurityEvent 테이블을 정렬합니다. 그런 다음, Analytics 포털은 10,000개의 레코드만을 표시하도록 디스플레이를 제한합니다. 이 방법은 물론 최적이 아닙니다.

최신 10개의 레코드만을 가져오는 가장 좋은 방법은 서버 쪽에서 전체 테이블을 정렬한 다음, 상위 레코드를 반환하는 **top**을 사용하는 것입니다.

```Kusto
SecurityEvent
| top 10 by TimeGenerated
```

내림차순은 기본 정렬 순서이므로 일반적으로 **desc** 인수를 생략합니다. 출력은 다음과 같습니다.

![상위 10개](media/get-started-queries/top10.png)


## <a name="where-filtering-on-a-condition"></a>Where: 조건에 대한 필터링
해당 이름으로 표시된 대로 Filters, 특정 조건에 따라 데이터를 필터링합니다. 이는 관련 정보에 대한 쿼리 결과를 제한하는 가장 일반적인 방법입니다.

쿼리에 필터를 추가하려면 하나 이상의 조건이 뒤에 오는 **where** 연산자를 사용합니다. 예를 들어 다음 쿼리는 _Level_이 _8_인 *SecurityEvent* 레코드만을 반환합니다.

```Kusto
SecurityEvent
| where Level == 8
```

필터 조건을 작성하는 경우 다음 식을 사용할 수 있습니다.

| 식 | 설명 | 예 |
|:---|:---|:---|
| == | 같은지 여부를 확인<br>(대/소문자 구분) | `Level == 8` |
| =~ | 같은지 여부를 확인<br>(대/소문자 구분하지 않음) | `EventSourceName =~ "microsoft-windows-security-auditing"` |
| !=, <> | 같지 않음 확인<br>(두 식이 모두 동일) | `Level != 4` |
| *and*, *or* | between 조건 필수| `Level == 16 or CommandLine != ""` |

여러 조건으로 필터링하려면 **and**를 사용하거나

```Kusto
SecurityEvent
| where Level == 8 and EventID == 4672
```

여러 **where** 요소를 번갈아 파이프할 수 있습니다.

```Kusto
SecurityEvent
| where Level == 8 
| where EventID == 4672
```
    
> [!NOTE]
> 값은 여러 형식을 가질 수 있으므로 올바른 형식에서 비교를 수행하도록 캐스트해야 할 수 있습니다. 예를 들어 SecurityEvent *Level* 열이 String 형식이므로 숫자 연산자를 사용하기 전에 *int* 또는 *long*과 같은 숫자 형식으로 캐스트해야 합니다. `SecurityEvent | where toint(Level) >= 10`

## <a name="specify-a-time-range"></a>시간 범위 지정

### <a name="time-picker"></a>시간 선택기
시간 선택기는 실행 단추 옆에 있고 지난 24시간의 레코드만을 쿼리하는 것을 나타냅니다. 이는 모든 쿼리에 적용되는 기본 시간 범위입니다. 지난 1시간의 레코드만을 가져오려면 _지난 1시간_을 선택하고 쿼리를 다시 실행합니다.

![시간 선택](media/get-started-queries/timepicker.png)


### <a name="time-filter-in-query"></a>쿼리에서 시간 필터
쿼리에 시간 필터를 추가하여 고유한 시간 범위를 정의할 수도 있습니다. 시간 필터를 테이블 이름 바로 뒤에 배치하는 것이 좋습니다. 

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| where toint(Level) >= 10
```

위의 시간 필터에서 `ago(30m)`는 "30분 전"을 의미하므로 이 쿼리는 최근 30분의 레코드만을 반환합니다. 기타 시간 단위에는 일(2d), 분(25m) 및 초(10s)가 포함됩니다.


## <a name="project-and-extend-select-and-compute-columns"></a>Project 및 Extend: 열 선택 및 계산
**project**를 사용하여 결과에 포함할 특정 열을 선택합니다.

```Kusto
SecurityEvent 
| top 10 by TimeGenerated 
| project TimeGenerated, Computer, Activity
```

앞의 예제에서는 이 출력을 생성합니다.

![쿼리 프로젝트 결과](media/get-started-queries/project.png)

**project**를 사용하여 열 이름을 바꾸고 새 열을 정의할 수도 있습니다. 다음 예제에서는 project를 사용하여 다음을 수행합니다.

* *Computer* 및 *TimeGenerated* 원본 열만을 선택합니다.
* *Activity* 열의 이름을 *EventDetails*로 바꿉니다.
* *EventCode*라는 새 열을 만듭니다. **substring()** 함수는 Activity 필드에서 첫 번째 네 개의 문자만을 가져오는 데 사용됩니다.


```Kusto
SecurityEvent
| top 10 by TimeGenerated 
| project Computer, TimeGenerated, EventDetails=Activity, EventCode=substring(Activity, 0, 4)
```

**extend**는 결과 집합에서 모든 원본 열을 유지하고 추가 항목을 정의합니다. 다음 쿼리에서 **확장할** 추가 하는 *EventCode* 열입니다. 보려는 레코드의 세부 정보를 확장 하는이 열이 표시 되지 테이블 결과의 끝에 있는 경우 있습니다 참고 해야 합니다.

```Kusto
SecurityEvent
| top 10 by TimeGenerated
| extend EventCode=substring(Activity, 0, 4)
```

## <a name="summarize-aggregate-groups-of-rows"></a>Summarize: 행 그룹 집계
**summarize**를 사용하여 하나 이상의 열에 따라 레코드 그룹을 식별하고, 집계를 적용합니다. **summarize**의 가장 일반적인 사용은 *count*이며 각 그룹의 결과 수를 반환합니다.

다음 쿼리는 지난 시간에서 모든 *Perf* 레코드를 검토하고, *ObjectName*으로 그룹화하고, 각 그룹의 레코드 수를 계산합니다. 
```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName
```

경우에 따라 여러 차원에서 그룹을 정의하는 것일 수 있습니다. 이러한 값의 각 고유한 조합은 별도 그룹을 정의합니다.

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName, CounterName
```

또 다른 일반적인 사용은 각 그룹에 대해 수치 연산 또는 통계 계산을 수행하는 것입니다. 예를 들어 다음은 각 컴퓨터에 대한 평균 *CounterValue*를 계산합니다.

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer
```

그러나 여기서 다양한 성능 카운터를 함께 혼합했으므로 이 쿼리의 결과는 의미가 없습니다. 더욱 의미 있게 하기 위해 *CounterName* 및 *Computer*의 각 조합에 대해 개별적으로 평균을 계산해야 합니다.

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer, CounterName
```

### <a name="summarize-by-a-time-column"></a>시간 열별 요약
결과 그룹화는 time 열 또는 다른 연속 값을 기준으로 할 수도 있습니다. 그러나 간단히 `by TimeGenerated`를 요약하면 이는 고유한 값이므로 시간 범위 동안 모든 단일 밀리초에 대한 그룹을 만듭니다. 

연속 값에 따라 그룹을 만들려면 **bin**을 사용하여 범위를 관리 가능한 단위로 나누는 것이 가장 좋습니다. 다음 쿼리는 특정 컴퓨터에서 사용 가능한 메모리(*Available MBytes*)를 측정하는 *Perf* 레코드를 분석합니다. 지난 7 일 동안 1 시간 동안 각의 평균 값을 계산 합니다.

```Kusto
Perf 
| where TimeGenerated > ago(7d)
| where Computer == "ContosoAzADDS2" 
| where CounterName == "Available MBytes" 
| summarize avg(CounterValue) by bin(TimeGenerated, 1h)
```

출력을 명확히 하려면 시간에 따른 사용 가능한 메모리를 보여주는 시간 차트로 표시하도록 선택합니다.

![시간에 따른 쿼리 메모리](media/get-started-queries/chart.png)



## <a name="next-steps"></a>다음 단계

- [검색 쿼리 작성](search-queries.md)에 대해 알아보기
