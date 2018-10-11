---
title: Bing Spell Check API에 대한 FAQ(질문과 대답)
titlesuffix: Azure Cognitive Services
description: Azure에서 Bing Spell Check API에 대한 일반적인 질문과 대답을 확인합니다.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: conceptual
ms.date: 07/26/2017
ms.author: heidist
ms.openlocfilehash: e6662ffcbab9ea274a67bc4437ca1600f1625ff1
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801508"
---
# <a name="frequently-asked-questions-about-the-bing-spell-check-api"></a>Bing Spell Check API에 대한 FAQ(질문과 대답)

 Azure에서 Microsoft Cognitive Services용 Bing Spell Check API와 관련된 개념, 코드 및 시나리오에 대한 일반적인 질문에 대한 대답을 찾습니다.

## <a name="how-do-i-get-the-optional-client-headers-when-calling-the-bing-spell-check-api-from-javascript"></a>JavaScript에서 Bing Spell Check API를 호출할 때 선택적 클라이언트 헤더를 가져오려면 어떻게 해야 하나요?

다음 헤더는 선택적이지만 필수 헤더로 간주하는 것이 좋습니다. 이러한 헤더는 Bing Spell Check API가 보다 정확한 결과를 반환하는 데 도움을 줍니다.

- X-Search-Location
- X-MSEdge-ClientID
- X-MSEdge-ClientIP

그러나 JavaScript에서 Bing Spell Check API를 호출하면 브라우저의 기본 제공 보안 기능이 이러한 헤더 값으로의 액세스를 차단할 수 있습니다.

이 문제를 해결하려면 CORS 프록시를 통해 Bing Spell Check API 요청을 수행할 수 있습니다. 이러한 프록시의 응답에는 응답 헤더를 허용 목록에 추가하고 이를 JavaScript에서 사용할 수 있게 해주는 `Access-Control-Expose-Headers` 헤더가 있습니다.

[자습서 앱](tutorials/spellcheck.md)이 선택적 클라이언트 헤더에 액세스할 수 있도록 CORS 프록시를 쉽게 설치할 수 있습니다. 먼저 [Node.js가 없는 경우 설치](https://nodejs.org/en/download/)합니다. 그런 다음, 명령 프롬프트에서 다음 명령을 입력합니다.

    npm install -g cors-proxy-server

다음으로, HTML 파일에서 Bing Spell Check API 엔드포인트를 다음으로 변경합니다.

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/

마지막으로 다음 명령을 사용하여 CORS 프록시를 시작합니다.

    cors-proxy-server

자습서 앱을 사용하는 동안에는 명령 창을 열어 두세요. 창을 닫으면 프록시가 중지됩니다. 검색 결과 아래의 확장 가능한 HTTP 헤더 섹션에서 여러 `X-MSEdge-ClientID` 헤더를 볼 수 있으며 요청마다 동일한지 확인합니다.

## <a name="next-steps"></a>다음 단계

없는 기능 또는 특징에 대해 질문이 있으면 [사용자 의견 웹 사이트](https://cognitive.uservoice.com/)에서 요청하거나 투표하세요.

## <a name="see-also"></a>참고 항목

 [StackOverflow: Cognitive Services](http://stackoverflow.com/questions/tagged/microsoft-cognitive)
