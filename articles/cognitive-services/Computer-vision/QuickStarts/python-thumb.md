---
title: '빠른 시작: 썸네일 생성 - REST, Python'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Python과 함께 Computer Vision API를 사용하여 이미지에서 썸네일을 생성합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 9962874600e259a639c70b7b180e5fc2a940461f
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59999555"
---
# <a name="quickstart-generate-a-thumbnail-using-the-rest-api-and-python-in-computer-vision"></a>빠른 시작: Computer Vision에서 REST API 및 Python을 사용하여 썸네일 생성

이 빠른 시작에서는 Computer Vision의 REST API를 사용하여 이미지에서 썸네일을 생성합니다. [썸네일 가져오기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) 메서드를 사용하여 원하는 높이와 너비를 지정할 수 있으며 Computer Vision은 스마트 자르기를 사용하여 관심 영역을 지능적으로 식별하고 해당 영역을 기반으로 자르기 좌표를 생성합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/try/cognitive-services/)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

- Computer Vision에 대한 구독 키가 있어야 합니다. [Cognitive Services 사용해보기](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)에서 평가판 키를 가져올 수 있습니다. 또는 [Cognitive Services 계정 만들기](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)의 지침에 따라 Computer Vision을 구독하고 키를 가져옵니다.
- [Visual Studio Code](https://code.visualstudio.com/download) 같은 코드 편집기

## <a name="create-and-run-the-sample"></a>샘플 만들기 및 실행

샘플을 만들고 실행하려면 코드 편집기에 다음 코드를 복사합니다. 

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

thumbnail_url = vision_base_url + "generateThumbnail"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params  = {'width': '50', 'height': '50', 'smartCropping': 'true'}
data    = {'url': image_url}
response = requests.post(thumbnail_url, headers=headers, params=params, json=data)
response.raise_for_status()

thumbnail = Image.open(BytesIO(response.content))

# Display the thumbnail.
plt.imshow(thumbnail)
plt.axis("off")

# Verify the thumbnail size.
print("Thumbnail is {0}-by-{1}".format(*thumbnail.size))
```

다음으로, 아래 작업을 수행합니다.
1. `subscription_key`의 값을 구독 키로 바꿉니다.
1. 필요한 경우 `vision_base_url`의 값을 구독 키를 가져온 Azure 지역의 Computer Vision 리소스에 대한 엔드포인트 URL로 바꿉니다.
1. 필요에 따라 `image_url`의 값을 썸네일을 생성하려는 다른 이미지의 URL로 바꿉니다.
1. 코드를 `.py` 확장명의 파일로 저장합니다. 예: `get-thumbnail.py`
1. 명령 프롬프트 창을 엽니다.
1. 프롬프트에서 `python` 명령을 사용하여 샘플을 실행합니다. 예: `python get-thumbnail.py`

## <a name="examine-the-response"></a>응답 검사

성공적인 응답이 썸네일에 대한 이미지 데이터를 나타내는 이진 데이터로 반환됩니다. 샘플은 이 이미지를 표시합니다. 요청이 실패하면 응답이 명령 프롬프트 창에 표시되고 오류 코드가 포함됩니다.

## <a name="run-in-jupyter-optional"></a>Jupyter에서 실행(선택 사항)

[MyBinder](https://mybinder.org)의 Jupyter Notebook을 사용하면 필요에 따라 이 빠른 시작을 단계별로 실행할 수 있습니다. Binder를 시작하려면 다음 단추를 선택합니다.

[![바인더](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="next-steps"></a>다음 단계

다음으로, 썸네일 생성 기능에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [썸네일 생성](../concept-generating-thumbnails.md)
