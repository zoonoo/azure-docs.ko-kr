---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: a491249e21db433d878a59cad19f59f8987b638b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586884"
---
[!INCLUDE [Prerequisites](prerequisites-python.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>프로젝트 만들기 및 필요한 모듈 가져오기

즐겨찾는 IDE 또는 편집기를 사용하여 새 Python 프로젝트를 만듭니다. 그런 다음, 아래 코드 조각을 `get-languages.py`라는 파일의 프로젝트에 복사합니다.

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
```

> [!NOTE]
> 이러한 모듈을 사용하지 않았다면 프로그램을 실행하기 전에 설치해야 합니다. 이러한 패키지를 설치하려면 `pip install requests uuid`를 실행합니다.

첫 번째 주석은 Python 해석기가 UTF-8 인코딩을 사용하라고 알려줍니다. 그런 다음, 필요한 모듈을 가져와 환경 변수에서 구독 키를 읽고 http 요청을 구성한 다음, 고유한 ID를 만들고 Translator에서 반환하는 JSON 응답을 처리합니다.

## <a name="set-the-endpoint-and-path"></a>엔드포인트 및 경로 설정

이 샘플에서는 환경 변수 `TRANSLATOR_TEXT_ENDPOINT`에서 Translator 엔드포인트를 읽으려고 합니다. 환경 변수를 잘 모르는 경우에는 `endpoint`를 문자열로 설정하고 조건문을 주석으로 처리할 수 있습니다.

```python
endpoint_var_name = 'TRANSLATOR_TEXT_ENDPOINT'
if not endpoint_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(endpoint_var_name))
endpoint = os.environ[endpoint_var_name]
```

Translator 글로벌 엔드포인트가 `endpoint`로 설정되어 있습니다. `path`는 `languages` 루트를 설정하며 API의 버전 3을 실행하기 원한다는 것을 식별합니다.

>[!NOTE]
> 엔드포인트, 경로 및 요청 매개 변수에 대한 자세한 내용은 [Translator 3.0: 언어](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages)를 참조하세요.

```python
path = '/languages?api-version=3.0'
constructed_url = endpoint + path
```

## <a name="add-headers"></a>헤더 추가

지원되는 언어를 가져오는 요청에는 인증이 필요하지 않습니다. `Content-type`을 `application/json`으로 설정하고 `X-ClientTraceId`를 추가하여 요청을 고유하게 식별합니다.

이 코드 조각을 프로젝트에 복사합니다.

```python
headers = {
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

Cognitive Services 다중 서비스 구독을 사용하는 경우 요청 매개 변수에 `Ocp-Apim-Subscription-Region`도 포함해야 합니다. [다중 서비스 구독을 사용한 인증에 대해 자세히 알아봅니다](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-request-to-get-a-list-of-supported-languages"></a>지원되는 언어 목록을 가져오는 요청 만들기

`requests` 모듈을 사용하여 GET 요청을 만듭니다. 두 가지 인수, 즉 잘려진 URL와 요청 헤더를 사용합니다.

```python
request = requests.get(constructed_url, headers=headers)
response = request.json()
```

## <a name="print-the-response"></a>응답 출력

마지막 단계는 결과를 인쇄하는 것입니다. 이 코드 조각은 키를 정렬하고 들여쓰기를 설정하며 항목 및 키 구분 기호를 선언하여 결과를 꾸밉니다.

```python
print(json.dumps(response, sort_keys=True, indent=4,
                 ensure_ascii=False, separators=(',', ': ')))
```

## <a name="put-it-all-together"></a>모든 요소 결합

이것으로, Translator를 호출하여 JSON 응답을 반환하는 간단한 프로그램이 만들어집니다. 이제 프로그램을 실행해 보겠습니다.

```console
python get-languages.py
```

코드를 우리 것과 비교하고 싶다면 전체 샘플은 [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python)에 있습니다.

## <a name="sample-response"></a>샘플 응답

국가/지역 약어는 이 [언어 목록](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)에서 확인하세요.

이 샘플은 결과 조각을 표시하도록 잘렸습니다.

```json
{
    "translation": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr"
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl"
        },
        ...
    },
    "transliteration": {
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "scripts": [
                {
                    "code": "Arab",
                    "name": "Arabic",
                    "nativeName": "العربية",
                    "dir": "rtl",
                    "toScripts": [
                        {
                            "code": "Latn",
                            "name": "Latin",
                            "nativeName": "اللاتينية",
                            "dir": "ltr"
                        }
                    ]
                },
                {
                    "code": "Latn",
                    "name": "Latin",
                    "nativeName": "اللاتينية",
                    "dir": "ltr",
                    "toScripts": [
                        {
                            "code": "Arab",
                            "name": "Arabic",
                            "nativeName": "العربية",
                            "dir": "rtl"
                        }
                    ]
                }
            ]
        },
      ...
    },
    "dictionary": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
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
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
      ...
    }
}
```

## <a name="clean-up-resources"></a>리소스 정리

구독 키를 프로그램으로 하드 코딩한 경우 이 빠른 시작을 마치면 구독 키를 반드시 제거하세요.

## <a name="next-steps"></a>다음 단계

Translator로 할 수 있는 모든 것에 대해 알아보려면 API 참조를 살펴보세요.

> [!div class="nextstepaction"]
> [API 참조](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
