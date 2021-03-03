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
ms.date: 01/12/2021
ms.openlocfilehash: 509d1dc0b94bdfa9be5185df0bad793f7702eb26
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101731037"
---
# <a name="correct-misspelled-words-with-bing-resource"></a>Bing 리소스에서 철자가 잘못 된 단어 수정

V3 예측 API는 이제 [Bing Spellcheck API](/bing/search-apis/bing-spell-check/overview)를 지원합니다. 요청 헤더에 Bing 검색 리소스에 대 한 키를 포함 하 여 응용 프로그램에 맞춤법 검사를 추가 합니다. 기존 Bing 리소스를 이미 소유 하 고 있는 경우이 리소스를 사용 하거나 새 Bing 리소스를 [만들어](https://portal.azure.com/#create/Microsoft.BingSearch) 이 기능을 사용할 수 있습니다. 

철자가 잘못 된 쿼리에 대 한 예측 출력 예제:

```json
{
  "query": "bouk me a fliht to kayro",
  "prediction": {
    "alteredQuery": "book me a flight to cairo",
    "topIntent": "book a flight",
    "intents": {
      "book a flight": {
        "score": 0.9480589
      }
      "None": {
        "score": 0.0332136229
      }
    },
    "entities": {}
  }
}
```

LUIS 사용자 utterance 예측 이전에 맞춤법을 수정 했습니다. 응답에서 맞춤법을 포함 하 여 원래 utterance의 변경 내용을 볼 수 있습니다.

## <a name="create-bing-search-resource"></a>Bing Search 리소스 만들기

Azure Portal에서 Bing Search 리소스를 만들려면 다음 지침을 따르세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 왼쪽 위 모서리에 있는 **리소스 만들기** 를 선택합니다.

3. 검색 상자에서 서비스를 입력 `Bing Search V7` 하 고 선택 합니다.

4. 법적 고지 사항을 비롯한 정보가 포함되는 정보 패널이 오른쪽에 표시됩니다. **만들기** 를 선택하여 구독 만들기 프로세스를 시작합니다.

> [!div class="mx-imgBorder"]
> ![Bing Spell Check API V7 리소스](./media/luis-tutorial-bing-spellcheck/bing-search-resource-portal.png)

5. 다음 패널에서 서비스 설정을 입력합니다. 서비스 만들기 프로세스가 완료될 때까지 기다립니다.

6. 리소스를 만든 후 왼쪽의 **키 및 끝점** 블레이드로 이동 합니다. 

7. 예측 요청의 헤더에 추가할 키 중 하나를 복사 합니다. 두 키 중 하나만 필요 합니다.

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="enable-spell-check-from-ui"></a>UI에서 맞춤법 검사 사용 
[Luis 포털](https://www.luis.ai)을 사용 하 여 예제 쿼리에 대해 spellcheck를 사용 하도록 설정할 수 있습니다. 화면 위쪽에서 **관리** 를 선택 하 고 왼쪽 탐색 영역에서 **Azure 리소스** 를 선택 합니다. 응용 프로그램에 예측 리소스를 연결한 후 페이지 맨 아래에서 **쿼리 매개 변수 변경** 을 선택 하 고 **맞춤법 검사 사용** 필드에 리소스 키를 붙여 넣을 수 있습니다.
    
   > [!div class="mx-imgBorder"]
   > ![맞춤법 검사 사용](./media/luis-tutorial-bing-spellcheck/spellcheck-query-params.png)


## <a name="adding-the-key-to-the-endpoint-url"></a>엔드포인트 URL에 키 추가
맞춤법 수정 내용을 적용 하려는 각 쿼리에 대해 끝점 쿼리에는 쿼리 헤더 매개 변수에 전달 된 Bing Spellcheck 리소스 키가 필요 합니다. LUIS를 호출하는 챗봇을 사용하거나 LUIS 엔드포인트 API를 직접 호출할 수 있습니다. 끝점을 호출 하는 방법에 관계 없이 각 호출과 모든 호출은 맞춤법 수정이 제대로 작동 하기 위해 헤더의 요청에 필요한 정보를 포함 해야 합니다. **Mkt** 를 사용 하 여 값을 키 값으로 설정 해야 합니다.

|헤더 키|헤더 값|
|--|--|
|`mkt-bing-spell-check-key`|키 및 리소스의 **끝점** 블레이드에서 찾은 키|

## <a name="send-misspelled-utterance-to-luis"></a>LUIS에 맞춤법이 틀린 발화 보내기
1. "Mountainn?"와 같이 보낼 예측 쿼리에서 철자가 틀린 utterance를 추가 합니다. 영어에서 하나의 `n`이 포함된 `mountain`은 올바른 맞춤법입니다.

2. LUIS는 `How far is the mountain?`에 대한 JSON 결과로 응답합니다. Bing Spell Check API v7에서 잘못된 맞춤법을 발견하면 LUIS 앱의 JSON 응답에 있는 `query` 필드에는 원래 쿼리가 포함되고 `alteredQuery` 필드에는 LUIS에 보내는 수정된 쿼리가 포함됩니다.

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

Bing Search API v7 서비스를 사용 하지 않으려면 올바른 철자와 잘못 된 철자를 추가 해야 합니다.

두 가지 솔루션은 다음과 같습니다.

* 레이블 예 길이 발언는 다른 모든 철자가 있으므로 LUIS가 정확한 철자와 오타를 배울 수 있습니다. 이 옵션은 맞춤법 검사기를 사용하는 것보다 더 많은 레이블 지정 작업이 필요합니다.
* 단어의 모든 변형이 포함 된 구 목록을 만듭니다. 이 솔루션을 사용 하면 예제 길이 발언의 단어 변형에 레이블을 추가할 필요가 없습니다.


> [!div class="nextstepaction"]
> [예제 발화에 대한 자세한 정보](./luis-how-to-add-entities.md)