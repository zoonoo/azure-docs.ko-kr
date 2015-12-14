<properties 
	pageTitle="Application Insights SDK에서 샘플링, 필터링 및 전처리" 
	description="원격 분석이 Application Insights 포털에 전송되기 전에 데이터에 대한 속성을 필터링, 샘플링 또는 속성을 추가하는 SDK용 플러그인을 작성합니다." 
	services="application-insights"
    documentationCenter="" 
	authors="alancameronwills" 
	manager="douge"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/04/2015" 
	ms.author="awills"/>

# Application Insights SDK에서 원격 분석 샘플링, 필터링 및 전처리

*Application Insights는 미리 보기 상태입니다.*

Application Insights SDK에 대한 플러그인을 작성하고 구성하여 원격 분석을 Application Insights 서비스에 전송하기 전에 캡처하고 처리하는 방법을 사용자 지정할 수 있습니다.

현재 이러한 기능은 ASP.NET SDK에 사용할 수 있습니다.

* [샘플링](#sampling) 통계를 왜곡하지 않고 원격 분석의 양을 줄입니다. 관련 데이터 요소를 함께 유지하여 문제를 진단할 때 데이터 요소 간을 탐색할 수 있습니다. 포털에서는 샘플링을 보완하기 위해 총 개수를 곱합니다.
 * 고정 비율 샘플링을 사용하면 전송되는 이벤트의 백분율을 결정할 수 있습니다.
 * 적응 샘플링(2.0.0-beta3부터 ASP.NET SDK에 대한 기본값)은 원격 분석의 볼륨에 따라 샘플링 주기를 자동으로 조정합니다. 대상 볼륨을 설정할 수 있습니다.
* [필터링](#filtering) 서버에 전송되기 전에 SDK에서 원격 분석을 선택하거나 수정할 수 있게 해줍니다. 예를 들어 로봇의 요청을 제외하여 원격 분석의 양을 줄일 수 있습니다. 이는 트래픽을 감소시키는 데 있어 샘플링보다 더 기본적인 방법입니다. 이를 통해 전송된 요청을 더 잘 제어할 수 있지만 통계에 영향을 준다는 점을 알고 있어야 합니다(예: 성공한 모든 요청을 필터링하는 경우).
* [속성 추가](#add-properties) 표준 모듈의 원격 분석을 포함하여 앱에서 보낸 모든 원격 분석에 속성을 추가합니다. 예를 들어 계산된 값을 추가하거나 포털에서 데이터를 필터링하는 데 사용할 버전 번호를 추가할 수 있습니다.
* [SDK API](app-insights-api-custom-events-metrics.md) 사용자 지정 이벤트 및 메트릭을 보내는 데 사용됩니다.

시작하기 전에 다음을 수행합니다.

* [Application Insights SDK](app-insights-asp-net.md)를 응용 프로그램에 설치합니다. NuGet 패키지를 수동으로 설치하고 최신 *시험판* 버전을 선택합니다.
* [Application Insights API](app-insights-api-custom-events-metrics.md)를 사용합니다. 


## 샘플링

*이 기능은 베타에 있습니다.*

[샘플링](app-insights-sampling.md)은 정확한 통계를 유지하면서 트래픽을 줄이는 권장 방법입니다. 필터는 관련된 항목을 선택하여 진단 항목 간을 탐색할 수 있도록 합니다. 이벤트 수는 메트릭 탐색기에서 조정되어 필터링된 항목을 보완합니다.

* 적응 샘플링을 사용하는 것이 좋습니다. 적응 샘플링은 특정 요청 볼륨을 달성하기 위해 샘플링 비율을 자동으로 조정합니다. 현재 ASP.NET 서버 측 원격 분석만 사용할 수 있습니다.  
* [고정 비율 샘플링](app-insights-sampling.md)을 사용할 수도 있습니다. 사용자가 샘플링 비율을 지정합니다. ASP.NET 웹앱 코드 및 JavaScript 웹 페이지에서 사용할 수 있습니다. 클라이언트와 서버는 샘플링을 동기화하므로 검색에서 관련된 페이지 보기 및 요청 사이를 이동할 수 있습니다.

### 샘플링을 사용하도록 설정하려면

최신 *시험판* 버전의 Application Insights로 **프로젝트의 NuGet 패키지를 업데이트**합니다. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고, NuGet 패키지 관리를 선택한 다음 **Include Prerelease(시험판 포함)**를 선택하고 Microsoft.ApplicationInsights.Web을 검색합니다.

[ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md)에서 적응 알고리즘에서 목표로 하는 원격 분석의 최대 비율을 조정할 수 있습니다.

    <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>

### 클라이언트 쪽 샘플링

웹 페이지에서 데이터에 대한 고정 비율 샘플링을 가져오려면 일반적으로 \_Layout.cshtml 등의 마스터 페이지에서 삽입한 [Application Insights 조각](app-insights-javascript.md)에 줄을 추가로 배치합니다.

*JavaScript*

```JavaScript

	}({ 

	samplingPercentage: 10.0, 

	instrumentationKey:...
	}); 
```

* 100/N(여기서 N은 정수)과 같은 백분율(이 예제에서는 10)을 설정합니다. 예를 들어 50(=100/2), 33.33(=100/3), 25(=100/4) 또는 10(=100/10)입니다. 
* 서버 쪽에서도 [고정 비율 샘플링](app-insights-sampling.md)을 사용하도록 설정하는 경우 클라이언트와 서버가 샘플링을 동기화하므로 검색에서 관련된 페이지 보기 및 요청 사이를 이동할 수 있습니다.

[샘플링에 대해 자세히 알아봅니다](app-insights-sampling.md).

## 필터링

이 기술을 사용하면 원격 분석 스트림에서 포함되거나 제외된 요청을 보다 직접적으로 제어할 수 있습니다. 샘플링과 함께 사용할 수도 있고 또는 따로 사용할 수도 있습니다.

원격 분석을 필터링하려면 원격 분석 프로세서를 작성하고 SDK를 사용하여 등록합니다. 모든 원격 분석은 해당 프로세서를 거친 다음 스트림에서 삭제하거나 속성을 추가할 수 있습니다. 직접 작성한 원격 분석뿐만 아니라 HTTP 요청 수집기 및 종속성 수집기와 같은 표준 모듈의 원격 분석이 여기에 포함됩니다. 예를 들어 로봇 또는 성공적인 종속성 호출에서 요청에 대한 원격 분석을 필터링할 수 있습니다.

> [AZURE.WARNING]프로세서를 사용하는 SDK에서 보낸 원격 분석을 필터링하는 작업은 포털에 표시되는 통계를 왜곡하고 관련된 항목을 수행하기 어렵게 만들 수 있습니다.
> 
> 대신 [샘플링](#sampling) 사용을 고려하세요.

### 원격 분석 프로세서 만들기

1. Application Insights SDK를 최신 버전(2.0.0-beta2 이상)으로 업데이트합니다. Visual Studio 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 NuGet 패키지 관리를 선택합니다. NuGet 패키지 관리자에서 **Include Prerelease(시험판 포함)**를 선택하고 Microsoft.ApplicationInsights.Web을 검색합니다.

1. 필터를 만들려면 ITelemetryProcessor를 구현합니다. 원격 분석 모듈, 원격 분석 이니셜라이저 및 원격 분석 채널와 같은 또 다른 확장성 지점입니다.

    원격 분석 프로세서는 일련의 프로세싱을 생성합니다. 원격 분석 프로세서를 인스턴스화할 때 과정에서 다음 프로세서에 대한 링크를 전달합니다. 원격 분석 데이터 요소가 프로세스 메서드에 전달되는 경우 해당 작업을 수행하고 과정에서 다음 원격 분석 프로세서를 호출합니다.

    ``` C#

    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
      {
        private ITelemetryProcessor Next { get; set; }

        // You can pass values from .config
        public string MyParamFromConfigFile { get; set; }

        // Link processors to each other in a chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }
        public void Process(ITelemetry item)
        {
            // To filter out an item, just return 
            if (!OKtoSend(item)) { return; }
            // Modify the item if required 
            ModifyItem(item);

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }

        // Example: replace with your own modifiers.
        private void ModifyItem (ITelemetry item)
        {
            item.Context.Properties.Add("app-version", "1." + MyParamFromConfigFile);
        }
    }
    

    ```
2. ApplicationInsights.config에 다음을 삽입합니다. 

```XML

    <TelemetryProcessors>
      <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
         <!-- Set public property -->
         <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
      </Add>
    </TelemetryProcessors>

```

(샘플링 필터를 초기화하는 섹션과 동일합니다.)

클래스에서 명명된 공용 속성을 제공하여 .config 파일의 문자열 값을 전달할 수 있습니다.

> [AZURE.WARNING].config 파일의 형식 이름 및 모든 속성 이름이 코드의 클래스 및 속성 이름과 일치하는지 주의하여 확인해야 합니다. .config 파일에서 존재하지 않는 형식 또는 속성을 참조하는 경우 SDK가 원격 분석을 자동으로 전송하지 못할 수 있습니다.

 
**또는** 코드에서 필터를 초기화할 수 있습니다. Global.asax.cs의 AppStart과 같은 적합한 초기화 클래스의 과정에서 프로세서를 삽입합니다.

```C#

    var builder = TelemetryConfiguration.Active.GetTelemetryProcessorChainBuilder();
    builder.Use((next) => new SuccessfulDependencyFilter(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

이 시점 이후에 만든 TelemetryClients는 프로세서를 사용합니다.

### 예제 필터

#### 가상 요청

보트 및 웹 테스트를 필터링합니다. 메트릭 탐색기가 가상 소스를 필터링하는 옵션을 제공하지만 이 옵션은 SDK에서 필터링하여 트래픽을 감소시킵니다.

``` C#

    public void Process(ITelemetry item)
    {
      if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

      // Send everything else: 
      this.Next.Process(item);
    }

```

#### 실패한 인증

"401" 응답을 사용하여 요청을 필터링합니다.

```C#

public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, just terminate the chain: 
        return;
    }
    // Send everything else: 
    this.Next.Process(item);
}

```

#### 빠른 원격 종속성 호출을 필터링합니다.

느린 호출을 진단하려는 경우 빠른 호출을 필터링합니다.

> [AZURE.NOTE]이 포털에서 참조하는 통계를 왜곡시킵니다. 종속성 차트는 종속성 호출이 모두 실패한 것처럼 보입니다.

``` C#

public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;
            
    if (request != null && request.Duration.Milliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}

```


## 속성 추가

원격 분석 이니셜라이저를 사용하여 모든 원격 분석과 함께 전송되는 전역 속성을 정의하고 표준 원격 분석 모듈의 선택한 동작을 재정의할 수 있습니다.

예를 들어, 웹 패키지에 대한 Application Insights는 HTTP 요청에 대한 원격 분석을 수집합니다. 기본적으로, 모든 요청을 응답 코드 > = 400으로 실패한 것으로 플래그합니다. 하지만 400를 성공으로 처리하려는 경우 성공 속성을 설정하는 원격 분석 이니셜라이저를 제공할 수 있습니다.

원격 분석 이니셜라이저를 제공하는 경우 Track*() 메소드가 호출될 때마다 호출됩니다. 표준 원격 분석 모듈에 의해 호출되는 메서드가 포함됩니다. 규칙에 따라 이러한 모듈은 이니셜라이저에서 이미 설정된 모든 속성을 설정하지 않습니다.

**이니셜라이저 정의**

*C#*

```C#

    using System;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MvcWebRole.Telemetry
    {
      /*
       * Custom TelemetryInitializer that overrides the default SDK 
       * behavior of treating response codes >= 400 as failed requests
       * 
       */
      public class MyTelemetryInitializer : ITelemetryInitializer
      {
        public void Initialize(ITelemetry telemetry)
        {
            var requestTelemetry = telemetry as RequestTelemetry;
            // Is this a TrackRequest() ?
            if (requestTelemetry == null) return;
            int code;
            bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
            if (!parsed) return;
            if (code >= 400 && code < 500)
            {
                // If we set the Success property, the SDK won't change it:
                requestTelemetry.Success = true;
                // Allow us to filter these requests in the portal:
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }
            // else leave the SDK to set the Success property      
        }
      }
    }
```

**이니셜라이저 로드**

ApplicationInsights.config에서:

    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/> 
        ...
      </TelemetryInitializers>
    </ApplicationInsights>

*또는*, 코드에서 이니셜라이저를 인스턴스화할 수 있습니다(예: Global.aspx.cs).


```C#
    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


[이 샘플에 대해 자세히 알아봅니다.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

<a name="js-initializer"></a>
### JavaScript 원격 분석 이니셜라이저

*JavaScript*

포털에서 가져온 초기화 코드 바로 뒤에 원격 분석 이니셜라이저를 삽입합니다.

```JS

    <script type="text/javascript">
        // ... initialization code
        ...({
            instrumentationKey: "your instrumentation key"
        });
        window.appInsights = appInsights;


        // Adding telemetry initializer.
        // This is called whenever a new telemetry item
        // is created.

        appInsights.queue.push(function () {
            appInsights.context.addTelemetryInitializer(function (envelope) {
                var telemetryItem = envelope.data.baseData;

                // To check the telemetry item’s type - for example PageView:
                if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                    // this statement removes url from all page view documents
                    telemetryItem.url = "URL CENSORED";
                }

                // To set custom properties:
                telemetryItem.properties = telemetryItem.properties || {};
                telemetryItem.properties["globalProperty"] = "boo";

                // To set custom metrics:
                telemetryItem.measurements = telemetryItem.measurements || {};
                telemetryItem.measurements["globalMetric"] = 100;
            });
        });

        // End of inserted code.

        appInsights.trackPageView();
    </script>
```

TelemetryItem에서 사용할 수 있는 사용자 지정이 아닌 속성의 요약은 [데이터 모델](app-insights-export-data-model.md/#lttelemetrytypegt)을 참조하세요.

이니셜라이저를 원하는 수만큼 추가할 수 있습니다.


## 참조 문서

* [API 개요](app-insights-api-custom-events-metrics.md)

* [ASP.NET 참조](https://msdn.microsoft.com/library/dn817570.aspx)


## SDK 코드

* [ASP.NET 핵심 SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)


## <a name="next"></a>다음 단계


[검색 이벤트 및 로그][diagnostic]

[샘플 및 연습](app-insights-code-samples.md)

[문제 해결][qna]


<!--Link references-->

[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[create]: app-insights-create-new-resource.md
[data]: app-insights-data-retention-privacy.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=AcomDC_1203_2015-->