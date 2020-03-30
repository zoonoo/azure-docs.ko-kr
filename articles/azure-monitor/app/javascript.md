---
title: 자바 스크립트 웹 앱에 대한 Azure 응용 프로그램 인사이트
description: 페이지 보기 및 세션 수, 웹 클라이언트 데이터, 단일 페이지 응용 프로그램(SPA) 및 사용 패턴을 추적합니다. JavaScript 웹 페이지의 예외 및 성능 문제를 감지합니다.
ms.topic: conceptual
author: Dawgfan
ms.author: mmcc
ms.date: 09/20/2019
ms.openlocfilehash: 5414a70180a82be8253dace7d800c90c1ae6a9bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276076"
---
# <a name="application-insights-for-web-pages"></a>웹 페이지용 Application Insights

웹 페이지 또는 앱의 성능 및 사용량 현황에 대해 알아봅니다. 페이지 스크립트에 [Application Insights를](app-insights-overview.md) 추가하면 페이지 로드 및 AJAX 호출, 브라우저 예외 및 AJAX 오류, 사용자 및 세션 수에 대한 세부 정보를 확인할 수 있습니다. 이러한 모든 요소를 페이지, 클라이언트 OS 및 브라우저 버전, 지리적 위치 및 기타 차원으로 분할할 수 있습니다. 실패 횟수 또는 느린 페이지 로딩에 대한 경고를 설정할 수도 있습니다. 또한 JavaScript 코드에 추적 호출을 삽입하여 웹 페이지 애플리케이션의 다양한 기능 사용 방법을 추적할 수 있습니다.

Application Insights는 다른 웹 페이지와 함께 사용할 수 있습니다. 간단한 JavaScript만 추가하면 됩니다. 웹 서비스가 [Java](java-get-started.md) 또는 [ASP.NET](asp-net.md)경우 클라이언트 측 JavaScript SDK와 함께 서버 측 SDK를 사용하여 앱의 성능을 종단 간 이해할 수 있습니다.

## <a name="adding-the-javascript-sdk"></a>자바 스크립트 SDK 추가

1. 먼저 응용 프로그램 인사이트 리소스가 필요합니다. 리소스 및 계측 키가 아직 없는 경우 [새 리소스 만들기 지침을](create-new-resource.md)따르십시오.
2. JavaScript 원격 분석을 보낼 리소스에서 계측 키를 복사합니다.
3. 다음 두 가지 옵션 중 하나를 통해 웹 페이지 또는 앱에 응용 프로그램 인사이트 JavaScript SDK를 추가합니다.
    * [npm 설정](#npm-based-setup)
    * [자바 스크립트 스니펫](#snippet-based-setup)

> [!IMPORTANT]
> 하나의 방법만 사용하여 응용 프로그램에 JavaScript SDK를 추가합니다. NPM 설정을 사용하는 경우 스니펫을 사용하지 말고 그 반대의 경우도 마찬가지입니다.

> [!NOTE]
> NPM 설치는 프로젝트에 대한 종속성으로 자바스크립트 SDK를 설치하여 IntelliSense를 사용하도록 설정하는 반면 스니펫은 런타임에 SDK를 가져옵니다. 둘 다 동일한 기능을 지원합니다. 그러나 더 많은 사용자 지정 이벤트 및 구성을 원하는 개발자는 일반적으로 NPM 설치 를 선택 하지만 즉시 사용 가능한 웹 분석의 빠른 활성화를 찾고 있는 사용자는 Snippet을 선택 합니다.

### <a name="npm-based-setup"></a>npm 기반 설정

```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView(); // Manually call trackPageView to establish the current user/session/pageview
```

### <a name="snippet-based-setup"></a>스니펫 기반 설정

앱에서 npm을 사용하지 않는 경우 각 페이지 상단에 이 스니펫을 붙여넣음으로써 응용 프로그램 인사이트로 웹 페이지를 직접 계측할 수 있습니다. 모든 종속성에 대한 잠재적인 `<head>` 문제를 모니터링할 수 있도록 섹션의 첫 번째 스크립트여야 합니다. Blazor Server 앱을 사용하는 경우 섹션의 파일 `_Host.cshtml` 맨 위에 스니펫을 추가합니다. `<head>`

```html
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(n){var o={config:n,initialize:!0},t=document,e=window,i="script";setTimeout(function(){var e=t.createElement(i);e.src=n.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",t.getElementsByTagName(i)[0].parentNode.appendChild(e)});try{o.cookie=t.cookie}catch(e){}function a(n){o[n]=function(){var e=arguments;o.queue.push(function(){o[n].apply(o,e)})}}o.queue=[],o.version=2;for(var s=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];s.length;)a("track"+s.pop());var r="Track",c=r+"Page";a("start"+c),a("stop"+c);var u=r+"Event";if(a("start"+u),a("stop"+u),a("addTelemetryInitializer"),a("setAuthenticatedUserContext"),a("clearAuthenticatedUserContext"),a("flush"),o.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===n.disableExceptionTracking||n.extensionConfig&&n.extensionConfig.ApplicationInsightsAnalytics&&!0===n.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){a("_"+(s="onerror"));var p=e[s];e[s]=function(e,n,t,i,a){var r=p&&p(e,n,t,i,a);return!0!==r&&o["_"+s]({message:e,url:n,lineNumber:t,columnNumber:i,error:a}),r},n.autoExceptionInstrumented=!0}return o}(
{
  instrumentationKey:"INSTRUMENTATION_KEY"
}
);(window[aiName]=aisdk).queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="sending-telemetry-to-the-azure-portal"></a>Azure 포털로 원격 분석 보내기

기본적으로 응용 프로그램 인사이트 JavaScript SDK는 응용 프로그램의 상태와 기본 사용자 환경을 결정하는 데 도움이 되는 여러 원격 분석 항목을 자동으로 수집합니다. 여기에는 다음이 포함됩니다.

- 앱에서 **catch되지 않은 예외(에** 대한 정보 포함)
    - 스택 추적
    - 오류와 함께 제공되는 예외 세부 정보 및 메시지
    - 줄 & 열 오류 수
    - 오류가 발생한 URL
- 앱 **XHR** 및 Fetch(수집을 **Fetch** 기본적으로 비활성화) 앱에서 만든 **네트워크 종속성** 요청은
    - 종속성 원본의 URL
    - 종속성을 요청하는 데 사용되는 명령 & 메서드
    - 요청 기간
    - 요청의 결과 코드 및 성공 상태
    - 요청을 하는 사용자의 ID(있는 경우)
    - 요청이 이루어지는 상관 관계 컨텍스트(있는 경우)
- **사용자** 정보(예: 위치, 네트워크, IP)
- **장치** 정보(예: 브라우저, OS, 버전, 언어, 해상도, 모델)
- **세션 정보**

### <a name="telemetry-initializers"></a>원격 분석 초기화
원격 분석 초기화자는 사용자의 브라우저에서 전송되기 전에 수집된 원격 분석의 내용을 수정하는 데 사용됩니다. 또한 반환하여 `false`특정 원격 분석이 전송되는 것을 방지하는 데 사용할 수도 있습니다. 여러 원격 분석 초기화자를 Application Insights 인스턴스에 추가할 수 있으며 추가 순서대로 실행됩니다.

입력 `addTelemetryInitializer` 인수는 인수로 a를 [`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API-reference.md#addTelemetryInitializer) 사용하여 `boolean` 또는 `void`를 반환하는 콜백입니다. 반환 `false`하는 경우 , 원격 분석 항목 전송 되지 않습니다.

원격 분석 초기화자를 사용하는 예:
```ts
var telemetryInitializer = (envelope) => {
  envelope.data.someField = 'This item passed through my telemetry initializer';
};
appInsights.addTelemetryInitializer(telemetryInitializer);
appInsights.trackTrace({message: 'This message will use a telemetry initializer'});

appInsights.addTelemetryInitializer(() => false); // Nothing is sent after this is executed
appInsights.trackTrace({message: 'this message will not be sent'}); // Not sent
```

## <a name="configuration"></a>Configuration
대부분의 구성 필드의 이름은 false로 기본값으로 지정될 수 있습니다. `instrumentationKey`을 제외한 모든 필드는 선택 사항입니다.

| 이름 | 기본값 | 설명 |
|------|---------|-------------|
| instrumentationKey | null | **필수**<br>Azure 포털에서 얻은 계측 키입니다. |
| accountId | null | 앱이 사용자를 계정으로 그룹화하는 경우 선택적 계정 ID입니다. 공백, 쉼표, 세미콜론, 같음 또는 세로 막대 없음 |
| 세션 리뉴얼 | 1800000 | 사용자가 밀리초 단위로 이 시간 동안 비활성 상태인 경우 세션이 기록됩니다. 기본값은 30분입니다. |
| 세션 익스피어레이션 | 86400000 | 세션이 밀리초 단위로 이 시간 동안 계속되면 세션이 기록됩니다. 기본값은 24시간입니다. |
| 맥스배치크기인바이트 | 10000 | 원격 분석 일괄 처리의 최대 크기입니다. 일괄 처리가 이 제한을 초과하면 즉시 전송되고 새 일괄 처리가 시작됩니다. |
| 최대 배치 간격 | 15000 | 전송 하기 전에 대 한 원격 분석을 일괄 처리 하는 시간 (밀리 초) |
| 사용 안 함예외 추적 | false | true이면 예외가 자동으로 수집되지 않습니다. 기본값은 false입니다. |
| 텔레메타이 사용 안 함 | false | true이면 원격 분석이 수집되지 않거나 전송되지 않습니다. 기본값은 false입니다. |
| 사용 디버그 | false | true이면 **internal** SDK 로깅 설정에 관계없이 내부 디버깅 데이터가 기록되지 **않고** 예외로 throw됩니다. 기본값은 false입니다. <br>***참고:*** 이 설정을 사용하면 내부 오류가 발생할 때마다 원격 분석이 삭제됩니다. 이 기능은 SDK의 구성 또는 사용과 관련된 문제를 신속하게 식별하는 데 유용할 수 있습니다. 디버깅하는 동안 원격 분석을 잃고 싶지 않다면 `consoleLoggingLevel` `enableDebug`을 `telemetryLoggingLevel` 사용하거나 대신 사용하는 것이 좋습니다. |
| 로깅레벨콘솔 | 0 | **내부** 응용 프로그램 인사이트 오류를 콘솔에 기록합니다. <br>0: 꺼져, <br>1: 중요한 오류만, <br>2 : 모든 것 (오류 & 경고) |
| 로깅레벨 텔레메트리 | 1 | **내부** 응용 프로그램 인사이트 오류를 원격 분석으로 보냅니다. <br>0: 꺼져, <br>1: 중요한 오류만, <br>2 : 모든 것 (오류 & 경고) |
| 진단로그간격 | 10000 | (내부) 내부 로깅 큐에 대한 폴링 간격(ms) |
| 샘플링백분율 | 100 | 전송될 이벤트의 백분율입니다. 기본값은 100이며 모든 이벤트가 전송됩니다. 대규모 응용 프로그램의 데이터 한도를 유지하려는 경우 이 설정을 설정합니다. |
| 자동 트랙페이지방문시간 | false | true이면 페이지뷰에서 이전 계측된 페이지의 보기 시간이 추적되어 원격 분석으로 전송되고 현재 페이지뷰에 대한 새 타이머가 시작됩니다. 기본값은 false입니다. |
| 비활성화AjaxTracking | false | true이면 Ajax 호출이 자동으로 수집되지 않습니다. 기본값은 false입니다. |
| 비활성화된페치트래킹 | true | true이면 가져오기 요청이 자동으로 수집되지 않습니다. 기본값은 true입니다. |
| 재정의페이지뷰지속시간 | false | true이면 trackPageView가 호출될 때 페이지 보기 지속 시간 간격의 끝을 기록하도록 trackPageView의 기본 동작이 변경됩니다. false 및 trackPageView에 사용자 지정 기간이 제공되지 않으면 탐색 타이밍 API를 사용하여 페이지 뷰 성능이 계산됩니다. 기본값은 false입니다. |
| maxAjaxCalls퍼뷰 | 500 | 기본 500 - 페이지 보기당 모니터링되는 Ajax 호출 수를 제어합니다. 페이지에서 모든 (무제한) Ajax 호출을 모니터링하려면 -1로 설정합니다. |
| 비활성화데이터 손실분석 | true | false인 경우 시작 시 아직 전송되지 않은 항목이 있는지 내부 원격 분석 보낸 사람 버퍼가 확인됩니다. |
| 비활성화상관 헤더 | false | false이면 SDK는 서버 측의 해당 요청과 상관 관계를 지정하기 위해 모든 종속성 요청에 두 개의 헤더('요청-Id' 및 '요청 컨텍스트')를 추가합니다. 기본값은 false입니다. |
| 상관 헤더제외 도메인 |  | 특정 도메인에 대한 상관 헤더 사용 안 함 |
| 상관 헤더도메인 |  | 특정 도메인에 대한 상관 헤더 사용 |
| 플러시온 전에 로드 해제 | false | 기본 거짓입니다. true이면 onBeforeUnload 이벤트 트리거가 호출될 때 플러시 메서드가 호출되지 않습니다. |
| 사용 설정세션저장저장버퍼 | true | 기본 값 true입니다. true이면 보내지 않은 모든 원격 분석이 있는 버퍼가 세션 저장소에 저장됩니다. 버퍼는 페이지 로드시 복원됩니다. |
| isCookieUse 사용 안 함 | false | 기본 거짓입니다. true이면 SDK는 쿠키의 데이터를 저장하거나 읽지 않습니다.|
| 쿠키 도메인 | null | 사용자 지정 쿠키 도메인입니다. 이 기능은 하위 도메인 간에 Application Insights 쿠키를 공유하려는 경우에 유용합니다. |
| is Retry 사용 안 함 | false | 기본 거짓입니다. false이면 206(부분 성공), 408(시간 시간 지정), 429(너무 많은 요청), 500(내부 서버 오류), 503(서비스 사용 불가) 및 0(검색된 경우에만 오프라인)에 다시 시도하십시오. |
| isStorageUse사용 안 함 | false | true이면 SDK는 로컬 및 세션 저장소의 데이터를 저장하거나 읽지 않습니다. 기본값은 false입니다. |
| isBeaconApi 사용 안 함 | true | false이면 SDK는 [비콘 API를](https://www.w3.org/TR/beacon) 사용하여 모든 원격 분석을 보냅니다. |
| 온언로드비활성화비콘 | false | 기본 거짓입니다. 탭이 닫히면 SDK는 [비콘 API를](https://www.w3.org/TR/beacon) 사용하여 남은 모든 원격 분석을 보냅니다. |
| sdk내연수익스텐션 | null | sdk 확장 이름을 설정합니다. 알파벳 문자만 허용됩니다. 확장 이름은 'ai.internal.sdkVersion' 태그(예: 'ext_javascript:2.0')의 접두사로 추가됩니다. 기본값은 null입니다. |
| is브라우저링크추적사용 | false | 기본값은 false입니다. true이면 SDK는 모든 [브라우저 링크](https://docs.microsoft.com/aspnet/core/client-side/using-browserlink) 요청을 추적합니다. |
| appId | null | AppId는 서버 측 요청과 클라이언트 측에서 발생하는 AJAX 종속성 간의 상관 관계에 사용됩니다. Beacon API를 사용하도록 설정하면 자동으로 사용할 수 없지만 구성에서 수동으로 설정할 수 있습니다. 기본값은 null입니다. |
| 인에이블코르스상관 | false | true이면 SDK는 나가는 AJAX 종속성과 서버 측의 해당 요청의 상관 관계를 상관시키는 두 개의 헤더('요청-ID' 및 '요청 컨텍스트')를 모든 CORS 요청에 추가합니다. 기본값은 false입니다. |
| 이름 사전 수정 | 정의되지 않음 | localStorage 및 쿠키 이름에 대 한 이름 후면으로 사용 되는 선택적 값입니다.
| 사용 자자동 경로 추적 | false | 단일 페이지 응용 프로그램(SPA)의 경로 변경 내용을 자동으로 추적합니다. true이면 각 경로 변경이 응용 프로그램 인사이트에 새 페이지뷰를 전송합니다. 해시 경로 변경`example.com/foo#bar`() 도 새 페이지 보기로 기록됩니다.
| 사용 요청 헤더 추적 | false | true이면 AJAX & 요청 헤더가 추적되고 기본값은 false입니다.
| 사용 자응답 헤더 추적 | false | true이면 AJAX & Fetch 요청의 응답 헤더가 추적되고 기본값은 false입니다.
| 분산T레이싱 모드 | `DistributedTracingModes.AI` | 분산 추적 모드를 설정합니다. AI_AND_W3C 모드 또는 W3C 모드가 설정된 경우 W3C 추적 컨텍스트 헤더(추적 부모/추적 상태)가 생성되어 모든 발신 요청에 포함됩니다. AI_AND_W3C 레거시 애플리케이션 인사이트 계측 서비스와의 백 호환성을 위해 제공됩니다.

## <a name="single-page-applications"></a>단일 페이지 응용 프로그램

기본적으로 이 SDK는 단일 페이지 응용 프로그램에서 발생하는 상태 기반 경로 변경을 **처리하지 않습니다.** 단일 페이지 응용 프로그램에 대한 자동 경로 변경 `enableAutoRouteTracking: true` 추적을 활성화하려면 설정 구성에 추가할 수 있습니다.

현재, 우리는 당신이 SDK로 초기화 할 수있는 별도의 [React 플러그인을](#react-extensions) 제공합니다. 또한 경로 변경 추적을 수행할 뿐만 아니라 [다른 React 특정 원격 분석을](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)수집합니다.

## <a name="react-extensions"></a>확장 반응

| 확장 |
|---------------|
| [React](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)|
| [React Native](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-native/README.md)|

## <a name="explore-browserclient-side-data"></a>브라우저/클라이언트 측 데이터 탐색

브라우저/클라이언트 측 데이터는 **메트릭으로** 이동하여 관심 있는 개별 메트릭을 추가하여 볼 수 있습니다.

![](./media/javascript/page-view-load-time.png)

포털의 브라우저 환경을 통해 JavaScript SDK에서 데이터를 볼 수도 있습니다.

**브라우저를** 선택한 다음 **실패** 또는 **성능**입니다.

![](./media/javascript/browser.png)

### <a name="performance"></a>성능

![](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>종속성

![](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>분석

JavaScript SDK에서 수집한 원격 분석을 쿼리하려면 **로그 보기(분석) 버튼을 선택합니다.** `where` 의 문을 `client_Type == "Browser"`추가하면 JavaScript SDK의 데이터만 표시되며 다른 SDK에서 수집한 서버 측 원격 분석은 제외됩니다.
 
```kusto
// average pageView duration by name
let timeGrain=5m;
let dataset=pageViews
// additional filters can be applied here
| where timestamp > ago(1d)
| where client_Type == "Browser" ;
// calculate average pageView duration for all pageViews
dataset
| summarize avg(duration) by bin(timestamp, timeGrain)
| extend pageView='Overall'
// render result in a chart
| render timechart
```

### <a name="source-map-support"></a>소스 맵 지원

예외 원격 분석의 minified 호출 스택Azure 포털에서 minified 수 있습니다. 예외 세부 정보 패널의 모든 기존 통합은 새로 처리되지 않은 호출 스택에서 작동합니다.

#### <a name="link-to-blob-storage-account"></a>Blob 저장소 계정에 대한 링크

응용 프로그램 인사이트 리소스를 자체 Azure Blob Storage 컨테이너에 연결하여 호출 스택을 자동으로 축소할 수 있습니다. 시작하려면 자동 [소스 맵 지원을](./source-map-support.md)참조하십시오.

### <a name="drag-and-drop"></a>끌어서 놓기

1. Azure 포털에서 예외 원격 분석 항목을 선택하여 "종단 간 트랜잭션 세부 정보"를 봅니다.
2. 이 호출 스택에 해당하는 소스 맵을 식별합니다. 소스 맵은 스택 프레임의 소스 파일과 일치해야 하지만`.map`
3. Azure 포털의 호출 스택에 소스 맵을 드래그 앤 드롭![](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>애플리케이션 인사이트 웹 기본

간단한 환경을 위해 기본 버전의 응용 프로그램 인사이트를 설치할 수 있습니다.
```
npm i --save @microsoft/applicationinsights-web-basic
```
이 버전은 최소한의 기능과 기능과 함께 제공되며 적합하다고 판단되면 빌드할 수 있습니다. 예를 들어 자동 수집(catch되지 않은 예외, AJAX 등)을 수행하지 않습니다. . `trackTrace` `trackException`등과 같은 특정 원격 분석 유형을 보낼 API는 이 버전에 포함되지 않으므로 고유한 래퍼를 제공해야 합니다. 사용할 수 있는 유일한 `track`API는 . [샘플이](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html) 여기에 있습니다.

## <a name="examples"></a>예

실행 가능한 예제는 [응용 프로그램 인사이트 자바스크립트 SDK 샘플을 참조하세요.](https://github.com/topics/applicationinsights-js-demo)

## <a name="upgrading-from-the-old-version-of-application-insights"></a>이전 버전의 응용 프로그램 인사이트 업그레이드

SDK V2 버전의 주요 변경 사항:
- 더 나은 API 서명을 허용하기 위해 trackPageView 및 trackException과 같은 일부 API 호출이 업데이트되었습니다. Internet Explorer 8 및 이전 버전의 브라우저에서 실행 중이면 지원되지 않습니다.
- 원격 분석 봉투에는 데이터 스키마 업데이트로 인한 필드 이름 및 구조 변경 사항이 있습니다.
- 로 `context.operation` `context.telemetryTrace`이동했습니다. 일부 필드도 변경되었습니다().`operation.id` --> `telemetryTrace.traceID`
  - 현재 페이지뷰 ID(예: SPA 앱)를 수동으로 `appInsights.properties.context.telemetryTrace.traceID = Util.generateW3CId()`새로 고치려면 을 사용합니다.
    > [!NOTE]
    > 추적 ID를 고유하게 유지하려면 `Util.newId()`이전에 사용한 `Util.generateW3CId()`위치가 이제 을 사용합니다. 둘 다 궁극적으로 작업 ID가 됩니다.

현재 응용 프로그램 인사이트 PRODUCTION SDK(1.0.20)를 사용하고 있고 새 SDK가 런타임에서 작동하는지 확인하려면 현재 SDK 로드 시나리오에 따라 URL을 업데이트합니다.

- CDN 시나리오를 통해 다운로드: 현재 다음 URL을 가리키기 위해 사용하는 코드 조각을 업데이트합니다.
   ```
   "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
   ```

- npm 시나리오: `downloadAndSetup` CDN에서 전체 ApplicationInsights 스크립트를 다운로드하고 계측 키로 초기화하려면 호출합니다.

   ```ts
   appInsights.downloadAndSetup({
     instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
     url: "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
     });
   ```

내부 환경에서 테스트하여 모니터링 원격 분석이 예상대로 작동하는지 확인합니다. 모든 것이 작동하는 경우 API 서명을 SDK V2 버전으로 적절하게 업데이트하고 프로덕션 환경에 배포합니다.

## <a name="sdk-performanceoverhead"></a>SDK 성능/오버헤드

단지 25 KB gzipped에서, 그리고 만 ~ 15 ms를 복용 초기화, 응용 프로그램 인사이트는 웹 사이트에 로드 시간의 무시할 금액을 추가합니다. 스니펫을 사용하면 라이브러리의 최소 구성 요소가 빠르게 로드됩니다. 그 동안 전체 스크립트가 백그라운드에서 다운로드됩니다.

스크립트가 CDN에서 다운로드되는 동안 페이지의 모든 추적이 큐에 대기됩니다. 다운로드한 스크립트가 비동기적으로 초기화를 완료하면 큐에 대기된 모든 이벤트가 추적됩니다. 따라서 페이지의 전체 수명 주기 동안 원격 분석이 손실되지 않습니다. 이 설정 프로세스는 사용자에게 보이지 않는 원활한 분석 시스템을 페이지에 제공합니다.

> 요약:
> - **25 KB** 지퍼
> - **15 ms** 전체 초기화 시간
> - 페이지의 수명 주기 동안 누락된 **추적 제로**

## <a name="browser-support"></a>브라우저 지원

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Opera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
크롬 최신 ✔ |  파이어 폭스 최신 ✔ | IE 9+ & 에지 ✔ | 오페라 최신 ✔ | 사파리 최신 ✔ |

## <a name="open-source-sdk"></a>오픈 소스 SDK

응용 프로그램 인사이트 자바 스크립트 SDK는 소스 코드를 보거나 프로젝트에 기여하기 위해 [공식 GitHub 리포지토리를](https://github.com/Microsoft/ApplicationInsights-JS)방문하는 오픈 소스입니다.

## <a name="next-steps"></a><a name="next"></a> 다음 단계
* [사용 현황 추적](usage-overview.md)
* [사용자 지정 이벤트 및 메트릭](api-custom-events-metrics.md)
* [빌드 - 측정 - 학습](usage-overview.md)
