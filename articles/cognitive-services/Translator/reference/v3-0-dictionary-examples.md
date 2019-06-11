---
title: Translator Text API 사전 예제 메서드
titlesuffix: Azure Cognitive Services
description: Translator Text API 사전 예제 메서드를 사용합니다.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: v-pawal
ms.openlocfilehash: 526fc3e51691773e27530a0fa775f4f07ee94acd
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389765"
---
# <a name="translator-text-api-30-dictionary-examples"></a>Translator Text API 3.0: 사전 예제

사전의 용어가 컨텍스트에서 사용되는 방식을 보여 주는 예제를 제공합니다. 이 작업은 [사전 조회](./v3-0-dictionary-lookup.md)와 함께 사용됩니다.

## <a name="request-url"></a>요청 URL

다음에 `POST` 요청을 보냅니다.

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
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

요청 본문은 JSON 배열입니다. 각 배열 요소는 다음 속성을 갖는 JSON 개체입니다.

  * `Text`: 조회할 용어를 지정하는 문자열입니다. 이 속성은 이전 [사전 조회](./v3-0-dictionary-lookup.md) 요청의 역번역에서 가져온 `normalizedText` 필드의 값이어야 합니다. `normalizedSource` 필드의 값일 수도 있습니다.

  * `Translation`: 이전에 [사전 조회](./v3-0-dictionary-lookup.md) 작업에서 반환된 번역된 텍스트를 지정하는 문자열입니다. 이 속성은 [사전 조회](./v3-0-dictionary-lookup.md) 응답의 `translations` 목록에 포함된 `normalizedTarget` 필드의 값이어야 합니다. 이 서비스는 특정 원본-대상 단어 쌍에 대한 예제를 반환합니다.

예제는 다음과 같습니다.

```json
[
    {"Text":"fly", "Translation":"volar"}
]
```

다음 제한 사항이 적용됩니다.

* 배열에는 최대 10개 요소가 있을 수 있습니다.
* 배열 요소의 텍스트 값은 공백을 포함하여 100자를 초과할 수 없습니다.

## <a name="response-body"></a>응답 본문

성공적인 응답은 입력 배열의 각 문자열에 대해 하나의 결과를 포함한 JSON 배열입니다. 결과 개체에는 다음과 같은 속성이 포함됩니다.

  * `normalizedSource`: 원본 용어의 정규화된 형식을 제공하는 문자열입니다. 일반적으로 이 속성은 요청 본문의 일치하는 목록 인덱스에 있는 `Text` 필드의 값과 동일해야 합니다.
    
  * `normalizedTarget`: 대상 용어의 정규화된 형식을 제공하는 문자열입니다. 일반적으로 이 속성은 요청 본문의 일치하는 목록 인덱스에 있는 `Translation` 필드의 값과 동일해야 합니다.
  
  * `examples`: (원본 용어, 대상 용어) 쌍에 대한 예제 목록입니다. 목록의 각 요소는 다음과 같은 속성이 있는 개체입니다.

    * `sourcePrefix`: 완전한 예제를 만들기 위해 `sourceTerm` 값 _앞에_ 연결할 문자열입니다. 공백 문자는 필요한 위치에 이미 있으므로 추가하지 않도록 합니다. 이 값은 빈 문자열일 수 있습니다.

    * `sourceTerm`: 조회된 실제 용어와 같은 문자열입니다. 이 문자열은 `sourcePrefix` 및 `sourceSuffix`와 함께 추가되어 전체 예제를 형성합니다. 해당 값은 사용자 인터페이스에서 표시될 수 있으므로(예: 굵게 표시) 구분됩니다.

    * `sourceSuffix`: 완전한 예제를 만들기 위해 `sourceTerm` 값 _뒤에_ 연결할 문자열입니다. 공백 문자는 필요한 위치에 이미 있으므로 추가하지 않도록 합니다. 이 값은 빈 문자열일 수 있습니다.

    * `targetPrefix`: `sourcePrefix`과 비슷하지만 대상에 사용되는 문자열입니다.

    * `targetTerm`: `sourceTerm`과 비슷하지만 대상에 사용되는 문자열입니다.

    * `targetSuffix`: `sourceSuffix`와 비슷하지만 대상에 사용되는 문자열입니다.

    > [!NOTE]
    > 사전에 예제가 없는 경우 응답은 200(정상)이지만 `examples` 목록은 빈 목록이 됩니다.

## <a name="examples"></a>예

이 예제에서는 영어 용어 `fly` 및 해당 스페인어 번역 `volar`로 구성된 쌍의 예제를 조회하는 방법을 보여 줍니다.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly', 'Translation':'volar'}]"
```

---

응답 본문은 다음과 같습니다(명확히 나타내기 위해 축약됨).

```
[
    {
        "normalizedSource":"fly",
        "normalizedTarget":"volar",
        "examples":[
            {
                "sourcePrefix":"They need machines to ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Necesitan máquinas para ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },      
            {
                "sourcePrefix":"That should really ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Eso realmente debe ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```
