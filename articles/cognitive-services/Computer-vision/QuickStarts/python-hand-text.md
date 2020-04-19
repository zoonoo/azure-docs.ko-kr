---
title: '빠른 시작:  Computer Vision 2.0 및 2.1 - 인쇄 및 필기 텍스트 추출 - REST, Python'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Python과 함께 Computer Vision API를 사용하여 이미지의 인쇄 및 필기 텍스트를 추출합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 29944311c2215ebcc46ff3752004092bdeb9a9c8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81404403"
---
# <a name="quickstart-extract-printed-and-handwritten-text-using-the-computer-vision-rest-api-and-python"></a>빠른 시작: Computer Vision REST API 및 Python을 사용하여 인쇄 및 필기 텍스트 추출

이 빠른 시작에서는 Computer Vision REST API를 사용하여 이미지에서 인쇄 및/또는 필기 텍스트를 추출합니다. [일괄 읽기](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) 및 [읽기 작업 결과](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/5be108e7498a4f9ed20bf96d) 메서드를 사용하면 이미지의 텍스트를 감지한 후 인식된 문자를 머신에서 판독 가능한 문자 스트림으로 추출할 수 있습니다. API는 각 텍스트 줄에 사용할 인식 모델을 결정하므로, 인쇄 텍스트와 필기 텍스트를 사용하는 이미지를 모두 지원합니다.

이 기능은 v2.1 API와 v3.0 공개 미리 보기 API에서 모두 사용할 수 있습니다. v2.1에 비해 3.0 API에는 다음이 포함되어 있습니다.

* 정확도 개선
* 단어에 대한 신뢰도 점수
* 추가 `language` 매개 변수를 사용하여 스페인어와 영어 모두 지원
* 다른 출력 형식

사용 중인 버전의 아래 탭을 선택합니다.

#### <a name="version-2"></a>[버전 2](#tab/version-2)

> [!IMPORTANT]
> [일괄 읽기](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) 메서드는 비동기적으로 실행됩니다. 이 메서드는 성공한 응답의 본문에 있는 정보를 반환하지 않습니다. 대신, 일괄 읽기 메서드는 `Operation-Location` 응답 헤더 필드의 값으로 URI를 반환합니다. 그러면 [읽기 작업 결과](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/5be108e7498a4f9ed20bf96d) API를 나타내는 이 URI를 호출하여 상태를 확인하고 일괄 읽기 메서드 호출 결과를 반환할 수 있습니다.

#### <a name="version-3-public-preview"></a>[버전 3(공개 미리 보기)](#tab/version-3)

> [!IMPORTANT]
> [일괄 읽기](https://westus2.dev.cognitive.microsoft.com/docs/services/5d98695995feb7853f67d6a6/operations/5d986960601faab4bf452005) 메서드는 비동기적으로 실행됩니다. 이 메서드는 성공한 응답의 본문에 있는 정보를 반환하지 않습니다. 대신, 일괄 읽기 메서드는 `Operation-Location` 응답 헤더 필드의 값으로 URI를 반환합니다. 그러면 [읽기 작업 결과](https://westus2.dev.cognitive.microsoft.com/docs/services/5d98695995feb7853f67d6a6/operations/5d9869604be85dee480c8750) API를 나타내는 이 URI를 호출하여 상태를 확인하고 일괄 읽기 메서드 호출 결과를 반환할 수 있습니다.

---

[MyBinder](https://mybinder.org)의 Jupyter Notebook을 사용하면 이 빠른 시작을 단계별로 실행할 수 있습니다. Binder를 시작하려면 다음 단추를 선택합니다.

[![시작 바인더 단추](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="prerequisites"></a>사전 요구 사항

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services)을 만듭니다.

- 샘플을 로컬로 실행하려면 [Python](https://www.python.org/downloads/)이 설치되어 있어야 합니다.
- Computer Vision에 대한 구독 키가 있어야 합니다. [Cognitive Services 사용해보기](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)에서 평가판 키를 가져올 수 있습니다. 또는 [Cognitive Services 계정 만들기](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)의 지침에 따라 Computer Vision을 구독하고 키를 가져옵니다. 그런 다음, 각각 `COMPUTER_VISION_SUBSCRIPTION_KEY` 및 `COMPUTER_VISION_ENDPOINT`라는 키 및 서비스 엔드포인트 문자열에 대한 [환경 변수를 만듭니다](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication).

## <a name="create-and-run-the-sample"></a>샘플 만들기 및 실행

#### <a name="version-2"></a>[버전 2](#tab/version-2)

샘플을 만들고 실행하려면 다음 단계를 수행합니다.

1. 다음 코드를 텍스트 편집기에 복사합니다.
1. 필요에 따라 `image_url`의 값을 텍스트를 추출하려는 다른 이미지의 URL로 바꿉니다.
1. 코드를 `.py` 확장명의 파일로 저장합니다. `get-text.py`)을 입력합니다.
1. 명령 프롬프트 창을 엽니다.
1. 프롬프트에서 `python` 명령을 사용하여 샘플을 실행합니다. `python get-text.py`)을 입력합니다.

```python
import os
import sys
import requests
import time
# If you are using a Jupyter notebook, uncomment the following line.
# %matplotlib inline
import matplotlib.pyplot as plt
from matplotlib.patches import Polygon
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

text_recognition_url = endpoint + "vision/v2.1/read/core/asyncBatchAnalyze"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/d/dd/Cursive_Writing_on_Notebook_paper.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
data = {'url': image_url}
response = requests.post(
    text_recognition_url, headers=headers, json=data)
response.raise_for_status()

# Extracting text requires two API calls: One call to submit the
# image for processing, the other to retrieve the text found in the image.

# Holds the URI used to retrieve the recognized text.
operation_url = response.headers["Operation-Location"]

# The recognized text isn't immediately available, so poll to wait for completion.
analysis = {}
poll = True
while (poll):
    response_final = requests.get(
        response.headers["Operation-Location"], headers=headers)
    analysis = response_final.json()
    print(analysis)
    time.sleep(1)
    if ("recognitionResults" in analysis):
        poll = False
    if ("status" in analysis and analysis['status'] == 'Failed'):
        poll = False

polygons = []
if ("recognitionResults" in analysis):
    # Extract the recognized text, with bounding boxes.
    polygons = [(line["boundingBox"], line["text"])
                for line in analysis["recognitionResults"][0]["lines"]]

# Display the image and overlay it with the extracted text.
plt.figure(figsize=(15, 15))
image = Image.open(BytesIO(requests.get(image_url).content))
ax = plt.imshow(image)
for polygon in polygons:
    vertices = [(polygon[0][i], polygon[0][i+1])
                for i in range(0, len(polygon[0]), 2)]
    text = polygon[1]
    patch = Polygon(vertices, closed=True, fill=False, linewidth=2, color='y')
    ax.axes.add_patch(patch)
    plt.text(vertices[0][0], vertices[0][1], text, fontsize=20, va="top")
```

#### <a name="version-3-public-preview"></a>[버전 3(공개 미리 보기)](#tab/version-3)

샘플을 만들고 실행하려면 다음 단계를 수행합니다.

1. 다음 코드를 텍스트 편집기에 복사합니다.
1. 필요에 따라 `image_url`의 값을 텍스트를 추출하려는 다른 이미지의 URL로 바꿉니다.
1. 코드를 `.py` 확장명의 파일로 저장합니다. `get-text.py`)을 입력합니다.
1. 명령 프롬프트 창을 엽니다.
1. 프롬프트에서 `python` 명령을 사용하여 샘플을 실행합니다. `python get-text.py`)을 입력합니다.

```python
import json
import os
import sys
import requests
import time
# If you are using a Jupyter notebook, uncomment the following line.
# %matplotlib inline
import matplotlib.pyplot as plt
from matplotlib.patches import Polygon
from PIL import Image
from io import BytesIO

missing_env = False
# Add your Computer Vision subscription key and endpoint to your environment variables.
if 'COMPUTER_VISION_ENDPOINT' in os.environ:
    endpoint = os.environ['COMPUTER_VISION_ENDPOINT']
else:
    print("From Azure Cogntivie Service, retrieve your endpoint and subscription key.")
    print("\nSet the COMPUTER_VISION_ENDPOINT environment variable, such as \"https://westus2.api.cognitive.microsoft.com\".\n")
    missing_env = True

if 'COMPUTER_VISION_SUBSCRIPTION_KEY' in os.environ:
    subscription_key = os.environ['COMPUTER_VISION_SUBSCRIPTION_KEY']
else:
    print("From Azure Cogntivie Service, retrieve your endpoint and subscription key.")
    print("\nSet the COMPUTER_VISION_SUBSCRIPTION_KEY environment variable, such as \"1234567890abcdef1234567890abcdef\".\n")
    missing_env = True

if missing_env:
    print("**Restart your shell or IDE for changes to take effect.**")
    sys.exit()

text_recognition_url = endpoint + "/vision/v3.0-preview/read/analyze"

# Set image_url to the URL of an image that you want to recognize.
image_url = "https://upload.wikimedia.org/wikipedia/commons/d/dd/Cursive_Writing_on_Notebook_paper.jpg"

# Set the langauge that you want to recognize. The value can be "en" for English, and "es" for Spanish
language = "en"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
data = {'url': image_url}
response = requests.post(
    text_recognition_url, headers=headers, json=data, params={'language': language})
response.raise_for_status()

# Extracting text requires two API calls: One call to submit the
# image for processing, the other to retrieve the text found in the image.

# Holds the URI used to retrieve the recognized text.
operation_url = response.headers["Operation-Location"]

# The recognized text isn't immediately available, so poll to wait for completion.
analysis = {}
poll = True
while (poll):
    response_final = requests.get(
        response.headers["Operation-Location"], headers=headers)
    analysis = response_final.json()
    
    print(json.dumps(analysis, indent=4))

    time.sleep(1)
    if ("analyzeResult" in analysis):
        poll = False
    if ("status" in analysis and analysis['status'] == 'failed'):
        poll = False

polygons = []
if ("analyzeResult" in analysis):
    # Extract the recognized text, with bounding boxes.
    polygons = [(line["boundingBox"], line["text"])
                for line in analysis["analyzeResult"]["readResults"][0]["lines"]]

# Display the image and overlay it with the extracted text.
image = Image.open(BytesIO(requests.get(image_url).content))
ax = plt.imshow(image)
for polygon in polygons:
    vertices = [(polygon[0][i], polygon[0][i+1])
                for i in range(0, len(polygon[0]), 2)]
    text = polygon[1]
    patch = Polygon(vertices, closed=True, fill=False, linewidth=2, color='y')
    ax.axes.add_patch(patch)
    plt.text(vertices[0][0], vertices[0][1], text, fontsize=20, va="top")
plt.show()
```

---

## <a name="examine-the-response"></a>응답 검사

성공적인 응답이 JSON을 통해 반환됩니다. 샘플 웹 페이지에서 다음 예제와 비슷한 성공적인 응답을 구문 분석하고 명령 프롬프트 창에 표시합니다.

#### <a name="version-2"></a>[버전 2](#tab/version-2)

```json
{
  "status": "Succeeded",
  "recognitionResult": {
    "lines": [
      {
        "boundingBox": [
          2,
          52,
          65,
          46,
          69,
          89,
          7,
          95
        ],
        "text": "dog",
        "words": [
          {
            "boundingBox": [
              0,
              59,
              63,
              43,
              77,
              86,
              3,
              102
            ],
            "text": "dog"
          }
        ]
      },
      {
        "boundingBox": [
          6,
          2,
          771,
          13,
          770,
          75,
          5,
          64
        ],
        "text": "The quick brown fox jumps over the lazy",
        "words": [
          {
            "boundingBox": [
              0,
              4,
              92,
              5,
              77,
              71,
              0,
              71
            ],
            "text": "The"
          },
          {
            "boundingBox": [
              74,
              4,
              189,
              5,
              174,
              72,
              60,
              71
            ],
            "text": "quick"
          },
          {
            "boundingBox": [
              176,
              5,
              321,
              6,
              306,
              73,
              161,
              72
            ],
            "text": "brown"
          },
          {
            "boundingBox": [
              308,
              6,
              387,
              6,
              372,
              73,
              293,
              73
            ],
            "text": "fox"
          },
          {
            "boundingBox": [
              382,
              6,
              506,
              7,
              491,
              74,
              368,
              73
            ],
            "text": "jumps"
          },
          {
            "boundingBox": [
              492,
              7,
              607,
              8,
              592,
              75,
              478,
              74
            ],
            "text": "over"
          },
          {
            "boundingBox": [
              589,
              8,
              673,
              8,
              658,
              75,
              575,
              75
            ],
            "text": "the"
          },
          {
            "boundingBox": [
              660,
              8,
              783,
              9,
              768,
              76,
              645,
              75
            ],
            "text": "lazy"
          }
        ]
      },
      {
        "boundingBox": [
          2,
          84,
          783,
          96,
          782,
          154,
          1,
          148
        ],
        "text": "Pack my box with five dozen liquor jugs",
        "words": [
          {
            "boundingBox": [
              0,
              86,
              94,
              87,
              72,
              151,
              0,
              149
            ],
            "text": "Pack"
          },
          {
            "boundingBox": [
              76,
              87,
              164,
              88,
              142,
              152,
              54,
              150
            ],
            "text": "my"
          },
          {
            "boundingBox": [
              155,
              88,
              243,
              89,
              222,
              152,
              134,
              151
            ],
            "text": "box"
          },
          {
            "boundingBox": [
              226,
              89,
              344,
              90,
              323,
              154,
              204,
              152
            ],
            "text": "with"
          },
          {
            "boundingBox": [
              336,
              90,
              432,
              91,
              411,
              154,
              314,
              154
            ],
            "text": "five"
          },
          {
            "boundingBox": [
              419,
              91,
              538,
              92,
              516,
              154,
              398,
              154
            ],
            "text": "dozen"
          },
          {
            "boundingBox": [
              547,
              92,
              701,
              94,
              679,
              154,
              525,
              154
            ],
            "text": "liquor"
          },
          {
            "boundingBox": [
              696,
              94,
              800,
              95,
              780,
              154,
              675,
              154
            ],
            "text": "jugs"
          }
        ]
      }
    ]
  }
}
```

#### <a name="version-3-public-preview"></a>[버전 3(공개 미리 보기)](#tab/version-3)

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-02-11T17:54:10Z",
    "lastUpdatedDateTime": "2020-02-11T17:54:10Z",
    "analyzeResult": {
        "version": "3.0.0",
        "readResults": [
            {
                "page": 1,
                "language": "en",
                "angle": 0.5268,
                "width": 1875,
                "height": 361,
                "unit": "pixel",
                "lines": [
                    {
                        "language": "en",
                        "boundingBox": [
                            8,
                            9,
                            1814,
                            23,
                            1812,
                            138,
                            7,
                            121
                        ],
                        "text": "The quick brown fox jumps over the lazy",
                        "words": [
                            {
                                "boundingBox": [
                                    31,
                                    10,
                                    184,
                                    14,
                                    177,
                                    116,
                                    24,
                                    112
                                ],
                                "text": "The",
                                "confidence": 0.905
                            },
                            {
                                "boundingBox": [
                                    204,
                                    14,
                                    430,
                                    18,
                                    422,
                                    123,
                                    197,
                                    117
                                ],
                                "text": "quick",
                                "confidence": 0.762
                            },
                            {
                                "boundingBox": [
                                    450,
                                    18,
                                    736,
                                    22,
                                    727,
                                    130,
                                    442,
                                    124
                                ],
                                "text": "brown",
                                "confidence": 0.57
                            },
                            {
                                "boundingBox": [
                                    756,
                                    23,
                                    895,
                                    24,
                                    886,
                                    133,
                                    747,
                                    130
                                ],
                                "text": "fox",
                                "confidence": 0.847
                            },
                            {
                                "boundingBox": [
                                    915,
                                    24,
                                    1168,
                                    25,
                                    1158,
                                    136,
                                    906,
                                    133
                                ],
                                "text": "jumps",
                                "confidence": 0.762
                            },
                            {
                                "boundingBox": [
                                    1188,
                                    25,
                                    1400,
                                    26,
                                    1390,
                                    138,
                                    1178,
                                    136
                                ],
                                "text": "over",
                                "confidence": 0.764
                            },
                            {
                                "boundingBox": [
                                    1420,
                                    26,
                                    1566,
                                    25,
                                    1556,
                                    138,
                                    1410,
                                    138
                                ],
                                "text": "the",
                                "confidence": 0.888
                            },
                            {
                                "boundingBox": [
                                    1586,
                                    25,
                                    1812,
                                    24,
                                    1801,
                                    138,
                                    1576,
                                    138
                                ],
                                "text": "lazy",
                                "confidence": 0.57
                            }
                        ]
                    },
                    {
                        "language": "en",
                        "boundingBox": [
                            15,
                            125,
                            186,
                            135,
                            183,
                            226,
                            8,
                            218
                        ],
                        "text": "dog.",
                        "words": [
                            {
                                "boundingBox": [
                                    15,
                                    125,
                                    187,
                                    133,
                                    182,
                                    227,
                                    10,
                                    218
                                ],
                                "text": "dog.",
                                "confidence": 0.424
                            }
                        ]
                    },
                    {
                        "language": "en",
                        "boundingBox": [
                            9,
                            219,
                            1858,
                            232,
                            1856,
                            338,
                            7,
                            317
                        ],
                        "text": "Pack my box with five dozen liquor jugs.",
                        "words": [
                            {
                                "boundingBox": [
                                    11,
                                    234,
                                    191,
                                    228,
                                    189,
                                    316,
                                    8,
                                    319
                                ],
                                "text": "Pack",
                                "confidence": 0.694
                            },
                            {
                                "boundingBox": [
                                    208,
                                    227,
                                    341,
                                    224,
                                    339,
                                    314,
                                    205,
                                    316
                                ],
                                "text": "my",
                                "confidence": 0.57
                            },
                            {
                                "boundingBox": [
                                    380,
                                    223,
                                    530,
                                    220,
                                    528,
                                    313,
                                    378,
                                    314
                                ],
                                "text": "box",
                                "confidence": 0.252
                            },
                            {
                                "boundingBox": [
                                    553,
                                    220,
                                    781,
                                    220,
                                    778,
                                    314,
                                    550,
                                    313
                                ],
                                "text": "with",
                                "confidence": 0.958
                            },
                            {
                                "boundingBox": [
                                    814,
                                    220,
                                    986,
                                    222,
                                    984,
                                    316,
                                    812,
                                    314
                                ],
                                "text": "five",
                                "confidence": 0.617
                            },
                            {
                                "boundingBox": [
                                    1003,
                                    222,
                                    1231,
                                    228,
                                    1229,
                                    320,
                                    1001,
                                    316
                                ],
                                "text": "dozen",
                                "confidence": 0.694
                            },
                            {
                                "boundingBox": [
                                    1303,
                                    230,
                                    1608,
                                    244,
                                    1607,
                                    330,
                                    1302,
                                    321
                                ],
                                "text": "liquor",
                                "confidence": 0.427
                            },
                            {
                                "boundingBox": [
                                    1647,
                                    247,
                                    1855,
                                    260,
                                    1855,
                                    338,
                                    1646,
                                    331
                                ],
                                "text": "jugs.",
                                "confidence": 0.57
                            }
                        ]
                    }
                ]
            }
        ]
    }
}
```

---

## <a name="next-steps"></a>다음 단계

다음으로, Computer Vision을 사용하여 OCR(광학 문자 인식)을 수행하고, 스마트하게 잘리는 썸네일을 만들고, 이미지의 시각적 기능을 검색, 분류, 태그 지정 및 설명하는 Python 애플리케이션을 살펴봅니다.

> [!div class="nextstepaction"]
> [Computer Vision API Python 자습서](../Tutorials/PythonTutorial.md)

* Computer Vision API를 사용하여 신속하게 실험하려면 [API 테스트 콘솔 열기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)를 사용하세요.
