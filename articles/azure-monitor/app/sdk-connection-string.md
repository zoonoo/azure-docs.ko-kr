---
title: Azure 응용 프로그램 인사이트 내 연결 문자열 | 마이크로 소프트 문서
description: 연결 문자열을 사용하는 방법.
ms.topic: conceptual
author: timothymothra
ms.author: tilee
ms.date: 01/17/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 7b049c04913d3415074f46b9d90ec34be874a2da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136709"
---
# <a name="connection-strings"></a>연결 문자열

## <a name="overview"></a>개요

연결 문자열은 응용 프로그램 인사이트 사용자에게 단일 구성 설정을 제공하므로 여러 프록시 설정이 필요하지 않습니다. 모니터링 서비스로 데이터를 전송하려는 인트라넷 웹 서버, 주권 또는 하이브리드 클라우드 환경에 매우 유용합니다.

키 값 쌍은 사용자가 각 AI(AI) 서비스/제품에 대한 접두사 접미사 조합을 쉽게 정의할 수 있는 방법을 제공합니다.

> [!IMPORTANT]
> 연결 문자열과 계측 키를 모두 설정하지 않는 것이 좋습니다. 사용자가 둘 다 설정하는 경우 마지막으로 설정된 것이 우선입니다. 


## <a name="scenario-overview"></a>시나리오 개요 

가장 큰 영향을 미치는 이를 시각화하는 고객 시나리오:

- 방화벽 예외 또는 프록시 리디렉션 

    인트라넷 웹 서버에 대한 모니터링이 필요한 경우 이전 솔루션에서 고객에게 구성에 개별 서비스 끝점을 추가하도록 요청했습니다. 자세한 내용은 [여기](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#can-i-monitor-an-intranet-web-server)를 참조하세요. 
    연결 문자열은 이러한 노력을 단일 설정으로 줄여 더 나은 대안을 제공합니다. 간단한 접두사, 접미사 개정은 올바른 서비스에 대한 모든 끝점의 자동 채우기 및 리디렉션을 허용합니다. 

- 주권 또는 하이브리드 클라우드 환경

    사용자는 정의된 [Azure 정부 리전으로](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights)데이터를 보낼 수 있습니다.
    연결 문자열을 사용하면 인트라넷 서버 또는 하이브리드 클라우드 설정에 대한 끝점 설정을 정의할 수 있습니다. 

## <a name="getting-started"></a>시작

### <a name="finding-my-connection-string"></a>연결 문자열을 찾으시나요?

연결 문자열은 응용 프로그램 인사이트 리소스의 개요 블레이드에 표시됩니다.

![개요 블레이드의 연결 문자열](media/overview-dashboard/overview-connection-string.png)

### <a name="schema"></a>스키마

#### <a name="max-length"></a>최대 길이

연결의 최대 지원 되는 길이 4096 문자입니다.

#### <a name="key-value-pairs"></a>키-값 쌍

연결 문자열은 세미콜론으로 구분된 키-값 쌍으로 표시되는 설정 목록으로 구성됩니다.`key1=value1;key2=value2;key3=value3`

#### <a name="syntax"></a>구문

- `InstrumentationKey`(예 : 00000000-0000-0000-0000-00000-00000000000000000000)  연결 문자열은 **필수** 필드입니다.
- `Authorization`(예: ikey) (이 설정은 선택 사항이므로 오늘은 ikey 권한 부여만 지원합니다.)
- `EndpointSuffix`(예: applicationinsights.azure.cn) 끝점 접미사를 설정하면 Azure 클라우드에 연결할 SDK가 지시됩니다. SDK는 개별 서비스에 대한 끝점의 나머지 부분을 조합합니다.
- 명시적 끝점.
  모든 서비스는 연결 문자열에서 명시적으로 재정의할 수 있습니다.
   - `IngestionEndpoint`(예:https://dc.applicationinsights.azure.com)
   - `LiveEndpoint`(예:https://live.applicationinsights.azure.com)
   - `ProfilerEndpoint`(예:https://profiler.applicationinsights.azure.com)
   - `SnapshotEndpoint`(예:https://snapshot.applicationinsights.azure.com)

#### <a name="endpoint-schema"></a>끝점 스키마

`<prefix>.<suffix>`
- 접두사: 서비스를 정의합니다. 
- 접미사: 공통 도메인 이름을 정의합니다.

##### <a name="valid-suffixes"></a>유효한 접미사

유효한 접미사 목록은 다음과 같습니다. 
- applicationinsights.azure.cn
- applicationinsights.us


https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification도 참조하세요.


##### <a name="valid-prefixes"></a>유효한 접두사

- [원격 분석 사용 :](./app-insights-overview.md)`dc`
- [라이브 메트릭](./live-stream.md):`live`
- [프로파일러](./profiler-overview.md):`profiler`
- [스냅샷](./snapshot-debugger.md):`snapshot`



## <a name="connection-string-examples"></a>연결 문자열 예제


### <a name="minimal-valid-connection-string"></a>최소 유효한 연결 문자열

`InstrumentationKey=00000000-0000-0000-0000-000000000000;`

이 예제에서는 계측 키만 설정되었습니다.

- 권한 부여 체계기본값은 "ikey"로 설정됩니다. 
- 계측 키: 00000000-0000-0000-0000-0000-0000000000000000
- 지역 서비스 URI는 [SDK 기본값을](https://github.com/microsoft/ApplicationInsights-dotnet/blob/e50d569cebf485e72e98f4a08a0bc0e30cdf42bc/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/Endpoints/Constants.cs#L6) 기반으로 하며 공용 전역 Azure에 연결됩니다.
   - 섭취:https://dc.services.visualstudio.com/
   - 실시간 측정항목:https://rt.services.visualstudio.com/
   - 프로파일러:https://agent.azureserviceprofiler.net/
   - 디버거:https://agent.azureserviceprofiler.net/  



### <a name="connection-string-with-endpoint-suffix"></a>끝점 접미사가 있는 연결 문자열

`InstrumentationKey=00000000-0000-0000-0000-000000000000;EndpointSuffix=ai.contoso.com;`

이 예제에서 이 연결 문자열은 끝점 접미사를 지정하고 SDK는 서비스 끝점을 생성합니다.

- 권한 부여 체계기본값은 "ikey"로 설정됩니다. 
- 계측 키: 00000000-0000-0000-0000-0000-0000000000000000
- 지역 서비스 URI는 제공된 끝점 접미사를 기반으로 합니다. 
   - 섭취:https://dc.ai.contoso.com
   - 실시간 측정항목:https://live.ai.contoso.com
   - 프로파일러:https://profiler.ai.contoso.com 
   - 디버거:https://snapshot.ai.contoso.com   



### <a name="connection-string-with-explicit-endpoint-overrides"></a>명시적 끝점 재정의가 있는 연결 문자열 

`InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://custom.com:111/;LiveEndpoint=https://custom.com:222/;ProfilerEndpoint=https://custom.com:333/;SnapshotEndpoint=https://custom.com:444/;`

이 예제에서 이 연결 문자열은 모든 서비스에 대해 명시적 재정의를 지정합니다. SDK는 수정 없이 제공된 정확한 끝점을 사용합니다.

- 권한 부여 체계기본값은 "ikey"로 설정됩니다. 
- 계측 키: 00000000-0000-0000-0000-0000-0000000000000000
- 지역 서비스 URI는 명시적 재정의 값을 기반으로 합니다. 
   - 섭취: https:\//custom.com:111/
   - 실시간 지표: https:\//custom.com:222/
   - 프로파일러: https:\//custom.com:333/ 
   - 디버거: https:\//custom.com:444/   


## <a name="how-to-set-a-connection-string"></a>연결 문자열을 설정하는 방법

연결 문자열은 다음 SDK 버전에서 지원됩니다.
- .NET 및 .NET 코어 v2.12.0
- 자바 v2.5.1
- 자바 스크립트 v2.3.0
- 노드JS v1.5.0
- 파이썬 v1.0.0

연결 문자열은 코드, 환경 변수 또는 구성 파일에서 설정할 수 있습니다.



### <a name="environment-variable"></a>환경 변수

- 연결 문자열: `APPLICATIONINSIGHTS_CONNECTION_STRING`

### <a name="net-sdk-example"></a>.Net SDK 예제

원격 분석 구성.연결 문자열:https://github.com/microsoft/ApplicationInsights-dotnet/blob/add45ceed35a817dc7202ec07d3df1672d1f610d/BASE/src/Microsoft.ApplicationInsights/Extensibility/TelemetryConfiguration.cs#L271-L274

.Net 명시적으로 설정:
```csharp
var configuration = new TelemetryConfiguration
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
};
```

.Net 구성 파일:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000</ConnectionString>
</ApplicationInsights>
```


넷코어 config.json: 

```json
{
  "ApplicationInsights": {
    "ConnectionString" : "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
    }
  }
```


### <a name="java-sdk-example"></a>자바 SDK 예제


자바 명시적으로 설정 :
```java
TelemetryConfiguration.getActive().setConnectionString("InstrumentationKey=00000000-0000-0000-0000-000000000000");
```

ApplicationInsights.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000;</ConnectionString>
</ApplicationInsights>
```

### <a name="javascript-sdk-example"></a>자바 스크립트 SDK 예제

중요: 자바스크립트는 환경 변수의 사용을 지원하지 않습니다.

스니펫 사용:

```javascript
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("addTelemetryInitializer"),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),t.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
{
  connectionString:"InstrumentationKey=00000000-0000-0000-0000-000000000000;"
}
);window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```


수동 설정:
```javascript
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'InstrumentationKey=00000000-0000-0000-0000-000000000000;'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
```

### <a name="node-sdk-example"></a>노드 SDK 예제

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;");
appInsights.start();
```

### <a name="python-sdk-example"></a>파이썬 SDK 예제

사용자가 환경 변수를 설정하는 것이 좋습니다.

연결 문자열을 명시적으로 설정하려면 다음을 수행하십시오.

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'), sampler=ProbabilitySampler(1.0))
```


## <a name="next-steps"></a>다음 단계

다음을 사용하여 런타임에 시작하세요.

* [Azure VM 및 Azure Virtual Machine Scale Set IIS 호스트 앱](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [IIS 서버](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure Web Apps](../../azure-monitor/app/azure-web-apps.md)

다음을 사용하여 개발 시에 시작하세요.

* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [Node.js](../../azure-monitor/app/nodejs.md)
* [Python](../../azure-monitor/app/opencensus-python.md)
