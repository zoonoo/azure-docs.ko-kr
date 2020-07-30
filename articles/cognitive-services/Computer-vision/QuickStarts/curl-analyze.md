---
title: '빠른 시작: 원격 이미지 분석 - REST, cURL'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 cURL과 함께 Computer Vision API를 사용하여 원격 이미지를 분석합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: c5cb2f7acb3f1432c2eadd769021c27ba41e9887
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87031935"
---
# <a name="quickstart-analyze-a-remote-image-using-the-computer-vision-rest-api-and-curl"></a>빠른 시작: Computer Vision REST API 및 cURL을 사용하여 원격 이미지 분석

이 빠른 시작에서는 원격으로 저장된 이미지를 분석하여 Computer Vision REST API를 사용하여 시각적 기능을 추출합니다. [이미지 분석](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) 메서드를 사용하면 이미지 콘텐츠를 기반으로 하여 시각적 특징을 추출할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/) 
* [cURL](https://curl.haxx.se/)
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Computer Vision 리소스 만들기"  target="_blank">Computer Vision 리소스 <span class="docon docon-navigate-external x-hidden-focus"></span></a>를 만들어 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동**을 클릭합니다.
  * 애플리케이션을 Computer Vision 서비스에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
  * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

## <a name="create-and-run-the-sample-command"></a>샘플 명령 만들기 및 실행

샘플을 만들고 실행하려면 다음 단계를 수행합니다.

1. 다음 명령을 텍스트 편집기에 복사합니다.
1. 필요한 경우 명령에서 다음 내용을 변경합니다.
    1. `<subscriptionKey>`의 값을 구독 키로 바꿉니다.
    1. 요청 URL(`westcentralus`)의 첫 번째 부분을 고유한 엔드포인트 URL의 텍스트로 바꿉니다.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. 필요한 경우 요청 본문의 이미지 URL(`http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\`)을 분석할 다른 이미지의 URL로 변경합니다.
1. 명령 프롬프트 창을 엽니다.
1. 텍스트 편집기에서 명령 프롬프트 창으로 명령을 붙여넣은 후 명령을 실행합니다.

```bash
curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v3.0/analyze?visualFeatures=Categories,Description&details=Landmarks" -d "{\"url\":\"http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\"}"
```

## <a name="examine-the-response"></a>응답 검사

성공적인 응답이 JSON을 통해 반환됩니다. 애플리케이션 예제는 다음 예제와 유사하게 명령 프롬프트 창에서 성공한 응답을 구문 분석하고 표시합니다.

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

## <a name="next-steps"></a>다음 단계

이미지를 분석하고, 유명인 및 랜드마크를 검색하고, 썸네일을 만들고, 인쇄 및 필기 텍스트를 추출하는 데 사용되는 Computer Vision API를 살펴봅니다. Computer Vision API를 사용하여 신속하게 실험하려면 [API 테스트 콘솔 열기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)를 사용하세요.

> [!div class="nextstepaction"]
> [Computer Vision API 살펴보기](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
