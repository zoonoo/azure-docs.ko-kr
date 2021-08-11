---
title: Application Insights를 사용하여 SharePoint 사이트 모니터링
description: 새 계측 키를 사용하여 새 애플리케이션 모니터링 시작
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: 74f0eeecb303d68af8aaa48b5c1806ceeef4d318
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102486372"
---
# <a name="monitor-a-sharepoint-site-with-application-insights"></a>Application Insights를 사용하여 SharePoint 사이트 모니터링

Azure Application Insights는 애플리케이션의 가용성, 성능 및 사용량을 모니터링합니다. 여기에서는 SharePoint 사이트에 맞게 설정하는 방법을 알아봅니다.

> [!NOTE]
> 보안 문제로 인해, 이 문서에 설명된 스크립트를 SharePoint 최신 UX의 웹 페이지에 직접 추가할 수 없습니다. 대신 [SharePoint Framework(SPFx)](/sharepoint/dev/spfx/extensions/overview-extensions)를 사용하여 SharePoint 사이트에 Application Insights를 설치하는 데 사용할 수 있는 사용자 지정 확장을 빌드할 수 있습니다.

## <a name="create-an-application-insights-resource"></a>Application Insights 리소스 만들기
[Azure Portal](https://portal.azure.com)에서 새 Application Insights 리소스를 만듭니다. 애플리케이션 유형으로 ASP.NET을 선택합니다.

![속성 클릭, 키 선택 및 ctrl+C 누르기](./media/sharepoint/001.png)

열리는 창에서 앱의 성능 및 사용량 현황 데이터를 볼 수 있습니다. 다음에 Azure에 로그인할 때 다시 이 블레이드로 돌아가려면 시작 화면에서 해당 타일을 찾아야 합니다. 또는 찾아보기를 클릭하여 찾아야 합니다.

## <a name="add-the-script-to-your-web-pages"></a>웹 페이지에 스크립트 추가

현재 코드 조각(아래에 나열됨)은 버전 "5"이며, 이 버전은 코드 조각에서 sv:"#"으로 인코딩됩니다. [현재 버전은 GitHub에서도 사용할 수 있습니다](https://go.microsoft.com/fwlink/?linkid=2156318).

```HTML
<!-- 
To collect user behavior analytics tools about your application, 
insert the following script into each page you want to track.
Place this code immediately before the closing </head> tag,
and before any other scripts. Your first data will appear 
automatically in just a few seconds.
-->
<script type="text/javascript">
!function(T,l,y){var S=T.location,k="script",D="instrumentationKey",C="ingestionendpoint",I="disableExceptionTracking",E="ai.device.",b="toLowerCase",w="crossOrigin",N="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"5",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[b](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,u,p,l;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][b]()]=i[1])}if(!e[C]){var r=e.endpointsuffix,o=r?e.location:null;e[C]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[D]||d[D]||"",u=s[C],p=u?u+"/v2/track":d.endpointUrl,(l=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=p,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),l.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,p)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:N,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(N,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(l,p))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(k);n.src=h;var e=y[w];return!e&&""!==e||"undefined"==n[w]||(n[w]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(k)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[I]&&!0!==s[I]){var c="onerror";t(["_"+c]);var u=T[c];T[c]=function(e,t,n,a,i){var r=u&&u(e,t,n,a,i);return!0!==r&&m["_"+c]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);function a(){y.onInit&&y.onInit(n)}(T[t]=n).queue&&0===n.queue.length?(n.queue.push(a),n.trackPageView({})):a()}(window,document,{
src: "https://js.monitor.azure.com/scripts/b/ai.2.gbl.min.js", // The SDK URL Source
// name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
// ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
// useXhr: 1, // Use XHR instead of fetch to report failures (if available),
crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag
// onInit: null, // Once the application insights instance has loaded and initialized this callback function will be called with 1 argument -- the sdk instance (DO NOT ADD anything to the sdk.queue -- As they won't get called)
cfg: { // Application Insights Configuration
  instrumentationKey:"INSTRUMENTATION_KEY"
}});
</script>
```

> [!NOTE]
> SharePoint용 Url에서는 다른 모듈 형식 "...\ai.2.gbl.min.js"(추가 **.gbl.** 유의)를 사용합니다. 이 대체 모듈 형식은 스크립트가 로드되는 주문에 의해 SDK가 초기화되지 않고 원격 분석 이벤트가 손실되는 문제를 방지하는 데 필요합니다.
>
> 이 문제는 requireJS가 SDK보다 먼저 로드되고 초기화되기 때문에 발생합니다.

추적하려는 모든 페이지의 &lt;/head&gt; 태그 바로 앞에 스크립트를 삽입합니다. 웹 사이트에 마스터 페이지가 있으면 스크립트를 넣을 수 있습니다. 예를 들어 ASP.NET MVC 프로젝트에서는 View\Shared\_Layout.cshtml에 추가합니다.

스크립트에는 Application Insights 리소스에 원격 분석을 전달하는 계측 키가 포함됩니다.

### <a name="add-the-code-to-your-site-pages"></a>사이트 페이지에 코드를 추가합니다.
#### <a name="on-the-master-page"></a>마스터 페이지에서
사이트의 마스터 페이지를 편집할 수 있는 경우 사이트의 모든 페이지에 대한 모니터링을 제공합니다.

마스터 페이지를 체크 아웃하고 SharePoint Designer 또는 다른 편집기를 사용하여 편집합니다.

![Sharepoing Designer 또는 다른 편집기를 사용하여 마스터 페이지를 편집하는 방법을 보여 주는 스크린샷.](./media/sharepoint/03-master.png)

태그 바로 앞에 코드를 추가합니다. </head> 태그에 대해 알아봅니다. 

![사이트 페이지에 코드를 추가할 위치를 보여 주는 스크린샷.](./media/sharepoint/04-code.png)

#### <a name="or-on-individual-pages"></a>또는 개별 페이지에서
제한된 페이지 집합을 모니터링하려면 각 페이지에 개별적으로 스크립트를 추가합니다. 

웹 파트를 삽입하고 코드 조각을 포함합니다.

![제한된 페이지 집합을 모니터링하는 스크립트의 추가 방법을 보여 주는 스크린샷.](./media/sharepoint/05-page.png)

## <a name="view-data-about-your-app"></a>앱에 대한 데이터 보기
응용 프로그램을 다시 배포 합니다.

[Azure Portal](https://portal.azure.com)에서 사용자 애플리케이션 블레이드로 돌아갑니다.

첫 번째 이벤트가 검색에 표시됩니다. 

![앱에서 볼 수 있는 새 데이터를 보여 주는 스크린샷.](./media/sharepoint/09-search.png)

더 많은 데이터를 기대하는 경우 몇 초 후에 새로고침을 클릭합니다.

## <a name="capturing-user-id"></a>사용자 ID 캡처
표준 웹 페이지 코드 조각은 SharePoint에서 사용자 ID를 캡처하지 않지만 약간 수정하여 캡처할 수 있습니다.

1. Application Insights의 Essentials 드롭 다운에서 앱의 계측 키를 복사합니다. 

    ![Application Insights의 Essentials 드롭다운에서 앱의 계측을 복사 하는 것을 보여 주는 스크린샷.](./media/sharepoint/02-props.png)

1. 다음 코드 조각에서 'XXXX'에 대한 계측 키를 대체합니다. 
2. 포털에서 가져온 코드 조각 대신 SharePoint 앱에 스크립트를 포함합니다.

```


<SharePoint:ScriptLink ID="ScriptLink1" name="SP.js" runat="server" localizable="false" loadafterui="true" /> 
<SharePoint:ScriptLink ID="ScriptLink2" name="SP.UserProfiles.js" runat="server" localizable="false" loadafterui="true" /> 

<script type="text/javascript"> 
var personProperties; 

// Ensure that the SP.UserProfiles.js file is loaded before the custom code runs. 
SP.SOD.executeOrDelayUntilScriptLoaded(getUserProperties, 'SP.UserProfiles.js'); 

function getUserProperties() { 
    // Get the current client context and PeopleManager instance. 
    var clientContext = new SP.ClientContext.get_current(); 
    var peopleManager = new SP.UserProfiles.PeopleManager(clientContext); 

    // Get user properties for the target user. 
    // To get the PersonProperties object for the current user, use the 
    // getMyProperties method. 

    personProperties = peopleManager.getMyProperties(); 

    // Load the PersonProperties object and send the request. 
    clientContext.load(personProperties); 
    clientContext.executeQueryAsync(onRequestSuccess, onRequestFail); 
} 

// This function runs if the executeQueryAsync call succeeds. 
function onRequestSuccess() { 
var appInsights=window.appInsights||function(config){
function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
    }({
        instrumentationKey:"XXXX"
    });
    window.appInsights=appInsights;
    appInsights.trackPageView(document.title,window.location.href, {User: personProperties.get_displayName()});
} 

// This function runs if the executeQueryAsync call fails. 
function onRequestFail(sender, args) { 
} 
</script> 


```



## <a name="next-steps"></a>다음 단계
* [웹 테스트](./monitor-web-app-availability.md)
* 다른 유형의 앱에 대한[Application Insights](./app-insights-overview.md).

<!--Link references-->
