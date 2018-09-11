---
title: Computer Vision Python 빠른 시작 만들기 썸네일 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 이 빠른 시작에서는 Cognitive Services에서 Python과 함께 Computer Vision을 사용하여 이미지에서 썸네일을 생성합니다.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: bc1d01cd4b8f15ba627d917825ee5205c34f5cdf
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43750659"
---
# <a name="quickstart-generate-a-thumbnail---rest-python"></a>빠른 시작: 썸네일 생성 - REST, Python

이 빠른 시작에서는 Computer Vision을 사용하여 이미지에서 썸네일을 생성합니다.

[MyBinder](https://mybinder.org)의 Jupyter Notebook을 사용하면 이 빠른 시작을 단계별로 실행할 수 있습니다. Binder를 시작하려면 다음 단추를 선택합니다.

[![바인더](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="prerequisites"></a>필수 조건

Computer Vision을 사용하려면 구독 키가 필요합니다. [구독 키 얻기](../Vision-API-How-to-Topics/HowToSubscribe.md)를 참조하세요.

## <a name="intelligently-generate-a-thumbnail"></a>지능적으로 썸네일 생성

[썸네일 가져오기 메서드](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb)를 사용하여 이미지의 썸네일을 생성할 수 있습니다. 높이와 너비를 지정합니다. 입력 이미지의 가로 세로 비율과 다를 수 있습니다. Computer Vision은 스마트 자르기를 사용하여 관심 영역을 지능적으로 식별하고 해당 영역을 기반으로 자르기 좌표를 생성합니다.

샘플을 실행하려면 다음 단계를 수행합니다.

1. 새 Python 스크립트 파일에 다음 코드를 복사합니다.
1. `<Subscription Key>`를 유효한 구독 키로 바꿉니다.
1. 필요한 경우 `vision_base_url` 값을 구독 키를 가져온 위치로 변경합니다.
1. 필요에 따라 `image_url` 값을 다른 이미지로 변경합니다.
1. 스크립트를 실행합니다.

다음 코드에서는 Python `requests` 라이브러리를 사용하여 Computer Vision 분석 이미지 API를 호출합니다. API 키는 `headers` 사전을 통해 전달됩니다. 썸네일의 크기는 `params` 사전을 통해 전달됩니다. 썸네일은 응답에서 바이트 배열로 반환됩니다.

## <a name="get-thumbnail-request"></a>썸네일 가져오기 요청

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

## <a name="next-steps"></a>다음 단계

Computer Vision을 사용하는 Python 응용 프로그램을 탐색합니다. 이 프로그램은 OCR(광학 문자 인식)을 수행하고, 스마트하게 자른 썸네일을 만들고, 이미지에서 얼굴을 비롯한 시각적 특징을 감지하고, 분류하고, 태그를 지정하고, 설명합니다. Computer Vision API를 사용하여 신속하게 실험하려면 [API 테스트 콘솔 열기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)를 사용하세요.

> [!div class="nextstepaction"]
> [Computer Vision API Python 자습서](../Tutorials/PythonTutorial.md)
