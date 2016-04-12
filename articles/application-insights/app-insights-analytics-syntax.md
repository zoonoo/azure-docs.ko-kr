<properties 
	pageTitle="분석 Application Insights의 문" 
	description="Application Insights의 강력한 검색 도구인 분석의 쿼리, 식 및 let 문입니다." 
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
	ms.date="03/30/2016" 
	ms.author="awills"/>

 
# Application Insights의 분석 문

[분석](app-insights-analytics.md)은 [Application Insights](app-insights-overview.md)의 유용한 검색 기능입니다. 다음 페이지에서는 분석 쿼리 언어에 대해 설명합니다.

[AZURE.INCLUDE [app-insights-analytics-top-index](../../includes/app-insights-analytics-top-index.md)]

## 데이터 모델

AIQL에는 다음이 포함됩니다.

* *데이터베이스*가 0개 이상의 명명된 *테이블*을 포함합니다.
* *테이블*에는 다음이 포함됩니다.
 * 하나 이상의 명명된 *열.* 각 열에 *형식*이 있습니다.
 * 하나 이상의 *행.*
* 각 행에 하나 이상의 *셀*이 해당 테이블의 각 열에 하나씩 있습니다.
* 셀은 해당 열 형식의 값 또는 `null`을 포함할 수 있습니다.


## 엔터티 이름

모든 열, 테이블 또는 데이터베이스에는 해당 컨테이너 내에서 고유한 이름이 있습니다.

컨텍스트가 명확한 경우 이름으로 엔터티를 참조하고 그렇지 않고 정규화된 경우 해당 컨테이너로 엔터티를 참조합니다. 예를 들어 `MyColumn`은 `MyTable.MyColumn` 또는 `MyDatabase.MyTable.MyColumn`으로도 참조할 수 있습니다.

이름은 최대 1024자까지 가능합니다. 이름은 대/소문자를 구분하며 문자, 숫자 및 밑줄(`_`)을 포함할 수 있습니다.

또한 이름은 따옴표로 묶어서 다른 문자를 포함할 수 있습니다. 예:

|||
|---|---|
|`['path\\file\n\'x\'']` | 이스케이프 문자에 ``를 사용|
|`["d-e.=/f#\n"]` | |
|`[@'path\file']`| 이스케이프 없음 - ``는 리터럴입니다.|
|`[@"\now & then"]` | |
|`[where]` | 언어 키워드를 이름으로 사용|

이름은 데이터베이스 이름으로 한정할 수도 있습니다([데이터베이스 간 쿼리](#cross-database-queries) 참조).

```
database("MyDb").Table
```


## 문

CSL에는 네 종류의 문이 있습니다.

### 데이터 쿼리
  
분석에 저장된 데이터에 대한 읽기 전용 요청입니다. 예:

* `event` - "event"라는 테이블에서 모든 레코드를 반환합니다.
* `event | count` - "event"에서 레코드 수를 반환합니다.

    
## let 문

#### 개요
데이터 쿼리 문에 하나 이상의 let 문을 접두사로 붙일 수 있습니다. 이렇게 하면 식에 이름(유효한 엔터티 이름)을 바인딩할 수 있습니다. 그러면 let 문으로 정의된 이름은 다음 데이터 쿼리 문에서 한 번 이상 사용할 수 있습니다.

let 문은 다음과 같은 종류의 식에 이름을 바인딩할 수 있습니다.
1. 스칼라
2. 표 형식 데이터 

표 형식 데이터에 바인딩하는 경우 let 문을 "view"로 승격하여 "union *" 작업에 참여할 수 있습니다.

#### 구문

`let <name> = <expression>`

<expression>은 인수를 사용하지 않거나 하나 이상 사용하는 람다 선언일 수 있습니다.

`() {...}`

`(<arg0>:<type0>, ...) {...}`

예제: 다음 예제에서는 이름 'x'를 스칼라 리터럴 '1'에 바인딩합니다.

```
let x=1;
range y from x to x step x
```

다음 예제에서는 이름 Window에 한 시간의 로그를 바인딩하고 Window에서 셀프 조인을 수행합니다.


```
let Window=Logs | where Timestamp > ago(1h);
Window | where  ... | join (Window | where …) on ActivityId| ...
```

다음 두 예제에서는 람다 식을 사용하여 let 문을 사용하는 것을 보여 줍니다.


```
let Test = () { range x from 1 to 10 step 1 };
Test | count

let step=1;
let Test = (start:long, end:long) { range x from start to end step 1 };
Test(1, 10) | count
```

'view' 키워드를 사용하여 let 문을 union * 작업에 참여하는 View로 승격합니다.


```
let Test1 = view () { range x from start to end step 1 };
let Test2 = view () { range x from start to end step 1 };
union * | count
// Result: 20
```


## restrict 문

#### 개요
다음 문을 사용하여 문 목록의 범위에서 액세스하는 액세스 쿼리를 제한할 수 있습니다.


```
restrict access to (<entity1, entity2, ...>);
```
 
문은 restrict 문에 의해 허용된 엔터티(let 문 또는 표 형식 엔터티)에만 액세스할 수 있습니다.
 
예제:

```
// Restricting access to Test statement only
let Test = () { range x from 1 to 10 step 1 };
restrict access to (Test);
Test
 
// Assume that there is a table called Table1, Table2 in the database
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
 
// When those statements appear before the command - the next works
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
View1 |  count
 
// When those statements appear before the command - the next access is not allowed
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
Table1 |  count
```


## 쿼리 컴퍼지션

*쿼리*는 다음 패턴을 따릅니다.

- *원본* | *필터* | *필터* ...

예:


```
Logs | where Timestamp > ago(1d) | count
```

* *원본*은 데이터베이스 테이블 또는 이전에 정의된 결과 테이블의 이름입니다.
* 각 *필터*는 적절한 매개 변수를 사용하여 `where` 또는 `count`와 같은 쿼리 연산자를 호출합니다. 매개 변수는 *스칼라 식*, 중첩된 * 데이터 쿼리* 또는 열 이름이 될 수 있습니다.

예:


```
Logs 
| where Timestamp > ago(1d) 
| join 
(
    Events 
    | where continent == 'Europe'
) on RequestId 
``` 

## let 문

let 문은 함수(0개 이상의 인수를 갖고 값을 반환하는 명명된 식)를 정의하는 데 사용할 수 있습니다. 그러면 추가 문으로 이러한 함수를 "호출"할 수 있습니다.

### 명명된 값

스칼라 값을 나타내는 이름을 정의합니다.


```
let n = 10;  // number
let place = "Dallas";  // string
let cutoff = ago(62d); // datetime
Events 
| where timestamp > cutoff 
    and city == place 
| take n
```

쿼리 결과를 나타내는 이름을 정의합니다.


```
let Cities = Events | summarize dcount(city) by country;
Cities | take 10
```

셀프 조인에 특히 유용합니다.


```
let Recent = Events | where timestamp > ago(7d);
Recent | where name contains "session_started" 
| project start = timestamp, session_id
| join (Recent 
        | where name contains "session_ended" 
        | project stop = timestamp, session_id)
    on session_id
| extend duration = stop - start 
```

## 명명된 함수

스칼라 결과를 반환하는 함수를 정의합니다.


```
let square = (n:long){n*n};
// yield 9 rows
Events | take square(3)    
```

쿼리 결과를 반환하는 함수를 정의합니다.


```
let TopEvents= (n:long, when:datetime){Events 
                | where timestamp > when | take n};
TopEvents(5, ago(7d)) 
```

명명된 함수의 매개 변수는 스칼라여야 합니다.





[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!---HONumber=AcomDC_0330_2016-->