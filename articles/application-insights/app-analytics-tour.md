<properties 
	pageTitle="Application Insights Analytics 둘러보기" 
	description="Application Insights의 강력한 검색 도구인 Application Insights Analytics의 모든 주된 쿼리의 짧은 샘플" 
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
	ms.date="03/07/2016" 
	ms.author="awills"/>


 
# Application Insights Analytics 둘러보기


Application Insights Analytics는 [Application Insights](app-insights-overview.md) 원격 분석을 위한 강력한 진단 검색 엔진입니다.


[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]
 
시작하기 위해 몇 가지 기본적인 쿼리를 연습해 보겠습니다.

## Application Insights 데이터에 연결

Application Insights의 앱 [개요 블레이드](app-insights-dashboards.md)에서 분석을 엽니다.

![portal.azure.com을 열고 Application Insights 리소스를 열고 Analytics를 클릭합니다.](./media/app-analytics/001.png)

## 행 수 계산

성능 카운터와 같은 메트릭은 메트릭이라는 테이블에 저장됩니다. 각 행은 앱의 Application Insights SDK에서 수신된 원격 분석 데이터 요소입니다. 테이블이 얼마 큰지 알아내기 위해 단지 행을 세기만 하는 연산자에 테이블의 내용을 파이핑합니다.

```AIQL
	
    requests | count
```

> [AZURE.NOTE] 이동을 클릭하기 전에 커서를 문의 어딘가에 둡니다. 문을 둘 이상의 행으로 분할할 수 있지만 한 문에 빈 라인을 넣으면 안 됩니다. 창에 여러 개의 쿼리를 유지하려면 빈 줄로 분리합니다.

결과:


![](./media/app-analytics-tour/010.png)

	
`Count`은 데이터를 파이프에 배열하고 여러 단계에 걸쳐 필터링, 변형 및 조인할 수 있는 많은 [쿼리 연산자](app-analytics-queries.md) 중 하나입니다.
	
## 가져올 내용: n 개의 행 표시


몇몇 데이터를 확인해 보겠습니다. - 샘플 5 행에 무엇이 있습니까?

```AIQL

	requests | take 5
```

그리고 가져온 내용:

![결과](./media/app-analytics-tour/020.png)

열을 선택하고 해당 위치를 조정합니다.

![결과의 오른쪽 위에 있는 열 선택을 클릭](./media/app-analytics-tour/030.png)

세부 정보를 보려면 모든 항목을 확장합니다.
 
![테이블을 선택하고 열을 구성](./media/app-analytics-tour/040.png)


## Top 및 sort

`take`은 빨리 확인할 결과 샘플을 가져오는 데 유용하지만 테이블의 행을 특정 순서 없이 표시합니다. 순서가 지정된 보기를 가져오려면 `top`(샘플의 경우) 또는 `sort`(전체 테이블에 대해)을 사용합니다.

특정 열을 기준으로 순서가 정해진 처음 n 행 표시:

```AIQL

	requests | top 10 by timestamp desc 
```

* *구문:* 대부분의 연산자는 `by`와 같은 키워드 매개 변수를 가집니다.
* `desc` = 내림차순, `asc` = 오름차순.

![](./media/app-analytics-tour/260.png)

`top...`은 `sort ... | take...`를 설명하는 더 효율적인 방법입니다. 다음과 같이 작성했을 수도 있음:

```AIQL

	requests | sort by timestamp desc | take 10
```

결과는 같지만 조금 더 느리게 실행될 수 있습니다. (`sort`의 별칭인 `order`을 작성할 수도 있습니다.)

테이블 보기의 열 머리글을 사용하여 화면에서 결과를 정렬할 수도 있습니다. 하지만 물론 `take` 또는 `top`를 사용하여 테이블의 일부만 검색했다면 검색한 레코드의 순서만 변경될 것입니다.


## 프로젝트: 열을 선택하고, 이름을 바꾸고 계산

`project`을 사용하여 원하는 열만 선택:

```AIQL

    requests | top 10 by timestamp desc
             | project timestamp, name, resultCode
```

![](./media/app-analytics-tour/240.png)


열 이름을 바꾸고 새 열을 정의할 수도 있음:

```AIQL

    requests 
    | top 10 by timestamp desc 
    | project timestamp, 
               timeOfDay = floor(timestamp % 1d, 1s), 
               name, 
               response = resultCode
```

![result](./media/app-analytics-tour/270.png)

다음 스칼라 식에서:

* `%`은 일반적인 모듈로 연산자입니다. 
* `1d`(자릿수 한 개이며 'd' 한 개)은 하루를 의미하는 시간 범위 리터럴입니다. 시간 범위 리터럴 몇 가지 더: `12h`, `30m`, `10s`, `0.01s`.
* `floor`(별칭 `bin`)은 값을 사용자가 제공하는 기저 값의 가장 가까운 낮은 배수로 반올림합니다. 따라서 `floor(aTime, 1s)`은 시간을 가장 가까운 초로 반올림합니다.

[식](app-analytics-scalars.md)은 모든 일반적인 연산자(`+`, `-`, ...)를 포함할 수 있으며 유용한 함수의 범위가 있습니다.

## 확장: 열 계산

단지 기존 열에 열을 추가하려면 `extend`을 사용:

```AIQL

    requests 
    | top 10 by timestamp desc
    | extend timeOfDay = floor(timestamp % 1d, 1s)
```

`extend`을 사용하면 기존의 열을 모두 유지하려는 경우 `project`보다 덜 자세합니다.

## 요약: 행의 그룹 집계

테이블의 샘플을 살펴보면 서로 다른 원격 분석 데이터가 보고되는 필드를 확인할 수 있습니다. 예를 `exception | take 20`은 `outerExceptionType`라는 필드에 예외 메시지가 보고되었음을 신속하게 보여줍니다.

하지만 개별 인스턴스를 이동하는 대신에 각 형식의 보고된 예외 수를 물어 보겠습니다.

```AIQL

	exceptions 
    | summarize count() by outerExceptionType
```

![](./media/app-analytics-tour/210.png)

`Summarize`은 `by` 절의 명명된 필드에 같은 값을 가진 행을 그룹화하여 각 그룹에 대해 단일 결과 행을 생성합니다. 따라서 이 경우 각 예외 유형에 대해 한 개의 행이 있습니다. 집계 함수 `count()`은 각 그룹의 행 수를 계산하여 결과에 열을 제공합니다.


다양한 [집계 함수](app-analytics-aggregations.md)가 있으며 이 중 여러 개를 하나의 summarize 연산자에 사용하여 여러 개의 계산된 열을 생성할 수 있습니다.

예를 들어 이러한 예외가 발생하는 HTTP 요청을 나열해 보겠습니다. 예외 테이블의 샘플을 다시 검사해 보면 `operation_Name`이라는 열에 HTTP 요청 경로가 보고된 것을 알 수 있습니다.

```AIQL

    exceptions 
    | summarize count(), makeset(operation_Name)
      by outerExceptionType	      
```

![](./media/app-analytics-tour/220.png)

집계 함수 `makeset()`은 각 그룹의 모든 지정된 값의 집합을 생성합니다. 이 예제에서 발생하는 것과 같이 각 예외를 발생시키는 단 하나의 연산이 있습니다.


summarize의 결과에 포함된 내용:

* `by`에 명명된 각 열;
* 그리고 각 집계 식에 대한 열;
* 각 `by` 값 조합에 대한 행.


## 스칼라 값에 의해 요약


스칼라(숫자, 시간 또는 간격) 값을 by 절에 사용할 수 있습니다. 하지만 일반적으로 숫자는 연속된 범위를 채웁니다. 데이터 요소를 그룹화하려면 해당 요소를 불연속 값의 bin에 할당해야 합니다. `bin` 함수가 이 작업에 유용합니다.

```AIQL

    exceptions 
       | summarize count()  
         by bin(timestamp, 1d)
```

![](./media/app-analytics-tour/225.png)

`bin`은 모든 타임스탬프를 1일 간격으로 줄입니다. 이는 `floor`의 별칭이며, 대부분의 언어에 있는 유사한 함수입니다. 이는 단지 `summarize`이 감지 가능한 크기의 그룹에 행을 할당할 수 있도록 모든 값을 지정하는 모듈로의 가장 가까운 배수로 줄일 뿐입니다. (이 함수가 없으면 초의 서로 다른 소수 부분에 대해 결과 행이 있어야 하므로 데이터가 전혀 요약되지 않을 것입니다.)

여기서 테이블 보기보다 더 잘 수행할 수 있습니다. 세로 막대 옵션을 사용하여 차트 보기에서 결과를 살펴보겠습니다.

![차트를 클릭한 다음 세로 막대 차트를 선택하고 x 및 y축을 할당](./media/app-analytics-tour/230.png)

참고로 결과를 시간에 의해 정렬하지 않았지만(테이블 표시에서 확인 가능) 차트 표시는 언제나 날짜 및 시간을 정확한 순서로 표시합니다.


## 여기서: 조건에 대한 필터링

앱의 [클라이언트](app-insights-javascript.md) 쪽과 서버 쪽에 대해 모두 Application Insights 모니터링을 설정했다면 데이터베이스의 일부 원격 분석은 브라우저에서 나왔을 것입니다.

브라우저에서 보고하는 예외만 확인하겠습니다.

```AIQL

    exceptions 
    | where device_Id == "browser" 
    |  summarize count() 
       by device_BrowserVersion, outerExceptionMessage 
```

![](./media/app-analytics-tour/250.png)

`where` 연산자는 부울 식을 가져옵니다. 여기서 이에 관한 몇 가지 중요한 사항이 있습니다.

 * `and`, `or`: 부울 연산자
 * `==`, `<>` : 같음 및 같지 않음
 * `=~`, `!=` : 대/소문자 구분 문자열 같음 및 같지 않음. 더 많은 문자열 비교 연산자가 있습니다.

모든 연산자에 대해서는 [스칼라 식](app-analytics-scalars.md)에서 확인하세요.

### 이벤트 필터링

성공하지 못한 요청 찾기

```AIQL

    requests 
    | where isnotempty(resultCode) and toint(resultCode) >= 400
```

`responseCode`은 형식 문자열을 가지고 있으므로 숫자 비교를 위해서는 [이를 캐스트](app-analytics-scalars.md#casts)해야 합니다.

서로 다른 응답 요약:

```AIQL

    requests
    | where isnotempty(resultCode) and toint(resultCode) >= 400
    | summarize count() 
      by resultCode
```

## 시간 차트

각 날짜에 발생한 이벤트 수 표시:

```AIQL

    requests
      | summarize event_count=count()
        by bin(timestamp, 1d)
```

차트 표시 옵션 선택:

![시간 차트](./media/app-analytics-tour/080.png)

꺾은선형 차트에 대한 x축은 DataTime 형식이어야 합니다.

## 여러 계열 

`summarize by` 절에 여러 값을 사용하여 각 값 조합에 대해 별도의 행을 생성:

```AIQL

    requests 
      | summarize event_count=count()   
        by bin(timestamp, 1d), client_StateOrProvince
```

![](./media/app-analytics-tour/090.png)

차트에 여러 줄을 표시하려면 **분할 기준**을 클릭하고 열을 선택 합니다.

![](./media/app-analytics-tour/100.png)



## 일일 평균 주기

사용량은 평균 일에 대해 얼마나 변화합니까?

시간으로 범주화된 시간 모듈로 하루 기준 계산 요청:

```AIQL

    requests
    | extend hour = floor(timestamp % 1d , 1h) 
          + datetime("2016-01-01")
    | summarize event_count=count() by hour
```

![평균 일의 시간에 대한 꺾은선형 차트](./media/app-analytics-tour/120.png)

>[AZURE.NOTE] 참고로 현재 우리는 차트에 표시하기 위해 기간을 날짜 및 시간으로 변환해야 합니다.


## 여러 개의 일일 계열 비교

서로 다른 주에서 시각에 따라 사용량은 어떻게 변화합니까?

```AIQL
    requests
     | extend hour= floor( timestamp % 1d , 1h)
           + datetime("2001-01-01")
     | summarize event_count=count() 
       by hour, client_StateOrProvince
```

상태별로 차트 분할:

![client\_StateOrProvince를 기준으로 분할](./media/app-analytics-tour/130.png)


## 분포 출력

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


![](./media/app-analytics-tour/290.png)



## 백분위수

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

![result](./media/app-analytics-tour/180.png)

확인할 수 있는 사항:

* 세션의 5%가 3분 34초 미만의 기간을 가지고 있음; 
* 세션의 50%가 36분 미만 동안 지속;
* 세션의 5%가 7일보다 오래 지속

각 국가에 대해 별도의 분석을 가져오기 위해 summarize 연산자 둘 다를 통해 client\_CountryOrRegion 열을 따로 가져와야 합니다.

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

![](./media/app-analytics-tour/190.png)


## Join

메트릭, 예외 및 이벤트의 테이블 세 개에 액세스해야 합니다. `event`은 요청 보고서, 페이지 보기, 사용자 지정 이벤트 등을 포함합니다.

실패 응답을 반환하는 요청과 관련된 예외를 찾기 위해 `session_Id`에 대해 테이블을 조인할 수 있음:

```AIQL

    requests 
    | where toint(responseCode) >= 500 
    | join (exceptions) on operation_Id 
    | take 30
```


조인을 수행하기 전에 `project`을 사용하여 필요한 열만 선택하는 것이 좋은 방식입니다. 동일한 절에서 타임스탬프 열의 이름을 바꿉니다.



## Let: 변수에 결과 할당

[let](./app-analytics-syntax.md#let-statements)을 사용하여 이전 식의 부분을 분리합니다. 결과는 변하지 않음:

```AIQL

    let bad_requests = 
      requests
        | where  toint(resultCode) >= 500  ;
    bad_requests
    | join (exceptions) on session_Id 
    | take 30
```

> 팁: AI Analytics 클라이언트에서 이러한 부분 사이에 공백 줄을 넣지 마십시오. 이 부분을 모두 실행해야 합니다.


[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0309_2016-->