<properties 
	pageTitle="Application Insights의 원격 분석 샘플링" 
	description="제어에서 원격 분석의 양을 유지하는 방법입니다." 
	services="application-insights" 
    documentationCenter="windows"
	authors="vgorbenko" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/23/2015" 
	ms.author="awills"/>

#  Application Insights의 샘플링

*Application Insights는 미리 보기 상태입니다.*


샘플링은 응용 프로그램 데이터의 통계적으로 올바른 분석을 유지하면서 원격 분석의 축소된 집합을 수집하고 저장할 수 있도록 하는 Application Insights의 기능입니다. 이는 트래픽을 줄이고 [제한](app-insights-pricing.md#data-rate)을 방지하는 데 도움을 줍니다. 데이터는 관련된 항목이 허용한 방법으로 필터링되므로 진단 조사를 수행할 때 항목 간 이동을 할 수 있습니다. 포털에서 메트릭 개수가 나타나면 통계에 영향을 최소화하기 위해 샘플링을 고려하도록 다시 정상화됩니다.

적응 샘플링은 ASP.NET, 버전 2.0.0-beta3 이상인 경우 Application Insights SDK에서 기본적으로 사용되도록 설정되어 있습니다. 샘플링은 현재 베타에 있으며 향후 변경될 수 있습니다.

다음과 같은 두 가지 대체 샘플링 모듈이 있습니다.

* 적응 샘플링은 특정 요청 볼륨을 달성하기 위해 샘플링 비율을 자동으로 조정합니다. 현재 ASP.NET 서버 측 원격 분석만 사용할 수 있습니다.  
* 고정 비율 샘플링을 사용할 수도 있습니다. 사용자가 샘플링 비율을 지정합니다. ASP.NET 웹앱 코드 및 JavaScript 웹 페이지에서 사용할 수 있습니다. 클라이언트와 서버는 샘플링을 동기화하므로 검색에서 관련된 페이지 보기 및 요청 사이를 이동할 수 있습니다.

## 적응 샘플링 사용

최신 *시험판* 버전의 Application Insights로 **프로젝트의 NuGet 패키지를 업데이트**합니다. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고, NuGet 패키지 관리를 선택하고 **Include prerelease(시험판 포함)**를 선택한 다음 Microsoft.ApplicationInsights.Web을 검색합니다.

[ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md)의 `AdaptiveSamplingTelemetryProcessor` 노드에서 매개 변수 수를 조정할 수 있습니다. 표시된 수치는 기본값입니다.

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`

    대상 비율은 **하나의 단일 서버 호스트에** 대한 것을 목표로 하는 적응 알고리즘입니다. 여러 호스트에서 웹앱을 실행하는 경우 Application Insights 포털에서 트래픽을 대상 비율 범위 내에서 유지하기 위해 이 값을 줄입니다.

* `<EvaluationInterval>00:00:15</EvaluationInterval>`

    현재 비율의 원격 분석 간격이 다시 평가됩니다. 평가는 이동 평균으로 수행됩니다. 원격 분석이 급격히 증가하는 경우 이 간격을 줄일 수 있습니다.

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`

    샘플링 비율 값을 변경한 후 더 적은 데이터를 캡처하기 위해 샘플링 비율을 다시 낮출 수 있는 시간 제한입니다.

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageDecreaseTimeout>`

    샘플링 비율 값을 변경한 후 더 많은 데이터를 캡처하기 위해 샘플링 비율을 다시 높일 수 있는 시간 제한입니다.

* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`

    샘플링 비율이 변경됨에 따라 사용자가 설정할 수 있는 최소값입니다.

* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`

    샘플링 비율이 변경됨에 따라 사용자가 설정할 수 있는 최대값입니다.

* `<MovingAverageRatio>0.25</MovingAverageRatio>`

    이동 평균 계산에서 가중치는 가장 최근의 값에 할당됩니다. 1보다 작거나 같은 값을 사용합니다. 값이 작을수록 알고리즘은 갑작스런 변화에 덜 반응합니다.

* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`

    앱을 막 시작했을 때 할당된 값입니다. 디버깅하는 동안 이 값을 줄이지 마십시오.

### 대체: 코드에서 적응 샘플링 구성

.config 파일의 샘플링을 조정하는 대신 코드를 사용할 수 있습니다. 이를 통해 샘플링 속도가 다시 계산될 때마다 호출되는 콜백 함수를 지정할 수 있습니다. 예를 들어 이를 사용하여 샘플링 속도를 사용하는 작업을 찾을 수 있습니다.

.config 파일에서 `AdaptiveSamplingTelemetryProcessor` 노드를 제거합니다.



*C#*

```C#

    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var adaptiveSamplingSettings = new SamplingPercentageEstimatorSettings();

    // Optional: here you can adjust the settings from their defaults.

    var builder = TelemetryConfiguration.Active.GetTelemetryProcessorChainBuilder();
    
    builder.UseAdaptiveSampling(
         adaptiveSamplingSettings,

        // Callback on rate re-evaluation:
        (double afterSamplingTelemetryItemRatePerSecond,
         double currentSamplingPercentage,
         double newSamplingPercentage,
         bool isSamplingPercentageChanged,
         SamplingPercentageEstimatorSettings s
        ) =>
        {
          if (isSamplingPercentageChanged)
          {
             // Report the sampling rate.
             telemetryClient.TrackMetric("samplingPercentage", newSamplingPercentage);
          }
      });

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([원격 분석 프로세서에 대해 알아봅니다](app-insights-api-filtering-sampling.md#filtering).)


<a name="other-web-pages"></a>
## JavaScript를 사용하는 웹 페이지에 대한 샘플링

서버에서 고정 비율 샘플링에 대한 웹 페이지를 구성할 수 있습니다.

[Application Insights에 대한 웹 페이지를 구성](app-insights-javascript.md)할 때 Application Insights 포털에서 얻은 코드 조각을 수정합니다. (ASP.NET 앱에서 코드 조각은 일반적으로 \_Layout.cshtml로 이동합니다.) 계측 키 앞에 `samplingPercentage: 10,`과 같은 줄을 삽입합니다.

    <script>
	var appInsights= ... 
	}({ 


    // Value must be 100/N where N is an integer.
    // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
	samplingPercentage: 10, 

	instrumentationKey:...
	}); 
	
	window.appInsights=appInsights; 
	appInsights.trackPageView(); 
	</script> 

샘플링 비율의 경우 100/N(여기서 N은 정수)에 가까운 백분율을 선택합니다. 현재 샘플링은 다른 값을 지원하지 않습니다.

서버에서도 고정 비율 샘플링을 사용하도록 설정하는 경우 클라이언트와 서버가 동기화되므로 검색에서 관련된 페이지 보기 및 요청 사이를 이동할 수 있습니다.


## 서버에서 고정 비율 샘플링 사용

1. 최신 *시험판* 버전의 Application Insights로 **프로젝트의 NuGet 패키지를 업데이트**합니다. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고, NuGet 패키지 관리를 선택하고 **시험판 포함**을 선택한 다음 Microsoft.ApplicationInsights.Web을 검색합니다. 

2. **적응 샘플링을 사용하지 않도록 설정**: [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md)에서 `AdaptiveSamplingTelemetryProcessor` 노드를 제거하거나 주석으로 처리합니다.

    ```xml

    <TelemetryProcessors>
    <!-- Disabled adaptive sampling:
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    -->
    

    ```

2. **고정 비율 샘플링 모듈을 사용하도록 설정합니다.** 다음 코드 조각을 [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md)에 추가합니다.

    ```XML

    <TelemetryProcessors>
     <Add  Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

      <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
      <SamplingPercentage>10</SamplingPercentage>
      </Add>
    </TelemetryProcessors>

    ```

> [AZURE.NOTE]샘플링 비율의 경우 100/N(여기서 N은 정수)에 가까운 백분율을 선택합니다. 현재 샘플링은 다른 값을 지원하지 않습니다.



### 대체: 서버 코드에서 고정 비율 샘플링을 사용하도록 설정


.config 파일에 샘플링 매개 변수를 설정하는 대신 코드를 사용할 수 있습니다.

*C#*

```C#

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.GetTelemetryProcessorChainBuilder();
    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([원격 분석 프로세서에 대해 알아봅니다](app-insights-api-filtering-sampling.md#filtering).)

## 언제 샘플링을 사용합니까?

ASP.NET SDK 버전 2.0.0-beta3 이상을 사용하는 경우 적응 샘플링이 자동으로 사용되도록 설정됩니다.

대부분의 중소 응용 프로그램은 샘플링하지 않아도 됩니다. 가장 유용한 진단 정보 및 가장 정확한 통계는 모든 사용자 활동에서 데이터를 수집하여 구합니다.

 
샘플링의 주요 장점은 다음과 같습니다.

* 앱이 짧은 시간 간격으로 매우 높은 비율의 원격 분석을 전송할 때 Application Insights 서비스는 ("제한") 데이터 요소를 삭제합니다. 
* 가격 책정 계층에 대한 데이터 요소의 [할당량](app-insights-pricing.md)을 유지합니다. 
* 원격 분석의 컬렉션에서 네트워크 트래픽을 줄입니다. 

### 고정 비율 샘플링 또는 적응 샘플링?

다음과 같은 경우 고정 비율 샘플링을 사용합니다.

* 사용자가 클라이언트 및 서버 간의 동기화된 샘플링을 원하는 경우 [검색](app-insights-diagnostic-search.md)에서 이벤트를 조사하고 있을 때 클라이언트 및 서버에서 페이지 보기 및 http 요청과 같은 관련 이벤트 사이를 탐색할 수 있습니다.
* 사용자가 앱에 대한 적절한 샘플링 비율을 확신하는 경우입니다. 샘플링 비율은 정확한 메트릭을 가져오기 위해 충분히 높아야 하지만 가격 책정 할당량 및 조정 제한을 초과하는 비율보다 낮아야 합니다. 
* 사용자가 앱을 디버깅하지 않는 경우입니다. F5를 눌러 사용자 앱의 몇 페이지를 디버깅하면 모든 원격 분석을 볼 수 있습니다.

그렇지 않은 경우 적응 샘플링을 사용하는 것이 좋습니다.

## 샘플링은 어떻게 작동되나요?

응용 프로그램 관점에서 샘플링은 Application Insights SDK의 기능.입니다. 모든 데이터 요소에서 어느 정도 비율을 Application Insights 서비스에 보내야 하는지 지정합니다. Application Insights SDK의 2.0.0 버전에서 사용자 코드의 샘플링 비율을 제어할 수 있습니다. (또한 이후 버전의 SDK는 ApplicationInsights.config 파일에서 샘플링 비율을 구성하도록 합니다.)

SDK는 어떤 원격 분석 항목을 삭제하고 어떤 항목을 유지할지 결정합니다. 샘플링 의사 결정은 상호 관련된 데이터 요소를 그대로 유지하려는 목표의 여러 규칙에 기반하며 이는 감소된 데이터 집합을 사용하더라도 실행할 수 있고 신뢰할 수 있는 Application Insights에서 진단 환경을 유지 관리합니다. 예를 들어 실패한 요청의 경우 앱이 추가 원격 분석 항목을 전송하면(예: 해당 요청에서 기록된 예외 및 추적) 샘플링은 해당 요청 및 기타 원격 분석을 분할하지 않습니다. 전체를 유지하거나 삭제합니다. 결과적으로 Application Insights에서 요청 세부 정보를 볼 때 항상 연결된 원격 분석 항목과 함께 요청을 확인할 수 있습니다.

"사용자"를 정의하는 응용 프로그램의 경우(즉, 가장 일반적인 웹 응용 프로그램) 샘플링 의사 결정은 사용자 ID의 해시에 기반하며 이는특정 사용자에 대한 모든 원격 분석이 유지되거나 삭제된다는 의미입니다. 사용자를 정의하지 않는 응용 프로그램 형식의 경우(예: 웹 서비스) 샘플링 의사 결정은 요청의 작업 ID를 기반으로 합니다. 마지막으로 사용자 또는 작업 ID가 없는 원격 분석 항목의 경우(예: http 컨텍스트가 없는 비동기 스레드에서 보고된 원격 분석 항목) 샘플링은 단순히 각 형식의 원격 분석 항목 백분율을 캡처합니다.

원격 분석을 다시 표시하는 경우 Application Insights 서비스는 누락된 데이터 요소를 보완하기 위해 컬렉션의 시간에 사용된 동일한 샘플링 백분율로 메트릭을 조정합니다. 따라서 Application Insights에서 원격 분석을 보면 사용자는 실제 수에 가까운 통계적으로 올바른 근사치를 확인할 수 있습니다.

근사치의 정확도는 주로 구성된 샘플링 비율에 따라 다릅니다. 또한 아주 많은 사용자에게서 많은 양의 일반적으로 비슷한 요청을 처리하는 응용 프로그램에 대해 정확도가 증가합니다. 반면에 상당한 부하가 있을 때 작동하지 않는 응용 프로그램의 경우 이러한 응용 프로그램이 일반적으로 할당량 내에 있으면서 제한에서 데이터 손실이 발생하지 않고 해당 원격 분석을 모두 보낼 수 있기에 샘플링은 필요하지 않습니다.

이러한 자릿수의 형식 감소가 매우 바람직하지 않을 수 있기 때문에 Application Insights는 메트릭 및 세션 원격 분석 형식을 샘플링하지 않습니다.

### 적응 샘플링

적응 샘플링은 SDK에서 현재의 전송 속도를 모니터링하는 구성 요소를 추가하여 샘플링 비율이 대상 최대 비율 안에서 유지되도록 조정합니다. 조정은 정기적으로 다시 계산되고 발신 전송 속도의 이동 평균에 기반합니다.

## 샘플링 및 JavaScript SDK

클라이언트 측(JavaScript) SDK는 서버 측 SDK와 샘플링에 참여합니다. 계측된 페이지는 서버 측이 "샘플"로 결정한 동일한 사용자로부터 클라이언트 측 원격 분석만을 보냅니다. 이 논리는 전체 클라이언트 및 서버 측에 대한 사용자 세션의 무결성을 유지하도록 설계되었습니다. 결과적으로 Application Insights의 특정 원격 분석 항목에서 해당 사용자 또는 세션에 대한 다른 모든 원격 분석 항목을 찾을 수 있습니다.

*위에 설명한 대로 내 클라이언트 및 서버 측 원격 분석은 조정된 샘플을 표시하지 않습니다.*

* 서버 및 클라이언트 모두에서 샘플링을 사용할 수 있는지 확인합니다.
* SDK 버전이 2.0 이상이어야 합니다.
* 클라이언트와 서버 모두에서 동일한 샘플링 비율을 설정하도록 합니다.


## 질문과 대답 

*샘플링은 왜 간단히 "각 원격 분석 형식의 X 퍼센트를 수집"하지 않습니까?*

 *  이 샘플링 방법이 메트릭 근사치에 매우 높은 정밀도를 제공하지만 사용자, 세션 및 요청 단위 당 진단 데이터를 연결하는 기능을 중단시키며 이는 진단에 있어 매우 중요합니다. 따라서 샘플링은 "앱 사용자의 X 퍼센트에 대해 모든 원격 분석 항목 수집" 또는 "앱 요청의 X퍼센트에 대한 모든 원격 분석 수집" 논리를 사용하여 더 잘 작동합니다. 요청과 연결되지 않은 원격 분석 항목의 경우(예: 배경 비동기 처리) "각 원격 분석 형식에 대해 모든 항목의 X퍼센트 수집"으로 대체됩니다. 

*샘플링 비율은 시간이 지나면서 달라질 수 있습니까?*

 * 예, 적응 샘플링은 원격 분석의 현재 관찰된 양에 따라 샘플링 비율을 점차적으로 변경합니다.

*적응 샘플링을 사용하는 샘플링 비율을 확인할 수 있습니까?*

 * 예, 적응 샘플링 구성의 코드 메서드를 사용하면 샘플링 비율을 가져오는 콜백을 제공할 수 있습니다.

*고정 비율 샘플링을 사용하는 경우 내 앱에 가장 적합한 샘플링 비율을 어떻게 알 수 있습니까?*

* 한 가지 방법은 적응 샘플링으로 시작하고 안정적인 비율(위 질문 참조)을 찾은 다음 해당 비율을 사용하여 고정 비율 샘플링으로 전환하는 것입니다. 

    그렇지 않으면 추측해야 합니다. AI에서 현재 원격 분석 사용량을 분석하고 발생하는 모든 제한을 관찰하며 수집된 원격 분석의 양을 추정합니다. 선택된 가격 책정 계층과 함께 이러한 세 번의 입력은 수집된 원격 분석의 양을 얼마나 줄여야 할지 제안합니다. 그러나 사용자 수가 증가하거나 원격 분석의 양이 약간 다르게 변화되면 추정치가 무효화될 수도 있습니다.

*샘플링 비율을 너무 낮게 구성하는 경우 어떻게 됩니까?*

* 샘플링 비율이 지나치게 낮으면(지나친 샘플링) Application Insights가 데이터의 양을 축소하기 위해 데이터의 시각화를 보완하려고 할 때 근사치의 정확도가 줄어듭니다. 또한 종종 실패하거나 요청이 느린 경우 샘플링되기에 진단 환경은 부정적으로 영향을 받습니다.

*샘플링 비율을 너무 높게 구성하는 경우 어떻게 됩니까?*

* 너무 높은 샘플링 비율를 구성(충분히 적극적이지 않은)하면 수집된 원격 분석의 양이 충분히 감소되지 않습니다. 여전히 제한에 관련된 원격 분석 데이터 손실이 발생할 수 있고 Application Insights를 사용하는 비용은 초과 요금때문에 계획된 것 보다 클 수 있습니다.

*샘플링을 어떤 플랫폼에서 사용할 수 있습니까?*

* 현재 적응 샘플링은 ASP.NET 웹앱(Azure 또는 사용자 서버에서 호스트된)의 서버 측에서 사용할 수 있습니다. 고정 비율 샘플링은 모든 웹 페이지, .NET 웹 응용 프로그램의 클라이언트 측 및 서버 측에서 사용할 수 있습니다.

*항상 보고 싶은 확실히 드문 이벤트가 있습니다. 이전의 샘플링 모듈에서 그 이벤트를 어떻게 가져올 수 있습니까?*

 * 기본 활성값이 아닌 새 TelemetryConfiguration을 사용하여 별도의 TelemetryClient 인스턴스를 초기화합니다. 이를 사용하여 드문 이벤트를 보냅니다.

<!---HONumber=AcomDC_1217_2015-->