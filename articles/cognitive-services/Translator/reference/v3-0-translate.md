---
title: Translator Text API Translate 메서드
titleSuffix: Azure Cognitive Services
description: Translator Text API Translate 메서드를 사용합니다.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: v-pawal
ms.openlocfilehash: be61d8932288b9a6b2cc96e53d3630124ec0f610
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389763"
---
# <a name="translator-text-api-30-translate"></a>Translator Text API 3.0: Translate

텍스트를 번역합니다.

## <a name="request-url"></a>요청 URL

다음에 `POST` 요청을 보냅니다.

```HTTP
https://api.cognitive.microsofttranslator.com/translate?api-version=3.0
```

## <a name="request-parameters"></a>요청 매개 변수

쿼리 문자열에 전달된 요청 매개 변수는 다음과 같습니다.

<table width="100%">
  <th width="20%">쿼리 매개 변수</th>
  <th>설명</th>
  <tr>
    <td>api-version</td>
    <td><em>필수 매개 변수</em>입니다.<br/>클라이언트에서 요청한 API 버전입니다. 값은 <code>3.0</code>이어야 합니다.</td>
  </tr>
  <tr>
    <td>from</td>
    <td><em>선택적 매개 변수</em>입니다.<br/>입력 텍스트의 언어를 지정합니다. <code>translation</code> 범위를 통해 <a href="./v3-0-languages.md">지원되는 언어</a>를 조회하여 번역할 수 있는 원본 언어를 찾습니다. <code>from</code> 매개 변수를 지정하지 않으면 자동 언어 검색에 따라 원본 언어가 결정됩니다.</td>
  </tr>
  <tr>
    <td>to</td>
    <td><em>필수 매개 변수</em>입니다.<br/>출력 텍스트의 언어를 지정합니다. 대상 언어는 <code>translation</code> 범위에 포함된 <a href="./v3-0-languages.md">지원되는 언어</a> 중 하나여야 합니다. 예를 들어, 독일어로 번역하려면 <code>to=de</code>를 사용합니다.<br/>쿼리 문자열의 매개 변수를 반복하여 동시에 여러 언어로 번역할 수도 있습니다. 예를 들어, 독일어 및 이탈리아어로 번역하려면 <code>to=de&to=it</code>를 사용합니다.</td>
  </tr>
  <tr>
    <td>textType</td>
    <td><em>선택적 매개 변수</em>입니다.<br/>번역되는 텍스트가 일반 텍스트인지 또는 HTML 인지를 정의합니다. 모든 HTML은 올바른 형식이 완전한 요소여야 합니다. 가능한 값은 <code>plain</code>(기본값) 또는 <code>html</code>입니다.</td>
  </tr>
  <tr>
    <td>카테고리</td>
    <td><em>선택적 매개 변수</em>입니다.<br/>번역의 범주(도메인)를 지정하는 문자열입니다. 이 매개 변수를 사용하여 <a href="../customization.md">Custom Translator</a>로 작성된 사용자 지정 시스템의 번역을 가져옵니다. 에 사용자 지정 변환기에서 범주 ID를 추가 <a href="https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/how-to-create-project#view-project-details">세부 정보를 프로젝트</a> 이 매개 변수를 사용 하 여 배포 된 시스템을 사용자 지정 합니다. 기본값은 <code>general</code>입니다.</td>
  </tr>
  <tr>
    <td>profanityAction</td>
    <td><em>선택적 매개 변수</em>입니다.<br/>번역에서 욕설을 처리하는 방식을 지정합니다. 가능한 값은 <code>NoAction</code>(기본값), <code>Marked</code> 또는 <code>Deleted</code>입니다. 욕설을 처리하는 방식을 알아보려면 <a href="#handle-profanity">욕설 처리</a>를 참조하세요.</td>
  </tr>
  <tr>
    <td>profanityMarker</td>
    <td><em>선택적 매개 변수</em>입니다.<br/>번역에서 욕설을 표시하는 방식을 지정합니다. 가능한 값은 <code>Asterisk</code>(기본값) 또는 <code>Tag</code>입니다. 욕설을 처리하는 방식을 알아보려면 <a href="#handle-profanity">욕설 처리</a>를 참조하세요.</td>
  </tr>
  <tr>
    <td>includeAlignment</td>
    <td><em>선택적 매개 변수</em>입니다.<br/>소스 텍스트의 맞춤 도법을 번역된 텍스트에 포함할지 여부를 지정합니다. 가능한 값은 <code>true</code> 또는 <code>false</code>(기본값)입니다. </td>
  </tr>
  <tr>
    <td>includeSentenceLength</td>
    <td><em>선택적 매개 변수</em>입니다.<br/>입력 텍스트 및 번역된 텍스트에 대한 문장 경계를 포함할지 여부를 지정합니다. 가능한 값은 <code>true</code> 또는 <code>false</code>(기본값)입니다.</td>
  </tr>
  <tr>
    <td>suggestedFrom</td>
    <td><em>선택적 매개 변수</em>입니다.<br/>입력 텍스트의 언어를 식별할 수 없으면 대체 언어를 지정합니다. <code>from</code> 매개 변수를 생략하면 언어 자동 검색이 적용됩니다. 검색이 실패하면 <code>suggestedFrom</code> 언어로 간주됩니다.</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td><em>선택적 매개 변수</em>입니다.<br/>입력 텍스트의 스크립트를 지정합니다.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td><em>선택적 매개 변수</em>입니다.<br/>번역된 텍스트의 스크립트를 지정합니다.</td>
  </tr>
  <tr>
    <td>allowFallback</td>
    <td><em>선택적 매개 변수</em>입니다.<br/>사용자 지정 시스템이 없을 때 서비스가 일반 시스템으로 대체(fallback)되도록 지정합니다. 가능한 값은 <code>true</code>(기본값) 또는 <code>false</code>입니다.<br/><br/><code>allowFallback=false</code>는 번역 시 요청으로 지정된 <code>category</code>에 대해 학습된 시스템만 사용하도록 지정합니다. 언어 X에서 언어 Y로 번역할 때 피벗 언어 E를 통한 체인 연결이 필요할 경우 체인(X->E 및 E->Y)의 모든 시스템은 사용자 지정 시스템이어야 하고 동일한 범주를 사용해야 합니다. 특정 범주를 사용하는 시스템이 없는 경우 요청은 400 상태 코드를 반환합니다. <code>allowFallback=true</code>는 사용자 지정 시스템이 없을 때 서비스가 일반 시스템으로 대체(fallback)되도록 지정합니다.
</td>
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
    <td><em>필수 요청 헤더</em><br/>페이로드의 콘텐츠 형식을 지정합니다. 가능한 값은 <code>application/json</code>입니다.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td><em>필수 요청 헤더</em><br/>요청 본문의 길이입니다.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td><em>선택 사항</em>입니다.<br/>요청을 고유하게 식별하는 클라이언트 생성 ID입니다. <code>ClientTraceId</code>라는 쿼리 매개 변수를 사용하는 쿼리 문자열에서 추적 ID를 포함하는 경우 이 헤더를 생략할 수 있습니다.</td>
  </tr>
</table> 

## <a name="request-body"></a>요청 본문

요청 본문은 JSON 배열입니다. 각 배열 요소는 번역할 문자열을 나타내는 `Text`라는 문자열 속성이 포함된 JSON 개체입니다.

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

다음 제한 사항이 적용됩니다.

* 배열에는 최대 100개 요소가 있을 수 있습니다.
* 요청에 포함된 전체 텍스트는 공백을 포함하여 5,000자를 초과할 수 없습니다.

## <a name="response-body"></a>응답 본문

성공적인 응답은 입력 배열의 각 문자열에 대해 하나의 결과를 포함한 JSON 배열입니다. 결과 개체에는 다음과 같은 속성이 포함됩니다.

  * `detectedLanguage`: 다음 속성을 통해 검색된 언어를 설명하는 개체입니다.

      * `language`: 감지된 언어의 코드를 나타내는 문자열입니다.

      * `score`: 결과의 신뢰도를 나타내는 부동 값입니다. 점수는 0~1 사이이고 낮은 점수는 낮은 신뢰도를 나타냅니다.

    `detectedLanguage` 속성은 언어 자동 검색이 요청될 때만 결과 개체에 존재합니다.

  * `translations`: 번역 결과의 배열입니다. 배열 크기는 `to` 쿼리 매개 변수를 통해 지정된 대상 언어의 수와 일치합니다. 배열의 각 요소에는 다음이 포함됩니다.

    * `to`: 대상 언어의 언어 코드를 나타내는 문자열입니다.

    * `text`: 번역된 텍스트를 제공하는 문자열입니다.

    * `transliteration`: `toScript` 매개 변수로 지정된 스크립트에서 번역된 텍스트를 제공하는 개체입니다.

      * `script`: 대상 스크립트를 지정하는 문자열입니다.   

      * `text`: 대상 스크립트에서 번역된 텍스트를 제공하는 문자열입니다.

    음차가 수행되지 않으면 `transliteration` 개체가 포함되지 않습니다.

    * `alignment`: 이름이 `proj`인 단일 문자열 속성을 갖는 개체로, 입력 텍스트를 번역된 텍스트에 매핑합니다. 맞춤 정보는 요청 매개 변수 `includeAlignment`가 `true`일 때만 제공됩니다. 맞춤은 원본의 모든 단어에 대해 `[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]]` 형식의 문자열 값으로 반환됩니다.  콜론은 시작 및 끝 인덱스를 구분하고, 대시는 언어를 구분하고, 공백은 단어를 구분합니다. 한 단어는 다른 언어의 0개, 1개 또는 여러 단어와 정렬될 수 있고, 정렬된 단어가 비연속적일 수도 있습니다. 맞춤 정보를 사용할 수 없는 경우 alignment 요소가 비어 있습니다. 예제 및 제한 사항에 대해서는 [맞춤 정보 가져오기](#obtain-alignment-information)를 참조하세요.

    * `sentLen`: 입력 및 출력 텍스트의 문장 경계를 반환하는 개체입니다.

      * `srcSentLen`: 입력 텍스트의 문장 길이를 나타내는 정수 배열입니다. 배열의 길이는 문장의 수이고 값은 각 문장의 길이입니다.

      * `transSentLen`:  번역된 텍스트의 문장 길이를 나타내는 정수 배열입니다. 배열의 길이는 문장의 수이고 값은 각 문장의 길이입니다.

    문장 경계는 요청 매개 변수 `includeSentenceLength`가 `true`일 때만 포함됩니다.

  * `sourceText`: 이름이 `text`인 단일 문자열 속성을 갖는 개체로, 소스 언어의 기본 스크립트로 입력 텍스트를 제공합니다. `sourceText` 속성은 입력이 언어에 대한 일반적이지 않은 스크립트로 표현될 때만 제공됩니다. 예를 들어, 입력이 라틴 스크립트로 작성된 아랍어인 경우 `sourceText.text`는 아랍 스크립트로 변환된 동일한 아랍어 텍스트가 됩니다.

JSON 응답 예제는 [예제](#examples) 섹션에 제공됩니다.

## <a name="response-headers"></a>응답 헤더

<table width="100%">
  <th width="20%">헤더</th>
  <th>설명</th>
    <tr>
    <td>X-RequestId</td>
    <td>요청을 식별하기 위해 서비스에서 생성한 값입니다. 문제 해결을 위해 사용됩니다.</td>
  </tr>
  <tr>
    <td>X-MT-System</td>
    <td>번역에 대해 요청된 각 'to' 언어에 대해 번역에 사용된 시스템 형식을 지정합니다. 값은 쉼표로 구분된 문자열 목록입니다. 각 문자열은 다음 형식을 나타냅니다.<br/><ul><li>Custom - 요청에는 사용자 지정 시스템이 포함되고, 번역 중에 하나 이상의 사용자 지정 시스템이 사용되었습니다.</li><li>Team - 다른 모든 요청</li></td>
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
    <td>408</td>
    <td>리소스가 없으므로 요청을 이행할 수 없습니다. 세부 정보 오류 메시지를 확인합니다. 사용자 지정 <code>category</code>을 사용하는 경우 요청을 처리하기 위해 사용자 지정 번역 시스템을 아직 사용할 수 없음을 나타냅니다. 대기 기간(예: 1분) 후에 요청을 다시 시도합니다.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>서버는 클라이언트 요청 한도 초과 했기 때문에 요청을 거부 합니다.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>예기치 않은 오류가 발생했습니다. 이 오류가 계속 발생하는 경우 오류 날짜와 시간, 응답 헤더 <code>X-RequestId</code>의 요청 식별자 및 요청 헤더 <code>X-ClientTraceId</code>의 클라이언트 식별자를 사용해서 보고합니다.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>서버를 일시적으로 사용할 수 없습니다. 요청을 다시 시도하십시오. 이 오류가 계속 발생하는 경우 오류 날짜와 시간, 응답 헤더 <code>X-RequestId</code>의 요청 식별자 및 요청 헤더 <code>X-ClientTraceId</code>의 클라이언트 식별자를 사용해서 보고합니다.</td>
  </tr>
</table> 

오류가 발생하는 경우 요청은 JSON 오류 응답도 반환합니다. 오류 코드는 오류를 더 범주화하도록 뒤에 3자리 숫자가 오는 3자리 HTTP 상태 코드로 결합된 6자리 숫자입니다. 일반적인 오류 코드는 [v3 Translator Text API 참조 페이지](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors)에서 확인할 수 있습니다. 

## <a name="examples"></a>예

### <a name="translate-a-single-input"></a>단일 입력 번역

이 예제에서는 단일 문장을 영어에서 중국어 간체로 번역하는 방법을 보여 줍니다.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

응답 본문은 다음과 같습니다.

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    }
]
```

`translations` 배열에는 입력의 단일 텍스트 번역을 제공하는 하나의 요소가 포함됩니다.

### <a name="translate-a-single-input-with-language-auto-detection"></a>언어 자동 검색을 사용하여 단일 입력 번역

이 예제에서는 단일 문장을 영어에서 중국어 간체로 번역하는 방법을 보여 줍니다. 요청은 입력 언어를 지정하지 않습니다. 대신, 원본 언어의 자동 검색이 사용됩니다.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

응답 본문은 다음과 같습니다.

```
[
    {
        "detectedLanguage": {"language": "en", "score": 1.0},
        "translations":[
            {"text": "你好, 你叫什么名字？", "to": "zh-Hans"}
        ]
    }
]
```
응답은 앞의 예제에 나온 응답과 유사합니다. 언어 자동 검색이 요청되었으므로 응답에는 입력 텍스트에 대해 검색된 언어에 대한 정보도 포함됩니다. 

### <a name="translate-with-transliteration"></a>음차를 사용한 번역

음차를 추가하여 이전 예제를 확장해 보겠습니다. 다음 요청은 라틴 스크립트로 작성된 중국어 번역을 요구합니다.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans&toScript=Latn" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

응답 본문은 다음과 같습니다.

```
[
    {
        "detectedLanguage":{"language":"en","score":1.0},
        "translations":[
            {
                "text":"你好, 你叫什么名字？",
                "transliteration":{"script":"Latn", "text":"nǐ hǎo , nǐ jiào shén me míng zì ？"},
                "to":"zh-Hans"
            }
        ]
    }
]
```

이제 번역 결과에는 번역된 텍스트를 라틴 문자를 사용하여 제공하는 `transliteration` 속성이 포함됩니다.

### <a name="translate-multiple-pieces-of-text"></a>여러 텍스트 번역

한 번에 여러 문자열을 번역하려면 요청 본문에서 문자열 배열을 지정하기만 하면 됩니다.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

---

응답 본문은 다음과 같습니다.

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    },            
    {
        "translations":[
            {"text":"我很好，谢谢你。","to":"zh-Hans"}
        ]
    }
]
```

### <a name="translate-to-multiple-languages"></a>여러 언어로 번역

이 예제에서는 하나의 요청에서 동일한 입력을 여러 다른 언어로 번역하는 방법을 보여 줍니다.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

응답 본문은 다음과 같습니다.

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"},
            {"text":"Hallo, was ist dein Name?","to":"de"}
        ]
    }
]
```

### <a name="handle-profanity"></a>욕설 처리

일반적으로 Translator 서비스는 원본에 있는 욕설을 번역에서 유지합니다. 욕설의 정도와 단어가 욕설로 인식되는 상황은 문화권마다 다르므로, 대상 언어에서 욕설의 정도가 증폭되거나 감소할 수 있습니다.

원본 텍스트에 욕설이 있는지 여부에 관계없이 번역에 욕설을 가져오지 않으려면 욕설 필터링 옵션을 사용할 수 있습니다. 이 옵션을 사용하면 삭제된 욕설을 확인할지, 욕설에 적절한 태그를 지정할지(자체적인 사후 처리를 적용하기 위한 옵션 제공) 또는 어떤 작업도 수행하지 않을지를 선택할 수 있습니다. `ProfanityAction`의 허용되는 값은 `Deleted`, `Marked` 및 `NoAction`(기본값)입니다.

<table width="100%">
  <th width="20%">ProfanityAction</th>
  <th>조치</th>
  <tr>
    <td><code>NoAction</code></td>
    <td>기본 동작입니다. 욕설이 원본에서 대상으로 전달됩니다.<br/><br/>
    <strong>예제 원본(일본어)</strong>: 彼はジャッカスです。<br/>
    <strong>예제 번역(영어)</strong>: He is a jackass.
    </td>
  </tr>
  <tr>
    <td><code>Deleted</code></td>
    <td>욕설 단어가 바뀌지 않고 출력에서 제거됩니다.<br/><br/>
    <strong>예제 원본(일본어)</strong>: 彼はジャッカスです。<br/>
    <strong>예제 번역(영어)</strong>: He is a.
    </td>
  </tr>
  <tr>
    <td><code>Marked</code></td>
    <td>욕설이 출력에서 표식으로 바뀝니다. 표식은 <code>ProfanityMarker</code> 매개 변수에 따라 달라집니다.<br/><br/>
<code>ProfanityMarker=Asterisk</code>의 경우 욕설이 다음과 같이 <code>***</code>로 바뀝니다.<br/>
    <strong>예제 원본(일본어)</strong>: 彼はジャッカスです。<br/>
    <strong>예제 번역(영어)</strong>: He is a \*\*\*.<br/><br/>
<code>ProfanityMarker=Tag</code>의 경우 욕설이 다음과 같이 XML 태그 &lt;profanity&gt; 및 &lt;/profanity&gt;로 묶입니다.<br/>
    <strong>예제 원본(일본어)</strong>: 彼はジャッカスです。<br/>
    <strong>예제 번역(영어)</strong>: He is a &lt;profanity&gt;jackass&lt;/profanity&gt;.
  </tr>
</table> 

예를 들면 다음과 같습니다.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'This is a freaking good idea.'}]"
```

---

반환 결과

```
[
    {
        "translations":[
            {"text":"Das ist eine *** gute Idee.","to":"de"}
        ]
    }
]
```

비교 대상

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked&profanityMarker=Tag" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'This is a freaking good idea.'}]"
```

---

마지막 요청은 다음을 반환합니다.

```
[
    {
        "translations":[
            {"text":"Das ist eine <profanity>verdammt</profanity> gute Idee.","to":"de"}
        ]
    }
]
```

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>태그가 있는 콘텐츠 번역 및 번역 내용 결정

HTML 페이지의 콘텐츠나 XML 문서의 콘텐츠처럼 태그를 포함하는 콘텐츠를 번역하는 것은 일반적입니다. 태그가 있는 콘텐츠를 번역할 때는 쿼리 매개 변수 `textType=html`을 포함합니다. 또한 경우에 따라 번역에서 특정 콘텐츠를 제외하는 것이 유용합니다. 특성 `class=notranslate`를 사용하여 원래 언어로 유지해야 하는 콘텐츠를 지정할 수 있습니다. 다음 예제에서 첫 번째 `div` 요소 내에 포함된 콘텐츠는 번역되지 않지만 두 번째 `div` 요소에 있는 콘텐츠는 번역됩니다.

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

샘플 요청은 다음과 같습니다.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&textType=html" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'<div class=\"notranslate\">This will not be translated.</div><div>This will be translated.</div>'}]"
```

---

응답은 다음과 같습니다.

```
[
    {
        "translations":[
            {"text":"<div class=\"notranslate\">This will not be translated.</div><div>这将被翻译。</div>","to":"zh-Hans"}
        ]
    }
]
```

### <a name="obtain-alignment-information"></a>맞춤 정보 가져오기

맞춤 정보를 받으려면 쿼리 문자열에 `includeAlignment=true`를 지정합니다.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeAlignment=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'The answer lies in machine translation.'}]"
```

---

응답은 다음과 같습니다.

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique.",
                "to":"fr",
                "alignment":{"proj":"0:2-0:1 4:9-3:9 11:14-11:19 16:17-21:24 19:25-40:50 27:37-29:38 38:38-51:51"}
            }
        ]
    }
]
```

맞춤 정보는 `0:2-0:1`로 시작합니다. 이것은 원본 텍스트의 처음 세 문자(`The`)가 번역된 텍스트의 처음 두 문자(`La`)에 매핑됨을 의미합니다.

다음 제한 사항이 적용됩니다.

* 다음 언어 쌍의 하위 집합에 대해서만 맞춤이 반환됩니다.
  - 영어에서 다른 모든 언어로
  - 다른 모든 언어에서 영어로(중국어 간체, 중국어 번체 및 라트비아어에서 영어로 번역하는 경우 제외)
  - 일본어에서 한국어로 또는 한국어에서 일본어로
* 문장에 미리 준비된 번역이 있으면 맞춤을 받지 못합니다. 미리 준비된 번역의 예로 “This is a test”, “I love you”, 기타 빈도가 높은 문장 등이 있습니다.

### <a name="obtain-sentence-boundaries"></a>문장 경계 가져오기

원본 텍스트와 번역된 텍스트의 문장 길이에 대한 정보를 받으려면 쿼리 문자열에 `includeSentenceLength=true`를 지정합니다.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeSentenceLength=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'The answer lies in machine translation. The best machine translation technology cannot always provide translations tailored to a site or users like a human. Simply copy and paste a code snippet anywhere.'}]"
```

---

응답은 다음과 같습니다.

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique. La meilleure technologie de traduction automatique ne peut pas toujours fournir des traductions adaptées à un site ou des utilisateurs comme un être humain. Il suffit de copier et coller un extrait de code n’importe où.",
                "to":"fr",
                "sentLen":{"srcSentLen":[40,117,46],"transSentLen":[53,157,62]}
            }
        ]
    }
]
```

### <a name="translate-with-dynamic-dictionary"></a>동적 사전을 사용하여 번역

단어나 구에 적용할 번역을 이미 알고 있는 경우 요청 내에 태그로 제공할 수 있습니다. 동적 사전은 적절한 이름 및 제품 이름과 같은 복합 명사에 사용할 때만 안전합니다.

제공할 태그는 다음 구문을 사용합니다.

``` 
<mstrans:dictionary translation=”translation of phrase”>phrase</mstrans:dictionary>
```

예를 들어, 영어 문장 "The word wordomatic is a dictionary entry"를 고려해보세요. 단어 _wordomatic_을 번역에 유지하려면 다음 요청을 보냅니다.

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.'}]"
```

결과는 다음과 같습니다.

```
[
    {
        "translations":[
            {"text":"Das Wort "wordomatic" ist ein Wörterbucheintrag.","to":"de"}
        ]
    }
]
```

이 기능은 `textType=text` 또는 `textType=html`을 사용할 때와 같은 결과를 가져옵니다. 이 기능은 자주 사용하지 않는 것이 좋습니다. 번역을 사용자 지정하는 보다 적절하고 훨씬 더 나은 방법은 Custom Translator를 사용하는 것입니다. Custom Translator는 컨텍스트 및 통계적 확률을 최대한 활용합니다. 컨텍스트에 따라 사용자 작업 또는 구를 보여 주는 학습 데이터를 보유하고 있거나 이러한 데이터를 만들 수 있는 경제적 여유가 있으면 훨씬 더 나은 결과를 얻을 수 있습니다. [Custom Translator에 대해 자세히 알아보세요](../customization.md).
 





