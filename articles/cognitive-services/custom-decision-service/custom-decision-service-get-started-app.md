---
title: 앱에서 API 호출 - Azure Cognitive Services | Microsoft Docs
description: 스마트폰 앱에서 API를 호출하는 경우 Azure Custom Decision Service를 시작하는 방법입니다.
services: cognitive-services
author: slivkins
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/10/2018
ms.author: slivkins
ms.reviewer: marcozo, alekh
ms.openlocfilehash: 2d02b0deaaa701dd0b4818638827c04e2c946558
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35382914"
---
# <a name="call-api-from-an-app"></a>앱에서 API 호출

스마트폰 앱에서 Azure Custom Decision Service API를 호출합니다. 이 문서에서는 몇 가지 기본적인 옵션을 사용하여 시작하는 방법을 설명합니다.

먼저 [응용 프로그램을 등록](custom-decision-service-get-started-register.md)해야 합니다.

스마트폰 앱에서 Custom Decision Service에 대해 수행하는 API 호출에는 2가지가 있습니다. 하나는 콘텐츠의 순위 목록을 가져오기 위한 Ranking API 호출이고, 다른 하나는 보상을 보고하기 위한 Reward API 호출입니다. [cURL](https://en.wikipedia.org/wiki/CURL)의 샘플 호출은 다음과 같습니다.

자세한 내용은 참조 [API](custom-decision-service-api-reference.md)를 참조하세요.

먼저 Ranking API를 호출합니다. 작업 집합 ID가 포함된 `<request.json>` 파일을 만듭니다. 이 ID는 포털에 입력한 해당 RSS 또는 Atom 피드의 이름입니다.

```json
{"decisions":
     [{ "actionSets":[{"id":{"id":"<actionSetId>"}}] }]}
```

많은 작업 집합을 다음과 같이 지정할 수 있습니다.

```json
{"decisions":
    [{ "actionSets":[{"id":{"id":"<actionSetId1>"}},
                     {"id":{"id":"<actionSetId2>"}}] }]}
```

그러면 이 JSON 파일은 순위 요청의 일부로 전송됩니다.

```shell
curl -d @<request.json> -X POST https://ds.microsoft.com/api/v2/<appId>/rank --header "Content-Type: application/json"
```

여기서 `<appId>`는 포털에 등록된 응용 프로그램의 이름입니다. 응용 프로그램에서 렌더링할 수 있는 콘텐츠 항목의 정렬된 집합이 수신됩니다. 샘플 반환 결과는 다음과 같습니다.

```json
[{ "ranking":[{"id":"actionId3"}, {"id":"actionId1"}, {"id":"actionId2"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "rewardAction":"actionId3",
   "actionSets":[{"id":"<actionSetId1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<actionSetId2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]}]
```

반환 결과의 첫 번째 부분에는 작업 ID로 지정된 정렬된 작업 목록이 포함되어 있습니다. 문서의 경우, 작업 ID가 URL입니다. 전체 요청에는 시스템에서 생성된 고유한 `<eventId>`도 포함되어 있습니다.

나중에 이 이벤트에서 첫 번째 콘텐츠 항목(`<actionId3>`)에 대한 클릭을 확인했는지를 지정할 수 있습니다. 그런 후 이 `<eventId>`에 대한 보상을 다음과 같은 다른 요청을 사용하여 Reward API를 통해 Custom Decision Service에 보고할 수 있습니다.

```shell
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST
```

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