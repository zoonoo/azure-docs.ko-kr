---
title: '빠른 시작: REST API 및 Ruby를 사용하여 이미지에서 얼굴 감지'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Ruby와 함께 Face API를 사용하여 이미지에서 얼굴을 감지합니다.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 05/30/2018
ms.author: pafarley
ms.openlocfilehash: 8b47c845e8523e1c35dee8a5732cb15fed09e030
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49955446"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-ruby"></a>빠른 시작: REST API 및 Ruby를 사용하여 이미지에서 얼굴 감지

이 빠른 시작에서는 Face API를 사용하여 이미지에서 사람 얼굴을 감지합니다.

## <a name="prerequisites"></a>필수 조건

샘플을 실행하려면 구독 키가 있어야 합니다. [Cognitive Services 시도](https://azure.microsoft.com/try/cognitive-services/?api=face-api)에서 평가판 구독 키를 가져올 수 있습니다.

## <a name="face---detect-request"></a>얼굴 - 감지 요청

[Face - Detect](https://westcentralus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) 메서드를 사용하여 이미지에서 얼굴을 감지하고 다음을 포함한 얼굴 특성을 반환합니다.

* 얼굴 Face: 여러 Face API 시나리오에 사용되는 고유 ID입니다.
* 얼굴 사각형: 이미지에서 얼굴의 위치를 나타내는 왼쪽, 위쪽, 너비 및 높이입니다.
* 랜드마크: 얼굴 구성 요소의 중요한 위치를 가리키는 27포인트 얼굴 랜드마크 배열입니다.
* 연령, 성별, 웃는 정도, 머리 포즈, 얼굴의 털 등을 포함한 얼굴 특성입니다.

샘플을 실행하려면 다음 단계를 수행합니다.

1. 다음 코드를 편집기에 복사합니다.
1. `<Subscription Key>`를 유효한 구독 키로 바꿉니다.
1. 필요한 경우 `uri` 값을 구독 키를 가져온 위치로 변경합니다.
1. 필요에 따라 `imageUri`을 분석할 이미지로 설정합니다.
1. `.rb` 확장명으로 파일을 저장합니다.
1. Ruby 명령 프롬프트를 열고 파일을 실행합니다(예: `ruby myfile.rb`).

```ruby
require 'net/http'

# You must use the same location in your REST call as you used to get your
# subscription keys. For example, if you got your subscription keys from  westus,
# replace "westcentralus" in the URL below with "westus".
uri = URI('https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect')
uri.query = URI.encode_www_form({
    # Request parameters
    'returnFaceId' => 'true',
    'returnFaceLandmarks' => 'false',
    'returnFaceAttributes' => 'age,gender,headPose,smile,facialHair,glasses,' +
        'emotion,hair,makeup,occlusion,accessories,blur,exposure,noise'
})

request = Net::HTTP::Post.new(uri.request_uri)

# Request headers
# Replace <Subscription Key> with your valid subscription key.
request['Ocp-Apim-Subscription-Key'] = '<Subscription Key>'
request['Content-Type'] = 'application/json'

imageUri = "https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg"
request.body = "{\"url\": \"" + imageUri + "\"}"

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request(request)
end

puts response.body
```

## <a name="face---detect-response"></a>얼굴 - 감지 응답

성공적인 응답이 JSON 형식으로 반환됩니다. 예:

```json
[
  {
    "faceId": "e93e0db1-036e-4819-b5b6-4f39e0f73509",
    "faceRectangle": {
      "top": 621,
      "left": 616,
      "width": 195,
      "height": 195
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 6.8,
        "yaw": 3.7
      },
      "gender": "male",
      "age": 37,
      "facialHair": {
        "moustache": 0.4,
        "beard": 0.4,
        "sideburns": 0.1
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.999,
        "sadness": 0.001,
        "surprise": 0
      },
      "blur": {
        "blurLevel": "high",
        "value": 0.89
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.51
      },
      "noise": {
        "noiseLevel": "medium",
        "value": 0.59
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": false
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0.04,
        "invisible": false,
        "hairColor": [
          {
            "color": "black",
            "confidence": 0.98
          },
          {
            "color": "brown",
            "confidence": 0.87
          },
          {
            "color": "gray",
            "confidence": 0.85
          },
          {
            "color": "other",
            "confidence": 0.25
          },
          {
            "color": "blond",
            "confidence": 0.07
          },
          {
            "color": "red",
            "confidence": 0.02
          }
        ]
      }
    }
  },
  {
    "faceId": "37c7c4bc-fda3-4d8d-94e8-b85b8deaf878",
    "faceRectangle": {
      "top": 693,
      "left": 1503,
      "width": 180,
      "height": 180
    },
    "faceAttributes": {
      "smile": 0.003,
      "headPose": {
        "pitch": 0,
        "roll": 2,
        "yaw": -2.2
      },
      "gender": "female",
      "age": 56,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0.001,
        "disgust": 0,
        "fear": 0,
        "happiness": 0.003,
        "neutral": 0.984,
        "sadness": 0.011,
        "surprise": 0
      },
      "blur": {
        "blurLevel": "high",
        "value": 0.83
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.41
      },
      "noise": {
        "noiseLevel": "high",
        "value": 0.76
      },
      "makeup": {
        "eyeMakeup": false,
        "lipMakeup": false
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0.06,
        "invisible": false,
        "hairColor": [
          {
            "color": "black",
            "confidence": 0.99
          },
          {
            "color": "gray",
            "confidence": 0.89
          },
          {
            "color": "other",
            "confidence": 0.64
          },
          {
            "color": "brown",
            "confidence": 0.34
          },
          {
            "color": "blond",
            "confidence": 0.07
          },
          {
            "color": "red",
            "confidence": 0.03
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
