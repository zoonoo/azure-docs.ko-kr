---
title: Translator Text API 사전 조회 메서드
titlesuffix: Azure Cognitive Services
description: Translator Text API 사전 조회 메서드를 사용합니다.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: v-pawal
ms.openlocfilehash: 10ccdcf0fda424e67b993c7c9300f0b84c56121f
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389782"
---
# <a name="translator-text-api-30-dictionary-lookup"></a>Translator Text API 3.0: 사전 조회

하나의 단어와 소수의 관용구에 대한 대체 번역을 제공합니다. 각 번역에는 음성 일부 및 역번역 목록이 포함되어 있습니다. 역번역을 사용하면 번역 맥락을 이해할 수 있습니다. [사전 예제](./v3-0-dictionary-examples.md) 작업을 사용하면 추가로 드릴다운하여 각 번역 쌍의 사용 예제를 볼 수 있습니다.

## <a name="request-url"></a>요청 URL

다음에 `POST` 요청을 보냅니다.

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0
```

## <a name="request-parameters"></a>요청 매개 변수

쿼리 문자열에 전달된 요청 매개 변수는 다음과 같습니다.

<table width="100%">
  <th width="20%">쿼리 매개 변수</th>
  <th>설명</th>
  <tr>
    <td>api-version</td>
    <td>*필수 매개 변수*입니다.<br/>클라이언트에서 요청한 API 버전입니다. 값은 `3.0`이어야 합니다.</td>
  </tr>
  <tr>
    <td>from</td>
    <td>*필수 매개 변수*입니다.<br/>입력 텍스트의 언어를 지정합니다. 원본 언어는 `dictionary` 범위에 포함된 [지원되는 언어](./v3-0-languages.md) 중 하나여야 합니다.</td>
  </tr>
  <tr>
    <td>to</td>
    <td>*필수 매개 변수*입니다.<br/>출력 텍스트의 언어를 지정합니다. 대상 언어는 `dictionary` 범위에 포함된 [지원되는 언어](./v3-0-languages.md) 중 하나여야 합니다.</td>
  </tr>
</table>

요청 헤더에는 다음이 포함됩니다.

<table width="100%">
  <th width="20%">헤더</th>
  <th>설명</th>
  <tr>
    <td>인증 헤더</td>
    <td><em>필수 요청 헤더</em><br/><a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">인증에 사용할 수 있는 옵션</a>을 참조하세요.</td>
  </tr>
  <tr>
    <td>콘텐츠 형식</td>
    <td>*필수 요청 헤더*<br/>페이로드의 콘텐츠 형식을 지정합니다. 가능한 값은 `application/json`입니다.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>*필수 요청 헤더*<br/>요청 본문의 길이입니다.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*선택 사항*입니다.<br/>요청을 고유하게 식별하는 클라이언트 생성 ID입니다. `ClientTraceId`라는 쿼리 매개 변수를 사용하는 쿼리 문자열에서 추적 ID를 포함하는 경우 이 헤더를 생략할 수 있습니다.</td>
  </tr>
</table> 

## <a name="request-body"></a>요청 본문

요청 본문은 JSON 배열입니다. 각 배열 요소는 조회할 용어를 나타내는 `Text`라는 문자열 속성이 포함된 JSON 개체입니다.

```json
[
    {"Text":"fly"}
]
```

다음 제한 사항이 적용됩니다.

* 배열에는 최대 10개 요소가 있을 수 있습니다.
* 배열 요소의 텍스트 값은 공백을 포함하여 100자를 초과할 수 없습니다.

## <a name="response-body"></a>응답 본문

성공적인 응답은 입력 배열의 각 문자열에 대해 하나의 결과를 포함한 JSON 배열입니다. 결과 개체에는 다음과 같은 속성이 포함됩니다.

  * `normalizedSource`: 원본 용어의 정규화된 형식을 제공하는 문자열입니다. 예를 들어, 요청이 "JOHN"인 경우 정규화된 형식은 "john"이 됩니다. 이 필드의 콘텐츠는 [조회 예제](./v3-0-dictionary-examples.md)의 입력이 됩니다.
    
  * `displaySource`: 최종 사용자 표시에 가장 적합한 형식으로 제공되는 원본 용어를 제공하는 문자열입니다. 예를 들어, 입력이 "JOHN"이면 표시 형식에는 이 이름의 일반적인 철자인 "John"이 반영됩니다. 

  * `translations`: 원본 용어에 대한 번역 목록입니다. 목록의 각 요소는 다음과 같은 속성이 있는 개체입니다.

    * `normalizedTarget`: 이 용어의 정규화된 형식을 대상 언어로 제공하는 문자열입니다. 이 값은 [조회 예제](./v3-0-dictionary-examples.md)의 입력으로 사용되어야 합니다.

    * `displayTarget`: 용어를 최종 사용자 표시에 가장 적합한 형식의 대상 언어로 제공하는 문자열입니다. 일반적으로 이 용어는 `normalizedTarget`과 대/소문자만 차이를 보입니다. 예를 들어, "Juan"과 같은 고유 명사는 `normalizedTarget = "juan"` 및 `displayTarget = "Juan"`이 지정됩니다.

    * `posTag`: 이 용어를 음성 부분 태그에 연결하는 문자열입니다.

        | 태그 이름 | 설명  |
        |----------|--------------|
        | ADJ      | 형용사   |
        | ADV      | 부사      |
        | CONJ     | 접속사 |
        | DET      | 한정사  |
        | MODAL    | 동사        |
        | NOUN     | 명사        |
        | PREP     | 전치사 |
        | PRON     | 대명사     |
        | VERB     | 동사        |
        | OTHER    | 기타        |

        영어 쪽에 음성 부분 태그를 지정한 후 각 원본/대상 쌍에 대해 가장 자주 나오는 태그가 선택되는 방식으로 구현되었습니다. 따라서 스페인어 단어를 영어의 다른 음성 부분 태그로 자주 변환하는 경우 태그가 잘못되기 쉽습니다(스페인어 단어와 관련해서).

    * `confidence`: 번역 쌍의 “신뢰도”(또는 좀 더 정확히 나타내자면 “학습 데이터의 확률”)를 나타내는 0.0과 1.0 사이의 값입니다. 한 원본 단어의 신뢰도 점수의 합계는 1.0이 될 수도 있고 그렇지 않을 수도 있습니다. 

    * `prefixWord`: 번역의 접두사로 표시할 단어를 제공하는 문자열입니다. 현재, 성별 한정사가 있는 언어에서 명사의 성별 한정사입니다. 예를 들어, 스페인어 단어 "mosca"는 스페인어에서 여성 명사이므로 "mosca"의 접두사는 "la"가 됩니다. 이 속성은 원본이 아닌 번역에만 종속됩니다. 접두사가 없는 경우 빈 문자열이 됩니다.
    
    * `backTranslations`: 대상의 “역번역” 목록입니다. 예를 들어, 대상이 번역될 수 있는 원본 단어를 나타냅니다. 이 목록은 요청된 원본 단어를 포함하도록 보장됩니다(예를 들어 조회할 원본 단어가 "fly"이면 "fly"가 `backTranslations` 목록에 포함되도록 보장됨). 그러나 첫 번째 위치에 표시될 것으로 보장할 수는 없으며 그렇지 않은 경우가 많습니다. `backTranslations` 목록의 각 요소는 다음과 같은 속성으로 설명되는 개체입니다.

        * `normalizedText`: 대상의 역번역에 해당하는 원본 용어의 정규화된 형식을 제공하는 문자열입니다. 이 값은 [조회 예제](./v3-0-dictionary-examples.md)의 입력으로 사용되어야 합니다.        

        * `displayText`: 최종 사용자에게 표시하기 가장 적합한 형태를 갖는 대상의 역번역에 해당하는 원본 용어를 제공하는 문자열입니다.

        * `numExamples`: 이 번역 쌍에 대해 사용할 수 있는 예제 수를 나타내는 정수입니다. 실제 예제는 별도의 [조회 예제](./v3-0-dictionary-examples.md) 호출을 통해 검색해야 합니다. 이 수는 주로 UX에 쉽게 표시하려는 용도로 고안된 것입니다. 예를 들어, 예제 수가 0보다 큰 경우 사용자 인터페이스는 백번역에 하이퍼링크를 추가하고, 예제가 없는 경우 역번역을 일반 텍스트로 표시할 수 있습니다. [조회 예제](./v3-0-dictionary-examples.md)를 호출하여 반환되는 실제 예제의 수는 `numExamples`보다 작을 수 있습니다. “잘못된” 예제를 제거하기 위해 즉석에서 추가 필터링이 적용될 수 있기 때문입니다.
        
        * `frequencyCount`: 데이터에서 이 번역 쌍의 빈도를 나타내는 정수입니다. 이 필드의 주요 목적은 사용자 인터페이스에 역번역을 정렬하는 수단을 제공하여 좀 더 자주 나오는 용어가 먼저 표시되도록 하는 것입니다.

    > [!NOTE]
    > 조회되는 용어가 사전에 없는 경우 응답은 200(정상)이지만 `translations` 목록은 빈 목록이 됩니다.

## <a name="examples"></a>예

이 예제에서는 영어 용어인 `fly`의 스페인어 대체 번역을 조회하는 방법을 보여 줍니다.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly'}]"
```

---

응답 본문은 다음과 같습니다(명확히 나타내기 위해 축약됨).

```
[
    {
        "normalizedSource":"fly",
        "displaySource":"fly",
        "translations":[
            {
                "normalizedTarget":"volar",
                "displayTarget":"volar",
                "posTag":"VERB",
                "confidence":0.4081,
                "prefixWord":"",
                "backTranslations":[
                    {"normalizedText":"fly","displayText":"fly","numExamples":15,"frequencyCount":4637},
                    {"normalizedText":"flying","displayText":"flying","numExamples":15,"frequencyCount":1365},
                    {"normalizedText":"blow","displayText":"blow","numExamples":15,"frequencyCount":503},
                    {"normalizedText":"flight","displayText":"flight","numExamples":15,"frequencyCount":135}
                ]
            },
            {
                "normalizedTarget":"mosca",
                "displayTarget":"mosca",
                "posTag":"NOUN",
                "confidence":0.2668,
                "prefixWord":"",
                "backTranslations":[
                    {"normalizedText":"fly","displayText":"fly","numExamples":15,"frequencyCount":1697},
                    {"normalizedText":"flyweight","displayText":"flyweight","numExamples":0,"frequencyCount":48},
                    {"normalizedText":"flies","displayText":"flies","numExamples":9,"frequencyCount":34}
                ]
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```

이 예제는 조회되는 용어가 유효한 사전 쌍에 없을 때 나타나는 결과를 보여 줍니다.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly123456'}]"
```

---

이 용어는 사전에서 찾을 수 없으므로 응답 본문에 빈 `translations` 목록이 포함됩니다.

```
[
    {
        "normalizedSource":"fly123456",
        "displaySource":"fly123456",
        "translations":[]
    }
]
```
