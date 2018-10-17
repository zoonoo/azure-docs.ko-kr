---
title: '빠른 시작: 원격 이미지 분석 - REST, cURL - Computer Vision'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 cURL과 함께 Computer Vision API를 사용하여 원격 이미지를 분석합니다.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: v-deken
ms.openlocfilehash: 51122da84e3845bba4919e4f2b2e46d513bbb3f4
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2018
ms.locfileid: "45633489"
---
# <a name="quickstart-analyze-a-remote-image-using-the-rest-api-and-curl-in-computer-vision"></a>빠른 시작: Computer Vision에서 REST API 및 cURL을 사용하여 원격 이미지 분석

이 빠른 시작에서는 Computer Vision의 REST API를 사용하여 시각적 기능을 추출하기 위해 원격으로 저장된 이미지를 분석합니다. [이미지 분석](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) 메서드를 사용하면 이미지 콘텐츠를 기반으로 시각적 기능을 추출할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

- [cURL](https://curl.haxx.se/windows)이 있어야 합니다.
- Computer Vision에 대한 구독 키가 있어야 합니다. 구독 키를 가져오려면 [구독 키 얻기](../Vision-API-How-to-Topics/HowToSubscribe.md)를 참조하세요.

## <a name="create-and-run-the-sample-command"></a>샘플 명령 만들기 및 실행

샘플을 만들고 실행하려면 다음 단계를 수행합니다.

1. 다음 명령을 텍스트 편집기에 복사합니다.
1. 필요한 경우 명령에서 다음 내용을 변경합니다.
    1. `<subscriptionKey>`의 값을 구독 키로 바꿉니다.
    1. 필요한 경우 요청 URL(`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze`)을 구독 키를 가져온 Azure 지역의 [이미지 분석](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) 메서드에 대한 엔드포인트 URL로 바꿉니다.
    1. 필요한 경우 다른 지원되는 언어를 사용하도록 요청 URL(`language=en`)의 언어 매개 변수를 변경합니다.
    1. 필요한 경우 요청 본문의 이미지 URL(`http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\`)을 분석할 다른 이미지의 URL로 변경합니다.
1. 명령 프롬프트 창을 엽니다.
1. 텍스트 편집기에서 명령 프롬프트 창으로 명령을 붙여넣은 후 명령을 실행합니다.

```console
curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Categories,Description&details=Landmarks&language=en" -d "{\"url\":\"http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\"}"
```

## <a name="examine-the-response"></a>응답 검사

성공적인 응답이 JSON을 통해 반환됩니다. 응용 프로그램 예제는 다음 예제와 유사하게 명령 프롬프트 창에서 성공한 응답을 구문 분석하고 표시합니다.

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
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
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "requestId": "b6e33879-abb2-43a0-a96e-02cb5ae0b795",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 명령 프롬프트 창과 텍스트 편집기를 닫습니다.

## <a name="next-steps"></a>다음 단계

이미지를 분석하고, 유명인 및 랜드마크를 검색하고, 썸네일을 만들고, 인쇄 및 필기 텍스트를 추출하는 데 사용되는 Computer Vision API를 살펴봅니다. Computer Vision API를 사용하여 신속하게 실험하려면 [API 테스트 콘솔 열기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)를 사용하세요.

> [!div class="nextstepaction"]
> [Computer Vision API 살펴보기](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
