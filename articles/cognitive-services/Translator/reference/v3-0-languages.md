---
title: Microsoft Translator Text API 언어 메서드 | Microsoft Docs
description: Microsoft Translator Text API 언어 메서드를 사용합니다.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 93c06218a560faf439f05903438d021b372ce257
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376438"
---
# <a name="text-api-30-languages"></a>Text API 3.0: 언어

현재 Text API의 다른 작업에서 지원하는 언어 집합을 가져옵니다. 

## <a name="request-url"></a>요청 URL

다음에 `GET` 요청을 보냅니다.
```HTTP
https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
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
    <td>scope</td>
    <td>*선택적 매개 변수*<br/>반환할 언어의 그룹을 정의하는 쉼표로 구분된 이름 목록입니다. 허용 그룹 이름은 `translation`, `transliteration` 및 `dictionary`입니다. 범위가 지정되지 않는 경우 모든 그룹이 반환됩니다. 그러면 `scope=translation,transliteration,dictionary`를 전달하는 것과 동일합니다. 시나리오에 적합한 지원되는 언어의 집합을 결정하려면 [응답 개체](#response-body)에 대한 설명을 참조하세요.</td>
  </tr>
</table> 

요청 헤더는 다음과 같습니다.

<table width="100%">
  <th width="20%">헤더</th>
  <th>설명</th>
  <tr>
    <td>Accept-Language</td>
    <td>*선택적 요청 헤더*<br/>사용자 인터페이스 문자열에 사용할 언어입니다. 응답에 있는 필드 중 일부는 언어의 이름 또는 지역 이름입니다. 이 매개 변수를 사용하여 이러한 이름을 반환하는 언어를 정의합니다. 언어는 올바른 형식의 BCP 47 언어 태그를 제공하여 지정됩니다. 예를 들어 `fr` 값을 사용하여 프랑스어로 이름을 요청하거나 `zh-Hant` 값을 사용하여 중국어 번체로 이름을 요청합니다.<br/>대상 언어를 지정하지 않거나 지역화를 사용할 수 없는 경우 이름은 영어로 제공됩니다.
    </td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*선택적 요청 헤더*<br/>요청을 고유하게 식별하는 클라이언트 생성 GUID입니다.</td>
  </tr>
</table> 

인증은 언어 리소스를 가져올 필요가 없습니다.

## <a name="response-body"></a>응답 본문

클라이언트는 `scope` 쿼리 매개 변수를 사용하여 관심 있는 언어의 그룹을 정의합니다.

* `scope=translation` 한 언어에서 다른 언어로 텍스트를 번역하는 데 지원되는 언어를 제공합니다.

* `scope=transliteration`은 특정 언어의 텍스트를 한 스크립트에서 다른 스크립트로 음차하여 변환하는 기능을 제공합니다.

* `scope=dictionary`는 `Dictionary` 작업이 데이터를 반환하는 언어 쌍을 제공합니다.

클라이언트는 쉼표로 구분된 이름 목록을 지정하여 동시에 여러 그룹을 검색할 수 있습니다. 예를 들어 `scope=translation,transliteration,dictionary`는 모든 그룹에 지원되는 언어를 반환합니다.

성공적인 응답은 요청된 각 그룹에 대한 하나의 속성이 있는 JSON 개체입니다.

```json
{
    "translation": {
        //... set of languages supported to translate text (scope=translation)
    },
    "transliteration": {
        //... set of languages supported to convert between scripts (scope=transliteration)
    },
    "dictionary": {
        //... set of languages supported for alternative translations and examples (scope=dictionary)
    }
}
```

각 속성에 대한 값은 다음과 같습니다.

* `translation` 속성

  `translation` 속성의 값은 키 값 쌍의 사전입니다. 각 키는 BCP 47 언어 태그입니다. 키는 텍스트를 번역할 수 있는 대상 또는 원본 언어를 식별합니다. 키와 연결된 값은 언어를 설명하는 속성이 있는 JSON 개체입니다.

  * `name`: `Accept-Language` 헤더를 통해 요청된 로캘에서 언어 이름을 표시합니다.

  * `nativeName`: 이 언어에 기본인 로캘에서 언어 이름을 표시합니다.

  * `dir`: 오른쪽에서 왼쪽 언어의 경우 `rtl` 또는 왼쪽에서 오른쪽 언어의 경우 `ltr`인 사전입니다.

  예제는 다음과 같습니다.
          
  ```json
  {
    "translation": {
      ...
      "fr": {
        "name": "French",
        "nativeName": "Français",
        "dir": "ltr"
      },
      ...
    }
  }
  ```

* `transliteration` 속성

  `transliteration` 속성의 값은 키 값 쌍의 사전입니다. 각 키는 BCP 47 언어 태그입니다. 키는 하나의 스크립트에서 다른 스크립트로 텍스트를 변환할 수 있는 언어를 식별합니다. 키와 연결된 값은 언어 및 지원되는 해당 언어를 설명하는 속성이 있는 JSON 개체입니다.

  * `name`: `Accept-Language` 헤더를 통해 요청된 로캘에서 언어 이름을 표시합니다.

  * `nativeName`: 이 언어에 기본인 로캘에서 언어 이름을 표시합니다.

  * `scripts`: 변환할 스크립트의 목록입니다. `scripts` 목록의 각 요소에는 다음 속성이 있습니다.

    * `code`: 스크립트를 식별하는 코드입니다.

    * `name`: `Accept-Language` 헤더를 통해 요청된 로캘에서 스크립트 이름을 표시합니다.

    * `nativeName`: 언어에 기본인 로캘에서 언어 이름을 표시합니다.

    * `dir`: 오른쪽에서 왼쪽 언어의 경우 `rtl` 또는 왼쪽에서 오른쪽 언어의 경우 `ltr`인 사전입니다.

    * `toScripts`: 텍스트를 변환하는 데 사용할 수 있는 스크립트 목록입니다. `toScripts` 목록의 각 요소에는 앞에서 설명한 대로 `code`, `name`, `nativeName` 및 `dir` 속성이 있습니다.

  예제는 다음과 같습니다.

  ```json
  {
    "transliteration": {
      ...
      "ja": {
        "name": "Japanese",
        "nativeName": "日本語",
        "scripts": [
          {
            "code": "Jpan",
            "name": "Japanese",
            "nativeName": "日本語",
            "dir": "ltr",
            "toScripts": [
              {
                "code": "Latn",
                "name": "Latin",
                "nativeName": "ラテン語",
                "dir": "ltr"
              }
            ]
          },
          {
            "code": "Latn",
            "name": "Latin",
            "nativeName": "ラテン語",
            "dir": "ltr",
            "toScripts": [
              {
                "code": "Jpan",
                "name": "Japanese",
                "nativeName": "日本語",
                "dir": "ltr"
              }
            ]
          }
        ]
      },
      ...
    }
  }
  ```

* `dictionary` 속성

  `dictionary` 속성의 값은 키 값 쌍의 사전입니다. 각 키는 BCP 47 언어 태그입니다. 키는 대체 번역 및 백 번역을 사용할 수 있는 언어를 식별합니다. 값은 사용할 수 있는 번역을 포함하는 소스 언어 및 대상 언어를 설명하는 JSON 개체입니다.

  * `name`: `Accept-Language` 헤더를 통해 요청된 로캘에서 소스 언어 이름을 표시합니다.

  * `nativeName`: 이 언어에 기본인 로캘에서 언어 이름을 표시합니다.

  * `dir`: 오른쪽에서 왼쪽 언어의 경우 `rtl` 또는 왼쪽에서 오른쪽 언어의 경우 `ltr`인 사전입니다.

  * `translations`: 소스 언어로 표현된 쿼리에 대한 대체 번역 및 예제를 포함한 언어 목록입니다. `translations` 목록의 각 요소에는 다음 속성이 있습니다.

    * `name`: `Accept-Language` 헤더를 통해 요청된 로캘에서 대상 언어 이름을 표시합니다.

    * `nativeName`: 대상 언어에 기본인 로캘에서 대상 언어 이름을 표시합니다.

    * `dir`: 오른쪽에서 왼쪽 언어의 경우 `rtl` 또는 왼쪽에서 오른쪽 언어의 경우 `ltr`인 사전입니다.
    
    * `code`: 대상 언어를 식별하는 언어 코드입니다.

  예제는 다음과 같습니다.

  ```json
  "es": {
    "name": "Spanish",
    "nativeName": "Español",
    "dir": "ltr",
    "translations": [
      {
        "name": "English",
        "nativeName": "English",
        "dir": "ltr",
        "code": "en"
      }
    ]
  },
  ```

응답 개체의 구조는 API 버전을 변경하지 않고 변경되지 않습니다. 동일한 버전의 API의 경우 Microsoft Translator가 해당 서비스에서 지원하는 언어 목록을 지속적으로 확장하기 때문에 사용 가능한 언어 목록은 시간이 지남에 따라 변경될 수 있습니다.

지원되는 언어 목록은 자주 변경되지 않습니다. 네트워크 대역폭을 절약하고 응답성을 향상시키려면 클라이언트 응용 프로그램은 캐싱 언어 리소스 및 해당 엔터티 태그(`ETag`)를 고려해야 합니다. 그런 다음, 클라이언트 응용 프로그램은 주기적으로(예: 24시간마다) 지원되는 언어의 최신 집합을 인출하는 서비스를 쿼리합니다. 현재 `If-None-Match` 헤더 필드에서 `ETag` 값을 전달하면 서비스가 응답을 최적화할 수 있습니다. 리소스가 수정되지 않은 경우 서비스는 상태 코드 304 및 빈 응답 본문을 반환합니다.

## <a name="response-headers"></a>응답 헤더

<table width="100%">
  <th width="20%">헤더</th>
  <th>설명</th>
  <tr>
    <td>ETag</td>
    <td>지원되는 언어의 요청된 그룹에 대한 엔터티 태그의 현재 값입니다. 보다 효율적인 후속 요청을 위해 클라이언트는 `If-None-Match` 헤더 필드에서 `ETag` 값을 보낼 수 있습니다.
    </td>
  </tr>
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
    <td>304</td>
    <td>버전이 요청 헤더 `If-None-Match`에 지정되었으므로 리소스가 수정되지 않았습니다.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>쿼리 매개 변수 중 하나가 누락되었거나 유효하지 않습니다. 다시 시도하기 전에 요청 매개 변수를 수정합니다.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>호출자가 너무 많은 요청을 보냅니다.</td>
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

## <a name="examples"></a>예

다음 예제에서는 텍스트 번역에 지원되는 언어를 검색하는 방법을 보여줍니다.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl "https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation"
```

---
