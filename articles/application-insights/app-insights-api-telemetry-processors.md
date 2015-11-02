<properties 
	pageTitle="사용자 지정 이벤트 및 메트릭용 Application Insights API" 
	description="장치 또는 데스크톱 앱, 웹 페이지, 서비스에 코드를 몇 줄 삽입하여 사용 및 진단 문제를 추적할 수 있습니다." 
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
	ms.date="09/23/2015" 
	ms.author="awills"/>

# Application Insights SDK에서 원격 분석 처리

*Application Insights는 미리 보기 상태입니다.*

Application Insights SDK에 대한 플러그인을 작성하고 구성하여 원격 분석을 Application Insights 서비스에 전송하기 전에 캡처하고 처리하는 방법을 사용자 지정할 수 있습니다.

* [샘플링](#sampling)은 통계를 왜곡하지 않고 원격 분석의 양을 줄입니다. 관련된 데이터 요소를 모아서 쉽게 검색하도록 합니다.
* [원격 분석 프로세서](#telemetry-processors)는 서버에 전송되기 전에 SDK에서 데이터를 초기화, 필터 및 샘플링합니다. 예를 들어 일부 원격 분석에 계산된 속성을 추가하거나 로봇에서 요청을 제외하여 원격 분석의 양을 줄일 수 있습니다. 
* [원격 분석 이니셜라이저](#telemetry-initializers)는 전송되는 모든 원격 분석에 기본 속성을 추가하고 표준 원격 분석 모듈의 일부 동작을 재정의할 수 있습니다. 각 추적 호출에 대한 속성을 평가하므로 이러한 값을 변경할 수 있습니다.
* 또한 [컨텍스트 이니셜라이저](#context-initializers)는 전역 속성을 설정합니다. 속성은 시작 시 한 번 설정하면 나중에 변경할 수 없습니다. CPU 시간을 일부 저장하지만(원격 분석 이니셜라이저와 반대로) 또한 유연성이 아주 낮습니다.
* [SDK API](app-insights-api-custom-events-metrics.md)는 사용자 지정 이벤트 및 메트릭을 전송하는 데 사용됩니다.

이러한 여러 메커니즘 간에 일부 중복성이 있으며 이니셜라이저는 나중에 변경될 가능성이 있습니다.

시작하기 전에 다음을 수행합니다.

* [Application Insights SDK](app-insights-asp-net.md)를 응용 프로그램에 설치합니다. 원격 분석 프로세서는 다음을 필요로 합니다. 
* [Application Insights API](app-insights-api-custom-events-metrics.md)를 사용합니다. 


## 샘플링

*이 기능은 베타에 있습니다.*

정확한 통계를 유지하면서 트래픽을 줄이는 방법이 권장됩니다. 필터는 관련된 항목을 선택하여 진단 항목 간을 탐색할 수 있도록 합니다. 이벤트 수는 메트릭 탐색기에서 조정되어 필터링된 항목을 보완합니다.

ASP.NET 서버의 경우 Application\_Start와 같은 적합한 초기화 위치에 삽입합니다.

*C#*

```C#

    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    // This configures sampling percentage at 10%:
    TelemetryConfiguration.Active.TelemetryChannel = new TelemetryChannelBuilder().UseSampling(10.0).Build();

```

웹 페이지의 경우 삽입한 [Application Insights 조각](app-insights-javascript.md)의 줄을 추가로 둡니다.(일반적으로 \_Layout.cshtml 같은 마스터 페이지에서)

*JavaScript*

```JavaScript

	}({ 

	samplingPercentage: 10.0, 

	instrumentationKey:...
	}); 
```

* 100/N(여기서 N은 정수)과 같은 백분율(이 예제의 10)을 설정합니다. - 예를 들어 50(=1/2), 33.33(=1/3), 25(=1/4), 10(=1/5)입니다.
* 웹 페이지와 서버 모두에서 샘플링을 설정한 경우 양쪽 모두에서 동일한 샘플링 비율을 설정해야 합니다.
* 클라이언트 및 서버 측은 관련된 항목을 선택하도록 조정합니다.

[샘플링에 대해 자세히 알아봅니다](app-insights-sampling.md).

## 원격 분석 프로세서

원격 분석 프로세서를 작성하여 백엔드 포털로 전송되기 전에 Application Insights SDK에서 생성된 원격 분석을 필터링하고 처리합니다. HTTP 요청 수집기 및 종속성 수집기와 같은 표준 모듈에서 원격 분석을 포함합니다. 예를 들어 로봇 또는 성공적인 종속성 호출에서 요청에 대한 원격 분석을 필터링할 수 있습니다.

> [AZURE.WARNING]프로세서를 사용하는 SDK에서 보낸 원격 분석을 필터링하는 작업은 포털에 표시되는 통계를 왜곡하고 관련된 항목을 수행하기 어렵게 만들 수 있습니다.
> 
> 대신 [샘플링](#sampling) 사용을 고려합니다.

### 원격 분석 프로세서 만들기

1. 필터를 만들려면 ITelemetryProcessor를 구현합니다. 원격 분석 모듈, 원격 분석 이니셜라이저 및 원격 분석 채널와 같은 또 다른 확장성 지점입니다. 

    원격 분석 프로세서는 일련의 프로세싱을 생성합니다. 원격 분석 프로세서를 인스턴스화할 때 과정에서 다음 프로세서에 대한 링크를 전달합니다. 원격 분석 데이터 요소가 프로세스 메서드에 전달되는 경우 해당 작업을 수행하고 과정에서 다음 원격 분석 프로세서를 호출합니다.

    ``` C#

    namespace FilteringTelemetryProcessor
    {
      using Microsoft.ApplicationInsights.Channel;
      using Microsoft.ApplicationInsights.DataContracts;

      class UnauthorizedRequestFilteringProcessor : ITelemetryProcessor
      {
        public UnauthorizedRequestFilteringProcessor(ITelemetryProcessor next)
		//Initialization will fail without this constructor. Link processors to each other
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
        }      private ITelemetryProcessor Next { get; set; }
      }
    }

    ```
2. Global.asax.cs의 AppStart과 같은 적합한 초기화 클래스의 과정에서 프로세서를 삽입합니다.

    ```C#

    var builder = new TelemetryChannelBuilder();
    builder.Use((next) => new UnauthorizedRequestFilteringProcessor(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    TelemetryConfiguration.Active.TelemetryChannel = builder.Build();

    ```

    이 시점 이후에 만든 TelemetryClients는 프로세서를 사용합니다.

### 예제 필터

#### 가상 요청

보트 및 웹 테스트를 필터링합니다. 메트릭 탐색기가 가상 소스를 필터링하는 옵션을 제공하지만 이 옵션은 SDK에서 필터링하여 트래픽을 감소시킵니다.

``` C#

public void Process(ITelemetry item)
{
    if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource))
    { return; }

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


## 원격 분석 이니셜라이저

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

telemetryItem에서 사용할 수 있는 사용자 지정이 아닌 속성의 요약은 [데이터 모델](app-insights-export-data-model.md/#lttelemetrytypegt)을 참조하세요.

이니셜라이저를 원하는 수만큼 추가할 수 있습니다.






## <a name="default-properties"></a>컨텍스트 이니셜라이저 - 모든 원격 분석에 대한 기본 속성 설정

유니버설 아니셜라이저를 설정하여 새로운 모든 TelemetryClients는 사용자 컨텍스트를 자동으로 사용할 수 있습니다. 여기에는 웹 서버 요청 추적처럼 플랫폼별 원격 분석 모듈에서 전송하는 표준 원격 분석이 포함됩니다.

일반적으로 여러 앱 버전 또는 여러 앱 구성 요소에서 들어오는 원격 분석을 식별하는 데 사용됩니다. 포털에서 "응용 프로그램 버전" 속성을 사용하여 결과를 필터링 또는 그룹화할 수 있습니다.

일반적으로 [컨텍스트 이니셜라이저 대신 원격 분석 이니셜라이저를 사용하는 것이 좋습니다](http://apmtips.com/blog/2015/06/09/do-not-use-context-initializers/).

#### 컨텍스트 이니셜라이저 정의


*C#*

```C#

    using System;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MyNamespace
    {
      // Context initializer class
      public class MyContextInitializer : IContextInitializer
      {
        public void Initialize (TelemetryContext context)
        {
            if (context == null) return;

            context.Component.Version = "v2.1";
        }
      }
    }
```

*Java*

```Java

    import com.microsoft.applicationinsights.extensibility.ContextInitializer;
    import com.microsoft.applicationinsights.telemetry.TelemetryContext;

    public class MyContextInitializer implements ContextInitializer {
      @Override
      public void initialize(TelemetryContext context) {
        context.Component.Version = "2.1";
      }
    }
```

#### 컨텍스트 이니셜라이저 로드

ApplicationInsights.config에서:

    <ApplicationInsights>
      <ContextInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MyNamespace.MyContextInitializer, MyAssemblyName"/> 
        ...
      </ContextInitializers>
    </ApplicationInsights>

*또는*, 코드에서 이니셜라이저를 인스턴스화할 수 있습니다.

*C#*

```C#

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.ContextInitializers
        .Add(new MyContextInitializer());
    }
```

*Java*

```Java

    // load the context initializer
    TelemetryConfiguration.getActive().getContextInitializers().add(new MyContextInitializer());
```



## 참조 문서

* [API 개요](app-insights-api-custom-events-metrics.md)

* [ASP.NET 참조](https://msdn.microsoft.com/library/dn817570.aspx)
* [Java 참조](http://dl.windowsazure.com/applicationinsights/javadoc/)
* [JavaScript 참조](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)
* [Android SDK](https://github.com/Microsoft/ApplicationInsights-Android)
* [iOS SDK](https://github.com/Microsoft/ApplicationInsights-iOS)


## SDK 코드

* [ASP.NET 핵심 SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [Android SDK](https://github.com/Microsoft/ApplicationInsights-Android)
* [Java SDK](https://github.com/Microsoft/ApplicationInsights-Java)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)
* [iOS SDK](https://github.com/Microsoft/ApplicationInsights-iOS)
* [모든 플랫폼](https://github.com/Microsoft?utf8=%E2%9C%93&query=applicationInsights)

## 질문

* *Track\_() 호출에서 발생할 수 있는 예외는 무엇인가요?*
    
    없음 try-catch 절에 래핑할 필요가 없습니다. SDK에 문제가 발생하는 경우 디버그 콘솔 출력에서 볼 수 있는 메시지를 작성하고 메시지가 완료되는 경우 진단 검색에 표시됩니다.



* *REST API가 있나요?*

    예, 하지만 아직은 게시하지 않고 있습니다.

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
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=Oct15_HO4-->