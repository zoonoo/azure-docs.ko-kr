---
title: LUIS에서 작성 및 엔드포인트 키 관리
titleSuffix: Azure Cognitive Services
description: Azure Portal에서 LUIS 엔드포인트 키를 만든 후 LUIS 앱에 키를 할당하고 올바른 엔드포인트 URL을 가져옵니다. 이 엔드포인트 URL을 사용하여 LUIS 예측을 가져옵니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 62081f96e2081833eb705992914899a6764bd792
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033212"
---
# <a name="add-an-azure-luis-resource-to-app"></a>앱에 Azure LUIS 리소스 추가

Azure Portal에서 LUIS 리소스를 만든 후 LUIS 앱에 리소스를 할당하고 올바른 엔드포인트 URL을 가져옵니다. 이 엔드포인트 URL을 사용하여 LUIS 예측을 가져옵니다.

<a name="programmatic-key" ></a>
<a name="authoring-key" ></a>
<a name="endpoint-key" ></a>
<a name="use-endpoint-key-in-query" ></a>
<a name="api-usage-of-ocp-apim-subscription-key" ></a>
<a name="key-limits" ></a>
<a name="key-limit-errors" ></a>
<a name="key-concepts"></a>
<a name="authoring-key"></a>
<a name="create-and-use-an-endpoint-key"></a>
<a name="assign-endpoint-key"></a>

## <a name="assign-resource"></a>리소스 할당

1. [Azure Portal](https://portal.azure.com)에서 LUIS 키를 만듭니다. 자세한 내용은 [Azure를 사용하여 끝점 키 만들기](luis-how-to-azure-subscription.md)를 참조하세요.
 
2. 맨 위 오른쪽 메뉴에서 **관리**를 선택한 다음, **키 및 엔드포인트**를 선택합니다.

    [ ![키 및 엔드포인트 페이지](./media/luis-manage-keys/keys-and-endpoints.png) ](./media/luis-manage-keys/keys-and-endpoints.png#lightbox)

3. LUIS를 추가하려면 **리소스 할당 +** 을 선택합니다.

    ![앱에 리소스 할당](./media/luis-manage-keys/assign-key.png)

4. LUIS 웹 사이트에 로그인한 이메일 주소와 연결된 대화 상자에서 테넌트를 선택합니다.  

5. 추가할 Azure 리소스와 연결된 **구독 이름**을 선택합니다.

6. **LUIS 리소스 이름**을 선택합니다. 

7. **리소스 할당**을 선택합니다. 

8. 표에서 새 행을 찾고 엔드포인트 URL을 복사합니다. 예측을 위해 LUIS 엔드포인트에 HTTP GET 요청을 할 수 있도록 올바르게 구성되어 있습니다. 

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

## <a name="unassign-resource"></a>리소스 할당 해제
엔드포인트 키를 할당 해제할 경우 Azure에서 삭제되지 않습니다. LUIS에서만 할당 해제됩니다. 

엔드포인트가 할당 해제되었거나, 앱에 할당되지 않은 경우 엔드포인트 URL에 대한 모든 요청은 오류 `401 This application cannot be accessed with the current subscription`을 반환합니다. 

## <a name="include-all-predicted-intent-scores"></a>예측된 모든 의도 점수 포함
**예측된 모든 의도 점수 포함** 확인란을 사용하여 엔드포인트 쿼리 응답에 각 의도의 예측 점수를 포함할 수 있습니다. 

이 설정을 사용하면 챗봇 또는 LUIS 호출 응용 프로그램이 반환된 의도의 점수에 따라 프로그래밍 방식으로 의사 결정을 내릴 수 있습니다. 일반적으로 상위 두 의도가 가장 흥미롭습니다. 상위 점수가 없음 의도이면, 챗봇은 없음 의도 및 기타 고득점 의도 간을 명확히 구분할 수 있는 후속 질문을 수행하도록 선택할 수 있습니다. 

의도 및 해당 점수도 엔드포인트 로그에 포함됩니다. 해당 로그를 [내보내고](luis-how-to-start-new-app.md#export-app) 점수를 분석할 수 있습니다. 

```JSON
{
  "query": "book a flight to Cairo",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.5223427
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.5223427
    },
    {
      "intent": "BookFlight",
      "score": 0.372391433
    }
  ],
  "entities": []
}
```

## <a name="enable-bing-spell-checker"></a>Bing spell checker 사용 
**엔드포인트 URL 설정**에서 **Bing Spell Checker** 토글을 통해 LUIS에서 예측 전에 철자가 잘못된 단어를 수정할 수 있습니다. **[Bing Spell Check 키](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)** 를 만듭니다. 

**spellCheck=true** 쿼리 문자열 매개 변수 및 **bing-spell-check-subscription-key={YOUR_BING_KEY_HERE}** 를 추가합니다. `{YOUR_BING_KEY_HERE}`를 Bing Spell Checker 키로 바꿉니다.

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```


## <a name="publishing-regions"></a>게시 지역

[유럽](luis-reference-regions.md#publishing-to-europe) 및 [오스트레일리아](luis-reference-regions.md#publishing-to-australia)의 게시를 포함하여 게시 [지역](luis-reference-regions.md)을 자세히 알아봅니다. 게시 지역은 작성 지역과 다릅니다. 쿼리 엔드포인트에 원하는 게시 지역에 해당하는 작성 지역에서 앱을 만듭니다.

## <a name="next-steps"></a>다음 단계

키를 사용하여 **앱 게시** 페이지에서 앱을 게시합니다. 게시에 대한 자세한 내용은 [앱 게시](luis-how-to-publish-app.md)를 참조하세요.

LUIS 작성 및 엔드포인트 키 개념을 이해하려면 [LUIS의 키](luis-concept-keys.md)를 참조하세요.