---
title: Project URL Preview 엔드포인트
titlesuffix: Azure Cognitive Services
description: URL Preview 엔드포인트의 요약입니다.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: reference
ms.date: 03/29/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 7cc52493ec0e2b9c81d52da4bb22102c2c7e5e5c
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55861990"
---
# <a name="project-url-preview-endpoint"></a>Project URL Preview 엔드포인트

URL Preview API에는 엔드포인트 하나가 포함됩니다.

## <a name="endpoint"></a>엔드포인트
URL Preview를 가져오려면 다음 엔드포인트에 요청을 보냅니다. 다른 사양에 대한 헤더 및 URL 매개 변수를 사용합니다.

GET:
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

```

### <a name="query-parameters"></a>쿼리 매개 변수
|Name|값|Type|필수|  
|----------|-----------|----------|--------------|  
|q|미리 보기 URL|문자열 |예|
|safeSearch|해적판 불법 성인 콘텐츠는 오류 코드 400으로 차단되고 *isFamilyFriendly* 플래그는 반환되지 않습니다. <p>합법적 성인 콘텐츠에 대한 동작은 아래와 같습니다. 상태 코드 200을 반환하며 *isFamilyFriendly* 플래그가 false로 설정됩니다.<ul><li>safeSearch=strict: 제목, 설명, URL 및 이미지는 반환되지 않습니다.</li><li>safeSearch=moderate: 설명이 포함된 이미지를 제외한 제목, URL 및 설명을 가져옵니다.</li><li>safeSearch=off: 제목, URL, 설명 및 이미지 등의 모든 응답 개체/요소를 가져옵니다.</li></ul> |문자열|필요하지 않습니다. </br> 기본값은 safeSearch=strict입니다.| 

## <a name="response-object"></a>응답 개체

다음의 `name`, `url`, `description`, `isFamilyFriendly` 및 `primaryImageOfPage` 예제에 표시된 대로 응답은 HTTP 헤더와 특성이 있는 WebPage 개체를 포함합니다.

```
BingAPIs-TraceId: 15AFE52A97AA422F960433A94803F6CE
BingAPIs-SessionId: 40587764F42142D3A8BA99F66B2B3BB6
X-MSEdge-ClientID: 0389E3EDED106B5E1424E82FEC436A56
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 15AFE52A97AA422F960433A94803F6CE Ref B: PAOEDGE0418 Ref C: 2018-03-30T16:36:27Z
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile ...",
  "url": "https://swiftkey.com/",
   "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https://swiftkey.com/images/og/default.jpg"
  }
}

```

## <a name="next-steps"></a>다음 단계
- [C# 빠른 시작](csharp.md)
- [Java 빠른 시작](java-quickstart.md)
- [JavaScript 빠른 시작](javascript.md)
- [Node 빠른 시작](node-quickstart.md)
- [Python 빠른 시작](python-quickstart.md)
