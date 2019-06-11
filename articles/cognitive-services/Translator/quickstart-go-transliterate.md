---
title: '빠른 시작: 텍스트 스크립트 변환, Go - Translator Text API'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Go와 함께 Translator Text API를 사용하여 하나의 스크립트에서 한 언어의 텍스트를 다른 언어로 변환합니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: erhopf
ms.openlocfilehash: 748754bdeec34d864efd05297664b935a06f1a06
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515204"
---
# <a name="quickstart-use-the-translator-text-api-to-transliterate-text-using-go"></a>빠른 시작: Translator Text API를 사용하여 Go를 통해 텍스트 음역

이 빠른 시작에서는 Translator Text API를 사용하여 한 언어의 텍스트를 다른 언어로 변환합니다.

이 빠른 시작에는Translator Text 리소스와 함께 [Azure Cognitive Services 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)이 필요합니다. 계정이 없는 경우 [평가판](https://azure.microsoft.com/try/cognitive-services/)을 사용하여 구독 키를 가져올 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작에는 다음이 필요합니다.

* [Go](https://golang.org/doc/install)
* Translator Text에 대한 Azure 구독 키

## <a name="create-a-project-and-import-required-modules"></a>프로젝트 만들기 및 필요한 모듈 가져오기

선호하는 IDE 또는 편집기를 사용하여 새 Go 프로젝트를 만듭니다. 그런 다음, 아래 코드 조각을 `transliterate-text.go`라는 파일의 프로젝트에 복사합니다.

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
    "os"
)
```

## <a name="create-the-main-function"></a>main 함수 만들기

이 샘플에서는 환경 변수 `TRANSLATOR_TEXT_KEY`에서 Translator Text 구독 키를 읽으려고 합니다. 환경 변수를 잘 모르는 경우에는 `subscriptionKey`를 문자열로 설정하고 조건문을 주석으로 처리할 수 있습니다.

이 코드를 프로젝트에 복사합니다.

```go
func main() {
    /*
     * Read your subscription key from an env variable.
     * Please note: You can replace this code block with
     * var subscriptionKey = "YOUR_SUBSCRIPTION_KEY" if you don't
     * want to use env variables. If so, be sure to delete the "os" import.
     */
    subscriptionKey := os.Getenv("TRANSLATOR_TEXT_KEY")
    if subscriptionKey == "" {
       log.Fatal("Environment variable TRANSLATOR_TEXT_KEY is not set.")
    }
    /*
     * This calls our transliterate function, which we'll
     * create in the next section. It takes a single argument,
     * the subscription key.
     */
    transliterate(subscriptionKey)
}
```

## <a name="create-a-function-to-transliterate-text"></a>텍스트를 음역하는 함수 만들기

텍스트를 음역하는 함수를 만들겠습니다. 이 함수는 단일 인수인 Translator Text 구독 키를 사용합니다.

```go
func transliterate(subscriptionKey string) {
    /*  
     * In the next few sections, we'll add code to this
     * function to make a request and handle the response.
     */
}
```

그런 다음, URL을 생성하겠습니다. URL은 `Parse()` 및 `Query()` 메서드를 사용하여 빌드됩니다. 매개 변수는 `Add()` 메서드와 함께 추가됩니다. 이 샘플에서는 일본어를 라틴어 알파벳으로 음역합니다.

이 코드를 `transliterate` 함수에 복사합니다.

```go
// Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
u, _ := url.Parse("https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0")
q := u.Query()
q.Add("language", "ja")
q.Add("fromScript", "jpan")
q.Add("toScript", "latn")
u.RawQuery = q.Encode()
```

>[!NOTE]
> 엔드포인트, 루트 및 요청 매개 변수에 대한 자세한 내용은 [Translator Text API 3.0: Transliterate](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-transliterate)를 참조하세요.

## <a name="create-a-struct-for-your-request-body"></a>요청 본문의 구조체 만들기

그런 다음, 요청 본문의 익명 구조체를 만들고 `json.Marshal()`을 사용하여 JSON으로 인코딩합니다. 이 코드를 `transliterate` 함수에 추가합니다.

```go
// Create an anonymous struct for your request body and encode it to JSON
body := []struct {
    Text string
}{
    {Text: "こんにちは"},
}
b, _ := json.Marshal(body)
```

## <a name="build-the-request"></a>요청 빌드

이제 요청 본문을 JSON으로 인코딩했으므로 POST 요청을 빌드하고 Translator Text API를 호출할 수 있습니다.

```go
// Build the HTTP POST request
req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
if err != nil {
    log.Fatal(err)
}
// Add required headers to the request
req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
req.Header.Add("Content-Type", "application/json")

// Call the Translator Text API
res, err := http.DefaultClient.Do(req)
if err != nil {
    log.Fatal(err)
}
```

## <a name="handle-and-print-the-response"></a>응답 처리 및 인쇄

이 코드를 `transliterate` 함수에 추가하여 JSON 응답을 디코딩한 다음, 결과에 형식을 지정하고 결과를 인쇄합니다.

```go
// Decode the JSON response
var result interface{}
if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
    log.Fatal(err)
}
// Format and print the response to terminal
prettyJSON, _ := json.MarshalIndent(result, "", "  ")
fmt.Printf("%s\n", prettyJSON)
```

## <a name="put-it-all-together"></a>모든 요소 결합

이것으로, Translator Text API를 호출하여 JSON 응답을 반환하는 간단한 프로그램이 만들어집니다. 이제 프로그램을 실행해 보겠습니다.

```console
go run transliterate-text.go
```

코드를 우리 것과 비교하고 싶다면 전체 샘플은 [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Go)에 있습니다.

## <a name="sample-response"></a>샘플 응답

```json
[
  {
    "script": "latn",
    "text": "konnichiwa"
  }
]
```

## <a name="next-steps"></a>다음 단계

GitHub의 [Go용 Azure SDK](https://github.com/Azure/azure-sdk-for-go)에서 Cognitive Services API용 Go 패키지를 살펴봅니다.

> [!div class="nextstepaction"]
> [GitHub에서 Go 패키지 살펴보기](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices)

## <a name="see-also"></a>참고 항목

Translator Text API를 사용하여 다음을 수행하는 방법을 알아봅니다.

* [텍스트 번역](quickstart-go-translate.md)
* [입력으로 언어 식별](quickstart-go-detect.md)
* [대체 번역 가져오기](quickstart-go-dictionary.md)
* [지원되는 언어 목록 가져오기](quickstart-go-languages.md)
* [입력으로 문장 길이 확인](quickstart-go-sentences.md)
