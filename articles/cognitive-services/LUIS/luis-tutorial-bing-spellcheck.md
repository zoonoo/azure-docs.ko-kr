---
title: 철자가 잘못 된 단어 수정-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS 엔드포인트 쿼리에 Bing Spell Check API V7을 추가하여 발화에서 맞춤법이 틀린 단어를 수정합니다.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 11/19/2019
ms.openlocfilehash: 120852c3e66594b1cf5cd28bb52ce06634211060
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541098"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Bing Spell Check로 맞춤법이 틀린 단어 수정

LUIS 앱을 [Bing Spell Check API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/)과 통합하여 LUIS에서 발화의 점수 및 엔터티를 예측하기 전에 발화에서 맞춤법이 틀린 단어를 수정할 수 있습니다.

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]

## <a name="create-endpoint-key"></a>엔드포인트 키 만들기

Azure Portal에서 Bing Spell Check 리소스를 만들려면 다음 지침을 따르세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 왼쪽 위 모서리에 있는 **리소스 만들기**를 선택합니다.

3. 검색 상자에 `Bing Spell Check API V7`를 입력합니다.

    ![Bing Spell Check API V7 검색](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. 서비스를 선택합니다.

5. 법적 고지 사항을 비롯한 정보가 포함되는 정보 패널이 오른쪽에 표시됩니다. **만들기**를 선택하여 구독 만들기 프로세스를 시작합니다.

6. 다음 패널에서 서비스 설정을 입력합니다. 서비스 만들기 프로세스가 완료될 때까지 기다립니다.

    ![서비스 설정 입력](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. 왼쪽 탐색의 **즐겨찾기** 제목 아래에서 **모든 리소스**를 선택합니다.

8. 새 서비스를 선택합니다. 해당 유형은 **Cognitive Services**이고 위치는 **글로벌**입니다.

9. 기본 패널에서 **키**를 선택하여 새 키를 확인합니다.

    ![키 선택](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. 첫 번째 키를 복사합니다. 두 키 중 하나만 필요합니다.

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="adding-the-key-to-the-endpoint-url"></a>엔드포인트 URL에 키 추가
맞춤법 교정을 적용하려는 각 쿼리에 대한 쿼리 문자열 매개 변수에 전달되는 키가 엔드포인트 쿼리에 필요합니다. LUIS를 호출하는 챗봇을 사용하거나 LUIS 엔드포인트 API를 직접 호출할 수 있습니다. 엔드포인트를 호출하는 방법에 상관없이 각각의 모든 호출은 맞춤법 교정이 올바르게 작동하는 데 필요한 정보를 포함해야 합니다.

엔드포인트 URL에는 올바르게 전달해야 하는 여러 값이 있습니다. Bing Spell Check API v7 키는 이러한 값 중 하나입니다. **spellCheck** 매개 변수를 true로 설정하고 **bing-spell-check-subscription-key** 값을 키 값으로 설정해야 합니다.

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appID}?subscription-key={luisKey}&spellCheck=true&bing-spell-check-subscription-key={bingKey}&verbose=true&timezoneOffset=0&q={utterance}`

## <a name="send-misspelled-utterance-to-luis"></a>LUIS에 맞춤법이 틀린 발화 보내기
1. 웹 브라우저에서 앞의 문자열을 복사하고 `region`, `appId`, `luisKey` 및 `bingKey`를 고유한 값으로 바꿉니다. 게시 [지역](luis-reference-regions.md)과 다른 경우 엔드포인트 지역을 사용해야 합니다.

2. “How far is the mountainn”과 같은 맞춤법이 틀린 발화를 추가합니다. 영어에서 하나의 `n`이 포함된 `mountain`은 올바른 맞춤법입니다.

3. Enter 키를 선택하여 쿼리를 LUIS로 보냅니다.

4. LUIS는 `How far is the mountain?`에 대한 JSON 결과로 응답합니다. Bing Spell Check API v7에서 잘못된 맞춤법을 발견하면 LUIS 앱의 JSON 응답에 있는 `query` 필드에는 원래 쿼리가 포함되고 `alteredQuery` 필드에는 LUIS에 보내는 수정된 쿼리가 포함됩니다.

```json
{
  "query": "How far is the mountainn?",
  "alteredQuery": "How far is the mountain?",
  "topScoringIntent": {
    "intent": "Concierge",
    "score": 0.183866
  },
  "entities": []
}
```

## <a name="ignore-spelling-mistakes"></a>맞춤법 오류 무시

Bing Spell Check API v7 서비스를 사용 하지 않으려면 올바른 철자와 잘못 된 철자를 추가 해야 합니다.

두 가지 솔루션은 다음과 같습니다.

* 레이블 예 길이 발언는 다른 모든 철자가 있으므로 LUIS가 정확한 철자와 오타를 배울 수 있습니다. 이 옵션은 맞춤법 검사기를 사용하는 것보다 더 많은 레이블 지정 작업이 필요합니다.
* 단어의 모든 변형이 포함 된 구 목록을 만듭니다. 이 솔루션을 사용 하면 예제 길이 발언의 단어 변형에 레이블을 추가할 필요가 없습니다.

## <a name="publishing-page"></a>게시 페이지
[게시](luis-how-to-publish-app.md) 페이지에는 **Bing Spell Checker 사용** 확인란이 있습니다. 이는 키를 만들고 엔드포인트 URL이 변경되는 방식을 이해할 수 있는 편리한 기능입니다. 각 발화에 대해 맞춤법을 교정하려면 올바른 엔드포인트 매개 변수를 사용해야 합니다.

> [!div class="nextstepaction"]
> [예제 발화에 대한 자세한 정보](luis-how-to-add-example-utterances.md)
