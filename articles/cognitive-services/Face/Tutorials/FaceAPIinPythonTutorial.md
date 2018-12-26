---
title: '빠른 시작: Python SDK를 사용하여 이미지에서 얼굴 감지 및 포착'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Face API를 사용하여 원격 이미지에서 얼굴을 감지하고 포착하는 간단한 Python 스크립트를 만들 것입니다.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 11/13/2018
ms.author: sbowles
ms.openlocfilehash: 1e35d650f6fc99bff5bf49e517e2b38fcdc58dde
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53077004"
---
# <a name="quickstart-create-a-python-script-to-detect-and-frame-faces-in-an-image"></a>빠른 시작: 이미지에서 얼굴을 감지 및 포착하는 Python 스크립트 만들기

이 빠른 시작에서는 Azure Face API를 사용하여 Python SDK를 통해 원격 이미지에서 사람 얼굴을 감지하는 간단한 Python 스크립트를 만들 것입니다. 이 애플리케이션은 선택한 이미지를 표시하고 감지된 각 얼굴 주위에 프레임을 그립니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다. 

## <a name="prerequisites"></a>필수 조건

- Face API 구독 키. [Cognitive Services 사용해보기](https://azure.microsoft.com/try/cognitive-services/?api=face-api)에서 평가판 구독 키를 가져올 수 있습니다. 또는 [Cognitive Services 계정 만들기](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)의 지침에 따라 Face API 서비스를 구독하고 키를 가져옵니다.
- [Python 2.7+ 또는 3.5+](https://www.python.org/downloads/)
- [pip](https://pip.pypa.io/en/stable/installing/) 도구

## <a name="get-the-face-sdk"></a>Face SDK 가져오기

명령 프롬프트를 열고 다음 명령을 실행하여 Face Python SDK를 설치합니다.

```shell
pip install cognitive_face
```

## <a name="detect-faces-in-an-image"></a>이미지에서 얼굴 감지

_FaceQuickstart.py_라는 새 Python 스크립트를 만들고 다음 코드를 추가합니다. 얼굴 감지의 핵심 기능입니다. `<Subscription Key>`를 키 값으로 바꿔야 합니다. 또한 키의 올바른 지역 식별자를 사용하도록 `BASE_URL` 값을 변경해야 할 수도 있습니다(모든 지역 엔드포인트 목록에 대해서는 [Face API 설명서](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) 참조). 평가판 구독 키는 **westus** 지역에 생성됩니다. 필요에 따라 `img_url`을 사용하려는 이미지의 URL로 설정합니다.

이 스크립트는 [Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) REST API를 래핑하고 얼굴 목록을 반환하는 **cognitive_face.face.detect** 메서드를 호출하여 얼굴을 감지합니다.

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

### <a name="try-the-app"></a>앱 시험

`python FaceQuickstart.py` 명령으로 앱을 실행합니다. 콘솔 창에 다음과 같은 텍스트 응답이 표시됩니다.

```shell
[{'faceId': '26d8face-9714-4f3e-bfa1-f19a7a7aa240', 'faceRectangle': {'top': 124, 'left': 459, 'width': 227, 'height': 227}}]
```

이것은 감지된 얼굴 목록입니다. 목록의 각 항목은 **dict** 인스턴스이고, 여기서 `faceId`는 감지된 얼굴의 고유 ID이고 `faceRectangle`은 감지된 얼굴의 위치를 설명합니다. 

> [!NOTE]
> Face ID는 24시간 후에 만료됩니다. 장기간 유지하려면 얼굴 데이터를 명시적으로 저장해야 합니다.

## <a name="draw-face-rectangles"></a>얼굴 사각형 그리기

이전 명령에서 얻은 좌표를 사용하여 이미지에 각 얼굴을 시각적으로 나타내는 사각형을 그릴 수 있습니다. Pillow 이미지 모듈을 사용하려면 Pillow(`pip install pillow`)를 설치해야 합니다. *FaceQuickstart.py* 맨 위에 다음 코드를 추가합니다.

```python
import requests
from io import BytesIO
from PIL import Image, ImageDraw
```

그런 다음, 스크립트 맨 아래에 다음 코드를 추가합니다. 이렇게 하면 사각형 좌표를 구문 분석하는 간단한 함수가 만들어지고, Pillow를 사용하여 원래 이미지에 사각형이 그려집니다. 그런 다음, 기본 이미지 뷰어에 이미지가 표시됩니다.

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

## <a name="run-the-app"></a>앱 실행

먼저 기본 이미지 뷰어를 선택하라는 메시지가 표시될 수 있습니다. 그 후 다음과 비슷한 이미지가 표시됩니다. 콘솔 창에 사각형 데이터가 출력된 것도 보일 것입니다.

![얼굴 주위에 빨간색 사각형이 그려진 젊은 여자](../images/face-rectangle-result.png)

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Face API Python SDK를 사용하는 기본 프로세스를 살펴보고, 이미지에서 얼굴을 감지하여 프레임을 그리는 스크립트를 작성했습니다. 다음으로, 좀 더 복잡한 Python SDK 사용 예제를 살펴보겠습니다. GitHub에서 Cognitive Face Python 샘플로 이동하여 프로젝트 폴더에 복제하고, _README.md_ 파일의 지침을 따르세요.

> [!div class="nextstepaction"]
> [Cognitive Face Python 샘플](https://github.com/Microsoft/Cognitive-Face-Python)