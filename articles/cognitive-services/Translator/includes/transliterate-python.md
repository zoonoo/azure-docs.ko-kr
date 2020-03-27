---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 86ef8f3730fe7ae3ab3428956aaafb86331c5cf5
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "69906626"
---
[!INCLUDE [Prerequisites](prerequisites-python.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>프로젝트 만들기 및 필요한 모듈 가져오기

즐겨 찾는 IDE 또는 편집기를 사용하여 새 프로젝트를 만들거나 바탕 화면에 이름이 `transliterate-text.py`라는 파일이 포함된 새 폴더를 만듭니다. 그런 후 이 코드 조각을 프로젝트/파일에 복사합니다.

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
```

> [!NOTE]
> 이러한 모듈을 사용하지 않았다면 프로그램을 실행하기 전에 설치해야 합니다. 이러한 패키지를 설치하려면 `pip install requests uuid`를 실행합니다.

첫 번째 주석은 Python 해석기가 UTF-8 인코딩을 사용하라고 알려줍니다. 그런 다음, 필요한 모듈을 가져와 환경 변수에서 구독 키를 읽고 http 요청을 구성한 다음, 고유한 ID를 만들고 Translator Text API에서 반환하는 JSON 응답을 처리합니다.

## <a name="set-the-subscription-key-endpoint-and-path"></a>구독 키, 엔드포인트 및 경로 설정

이 샘플에서는 `TRANSLATOR_TEXT_KEY` 및 `TRANSLATOR_TEXT_ENDPOINT` 환경 변수에서 Translator Text 구독 키와 엔드포인트를 읽으려고 합니다. 환경 변수에 익숙하지 않은 경우 `subscription_key` 및 `endpoint`를 문자열로 설정하고 조건문을 주석으로 처리할 수 있습니다.

이 코드를 프로젝트에 복사합니다.

```python
key_var_name = 'TRANSLATOR_TEXT_SUBSCRIPTION_KEY'
if not key_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(key_var_name))
subscription_key = os.environ[key_var_name]

endpoint_var_name = 'TRANSLATOR_TEXT_ENDPOINT'
if not endpoint_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(endpoint_var_name))
endpoint = os.environ[endpoint_var_name]
```

Translator Text 글로벌 엔드포인트가 `endpoint`로 설정되어 있습니다. `path`는 `transliterate` 루트를 설정하며 API의 버전 3을 실행하기 원한다는 것을 식별합니다.

`params`는 입력 언어와 입력 및 출력 스크립트를 설정하는 데 사용됩니다. 이 샘플에서는 일본어를 라틴어 알파벳으로 음역합니다.

>[!NOTE]
> 엔드포인트, 루트 및 요청 매개 변수에 대한 자세한 내용은 [Translator Text API 3.0: Transliterate](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-transliterate)를 참조하세요.

```python
path = '/transliterate?api-version=3.0'
params = '&language=ja&fromScript=jpan&toScript=latn'
constructed_url = endpoint + path + params
```

## <a name="add-headers"></a>헤더 추가

요청을 인증하는 가장 쉬운 방법은 구독 키에서 `Ocp-Apim-Subscription-Key` 헤더로 전달하는 것이며, 이 샘플에서 이 방법을 사용할 것입니다. 대안으로, 액세스 토큰에 구독 키를 대체하고 `Authorization` 헤더로서 액세스 토큰을 전달하여 요청의 유효성을 검사할 수 있습니다. 자세한 내용은 [인증](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)을 참조하세요.

이 코드 조각을 프로젝트에 복사합니다.

```python
headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

Cognitive Services 다중 서비스 구독을 사용하는 경우 요청 매개 변수에 `Ocp-Apim-Subscription-Region`도 포함해야 합니다. [다중 서비스 구독을 사용한 인증에 대해 자세히 알아봅니다](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-request-to-transliterate-text"></a>텍스트 음역 요청 만들기

음역하려는 문자열을 정의합니다.

```python
# Transliterate "good afternoon" from source Japanese.
# Note: You can pass more than one object in body.
body = [{
    'text': 'こんにちは'
}]
```

다음으로, `requests` 모듈을 사용하는 POST 요청을 만듭니다. 세 가지 인수, 즉 잘려진 URL, 요청 헤더 및 요청 본문을 사용합니다.

```python
request = requests.post(constructed_url, headers=headers, json=body)
response = request.json()
```

## <a name="print-the-response"></a>응답 출력

마지막 단계는 결과를 인쇄하는 것입니다. 이 코드 조각은 키를 정렬하고 들여쓰기를 설정하며 항목 및 키 구분 기호를 선언하여 결과를 꾸밉니다.

```python
print(json.dumps(response, sort_keys=True, indent=4,
                 ensure_ascii=False, separators=(',', ': ')))
```

## <a name="put-it-all-together"></a>모든 요소 결합

이것으로, Translator Text API를 호출하여 JSON 응답을 반환하는 간단한 프로그램이 만들어집니다. 이제 프로그램을 실행해 보겠습니다.

```console
python transliterate-text.py
```

코드를 우리 것과 비교하고 싶다면 전체 샘플은 [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python)에 있습니다.

## <a name="sample-response"></a>샘플 응답

```json
[
    {
        "script": "latn",
        "text": "konnichiwa"
    }
]
```

## <a name="clean-up-resources"></a>리소스 정리

구독 키를 프로그램으로 하드 코딩한 경우 이 빠른 시작을 마치면 구독 키를 반드시 제거하세요.

## <a name="next-steps"></a>다음 단계

Translator Text API로 할 수 있는 모든 것에 대해 알아보려면 API 참조를 살펴보세요.

> [!div class="nextstepaction"]
> [API 참조](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
