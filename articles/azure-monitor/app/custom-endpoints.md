---
title: Azure 애플리케이션 Insights 기본 SDK 끝점 재정의
description: Azure Government 같은 영역에 대 한 기본 Azure Monitor Application Insights SDK 끝점을 수정 합니다.
ms.topic: conceptual
ms.date: 07/26/2019
ms.custom: references_regions, devx-track-js
ms.openlocfilehash: 13470017281ecfa616715777e78e446e71a08b0e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102489806"
---
# <a name="application-insights-overriding-default-endpoints"></a>기본 끝점 재정의 Application Insights

Application Insights에서 특정 지역으로 데이터를 보내려면 기본 끝점 주소를 재정의 해야 합니다. 각 SDK에는 약간 다른 수정이 필요 하며,이에 대해서는이 문서에 설명 되어 있습니다. 이러한 변경을 수행 하려면 샘플 코드를 조정 하 고, 및에 대 한 자리 표시자 값을 `QuickPulse_Endpoint_Address` `TelemetryChannel_Endpoint_Address` `Profile_Query_Endpoint_address` 특정 지역의 실제 끝점 주소로 바꾸어야 합니다. 이 문서의 끝에는이 구성이 필요한 지역의 끝점 주소에 대 한 링크가 포함 되어 있습니다.

> [!NOTE]
> [연결 문자열](./sdk-connection-string.md?tabs=net) 은 Application Insights 내에서 사용자 지정 끝점을 설정 하는 새로운 기본 방법입니다.

---

## <a name="sdk-code-changes"></a>SDK 코드 변경

# <a name="net"></a>[.NET](#tab/net)

> [!NOTE]
> SDK 업그레이드가 수행 될 때마다 applicationinsights.config 파일을 자동으로 덮어씁니다. SDK 업그레이드를 수행한 후에는 지역별 끝점 값을 다시 입력 해야 합니다.

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

# <a name="net-core"></a>[.NET Core](#tab/netcore)

다음과 같이 프로젝트에서 파일의 appsettings.js를 수정 하 여 주 끝점을 조정 합니다.

```json
"ApplicationInsights": {
    "InstrumentationKey": "instrumentationkey",
    "TelemetryChannel": {
      "EndpointAddress": "TelemetryChannel_Endpoint_Address"
    }
  }
```

라이브 메트릭 및 프로필 쿼리 끝점에 대 한 값은 코드를 통해서만 설정할 수 있습니다. 코드를 통해 모든 끝점 값의 기본값을 재정의 하려면 `ConfigureServices` 파일의 메서드에서 다음과 같이 변경 합니다 `Startup.cs` .

```csharp
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse; //Place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="QuickPulse_Endpoint_Address");

   services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "Profile_Query_Endpoint_address" });

   services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "TelemetryChannel_Endpoint_Address" });

    //Place in the ConfigureServices method. Place this before services.AddApplicationInsightsTelemetry("instrumentation key"); if it's present
```

# <a name="azure-functions"></a>[Azure Functions](#tab/functions)

Azure Functions 이제 함수의 응용 프로그램 설정에 설정 된 [연결 문자열](./sdk-connection-string.md?tabs=net) 을 사용 하는 것이 좋습니다. 함수 창 내에서 함수에 대 한 응용 프로그램 설정에 액세스 하려면 **설정**  >  **구성**  >  **응용 프로그램 설정** 을 선택 합니다. 

이름: `APPLICATIONINSIGHTS_CONNECTION_STRING` 값: `Connection String Value`

# <a name="java"></a>[Java](#tab/java)

applicationinsights.xml 파일을 수정 하 여 기본 끝점 주소를 변경 합니다.

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

파일을 수정 `application.properties` 하 고 다음을 추가 합니다.

```yaml
azure.application-insights.channel.in-process.endpoint-address= TelemetryChannel_Endpoint_Address
```

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY');
appInsights.defaultClient.config.endpointUrl = "TelemetryChannel_Endpoint_Address"; // ingestion
appInsights.defaultClient.config.profileQueryEndpoint = "Profile_Query_Endpoint_address"; // appid/profile lookup
appInsights.defaultClient.config.quickPulseHost = "QuickPulse_Endpoint_Address"; //live metrics
appInsights.Configuration.start();
```

환경 변수를 통해 끝점을 구성할 수도 있습니다.

```
Instrumentation Key: "APPINSIGHTS_INSTRUMENTATIONKEY"
Profile Endpoint: "Profile_Query_Endpoint_address"
Live Metrics Endpoint: "QuickPulse_Endpoint_Address"
```

# <a name="javascript"></a>[JavaScript](#tab/js)

현재 코드 조각 (아래에 나열 됨)은 버전 "5"이 고, 버전은 sv: "#" 이며 [현재 버전은 GitHub 에서도 사용할 수](https://go.microsoft.com/fwlink/?linkid=2156318)있습니다.

```html
<script type="text/javascript">
!function(T,l,y){var S=T.location,k="script",D="instrumentationKey",C="ingestionendpoint",I="disableExceptionTracking",E="ai.device.",b="toLowerCase",w="crossOrigin",N="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"5",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[b](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,u,p,l;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][b]()]=i[1])}if(!e[C]){var r=e.endpointsuffix,o=r?e.location:null;e[C]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[D]||d[D]||"",u=s[C],p=u?u+"/v2/track":d.endpointUrl,(l=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=p,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),l.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,p)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:N,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(N,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(l,p))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(k);n.src=h;var e=y[w];return!e&&""!==e||"undefined"==n[w]||(n[w]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(k)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[I]&&!0!==s[I]){var c="onerror";t(["_"+c]);var u=T[c];T[c]=function(e,t,n,a,i){var r=u&&u(e,t,n,a,i);return!0!==r&&m["_"+c]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);function a(){y.onInit&&y.onInit(n)}(T[t]=n).queue&&0===n.queue.length?(n.queue.push(a),n.trackPageView({})):a()}(window,document,{
src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js", // The SDK URL Source
// name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
// ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
// useXhr: 1, // Use XHR instead of fetch to report failures (if available),
crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag
// onInit: null, // Once the application insights instance has loaded and initialized this callback function will be called with 1 argument -- the sdk instance (DO NOT ADD anything to the sdk.queue -- As they won't get called)
cfg: { // Application Insights Configuration
    instrumentationKey:"INSTRUMENTATION_KEY",
    endpointUrl: "TelemetryChannel_Endpoint_Address"
}});
</script>
```

> [!NOTE]
> 가독성을 높이고 가능한 JavaScript 오류를 줄이기 위해 가능한 모든 구성 옵션은 위의 코드 조각 코드에서 새 줄에 나열 됩니다. 주석 처리 된 줄의 값을 변경 하지 않으려는 경우 제거할 수 있습니다.

# <a name="python"></a>[Python](#tab/python)

Opencensus에 대 한 수집 끝점을 수정 하는 방법에 대 한 지침은 [opencensus 리포지토리](https://github.com/census-instrumentation/opencensus-python/blob/af284a92b80bcbaf5db53e7e0813f96691b4c696/contrib/opencensus-ext-azure/opencensus/ext/azure/common/__init__.py) 를 참조 하세요.

---

## <a name="regions-that-require-endpoint-modification"></a>끝점을 수정 해야 하는 영역

현재는 끝점을 수정 해야 하는 유일한 지역은 [Azure Government](../../azure-government/compare-azure-government-global-azure.md#application-insights) 및 [Azure 중국](/azure/china/resources-developer-guide)입니다.

|지역 |  엔드포인트 이름 | 값 |
|-----------------|:------------|:-------------|
| Azure 중국 | 원격 분석 채널 | `https://dc.applicationinsights.azure.cn/v2/track` |
| Azure 중국 | QuickPulse (라이브 메트릭) |`https://live.applicationinsights.azure.cn/QuickPulseService.svc` |
| Azure 중국 | 프로필 쿼리 |`https://dc.applicationinsights.azure.cn/api/profiles/{0}/appId`  |
| Azure Government | 원격 분석 채널 |`https://dc.applicationinsights.us/v2/track` |
| Azure Government | QuickPulse (라이브 메트릭) |`https://quickpulse.applicationinsights.us/QuickPulseService.svc` |
| Azure Government | 프로필 쿼리 |`https://dc.applicationinsights.us/api/profiles/{0}/appId` |

현재 ' api.applicationinsights.io '를 통해 액세스 되는 [Application Insights REST API](https://dev.applicationinsights.io/
) 를 사용 하는 경우 지역에 로컬인 끝점을 사용 해야 합니다.

|지역 |  엔드포인트 이름 | 값 |
|-----------------|:------------|:-------------|
| Azure 중국 | REST API | `api.applicationinsights.azure.cn` |
| Azure Government | REST API | `api.applicationinsights.us`|

> [!NOTE]
> Azure 앱 Services에 대 한 코드 없는 agent/extension 기반 모니터링은 현재 이러한 지역에서 **지원 되지** 않습니다. 이 기능을 사용할 수 있게 되 면이 문서가 업데이트 됩니다.

## <a name="next-steps"></a>다음 단계

- Azure Government에 대 한 사용자 지정 수정 사항에 대해 자세히 알아보려면 [Azure 모니터링 및 관리](../../azure-government/compare-azure-government-global-azure.md#application-insights)에 대 한 자세한 지침을 참조 하세요.
- Azure 중국에 대해 자세히 알아보려면 [Azure 중국 플레이 북](/azure/china/)를 참조 하세요.
