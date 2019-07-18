---
title: API 참조 - Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Custom Decision Service에 대한 완벽한 API 가이드입니다.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: slivkins
ms.openlocfilehash: be9966f5d8e8d94aa3f49aac91b35b105195b108
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60510949"
---
# <a name="api"></a>API

Azure Custom Decision Service에서는 각 의사 결정에 대해 호출되는 두 가지 API를 제공합니다. [순위 API](#ranking-api)는 작업의 순위를 입력하고 [보상 API](#reward-api)는 보상을 출력합니다. 또한 [작업 설정 API](#action-set-api-customer-provided)를 제공하여 Azure Custom Decision Service에 작업을 지정합니다. 이 아티클에는 세 가지 API가 포함됩니다. 아래에서 일반적인 시나리오를 사용하여 Custom Decision Service가 아티클의 순위를 최적화하는 경우를 보여줍니다.

## <a name="ranking-api"></a>순위 API

순위 API는 표준 [JSONP](https://en.wikipedia.org/wiki/JSONP) 스타일 통신 패턴을 사용하여 대기 시간을 최적화하고 [동일 원본 정책](https://en.wikipedia.org/wiki/Same-origin_policy)을 우회합니다. 후자는 JavaScript가 페이지의 원본 이외의 데이터를 인출하지 않도록 금지합니다.

아티클의 개인 설정된 목록을 표시하는 기본 페이지의 HTML 헤드에 이 코드 조각을 삽입합니다.

```html
// define the "callback function" to render UI
<script> function callback(data) { … } </script>
// "data" provides the ranking of URLs, see example below.

// call to Ranking API
<script src="https://ds.microsoft.com/api/v2/<appId>/rank/<actionSetId>?<parameters>" async></script>
// action set id is the name of the corresponding RSS/Atom feed.

// same call with multiple action sets:
// <script src="https://ds.microsoft.com/api/v2/<appId>/rank/<A1>/<A2>/.../<An>?<parameters>" async></script>
```

> [!IMPORTANT]
> 콜백 함수는 순위 API를 호출하기 전에 정의되어야 합니다.

> [!TIP]
> 대기 시간을 개선하기 위해 순위 API가 `https://ds.microsoft.com/api/v2/<appId>/rank/*`에서처럼 HTTPS 대신 HTTP를 통해 노출됩니다.
> 그러나 기본 페이지가 HTTPS를 통해 제공되는 경우 HTTPS 엔드포인트를 사용해야 합니다.

매개 변수를 사용하지 않는 경우 순위 API의 HTTP 응답은 JSONP 형식 문자열입니다.

```json
callback({
   "ranking":[{"id":"url1"}, {"id":"url2"}, {"id":"url3"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<A2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]});
```

그런 다음, 브라우저는 이 문자열을 `callback()` 함수에 대한 호출로 실행합니다.

앞의 예제에서 콜백 함수에 대한 매개 변수에는 다음 스키마가 있습니다.

- `ranking`은 표시되는 URL의 순위를 제공합니다.
- `eventId`는 Custom Decision Service에서 내부적으로 사용되며 해당하는 항목을 클릭하여 이 순위와 일치시킵니다.
- `appId`를 사용하면 콜백 함수가 동일한 웹 페이지에서 실행하는 Custom Decision Service의 여러 애플리케이션을 구별할 수 있습니다.
- `actionSets`는 마지막으로 성공한 새로 고침의 UTC 타임스탬프와 함께 순위 API 호출에 사용되는 각 작업 집합을 나열합니다. Custom Decision Service는 작업 집합 피드를 주기적으로 새로 고칩니다. 예를 들어 작업 집합 중 일부가 현재 사용되지 않는 경우 콜백 함수는 해당 기본 순위로 대체해야 합니다.

> [!IMPORTANT]
> 지정된 작업 집합은 처리되고 정리되어 아티클의 기본 순위를 구성합니다. 그런 다음, 기본 순서는 HTTP 응답에서 다시 정렬되고 반환됩니다. 여기에 기본 순서가 정의됩니다.
>
> - 각 작업 집합 내에서 아티클은 15개의 최신 아티클로 정리됩니다(15개 넘게 반환되는 경우).
> - 여러 작업 직합이 지정되면 API 호출에서와 동일한 순서로 병합됩니다. 아티클의 원본 순서는 각 작업 집합 내에서 유지됩니다. 이전 복사본을 우선하여 중복이 제거됩니다.
> - 첫 번째 `n` 아티클은 병합된 문서 목록과 구분됩니다. 기본적으로 `n=20`입니다.

### <a name="ranking-api-with-parameters"></a>매개 변수를 사용하는 순위 API

순위 API를 사용하면 다음과 같은 매개 변수가 허용됩니다.

- `details=1` 및 `details=2`는 `ranking`에 나열된 각 아티클에 대한 추가 세부 정보를 삽입합니다.
- `limit=<n>`는 기본 순위에서 최대 아티클 수를 지정합니다. `n`은 `2`와 `30` 사이여야 합니다.(또는 각각 `2` 또는 `30`으로 자릅니다.)
- `dnt=1`은 사용자 쿠키를 사용하지 않습니다.

표준 쿼리 문자열 구문에서 매개 변수를 결합할 수 있습니다(예: `details=2&dnt=1`).

> [!IMPORTANT]
> 고객이 쿠키 배너에 동의할 때까지 유럽의 기본 설정은 `dnt=1`이어야 합니다. 미성년자를 대상으로 하는 웹 사이트에 대한 기본 설정도 있어야 합니다. 자세한 내용은 [사용 약관](https://www.microsoft.com/cognitive-services/en-us/legal/CognitiveServicesTerms20160804)을 참조하세요.

`details=1` 요소가 작업 집합 API에서 제공되는 경우 각 아티클의 `guid`를 삽입합니다. HTTP 응답:

```json
callback({
   "ranking":[{"id":"url1","details":[{"guid":"123"}]},
              {"id":"url2","details":[{"guid":"456"}]},
              {"id":"url3","details":[{"guid":"789"}]}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"timeStamp1"},
                 {"id":"<A2>","lastRefresh":"timeStamp2"}]});
```

`details=2` 요소는 아티클의 SEO 메타태그 [기능 개발 코드](https://github.com/Microsoft/mwt-ds/tree/master/Crawl)에서 Custom Decision Service를 추출하는 자세한 세부 정보를 추가합니다.

- `<meta property="og:title" content="..." />` 또는 `<meta property="twitter:title" content="..." />` 또는 `<title>...</title>`의 `title`
- `<meta property="og:description" ... />` 또는 `<meta property="twitter:description" content="..." />` 또는 `<meta property="description" content="..." />`의 `description`
- `<meta property="og:image" content="..." />`의 `image`
- `<meta name=”microsoft:ds_id” content="..." />`의 `ds_id`

HTTP 응답:

```json
callback({
   "ranking":[{"id":"url1","details":<details>},
              {"id":"url2","details":<details>},
              {"id":"url3","details":<details>}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"timeStamp1"},
                 {"id":"<A2>","lastRefresh":"timeStamp2"}]});
```

`<details>` 요소:

```json
[{"guid":"123"}, {"description":"some text", "ds_id":"234", "image":"ImageUrl1", "title":"some text"}]
```

## <a name="reward-api"></a>보상 API

Custom Decision Service는 맨 위 슬롯에서만 클릭을 사용합니다. 클릭할 때마다 1을 보상으로 해석됩니다. 클릭하지 않을 때마다 0을 보상으로 해석됩니다. 클릭은 [순위 API](#ranking-api) 호출에서 생성한 이벤트 ID를 사용하여 해당하는 순위와 일치됩니다. 필요한 경우 이벤트 ID는 세션 쿠키를 통해 전달될 수 있습니다.

맨 위 슬롯에서 클릭을 처리하려면 이 코드를 기본 페이지에 배치합니다.

```javascript
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

이전에 설명한 대로 여기서 `data`는 `callback()` 함수에 대한 인수입니다. 클릭 처리 코드에서 `data`를 사용하려면 주의해야 합니다. 이 [자습서](custom-decision-service-tutorial-news.md#use-the-apis)에 예제가 나와 있습니다.

테스트 전용의 경우 보상 API는 [cURL](https://en.wikipedia.org/wiki/CURL)을 통해 호출될 수 있습니다.

```sh
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST -d 1 -H "Content-Type: application/json"
```

200(확인)이라는 HTTP 응답이 예상됩니다. (Azure Storage 계정 키를 포털에 제공한 경우) 로그에서 이 이벤트에 대한 1의 보상을 볼 수 있습니다.

## <a name="action-set-api-customer-provided"></a>작업 집합 API(고객 제공)

상위 수준에서 작업 집합 API는 아티클(작업) 목록을 반환합니다. 각 아티클은 해당 URL 및 아티클 제목 및 게시 날짜(선택 사항)로 지정됩니다. 포털에서 설정된 몇 가지 작업을 지정할 수 있습니다. 다양한 작업 집합 API는 구분된 URL로 각 작업 집합에 사용되어야 합니다.

각 작업 직합 API는 RSS 피드 또는 Atom 피드라는 두 가지 방법으로 구현할 수 있습니다. 이 중 하나는 표준을 준수하고 올바른 XML을 반환해야 합니다. RSS의 경우 예제는 다음과 같습니다.

```xml
<rss version="2.0">
<channel>
   <item>
      <title><![CDATA[title (possibly with url) ]]></title>
      <link>url</link>
      <guid>guid (could be a your internal database id)</guid>
      <pubDate>date</pubDate>
    </item>
   <item>
       ....
   </item>
</channel>
</rss>
```

각 최상위 `<item>` 요소는 작업을 설명합니다.

- `<link>`는 필수이며 작업 ID로 사용됩니다.
- 15개 이하인 경우 `<date>`를 무시합니다. 그렇지 않으면 필수입니다.
  - 15개를 넘는 항목이 있는 경우 15개의 최신 항목을 사용합니다.
  - RSS 또는 Atom에 대해 각각 표준 형식이여야 합니다.
    - RSS의 경우 [RFC 822](https://tools.ietf.org/html/rfc822)(예: `"Fri, 28 Apr 2017 18:02:06 GMT"`)
    - Atom의 경우 [RFC 3339](https://tools.ietf.org/html/rfc3339)(예: `"2016-12-19T16:39:57-08:00"`)
- `<title>`은 선택 사항이며 아티클을 설명하는 기능을 생성하는 데 사용됩니다.
- `<guid>`는 선택 사항이며 시스템을 통해 콜백 함수에 전달됩니다(`?details` 매개 변수가 순위 API 호출에서 지정된 경우).

`<item>` 내의 다른 요소는 무시됩니다.

Atom 피드 버전은 동일한 XML 구문 및 규칙을 사용합니다.

> [!TIP]
> 시스템에서 고유한 아티클 ID를 사용하는 경우 `<guid>`를 사용하여 콜백 함수에 전달될 수 있습니다.
