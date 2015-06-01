<properties 
	pageTitle="진단 검색 사용" 
	description="개별 이벤트, 요청 및 로그 추적을 검색하고 필터링합니다." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/22/2015" 
	ms.author="awills"/>
 
#Application Insights에서 진단 검색 사용

진단 검색은 페이지 보기, 예외 사항 또는 웹 요청과 같은 개별 원격 분석 항목을 찾고 검색하는 데 사용하는 [Application Insights][start]에 있는 블레이드입니다. 또한 코딩한 로그 추적 및 이벤트를 볼 수 있습니다.

##진단 검색을 언제 보나요?

다음과 같이 명시적으로 진단 검색을 열 수 있습니다.

![진단 검색 열기](./media/app-insights-diagnostic-search/01-open-Diagnostic.png)


일부 차트와 표 항목을 클릭할 때에도 열립니다. 이 경우 해당 필터는 선택한 항목의 형식에 초점을 맞추어 미리 설정됩니다.

예를들어, 응용 프로그램이 웹 서비스인 경우 개요 블레이드가 요청 볼륨의 차트를 보여줍니다. 이를 클릭하여 각 URL에 대한 요청 수를 보여주는 표가 있는 더 자세한 차트를 가져옵니다. 행을 클릭하고 해당 URL에 대한 개별 요청의 목록을 가져옵니다.

![진단 검색 열기](./media/app-insights-diagnostic-search/07-open-from-filters.png)


진단 검색의 본문은 코딩한 서버 요청, 페이지 보기, 사용자 지정 이벤트 등의 원격 분석 항목의 목록입니다. 목록의 위쪽은 시간이 지남에 따른 이벤트의 수를 보여주는 요약 차트입니다.


##개별 항목 검사

키 필드 및 관련 항목을 보려면 원격 분석 항목을 선택합니다. 필드의 전체 집합을 확인하려는 경우 "..."를 클릭합니다.

![진단 검색 열기](./media/app-insights-diagnostic-search/10-detail.png)

필드의 전체 집합을 찾으려면 일반 문자열(와일드 카드 없이)을 사용합니다. 사용할 수 있는 필드는 원격 분석 유형에 따라 다릅니다.

##이벤트 유형 필터링

필터 블레이드를 열고 확인하려는 이벤트 유형을 선택합니다. (나중에 열었던 블레이드로 필터를 복원하려는 경우 재설정을 클릭합니다.)


![필터 선택 및 원격 분석 유형 선택](./media/app-insights-diagnostic-search/02-filter-req.png)


이벤트 유형은 다음과 같습니다.

* **추적** - TrackTrace, log4Net, NLog 및 System.Diagnostic.Trace 호출을 포함한 진단 로그입니다.
* **요청** - 페이지, 스크립트, 이미지, 스타일 파일 및 데이터를 포함하는 서버 응용 프로그램이 수신하는 HTTP 요청입니다. 이러한 이벤트는 요청을 만들고 개요 차트에 응답하는 데 사용됩니다.
* **페이지 보기** - 웹 클라이언트가 전송한 원격 분석이며, 페이지 보기 보고서를 만드는 데 사용됩니다. 
* **사용자 지정 이벤트** - [사용량을 모니터링][track]하기 위해 TrackEvent()에 호출을 삽입한 경우 여기에서 검색할 수 있습니다.
* **예외 사항** - 서버에서 확인할 수 없는 예외 사항이며 TrackException()을 사용하여 로그합니다.

##속성 값에서 필터링

해당 속성 값에서 이벤트를 필터링할 수 있습니다. 사용 가능한 속성은 선택한 이벤트 유형에 따라 다릅니다.

예를들어 특정 응답 코드에 대한 요청을 선택합니다.

![속성 확장 및 값 선택](./media/app-insights-diagnostic-search/03-response500.png)

특정 속성 값을 선택하지 않는 것은 모든 값을 선택하는 것과 동일한 효과가 있습니다. 해당 속성에서 필터링을 전한합니다.


###검색 범위 좁히기

필터 값의 오른쪽에 있는 수는 현재 필터링된 집합에서 발생한 수를 보여줍니다.

이 예제에서는 `Reports/Employees` 요청이 500개의 오류 중 대부분을 초래함이 명확합니다.

![속성 확장 및 값 선택](./media/app-insights-diagnostic-search/04-failingReq.png)

또한 이 시간 동안 발생하고 있는 기타 이벤트가 무엇인지도 보려는 경우 **정의되지 않은 속성의 이벤트 포함**을 선택할 수 있습니다.

##개별 항목 검사

해당 요청 이름을 필터 집합에 추가하고 해당 이벤트의 개별 항목을 검사할 수 있습니다.

![값 선택](./media/app-insights-diagnostic-search/05-reqDetails.png)

요청 이벤트의 경우, 세부 정보는 요청이 처리되는 동안 발생한 에외 사항을 보여줍니다.

세부 정보를 보려면 예외 사항을 클릭합니다.

![예외 사항 클릭](./media/app-insights-diagnostic-search/06-callStack.png)

##동일한 속성이 있는 이벤트 찾기

동일한 속성 값을 가지는 모든 항목 찾기:

![마우스 오른쪽 단추로 속성 클릭](./media/app-insights-diagnostic-search/12-samevalue.png)

##메트릭 값을 기준으로 검색

5초 미만인 요청 응답 시간을 모두 가져옵니다. 시간은 틱 단위로 나타납니다(10,000틱 = 1ms).

!["Response time":(threshold TO *)](./media/app-insights-diagnostic-search/11-responsetime.png)



##<a name="search"></a>데이터 검색

속성 값 중 하나에서 용어를 검색할 수 있습니다. 속성 값을 포함하는 [사용자 지정 이벤트][track]를 작성한 경우에 특히 유용합니다.

더 짧은 시간 범위로 검색하는 것이 더 빠른 것처럼 시간 범위를 설정하고자 할 수 있습니다.

![진단 검색 열기](./media/appinsights/appinsights-311search.png)

문자열의 일부가 아닌 용어를 검색합니다. 용어는 '.' 및 '_'과 같은 문장 부호를 포함하는 영숫자 문자열입니다. 예를 들면 다음과 같습니다.

용어|해당 용어가 검색되지 *않는* 문자열|해당 용어가 검색되는 문자열
---|---|---
HomeController.About|about<br/>home|h*about<br/>home*
IsLocal|local<br/>is<br/>*local|isl*<br/>islocal<br/>i*l*
New Delay|w d|new<br/>delay<br/>n* AND d*


다음은 사용할 수 있는 검색 식입니다.

샘플 쿼리 | 결과 
---|---
slow|지정된 날짜 범위의 필드에 "slow" 용어가 포함된 모든 이벤트를 찾습니다.
database??|Matches database01, databaseAB, ...<br/>?와 같이 검색 용어를 시작할 수 없습니다.
database*|Matches database, database01, databaseNNNN<br/>*과 같이 검색 용어를 시작할 수 없음
apple AND banana|두 용어를 모두 포함하는 이벤트를 찾습니다. "and"가 아닌 대문자 "AND"를 사용하세요.
apple OR banana<br/>apple banana|둘 중 한 용어를 포함하는 이벤트를 찾습니다. "or"가 아닌 "OR"를 사용하세요.</br/>약식입니다.
apple NOT banana<br/>apple -banana|한 용어를 포함하지만 다른 용어는 포함하지 않는 이벤트를 찾습니다.<br/>약식입니다.
app* AND banana -(grape pear)|논리적 연산자 및 괄호 사용
"Metric": 0 TO 500<br/>"Metric" : 500 TO * | 값의 범위 내에서 명명된 측정을 포함하는 이벤트를 찾습니다.


##검색 저장

원하는 모든 필터를 설치할 때 즐겨찾기로 검색을 저장할 수 있습니다. 조직 계정으로 작업하는 경우 다른 팀 구성원과 공유할 것인지를 선택할 수 있습니다.

![즐겨찾기 클릭, 이름 설정 및 저장 클릭](./media/app-insights-diagnostic-search/08-favorite-save.png)


검색을 다시 보려면 **개요 블레이드로 이동**하여 즐겨찾기를 엽니다.

![즐겨찾기 타일](./media/app-insights-diagnostic-search/09-favorite-get.png)

상대 시간 범위로 저장한 경우 다시 열린 블레이드는 최신 데이터입니다. 절대 시간 범위로 저장한 경우 매번 같은 데이터가 보입니다.


##Application Insights에 더 많은 원격 분석 전송

Application Insights SDK에서 보낸 기본 원격 분석 외에도 다음을 수행할 수 있습니다.

* [.NET][netlogs] 또는 [Java][javalogs]의 즐겨찾는 로깅 프레임워크에서 로그 추적을 캡처합니다. 이는 로그 추적을 통해 검색하고 페이지 보기, 예외 사항 및 기타 이벤트와 상관 관계를 지정할 수 있음을 의미합니다. 
* [코드를 작성][track]하여 사용자 지정 이벤트, 페이지 보기 및 예외 사항을 보냅니다. 

[Application Insights에 로그 및 사용자 지정 원격 분석을 보내는 방법에 대해 알아봅니다][trace].


##<a name="questions"></a>질문 및 답변

###<a name="limits"></a>얼마나 많은 데이터가 보존되나요?

각 응용 프로그램에서 초당 최대 500개의 이벤트가 보존됩니다. 이벤트는 7일 동안 보존됩니다.

###내 서버 요청에서 게시 데이터를 어떻게 볼 수 있나요?

게시 데이터를 자동으로 기록하지는 않지만 [TrackTrace 또는 로그 호출][trace]을 사용할 수 있습니다. 메시지 매개 변수에 게시 데이터를 넣습니다. 속성을 지정할 수 있는 방법으로 메시지에서 필터링할 수는 없지만 크기 제안은 더 깁니다.

##<a name="add"></a>다음 단계

* [Application Insights에 로그 및 사용자 지정 원격 분석 전송][trace]
* [가용성 및 응답성 테스트 설정][availability]
* [문제 해결][qna]





<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[javalogs]: app-insights-java-trace-logs.md
[netlogs]: app-insights-asp-net-trace-logs.md
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-get-started.md
[trace]: app-insights-search-diagnostic-logs.md
[track]: app-insights-custom-events-metrics-api.md


<!--HONumber=54-->