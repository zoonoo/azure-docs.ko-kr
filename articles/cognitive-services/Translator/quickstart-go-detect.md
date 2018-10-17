---
title: '빠른 시작: 텍스트의 언어 식별 - Translator Text, Go'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Go와 함께 Translator Text API를 사용하여 원본 텍스트의 언어를 식별합니다.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/29/2018
ms.author: nolachar
ms.openlocfilehash: 25834fae2ce60d93ebeb3a25298b97294748bf9b
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129712"
---
# <a name="quickstart-identify-language-from-text-with-go"></a>빠른 시작: Go를 사용하여 텍스트의 언어 식별

이 빠른 시작에서는 Translator Text API를 사용하여 원본 텍스트의 언어를 식별합니다.

## <a name="prerequisites"></a>필수 조건

이 코드를 실행하려면 [Go 배포판](https://golang.org/doc/install)을 설치해야 합니다. 샘플 코드는 **핵심** 라이브러리만 사용하므로 외부 종속성이 없습니다.

Translator Text API를 사용하려면 구독 키도 필요합니다. [Translator Text API에 등록하는 방법](translator-text-how-to-signup.md)을 참조하세요.

## <a name="detect-request"></a>감지 요청

다음 코드는 [Detect](./reference/v3-0-detect.md) 메서드를 사용하여 원본 텍스트의 언어를 식별합니다.

1. 원하는 코드 편집기에서 Go 프로젝트를 새로 만듭니다.
2. 아래 제공된 코드를 추가합니다.
3. `subscriptionKey` 값을 구독에 유효한 액세스 키로 바꿉니다.
4. '.go' 확장명으로 파일을 저장합니다.
5. Go가 설치된 컴퓨터에서 명령 프롬프트를 엽니다.
6. 파일을 빌드합니다(예: 'go build quickstart-detect.go').
7. 파일을 실행합니다(예: 'quickstart-detect').

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strconv"
    "strings"
    "time"
)

func main() {
    // Replace the subscriptionKey string value with your valid subscription key
    const subscriptionKey = "<Subscription Key>"

    const uriBase = "https://api.cognitive.microsofttranslator.com"
    const uriPath = "/detect?api-version=3.0"

    const uri = uriBase + uriPath

    const text = "Salve, mondo!"

    r := strings.NewReader("[{\"Text\" : \"" + text + "\"}]")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Content-Length", strconv.FormatInt(req.ContentLength, 10))
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

## <a name="detect-response"></a>감지 응답

성공한 응답은 다음 예제와 같이 JSON으로 반환됩니다.

```json
[
  {
    "alternatives": [
      {
        "isTranslationSupported": true,
        "isTransliterationSupported": false,
        "language": "pt",
        "score": 1
      },
      {
        "isTranslationSupported": true,
        "isTransliterationSupported": false,
        "language": "en",
        "score": 1
      }
    ],
    "isTranslationSupported": true,
    "isTransliterationSupported": false,
    "language": "it",
    "score": 1
  }
]
```

## <a name="next-steps"></a>다음 단계

GitHub의 [Go용 Azure SDK](https://github.com/Azure/azure-sdk-for-go)에서 Cognitive Services API용 Go 패키지를 살펴봅니다.

> [!div class="nextstepaction"]
> [GitHub에서 Go 패키지 살펴보기](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices)
