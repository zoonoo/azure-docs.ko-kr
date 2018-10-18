---
title: Translator Speech API Languages 메서드
titleSuffix: Azure Cognitive Services
description: Translator Speech API Languages 메서드를 사용합니다.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: conceptual
ms.date: 05/18/18
ms.author: v-jansko
ms.openlocfilehash: b7005811898df9132be6bc199e26f6c6dc358618
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49345206"
---
# <a name="translator-speech-api-languages"></a>Translator Speech API: 언어

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Translator Speech는 서비스에서 지원되는 언어 목록을 지속적으로 확장합니다. 이 API를 사용하여 현재 Translator Speech 서비스에서 사용할 수 있는 언어 집합을 검색합니다.

이 API를 사용하여 사용 가능한 언어를 확인하는 방법을 보여 주는 코드 샘플은 [Microsoft Translator Github 사이트](https://github.com/MicrosoftTranslator)에서 사용할 수 있습니다.

## <a name="implementation-notes"></a>구현 참고 사항

GET /languages 

음성을 기록하고, 기록한 텍스트를 번역하고, 번역의 합성 음성을 생성하는 데 다양한 언어를 사용할 수 있습니다.

클라이언트는 `scope` 쿼리 매개 변수를 사용하여 관심 있는 언어의 집합을 정의합니다.

* **음성-텍스트:** 쿼리 매개 변수 `scope=speech`를 사용하여 음성을 텍스트로 기록하는 데 사용할 수 있는 언어 집합을 검색합니다.
* **텍스트 번역:** 쿼리 매개 변수 `scope=text`를 사용하여 기록한 텍스트를 번역하는 데 사용할 수 있는 언어 집합을 검색합니다.
* **텍스트 음성 변환:** 쿼리 매개 변수 `scope=tts`를 사용하여 번역된 텍스트를 다시 음성으로 합성하는 데 사용할 수 있는 언어 및 음성 집합을 검색합니다.

클라이언트는 쉼표로 구분된 선택 항목 목록을 지정하여 동시에 여러 집합을 검색할 수 있습니다. 예: `scope=speech,text,tts`.

성공적인 응답은 요청된 각 집합에 대한 하나의 속성이 있는 JSON 개체입니다.

```
{
    "speech": {
        //... Set of languages supported for speech-to-text
    },
    "text": {
        //... Set of languages supported for text translation
    },
    "tts": {
        //... Set of languages supported for text-to-speech
    }
}
```

클라이언트는 `scope` 쿼리 매개 변수를 사용하여 반환되어야 하는 지원 언어 집합을 선택할 수 있으므로, 실제 응답은 위에 표시된 모든 속성의 하위 집합만 포함할 수 있습니다.

각 속성에 제공되는 값은 다음과 같습니다.

### <a name="speech-to-text-speech"></a>음성 텍스트 변환(음성)

음성-텍스트 속성 `speech`와 연결된 값은 (키, 값) 쌍의 사전입니다. 각 키는 음성-텍스트 변환이 지원되는 언어를 식별합니다. 키는 클라이언트가 API에 전달하는 식별자입니다. 키와 연결된 값은 다음과 같은 속성을 갖는 개체입니다.

* `name`: 언어의 표시 이름입니다.
* `language`: 연결된 문자 언어의 언어 태그입니다. 아래의 "텍스트 트랜잭션"을 참조하세요.
예제는 다음과 같습니다.

```
{
    "speech": {
        "en-US": { name: "English", language: "en" }
    }
}
```

### <a name="text-translation-text"></a>텍스트 번역(텍스트)

`text` 속성과 연결된 값은 각 키가 텍스트 번역이 지원되는 언어를 식별하는 사전으로도 사용됩니다. 키와 연결된 값은 언어를 설명합니다.

* `name`: 언어의 표시 이름입니다.
* `dir`: 오른쪽에서 왼쪽 언어의 경우 `rtl` 또는 왼쪽에서 오른쪽 언어의 경우 `ltr`인 사전입니다.

예제는 다음과 같습니다.

```
{
    "text": {
        "en": { name: "English", dir: "ltr" }
    }
}
```

### <a name="text-to-speech-tts"></a>TTS(텍스트 음성 변환)

TTS(텍스트 음성 변환) 속성과 연결된 값은 각 키가 지원되는 음성을 식별하는 사전으로도 사용됩니다. 음성 개체의 특성은 다음과 같습니다.

* `displayName`: 음성의 표시 이름입니다.
* `gender`: 음성의 성별(남성 또는 여성)입니다.
* `locale`: 주 언어 하위 태브 및 지역 하위 태그가 지정된 음성의 언어 태그입니다.
* `language`: 연결된 문자 언어의 언어 태그입니다.
* `languageName`: 언어의 표시 이름입니다.
* `regionName`:이 언어에 대한 지역의 표시 이름입니다.

예제는 다음과 같습니다.

```
{
    "tts": {
        "en-US-Zira": {
            "displayName": "Zira",
            "gender": "female",
            "locale": "en-US",
            "languageName": "English",
            "regionName": "United States",
            "language": "en"
        }
}
```

### <a name="localization"></a>지역화
이 서비스는 텍스트 번역에서 지원되는 모든 언어에 대해 'Accept-Language' 헤더 언어의 모든 이름을 반환합니다.

### <a name="response-class-status-200"></a>응답 클래스(상태: 200)
지원되는 언어의 집합을 설명하는 개체입니다.

ModelExample 값: 

Langagues { speech (object, optional), text (object, optional), tts (object, optional) }

### <a name="headers"></a>헤더

|헤더|설명|type|
:--|:--|:--|
X-RequestId|서버가 요청을 식별하기 위해 생성하며, 문제 해결에 사용되는 값입니다.|string|

### <a name="parameters"></a>매개 변수

|매개 변수|설명|매개 변수 형식|데이터 형식|
|:--|:--|:--|:--|
|api-version    |클라이언트에서 요청한 API 버전입니다. 허용되는 값은 `1.0`입니다.|쿼리|string|
|scope  |클라이언트에 반환될 지원되는 언어 또는 음성 설정입니다. 이 매개 변수는 쉼표로 구분된 키워드 목록으로 지정됩니다. 사용할 수 있는 키워드는 다음과 같습니다.<ul><li>`speech`: 음성을 기록하도록 지원되는 언어 집합을 제공합니다.</li><li>`tts`: 텍스트 음성 변환이 지원되는 음성의 집합을 제공합니다.</li><li>`text`: 텍스트를 번역하도록 지원되는 언어 집합을 제공합니다.</li></ul>값을 지정하지 않으면 `scope` 값의 기본값은 `text`입니다.|쿼리|string|
|X-ClientTraceId    |요청을 추적하는 데 사용되는 클라이언트 생성 GUID입니다. 용이한 문제 해결을 위해, 클라이언트는 각 요청에 새 값을 제공하고 로깅해야 합니다.|머리글|string|
|Accept-Language    |응답에 있는 필드 중 일부는 언어 또는 지역 이름입니다. 이 매개 변수를 사용하여 이름을 반환하는 언어를 정의합니다. 언어는 올바른 형식의 BCP 47 언어 태그를 제공하여 지정됩니다. `text` 범위와 함께 반환된 언어 식별자 목록에서 태그를 선택합니다. 지원되지 않는 언어의 경우 이 이름은 영어로 제공됩니다.<br/>예를 들어, `fr` 값을 사용하여 프랑스어로 이름을 요청하거나 `zh-Hant` 값을 사용하여 중국어 번체로 이름을 요청합니다.|머리글|string|
    
### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|이유|
|:--|:--|
|400|잘못된 요청입니다. 입력 매개 변수가 유효한지 확인합니다. 응답 개체에는 좀 더 자세한 오류 설명이 포함되어 있습니다.|
|429|요청이 너무 많습니다.|
|500|오류가 발생했습니다. 오류가 계속 발생하는 경우 클라이언트 추적 식별자(X-ClientTraceId) 또는 요청 식별자(X-RequestId)를 사용하여 보고하세요.|
|503|서버를 일시적으로 사용할 수 없습니다. 요청을 다시 시도하세요. 오류가 계속 발생하는 경우 클라이언트 추적 식별자(X-ClientTraceId) 또는 요청 식별자(X-RequestId)를 사용하여 보고하세요.|
