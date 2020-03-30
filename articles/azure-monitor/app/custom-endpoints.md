---
title: Azure 응용 프로그램 인사이트 기본 SDK 끝점을 재정의
description: Azure 정부와 같은 지역에 대한 기본 Azure 모니터 응용 프로그램 인사이트 SDK 끝점을 수정합니다.
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: b4ab05c7ee815b385ffb2d1ff9e621063d744dd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298330"
---
# <a name="application-insights-overriding-default-endpoints"></a>기본 끝점을 재정의하는 애플리케이션 인사이트

응용 프로그램 인사이트에서 특정 지역으로 데이터를 보내려면 기본 끝점 주소를 재정의해야 합니다. 각 SDK에는 약간 다른 수정이 필요하며 이 문서에서 설명하는 모든 수정 사항이 있습니다. 이러한 변경 을 변경하려면 샘플 코드를 조정하고 `QuickPulse_Endpoint_Address`에 `TelemetryChannel_Endpoint_Address`대한 `Profile_Query_Endpoint_address` 자리 표시자 값을 대체하고 특정 지역의 실제 끝점 주소로 바꿔야 합니다. 이 문서의 끝에는 이 구성이 필요한 지역의 끝점 주소에 대한 링크가 포함되어 있습니다.

## <a name="sdk-code-changes"></a>SDK 코드 변경

### <a name="net-with-applicationinsightsconfig"></a>.NET with 어플리케이션 인사이트.config

> [!NOTE]
> applicationinsights.config 파일은 SDK 업그레이드가 수행될 때마다 자동으로 덮어씁니까. SDK 업그레이드를 수행한 후에는 영역별 끝점 값을 다시 입력해야 합니다.

```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>Custom_QuickPulse_Endpoint_Address</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>TelemetryChannel_Endpoint_Address</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>Profile_Query_Endpoint_address</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

### <a name="aspnet-core"></a>ASP.NET Core

프로젝트의 appsettings.json 파일을 다음과 같이 수정하여 기본 끝점을 조정합니다.

```json
"ApplicationInsights": {
    "InstrumentationKey": "instrumentationkey",
    "TelemetryChannel": {
      "EndpointAddress": "TelemetryChannel_Endpoint_Address"
    }
  }
```

라이브 메트릭 및 프로필 쿼리 끝점의 값은 코드를 통해서만 설정할 수 있습니다. 코드를 통해 모든 끝점 값의 기본값을 재정의하려면 `ConfigureServices` `Startup.cs` 파일 메서드를 다음과 같은 변경 사항으로 변경합니다.

```csharp
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse; //Place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="QuickPulse_Endpoint_Address");

   services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "Profile_Query_Endpoint_address" });

   services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "TelemetryChannel_Endpoint_Address" });

    //Place in the ConfigureServices method. Place this before services.AddApplicationInsightsTelemetry("instrumentation key"); if it's present
```

### <a name="azure-functions-v2x"></a>Azure 함수 v2.x

함수 프로젝트에 다음 패키지를 설치합니다.

- 마이크로소프트.애플리케이션인사이트 버전 2.10.0
- 마이크로소프트.애플리케이션인사이트.퍼프카운터컬렉터 버전 2.10.0
- 마이크로소프트.애플리케이션인사이트.윈도우서버.텔레메트리채널 버전 2.10.0

그런 다음 함수 응용 프로그램에 대한 시작 코드를 추가(또는 수정)합니다.

```csharp
[assembly: WebJobsStartup(typeof(Example.Startup))]
namespace Example
{
  class Startup : FunctionsStartup
  {
      public override void Configure(IWebJobsBuilder builder)
      {
          var quickPulseFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryModule) && 
                                               sd.ImplementationType == typeof(QuickPulseTelemetryModule));
          if (quickPulseFactory != null)
          {
              builder.Services.Remove(quickPulseFactory);
          }

          var appIdFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(IApplicationIdProvider));
          if (appIdFactory != null)
          {
              builder.Services.Remove(appIdFactory);
          }

          var channelFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryChannel));
          if (channelFactory != null)
          {
              builder.Services.Remove(channelFactory);
          }

          builder.Services.AddSingleton<ITelemetryModule, QuickPulseTelemetryModule>(_ =>
              new QuickPulseTelemetryModule
              {
                  QuickPulseServiceEndpoint = "QuickPulse_Endpoint_Address"
              });

          builder.Services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "Profile_Query_Endpoint_address" });

          builder.Services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "TelemetryChannel_Endpoint_Address" });
      }
  }
}
```

### <a name="java"></a>Java

기본 끝점 주소를 변경하려면 applicationinsights.xml 파일을 수정합니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>ffffeeee-dddd-cccc-bbbb-aaaa99998888</InstrumentationKey>
  <TelemetryModules>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
  </TelemetryModules>
  <TelemetryInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
  </TelemetryInitializers>
  <!--Add the following Channel value to modify the Endpoint address-->
  <Channel type="com.microsoft.applicationinsights.channel.concrete.inprocess.InProcessTelemetryChannel">
  <EndpointAddress>TelemetryChannel_Endpoint_Address</EndpointAddress>
  </Channel>
</ApplicationInsights>
```

### <a name="spring-boot"></a>Spring Boot

파일을 `application.properties` 수정하고 다음을 추가합니다.

```yaml
azure.application-insights.channel.in-process.endpoint-address= TelemetryChannel_Endpoint_Address
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY');
appInsights.defaultClient.config.endpointUrl = "TelemetryChannel_Endpoint_Address"; // ingestion
appInsights.defaultClient.config.profileQueryEndpoint = "Profile_Query_Endpoint_address"; // appid/profile lookup
appInsights.defaultClient.config.quickPulseHost = "QuickPulse_Endpoint_Address"; //live metrics
appInsights.Configuration.start();
```

끝점은 환경 변수를 통해 구성할 수도 있습니다.

```
Instrumentation Key: "APPINSIGHTS_INSTRUMENTATIONKEY"
Profile Endpoint: "Profile_Query_Endpoint_address"
Live Metrics Endpoint: "QuickPulse_Endpoint_Address"
```

### <a name="javascript"></a>JavaScript

```javascript
<script type="text/javascript">
    var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
    {
      instrumentationKey:"INSTRUMENTATION_KEY",
      endpointUrl: "TelemetryChannel_Endpoint_Address"
    }
    );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="python"></a>Python

opencensus-파이썬 SDK에 대한 섭취 끝점을 수정하는 방법에 대한 지침은 [opencensus-파이썬 리포지토리를 참조하십시오.](https://github.com/census-instrumentation/opencensus-python/blob/af284a92b80bcbaf5db53e7e0813f96691b4c696/contrib/opencensus-ext-azure/opencensus/ext/azure/common/__init__.py)

## <a name="regions-that-require-endpoint-modification"></a>끝점 수정이 필요한 영역

현재 끝점 수정이 필요한 유일한 지역은 [Azure 정부](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights) 및 [Azure China입니다.](https://docs.microsoft.com/azure/china/resources-developer-guide)

|지역 |  엔드포인트 이름 | 값 |
|-----------------|:------------|:-------------|
| Azure 중국 | 원격 분석 채널 | `https://dc.applicationinsights.azure.cn/v2/track` |
| Azure 중국 | 퀵펄스(라이브 메트릭) |`https://live.applicationinsights.azure.cn/QuickPulseService.svc` |
| Azure 중국 | 프로필 쿼리 |`https://dc.applicationinsights.azure.cn/api/profiles/{0}/appId`  |
| Azure Government | 원격 분석 채널 |`https://dc.applicationinsights.us/v2/track` |
| Azure Government | 퀵펄스(라이브 메트릭) |`https://quickpulse.applicationinsights.us/QuickPulseService.svc` |
| Azure Government | 프로필 쿼리 |`https://dc.applicationinsights.us/api/profiles/{0}/appId` |

현재 'api.applicationinsights.io'을 통해 일반적으로 액세스되는 [응용 프로그램 인사이트 REST API를](https://dev.applicationinsights.io/
) 사용하는 경우 해당 지역에 로컬인 끝점을 사용해야 합니다.

|지역 |  엔드포인트 이름 | 값 |
|-----------------|:------------|:-------------|
| Azure 중국 | REST API | `api.applicationinsights.azure.cn` |
| Azure Government | REST API | `api.applicationinsights.us`|

> [!NOTE]
> Azure App 서비스에 대한 코드 없는 에이전트/확장 기반 모니터링은 현재 이러한 리전에서 **지원되지 않습니다.** 이 기능을 사용할 수 있게 되 면 이 문서는 업데이트됩니다.

## <a name="next-steps"></a>다음 단계

- Azure 정부에 대한 사용자 지정 수정에 대해 자세히 알아보려면 [Azure 모니터링 및 관리에](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights)대한 자세한 지침을 참조하십시오.
- Azure 중국에 대해 자세히 알아보려면 [Azure 중국 플레이북을](https://docs.microsoft.com/azure/china/)참조하십시오.
