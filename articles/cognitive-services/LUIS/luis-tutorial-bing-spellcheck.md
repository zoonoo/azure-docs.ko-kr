---
title: LUIS 쿼리에 Bing Spell Check API v7 추가 | Microsoft Docs
titleSuffix: Azure
description: LUIS 엔드포인트 쿼리에 Bing Spell Check API V7을 추가하여 발화에서 맞춤법이 틀린 단어를 수정합니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 6d197b014d6936b12b34a6d8783d1ebd29dfa28a
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52443332"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Bing Spell Check로 맞춤법이 틀린 단어 수정

LUIS 앱을 [Bing Spell Check API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/)과 통합하여 LUIS에서 발화의 점수 및 엔터티를 예측하기 전에 발화에서 맞춤법이 틀린 단어를 수정할 수 있습니다. 

## <a name="create-first-key-for-bing-spell-check-v7"></a>Bing Spell Check V7의 첫 번째 키 만들기
[첫 번째 Bing Spell Check API v7 키](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)는 무료입니다. 

![무료 키 만들기](./media/luis-tutorial-bing-spellcheck/free-key.png)

<a name"create-subscription-key"></a>
## <a name="create-endpoint-key"></a>끝점 키 만들기
무료 키가 만료되면 끝점 키를 만듭니다.

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

## <a name="using-the-key-in-luis-test-panel"></a>LUIS 테스트 패널에서 키 사용
LUIS에는 키를 사용할 두 개의 위치가 있습니다. 첫 번째는 [테스트 패널](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel)에 있습니다. 키는 LUIS에 저장되는 것이 아니라 세션 변수입니다. 테스트 패널에서 Bing Spell Check API v7 서비스를 발화에 적용하려고 할 때마다 키를 설정해야 합니다. 키 설정에 대해서는 테스트 패널의 [지침](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel)을 참조하세요.

## <a name="adding-the-key-to-the-endpoint-url"></a>엔드포인트 URL에 키 추가
맞춤법 교정을 적용하려는 각 쿼리에 대한 쿼리 문자열 매개 변수에 전달되는 키가 엔드포인트 쿼리에 필요합니다. LUIS를 호출하는 챗봇을 사용하거나 LUIS 엔드포인트 API를 직접 호출할 수 있습니다. 엔드포인트를 호출하는 방법에 상관없이 각각의 모든 호출은 맞춤법 교정이 올바르게 작동하는 데 필요한 정보를 포함해야 합니다.

엔드포인트 URL에는 올바르게 전달해야 하는 여러 값이 있습니다. Bing Spell Check API v7 키는 이러한 값 중 하나입니다. **spellCheck** 매개 변수를 true로 설정하고 **bing-spell-check-subscription-key** 값을 키 값으로 설정해야 합니다.

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appID}?subscription-key={luisKey}&spellCheck=**true**&bing-spell-check-subscription-key=**{bingKey}**&verbose=true&timezoneOffset=0&q={utterance}`

## <a name="send-misspelled-utterance-to-luis"></a>LUIS에 맞춤법이 틀린 발화 보내기
1. 웹 브라우저에서 앞의 문자열을 복사하고 `region`, `appId`, `luisKey` 및 `bingKey`를 고유한 값으로 바꿉니다. 게시 [지역](luis-reference-regions.md)과 다른 경우 엔드포인트 지역을 사용해야 합니다.

2. “How far is the mountainn”과 같은 맞춤법이 틀린 발화를 추가합니다. 영어에서 하나의 `n`이 포함된 `mountain`은 올바른 맞춤법입니다. 

3. Enter 키를 선택하여 쿼리를 LUIS로 보냅니다.

4. LUIS는 `How far is the mountain?`에 대한 JSON 결과로 응답합니다. Bing Spell Check API v7에서 잘못된 맞춤법을 발견하면 LUIS 앱의 JSON 응답에 있는 `query` 필드에는 원래 쿼리가 포함되고 `alteredQuery` 필드에는 LUIS에 보내는 수정된 쿼리가 포함됩니다.

```
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
Bing Spell Check API v7 서비스를 사용하지 않으려면 LUIS에서 맞춤법 및 오타를 학습할 수 있도록 맞춤법 오류가 있는 발화에 레이블을 지정할 수 있습니다. 이 옵션은 맞춤법 검사기를 사용하는 것보다 더 많은 레이블 지정 작업이 필요합니다.

## <a name="publishing-page"></a>게시 페이지
[게시](luis-how-to-publish-app.md) 페이지에는 **Bing Spell Checker 사용** 확인란이 있습니다. 이는 키를 만들고 엔드포인트 URL이 변경되는 방식을 이해할 수 있는 편리한 기능입니다. 각 발화에 대해 맞춤법을 교정하려면 올바른 엔드포인트 매개 변수를 사용해야 합니다. 

> [!div class="nextstepaction"]
> [예제 발화에 대해 자세히 알아보기](luis-how-to-add-example-utterances.md)
