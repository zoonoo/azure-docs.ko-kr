---
title: '빠른 시작: 로컬 이미지 분석 - REST, Python - Computer Vision'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Python과 함께 Computer Vision API를 사용하여 로컬 이미지를 분석합니다.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: pafarley
ms.openlocfilehash: ef95d68f37de7637abd546a1a7fdffff547bda6b
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2018
ms.locfileid: "51854138"
---
# <a name="quickstart-analyze-a-local-image-using-the-rest-api-and-python-in-computer-vision"></a>빠른 시작: Computer Vision에서 REST API 및 Python을 사용하여 로컬 이미지 분석

이 빠른 시작에서는 Computer Vision의 REST API를 사용하여 시각적 기능을 추출하기 위해 로컬로 저장된 이미지를 분석합니다. [이미지 분석](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) 메서드를 사용하면 이미지 콘텐츠를 기반으로 하여 시각적 특징을 추출할 수 있습니다.

[MyBinder](https://mybinder.org)의 Jupyter Notebook을 사용하면 이 빠른 시작을 단계별로 실행할 수 있습니다. Binder를 시작하려면 다음 단추를 선택합니다.

[![바인더](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

- 샘플을 로컬로 실행하려면 [Python](https://www.python.org/downloads/)이 설치되어 있어야 합니다.
- Computer Vision에 대한 구독 키가 있어야 합니다. 구독 키를 가져오려면 [구독 키 얻기](../Vision-API-How-to-Topics/HowToSubscribe.md)를 참조하세요.

## <a name="create-and-run-the-sample"></a>샘플 만들기 및 실행

샘플을 만들고 실행하려면 다음 단계를 수행합니다.

1. 다음 코드를 텍스트 편집기에 복사합니다.
1. 필요한 경우 코드에서 다음 내용을 변경합니다.
    1. `subscription_key`의 값을 구독 키로 바꿉니다.
    1. 필요한 경우 `vision_base_url`의 값을 구독 키를 가져온 Azure 지역의 Computer Vision 리소스에 대한 엔드포인트 URL로 바꿉니다.
    1. 필요한 경우 `image_path`의 값을 분석하려는 다른 이미지의 경로 및 파일 이름으로 바꿉니다.
1. 코드를 `.py` 확장명의 파일로 저장합니다. 예: `analyze-local-image.py`
1. 명령 프롬프트 창을 엽니다.
1. 프롬프트에서 `python` 명령을 사용하여 샘플을 실행합니다. 예: `python analyze-local-image.py`

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
# Free trial subscription keys are generated in the "westus" region.
# If you use a free trial subscription key, you shouldn't need to change
# this region.
vision_base_url = "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/"

analyze_url = vision_base_url + "analyze"

# Set image_path to the local path of an image that you want to analyze.
image_path = "C:/Documents/ImageToAnalyze.jpg"

# Read the image into a byte array
image_data = open(image_path, "rb").read()
headers    = {'Ocp-Apim-Subscription-Key': subscription_key,
              'Content-Type': 'application/octet-stream'}
params     = {'visualFeatures': 'Categories,Description,Color'}
response = requests.post(
    analyze_url, headers=headers, params=params, data=image_data)
response.raise_for_status()

# The 'analysis' object contains various fields that describe the image. The most
# relevant caption for the image is obtained from the 'description' property.
analysis = response.json()
print(analysis)
image_caption = analysis["description"]["captions"][0]["text"].capitalize()

# Display the image and overlay it with the caption.
image = Image.open(BytesIO(image_data))
plt.imshow(image)
plt.axis("off")
_ = plt.title(image_caption, size="x-large", y=-0.1)
```

## <a name="examine-the-response"></a>응답 검사

성공적인 응답이 JSON을 통해 반환됩니다. 샘플 웹 페이지에서 다음 예제와 비슷한 성공적인 응답을 구문 분석하고 명령 프롬프트 창에 표시합니다.

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

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 파일을 삭제합니다.

## <a name="next-steps"></a>다음 단계

Computer Vision을 사용하는 Python 응용 프로그램을 탐색합니다. 이 프로그램은 OCR(광학 문자 인식)을 수행하고, 스마트하게 자른 썸네일을 만들고, 이미지에서 얼굴을 비롯한 시각적 특징을 감지하고, 분류하고, 태그를 지정하고, 설명합니다. Computer Vision API를 사용하여 신속하게 실험하려면 [API 테스트 콘솔 열기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)를 사용하세요.

> [!div class="nextstepaction"]
> [Computer Vision API Python 자습서](../Tutorials/PythonTutorial.md)
