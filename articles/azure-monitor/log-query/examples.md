---
title: Azure Monitor 로그 쿼리 예제 | Microsoft Docs
description: Kusto 쿼리 언어를 사용한 Azure Monitor의 로그 쿼리 예제입니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/16/2020
ms.openlocfilehash: 18cd74ac9298b7dd058de2b224f677ec0d8f2d64
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "79480286"
---
# <a name="azure-monitor-log-query-examples"></a>Azure Monitor 로그 쿼리 예제
이 문서에는 [Kusto 쿼리 언어](/azure/kusto/query/)를 사용하여 Azure Monitor에서 여러 형식의 로그 데이터를 검색하는 [쿼리](log-query-overview.md)의 다양한 예제가 포함되어 있습니다. 여기서 소개하는 예제에서는 여러 가지 방법으로 데이터를 통합하고 분석하므로, 이러한 샘플을 활용하면 고유한 요구 사항을 충족하는 데 사용할 수 있는 여러 전략을 파악할 수 있습니다.  

이러한 샘플에서 사용되는 다양한 키워드에 대한 자세한 내용은 [Kusto 언어 참조](https://docs.microsoft.com/azure/kusto/query/)를 참조하세요. Azure Monitor를 처음 사용하는 경우에는 [쿼리 작성 단원](get-started-queries.md)을 진행하세요.

## <a name="events"></a>이벤트

### <a name="search-application-level-events-described-as-cryptographic"></a>"cryptographic"으로 기술되는 애플리케이션 수준 이벤트 검색
이 예제에서는 **Events** 테이블에서 **EventLog**가 _Application_이며 **RenderedDescription**에 _cryptographic_ 이 포함된 레코드를 검색합니다. 검색 결과에는 지난 24시간 동안의 레코드가 포함됩니다.

```Kusto
Event
| where EventLog == "Application" 
| where TimeGenerated > ago(24h) 
| where RenderedDescription contains "cryptographic"
```

### <a name="search-events-related-to-unmarshaling"></a>unmarshaling 관련 이벤트 검색
**Event** 및 **SecurityEvents** 테이블에서 _unmarshaling_이 언급된 레코드를 검색합니다.

```Kusto
search in (Event, SecurityEvent) "unmarshaling"
```

## <a name="heartbeat"></a>Heartbeat

### <a name="chart-a-week-over-week-view-of-the-number-of-computers-sending-data"></a>데이터를 전송하는 컴퓨터 수의 주 단위 보기 차트 작성

다음 예제에서는 매주 하트비트를 전송한 고유 컴퓨터 수의 차트를 작성합니다.

```Kusto
Heartbeat
| where TimeGenerated >= startofweek(ago(21d))
| summarize dcount(Computer) by endofweek(TimeGenerated) | render barchart kind=default
```

### <a name="find-stale-computers"></a>부실 컴퓨터 찾기

다음 예제에서는 전날에는 활성 상태였지만 지난 1시간 동안 하트비트를 전송하지 않은 컴퓨터를 찾습니다.

```Kusto
Heartbeat
| where TimeGenerated > ago(1d)
| summarize LastHeartbeat = max(TimeGenerated) by Computer
| where isnotempty(Computer)
| where LastHeartbeat < ago(1h)
```

### <a name="get-the-latest-heartbeat-record-per-computer-ip"></a>컴퓨터 IP별 최신 하트비트 레코드 가져오기

이 예제에서는 각 컴퓨터 IP의 마지막 하트비트 레코드를 반환합니다.
```Kusto
Heartbeat
| summarize arg_max(TimeGenerated, *) by ComputerIP
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>보호 상태 레코드와 하트비트 레코드의 일치 여부 확인

이 예제에서는 컴퓨터와 시간이 모두 일치하는 관련 보호 상태 레코드와 하트비트 레코드를 찾습니다.
시간 필드는 가장 가까운 분 단위로 반올림됩니다. 이 반올림에는 런타임 bin 계산이 사용되었습니다. `round_time=bin(TimeGenerated, 1m)`

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```

### <a name="server-availability-rate"></a>서버 가용성 비율

하트비트 레코드를 기준으로 하여 서버 가용성 비율을 계산합니다. 가용성은 "시간당 하트비트 1개 이상"으로 정의됩니다.
즉, 100시간 중에 98시간 동안 서버를 사용할 수 있었다면 가용성 비율은 98%입니다.

```Kusto
let start_time=startofday(datetime("2018-03-01"));
let end_time=now();
Heartbeat
| where TimeGenerated > start_time and TimeGenerated < end_time
| summarize heartbeat_per_hour=count() by bin_at(TimeGenerated, 1h, start_time), Computer
| extend available_per_hour=iff(heartbeat_per_hour>0, true, false)
| summarize total_available_hours=countif(available_per_hour==true) by Computer 
| extend total_number_of_buckets=round((end_time-start_time)/1h)+1
| extend availability_rate=total_available_hours*100/total_number_of_buckets
```


## <a name="multiple-data-types"></a>여러 데이터 형식

### <a name="chart-the-record-count-per-table"></a>테이블당 레코드 개수 차트 작성
다음 예제에서는 모든 테이블에서 지난 5시간 동안의 모든 레코드를 수집한 다음 각 테이블의 레코드 개수를 계산합니다. 결과는 시간 차트에 표시됩니다.

```Kusto
union withsource=sourceTable *
| where TimeGenerated > ago(5h) 
| summarize count() by bin(TimeGenerated,10m), sourceTable
| render timechart
```

### <a name="count-all-logs-collected-over-the-last-hour-by-type"></a>지난 1시간 동안 수집된 모든 로그의 개수를 형식별로 계산
다음 예제에서는 지난 1시간 동안 보고된 모든 항목을 검색한 다음 **Type**을 기준으로 각 테이블의 레코드 개수를 계산합니다. 결과는 막대형 차트에 표시됩니다.

```Kusto
search *
| where TimeGenerated > ago(1h) 
| summarize CountOfRecords = count() by Type
| render barchart
```

## <a name="azurediagnostics"></a>AzureDiagnostics

### <a name="count-azure-diagnostics-records-per-category"></a>범주별 Azure 진단 레코드 개수 계산
이 예제에서는 각 고유 범주의 모든 Azure 진단 레코드 개수를 계산합니다.

```Kusto
AzureDiagnostics 
| where TimeGenerated > ago(1d)
| summarize count() by Category
```

### <a name="get-a-random-record-for-each-unique-category"></a>각 고유 범주의 임의 레코드 가져오기
이 예제에서는 각 고유 범주의 임의 Azure 진단 레코드 하나를 가져옵니다.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize any(*) by Category
```

### <a name="get-the-latest-record-per-category"></a>범주별 최신 레코드 가져오기
이 예제에서는 각 고유 범주의 최신 Azure 진단 레코드를 가져옵니다.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize arg_max(TimeGenerated, *) by Category
```

## <a name="network-monitoring"></a>네트워크 모니터링

### <a name="computers-with-unhealthy-latency"></a>대기 시간이 비정상인 컴퓨터
이 예제에서는 대기 시간이 비정상인 고유 컴퓨터 목록을 만듭니다.

```Kusto
NetworkMonitoring 
| where LatencyHealthState <> "Healthy" 
| where Computer != "" 
| distinct Computer
```

## <a name="performance"></a>성능

### <a name="join-computer-perf-records-to-correlate-memory-and-cpu"></a>컴퓨터 성능 레코드를 조인하여 메모리와 CPU의 상관 관계 설정
이 예제에서는 특정 컴퓨터의 **perf** 레코드 상관 관계를 설정하고 시간 차트 2개(평균 CPU/최대 메모리)를 만듭니다.

```Kusto
let StartTime = now()-5d;
let EndTime = now()-4d;
Perf
| where CounterName == "% Processor Time"  
| where TimeGenerated > StartTime and TimeGenerated < EndTime
| project TimeGenerated, Computer, cpu=CounterValue 
| join kind= inner (
   Perf
    | where CounterName == "% Used Memory"  
    | where TimeGenerated > StartTime and TimeGenerated < EndTime
    | project TimeGenerated , Computer, mem=CounterValue 
) on TimeGenerated, Computer
| summarize avgCpu=avg(cpu), maxMem=max(mem) by TimeGenerated bin=30m  
| render timechart
```

### <a name="perf-cpu-utilization-graph-per-computer"></a>컴퓨터당 성능 CPU 사용률 그래프
이 예제에서는 _Contoso_로 시작하는 컴퓨터의 CPU 사용률을 계산하여 차트로 작성합니다.

```Kusto
Perf
| where TimeGenerated > ago(4h)
| where Computer startswith "Contoso" 
| where CounterName == @"% Processor Time"
| summarize avg(CounterValue) by Computer, bin(TimeGenerated, 15m) 
| render timechart
```

## <a name="protection-status"></a>보호 상태

### <a name="computers-with-non-reporting-protection-status-duration"></a>보고하지 않는 보호 상태의 컴퓨터 및 해당 기간
이 예제에서는 보호 상태가 _Not Reporting_ 이었던 컴퓨터 및 해당 상태가 지속되었던 기간의 목록을 생성합니다.

```Kusto
ProtectionStatus
| where ProtectionStatus == "Not Reporting"
| summarize count(), startNotReporting = min(TimeGenerated), endNotReporting = max(TimeGenerated) by Computer, ProtectionStatusDetails
| join ProtectionStatus on Computer
| summarize lastReporting = max(TimeGenerated), startNotReporting = any(startNotReporting), endNotReporting = any(endNotReporting) by Computer
| extend durationNotReporting = endNotReporting - startNotReporting
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>보호 상태 레코드와 하트비트 레코드의 일치 여부 확인
이 예제에서는 컴퓨터와 시간이 모두 일치하는 관련 보호 상태 레코드와 하트비트 레코드를 찾습니다.
시간 필드는 **bin**을 사용하여 가장 가까운 분 단위로 반올림됩니다.

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```


## <a name="security-records"></a>보안 레코드

### <a name="count-security-events-by-activity-id"></a>활동 ID별 보안 이벤트 개수 계산


이 예에서는 **활동** 열의 고정 구조를 사용 \<ID\> - \<Name\> 합니다.
이 구조에서는 **Activity** 값을 새 열 2개로 구문 분석하고 각 **activityID**의 발생 횟수를 계산합니다.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| project Activity 
| parse Activity with activityID " - " activityDesc
| summarize count() by activityID
```

### <a name="count-security-events-related-to-permissions"></a>권한 관련 보안 이벤트 개수 계산
이 예제에서는 **Activity** 열에 _Permissions_ 라는 용어 전체가 포함된 **securityEvent** 레코드의 수를 표시합니다. 쿼리는 지난 30분 동안 생성된 레코드에 적용됩니다.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m)
| summarize EventCount = countif(Activity has "Permissions")
```

### <a name="find-accounts-that-failed-to-log-in-from-computers-with-a-security-detection"></a>보안 검색 기능이 있는 컴퓨터에서 로그인하지 못한 계정 찾기
이 예제에서는 보안 검색 기능이 있는 컴퓨터에서 로그인하지 못한 계정을 찾아서 개수를 계산합니다.

```Kusto
let detections = toscalar(SecurityDetection
| summarize makeset(Computer));
SecurityEvent
| where Computer in (detections) and EventID == 4624
| summarize count() by Account
```

### <a name="is-my-security-data-available"></a>보안 데이터 사용 가능 여부 확인
데이터 탐색을 시작할 때는 대개 데이터 가용성부터 확인합니다. 이 예제에서는 지난 30분 동안의 **SecurityEvent** 수를 표시합니다.

```Kusto
SecurityEvent 
| where TimeGenerated  > ago(30m) 
| count
```

### <a name="parse-activity-name-and-id"></a>활동 이름 및 ID 구문 분석
아래의 두 예제는 **활동** 열의 고정 구조를 사용 \<ID\> - \<Name\> 합니다. 첫 번째 예제에서는 **parse** 연산자를 사용하여 새 열 2개(**activityID** 및 **activityDesc**)에 값을 할당합니다.

```Kusto
SecurityEvent
| take 100
| project Activity 
| parse Activity with activityID " - " activityDesc
```

이 예제에서는 **split** 연산자를 사용하여 개별 값 배열을 만듭니다.
```Kusto
SecurityEvent
| take 100
| project Activity 
| extend activityArr=split(Activity, " - ") 
| project Activity , activityArr, activityId=activityArr[0]
```

### <a name="explicit-credentials-processes"></a>명시적 자격 증명 프로세스
다음 예에서는 지난 주에 명시적 자격 증명을 사용한 프로세스의 원형 차트를 표시합니다.

```Kusto
SecurityEvent
| where TimeGenerated > ago(7d)
// filter by id 4648 ("A logon was attempted using explicit credentials")
| where EventID == 4648
| summarize count() by Process
| render piechart 
```

### <a name="top-running-processes"></a>최다 실행 프로세스

다음 예제에서는 지난 3일 동안 가장 많이 실행된 5개 프로세스의 활동 타임라인을 표시합니다.

```Kusto
// Find all processes that started in the last three days. ID 4688: A new process has been created.
let RunProcesses = 
    SecurityEvent
    | where TimeGenerated > ago(3d)
    | where EventID == "4688";
// Find the 5 processes that were run the most
let Top5Processes =
RunProcesses
| summarize count() by Process
| top 5 by count_;
// Create a time chart of these 5 processes - hour by hour
RunProcesses 
| where Process in (Top5Processes) 
| summarize count() by bin (TimeGenerated, 1h), Process
| render timechart
```


### <a name="find-repeating-failed-login-attempts-by-the-same-account-from-different-ips"></a>동일 계정이 서로 다른 IP에서 시도하여 반복적으로 실패한 로그인 찾기

다음 예제에서는 지난 6시간 동안 서로 다른 IP 6개 이상에서 같은 계정이 시도하여 실패한 로그인을 찾습니다.

```Kusto
SecurityEvent 
| where AccountType == "User" and EventID == 4625 and TimeGenerated > ago(6h) 
| summarize IPCount = dcount(IpAddress), makeset(IpAddress)  by Account
| where IPCount > 5
| sort by IPCount desc
```

### <a name="find-user-accounts-that-failed-to-log-in"></a>로그인이 실패한 사용자 계정 찾기 
다음 예제에서는 전날 6번 이상 로그인이 실패한 사용자 계정 및 해당 계정이 마지막으로 로그인을 시도한 시간을 확인합니다.

```Kusto
let timeframe = 1d;
SecurityEvent
| where TimeGenerated > ago(1d)
| where AccountType == 'User' and EventID == 4625 // 4625 - failed log in
| summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
| where failed_login_attempts > 5
| project-away Account1
```

**join** 및 **let** 문을 사용하면 동일한 의심 계정이 나중에는 정상적으로 로그인할 수 있었는지를 확인할 수 있습니다.

```Kusto
let timeframe = 1d;
let suspicious_users = 
    SecurityEvent
    | where TimeGenerated > ago(timeframe)
    | where AccountType == 'User' and EventID == 4625 // 4625 - failed login
    | summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
    | where failed_login_attempts > 5
    | project-away Account1;
let suspicious_users_that_later_logged_in = 
    suspicious_users 
    | join kind=innerunique (
        SecurityEvent
        | where TimeGenerated > ago(timeframe)
        | where AccountType == 'User' and EventID == 4624 // 4624 - successful login,
        | summarize latest_successful_login=arg_max(TimeGenerated, Account) by Account
    ) on Account
    | extend was_login_after_failures = iif(latest_successful_login>latest_failed_login, 1, 0)
    | where was_login_after_failures == 1
;
suspicious_users_that_later_logged_in
```

## <a name="usage"></a>사용량

`Usage`데이터 형식은 솔루션 또는 데이터 형식에 따라 수집 데이터 볼륨을 추적 하는 데 사용할 수 있습니다. [컴퓨터](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#data-volume-by-computer) 또는 [Azure 구독, 리소스 그룹 또는 리소스](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#data-volume-by-azure-resource-resource-group-or-subscription)별로 수집 데이터 볼륨을 연구 하는 다른 방법이 있습니다.

#### <a name="data-volume-by-solution"></a>솔루션별 데이터 볼륨

지난 한 달 동안(마지막 1일 미만의 시간은 제외) 솔루션별로 청구 가능한 데이터 볼륨을 보는 데 사용되는 쿼리는 다음과 같습니다.

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution | render barchart
```

여기서 `where IsBillable = true` 절은 수집 비용이 없는 특정 솔루션에서 데이터 형식을 필터링합니다.  또한를 사용 하는 절은 `TimeGenerated` Azure Portal의 쿼리 환경이 기본값 24 시간을 초과 하지 않도록 하는 데만 사용 됩니다. 사용량 데이터 형식을 사용하는 경우, `StartTime` 및 `EndTime`은 결과가 표시되는 시간 버킷을 나타냅니다. 

#### <a name="data-volume-by-type"></a>형식별 데이터 볼륨

데이터 형식별 데이터 추세를 확인하기 위해 다음과 같이 더 자세한 내용을 다룰 수 있습니다.

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType | render barchart
```

또는 지난 한 달 동안 솔루션 및 형식별로 테이블을 표시하려면

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by Solution, DataType
| sort by Solution asc, DataType asc
```

> [!NOTE]
> 사용량 데이터 형식의 일부 필드가 여전히 스키마에 있지만 더 이상 사용되지 않으며 해당 값은 더 이상 채워지지 않습니다. 이는 **컴퓨터**일 뿐 아니라 수집과 관련된 필드(**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** 및 **AverageProcessingTimeMs**)이기도 합니다.

## <a name="updates"></a>업데이트

### <a name="computers-still-missing-updates"></a>아직 업데이트가 누락된 상태의 컴퓨터
이 예제에서는 며칠 전에 중요 업데이트 하나 이상이 설치되어 있지 않았으며 아직도 해당 업데이트가 누락되어 있는 컴퓨터 목록을 표시합니다.

```Kusto
let ComputersMissingUpdates3DaysAgo = Update
| where TimeGenerated between (ago(30d)..ago(1h))
| where Classification !has "Critical" and UpdateState =~ "Needed"
| summarize makeset(Computer);
Update
| where TimeGenerated > ago(1d)
| where Classification has "Critical" and UpdateState =~ "Needed"
| where Computer in (ComputersMissingUpdates3DaysAgo)
| summarize UniqueUpdatesCount = dcount(Product) by Computer, OSType
```


## <a name="next-steps"></a>다음 단계

- [Kusto 언어 참조](/azure/kusto/query)에서 언어에 대한 자세한 내용을 참조합니다.
- [Azure Monitor에서 로그 쿼리 작성 단원](get-started-queries.md)을 계속 진행합니다.