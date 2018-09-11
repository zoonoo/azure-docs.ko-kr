---
title: Translator Text Go를 사용하여 지원되는 언어 가져오기 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 이 빠른 시작에서는 Cognitive Services에서 Go와 함께 Translator Text API를 사용하여 번역, 음역, 사전 조회에 지원되는 언어 목록과 예제를 가져옵니다.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/29/2018
ms.author: nolachar
ms.openlocfilehash: 91ba39f072d97a87250a2d6284df8571905c1b0f
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2018
ms.locfileid: "43750512"
---
# <a name="quickstart-get-supported-languages-with-go"></a>빠른 시작: Go를 사용하여 지원되는 언어 가져오기

이 빠른 시작에서는 Translator Text API를 사용하여 번역, 음역, 사전 조회에 지원되는 언어 목록과 예제를 가져옵니다.

## <a name="prerequisites"></a>필수 조건

이 코드를 실행하려면 [Go 배포판](https://golang.org/doc/install)을 설치해야 합니다. 샘플 코드는 **핵심** 라이브러리만 사용하므로 외부 종속성이 없습니다.

Translator Text API를 사용하려면 구독 키도 필요합니다. [Translator Text API에 등록하는 방법](translator-text-how-to-signup.md)을 참조하세요.

## <a name="languages-request"></a>언어 요청

다음 코드는 [Languages](./reference/v3-0-languages.md) 메서드를 사용하여 번역, 음역, 사전 조회에 지원되는 언어 목록과 예제를 가져옵니다.

1. 원하는 코드 편집기에서 Go 프로젝트를 새로 만듭니다.
2. 아래 제공된 코드를 추가합니다.
3. `subscriptionKey` 값을 구독에 유효한 액세스 키로 바꿉니다.
4. '.go' 확장명으로 파일을 저장합니다.
5. Go가 설치된 컴퓨터에서 명령 프롬프트를 엽니다.
6. 파일을 빌드합니다(예: 'go build quickstart-languages.go').
7. 파일을 실행합니다(예: 'quickstart-languages').

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "time"
)

func main() {
    // Replace the subscriptionKey string value with your valid subscription key
    const subscriptionKey = "<Subscription Key>"

    const uriBase = "https://api.cognitive.microsofttranslator.com"
    const uriPath = "/languages?api-version=3.0"

    const uri = uriBase + uriPath

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("GET", uri, nil)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    resp, err := client.Do(req)
    if err != nil {
        fmt.Printf("Error on request: %v\n", err)
        return
    }
    defer resp.Body.Close()

    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Printf("Error reading response body: %v\n", err)
        return
    }

    var f interface{}
    json.Unmarshal(body, &f)

    jsonFormatted, err := json.MarshalIndent(f, "", "  ")
    if err != nil {
        fmt.Printf("Error producing JSON: %v\n", err)
        return
    }
    fmt.Println(string(jsonFormatted))
}
```

## <a name="languages-response"></a>언어 응답

성공한 응답은 다음 예제와 같이 JSON으로 반환됩니다.

```json
{
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
  },
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
  }
}
```

## <a name="next-steps"></a>다음 단계

GitHub의 [Go용 Azure SDK](https://github.com/Azure/azure-sdk-for-go)에서 Cognitive Services API용 Go 패키지를 살펴봅니다.

> [!div class="nextstepaction"]
> [GitHub에서 Go 패키지 살펴보기](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices)
