---
title: JavaScript 웹 애플리케이션에 대한 SDK 로드 실패 문제 해결 - Azure Application Insights
description: JavaScript 웹 애플리케이션에 대한 SDK 로드 실패 문제를 해결하는 방법
ms.topic: conceptual
author: MSNev
ms.author: newylie
ms.date: 06/05/2020
ms.custom: devx-track-js
ms.openlocfilehash: 6295a56abbf3466c68b968c935936dbc10e22fb5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711419"
---
# <a name="troubleshooting-sdk-load-failure-for-javascript-web-apps"></a>JavaScript 웹앱에 대한 SDK 로드 실패 문제 해결

<!-- 
Editor Note: This link name above "SDK Load Failure" has a direct references by https://go.microsoft.com/fwlink/?linkid=2128109 which is embedded in the snippet and from the JavaScript Page. If you change this text you MUST also update these references.
-->

SDK 로드 실패 예외는 SDK 스크립트를 다운로드하거나 초기화하지 못했음을 감지한 JavaScript 코드 조각(v3 이상)에서 만들고 보고합니다. 간단히 말해서 최종 사용자의 클라이언트(브라우저)는 Application Insights SDK를 다운로드하거나 식별된 호스팅 페이지에서 초기화할 수 없으므로 원격 분석 또는 이벤트가 보고되지 않습니다.

![Azure Portal 브라우저 오류 개요](./media/javascript-sdk-load-failure/overview.png)

> [!NOTE]
> 이 예외는 fetch() API 또는 XMLHttpRequest를 지원하는 모든 주요 브라우저에서 지원됩니다. 여기에는 IE 8 이하는 제외되므로 해당 브라우저에서 이 유형의 예외가 보고되지 않습니다(환경에 fetch polyfill이 포함되지 않은 경우).

![브라우저 예외 정보](./media/javascript-sdk-load-failure/exception.png)

스택 정보에는 최종 사용자가 사용하는 URL에 기본 정보가 포함됩니다.

| 이름                      | Description                                                                                                  |
|---------------------------|--------------------------------------------------------------------------------------------------------------|
| &lt;CDN&nbsp;엔드포인트&gt; | SDK를 다운로드하는 데 사용되거나 다운로드하지 못한 URL입니다.                                                      |
| &lt;도움말&nbsp;링크&gt;    | 문제 해결 설명서(이 페이지)로 연결되는 URL입니다.                                              |
| &lt;호스트&nbsp;URL&gt;     | 최종 사용자가 사용하는 페이지의 전체 URL입니다.                                                    |
| &lt;엔드포인트&nbsp;URL&gt; | 예외를 보고하는 데 사용된 URL입니다. 이 값은 퍼블릭 인터넷과 프라이빗 클라우드 중 어디에서 호스팅 페이지에 액세스했는지를 식별하는 데 유용할 수 있습니다.

이 예외가 발생하는 가장 일반적인 이유는 다음과 같습니다.

- 일시적인 네트워크 연결 오류
- Application Insights CDN 중단
- 스크립트를 로드한 후 SDK를 초기화하지 못함
- Application Insights JavaScript CDN이 차단됨

[일시적인 네트워크 연결 오류](#intermittent-network-connectivity-failure)는 이 예외의 가장 일반적인 이유로, 특히 일시적으로 네트워크 연결이 끊어지는 모바일 로밍 시나리오에서 가장 일반적으로 나타납니다.

다음 섹션에서는 이 오류의 각 잠재적 근본 원인을 해결하는 방법을 설명합니다.

> [!NOTE]
> 일부 문제 해결 단계에서는 애플리케이션이 코드 조각 &lt;script /&gt; 태그를 직접 제어하며 호스팅 HTML 페이지의 일부로 반환되는 구성이라고 가정합니다. 그렇지 않으면 식별된 단계가 사용자의 시나리오에 적용되지 않습니다.

## <a name="intermittent-network-connectivity-failure"></a>일시적인 네트워크 연결 오류

**일시적인 네트워크 연결 오류가 발생하는 경우에는 가능한 해결 방법이 더 적으며 일반적으로 짧은 시간 동안 자체적으로 해결됩니다.** 예를 들어, 사용자가 사이트를 다시 로드하는 경우(페이지 새로 고침) 업데이트된 버전이 릴리스될 때까지 파일이 (결과적으로) 로컬로 다운로드되고 캐시됩니다.

> [!NOTE]
> 이 예외가 영구적이며 일반적인 클라이언트 원격 분석의 감소와 더불어 많은 사용자에게서 발생하는 경우(이 예외의 신속하고 지속적인 수준이 보고되는 상황에 따라 진단됨) 일시적인 네트워크 연결 문제가 해당 문제의 실제 원인이 될 _가능성이 없으며_ 알려진 다른 가능한 문제를 계속 진단해야 합니다.

이러한 상황에서 사용자 고유의 CDN에 SDK를 호스트해도 해당 CDN이 동일한 문제로 인해 영향을 받으므로 이 예외가 발생하거나 발생 횟수가 줄어들 가능성은 없습니다.

NPM 패키지 솔루션을 통해 SDK를 사용하는 경우에도 마찬가지입니다. 그러나 이 경우 최종 사용자의 관점에서 _단지_ 원격 분석 SDK가 아니라 전체 애플리케이션의 로드/초기화가 실패하며 이 사실은 육안으로 확인할 수 없으므로 완전히 로드될 때까지 사이트를 새로 고치게 됩니다.

[NPM 패키지](#use-npm-packages-to-embed-the-application-insight-sdk)를 사용하여 Application Insights SDK를 포함할 수도 있습니다.

일시적인 네트워크 연결 오류를 최소화하기 위해 모든 CDN 파일에서 Cache-Control 헤더를 구현했으므로 최종 사용자의 브라우저가 현재 버전의 SDK를 다운로드한 후에는 다시 다운로드할 필요가 없으며 브라우저에서 이전에 가져온 복사본을 다시 사용할 수 있습니다([캐싱 작동 방식](../../cdn/cdn-how-caching-works.md) 참조). 캐싱 검사가 실패하거나 새 릴리스가 있는 경우 최종 사용자의 브라우저에서 업데이트된 버전을 다운로드해야 합니다. 따라서 검사 실패 시나리오에서 백그라운드 수준의 _"노이즈"_ 가 나타나거나, 새 릴리스가 출시되고 일반 공급(CDN에 배포)될 때 노이즈가 일시적으로 급증할 수 있습니다.
 
## <a name="application-insights-cdn-outage"></a>Application Insights CDN 중단

브라우저에서 직접(예: https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js ) 또는 최종 사용자가 사용할 수 있는 자체 개발 머신 이외의 다른 위치에서(조직에서 이 도메인을 차단하지 않았다고 가정할 경우 https://js.monitor.azure.com/scripts/b/ai.2.min.js) 에서) CDN 엔드포인트에 액세스하려고 시도하여 Application Insights CDN 중단이 발생하는지 확인할 수 있습니다.

작동 중단이 발생하는 것을 확인하는 경우 [새 지원 티켓을 만들거나](https://azure.microsoft.com/support/create-ticket/) SDK 다운로드에 사용되는 URL을 변경해 볼 수 있습니다.

### <a name="change-the-cdn-endpoint"></a>CDN 엔드포인트 변경
  
코드 조각과 해당 구성이 생성된 각 페이지의 일부로 애플리케이션에서 반환되면 SDK에 대해 다른 URL을 사용하도록 코드 조각의 `src` 구성을 변경할 수 있습니다. 이 접근 방식을 사용하면 새 URL이 차단되지 않으므로 CDN 차단 문제를 우회할 수 있습니다.

현재 Application Insights JavaScript SDK CDN 엔드포인트
- `https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js`
- `https://js.monitor.azure.com/scripts/b/ai.2.min.js`

> [!NOTE]
> `https://js.monitor.azure.com/` 엔드포인트는 구성을 변경할 필요 없이 약 5분 이내에 CDN 공급자 간을 전환할 수 있도록 하는 별칭입니다. 이 기능은 CDN 공급자에게 모든 사람이 설정을 조정하도록 요구하지 않고도 지역 또는 글로벌 문제가 있는 경우 검색된 CDN 관련 문제를 더 신속하게 수정할 수 있도록 하기 위한 것입니다.

## <a name="sdk-failed-to-initialize-after-loading-the-script"></a>스크립트를 로드한 후 SDK를 초기화하지 못함

SDK를 초기화하지 못하면 CDN에서 &lt;script /&gt;가 성공적으로 다운로드되었더라도 초기화하는 동안 실패합니다. 이러한 문제는 누락되었거나 잘못된 종속성이나 특정 형식의 JavaScript 예외가 발생했기 때문일 수 있습니다.

가장 먼저 확인할 사항은 SDK가 성공적으로 다운로드되었는지 여부입니다. 스크립트가 다운로드되지 않은 경우 이 시나리오는 SDK 로드 실패 예외의 원인이 __아닙니다__.

빠른 검사: 개발자 도구(F12)를 지원하는 브라우저를 사용하여 ```src``` 코드 조각 구성에 정의된 스크립트가 200(성공) 또는 304(변경되지 않음) 응답 코드를 표시하면서 다운로드된 네트워크 탭에 대해 유효성을 검사합니다. Fiddler와 같은 도구를 사용하여 네트워크 트래픽을 검토할 수도 있습니다.

아래 섹션에는 다양한 보고 옵션이 포함되어 있습니다. 이 섹션에서는 GitHub에서 지원 티켓을 만들거나 문제를 제기할 것을 권장합니다.

 기본 보고 규칙:

- 보고된 예외에 대한 세부 정보를 확인했을 때 문제가 소수의 사용자와 특정 브라우저 버전이나 하위 버전에만 영향을 주는 것으로 확인되면 해당 문제는 최종 사용자 또는 환경에만 나타나는 문제일 수 있으며, 애플리케이션에서 추가적인 `polyfill` 구현을 제공해야 할 수 있습니다. 이러한 경우 [GitHub에서 문제를 제출](https://github.com/Microsoft/ApplicationInsights-JS/issues)합니다.
- 이 문제가 전체 애플리케이션에 영향을 주고 모든 사용자가 영향을 받는 경우 릴리스 관련 문제일 수 있으므로 [새 지원 티켓을 만들어야](https://azure.microsoft.com/support/create-ticket/) 합니다.

### <a name="javascript-exceptions"></a>JavaScript 예외

먼저 개발자 도구(F12)를 지원하는 브라우저를 사용하여 JavaScript 예외를 확인하고, 페이지를 로드한 후 예외가 발생했는지 확인합니다.

SDK 스크립트에서 보고되는 예외가 있는 경우(예: ai.2.min.js) SDK에 전달된 구성에 예기치 않은 구성이 있거나, 필수 구성이 누락되었거나, 잘못된 릴리스가 CDN에 배포되었음을 나타낼 수 있습니다.

잘못된 구성을 확인하려면 코드 조각에 전달된 구성을 변경하여(아직 변경하지 않은 경우) 계측 키를 문자열 값으로 포함합니다.

> src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js ",<br />
> cfg:{<br />
> instrumentationKey: "INSTRUMENTATION_KEY"<br />
> }});<br />

이 최소 구성을 사용해도 SDK 스크립트에 JavaScript 예외가 계속 표시되는 경우 새로 배포된 버전의 문제일 수 있으므로 잘못된 빌드를 롤백해아 합니다. 따라서 [새 지원 티켓을 만듭니다.](https://azure.microsoft.com/support/create-ticket/)

예외가 사라지면 형식 불일치 또는 예기치 않은 값으로 인해 이 문제가 발생한 것일 수 있습니다. 구성 옵션을 하나씩 다시 추가하고 예외가 다시 발생될 때까지 테스트를 진행합니다. 그런 다음, 문제의 원인이 되는 항목에 대한 설명서를 확인합니다. 설명서가 명확하지 않거나 도움이 필요한 경우 [GitHub에서 문제를 제출](https://github.com/Microsoft/ApplicationInsights-JS/issues)하세요.

이전에 구성을 배포했으며 해당 구성이 작동되고 있지만 이 예외를 보고하기 시작한 경우에는 새로 배포된 버전에 문제가 있을 수 있습니다. 이 버전이 소수의 사용자/브라우저에만 영향을 주는지 여부를 확인하고 [GitHub에서 문제를 제출](https://github.com/Microsoft/ApplicationInsights-JS/issues)하거나 [새 지원 티켓을 만드세요](https://azure.microsoft.com/support/create-ticket/).

### <a name="enable-console-debugging"></a>콘솔 디버깅 사용

예외가 발생하지 않는다고 가정할 경우 다음에 수행할 단계는 구성에 `loggingLevelConsole` 설정을 추가하여 콘솔 디버깅을 사용하도록 설정하는 것입니다. 이렇게 하면 모든 초기화 오류 및 경고가 브라우저 콘솔에 전송됩니다. 이 작업은 일반적으로 개발자 도구(F12)를 통해 수행할 수 있습니다. 보고된 모든 오류는 설명을 포함하고 있으며 추가 지원이 필요한 경우에는 [GitHub에서 문제를 제출](https://github.com/Microsoft/ApplicationInsights-JS/issues)하세요.

> cfg:{<br />
> instrumentationKey: "INSTRUMENTATION_KEY",<br />
> loggingLevelConsole: 2<br />
> }});<br />

> [!NOTE]
> 초기화하는 동안 SDK는 알려진 주요 종속성에 대한 몇 가지 기본 검사를 수행합니다. 이러한 종속성을 현재 런타임에서 제공하지 않으면 오류를 콘솔에 경고 메시지로 보고합니다. 단, `loggingLevelConsole`이 0보다 큰 경우에만 해당됩니다.

여전히 초기화에 실패하는 경우 ```enableDebug``` 구성 설정을 사용 하도록 설정해 보세요. 이렇게 하면 모든 내부 오류가 예외로 throw됩니다(이로 인해 원격 분석이 손실됨). 이 설정은 개발자 전용 설정이므로 특정 내부 검사의 일부로 throw되는 예외로 인해 노이즈가 발생할 수 있습니다. 따라서 각 예외를 검토하여 SDK 실패를 유발하는 문제를 확인해야 합니다. 스크립트의 축소되지 않은 버전을 사용합니다(아래 확장은 ".min.js"가 아니라 ".js"임). 그러지 않으면 예외를 읽을 수 없습니다.

> [!WARNING]
> 이것은 개발자 전용 설정이며 원격 분석이 손실되므로 전체 프로덕션 환경에서 사용하도록 설정하면 안 됩니다.

> src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js ",<br />
> cfg:{<br />
> instrumentationKey: "INSTRUMENTATION_KEY",<br />
> enableDebug: true<br />
> }});<br />

여전히 어떤 인사이트도 얻을 수 없으면 알고 있는 세부 정보 및 예제 사이트(있는 경우)와 함께 [GitHub에서 문제를 제출](https://github.com/Microsoft/ApplicationInsights-JS/issues)해야 합니다. 문제를 식별하는 데 도움이 되는 브라우저 버전, 운영 체제 및 JS 프레임워크 세부 정보를 포함합니다.

## <a name="the-application-insights-javascript-cdn-has-been-blocked"></a>Application Insights JavaScript CDN이 차단됨

CDN이 차단되는 문제는 Application Insights JavaScript SDK CDN 엔드포인트가 안전하지 않다고 보고되거나 확인될 때 발생할 수 있습니다. 이 경우 엔드포인트가 공개적으로 차단 목록에 추가되고 이러한 목록의 소비자가 모든 액세스를 차단하기 시작합니다.

이 문제를 해결하려면 CDN 엔드포인트 소유자가 관련 목록에서 제거될 수 있도록 엔드포인트를 안전하지 않은 것으로 표시하는 차단 목록 엔터티를 사용해야 합니다.

CDN 엔드포인트가 안전하지 않은 것으로 식별되었는지 확인합니다.
- [Google 투명도 보고서](https://transparencyreport.google.com/safe-browsing/search)
- [VirusTotal](https://www.virustotal.com/gui/home/url)
- [Sucuri SiteCheck](https://sitecheck.sucuri.net/)

애플리케이션, 방화벽 또는 환경에서 이러한 목록의 로컬 복사본을 업데이트하는 주기에 따라, 상당한 시간이 걸리거나 최종 사용자 또는 회사 IT 부서가 수동으로 개입하여 업데이트를 강제로 진행하거나 CDN 엔드포인트에서 문제를 해결하도록 명시적으로 허용해야 할 수 있습니다.

CDN 엔드포인트가 안전하지 않은 것으로 확인되면 가능한 한 빨리 문제가 해결되도록 [지원 티켓을 만듭니다.](https://azure.microsoft.com/support/create-ticket/)

잠재적으로 이 문제를 더 빠르게 피하기 위해 [SDK CDN 엔드포인트를 변경](#change-the-cdn-endpoint)할 수 있습니다.

### <a name="application-insights-javascript-cdn-is-blocked-by-end-user---blocked-by-browser-installed-blocker-personal-firewall"></a>Application Insights JavaScript CDN이 차단됨(최종 사용자가 차단했거나 브라우저, 설치된 차단기, 개인 방화벽에 의해 차단됨)

최종 사용자가 다음을 수행했는지 확인합니다.

- 브라우저 플러그 인(일반적으로 광고, 맬웨어 또는 팝업 차단 형태)을 설치했습니다.
- 브라우저 또는 프록시에서 Application Insights CDN 엔드포인트를 차단했거나 허용되지 않습니다.
- SDK에 대한 CDN 도메인이 차단되도록 하는(또는 DNS 항목이 확인되지 않도록 하는) 방화벽 규칙을 구성했습니다.

이러한 옵션을 구성한 경우 사용자와 협의하거나 설명서를 제공하여 CDN 엔드포인트를 허용하도록 해야 합니다.

설치한 플러그 인이 퍼블릭 차단 목록을 사용하고 있을 수 있습니다. 그렇지 않은 경우 수동으로 구성된 다른 솔루션이 있거나 프라이빗 도메인 차단 목록를 사용하는 것이 가장 일반적일 수 있습니다.

#### <a name="add-exceptions-for-cdn-endpoints"></a>CDN 엔드포인트에 대한 예외 추가

최종 사용자와 협의하거나 설명서를 제공하여 Application Insights CDN 엔드포인트의 스크립트를 브라우저의 플러그 인 또는 방화벽 규칙 예외 목록(최종 사용자 환경에 따라 다름)에 포함하는 방식으로 다운로드를 허용해야 한다는 사실을 알립니다.

다음은 [웹 사이트에 대한 액세스를 허용하거나 차단하도록 Chrome](https://support.google.com/chrome/a/answer/7532419?hl=en)을 구성하는 방법의 예입니다.

### <a name="application-insights-cdn-is-blocked-by-corporate-firewall"></a>Application Insights CDN이 회사 방화벽에 의해 차단됨

최종 사용자가 회사 네트워크에 있는 경우에는 방화벽 솔루션에 의해 차단된 것일 수 있으며, IT 부서에서 일종의 인터넷 필터링 시스템을 구현했을 수도 있습니다. 이 경우 최종 사용자와 협의하여 필요한 규칙을 허용하도록 해야 합니다.

#### <a name="add-exceptions-for-cdn-endpoints-for-corporations"></a>회사의 CDN 엔드포인트에 대한 예외 추가

  이 작업은 [최종 사용자에 대한 예외를 추가](#add-exceptions-for-cdn-endpoints)하는 것과 유사하지만, 회사의 IT 부서와 협력하여 모든 도메인 차단 목록 또는 허용 목록 서비스에 Application Insights CDN 엔드포인트를 포함(또는 제거)하여 다운로드할 수 있도록 구성해야 합니다.

  > [!WARNING]
  > 회사 사용자가 [프라이빗 클라우드](https://azure.microsoft.com/overview/what-is-a-private-cloud/)를 사용하며 CDN 엔드포인트에 대한 퍼블릭 액세스를 내부 사용자에게 제공하기 위해 어떤 형태의 예외도 사용하도록 설정할 수 없는 경우 [Application Insights NPM 패키지](https://www.npmjs.com/package/applicationinsights)를 사용하거나 자체 CDN에서 Application Insights SDK를 호스트해야 합니다.  

### <a name="additional-troubleshooting-for-blocked-cdn"></a>차단된 CDN에 대한 추가 문제 해결

> [!NOTE]
> 사용자가 [프라이빗 클라우드](https://azure.microsoft.com/overview/what-is-a-private-cloud/)를 사용하고 있으며 퍼블릭 인터넷에 액세스할 수 없는 경우 자체 CDN에서 SDK를 호스트하거나 NPM 패키지를 사용해야 합니다.

#### <a name="host-the-sdk-on-your-own-cdn"></a>고유한 CDN에서 SDK 호스트

 최종 사용자는 퍼블릭 CDN에서 Application Insights SDK를 다운로드하는 대신 사용자의 고유한 CDN 엔드포인트에서 Application Insights SDK를 호스트할 수 있습니다. 사용 중인 버전을 더 쉽게 식별할 수 있도록 특정 버전(ai.2.#.#.min.js)을 사용하는 것이 좋습니다. 또한 버그 수정 및 사용할 수 있는 새로운 기능을 활용할 수 있도록 정기적으로 현재 버전(ai.2.min.js)으로 업데이트합니다.

#### <a name="use-npm-packages-to-embed-the-application-insight-sdk"></a>NPM 패키지를 사용하여 Application Insight SDK 포함

코드 조각 및 퍼블릭 CDN 엔드포인트를 사용하는 대신, [NPM 패키지](https://www.npmjs.com/package/applicationinsights)를 사용하여 SDK를 사용자 고유의 JavaScript 파일로 포함할 수 있습니다. SDK는 사용자 고유의 스크립트 내에서 또 다른 패키지일 뿐입니다.

> [!NOTE]
> NPM 패키지를 사용하는 경우 코드 분할 및 축소를 지원하기 위해 일종의 [JavaScript 번들러](https://www.bing.com/search?q=javascript+bundler)도 사용하는 것이 좋습니다.

이 코드 조각과 마찬가지로 SDK NPM 패키지를 사용하거나 사용하지 않는 사용자 고유의 스크립트가 여기에 나열된 것과 동일한 차단 문제의 영향을 받을 수도 있으므로 애플리케이션, 사용자 및 프레임워크에 따라 이러한 문제를 감지하고 보고하기 위해 코드 조각의 논리와 비슷한 구현을 고려할 수 있습니다.


## <a name="next-steps"></a>다음 단계 
- [GitHub에서 문제를 제출하여 추가 도움말 얻기](https://github.com/Microsoft/ApplicationInsights-JS/issues)
- [웹 페이지 사용 모니터링](javascript.md)
