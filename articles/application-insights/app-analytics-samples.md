<properties 
	pageTitle="Application Insights Analytics의 쿼리 샘플" 
	description="Application Insights의 강력한 검색 도구인 Application Insights Analytics의 쿼리 샘플" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/01/2016" 
	ms.author="awills"/>


# Application Insights Analytics 샘플

[Application Insights Analytics](app-analytics.md)는 [Application Insights](app-insights-overview.md) 원격 분석을 위한 강력한 검색 엔진입니다. 이 페이지에서는 Application Insights Analytics 쿼리 언어인 AIQL에 대해 설명합니다. 또한 시작하기 위해 권장되는 [언어 둘러보기](app-analytics-tour.md)도 있습니다.


[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

## 쿼리 모범 사례 

쿼리를 더 빨리 실행하기 위해 따를 수 있는 여러 가지 할 일과 하지 않아야 할 일을 제공합니다.

할 일:

-	먼저 시간 필터를 사용합니다. Application Insights Analytics는 시간 필터를 이용하도록 고도로 최적화되어 있습니다.
-	대부분의 데이터를 제거할 것으로 예상하는 필터를 쿼리 시작 부분(시간 필터 바로 뒤)에 넣습니다.
-	대부분의 필터가 쿼리 시작 부분(‘extend’를 사용하기 전)에 나오는지 확인하십시오. 
-	전체 토큰을 찾을 때 키워드 ‘contains’보다 ‘has’를 사용하는 것이 좋습니다. ‘has’는 부분 문자열을 찾을 필요가 없기 때문에 성능이 향상됩니다.
-	‘*’(모든 열에 대해 전체 텍스트 검색)를 사용하기보다는 특정 열에서 찾는 것이 좋습니다.
-	조인을 사용하는 경우 조인의 양쪽에서 모두 필요한 열만 검색 대상으로 합니다(이렇게 하면 컴퓨터 간에 가져가야 하는 페이로드가 감소함).

하지 말아야 할 일:

-	새 쿼리를 'limit [작은 숫자]' 또는 'count' 없이 끝에서 시도합니다. 알 수 없는 데이터 집합에 대해 바인딩 해제된 쿼리를 실행하면 클라이언트에 반환할 결과가 엄청나게 많이 생성되어 응답이 느려지고 클러스터를 많이 사용하게 됩니다.
-	1B 이상의 레코드에 대해 변환(JSON, 문자열 등)을 적용하는 것을 발견하는 경우 변환에 공급되는 데이터의 양을 줄이도록 쿼리를 다시 작성합니다.





<a name="activities"></a>
## 시작 및 정지 입네트에서 세션 가져오기

이벤트 로그가 있고 일부 이벤트가 확장된 활동 또는 세션의 시작 또는 끝을 표시한다고 가정해 보겠습니다.

|이름|City|SessionId|Timestamp|
|---|---|---|---|
|시작|런던|2817330|2015-12-09T10:12:02.32|
|게임|런던|2817330|2015-12-09T10:12:52.45|
|시작|맨체스터|4267667|2015-12-09T10:14:02.23|
|중지|런던|2817330|2015-12-09T10:23:43.18|
|취소|맨체스터|4267667|2015-12-09T10:27:26.29|
|중지|맨체스터|4267667|2015-12-09T10:28:31.72|

모든 이벤트에는 세션 Id가 있으므로 문제는 같은 id를 가진 시작 및 중지 이벤트를 일치시키는 것입니다.

```AIQL
let Events = MyLogTable | where ... ;

Events
| where Name == "Start"
| project Name, City, SessionId, StartTime=timestamp
| join (Events 
        | where Name="Stop"
        | project StopTime=timestamp, SessionId) 
    on activityId
| project City, SessionId, StartTime, StopTime, Duration = StopTime - StartTime
```

[`let`](app-analytics-syntax.md#let-statements)를 사용하여 조인에 진입하기 전에 가능하면 많이 축소된 테이블 프로젝션의 이름을 지정합니다. 결과에 시작 및 중지 시간이 모두 나타날 수 있도록 타임스탬프 이름을 변경하려면 [`Project`](app-analytics-queries.md#project-operator)를 사용합니다. 또한 결과에서 보고자 하는 다른 열을 선택합니다. [`join`](app-analytics-queries.md#join-operator)은 같은 활동에 대해 시작 및 중지 항목을 일치시켜 각 활동에 대한 행을 만듭니다. 마지막으로, `project`도 역시 활동의 기간을 표시하는 열을 추가합니다.


|City|SessionId|StartTime|StopTime|기간|
|---|---|---|---|---|
|런던|2817330|2015-12-09T10:12:02.32|2015-12-09T10:23:43.18|00:11:40.46|
|맨체스터|4267667|2015-12-09T10:14:02.23|2015-12-09T10:28:31.72|00:14:29.49|

### 세션 id 없이 세션 가져오기

이제 편의상 시작 및 중지 이벤트에 일치시킬 수 있는 세션 ID가 없다고 가정해 보겠습니다. 하지만 세션이 발생한 클라이언트의 IP 주소를 가지고 있습니다. 각 클라이언트 주소가 한 번에 한 세션만 수행한다고 가정하면 각 시작 이벤트를 같은 IP 주소의 다음 중지 이벤트에 일치시킬 수 있습니다.

```AIQL
Events 
| where Name == "Start" 
| project City, ClientIp, StartTime = timestamp
| join  kind=inner
    (Events
    | where Name == "Stop" 
    | project StopTime = timestamp, ClientIp)
    on ClientIp
| extend duration = StopTime - StartTime 
    // Remove matches with earlier stops:
| where  duration > 0  
    // Pick out the earliest stop for each start and client:
| summarize argmin(duration, *) by bin(StartTime,1s), ClientIp
```

조인은 모든 시작 시간을 같은 클라이언트 IP 주소의 모든 중지 시간과 일치시킵니다. 따라서 더 이전의 중지 시간과 일치하는 항목을 먼저 제거합니다.

그런 다음 시작 시간 및 ip를 기준으로 그룹화하여 각 세션에 대한 그룹을 가져옵니다. StartTime 매개 변수에 대해 `bin` 함수를 제공해야 합니다. 그렇지 않으면 AI Analytics가 자동으로 1시간 bin을 사용하여 일부 시작 시간을 잘못된 중지 시간과 일치시킵니다.

`argmin`은 각 그룹에서 최소 기간을 가진 행을 선택하며 `*` 매개 변수는 각 열 이름에 "min\_" 접두사를 추가하기는 하지만 다른 모든 열을 통과합니다.


![](./media/app-analytics-samples/040.png)

다음에는 편의상 크기의 bin에서 기간을 계산하기 위해 일부 코드를 추가할 수 있습니다. 가로 막대형 차트에 대한 기본 설정을 어느 정도 가지고 있으므로 `1s`로 나누어 시간 범위를 숫자로 변환합니다.


      // Count the frequency of each duration:
    | summarize count() by duration=bin(min_duration/1s, 10) 
      // Cut off the long tail:
    | where duration < 300
      // Prepare for display in a bar chart:
    | sort by duration asc 


![](./media/app-analytics-samples/050.png)


### 실제 예제

```AIQL

Logs  
| filter ActivityId == "ActivityId with Blablabla" 
| summarize max(Timestamp), min(Timestamp)  
| extend Duration = max_Timestamp - min_Timestamp 
 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  	 
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)  
| extend TotalLaunchedMaps = extract("totalLaunchedMaps=([^,]+),", 1, EventText, typeof(real))  
| extend MapsSeconds = extract("mapsMilliseconds=([^,]+),", 1, EventText, typeof(real)) / 1000 
| extend TotalMapsSeconds = MapsSeconds  / TotalLaunchedMaps 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2'  
| filter TotalLaunchedMaps > 0 
| summarize sum(TotalMapsSeconds) by UnitOfWorkId  
| extend JobMapsSeconds = sum_TotalMapsSeconds * 1 
| project UnitOfWorkId, JobMapsSeconds 
| join ( 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)   
| extend TotalLaunchedReducers = extract("totalLaunchedReducers=([^,]+),", 1, EventText, typeof(real)) 
| extend ReducesSeconds = extract("reducesMilliseconds=([^,]+)", 1, EventText, typeof(real)) / 1000 
| extend TotalReducesSeconds = ReducesSeconds / TotalLaunchedReducers 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2'  
| filter TotalLaunchedReducers > 0 
| summarize sum(TotalReducesSeconds) by UnitOfWorkId  
| extend JobReducesSeconds = sum_TotalReducesSeconds * 1 
| project UnitOfWorkId, JobReducesSeconds ) 
on UnitOfWorkId 
| join ( 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend JobName = extract("jobName=([^,]+),", 1, EventText)  
| extend StepName = extract("stepName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)  
| extend LaunchTime = extract("launchTime=([^,]+),", 1, EventText, typeof(datetime))  
| extend FinishTime = extract("finishTime=([^,]+),", 1, EventText, typeof(datetime)) 
| extend TotalLaunchedMaps = extract("totalLaunchedMaps=([^,]+),", 1, EventText, typeof(real))  
| extend TotalLaunchedReducers = extract("totalLaunchedReducers=([^,]+),", 1, EventText, typeof(real)) 
| extend MapsSeconds = extract("mapsMilliseconds=([^,]+),", 1, EventText, typeof(real)) / 1000 
| extend ReducesSeconds = extract("reducesMilliseconds=([^,]+)", 1, EventText, typeof(real)) / 1000 
| extend TotalMapsSeconds = MapsSeconds  / TotalLaunchedMaps  
| extend TotalReducesSeconds = (ReducesSeconds / TotalLaunchedReducers / ReducesSeconds) * ReducesSeconds  
| extend CalculatedDuration = (TotalMapsSeconds + TotalReducesSeconds) * time(1s) 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2') 
on UnitOfWorkId 
| extend MapsFactor = TotalMapsSeconds / JobMapsSeconds 
| extend ReducesFactor = TotalReducesSeconds / JobReducesSeconds 
| extend CurrentLoad = 1536 + (768 * TotalLaunchedMaps) + (1536 * TotalLaunchedMaps) 
| extend NormalizedLoad = 1536 + (768 * TotalLaunchedMaps * MapsFactor) + (1536 * TotalLaunchedMaps * ReducesFactor) 
| summarize sum(CurrentLoad), sum(NormalizedLoad) by  JobName  
| extend SaveFactor = sum_NormalizedLoad / sum_CurrentLoad 
```

<a name="concurrent-activities"><a/>
## 시간 경과에 따른 동시 세션 차트 작성

시작 및 종료 시간이 포함된 활동의 테이블이 있다고 가정합니다. 임의 시기에 동시에 실행할 수 있는 양을 표시하는 시간 경과에 따른 차트를 확인하려고 합니다.

다음은 예제 입력이며, 이를 편의상 `X`이라고 부르겠습니다.

|SessionId | StartTime | StopTime |
|---|---|---|
| a | 10:01:03 | 10:10:08 |
| b | 10:01:29 | 10:03:10 |
| C | 10:03:02 | 10:05:20 |

1분 bin의 차트를 원하므로 매 1m 간격으로 실행 중인 각 활동에 대해 계산할 수 있는 무언가를 만들어야 합니다.

중간 결과는 다음과 같습니다.

     X | extend samples = range(bin(StartTime, 1m), Stop, 1m)

`range` 지정된 간격으로 값의 배열을 생성합니다.

|SessionId | StartTime | StopTime | 샘플|
|---|---|---|---|
| a | 10:03:33 | 10:06:31 | [10:01:00,10:02:00,...10:06:00]|
| b | 10:02:29 | 10:03:45 | [10:02:00,10:03:00]|
| C | 10:03:12 | 10:04:30 | [10:03:00,10:04:00]|

하지만 해당 배열을 유지하는 대신에 [확장](app-analytics-queries.md#mvexpand-operator)합니다.

    X | mvexpand samples = range(bin(StartTime, 1m), StopTime , 1m)


|SessionId | StartTime | StopTime | 샘플|
|---|---|---|---|
| a | 10:03:33 | 10:06:31 | 10:01:00|
| a | 10:03:33 | 10:06:31 | 10:02:00|
| b | 10:02:29 | 10:03:45 | 10:02:00|
| a | 10:03:33 | 10:06:31 | 10:03:00|
| b | 10:02:29 | 10:03:45 | 10:03:00|
| C | 10:03:12 | 10:04:30 | 10:03:00|
| a | 10:03:33 | 10:06:31 | 10:04:00|
| C | 10:03:12 | 10:04:30 | 10:04:00|
|...||||

이제 샘플 시간을 기준으로 이들을 그룹화하여 각 활동의 동시성을 계산할 수 있습니다.

```
X
| mvexpand samples = range(bin(StartTime, 1m), StopTime , 1m)
| summarize count(SessionId) by bin(todatetime(samples),1m)
```

* `mvexpand`은 동적 형식의 열을 생성하기 때문에 todatetime()이 필요합니다.
* 숫자 값 및 날짜의 경우 summarize는 간격을 제공하지 않으면 언제나 기본 간격을 가진 bin 함수를 적용하므로 bin()이 필요합니다. 


| count\_SessionId | 샘플|
|---|---|
| 1 | 10:01:00|
| 2 | 10:02:00|
| 3 | 10:03:00|
| 2 | 10:04:00|
| 1 | 10:05:00|

이 정보를 가로 막대형 차트 또는 시간 차트로 렌더링할 수 있습니다.


## 조인의 다양한 특징

join 연산자의 정확한 특징은 kind 키워드를 사용하여 지정됩니다. 현재 AI Analytics는 왼쪽 중복 제거를 포함한 내부 조인(기본값), 표준 내부 조인, 왼쪽 우선 외부, 오른쪽 우선 외부, 전체 외부 및 왼쪽 앤티 조인의 여섯 가지 조인 연산자 특징을 지원합니다.
 
기본 조인 특징(지정된 kind 없음) 조인의 작업을 설명하기 위해 샘플 테이블 두 개를 사용하겠습니다.
 
테이블 X


|키 |값1 
|---|---
|a |1 
|b |2 
|b |3 
|C |4 

테이블 Y

|키 |값2 
|---|---
|b |10 
|C |20 
|C |30 
|d |40 

 
기본 조인은 조인 키의 왼쪽에서 중복 제거 뒤에서 내부 조인을 수행합니다(중복 제거에서 첫 번째 레코드는 보존됨). 이 문을 가정합니다.

    X | join Y on Key 

조인의 효과적인 왼쪽(중복 제거 후 테이블 X)은 다음과 같습니다.

|키 |값1 
|---|---
|a |1 
|b |2 
|C |4 

및 조인의 결과:

|키 |값1 |키 |값2 
|---|---|---|---
|b |2 |b |10 
|C |4 |C |20 
|C |4 |C |30 

(참고로 키 ‘a’와 ‘d’는 왼쪽과 오른쪽에 모두 일치하는 키가 없으므로 출력에 나타나지 않습니다.)
 
(지금까지 이 기능은 AI Analytics의 초기 버전에서 지원한 조인의 첫 번째 구현이었으며, 같은 상관성 ID 아래의 두 이벤트(각각 몇몇 필터링 기준을 일치시킴)를 상호 관련시키고 기여하는 추적 레코드가 여러 개 나오더라도 무시하고 찾으려는 현상의 모든 모양을 다시 가져오는 대표적인 로그/추적 분석 시나리오에서 유용합니다.)  

 
### 내부 조인(kind=inner) 

이는 SQL 환경에서 알려진 표준 내부 조인입니다. 출력 레코드는 왼쪽의 레코드가 오른쪽의 레코드와 같은 조인 키를 가질 때마다 생성됩니다.
 
    X | join kind=inner Y on Key 

결과:

|키 |값1 |키 |값2 
|---|---|---|---
|b |2 |b |10 
|b |3 |b |10 
|C |4 |C |20 
|C |4 |C |30 

참고로 오른쪽에서 나온 (b,10)은 왼쪽의 (b,2)와 (b,3)을 모두 사용하여 두 번 조인되었습니다. 또한 왼쪽의 (c,4)는 오른쪽의 (c,20)과 (c,30)을 모두 사용하여 두 번 조인되었습니다.

### 왼쪽 외부 조인(kind=leftouter) 

테이블 X 및 Y에 대한 왼쪽 우선 외부 조인의 결과는 조인 조건이 오른쪽 테이블(Y)에서 일치하는 레코드를 찾을 수 없더라도 언제나 왼쪽 테이블(X)의 모든 레코드를 포함합니다.
 
지정된 조건:

    X | join kind=leftouter Y on Key 

결과:

|키 |값1 |키 |값2 
|---|---|---|---
|a |1 |null |null 
|b |2 |b |10 
|b |3 |b |10 
|C |4 |C |20 
|C |4 |C |30 
 
### 오른쪽 우선 외부 조인(kind=rightouter) 

왼쪽 우선 외부 조인과 유사하지만 테이블에 대한 처리가 반대로 바뀝니다.
 
지정된 조건:

    X | join kind=rightouter Y on Key 

결과:


|키 |값1 |키 |값2 
|---|---|---|---
|b |2 |b |10 
|b |3 |b |10 
|C |4 |C |20 
|C |4 |C |30 
|null |null |d |40 
 
### 전체 외부 조인(kind=fullouter) 

개념적으로 볼 때 전체 외부 조인은 왼쪽과 오른쪽 조인을 모두 적용한 효과를 결합합니다. 조인된 테이블의 레코드가 일치하지 않는 경우 결과 집합은 일치하는 행이 없는 테이블의 모든 열에 대해 NULL 값을 갖게 됩니다. 일치하는 레코드의 경우 결과 집합(두 테이블에서 나온 내용으로 채워진 필드를 포함함)에 단일 행이 생성됩니다.
 
지정된 조건:

    X | join kind=fullouter Y on Key 

결과:

|키 |값1 |키 |값2 
|---|---|---|---
|a |1 |null |null 
|b |2 |b |10 
|b |3 |b |10 
|C |4 |C |20 
|C |4 |C |30 
|null |null |d |40 
 
### 왼쪽 앤티 조인(kind=leftanti) 

왼쪽 앤티 조인은 오른쪽의 어떤 레코드와도 일치하지 않는 왼쪽의 모든 레코드를 반환합니다.
 
    X | join kind=leftanti Y on Key 
 
결과:

|키 |값1 
|---|---
|a |1 
 
앤티 조인은 “NOT IN” 쿼리를 모델링합니다.



## 인라인 사전 매핑을 사용하여 조인

```AIQL

let TeamFoundationJobResult = range i from 1 to 1 step 1 
  | extend recordsJson = "[ 
    { 'key': -1, 'value': 'None'}, 
    { 'key': 0, 'value': 'Succeeded'}, 
    { 'key': 1, 'value': 'PartiallySucceeded'}, 
    { 'key': 2, 'value': 'Failed'}, 
    { 'key': 3, 'value': 'Stopped'}, 
    { 'key': 4, 'value': 'Killed'}, 
    { 'key': 5, 'value': 'Blocked'}, 
    { 'key': 6, 'value': 'ExtensionNotFound'}, 
    { 'key': 7, 'value': 'Inactive'}, 
    { 'key': 8, 'value': 'Disabled'}, 
    { 'key': 9, 'value': 'JobInitializationError'} 
  ]" 
  | mvexpand record = parsejson(replace("'", """, recordsJson)) 
  | project Result = toint(record.key), ResultString = tostring(record.value); 
JobHistory 
  | where PreciseTimeStamp > ago(1h)  
  | where Service  <> "AX" 
  | where Plugin has "Analytics"  
  | sort by PreciseTimeStamp desc 
  | join kind=leftouter TeamFoundationJobResult on Result 
  | extend ExecutionTimeSpan = totimespan(ExecutionTime) 
  | project JobName, StartTime, ExecutionTimeSpan, ResultString, ResultMessage  
```





[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0309_2016-->