---
title: JavaScript 웹 응용 프로그램에 대 한 SDK 로드 실패 문제 해결-Azure 애플리케이션 정보
description: JavaScript 웹 응용 프로그램에 대 한 SDK 로드 실패 문제를 해결 하는 방법
ms.topic: conceptual
author: MSNev
ms.author: newylie
ms.date: 06/05/2020
ms.openlocfilehash: a76ed65ebc1c56232d4fa42c6df20f619fe14ca3
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86517062"
---
# <a name="troubleshooting-sdk-load-failure-for-javascript-web-apps"></a>JavaScript 웹 앱에 대 한 SDK 로드 오류 문제 해결

<!-- 
Editor Note: This link name above "SDK Load Failure" has a direct references by https://go.microsoft.com/fwlink/?linkid=2128109 which is embedded in the snippet and from the JavaScript Page. If you change this text you MUST also update these references.
-->

Sdk 로드 실패 예외는 SDK 스크립트를 다운로드 하거나 초기화 하지 못했음을 감지한 JavaScript 조각 (v3 이상)에서 만들고 보고 합니다. Simplistically 최종 사용자의 클라이언트 (브라우저)가 Application Insights SDK를 다운로드 하거나 식별 된 호스팅 페이지에서 초기화할 수 없으므로 원격 분석 또는 이벤트가 보고 되지 않습니다.

![Azure Portal 브라우저 오류 개요](./media/javascript-sdk-load-failure/overview.png)

> [!NOTE]
> 이 예외는 fetch () API 또는 XMLHttpRequest를 지 원하는 모든 주요 브라우저에서 지원 됩니다. 여기에는 IE 8이 제외 되므로 해당 브라우저에서이 유형의 예외를 보고 하지 않습니다 (환경에 fetch safehtml 포함 되지 않은 경우).

![브라우저 예외 정보](./media/javascript-sdk-load-failure/exception.png)

스택 정보에는 최종 사용자가 사용 하는 Url의 기본 정보가 포함 됩니다.

| 이름                      | 설명                                                                                                  |
|---------------------------|--------------------------------------------------------------------------------------------------------------|
| &lt;CDN &nbsp; 끝점&gt; | SDK를 다운로드 하는 데 사용 되거나 실패 한 URL입니다.                                                      |
| &lt;도움말 &nbsp; 링크&gt;    | 문제 해결 설명서 (이 페이지)에 연결 되는 URL입니다.                                              |
| &lt;호스트 &nbsp; URL&gt;     | 최종 사용자가 사용 하는 페이지의 전체 URL입니다.                                                    |
| &lt;끝점 &nbsp; URL&gt; | 예외를 보고 하는 데 사용 된 URL입니다 .이 값은 공용 인터넷 또는 사설 클라우드에서 호스팅 페이지에 액세스 했는지 여부를 식별 하는 데 유용할 수 있습니다.

이 예외가 발생 하는 가장 일반적인 이유는 다음과 같습니다.

- 일시적인 네트워크 연결에 실패 했습니다.
- CDN 중단을 Application Insights 합니다.
- 스크립트를 로드 한 후 SDK를 초기화 하지 못했습니다.
- Application Insights JavaScript CDN이 차단 되었습니다.

[일시적인 네트워크 연결 오류](#intermittent-network-connectivity-failure) 는 특히 사용자가 간헐적으로 네트워크 연결을 분실 한 모바일 로밍 시나리오에서 발생 하는 가장 일반적인 이유입니다.

다음 섹션에서는이 오류의 각 잠재적 근본 원인에 대 한 문제를 해결 하는 방법을 설명 합니다.

> [!NOTE]
> 몇 가지 문제 해결 단계에서는 응용 프로그램이 코드 조각 &lt; 스크립트/태그를 직접 제어 하 &gt; 고 호스팅 HTML 페이지의 일부로 반환 되는 구성을 가정 합니다. 그렇지 않으면 식별 된 단계가 시나리오에 적용 되지 않습니다.

## <a name="intermittent-network-connectivity-failure"></a>일시적인 네트워크 연결 오류

**사용자에 게 일시적인 네트워크 연결 오류가 발생 하는 경우에는 가능한 해결 방법이 줄어들고 일반적으로 짧은 시간 동안 자체적으로 해결 됩니다.** 예를 들어 사용자가 사이트를 다시 로드 하는 경우 (페이지 새로 고침) 업데이트 된 버전이 릴리스될 때까지 파일이 로컬로 다운로드 되 고 캐시 됩니다.

> [!NOTE]
> 정상적인 클라이언트 원격 분석의 감소와 함께이 예외가 지속적으로 발생 하 고 많은 사용자에 게 발생 하는 경우 (보고 되는이 예외의 빠른 및 지속적인 수준에서 진단 됨) 간헐적으로 발생 하는 네트워크 연결 문제는 문제의 _원인이 아닐 수_ 있으므로 다른 알려진 문제를 계속 진단 해야 합니다.

사용자 고유의 cdn에서 SDK를 호스트 하는 경우에는 동일한 문제로 인해 자체 CDN이 영향을 받으므로이 예외의 발생을 제공 하거나 줄일 수 없습니다.

NPM 패키지 솔루션을 통해 SDK를 사용 하는 경우에도 마찬가지입니다. 그러나이 경우 최종 사용자 관점에서 보면 전체 응용 프로그램이 시각적으로 표시 되지 않는 원격 분석 SDK _가 아닌 로드_ /초기화에 실패 하 게 되므로가 완전히 로드 될 때까지 사이트를 새로 고칠 가능성이 높습니다.

[NPM 패키지](#use-npm-packages-to-embed-the-application-insight-sdk) 를 사용 하 여 Application Insights SDK를 포함할 수도 있습니다.

일시적인 네트워크 연결 오류를 최소화 하기 위해 모든 CDN 파일에 Cache-control 헤더를 구현 했습니다. 그러면 최종 사용자의 브라우저에서 현재 버전의 SDK를 다운로드 한 후에는 다시 다운로드할 필요가 없으며 브라우저에서 이전에 가져온 복사본을 다시 사용 하 게 됩니다 ( [캐싱이 작동 하는 방법](../../cdn/cdn-how-caching-works.md)참조). 캐싱 확인이 실패 하거나 새 릴리스가 있는 경우 최종 사용자의 브라우저에서 업데이트 된 버전을 다운로드 해야 합니다. 따라서 확인 실패 시나리오에서 _"노이즈"_ 의 배경 수준이 표시 되거나 새 릴리스가 발생 하 고 일반 공급 (CDN에 배포) 될 때 임시 스파이크가 발생할 수 있습니다.
 
## <a name="application-insights-cdn-outage"></a>Application Insights CDN 중단

브라우저에서 직접 CDN 끝점에 액세스 하려고 시도 하 여 Application Insights CDN 중단이 있는지 확인할 수 있습니다. 예를 들어 https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js) 조직에서이 도메인을 차단 하지 않았다고 가정 하 고 최종 사용자가 아닌 다른 위치에서 사용 하는 것이 좋습니다.

작동 중단이 발생 하는 것을 확인 한 경우 [새 지원 티켓을 만들거나](https://azure.microsoft.com/support/create-ticket/) SDK 다운로드에 사용 되는 URL을 변경해 볼 수 있습니다.

### <a name="change-the-cdn-endpoint"></a>CDN 끝점 변경
  
코드 조각과 해당 구성이 생성 된 각 페이지의 일부로 응용 프로그램에 의해 반환 되 면 코드 조각 구성을 변경 하 여 `src` SDK에 대해 다른 URL을 사용할 수 있습니다. 이 접근 방식을 사용 하면 새 URL을 차단 하지 않기 때문에 CDN 차단 문제를 무시할 수 있습니다.

현재 Application Insights JavaScript SDK CDN 끝점
- `https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js`
- `https://js.monitor.azure.com/scripts/b/ai.2.min.js`

> [!NOTE]
> `https://js.monitor.azure.com/`끝점은 구성을 변경할 필요 없이 약 5 분 이내에 CDN 공급자 간을 전환할 수 있도록 하는 별칭입니다. 이는 CDN 공급자에 게 모든 사람이 설정을 조정할 필요 없이 지역 또는 글로벌 문제가 있는 경우 검색 된 CDN 관련 문제를 더 신속 하 게 수정할 수 있도록 하기 위한 것입니다.

## <a name="sdk-failed-to-initialize-after-loading-the-script"></a>스크립트를 로드 한 후 SDK를 초기화 하지 못했습니다.

SDK를 초기화 하지 못하면 &lt; &gt; CDN에서 스크립트/가 성공적으로 다운로드 되었지만 초기화 하는 동안 실패 합니다. 이는 종속성이 누락 되거나 잘못 되었거나 일부 형식의 JavaScript 예외가 발생 했기 때문일 수 있습니다.

가장 먼저 확인할 사항은 SDK가 성공적으로 다운로드 되었는지 여부입니다. 스크립트가 다운로드 되지 않은 경우이 시나리오는 SDK 로드 실패 예외의 원인이 __아닙니다__ .

빠른 검사: 개발자 도구 (F12)를 지 원하는 브라우저를 사용 하 여, 조각 구성에 정의 된 스크립트가 ```src``` 200 (성공) 또는 304 (변경 되지 않음)의 응답 코드로 다운로드 된 네트워크 탭에서 유효성을 검사 합니다. Fiddler와 같은 도구를 사용 하 여 네트워크 트래픽을 검토할 수도 있습니다.

아래 섹션에는 다양 한 보고 옵션이 포함 되어 있습니다. 지원 티켓을 만들거나 GitHub에서 문제를 발생 시키는 것이 좋습니다.

 기본 보고 규칙:

- 문제가 적은 수의 사용자 및 브라우저 버전의 특정 또는 하위 집합에만 영향을 주는 경우에는 최종 사용자 또는 환경 에서만 문제가 발생할 수 있습니다 .이 경우 응용 프로그램에서 추가 구현을 제공 해야 합니다 `polyfill` . 이러한 경우 [GitHub에서 문제를 해결](https://github.com/Microsoft/ApplicationInsights-JS/issues)합니다.
- 이 문제가 전체 응용 프로그램에 영향을 주는 경우 모든 사용자에 게 영향을 줄 수 있는 경우 릴리스 관련 문제가 원인일 수 있으므로 [새 지원 티켓을 만들어야](https://azure.microsoft.com/support/create-ticket/)합니다.

### <a name="javascript-exceptions"></a>JavaScript 예외

먼저 개발자 도구 (F12)를 지 원하는 브라우저를 사용 하 여 JavaScript 예외를 확인 하 고, 페이지를 로드 하 고, 예외가 발생 했는지 확인 합니다.

SDK 스크립트에서 예외가 보고 되는 경우 (예: ai.2.min.js) SDK에 전달 된 구성이 예기치 않거나 누락 된 필수 구성을 포함 하거나 잘못 된 릴리스가 CDN에 배포 된 것을 나타낼 수 있습니다.

잘못 된 구성을 확인 하려면 코드 조각에 전달 된 구성 (아직 없는 경우)을 변경 하 여 계측 키를 문자열 값으로 포함 합니다.

> src: " https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js ",<br />
> cfg: {<br />
> instrumentationKey: "INSTRUMENTATION_KEY"<br />
> }});<br />

이 최소 구성을 사용 하는 경우 SDK 스크립트에서 JavaScript 예외가 계속 표시 되는 경우 [새 지원 티켓을 만듭니다](https://azure.microsoft.com/support/create-ticket/) . 그러면 새로 배포 된 버전의 문제일 수 있으므로 잘못 된 빌드를 롤백해야 합니다.

예외가 사라지면 형식이 일치 하지 않거나 예기치 않은 값으로 인해 발생할 수 있습니다. 구성 옵션을 하나씩 다시 추가 하 고 예외가 다시 발생 될 때까지 테스트를 시작 합니다. 그런 다음 문제의 원인이 되는 항목에 대 한 설명서를 확인 합니다. 설명서가 명확 하지 않거나 도움이 필요한 경우 [GitHub에서 문제를 해결](https://github.com/Microsoft/ApplicationInsights-JS/issues)하세요.

이전에 구성을 배포 하 고 작업 했지만이 예외를 보고 하기 시작한 경우에는 새로 배포 된 버전에 문제가 있을 수 있습니다 .이 버전은 사용자/브라우저의 작은 집합에만 영향을 주고 [GitHub에서 문제](https://github.com/Microsoft/ApplicationInsights-JS/issues) 를 제출 하거나 [새 지원 티켓을 만들지](https://azure.microsoft.com/support/create-ticket/)여부를 확인 합니다.

### <a name="enable-console-debugging"></a>콘솔 디버깅 사용

예외가 발생 하지 않는다고 가정 하 고 구성에 설정을 추가 하 여 콘솔 디버깅을 사용 하도록 설정 하면 `loggingLevelConsole` 모든 초기화 오류 및 경고가 브라우저 콘솔에 전송 됩니다 (일반적으로 F12 (개발자 도구)를 통해 사용 가능). 보고 된 모든 오류는 설명이 필요 없고 추가 지원이 필요한 경우 [GitHub에서 문제를 해결](https://github.com/Microsoft/ApplicationInsights-JS/issues)해야 합니다.

> cfg: {<br />
> instrumentationKey: "INSTRUMENTATION_KEY",<br />
> loggingLevelConsole: 2<br />
> }});<br />

> [!NOTE]
> 초기화 하는 동안 SDK는 알려진 주요 종속성에 대 한 몇 가지 기본 검사를 수행 합니다. 현재 런타임에서 제공 하지 않으면 오류를 콘솔에 경고 메시지로 보고 합니다. 단, `loggingLevelConsole` 가 0 보다 큰 경우에만 해당 됩니다.

그래도 초기화에 실패 하는 경우 구성 설정을 사용 하도록 설정 해 보세요 ```enableDebug``` . 이렇게 하면 모든 내부 오류가 예외로 throw 됩니다 (이로 인해 원격 분석이 손실 됨). 이는 개발자만 설정 하므로 일부 내부 검사의 일부로 throw 되는 예외를 사용 하 여 잡음이 발생할 수 있으므로, 각 예외를 검토 하 여 SDK 실패를 유발 하는 문제를 확인 해야 합니다. 스크립트의 비호환 버전을 사용 합니다. (아래 확장은 ".min.js"이 아니라 ".js"), 그렇지 않으면 예외를 읽을 수 없습니다.

> [!WARNING]
> 이는 개발자 전용 설정 이며 원격 분석이 손실 되므로 전체 프로덕션 환경에서 사용 하도록 설정 하면 안 됩니다.

> src: " https://az416426.vo.msecnd.net/scripts/b/ai.2.js ",<br />
> cfg: {<br />
> instrumentationKey: "INSTRUMENTATION_KEY",<br />
> enableDebug: true<br />
> }});<br />

그래도 정보를 제공 하지 않는 경우에는 세부 정보 및 예제 사이트 (있는 경우)와 함께 [GitHub에서 문제를 해결](https://github.com/Microsoft/ApplicationInsights-JS/issues) 해야 합니다. 문제를 식별 하는 데 도움이 되는 브라우저 버전, 운영 체제 및 JS 프레임 워크 세부 정보를 포함 합니다.

## <a name="the-application-insights-javascript-cdn-has-been-blocked"></a>Application Insights JavaScript CDN이 차단 되었습니다.

Application Insights JavaScript SDK CDN 끝점이 보고 되거나 안전 하지 않은 것으로 식별 되는 경우 차단 되는 CDN이 가능 합니다. 이 경우 끝점은 공개적으로 차단 됩니다. 그러면 이러한 목록의 소비자가 모든 액세스를 차단 하기 시작 합니다.

이 문제를 해결 하려면 CDN 끝점의 소유자가 끝점을 관련 목록에서 제거할 수 있도록 unsafe로 표시 한 블록 목록 엔터티를 사용 해야 합니다.

CDN 끝점이 unsafe로 식별 되었는지 확인 합니다.
- [Google 투명도 보고서](https://transparencyreport.google.com/safe-browsing/search)
- [VirusTotal](https://www.virustotal.com/gui/home/url)
- [Sucuri SiteCheck](https://sitecheck.sucuri.net/)

응용 프로그램, 방화벽 또는 환경에서 이러한 목록의 로컬 복사본을 업데이트 하는 빈도에 따라 시간이 오래 걸릴 수 있으며, 최종 사용자 또는 회사 IT 부서에서 수동으로 업데이트를 적용 하거나 CDN 끝점에서 문제를 해결 하기 위해 명시적으로 허용 하는 작업이 필요 합니다.

CDN 끝점이 안전 하지 않은 것으로 식별 되 면 가능한 한 빨리 문제를 해결할 수 있도록 [지원 티켓을 만듭니다](https://azure.microsoft.com/support/create-ticket/) .

이러한 문제를 더 *신속 하 게 무시 하려면* [SDK CDN 끝점을 변경](#change-the-cdn-endpoint)하면 됩니다.

### <a name="application-insights-javascript-cdn-is-blocked-by-end-user---blocked-by-browser-installed-blocker-personal-firewall"></a>Application Insights JavaScript CDN이 차단 됨 (최종 사용자-브라우저에서 차단 됨, 설치 된 차단기, 개인 방화벽)

최종 사용자에 게 다음이 있는지 확인 합니다.

- 브라우저 플러그 인을 설치 했습니다 (일반적으로 일부 형식의 ad, 맬웨어 또는 팝업 차단).
- 브라우저나 프록시의 Application Insights CDN 끝점을 차단 하거나 허용 하지 않습니다.
- SDK에 대 한 CDN 도메인을 차단 하는 방화벽 규칙이 구성 되어 있거나 확인할 수 없는 DNS 항목을 구성 했습니다.

이러한 옵션을 구성한 경우 CDN 끝점을 허용 하려면 해당 옵션을 사용 하거나 설명서를 제공 해야 합니다.

설치 된 플러그 인이 공용 차단 목록을 사용 하 고 있을 수 있습니다. 그렇지 않은 경우 수동으로 구성 된 다른 솔루션이 있거나 개인 도메인 차단 목록를 사용 하는 것이 가장 일반적입니다.

#### <a name="add-exceptions-for-cdn-endpoints"></a>CDN 끝점에 대 한 예외 추가

최종 사용자에 게 문의 하거나 Application Insights CDN 끝점의 스크립트를 브라우저의 플러그 인 또는 방화벽 규칙 예외 목록 (최종 사용자 환경에 따라 다름)에 포함 하 여 다운로드 하도록 허용 해야 한다는 사실을 알리는 설명서를 제공 합니다.

다음은 [웹 사이트에 대 한 액세스를 허용 하거나 차단 하도록 Chrome을](https://support.google.com/chrome/a/answer/7532419?hl=en) 구성 하는 방법의 예입니다.

### <a name="application-insights-cdn-is-blocked-by-corporate-firewall"></a>Application Insights CDN이 회사 방화벽에 의해 차단 됨

최종 사용자가 회사 네트워크에 있는 경우에는 방화벽 솔루션이 될 수 있으며, IT 부서에서 일종의 인터넷 필터링 시스템을 구현 했습니다. 이 경우 최종 사용자에 게 필요한 규칙을 허용 하는 작업을 수행 해야 합니다.

#### <a name="add-exceptions-for-cdn-endpoints-for-corporations"></a>회사의 CDN 끝점에 대 한 예외 추가

  이는 [최종 사용자에 대 한 예외를 추가](#add-exceptions-for-cdn-endpoints)하는 것과 유사 하지만, 회사의 IT 부서와 협력 하 여 모든 도메인 블록 목록 또는 허용 목록 서비스에 해당 항목을 포함 (또는 제거) 하 여 다운로드할 Application Insights CDN 끝점을 구성 해야 합니다.

  > [!WARNING]
  > 회사 사용자가 [사설 클라우드](https://azure.microsoft.com/overview/what-is-a-private-cloud/) 를 사용 하는 경우 모든 형식의 예외를 사용 하 여 cdn 끝점에 대 한 공용 액세스를 내부 사용자에 게 제공할 수 없는 경우 [Application Insights NPM 패키지](https://www.npmjs.com/package/applicationinsights) 를 사용 하거나 자체 cdn에서 Application Insights SDK를 호스트 해야 합니다.  

### <a name="additional-troubleshooting-for-blocked-cdn"></a>차단 된 CDN에 대 한 추가 문제 해결

> [!NOTE]
> 사용자가 [사설 클라우드](https://azure.microsoft.com/overview/what-is-a-private-cloud/) 를 사용 하 고 있으며 공용 인터넷에 액세스할 수 없는 경우 자체 CDN에서 SDK를 호스팅하거나 NPM 패키지를 사용 해야 합니다.

#### <a name="host-the-sdk-on-your-own-cdn"></a>고유한 CDN에서 SDK 호스트

 최종 사용자가 공용 CDN에서 Application Insights SDK를 다운로드 하는 대신 사용자의 CDN 끝점에서 Application Insights SDK를 호스트할 수 있습니다. 특정 버전 (ai. 2. #. # .min.js)을 사용 하 여 사용 중인 버전을 보다 쉽게 식별할 수 있도록 하는 것이 좋습니다. 또한 최신 버전 (ai.2.min.js)으로 정기적으로 업데이트 하 여 사용할 수 있게 되는 모든 버그 수정과 새로운 기능을 활용할 수 있습니다.

#### <a name="use-npm-packages-to-embed-the-application-insight-sdk"></a>NPM 패키지를 사용 하 여 응용 프로그램 정보 SDK를 포함 합니다.

코드 조각 및 공용 CDN 끝점을 사용 하는 대신 [NPM 패키지](https://www.npmjs.com/package/applicationinsights) 를 사용 하 여 SDK를 고유한 JavaScript 파일의 일부로 포함할 수 있습니다. SDK는 자체 스크립트 내의 다른 패키지만 됩니다.

> [!NOTE]
> NPM 패키지를 사용 하는 경우 코드 분할 및 축소를 지원 하기 위해 [JavaScript 번들러](https://www.bing.com/search?q=javascript+bundler) 형식을 사용 하는 것이 좋습니다.

코드 조각과 마찬가지로 사용자 고유의 스크립트 (SDK NPM 패키지를 사용 하거나 사용 하지 않음)는 여기에 나열 된 것과 동일한 차단 문제로 인해 영향을 받을 수 있습니다. 따라서 응용 프로그램, 사용자 및 프레임 워크에 따라 코드 조각의 논리와 유사한 항목을 구현 하 여 이러한 문제를 감지 하 고 보고 하는 것이 좋을 수도 있습니다.


## <a name="next-steps"></a>다음 단계 
- [GitHub에서 문제를 제출 하 여 추가 도움 받기](https://github.com/Microsoft/ApplicationInsights-JS/issues)
- [웹 페이지 사용 모니터링](javascript.md)
