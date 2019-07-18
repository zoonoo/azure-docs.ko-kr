---
title: '자습서: Azure Time Series Insights JavaScript 클라이언트 라이브러리 살펴보기 | Microsoft Docs'
description: Azure Time Series Insights JavaScript 클라이언트 라이브러리 및 관련 프로그래밍 모델에 대해 알아봅니다.
author: ashannon7
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: dpalled
ms.custom: seodec18
ms.openlocfilehash: c6cfd2069851138d738b1533eaab74d9d7aedda6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243993"
---
# <a name="tutorial-explore-the-azure-time-series-insights-javascript-client-library"></a>자습서: Azure Time Series Insights JavaScript 클라이언트 라이브러리

웹 개발자가 Time Series Insights에 저장된 데이터를 쿼리하고 시각화하는 데 도움이 되는 JavaScript D3 기반 Time Series Insights 클라이언트 라이브러리가 개발되었습니다. 이 자습서는 호스팅되는 샘플 앱을 사용하여 Time Series Insights 클라이언트 라이브러리 및 프로그래밍 모델을 안내합니다.

이 자습서에서는 라이브러리를 사용하는 방법, Time Series Insights 데이터에 액세스하는 방법, 차트 컨트롤을 사용하여 데이터를 렌더링하고 시각화하는 방법에 대해 자세히 설명합니다. 또한 다른 종류의 그래프를 실험해 보면서 데이터를 시각화하는 방법도 알아봅니다. 자습서가 완료되면 클라이언트 라이브러리를 사용하여 Time Series Insights 기능을 사용자 고유의 웹앱에 통합할 수 있습니다.

특히 다음에 대해 알아봅니다.

> [!div class="checklist"]
> * Time Series Insights 샘플 애플리케이션
> * Time Series Insights JavaScript 클라이언트 라이브러리
> * 샘플 애플리케이션에서 라이브러리를 사용하여 Time Series Insights 데이터를 시각화하는 방법.

> [!NOTE]
> * 이 자습서에서는 체험 서비스로 호스팅되는 [Time Series Insights 웹 데모](https://insights.timeseries.azure.com/clientsample)를 사용합니다.
> * Time Series Insights 샘플 앱 소스 파일은 [GitHub 샘플 리포지토리](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial)에 제공됩니다.
> * [Time Series Insights 클라이언트 참조 설명서](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)를 참조하세요.

## <a name="video"></a>비디오

이 비디오에서는 오픈 소스 Time Series Insights JavaScript SDK를 소개합니다.
<br /><br />

> [!VIDEO https://www.youtube.com/embed/X8sSm7Pl9aA]

## <a name="prerequisites"></a>필수 조건

이 자습서에서는 브라우저의 **개발자 도구** 기능을 사용합니다. 최신 웹 브라우저([Microsoft Edge](/microsoft-edge/devtools-guide), [Chrome](https://developers.google.com/web/tools/chrome-devtools/), [Firefox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools), [Safari](https://developer.apple.com/safari/tools/) 등)는 일반적으로 키보드의 F12 바로 가기 키를 통해 **Web Inspector 보기**에 액세스합니다. 보기에 액세스하는 또 다른 방법은 마우스 오른쪽 단추로 웹 페이지를 클릭한 다음, **요소 검사**를 선택하는 것입니다.

## <a name="time-series-insights-sample-application"></a>Time Series Insights 샘플 애플리케이션

이 자습서 전체에서 체험 서비스로 호스팅되는 Time Series Insights 샘플 앱을 사용하여 애플리케이션 이면의 소스 코드 및 Time Series Insights JavaScript 클라이언트 라이브러리를 살펴봅니다. 샘플 앱을 사용하면 JavaScript에서 Time Series Insights와 상호 작용하고 차트와 그래프를 통해 데이터를 시각화하는 방법을 알아볼 수 있습니다.

1. [Time Series Insights 샘플 애플리케이션](https://insights.timeseries.azure.com/clientsample)으로 이동합니다. 다음 로그인 프롬프트가 표시됩니다.

   [![Time Series Insights 클라이언트 샘플 로그인 프롬프트](media/tutorial-explore-js-client-lib/tcs-sign-in.png)](media/tutorial-explore-js-client-lib/tcs-sign-in.png#lightbox)

1. **로그인**을 선택하고 자격 증명을 입력하거나 선택합니다. 엔터프라이즈 조직 계정(Azure Active Directory) 또는 개인 계정(Microsoft 계정)을 사용합니다.

   [![Time Series Insights 클라이언트 샘플 자격 증명 프롬프트](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png#lightbox)

1. 로그인하면 Time Series Insights 데이터로 채워진 차트를 보여 주는 페이지가 표시됩니다. 사용자 계정 및 **로그아웃** 옵션이 오른쪽 위 모서리에 표시됩니다.

   [![Time Series Insights 클라이언트 샘플 로그인 후 기본 페이지](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png#lightbox)

### <a name="page-source-and-structure"></a>페이지 원본 및 구조

먼저 렌더링된 웹 페이지의 [HTML 및 JavaScript 소스 코드](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html)를 살펴보겠습니다.

1. 브라우저에서 **개발자 도구**를 엽니다. 현재 페이지를 구성하는 HTML 요소, 일명 HTML 또는 DOM 트리를 검사합니다.

1. `<head>` 및 `<body>` 요소를 확장하고 다음 섹션을 살펴봅니다.

   * `<head>` 요소 아래에서 앱을 실행할 수 있게 하는 페이지 메타데이터 및 종속성을 찾을 수 있습니다.
     * Azure ADAL(Active Directory 인증 라이브러리) 파일인 *adal.min.js*를 참조하는 데 사용되는 `<script>` 요소. ADAL은 OAuth 2.0 인증(로그인)과 API 액세스를 위한 토큰 획득을 지원하는 JavaScript 라이브러리입니다.
     * *sampleStyles.css* 및 *tsiclient.css*와 같은 스타일 시트(*CSS*라고도 함)에 대한 여러 `<link>` 요소. 스타일 시트는 시각적 페이지 스타일 세부 정보(예: 색, 글꼴 및 간격)를 제어합니다.
     * Time Series Insights JavaScript 클라이언트 라이브러리인 *tsiclient.js*를 참조하는 데 사용되는 `<script>` 요소. 페이지는 라이브러리를 사용하여 Time Series Insights 서비스 API를 호출하고 차트 컨트롤을 페이지에 렌더링합니다.

     >[!NOTE]
     > * ADAL JavaScript 라이브러리에 대한 소스 코드는 [azure-activedirectory-library-for-js 리포지토리](https://github.com/AzureAD/azure-activedirectory-library-for-js)에서 사용할 수 있습니다.
     > * Time Series Insights JavaScript 클라이언트 라이브러리에 대한 소스 코드는 [tsiclient 리포지토리](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial)에서 사용할 수 있습니다.

   * `<body>` 요소 아래에 페이지 항목 및 다른 `<script>` 요소의 레이아웃을 정의하기 위한 `<div>` 요소가 있습니다.
     * 첫 번째 `<div>` 요소는 **로그인** 대화 상자를 지정합니다(`id="loginModal"`).
     * 두 번째 `<div>` 요소는 다음에 대한 상위 요소 역할을 합니다.
       * 페이지 맨 위 부근의 상태 메시지 및 로그인 정보(`class="header"`)를 위해 사용되는 헤더 `<div>` 요소.
       * 차트를 포함하여 페이지 본문의 나머지 요소에 대한 `<div>` 요소(`class="chartsWrapper"`)
       * 페이지를 제어하는 데 사용되는 JavaScript가 포함된 `<script>` 섹션

   [![개발자 도구가 있는 Time Series Insights 클라이언트 샘플](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

1. `<div class="chartsWrapper">` 요소를 확장하면 더 많은 하위 `<div>` 요소를 찾을 수 있습니다. 이러한 요소는 각 차트 컨트롤 예제의 위치를 지정하는 데 사용됩니다. 다음과 같이 각 차트 예제마다 하나씩 여러 개의 `<div>` 요소가 있습니다.

   * 첫 번째(`class="rowOfCardsTitle"`) 요소는 차트가 보여 주는 내용을 요약하는 설명 텍스트를 포함합니다. 예: `Static Line Charts With Full-Size Legends.`
   * 두 번째(`class="rowOfCards"`) 요소는 실제 차트 컨트롤을 한 행 안에 배치하는 추가 자식 `<div>` 요소를 포함한 부모 요소입니다.

   [![본문 div 요소](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png#lightbox)

1. `<div class="chartsWrapper">` 요소 바로 아래의 `<script type="text/javascript">` 요소를 펼칩니다. 페이지 수준 JavaScript 섹션의 시작 부분은 모든 페이지 논리(인증, Time Series Insights 서비스 API 호출, 차트 컨트롤 렌더링 등)를 처리하는 데 사용됩니다.

   [![본문 스크립트](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png#lightbox)

## <a name="time-series-insights-javascript-client-library-concepts"></a>Time Series Insights JavaScript 클라이언트 라이브러리에 대한 개념

Time Series Insights 클라이언트 라이브러리(*tsclient.js*)는 다음 두 가지 중요한 JavaScript 기능에 대한 추상화를 제공합니다.

* **Time Series Insights 쿼리 API를 호출하는 래퍼 메서드**: 집계 식을 사용하여 Time Series Insights 데이터를 쿼리하는 데 사용할 수 있는 REST API입니다. 이러한 메서드는 라이브러리의 TsiClient.Server 네임스페이스 아래에 구성됩니다.

* **여러 유형의 차트 작성 컨트롤을 만들고 채우는 메서드**: 웹 페이지에서 Time Series Insights 집계 데이터를 렌더링하는 데 사용할 수 있는 메서드입니다. 이러한 메서드는 라이브러리의 TsiClient.UX 네임스페이스 아래에 구성됩니다.

이러한 단순화를 통해 개발자는 Time Series Insights 데이터로 구동되는 UI 그래프 및 차트 구성 요소를 더 쉽게 빌드할 수 있습니다.

### <a name="authentication"></a>인증

[Time Series Insights 샘플 애플리케이션](https://insights.timeseries.azure.com/clientsample)은 ADAL OAuth 2.0 사용자 인증을 지원하는 단일 페이지 앱입니다.

1. 인증에 ADAL을 사용하는 경우 클라이언트 앱을 Azure AD(Azure Active Directory)에 등록해야 합니다. 실제로, 단일 페이지 앱은 [OAuth 2.0 암시적 허용 흐름](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-implicit-grant-flow)을 사용하도록 등록됩니다.
1. 애플리케이션에서 런타임에 일부 등록 속성을 지정해야 합니다. 이러한 속성에는 클라이언트 GUID(`clientId`) 및 리디렉션 URI(`postLogoutRedirectUri`)가 포함됩니다.
1. 나중에 앱은 Azure AD에서 *액세스 토큰*을 요청합니다. 특정 서비스/API 식별자(https:\//api.timeseries.azure.com)의 제한된 권한 세트에 대한 액세스 토큰이 발급됩니다. 토큰 권한은 로그인한 사용자를 대신하여 발급됩니다. 서비스/API의 식별자는 앱의 Azure AD 등록에 포함되는 또 다른 속성입니다.
1. ADAL에서 앱에 대한 액세스 토큰이 반환되면 Time Series Insights 서비스 API에 액세스할 때 *전달자 토큰*으로 전달됩니다.

   [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-204&highlight=3-7,34-37)]

> [!TIP]
> Microsoft에서 지원하는 Azure AD 인증 라이브러리에 대한 자세한 내용은 [Azure Active Directory 인증 라이브러리 참조 설명서](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries#microsoft-supported-client-libraries)를 참조하세요.

### <a name="control-identification"></a>컨트롤 식별

제공된 예제에서 `<div>` 요소는 부모 `<body>` 요소에 정렬되어 페이지에 렌더링되는 차트 컨트롤에 대한 적절한 레이아웃을 제공합니다.

각 `<div>` 요소는 차트 컨트롤의 배치 속성 및 시각적 특성을 지정합니다. HTML 요소인 `id` 속성은 시각화된 데이터를 렌더링하고 업데이트하기 위해 특정 컨트롤에 바인딩하는 고유 식별자로 사용됩니다.

### <a name="aggregate-expressions"></a>집계 식

Time Series Insights 클라이언트 라이브러리 API는 집계 식을 사용합니다.

* 집계 식은 *검색 용어*를 하나 이상 생성하는 기능을 제공합니다.

* 클라이언트 API는 검색 범위, `where` 조건자, 측정값 및 `splitBy` 값을 사용하는 다른 데모 앱([Time Series Insights 탐색기](https://insights.timeseries.azure.com/demo))과 비슷한 기능을 제공하도록 설계되었습니다.

* 대부분의 클라이언트 라이브러리 API는 서비스에서 Time Series Insights 데이터 쿼리를 작성하는 데 사용하는 집계 식의 배열을 사용합니다.

### <a name="call-pattern"></a>호출 패턴

차트 컨트롤 채우기 및 렌더링은 일반적인 패턴을 따릅니다. 이 일반적인 패턴은 샘플 앱 전체에서 관찰할 수 있으며, 클라이언트 라이브러리를 사용할 때 도움이 될 수 있습니다.

1. 하나 이상의 Time Series Insights 집계 식을 포함하도록 `array`를 선언합니다.

   ```javascript
   var aes =  [];
   ```

1. *1* ~ *n*개의 집계 식 개체를 빌드합니다. 그런 다음, 집계 식 배열에 추가합니다.

   ```javascript
   var ae = new tsiClient.ux.aggregateExpression(predicateObject, measureObject, measureTypes, searchSpan, splitByObject, color, alias, contextMenuActions);
   aes.push(ae);
   ```

   **집계 식 매개 변수**

   | 매개 변수 | 설명 | 예 |
   | --------- | ----------- | ------- |
   | `predicateObject` | 데이터 필터링 식 |`{predicateString: "Factory = 'Factory3'"}` |
   | `measureObject`   | 사용되는 측정값의 속성 이름 | `{property: 'Temperature', type: "Double"}` |
   | `measureTypes`    | 원하는 측정값 속성의 집계 | `['avg', 'min']` |
   | `searchSpan`      | 집계 식의 지속 시간 및 간격 크기 | `{from: startDate, to: endDate, bucketSize: '2m'}` |
   | `splitByObject`   | 분할하려는 문자열 속성(선택 사항: null일 수 있음) | `{property: 'Station', type: 'String'}` |
   | `color`         | 렌더링하려는 개체의 색 | `'pink'` |
   | `alias`           | 집계 식에 대한 식별 이름 | `'Factory3Temperature'` |
   | `contextMenuActions` | 시각화에서 시계열 개체에 바인딩할 작업의 배열(선택 사항) | 자세한 내용은 [상황에 맞는 팝업 메뉴](#pop-up-context-menus)를 참조하세요. |

1. TsiClient.Server API를 통해 Time Series Insights 쿼리를 호출하여 집계 데이터를 요청합니다.

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```

   **getAggregates 매개 변수**

   | 매개 변수 | 설명 | 예 |
   | --------- | ----------- | ------- |
   | `token`     | Time Series Insights API에 대한 액세스 토큰 |  `authContext.getTsiToken()`<br />자세한 내용은 [인증](#authentication)을 참조하세요. |
   | `envFQDN`   | Time Series Insights 환경에 대한 FQDN(정규화된 도메인 이름) | Azure Portal에서. 예: `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com` |
   | `aeTsxArray` | Time Series Insights 쿼리 식의 배열 | 앞에서 설명한 대로 `aes` 변수를 사용합니다(`aes.map(function(ae){return ae.toTsx()}`). |

1. 시각화를 위해 Time Series Insights 쿼리에서 반환된 압축된 결과를 JSON으로 변환합니다.

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

1. TsiClient.UX API를 사용하여 차트 컨트롤을 만듭니다. 페이지의 `<div>` 요소 중 하나에 바인딩합니다.

   ```javascript
   var barChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

1. 차트 컨트롤을 변환된 JSON 데이터 개체로 채우고, 컨트롤을 페이지에 렌더링합니다.

   ```javascript
   barChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="render-controls"></a>컨트롤 렌더링

Time Series Insights 클라이언트 라이브러리에서 제공하는 8개의 고유한 기본 제공 분석 컨트롤은 다음과 같습니다.

* **꺾은선형 차트**
* **원형 차트**
* **가로 막대형 차트**
* **heatmap**
* **계층 구조 컨트롤**
* **액세스 가능 그리드**
* **불연속 이벤트 타임라인**
* **상태 전환 타임라인**

### <a name="line-chart-bar-chart-and-pie-chart-examples"></a>꺾은선형 차트, 막대형 차트 및 원형 차트 예제

일부 표준 차트 컨트롤을 렌더링하는 데 사용하는 데모 코드를 살펴봅니다. 해당 컨트롤을 만들기 위한 프로그래밍 모델 및 패턴에 주의하세요. 특히 `// Example 3/4/5` 주석 아래에서 `id` HTML 값(`chart3`, `chart4` 및 `chart5`)을 사용하여 컨트롤을 렌더링하는 HTML을 검사합니다.

[페이지 원본 및 구조 섹션](#page-source-and-structure)의 3단계에서 설명한 대로 차트 컨트롤은 페이지의 행에 정렬됩니다. 각 차트 컨트롤에는 설명이 포함된 제목 행이 있습니다. 다음 예제에서는 세 개의 차트가 `Multiple Chart Types From the Same Data` 제목의 `<div>` 요소 아래에 채워지고, 제목 아래에 있는 세 개의 `<div>` 요소에 바인딩됩니다.

[!code-html[code-sample1-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=59-73&highlight=1,5,9,13)]

다음 JavaScript 코드 섹션에서는 앞에서 설명한 패턴을 사용합니다. 즉 Time Series Insights 집계 식을 작성하고, 이러한 식을 사용하여 Time Series Insights 데이터를 쿼리한 다음, 세 개의 차트를 렌더링합니다. tsiClient.ux 네임스페이스에서 세 가지 차트 종류(`LineChart`, `BarChart` 및 `PieChart`)가 사용됩니다. 이러한 차트 종류는 각 차트를 만들고 렌더링하는 데 사용됩니다. 또한 세 개의 차트는 모두 동일한 `transformedResult` 집계 식 데이터를 사용할 수 있습니다.

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=241-262&highlight=13-14,16-17,19-20)]

차트 3개는 렌더링할 때 다음과 같이 나타납니다.

[![동일한 데이터의 여러 차트 형식](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="advanced-features"></a>고급 기능

Time Series Insights 클라이언트 라이브러리에는 데이터 시각화를 창의적으로 구현하는 데 사용할 수 있는 몇 가지 추가 기능이 있습니다.

### <a name="states-and-events"></a>상태 및 이벤트

한 가지 고급 기능으로 상태 전환 및 불연속 이벤트를 차트에 추가하는 기능이 있습니다. 이 기능은 인시던트 강조 표시, 경고 및 상태 스위치(예: 켜기/끄기 스위치)를 만드는 데 유용합니다.

`// Example 10` 주석 주변의 코드를 확인합니다. 이 코드는 줄 컨트롤을 `Line Charts with Multiple Series Types` 제목 아래에 렌더링하고 `id` HTML 값이 `chart10`인 `<div>` 요소에 바인딩합니다.

다음 단계에서는 이 프로세스를 설명합니다.

1. `events4`라는 구조체에서 추적할 상태 변경 요소를 포함하도록 정의합니다. 구조에는 다음과 같은 요소가 포함되어 있습니다.

   * `Component States`라는 문자열 키.
   * 상태를 나타내는 값 개체의 배열. 각 개체에는 다음이 포함됩니다.
     * JavaScript ISO 타임스탬프를 포함한 문자열 키.
     * 상태의 특성, 즉 색상 및 설명을 포함하는 배열.

1. `Incidents`에 대한 `events5` 구조체에서 추적할 이벤트 요소의 배열을 포함하도록 정의합니다. 배열 구조체는 `events4`에 대해 요약한 구조체와 같은 도형입니다.

1. 꺾은선형 차트를 렌더링하고 차트 옵션 매개 변수가 있는 두 구조체(`events:` 및 `states:`)를 전달합니다. `tooltip:`, `theme:` 또는 `grid:`를 지정하는 다른 옵션 매개 변수에 주의하세요.

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/tutorial/index.html?range=337-389&highlight=5,26,51)]

인시던트를 나타내는 데 사용되는 다이아몬드 마커/팝업 창 및 시간 단위를 따라 색으로 표시되는 막대/팝업 창에서 상태 변경을 나타냅니다.

[![여러 계열 형식이 있는 꺾은선형 차트](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox)

### <a name="pop-up-context-menus"></a>팝업 바로 가기 메뉴

또 다른 고급 기능으로 사용자 지정 바로 가기 메뉴(오른쪽 클릭 팝업 메뉴)를 만드는 기능이 있습니다. 사용자 지정 바로 가기 메뉴는 애플리케이션의 범위 내에서 작업 및 논리적 다음 단계가 논리를 활성화하는 데 유용합니다.

`// Example 13/14/15` 주석 주변의 코드를 살펴봅니다. 이 코드는 처음에는 꺾은선형 차트를 `Line Chart with Context Menu to Create Pie/Bar Chart` 제목 아래에 렌더링합니다. 차트는 `id` HTML 값인 `chart13`인 `<div>` 요소에 바인딩됩니다.

바로 가기 메뉴를 사용할 경우, 꺾은선형 차트는 ID가 `chart14` 및 `chart15`인 `<div>` 요소에 바인딩된 원형 및 가로 막대형 차트를 동적으로 만드는 기능을 제공합니다. 원형 차트와 막대형 차트는 모두 상황에 맞는 메뉴도 사용하여 자체의 기능, 즉 데이터를 원형 차트에서 막대형 차트로 복사하는 기능과 막대형 차트 데이터를 브라우저 콘솔 창에 출력하는 기능을 개별적으로 활성화합니다.

다음 단계에서는 이 프로세스를 설명합니다.

1. 일련의 사용자 지정 작업을 정의합니다. 하나 이상의 요소가 있는 배열을 포함하는 각 작업. 각 요소는 단일 바로 가기 메뉴 항목을 정의합니다.

   * `barChartActions`: 이 작업은 원형 차트에 대한 바로 가기 메뉴를 정의하며, 이 메뉴에는 단일 항목을 정의하는 하나의 요소가 포함됩니다.
     * `name`: 메뉴 항목에 사용되는 텍스트, 즉 "콘솔에 매개 변수 출력"입니다.
     * `action`: 메뉴 항목과 연결된 작업입니다. 이 작업은 언제나 차트를 만드는 데 사용한 집계 식을 기반으로 인수 3개를 취하는 익명 함수입니다. 이 경우 인수를 브라우저 콘솔 창에 씁니다.
       * `ae`: 집계 식 배열입니다.
       * `splitBy`: `splitBy` 값입니다.
       * `timestamp`: 타임스탬프입니다.

   * `pieChartActions`: 이 작업은 가로 막대형 차트에 대한 바로 가기 메뉴를 정의하며, 이 메뉴에는 단일 항목을 정의하는 하나의 요소가 포함됩니다. 도형과 스키마는 앞에서 설명한 `barChartActions` 요소와 동일하지만, `action` 함수는 막대형 차트를 인스턴스화하고 렌더링한다는 점에서 크게 다릅니다. `ae` 인수는 메뉴 항목이 열릴 때 런타임에 전달되는 집계 식 배열을 지정하는 데 사용됩니다. 또한 이 함수는 `barChartActions` 바로 가기 메뉴를 사용하여 `ae.contextMenu` 속성을 설정합니다.
   * `contextMenuActions`: 이 작업은 꺾은선형 차트에 대한 바로 가기 메뉴를 정의하며, 이 메뉴에는 세 개의 메뉴 항목을 정의하는 세 개의 요소가 포함됩니다. 각 요소에 대한 도형과 스키마는 앞에서 설명한 요소와 동일합니다. `barChartActions` 요소와 마찬가지로, 첫 번째 항목은 함수 인수 3개를 브라우저 콘솔 창에 씁니다. `pieChartActions` 요소와 마찬가지로 두 번째의 두 항목은 각각 원형 차트와 막대형 차트를 인스턴스화하고 렌더링합니다. 두 번째 항목 두 개도 각각 `pieChartActions` 및 `barChartActions` 바로 가기 메뉴를 사용하여 해당 `ae.contextMenu` 속성을 설정합니다.

1. 두 개의 집계 식은 `aes` 집계 식 배열로 푸시됩니다. 각 항목에 대해 `contextMenuActions` 배열을 지정합니다. 이 식은 꺾은선형 차트 컨트롤과 함께 사용됩니다.

1. 처음에는 꺾은선형 차트만 렌더링되고, 여기서 원형 차트와 막대형 차트는 모두 런타임에 렌더링될 수 있습니다.

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/tutorial/index.html?range=461-540&highlight=7,16,29,61-64,78)]

다음 스크린샷에서는 각각의 상황에 맞는 팝업 메뉴가 있는 차트를 보여 줍니다. 원형 차트 및 막대형 차트는 꺾은선형 차트 상황에 맞는 메뉴 옵션을 사용하여 동적으로 만들어졌습니다.

[![원형/막대형 차트를 만드는 상황에 맞는 메뉴가 있는 꺾은선형 차트](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>브러시

브러시를 사용하여 확대/축소 및 검색과 같은 작업을 정의하는 시간 범위를 설정할 수 있습니다.

브러시를 설명하는 데 사용되는 코드는 상황에 맞는 팝업 메뉴를 설명하는 `Line Chart with Context Menu to Create Pie/Bar Chart` 예제에 나와 있습니다.

* 브러시 작업은 바로 가기 메뉴와 유사하게 브러시에 대한 일련의 사용자 지정 작업을 정의합니다. 각 작업에는 하나 이상의 요소가 있는 배열이 포함됩니다. 각 요소는 단일 바로 가기 메뉴 항목을 정의합니다.
   * `name`: 메뉴 항목에 사용되는 텍스트, 즉 "콘솔에 매개 변수 출력"입니다.
   * `action`: 메뉴 항목과 연결된 작업이며, 항상 두 개의 인수를 사용하는 익명 함수입니다. 이 경우 인수를 브라우저 콘솔 창에 씁니다.
     * `fromTime`: 브러시 선택 영역의 `from` 타임스탬프입니다.
     * `toTime`: 브러시 선택 영역의 `to` 타임스탬프입니다.

* 브러시 작업은 또 다른 차트 옵션 속성으로 추가됩니다. `brushContextMenuActions: brushActions` 속성이 `linechart.Render` 호출에 전달됩니다.

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/tutorial/index.html?range=526-540&highlight=1,13)]

[![브러시를 사용하여 원형/막대형 차트를 만드는 상황에 맞는 메뉴가 있는 꺾은선형 차트](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png#lightbox)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 로그인하여 Time Series Insights 샘플 애플리케이션 및 해당 원본 살펴보기
> * Time Series Insights JavaScript 클라이언트 라이브러리의 API 사용
> * JavaScript를 사용하여 차트 컨트롤 만들기 및 Time Series Insights 데이터로 채우기

Time Series Insights 샘플 애플리케이션은 데모 데이터 세트를 사용합니다. 사용자 고유의 Time Series Insights 환경 및 데이터 세트를 만드는 방법을 알아보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [자습서: Azure Time Series Insights 환경 만들기](tutorial-create-populate-tsi-environment.md)

또는 Time Series Insights 샘플 애플리케이션 원본 파일을 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [Time Series Insights 샘플 앱 리포지토리](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial)

Time Series Insights 클라이언트 참조 설명서를 참조하세요.

> [!div class="nextstepaction"]
> [Azure Time Series Insights 참조 설명서](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)
