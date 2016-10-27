<properties 
    pageTitle="Application Insights 내 Analytics 둘러보기 | Microsoft Azure" 
    description="Application Insights의 강력한 검색 도구인 Analytics의 모든 주요 쿼리에 대한 간단한 샘플" 
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
    ms.date="10/03/2016" 
    ms.author="awills"/>


 

# <a name="a-tour-of-analytics-in-application-insights"></a>Application Insights의 Analytics 둘러보기


[분석](app-insights-analytics.md)은 [Application Insights](app-insights-overview.md)의 강력한 검색 기능입니다. 다음 페이지에서는 분석 쿼리 언어에 대해 설명합니다.


* **[소개 비디오 보기](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**
* 앱이 아직 데이터를 Application Insights로 전송하지 않은 경우, **[시뮬레이션된 데이터에 대한 분석을 테스트](https://analytics.applicationinsights.io/demo)**합니다.


시작하기 위해 몇 가지 기본적인 쿼리를 연습해 보겠습니다.

## <a name="connect-to-your-application-insights-data"></a>Application Insights 데이터에 연결

Application Insights의 앱 [개요 블레이드](app-insights-dashboards.md) 에서 분석을 엽니다.

![portal.azure.com을 열고 Application Insights 리소스를 열고 Analytics를 클릭합니다.](./media/app-insights-analytics-tour/001.png)

    
## <a name="[take](app-insights-analytics-reference.md#take-operator):-show-me-n-rows"></a>[Take](app-insights-analytics-reference.md#take-operator): n개의 행 표시

사용자 작업(일반적으로 웹앱에서 받는 HTTP 요청)을 기록하는 데이터 요소는 `requests`라는 테이블에 저장됩니다. 각 행은 앱의 Application Insights SDK에서 수신된 원격 분석 데이터 요소입니다.

테이블의 몇 가지 샘플 행을 검사하는 것으로 시작해 보겠습니다.

![결과](./media/app-insights-analytics-tour/010.png)

> [AZURE.NOTE] 이동을 클릭하기 전에 커서를 문의 어딘가에 둡니다. 문을 둘 이상의 행으로 분할할 수 있지만 문에 빈 줄을 넣으면 안 됩니다. 빈 줄은 창에서 여러 개의 별도 쿼리를 유지하는 편리한 방법입니다.


열을 선택하고 해당 위치를 조정합니다.

![결과의 오른쪽 위에 있는 열 선택을 클릭](./media/app-insights-analytics-tour/030.png)


세부 정보를 보려면 모든 항목을 확장합니다.
 
![테이블을 선택하고 열을 구성](./media/app-insights-analytics-tour/040.png)

> [AZURE.NOTE] 웹 브라우저에서 사용할 수 있는 결과의 순서를 변경하려면 열 머리글을 클릭합니다. 단, 큰 결과 집합의 경우에는 브라우저에 다운로드되는 행의 수가 제한된다는 점을 고려해야 합니다. 또한 이 방식으로 정렬한다고 해서 항상 최상위 항목이나 최하위 항목이 표시되지는 않습니다. 최상위 항목이나 최하위 항목을 표시하려면 `top` 또는 `sort` 연산자를 사용해야 합니다. 

## <a name="[top](app-insights-analytics-reference.md#top-operator)-and-[sort](app-insights-analytics-reference.md#sort-operator)"></a>[Top](app-insights-analytics-reference.md#top-operator) 및 [sort](app-insights-analytics-reference.md#sort-operator)

`take` 은(는) 빨리 확인할 결과 샘플을 가져오는 데 유용하지만 테이블의 행을 특정 순서 없이 표시합니다. 순서가 지정된 보기를 가져오려면 `top`(샘플의 경우) 또는 `sort`(전체 테이블에 대해)을(를) 사용합니다.

특정 열을 기준으로 순서가 정해진 처음 n 행 표시:

```AIQL

    requests | top 10 by timestamp desc 
```

* *구문:* 대부분의 연산자에는 `by`와(과) 같은 키워드 매개 변수가 있습니다.
* `desc`은(는) 내림차순을 의미하고 `asc`은(는) 오름차순을 의미합니다.

![](./media/app-insights-analytics-tour/260.png)

`top...`을(를) 사용하면 `sort ... | take...`을(를) 보다 효율적으로 설명할 수 있습니다. 다음과 같이 작성했을 수도 있음:

```AIQL

    requests | sort by timestamp desc | take 10
```

결과는 같지만 조금 더 느리게 실행될 수 있습니다. `sort`의 별칭인 `order`을(를) 작성할 수도 있습니다.

테이블 보기의 열 머리글을 사용하여 화면에서 결과를 정렬할 수도 있습니다. 하지만 물론 `take` 또는 `top`을(를) 사용하여 테이블의 일부만 검색했다면 검색한 레코드의 순서만 변경될 것입니다.


## <a name="[project](app-insights-analytics-reference.md#project-operator):-select,-rename-and-compute-columns"></a>[Project](app-insights-analytics-reference.md#project-operator): 열 선택, 이름 바꾸기 및 계산

원하는 열만 선택하려면 [`project`](app-insights-analytics-reference.md#project-operator) 을(를) 사용합니다.

```AIQL

    requests | top 10 by timestamp desc
             | project timestamp, name, resultCode
```

![](./media/app-insights-analytics-tour/240.png)


열 이름을 바꾸고 새 열을 정의할 수도 있음:

```AIQL

    requests 
  	| top 10 by timestamp desc 
  	| project  
            name, 
            response = resultCode,
            timestamp, 
            ['time of day'] = floor(timestamp % 1d, 1s)
```

![result](./media/app-insights-analytics-tour/270.png)

* [열 이름](app-insights-analytics-reference.md#names)은 `['...']` 또는 `["..."]`와(과) 같이 대괄호로 묶은 경우 공백이나 기호를 포함할 수 있습니다.
* `%` 은(는) 일반적인 모듈로 연산자입니다. 
* `1d` (한 자릿수, 'd' 한 개)은(는) 하루를 의미하는 시간 간격 리터럴입니다. 기타 시간 간격 리터럴에는 `12h`, `30m`, `10s`, `0.01s` 등이 있습니다.
* `floor`(별칭 `bin`)은(는) 값을 사용자가 입력하는 기준 값에 가장 가까운 낮은 배수로 반올림합니다. 따라서 `floor(aTime, 1s)` 은(는) 시간을 가장 가까운 초로 반올림합니다.

[식](app-insights-analytics-reference.md#scalars)은 모든 일반적인 연산자(`+`, `-` 등)를 포함할 수 있으며, 유용한 함수가 다양하게 사용됩니다.

    

## <a name="[extend](app-insights-analytics-reference.md#extend-operator):-compute-columns"></a>[확장](app-insights-analytics-reference.md#extend-operator): 열 계산

기존 열에 열을 추가하기만 하려면 [`extend`](app-insights-analytics-reference.md#extend-operator)을(를) 사용합니다.

```AIQL

    requests 
  	| top 10 by timestamp desc
  	| extend timeOfDay = floor(timestamp % 1d, 1s)
```

기존 열을 모두 유지하려는 경우 [`extend`](app-insights-analytics-reference.md#extend-operator)을(를) 사용하면 [`project`](app-insights-analytics-reference.md#project-operator)에 비해 표시되는 정보가 상세하지 않습니다.


## <a name="[summarize](app-insights-analytics-reference.md#summarize-operator):-aggregate-groups-of-rows"></a>[요약](app-insights-analytics-reference.md#summarize-operator): 행 그룹 집계

`Summarize` 은(는) 행 그룹에서 지정된 *집계 함수* 를 적용합니다. 

예를 들어 웹앱이 요청에 응답하는 데 걸리는 시간은 `duration`필드에 보고됩니다. 모든 요청에 대한 평균 응답 시간을 살펴보겠습니다.

![](./media/app-insights-analytics-tour/410.png)

또는 결과를 다양한 이름의 요청으로 분리할 수 있습니다.


![](./media/app-insights-analytics-tour/420.png)

`Summarize`은(는) 스트림의 데이터 요소를 `by` 절에서 동일하게 평가하는 그룹으로 수집합니다. `by` 식의 각 값(예: 위 예제의 경우 각 작업 이름)은 결과 테이블의 행이 됩니다. 

또는 하루 중 시간으로 결과를 그룹화할 수 있습니다.

![](./media/app-insights-analytics-tour/430.png)

`bin` 함수(즉, `floor`)를 사용하는 방법을 알아보겠습니다. `by timestamp`을(를) 사용하면 모든 입력 행이 고유한 작은 그룹이 됩니다. 시간 또는 숫자와 같은 모든 연속적인 스칼라의 경우 연속되는 범위를 관리 가능한 개수의 불연속 값으로 나누어야 하며, 그러려면 `floor`, 즉 실제로 친숙한 내림 함수인 `bin` 함수를 사용하는 것이 가장 간편합니다.

동일한 기술을 사용하여 문자열의 범위를 줄일 수 있습니다.


![](./media/app-insights-analytics-tour/440.png)

`name=` 을(를) 사용하여 집계 식 또는 by 절에서 결과 열의 이름을 설정할 수 있습니다.

## <a name="counting-sampled-data"></a>샘플링된 데이터 수 계산

`sum(itemCount)` 이벤트 수에 대한 권장 집계입니다. 대부분의 경우 itemCount=1이므로 함수는 단순히 그룹의 행 수를 계산합니다. 하지만 [샘플링](app-insights-sampling.md)이 작동 중인 경우에는 Application Insights에서 원래 이벤트의 일부만 데이터 요소로 유지되므로 각 데이터 요소에 `itemCount`개의 이벤트가 있습니다. 

예를 들어 샘플링에서 원래 이벤트의 75%를 삭제하는 경우 유지되는 레코드에서 itemCount==4가 됩니다. 즉, 유지된 모든 레코드마다 4개의 원본 레코드가 있게 됩니다. 

적응 샘플링을 사용하면 응용 프로그램이 과도하게 사용되는 기간 동안 itemCount가 더 높아집니다.

따라서 itemCount를 합하면 원래 이벤트 수에 대한 적절한 추정치가 제공됩니다.


![](./media/app-insights-analytics-tour/510.png)

그룹의 행 수를 계산하려는 경우 `count()` 집계(및 개수 계산 작업)도 있습니다.


[집계 함수](app-insights-analytics-reference.md#aggregations)의 범위가 있습니다.


## <a name="charting-the-results"></a>결과 차트로 작성


```AIQL

    exceptions 
       | summarize count()  
         by bin(timestamp, 1d)
```

기본적으로 결과는 테이블로 표시됩니다.

![](./media/app-insights-analytics-tour/225.png)


테이블 보기보다 더 효과적인 보기가 있습니다. 세로 막대 옵션을 사용하여 차트 보기에서 결과를 살펴보겠습니다.

![차트를 클릭한 다음 세로 막대 차트를 선택하고 x 및 y축을 할당](./media/app-insights-analytics-tour/230.png)

참고로 결과를 시간에 의해 정렬하지 않았지만(테이블 표시에서 확인 가능) 차트 표시는 언제나 날짜 및 시간을 정확한 순서로 표시합니다.


## <a name="[where](app-insights-analytics-reference.md#where-operator):-filtering-on-a-condition"></a>[Where](app-insights-analytics-reference.md#where-operator): 조건에 대한 필터링

앱의 [클라이언트](app-insights-javascript.md) 쪽과 서버 쪽에 대해 모두 Application Insights 모니터링을 설정했다면 데이터베이스의 일부 원격 분석은 브라우저에서 나왔을 것입니다.

브라우저에서 보고하는 예외만 확인하겠습니다.

```AIQL

    exceptions 
  	| where device_Id == "browser" 
  	|  summarize count() 
       by device_BrowserVersion, outerExceptionMessage 
```

![](./media/app-insights-analytics-tour/250.png)

`where` 연산자는 부울 식을 가져옵니다. 여기서 이에 관한 몇 가지 중요한 사항이 있습니다.

 * `and`, `or`: 부울 연산자
 * `==`, `<>`: 같음 및 같지 않음
 * `=~`, `!=`: 대/소문자 구분 없는 문자열 같음 및 같지 않음 더 많은 문자열 비교 연산자가 있습니다.

[스칼라 식](app-insights-analytics-reference.md#scalars)에 대한 모든 내용을 읽어보세요.

### <a name="filtering-events"></a>이벤트 필터링

성공하지 못한 요청 찾기

```AIQL

    requests 
  	| where isnotempty(resultCode) and toint(resultCode) >= 400
```

`responseCode` 은(는) 형식 문자열을 가지고 있으므로 숫자 비교를 위해서는 [캐스트](app-insights-analytics-reference.md#casts) 해야 합니다.

서로 다른 응답 요약:

```AIQL

    requests
  	| where isnotempty(resultCode) and toint(resultCode) >= 400
  	| summarize count() 
      by resultCode
```

## <a name="timecharts"></a>시간 차트

각 날짜에 발생한 이벤트 수 표시:

```AIQL

    requests
      | summarize event_count=count()
        by bin(timestamp, 1d)
```

차트 표시 옵션 선택:

![시간 차트](./media/app-insights-analytics-tour/080.png)

꺾은선형 차트에 대한 x축은 DataTime 형식이어야 합니다. 

## <a name="multiple-series"></a>여러 계열 

`summarize by` 절에 여러 값을 사용하여 각 값 조합에 대해 별도의 행을 생성합니다.

```AIQL

    requests 
      | summarize event_count=count()   
        by bin(timestamp, 1d), client_StateOrProvince
```

![](./media/app-insights-analytics-tour/090.png)

차트에 여러 줄을 표시하려면 **분할 기준** 을 클릭하고 열을 선택합니다.

![](./media/app-insights-analytics-tour/100.png)



## <a name="daily-average-cycle"></a>일일 평균 주기

사용량은 평균 일에 대해 얼마나 변화합니까?

시간으로 범주화된 시간 모듈로 하루 기준 계산 요청:

```AIQL

    requests
  	| extend hour = floor(timestamp % 1d , 1h) 
          + datetime("2016-01-01")
  	| summarize event_count=count() by hour
```

![평균 일의 시간에 대한 꺾은선형 차트](./media/app-insights-analytics-tour/120.png)

>[AZURE.NOTE] 참고로 현재 우리는 차트에 표시하기 위해 기간을 날짜 및 시간으로 변환해야 합니다.


## <a name="compare-multiple-daily-series"></a>여러 개의 일일 계열 비교

서로 다른 주에서 시각에 따라 사용량은 어떻게 변화합니까?

```AIQL
    requests
     | extend hour= floor( timestamp % 1d , 1h)
           + datetime("2001-01-01")
     | summarize event_count=count() 
       by hour, client_StateOrProvince
```

상태별로 차트 분할:

![client_StateOrProvince를 기준으로 분할](./media/app-insights-analytics-tour/130.png)


## <a name="plot-a-distribution"></a>분포 출력

서로 다른 길이의 세션이 몇 개 있습니까?

```AIQL

    requests 
  	| where isnotnull(session_Id) and isnotempty(session_Id) 
  	| summarize min(timestamp), max(timestamp) 
      by session_Id 
  	| extend sessionDuration = max_timestamp - min_timestamp 
  	| where sessionDuration > 1s and sessionDuration < 3m 
  	| summarize count() by floor(sessionDuration, 3s) 
  	| project d = sessionDuration + datetime("2016-01-01"), count_
```

마지막 줄은 날짜 및 시간으로 변환해야 합니다. - 현재 꺾은선형 차트의 x만이 날짜 및 시간일 수 있습니다.

`where` 절은 단발 세션(sessionDuration==0)을 제외하고 x축의 길이를 설정합니다.


![](./media/app-insights-analytics-tour/290.png)



## <a name="[percentiles](app-insights-analytics-reference.md#percentiles)"></a>[백분위수](app-insights-analytics-reference.md#percentiles)

서로 다른 세션 백분위수를 다루는 기간 범위는 무엇입니까?

위의 쿼리를 사용하되 마지막 줄을 다음으로 바꿈:

```AIQL

    requests 
  	| where isnotnull(session_Id) and isnotempty(session_Id) 
  	| summarize min(timestamp), max(timestamp) 
      by session_Id 
  	| extend sesh = max_timestamp - min_timestamp 
  	| where sesh > 1s
  	| summarize count() by floor(sesh, 3s) 
  	| summarize percentiles(sesh, 5, 20, 50, 80, 95)
```

또한 둘 이상의 요청을 포함하고 있는 모든 세션을 비롯하여 정확한 그림을 가져오기 위해 where 절에서 상한을 제거했습니다.

![result](./media/app-insights-analytics-tour/180.png)

확인할 수 있는 사항:

* 세션의 5%가 3분 34초 미만의 기간을 가지고 있음; 
* 세션의 50%가 36분 미만 동안 지속;
* 세션의 5%가 7일보다 오래 지속

각 국가에 대해 별도의 분석을 가져오기 위해 summarize 연산자 둘 다를 통해 client_CountryOrRegion 열을 따로 가져와야 합니다.

```AIQL

    requests 
  	| where isnotnull(session_Id) and isnotempty(session_Id) 
  	| summarize min(timestamp), max(timestamp) 
      by session_Id, client_CountryOrRegion
  	| extend sesh = max_timestamp - min_timestamp 
  	| where sesh > 1s
  	| summarize count() by floor(sesh, 3s), client_CountryOrRegion
  	| summarize percentiles(sesh, 5, 20, 50, 80, 95)
      by client_CountryOrRegion
```

![](./media/app-insights-analytics-tour/190.png)


## <a name="[join](app-insights-analytics-reference.md#join)"></a>[Join](app-insights-analytics-reference.md#join)

요청 및 예외를 비롯한 일부 테이블에 액세스할 수 있습니다.

실패 응답을 반환하는 요청과 관련된 예외를 찾으려면 `session_Id`에 대해 테이블을 조인할 수 있습니다.

```AIQL

    requests 
  	| where toint(responseCode) >= 500 
  	| join (exceptions) on operation_Id 
  	| take 30
```


조인을 수행하기 전에 `project` 을(를) 사용하여 필요한 열만 선택하는 것이 좋습니다.
동일한 절에서 타임스탬프 열의 이름을 바꿉니다.



## <a name="[let](app-insights-analytics-reference.md#let-clause):-assign-a-result-to-a-variable"></a>[Let](app-insights-analytics-reference.md#let-clause): 변수에 결과 할당

[let](./app-insights-analytics-reference.md#let-statements) 을 사용하여 이전 식의 일부를 분리할 수 있습니다. 결과는 변하지 않음:

```AIQL

    let bad_requests = 
      requests
        | where  toint(resultCode) >= 500  ;
    bad_requests
  	| join (exceptions) on session_Id 
  	| take 30
```

> 팁: Analytics 클라이언트에서 이러한 부분 사이에 공백 줄을 넣지 마세요. 이 부분을 모두 실행해야 합니다.


## <a name="accessing-nested-objects"></a>중첩된 개체에 액세스

중첩된 개체에 쉽게 액세스할 수 있습니다. 예를 들어 예외 스트림에서 다음과 같이 구조화된 객체가 표시됩니다.

![result](./media/app-insights-analytics-tour/520.png)

관심이 있는 속성을 선택하여 평면화할 수 있습니다.

```AIQL

    exceptions | take 10
  	| extend method1 = tostring(details[0].parsedStack[1].method)
```

적절한 형식에 대한 [캐스트](app-insights-analytics-reference.md#casts) 를 사용해야 합니다.

## <a name="custom-properties-and-measurements"></a>사용자 지정 속성 및 측정

응용 프로그램이 이벤트에 대한 [사용자 지정 차원(속성) 및 사용자 지정 측정](app-insights-api-custom-events-metrics.md#properties)에 연결되면 `customDimensions` 및 `customMeasurements` 개체에 표시됩니다.


예를 들어 앱이 다음을 포함한 경우입니다.

```C#

    var dimensions = new Dictionary<string, string> 
                     {{"p1", "v1"},{"p2", "v2"}};
    var measurements = new Dictionary<string, double>
                     {{"m1", 42.0}, {"m2", 43.2}};
    telemetryClient.TrackEvent("myEvent", dimensions, measurements);
```

분석에서 이러한 값을 추출하려면:

```AIQL

    customEvents
  	| extend p1 = customDimensions.p1, 
      m1 = todouble(customMeasurements.m1) // cast to expected type

``` 

## <a name="tables"></a>테이블

앱에서 수신하는 원격 분석의 스트림이 여러 테이블을 통해 액세스할 수 있습니다. 각 테이블에 대해 사용할 수 있는 속성의 스키마는 창의 왼쪽에 표시 됩니다.

### <a name="requests-table"></a>요청 테이블

웹 앱 및 페이지 이름별 세그먼트에 대 한 HTTP 요청 수 계산:

![이름별로 분할된 요청 수 계산](./media/app-insights-analytics-tour/analytics-count-requests.png)

가장 많이 실패하는 요청 찾기:

![이름별로 분할된 요청 수 계산](./media/app-insights-analytics-tour/analytics-failed-requests.png)

### <a name="custom-events-table"></a>사용자 지정 이벤트 테이블

사용자 고유의 이벤트를 보내기 위해 [Trackevent()](app-insights-api-custom-events-metrics.md#track-event)를 사용하면 이 테이블에서 이벤트를 읽을 수 있습니다. 

응용 프로그램 코드에 이러한 줄을 포함하는 사례:

```C#

    telemetry.TrackEvent("Query", 
       new Dictionary<string,string> {{"query", sqlCmd}},
       new Dictionary<string,double> {
           {"retry", retryCount},
           {"querytime", totalTime}})
```

이러한 이벤트의 빈도 표시:
 
![사용자 지정 이벤트의 표시 속도](./media/app-insights-analytics-tour/analytics-custom-events-rate.png)

측정값 및 차원을 이벤트에서 추출:

![사용자 지정 이벤트의 표시 속도](./media/app-insights-analytics-tour/analytics-custom-events-dimensions.png)

### <a name="custom-metrics-table"></a>사용자 지정 메트릭 테이블

사용자 지정 메트릭 값을 보내기 위해 [TrackMetric()](app-insights-api-custom-events-metrics.md#track-metric)을 사용하면 **customMetrics** 스트림에 결과가 나타납니다. 예:  

![Application Insights 분석의 사용자 지정 메트릭](./media/app-insights-analytics-tour/analytics-custom-metrics.png)


> [AZURE.NOTE] [메트릭 탐색기](app-insights-metrics-explorer.md)에서 원격 분석의 모든 형식에 연결된 모든 사용자 지정 측정값은 `TrackMetric()`을(를) 사용하여 전송되는 메트릭과 함께 메트릭 블레이드에 표시됩니다. 하지만 분석에서 사용자 지정 측정은 이벤트, 요청 등 수행된 형식의 원격 분석에 연결되고 TrackMetric에 의해 전송된 메트릭은 해당 스트림에 표시됩니다.

### <a name="performance-counters-table"></a>성능 카운터 테이블

[성능 카운터](app-insights-web-monitor-performance.md#system-performance-counters)는 CPU, 메모리, 네트워크 사용률 등 앱에 대한 기본 시스템 메트릭을 보여 줍니다. 사용자 지정 카운터를 포함하여 추가 카운터를 보내도록 SDK를 구성할 수 있습니다.

**performanceCounters** 스키마는 `category`, `counter` 이름 및 각 성능 카운터의 `instance` 이름을 노출합니다. 카운터 인스턴스 이름은이 일부 성능 카운터에만 적용할 수 있으며 일반적으로 개수와 관련된 프로세스의 이름을 나타냅니다. 각 응용 프로그램에 대한 원격 분석 데이터에는 해당 응용 프로그램에 대한 카운터에만 표시됩니다. 예를 들면 어떤 카운터를 사용할 수 있는지 알아보기: 

![Application Insights 분석의 성능 카운터](./media/app-insights-analytics-tour/analytics-performance-counters.png)

최근 기간 동안 사용 가능한 메모리의 차트를 가져오려면: 

![Application Insights 분석의 메모리 시간 차트](./media/app-insights-analytics-tour/analytics-available-memory.png)


다른 원격 분석과 마찬가지로, **performanceCounters**에도 앱이 실행되는 호스트 컴퓨터의 id를 나타내는 열 `cloud_RoleInstance`이 있습니다. 예를 들어, 서로 다른 컴퓨터에서 앱의 성능을 비교하려면: 


![Application Insights 분석에서 역할 인스턴스에 의해 분할된 성능](./media/app-insights-analytics-tour/analytics-metrics-role-instance.png)

### <a name="exceptions-table"></a>예외 테이블

[앱에서 보고된 예외](app-insights-asp-net-exceptions.md)는 이 테이블에서 사용할 수 있습니다. 

예외가 발생하는 경우 앱이 처리하는 HTTP 요청을 찾으려면 operation_Id에 참여합니다.

![Operation_Id에 요청을 사용하여 예외를 조인](./media/app-insights-analytics-tour/analytics-exception-request.png)


### <a name="browser-timings-table"></a>브라우저 타이밍 테이블

`browserTimings`은 사용자의 브라우저에서 수집된 페이지 로드 데이터를 보여 줍니다.

이러한 메트릭을 보려면 [클라이언트쪽 원격 분석을 위해 앱을 설정](app-insights-javascript.md)합니다. 

스키마에 [페이지 로드 프로세스의 여러 단계의 길이를 나타내는 메트릭](app-insights-javascript.md#page-load-performance)이 포함됩니다. (사용자가 한 페이지를 읽는 시간의 길이 나타내지 않습니다.)  

서로 다른 페이지의 popularities 표시하 고 각 페이지에 대한 시간을 로드:

![Analytics에서 페이지 로드 시간](./media/app-insights-analytics-tour/analytics-page-load.png)

### <a name="availbility-results-table"></a>가용성 결과 테이블

`availabilityResults`은 [웹 테스트](app-insights-monitor-web-app-availability.md) 결과를 표시합니다. 각 테스트 위치에서 테스트가 실행될 때마다 개별적으로 보고됩니다. 


![Analytics에서 페이지 로드 시간](./media/app-insights-analytics-tour/analytics-availability.png)

### <a name="dependencies-table"></a>종속성 테이블

앱이 데이터베이스 및 REST Api에 대해 수행한 호출과 TrackDependency()에 대한 다른 호출의 결과가 포함됩니다.

### <a name="traces-table"></a>추적 테이블

Tracktrace()를 사용하여 앱에서 보낸 원격 분석 데이터나 [다른 로깅 프레임워크](app-insights-asp-net-trace-logs.md)가 포함됩니다.

## <a name="try-it!"></a>사용해 보세요.

* 앱이 아직 데이터를 Application Insights로 전송하지 않은 경우, **[시뮬레이션된 데이터에 대한 분석을 테스트](https://analytics.applicationinsights.io/demo)**합니다.


[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]





<!--HONumber=Oct16_HO2-->


