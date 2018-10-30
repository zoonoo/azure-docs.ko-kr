---
title: '빠른 시작: REST API 및 cURL을 사용하여 이미지에서 얼굴 감지'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 cURL과 함께 Face API를 사용하여 이미지에서 얼굴을 감지합니다.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 05/10/2018
ms.author: pafarley
ms.openlocfilehash: ab403ec6a9db4d1a0dc03074044eeb424e4ba875
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49953351"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-curl"></a>빠른 시작: REST API 및 cURL을 사용하여 이미지에서 얼굴 감지

이 빠른 시작에서는 Face API를 사용하여 이미지에서 얼굴을 감지합니다.

## <a name="prerequisites"></a>필수 조건

샘플을 실행하려면 구독 키가 있어야 합니다. [Cognitive Services 시도](https://azure.microsoft.com/try/cognitive-services/?api=face-api)에서 평가판 구독 키를 가져올 수 있습니다.

## <a name="detect-faces-in-an-image"></a>이미지에서 얼굴 감지

[Face - Detect](https://westcentralus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) 메서드를 사용하여 이미지에서 얼굴을 감지하고 다음을 포함한 얼굴 특성을 반환합니다.

* 얼굴 Face: 여러 Face API 시나리오에 사용되는 고유 ID입니다.
* 얼굴 사각형: 이미지에서 얼굴의 위치를 나타내는 왼쪽, 위쪽, 너비 및 높이입니다.
* 랜드마크: 얼굴 구성 요소의 중요한 위치를 가리키는 27포인트 얼굴 랜드마크 배열입니다.
* 연령, 성별, 웃는 정도, 머리 포즈, 얼굴의 털 등을 포함한 얼굴 특성입니다.

샘플을 실행하려면 다음 단계를 수행합니다.

1. 명령 프롬프트를 엽니다.
2. `<Subscription Key>`를 유효한 구독 키로 바꿉니다.
3. 필요한 경우 URL(`https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect`)을 변경하여 구독 키를 가져온 위치를 사용합니다.
4. 필요에 따라 분석할 이미지(`"{\"url\":...`)를 변경합니다.
5. 명령 창에 코드를 붙여넣습니다.
6. 명령을 실행합니다.

### <a name="face---detect-request"></a>얼굴 - 감지 요청

> [!NOTE]
> 구독 키를 가져오는 데 사용한 것과 동일한 위치를 REST 호출에서 사용해야 합니다. 예를 들어 westus에서 구독 키를 받은 경우 다음 URL의 “westcentralus”를 “westus”로 바꿉니다.

```shell
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise" -H "Content-Type: application/json" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}"
```

### <a name="face---detect-response"></a>얼굴 - 감지 응답

성공적인 응답이 JSON을 통해 반환됩니다.

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 0.1,
        "yaw": -32.9
      },
      "gender": "female",
      "age": 22.9,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.986,
        "sadness": 0.009,
        "surprise": 0.005
      },
      "blur": {
        "blurLevel": "low",
        "value": 0.06
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.67
      },
      "noise": {
        "noiseLevel": "low",
        "value": 0
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": true
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0,
        "invisible": false,
        "hairColor": [
          {
            "color": "brown",
            "confidence": 1
          },
          {
            "color": "black",
            "confidence": 0.87
          },
          {
            "color": "other",
            "confidence": 0.51
          },
          {
            "color": "blond",
            "confidence": 0.08
          },
          {
            "color": "red",
            "confidence": 0.08
          },
          {
            "color": "gray",
            "confidence": 0.02
          }
        ]
      }
    }
  }
]
```

## <a name="next-steps"></a>다음 단계

이미지에서 사람 얼굴을 감지하고, 사각형으로 얼굴 경계를 표시하고, 연령이나 성별 같은 특성을 반환하는 데 사용되는 Face API를 살펴봅니다.

> [!div class="nextstepaction"]
> [Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
