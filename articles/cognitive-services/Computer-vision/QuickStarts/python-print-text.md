---
title: '빠른 시작: 인쇄된 텍스트 추출 - REST, Python'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Python과 함께 Computer Vision API를 사용하여 이미지의 인쇄 텍스트를 추출합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 7daa630c47fc677f6659fa51cca6583a0b583865
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81404378"
---
# <a name="quickstart-extract-printed-text-ocr-using-the-computer-vision-rest-api-and-python"></a>빠른 시작: Computer Vision REST API 및 Python을 사용하여 인쇄된 텍스트(OCR) 추출

> [!NOTE]
> 영어 텍스트를 추출하는 경우 새 [읽기 작업](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text)을 사용하는 것이 좋습니다. [Python 빠른 시작](https://docs.microsoft.com/azure/cognitive-services/computer-vision/quickstarts/python-hand-text)을 사용할 수 있습니다. 

이 빠른 시작에서는 Computer Vision REST API를 사용하여 이미지에서 OCR(광학 문자 인식)이 포함된 인쇄 텍스트를 추출합니다. [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) 메서드를 사용하여 이미지의 인쇄 텍스트를 감지하고, 인식된 문자를 머신에서 사용 가능한 문자 스트림으로 추출할 수 있습니다.

[MyBinder](https://mybinder.org)의 Jupyter Notebook을 사용하면 이 빠른 시작을 단계별로 실행할 수 있습니다. Binder를 시작하려면 다음 단추를 선택합니다.

[![바인더](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/try/cognitive-services/)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

- 샘플을 로컬로 실행하려면 [Python](https://www.python.org/downloads/)이 설치되어 있어야 합니다.
- Computer Vision에 대한 구독 키가 있어야 합니다. [Cognitive Services 사용해보기](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)에서 평가판 키를 가져올 수 있습니다. 또는 [Cognitive Services 계정 만들기](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)의 지침에 따라 Computer Vision을 구독하고 키를 가져옵니다. 그런 다음, 각각 `COMPUTER_VISION_SUBSCRIPTION_KEY` 및 `COMPUTER_VISION_ENDPOINT`라는 키 및 서비스 엔드포인트 문자열에 대한 [환경 변수를 만듭니다](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication).

## <a name="create-and-run-the-sample"></a>샘플 만들기 및 실행

샘플을 만들고 실행하려면 다음 단계를 수행합니다.

1. 다음 코드를 텍스트 편집기에 복사합니다.
1. 필요에 따라 `image_url`의 값을 인쇄 텍스트를 추출하려는 다른 이미지의 URL로 바꿉니다.
1. 코드를 `.py` 확장명의 파일로 저장합니다. `get-printed-text.py`)을 입력합니다.
1. 명령 프롬프트 창을 엽니다.
1. 프롬프트에서 `python` 명령을 사용하여 샘플을 실행합니다. `python get-printed-text.py`)을 입력합니다.

```python
import os
import sys
import requests
# If you are using a Jupyter notebook, uncomment the following line.
# %matplotlib inline
import matplotlib.pyplot as plt
from matplotlib.patches import Rectangle
from PIL import Image
from io import BytesIO

# Add your Computer Vision subscription key and endpoint to your environment variables.
if 'COMPUTER_VISION_SUBSCRIPTION_KEY' in os.environ:
    subscription_key = os.environ['COMPUTER_VISION_SUBSCRIPTION_KEY']
else:
    print("\nSet the COMPUTER_VISION_SUBSCRIPTION_KEY environment variable.\n**Restart your shell or IDE for changes to take effect.**")
    sys.exit()

if 'COMPUTER_VISION_ENDPOINT' in os.environ:
    endpoint = os.environ['COMPUTER_VISION_ENDPOINT']

ocr_url = endpoint + "vision/v2.1/ocr"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/" + \
    "Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'language': 'unk', 'detectOrientation': 'true'}
data = {'url': image_url}
response = requests.post(ocr_url, headers=headers, params=params, json=data)
response.raise_for_status()

analysis = response.json()

# Extract the word bounding boxes and text.
line_infos = [region["lines"] for region in analysis["regions"]]
word_infos = []
for line in line_infos:
    for word_metadata in line:
        for word_info in word_metadata["words"]:
            word_infos.append(word_info)
word_infos

# Display the image and overlay it with the extracted text.
plt.figure(figsize=(5, 5))
image = Image.open(BytesIO(requests.get(image_url).content))
ax = plt.imshow(image, alpha=0.5)
for word in word_infos:
    bbox = [int(num) for num in word["boundingBox"].split(",")]
    text = word["text"]
    origin = (bbox[0], bbox[1])
    patch = Rectangle(origin, bbox[2], bbox[3],
                      fill=False, linewidth=2, color='y')
    ax.axes.add_patch(patch)
    plt.text(origin[0], origin[1], text, fontsize=20, weight="bold", va="top")
plt.show()
plt.axis("off")
```

## <a name="upload-image-from-local-storage"></a>로컬 스토리지의 이미지 업로드

로컬 이미지를 분석하려면 Content-Type 헤더를 애플리케이션/옥텟 스트림으로 설정하고, 요청 본문을 JSON 데이터가 아닌 바이트 배열로 설정합니다.

```python
image_path = "<path-to-local-image-file>"
# Read the image into a byte array
image_data = open(image_path, "rb").read()
# Set Content-Type to octet-stream
headers = {'Ocp-Apim-Subscription-Key': subscription_key, 'Content-Type': 'application/octet-stream'}
# put the byte array into your post request
response = requests.post(ocr_url, headers=headers, params=params, data = image_data)
```


## <a name="examine-the-response"></a>응답 검사

성공적인 응답이 JSON을 통해 반환됩니다. 샘플 웹 페이지에서 다음 예제와 비슷한 성공적인 응답을 구문 분석하고 명령 프롬프트 창에 표시합니다.

```json
{
  "language": "en",
  "orientation": "Up",
  "textAngle": 0,
  "regions": [
    {
      "boundingBox": "21,16,304,451",
      "lines": [
        {
          "boundingBox": "28,16,288,41",
          "words": [
            {
              "boundingBox": "28,16,288,41",
              "text": "NOTHING"
            }
          ]
        },
        {
          "boundingBox": "27,66,283,52",
          "words": [
            {
              "boundingBox": "27,66,283,52",
              "text": "EXISTS"
            }
          ]
        },
        {
          "boundingBox": "27,128,292,49",
          "words": [
            {
              "boundingBox": "27,128,292,49",
              "text": "EXCEPT"
            }
          ]
        },
        {
          "boundingBox": "24,188,292,54",
          "words": [
            {
              "boundingBox": "24,188,292,54",
              "text": "ATOMS"
            }
          ]
        },
        {
          "boundingBox": "22,253,297,32",
          "words": [
            {
              "boundingBox": "22,253,105,32",
              "text": "AND"
            },
            {
              "boundingBox": "144,253,175,32",
              "text": "EMPTY"
            }
          ]
        },
        {
          "boundingBox": "21,298,304,60",
          "words": [
            {
              "boundingBox": "21,298,304,60",
              "text": "SPACE."
            }
          ]
        },
        {
          "boundingBox": "26,387,294,37",
          "words": [
            {
              "boundingBox": "26,387,210,37",
              "text": "Everything"
            },
            {
              "boundingBox": "249,389,71,27",
              "text": "else"
            }
          ]
        },
        {
          "boundingBox": "127,431,198,36",
          "words": [
            {
              "boundingBox": "127,431,31,29",
              "text": "is"
            },
            {
              "boundingBox": "172,431,153,36",
              "text": "opinion."
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계

다음으로, Computer Vision을 사용하여 OCR(광학 문자 인식)을 수행하고, 스마트하게 잘리는 썸네일을 만들고, 이미지의 시각적 기능을 검색, 분류, 태그 지정 및 설명하는 Python 애플리케이션을 살펴봅니다.

> [!div class="nextstepaction"]
> [Computer Vision API Python 자습서](../Tutorials/PythonTutorial.md)

* Computer Vision API를 사용하여 신속하게 실험하려면 [API 테스트 콘솔 열기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)를 사용하세요.
