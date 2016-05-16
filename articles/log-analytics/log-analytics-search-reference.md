<properties
	pageTitle="Log Analytics 검색 참조 | Microsoft Azure"
	description="Log Analytics 검색 참조는 검색 언어에 대해 설명하며 데이터를 검색하고 검색 결과를 좁히기 위한 식을 필터링할 때 사용할 수 있는 일반 쿼리 구문 옵션을 제공합니다."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>


# Log Analytics 검색 참조

검색 언어에 대한 다음의 참조 섹션은 데이터및 필터링 식을 검색 할 때 사용할 수 있는 일반 쿼리 구문 옵션을 설명하여 검색 범위를 좁힐 수 있도록 합니다. 검색된 데이터에 조치로 사용할 수 있는 명령을 설명합니다.

[필드 및 패싯 참조 검색 섹션](#search-field-and-facet-reference)에서 데이터와 유사한 범주를 dill-into하는 검색 및 패싯을 반환하는 필드에 대해 알 수 있습니다.

## 일반 쿼리 구문

구문

```
filterExpression | command1 | command2 …
```

필터 식(`filterExpression`)은 쿼리에 "where" 조건을 정의합니다. 명령은 쿼리에 의해 반환된 결과에 적용됩니다. 여러 명령은 세로줄 문자(|)로 구분해야 합니다.

### 일반 구문 예제

예제:

```
system
```

이 쿼리는 전체 텍스트 또는 검색 조건에 인덱싱된 모든 필드에서 단어 "시스템"을 포함하는 결과를 반환합니다.

>[AZURE.NOTE] 모든 필드가 이러한 방식으로 인덱싱되지 않지만 가장 일반적으로 텍스트 필드(예: 설명 및 이름)는 일반적으로 그렇습니다.

```
system error
```

이 쿼리는 *시스템*과 *오류* 단어를 포함하는 결과를 반환합니다.

```
system error | sort ManagementGroupName, TimeGenerated desc | top 10
```

이 쿼리는 *시스템*과 *오류* 단어를 포함하는 결과를 반환합니다. *ManagementGroupName* 필드(오름차순)의 결과를 정렬한 다음 *TimeGenerated*(내림차순)의 결과를 정렬합니다. 처음 10개의 결과만 표시합니다.

>[AZURE.IMPORTANT] 모든 필드 이름과 문자열 및 텍스트 필드에 대한 값은 대/소문자를 구분합니다.

## 필터 식

다음 하위 섹션에서 필터 식을 설명합니다.

### 문자열 리터럴

리터럴 문자열은 키워드 또는 미리 정의된 데이터 형식(예: 숫자 또는 날짜)으로 파서에서 인식되지 않은 모든 문자열입니다.

예제:

```
These all are string literals
```

이 쿼리는 발생하는 5개 단어를 모두 포함하는 결과를 검색합니다. 복잡한 문자열 검색을 수행하려면 다음과 같이 문자열 리터럴을 큰따옴표로 묶습니다.

```
" Windows Server"
```

"Windows Server"와 정확히 일치하는 결과를 반환합니다.

### 숫자

파서는 숫자 필드에 10 진수 정수 및 부동 소수점 수 구문을 지원합니다.

예제:

```
Type:Perf 0.5
```

```
HTTP 500
```

### 날짜/시간

시스템에서 데이터의 모든 부분에 원래 날짜와 레코드의 시간을 나타내는 *TimeGenerated* 속성이 있습니다. 또한 일부 데이터 형식에 더 많은 날짜/시간 필드가 있을 수 있습니다.(예를 들어 *LastModified*)

Log Analytics에서 타임라인 차트/시간 선택기는 *TimeGenerated* 필드를 기준으로 시간별 (현재 실행되는 쿼리에 따라) 결과 분포를 보여 줍니다. 날짜/시간 필드는 쿼리에 사용할 수 있는 특정 문자열 형식이 있어서 특정 기간에 쿼리를 제한합니다. 또한 상대적 시간 간격을 참조하도록 구문을 사용할 수 있습니다.(예를 들어 "3일 전 및 2시간 전")

구문

```
yyyy-mm-ddThh:mm:ss.dddZ
```

```
yyyy-mm-ddThh:mm:ss.ddd
```

```
yyyy-mm-ddThh:mm:ss
```

```
yyyy-mm-ddThh:mm:ss
```

```
yyyy-mm-ddThh:mm
```

```
yyyy-mm-dd
```


예제:

```
TimeGenerated:2013-10-01T12:20
```

이전 명령은 정확하게 2013년 10월 1일 12시 20분이라는 *TimeGenerated* 값으로 레코드를 반환합니다. 어떤 결과를 제공할 가능성이 있지만 개념은 이해했을 것입니다.

또한 파서가 mnemonic 날짜/시간 값을 지원합니다.


다시 데이터가 빠른 시스템을 통과하지 않기 때문에 모든 결과를 생성할 가능성이 적습니다.

이러한 예제는 상대 및 절대 날짜에 사용할 구성 요소입니다. 다음 세 하위 섹션에서 상대 날짜 범위를 사용 하는 예제를 고급 필터에서 사용하는 방법에 대해 설명합니다.

### 날짜/시간 수학

간단한 날짜/시간 계산을 사용하여 날짜/시간 수학 연산자로 날짜/시간 값을 오프셋 또는 반올림합니다.

구문

```
datetime/unit
```

```
datetime[+|-]count unit
```

|연산자|설명|
|---|---|
|/|날짜/시간을 지정된 단위로 반올림합니다. 예를 들어 NOW/DAY는 현재 날짜의 자정으로 현재 날짜/시간을 반올림합니다.|
|+ 또는-|단위의 지정한 수 만큼 날짜/시간을 오프셋합니다 예:NOW+1HOUR는 현재 날짜/시간을 한 시간 앞으로 오프셋합니다. 2013-10-01T12:00-10DAYS는 날짜 값을 뒤로 10일 오프셋합니다.|



다음과 같이 날짜/시간 산술 연산자를 함께 연결할 수 있습니다.

```
NOW+1HOUR-10MONTHS/MINUTE
```

다음 테이블에서 지원되는 날짜/시간 단위를 나열합니다.

날짜/시간 단위|설명
---|---
YEAR, YEARS|현재 연도를 반올림하거나 지정된 년 수 만큼 오프셋합니다.
MONTH, MONTHS|현재 월을 반올림하거나 지정된 개월 수 만큼 오프셋합니다.
DAY, DAYS, DATE|현재 날을 반올림하거나 지정된 일 수 만큼 오프셋합니다.
HOUR, HOURS|현재 시간을 반올림하거나 지정된 시간 수 만큼 오프셋합니다.
MINUTE, MINUTES|현재 분을 반올림하거나 지정된 분 수 만큼 오프셋합니다.
SECOND, SECONDS|현재 초를 반올림하거나 지정된 초 수 만큼 오프셋합니다.
MILLISECOND, MILLISECONDS, MILLI, MILLIS|현재 밀리초를 반올림하거나 지정된 밀리초 수 만큼 오프셋합니다.


### 필드 패싯

패싯 필드를 사용하여 인덱스 전체에서 다양한 용어에 "자유 텍스트" 쿼리를 작성하는 대신 특정 필드 및 정확한 값에 대한 검색 조건을 지정할 수 있습니다. 이 구문은 이전 단락에서 몇가지 예제에 이미 사용했습니다. 여기에서 더 복잡한 예제를 제공합니다.

**구문**

```
field:value
```

```
field=value
```

**설명**

특정 값에 대한 필드를 검색합니다. 값은 문자열 리터럴, 숫자 또는 날짜/시간일 수 있습니다.

예제:

```
TimeGenerated:NOW
```

```
ObjectDisplayName:"server01.contoso.com"
```

```
SampleValue:0.3
```

**구문**

*field>value*

*field<value*

*field>=value*

*field<=value*

*field!=value*

**설명**

비교를 제공합니다.

예제:

```
TimeGenerated>NOW+2HOURS
```

**구문**

```
field:[from..to]
```

**설명**

패싯 범위를 제공합니다.

예제:

```
TimeGenerated:[NOW..NOW+1DAY]
```

```
SampleValue:[0..2]
```

### 논리 연산자

쿼리 언어는 논리 연산자(*AND*, *OR*, *NOT*)와 각각의 C-스타일 별칭(*&&*, *||*, *!*)을 지원합니다. 이러한 연산자를 그룹화하려면 괄호를 사용할 수 있습니다.

예제:

```
system OR error

```

```
Type:Alert AND NOT(Severity:1 OR ObjectId:"8066bbc0-9ec8-ca83-1edc-6f30d4779bcb8066bbc0-9ec8-ca83-1edc-6f30d4779bcb")
```

최상위 필터 인수에서 논리 연산자를 생략할 수 있습니다. 이 경우 AND 연산자를 가정합니다.


필터 식|Equivalent to
---|---
시스템 오류|시스템 AND 오류
시스템 "Windows Server" OR 심각도:1|시스템 AND ("Windows Server" OR 심각도:1)

### 와일드 카드 사용

쿼리 언어는 쿼리의 값에 대해 하나 이상의 문자를 나타내는 (**) 문자 사용을 지원합니다.

예제:

 "Redmond-SQL"과 같이 이름에 "SQL"이 포함된 모든 컴퓨터를 검색합니다.

```
Type=Event Computer=*SQL*
```

>[AZURE.NOTE] 와일드 카드는 현재 따옴표 안에서 사용할 수 없습니다. 메시지=`"*This text*"`는 사용된 (*)를 리터럴 (*) 문자로 간주합니다.

## 명령

명령은 쿼리에 의해 반환된 결과에 적용됩니다. 파이프 문자(|)를 사용하여 검색된 결과에 명령을 적용합니다. 여러 명령은 세로줄 문자로 구분해야 합니다.

>[AZURE.NOTE] 명령 이름은 필드 이름 및 데이터와는 달리 대문자 또는 소문자로 작성할 수 있습니다.

### 정렬

구문

	sort field1 asc|desc, field2 asc|desc, …

특정 필드에 의한 결과를 정렬합니다. asc/desc 접두사는 선택 사항입니다. 생략된 경우에는 *asc* 정렬 순서를 가정합니다. 쿼리가 *정렬* 명령을 명시적으로 사용하지 않는 경우 정렬 **TimeGenerated** desc는 기본 동작이고 가장 최근의 결과를 항상 먼저 반환합니다.

### 위쪽/제한

구문

	top number


	limit number
상위 N개 결과에 대한 응답을 제한합니다.

예제:

	Type:Alert errors detected | top 10

상위 10개의 일치하는 결과를 반환합니다.

### Skip

구문

	skip number

나열된 결과의 수를 건너뜁니다.

예제:

	Type:Alert errors detected | top 10 | skip 200

200개 결과에서 시작하여 상위 10개의 일치하는 결과를 반환합니다.

### 여기서

구문

	select field1, field2, ...

선택한 필드에 대한 결과를 제한합니다.

예제:

	Type:Alert errors detected | select Name, Severity

반환되는 결과 필드를 *이름** 및 *심각도*로 제한합니다.

### 측정값

*측정값* 명령은 통계 함수를 원시 검색 결과에 적용하는 데 사용됩니다. 이 명령은 데이터에서 *group-by* 보기를 가져오는 데 매우 유용합니다. *measure* 명령을 사용하는 경우 Log Analytics 검색은 집계된 결과가 포함된 테이블을 표시합니다.

구문

	 measure aggregateFunction([aggregatedField]) [as fieldAlias] by groupField [interval interval]

	 measure aggregateFunction1([aggregatedField]) [as fieldAlias1] , aggregateFunction2([aggregatedField]) [as fieldAlias2] by groupField [interval interval]

	 measure aggregateFunction([aggregatedField])  interval interval

	 measure aggregateFunction1([aggregatedField]), aggregateFunction2([aggregatedField]), ...  interval interval


*groupField*로 결과를 집계하고 *aggregatedField*를 사용하여 집계된 측정값을 계산합니다.


|측정 통계 함수|설명|
|---|---|
|*aggregateFunction*|집계 함수의 이름(대소문자 구분). 다음 집계 함수가 지원됩니다. OUNT MAX MIN SUM AVG STDDEV COUNTDISTINCT PERCENTILE## 또는 PCT##(##은 1 ~ 99 사이의 숫자)|
|*aggregatedField*|집계되고 있는 필드. 이 필드는 COUNT 집계 함수에 대해 선택적이지만 SUM, MAX, MIN, AVG STDDEV, PERCENTILE## 또는 PCT##(##은 1 ~ 99 사이의 숫자)의 기존 숫자 필드여야 합니다.|
|*fieldAlias*|(선택 사항) 계산된 집계 값의 별칭. 지정하지 않을 경우 필드 이름은 AggregatedValue가 됩니다.|
|*groupField*|결과 집합을 그룹화하는 기준이 되는 필드의 이름|
|*간격*|**nnnNAME** 형식의 시간 간격. 여기서 nnn은 양의 정수입니다. **NAME**은 간격 이름입니다. 지원되는 간격 이름은(대소문자 구분) MILLISECOND[S] SECOND[S] MINUTE[S] HOUR[S] DAY[S] MONTH[S] YEAR[S]입니다.|


날짜/시간 그룹 필드에서만 간격 옵션을 사용할 수 있습니다(예: *TimeGenerated* 및 *TimeCreated*). 현재 서비스에 의해 적용되지 않지만 백 엔드로 전달되는 날짜/시간 없는 필드에 런타임 오류가 발생합니다. 스키마 유효성 검사를 구현하는 경우 서비스 API는 집계 간격에 대해 날짜/시간 없이 필드를 사용하는 쿼리를 거부합니다. 현재 *Measure* 구현은 집계 함수를 그룹화하는 간격을 지원합니다.

BY 절을 생략하지만 간격을 지정하는 경우(두 번째 구문처럼) 기본적으로 *TimeGenerated* 필드를 가정합니다.

예제:

**예 1**

	Type:Alert | measure count() as Count by ObjectId

*설명*

*ObjectID*에서 경고를 그룹화하고 각 그룹에 대한 경고의 수를 계산합니다. 집계된 값을 *Count* 필드(별칭)로 반환합니다.

**예 2**

	Type:Alert | measure count() interval 1HOUR

*설명*

*TimeGenerated* 필드를 사용하여 1시간 간격으로 경고를 그룹화하고 각 간격의 경고 수를 반환합니다.

**예 3**

	Type:Alert | measure count() as AlertsPerHour interval 1HOUR

*설명*

이전 예제와 동일하지만 집계된 필드 별칭을 사용합니다.(*AlertsPerHour*)

**예제 4**

	* | measure count() by TimeCreated interval 5DAYS

*설명*

*TimeCreated* 필드를 사용하여 5일 간격으로 결과를 그룹화하고 각 간격의 결과 수를 반환합니다.

**예제 5**

	Type:Alert | measure max(Severity) by WorkflowName

*설명*

워크로드 이름 별로 경고를 그룹화하고 각 워크플로에 대한 최대 경고 심각도 값을 반환합니다.

**예제 6**

	Type:Alert | measure min(Severity) by WorkflowName

*설명*

이전 예제와 동일하지만 *Min* 집계 함수를 사용합니다.

**예제 7**

	Type:Perf | measure avg(CounterValue) by Computer

*설명*

컴퓨터별로 성능을 그룹화하고 평균을 계산합니다.

**예제 8**

	Type:Perf | measure sum(CounterValue) by Computer

*설명*

이전 예제와 동일하지만 *합계*를 사용합니다.

**예제 9**

	Type:Perf | measure stddev(CounterValue) by Computer

*설명*

이전 예제와 동일하지만 *STDDEV*를 사용합니다.

**예제 10**

	Type:Perf | measure percentile70(CounterValue) by Computer

*설명*

이전 예제와 동일하지만 *PERCENTILE70*을 사용합니다.

**예제 11**

	Type:Perf | measure pct70(CounterValue) by Computer

*설명*

이전 예제와 동일하지만 *PCT70*을 사용합니다. *PCT##*은 *PERCENTILE##* 함수의 유일한 별칭입니다.

**예제 12**

	Type:Alert | measure count() as Count by WorkflowName | sort Count desc | top 5

*설명*

경고의 최대 수와 상위 5개 워크플로를 가져옵니다.

**예제 13**

	* | measure countdistinct(Computer) by Type

*설명*

각 유형에 대해 보고하는 고유 컴퓨터의 수를 계산합니다.

**예제 14**

	* | measure countdistinct(Computer) Interval 1HOUR

*설명*

매 시간별로 보고하는 고유 컴퓨터의 수를 계산합니다.

**예제 15**

```
Type:Perf CounterName=”% Processor Time” InstanceName=”_Total” | measure avg(CounterValue) by Computer Interval 1HOUR
```

*설명*

컴퓨터별로 % Processor Time을 그룹화하고 1시간당 평균을 반환합니다.

**예제 16**

	Type:W3CIISLog | measure max(TimeTaken) by csMethod Interval 5MINUTES

*설명*

W3CIISLog를 메서드별로 그룹화하고 5분마다 최대값을 반환합니다.

**예제 17**

```
Type:Perf CounterName=”% Processor Time” InstanceName=”_Total”  | measure min(CounterValue) as MIN, avg(CounterValue) as AVG, percentile75(CounterValue) as PCT75, max(CounterValue) as MAX by Computer Interval 1HOUR
```

*설명*

컴퓨터별로 % Processor Time을 그룹화하고 1시간당 최소값, 평균, 75백분위수, 최대값을 반환합니다.

### Where

구문

```
**where** AggregatedValue>20
```

*측정값* 명령 뒤에 사용하여 *측정값* 집계 함수가 만든 집계된 결과를 자세히 필터링할 수 있습니다.

예제:

	Type:Perf CounterName:"% Total Run Time" | Measure max(CounterValue) as MAXCPU by Computer

	Type:Perf CounterName:"% Total Run Time" | Measure max(CounterValue) by Computer | where (AggregatedValue>50 and AggregatedValue<90)

### IN

구문

```
(Outer Query) (Field to use with inner query results) IN {Inner query | measure count() by (Field to send to outer query)} (rest  of outer query)  
```

설명: 이 구문에서는 집계를 만들고 해당 집계의 값 목록을 해당 값과 일치하는 이벤트를 검색하는 다른 외부(기본) 검색으로 가져옵니다. 그렇게 하려면 내부 검색을 괄호로 묶고 그 결과를 IN 연산자를 사용하는 외부 검색 필드에 가능한 값으로 제공합니다.

내부 쿼리 예제: 다음 집계 쿼리에서 *현재 보안 업데이트가 없는 컴퓨터*

```
Type:Update Classification="Security Updates"  UpdateState=needed TimeGenerated>NOW-25HOURS | measure count() by Computer
```    

*현재 보안 업데이트가 없는 컴퓨터의 모든 Windows 이벤트*를 검색하는 최종 쿼리는 다음과 유사합니다.

```
Type=Event Computer IN {Type:Update Classification="Security Updates"  UpdateState=needed TimeGenerated>NOW-25HOURS | measure count() by Computer}
```

### Dedup

**구문**

	Dedup FieldName

**설명** 특정 필드의 모든 고유 값에 대해 검색된 첫 번째 문서를 반환합니다.

**예제**

	Type=Event | sort TimeGenerated DESC | Dedup EventID

위 예제는 EventID당 하나의 이벤트를 반환합니다(TimeGenerated에서 DESC를 사용하는 이후 최근 이벤트).


### Extend

**설명** 쿼리에 런타임 필드를 만들 수 있습니다.

**예 1**

	Type=SQLAssessmentRecommendation | Extend product(RecommendationScore, RecommendationWeight) AS RecommendationWeightedScore
SQL 평가 권장 사항에 대해 가중 평균 권장 점수를 표시합니다.

**예 2**

	Type=Perf CounterName="Private Bytes" | EXTEND div(CounterValue,1024) AS KBs | Select CounterValue,Computer,KBs
바이트가 아닌 KB 단위로 카운터 값을 표시합니다.

**예 3**

	Type=WireData | EXTEND scale(TotalBytes,0,100) AS ScaledTotalBytes | Select ScaledTotalBytes,TotalBytes | SORT TotalBytes DESC
모든 결과가 0 ~ 100이 되도록 WireData TotalBytes 값을 조정합니다.

**예제 4**

	Type=Perf CounterName="% Processor Time" | EXTEND if(map(CounterValue,0,50,0,1),"HIGH","LOW") as UTILIZATION
50% 미만이 성능 카운터 값을 LOW로 태깅하고 나머지는 HIGH로 태깅합니다.

**지원되는 함수**


|함수 |설명 |구문 예제|  
|---------|---------|---------|
|abs | 지정된 값 또는 함수의 절대 값을 반환합니다.|<p>abs(x)</p> <p>abs(-5)</p>|
|and | 모든 피연산자가 true로 계산될 경우에만 true 값을 반환합니다. | and(not(exists(**popularity**)),exists(**price**)):|
|def | def는 기본값(default)의 약어입니다. 필드 "필드" 값을 반환하고 필드가 없을 경우에는 지정된 기본 값을 반환하며 exists()==true일 때 첫 번째 값을 산출합니다. |<p>div(1,y)</p><p>div(sum(x,100),max(y,1))</p>|
| div | div(x,y)는 x를 y로 나눕니다. | div(1,y),div(sum(x,100),max(y,1)) |
| dist | n차원 공간의 두 벡터(점) 사이의 거리를 반환합니다. 거듭제곱과 두 개 이상의 ValueSource 인스턴스를 가져와 두 벡터 간 거리를 계산합니다. 각 ValueSource는 숫자여야 합니다. 전달하는 ValueSource 인스턴스 수는 짝수여야 하며 메서드는 처음 절반이 첫 번째 벡터를 나타내고 나머지 절반이 두 번째 벡터를 나타내는 것으로 가정합니다. | <p>dist(2, x, y, 0, 0): 각 문서에 대해 (0,0)과 (x,y) 사이의 유클리드 거리를 계산합니다.</p><p>dist(1, x, y, 0, 0): 각 문서에 대해 (0,0)과 (x,y) 사이의 맨해턴 거리를 계산합니다.</p><p>dist(2,,x,y,z,0,0,0): 각 문서에 대해 (0,0,0)과 (x,y,z) 사이의 유클리드 거리를 계산합니다.</p><p>dist(1,x,y,z,e,f,g): (x,y,z)와 (e,f,g) 사이의 맨해턴 거리이며 각 문자는 필드 이름입니다.</p> |
| exists | 필드에 구성원이 있을 경우 TRUE를 반환합니다. | <p>exists(author)는 문서의 "author" 필드에 값이 있을 경우 TRUE를 반환합니다.</p><p>exists(query(price:5.00))는 "price"가 "5.00"과 일치할 경우 TRUE를 반환합니다.</p> |
| hsin | Haversine 거리는 구를 이동할 때 구상의 두 점 간 거리를 계산합니다. 값은 라디안이어야 합니다. hsin도 부울 인수를 사용하여 함수가 출력을 라디안으로 변환해야 하는지 여부를 지정합니다. | hsin(2, true, x, y, 0, 0) |
| if | 조건부 함수 쿼리를 사용합니다. In if(test,value1,value2): 테스트가 논리적 값(TRUE 또는 FALSE)을 반환하는 논리적 값 또는 식이거나 논리적 값 또는 식을 나타냅니다. value1은 테스트가 TRUE를 산출할 때 함수에서 반환하는 값입니다. value2는 테스트에서 FALSE를 산출할 때 함수에서 반환하는 값입니다. 식은 부울 값을 출력하는 함수 또는 숫자 값을 반환하는 함수도 가능하며, 이 경우 0은 false 또는 문자열(빈 문자열은 false로 해석됨)로 해석됩니다. | if(termfreq(cat,'electronics'),popularity,42) : 이 함수는 각 문서의 cat 필드에 "electronics"라는 단어가 포함되었는지 여부를 검사합니다. 이 단어가 포함된 경우 인기도 필드의 값이 반환되며 그렇지 않을 경우 42 값이 반환됩니다. |
| linear | m*x+c를 구현하며, 여기서 m과 c는 상수이고 x는 임의의 함수입니다. 이 함수는 sum(product(m,x),c)과 동일하지만 단일 함수로 구현되므로 약간 더 효율적입니다. | linear(x,m,c) linear(x,2,4)는 2*x+4</font?를 반환합니다. |
| 로그 | 지정된 함수의 로그 밑 10을 반환합니다. | log(x) log(sum(x,100)) |
| map | min(포함) 및 max(포함)에 포함되는 입력 함수 x의 모든 값을 지정된 대상에 매핑합니다. 인수 min 및 max는 상수여야 합니다. 인수 대상과 기본값은 상수 또는 함수가 가능합니다. x 값이 min 및 max 사이에 포함되지 않는 경우 x 값이 반환되며 5번째 인수로 지정된 경우는 기본값이 반환됩니다. | <p>map(x,min,max,target) map(x,0,0,1) - 모든 0 값을 1로 바꿉니다. 이 함수는 기본값 0을 처리하는 데 유용할 수 있습니다.</p> <p> map(x,min,max,target,default) map(x,0,100,1,-1) - 0 ~ 100 사이의 모든 값을 1로 바꾸고 나머지 값을 -1로 바꿉니다.</p> </p> map(x,0,100,sum(x,599),docfreq(text,solr)) - 0 ~ 100 사이의 모든 값을 x+599로 바꾸고 나머지 값을 필드 텍스트의 'solr' 용어의 빈도로 바꿉니다.</p> |
| max | 인수: max(x,y,...)로 지정되어 여러 번 중첩된 함수 또는 상수의 최대 숫자 값을 반환합니다. max 함수는 지정된 상수에서 다른 함수 또는 필드를 "하한값으로 유지"하는 데에도 유용할 수 있습니다. (단일 다중값 필드의 최대값을 선택할 경우 field(myfield,max) 구문 사용) | max(myfield,myotherfield,0) |
| min | 인수 max(x,y,...)로 지정되어 여러 번 중첩된 함수 또는 상수의 최소 숫자 값을 반환합니다. min 함수는 상수를 사용하는 함수에서 "상한"을 제공하는 데에도 유용할 수 있습니다. (단일 다중값 필드의 최소값을 선택할 경우 field(myfield,min) 구문 사용) | min(myfield,myotherfield,0) |
| ms | 인수간 차이를 밀리초 단위로 반환합니다. 날짜는 Unix 또는 POSIX time epoch인 1970년 1월 1일 자정(UTC) 기준입니다. 인수는 상수 날짜 또는 NOW 기준의 인덱싱된 TrieDateField 또는 날짜 연산의 이름이 가능합니다. · ms(): epoch 이후 밀리초인 ms(NOW)와 동일합니다. · ms(a): 인수가 나타내는 epoch 이후의 밀리초를 반환합니다. · ms(a,b) : a 이전에 b가 발생한 밀리초를 반환합니다(즉, a - b). | <p> </p><p> ms(NOW/DAY) </p> <p> ms(2000-01-01T00:00:00Z)</p> <p> ms(mydatefield)</p> <p> ms(NOW,mydatefield)</p> <p> ms(mydatefield,2000-01-01T00:00:00Z)</p> <p> ms(datefield1,datefield2)</p> |
| not | 래핑된 함수의 논리적 부정 값입니다. | not(exists(author)): exists(author)가 false일 경우에만 TRUE입니다. |
| 또는 | 논리적 분리. | or(value1,value2): value1 또는 value2가 true일 경우에만 TRUE입니다. |
| pow | 지정된 밑에 지정된 값으로 거듭제곱합니다. pow(x,y): x를 y 거듭제곱합니다. | <p> pow(x,y)</p> <p>pow(x,log(y))</p> <p> pow(x,0.5)</p>: sqrt와 동일 |
| product | 쉼표로 구분된 목록으로 지정하는 여러 값 또는 함수의 곱을 반환합니다. mul(...)도 이 함수의 별칭으로 사용될 수 있습니다. | <p> product(x,y,...)</p> <p>product(x,2)</p> <p> product(x,y)</p> <p>mul(x,y)</p> |
| recip | `<a/(m*x+b)`를 구현하는 `recip(x,m,a,b)`를 사용하는 역수를 수행합니다. 여기서 m, a, b는 상수이며 x는 임의의 복잡 함수입니다. a와 b가 동일하고 x>=0일 경우 이 함수의 최대값 1은 x가 증가함에 따라 감소합니다. a와 b 값을 함께 증가할 경우 전체 함수가 곡선의 평평한 부분으로 이동합니다. 이러한 속성으로 인해 x가 rord(datefield)일 때 더 많은 최근 문서를 출력하는 데 이상적인 함수가 됩니다. | <p>recip(myfield,m,a,b)<p> <p> recip(rord(creationDate),1,1000,1000)</p> |
| 크기 조정 | 함수 x 값이 지정된 minTarget(포함) 및 maxTarget(포함) 안에 포함되도록 조정합니다. 현재 구현은 올바른 배율을 선택할 수 있도록 모든 함수 값을 확인하여 min 및 max를 구합니다. 현재 구현은 문서가 삭제된 시기 또는 값이 없는 문서를 구분할 수 없습니다. 그러한 경우는 0.0 값을 사용합니다. 즉, 일반적으로 모든 값이 0.0보다 클 경우 매핑을 시작하는 최소값이 0.0이 될 수 있습니다. 그럴 경우에는 해결 방법으로 적절한 map() 함수를 사용하여 0.0을 scale(map(x,0,0,5),1,2)와 같은 실제 범위 내의 값으로 바꿀 수 있습니다. | <p>scale(x,minTarget,maxTarget)</p> <p>scale(x,1,2): 모든 값이 1(포함) ~ 2(포함) 사이가 되도록 x 값을 조정합니다. </p> |
| sqedist | 유클리드 제곱 거리는 2개의 표준(유클리드 거리)을 계산하지만 제곱근을 계산하지 않아 매우 많은 작업을 절감합니다. 유클리드 거리를 계산하는 응용 프로그램은 실제 거리가 필요하지 않은 경우가 많지만 거리의 제곱을 사용할 수 있습니다. 전달하는 ValueSource 인스턴스 수는 짝수여야 하며 메서드는 처음 절반이 첫 번째 벡터를 나타내고 나머지 절반이 두 번째 벡터를 나타내는 것으로 가정합니다. | sqedist(x\_td, y\_td, 0, 0) |
| sqrt | 지정된 값 도는 함수의 제곱근을 반환합니다. | <p>sqrt(x)</p><p>sqrt(100)</p><p>sqrt(sum(x,100))</p> |
| strdist | 두 문자열 간의 거리를 계산합니다. Lucene 맞춤법 검사기 StringDistance 인터페이스를 사용하며 해당 패키지에서 사용 가능한 모든 구현을 지원하고 응용 프로그램에서 Solr의 리소스 로딩 기능을 통해 자체 구현을 플러그인할 수 있습니다. strdist는 인수로 string1, string2, 거리 측정값을 사용합니다. 거리 측정에 가능한 값은 jw: Jaro-Winkler edit: Levenstein 또는 Edit distance ngram입니다(NGramDistance를 지정할 경우 ngram 크기도 전달할 수 있음). 기본값은 2입니다. FQN: StringDistance 인터페이스 구현의 정규화된 클래스 이름. 인수가 없는 생성자여야 합니다. | strdist("SOLR",id,edit) |
| sub | sub(x,y)에서 x-y를 반환합니다. | <p>sub(myfield,myfield2)</p> <p> sub(100,sqrt(myfield))</p> |
| sum | 쉼표로 구분된 목록으로 지정하는 여러 값 또는 함수의 합을 반환합니다. add(...)도 이 함수의 별칭으로 사용될 수 있습니다. | <p>sum(x,y,...)</p> <p>sum(x,1)</p> <p> sum(x,y)</p> <p> sum(sqrt(x),log(y),z,0.5)</p> <p> add(x,y)</p> |
| xor | 둘 중 하나가 해당하며 둘 다는 해당하지 않습니다. | xor(field1,field2): field1 또는 field2가 ture일 경우 TRUE를 반환하며 둘 다 true일 경우 FALSE를 반환합니다. |



## 검색 필드 및 패싯 참조

로그 검색을 사용하여 데이터를 찾을 때 결과에 다양한 필드와 패싯이 표시됩니다. 그러나 표시하는 정보 중 일부는 매우 자세히 나타나지 않을 수 있습니다. 다음 정보를 사용하여 결과를 이해할 수 있습니다.



|필드|검색 유형|설명|
|---|---|---|
|TenantId|모두|데이터를 분할하는 데 사용됨|
|TimeGenerated|모두|타임라인을 구동하는 데 사용되는 timeselectors입니다.(검색 및 다른 화면에서) 데이터의 일부가 생성된 때를 나타냅니다.(일반적으로 에이전트에서) ISO 형식으로 표현되는 시간이며 항상 UTC입니다. 기존 계측을 기반으로 하는 '형식'의 경우 (즉, 로그의 이벤트) 일반적으로 실시간으로 로그 항목/선/레코드를 기록합니다. 관리 팩을 통해 또는 클라우드에서 생성되는 일부 다른 유형(즉, 권장 사항/경고/updateagent/등)은 일종의 구성 스냅숏을 사용하여 새 데이터 조각이 수집되거나 그에 기반하여 권장 사항/경고를 발생시키는 시간입니다.|
|EventID|이벤트|Windows 이벤트 로그에서 EventID|
|EventLog|이벤트|Windows에서 이벤트를 로그한 이벤트 로그|
|EventLevelName|이벤트|중요 / 경고 / 정보 / 성공|
|EventLevel|이벤트|중요 / 경고 / 정보 / 성공에 대한 숫자 값(보다 쉽게/보다 읽기 쉬운 쿼리 대신 EventLevelName 사용)|
|SourceSystem|모두|데이터가 제공된 위치(서비스의 '연결' 관점에서 - 즉, Operations Manager, OMS(=데이터가 클라우드에서 생성됨), Azure 저장소 (WAD에서 가져온 데이터) 등)|
|ObjectName|PerfHourly|Windows 성능 개체 이름|
|InstanceName|PerfHourly|Windows 성능 카운터 인스턴스 이름|
|CounteName|PerfHourly|Windows 성능 카운터 이름|
|ObjectDisplayName|PerfHourly, ConfigurationAlert, ConfigurationObject, ConfigurationObjectProperty|Operations Manager에서 성능 수집 규칙이 대상으로 하는 개체 또는 Operational Insights에서 발견된 개체 또는 경고가 생성된 데에 반하는 표시 이름|
|RootObjectName|PerfHourly, ConfigurationAlert, ConfigurationObject, ConfigurationObjectProperty|Operations Manager에서 성능 수집 규칙이 대상으로 하는 개체 또는 Operational Insights에서 발견된 개체 또는 경고가 생성된 데에 반하는 부모의 표시 이름(이중 호스팅 관계 즉, Windows 컴퓨터에서 호스팅하는 SqlInstance에서 호스팅하는 SqlDatabase)|
|컴퓨터|대부분의 형식|데이터가 속하는 컴퓨터 이름|
|DeviceName|ProtectionStatus|데이터가 속한 컴퓨터 이름('Computer'와 동일)|
|DetectionId|ProtectionStatus| |
|ThreatStatusRank|ProtectionStatus|위협 상태 순위는 위협 상태에 대한 숫자 표현이며 HTTP 응답 코드와 유사합니다. 숫자 사이의 간격을 남겨두었으므로(100 또는 0이 아닌 150은 위협이 아닌 이유) 새 상태를 추가할 공간이 있습니다. 위협 상태 및 보호 상태를 롤업하는 경우 선택된 기간 동안 컴퓨터에 있었던 최악의 상태를 표시하려고 합니다. 숫자를 사용하여 다양한 상태 순위를 지정하므로 가장 높은 번호와 레코드를 찾을 수 있습니다.|
|ThreatStatus|ProtectionStatus|Description of ThreatStatus, ThreatStatusRank와 1:1로 매핑|
|TypeofProtection|ProtectionStatus|컴퓨터에서 검색된 맬웨어 방지 제품은 없음, Microsoft 맬웨어 제거 도구, Forefront, 등입니다.|
|ScanDate|ProtectionStatus| |
|SourceHealthServiceId|ProtectionStatus, RequiredUpdate|이 컴퓨터의 에이전트에 대한 HealthService ID|
|HealthServiceId|대부분의 형식|이 컴퓨터의 에이전트에 대한 HealthService ID|
|ManagementGroupName|대부분의 형식|Operations Manager에 연결된 에이전트용 관리 그룹 이름입니다. 그렇지 않은 경우 null/blank가 됩니다.|
|ObjectType|ConfigurationObject|Log Analytics 구성 평가에서 발견한 개체의 유형(Operations Manager 관리 팩의 'type'/클래스와 동일)|
|UpdateTitle|RequiredUpdate|설치되지 않고 발견된 업데이트의 이름|
|PublishDate|RequiredUpdate|Microsoft update에 게시된 업데이트는 언제입니까?|
|서버|RequiredUpdate|데이터가 속한 컴퓨터 이름('Computer'와 동일)|
|제품|RequiredUpdate|업데이트가 적용되는 제품|
|UpdateClassification|RequiredUpdate|업데이트의 유형(업데이트 롤업, 서비스 팩 등)|
|KBID|RequiredUpdate|모범 사례 또는 업데이트를 설명하는 KB 문서 ID|
|WorkflowName|ConfigurationAlert|경고를 생성하는 모니터 또는 규칙의 이름|
|심각도|ConfigurationAlert|경고의 심각도|
|우선 순위|ConfigurationAlert|경고의 우선 순위|
|IsMonitorAlert|ConfigurationAlert|이 경고는 모니터(true) 또는 규칙(false)에 의해 생성되었습니까?|
|AlertParameters|ConfigurationAlert|Log Analytics 경고의 매개 변수가 포함된 XML|
|Context|ConfigurationAlert|Log Analytics 경고의 '컨텍스트'가 포함된 XML|
|워크로드|ConfigurationAlert|경고를 참조하는 기술 또는 '워크로드'|
|AdvisorWorkload|권장 사항|권장 사항을 참조하는 기술 또는 '워크로드'|
|설명|ConfigurationAlert|경고 설명(간략)|
|DaysSinceLastUpdate|UpdateAgent|WSUS/Microsoft Update에서 이 에이전트가 업데이트를 설치한 날은 언제입니까?(이 레코드의 'TimeGenerated'과 관련)|
|DaysSinceLastUpdateBucket|UpdateAgent|DaysSinceLastUpdate에 따라 얼마나 경과했는지 '시간 버킷'에서 분류하는 것은 WSUS/Microsoft Update에서 마지막으로 업데이트를 설치한 컴퓨터였습니다.|
|AutomaticUpdateEnabled|UpdateAgent|자동 업데이트가 이 에이전트에서 활성화 또는 비활성화를 확인하고 있습니까?
|AutomaticUpdateValue|UpdateAgent|자동 업데이트가 자동으로 다운로드 및 설치, 다운로드만 또는 확인만 하도록 설정을 확인하고 있습니까?|
|WindowsUpdateAgentVersion|UpdateAgent|Microsoft 업데이트 에이전트의 버전 번호|
|WSUSServer|UpdateAgent|어떤 WSUS 서버가 이 업데이트 에이전트를 대상으로 합니까?|
|OSVersion|UpdateAgent|이 업데이트 에이전트에서 실행 중인 운영 체제의 버전|
|이름|Recommendation, ConfigurationObjectProperty|권장 사항의 이름/제목, 또는 Log Analytics 구성 평가에서 속성의 이름|
|값|ConfigurationObjectProperty|Log Analytics 구성 평가에서 속성의 값|
|KBLink|권장 사항|모범 사례 또는 업데이트를 설명하는 KB 문서의 URL|
|RecommendationStatus|권장 사항|권장 사항은 레코드가 '업데이트'를 얻는 몇가지 형식 사이에 있으며 검색 인덱스에 추가되지 않습니다. 이 상태는 권장 사항이 활성/미결 상태인지 또는 Log Analytics에서 해결되었음을 감지했는지 여부에 따라 변경됩니다.|
|RenderedDescription|이벤트|Windows 이벤트의 렌더링된 설명(채워진 매개 변수와 재사용된 텍스트)|
|ParameterXml|이벤트|Windows 이벤트(이벤트 뷰어처럼)의 '데이터' 섹션에서 매개 변수를 사용하는 XML|
|EventData|이벤트|Windows 이벤트(이벤트 뷰어처럼)의 전체 '데이터' 섹션을 사용하는 XML|
|원본|이벤트|이벤트를 생성하는 이벤트 로그 원본|
|EventCategory|이벤트|Windows 이벤트 로그에서 직접 이벤트의 범주|
|사용자 이름|이벤트|Windows 이벤트의 사용자 이름(일반적으로 NT AUTHORITY\\LOCALSYSTEM)|
|SampleValue|PerfHourly|성능 카운터의 시간별 집계에 대한 평균 값|
|최소|PerfHourly|성능 카운터 매시간 집계의 시간 간격의 최소값|
|최대|PerfHourly|성능 카운터 매시간 집계의 시간 간격의 최대값|
|Percentile95|PerfHourly|성능 카운터 매시간 집계의 시간 간격의 95번째 백분위 값|
|SampleCount|PerfHourly|매시간 집계 레코드를 생성하는데 사용된 '원시' 성능 카운터 샘플의 수|
|위협|ProtectionStatus|발견한 맬웨어의 이름|
|StorageAccount|W3CIISLog|로그를 읽은 azure 저장소 계정|
|AzureDeploymentID|W3CIISLog|로그가 속한 클라우드 서비스의 Azure 배포 ID|
|역할|W3CIISLog|로그가 속한 Azure 클라우드 서비스의 역할|
|RoleInstance|W3CIISLog|로그가 속한 Azure 역할의 RoleInstance|
|sSiteName|W3CIISLog|로그가 속한 IIS 웹 사이트(메타베이스 표기법);원래 로그에서 s-sitename 필드|
|sComputerName|W3CIISLog|원래 로그에서 s-computername 필드|
|sIP|W3CIISLog|HTTP 요청이 지정된 서버 IP 주소입니다. 원래 로그에서 s-ip 필드|
|csMethod|W3CIISLog|HTTP 요청에서 클라이언트가 사용한 HTTP 메서드(GET/POST/등)입니다. 원래 로그에서 cs-method|
|cIP|W3CIISLog|HTTP 요청이 발생한 클라이언트 IP 주소 원래 로그에서 c-ip 필드|
|csUserAgent|W3CIISLog|클라이언트가 선언한 HTTP 사용자 에이전트(브라우저 또는 그렇지 않은 경우) 원래 로그에서 cs-user-agent|
|scStatus|W3CIISLog|서버에서 클라이언트에 반환한 HTTP 상태 코드(200/403/500/등)입니다. 원래 로그에서 cs-status|
|TimeTaken|W3CIISLog|요청이 완료되는데 얼마나 걸립니까(밀리초). 원래 로그에서 timetaken 필드|
|csUriStem|W3CIISLog|요청된 상대 Uri( 호스트 주소 즉, '/검색'없이)입니다. 원래 로그에서 cs-uristem 필드|
|csUriQuery|W3CIISLog|URI 쿼리입니다. URI 쿼리는 ASP 페이지와 같은 동적 페이지에만 필요하므로 이 필드는 일반적으로 정적 페이지에 대한 하이픈을 포함합니다.|
|sPort|W3CIISLog|HTTP 요청이 전송된 서버 포트(선택되었기 때문에 IIS에서 수신함)|
|csUserName|W3CIISLog|인증된 사용자 이름(요청이 인증되고 익명이 아닌 경우)|
|csVersion|W3CIISLog|요청에 사용되는 HTTP 프로토콜 버전(즉, 'HTTP/1.1')|
|csCookie|W3CIISLog|쿠키 정보|
|csReferer|W3CIISLog|사용자가 마지막으로 방문한 사이트입니다. 이 사이트는 현재 사이트에 링크를 제공했습니다.|
|csHost|W3CIISLog|요청된 호스트 헤더(예:' www.mysite.com')|
|scSubStatus|W3CIISLog|하위 상태 오류 코드|
|scWin32Status|W3CIISLog|Windows 상태 코드|
|csBytes|W3CIISLog|클라이언트에서 서버로 요청에서 보낸 바이트|
|scBytes|W3CIISLog|서버에서 클라이언트로 응답을 다시 반환된 바이트|
|ConfigChangeType|ConfigurationChange|변경의 유형(WindowsServices / 소프트웨어 / 등)|
|ChangeCategory|ConfigurationChange|변경의 범주(수정 / 추가 / 제거됨)|
|SoftwareType|ConfigurationChange|소프트웨어의 종류 (업데이트 / 응용 프로그램)|
|SoftwareName|ConfigurationChange|소프트웨어의 이름(소프트웨어 변경에 적용)|
|게시자|ConfigurationChange|소프트웨어를 게시하는 공급 업체(소프트웨어 변경에 적용)|
|SvcChangeType|ConfigurationChange|Windows 서비스에 적용된 변경의 유형(상태 / StartupType / 경로 / ServiceAccount) - Windows 서비스 변경 사항에만 적용|
|SvcDisplayName|ConfigurationChange|변경된 서비스의 표시 이름입니다|
|SvcName|ConfigurationChange|변경된 서비스의 이름|
|SvcState|ConfigurationChange|서비스의 새 (현재) 상태입니다.|
|SvcPreviousState|ConfigurationChange|이전에 알려진 서비스의 상태(서비스 상태를 변경하는 경우에 해당)|
|SvcStartupType|ConfigurationChange|서비스 시작 유형|
|SvcPreviousStartupType|ConfigurationChange|이전 서비스 시작 유형(서비스 시작 유형을 변경하는 경우에 해당)|
|SvcAccount|ConfigurationChange|서비스 계정|
|SvcPreviousAccount|ConfigurationChange|이전 서비스 계정(서비스 계정이 변경되는 경우에 해당)|
|SvcPath|ConfigurationChange|Windows 서비스의 실행 파일 경로|
|SvcPreviousPath|ConfigurationChange|Windows 서비스에 대한 실행 파일의 이전 경로(이것이 변경된 경우에 해당)|
|SvcDescription|ConfigurationChange|서비스에 대한 설명|
|이전|ConfigurationChange|이 소프트웨어의 이전 상태(설치됨 / 설치되지 않음 / 이전 버전)|
|Current|ConfigurationChange|이 소프트웨어의 최근 상태(설치됨 / 설치되지 않음 / 현재 버전)|

## 다음 단계
로그 검색에 대한 자세한 내용은 다음을 참조하십시오.

- [로그 검색](log-analytics-log-searches.md) 방법을 숙지하여 솔루션에서 수집한 자세한 정보를 확인합니다.
- [Log Analytics의 사용자 지정 필드](log-analytics-custom-fields.md)를 사용하여 로그 검색을 확장합니다.

<!---HONumber=AcomDC_0504_2016-->