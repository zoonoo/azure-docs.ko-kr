---
title: '빠른 시작: 썸네일 생성 - REST, Go'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Go와 함께 Computer Vision API를 사용하여 이미지에서 썸네일을 생성합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 7e9980bfe3065b4ce0df5a2f083f5f4bef366f35
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681096"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-with-go"></a>빠른 시작: Go와 함께 Computer Vision REST API를 사용하여 썸네일 생성

이 빠른 시작에서는 Computer Vision REST API를 사용하여 이미지에서 썸네일을 생성합니다. 높이와 너비를 지정합니다. 입력 이미지의 가로 세로 비율과 다를 수 있습니다. Computer Vision은 스마트 자르기를 사용하여 관심 영역을 지능적으로 식별하고 해당 영역을 기반으로 자르기 좌표를 생성합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

- [Go](https://golang.org/dl/)가 설치되어 있어야 합니다.
- Computer Vision에 대한 구독 키가 있어야 합니다. [Cognitive Services 사용해보기](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)에서 평가판 키를 가져올 수 있습니다. 또는 [Cognitive Services 계정 만들기](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)의 지침에 따라 Computer Vision을 구독하고 키를 가져옵니다. 그런 다음, 각각 `COMPUTER_VISION_SUBSCRIPTION_KEY` 및 `COMPUTER_VISION_ENDPOINT`라는 키 및 서비스 엔드포인트 문자열에 대한 [환경 변수를 만듭니다](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication).

## <a name="create-and-run-the-sample"></a>샘플 만들기 및 실행

샘플을 만들고 실행하려면 다음 단계를 수행합니다.

1. 다음 코드를 텍스트 편집기에 복사합니다.
1. 필요에 따라 `imageUrl`의 값을 썸네일을 생성하려는 다른 이미지의 URL로 바꿉니다.
1. 코드를 `.go` 확장명의 파일로 저장합니다. `get-thumbnail.go`)을 입력합니다.
1. 명령 프롬프트 창을 엽니다.
1. 프롬프트에서 `go build` 명령을 실행하여 파일에서 패키지를 컴파일합니다. `go build get-thumbnail.go`)을 입력합니다.
1. 프롬프트에서 컴파일된 패키지를 실행합니다. `get-thumbnail`)을 입력합니다.

```go
package main

import (
    "bytes"
    "fmt"
    "io/ioutil"
    "io"
    "log"
    "net/http"
    "os"
    "strings"
    "time"
)

func main() {
    // Add your Computer Vision subscription key and endpoint to your environment variables.
    subscriptionKey := os.Getenv("COMPUTER_VISION_SUBSCRIPTION_KEY")
    endpoint := os.Getenv("COMPUTER_VISION_ENDPOINT")

    uriBase := endpoint + "vision/v3.0/generateThumbnail"
    const imageUrl = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

    const params = "?width=100&height=100&smartCropping=true"
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
    
    // Convert byte[] to io.Reader type
    readerThumb := bytes.NewReader(data)

    // Write the image binary to file
    file, err := os.Create("thumb_local.png")
    if err != nil { log.Fatal(err) }
    defer file.Close()
    _, err = io.Copy(file, readerThumb)
    if err != nil { log.Fatal(err) }

    fmt.Println("The thunbnail from local has been saved to file.")
    fmt.Println()
}
```

## <a name="examine-the-response"></a>응답 검사

성공한 응답에는 썸네일 이미지 이진 파일 데이터가 포함됩니다. 요청이 실패하면 무엇이 잘못되었는지 확인할 수 있도록 응답에 오류 코드 및 메시지가 포함됩니다.

## <a name="next-steps"></a>다음 단계

이미지를 분석하고, 유명인 및 랜드마크를 검색하고, 썸네일을 만들고, 인쇄 및 필기 텍스트를 추출하는 Computer Vision API를 살펴봅니다. Computer Vision API를 사용하여 신속하게 실험하려면 [API 테스트 콘솔 열기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)를 사용하세요.

> [!div class="nextstepaction"]
> [Computer Vision API 살펴보기](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
