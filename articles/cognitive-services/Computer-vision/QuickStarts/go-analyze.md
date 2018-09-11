---
title: Computer Vision API Go 빠른 시작 분석 이미지 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 이 빠른 시작에서는 Cognitive Services에서 Go와 함께 Computer Vision을 사용하여 이미지를 분석합니다.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: ef7d6ac818f517615fc98f40ac073e6bfc9a65fd
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43750684"
---
# <a name="quickstart-analyze-a-remote-image---rest-go"></a>빠른 시작: 원격 이미지 분석 - REST, Go

이 빠른 시작에서는 Computer Vision을 사용하여 시각적 기능을 추출하기 위해 이미지를 분석합니다.

## <a name="prerequisites"></a>필수 조건

Computer Vision을 사용하려면 구독 키가 필요합니다. [구독 키 얻기](../Vision-API-How-to-Topics/HowToSubscribe.md)를 참조하세요.

## <a name="analyze-image-request"></a>이미지 분석 요청

[이미지 분석 메서드](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)를 사용하여 이미지 콘텐츠를 기반으로 하는 시각적 기능을 추출할 수 있습니다. 이미지를 업로드하거나 이미지 URL을 지정하고 다음을 비롯한 반환할 기능을 선택할 수 있습니다.

* 이미지 콘텐츠와 관련된 자세한 태크 목록입니다.
* 완전한 문장에서 이미지 콘텐츠의 설명입니다.
* 이미지에 포함된 모든 얼굴의 좌표, 성별 및 나이입니다.
* ImageType(클립 아트 또는 선 그리기)
* 기조색, 강조색, 이미지가 흑백인지 여부입니다.
* 이 [분류](../Category-Taxonomy.md)에 정의된 범주입니다.
* 이미지에 성인 또는 성적인 콘텐츠가 포함되어 있나요?

샘플을 실행하려면 다음 단계를 수행합니다.

1. 다음 코드를 편집기에 복사합니다.
1. `<Subscription Key>`를 유효한 구독 키로 바꿉니다.
1. 필요한 경우 `uriBase` 값을 구독 키를 가져온 위치로 변경합니다.
1. 필요에 따라 `imageUrl` 값을 분석할 이미지로 변경합니다.
1. 확장명이 `.go`인 파일을 저장합니다.
1. Go가 설치된 컴퓨터에서 명령 프롬프트를 엽니다.
1. 파일을 빌드합니다(예: `go build analyze-image.go`).
1. 파일을 실행합니다(예: `analyze-image`).

```go
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strings"
    "time"
)

func main() {
    // For example, subscriptionKey = "0123456789abcdef0123456789ABCDEF"
    const subscriptionKey = "<Subscription Key>"

    // You must use the same location in your REST call as you used to get your
    // subscription keys. For example, if you got your subscription keys from
    // westus, replace "westcentralus" in the URL below with "westus".
    const uriBase =
        "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze"
    const imageUrl =
        "http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg"

    const params = "?visualFeatures=Description&details=Landmarks&language=en"
    const uri = uriBase + params
    const imageUrlEnc = "{\"url\":\"" + imageUrl + "\"}"

    reader := strings.NewReader(imageUrlEnc)

    // Create the Http client
    client := &http.Client{
        Timeout: time.Second * 2,
    }

    // Create the Post request, passing the image URL in the request body
    req, err := http.NewRequest("POST", uri, reader)
    if err != nil {
        panic(err)
    }

    // Add headers
    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    // Send the request and retrieve the response
    resp, err := client.Do(req)
    if err != nil {
        panic(err)
    }

    defer resp.Body.Close()

    // Read the response body.
    // Note, data is a byte array
    data, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        panic(err)
    }

    // Parse the Json data
    var f interface{}
    json.Unmarshal(data, &f)

    // Format and display the Json result
    jsonFormatted, _ := json.MarshalIndent(f, "", "  ")
    fmt.Println(string(jsonFormatted))
}
```

## <a name="analyze-image-response"></a>이미지 분석 응답

성공적인 응답을 JSON으로 반환합니다. 예:

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

Computer Vision API를 탐색하여 이미지를 분석하고, 유명인 및 랜드마크를 검색하고, 썸네일을 만들고, 인쇄되고 필기된 텍스트를 추출합니다. Computer Vision API를 사용하여 신속하게 실험하려면 [API 테스트 콘솔 열기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)를 사용하세요.

> [!div class="nextstepaction"]
> [Computer Vision API 탐색](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
