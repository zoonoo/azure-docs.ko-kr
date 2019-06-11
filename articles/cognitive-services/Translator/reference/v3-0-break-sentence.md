---
title: Translator Text API BreakSentence 메서드
titlesuffix: Azure Cognitive Services
description: Translator Text API BreakSentence 메서드를 사용합니다.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: v-pawal
ms.openlocfilehash: 0ecb620757bc58b109747f69eb3b5afe0c56bac4
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66387570"
---
# <a name="translator-text-api-30-breaksentence"></a>Translator Text API 3.0: BreakSentence

텍스트에서 문장 경계의 위치를 식별합니다.

## <a name="request-url"></a>요청 URL

다음에 `POST` 요청을 보냅니다.

```HTTP
https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0
```

## <a name="request-parameters"></a>요청 매개 변수

쿼리 문자열에 전달된 요청 매개 변수는 다음과 같습니다.

<table width="100%">
  <th width="20%">쿼리 매개 변수</th>
  <th>설명</th>
  <tr>
    <td>api-version</td>
    <td>*필수 쿼리 매개 변수*입니다.<br/>클라이언트에서 요청한 API 버전입니다. 값은 `3.0`이어야 합니다.</td>
  </tr>
  <tr>
    <td>언어</td>
    <td>*선택적 쿼리 매개 변수*입니다.<br/>입력 텍스트의 언어를 식별하는 언어 태그입니다. 코드를 지정하지 않으면 자동 언어 검색이 적용됩니다.</td>
  </tr>
  <tr>
    <td>script</td>
    <td>*선택적 쿼리 매개 변수*입니다.<br/>입력 텍스트에서 사용되는 스크립트를 식별하는 스크립트 태그입니다. 스크립트를 지정하지 않으면 언어의 기본 스크립트가 가정됩니다.</td>
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

요청 본문은 JSON 배열입니다. 각 배열 요소는 `Text`라는 문자열 속성이 포함된 JSON 개체입니다. 문장 경계는 `Text` 속성 값에 대해 계산됩니다. 텍스트의 한 부분을 포함하는 샘플 요청 본문은 다음과 같습니다.

```json
[
    { "Text": "How are you? I am fine. What did you do today?" }
]
```

다음 제한 사항이 적용됩니다.

* 배열에는 최대 100개 요소가 있을 수 있습니다.
* 배열 요소의 텍스트 값은 공백을 포함하여 10,000자를 초과할 수 없습니다.
* 요청에 포함된 전체 텍스트는 공백을 포함하여 50,000자를 초과할 수 없습니다.
* `language` 쿼리 매개 변수를 지정하는 경우 모든 배열 요소가 동일한 언어여야 합니다. 그렇지 않은 경우, 언어 자동 검색이 각 배열 요소에 독립적으로 적용됩니다.

## <a name="response-body"></a>응답 본문

성공적인 응답은 입력 배열의 각 문자열에 대해 하나의 결과를 포함한 JSON 배열입니다. 결과 개체에는 다음과 같은 속성이 포함됩니다.

  * `sentLen`: 텍스트 요소의 문장 길이를 나타내는 정수의 배열입니다. 배열의 길이는 문장의 수이고 값은 각 문장의 길이입니다. 

  * `detectedLanguage`: 다음 속성을 통해 검색된 언어를 설명하는 개체입니다.

     * `language`: 검색된 언어의 코드입니다.

     * `score`: 결과의 신뢰도를 나타내는 부동 값입니다. 점수는 0~1 사이이고 낮은 점수는 낮은 신뢰도를 나타냅니다.
     
    `detectedLanguage` 속성은 언어 자동 검색이 요청될 때만 결과 개체에 존재합니다.

예제 JSON 응답은 다음과 같습니다.

```json
[
  {
    "sentenceLengths": [ 13, 11, 22 ]
    "detectedLanguage": {
      "language": "en",
      "score": 401
    },
  }
]
```

## <a name="response-headers"></a>응답 헤더

<table width="100%">
  <th width="20%">헤더</th>
  <th>설명</th>
  <tr>
    <td>X-RequestId</td>
    <td>요청을 식별하기 위해 서비스에서 생성한 값입니다. 문제 해결을 위해 사용됩니다.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>응답 상태 코드

요청을 반환하는 가능한 HTTP 상태 코드는 다음과 같습니다. 

<table width="100%">
  <th width="20%">상태 코드</th>
  <th>설명</th>
  <tr>
    <td>200</td>
    <td>성공.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>쿼리 매개 변수 중 하나가 누락되었거나 유효하지 않습니다. 다시 시도하기 전에 요청 매개 변수를 수정합니다.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>요청을 인증할 수 없습니다. 지정된 자격 증명이 있고 유효한지 확인합니다.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>요청에 권한이 없습니다. 세부 정보 오류 메시지를 확인합니다. 이것은 흔히 평가판 구독으로 제공된 사용 가능한 모든 번역이 모두 사용되었음을 나타냅니다.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>서버는 클라이언트 요청 한도 초과 했기 때문에 요청을 거부 합니다.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>예기치 않은 오류가 발생했습니다. 이 오류가 계속 발생하는 경우 오류 날짜와 시간, 응답 헤더 `X-RequestId`의 요청 식별자 및 요청 헤더 `X-ClientTraceId`의 클라이언트 식별자를 사용해서 보고합니다.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>서버를 일시적으로 사용할 수 없습니다. 요청을 다시 시도하십시오. 이 오류가 계속 발생하는 경우 오류 날짜와 시간, 응답 헤더 `X-RequestId`의 요청 식별자 및 요청 헤더 `X-ClientTraceId`의 클라이언트 식별자를 사용해서 보고합니다.</td>
  </tr>
</table> 

오류가 발생하는 경우 요청은 JSON 오류 응답도 반환합니다. 오류 코드는 오류를 더 범주화하도록 뒤에 3자리 숫자가 오는 3자리 HTTP 상태 코드로 결합된 6자리 숫자입니다. 일반적인 오류 코드는 [v3 Translator Text API 참조 페이지](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors)에서 확인할 수 있습니다. 

## <a name="examples"></a>예

다음 예제에서는 단일 문장에 대한 문장 경계를 가져오는 방법을 보여 줍니다. 문장의 언어는 서비스에서 자동으로 검색됩니다.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'How are you? I am fine. What did you do today?'}]"
```

---

