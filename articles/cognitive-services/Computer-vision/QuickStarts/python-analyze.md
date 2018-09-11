---
title: Computer Vision Python 빠른 시작 분석 원격 이미지 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 이 빠른 시작에서는 Cognitive Services에서 Python과 함께 Computer Vision을 사용하여 원격 이미지를 분석합니다.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 65f9b0d4fb007a6a9b8ef489ca59f384e047a0dd
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43750691"
---
# <a name="quickstart-analyze-a-remote-image---rest-python"></a>빠른 시작: 원격 이미지 분석 - REST, Python

이 빠른 시작에서는 Computer Vision을 사용하여 원격 이미지를 분석합니다. 로컬 이미지를 분석하려면 [Python을 사용하여 로컬 이미지 분석](python-disk.md)을 참조하세요.

[MyBinder](https://mybinder.org)에서 Jupyter Notebook을 사용하여 단계별로 이 빠른 시작을 실행할 수 있습니다. Binder를 시작하려면 다음 단추를 선택합니다.

[![바인더](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="prerequisites"></a>필수 조건

Computer Vision을 사용하려면 구독 키가 필요합니다. [구독 키 얻기](../Vision-API-How-to-Topics/HowToSubscribe.md)를 참조하세요.

## <a name="analyze-a-remote-image"></a>원격 이미지 분석

[이미지 분석 메서드](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)를 사용하여 이미지 콘텐츠를 기반으로 하는 시각적 기능을 추출할 수 있습니다. 이미지를 업로드하거나 이미지 URL을 지정하고 다음을 비롯한 반환할 기능을 선택할 수 있습니다.

* 이미지 콘텐츠와 관련된 자세한 태크 목록입니다.
* 완전한 문장에서 이미지 콘텐츠의 설명입니다.
* 이미지에 포함된 모든 얼굴의 좌표, 성별 및 나이입니다.
* ImageType(클립 아트 또는 선 그리기)
* 기조색, 강조색, 이미지가 흑백인지 여부입니다.
* 이 [분류](../Category-Taxonomy.md)에 정의된 범주입니다.
* 이미지에 성인 또는 성적인 콘텐츠가 포함되어 있나요?

샘플을 실행하려면 다음 단계를 수행합니다.

1. 새 Python 스크립트 파일에 다음 코드를 복사합니다.
1. `<Subscription Key>`를 유효한 구독 키로 바꿉니다.
1. 필요한 경우 `vision_base_url` 값을 구독 키를 가져온 위치로 변경합니다.
1. 필요에 따라 `image_url` 값을 다른 이미지로 변경합니다.
1. 스크립트를 실행합니다.

다음 코드에서는 Python `requests` 라이브러리를 사용하여 Computer Vision 분석 이미지 API를 호출합니다. 결과를 JSON 개체로 반환합니다. API 키는 `headers` 사전을 통해 전달됩니다. 인식할 기능의 형식은 `params` 사전을 통해 전달됩니다.

## <a name="analyze-image-request"></a>이미지 분석 요청

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
#%matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Replace <Subscription Key> with your valid subscription key.
subscription_key = "<Subscription Key>"
assert subscription_key

# You must use the same region in your REST call as you used to get your
# subscription keys. For example, if you got your subscription keys from
# westus, replace "westcentralus" in the URI below with "westus".
#
# Free trial subscription keys are generated in the westcentralus region.
# If you use a free trial subscription key, you shouldn't need to change
# this region.
vision_base_url = "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/"

analyze_url = vision_base_url + "analyze"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/" + \
    "Broadway_and_Times_Square_by_night.jpg/450px-Broadway_and_Times_Square_by_night.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key }
params  = {'visualFeatures': 'Categories,Description,Color'}
data    = {'url': image_url}
response = requests.post(analyze_url, headers=headers, params=params, json=data)
response.raise_for_status()

# The 'analysis' object contains various fields that describe the image. The most
# relevant caption for the image is obtained from the 'description' property.
analysis = response.json()
print(analysis)
image_caption = analysis["description"]["captions"][0]["text"].capitalize()

# Display the image and overlay it with the caption.
image = Image.open(BytesIO(requests.get(image_url).content))
plt.imshow(image)
plt.axis("off")
_ = plt.title(image_caption, size="x-large", y=-0.1)
```

## <a name="analyze-image-response"></a>이미지 분석 응답

성공적인 응답을 JSON으로 반환합니다. 예:

```json
{
  "categories": [
    {
      "name": "outdoor_",
      "score": 0.00390625,
      "detail": {
        "landmarks": []
      }
    },
    {
      "name": "outdoor_street",
      "score": 0.33984375,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "building",
      "outdoor",
      "street",
      "city",
      "people",
      "busy",
      "table",
      "walking",
      "traffic",
      "filled",
      "large",
      "many",
      "group",
      "night",
      "light",
      "crowded",
      "bunch",
      "standing",
      "man",
      "sign",
      "crowd",
      "umbrella",
      "riding",
      "tall",
      "woman",
      "bus"
    ],
    "captions": [
      {
        "text": "a group of people on a city street at night",
        "confidence": 0.9122243847383961
      }
    ]
  },
  "color": {
    "dominantColorForeground": "Brown",
    "dominantColorBackground": "Brown",
    "dominantColors": [
      "Brown"
    ],
    "accentColor": "B54316",
    "isBwImg": false
  },
  "requestId": "c11894eb-de3e-451b-9257-7c8b168073d1",
  "metadata": {
    "height": 600,
    "width": 450,
    "format": "Jpeg"
  }
}
```

## <a name="next-steps"></a>다음 단계

Computer Vision을 사용하여 OCR(광학 문자 인식)을 수행하고, 스마트하게 잘리는 썸네일을 만들고, 이미지에서 얼굴을 비롯한 시각적 기능을 검색하고, 분류하고, 태그를 지정하고, 설명하는 Python 응용 프로그램을 탐색합니다. Computer Vision API를 사용하여 신속하게 실험하려면 [API 테스트 콘솔 열기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)를 사용하세요.

> [!div class="nextstepaction"]
> [Computer Vision API Python 자습서](../Tutorials/PythonTutorial.md)
