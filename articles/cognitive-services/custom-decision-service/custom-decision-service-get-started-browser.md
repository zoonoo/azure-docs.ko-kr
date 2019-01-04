---
title: 브라우저에서 API 호출 - Custom Decision Service
titlesuffix: Azure Cognitive Services
description: 브라우저에서 Custom Decision Service로 직접 API 호출을 수행하여 웹 페이지를 최적화하는 방법입니다.
services: cognitive-services
author: slivkins
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: slivkins
ms.openlocfilehash: 01f75d333e3eea5af35ca57bd44bc76148a481f2
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48867618"
---
# <a name="call-api-from-a-browser"></a>브라우저에서 API 호출

이 문서는 브라우저에서 직접 Azure Custom Decision Service API를 호출하는 데 도움이 됩니다.

먼저 [애플리케이션을 등록](custom-decision-service-get-started-register.md)해야 합니다.

이제 시작하겠습니다. 애플리케이션은 여러 문서 페이지로 연결되는 앞 페이지가 있는 것으로 모델링됩니다. 앞 페이지는 Custom Decision Service를 사용하여 문서 페이지의 순서를 지정합니다. 앞 페이지의 HTML 헤드에 다음 코드를 삽입합니다.

```html
// Define the "callback function" to render UI
<script> function callback(data) { … } </script>

// call Ranking API, after callback() is defined
<script src="https://ds.microsoft.com/api/v2/<appId>/rank/<actionSetId>" async></script>
```

`data` 인수에는 렌더링할 URL의 순위가 포함됩니다. 자세한 내용은 참조 [API](custom-decision-service-api-reference.md)를 참조하세요.

최상위 문서의 사용자 클릭을 처리하려면 앞 페이지에서 다음 코드를 호출합니다.

```javascript
// call Reward API to report a click
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

여기서 `data`는 `callback()` 함수에 대한 인수입니다. 구현 예제는 이 [자습서](custom-decision-service-tutorial-news.md#use-the-apis)에서 확인할 수 있습니다.

마지막으로, Custom Decision Service에서 고려할 문서(작업) 목록을 반환하는 작업 집합 API를 제공해야 합니다. 이 API를 다음과 같이 RSS 피드로 구현합니다.

```xml
<rss version="2.0">
<channel>
   <item>
      <title><![CDATA[title (possibly with url) ]]></title>
      <link>url</link>
      <pubDate>Thu, 27 Apr 2017 16:30:52 GMT</pubDate>
    </item>
   <item>
       ....
   </item>
</channel>
</rss>
```

여기서 각 최상위 `<item>` 요소는 문서를 설명합니다. `<link>`는 필수이며, Custom Decision Service에서 작업 ID로 사용됩니다. 문서 수가 15개를 초과하는 경우 표준 RSS 형식으로 `<date>`를 지정합니다. 15개의 최근 문서가 사용됩니다. `<title>`은 선택 사항이며, 문서에 대한 텍스트 관련 기능을 만드는 데 사용됩니다.

## <a name="next-steps"></a>다음 단계

* 자세한 예제를 보려면 [자습서](custom-decision-service-tutorial-news.md)를 진행합니다.
* 제공되는 기능에 대한 자세한 내용은 참조 [API](custom-decision-service-api-reference.md)를 참조하세요.