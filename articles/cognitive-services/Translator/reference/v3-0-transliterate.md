---
title: Translator Text API Transliterate 메서드
titlesuffix: Azure Cognitive Services
description: Translator Text API Transliterate 메서드를 사용합니다.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: v-pawal
ms.openlocfilehash: 138a04cca1bbbaf7b59f628f491a5f13d73fb6f7
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66387386"
---
# <a name="translator-text-api-30-transliterate"></a>Translator Text API 3.0: Transliterate

특정 언어의 텍스트를 한 스크립트에서 다른 스크립트로 변환합니다.

## <a name="request-url"></a>요청 URL

다음에 `POST` 요청을 보냅니다.

```HTTP
https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0
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
    <td>언어</td>
    <td>*필수 매개 변수*입니다.<br/>한 스크립트에서 다른 스크립트로 변환할 텍스트의 언어를 지정합니다. 사용 가능한 언어는 서비스에서 [지원되는 언어](./v3-0-languages.md)를 쿼리하여 표시되는 `transliteration` 범위에 표시됩니다.</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td>*필수 매개 변수*입니다.<br/>입력 텍스트에서 사용되는 스크립트를 지정합니다. `transliteration` 범위를 사용하여 [지원되는 언어](./v3-0-languages.md)를 조회하면 선택한 언어로 사용 가능한 입력 스크립트가 검색됩니다.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td>*필수 매개 변수*입니다.<br/>출력 스크립트를 지정합니다. `transliteration` 범위를 사용하여 [지원되는 언어](./v3-0-languages.md)를 조회하면 선택한 입력 언어 및 입력 스크립트 조합으로 사용 가능한 출력 스크립트가 검색됩니다.</td>
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

요청 본문은 JSON 배열입니다. 각 배열 요소는 변환할 문자열을 나타내는 `Text`라는 문자열 속성이 포함된 JSON 개체입니다.

```json
[
    {"Text":"こんにちは"},
    {"Text":"さようなら"}
]
```

다음 제한 사항이 적용됩니다.

* 배열에는 최대 10개 요소가 있을 수 있습니다.
* 배열 요소의 텍스트 값은 공백을 포함하여 1,000자를 초과할 수 없습니다.
* 요청에 포함된 전체 텍스트는 공백을 포함하여 5,000자를 초과할 수 없습니다.

## <a name="response-body"></a>응답 본문

성공적인 응답은 입력 배열의 각 요소에 대해 하나의 결과를 포함한 JSON 배열입니다. 결과 개체에는 다음과 같은 속성이 포함됩니다.

  * `text`: 입력 문자열을 출력 스크립트로 변환한 결과 문자열입니다.
  
  * `script`: 출력에 사용되는 스크립트를 지정하는 문자열입니다.

예제 JSON 응답은 다음과 같습니다.

```json
[
    {"text":"konnnichiha","script":"Latn"},
    {"text":"sayounara","script":"Latn"}
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

다음 예제에서는 2개의 일본어 문자열을 로마자 일본어로 변환하는 방법을 보여 줍니다.

# <a name="curltabcurl"></a>[curl](#tab/curl)

이 예제에서 요청에 대한 JSON 페이로드는 다음과 같습니다.

```
[{"text":"こんにちは","script":"jpan"},{"text":"さようなら","script":"jpan"}]
```

유니코드 문자를 지원하지 않는 명령줄 창에서 cURL을 사용하는 경우 다음 JSON 페이로드를 가져온 후 `request.txt`라는 파일에 저장합니다. 이 파일은 `UTF-8` 인코딩을 사용해서 저장해야 합니다.

```
curl -X POST "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=Jpan&toScript=Latn" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d @request.txt
```

---
