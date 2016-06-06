<properties 
	pageTitle="Application Insights의 가격 책정 및 할당량 관리" 
	description="필요한 가격 계획 선택" 
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
	ms.date="05/17/2016" 
	ms.author="awills"/>

# Application Insights의 가격 책정 및 할당량 관리

*Application Insights는 미리 보기 상태입니다.*

[Visual Studio Application Insights][start]의 [가격][pricing]은 응용 프로그램 당 데이터 볼륨을 기반으로 합니다. 기능의 일부만 제한하고 대부분을 사용할 수 있도록 하는 실질적인 무료 계층이 있습니다.

각 Application Insights 리소스는 별도의 서비스로 요금이 부과되고 Azure 구독에 대한 청구서에 추가됩니다.

[가격 체계를 참고하십시오][pricing].

## Application Insights 리소스에 대한 할당량 및 가격 책정 계획 검토

응용 프로그램 리소스의 설정에서 할당량 + 블레이드 가격을 열 수 있습니다.

![설정, 할당량 + 가격 선택](./media/app-insights-pricing/01-pricing.png)

사용자가 선택한 가격 체계는 다음에 영향을 미칩니다.

* [월간 할당량](#monthly-quota) - 매월 분석할 수 있는 원격 분석의 크기입니다.
* [데이터 속도](#data-rate) - 앱의 데이터를 처리할 수 있는 최대 속도입니다.
* [연속 내보내기](#continuous-export) - 다른 도구 및 서비스에 데이터를 내보낼 수 있는지 여부입니다.

이러한 제한은 각 Application Insights 리소스에 대해 개별적으로 설정됩니다.

### 무료 Premium 평가판

새 Application Insights 리소스를 처음 만들 때 무료 계층에서 시작합니다.

언제든지 30일 무료 Premium 평가판으로 전환할 수 있습니다. 이것은 프리미엄 평가판의 이점을 가져다줍니다. 다른 계층을 명시적으로 선택하지 않으면, 이전에 어떤 계층인지에 관계 없이 30일 후 자동으로 되돌아갑니다 평가 기간 중 언제든지 원하는 계층을 선택해도 30일 기간 끝까지 무료 평가판을 얻을 수 있습니다.


## 월간 할당량

* 응용 프로그램이 달력의 매월에 Application Insights에 대한 원격 분석을 지정된 수량까지 보낼 수 있습니다. 현재 무료 가격 책정 계층에 대한 할당량은 매월 500만 개 데이터 요소이며 다른 체계에 대해서는 대체로 그 이상입니다. 할당량에 도달하면 추가로 구입할 수 있습니다. 실제 숫자에 대한 [가격 체계][pricing]를 참고하십시오. 
* 할당량은 택하고자 하는 가격 책정 계층에 따라 달라집니다.
* 할당량은 매월 첫 날에 자정 UTC부터 계산 됩니다.
* 데이터 요소 차트는 이번달 모두 사용된 할당량이 어느 정도인지를 보여줍니다.
* 할당량은 *데이터 요소*로 측정됩니다. 단일 데이터 요소는 코드에서 또는 표준 원격 분석 모듈 중 하나가 명시적으로 호출하든지, 추적 방법 중 하나를 호출합니다. 여러 연결된 속성 및 메트릭이 있을 수 있습니다.
* 데이터 요소는 다음에 의해 생성됩니다.
 * 예를 들어 요청 또는 충돌을 보고하거나 성능을 측정하는 데이터를 자동으로 수집하는 [SDK 모듈](app-insights-configuration-with-applicationinsights-config.md)입니다.
 * [API](app-insights-api-custom-events-metrics.md) `Track...`는 `TrackEvent` 또는 `trackPageView`와 같은 기록을 호출합니다.
 * 설정한 [가용성 웹 테스트](app-insights-monitor-web-app-availability.md)입니다.
* 디버그하는 동안 Visual Studio 출력 창에 앱에서 전송되는 데이터 요소가 표시됩니다. 브라우저의 디버그 창(일반적으로 F12 키)에서 네트워크 탭을 열어 클라이언트 이벤트를 볼 수 있습니다.
* *세션 데이터*는 할당량 계산에서 제외됩니다. 여기에 사용자, 세션, 환경 및 장치 데이터의 수가 포함됩니다.
* 데이터 요소를 검사하여 계산하려는 경우 다양한 위치에서 찾을 수 있습니다.
 * [진단 검색](app-insights-diagnostic-search.md)에 나타나는 각 항목에는 HTTP 요청, 예외, 로그 추적, 페이지 보기, 종속성 이벤트 및 사용자 지정 이벤트가 있습니다.
 * 성능 카운터와 같은 각 [메트릭](app-insights-metrics-explorer.md)의 원시 측정입니다. (차트에서 보는 요소는 일반적으로 여러 원시 데이터 요소의 집계입니다.)
 * 또한 웹 가용성 차트의 각 요소는 여러 데이터 요소에 대한 집계입니다.
* 디버그하는 동안 원본에서 개별 데이터 요소를 검사할 수도 있습니다.
 * Visual Studio의 디버그 모드에서 앱을 실행하는 경우 데이터 요소는 출력 창에 기록됩니다. 
 * 클라이언트 데이터 요소를 확인하려면 브라우저의 디버그 창(일반적으로 F12 키)을 열고 네트워크 탭을 엽니다.
* 데이터 속도가 [적응 샘플링](app-insights-sampling.md)에서 기본적으로 감소될 수 있습니다. 이는 응용 프로그램 사용이 늘어나도 원격 분석 비율이 예상만큼 증가하지 않는다는 것입니다.

### 초과분

응용 프로그램이 월간 할당량보다 더많이 보내는 경우에 다음을 수행할 수 있습니다:

* 추가 데이터의 지급 자세한 내용은 [가격 체계][pricing]를 참고하십시오. 사전에 이 옵션을 선택할 수 있습니다. 이 옵션은 무료 가격 책정 계층에서 사용할 수 없습니다.
* 가격 책정 계층을 업그레이드하십시오.
* 아무 작업도 수행하지 않습니다. 세션 데이터는 계속해서 기록될 수 있지만 진단 검색 또는 메트릭 탐색기에 다른 데이터가 표시되지 않습니다.


### 보내는 데이터 양이 얼마나 되나요?

가격 책정 블레이드의 아래쪽에 있는 차트는 데이터 지점 유형별로 그룹화된 응용 프로그램의 데이터 지점 볼륨을 보여 줍니다. 메트릭 탐색기에서 이 차트를 만들 수도 있습니다.

![가격 책정 블레이드의 맨 아래](./media/app-insights-pricing/03-allocation.png)

자세한 내용을 보려면 차트를 클릭하거나 자세한 시간 범위는 차트를 가로질러 끌고 (+)를 클릭합니다.

[샘플링](app-insights-sampling.md) 후 차트에 Application Insights 서비스에 도착하는 데이터 양이 표시됩니다.


## 데이터 속도

월간 할당량 외에도 데이터 속도에 대한 조정 제한이 있습니다. 무료 [가격 책정 계층][pricing]의 경우 5분에 걸쳐 평균 초당 200개의 데이터 지점으로 제한되며, 유료 계층의 경우에는 1분에 걸쳐 평균 초당 500개 데이터 지점으로 제한됩니다.

다음과 같이 따로 계산되는 3가지 버킷이 있습니다.

* [TrackTrace 호출](app-insights-api-custom-events-metrics.md#track-trace) 및 [캡처된 로그](app-insights-asp-net-trace-logs.md)
* [예외](app-insights-api-custom-events-metrics.md#track-exception)는 초당 50개 지점으로 제한됩니다.
* 다른 모든 원격 분석(페이지 보기, 세션, 요청, 종속성, 메트릭, 사용자 지정 이벤트, 웹 테스트 결과)



*내 앱이 초당 속도를 초과하게 되면 어떻게 됩니까?*

* 앱에 보내는 데이터의 양은 1분마다 평가됩니다. 해당 분에 대한 평균 초당 속도를 초과하면 서버는 일부 요청을 거부합니다. 그런 다음 SDK의 일부 버전의 경우 몇 분 동안 다시 전송을 시도하고, JavaScript SDK와 같은 다른 SDK만 거부된 데이터를 삭제합니다.

제한이 발생하는 경우 이를 경고하는 알림이 표시됩니다.

*내 앱이 보내는 데이터 요소 수를 어떻게 알 수 있나요?*

* 데이터 볼륨 차트를 보려면 설정/할당량 및 가격을 엽니다.
* 또는 메트릭 탐색기에서 새 차트를 추가하고 **데이터 요소 볼륨**을 메트릭으로 선택합니다. 그룹화로 전환한 다음 **데이터 형식**을 기준으로 그룹화합니다.


### 데이터 속도 줄이기 위한 팁

조정 제한에 도달하는 경우 다음과 같은 몇 가지 작업을 수행할 수 있습니다.

* [샘플링](app-insights-sampling.md)을 사용합니다. 이 기술은 메트릭을 왜곡하지 않으며 검색에서 관련 항목 간 이동 기능을 중단하지 않고 데이터 속도를 낮춥니다.
* 모든 페이지 뷰에 대해 [보고될 수 있는 Ajax 호출 수를 제한](app-insights-javascript.md#detailed-configuration)하거나 Ajax 보고를 해제합니다.
* [ApplicationInsights.config를 편집](app-insights-configuration-with-applicationinsights-config.md)하여 필요하지 않은 컬렉션 모듈을 끕니다. 예를 들어 성능 카운터 또는 종속성 데이터가 필요하지 않다고 결정할 수 있습니다.
* 메트릭을 미리 집계합니다. 앱에 TrackMetric에 대한 호출을 추가한 경우 측정 일괄 처리의 평균 및 표준 편차 계산을 허용하는 오버로드를 사용하여 트래픽을 줄일 수 있습니다. 또는 [사전 집계 패키지](https://www.myget.org/gallery/applicationinsights-sdk-labs)를 사용할 수 있습니다. 


## 샘플링

[샘플링](app-insights-sampling.md)은 진단 검색 중에 관련 이벤트를 찾는 기능은 계속 유지하고 올바른 이벤트 개수를 유지하면서 앱으로 원격 분석이 전송되는 속도를 줄이는 방법입니다. 샘플링은 월간 할당량 내로 유지하는 데 도움이 됩니다.

샘플링에는 여러 가지 유형이 있습니다. 앱이 보내는 원격 분석의 양을 자동으로 조정하는 [적응 샘플링](app-insights-sampling.md)을 사용하는 것이 좋습니다. 웹앱의 SDK에서 작동하여 네트워크에서 원격 분석 트래픽이 감소됩니다. 웹앱 프레임워크가 .NET인 경우 사용할 수 있으려면 SDK의 최신 (베타) 버전만 설치하면 됩니다.

또는 할당량 + 가격 책정 블레이드에 *수집 샘플링*을 설정할 수 있습니다. 이 샘플링 형식은 앱의 원격 분석이 Application Insights 서비스에 들어가는 지점에서 작동합니다. 앱에서 보낸 원격 분석의 양에 영향을 주지 않지만 서비스에서 보존하는 양을 줄여 줍니다.

![할당량 및 가격 책정 블레이드에서 샘플 타일을 클릭하고 샘플링 비율을 선택합니다.](./media/app-insights-pricing/04.png)

샘플링은 요금을 줄이고 월간 할당량 내로 유지하는 효과적인 방법입니다. 샘플링 알고리즘은 예를 들어 검색을 사용하면 특정 예외와 관련된 요청을 찾을 수 있도록 원격 분석의 관련된 항목을 유지합니다. 알고리즘은 또한 요청 속도, 예외 속도 및 기타 카운트에 대해 메트릭 탐색기에 올바른 값을 확인할 수 있도록 올바른 카운트를 유지합니다.


## Azure 구독용 청구서를 검토합니다.

Application Insights 요금은 Azure 청구서에 추가됩니다. Azure 청구서의 자세한 내용은 Azure 포털의 청구 섹션 또는 [Azure 청구 포털](https://account.windowsazure.com/Subscriptions)에서 참고할 수 있습니다.

![측면 메뉴에서 대금 청구를 선택합니다.](./media/app-insights-pricing/02-billing.png)



## 이름 제한

1.	응용 프로그램에는 최대 200개의 고유한 메트릭 이름과 200개의 고유한 속성 이름이 허용됩니다. 메트릭은 TrackMetric을 통해 전송된 데이터와 이벤트 같은 기타 데이터 형식의 측정을 포함합니다. [메트릭 및 속성 이름][api]의 범위는 데이터 형식으로 한정되지 않고 계측 키마다 전역적입니다.
2.	[속성][apiproperties]은 필터링 및 그룹화에만 사용할 수 있으며 각 속성에 허용되는 고유한 값은 100개 미만입니다. 고유한 값이 100개를 초과할 경우 해당 속성을 검색 및 필터링에는 계속 사용할 수 있지만 필터에는 더 이상 사용할 수 없습니다.
3.	요청 이름 및 페이지 URL 같은 표준 속성은 일주일에 고유한 값 1000개로 제한됩니다. 고유한 값이 1000개를 초과할 경우 초과하는 값이 "기타 값"으로 표시됩니다. 원래 값을 전체 텍스트 검색 및 필터링에 계속 사용할 수 있습니다.


## 제한 요약

[AZURE.INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/

 

<!---HONumber=AcomDC_0525_2016-->