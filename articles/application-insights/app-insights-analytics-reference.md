---
title: "Azure Application Insights 내 Analytics 참조 | Microsoft Docs"
description: "Application Insights의 강력한 검색 도구인 분석의 문 참조입니다. "
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: eea324de-d5e5-4064-9933-beb3a97b350b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: dd3478966e4e5ccc9f108940401c7ee9454087dd
ms.contentlocale: ko-kr
ms.lasthandoff: 07/06/2017


---
# <a name="reference-for-analytics"></a>분석에 대한 참조
[분석](app-insights-analytics.md)은 [Application Insights](app-insights-overview.md)의 강력한 검색 기능입니다. 다음 페이지에서는 분석 쿼리 언어에 대해 설명합니다.

추가 정보 출처:

* 입력 시 분석 기능에서 많은 참조 자료를 사용할 수 있습니다. 쿼리를 입력하기 시작하면 가능한 완성 단어가 표시됩니다.
* [자습서 페이지](app-insights-analytics-tour.md)에는 언어 기능에 대한 단계별 소개가 표시됩니다.
* [SQL 사용자 치트 시트](https://aka.ms/sql-analytics)에서는 가장 일반적인 코드를 변환합니다.
* 앱이 아직 데이터를 Application Insights로 전송하지 않은 경우 [시뮬레이션된 데이터에 대한 분석 시험 사용](https://analytics.applicationinsights.io/demo)을 수행합니다.
 

## <a name="index"></a>인덱스
**Let** [let](#let-clause) | [materialize](#materialize) 

**쿼리 및 연산자** [as](#as-operator) | [autocluster](#evaluate-autocluster_v2) | [basket](#evaluate-basketv2) | [count](#count-operator) | [datatable](#datatable-operator) | [diffpatterns](#evaluate-diffpatterns_v2) | [distinct](#distinct-operator) | [evaluate](#evaluate-operator) | [extend](#extend-operator) | [extractcolumns](#evaluate-extractcolumns) | [find](#find-operator) | [getschema](#getschema-operator) | [join](#join-operator) | [limit](#limit-operator) | [make-series](#make-series-operator) | [mvexpand](#mvexpand-operator) | [parse](#parse-operator) | [project](#project-operator) | [project-away](#project-away-operator) | [range](#range-operator) | [reduce](#reduce-operator) | [render directive](#render-directive) | [restrict clause](#restrict-clause) | [sample](#sample-operator) | [sample-distinct](#sample-distinct-operator) | [sort](#sort-operator) | [summarize](#summarize-operator) | [table](#table-operator) | [take](#take-operator) | [top](#top-operator) | [top-nested](#top-nested-operator) | [union](#union-operator) | [where](#where-operator) 

**집계** [모든](#any) | [argmax](#argmax) | [argmin](#argmin) | [avg](#avg) | [buildschema](#buildschema) | [count](#count) | [countif](#countif) | [dcount](#dcount) | [dcountif](#dcountif) | [makelist](#makelist) | [makeset](#makeset) | [최대](#max) | [min](#min) | [백분위 수](#percentile) | [백분위 수](#percentiles) | [percentilesw](#percentilesw) | [percentilew](#percentilew) | [stdev](#stdev) | [sum](#sum) | [차이](#variance)

**스칼라** [부울 리터럴](#boolean-literals) | [부울 연산자](#boolean-operators) | [캐스트](#casts) | [스칼라 비교](#scalar-comparisons) | [gettype](#gettype) | [해시](#hash) | [iff](#iff) | [isnotnull](#isnotnull) | [isnull](#isnull) | [notnull](#notnull) | [toscalar](#toscalar)

**숫자** [산술 연산자](#arithmetic-operators) | [숫자 리터럴](#numeric-literals) | [abs](#abs) | [bin](#bin) | [exp](#exp) | [층](#floor) | [감마](#gamma) | [로그](#log) | [rand](#rand) | [sqrt](#sqrt) | [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

**숫자 계열** 
[series_fir](#seriesfir) | [series\_fit\_line](#seriesfitline) | [series\_fit\_2lines](#seriesfit2lines) | [series_iir](#seriesiir) |[series_outliers](#seriesoutliers)| [series_periods](#seriesperiods) | [series_stats](#seriesstats) | 

**날짜 및 시간** [날짜 및 시간 식](#date-and-time-expressions) | [날짜 및 시간 리터럴](#date-and-time-literals) | [전](#ago) | [datepart](#datepart) | [dayofmonth](#dayofmonth) | [dayofweek](#dayofweek) | [dayofyear](#dayofyear) | [endofday](#endofday) | [endofmonth](#endofmonth) | [endofweek](#endofweek) | [endofyear](#endofyear) | [getmonth](#getmonth) | [getyear](#getyear) | [지금](#now) | [startofday](#startofday) | [startofmonth](#startofmonth) | [startofweek](#startofweek) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan) | [weekofyear](#weekofyear)

**문자열** [GUID](#guids) | [난독 처리된 문자열 리터럴](#obfuscated-string-literals) | [문자열 리터럴](#string-literals) | [문자열 비교](#string-comparisons) | [countof](#countof) | [extract](#extract) | [in, !in](#in) | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty)| [parseurl](#parseurl) | [replace](#replace) | [split](#split) | [strcat](#strcat) | [strlen](#strlen) | [substring](#substring) | [tolower](#tolower) | [toupper](#toupper)

**배열, 개체 및 동적** [배열 및 개체 리터럴](#array-and-object-literals) | [동적 개체 함수](#dynamic-object-functions) | [let 절의 동적 개체](#dynamic-objects-in-let-clauses) | [JSON Path 식](#json-path-expressions) | [이름](#names) | [arraylength](#arraylength) | [extractjson](#extractjson) | [in, !in](#in) | [parsejson](#parsejson) | [range](#range) | [todynamic](#todynamic) | [treepath](#treepath)

## <a name="let"></a>사용
### <a name="let-clause"></a>let 절
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

    let us_date = (t:datetime)
    {
      strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ", 
      bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
    };
    requests 
    | summarize count() by bin(timestamp, 1h) 
    | project count_, pacificTime=us_date(timestamp-8h)

Let 절은 테이블 형식 결과, 스칼라 값 또는 함수에 [name](#names)을 바인딩합니다. 이 절은 쿼리에 대한 접두사이며 바인딩 범위는 해당 쿼리입니다. (Let은 세션에서 나중에 사용할 사항의 이름을 지정하는 방법을 제공하지 않습니다.)

**구문**

    let name = scalar_constant_expression ; query

    let name = query ; query

    let name = (parameterName : type [, ...]) { plain_query }; query

    let name = (parameterName : type [, ...]) { scalar_expression }; query

* *type:* `bool`, `int`, `long`, `double`, `string`, `timespan`, `datetime`, `guid`, [`dynamic`](#dynamic-type)
* *plain_query:* Let 절이 접두사로 되어있지 않은 쿼리입니다.

**예**

    let rows = (n:long) { range steps from 1 to n step 1 };
    rows(10) | ...

테이블 결과를 스칼라로 변환하고 쿼리에 사용합니다.

```
let topCities =  toscalar ( // convert single column to value
   requests
   | summarize count() by client_City 
   | top 4 by count_ 
   | summarize makeset(client_City)) ;
requests
| where client_City in (topCities) 
| summarize count() by client_City;
```

### <a name="materialize"></a>구체화

materialize()를 사용하여 let 절의 결과가 한 번 이상의 다운스트림에 사용되는 성능을 개선합니다. materialize()는 쿼리가 두 번 이상 실행되지 않도록 하기 위해 쿼리 실행 시 테이블 형식 let 절을 계산 및 캐시합니다.

**구문**

    materialize(expression)

**인수**

* `expresion`: 쿼리 실행 시 계산 및 캐시할 테이블 형식의 식입니다.

**팁**

* 피연산자가 한 번 실행할 수 있는 상호 하위 쿼리를 갖는 조인/유니언이 있는 경우 구체화를 사용합니다.
* 또한 시나리오에서 분기 기간을 조인/유니언해야 할 때 유용합니다.
* 구체화는 캐시된 결과에 이름을 부여함으로써 let 문에서만 사용하도록 허용되어 있습니다.
* 구체화의 캐시 크기 제한이 5GB입니다. 이 제한은 클러스터 노드당이며 모든 쿼리에 대해 상호적입니다.

**예제: 셀프 조인**


```AIQL
let totalPagesPerDay = pageViews
| summarize by name, Day = startofday(timestamp)
| summarize count() by Day;
let materializedScope = pageViews
| summarize by name, Day = startofday(timestamp);
let cachedResult = materialize(materializedScope);
cachedResult
| project name, Day1 = Day
| join kind = inner
(
    cachedResult
    | project name, Day2 = Day
)
on name
| where Day2 > Day1
| summarize count() by Day1, Day2
| join kind = inner
    totalPagesPerDay
on $left.Day1 == $right.Day
| project Day1, Day2, Percentage = count_*100.0/count_1
```

캐시되지 않은 버전은 결과 `scope`을 두 번 사용합니다.

```AIQL
let totalPagesPerDay = pageViews
| summarize by name, Day = startofday(timestamp)
| summarize count() by Day;
let scope = pageViews
| summarize by name, Day = startofday(timestamp);
scope      // First use of this table.
| project name, Day1 = Day
| join kind = inner
(
    scope  // Second use can cause evaluation twice.
    | project name, Day2 = Day
)
on name
| where Day2 > Day1
| summarize count() by Day1, Day2
| join kind = inner
    totalPagesPerDay
on $left.Day1 == $right.Day
| project Day1, Day2, Percentage = count_*100.0/count_1
```

## <a name="queries-and-operators"></a>쿼리 및 연산자
원격 분석에 대한 쿼리는 소스 스트림에 대한 참조 및 그 뒤의 필터 파이프라인으로 이루어져 있습니다. 예:

```AIQL
requests // The request table starts this pipeline.
| where client_City == "London" // filter the records
   and timestamp > ago(3d)
| count 
```

파이프 문자 `|` 이 접두사로 붙는 각 필터는 일부 매개 변수를 가진 *연산자*의 인스턴스입니다. 연산자에 대한 입력은 앞에 붙는 파이프라인의 결과인 테이블입니다. 대부분의 경우 모든 매개 변수는 입력 열에 대한 [스칼라 식](#scalars) 입니다. 매개 변수가 입력 열의 이름인 경우도 있고 매개 변수가 두 번째 테이블인 경우도 있습니다. 테이블에 열 및 행이 한 개만 있더라도 쿼리의 결과는 언제나 테이블입니다.

쿼리는 단일 줄 바꿈을 포함할 수 있지만 빈 줄이 있으면 종료됩니다. 쿼리는 `//` 와(과) 줄의 끝 사이에 주석을 포함할 수 있습니다.

쿼리에는 스칼라, 테이블 또는 쿼리 내에서 사용할 수 있는 함수를 정의하는 [Let 절](#let-clause)이 한 개 이상 접두사로 붙을 수 있습니다.

```AIQL

    let interval = 3d ;
    let city = "London" ;
    let req = (city:string) {
      requests
      | where client_City == city and timestamp > ago(interval) };
    req(city) | count
```

> `T` 은(는) 앞에 붙는 파이프라인 또는 원본 테이블을 나타내기 위해 사용됩니다.
> 
> 

### <a name="as-operator"></a>as 연산자

이름을 입력 테이블 형식 식으로 일시적으로 바인딩합니다.

**구문**

    T | as name

**인수**

* *name:* 테이블에 사용할 임시 이름

**참고 사항**

* 해당 이름을 향후 하위식에서 사용하려는 경우 *as* 대신 [let](#let-clause)을 사용합니다.
* *as*를 사용하여 테이블 이름을 지정하면 [union](#union-operator), [find](#find-operator) 또는 [search](#search-operator) 결과에 표시됩니다.

**예제**

```AIQL
range x from 1 to 10 step 1 | as T1
| union withsource=TableName (requests | take 10 | as T2)
```

### <a name="count-operator"></a>count 연산자
`count` 연산자는 입력 레코드 집합의 레코드(행) 수를 반환합니다.

**구문**

    T | count

**인수**

* *T*: 레코드 수를 계산할 테이블 형식 데이터입니다.

**반환**

이 함수는 형식 `long`의 단일 레코드 및 열을 가진 테이블을 반환합니다. 유일한 셀의 값은 *T*의 레코드 수입니다. 

**예제**

```AIQL
requests | count
```

### <a name="datatable-operator"></a>datatable 연산자

인라인 테이블을 지정합니다. 스키마 및 값은 쿼리 자체에 정의됩니다.

이 연산자에는 파이프라인 입력이 없습니다.

**구문**

    datatable ( ColumnName1 : ColumnType1 , ...) [ScalarValue1, ...]

* *ColumnName* 열의 이름입니다.
* *ColumnType* [데이터 형식](#scalars)입니다. 
* *ScalarValue* 적절한 형식의 값입니다. 값 개수는 열 개수의 배수여야 합니다. 

**반환**

지정된 값을 포함하는 테이블입니다.

**예제**

```AIQL
datatable (Date:datetime, Event:string)
    [datetime(1910-06-11), "Born",
     datetime(1930-01-01), "Enters Ecole Navale",
     datetime(1953-01-01), "Published first book",
     datetime(1997-06-25), "Died"]
| where strlen(Event) > 4
```

### <a name="distinct-operator"></a>distinct 연산자

값의 고유한 조합을 갖는 행 집합이 포함된 테이블을 반환합니다. 경우에 따라 해당 연산 이전의 열 하위 집합으로 프로젝션됩니다.

**구문**

    T | distinct *              // All columns
    T | distinct Column1, ...   // Columns to project

**예제**

```AIQL
datatable (Supplier: string, Fruit: string, Price:int) 
["Contoso", "Grapes", 22,
"Fabrikam", "Apples", 14,
"Contoso", "Apples", 15,
"Fabrikam", "Grapes", 22]
| distinct Fruit, Price 
```


|Fruit|가격|
|---|---|
|Grapes|22|
|Apples|14|
|Apples|15|


### <a name="evaluate-operator"></a>evaluate 연산자
`evaluate`은(는) 특수화된 알고리즘이 쿼리에 추가될 수 있도록 하는 확장 메커니즘입니다.

`evaluate`은(는) 쿼리 파이프라인의 마지막 연산자여야 합니다(가능한 `render` 제외). 함수 본문에는 나타나지 않아야 합니다.

[evaluate autocluster](#evaluate-autocluster_v2) | [evaluate basket](#evaluate-basketv2) | [evaluate diffpatterns](#evaluate-diffpatterns_v2) | [evaluate extractcolumns](#evaluate-extractcolumns)

#### <a name="evaluate-autocluster-deprecated"></a>evaluate autocluster(사용되지 않음)
     T | evaluate autocluster()

Autocluster는 데이터 집합에서 자연적인 그룹화를 찾는 빠른 방법입니다. 예를 들어 대량의 요청 데이터에서 404 오류의 80%가 특정 도시의 클라이언트가 수행한 하나의 특정 URL에 대한 요청이라는 사실을 빠르게 파악할 수 있습니다.

AutoCluster는 데이터에 나타나는 불연속 특성(차원)의 일반적인 패턴을 찾은 후 원래 쿼리의 결과(100개 행 또는 100,000개 행)를 더 작은 수의 패턴으로 줄입니다. AutoCluster는 실패(예: 예외, 작동 중단)를 분석하는 데 도움을 주기 위해 개발되었으나 필터링된 모든 데이터 집합에 작동할 수 있습니다. 

**이 버전의 `autocluster`은 더 이상 사용되지 않습니다. [autocluster_v2](#evaluate-autocluster_v2)를 사용하세요.**

**구문**

    T | evaluate autocluster( arguments )

**반환**

AutoCluster는 여러 불연속 특성 간에 공통된 값을 공유하는 데이터 부분을 캡처하는 일반적으로 작은 패턴 집합을 반환합니다. 각 패턴은 결과의 행으로 표시됩니다. 

처음 두 열은 패턴에 의해 캡처되는 원래 쿼리 중에서 행의 개수 및 백분율입니다. 나머지 열은 원래 쿼리에서 가져오며, 해당 값은 열의 특정 값 또는 '*'(변수 값 의미)입니다. 

패턴은 분리되어 있지 않습니다. 겹칠 수도 있으며 일반적으로 모든 원본 행을 포함하지는 않습니다. 일부 행은 어떤 패턴에도 속하지 않을 수 있습니다.

**팁**

* 입력 파이프에 `where` 및 `project`을(를) 사용하여 관심 있는 데이터 부분만 남깁니다.
* 관심 있는 행을 찾으면 `where` 필터에 특정 값을 추가하여 추가로 드릴인투할 수 있습니다.

**인수(모두 선택 사항)**

* `output=all | values | minimal` 
  
    결과의 형식입니다. Count 및 Percent 열은 결과에 항상 표시됩니다. 
  
  * `all` - 입력의 모든 열이 출력입니다.
  * `values` - 결과에 '*'가 있는 열만 필터링합니다.
  * `minimal` - 원래 쿼리의 모든 행에 대해 동일한 열도 필터링합니다. 
* `min_percent=`*double* (기본값: 1)
  
    생성된 행의 최소 비율 적용 범위입니다.
  
    예제: `T | evaluate autocluster("min_percent=5.5")`
* `num_seeds=` *int* (기본값: 25) 
  
    시드 수는 알고리즘의 초기 로컬 검색 지점의 수를 결정합니다. 일부 경우 데이터의 구조에 따라, 시드 수를 늘리면 증가된 검색 공간을 통해 쿼리는 느려지지만 결과의 수(또는 품질)가 증가합니다. num_seeds 인수는 양쪽 방향에서 감소하는 결과를 나타내므로 5보다 작게 줄이면 성능 향상이 미미하고, 50보다 크게 늘리면 추가 패턴이 거의 생성되지 않습니다.
  
    예제: `T | evaluate autocluster("num_seeds=50")`
* `size_weight=` *0<double<1*+(기본값: 0.5)
  
    제네릭(높은 적용 범위) 및 정보(많은 공유 값) 사이의 균형을 조절할 수 있습니다. 일반적으로 size_weight을 늘리면 패턴 수가 줄어들고 각 패턴은 더 큰 비율에 적용되게 됩니다. 일반적으로 size_weight를 줄이면 공유 값은 더 많고 비율 적용 범위는 더 작으며 더욱 구체적인 패턴이 생성됩니다. 내부의 공식은 size_weight 및 1-size_weight가 가중치로 적용된 표준화된 제네릭 점수와 정보 점수 간의 기하 평균입니다. 
  
    예제: `T | evaluate autocluster("size_weight=0.8")`
* `weight_column=` *column_name*
  
    지정된 가중치(기본적으로 각 행의 가중치는 '1'임)에 따라 입력의 각 행을 고려할 때 이미 각 행에 포함되어 있는 데이터의 계정 샘플링 또는 버킷팅/집계가 가중치 열의 일반적인 용도로 간주됩니다.
  
    예: `T | evaluate autocluster("weight_column=sample_Count")` 

<a name="evaluate-autocluster_v2"></a>

#### <a name="evaluate-autoclusterv2"></a>evaluate autocluster_v2

    T | evaluate autocluster_v2()

AutoCluster는 데이터에 나타나는 불연속 특성(차원)의 일반적인 패턴을 찾은 후 원래 쿼리의 결과(100개 행 또는 100,000개 행)를 더 작은 수의 패턴으로 줄입니다. AutoCluster는 실패(예: 예외, 작동 중단)를 분석하는 데 도움을 주기 위해 개발되었으나 필터링된 모든 데이터 집합에 작동할 수 있습니다. AutoCluster 알고리즘은 개발자 분석 연구 팀(KustoML@microsoft.com)에서 개발했습니다.

이 플러그 인은 사용되지 않는 autocluster 플러그 인 구문을 바꿉니다.     

**구문**
`T | evaluate autocluster_v2( arguments )`

**반환** AutoCluster는 여러 불연속 특성 간에 공통된 값을 공유하는 데이터 부분을 캡처하는 일반적으로 작은 패턴 집합을 반환합니다. 각 패턴은 결과의 행으로 표시됩니다. 첫 번째 열은 세그먼트 ID입니다. 다음 두 열은 패턴에 의해 캡처되는 원래 쿼리 중에서 행의 개수 및 백분율입니다. 나머지 열은 원래 쿼리에서 가져오며, 해당 값은 열의 특정 값 또는 와일드카드 값(기본적으로 null이며, 변수 값을 의미함)입니다. 패턴은 분리되어 있지 않습니다. 겹칠 수도 있으며 일반적으로 모든 원본 행을 포함하지는 않습니다. 일부 행은 어떤 패턴에도 속하지 않을 수 있습니다.

**팁** 입력 파이프에 `where` 및 `project`를 사용하여 관심 있는 데이터 부분만 남깁니다.
관심 있는 행을 찾으면 `where` 필터에 특정 값을 추가하여 추가로 드릴인투할 수 있습니다.

**인수(모두 선택 사항)** `T | evaluate autocluster_V2([*SizeWight*,*WeightColumn*,*NumSeeds*,*CustomWildcard*,...])

모든 인수는 선택 사항이지만, 위와 같은 순서여야 합니다. 기본값을 사용해야 함을 나타내려면 문자열 물결표 값 '~'을 사용합니다(아래 예 참조).

**사용 가능한 인수**

- SizeWeight - 0<*double* <1 [기본값 0.5] 제네릭(높은 적용 범위) 및 정보(많은 공유 값) 사이의 균형을 조절할 수 있습니다. 일반적으로 이 값을 늘리면 패턴 수가 줄어들고 각 패턴은 더 큰 비율에 적용되게 됩니다. 일반적으로 이 값을 줄이면 공유 값은 더 많고 비율 적용 범위는 더 작으며 더욱 구체적인 패턴이 생성됩니다. 내부의 공식은 *SizeWeight* 및 *1-SizeWeight*가 가중치로 적용된 표준화된 제네릭 점수와 정보 점수 간의 기하 평균입니다. 

**예**
`T | evaluate autocluster_v2(0.8)`

- WeightColumn - *column_name*

지정된 가중치(기본적으로 각 행의 가중치는 '1'임)에 따라 입력의 각 행을 고려합니다. 이 인수는 숫자 열의 이름이어야 합니다(예: int, long, real). 이미 각 행에 포함되어 있는 데이터의 계정 샘플링 또는 버킷팅/집계가 가중치 열의 일반적인 용도로 간주됩니다.

**예**
`T | evaluate autocluster_v2('~', sample_Count)`

`- NumSeeds - *int* [기본값 25]

시드 수는 알고리즘의 초기 로컬 검색 지점의 수를 결정합니다. 일부 경우 데이터의 구조에 따라, 시드 수를 늘리면 증가된 검색 공간을 통해 쿼리는 느려지지만 결과의 수(또는 품질)가 증가합니다. 이 값은 양쪽 방향에서 감소하는 결과를 나타내므로 5보다 작게 줄이면 성능 향상이 미미하고, 50보다 크게 늘리면 추가 패턴이 거의 생성되지 않습니다.

**예**
`T | evaluate autocluster_v2('~','~',15)`

- CustomWildcard - *any_value_per_type*

현재 패턴이 이 열을 제한하지 않음을 나타내는 특정 형식에 대한 와일드카드 값을 결과 테이블에 설정합니다. 기본값은 null이고, 문자열의 경우 기본값은 빈 문자열입니다. 기본값이 데이터의 실행 가능한 값인 경우 다른 와일드카드 값을 사용해야 합니다(예: *).

**예제**

`T | evaluate autocluster_v2('~','~','~',int (-1), double(-1), long(0), datetime(1900-1-1))`

**예제**
```
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State , EventType , Damage
| evaluate autocluster_v2(0.6)
```
**결과**
|SegmentId|개수|백분율|시스템 상태|EventType|피해|
----------|-----|-------|-----|---------|------|
0|2278|38.7||우박|아니요
1|512|8.7||뇌우를 동반한 바람|예
2|898|15.3|텍사스|||

**사용자 지정 와일드카드가 사용된 예**
```
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State , EventType , Damage 
| evaluate autocluster_v2(0.2, '~', '~', '*')
```
**결과**
|SegmentId|개수|백분율|시스템 상태|EventType|피해|
----------|-----|-------|-----|---------|------|
0|2278|38.7|\*|우박|아니요
1|512|8.7|\*|뇌우를 동반한 바람|예
2|898|15.3|텍사스|\*|\*|

**추가 정보**

-  AutoCluster는 주로 [Algorithms for Telemetry Data Mining using Discrete Attributes](http://www.scitepress.org/DigitalLibrary/PublicationsDetail.aspx?ID=d5kcrO+cpEU=&t=1)(불연속 특성을 사용한 원격 분석 데이터 마이닝을 위한 알고리즘) 논문(전체 텍스트 링크: [pdf](https://kusto.azurewebsites.net/docs/queryLanguage/images/queries/ICPRAM17telemetry.pdf))에서 가져온 시드-확장 알고리즘을 기반으로 합니다. 


#### <a name="evaluate-basket-deprecated"></a>evaluate basket(사용되지 않음)

     T | evaluate basket()

**이 버전의 `basket`은 더 이상 사용되지 않습니다. [basket_v2](#evaluate-basketv2)를 사용하세요.**

**반환**

이벤트의 지정된 비율(기본값 0.05)보다 많이 나타나는 모든 패턴입니다.

**인수(모두 선택 사항)**

* `threshold=` *0.015<double<1* (기본값: 0.05) 
  
    자주 나오는 것으로 간주되는 행의 최소 비율을 설정합니다(비율이 더 작은 패턴은 반환되지 않음).
  
    예제: `T | evaluate basket("threshold=0.02")`
* `weight_column=` *column_name*
  
    지정된 가중치(기본적으로 각 행의 가중치는 '1'임)에 따라 입력의 각 행을 고려할 때 이미 각 행에 포함되어 있는 데이터의 계정 샘플링 또는 버킷팅/집계가 가중치 열의 일반적인 용도로 간주됩니다.
  
    예: T | evaluate basket("weight_column=sample_Count")
* `max_dims=` *1<int*(기본값: 5)
  
    쿼리 런타임의 줄이기 위해 기본적으로 제한되는 바구니당 상관 관계가 없는 차원의 최대 수를 설정합니다.
* `output=minimize` | `all` 
  
    결과의 형식입니다. Count 및 Percent 열은 결과에 항상 표시됩니다.
  
  * `minimize` - 결과에 '*'가 있는 열만 필터링합니다.
  * `all` - 입력의 모든 열이 출력입니다.

<a name="evaluate-basket"></a>
#### <a name="evaluate-basketv2"></a>evaluate basket_v2

     T | evaluate basket_v2()

Basket은 데이터에서 자주 나오는 모든 불연속 특성(차원) 패턴을 찾고, 원본 쿼리의 빈도 임계값을 통과한 자주 나오는 모든 패턴을 반환합니다. Basket은 데이터에서 자주 나오는 모든 패턴을 확실히 찾지만 다항의 실행 시간을 갖는다고 보장할 수는 없습니다. 쿼리 실행 시간은 행의 수에 대해 선형 관계일 수 있지만 경우에 따라 행의 수에 대해 지수 관계(차원)일 수도 있습니다. Basket은 바구니 분석 데이터 마이닝용으로 개발된 Apriori 알고리즘을 기준으로 합니다. 

더 이상 사용되지 않는 `evaluate basket` 구문을 대체합니다.

**반환**

바구니는 행의 비율 임계값(기본값: 0.05) 위에 나타나는 모든 빈번한 패턴을 반환합니다. 각 패턴은 결과의 행으로 표시됩니다.

첫 번째 열은 세그먼트 ID입니다. 다음 두 열은 패턴에 의해 캡처되는 원래 쿼리 중에서 행의 개수 및 백분율입니다. 나머지 열은 원래 쿼리에서 가져오며, 해당 값은 열의 특정 값 또는 와일드카드 값(기본적으로 null이며, 변수 값을 의미함)입니다.

**인수(모두 선택 사항)**

예: `T | evaluate basket_v2([Threshold, WeightColumn, MaxDimensions, CustomWildcard, CustomWildcard, ...])`

모든 인수는 선택적이지만 다음 순서여야 합니다. 기본값을 사용해야 함을 나타내려면 물결표 문자 '~'를 사용합니다(아래 예제 참조).

* 임계값: 0.015 < *double* < 1(기본값: 0.05) 
  
    자주 나오는 것으로 간주되는 행의 최소 비율을 설정합니다(비율이 더 작은 패턴은 반환되지 않음).
  
    예: `T | evaluate basket_v2(0.02)`
* 가중치 열 *-column_name*
  
    지정된 가중치(기본적으로 각 행의 가중치는 '1'임)에 따라 입력의 각 행을 고려합니다. 이 인수는 숫자 열의 이름이어야 합니다(예: int, long, real). 이미 각 행에 포함되어 있는 데이터의 계정 샘플링 또는 버킷팅/집계가 가중치 열의 일반적인 용도로 간주됩니다.
  
    예: `T | evaluate basket_v2('~', sample_Count)`
* 최대 크기: 1 < *int*(기본값: 5)
  
    쿼리 런타임의 줄이기 위해 기본적으로 제한되는 바구니당 상관 관계가 없는 차원의 최대 수를 설정합니다.

    예제: `T | evaluate basket_v2('~', '~', 3)`
* 사용자 지정 와일드카드 형식: *형식별 임의 값*
  
    현재 패턴이 이 열을 제한하지 않음을 나타내는 특정 형식에 대한 와일드카드 값을 결과 테이블에 설정합니다. 기본값은 null이고, 문자열의 경우 기본값은 빈 문자열입니다. 기본값이 데이터의 실행 가능한 값인 경우 다른 와일드카드 값을 사용해야 합니다(예: *).

    예: `T | evaluate basket_v2('~', '~', '~', '*', int(-1), double(-1), long(0), datetime(1900-1-1))`

**예**

``` 
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State, EventType, Damage, DamageCrops
| evaluate basket_v2(0.2)
```
결과

|SegmentId|개수|백분율|시스템 상태|EventType|피해|DamageCrops
----------|-----|-------|-----|---------|------|-----------
0|4574|77.7|||아니요|0
1|2278|38.7||우박|아니요|0
2|5675|96.4||||0
3|2371|40.3||우박||0
4|1279|21.7||뇌우를 동반한 바람||0
5|2468|41.9||우박|||
6|1310|22.3|||예||
7|1291|21.9||뇌우를 동반한 바람||

사용자 지정 와일드카드가 사용된 예
```
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State, EventType, Damage, DamageCrops
| evaluate basket_v2(0.2, '~', '~', '*', int(-1))
```
결과

|SegmentId|개수|백분율|시스템 상태|EventType|피해|DamageCrops
----------|-----|-------|-----|---------|------|-----------
0|4574|77.7|\*|\*|아니요|0
1|2278|38.7|\*|우박|아니요|0
2|5675|96.4|\*|\*|\*|0
3|2371|40.3|\*|우박|\*|0
4|1279|21.7|\*|뇌우를 동반한 바람|\*|0
5|2468|41.9|\*|우박|\*|-1|
6|1310|22.3|\*|\*|예|-1|
7|1291|21.9|\*|뇌우를 동반한 바람|\*|-1|

#### <a name="evaluate-diffpatterns-deprecated"></a>evaluate diffpatterns(사용되지 않음)
**이 버전의 diffpatterns 플러그 인은 사용되지 않습니다. 새로운 [diffpatterns](#evaluate-diffpatterns_v2) 플러그 인 구문을 사용하세요.**
requests | evaluate diffpatterns(“split=success”)

Diffpatterns는 같은 구조의 두 데이터 집합 간 차이를 식별합니다. 인시던트 시의 요청 로그와 정상 요청 로그를 예로 들 수 있습니다. Diffpatterns는 오류를 분석하는 데 도움을 주기 위해 개발되었으나(예: 지정된 시간 내에 오류를 비오류와 비교) 동일한 구조의 모든 두 데이터 집합 간의 차이를 발견할 수도 있습니다. 

**구문**

`T | evaluate diffpatterns("split=` *BinaryColumn* `" [, arguments] )`

**반환**

Diffpatterns는 두 집합에 있는 데이터의 서로 다른 부분을 캡처하는 패턴 (일반적으로 작은)(예: 첫 번째 데이터 집합에서는 높은 비율의 행을 캡처하고, 두 번째 집합에서는 낮은 비율의 행을 캡처하는 패턴) 집합을 반환합니다. 각 패턴은 결과의 행으로 표시됩니다.

처음 네 열은 개수 및 각 집합의 패턴에 의해 캡처되는 원래 쿼리에서 행의 비율, 다섯 번째 열은 두 집합 간의 차이(절대 백분율 포인트)입니다. 나머지 열은 원래 쿼리에서 가져오며, 해당 값은 열의 특정 값 또는 *(변수 값 의미)입니다. 

패턴은 분리되어 있지 않습니다. 겹칠 수도 있으며 일반적으로 모든 원본 행을 포함하지는 않습니다. 일부 행은 어떤 패턴에도 속하지 않을 수 있습니다.

**팁**

* 입력 파이프에 where 및 project를 사용하여 관심 있는 데이터 부분만 남깁니다.
* 관심 있는 행을 찾으면 where 필터에 특정 값을 추가하여 추가로 드릴인투할 수 있습니다.

**인수**

* `split=` *열 이름* (필수)
  
    열은 정확하게 두 개의 값을 가져야 합니다. 필요한 경우 다음과 같은 열을 만듭니다.
  
    `requests | extend fault = toint(resultCode) >= 500` <br/>
    `| evaluate diffpatterns("split=fault")`
* `target=` *string*
  
    알고리즘에 대상 데이터 집합에서 더 큰 백분율 값을 갖는 패턴만 찾도록 합니다. 대상은 분할된 열의 두 값 중 하나여야 합니다.
  
    `requests | evaluate diffpatterns("split=success", "target=false")`
* `threshold=` *0.015<double<1* (기본값: 0.05) 
  
    두 집합 간의 최소 패턴(비율) 차이를 설정합니다.
  
    `requests | evaluate diffpatterns("split=success", "threshold=0.04")`
* `output=minimize | all`
  
    결과의 형식입니다. Count 및 Percent 열은 결과에 항상 표시됩니다. 
  
  * `minimize` - 결과에 '*'가 있는 열만 필터링합니다.
  * `all` - 입력의 모든 열이 출력입니다.
* `weight_column=` *column_name*
  
    지정된 가중치(기본적으로 각 행의 가중치는 '1'임)에 따라 입력의 각 행을 고려합니다. 이미 각 행에 포함되어 있는 데이터의 계정 샘플링 또는 버킷팅/집계가 가중치 열의 일반적인 용도로 간주됩니다.
  
    `requests | evaluate autocluster("weight_column=itemCount")`

<a name="evaluate-diffpatterns_v2"></a>
#### <a name="evaluate-diffpatternsv2"></a>evaluate diffpatterns_v2
'T | evaluate diffpatterns_v2(splitColumn)`

Diffpatterns는 같은 구조체의 두 데이터 집합을 비교하고 두 데이터 집합 간의 차이를 결정하는 불연속 특성(차원)의 패턴을 찾습니다. Diffpatterns는 오류를 분석하는 데 도움을 주기 위해 개발되었으나(예: 지정된 시간 내에 오류를 비오류와 비교) 동일한 구조의 모든 두 데이터 집합 간의 차이를 발견할 수도 있습니다. Diffpatterns 알고리즘은 개발자 분석 연구 팀(KustoML@microsoft.com)에서 개발했습니다.

이 플러그 인은 사용되지 않는 diffpatterns 플러그 인 구문을 바꿉니다.

**구문**

`T | evaluate diffpatterns_v2(SplitColumn, SplitValueA, SplitValueB [, arguments] )`

**반환**

Diffpatterns는 두 집합에 있는 데이터의 서로 다른 부분을 캡처하는 패턴 (일반적으로 작은)(예: 첫 번째 데이터 집합에서는 높은 비율의 행을 캡처하고, 두 번째 집합에서는 낮은 비율의 행을 캡처하는 패턴) 집합을 반환합니다. 각 패턴은 결과의 행으로 표시됩니다.
첫 번째 열은 세그먼트 ID입니다. 다음 네 열은 개수 및 각 집합의 패턴에 의해 캡처되는 원래 쿼리에서 행의 비율, 여섯 번째 열은 두 집합 간의 차이(절대 백분율 포인트)입니다. 나머지 열은 원래 쿼리에서 가져옵니다.
각 패턴에 대해 패턴에 설정되지 않은 열(즉, 특정 값에 대한 제한 없음)에는 기본적으로 null인 와일드카드 값이 포함됩니다(아래의 인수 섹션에서 수동으로 와일드카드를 변경하는 방법 참조).
패턴은 분리되어 있지 않습니다. 겹칠 수도 있으며 일반적으로 모든 원본 행을 포함하지는 않습니다. 일부 행은 어떤 패턴에도 속하지 않을 수 있습니다.

**팁**

입력 파이프에 where 및 project를 사용하여 관심 있는 데이터 부분만 남깁니다.
관심 있는 행을 찾으면 `where` 필터에 특정 값을 추가하여 추가로 드릴인투할 수 있습니다.

**필수 인수**

`T | evaluate diffpatterns_v2(SplitColumn, SplitValueA, SplitValueB [, WeightColumn, Threshold, MaxDimensions, CustomWildcard, ...])` 

- SplitColumn - *column_name* 

알고리즘에 쿼리를 데이터 집합으로 분할하는 방법을 알려줍니다. SplitValueA 및 SplitValueB 인수(아래 참조)에 지정된 값에 따라 알고리즘은 쿼리를 두 개의 데이터 집합 “A” 및 “B”로 분할하고 둘 사이의 차이점을 분석합니다. 따라서 분할 열은 둘 이상의 고유 값을 가져야 합니다.

- SplitValueA - *string*

지정된 SplitColumn에 있는 값 중 하나를 문자열로 나타낸 것입니다. 해당 SplitColumn에 이 값이 있는 모든 행은 데이터 집합 “A”로 간주됩니다.

- SplitValueB - *string*

지정된 SplitColumn에 있는 값 중 하나를 문자열로 나타낸 것입니다. 해당 SplitColumn에 이 값이 있는 모든 행은 데이터 집합 “B”로 간주됩니다.

**예제**

```
T | extend splitColumn=iff(request_responseCode == 200, "Success" , "Failure") | evaluate diffpatterns_v2(splitColumn, "Success","Failure")
```
**선택적 인수**

다른 모든 인수는 선택 사항이지만, 아래와 같은 순서여야 합니다. 기본값을 사용해야 함을 나타내려면 문자열 물결표 값 '~'을 사용합니다(아래 예 참조).

- WeightColumn - *column_name*

지정된 가중치(기본적으로 각 행의 가중치는 '1'임)에 따라 입력의 각 행을 고려합니다. 이 인수는 숫자 열의 이름이어야 합니다(예: int, long, real). 이미 각 행에 포함되어 있는 데이터의 계정 샘플링 또는 버킷팅/집계가 가중치 열의 일반적인 용도로 간주됩니다.

**예제**
```
T | extend splitColumn=iff(request_responseCode == 200, "Success" , "Failure") | evaluate diffpatterns_v2(splitColumn, "Success","Failure", sample_Count)
```
- 임계값 - 0.015 < double < 1 [기본값: 0.05]

두 집합 간의 최소 패턴(비율) 차이를 설정합니다.

**예제**
```
T | extend splitColumn = iff(request_responseCode == 200, "Success" , "Failure") | evaluate diffpatterns_v2(splitColumn, "Success","Failure", "~", 0.04)
```
- MaxDimensions - 0 < int [기본값: unlimited]

결과 패턴당 상관 관계가 없는 차원의 최대 수를 설정하여 제한을 지정함으로써 쿼리 런타임을 줄입니다.

**예제**
```
T | extend splitColumn = iff(request_responseCode == 200, "Success" , "Failure") | evaluate diffpatterns_v2(splitColumn, "Success","Failure", "~", "~", 3)
```
- CustomWildcard - *any_value_per_type*

현재 패턴이 이 열을 제한하지 않음을 나타내는 특정 형식에 대한 와일드카드 값을 결과 테이블에 설정합니다. 기본값은 null이고, 문자열의 경우 기본값은 빈 문자열입니다. 기본값이 데이터의 실행 가능한 값인 경우 다른 와일드카드 값을 사용해야 합니다(예: *). 아래 예제를 참조하세요.

**예제**
```
T | extend splitColumn = iff(request_responseCode == 200, "Success" , "Failure") | evaluate diffpatterns_v2(splitColumn, "Success","Failure", "~", "~", "~", int(-1), double(-1), long(0), datetime(1900-1-1))
```

**예제**
```
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , 1 , 0)
| project State , EventType , Source , Damage, DamageCrops
| evaluate diffpatterns_v2(Damage, "0", "1" )
```
**결과**

|SegmentId|CountA|CountB|PercentA|PercentB|DiffAB|시스템 상태|EventType|원본|DamageCrops
----------|------|------|--------|--------|------|-----|---------|------|-----------
0|2278|93|49.8|7.1|42.7||우박||0
1|779|512|17.03|39.08|22.05||뇌우를 동반한 바람|||
2|1098|118|24.01|9.01|15|||숙련된 관찰자|0|
3|136|158|2.97|12.06|9.09|||신문||
4|359|214|7.85|16.34|8.49||갑작스러운 홍수|||
5|50|122|1.09|9.31|8.22|아이오와||||
6|655|279|14.32|21.3|6.98|||사법 기관||
7|150|117|3.28|8.93|5.65||홍수|||
8|362|176|7.91|13.44|5.52|||긴급 상황 관리자||

#### <a name="evaluate-extractcolumns"></a>evaluate extractcolumns
     exceptions | take 1000 | evaluate extractcolumns("details=json") 

Extractcolumns는 형식을 기준으로 (반)구조화된 열에서 동적으로 추출된 여러 단순 열로 테이블을 채우는 데 사용됩니다. 현재 json 열만 지원하며 동적 직렬화 및 문자열 직렬화 형태가 모두 지원됩니다.

* `max_columns=` *int* (기본값: 10) 
  
    새로 추가된 열의 수는 동적이며 아주 커질 수 있으므로(실제로 모든 json 레코드에 포함된 고유 키의 수) 제한해야 합니다. 새 열은 빈도에 따라 내림차순으로 정렬되고 max_columns까지 테이블에 추가됩니다.
  
    `T | evaluate extractcolumns("json_column_name=json", "max_columns=30")`
* `min_percent=` *double* (기본값: 10.0) 
  
    빈도가 min_percent보다 낮은 열을 무시하여 새 열을 제한하는 또 다른 방법입니다.
  
    `T | evaluate extractcolumns("json_column_name=json", "min_percent=60")`
* `add_prefix=` *bool* (기본값: true) 
  
    true인 경우 복합 열의 이름이 추출된 열 이름에 접두사로 추가됩니다.
* `prefix_delimiter=` *문자열* (기본값: "_") 
  
    add_prefix=true인 경우 이 매개 변수는 새 열 이름을 연결하는 데 사용할 구분 기호를 정의합니다.
  
    `T | evaluate extractcolumns("json_column_name=json",` <br/>
    `"add_prefix=true", "prefix_delimiter=@")`
* `keep_original=` *bool* (기본값: false) 
  
    true인 경우 원래(json) 열이 출력 테이블에 유지됩니다.
* `output=query | table` 
  
    결과의 형식입니다. 
  
  * `table` - 출력은 수신된 열에서 지정된 입력 열을 뺀 후 입력 열에서 추출된 새 열을 더한 것과 동일한 테이블입니다.
  * `query` - 출력은 결과를 테이블로 얻기 위해 만드는 쿼리를 나타내는 문자열입니다. 

### <a name="extend-operator"></a>extend 연산자
     T | extend duration = stopTime - startTime

하나 이상의 계산된 열을 테이블에 추가합니다. 

**구문**

    T | extend ColumnName = Expression [, ...]

**인수**

* *T:* 입력 테이블입니다.
* *ColumnName:* 추가할 열의 이름입니다. [이름](#names)은 대/소문자를 구분하며 영문자, 숫자 또는 '_' 문자를 포함할 수 있습니다. `['...']` 또는 `["..."]`을(를) 사용하여 다른 문자가 있는 키워드 또는 이름을 따옴표로 묶습니다.
* *Expression:* 기존 열에 대한 계산입니다.

**반환**

추가 열이 지정된 입력된 테이블의 복사본.

**팁**

* 일부 열의 삭제 또는 이름을 바꾸려는 경우에도 [`project`](#project-operator) 를 대신 사용합니다.
* 단지 긴 식에서 더 짧은 이름을 얻기 위해 `extend` 를 사용하지 마세요. `...| extend x = anonymous_user_id_from_client | ... func(x) ...` 
  
    테이블의 기본 열은 인덱싱되었으며, 새 이름은 인덱싱되지 않은 추가 열을 정의하므로 쿼리가 더 느리게 실행될 가능성이 있습니다.

**예제**

```AIQL
traces
| extend
    Age = now() - timestamp
```

### <a name="find-operator"></a>find 연산자

    find in (Table1, Table2, Table3) where id=="a string"
    find in (Table1, Table2, Table3) where id=="a string" project column1, column2
    find in (Table1, Table2, Table3) where * has "a string"
    find in (Table1, Table2, Table3) where appName in ("string 1", "string 2", "string 3")

테이블 집합 사이에서 조건자와 일치하는 행을 찾습니다.

**구문**

    find in (Table1, ...) 
    where Predicate 
    [project Column1, ...]

**인수**

* *Table1* 테이블 이름 또는 쿼리. let 정의 테이블일 수 있으나 함수는 아닙니다. 테이블 이름이 쿼리보다 더 효율적입니다.
* *조건자* 지정된 테이블의 모든 행에 대해 계산되는 부울 식입니다.
 * 문자열 비교 시 열 이름 대신 "*"를 사용할 수 있습니다.
* *Column1* `project` 옵션을 사용하여 출력에 항상 표시되어야 하는 열을 지정할 수 있습니다. 

**결과**

기본적으로 출력 테이블에는 다음이 포함됩니다.

* `source_` - 각 행의 원본 테이블을 나타내는 표시입니다. 이 열에 표시되는 이름을 지정하려면 각 테이블 식의 끝에 [as](#as-operator)를 사용합니다.
* 조건자에서 명시적으로 언급된 열
* 모든 입력 테이블에 공통되는 비어 있지 않은 열
* `pack_` - 다른 열에서 데이터를 포함하는 속성 모음입니다.

이 형식은 입력 데이터 또는 조건자가 변경되면 달라질 수 있습니다. 고정된 열 집합을 지정하려면 `project`를 사용합니다.

**예제**

가용성 테스트 및 로봇의 경우를 제외한 모든 요청 및 예외를 가져옵니다.

```AIQL

    find in (requests, exceptions) where isempty(operation_SyntheticSource)
```

가용성 테스트 및 로봇의 경우를 제외한 UK의 모든 요청 및 예외를 찾습니다.

```AIQL

    let requk = requests
    | where client_CountryOrRegion == "United Kingdom";
    let exuk = exceptions
    | where client_CountryOrRegion == "United Kingdom";
    find in (requk, exuk) where isempty(operation_SyntheticSource)
```

모든 필드에 용어 'test'가 포함된 가장 최근 원격 분석을 찾습니다.

```AIQL

    find in (traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions) 
    where * has 'test' 
    | top 100 by timestamp desc
```

**성능 팁**

* 시간 기반 조건을 `where` 조건자에 추가합니다.
* 인라인으로 쿼리를 작성하지 않고 `let` 절을 사용합니다.

### <a name="getschema-operator"></a>getschema 연산자

   T | getschema
   
입력된 테이블의 열 이름 및 형식을 보여 주는 테이블이 산출됩니다.

```AIQL
requests
| project appId, appName, customDimensions, duration, iKey, itemCount, success, timestamp 
| getschema 
```

![getschema의 결과](./media/app-insights-analytics-reference/getschema.png)

### <a name="join-operator"></a>join 연산자
    Table1 | join (Table2) on CommonColumn

지정된 열의 값을 일치시켜 두 테이블의 행을 병합합니다.

**구문**

    Table1 | join [kind=Kind] (Table2) on CommonColumn [, ...]

**인수**

* *Table1* - join의 '좌변'입니다.
* *Table2* - join의 '우변'입니다. 테이블을 출력하는 중첩 된 쿼리 식일 수 있습니다.
* *CommonColumn* - 두 테이블에 같은 이름을 가진 열입니다.
* *Kind* - 두 테이블의 행을 일치시키는 방법을 지정합니다.

**반환**

다음을 포함하고 있는 테이블:

* 일치하는 키를 포함하여 두 테이블 각각의 모든 열에 대한 열. 이름 충돌이 있는 경우 우변의 열 이름이 자동으로 바뀝니다.
* 입력된 테이블 간의 모든 일치 항목에 대한 행. 일치 항목은 한 테이블에서 선택된 행이며 모든 `on` 필드에 대해 다른 테이블의 행과 같은 값을 가지고 있습니다. 
* `Kind` 지정 안 함 또는 `= innerunique`
  
    `on` 키의 각 값에 대해 좌변의 한 개의 행만 일치합니다. 출력은 오른쪽부터 이 행과 행 일치 각각에 대해 한 개의 행을 포함하고 있습니다.
* `kind=inner`
  
     왼쪽 및 오른쪽에서 일치하는 행의 모든 조합에 대해 한 개의 출력 행이 있습니다.
* `kind=leftouter`(또는 `kind=rightouter` 또는 `kind=fullouter`)
  
     내부 일치 외에도 일치 항목이 없더라도 왼쪽(및/또는 오른쪽)의 모든 행에 대한 행이 있습니다. 이 경우 일치하지 않는 출력 셀에는 null 값이 포함되어 있습니다.
* `kind=leftanti`
  
     오른쪽에서 일치 항목이 없는 좌변의 모든 레코드를 반환합니다. 결과 테이블에는 좌변의 열만 있습니다. 
* `kind=leftsemi`(또는 `leftantisemi`)

    오른쪽 테이블에 일치 항목이 있는(없는) 경우 왼쪽 테이블의 행을 반환합니다. 결과에 오른쪽의 데이터는 포함되지 않습니다.


**팁**

결과 테이블에는 64MB의 제한이 적용됩니다.

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
    | project City, ActivityId, StartTime, StopTime, Duration=StopTime-StartTime

```


### <a name="limit-operator"></a>limit 연산자
     T | limit 5

입력된 테이블에서 지정한 수까지의 행을 반환합니다. 레코드가 반환된다는 보장은 없습니다. (특정 레코드를 반환하려면 [`top`](#top-operator)을(를) 사용합니다.)

**별칭** `take`

**구문**

    T | limit NumberOfRows


**팁**

`Take` 은(는) 대화형으로 작업하는 경우 결과의 샘플을 참조하는 간단하고 효율적인 방법입니다. 특정 행을 생성하거나 행을 특정 순서로 생성한다는 보장이 없다는 데 유의하십시오.

`take`을(를) 사용하지 않더라도 클라이언트에 반환되는 행 수에 대한 암시적 제한이 있습니다. 이 제한을 올리려면 `notruncation` 클라이언트 요청 옵션을 사용합니다.

### <a name="make-series-operator"></a>make-series 연산자

집계를 수행합니다. [summarize](#summarize-operator)와 달리 각 그룹의 출력 행이 하나 있습니다. 결과 열에서 각 그룹의 값은 배열로 압축됩니다. 

**구문**

    T | 
    make-series [Column =] Aggregation default = DefaultValue [, ...] 
    on AxisColumn in range(start, stop, step) 
    by [Column =] GroupExpression [, ...]


**인수**

* *Column:* 결과 열에 대한 선택적 이름입니다. 기본적으로 식에서 파생된 이름입니다.
* *DefaultValue:* 행에 AxisColumn 및 GroupExpression의 특정 값이 없는 경우 결과에서 배열의 해당 요소가 DefaultValue로 할당됩니다. 
* *Aggregation:* [집계 함수](#aggregations)를 사용하는 숫자 식입니다. 
* *AxisColumn:* 시리즈가 정렬되는 열입니다. 타임 라인으로 간주할 수 있지만 모든 숫자 형식이 허용 됩니다.
*start, stop, step:* 모든 행에 대한 AxisColumn의 값 목록을 정의합니다. 다른 모든 결과 집계 열은 길이가 같은 배열을 갖습니다. 
* *GroupExpression:* 고유 값 집합을 제공하는 열에 대한 식입니다. GroupExpression의 각 값에 대한 출력에 행이 하나씩 있습니다. 일반적으로 이미 제한된 값 집합을 제공하는 열 이름입니다. 

**팁**

결과 배열은 해당 요약 작업과 동일한 방식으로 분석 차트에 렌더링됩니다.

**예제**

```AIQL
requests
| make-series sum(itemCount) default=0, avg(duration) default=0
  on timestamp in range (ago(7d), now(), 1d)
  by client_City
```

![make-series의 결과](./media/app-insights-analytics-reference/make-series.png)

### <a name="mvexpand-operator"></a>mvexpand 연산자
    T | mvexpand listColumn 

동적 형식(JSON)의 셀에서 각 항목이 별도의 행을 가진 목록을 확장합니다. 확장된 행의 모든 다른 셀이 중복됩니다. 

(반대 기능을 수행하는 [`summarize makelist`](#summarize-operator)을(를) 참조하세요.)

**예제**

입력 테이블이 다음과 같다고 가정:

| A:int | B:string | D:dynamic |
| --- | --- | --- |
| 1 |"hello" |{"key":"value"} |
| 2 |"world" |[0,1,"k","v"] |

    mvexpand D

결과:

| A:int | B:string | D:dynamic |
| --- | --- | --- |
| 1 |"hello" |{"key":"value"} |
| 2 |"world" |0 |
| 2 |"world" |1 |
| 2 |"world" |"k" |
| 2 |"world" |"v" |

**구문**

    T | mvexpand  [bagexpansion=(bag | array)] ColumnName [limit Rowlimit]

    T | mvexpand  [bagexpansion=(bag | array)] [Name =] ArrayExpression [to typeof(Typename)] [limit Rowlimit]

**인수**

* *ColumnName:* 결과적으로 명명된 열의 배열이 여러 행으로 확장됩니다. 
* *ArrayExpression:* 배열을 생성하는 식입니다. 이 양식을 사용하면 새 열이 추가되며 기존 열은 보존됩니다.
* *Name:* 새 열에 대한 이름입니다.
* *Typename:* 확장된 식을 특정 형식으로 캐스트합니다.
* *RowLimit:* 각각의 원래 행에서 생성되는 최대 행 수입니다. 기본값은 128입니다.

**반환**

명명된 열의 배열 또는 배열 식에 있는 각 값에 대한 여러 행.

확장된 열은 언제나 동적 형식을 가집니다. 값을 계산하거나 집계하려는 경우 `todatetime()` 또는 `toint()`와(과) 같은 캐스트를 사용합니다.

속성 모음 확장의 두 가지 모드가 지원됩니다.

* `bagexpansion=bag`: 속성 모음이 단일 항목 속성 모음으로 확장됩니다. 이는 기본 확장입니다.
* `bagexpansion=array`: 속성 모음이 두 요소로 이루어진 `[`*key*`,`*value*`]` 배열 구조로 확장되며 키 및 값에 대한 균일한 액세스가 가능합니다(또한 예를 들어 속성 이름에 대해 고유 카운트 집계 실행).

**예**

    exceptions | take 1
    | mvexpand details[0]

예외 레코드를 세부 정보 필드의 각 항목에 대한 행으로 분할합니다.

### <a name="parse-operator"></a>parse 연산자
    T | parse "I got 2 socks for my birthday when I was 63 years old"
    with * "got" counter:long " " present "for" * "was" year:long *


    T | parse kind=relaxed
          "I got no socks for my birthday when I was 63 years old"
    with * "got" counter:long " " present "for" * "was" year:long *

    T |  parse kind=regex "I got socks for my 63rd birthday"
    with "(I|She) got " present " for .*?" year:long *

문자열에서 값을 추출합니다. simple 또는 보통의 식 일치를 사용할 수 있습니다.

**구문**

    T | parse [kind=regex|relaxed] SourceText
        with [Match | Column [: Type [*]] ]  ...

**인수**

* `T`: 입력 테이블입니다.
* `kind`: 
  * `simple`(기본값): `Match` 문자열은 일반 문자열입니다.
  * `relaxed`: 텍스트를 열 형식으로 구문 분석하지 않으면 열은 null로 설정되고 구문 분석이 계속됩니다. 
  * `regex`: `Match` 문자열은 정규식입니다.
* `Text`: 문자열로 계산하거나 변환될 수 있는 열 또는 기타 식입니다.
* *Match:* 문자열의 다음 부분과 일치시킨 후 취소합니다.
* *Column:* 문자열의 다음 부분을 이 열에 할당합니다. 열이 존재하지 않는 경우 생성됩니다.
* *Type:* 문자열의 다음 부분을 지정된 형식(예: int, date, double)으로 구문 분석합니다. 

**반환**

입력된 테이블, 열 목록에 따라 확장됩니다.

`with` 절의 요소는 원본 텍스트에 대해 차례로 일치합니다. 각 요소는 소스 텍스트의 청크를 분할합니다. 

* 리터럴 문자열 또는 정규식은 일치 항목의 길이만큼 일치 커서를 이동합니다.
* regex 구문 분석 시 정규식은 최소화 연산자 '?'를 사용하여 다음 일치 항목으로 가능한 한 빨리 이동합니다.
* 형식이 있는 열 이름은 텍스트를 지정된 형식으로 구문 분석합니다. kind=relaxed가 아니면 실패한 구문 분석은 전체 패턴과 일치 항목을 무효화합니다.
* 형식이 없거나 'string' 형식이 있는 열 이름은 다음 일치 항목에 가져올 최소 수의 문자를 복사합니다.
* ‘ * ’ 다음 일치 항목에 가져올 최소 수의 문자를 건너뜁니다. 패턴의 시작과 끝, 문자열이 아닌 형식 뒤 또는 문자열 일치 항목 중간에 ‘*’를 사용할 수 있습니다.

구문 분석 패턴의 모든 요소가 바르게 일치해야 하며 그렇지 않으면 결과가 생성되지 않습니다. 이 규칙의 예외로, kind=relaxed일 때 형식화된 변수의 구문 분석에 실패하면 나머지 구문 분석이 진행됩니다.

**예**

*Simple:*

```AIQL

// Test without reading a table:
 range x from 1 to 1 step 1 
 | parse "I got 2 socks for my birthday when I was 63 years old" 
    with 
     *   // skip until next match
     "got" 
     counter: long // read a number
     " " // separate fields
     present // copy string up to next match
     "for" 
     *  // skip until next match
     "was" 
     year:long // parse number
     *  // skip rest of string
```

| x | counter | present | Year |
| --- | --- | --- | --- |
| 1 |2 |socks |63 |

*Relaxed:*

입력에 모든 형식화된 열에 대한 올바른 일치 항목이 있는 경우 relaxed 구문 분석은 simple 구문 분석과 동일한 결과를 생성합니다. 하지만 형식화된 열 중 하나라도 제대로 구문 분석되지 않으면 relaxed 구문 분석은 나머지 패턴을 계속 처리하지만 simple 구문 분석은 중지되고 어떠한 결과도 생성하지 않습니다.

```AIQL

// Test without reading a table:
 range x from 1 to 1 step 1 
 | parse kind="relaxed"
        "I got several socks for my birthday when I was 63 years old" 
    with 
     *   // skip until next match
     "got" 
     counter: long // read a number
     " " // separate fields
     present // copy string up to next match
     "for" 
     *  // skip until next match
     "was" 
     year:long // parse number
     *  // skip rest of string
```


| x | present | Year |
| --- | --- | --- |
| 1 |socks |63 |

*Regex:*

```AIQL

// Run a test without reading a table:
range x from 1 to 1 step 1 
// Test string:
| extend s = "Event: NotifySliceRelease (resourceName=Scheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 07:31, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 06:20:00 ) }" 
// Parse it:
| parse kind=regex s 
  with ".*?=" resource 
       ", total.*?sliceNumber=" slice:long *
       "lockTime=" lock
       ",.*?releaseTime=" release 
       ",.*?previousLockTime=" previous:date 
       @".*\)" *
| project-away x, s
```

| resource | slice | lock | 릴리스 | previous |
| --- | --- | --- | --- | --- |
| 스케줄러 |16 |02/17/2016 07:31:00 |02/17/2016 08:41 |2016-02-17T06:20:00Z |

### <a name="project-operator"></a>project 연산자
    T | project cost=price*quantity, price

포함, 이름 바꾸기 또는 삭제할 열을 선택하고 새 계산된 열을 삽입합니다. 결과의 열 순서는 인수 순서에 의해 지정됩니다. 인수에 지정된 열만이 결과에 포함되며: 입력의 다른 열은 삭제됩니다.  ( `extend`참조)

**구문**

    T | project ColumnName [= Expression] [, ...]

**인수**

* *T:* 입력 테이블입니다.
* *ColumnName:* 출력에 나타낼 열 이름입니다. *Expression*이 없는 경우 해당 이름의 열이 입력에 나타나야 합니다. [이름](#names)은 대/소문자를 구분하며 영문자, 숫자 또는 '_' 문자를 포함할 수 있습니다. `['...']` 또는 `["..."]`을(를) 사용하여 다른 문자가 있는 키워드 또는 이름을 따옴표로 묶습니다.
* *Expression:* 입력 열을 참조하는 선택적 스칼라 식입니다. 
  
    입력의 기존 열과 같은 이름을 가진 새 계산된 열을 반환하는 것이 올바릅니다.

**반환**

인수로 이름 지정된 열 및 입력 테이블만큼의 행을 가진 테이블입니다.

**예제**

다음 예제에서는 `project` 연산자를 사용하여 수행할 수 있는 여러 종류의 조작을 보여 줍니다. 입력 테이블 `T`에는 `int` 형식의 열로 `A`, `B`, `C`의 3개가 있습니다. 

```AIQL
T
| project
    X=C,               // Rename column C to X
    A=2*B,             // Calculate a new column A from the old B
    C=strcat("-",tostring(C)), // Calculate a new column C from the old C
    B=2*B,              // Calculate a new column B from the old B
    ['where'] = client_City // rename, using a keyword as a column name
```

### <a name="project-away-operator"></a>project-away 연산자
    T | project-away column1, column2, ...

지정된 열을 제외합니다. 결과는 이름을 지정하는 것을 제외하고 모든 입력 열을 포함합니다.

### <a name="range-operator"></a>range 연산자
    range LastWeek from ago(7d) to now() step 1d

단일 열로 이루어진 값 테이블을 생성합니다. 참고로 파이프라인 입력이 없습니다. 

| 전주 |
| --- |
| 2015-12-05 09:10:04.627 |
| 2015-12-06 09:10:04.627 |
| ... |
| 2015-12-12 09:10:04.627 |

**구문**

    range ColumnName from Start to Stop step Step

**인수**

* *ColumnName:* 출력 테이블의 단일 열 이름입니다.
* *Start:* 출력에서 가장 작은 값입니다.
* *Stop:* 출력에서 생성되는 가장 높은 값입니다(또는 *step*으로 이 값에 대해 단계를 지정한 경우 가장 높은 값에 대한 범위).
* *Step:* 연속된 두 값 사이의 차입니다. 

인수는 숫자, 날짜 또는 시간 간격 값이어야 합니다. 아무 테이블의 열이나 참조할 수는 없습니다. 입력 테이블을 기반으로 범위를 계산하려면 [range *함수*](#range)를 사용하며, [mvexpand 연산자](#mvexpand-operator)와 함께 사용할 수 있습니다. 

**반환**

*ColumnName*이라는 단일 열을 가진 테이블이며, 해당 열의 값은 *Start*, *Start* + *Step*, ... *Stop*까지 입니다.

**예제**  

```AIQL
range Steps from 1 to 8 step 3
```

`Steps`라는 단일 열을 가진 테이블이며, 해당 열의 형식은 `long`, 해당 열의 값은 `1`, `4`, `7`입니다.

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

`range` 연산자를 사용하여 작은 임시 차원 테이블을 생성할 수 있는 방법을 보여 주며 이 테이블은 원본 데이터에 값이 없는 경우 0을 도입하기 위해 사용됩니다.

### <a name="reduce-operator"></a>reduce 연산자
    exceptions | reduce by outerMessage

유사한 레코드에 대해 그룹화를 시도합니다. 각 그룹에 대해 연산자는 그룹을 가장 잘 설명하는 것으로 여겨지는 `Pattern` 및 그룹의 레코드 `Count`을(를) 출력합니다.

![](./media/app-insights-analytics-reference/reduce.png)

**구문**

    T | reduce by  ColumnName [ with threshold=Threshold ]

**인수**

* *ColumnName:* 검사할 열입니다. 문자열 형식이어야 합니다.
* <seg>
  *Threshold:* 범위 {0..1}의 값입니다.</seg> 기본값은 0.001입니다. 큰 입력의 경우 임계값이 작아야 합니다. 

**반환**

두 개의 열 `Pattern` 및 `Count`입니다. 대부분의 경우 패턴은 열의 전체 값입니다. 경우에 따라 일반적인 용어를 식별하고 변수 부분을 '*'로 바꿉니다.

예를 들어 `reduce by city`의 결과는 포함할 수도 있습니다. 

| 패턴 | 개수 |
| --- | --- |
| San * |5182 |
| Saint * |2846 |
| Moscow |3726 |
| \* -on- \* |2730 |
| 파리 |27163 |

### <a name="render-directive"></a>render 지시문
    T | render [ table | timechart  | barchart | piechart | areachart | scatterchart ] 
        [kind= default|stacked|stacked100|unstacked]

Render는 프레젠테이션 계층에 테이블 표시 방법을 지시합니다. 파이프의 마지막 요소여야 합니다. 화면의 컨트롤을 사용하는 대신 사용할 수 있는 편리한 방법으로, 특정 프레젠테이션 방법으로 쿼리를 저장할 수 있습니다.

일부 유형의 차트에서 `kind`는 추가 옵션을 제공합니다. 예를 들어 `stacked` 막대형 차트는 각 막대를 선택한 차원으로 분할하여 차원의 여러 다른 값이 합계에서 차지하는 비율을 보여 줍니다. `stacked100` 차트에서 모든 막대의 높이는 100%로 동일하므로 상대적인 비율을 비교할 수 있습니다.


### <a name="restrict-clause"></a>restrict 절
뒤에 나오는 연산자에 사용할 수 있는 테이블 이름 집합을 지정합니다. 예:

    let e1 = requests | project name, client_City;
    let e2 =  requests | project name, success;
    // Exclude predefined tables from the union:
    restrict access to (e1, e2);
    union * |  take 10 

### <a name="sample-operator"></a>sample 연산자

입력 테이블에서 균일하게 분산된 임의 행을 반환합니다.


**구문**

    T | sample NumerOfRows

* *NumberOfRows* 샘플에 반환할 행 수입니다.

**팁**

균일하게 분산된 샘플이 필요하지 않으면 `Take`를 사용합니다.


### <a name="sample-distinct-operator"></a>sample-distinct 연산자

요청된 열의 고유 값을 지정된 수만큼 포함하는 단일 열을 반환합니다. 현재, 균일하게 분산된 샘플을 반환하지 않습니다.

**구문**

    T | sample-distinct NumberOfValues of ColumnName

* *NumberOfValues* 원하는 테이블의 길이입니다.
* *ColumnName* 원하는 열입니다.

**팁**

let 문에 sample-distinct를 추가하여 모집단을 샘플링하고 나중에 in 연산자를 사용하여 필터링할 수 있습니다(예제 참조).
 
단지 샘플이 아닌 상위 값을 원할 경우 top-hitters 연산자를 사용할 수 있습니다.

특정 열 값이 아닌 데이터 행을 샘플링하려면 [sample 연산자](#sample-operator)를 참조하세요.

**예제**

모집단을 샘플링하고 추가 계산을 수행하여 합계가 쿼리 한도를 초과하지 않을지 알아냅니다.

```AIQL
let sampleops = toscalar(requests | sample-distinct 10 of OperationName);
requests | where OperationName in (sampleops) | summarize total=count() by OperationName
```
### <a name="search-operator"></a>search 연산자

여러 테이블 및 열에서 문자열을 검색합니다.

**구문**

    search [kind=case_sensitive] [in (TableName, ...)] SearchToken

    T | search [kind=case_sensitive] SearchToken

    search [kind=case_sensitive] [in (TableName, ...)] SearchPredicate

    T | search [kind=case_sensitive] SearchPredicate

모든 테이블의 모든 열에서 지정된 토큰 문자열의 발생을 찾습니다.
 
* *TableName* 전역적으로(요청, 예외 등) 또는 [let 절](#let-clause)에 의해 정의된 테이블의 이름입니다. r*과 같은 와일드카드를 사용할 수 있습니다.
* *SearchToken:* 단어 단위로 일치해야 하는 토큰 문자열입니다. 후행 와일드 카드를 사용할 수 있습니다. "Amster *"는 "Amsterdam"과 일치하지만 "Amster"와는 일치하지 않습니다.
* *SearchPredicate:* 테이블의 열에 대한 부울 식입니다. 열 이름에 "*"을 와일드 카드로 사용할 수 있습니다.

**예**

```AIQL
search "Amster*"  //All columns, all tables

search name has "home"  // one column

search * has "home"     // all columns

search in (requests, exceptions) "Amster*"  // two tables

requests | search "Amster*"

requests | search name has "home"

```




### <a name="sort-operator"></a>sort 연산자
    T | sort by country asc, price desc

입력 테이블의 행을 하나 이상의 열에 의해 순서대로 정렬합니다.

**별칭** `order`

**구문**

    T  | sort by Column [ asc | desc ] [ , ... ]

**인수**

* *T:* 정렬하기 위해 입력한 테이블입니다.
* *Column*: 정렬 기준이 되는 *T*의 열입니다. 값의 형식은 숫자, 날짜, 시간 또는 문자열이어야 합니다.
* `asc` 오름차순으로, 즉 낮은 값에서 높은 값의 순서로 정렬합니다. 기본값은 `desc`내림차순, 즉 높은 값에서 낮은 값으로 정렬하는 것입니다.

**예제**

```AIQL
Traces
| where ActivityId == "479671d99b7b"
| sort by Timestamp asc
```
특정 `ActivityId`가 지정된 Traces 테이블 내의 모든 행을 타임스탬프 기준으로 정렬하여 반환합니다.

### <a name="summarize-operator"></a>summarize 연산자
입력된 테이블의 내용을 집계하는 테이블을 생성합니다.

    requests
    | summarize count(), avg(duration), makeset(client_City) 
      by client_CountryOrRegion

숫자, 평균 요청 기간 및 각 국가의 도시 집합을 표시하는 테이블입니다. 각 고유 국가에 대한 출력의 행이 있습니다. 출력 열은 개수, 평균 기간, 도시 및 국가를 표시합니다. 모든 다른 입력된 열은 무시됩니다.

    T | summarize count() by price_range=bin(price, 10.0)

[0,10.0], [10.0,20.0] 등의 각 간격에 가격을 가진 항목 수를 표시하는 테이블입니다. 이 예제는 개수에 대한 열 및 가격 범위에 대한 열을 가지고 있습니다. 모든 다른 입력된 열은 무시됩니다.

**구문**

    T | summarize
         [  [ Column = ] Aggregation [ , ... ] ]
         [ by
            [ Column = ] GroupExpression [ , ... ] ]

**인수**

* *Column:* 결과 열에 대한 선택적 이름입니다. 기본적으로 식에서 파생된 이름입니다. [이름](#names)은 대/소문자를 구분하며 영문자, 숫자 또는 '_' 문자를 포함할 수 있습니다. `['...']` 또는 `["..."]`을(를) 사용하여 다른 문자가 있는 키워드 또는 이름을 따옴표로 묶습니다.
* *Aggregation:* 열 이름을 인수로 하는 `count()` 또는 `avg()` 등과 같은 집계 함수에 대한 호출입니다. [집계](#aggregations)를 참조하세요.
* *GroupExpression:* 고유 값 집합을 제공하는 열에 대한 식입니다. 일반적으로 이미 제한된 값 집합을 제공한 열의 이름 또는 숫자나 시간 열을 인수로 하는 `bin()` 입니다. 

`bin()`을(를) 사용하지 않고 숫자 또는 시간 식을 제공할 경우 분석은 시간에 대해 `1h`의 간격 또는 숫자에 대해 `1.0`와(과) 함께 자동으로 이를 적용합니다.

*GroupExpression* 을 제공하지 않으면 전체 테이블이 단일 출력 행에 요약됩니다.

**반환**

입력 행은 `by` 식의 같은 값을 가진 그룹으로 배열됩니다. 그런 다음 지정된 집계 함수를 각 그룹에 대해 계산하여 각 그룹에 대해 한 행을 생성합니다. 결과는 `by` 열 및 계산된 각 집계에 대해 열을 하나 이상 포함하고 있습니다. (일부 집계 함수는 여러 열을 반환합니다.)

결과는 `by` 값의 고유 조합의 수만큼 행이 있습니다. 숫자 값의 범위에 대해 요약하려면 `bin()`을(를) 사용하여 불연속 값으로 범위를 줄입니다.

> [!NOTE]
> 집계와 그룹화 식에 대해 모두 임의 식을 제공할 수 있지만 단순 열 이름을 사용하거나 `bin()`을(를) 숫자 열에 적용하는 것이 더 효율적입니다.

### <a name="table-operator"></a>table 연산자

    table('pageViews')

인수 문자열에 지정된 테이블입니다.

**구문**

    table(tableName)

**인수**

* *tableName:* 문자열입니다. 정적이거나 let 절 결과일 수 있는 테이블의 이름입니다.

**예**

    table('requests');


    let size = (tableName: string) {
        table(tableName) | summarize sum(itemCount)
    };
    size('pageViews');



### <a name="take-operator"></a>take 연산자
[limit](#limit-operator)의 별칭

### <a name="top-operator"></a>top 연산자
    T | top 5 by Name desc nulls first

지정한 열을 기준으로 정렬된 처음 *N* 개 레코드를 반환합니다.

**구문**

    T | top NumberOfRows by Sort_expression [ asc | desc ] [nulls first|nulls last] [, ... ]

**인수**

* *NumberOfRows:* 반환할 *T*의 행 수입니다.
* *Sort_expression:* 행을 정렬하는 기준이 되는 식입니다. 일반적으로 단지 열 이름일 뿐입니다. sort_expression을 두 개 이상 지정할 수 있습니다.
* 선택이 실제로 범위의 "맨 아래"에서 시작되는지 아니면 "맨 위"에서 시작되는지를 제어하기 위해 `asc` 또는 `desc`(기본값)가 나타날 수 있습니다.
* `nulls first` 또는 `nulls last`가 null 값이 나타나는 곳을 컨트롤합니다. `First`은 `asc`에 대한 기본값이며, `last`은 `desc`에 대한 기본값입니다.

**팁**

`top 5 by name`은(는) 외관상으로는 `sort by name | take 5`와(과) 동일하지만 그러나 이는 더 빠르게 실행되며 언제나 정렬된 결과를 반환하는 반면에, `take` 은 그러한 실행과 결과가 보장되지 않습니다.

### <a name="top-nested-operator"></a>top-nested 연산자
    requests
    | top-nested 5 of name by count()
    , top-nested 3 of performanceBucket by count()
    , top-nested 3 of client_CountryOrRegion by count()
    | render barchart

각 수준이 이전 수준에서의 드릴다운에 해당하는 계층적 결과를 생성합니다. 이 연산자는 "상위 5개의 요청은 무엇이며, 각각에 대해 상위 3개의 성능 버킷은 무엇이며, 각각에 대해 요청이 시작된 상위 3개 국가는 어느 것입니까?"와 같은 질문에 답변하는 데 유용합니다.

**구문**

   T | top-nested N of COLUMN by AGGREGATION [, ...]

**인수**

* N:int - 반환하거나 다음 수준으로 전달할 행 개수입니다. N이 5, 3, 3인 세 개 수준의 쿼리에서 총 행 수는 45개가 됩니다.
* COLUMN - 집계를 위한 그룹화 기준 열입니다. 
* AGGREGATION - 각 행 그룹에 적용할 [집계 함수](#aggregations) 입니다. 이러한 집계의 결과에 따라 표시할 상위 그룹이 결정됩니다.

### <a name="union-operator"></a>union 연산자
     Table1 | union Table2, Table3

두 개 이상의 테이블을 사용하며 모든 행을 반환합니다. 

**구문**

    T | union [ kind= inner | outer ] [ withsource = ColumnName ] Table2 [ , ...]  

    union [ kind= inner | outer ] [ withsource = ColumnName ] Table1, Table2 [ , ...]  

**인수**

* *Table1*, *Table2* ...
  * `requests`와(과) 같은 테이블의 이름 또는 [let 절](#let-clause)에 정의된 테이블 또는
  * `(requests | where success=="True")`
  * 와일드 카드를 사용하여 지정한 테이블 집합입니다. 예를 들어 `e*`은(는) 'exceptions' 테이블과 함께 이름이 'e'로 시작하는 이전 let 절에 정의된 모든 테이블의 합집합을 형성합니다.
* `kind`: 
  * `inner` - 결과에는 모든 입력 테이블에 공통인 열의 하위 집합이 있습니다.
  * `outer` - 결과에는 입력에서 발생하는 모든 열이 있습니다. 입력 행에 의해 정의되지 않은 셀은 `null`(으)로 설정됩니다.
* `withsource=`*ColumnName:* 지정된 경우 출력은 값이 각 행에 기여한 원본 테이블을 나타내는 *ColumnName*이라는 열을 포함합니다. 이 열에 표시되는 이름을 지정하려면 각 테이블 식의 끝에 [as](#as-operator)를 사용합니다.

**반환**

모든 입력 테이블에 있는 만큼의 행과 입력에서 고유한 열이 있는 만큼의 열을 가진 테이블을 반환합니다.

행에 순서대로 표시된다고 보장할 수 없습니다.


**예제**

과거 12시간 동안 `exceptions` 이벤트 또는 `traces` 이벤트를 생성한 고유 사용자의 수입니다. 결과에서 'SourceTable' 열은 "exceptions" 또는 "traces"을 나타냅니다.

```AIQL
    
    union withsource=SourceTable kind=outer exceptions, traces
    | where timestamp > ago(12h)
    | summarize dcount(user_Id) by SourceTable
```

더 효율적인 이 버전은 동일한 결과를 생성합니다. 합집합을 생성하기 전에 각 테이블을 필터링합니다.

```AIQL
    exceptions
    | where timestamp > ago(24h) | as exceptions
    | union withsource=SourceTable kind=outer (requests | where timestamp > ago(12h) | as traces)
    | summarize dcount(user_Id) by SourceTable 
```

[as](#as-operator)를 사용하여 원본 열에 표시할 이름을 지정합니다.

#### <a name="forcing-an-order-of-results"></a>강제로 결과 순서 지정

합집합의 경우도 결과 행이 특정 순서로 표시된다고 보장할 수 없습니다.
쿼리를 실행할 때마다 동일한 순서를 가져오려면 각 입력 테이블에 태그 열을 추가합니다.

    let r1 = (traces | count | extend tag = 'r1');
    let r2 = (requests | count| extend tag = 'r2');
    let r3 = (pageViews | count | extend tag = 'r3');
    r1 | union r2,r3 | sort by tag

#### <a name="see-also"></a>참고 항목

[join 연산자](#join-operator)를 대신 사용할 수도 있습니다.

### <a name="where-operator"></a>where 연산자
     requests | where resultCode=="200"

조건자를 만족하는 행의 부분집합으로 테이블을 필터링합니다.

**별칭** `filter`

**구문**

    T | where Predicate
    T | where * has Term

**인수**

* *T:* 레코드를 필터링할 테이블 형식 입력입니다.
* *Predicate:* *T*의 열에 대한 `boolean` [식](#boolean)으로, *T*의 각 행에 대해 계산됩니다.
* *Term* - 열의 단어 전체와 일치해야 하는 문자열입니다.

**반환**

*Predicate*가 `true`인 *T*의 행입니다.

**팁**

가장 빠른 성능을 얻으려면:

* **단순 비교를 사용** 합니다('상수'는 테이블에 대한 상수를 의미합니다. ('상수'는 테이블에 대한 상수를 의미합니다. 따라서 `now()` 및 `ago()`을(를) 사용 가능하며 [`let` 절](#let-clause)을 사용하여 할당된 스칼라 값입니다).
  
    예를 들어 `where floor(Timestamp, 1d) == ago(1d)`보다 `where Timestamp >= ago(1d)`을(를) 선호합니다.
* **가장 단순한 용어를 먼저 사용**합니다. `and`을(를) 사용하여 여러 절을 결합하는 경우에는 열이 하나만 포함된 절을 앞쪽에 배치합니다. 따라서 `Timestamp > ago(1d) and OpId == EventId` 이 다른 방법보다 낫습니다.

**예제**

```AIQL
traces
| where Timestamp > ago(1h)
    and Source == "Kuskus"
    and ActivityId == SubActivityIt 
```

1시간보다 더 오래되지 않고 "Kuskus"라는 소스에서 나오고 같은 값의 열 두 개를 가진 레코드. 

참고로 마지막 두 열은 인덱스를 이용할 수 없고 스캔을 강제로 실행하므로 해당 두 열 사이에 비교를 넣습니다.



## <a name="aggregations"></a>집계
집계는 [작업 요약](#summarize-operator)에서 만든 그룹의 값을 결합하는 데 사용되는 함수입니다. 예를 들어 이 쿼리에서 dcount()는 집계 함수입니다.

    requests | summarize dcount(name) by success

### <a name="any"></a>모든
    any(Expression)

그룹의 한 행을 무작위로 선택하고 지정된 식의 값을 반환합니다.

이 작업은 예를 들어 일부 열에 유사한 값(예, “오류 텍스트" 열)이 있는데 복합 그룹 키의 고유한 값에 따라 해당 열을 한 번 샘플링하고자 할 때 유용합니다. 

**예제**  

```

traces 
| where timestamp > now(-15min)  
| summarize count(), any(message) by operation_Name 
| top 10 by count_level desc 
```

<a name="argmin"></a>
<a name="argmax"></a>

### <a name="argmin-argmax"></a>argmin, argmax
    argmin(ExprToMinimize, * | ExprToReturn  [ , ... ] )
    argmax(ExprToMaximize, * | ExprToReturn  [ , ... ] ) 

*ExprToMaximize*를 최소화/최대화하는 그룹의 행을 찾고 *ExprToReturn* (또는 `*`전체 행을 반환하려면)의 값을 반환합니다.

**팁**: 통과된 열의 이름은 자동으로 바뀝니다. 올바른 이름을 사용하고 있는지 확인하려면 결과를 다른 연산자에 파이프하기 전에 `take 5`을(를) 사용하여 결과를 검사합니다.

**예**

각 요청 이름에 대해 가장 긴 요청이 발생한 시기를 표시합니다.

    requests | summarize argmax(duration, timestamp) by name

타임스탬프뿐만 아니라 가장 긴 요청의 모든 세부 정보를 표시합니다.

    requests | summarize argmax(duration, *) by name


각 메트릭의 가장 낮은 값을 해당 타임스탬프 및 다른 데이터와 함께 찾습니다.

    metrics 
    | summarize minValue=argmin(value, *) 
      by name


![](./media/app-insights-analytics-reference/argmin.png)

### <a name="avg"></a>avg
    avg(Expression)

그룹 전체에서 *Expression* 의 평균을 계산합니다.

### <a name="buildschema"></a>buildschema
    buildschema(DynamicExpression)

*DynamicExpression*의 모든 값을 허용하는 최소 스키마를 반환합니다. 

매개 변수 열 형식은 `dynamic` (배열 또는 속성 모음)이어야 합니다. 

**예제**

    exceptions | summarize buildschema(details)

결과:

    { "indexer":
     {"id":"string",
       "parsedStack":
       { "indexer": 
         {  "level":"int",
            "assembly":"string",
            "fileName":"string",
            "method":"string",
            "line":"int"
         }},
      "outerId":"string",
      "message":"string",
      "type":"string",
      "rawStack":"string"
    }}

참고로 `indexer`은(는) 숫자 인덱스를 사용해야 하는 위치를 표시하기 위해 사용됩니다. 이 스키마의 경우 일부 유효한 경로는 다음과 같을 수 있습니다(이 예제 인덱스가 범위 안에 든다고 가정).

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)

**예제**

입력된 열이 동적 값 세 개를 가진다고 가정합니다.

|  |
| --- |
| `{"x":1, "y":3.5}` |
| `{"x":"somevalue", "z":[1, 2, 3]}` |
| `{"y":{"w":"zzz"}, "t":["aa", "bb"], "z":["foo"]}` |

결과 스키마는 다음과 같습니다.

    {
      "x":["int", "string"],
      "y":["double", {"w": "string"}],
      "z":{"indexer": ["int", "string"]},
      "t":{"indexer": "string"}
    }

스키마는 다음 정보를 알려줍니다.

* 루트 개체는 x, y, z 및 t라는 네 속성을 가진 컨테이너입니다.
* 형식 “Int” 또는 형식 “string” "x"라는 속성.
* 형식이 “double”인 “y”라는 속성 또는 형식 “string”의 “w”라는 속성을 가진 다른 컨테이너.
* ``indexer`` 키워드는 "z"와 "t"가 배열임을 나타냅니다.
* "z" 배열의 각 항목은 int 또는 문자열입니다.
* "t"는 문자열의 배열입니다.
* 모든 속성은 암시적으로 선택적이며 배열은 비어 있을 수 있습니다.

##### <a name="schema-model"></a>스키마 모델
반환되는 스키마의 구문은 다음과 같습니다.

    Container ::= '{' Named-type* '}';
    Named-type ::= (name | '"indexer"') ':' Type;
    Type ::= Primitive-type | Union-type | Container;
    Union-type ::= '[' Type* ']';
    Primitive-type ::= "int" | "string" | ...;

이는 동적 값으로 인코드된 TypeScript 형식 주석의 하위 집합과 동일합니다. Typescript에서 예제 스키마는 다음과 같습니다.

    var someobject:
    {
      x?: (number | string),
      y?: (number | { w?: string}),
      z?: { [n:number] : (int | string)},
      t?: { [n:number]: string }
    }


### <a name="count"></a>count
    count([ Predicate ])

*Predicate*가 `true`(으)로 계산되는 행 수를 반환합니다. *Predicate* 를 지정하지 않으면 그룹의 총 레코드 수를 반환합니다. 

**성능 팁**: `where filter | summarize count()` 대신 `summarize count(filter)` 사용

> [!NOTE]
> 발생한 요청, 예외 또는 기타 이벤트 수를 찾는 데는 count()를 사용하지 마세요. [샘플링](app-insights-sampling.md) 이 작동 중이면 Application Insights에 유지되는 데이터 요소 수가 원래 이벤트 수보다 적어집니다. 대신 `summarize sum(itemCount)...`을(를) 사용하세요. itemCount 속성은 보존된 각 데이터 요소로 표시되는 원래 이벤트 수를 반영합니다.
> 
> 

### <a name="countif"></a>countif
    countif(Predicate)

*Predicate*가 `true`(으)로 계산되는 행 수를 반환합니다.

**성능 팁**: `where filter | summarize count()` 대신 `summarize countif(filter)` 사용

> [!NOTE]
> 발생한 요청, 예외 또는 기타 이벤트 수를 찾는 데는 countif()를 사용하지 마세요. [샘플링](app-insights-sampling.md) 이 작동 중이면 데이터 요소 수가 실제 이벤트 수보다 적어집니다. 대신 `summarize sum(itemCount)...`을(를) 사용하세요. itemCount 속성은 보존된 각 데이터 요소로 표시되는 원래 이벤트 수를 반영합니다.
> 
> 

### <a name="dcount"></a>dcount
    dcount( Expression [ ,  Accuracy ])

그룹에 있는 *Expr* 의 고유 값 수에 대한 추정치를 반환합니다. (고유 값을 나열하려면 [`makeset`](#makeset)을(를) 사용합니다.)

*정확도*를 지정한 경우 속도와 정확도 간의 균형을 제어합니다.

* `0` = 정확성은 가장 떨어지지만 가장 빠른 계산입니다.
* `1` 기본값이며, 정확도와 계산 시간의 균형을 맞춥니다. 오류 비율: 약 0.8%
* `2` = 가장 정확하지만 계산 속도는 가장 느리며 오류 비율은 약 0.4%입니다.

**예제**

    pageViews 
    | summarize cities=dcount(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-reference/dcount.png)

### <a name="dcountif"></a>dcountif
    dcountif( Expression, Predicate [ ,  Accuracy ])

*Predicate*가 true인 그룹에 있는 행의 *Expr*에 대한 고유 값 수 추정치를 반환합니다. (고유 값을 나열하려면 [`makeset`](#makeset)을(를) 사용합니다.)

*정확도*를 지정한 경우 속도와 정확도 간의 균형을 제어합니다.

* `0` = 정확성은 가장 떨어지지만 가장 빠른 계산입니다.
* `1` 기본값이며, 정확도와 계산 시간의 균형을 맞춥니다. 오류 비율: 약 0.8%
* `2` = 가장 정확하지만 계산 속도는 가장 느리며 오류 비율은 약 0.4%입니다.

**예제**

    pageViews 
    | summarize cities=dcountif(client_City, client_City startswith "St") 
      by client_CountryOrRegion


### <a name="makelist"></a>makelist
    makelist(Expr [ ,  MaxListSize ] )

그룹에 있는 *Expr*의 모든 값의 `dynamic` (JSON) 배열을 반환합니다. 

* *MaxListSize*는 반환되는 최대 요소 수에 대한 선택적 정수 한계(기본값: *128*)입니다.

### <a name="makeset"></a>makeset
    makeset(Expression [ , MaxSetSize ] )

*Expr*이 그룹에서 가지는 고유 값 집합의 `dynamic` (JSON) 배열을 반환합니다. (팁: 고유 값을 세기만 하려면 [`dcount`](#dcount)을(를) 사용합니다.)

* *MaxSetSize*는 반환되는 최대 요소 수에 대한 선택적 정수 한계(기본값: *128*)입니다.

**예제**

    pageViews 
    | summarize cities=makeset(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-reference/makeset.png)

반대 함수는 [`mvexpand` 연산자](#mvexpand-operator)를 참조하세요.

### <a name="max-min"></a>max, min
    max(Expr)

*Expr*의 최대값을 계산합니다.

    min(Expr)

*Expr*의 최소값을 계산합니다.

**팁**: 이러한 함수를 단독으로 사용하는 경우 최고가 또는 최저가 등 원하는 최소값이나 최대값을 구할 수 있습니다. 그러나 행의 다른 열(예: 가장 낮은 가격을 가진 공급업체의 이름)을 원하는 경우 [argmin 또는 argmax](#argmin-argmax)를 사용합니다.

<a name="percentile"></a>
<a name="percentiles"></a>
<a name="percentilew"></a>
<a name="percentilesw"></a>

### <a name="percentile-percentiles-percentilew-percentilesw"></a>percentile, percentiles, percentilew, percentilesw
    percentile(Expression, Percentile)

그룹에 지정된 백분위수의 *식* 에 대한 추정치를 반환합니다. 정확도는 백분위수 지역의 인구 밀도에 따라 달라집니다.

    percentiles(Expression, Percentile1 [ , Percentile2 ...] )

`percentile()`와(과) 유사하지만 백분위 수 값을 계산합니다(각 백분위 수를 개별적으로 계산하는 것보다 더 빠름).

    percentilew(Expression, WeightExpression, Percentile)

가중치가 적용된 백분위 수입니다. 사전 집계된 데이터에 사용합니다.  `WeightExpression` 은(는) 각 집계된 행이 몇 개의 원래 행을 나타내는지 표시하는 정수입니다.

    percentilesw(Expression, WeightExpression, Percentile1, [, Percentile2 ...])

`percentilew()`와(과) 비슷하지만 백분위 수 값을 계산합니다.

**예**

샘플 집합의 95%보다는 크고 5%보다는 작은 `duration` 의 값을 각 요청 이름에 대해 계산하는 함수는 다음과 같습니다.

    request 
    | summarize percentile(duration, 95)
      by name

전체 테이블에 대해 계산하려면 “by...”를 생략합니다.

서로 다른 요청 이름에 대해 여러 백분위수를 동시에 계산합니다.

    requests 
    | summarize 
        percentiles(duration, 5, 20, 50, 80, 95) 
      by name

![](./media/app-insights-analytics-reference/percentiles.png)

이 결과는 요청/이벤트/인덱스에 대해 요청의 5%가 2.44s 이내에 응답하고, 그 중 절반이 3.52s 이내에 응답하고, 5%가 6.85s보다 더 느리다는 것을 보여줍니다.

여러 통계를 계산합니다.

    requests 
    | summarize 
        count(), 
        avg(Duration),
        percentiles(Duration, 5, 50, 95)
      by name

#### <a name="weighted-percentiles"></a>가중치가 적용된 백분위 수입니다.
데이터가 사전 집계된 경우 가중치가 적용된 백분위 수 함수를 사용합니다. 

예를 들어, 앱이 초당 수천 개의 연산을 실행할 경우 대기 시간을 알고 싶을 것입니다. 이 단순한 솔루션은 각 연산에 대해 Application Insights 요청이나 사용자 지정 이벤트를 생성합니다. 적응 샘플링이 트래픽을 줄일 수는 있지만, 여전히 많은 트래픽이 발생합니다. 그러나 이보다 나은 솔루션을 구현하려면 Application Insights에 전송하기 전에 데이터를 집계하는 코드를 앱에서 작성할 것입니다. 집계된 요약은 일정한 간격으로 전송되므로 최대 분당 몇 포인트 정도까지 데이터 속도를 낮출 수 있습니다.

이 코드는 밀리초 단위로 대기 스트림을 측정합니다. 예:

     { 15, 12, 2, 21, 2, 5, 35, 7, 12, 22, 1, 15, 18, 12, 26, 7 }

다음 bin 단위로 계산합니다. `{ 10, 20, 30, 40, 50, 100 }`

간헐적으로 일련의 TrackEvent를 호출합니다. 각 버킷마다 호출하고, 각 호출에 사용자 지정 측정값이 포함됩니다. 

    foreach (var latency in bins.Keys)
    { telemetry.TrackEvent("latency", null, 
         new Dictionary<string, double>
         ({"latency", latency}, {"opCount", bins[latency]}}); }

Analytics에서 다음과 같은 이벤트 그룹이 표시됩니다.

| `opCount` | `latency` | 의미 |
| --- | --- | --- |
| 8 |10 |= 10ms bin 기준 8개 연산 |
| 6 |20 |= 20ms bin 기준 6개 연산 |
| 3 |30 |= 30ms bin 기준 3개 연산 |
| 1 |40 |= 40ms bin 기준 1개 연산 |

이벤트 대기 시간의 원래 분포를 정확히 알아보려면 다음을 사용합니다. `percentilesw`

    customEvents | summarize percentilesw(latency, opCount, 20, 50, 80)

원래 측정 집합에서 기본적인 `percentiles`을(를) 사용했을 때 얻는 결과와 같습니다.

> [!NOTE]
> 가중치가 적용된 백분위 수는 [샘플링한 데이터](app-insights-sampling.md)에 적용할 수 없습니다. 샘플링된 데이터에서는 각 샘플링된 행이 bin이 아니라 원래 행의 무작위 샘플을 나타냅니다. 일반 백분위 수 함수는 샘플링된 데이터에 적합합니다.
> 
> 

#### <a name="estimation-error-in-percentiles"></a>백분위수 추정 오류
백분위 수 집계는 [T-Digest](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf)를 사용하여 근사값을 제공합니다. 

몇 가지 중요 사항: 

* 추정 오류의 범위는 요청한 백분위수의 값에 따라 달라집니다. 최고의 정확도는 [0 ~ 100] 눈금의 끝에서 얻어지며, 백분위수 0 및 100은 분포의 최소값 및 최대값과 똑같습니다. 정확도는 눈금의 중앙으로 갈수록 서서히 감소합니다. 중앙값에서 최악이 되고 1%에서 최고가 됩니다. 
* 오류 범위는 값이 아닌 순위에서 관찰됩니다. 백분위수(X, 50)가 Xm의 값을 반환한다고 가정합니다. 추정치는 X의 값 중 최소 49%와 최대 51%는 Xm보다 작다는 것을 보장합니다. Xm과 X의 실제 중간값 사이에 이론적 제한은 없습니다.

### <a name="stdev"></a>stdev
     stdev(Expr)

그룹에 대해 *Expr* 의 표준 편차를 반환합니다.

### <a name="variance"></a>variance
    variance(Expr)

그룹에 대해 *Expr* 의 분산을 반환합니다.

### <a name="sum"></a>sum
    sum(Expr)

그룹에 대해 *Expr* 의 합계를 반환합니다.                      

## <a name="scalars"></a>스칼라
[캐스트](#casts) | [비교](#scalar-comparisons)
<br/>
[gettype](#gettype) | [해시](#hash) | [iff](#iff) |  [isnull](#isnull) | [isnotnull](#isnotnull) | [notnull](#notnull) | [toscalar](#toscalar)

지원 되는 형식은 다음과 같습니다.

| 형식 | 추가 이름 | 해당하는.NET 형식 |
| --- | --- | --- |
| `bool` |`boolean` |`System.Boolean` |
| `datetime` |`date` |`System.DateTime` |
| `dynamic` | |`System.Object` |
| `guid` |`uuid`, `uniqueid` |`System.Guid` |
| `int` | |`System.Int32` |
| `long` | |`System.Int64` |
| `double` |`real` |`System.Double` |
| `string` | |`System.String` |
| `timespan` |`time` |`System.TimeSpan` |

### <a name="casts"></a>Casts
한 형식에서 다른 형식으로 캐스트할 수 있습니다. 일반적으로 변환이 적합한 경우 다음과 같이 작동합니다.

    todouble(10), todouble("10.6")
    toint(10.6) == 11
    floor(10.6) == 10
    toint("200")
    todatetime("2016-04-28 13:02")
    totimespan("1.5d"), totimespan("1.12:00:00")
    toguid("00000000-0000-0000-0000-000000000000")
    tostring(42.5)
    todynamic("{a:10, b:20}")

문자열을 특정 형식으로 변환할 수 있는지 여부를 확인합니다.

    iff(notnull(todouble(customDimensions.myValue)),
       ..., ...)







### <a name="scalar-comparisons"></a>스칼라 비교
|  |  |
| --- | --- |
| `<` |더 작음 |
| `<=` |작거나 같음 |
| `>` |더 큼 |
| `>=` |크거나 같음 |
| `<>` |같지 않음 |
| `!=` |같지 않음 |
| `in` |오른쪽 피연산자는 (동적) 배열이며 왼쪽 피연산자는 해당 배열 요소 중 하나와 같습니다. |
| `!in` |오른쪽 피연산자는 (동적) 배열이며 왼쪽 피연산자는 해당 배열 요소 중 하나와 같지 않습니다. |

### <a name="gettype"></a>gettype
**반환**

단일 인수의 기본 저장소 유형을 나타내는 문자열. 이는 종류 `dynamic`의 값을 가질 때 특히 유용합니다. 이 경우 `gettype()`은(는) 값이 인코딩되는 방법을 표시합니다.

**예**

|  |  |
| --- | --- |
| `gettype("a")` |`"string" ` |
| `gettype(111)` |`"long" ` |
| `gettype(1==1)` |`"int8"` |
| `gettype(now())` |`"datetime" ` |
| `gettype(1s)` |`"timespan" ` |
| `gettype(parsejson('1'))` |`"int" ` |
| `gettype(parsejson(' "abc" '))` |`"string" ` |
| `gettype(parsejson(' {"abc":1} '))` |`"dictionary"` |
| `gettype(parsejson(' [1, 2, 3] '))` |`"array"` |
| `gettype(123.45)` |`"real" ` |
| `gettype(guid(12e8b78d-55b4-46ae-b068-26d7a0080254))` |`"guid"` |
| `gettype(parsejson(''))` |`"null"` |
| `gettype(1.2)==real` |`true` |

### <a name="hash"></a>hash
**구문**

    hash(source [, mod])

**인수**

* *source*: 해시를 계산하는 대상인 원본 스칼라입니다.
* *mod*: 해시 결과에 적용할 모듈로 값입니다.

**반환**

지정된 스칼라의 xxhash(long)값, 지정된 mod 값(지정한 경우) 모듈로.

**예**

```
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```
### <a name="iff"></a>iff
`iff()` 함수는 첫 번째 인수(조건자)를 계산하고 조건자가 `true`인지 또는 `false`인지에 따라 두 번째 또는 세 번째 인수의 값을 반환합니다. 두 번째 및 세 번째 인수는 동일한 형식이어야 합니다.

**구문**

    iff(predicate, ifTrue, ifFalse)


**인수**

* *Predicate:* `boolean` 값으로 계산되는 식입니다.
* *ifTrue:* *predicate*이 `true`(으)로 계산되는 경우 계산이 이루어지는 식 및 함수에서 반환되는 값입니다.
* *ifFalse:* *predicate*이 `false`(으)로 계산되는 경우 계산이 이루어지는 식 및 함수에서 반환되는 식의 값입니다.

**반환**

이 함수는 *predicate*이 `true`(으)로 계산되는 경우 *ifTrue*의 값을 반환하며 그렇지 않은 경우 *ifFalse*의 값을 반환합니다.

**예제**

```
iff(floor(timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```

<a name="isnull"/></a>
<a name="isnotnull"/></a>
<a name="notnull"/></a>

### <a name="isnull-isnotnull-notnull"></a>isnull, isnotnull, notnull
    isnull(parsejson("")) == true

단일 인수를 사용하고 null인지 여부를 알려줍니다.

**구문**

    isnull([value])


    isnotnull([value])


    notnull([value])  // alias for isnotnull

**반환**

값이 null인지 여부에 따라 true 또는 false.

| x | isnull(x) |
| --- | --- |
| "" |false |
| "x" |false |
| parsejson("") |true |
| parsejson("[]") |false |
| parsejson("{}") |false |

**예제**

    T | where isnotnull(PossiblyNull) | count

참고로 이 효과를 달성하는 다른 방법이 있습니다.

    T | summarize count(PossiblyNull)

### <a name="toscalar"></a>toscalar
쿼리 또는 식을 평가하고 결과를 단일 값으로 반환합니다. 이 함수는 단계적 계산(예: 이벤트의 총 개수를 계산한 후 기준으로 사용)에 유용합니다.

**구문**

    toscalar(query)
    toscalar(scalar)

**반환**

평가된 인수입니다. 인수가 테이블인 경우 첫 번째 행의 첫 번째 열을 반환합니다. 인수에 열과 행이 하나씩만 있는 경우가 모범 사례입니다.

**예제**

```AIQL

    // Get the count of requests 5 days ago:
    let baseline = toscalar(requests  
        | where floor(timestamp, 1d) == floor(ago(5d),1d) | count);
    // List the counts relative to that baseline:
    requests | summarize daycount = count() by floor(timestamp, 1d)  
    | extend relative = daycount - baseline
```




### <a name="boolean-literals"></a>부울 리터럴
    true == 1
    false == 0
    gettype(true) == "int8"
    typeof(bool) == typeof(int8)

### <a name="boolean-operators"></a>부울 연산자
    and 
    or 

### <a name="convert-to-boolean"></a>부울로 변환

값 "true" 또는 "false"를 포함하는 문자열 `aStringBoolean`이 있는 경우 다음과 같이 부울로 변환할 수 있습니다.

    booleanResult = aStringBoolean =~ "true"



## <a name="numbers"></a>숫자
[abs](#abs) | [bin](#bin) | [exp](#exp) | [층](#floor) | [감마](#gamma) |[로그](#log) | [rand](#rand) | [범위](#range) | [sqrt](#sqrt) 
| [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

### <a name="numeric-literals"></a>숫자 리터럴
|  |  |
| --- | --- |
| `42` |`long` |
| `42.0` |`real` |

### <a name="arithmetic-operators"></a>산술 연산자
|  |  |
| --- | --- |
| + |추가 |
| - |빼기 |
| * |곱하기 |
| / |나누기 |
| % |모듈로 |
| `<` |더 작음 |
| `<=` |작거나 같음 |
| `>` |더 큼 |
| `>=` |크거나 같음 |
| `<>` |같지 않음 |
| `!=` |같지 않음 |

### <a name="abs"></a>abs
**구문**

    abs(x)

**인수**

* x - 정수, 실수 또는 Timespan

**반환**

    iff(x>0, x, -x)

<a name="bin"></a><a name="floor"></a>

### <a name="bin-floor"></a>bin, floor
값을 지정된 bin 크기의 아래쪽 정수 배로 반올림합니다. [`summarize by`](#summarize-operator) 쿼리에 많이 사용됩니다. 분산된 값 집합이 있는 경우 특정 값의 더 작은 집합으로 그룹화됩니다.

별칭 `floor`입니다.

**구문**

     bin(value, roundTo)
     floor(value, roundTo)

**인수**

* *value:* 숫자, 날짜 또는 시간 범위입니다. 
* *roundTo:* "bin 크기"입니다. *값*을 나누는 숫자, 날짜 또는 시간 범위입니다. 

**반환**

*값*보다 작은 *roundTo*의 가장 가까운 배수입니다.  

    (toint(value/roundTo)) * roundTo

**예**

| Expression | 결과 |
| --- | --- |
| `bin(4.5, 1)` |`4.0` |
| `bin(time(16d), 7d)` |`14d` |
| `bin(datetime(1953-04-15 22:25:07), 1d)` |`datetime(1953-04-15)` |

다음 식은 버킷 크기 1초를 사용하여 기간의 히스토그램을 계산합니다.

```AIQL

    T | summarize Hits=count() by bin(Duration, 1s)
```

### <a name="exp"></a>exp
    exp(v)   // e raised to the power v
    exp2(v)  // 2 raised to the power v
    exp10(v) // 10 raised to the power v


### <a name="floor"></a>floor
[`bin()`](#bin)에 대한 별칭입니다.

### <a name="gamma"></a>감마
[감마 함수](https://en.wikipedia.org/wiki/Gamma_function)

**구문**

    gamma(x)

**인수**

* *x:* 실수

양의 정수의 경우, `gamma(x) == (x-1)!` 예를 들어 `gamma(5) == 4 * 3 * 2 * 1`입니다.

[loggamma](#loggamma)를 참조하세요.

### <a name="log"></a>로그
    log(v)    // Natural logarithm of v
    log2(v)   // Logarithm base 2 of v
    log10(v)  // Logarithm base 10 of v


`v`은(는) 0보다 큰 실수여야 합니다. 그렇지 않으면 null이 반환됩니다.

### <a name="loggamma"></a>loggamma
[감마 함수](#gamma)의 절대 값의 자연 로그입니다.

**구문**

    loggamma(x)

**인수**

* *x:* 실수

### <a name="rand"></a>rand
난수 생성기입니다.

* `rand()` - 0.0 ~ 1.0 사이의 실수
* `rand(n)` - 0 ~ n-1 사이의 정수

### <a name="sqrt"></a>sqrt
제곱근 함수입니다.  

**구문**

    sqrt(x)

**인수**

* *x:* 실수 >= 0.

**반환**

* `sqrt(x) * sqrt(x) == x`
* 인수가 음수이거나 `real` 값으로 변환할 수 없는 경우 `null`입니다. 

### <a name="toint"></a>toint
    toint(100)        // cast from long
    toint(20.7) == 20 // nearest int below double
    toint(20.4) == 20 // nearest int below double
    toint("  123  ")  // parse string
    toint(a[0])       // cast from dynamic
    toint(b.c)        // cast from dynamic


### <a name="todouble"></a>todouble
    todouble(20) == 20.0 // conversion from long or int
    todouble(" 12.34 ")  // parse string
    todouble(a[0])       // cast from dynamic
    todouble(b.c)        // cast from dynamic


### <a name="tolong"></a>tolong
    tolong(20.7) == 20 // conversion from double
    tolong(20.4) == 20 // conversion from double
    tolong("  123  ")  // parse string
    tolong(a[0])       // cast from dynamic
    tolong(b.c)        // cast from dynamic

## <a name="numeric-series"></a>숫자 계열

[series_fir](#seriesfir) | [series\_fit\_line](#seriesfitline) | [series\_fit\_2lines](#seriesfit2lines) | [series_iir](#seriesiir) |  [series_periods](#seriesperiods) | [series_stats](#seriesstats)  

### <a name="seriesfir"></a>series_fir
series_fir() 함수는 계열에 [Finite Impulse Response](https://wikipedia.org/wiki/Finite_impulse_response) 필터를 적용합니다(숫자 배열을 포함하는 동적 열로 표현).

필터 계수를 지정하여 이동 평균 계산, 다듬기, 변경 검색 및 기타 여러 사용 사례에 사용할 수 있습니다.

이 함수는 필터 계수의 정적 동적 배열 및 동적 배열을 포함하는 열을 입력으로 사용하고 열에 해당 필터를 적용합니다. 필터링된 출력을 포함하는 새 동적 배열 열을 출력합니다. 

**구문**

`series_fir(x, filter [, normalize[, center]])`

**인수**

* *x:* 숫자 값의 배열이며, 일반적으로 [make-series](#make-series-operator) 또는 [makelist](#makelist-operator) 연산자의 결과 출력인 동적 배열 셀입니다.
* *filter:* 필터 계수를 정규화(즉 합계로 나누기)해야 하는지 여부를 나타내는 선택적 부울 값입니다. 기본적으로 normalize는 true입니다. 필터가 음수 값을 포함하면 자동 정규화를 수행할 수 없고 normalize를 명시적으로 false로 설정해야 합니다.
* *normalize:* 필터를 정규화해야 하는지 여부를 나타내는 선택적 부울 값입니다. 기본적으로 normalize는 true입니다. 필터가 음수 값을 포함하면 normalize를 false로 지정해야 합니다. 
* *center:* 필터가 현재 시점 전과 후에 또는 현재 시점부터 이전 기간에 대칭적으로 적용되는지 여부를 나타내는 선택적 부울 값입니다. 기본적으로 center는 데이터 스트리밍 시나리오에 적합한 false입니다. 이 경우에는 현재 및 이전 시점에만 필터를 적용할 수 있습니다. 그렇지만 임시 처리를 위해 center를 true로 설정하여 시계열과 동기화된 상태를 유지할 수 있습니다(아래 예제 참조). 기술적으로 설명하자면 이 매개 변수는 필터의 그룹 지연을 제어합니다.

**예**

5개 시점의 이동 평균을 계산하려면 filter=[1,1,1,1,1] 및 normalize=true(기본값)로 설정할 수 있습니다. center=false(기본값)일 때와 true일 때의 결과는 다음과 같습니다.

```AIQL
range t from bin(now(), 1h)-23h to bin(now(), 1h) step 1h
| summarize t=makelist(t)
| project id='TS', val=dynamic([0,0,0,0,0,0,0,0,0,10,20,40,100,40,20,10,0,0,0,0,0,0,0,0]), t
| extend 5h_MovingAvg=series_fir(val, dynamic([1,1,1,1,1])),
         5h_MovingAvg_centered=series_fir(val, dynamic([1,1,1,1,1]), true, true)
| render timechart
```

이 쿼리는 다음을 반환합니다.

* *5h_MovingAvg:* 5개 시점 이동 평균 필터입니다. 스파이크는 다듬어지고 피크는 (5-1)/2 = 2h로 전환됩니다.
* *5h_MovingAvg_centered:* 다른 부분은 동일하게 유지하지만 center=true를 설정하면 피크는 원래 위치를 유지합니다.

![쿼리 결과](./media/app-insights-analytics-reference/series-fir-1.png)(여러 줄을 보려면 차트 컨트롤에서 '분할' 선택 취소)

한 시점과 이전 시점 간 차이를 계산하려면 filter=[1,-1]로 설정합니다.

```AIQL
range t from bin(now(), 1h)-11h to bin(now(), 1h) step 1h
| summarize t=makelist(t)
| project id='TS',t,value=dynamic([0,0,0,0,2,2,2,2,3,3,3,3])
| extend diff=series_fir(value, dynamic([1,-1]), false, false)
| render timechart
```


![쿼리 결과](./media/app-insights-analytics-reference/series-fir-2.png)


### <a name="seriesfitline"></a>series\_fit\_line
series_fit_line() 함수는 동적 숫자 배열을 입력으로 포함하는 열을 사용하고 가장 적합한 선을 찾기 위해 선형 회귀를 수행합니다. 이 함수는 시계열 배열에 사용되며, make-series 연산자의 출력에 적합합니다. 다음 필드를 포함하는 동적 열을 생성합니다.

* *slope:* 근사 선의 기울기입니다(`y=ax+b`의 `a`)
* *interception:* 근사 선의 절편입니다(`y=ax+b`의 `b`).
* *rsquare:* r 제곱은 적합 품질의 표준 측정입니다. [0-1] 범위의 숫자입니다. 여기서 1은 가장 적합할 가능성이 높은 경우이고 0은 데이터 순서가 완전히 잘못되었으며 어떤 선에도 맞지 않음을 의미합니다.
* *variance:* 입력 데이터의 분산입니다.
* *rvariance:* 입력 데이터 값과 근사값 사이의 편차를 나타내는 잔차 분산입니다.
* *line_fit:* 가장 적합한 선의 값 시리즈를 포함하는 숫자 배열입니다. 계열 길이는 입력 배열의 길이와 같습니다. 이 길이는 차트에 주로 사용됩니다.

이 함수를 사용하는 가장 편리한 방법은 make-series 연산자의 결과에 적용하는 것입니다.

**구문**
    
    series_fit_line(x)

**인수**

* `x:` 숫자 값의 동적 배열입니다. 이 함수에서 모든 행은 같은 수의 배열 요소를 가져야 합니다. 그렇지 않으면 빈 결과가 반환됩니다. 

**예**

```AIQL
range x from 1 to 1 step 1
| project id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([2,5,6,8,11,15,17,18,25,26,30,30])
| extend (s,i,rs,v,rv,LineFit)=series_fit_line(y)
| render timechart 
```

![쿼리 결과](./media/app-insights-analytics-reference/series-fit-line.png)

|Slope|Interception|RSquare|Variance|RVariance|LineFit|
|---|---|---|---|---|---|
|0.982|2.730|98.628|1.686|-1.666| 1.064, 3.7945, 6.526, 9.256, 11.987, 14.718, 17.449, 20.180, 22.910, 25.641, 28.371, |

### <a name="seriesfit2lines"></a>series\_fit\_2lines

series_fit_2lines() 함수는 계열의 추세 변경을 식별하고 정량화하기 위해 시계열에 2개의 세그먼트 선형 회귀를 적용합니다. 이 함수는 계열 인덱스를 반복하고, 각 반복에서 계열을 2 부분으로 분할하고 각 부분에 별도 선을 맞추고(series_fit_line() 사용), 전체 r 제곱을 계산합니다. 가장 적합한 분할은 r 제곱을 최대화한 분할입니다. 이 함수는 해당 매개 변수를 반환합니다.

* *rsquare:* r 제곱은 적합 품질의 표준 측정입니다. [0-1] 범위의 숫자입니다. 여기서 1은 가장 적합할 가능성이 높은 경우이고 0은 데이터 순서가 완전히 잘못되었으며 어떤 선에도 맞지 않음을 의미합니다.
* *split_idx:* 점을 2개 세그먼트로 분리하는 인덱스(0부터 시작)입니다.
* *variance:* 입력 데이터의 분산입니다.
* *rvariance:* 입력 데이터 값과 근사값 사이의 편차를 나타내는 잔차 분산입니다(2개 선 세그먼트별).
* *line_fit:* 가장 적합한 선의 값 시리즈를 포함하는 숫자 배열입니다. 계열 길이는 입력 배열의 길이와 같습니다. 이 길이는 차트에 주로 사용됩니다.
* *right_rsquare:* 분할 오른쪽에 있는 선의 r 제곱입니다. series_fit_line()을 참조하세요.
* *right_slope:* 오른쪽 근사 선의 기울기입니다(y=ax+b의 a).
* *right_interception:* 왼쪽 근사 선의 절편입니다(y=ax+b의 b).
* *right_variance:* 분할 오른쪽의 입력 데이터 분산입니다.
* *right_rvariance:* 분할 오른쪽의 입력 데이터 잔차 분산입니다.
* *left_rsquare:* 분할 왼쪽에 있는 선의 r 제곱입니다. series_fit_line()을 참조하세요.
* *left_slope:* 왼쪽 근사 선의 기울기입니다(y=ax+b의 a).
* *left_interception:* 왼쪽 근사 선의 절편입니다(y=ax+b의 b).
* *left_variance:* 분할 왼쪽의 입력 데이터 분산입니다.
* *left_rvariance:* 분할 왼쪽의 입력 데이터 잔차 분산입니다.

이 함수는 여러 열을 반환하므로 다른 함수에 대한 인수로 사용할 수 없습니다.

**구문**

    project series_fit_2lines(x)

다음과 같은 이름을 갖는 위에서 언급된 모든 열을 반환합니다. `series_fit_2lines_x_rsquare, series_fit_2lines_x_split_idx and etc.`

    project (rs, si, v)=series_fit_2lines(x)

열 `rs (r-square), si (split index), v (variance)`를 반환하고 나머지는 `series_fit_2lines_x_rvariance, series_fit_2lines_x_line_fit` 등과 같이 표시됩니다.

    extend (rs, si, v)=series_fit_2lines(x)

rs(r 제곱), si(분할 인덱스) 및 v(분산)만 반환합니다.

**인수**

* *x:* 숫자 값의 동적 배열입니다. 

이 함수를 사용하는 가장 편리한 방법은 make-series 연산자의 결과에 적용하는 것입니다.

**예**

```AIQL
range x from 1 to 1 step 1
| project id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([1,2.2, 2.5, 4.7, 5.0, 12, 10.3, 10.3, 9, 8.3, 6.2])
| extend (Slope,Interception,RSquare,Variance,RVariance,LineFit)=series_fit_line(y), (RSquare2, SplitIdx, Variance2,RVariance2,LineFit2)=series_fit_2lines(y)
| project id, x, y, LineFit, LineFit2
| render timechart
```


![쿼리 결과](./media/app-insights-analytics-reference/series-fit-2lines.png)

### <a name="seriesiir"></a>series_iir

series_iir() 함수는 계열에 Infinite Impulse Response 필터를 적용합니다(숫자 배열을 포함하는 동적 열로 표현).

필터 계수를 지정하여 예를 들어 계열의 누적 합계를 계산하고, 다양한 high-pass, band-pass 및 low-pass 필터 뿐 아니라 다듬기 작업을 적용할 때 사용할 수 있습니다.

이 함수는 필터 계수 a 및 b의 정적 동적 배열 2개 및 동적 배열을 포함하는 열을 입력으로 사용하고 열에 해당 필터를 적용합니다. 필터링된 출력을 포함하는 새 동적 배열 열을 출력합니다. 

**구문**

    series_iir(x, b , a)

**인수**


* *x:* 숫자 값의 배열이며, 일반적으로 make-series 또는 makelist 연산자의 결과 출력인 동적 배열 셀입니다.
* *b:* 필터의 분자 계수를 포함하는 상수 식(숫자 값의 동적 배열로 저장)입니다.
* *a:* b와 같은 상수 식입니다. 필터의 분모 계수를 포함합니다.

    a의 첫 번째 요소(즉, a[0])는 0이 아니어야 합니다(0으로 나누기 방지에 필요. 아래 수식 참조).

**필터의 재귀 수식에 대해 자세히 알아보기**

`n_a` 및 `n_b` 길이의 입력 배열 X 및 계수 배열 a, b가 각각 지정될 경우 출력 배열 Y를 생성하는 필터의 전송 함수는 다음과 같이 정의됩니다(Wikipedia에서도 참조). 

    Y[i] = 1/a[0] * ( b[0]*X[i] + b[1]*X[i-1] + … 
                 + b[n_b-1]*X[i-n_b-1] — a[1]*Y[i-1] – a[2]*Y[i-2] – …
                 – a[n_a-1]*Y[i-n_a-1] )


**예**

누적 합계 계산은 계수 a=[1,-1] 및 b=[1]을 사용하여 iir 필터에 의해 수행될 수 있습니다. 

```AIQL
let x = range(1.0, 10, 1);
range t from 1 to 1 step 1
| project x=x, y = series_iir(x, dynamic([1]), dynamic([1,-1]))
| mvexpand x, y
```

|x|y|
|---|---|
|1.0|1.0|
|2.0|3.0|
|3.0|6.0|
|4.0|10.0|
### <a name="seriesoutliers"></a>series_outliers 

series_outliers() 함수는 동적 배열을 포함하는 열을 입력으로 사용하여 입력과 같은 길이의 동적 숫자형 배열을 생성합니다. 배열의 각 값은 Tukey의 테스트를 사용하여 가능한 비정상을 나타내는 점수를 나타냅니다. 1.5보다 크거나 -1.5보다 작은 값은 각각 입력의 같은 요소에서 증가 또는 감소 비정상을 나타냅니다.  

**구문**  

```
series_outliers(x,kind,ignore_val,min_percentile,max_percentile)  
```
**인수** 
* *x:* 숫자 값 배열인 동적 배열 셀입니다. 값은 등거리인 것으로 간주되며, 그렇지 않으면 예기치 않은 결과가 생성될 수 있습니다.  
* *kind:* 이상값 검색 알고리즘입니다. 현재 “tukey” 및 “ctukey”를 지원합니다. 기본값은 “ctukey”입니다.  
* *ignore_val:* 계열의 누락 값을 나타내는 숫자 값이며, 기본값은 double(null)입니다.
* *min_percentile:* 정규 변위치 간 범위를 계산하기 위한 것이며, 기본값은 10입니다(ctukey만 해당).
* *max_percentile:* 정규 변위치 간 범위를 계산하기 위한 것이며, 기본값은 90입니다(ctukey만 해당).

다음 표에는 “tukey”와 “ctukey” 간의 차이점이 설명되어 있습니다.

|알고리즘|기본 변위치 범위|사용자 지정 변위치 범위 지원|
|---------|----------------------|------------------------------|
|“tukey”|25%/75%|아니요|
|“ctukey”|10%/90%|예|

**중요 참고** 이 함수를 사용하는 가장 편리한 방법은 `make-series` 연산자의 결과에 적용하는 것입니다.

**예** 

다음 입력의 경우   
```
[30,28,5,27,31,38,29,80,25,37,30]
``` 
series_outliers()는  
[0.0,0.0,-3.206896551724138,-0.1724137931034483,0.0,2.6666666666666667,0.0,16.666666666666669,-0.4482758620689655,2.3333333333333337,0.0]을 반환합니다.

여기서 5는 나머지 계열과 비교했을 때 감소에서의 비정상이며 80은 증가에서의 비정상입니다. 

### <a name="seriesperiods"></a>series_periods

series_periods() 함수는 시계열에 존재하는 가장 중요한 기간을 찾습니다.

예를 들어 자주 응용 프로그램 트래픽을 측정하는 메트릭은 두 가지 중요한 주기인 주별 또는 일별로 특징이 지정됩니다. 이러한 시계열이 지정될 경우 series_periods()는 이러한 2가지 주요 기간을 검색할 수 있습니다.

이 함수는 시계열의 동적 배열(일반적으로 make-series 연산자의 결과 출력), 검색할 최소 및 최대 기간 크기를 정의하는 두 개의 실수(즉, bin의 수, 예를 들어 1시간 bin의 경우 일별 기간 크기는 24임), 함수가 검색할 총 기간 수를 정의하는 long 형식의 숫자를 포함하는 열을 입력으로 사용합니다. 출력은 발견된 기간의 크기를 데이터의 기간 중요도 순서로 포함하는 동적 배열입니다.

**구문**

    series_periods(x, min_period, max_period, num_periods)`

**인수**

* *x:* 숫자 값의 배열이며, 일반적으로 make-series 또는 makelist 연산자의 결과 출력인 동적 배열 셀입니다.
* *min_period:* 검색할 최소 기간을 지정하는 실수입니다.
* *max_period:* 검색할 최대 기간을 지정하는 실수입니다.
* *num_periods:* 필요한 최대 기간 수를 지정하는 long 숫자입니다. 이것은 출력 동적 배열 길이가 됩니다.

**중요**

* series\_periods() 이면의 알고리즘에서는 검색을 위해 기간 안에 4개 이상의 지점이 필요합니다. 따라서 min_period의 최소값은 4입니다. min_period를 더 낮은 값으로 설정하면 함수에서는 4로 대체됩니다.
* max_period의 최대값은 입력 계열 길이의 1/2입니다. max_period를 더 높은 값으로 설정하면 함수에서는 해당 값으로 클리핑합니다.
* 위에서 설명한 대로 결과 기간은 bin의 단위입니다. 예를 들어 원래 계열이 일별 기간을 가지며 시간별 bin으로 집계될 경우 출력의 일별 기간은 24가 됩니다. 데이터가 분 단위로 집계되면 출력은 60*24=1440이 됩니다.
* min\_period는 시계열에서 찾으려는 기간보다 약간 더 낮게 설정하고 max\_period는 약간 더 높게 설정해야 합니다. 예를 들어 시간별로 집계되는 신호가 있으며 일별 및 주별 마침표를 모두 찾으려는 경우(각각 24 및 168일 수 있음) min\_period=0.824, *max\_period=1.2*168을 설정할 수 있습니다.
* 출력 동적 배열의 길이는 num\_of\_periods입니다. 이 함수가 num\_of\_periods 유효 기간보다 낮은 기간을 찾으면 배열 항목의 나머지는 0으로 설정됩니다.
* 입력 시계열은 정기적(즉 일정한 bin으로 집계)이어야 합니다(make-series를 사용하여 만든 경우). 그렇지 않은 경우 출력은 의미가 없습니다.

**예제**

예를 들어 다음 쿼리는 하루에 2번(즉 12시간 간격으로) 집계된 1달 간의 응용 프로그램 트래픽 스냅숏을 포함합니다.

```AIQL
range x from 1 to 1 step 1
| project y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, arraylength(y), 1), y  
| render linechart
```

![쿼리 결과](./media/app-insights-analytics-reference/series-periods1.png)

이 계열에서 series_periods()를 실행하면 다음과 같은 주별 기간(14개 지점)이 발생합니다.

```AIQL
range x from 1 to 1 step 1
| project y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, arraylength(y), 1), y  
| project periods = series_periods(y, 4.0, 50.0, 2)
```

|기간|
|---|
|[14.0, 0.0]|

### <a name="seriesstats"></a>series_stats

series_stats() 함수는 동적 숫자 배열을 포함하는 열을 입력으로 사용하고 다음 열을 계산합니다.

* *min:* 입력 배열의 최소값
* *min_idx:* 입력 배열의 최대값
* *max:* 입력 배열의 최대값
* *max_idx:* 입력 배열의 최대값
* *average:* 입력 배열의 평균 값
* *variance:* 입력 배열의 샘플 분산
* *stdev:* 입력 배열의 샘플 표준 편차

이 함수는 여러 열을 반환하므로 다른 함수에 대한 인수로 사용할 수 없습니다.

**구문**

    project series_stats(x)

serie\_stats\_x\_min, series\_stats\_x\_min\_idx 등의 이름을 사용해서 언급된 위의 모든 열을 반환합니다.

    project (m, mi)=series_stats(x)

열 `m (min), mi (min_idx)`를 반환하며 나머지는 `series_stats_x_max, series_stats_x_line_max_idx` 등과 같이 표시됩니다.

    extend (m, mi)=series_stats(x)

m (min) 및 mi (min_idx)만 반환합니다.

**인수**

* *x:* 숫자 값 배열인 동적 배열 셀입니다. 

**예**

입력:

` [1,6,11,16,21,26,31,36,41,46,51,56,61,66,71,76,81,86,91,96]`

series_stats() 반환값:

|min|min\_idx|max|max\_idx|average|variance|stdev|
|---|---|---|---|---|---|---|
|1.0|1|96.0|19|48.5|29.58039891549808|875.0|


## <a name="date-and-time"></a>날짜 및 시간
[전](#ago) | [dayofmonth](#dayofmonth) | [dayofweek](#dayofweek) |  [dayofyear](#dayofyear) |[datepart](#datepart) | [endofday](#endofday) | [endofmonth](#endofmonth) | [endofweek](#endofweek) | [endofyear](#endofyear) | [getmonth](#getmonth)|  [getyear](#getyear) | [이제](#now) | [startofday](#startofday) | [startofmonth](#startofmonth) | [startofweek](#startofweek) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan) | [weekofyear](#weekofyear)

timespan은 3시간 또는 1년과 같은 시간 간격을 나타냅니다.

datetime은 특정 달력/날짜/시간을 UTC 단위로 나타냅니다.

별도의 'date' 형식은 없습니다. datatime에서 시간을 제거하려면 `bin(timestamp, 1d)`과 같은 식을 사용합니다.

### <a name="date-and-time-literals"></a>날짜 및 시간 리터럴
|  |  |
| --- | --- |
| **datetime** | |
| `datetime("2015-12-31 23:59:59.9")`<br/>`datetime("2015-12-31")` |시간은 항상 UTC 기준입니다. 날짜를 생략하면 오늘 시간을 제공합니다. |
| `now()` |현재 시간입니다. |
| `now(`-*timespan*`)` |`now()-`*timespan* |
| `ago(`*timespan*`)` |`now()-`*timespan* |
| **timespan** | |
| `2d` |2일 |
| `1.5h` |1.5시간 |
| `30m` |30분 |
| `10s` |10초 |
| `0.1s` |0.1초 |
| `100ms` |100밀리초 |
| `10microsecond` | |
| `1tick` |100나노초 |
| `time("15 seconds")` | |
| `time("2")` |2일 |
| `time("0.12:34:56.7")` |`0d+12h+34m+56.7s` |

### <a name="date-and-time-expressions"></a>날짜 및 시간 식
| 식 | 결과 |결과|
| --- | --- |---|
| `datetime("2015-01-02") - datetime("2015-01-01")` |`1d` | 시간 차이|
| `datetime("2015-01-01") + 1d` |`datetime("2015-01-02")` | 일 추가 |
| `datetime("2015-01-01") - 1d` |`datetime("2014-12-31")` | 일 빼기|
| `2h * 24` |`2d` |시간 범위 곱하기|
| `2d` / `2h` |`24` |시간 범위 나누기|
| `datetime("2015-04-15T22:33") % 1d` |`timespan("22:33")` |날짜/시간 중 시간|
| `bin(datetime("2015-04-15T22:33"), 1d)` |`datetime("2015-04-15T00:00")` |날짜/시간 중 날짜|
|  | ||
| `<` ||더 작음 |
| `<=` ||작거나 같음 |
| `>` ||더 큼 |
| `>=` ||크거나 같음 |
| `<>` ||같지 않음 |
| `!=` ||같지 않음 |

### <a name="ago"></a>ago
현재 UTC 시계 시간에서 지정된 시간 범위를 뺍니다. `now()`와(과) 마찬가지로, 이 함수를 문에 여러 번 사용할 수 있으며 참조하는 UTC 시계 시간은 모든 인스턴스에 대해 동일합니다.

**구문**

    ago(a_timespan)

**인수**

* *a_timespan*: 현재 UTC 시계 시간(`now()`)에서 뺄 간격입니다.

**반환**

    now() - a_timespan

**예제**

과거 시간에 타임스탬프를 가진 모든 행:

```AIQL

    T | where timestamp > ago(1h)
```

### <a name="datepart"></a>datepart
    datepart("Day", datetime(2015-12-14)) == 14

날짜의 지정된 부분을 정수로 추출합니다.

**구문**

    datepart(part, datetime)

**인수**

* `part:String` - {"Year", "Month", "Day", "Hour", "Minute", "Second", "Millisecond", "Microsecond", "Nanosecond"}
* `datetime`

**반환**

지정된 부분을 나타내는 Long 값입니다.

### <a name="dayofmonth"></a>dayofmonth
    dayofmonth(datetime("2016-05-15")) == 15 

월 중 일의 서수입니다.

**구문**

    dayofmonth(a_date)

**인수**

* `a_date`: `datetime`입니다.

### <a name="dayofweek"></a>dayofweek
    dayofweek(datetime("2015-12-14")) == 1d  // Monday

`timespan`와(과) 같이 이전 일요일 이후 일의 정수입니다.

**구문**

    dayofweek(a_date)

**인수**

* `a_date`: `datetime`입니다.

**반환**

이전 일요일부터 시작되는 자정 이후의 `timespan` 이며, 일 수의 낮은 쪽 정수로 내림됩니다.

**예**

```AIQL
dayofweek(1947-11-29 10:00:05)  // time(6.00:00:00), indicating Saturday
dayofweek(1970-05-11)           // time(1.00:00:00), indicating Monday
```

### <a name="dayofyear"></a>dayofyear
    dayofyear(datetime("2016-05-31")) == 152 
    dayofyear(datetime("2016-01-01")) == 1 

년 중 일의 서수입니다.

**구문**

    dayofyear(a_date)

**인수**

* `a_date`: `datetime`입니다.

<a name="endofday"></a><a name="endofweek"></a><a name="endofmonth"></a><a name="endofyear"></a>

### <a name="endofday-endofweek-endofmonth-endofyear"></a>endofday, endofweek, endofmonth, endofyear
    dt = datetime("2016-05-23 12:34")

    endofday(dt) == 2016-05-23T23:59:59.999
    endofweek(dt) == 2016-05-28T23:59:59.999 // Saturday
    endofmonth(dt) == 2016-05-31T23:59:59.999 
    endofyear(dt) == 2016-12-31T23:59:59.999 


### <a name="getmonth"></a>getmonth
datetime에서 월 번호(1-12)를 가져옵니다.

**예제**

    ... | extend month = getmonth(datetime(2015-10-12))

    --> month == 10

### <a name="getyear"></a>getyear
datetime에서 연도를 가져옵니다.

**예제**

    ... | extend year = getyear(datetime(2015-10-12))

    --> year == 2015

### <a name="now"></a>now
    now()
    now(-2d)

현재 UTC 시계 시간, 선택적으로 지정된 시간 범위에 대한 간격 표시. 이 함수를 문에 여러 번 사용할 수 있으며 참조하는 시계 시간은 모든 인스턴스에 대해 동일합니다.

**구문**

    now([offset])

**인수**

* *offset:* 현재 UTC 시계 시간에 더해지는 `timespan`입니다. 기본값: 0.

**반환**

`datetime`(으)로 반환되는 현재 UTC 시계 시간입니다.

    now() + offset

**예제**

조건자에 의해 식별된 이벤트 이후 간격을 결정;

```AIQL
T | where ... | extend Elapsed=now() - timestamp
```

<a name="startofday"></a><a name="startofweek"></a><a name="startofmonth"></a><a name="startofyear"></a>

### <a name="startofday-startofweek-startofmonth-startofyear"></a>startofday, startofweek, startofmonth, startofyear
    date=datetime("2016-05-23 12:34:56")

    startofday(date) == datetime("2016-05-23")
    startofweek(date) == datetime("2016-05-22") // Sunday
    startofmonth(date) == datetime("2016-05-01")
    startofyear(date) == datetime("2016-01-01")



### <a name="todatetime"></a>todatetime
별칭 `datetime()`입니다.

     todatetime("2016-03-28")
     todatetime("03/28/2016")
     todatetime("2016-03-28 14:34:00")
     todatetime("03/28/2016 2:34pm")
     todatetime("2016-03-28T14:34.5Z")
     todatetime(a[0]) 
     todatetime(b.c) 

문자열이 유효한 날짜인지 여부를 확인합니다.

     iff(notnull(todatetime(customDimensions.myDate)),
         ..., ...)


### <a name="totimespan"></a>totimespan
별칭 `timespan()`입니다.

    totimespan("21d")
    totimespan("21h")
    totimespan(request.duration)

### <a name="weekofyear"></a>weekofyear
    weekofyear(datetime("2016-05-14")) == 21
    weekofyear(datetime("2016-01-03")) == 1
    weekofyear(datetime("2016-12-31")) == 53

정수 결과는 ISO 8601 표준에 따라 주차를 나타냅니다. 주의 첫째 요일은 일요일이고, 년의 첫째 주는 해당 년의 첫 번째 목요일을 포함하는 주입니다. 따라서 년의 마지막 주중 일에 다음 년의 첫째 주에 포함된 일부 일이 포함되거나, 첫째 주중 일에 이전 년의 52번째 또는 53번째 주 일부가 포함될 수 있습니다.

## <a name="string"></a>String
[countof](#countof) | [추출](#extract) | [extractjson](#extractjson)  | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty) | [parseurl](#parseurl) | [대체](#replace) | [분할](#split) | [strcat](#strcat) | [strlen](#strlen) | [부분 문자열](#substring) | [tolower](#tolower) | [tostring](#tostring) | [toupper](#toupper)

### <a name="string-literals"></a>문자열 리터럴
규칙은 JavaScript에서와 동일 합니다.

문자열을 작은따옴표 또는 큰따옴표 문자로 묶을 수 있습니다. 

백슬래시(`\`)는 `\t`(탭), `\n`(줄 바꿈)과 같은 문자 및 묶는 따옴표 문자의 인스턴스를 이스케이프하는 데 사용됩니다.

* `'this is a "string" literal in single \' quotes'`
* `"this is a 'string' literal in double \" quotes"`
* `@"C:\backslash\not\escaped\with @ prefix"`

### <a name="obfuscated-string-literals"></a>난독 처리된 문자열 리터럴
난독 처리된 문자열 리터럴은 문자열을 출력할 때(예: 추적하는 경우) 분석이 가리는 문자열입니다. 난독 처리 프로세스는 모든 난독 처리된 문자를 시작(`*`) 문자에 의해 바꿉니다.

난독 처리된 문자열 리터럴을 형성하려면 앞에 `h` 또는 'H'를 추가합니다. 예:

```
h'hello'
h@'world' 
h"hello"
```

### <a name="string-comparisons"></a>문자열 비교
| 연산자 | 설명 | 대/소문자 구분 | True 예제 |
| --- | --- | --- | --- |
| `==` |같음 |예 |`"aBc" == "aBc"` |
| `<>` `!=` |같지 않음 |예 |`"abc" <> "ABC"` |
| `=~` |같음 |아니요 |`"abc" =~ "ABC"` <br/>`boolAsString =~ "true"` |
| `!~` |같지 않음 |아니요 |`"aBc" !~ "xyz"` |
| `has` |오른쪽(RHS)이 왼쪽(LHS)의 전체 항임 |아니요 |`"North America" has "america"` |
| `!has` |RHS가 LHS의 전체 항이 아님 |아니요 |`"North America" !has "amer"` |
| `hasprefix` |RHS가 LHS에 있는 항의 접두사임 |아니요 |`"North America" hasprefix "ame"` |
| `!hasprefix` |RHS가 LHS에 있는 항의 접두사가 아님 |아니요 |`"North America" !hasprefix "mer"` |
| `hassuffix` |RHS가 LHS에 있는 항의 접미사임 |아니요 |`"North America" hassuffix "rth"` |
| `!hassuffix` |RHS가 LHS에 있는 항의 접미사가 아님 |아니요 |`"North America" !hassuffix "mer"` |
| `contains` |RHS가 LHS의 하위 문자열로 발생함 |아니요 |`"FabriKam" contains "BRik"` |
| `!contains` |RHS가 LHS에서 발생하지 않음 |아니요 |`"Fabrikam" !contains "xyz"` |
| `containscs` |RHS가 LHS의 하위 문자열로 발생함 |예 |`"FabriKam" contains "Kam"` |
| `!containscs` |RHS가 LHS에서 발생하지 않음 |예 |`"Fabrikam" !contains "Kam"` |
| `startswith` |RHS가 LHS의 초기 하위 문자열임 |아니요 |`"Fabrikam" startswith "fab"` |
| `!startswith` |RHS가 LHS의 초기 하위 문자열이 아님 |아니요 |`"Fabrikam" !startswith "abr"` |
| `endswith` |RHS가 LHS의 종료 하위 문자열임 |아니요 |`"Fabrikam" endswith "kam"` |
| `!endswith` |RHS가 LHS의 종료 하위 문자열이 아님 |아니요 |`"Fabrikam" !endswith "ka"` |
| `matches regex` |LHS가 RHS에 대한 일치 항목을 포함 |예 |`"Fabrikam" matches regex "b.*k"` |
| [`in`](#in) |요소와 같음 |예 |`"abc" in ("123", "345", "abc")` |
| [`!in`](#in) |요소와 같지 않음 |예 |`"bc" !in ("123", "345", "abc")` |

전체 어휘 용어의 존재를 시험하는 경우 `has` 또는 `in`을 사용합니다. 즉, 기호 또는 필드 시작 또는 끝에 경계가 지어진 영숫자 단어입니다. `has` 는(은) `contains`, `startswith` 또는 `endswith` 보다 빠르게 수행합니다. 이러한 쿼리의 첫 번째 쿼리가 더 빠르게 실행됩니다.

    EventLog | where continent has "North" | count;
    EventLog | where continent contains "nor" | count





### <a name="countof"></a>countof
    countof("The cat sat on the mat", "at") == 3
    countof("The cat sat on the mat", @"\b.at\b", "regex") == 3

문자열의 부분 문자열 발생을 계산합니다. 일반 문자열 일치는 겹칠 수 있으며, 정규식 일치는 겹칠 수 없습니다.

**구문**

    countof(text, search [, kind])

**인수**

* *text:* 문자열입니다.
* *search:* *text*내에서 일치하는 일반 문자열 또는 정규식입니다.
* *kind:* `"normal"|"regex"`입니다(기본값: `normal`). 

**반환**

검색 문자열이 컨테이너에서 일치시킬 수 있는 횟수입니다. 일반 문자열 일치는 겹칠 수 있으며, 정규식 일치는 겹칠 수 없습니다.

**예**

|  |  |
| --- | --- |
| `countof("aaa", "a")` |3 |
| `countof("aaaa", "aa")` |3(2 아님!) |
| `countof("ababa", "ab", "normal")` |2 |
| `countof("ababa", "aba")` |2 |
| `countof("ababa", "aba", "regex")` |1 |
| `countof("abcabc", "a.c", "regex")` |2 |

### <a name="extract"></a>extract
    extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"

텍스트 문자열에서 [정규식](#regular-expressions) 에 대한 일치 항목을 가져옵니다. 선택적으로 그런 다음 추출된 부분 문자열을 지시된 유형으로 변환합니다.

**구문**

    extract(regex, captureGroup, text [, typeLiteral])

**인수**

* *regex:*[정규식](#regular-expressions)입니다.
* *captureGroup:* 추출할 캡처 그룹을 나타내는 양의 `int` 상수입니다. 0은 전체 일치, 1은 정규식의 첫 번째 '(' 괄호')'에 의해 일치된 값, 2 이상은 이후 괄호를 나타냅니다.
* *text:*  `string` 입니다.
* *typeLiteral:* 선택적 형식 리터럴(예: `typeof(long)`)입니다. 제공된 경우 추출된 부분 문자열이 이 형식으로 변환됩니다. 

**반환**

*regex*가 *text*에서 일치 항목을 찾은 경우: 지시된 캡처 그룹 *captureGroup*에 대해 일치된 부분 문자열, 선택적으로 *typeLiteral*로 변환됩니다.

일치 항목이 없거나 형식 변환에 실패한 경우 `null`입니다. 

**예**

예제 문자열 `Trace`에서 `Duration`에 대한 정의를 검색합니다. 일치 항목은 `real`로 변환된 다음, 시간 상수(`1s`)를 곱하여 `Duration`이 `timespan` 형식이 됩니다. 이 예제에서 이는 123.45초와 같음:

```AIQL
...
| extend Trace="A=1, B=2, Duration=123.45, ..."
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s) 
```

이 예제는 `substring(Text, 2, 4)`과 같습니다.

```AIQL
extract("^.{2,2}(.{4,4})", 1, Text)
```

<a name="notempty"></a>
<a name="isnotempty"></a>
<a name="isempty"></a>



### <a name="isempty-isnotempty-notempty"></a>isempty, isnotempty, notempty
    isempty("") == true

인수가 비어 있거나 null이면 True입니다.
[isnull](#isnull)을 참조하세요.

**구문**

    isempty([value])


    isnotempty([value])


    notempty([value]) // alias of isnotempty

**반환**

인수가 빈 문자열인지 아니면 null인지를 나타냅니다.

| x | isempty(x) |
| --- | --- |
| "" |true |
| "x" |false |
| parsejson("") |true |
| parsejson("[]") |false |
| parsejson("{}") |false |

**예제**

    T | where isempty(fieldName) | count


### <a name="parseurl"></a>parseurl
URL을 해당 부분으로 분할합니다.

**구문**

    parseurl(urlstring)

**인수**

* *urlstring:* URL입니다.

**반환**

문자열로 부분을 포함하는 개체입니다.

**예제**

    parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")

    {
    "Scheme" : "http",
    "Host" : "contoso.com",
    "Port" : "80",
    "Path" : "/icecream/buy.aspx",
    "Username" : "user",
    "Password" : "pass",
    "Query Parameters" : {"a":"1","b":"2"},
    "Fragment" : "tag"
    }

### <a name="replace"></a>replace
모든 정규식 일치 항목을 다른 문자열로 바꿉니다.

**구문**

    replace(regex, rewrite, text)

**인수**

* *regex:* *text*를 검색할 [정규식](https://github.com/google/re2/wiki/Syntax)입니다. '('괄호')'에 캡처 그룹을 포함할 수 있습니다. 
* *rewrite:* *matchingRegex*에 의해 수행된 모든 일치에 대한 대체 정규식입니다. 전체 일치를 참조하려면 `\0`, 첫 번째 캡처 그룹을 참조하려면 `\1`, 이후 캡처 그룹을 참조하려면 `\2`를 사용하는 식입니다.
* *text:* 문자열입니다.

**반환**

*regex*의 모든 일치 항목을 *rewrite*로 바꾼 후의 *text*입니다. 일치 항목은 겹치지 않습니다.

**예제**

이 문은:

```AIQL
range x from 1 to 5 step 1
| extend str=strcat('Number is ', tostring(x))
| extend replaced=replace(@'is (\d+)', @'was: \1', str)
```

다음과 같은 결과를 나타냄:

| x | str | 대체됨 |
| --- | --- | --- |
| 1 |숫자는 1.000000임 |이전 숫자: 1.000000 |
| 2 |숫자는 2.000000임 |이전 숫자: 2.000000 |
| 3 |숫자는 3.000000임 |이전 숫자: 3.000000 |
| 4 |숫자는 4.000000임 |이전 숫자: 4.000000 |
| 5 |숫자는 5.000000임 |이전 숫자: 5.000000 |

### <a name="split"></a>분할
    split("aaa_bbb_ccc", "_") == ["aaa","bbb","ccc"]

지정된 구분 기호에 따라 지정된 문자열을 분할하고 포함된 부분 문자열과 함께 문자열 배열을 반환합니다. 선택적으로 특정 부분 문자열(있는 경우)을 반환할 수 있습니다.

**구문**

    split(source, delimiter [, requestedIndex])

**인수**

* *source*: 지정된 구분 기호에 따라 분할될 소스 문자열입니다.
* *delimiter*: 소스 문자열을 분할하기 위해 사용될 구분 기호입니다.
* *requestedIndex*: 선택적 0부터 시작하는 인덱스 `int`입니다. 제공된 경우, 반환되는 문자열 배열은 요청된 부분 문자열(있는 경우)을 포함합니다. 

**반환**

지정된 구분 기호로 구분되는 지정된 소스 문자열의 부분 문자열을 포함하는 문자열의 배열입니다.

**예**

```
split("aa_bb", "_")           // ["aa","bb"]
split("aaa_bbb_ccc", "_", 1)  // ["bbb"]
split("", "_")                // [""]
split("a__b")                 // ["a","","b"]
split("aabbcc", "bb")         // ["aa","cc"]
```




### <a name="strcat"></a>strcat
    strcat("hello", " ", "world")

인수 1개에서 16개 사이를 연결하며, 인수는 문자열이어야 합니다.

### <a name="strlen"></a>strlen
    strlen("hello") == 5

문자열의 길이입니다.

### <a name="substring"></a>substring
    substring("abcdefg", 1, 2) == "bc"

지정된 인덱스에서 시작하여 지정된 소스 문자열에서 부분 문자열을 추출합니다. 선택적으로 요청된 부분 문자열의 길이를 지정할 수 있습니다.

**구문**

    substring(source, startingIndex [, length])

**인수**

* *source:* 소스 문자열을 가져올 소스 문자열입니다.
* *startingIndex:* 요청된 부분 문자열의 0부터 시작하는 시작 문자 위치입니다.
* *length:* 부분 문자열에서 요청된 문자 수를 지정하는 데 사용할 수 있는 선택적 매개 변수입니다. 

**반환**

지정된 문자열에서 나온 부분 문자열입니다. 부분 문자열은 startingIndex(0 기반) 문자 위치에서 시작하며 문자열의 끝 또는 length 문자(지정된 경우)까지 계속합니다.

**예**

```
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
```

### <a name="tolower"></a>tolower
    tolower("HELLO") == "hello"

문자열을 소문자로 변환합니다.

### <a name="toupper"></a>toupper
    toupper("hello") == "HELLO"

문자열을 대문자로 변환합니다.

### <a name="guids"></a>GUIDs
    guid(00000000-1111-2222-3333-055567f333de)


## <a name="arrays-objects-and-dynamic"></a>배열, 개체 및 동적
[리터럴](#dynamic-literals) | [캐스팅](#casting-dynamic-objects) | [연산자](#operators) | [절 사용](#dynamic-objects-in-let-clauses)
<br/>
[arraylength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [range](#range) | [treepath](#treepath) | [todynamic](#todynamic) | [zip](#zip)

다음은 Application Insights 예외에 대한 쿼리의 결과입니다. `details` 의 값은 배열입니다.

![](./media/app-insights-analytics-reference/310.png)

**Indexing:** JavaScript에서와 같은 인덱스 배열 및 개체입니다.

    exceptions | take 1
    | extend 
        line = details[0].parsedStack[0].line,
        stackdepth = arraylength(details[0].parsedStack)

* 하지만 `arraylength` 및 다른 분석 함수(".length" 아님!)를 사용합니다.

**Casting:** 경우에 따라 형식이 달라질 수 있기 때문에 개체에서 추출하는 요소를 캐스트해야 합니다. 예를 들어 `summarize...to` 에 특정 형식이 필요합니다.

    exceptions 
    | summarize count() 
      by toint(details[0].parsedStack[0].line)

    exceptions
    | summarize count()
      by tostring(details[0].parsedStack[0].assembly)

**Literals:** 명시적 배열 또는 속성 모음 개체를 만들려면 JSON 문자열로 쓰고 캐스트합니다.

    todynamic('[{"x":"1", "y":"32"}, {"x":"6", "y":"44"}]')


**mvexpand:** 개체의 속성을 별도의 행으로 끌어오려면 mvexpand를 사용합니다.

    exceptions | take 1
    | mvexpand details[0].parsedStack[0]


![](./media/app-insights-analytics-reference/410.png)

**treepath:** 복합 개체에서 모든 경로를 찾으려면 다음을 수행합니다.

    exceptions | take 1 | project timestamp, details
    | extend path = treepath(details)
    | mvexpand path


![](./media/app-insights-analytics-reference/420.png)

**buildschema:** 테이블에 있는 식의 모든 값을 인정하는 최소 스키마를 찾으려면 다음을 수행합니다.

    exceptions | summarize buildschema(details)

결과:

    { "indexer":
     {"id":"string",
       "parsedStack":
       { "indexer":
         {  "level":"int",
            "assembly":"string",
            "fileName":"string",
            "method":"string",
            "line":"int"
         }},
      "outerId":"string",
      "message":"string",
      "type":"string",
      "rawStack":"string"
    }}

참고로 `indexer`은(는) 숫자 인덱스를 사용해야 하는 위치를 표시하기 위해 사용됩니다. 이 스키마의 경우 일부 유효한 경로는 다음과 같을 수 있습니다(이 예제 인덱스가 범위 안에 든다고 가정).

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)



### <a name="array-and-object-literals"></a>배열 및 개체 리터럴
동적 리터럴을 만들려면 JSON 문자열 인수와 함께 `parsejson`(별칭 `todynamic`)을 사용합니다.

* `parsejson('[43, 21, 65]')` - 숫자의 배열
* `parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')`
* `parsejson('21')` - 숫자를 포함하는 동적 형식의 단일 값
* `parsejson('"21"')` - 문자열을 포함하는 동적 형식의 단일 값

> [!NOTE]
> 큰따옴표(`"`)는 JSON에서 레이블 및 문자열 값을 묶는 데 사용해야 합니다. 그러므로 일반적으로 작은따옴표(`'`)를 사용하여 JSON 인코딩 문자열 리터럴을 인용하는 것이 더 쉽습니다.
> 

이 예제에서는 동적 값을 만든 다음 해당 필드를 사용함:

```

T
| extend person = parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')
| extend n = person.name, add = person.address.street
```


### <a name="dynamic-object-functions"></a>동적 개체 함수
|  |  |
| --- | --- |
| [*value* `in` *array*](#in) |*array*에서 *value* 포함 |
| [*value* `!in` *array*](#in) |*array*에서 *value* 포함 안 함 |
| [`arraylength(`array`)`](#arraylength) |배열이 아니면 Null |
| [`extractjson(`path,object`)`](#extractjson) |path를 사용하여 object를 탐색합니다. |
| [`parsejson(`source`)`](#parsejson) |JSON 개체를 동적 개체로 변환합니다. |
| [`range(`from,to,step`)`](#range) |값의 배열 |
| [`mvexpand` listColumn](#mvexpand-operator) |각 값에 대한 행을 지정된 셀의 목록에 복제합니다. |
| [`summarize buildschema(`열`)`](#buildschema) |열 내용에서 형식 스키마를 유추 |
| [`summarize makelist(`열`)` ](#makelist) |행 그룹을 평면화하고 열의 값을 배열에 넣습니다. |
| [`summarize makeset(`열`)`](#makeset) |행 그룹을 평면화하고 열의 값을 중복 없이 배열에 넣습니다. |

### <a name="dynamic-objects-in-let-clauses"></a>let 절의 동적 개체
[Let 절](#let-clause)은 동적 값을 문자열로 저장하므로 이 두 절은 동일하며 둘 다 사용하기 전에 `parsejson` 또는 `todynamic`이(가) 필요합니다.

    let list1 = '{"a" : "somevalue"}';
    let list2 = parsejson('{"a" : "somevalue"}');

    T | project parsejson(list1).a, parsejson(list2).a


### <a name="in"></a>in
    value in (listExpression)
    value !in (listExpression)

값에 해당하는 항목이 목록에 있는지 여부를 결정합니다. 값이 문자열인 경우 대/소문자를 구분합니다.

**인수**

* `value`: 스칼라 식입니다.
* `listExpression`...: 스칼라 식의 목록 또는 목록으로 평가되는 식입니다. 

중첩된 배열은 단일 목록으로 결합됩니다. 예를 들어 `where x in (dynamic([1,[2,3]]))`은 `where x in (1,2,3)`이 됩니다.  

**예**

```AIQL
    requests | where client_City in ("London", "Paris", "Rome")
```

```AIQL
let cities = dynamic(['Dublin','Redmond','Amsterdam']);
requests | where client_City in (cities) 
|  summarize count() by client_City
```

계산된 목록:

```AIQL
let topCities =  toscalar ( // convert single column to value
   requests
   | summarize count() by client_City 
   | top 4 by count_ 
   | summarize makeset(client_City)) ;
requests
| where client_City in (topCities) 
| summarize count() by client_City;
```

함수 호출을 목록 식으로 사용:

```AIQL
let topCities =  (n:int) {toscalar (
   requests
   | summarize count() by client_City 
   | top n by count_ 
   | summarize makeset(client_City)) };
requests
| where client_City in (topCities(3)) 
| summarize count() by client_City;
```
 

### <a name="arraylength"></a>arraylength
동적 배열 내의 요소 수입니다.

**구문**

    arraylength(array)

**인수**

* *array:* `dynamic` 값입니다.

**반환**

*array*의 배열 수 또는 *array*가 배열이 아닌 경우 `null`입니다.

**예**

```
arraylength(parsejson('[1, 2, 3, "four"]')) == 4
arraylength(parsejson('[8]')) == 1
arraylength(parsejson('[{}]')) == 1
arraylength(parsejson('[]')) == 0
arraylength(parsejson('{}')) == null
arraylength(parsejson('21')) == null
```



### <a name="extractjson"></a>extractjson
    extractjson("$.hosts[1].AvailableMB", EventText, typeof(int))

path 식을 사용하여 JSON 텍스트에서 지정된 요소를 가져옵니다. 선택적으로 추출한 문자열을 특정 형식으로 변환합니다.

**구문**

```

    string extractjson(jsonPath, dataSource) 
    resulttype extractjson(jsonPath, dataSource, typeof(resulttype))
```


**반환**

이 함수는 유효한 JSON 문자열을 포함하고 있는 dataSource에 대해 JsonPath 쿼리를 수행하며, 선택적으로 이 값을 세 번째 인수에 따라 다른 형식으로 변환합니다.

**예제**

[대괄호] 표기와 점 표기는 동일합니다.

    ... | extend AvailableMB = extractjson("$.hosts[1].AvailableMB", EventText, typeof(int)) | ...

    ... | extend AvailableMD = extractjson("$['hosts'][1]['AvailableMB']", EventText, typeof(int)) | ...



**성능 팁**

* `extractjson()`
* 정규식 일치 사용은 [extract](#extract) 를 대신 사용하는 것으로 간주합니다. 이렇게 하면 훨씬 더 빠르게 실행될 수 있으며 JSON이 템플릿에서 생성된 경우 효과적입니다.
* JSON에서 값을 둘 이상 추출해야 하는 경우 `parsejson()`을 사용합니다.
* 열 형식을 동적으로 선언하여 수집 시 JSON이 구문 분석되게 하는 것으로 간주합니다.

### <a name="json-path-expressions"></a>JSON Path 식
|  |  |
| --- | --- |
| `$` |루트 개체 |
| `@` |현재 개체 |
| `[0]` |배열 첨자 |
| `.` 또는 `[0]` |자식 |

*(현재 와일드카드, 재귀, 합집합 또는 조각이 구현되지 않았습니다.)*

### <a name="parsejson"></a>parsejson
`string`을 [JSON 값](http://json.org/)으로 해석하고 값을 `dynamic`으로 반환합니다. JSON 복합 개체의 요소를 둘 이상 추출해야 하는 경우 `extractjson()` 을 사용하는 것이 좋습니다.

**구문**

    parsejson(json)

**인수**

* *json:* JSON 문서입니다.

**반환**

*json*에 의해 지정된 형식 `dynamic`의 개체입니다.

**예제**

다음 예제에서 `customDimensions.person`은 `string`이며 다음과 유사합니다. 

```
"\"addresses\":[{\"postcode\":\"C789\",\"street\":\"high st\",\"town\":\"Cardigan\"},{\"postcode\":\"J456\",\"street\":\"low st\",\"town\":\"Jumper\"}],\"name\":\"Ada\""
```

그러면 다음 조각은 개체에 있는 `duration` 슬롯의 값을 검색하며 거기서부터 `duration.value` 및 `duration.min`의 두 슬롯을 검색합니다(각각 `118.0` 및 `110.0`).

```AIQL
customEvents
| where name == "newMember"
| extend d=parsejson(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```

> [!NOTE]
> 큰따옴표 문자는 JSON에서 레이블 및 문자열 값을 묶는 데 사용해야 합니다. 
>


### <a name="range"></a>range
`range()` 함수(`range` 연산자와 혼동하지 말 것)는 간격이 같은 일련의 값을 저장하는 동적 배열을 생성합니다.

**구문**

    range(start, stop, step)

**인수**

* *start:* 결과 배열의 첫 번째 요소의 값입니다. 
* *stop:* 결과 배열의 마지막 요소의 값 또는 결과 배열 및 *start*부터 *step*의 정수 배 내에서 마지막 요소보다 더 큰 가장 작은 값입니다.
* *step:* 배열의 연속된 두 요소 사이의 차이입니다.

**예**

다음 예제는 `[1, 4, 7]`을 반환합니다.

```AIQL
range(1, 8, 3)
```

다음 예제는 2015 년의 모든 날짜를 저장하는 배열을 반환합니다.

```AIQL

    range(datetime(2015-01-01), datetime(2015-12-31), 1d)
```

### <a name="todynamic"></a>todynamic
    todynamic('{"a":"a1", "b":["b1", "b2"]}')

문자열을 동적 값으로 변환합니다.

### <a name="treepath"></a>treepath
    treepath(dynamic_object)

동적 개체의 리프를 식별하는 모든 path 식을 열거합니다. 

**반환**

path 식의 배열입니다.

**예**

    treepath(parsejson('{"a":"b", "c":123}')) 
    =>       ["['a']","['c']"]
    treepath(parsejson('{"prop1":[1,2,3,4], "prop2":"value2"}'))
    =>       ["['prop1']","['prop1'][0]","['prop2']"]
    treepath(parsejson('{"listProperty":[100,200,300,"abcde",{"x":"y"}]}'))
    =>       ["['listProperty']","['listProperty'][0]","['listProperty'][0]['x']"]

참고로 “[0]”은 배열이 존재하지만 특정 경로에 사용되는 인덱스를 지정하지 않았음을 나타냅니다.

### <a name="zip"></a>zip
    zip(list1, list2, ...)

목록 집합을 하나의 튜플 목록으로 결합합니다.

* `list1...`: 값의 목록

**예**

    zip(parsejson('[1,3,5]'), parsejson('[2,4,6]'))
    => [ [1,2], [3,4], [5,6] ]


    zip(parsejson('[1,3,5]'), parsejson('[2,4]'))
    => [ [1,2], [3,4], [5,null] ]


### <a name="names"></a>이름
이름은 최대 1024자까지 가능합니다. 이름은 대/소문자를 구분하며 문자, 숫자 및 밑줄(`_`)을 포함할 수 있습니다. 

다른 문자를 포함하거나 이름으로 키워드를 사용하려면 [' ... '] 또는 [" ... "]를 사용하여 이름을 따옴표로 묶습니다. 예:

```AIQL

    requests | 
    summarize  ["distinct urls"] = dcount(name) // non-alphanumerics
    by  ['where'] = client_City, // using a keyword as a name
        ['outcome!'] = success // non-alphanumerics
```


|  |  |
| --- | --- |
| ['경로\\file\n\'x\''] |이스케이프 문자에 \를 사용 |
| ["d-e.=/f#\n"] | |
| [@'path\file'] |이스케이프 없음 - \는 리터럴입니다. |
| [@"\now & then\"] | |
| [where] |언어 키워드를 이름으로 사용 |

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]


