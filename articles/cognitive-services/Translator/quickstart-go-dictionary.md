---
title: '빠른 시작: 대체 번역 찾기, Go - Translator Text API'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Go와 함께 Translator Text API를 사용하여 컨텍스트의 용어를 대체하는 번역과 예제를 찾아봅니다.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/29/2018
ms.author: erhopf
ms.openlocfilehash: 0c4872aaf222110a0044095040db08d0180e37c4
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49649638"
---
# <a name="quickstart-find-alternate-translations-with-the-translator-text-rest-api-go"></a>빠른 시작: Translator Text REST API(Go)로 대체 번역 찾기

이 빠른 시작에서는 Translator Text API를 사용하여 용어를 대신할 수 있는 대체 번역의 상세 정보와 대체 번역의 사용 예제를 찾아봅니다.

## <a name="prerequisites"></a>필수 조건

이 코드를 실행하려면 [Go 배포판](https://golang.org/doc/install)을 설치해야 합니다. 샘플 코드는 **핵심** 라이브러리만 사용하므로 외부 종속성이 없습니다.

Translator Text API를 사용하려면 구독 키도 필요합니다. [Translator Text API에 등록하는 방법](translator-text-how-to-signup.md)을 참조하세요.

## <a name="dictionary-lookup-request"></a>사전 조회 요청

다음은 [Dictionary Lookup](./reference/v3-0-dictionary-lookup.md) 메서드를 사용하여 단어의 대체 번역을 가져옵니다.

1. 원하는 코드 편집기에서 Go 프로젝트를 새로 만듭니다.
2. 아래 제공된 코드를 추가합니다.
3. `subscriptionKey` 값을 구독에 유효한 액세스 키로 바꿉니다.
4. '.go' 확장명으로 파일을 저장합니다.
5. Go가 설치된 컴퓨터에서 명령 프롬프트를 엽니다.
6. 파일을 빌드합니다(예: 'go build quickstart-lookup.go').
7. 파일을 실행합니다(예: 'quickstart-lookup').

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
    const uriPath = "/dictionary/lookup?api-version=3.0"

    // From English to French
    const params = "&from=en&to=fr"

    const uri = uriBase + uriPath + params

    const text = "great"

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

## <a name="dictionary-lookup-response"></a>사전 조회 응답

성공한 응답은 다음 예제와 같이 JSON으로 반환됩니다.

```json
[
  {
    "normalizedSource": "great",
    "displaySource": "great",
    "translations": [
      {
        "normalizedTarget": "grand",
        "displayTarget": "grand",
        "posTag": "ADJ",
        "confidence": 0.2783,
        "prefixWord": "",
        "backTranslations": [
          {
            "normalizedText": "great",
            "displayText": "great",
            "numExamples": 15,
            "frequencyCount": 34358
          },
          {
            "normalizedText": "big",
            "displayText": "big",
            "numExamples": 15,
            "frequencyCount": 21770
          },
...
        ]
      },
      {
        "normalizedTarget": "super",
        "displayTarget": "super",
        "posTag": "ADJ",
        "confidence": 0.1514,
        "prefixWord": "",
        "backTranslations": [
          {
            "normalizedText": "super",
            "displayText": "super",
            "numExamples": 15,
            "frequencyCount": 12023
          },
          {
            "normalizedText": "great",
            "displayText": "great",
            "numExamples": 15,
            "frequencyCount": 10931
          },
...
        ]
      },
...
    ]
  }
]
```

## <a name="dictionary-examples-request"></a>사전 예제 요청

다음은 [Dictionary Examples](./reference/v3-0-dictionary-examples.md) 메서드를 사용하여 사전의 용어를 사용하는 방법에 대한 컨텍스트 예제를 가져옵니다.

1. 원하는 코드 편집기에서 Go 프로젝트를 새로 만듭니다.
2. 아래 제공된 코드를 추가합니다.
3. `subscriptionKey` 값을 구독에 유효한 액세스 키로 바꿉니다.
4. '.go' 확장명으로 파일을 저장합니다.
5. Go가 설치된 컴퓨터에서 명령 프롬프트를 엽니다.
6. 파일을 빌드합니다(예: 'go build quickstart-examples.go').
7. 파일을 실행합니다(예: 'quickstart-examples').

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
    const uriPath = "/dictionary/examples?api-version=3.0"

    // From English to French
    const params = "&from=en&to=fr"

    const uri = uriBase + uriPath + params

    const text = "great"
    const translation = "formidable"

    r := strings.NewReader("[{\"Text\" : \"" + text + "\", \"Translation\" : \"" + translation + "\"}]")

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

## <a name="dictionary-examples-response"></a>사전 예제 응답

성공한 응답은 다음 예제와 같이 JSON으로 반환됩니다.

```json
[
  {
    "normalizedSource": "great",
    "normalizedTarget": "formidable",
    "examples": [
      {
        "sourcePrefix": "You have a ",
        "sourceTerm": "great",
        "sourceSuffix": " expression there.",
        "targetPrefix": "Vous avez une expression ",
        "targetTerm": "formidable",
        "targetSuffix": "."
      },
      {
        "sourcePrefix": "You played a ",
        "sourceTerm": "great",
        "sourceSuffix": " game today.",
        "targetPrefix": "Vous avez été ",
        "targetTerm": "formidable",
        "targetSuffix": "."
      },
...
    ]
  }
]
```

## <a name="next-steps"></a>다음 단계

GitHub의 [Go용 Azure SDK](https://github.com/Azure/azure-sdk-for-go)에서 Cognitive Services API용 Go 패키지를 살펴봅니다.

> [!div class="nextstepaction"]
> [GitHub에서 Go 패키지 살펴보기](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices)
