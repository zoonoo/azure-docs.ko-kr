---
title: '빠른 시작: 원격 이미지 분석 - REST, Go'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Go와 함께 Computer Vision API를 사용하여 이미지를 분석합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 6d7e9f37c794349e9f61b9c822e63a9b48984e91
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "87835275"
---
# <a name="quickstart-analyze-a-remote-image-using-the-computer-vision-rest-api-with-go"></a>빠른 시작: Go와 함께 Computer Vision REST API를 사용하여 원격 이미지 분석

이 빠른 시작에서는 원격으로 저장된 이미지를 분석하여 Computer Vision REST API를 사용하여 시각적 기능을 추출합니다. [이미지 분석](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) 메서드를 사용하면 이미지 콘텐츠를 기반으로 하여 시각적 특징을 추출할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* [Go](https://golang.org/dl/)
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Computer Vision 리소스 만들기"  target="_blank">Computer Vision 리소스 <span class="docon docon-navigate-external x-hidden-focus"></span></a>를 만들어 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동**을 클릭합니다.
    * 애플리케이션을 Computer Vision 서비스에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.
* 각각 `COMPUTER_VISION_SUBSCRIPTION_KEY` 및 `COMPUTER_VISION_ENDPOINT`라는 키 및 서비스 엔드포인트 URL에 대한 [환경 변수를 만듭니다](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication).

## <a name="create-and-run-the-sample"></a>샘플 만들기 및 실행

샘플을 만들고 실행하려면 다음 단계를 수행합니다.

1. 아래 코드를 텍스트 편집기에 복사합니다.
1. 필요에 따라 `imageUrl` 값을 분석하려는 다른 이미지의 URL로 바꿉니다.
1. 코드를 `.go` 확장명의 파일로 저장합니다. 예들 들어 `analyze-image.go`입니다.
1. 명령 프롬프트 창을 엽니다.
1. 프롬프트에서 `go build` 명령을 실행하여 파일에서 패키지를 컴파일합니다. 예들 들어 `go build analyze-image.go`입니다.
1. 프롬프트에서 컴파일된 패키지를 실행합니다. 예들 들어 `analyze-image`입니다.

```go
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "os"
    "strings"
    "time"
)

func main() {
    // Add your Computer Vision subscription key and endpoint to your environment variables.
    subscriptionKey := os.Getenv("COMPUTER_VISION_SUBSCRIPTION_KEY")
    endpoint := os.Getenv("COMPUTER_VISION_ENDPOINT")

    uriBase := endpoint + "vision/v3.0/analyze"
    const imageUrl =
        "https://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg"

    const params = "?visualFeatures=Description&details=Landmarks"
    uri := uriBase + params
    const imageUrlEnc = "{\"url\":\"" + imageUrl + "\"}"

    reader := strings.NewReader(imageUrlEnc)

    // Create the HTTP client
    client := &http.Client{
        Timeout: time.Second * 2,
    }

    // Create the POST request, passing the image URL in the request body
    req, err := http.NewRequest("POST", uri, reader)
    if err != nil {
        panic(err)
    }

    // Add request headers
    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    // Send the request and retrieve the response
    resp, err := client.Do(req)
    if err != nil {
        panic(err)
    }

    defer resp.Body.Close()

    // Read the response body
    // Note, data is a byte array
    data, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        panic(err)
    }

    // Parse the JSON data from the byte array
    var f interface{}
    json.Unmarshal(data, &f)

    // Format and display the JSON result
    jsonFormatted, _ := json.MarshalIndent(f, "", "  ")
    fmt.Println(string(jsonFormatted))
}
```

## <a name="examine-the-response"></a>응답 검사

성공적인 응답이 JSON을 통해 반환됩니다. 애플리케이션 예제는 다음 예제와 유사하게 명령 프롬프트 창에서 성공한 응답을 구문 분석하고 표시합니다.

```json
{
  "categories": [
    {
      "detail": {
        "landmarks": []
      },
      "name": "outdoor_water",
      "score": 0.9921875
    }
  ],
  "description": {
    "captions": [
      {
        "confidence": 0.916458423253597,
        "text": "a large waterfall over a rocky cliff"
      }
    ],
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ]
  },
  "metadata": {
    "format": "Jpeg",
    "height": 959,
    "width": 1280
  },
  "requestId": "a92f89ab-51f8-4735-a58d-507da2213fc2"
}
```

## <a name="next-steps"></a>다음 단계

이미지를 분석하고, 유명인 및 랜드마크를 검색하고, 썸네일을 만들고, 인쇄 및 필기 텍스트를 추출하는 데 사용되는 Computer Vision API를 살펴봅니다. Computer Vision API를 사용하여 신속하게 실험하려면 [API 테스트 콘솔 열기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)를 사용하세요.

> [!div class="nextstepaction"]
> [Computer Vision API 살펴보기](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
