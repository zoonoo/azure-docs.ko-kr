<properties 
	pageTitle="Application Insights의 분석 연산자 및 쿼리" 
	description="Application Insights의 강력한 검색 도구인 분석에서 쿼리를 만드는 데 사용되는 연산자에 대한 참조입니다." 
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
	ms.date="03/21/2016" 
	ms.author="awills"/>

# 분석의 쿼리


[분석](app-analytics.md)을 사용하면 [Application Insights](app-insights-overview.md)에서 수집된 앱의 원격 분석을 통해 유용한 쿼리를 실행할 수 있습니다. 다음 페이지에서는 해당 쿼리 언어를 설명합니다.


[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

원격 분석에 대한 쿼리는 소스 스트림에 대한 참조 및 그 뒤의 필터 파이프라인으로 이루어져 있습니다. 예:


```AIQL
requests
| where client_City == "London" and timestamp > ago(3d)
| count
```
    
파이프 문자 `|`이 접두사로 붙는 각 필터는 몇몇 매개 변수를 가진 *연산자*의 인스턴스입니다. 연산자에 대한 입력은 앞에 붙는 파이프라인의 결과인 테이블입니다. 대부분의 경우 모든 매개 변수는 입력 열에 대한 [스칼라 식](app-analytics-scalars.md)입니다. 매개 변수가 입력 열의 이름인 경우도 있고 매개 변수가 두 번째 테이블인 경우도 있습니다. 테이블에 열 및 행이 한 개만 있더라도 쿼리의 결과는 언제나 테이블입니다.

쿼리에는 스칼라, 테이블 또는 쿼리 내에서 사용할 수 있는 함수를 정의하는 [let clauses](#let-clause)가 한 개 이상 접두사로 붙을 수 있습니다.

```AIQL

    let interval = 3d ;
    let city = "London" ;
    let req = (city:string) {
      requests
      | where client_City == city and timestamp > ago(interval) };
    req(city) | count
```

> 아래 쿼리 예제에서 `T`은 앞에 붙는 파이프라인 또는 소스 테이블을 나타내기 위해 사용됩니다.

## count 연산자

`count` 연산자는 입력된 레코드 집합의 레코드(행) 수를 반환합니다.

**구문**

    T | count

**인수**

* *T*: 레코드 수를 셀 테이블 형식 데이터입니다.

**반환**

이 함수는 형식 `long`의 단일 레코드 및 열을 가진 테이블을 반환합니다. 유일한 셀의 값은 *T*의 레코드 수입니다.

**예제**

```AIQL
requests | count
```



## extend 연산자

     T | extend duration = stopTime - startTime

하나 이상의 계산된 열을 테이블에 추가합니다.


**구문**

    T | extend ColumnName = Expression [, ...]

**인수**

* *T:* 입력 테이블입니다.
* *ColumnName:* 추가할 열의 이름입니다. 
* *Expression:* 기존 열에 대한 계산입니다.

**반환**

추가 열이 지정된 입력된 테이블의 복사본.

**팁**

* 일부 열의 삭제 또는 이름 바꾸기도 원하는 경우 [`project`](#project-operator)을 대신 사용합니다.
* 단지 긴 식에 사용할 목적으로 더 짧은 이름을 얻기 위해 `extend`을 사용하지 마십시오. `...| extend x = anonymous_user_id_from_client | ... func(x) ...` 

    테이블의 기본 열은 인덱싱되었으며, 새 이름은 인덱싱되지 않은 추가 열을 정의하므로 쿼리가 더 느리게 실행될 가능성이 있습니다.

**예제**

```AIQL
traces
| extend
    Age = now() - timestamp
```


## join 연산자

    Table1 | join (Table2) on CommonColumn

지정된 열의 값을 일치시켜 두 테이블의 행을 병합합니다.


**구문**

    Table1 | join [kind=Kind] (Table2) on CommonColumn [, ...]

**인수**

* *Table1* - join의 '좌변'입니다.
* *Table2* - join의 '우변'입니다. 테이블을 출력하는 중첩 된 쿼리 식일 수 있습니다.
* *CommonColumn* - 두 테이블에 같은 이름을 가진 열입니다.
* *Kind* - 두 테이블의 행을 일치시킬 방법을 지정합니다.

**반환**

다음을 포함하고 있는 테이블:

* 일치하는 키를 포함하여 두 테이블 각각의 모든 열에 대한 열. 이름 충돌이 있는 경우 우변의 열 이름이 자동으로 바뀝니다.
* 입력된 테이블 간의 모든 일치 항목에 대한 행. 일치 항목은 한 테이블에서 선택된 행이며 모든 `on` 필드에 대해 다른 테이블의 행과 같은 값을 가지고 있습니다. 

* `Kind` 지정 안 함

    `on` 키의 각 값에 대해 좌변의 행 한 개만 일치합니다. 출력은 오른쪽부터 이 행과 행 일치 각각에 대해 한 개의 행을 포함하고 있습니다.

* `Kind=inner`
 
     왼쪽 및 오른쪽에서 일치하는 행의 모든 조합에 대해 한 개의 출력 행이 있습니다.

* `kind=leftouter`(또는 `kind=rightouter` 또는 `kind=fullouter`)

     내부 일치 외에도 일치 항목이 없더라도 왼쪽(및/또는 오른쪽)의 모든 행에 대한 행이 있습니다. 이 경우 일치하지 않는 출력 셀에는 null 값이 포함되어 있습니다.

* `kind=leftanti`

     오른쪽에서 일치 항목이 없는 좌변의 모든 레코드를 반환합니다. 결과 테이블에는 좌변의 열만 있습니다.
 
이러한 필드에 대해 같은 값을 가진 여러 행이 있는 경우 모든 조합에 대한 행을 얻습니다.

**팁**

최상의 성능을 얻으려면:

* `where` 및 `project`를 사용하여 `join`에 앞서 입력 테이블의 행 및 열 수를 줄입니다. 
* 한 테이블이 언제나 다른 테이블보다 더 작으면 해당 테이블을 조인의 좌변(파이프된)으로 사용합니다.
* 조인 일치에 대한 열은 같은 이름을 가져야 합니다. 테이블 중 하나의 열 이름을 바꾸어야 할 경우 project 연산자를 사용합니다.

**예제**

로그에서 일부 항목이 활동의 시작과 끝을 표시하는 확장된 활동을 가져옵니다.

```AIQL
    let Events = MyLogTable | where type=="Event" ;
    Events
    | where Name == "Start"
    | project Name, City, ActivityId, StartTime=timestamp
    | join (Events
           | where Name == "Stop"
           | project StopTime=timestamp, ActivityId)
        on ActivityId
    | project City, ActivityId, StartTime, StopTime, Duration, StopTime, StartTime

```

[조인의 다양한 특징 정보](app-analytics-samples.md#join-flavors)

## let 절

**Tabular let - 테이블 이름 지정**

    let recentReqs = requests | where timestamp > ago(3d); 
    recentReqs | count

**Scalar let - 값 이름 지정**

    let interval = 3d; 
    requests | where timestamp > ago(interval)

**Lambda let - 함수 이름 지정**

    let Recent = 
       (interval:timespan) { requests | where timestamp > ago(interval) };
    Recent(3h) | count

let 절은 이름을 표 형식 결과, 스칼라 값 또는 함수에 바인딩합니다. 이 절은 쿼리에 대한 접두사이며 바인딩 범위는 해당 쿼리입니다. (Let은 세션에서 나중에 사용할 사항의 이름을 지정하는 방법을 제공하지 않습니다.)

**구문**

    let name = scalar_constant_expression ; query

    let name = query ; query

    let name = (parameterName : type [, ...]) { plain_query }; query

* *type:* `bool`, `int`, `long`, `double`, `string`, `timespan`, `datetime`, `guid`, [`dynamic`](app-analytics-scalars.md#dynamic-type)
* *plain\_query:* let 절이 앞에 들어가지 않은 쿼리.

**예**




    let rows(n:long) = range steps from 1 to n step 1;
    rows(10) | ...


Self-join:

    let Recent = events | where timestamp > ago(7d);
    Recent | where name contains "session_started" 
    | project start = timestamp, session_id
    | join (Recent 
        | where name contains "session_ended" 
        | project stop = timestamp, session_id)
      on session_id
    | extend duration = stop - start 

## limit 연산자

     T | limit 5

입력된 테이블에서 지정한 수까지의 행을 반환합니다. 레코드가 반환된다는 보장은 없습니다. (특정 레코드를 반환하려면 [`top`](#top-operator)을 사용합니다.)

**Alias** `take`

**구문**

    T | limit NumberOfRows


**팁**

`Take`은 대화형으로 작업하는 경우 결과의 샘플을 참조하는 간단하고 효율적인 방법입니다. 특정 행을 생성하거나 행을 특정 순서로 생성한다는 보장이 없다는 데 유의하십시오.

`take`을 사용하더라도 클라이언트에 반환되는 행 수에 대한 암시적 제한이 있습니다. 이 제한을 사용하려면 `notruncation` 클라이언트 요청 옵션을 사용합니다.



## mvexpand 연산자

    T | mvexpand listColumn 

동적 형식(JSON)의 셀에서 각 항목이 별도의 행을 가진 목록을 확장합니다. 확장된 행의 모든 다른 셀이 중복됩니다.

(반대 기능을 수행하는 [`summarize makelist`](#summarize-operator)을 참조하세요.)

**예제**

입력 테이블이 다음과 같다고 가정:

|A:int|B:string|D:dynamic|
|---|---|---|
|1|"hello"|{"key":"value"}|
|2|"world"|[0,1,"k","v"]|

    mvexpand D

결과:

|A:int|B:string|D:dynamic|
|---|---|---|
|1|"hello"|{"key":"value"}|
|2|"world"|0|
|2|"world"|1|
|2|"world"|"k"|
|2|"world"|"v"|


**구문**

    T | mvexpand  [bagexpansion=(bag | array)] ColumnName [limit Rowlimit]

    T | mvexpand  [bagexpansion=(bag | array)] [Name =] ArrayExpression [to typeof(Typename)] [limit Rowlimit]

**인수**

* *ColumnName:* 결과에서 명명된 열의 배열이 여러 행으로 확장됩니다. 
* *ArrayExpression:* 배열을 생성하는 식입니다. 이 양식을 사용하면 새 열이 추가되며 기존 열은 보존됩니다.
* *Name:* 새 열에 대한 이름입니다.
* *Typename:* 확장된 식을 특정 형식으로 캐스트
* *RowLimit:* 각 원래 행에서 생성되는 최대 행 수. 기본값은 128입니다.

**반환**

명명된 열의 배열 또는 배열 식에 있는 각 값에 대한 여러 행.

확장된 열은 언제나 동적 형식을 가집니다. 값을 계산하거나 집계하려는 경우 `todatetime()` 또는 `toint()` 등과 같은 캐스트를 사용합니다.

속성 모음 확장의 두 가지 모드가 지원됩니다.

* `bagexpansion=bag`: 속성 모음이 단일 항목 속성 모음으로 확장됩니다. 이는 기본 확장입니다.
* `bagexpansion=array`: 속성 모음이 두 요소로 이루어진 `[`*key*`,`*value*`]` 배열로 확장되며, 키 및 값에 대한 균일한 액세스가 가능합니다(또한 예를 들어 속성 이름에 대해 고유 개수 집계를 실행). 

**예**


    exceptions | take 1 
    | mvexpand details[0]

예외 레코드를 세부 정보 필드의 각 항목에 대한 행으로 분할합니다.

[시간 경과에 따른 실시간 활동의 차트 개수](app-analytics-samples.md#concurrent-activities)를 참조하세요.


## parse 연산자

    T | parse "I am 63 next birthday" with "I am" Year:int "next birthday"

    T | parse kind=regex "My 62nd birthday" 
        with "My" Year:regex("[0..9]+") regex("..") "birthday"

문자열에서 값을 추출합니다. simple 또는 보통의 식 일치를 사용할 수 있습니다.

`with` 절의 요소는 소스 문자열에 대해 차례로 일치됩니다. 각 요소는 소스 텍스트의 청크를 분할합니다. 일반 문자열인 경우 일치한다면 일치하는 커서가 따라서 움직입니다. 형식 이름을 가진 열인 경우 커서는 지정된 형식을 구문 분석하기에 충분할 만큼 따라서 움직입니다. (문자열 일치는 다음 요소에 대한 일치가 발견될 때까지 따라서 움직입니다.) regex인 경우 정규 식을 일치시킵니다(그리고 결과 열이 언제나 문자열 형식을 가짐).

**구문**

    T | parse StringExpression with [SimpleMatch | Column:Type] ...

    T | parse kind=regex StringExpression 
        with [SimpleMatch | Column : regex("Regex")] ...

**인수**

* *T:* 입력 테이블입니다.
* *kind:* simple 또는 regex. 기본값은 simple입니다.
* *StringExpression:* 문자열로 계산되거나 변환할 수 있는 식입니다.
* *SimpleMatch:* 텍스트의 다음 부분과 일치시키는 문자열입니다.
* *Column:* 일치 항목을 할당할 새 열을 지정합니다.
* *Type:* 소스 문자열의 다음 부분을 구문 분석하는 방법을 지정합니다.
* *Regex:* 문자열의 다음 부분과 일치시키는 정규식입니다. 

**반환**

입력된 테이블, 열 목록에 따라 확장됩니다.


**예**

`parse` 연산자는 같은 `string` 식에 대해 여러 개의 `extract` 응용 프로그램을 사용하여 테이블에 대해 `extend`을 실행하는 효율적인 방법을 제공합니다. 이는 테이블에 developer trace("`printf`"/"`Console.WriteLine`") 문에 의해 생성된 열과 같은 개별 열로 분할할 여러 개의 값을 포함하고 있는 `string`열이 있는 경우에 가장 유용합니다.

아래 예제에서는 테이블 `StormEvents`의 `EventNarrative` 열에 `{0} at {1} crested at {2} feet around {3} on {4} {5}` 형식의 문자열이 포함되어 있다고 가정합니다. 아래 작업은 `SwathSize` 및 `FellLocation` 두 열을 가지고 테이블을 확장합니다.


|EventNarrative|
|---|
|브라운스빌(Brownsville)의 그린 리버(Green River) 강은 12월 12일 오전 9시 30분 EST 경에 18.8피트의 수위를 형성하였습니다. Brownsville의 고수위는 18피트입니다. 이 수위에 경미한 홍수가 발생합니다. 강의 범람으로 벽면과 일부 하부 둑이 일부 농지와 함께 잠깁니다.|
|보스턴(Boston)의 롤링 포크 리버 강은 12월 12일 오후 5시 EST 경에 39.3피트의 수위를 형성하였습니다. 보스턴의 고수위는 35피트입니다. 이 수위에서 경미한 홍수가 발생하여 일부 농지가 덮였습니다.|
|우드베리(Woodbury)의 그린 리버(Green River) 강은 12월 16일 오전 6시 EST 경에 36.7피트의 수위를 형성하였습니다. Woodbury에서 홍수 단계 33 피트입니다. 이 수위에 경미한 홍수가 발생하고 우드베리 시내 주위의 저지대가 물로 덮였습니다.|
|텔 시티(Tell City)의 오하이오 리버(Ohio River) 강이 12월18일 오전 7시 EST에 경에 39.0피트의 수위를 형성하였습니다. 텔 시티의 고수위는 38피트입니다. 이 수위에서 강은 표준보다 높게 둑을 범람하기 시작합니다. 인디아나 고속도로 66번 가가 로마와 더비(Derby) 사이에 범람합니다.|

```AIQL

StormEvents 
|  parse EventNarrative 
   with RiverName:string 
        "at" 
        Location:string 
        "crested at" 
        Height:double  
        "feet around" 
        Time:string 
        "on" 
        Month:string 
        " " 
        Day:long 
        "." 
        notImportant:string
| project RiverName , Location , Height , Time , Month , Day

```

|RiverName|위치|높이|Time|월|일|
|---|---|---|---|---|---|
|그린 리버 | 우드베리 |36\.7| 오전 6시 EST | 12월|16|
|롤링 포크 강 | 보스턴 |39\.3| 오후 5시 EST | 12월|12|
|그린 리버 | 브라운스빌 |18\.8| 오전 9시 30분 EST | 12월|12|
|오하이오 강 | 텔 시티 |39| 오전 7시 EST | 12월|18|

정규식을 사용하여 일치시킬 수도 있습니다. 이렇게 해도 같은 결과가 생성되지만 모든 결과 열이 문자열 형식을 갖게 됩니다.

```AIQL

StormEvents
| parse kind=regex EventNarrative 
  with RiverName:regex("(\\s?[a-zA-Z]+\\s?)+") 
  "at" Location:regex(".*") 
  "crested at " Height:regex("\\d+\\.\\d+") 
  " feet around" Time:regex(".*") 
  "on " Month:regex("(December|November|October)") 
   " " Day:regex("\\d+") 
   "." notImportant:regex(".*")
| project RiverName , Location , Height , Time , Month , Day
```


## project 연산자

    T | project cost=price*quantity, price

포함, 이름 바꾸기 또는 삭제할 열을 선택하고 새 계산된 열을 삽입합니다. 결과의 열 순서는 인수 순서에 의해 지정됩니다. 인수에 지정된 열만이 결과에 포함되며: 입력의 다른 열은 삭제됩니다. (`extend` 참조.)


**구문**

    T | project ColumnName [= Expression] [, ...]

**인수**

* *T:* 입력 테이블입니다.
* *ColumnName:* 출력에 나타낼 열 이름입니다. *Expression*이 없는 경우 해당 이름의 열이 입력에 나타납니다. 
* *Expression:* 입력 열을 참조하는 선택적 스칼라 식입니다. 

    입력의 기존 열과 같은 이름을 가진 새 계산된 열을 반환하는 것이 올바릅니다.

**반환**

인수로 이름 지정된 열 및 입력 테이블만큼의 행을 가진 테이블입니다.

**예제**

다음 예제에서는 `project` 연산자를 사용하여 수행할 수 있는 여러 종류의 조작을 보여줍니다. 입력된 테이블 `T`에는 형식 `int`의 열 세 개: 즉, `A`, `B` 및 `C`가 있습니다.

```AIQL
T
| project
    X=C,                       // Rename column C to X
    A=2*B,                     // Calculate a new column A from the old B
    C=strcat("-",tostring(C)), // Calculate a new column C from the old C
    B=2*B                      // Calculate a new column B from the old B
```


[추가 예제](app-analytics-samples.md#activities).


## range 연산자

    range LastWeek from ago(7d) to now() step 1d

단일 열로 이루어진 값 테이블을 생성합니다. 참고로 파이프라인 입력이 없습니다.

|전주|
|---|
|2015-12-05 09:10:04.627|
|2015-12-06 09:10:04.627|
|...|
|2015-12-12 09:10:04.627|



**구문**

    range ColumnName from Start to Stop step Step

**인수**

* *ColumnName:* 출력 테이블의 단일 열 이름입니다.
* *Start:* 출력에서 가장 작은 값입니다.
* *Stop:* 출력에서 생성되는 가장 높은 값입니다(또는 *step*으로 이 값에 대해 단계를 지정한 경우 가장 높은 값에 대한 범위).
* *Step:* 연속된 두 값 사이의 차입니다. 

인수는 숫자, 날짜 또는 시간 간격 값이어야 합니다. 아무 테이블의 열이나 참조할 수는 없습니다. (입력 테이블을 기반으로 범위를 계산하려면 [range *함수*](app-analytics-scalars.md#range)를 사용하며, [mvexpand 연산자](#mvexpand-operator)와 함께 사용할 수 있습다.)

**반환**

*ColumnName*이라는 단일 열을 가진 테이블이며, 해당 열의 값은 *Start*, *Start* + *Step*, ... *Stop*까지입니다.

**예제**

```AIQL
range Steps from 1 to 8 step 3
```

`Steps`이라는 단일 열을 가진 테이블이며, 해당 열의 형식은 `long`, 해당 열의 값은 `1`, `4` 및 `7`입니다.

**예제**

    range LastWeek from bin(ago(7d),1d) to now() step 1d

지난 7일 자정에 대한 테이블입니다. bin(floor) 함수는 각 시간을 날짜의 시작으로 줄입니다.

**예제**

```AIQL
range timestamp from ago(4h) to now() step 1m
| join kind=fullouter
  (traces
      | where timestamp > ago(4h)
      | summarize Count=count() by bin(timestamp, 1m)
  ) on timestamp
| project Count=iff(isnull(Count), 0, Count), timestamp
| render timechart  
```

`range` 연산자를 사용하여 작은 임시 차원 테이블을 생성할 수 있는 방법을 보여주며, 이 테이블은 소스 데이터에 값이 없는 경우 0을 도입하기 위해 사용됩니다.

## reduce 연산자

    exceptions | reduce by outerMessage

유사한 레코드에 대해 그룹화를 시도합니다. 각 그룹에 대해 연산자는 그룹을 가장 잘 설명하는 것으로 여겨지는 `Pattern` 및 그룹의 레코드 `Count`개를 출력합니다.


![](./media/app-analytics-queries/reduce.png)

**구문**

    T | reduce by  ColumnName [ with threshold=Threshold ]

**인수**

* *ColumnName:* 검사할 열입니다. 문자열 형식이어야 합니다.
* *Threshold:* 범위 {0..1}의 값입니다. 기본값은 0.001입니다. 큰 입력의 경우 임계값이 작아야 합니다. 

**반환**

두 개의 열 `Pattern` 및 `Count`. 대부분의 경우 패턴은 열의 전체 값입니다. 경우에 따라 일반적인 용어를 식별하고 변수 부분을 '*'로 바꿉니다.

예를 들어 `reduce by city`의 결과는 다음을 포함할 수 있습니다.

|패턴 | 개수 |
|---|---|
| San * | 5182 |
| Saint * | 2846 |
| Moscow | 3726 |
| * -on- * | 2730 |
| 파리 | 27163 |


## render 지시문

    T | render [ table | timechart  | barchart | piechart ]

Render는 프레젠테이션 계층에 테이블 표시 방법을 지시합니다. 파이프의 마지막 요소여야 합니다. 화면의 컨트롤을 사용하는 대신 사용할 수 있는 편리한 방법으로, 특정 프레젠테이션 방법으로 쿼리를 저장할 수 있습니다.


## sort 연산자 

    T | sort by country asc, price desc

입력 테이블의 행을 하나 이상의 열에 의해 순서대로 정렬합니다.

**별칭** `order`

**구문**

    T  | sort by Column [ asc | desc ] [ `,` ... ]

**인수**

* *T:* 정렬하기 위해 입력한 테이블입니다.
* *Column:* 정렬 기준이 되는 *T*의 열입니다. 값의 형식은 숫자, 날짜, 시간 또는 문자열이어야 합니다.
* `asc` 오름차순으로, 즉 낮은 값에서 높은 값의 순서로 정렬합니다. 기본값은 `desc` 내림차순, 즉 높은 값에서 낮은 값으로 정렬하는 것입니다.

**예제**

```AIQL
Traces
| where ActivityId == "479671d99b7b"
| sort by Timestamp asc
```
특정 `ActivityId`을 갖고 해당 타임스탬프를 기준으로 정렬된 테이블의 모든 행을 추적합니다.

## summarize 연산자

입력된 테이블의 내용을 집계하는 테이블을 생성합니다.
 
    requests
	| summarize count(), avg(duration), makeset(client_City) 
      by client_CountryOrRegion

숫자, 평균 요청 기간 및 각 국가의 도시 집합을 표시하는 테이블입니다. 각 고유 국가에 대한 출력의 행이 있습니다. 출력 열은 개수, 평균 기간, 도시 및 국가를 표시합니다. 모든 다른 입력된 열은 무시됩니다.


    T | summarize count() by price_range=bin(price, 10.0)

[0,10.0], [10.0,20.0] 등의 각 간격에 가격을 가진 항목 수를 표시하는 테이블입니다. 이 예제는 개수에 대한 열 및 가격 범위에 대한 열을 가지고 있습니다. 모든 다른 입력된 열은 무시됩니다.

[추가 예제](app-analytics-aggregations.md)



**구문**

    T | summarize
         [  [ Column = ] Aggregation [ `,` ... ] ]
         [ by
            [ Column = ] GroupExpression [ `,` ... ] ]

**인수**

* *Column:* 결과 열에 대한 선택적 이름입니다. 기본적으로 식에서 파생된 이름입니다.
* *Aggregation:* 열 이름을 인수로 하는 `count()` 또는 `avg()` 등과 같은 [집계 함수](app-analytics-aggregations.md)에 대한 호출입니다. [집계 함수의 목록](app-analytics-aggregations.md)을 참조하세요.
* *GroupExpression:* 고유 값 집합을 제공하는 열에 대한 식입니다. 일반적으로 제한된 값 집합을 제공하는 열 이름, 또는 숫자 또는 시간 열을 인수로 하는 `bin()`입니다. 

`bin()`을 사용하지 않고 숫자 또는 시간 식을 제공할 경우 분석은 시간에 대해 `1h`의 간격 또는 숫자에 대해 `1.0`과 함께 자동으로 이를 적용합니다.

*GroupExpression*을 제공하지 않으면 전체 테이블이 단일 출력 행에 요약됩니다.



**반환**

입력 행은 `by` 식의 같은 값을 가진 그룹으로 배열됩니다. 그런 다음 지정된 집계 함수를 각 그룹에 대해 계산하여 각 그룹에 대해 한 행을 생성합니다. 결과는 `by` 열 및 계산된 각 집계에 대해 열을 하나 이상 포함하고 있습니다. (일부 집계 함수는 여러 열을 반환합니다.)

결과에는 `by` 값의 고유 집합의 수만큼 행이 있습니다. 숫자 값의 범위에 대해 요약하려는 경우 `bin()`를 사용하여 범위를 불연속 값으로 줄입니다.

**참고**

집계와 그룹화 식에 대해 모두 임의 식을 제공할 수 있지만 단순 열 이름을 사용하거나 `bin()`을 숫자 열에 적용하는 것이 더 효율적입니다.



## take 연산자

[제한](#limit-operator)의 별칭


## top 연산자

    T | top 5 by Name desc

지정된 열을 기준으로 정렬된 첫 번째 *N* 레코드를 반환합니다.


**구문**

    T | top NumberOfRows by Sort_expression [ `asc` | `desc` ] [, ... ]

**인수**

* *NumberOfRows:* 반환할 *T*의 행 수입니다.
* *Sort\_expression:* 행을 정렬하는 기준이 되는 식입니다. 일반적으로 단지 열 이름일 뿐입니다. sort\_expression을 두 개 이상 지정할 수 있습니다.
* 선택이 실제로 범위의 "맨 아래" 또는 "맨 위"인지를 제어하기 위해 `asc` 또는 `desc`(기본값)가 나타날 수 있습니다.


**팁**

`top 5 by name`은 겉보기에 `sort by name | take 5`와 같습니다. 그러나 이는 더 빠르게 실행되며 언제나 정렬된 결과를 반환하는 반면에, `take`은 그러한 실행과 결과가 보장되지 않습니다.


## union 연산자

     Table1 | union Table2, Table3

두 개 이상의 테이블을 사용하며 모든 행을 반환합니다.

**구문**

    T | union [ kind= inner | outer ] [ withsource = ColumnName ] Table2 [ , ...]  

    union [ kind= inner | outer ] [ withsource = ColumnName ] Table1, Table2 [ , ...]  

**인수**

* *Table1*, *Table2* ...
 *  `events` 등과 같은 테이블의 이름, 또는
 *  `(events | where id==42)`과 같은 쿼리 식
 *  와일드 카드를 사용하여 지정한 테이블 집합입니다. 예를 들어 `E*`은 이름이 `E`로 시작하는 데이터베이스의 모든 테이블의 합집합을 형성할 수 있습니다.
* `kind`: 
 * `inner` - 결과에는 모든 입력 테이블에 공통인 열의 하위 집합이 있습니다.
 * `outer` - 결과에는 입력에서 발생하는 모든 열이 있습니다. 입력된 행에 의해 정의되지 않은 셀은 `null`로 설정됩니다.
* `withsource=`*ColumnName:* 지정된 경우 출력은 값이 각 행에 기여한 소스 테이블을 나타내는 *ColumnName*이라는 열을 포함합니다.

**반환**

모든 입력된 테이블에 있는 만큼 행을 가진 테이블입니다.

**예제**

```AIQL

let ttrr = requests | where timestamp > ago(1h);
let ttee = exceptions | where timestamp > ago(1h);
union tt* | count
```
이름이 "tt"로 시작하는 모든 테이블의 합집합입니다.


**예제**

```AIQL

union withsource=SourceTable kind=outer Query, Command
| where Timestamp > ago(1d)
| summarize dcount(UserId)
```
지난 날에 대해 `exceptions` 이벤트 또는 `traces` 이벤트를 생성한 고유 사용자 수입니다. 결과에서 'SourceTable' 열은 "쿼리" 또는 "명령"을 나타냅니다.

```AIQL
exceptions
| where Timestamp > ago(1d)
| union withsource=SourceTable kind=outer 
   (Command | where Timestamp > ago(1d))
| summarize dcount(UserId)
```

더 효율적인 이 버전은 동일한 결과를 생성합니다. 합집합을 생성하기 전에 각 테이블을 필터링합니다.

## where 연산자

     T | where fruit=="apple"

조건자를 만족하는 행의 부분집합으로 테이블을 필터링합니다.

**별칭** `filter`

**구문**

    T | where Predicate

**인수**

* *T*: 레코드를 필터링할 테이블 형식 입력입니다.
* *Predicate:* *T*의 열에 대한 `boolean` [식](app-analytics-scalars.md#boolean)입니다. *T*의 각 행에 대해 계산합니다.

**반환**

*Predicate*이 `true`인 *T* 의 행입니다.

**팁**

가장 빠른 성능을 얻으려면:

* 열 이름과 상수 사이에 **단순 비교 사용**('상수'는 테이블에 대한 상수를 의미합니다. 따라서 `now()` 및 `ago()`을 사용 가능하며 [`let` 문](app-analytics-syntax.md#let-statements)을 사용하여 할당한 스칼라 값입니다.)

    예를 들어 `where floor(Timestamp, 1d) == ago(1d)`보다 `where Timestamp >= ago(1d)`를 선호합니다.

* **가장 단순한 행 먼저**: 여러 개의 절을 `and`와 공동 조인한 경우 열을 한 개만 포함하고 있는 절을 먼저 놓습니다. 따라서 `Timestamp > ago(1d) and OpId == EventId`이 다른 방법보다 낫습니다.


**예제**

```AIQL
Traces
| where Timestamp > ago(1h)
    and Source == "Kuskus"
    and ActivityId == SubActivityIt 
```

1시간보다 더 오래되지 않고 "Kuskus"라는 소스에서 나오고 같은 값의 열 두 개를 가진 레코드.

참고로 마지막 두 열은 인덱스를 이용할 수 없고 스캔을 강제로 실행하므로 해당 두 열 사이에 비교를 넣습니다.





[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0330_2016-->