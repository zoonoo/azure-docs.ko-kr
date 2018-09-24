---
title: Azure Log Analytics의 고급 쿼리 | Microsoft Docs
description: 이 문서에서는 Analytics 포털을 사용하여 Log Analytics에서 쿼리를 작성하는 것에 대한 자습서를 제공합니다.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 2f9868abd0eb8bf96928aeba6f96c10bcb91c4e2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46958560"
---
# <a name="writing-advanced-queries-in-log-analytics"></a>Log Analytics에서 고급 쿼리 작성

> [!NOTE]
> 이 단원을 완료하기 전에 [Analytics 포털 시작](get-started-analytics-portal.md) 및 [쿼리 시작](get-started-queries.md)을 완료해야 합니다.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

## <a name="reusing-code-with-let"></a>let을 사용하여 코드를 다시 사용
`let`을 사용하여 변수에 결과를 할당하고 나중에 쿼리에서 참조할 수 있습니다.

```Kusto
// get all events that have level 2 (indicates warning level)
let warning_events=
Event
| where EventLevel == 2;
// count the number of warning events per computer
warning_events
| summarize count() by Computer 
```

또한 변수에 상수 값을 할당할 수도 있습니다. 이렇게 하면 쿼리를 실행할 때마다 변경해야 하는 필드에 대해 매개 변수를 설정할 수 있습니다. 필요에 따라 이러한 매개 변수를 수정합니다. 예를 들어 지정된 기간에 사용 가능한 디스크 공간 및 사용 가능한 메모리(백분위수)를 계산하려는 경우 다음을 입력합니다.

```Kusto
let startDate = datetime(2018-08-01T12:55:02);
let endDate = datetime(2018-08-02T13:21:35);
let FreeDiskSpace =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Logical Disk" and CounterName=="Free Megabytes"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
let FreeMemory =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Memory" and CounterName=="Available MBytes Memory"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
union FreeDiskSpace, FreeMemory
```

이렇게 하면 쿼리를 실행하는 다음 번에 시작 시간 및 종료 시간을 쉽게 변경할 수 있습니다.

### <a name="local-functions-and-parameters"></a>로컬 함수 및 매개 변수
`let` 문을 사용하여 같은 쿼리에서 사용할 수 있는 함수를 만듭니다. 예를 들어 날짜/시간 필드(UTC 형식)를 사용하는 함수를 정의한 후 표준 미국 형식으로 변환합니다. 

```Kusto
let utc_to_us_date_format = (t:datetime)
{
  strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ",
  bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
};
Event 
| where TimeGenerated > ago(1h) 
| extend USTimeGenerated = utc_to_us_date_format(TimeGenerated)
| project TimeGenerated, USTimeGenerated, Source, Computer, EventLevel, EventData 
```

## <a name="functions"></a>Functions
다른 쿼리에서 참조될 수 있도록, 함수 별칭을 사용하여 쿼리를 저장할 수 있습니다. 예를 들어, 다음 표준 쿼리는 마지막 날에 보고된 모든 누락된 보안 업데이트를 반환합니다.

```Kusto
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

이 쿼리를 함수로 저장하고 _security_updates_last_day_와 같이 별칭을 지정할 수 있습니다. 그런 후, SQL과 관련된 필수 보안 업데이트를 검색하기 위해 다른 쿼리에서 사용할 수 있습니다.

```Kusto
security_updates_last_day | where Title contains "SQL"
```

쿼리를 함수로 저장하려면 포털에서 **저장** 단추를 선택하고 **다른 이름으로 저장**을 _함수_로 변경합니다. 함수 별칭에는 문자, 숫자 또는 밑줄을 포함할 수 있지만 문자 또는 밑줄로 시작해야 합니다.

> [!NOTE]
> Log Analytics 쿼리에는 함수를 저장할 수 있지만, Application Insights 쿼리의 경우는 현재 이러한 기능이 지원되지 않습니다.


## <a name="print"></a>Print
`print`는 단일 열 및 단일 행이 있는 테이블을 반환하며, 계산 결과를 표시합니다. 이 함수는 단순 계산이 필요한 경우에 종종 사용됩니다. 예를 들어, PST로 현재 시간을 확인한 후 EST를 사용하여 열에 추가하려면 다음을 입력합니다.

```Kusto
print nowPst = now()-8h
| extend nowEst = nowPst+3h
```

## <a name="datatable"></a>Datatable
`datatable`을 사용하면 데이터 집합을 정의할 수 있습니다. 스키마 및 값 집합을 제공한 후, 해당 테이블을 다른 쿼리 요소에 파이프합니다. 예를 들어, RAM 사용량 테이블을 만든 후 시간당 평균 값을 계산하려면 다음을 입력합니다.

```Kusto
datatable (TimeGenerated: datetime, usage_percent: double)
[
  "2018-06-02T15:15:46.3418323Z", 15.5,
  "2018-06-02T15:45:43.1561235Z", 20.2,
  "2018-06-02T16:16:49.2354895Z", 17.3,
  "2018-06-02T16:46:44.9813459Z", 45.7,
  "2018-06-02T17:15:41.7895423Z", 10.9,
  "2018-06-02T17:44:23.9813459Z", 24.7,
  "2018-06-02T18:14:59.7283023Z", 22.3,
  "2018-06-02T18:45:12.1895483Z", 25.4
]
| summarize avg(usage_percent) by bin(TimeGenerated, 1h)
```

Datatable 구문은 조회 테이블을 만들 때도 매우 유용합니다. 예를 들어, _SecurityEvent_ 테이블의 이벤트 ID와 같은 테이블 데이터를 다른 위치에 나열된 이벤트 형식으로 매핑하고, `datatable`을 사용하여 이벤트 형식의 조회 테이블을 만들고, 이 데이터 테이블을  _SecurityEvent_ 데이터에 조인하려면 다음을 입력합니다.

```Kusto
let eventCodes = datatable (EventID: int, EventType:string)
[
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4672, "Access",
    4624, "Account activity",
    4799, "Access management",
    4798, "Access management",
    5059, "Key operation",
    4648, "A logon was attempted using explicit credentials",
    4768, "Access management",
    4662, "Other",
    8002, "Process action",
    4904, "Security event management",
    4905, "Security event management",
];
SecurityEvent
| where TimeGenerated > ago(1h) 
| join kind=leftouter (
  eventCodes
) on EventID
| project TimeGenerated, Account, AccountType, Computer, EventType
```

## <a name="next-steps"></a>다음 단계
Log Analytics 쿼리 언어를 사용에 대해서는 다른 단원을 참조하세요.

- [문자열 작업](string-operations.md)
- [날짜 및 시간 작업](datetime-operations.md)
- [집계 함수](aggregations.md)
- [고급 집계](advanced-aggregations.md)
- [JSON 및 데이터 구조](json-data-structures.md)
- [조인](joins.md)
- [차트](charts.md)