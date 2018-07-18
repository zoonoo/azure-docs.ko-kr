---
title: Face API Python 자습서 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Python SDK와 함께 Face API를 사용하여 Cognitive Services에서 이미지의 인간 얼굴을 감지하는 방법을 알아봅니다.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 90d74d8df2ed59e6f3313ef7c620284d1022a667
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049114"
---
# <a name="getting-started-with-face-api-in-python-tutorial"></a>Python에서 Face API 시작 자습서

이 자습서에서는 Python SDK를 통해 Face API를 호출하여 이미지의 인간 얼굴을 감지하는 방법을 알아봅니다.

## <a name="prerequisites"></a> 필수 조건

이 자습서를 사용하려면 다음을 수행해야 합니다.

- Python 2.7+ 또는 Python 3.5+ 중 하나를 설치합니다.
- pip를 설치합니다.
- 다음과 같이 Face API용 Python SDK를 설치합니다.

```bash
pip install cognitive_face
```

- Microsoft Cognitive Services에 대한 [구독 키](https://azure.microsoft.com/try/cognitive-services/)를 구합니다. 이 자습서의 주 또는 보조 키를 사용할 수 있습니다. (Face API를 사용하려면 유효한 구독 키가 있어야 합니다.)

## <a name="sdk-example"></a> 이미지의 얼굴 감지

```python
import cognitive_face as CF

KEY = '<Subscription Key>'  # Replace with a valid subscription key (keeping the quotes in place).
CF.Key.set(KEY)

BASE_URL = 'https://westus.api.cognitive.microsoft.com/face/v1.0/'  # Replace with your regional Base URL
CF.BaseUrl.set(BASE_URL)

# You can use this example JPG or replace the URL below with your own URL to a JPEG image.
img_url = 'https://raw.githubusercontent.com/Microsoft/Cognitive-Face-Windows/master/Data/detection1.jpg'
faces = CF.face.detect(img_url)
print(faces)
```

다음은 예제 결과입니다. 감지된 얼굴의 `list`입니다. 목록의 각 항목은 `dict` 인스턴스입니다. 여기서 `faceId`는 감지된 얼굴의 고유 ID이고 `faceRectangle`은 감지된 얼굴 위치를 설명합니다. 얼굴 ID는 24시간 후에 만료됩니다.

```python
[{u'faceId': u'68a0f8cf-9dba-4a25-afb3-f9cdf57cca51', u'faceRectangle': {u'width': 89, u'top': 66, u'height': 89, u'left': 446}}]
```

## <a name="draw-rectangles-around-the-faces"></a>얼굴 주위의 사각형 그리기

이전 명령에서 얻은 json 좌표를 사용하여 이미지에 각 얼굴을 시각적으로 나타내는 사각형을 그릴 수 있습니다. `pip install Pillow`를 수행하여 `PIL` 이미징 모듈을 사용해야 합니다.  파일 맨 위에 다음을 추가합니다.

```python
import requests
from io import BytesIO
from PIL import Image, ImageDraw
```

그런 후, 스크립트에서 `print(faces)` 뒤에 다음을 포함합니다.

```python
#Convert width height to a point in a rectangle
def getRectangle(faceDictionary):
    rect = faceDictionary['faceRectangle']
    left = rect['left']
    top = rect['top']
    bottom = left + rect['height']
    right = top + rect['width']
    return ((left, top), (bottom, right))

#Download the image from the url
response = requests.get(img_url)
img = Image.open(BytesIO(response.content))

#For each face returned use the face rectangle and draw a red box.
draw = ImageDraw.Draw(img)
for face in faces:
    draw.rectangle(getRectangle(face), outline='red')

#Display the image in the users default image browser.
img.show()
```

## <a name='further'></a> 추가 탐색

Face API를 추가로 탐색하는 데 도움이 되도록 이 자습서는 GUI 예제를 제공합니다. 이를 실행하려면 먼저 [wxPython](https://wxpython.org/pages/downloads/)을 설치한 후 아래 명령을 실행합니다.

```bash
git clone https://github.com/Microsoft/Cognitive-Face-Python.git
cd Cognitive-Face-Python
python sample
```

## <a name="summary"></a> 요약

이 자습서에서는 Python SDK 호출을 통해 Face API를 사용하기 위한 기본 프로세스에 배웠습니다. API 세부 사항에 대한 자세한 내용은 방법 및 [API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)를 참조하세요.

## <a name="related"></a> 관련 항목

- [CSharp에서 Face API 시작](FaceAPIinCSharpTutorial.md)
- [Android용 Java에서 Face API 시작](FaceAPIinJavaForAndroidTutorial.md)
