---
title: Project URL Preview란?
titlesuffix: Azure Cognitive Services
description: Project URL Preview 소개.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: overview
ms.date: 03/16/2018
ms.author: rosh
ms.openlocfilehash: 7022c3b2d2f3618d55b0a70d2690abf1497ec6a6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "61473182"
---
# <a name="what-is-project-url-preview"></a>Project URL Preview란?
URL Preview 엔드포인트는 URL 쿼리 매개 변수를 사용하고 대상 리소스 이름, 간략한 설명 및 미리 보기에 표시할 이미지에 대한 링크로 JSON 응답을 반환합니다. 이 응답에는 URL이 성인, 해적판 또는 기타 불법 콘텐츠를 포함하는지 여부를 나타내는 [isFamilyFriendly](url-preview-reference.md#query-parameters) 플래그도 포함됩니다. 

URL 미리 보기 결과를 가져오려면 GET 요청을 제출하고 유효한 토큰을 사용하여 *Ocp-Apim-Subscription-Key* 헤더를 포함합니다.  
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

```
응답 
```
HTTP Headers:
BingAPIs-TraceId: 3CC74C94769440C0851D9DF0869FCE7F
BingAPIs-SessionId: 52219085A6364692958C9C83983A0DBA
X-MSEdge-ClientID: 13D44DC2DE946B4B0F25460CDF036AD6
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 3CC74C94769440C0851D9DF0869FCE7F Ref B: CO1EDGE0315 Ref C: 2018-04-11T18:47:40Z
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile typing",
  "url": "https:\/\/swiftkey.com\/en",
  "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https:\/\/swiftkey.com\/images\/og\/default.jpg"
  }
}

```
## <a name="scenarios"></a>시나리오 

URL Preview API는 웹 리소스에 대한 간략한 설명을 지원합니다. 개발자는 이 API를 사용하여 풍부한 미리 보기 환경을 빌드합니다.  사용자는 웹 페이지, 뉴스, 블로그, 포럼 등을 공유하거나 책갈피할 수 있습니다. 이 API는 콘텐츠 조정에도 사용할 수 있습니다.    

애플리케이션은 URL Preview를 사용하여 미리 보기 URL에 할당된 쿼리를 통해 엔드포인트에 웹 요청을 보냅니다.  JSON 응답은 이름, 리소스의 설명, *familyFriendly* 플래그 및 전체 리소스와 대표 이미지에 대한 온라인 액세스를 제공하는 링크 등의 미리 보기 정보를 포함합니다. 

## <a name="terms-of-use"></a>사용 약관
Project URL Preview에서 데이터만 사용하여 최종 사용자가 시작한 URL 공유에서 미리 보기 코드 조각 및 원본 사이트에 하이퍼링크로 연결된 썸네일 이미지를 소셜 미디어, 챗봇 또는 유사한 제품에 표시합니다. Project URL Preview에서 받은 모든 데이터를 복사, 저장 또는 캐시하지 마세요. 웹 사이트 또는 콘텐츠 소유자에서 받을 수 있는 미리 보기를 사용하지 않도록 설정하려면 모든 요청을 적용합니다.

사용자 또는 사용자를 대신한 타사는 모든 타사 서비스나 기능을 테스트, 개발, 교육, 배포 또는 사용하게 하기 위해 URL Preview API의 모든 데이터를 사용, 유지, 저장, 캐시, 공유 또는 배포할 수 없습니다. 

## <a name="throttling-requests"></a>제한 요청

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>다음 단계
- [C# 빠른 시작](csharp.md)
- [Java 빠른 시작](java-quickstart.md)
- [JavaScript 빠른 시작](javascript.md)
- [Node 빠른 시작](node-quickstart.md)
- [Python 빠른 시작](python-quickstart.md)
