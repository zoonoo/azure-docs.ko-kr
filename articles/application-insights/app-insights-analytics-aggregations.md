<properties 
	pageTitle="Application Insights 분석의 요약 및 집계" 
	description="Application Insights의 강력한 검색 도구인 분석의 집계 함수 및 summarize 문에 대한 참조입니다." 
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

# 분석의 집계

[Analytics](app-insights-analytics.md)는 [Application Insights](app-insights-overview.md)의 강력한 검색 기능입니다. 다음 페이지에서는 분석 쿼리 언어에 대해 설명합니다.

[AZURE.INCLUDE [app-insights-analytics-top-index](../../includes/app-insights-analytics-top-index.md)]


## summarize 연산자

입력된 테이블의 내용을 집계하는 테이블을 생성합니다.

    purchases
    | summarize avg(Price) 
      by Fruit, Supplier

![](./media/app-insights-analytics-aggregations/01.png)

* 입력된 레코드는 각 그룹이 `by` 필드에 특정 조합의 값을 갖도록 그룹으로 수집됩니다.
* 각 그룹의 멤버에 대해 집계 식이 계산됩니다.
* ‘by’ 식의 값의 각 고유 조합에 대해 출력 행이 있습니다. 
* 각 집계 식 및 각 ‘by’ 식에 대 한 출력 열이 있습니다. 모든 다른 입력된 열은 삭제됩니다.

### 구문

    T | summarize
         [  [ Column = ] Aggregation [ , ... ]]
         [ by
            [ Column = ] GroupExpression [ , ... ]]

**인수**

* *Column:* 결과 열에 대한 선택적 이름입니다. 기본적으로 식에서 파생된 이름입니다.
* *Aggregation:* 열 이름을 인수로 하는 `count()` 또는 `avg()` 등과 같은 집계 함수에 대한 호출입니다. 아래 집계 함수의 목록을 참조하세요.
* *GroupExpression:* 고유 값 집합을 제공하는 열에 대한 식입니다. 일반적으로 제한된 값 집합을 제공하는 열 이름, 또는 숫자 또는 시간 열을 인수로 하는 `bin()`입니다. 

`bin()`을 사용하지 않고 숫자 또는 시간 식을 제공할 경우 분석은 시간에 대해 `1h`의 간격 또는 숫자에 대해 `1.0`과 함께 자동으로 이를 적용합니다.

*GroupExpression*을 제공하지 않으면 전체 테이블이 단일 출력 행에 요약됩니다.

`by` 절에 동적 유형이 아닌 단순 유형을 사용해야 합니다. 예를 들어 여기서 `tostring` 캐스트는 필수입니다.

    exceptions
	| summarize count()
      by tostring(customDimensions.ClientRequestId)

### 불연속 값이 있는 열에 의한 요약

서로 다른 응답 코드를 가진 요청을 분리하는 서로 다른 HTTP 요청에 대한 평균 응답 시간을 나타내는 쿼리:

    requests 
    | summarize count(), avg(duration) 
      by operation_Name, resultCode

![result](./media/app-insights-analytics-aggregations/03.png)


* 이 문서에서 설명하는 집계 함수에서 집계 식(count 및 avg 등)을 형성해야 합니다. 스칼라 함수가 이 식의 인수가 될 수 있습니다.
* 그룹화 식(‘by’ 뒤)은 어떤 스칼라 식이나 가능하지만 단지 필드 이름일 경우에 더 잘 수행됩니다.

### 숫자 열에 의한 요약

숫자 또는 시간 등 연속적인 스칼라에 의해 그룹화하려는 경우 `bin`(일명 `floor`) 함수를 사용하여 연속된 범위를 bin으로 구분할 수 있습니다.

    requests
    | summarize count() 
      by bin(duration, 1000)/1000

![result](./media/app-insights-analytics-aggregations/04.png)

(요청 기간 필드는 밀리초 단위의 숫자입니다.)
 
## 팁

* `where`을 사용하여 `summarize` 앞의 원하지 않는 모든 행을 제거합니다.
 * Null 값을 제거합니다. 그룹의 null 값 한 개가 집계 null의 결과를 만듭니다. 

```

        requests 
        | where isnotnull(duration) 
        | summarize count(), avg(duration)
          by operation_Name
```

* 집계와 그룹화 식에 대해 모두 임의 식을 제공할 수 있지만 단순 필드 이름을 사용하거나 `bin()`을 숫자 필드에 적용하는 것이 더 효율적입니다.





## 예

#### 요청 수 계산

지난 1시간 동안 각 이름의 요청 수를 찾습니다.

    requests | where timestamp > ago(1h)
    | summarize req_count = sum(itemCount) by name

> [AZURE.NOTE] 여기서는 단순히 요청 데이터 요소의 수만 계산하는 대신 itemCount 속성을 사용합니다. 이렇게 하면 해당 시점에 작동 중일 수 있는 [샘플링](app-insights-sampling.md)을 보정할 수 있습니다. 예를 들어 샘플링을 33%로 설정하면 각 데이터 요소의 itemCount는 3이 됩니다.

#### Min 및 Max 사용

요청 스트림에서 모든 레코드의 최소 및 최대 타임스탬프를 찾습니다. 출력에 행이 한 개만 있으므로 group-by 절은 없습니다.

```

requests | summarize Min = min(timestamp), Max = max(timestamp)
```

|`Min`|`Max`
|---|---
|`2015-12-09 09:21:45` | `2015-12-24 23:45:00`



#### 세션 기간

이벤트 로그의 세션에 여러 이벤트가 있습니다. 각 세션의 가장 이른 최근 이벤트를 찾아서 각 세션의 시작 및 종료를 찾습니다.

```

    requests 
    | where isnotempty(session_Id)
    | summarize start=min(timestamp), stop=max(timestamp) by session_Id 
    | extend duration = bin(stop - start, 1s)
```

`extend` 연산은 duration 열을 추가하며 흔히 `floor`로 지칭되는 `bin`을 사용하여 이 열의 값을 가장 가까운 초로 반올림합니다.

![](./media/app-insights-analytics-aggregations/minmax.png)

#### 예: 평균 기간

이제 다양한 도시의 클라이언트에 대한 평균 세션 기간을 찾아 봅니다.

```

    requests
    | where isnotempty(session_Id)
    | summarize start=min(timestamp), stop=max(timestamp) 
      by session_Id, client_City 
    | extend duration = stop - start
    | summarize duration_by_city=bin(avg(duration),1s) by client_City
    | top 50 by duration_by_city
```

첫 번째 summarize 연산을 통과하도록 `client_City` 열을 `by` 절에 추가했습니다. 한 클라이언트 세션의 모든 이벤트가 같은 도시에서 일어난다고 가정하면 해당 세션은 요약의 출력 횟수에 추가되지 않습니다.


![](./media/app-insights-analytics-aggregations/durationcity.png)


#### 예제: 

각 클라이언트 도시에서 하루 중 가장 바쁜 시간을 찾습니다. 여기서는 ‘가장 바쁜’이란 평균적인 날에 최대 세션 수가 시작되는 시각을 의미합니다.

```
requests  
| summarize start=min(timestamp) by session_Id, city=client_City 
| extend timeofday=start % 1d 
| summarize popularity=dcount(session_Id) by bin(timeofday, 1h), city 
| summarize argmax(popularity, *) by city  
| sort by max_pop_tod asc
```

## AGGREGATIONS

## 모든 

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

<a name="argmin"></a> <a name="argmax"></a>
## argmin, argmax

    argmin(ExprToMinimize, * | ExprToReturn  [ , ... ] )
    argmax(ExprToMaximize, * | ExprToReturn  [ , ... ] ) 

*ExprToMaximize*를 최소화/최대화하는 그룹의 행을 찾고 *ExprToReturn*(전체 행을 반환하려는 경우에는 `*`)의 값을 반환합니다.

**팁**: 통과된 열의 이름은 자동으로 바뀝니다. 올바른 이름을 사용하고 있는지 확인하려면 결과를 다른 연산자에 파이프하기 전에 `take 5`를 사용하여 결과를 검사합니다.

**예**

각 요청 이름에 대해 가장 긴 요청이 발생한 시기를 표시합니다.

    requests | summarize argmax(duration, timestamp) by name

타임스탬프뿐만 아니라 가장 긴 요청의 모든 세부 정보를 표시합니다.

    requests | summarize argmax(duration, *) by name


각 메트릭의 가장 낮은 값을 해당 타임스탬프 및 다른 데이터와 함께 찾습니다.

    metrics 
    | summarize minValue=argmin(value, *) 
      by name


![](./media/app-insights-analytics-aggregations/argmin.png)
 


## avg

    avg(Expression)

그룹 전체에서 *Expression*의 평균을 계산합니다.

## buildschema

    buildschema(DynamicExpression)

*DynamicExpression*의 모든 값을 허용하는 최소 스키마를 반환합니다.

매개 변수 열 형식은 `dynamic`(배열 또는 속성 모음)이어야 합니다.

**예제**

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

`indexer`는 숫자 인덱스를 사용해야 하는 위치를 표시하는 데 사용됩니다. 이 스키마의 경우 일부 유효한 경로는 다음과 같을 수 있습니다(이 예제 인덱스가 범위 안에 든다고 가정).

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)

**예제**

입력된 열이 동적 값 세 개를 가진다고 가정합니다.

| |
|---|
|`{"x":1, "y":3.5}`
|`{"x":"somevalue", "z":[1, 2, 3]}`
|`{"y":{"w":"zzz"}, "t":["aa", "bb"], "z":["foo"]}`


결과 스키마는 다음과 같습니다.

    { 
      "x":["int", "string"], 
      "y":["double", {"w": "string"}], 
      "z":{"`indexer`": ["int", "string"]}, 
      "t":{"`indexer`": "string"} 
    }

스키마는 다음 정보를 알려줍니다.

* 루트 개체는 x, y, z 및 t라는 네 속성을 가진 컨테이너입니다.
* 형식 “Int” 또는 형식 “string” "x"라는 속성.
* 형식이 “double”인 “y”라는 속성 또는 형식 “string”의 “w”라는 속성을 가진 다른 컨테이너.
* ``indexer`` 키워드는 "z"와 "t"가 배열임을 나타냅니다.
* "z" 배열의 각 항목은 int 또는 문자열입니다.
* "t"는 문자열의 배열입니다.
* 모든 속성은 암시적으로 선택적이며 배열은 비어 있을 수 있습니다.

#### 스키마 모델

반환되는 스키마의 구문은 다음과 같습니다.

    Container ::= '{' Named-type* '}';
    Named-type ::= (name | '"`indexer`"') ':' Type;
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


## count

    count([ Predicate ])

*Predicate*가 `true`로 계산되는 행 수를 반환합니다. *Predicate*를 지정하지 않으면 그룹의 총 레코드 수를 반환합니다.

**성능 팁**: `where filter | summarize count()` 대신 `summarize count(filter)` 사용

> [AZURE.NOTE] 발생한 요청, 예외 또는 기타 이벤트 수를 찾는 데는 count()를 사용하지 마세요. [샘플링](app-insights-sampling.md)이 작동 중이면 데이터 요소 수가 실제 이벤트 수보다 적어집니다. 대신 `summarize sum(itemCount)...`를 사용하세요. itemCount 속성은 보존된 각 데이터 요소로 표시되는 원래 이벤트 수를 반영합니다.
   

## dcount

    dcount( Expression [ ,  Accuracy ])

그룹에 있는 *Expr*의 고유 값 수에 대한 추정치를 반환합니다. 고유 값을 나열하려면 [`makeset`](#makeset)를 사용합니다.

*Accuracy*는 지정하는 경우 속도와 정확도 간의 균형을 제어합니다.

 * `0` = 정확성은 가장 떨어지지만 계산 속도는 가장 빠릅니다.
 * `1`(기본값)이며, 정확도와 계산 시간의 균형을 적절하게 유지합니다. 오류 비율은 약 0.8%입니다.
 * `2` = 가장 정확하지만 계산 속도는 가장 느리며 오류 비율은 약 0.4%입니다.

**예제**

    pageViews 
    | summarize countries=dcount(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-aggregations/dcount.png)

## makelist

    makelist(Expr [ ,  MaxListSize ] )

그룹 내 모든 *Expr* 값의 `dynamic`(JSON) 배열을 반환합니다.

* *MaxListSize*는 반환되는 최대 요소 수에 대한 선택적 정수 한계(기본값 *128*)입니다.

## makeset

    makeset(Expression [ , MaxSetSize ] )

*Expr*이 그룹에서 사용하는 고유 값 집합의 `dynamic`(JSON) 배열을 반환합니다. 팁: 고유 값 수만 계산하려면 [`dcount`](#dcount)를 사용합니다.)
  
*  *MaxSetSize*는 반환되는 최대 요소 수에 대한 선택적 정수 한계(기본값 *128*)입니다.

**예제**

    pageViews 
    | summarize countries=makeset(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-aggregations/makeset.png)

[`mvexpand` 연산자](app-insights-analytics-queries.md#mvexpand-operator)에서 반대 함수도 참조하세요.


## max, min

    max(Expr)

*Expr*의 최대값을 계산합니다.
    
    min(Expr)

*Expr*의 최소값을 계산합니다.

**팁**: 이러한 함수를 단독으로 사용하는 경우 최고가 또는 최저가 등 원하는 최소값이나 최대값을 구할 수 있습니다. 행의 다른 열(예: 최저가를 제공하는 공급업체의 이름)을 반환하려는 경우에는 [argmin 또는 argmax](#argmin-argmax)를 사용합니다.


<a name="percentile"></a> <a name="percentiles"></a>
## percentile, percentiles

    percentile(Expression, Percentile)

*Expression*에 대한 추정치를 그룹에 지정된 백분위수로 반환합니다. 정확도는 백분위수 지역의 인구 밀도에 따라 달라집니다.
    
    percentiles(Expression, Percentile1 [ , Percentile2 ] )

위의 함수도 `percentile()`과 유사하지만 백분위수 값의 수를 계산하며, 각 백분위수를 개별적으로 계산하는 것보다 속도가 더 빠릅니다.

**예**


샘플 집합의 95%보다는 크고 5%보다는 작은 `duration`의 값을 각 요청 이름에 대해 계산하는 함수는 다음과 같습니다.

    request 
    | summarize percentile(duration, 95)
      by name

전체 테이블에 대해 계산하려면 “by...”를 생략합니다.

서로 다른 요청 이름에 대해 여러 백분위수를 동시에 계산합니다.

    
    requests 
    | summarize 
        percentiles(duration, 5, 20, 50, 80, 95) 
      by name

![](./media/app-insights-analytics-aggregations/percentiles.png)

이 결과는 요청/이벤트/인덱스에 대해 요청의 5%가 2.44s 이내에 응답하고 그 중 절반이 3.52s 이내에 응답하고 5%가 6.85s보다 더 느리다는 것을 보여줍니다.


여러 통계를 계산합니다.

    requests 
    | summarize 
        count(), 
        avg(Duration),
        percentiles(Duration, 5, 50, 95)
      by name

#### 백분위수 추정 오류

백분위수 집계는 [T-Digest](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf)를 사용하여 근사값을 제공합니다.

몇 가지 중요 사항:

* 추정 오류의 범위는 요청한 백분위수의 값에 따라 달라집니다. 최고의 정확도는 [0 ~ 100] 눈금의 끝에서 얻어지며, 백분위수 0 및 100은 분포의 최소값 및 최대값과 똑같습니다. 정확도는 눈금의 중앙으로 갈수록 서서히 감소합니다. 중앙값에서 최악이 되고 1%에서 최고가 됩니다. 
* 오류 범위는 값이 아닌 순위에서 관찰됩니다.. 백분위수(X, 50)가 Xm의 값을 반환한다고 가정합니다. 추정치는 X의 값 중 최소 49%와 최대 51%는 Xm보다 작다는 것을 보장합니다. Xm과 X의 실제 중간값 사이에 이론적 제한은 없습니다.

## stdev

     stdev(Expr)

그룹에 대해 *Expr*의 표준 편차를 반환합니다.

## variance

    variance(Expr)

그룹에 대해 *Expr*의 분산을 반환합니다.

## sum

    sum(Expr)

그룹에 대해 *Expr*의 합계를 반환합니다.




[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!---HONumber=AcomDC_0330_2016-->