<properties 
	pageTitle="Application Insights 분석의 스칼라 식" 
	description="Application Insights의 강력한 검색 도구인 분석의 숫자, 문자열, 동적 식 및 형식" 
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

 
# 분석의 스칼라 식


[분석](app-insights-analytics.md)은 
[Application Insights](app-insights-overview.md)의 강력한 검색 기능입니다. 
다음 페이지에서는 분석 쿼리 언어에 대해 설명합니다.

[AZURE.INCLUDE [app-insights-analytics-top-index](../../includes/app-insights-analytics-top-index.md)]

---

[ago](#ago) | [arraylength](#arraylength) | [bin](#bin) [countof](#countof) | [dayofweek](#dayofweek) | [extract](#extract) | [extractjson](#extractjson) | [floor](#floor) 
<br/>[getmonth](#getmonth) | [gettype](#gettype) [getyear](#getyear) | [hash](#hash) | [iff](#iff) | [isempty](#isempty) | [isnotempty](#isnotempty) | [isnull](#isnull) | [isnotnull](#isnotnull)
<br/> [now](#now) | [notempty](#notempty) | [notnull](#notnull) | [parsejson](#parsejson)| [rand](#rand) | [range](#range) | [replace](#replace) 
| [itemCount](#itemCount) | [split](#split) | [sqrt](#sqrt) 
<br/>[startofmonth](#startofmonth) | [startofyear](#startofyear) | [strcat](#strcat) | [strlen](#strlen) | [substring](#substring) 
| [tolower](#tolower) | [toupper](#toupper) | [treepath](#treepath)

---



"스칼라"는 숫자 또는 문자열과 같이 테이블에서 단일 셀을 차지할 수 있는 값을 의미합니다. 스칼라 식은 스칼라 함수 및 연산자에서 만들어집니다. `sqrt(score)/100 > target+2`는 스칼라 식입니다.

"스칼라"는 역시 단일 데이터베이스 셀에 저장할 수 있는 배열 및 복합 개체도 포함합니다.

스칼라 식은 결과가 테이블인 [쿼리](app-insights-analytics-queries.md)와 구별됩니다.

## 스칼라

[casts](#casts) | [comparisons](#scalar-comparisons)
<br/>
[gettype](#gettype) | [hash](#hash) | [iff](#iff)| [isnull](#isnull) | [isnotnull](#isnotnull) | [notnull](#notnull)

지원 되는 형식은 다음과 같습니다.

| 형식 | 추가 이름 | 해당하는.NET 형식 |
| --------- | -------------------- | -------------------- |
| `bool`    | `boolean`            | `System.Boolean`     |
| `datetime`| `date`               | `System.DateTime`    |
| `dynamic` |                      | `System.Object`      |
| `guid`    | `uuid`, `uniqueid`   | `System.Guid`        |
| `int`     |                      | `System.Int32`       |
| `long`    |                      | `System.Int64`       |
| `double`  | `real`               | `System.Double`      |
| `string`  |                      | `System.String`      |
| `timespan`| `time`               | `System.TimeSpan`    |

### Casts

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

### 스칼라 비교

||
---|---
`<` |더 작음
`<=`|작거나 같음
`>` |더 큼
`>=`|크거나 같음
`<>`|같지 않음
`!=`|같지 않음 
`in`| 오른쪽 피연산자는 (동적) 배열이며 왼쪽된 피연산자는 해당 배열 요소 중 하나와 같습니다.
`!in`| 오른쪽 피연산자는 (동적) 배열이며 왼쪽된 피연산자는 해당 배열 요소 중 하나와 같지 않습니다.




### gettype

**반환**

단일 인수의 기본 저장소 유형을 나타내는 문자열. 이는 종류 `dynamic`의 값을 가질 때 특히 유용합니다. 이 경우 `gettype()`는 값이 인코딩되는 방법을 표시합니다.

**예**

|||
---|---
`gettype("a")` |`"string" `
`gettype(111)` |`"long" `
`gettype(1==1)` |`"int8" (*) `
`gettype(now())` |`"datetime" `
`gettype(1s)` |`"timespan" `
`gettype(parsejson('1'))` |`"int" `
`gettype(parsejson(' "abc" '))` |`"string" `
`gettype(parsejson(' {"abc":1} '))` |`"dictionary"` 
`gettype(parsejson(' [1, 2, 3] '))` |`"array"` 
`gettype(123.45)` |`"real" `
`gettype(guid(12e8b78d-55b4-46ae-b068-26d7a0080254))` |`"guid"` 
`gettype(parsejson(''))` |`"null"`



### hash

**구문**

    hash(source [, mod])

**인수**

* *source*: 해시를 계산하는 대상인 소스 스칼라입니다.
* *mod*: 해시 결과에 적용할 모듈로 값입니다.

**반환**

지정된 스칼라의 xxhash(long)값, 지정된 mod 값(지정한 경우) 모듈로.

**예**

```
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```
### iff

`iff()` 함수는 첫 번째 인수(조건자)를 계산하고 조건자가 `true`인지 또는 `false`인지에 따라 두 번째 또는 세 번째 인수의 값을 반환합니다. 두 번째 및 세 번째 인수는 동일한 형식이어야 합니다.

**구문**

    iff(predicate, ifTrue, ifFalse)


**인수**

* *조건자:* `boolean` 값으로 계산되는 식입니다.
* *ifTrue:* *조건자*가 `true`으로 계산되는 경우 계산이 이루어지는 식 및 함수에서 반환되는 식의 값입니다.
* *ifFalse:* *조건자*가 `false`으로 계산되는 경우 계산이 이루어지는 식 및 함수에서 반환되는 식의 값입니다.

**반환**

이 함수는 *조건자*가 `true`으로 계산되는 경우 *ifTrue*의 값 또는 그렇지 않은 경우 *ifFalse*의 값을 반환합니다.

**예제**

```
iff(floor(timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```

<a name="isnull"/></a>
<a name="isnotnull"/></a>
<a name="notnull"/></a>
### isnull, isnotnull, notnull

    isnull(parsejson("")) == true

단일 인수를 사용하고 null인지 여부를 알려줍니다.

**구문**


    isnull([value])


    isnotnull([value])


    notnull([value])  // alias for isnotnull

**반환**

값이 null인지 여부에 따라 true 또는 false.


|x|isnull(x)
|---|---
| "" | false
|"x" | false
|parsejson("")|true
|parsejson("[]")|false
|parsejson("{}")|false

**예제**

    T | where isnotnull(PossiblyNull) | count

참고로 이 효과를 달성하는 다른 방법이 있습니다.

    T | summarize count(PossiblyNull)




## Boolean 

### 부울 리터럴

	true == 1
    false == 0
    gettype(true) == "int8"
    typeof(bool) == typeof(int8)

### 부울 연산자

	and 
    or 

    

## 숫자

[bin](#bin) | [floor](#floor) | [rand](#rand) | [range](#range) | [sqrt](#sqrt) 
| [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

### 숫자 리터럴

|||
|---|---
|`42`|`long`
|`42.0`|`real`

### 산술 연산자

|| |
|---|-------------|
| + | 추가 |
| - | 빼기 |
| * | 곱하기 |
| / | 나누기 |
| % | 모듈로 |
||
|`<` |보다 작음
|`<=`|작거나 같음
|`>` |보다 큼
|`>=`|크거나 같음
|`<>`|같지 않음
|`!=`|같지 않음




### bin

값을 지정된 bin 크기의 아래쪽 정수 배로 반올림합니다. [`summarize by`](app-insights-analytics-queries.md#summarize-operator) 쿼리에 많이 사용됩니다. 분산된 값 집합이 있는 경우 특정 값의 더 작은 집합으로 그룹화됩니다.

별칭 `floor`입니다.

**구문**

     bin(value, roundTo)

**인수**

* *value:* 숫자, 날짜 또는 시간 범위입니다. 
* *roundTo:* "bin 크기"입니다. *값*을 나누는 숫자, 날짜 또는 시간 범위입니다. 

**반환**

*값*보다 작은 *roundTo*의 가장 가까운 배수입니다.
 
    (toint((value/roundTo)-0.5)) * roundTo

**예**

식 | 결과
---|---
`bin(4.5, 1)` | `4.0`
`bin(time(16d), 7d)` | `14d`
`bin(datetime(1953-04-15 22:25:07), 1d)`|  `datetime(1953-04-15)`


다음 식은 버킷 크기 1초를 사용하여 기간의 히스토그램을 계산합니다.

```AIQL

    T | summarize Hits=count() by bin(Duration, 1s)
```

### floor

[`bin()`](#bin)에 대한 별칭입니다.


### rand

난수 생성기입니다.

* `rand()` - 0.0에서 1.0 사이의 실수입니다.
* `rand(n)` - 0에서 n-1 사이의 정수입니다.




### sqrt

제곱근 함수입니다.

**구문**

    sqrt(x)

**인수**

* *x:* 0보다 크거나 같은 실수입니다.

**반환**

* `sqrt(x) * sqrt(x) == x`가 되도록 하는 양수입니다.
* 인수가 음수이거나 `real` 값으로 변환할 수 없는 경우 `null`입니다. 




### toint

    toint(100)        // cast from long
    toint(20.7) == 21 // nearest int from double
    toint(20.4) == 20 // nearest int from double
    toint("  123  ")  // parse string
    toint(a[0])       // cast from dynamic
    toint(b.c)        // cast from dynamic

### tolong

    tolong(20.7) == 21 // conversion from double
    tolong(20.4) == 20 // conversion from double
    tolong("  123  ")  // parse string
    tolong(a[0])       // cast from dynamic
    tolong(b.c)        // cast from dynamic


### todouble

    todouble(20) == 20.0 // conversion from long or int
    todouble(" 12.34 ")  // parse string
    todouble(a[0])       // cast from dynamic
    todouble(b.c)        // cast from dynamic



## 날짜 및 시간


[ago](#ago) | [dayofweek](#dayofweek) | [getmonth](#getmonth)|  [getyear](#getyear) | [now](#now) | [startofmonth](#startofmonth) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan)

### 날짜 및 시간 리터럴

|||
---|---
**datetime**|
`datetime("2015-12-31 23:59:59.9")`<br/>`datetime("2015-12-31")`|시간은 항상 UTC 기준입니다. 날짜를 생략하면 오늘 시간을 제공합니다.
`now()`|현재 시간입니다.
`now(`-*timespan*`)`|`now()-`*timespan*
`ago(`*timespan*`)`|`now()-`*timespan*
**TimeSpan**|
`2d`|2일
`1.5h`|1\.5시간 
`30m`|30분
`10s`|10초
`0.1s`|0\.1초
`100ms`| 100밀리초
`10microsecond`|
`1tick`|100나노초
`time("15 seconds")`|
`time("2")`| 2일
`time("0.12:34:56.7")`|`0d+12h+34m+56.7s`

### 날짜 및 시간 식

식 |결과
---|---
`datetime("2015-01-02") - datetime("2015-01-01")`| `1d`
`datetime("2015-01-01") + 1d`| `datetime("2015-01-02")`
`datetime("2015-01-01") - 1d`| `datetime("2014-12-31")`
`2h * 24` | `2d`
`2d` / `2h` | `24`
`datetime("2015-04-15T22:33") % 1d` | `timespan("22:33")`
`bin(datetime("2015-04-15T22:33"), 1d)` | `datetime("2015-04-15T00:00")`
||
`<` |더 작음
`<=`|작거나 같음
`>` |더 큼
`>=`|크거나 같음
`<>`|같지 않음
`!=`|같지 않음 




### ago

현재 UTC 시계 시간에서 지정된 시간 범위를 
뺍니다. `now()`와 마찬가지로, 이 함수를 문에 여러 번 사용할 수 
있으며 참조하는 UTC 시계 시간은 모든 인스턴스에 
대해 동일합니다.

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



### dayofweek

    dayofweek(datetime("2015-12-14")) == 1d  // Monday

`timespan`과 같이 이전 일요일 이후의 일 수(정수)입니다.

**구문**

    dayofweek(a_date)

**인수**

* `a_date`: `datetime`입니다.

**반환**

이전 일요일부터 시작되는 자정 이후의 `timespan`이며, 일 수의 낮은 쪽 정수로 내림됩니다.

**예**

```AIQL
dayofweek(1947-11-29 10:00:05)  // time(6.00:00:00), indicating Saturday
dayofweek(1970-05-11)           // time(1.00:00:00), indicating Monday
```

### getmonth

datetime에서 월 번호(1-12)를 가져옵니다.

**예제**

    ... | extend month = getmonth(datetime(2015-10-12))

    --> month == 10

### getyear

datetime에서 연도를 가져옵니다.

**예제**

    ... | extend year = getyear(datetime(2015-10-12))

    --> year == 2015

### now

    now()
    now(-2d)

현재 UTC 시계 시간, 선택적으로 지정된 시간 범위에 대한 간격 표시. 이 함수를 문에 여러 번 사용할 수 있으며 참조하는 시계 시간은 모든 인스턴스에 대해 동일합니다.

**구문**

    now([offset])

**인수**

* *offset:* 현재 UTC 시계 시간에 더해지는 `timespan`입니다. 기본값: 0.

**반환**

`datetime`로 반환되는 현재 UTC 시계 시간입니다.

    now() + offset

**예제**

조건자에 의해 식별된 이벤트 이후 간격을 결정;

```AIQL
T | where ... | extend Elapsed=now() - timestamp
```

### startofmonth

    startofmonth(date)

날짜를 포함하는 월의 시작입니다.

### startofyear

    startofyear(date)

날짜를 포함하는 연도의 시작입니다.


### todatetime

별칭 `datetime()`입니다.

     todatetime("2016-03-28")
     todatetime("03/28/2016")
     todatetime("2016-03-28 14:34")
     todatetime("03/28/2016 2:34pm")
     todatetime("2016-03-28T14:34.5Z")
     todatetime(a[0])  // cast a dynamic type
     todatetime(b.c)   // cast a dynamic type

### totimespan

별칭 `timespan()`입니다.

    totimespan("21d")
    totimespan("21h")
    totimespan(request.duration)


## String

[countof](#countof) | [extract](#extract) | [extractjson](#extractjson)  | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty) | [replace](#replace) | [split](#split) | [strcat](#strcat) | [strlen](#strlen) | [substring](#substring) | [tolower](#tolower) | [tostring](#tostring) | [toupper](#toupper)


### 문자열 리터럴

규칙은 JavaScript에서와 동일 합니다.

문자열을 작은따옴표 또는 큰따옴표 문자로 묶을 수 있습니다.

백슬래시(`\`)는 `\t`(탭), `\n`(줄 바꿈)과 같은 문자 및 묶는 따옴표 문자의 인스턴스를 이스케이프하는 데 사용됩니다.

* `'this is a "string" literal in single \' quotes'`
* `"this is a 'string' literal in double \" quotes"`
* `@"C:\backslash\not\escaped\with @ prefix"`

### 난독 처리된 문자열 리터럴

난독 처리된 문자열 리터럴은 문자열을 출력할 때(예: 추적하는 경우) 분석이 가리는 문자열입니다. 난독 처리 프로세스는 모든 난독 처리된 문자를 시작(`*`) 문자에 의해 바꿉니다.

난독 처리된 문자열 리터럴을 형성하려면 앞에 `h` 또는 'H'를 추가합니다. 예:

```
h'hello'
h@'world' 
h"hello"
```

### 문자열 비교

연산자|설명|대/소문자 구분|True 예제
---|---|---|---
`==`|같음 |예| `"aBc" == "aBc"`
`<>`|같지 않음|예| `"abc" <> "ABC"`
`=~`|같음 |아니요| `"abc" =~ "ABC"`
`!~`|같지 않음 |아니요| `"aBc" !~ "xyz"`
`has`|오른쪽(RHS)이 왼쪽(LHS)의 전체 항임|아니요| `"North America" has "america"`
`!has`|RHS가 LHS의 전체 항이 아님|아니요|`"North America" !has "amer"` 
`contains` | RHS가 LHS의 하위 시퀀스로 발생|아니요| `"FabriKam" contains "BRik"`
`!contains`| RHS가 LHS에서 발생하지 않음|아니요| `"Fabrikam" !contains "xyz"`
`containscs` | RHS가 LHS의 하위 시퀀스로 발생|예| `"FabriKam" contains "Kam"`
`!containscs`| RHS가 LHS에서 발생하지 않음|예| `"Fabrikam" !contains "Kam"`
`startswith`|RHS가 LHS의 초기 하위 시퀀스임|아니요|`"Fabrikam" startswith "fab"`
`matches regex`|LHS가 RHS에 대한 일치 항목을 포함|예| `"Fabrikam" matches regex "b.*k"`


전체 어휘 항의 존재를 시험하는 경우 `has` 또는 `in`를 사용합니다. - 즉, 기호 또는 영숫자가 아닌 문자 또는 필드 시작 또는 끝에 경계가 지어진 영숫자 워드입니다. `has`은 `contains` 또는 `startswith`보다 더 빠르게 수행합니다. 이러한 쿼리의 첫 번째 쿼리가 더 빠르게 실행됩니다.

    EventLog | where continent has "North" | count;
	EventLog | where continent contains "nor" | count





### countof

    countof("The cat sat on the mat", "at") == 3
    countof("The cat sat on the mat", @"\b.at\b", "regex") == 3

문자열의 부분 문자열 발생을 계산합니다. 일반 문자열 일치는 겹칠 수 있으며, 정규식 일치는 겹칠 수 없습니다.

**구문**

    countof(text, search [, kind])

**인수**

* *text:* 문자열입니다.
* *search:* *text* 내에서 일치하는 일반 문자열 또는 [정규식](app-insights-analytics-reference.md#regular-expressions)입니다.
* *kind:* `"normal"|"regex"`입니다(기본값: `normal`). 

**반환**

검색 문자열이 컨테이너에서 일치시킬 수 있는 횟수입니다. 일반 문자열 일치는 겹칠 수 있으며, 정규식 일치는 겹칠 수 없습니다.

**예**

|||
|---|---
|`countof("aaa", "a")`| 3 
|`countof("aaaa", "aa")`| 3(2 아님!)
|`countof("ababa", "ab", "normal")`| 2
|`countof("ababa", "aba")`| 2
|`countof("ababa", "aba", "regex")`| 1
|`countof("abcabc", "a.c", "regex")`| 2
    



### extract

    extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"

텍스트 문자열에서 [정규식](app-insights-analytics-reference.md#regular-expressions)에 대한 일치 항목을 가져옵니다. 선택적으로 그런 다음 추출된 부분 문자열을 지시된 유형으로 변환합니다.

**구문**

    extract(regex, captureGroup, text [, typeLiteral])

**인수**

* *regex:* [정규식](app-insights-analytics-reference.md#regular-expressions)입니다.
* *captureGroup:* 추출할 캡처 그룹을 나타내는 양의 `int` 상수입니다. 0은 전체 일치, 1은 정규식의 첫 번째 '(' 괄호')'에 의해 일치된 값, 2 이상은 이후 괄호를 나타냅니다.
* *text:* 검색할 `string`입니다.
* *typeLiteral:* 선택적 형식 리터럴(예: `typeof(long)`)입니다. 제공된 경우 추출된 부분 문자열이 이 형식으로 변환됩니다. 

**반환**

*regex*가 *text*에서 일치 항목을 찾은 경우: 지시된 캡처 그룹 *captureGroup*에 대해 일치된 부분 문자열, 선택적으로 *typeLiteral*로 변환됩니다.

일치 항목이 없거나 형식 변환에 실패한 경우 `null`입니다.

**예**

예제 문자열 `Trace`에서 `Duration`에 대한 정의를 검색합니다. 일치 항목은 `real`으로 변환된 다음, 시간 상수(`1s`)를 곱하여 `Duration`가 `timespan` 형식이 됩니다. 이 예제에서 이는 123.45초와 같음:

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
### isempty, isnotempty, notempty

    isempty("") == true

인수가 비어 있거나 null이면 True입니다. [isnull](#isnull)을 참조하세요.


**구문**

    isempty([value])


    isnotempty([value])


    notempty([value]) // alias of isnotempty

**반환**

인수가 빈 문자열인지 아니면 null인지를 나타냅니다.

|x|isempty(x)
|---|---
| "" | true
|"x" | false
|parsejson("")|true
|parsejson("[]")|false
|parsejson("{}")|false


**예제**


    T | where isempty(fieldName) | count




### replace

모든 정규식 일치 항목을 다른 문자열로 바꿉니다.

**구문**

    replace(regex, rewrite, text)

**인수**

* *regex:* *text*를 검색할 [정규식](https://github.com/google/re2/wiki/Syntax)입니다. '('괄호')'에 캡처 그룹을 포함할 수 있습니다. 
* *rewrite:* *matchingRegex*에 의해 수행된 모든 일치에 대한 대체 정규식입니다. 전체 일치를 참조하려면 `\0`, 첫 번째 캡처 그룹을 참조하려면 `\1`, 이후 캡처 그룹을 참조하려면 `\2`를 사용하는 식입니다.
* *text:* 문자열입니다.

**반환**

*regex*의 모든 일치 항목을 *rewrite*로 바꾼 이후의 *text*입니다. 일치 항목은 겹치지 않습니다.

**예제**

이 문은:

```AIQL
range x from 1 to 5 step 1
| extend str=strcat('Number is ', tostring(x))
| extend replaced=replace(@'is (\d+)', @'was: \1', str)
```

다음과 같은 결과를 나타냄:

| x | str | 대체됨|
|---|---|---|
| 1 | 숫자는 1.000000임 | 이전 숫자: 1.000000|
| 2 | 숫자는 2.000000임 | 이전 숫자: 2.000000|
| 3 | 숫자는 3.000000임 | 이전 숫자: 3.000000|
| 4 | 숫자는 4.000000임 | 이전 숫자: 4.000000|
| 5 | 숫자는 5.000000임 | 이전 숫자: 5.000000|
 



### 분할

    split("aaa_bbb_ccc", "_") == ["aaa","bbb","ccc"]

지정된 구분 기호에 따라 지정된 문자열을 분할하고 포함된 부분 문자열과 함께 문자열 배열을 반환합니다. 선택적으로 특정 부분 문자열(있는 경우)을 반환할 수 있습니다.

**구문**

    split(source, delimiter [, requestedIndex])

**인수**

* *source*: 지정된 구분 기호에 따라 분할될 소스 문자열입니다.
* *delimiter*: 소스 문자열을 분할하기 위해 사용될 구분 기호입니다.
* *requestedIndex*: 선택적 0 기반 인덱스`int`입니다. 제공된 경우, 반환되는 문자열 배열은 요청된 부분 문자열(있는 경우)을 포함합니다. 

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




### strcat

    strcat("hello", " ", "world")

인수 1개에서 16개 사이를 연결하며, 인수는 문자열이어야 합니다.

### strlen

    strlen("hello") == 5

문자열의 길이입니다.

### substring

    substring("abcdefg", 1, 2) == "bc"

지정된 인덱스에서 시작하여 지정된 소스 문자열에서 부분 문자열을 추출합니다. 선택적으로 요청된 부분 문자열의 길이를 지정할 수 있습니다.

**구문**

    substring(source, startingIndex [, length])

**인수**

* *source:* 부분 문자열을 가져올 소스 문자열입니다.
* *startingIndex:* 요청된 부분 문자열의 0 기반 시작 문자 위치입니다.
* *length:* 부분 문자열에서 요청된 문자 수를 지정하는 데 사용할 수 있는 선택적 매개 변수입니다. 

**반환**

지정된 문자열에서 나온 부분 문자열입니다. 부분 문자열은 startingIndex(0 기반) 문자 위치에서 시작하며 문자열의 끝 또는 length 문자(지정된 경우)까지 계속합니다.

**예**

```
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
```

### tolower

    tolower("HELLO") == "hello"

문자열을 소문자로 변환합니다.

### toupper

    toupper("hello") == "HELLO"

문자열을 대문자로 변환합니다.



## GUIDs

    guid(00000000-1111-2222-3333-055567f333de)


## 배열 및 개체 - 동적 형식

[literals](#dynamic-literals) | [casting](#casting-dynamic-objects) | [operators](#operators) | [let clauses](#dynamic-objects-in-let-clauses)
<br/>
[arraylength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [range](#range) | [treepath](#treepath) | [todynamic](#todynamic)


다음은 Application Insights 예외에 대한 쿼리의 결과입니다. `details`의 값은 배열입니다.

![](./media/app-insights-analytics-scalars/310.png)

**Indexing:** JavaScript에서와 같은 인덱스 배열 및 개체입니다.

    exceptions | take 1
    | extend 
        line = details[0].parsedStack[0].line,
        stackdepth = arraylength(details[0].parsedStack)

* 하지만 `arraylength` 및 다른 분석 함수(".length" 아님!)를 사용합니다.

**Casting** 경우에 따라 형식이 달라질 수 있기 때문에 개체에서 추출하는 요소를 캐스트해야 합니다. 예를 들어 `summarize...to`에는 특정 형식이 필요합니다.

    exceptions 
    | summarize count() 
      by toint(details[0].parsedStack[0].line)

    exceptions 
    | summarize count() 
      by tostring(details[0].parsedStack[0].assembly)

**Literals** 명시적 배열 또는 속성 모음 개체를 만들려면 JSON 문자열로 쓰고 캐스트합니다.

    todynamic('[{"x":"1", "y":"32"}, {"x":"6", "y":"44"}]')


**mvexpand:** 개체의 속성을 별도의 행으로 끌어오려면 mvexpand를 사용합니다.

    exceptions | take 1 
    | mvexpand details[0].parsedStack[0]


![](./media/app-insights-analytics-scalars/410.png)


**treepath:** 복잡한 개체에서 모든 경로를 찾습니다.

    exceptions | take 1 | project timestamp, details 
    | extend path = treepath(details) 
    | mvexpand path


![](./media/app-insights-analytics-scalars/420.png)

**buildschema:** 테이블에 있는 식의 모든 값을 인정하는 최소 스키마를 찾습니다.

    exceptions | summarize buildschema(details)

결과:

    { "`indexer`":
     {"id":"string",
       "parsedStack":
       { "`indexer`": 
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

참고로 `indexer`은 숫자 인덱스를 사용해야 하는 위치를 표시하기 위해 사용됩니다. 이 스키마의 경우 일부 유효한 경로는 다음과 같을 수 있습니다(이 예제 인덱스가 범위 안에 든다고 가정).

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)



### 배열 및 개체 리터럴

동적 리터럴을 만들려면 JSON 문자 인수와 함께 `parsejson`(별칭 `todynamic`)을 사용합니다.

* `parsejson('[43, 21, 65]')` - 숫자의 배열
* `parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')` 
* `parsejson('21')` - 숫자를 포함하는 동적 형식의 단일 값
* `parsejson('"21"')` - 문자열을 포함하는 동적 형식의 단일 값

참고로 JavaScript와 달리 JSON은 문자열 주위에 큰따옴표(`"`)를 의무적으로 사용해야 합니다. 그러므로 일반적으로 작은따옴표(`'`)를 사용하여 JSON 인코딩 문자열 리터럴을 인용하는 것이 더 쉽습니다.

이 예제에서는 동적 값을 만든 다음 해당 필드를 사용함:

```

T
| extend person = parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')
| extend n = person.name, add = person.address.street
```


<a name="operators"></a>
### 동적 형식에 대한 연산자 및 함수

|||
|---|---|
| *value* `in` *array*| *value*<br/>`where City in ('London', 'Paris', 'Rome')`인 *array*의 요소가 있으면 True
| *value* `!in` *array*| *value*인 *array*의 요소가 없으면 True
|[`arraylength(`array`)`](#arraylength)| 배열이 아니면 Null
|[`extractjson(`path,object`)`](#extractjson)|path를 사용하여 object를 탐색합니다.
|[`parsejson(`source`)`](#parsejson)| JSON 개체를 동적 개체로 변환합니다.
|[`range(`from,to,step`)`](#range)| 값의 배열
|[`mvexpand` listColumn](app-insights-analytics-queries.md#mvexpand-operator) | 각 값에 대한 행을 지정된 셀의 목록에 복제합니다.
|[`summarize buildschema(`column`)`](app-insights-analytics-queries.md#summarize-operator) |열 내용에서 형식 스키마를 유추
|[`summarize makelist(`column`)` ](app-insights-analytics-queries.md#summarize-operator)| 행 그룹을 평면화하고 열의 값을 배열에 넣습니다.
|[`summarize makeset(`column`)`](app-insights-analytics-queries.md#summarize-operator) | 행 그룹을 평면화하고 열의 값을 중복 없이 배열에 넣습니다.

### let 절의 동적 개체


[let 절](app-insights-analytics-queries.md#let-clause)은 동적 값을 문자열로 저장하므로 이 두 절은 동일하며 둘 다 사용하기 전에 `parsejson`(또는 `todynamic`)가 필요합니다.

    let list1 = '{"a" : "somevalue"}';
    let list2 = parsejson('{"a" : "somevalue"}');

    T | project parsejson(list1).a, parsejson(list2).a




### arraylength

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



### extractjson

    extractjson("$.hosts[1].AvailableMB", EventText, typeof(int))

path 식을 사용하여 JSON 텍스트에서 지정된 요소를 가져옵니다. 선택적으로 추출한 문자열을 특정 형식으로 변환합니다.


**구문**

```

    string extractjson(jsonPath, dataSource)​​ 
    resulttype extractjson(jsonPath, dataSource, typeof(resulttype))​​
```


**반환**

이 함수는 유효한 JSON 문자열을 포함하고 있는 dataSource에 대해 JsonPath 쿼리를 수행하며, 선택적으로 이 값을 세 번째 인수에 따라 다른 형식으로 변환합니다.



**예제**

[대괄호] 표기와 점 표기는 동일합니다.

    ... | extend AvailableMB = extractjson("$.hosts[1].AvailableMB", EventText, typeof(int)) | ...

    ... | extend AvailableMD = extractjson("$['hosts'][1]['AvailableMB']", EventText, typeof(int)) | ...

#### JSON Path 식

|||
|---|---|
|`$`|루트 개체|
|`@`|현재 개체|
|`.` 또는 `[ ]` | 자식|
|`[ ]`|배열 첨자|

*(현재 와일드카드, 재귀, 합집합 또는 조각이 구현되지 않았습니다.)*


**성능 팁**

* `extractjson()`을 사용하기 전에 where 절을 적용합니다.
* 정규식 일치 사용은 [extract](#extract)를 대신 사용하는 것으로 간주합니다. 이렇게 하면 훨씬 더 빠르게 실행될 수 있으며 JSON이 템플릿에서 생성된 경우 효과적입니다.
* JSON에서 둘 이상의 값을 추출해야 하는 경우 `parsejson()`을 사용합니다.
* 열 형식을 동적으로 선언하여 수집 시 JSON이 구문 분석되게 하는 것으로 간주합니다.



### parsejson

`string`을 [JSON 값](http://json.org/)으로 해석하고 값을 `dynamic`으로 반환합니다. JSON 복합 개체의 요소를 두 개 이상 추출해야 하는 경우 `extractjson()`를 사용하는 것이 좋습니다.

**구문**

    parsejson(json)

**인수**

* *json:* JSON 문서입니다.

**반환**

*json*에 의해 지정된 형식 `dynamic`의 개체입니다.

**예제**

다음 예제에서 `context_custom_metrics`이 `string`인 경우 다음과 유사합니다.

```
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

그러면 다음 조각은 개체에 있는 `duration` 슬롯의 값을 검색하며 거기서부터 `duration.value` 및 `duration.min`의 두 슬롯을 검색합니다(각각 `118.0` 및 `110.0`).

```AIQL
T
| ...
| extend d=parsejson(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```



### range

`range()` 함수(`range` 연산자와 혼동하지 말 것)는 간격이 같은 일련의 값을 저장하는 동적 배열을 생성합니다.

**구문**

    range(start, stop, step)

**인수**

* *start:* 결과 배열의 첫 번째 요소의 값입니다. 
* *stop:* 결과 배열의 마지막 요소의 값 또는 결과 배열 및 *start*부터 *step*의 정수 배 내에서 마지막 요소보다 더 큰 가장 작은 값입니다.
* *step:* 배열의 연속된 두 요소 사이의 차입니다.

**예**

다음 예제에서는 `[1, 4, 7]`을 반환합니다.

```AIQL
range(1, 8, 3)
```

다음 예제는 2015 년의 모든 날짜를 저장하는 배열을 반환합니다.

```AIQL

    range(datetime(2015-01-01), datetime(2015-12-31), 1d)
```

### todynamic

    todynamic('{"a":"a1", "b":["b1", "b2"]}')

문자열을 동적 값으로 변환합니다.

### treepath

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


## 샘플링

### itemCount

[샘플링](app-insights-sampling.md)이 작동 중인 경우 SDK에서 생성된 데이터의 일부만 Application Insights 포털에 수집됩니다. SDK 또는 포털 끝점에 대한 수집에서 샘플링을 적용할 수 있습니다.

`itemCount`는 스트림에 나타나는 각 이벤트에 대해 SDK에서 생성된 이벤트 수를 나타냅니다. 예를 들어 경우 현재 샘플링 속도가 25%인 경우 itemCount = 4입니다.

이벤트 수를 계산하려면 `sum(itemCount)` 대신 `count()`을 사용하여 실제 숫자의 정확한 추정치를 가져옵니다. 예:

* 지난 2시간 이내에 발생한 예외 수:

    ![](./media/app-insights-analytics-scalars/510.png)




[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!---HONumber=AcomDC_0330_2016-->