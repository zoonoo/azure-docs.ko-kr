---
title: '빠른 시작: 이미지에 있는 얼굴의 감정 인식 - Emotion API, Python'
description: Python으로 Emotion API를 빠르게 사용하는 데 도움이 되는 정보 및 코드 샘플을 가져옵니다.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: quickstart
ms.date: 02/05/2018
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: bdc42233d027134ca42e52398eeefd83ae45669f
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55211256"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>빠른 시작: 이미지에서 얼굴의 감정을 인식하는 앱을 빌드합니다.

> [!IMPORTANT]
> Emotion API는 2019년 2월 15일부터 더 이상 사용되지 않습니다. 이제 감정 인식 기능은 [Face API](https://docs.microsoft.com/azure/cognitive-services/face/)의 일부로 일반 공급됩니다. 

이 문서에서는 Python으로 [Emotion API Recognize 메서드](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)를 빠르게 사용하여 이미지에서 한 명 이상의 사용자가 표현하는 감정을 인식하는 데 도움이 되는 정보 및 코드 샘플을 제공합니다.

바인더 배지 시작을 클릭하여 [MyBinder](https://mybinder.org)에서 Jupyter 노트북으로 이 예제를 실행할 수 있습니다. [![바인더](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=EmotionAPI.ipynb)


## <a name="prerequisite"></a>필수 요소
[여기](https://azure.microsoft.com/try/cognitive-services/)에서 무료 구독 키 가져오기

## <a name="running-the-walkthrough"></a>연습 실행
이 연습을 계속하려면 `subscription_key`를 앞에서 받은 API 키로 바꿉니다.


```python
subscription_key = None
assert subscription_key
```

다음으로, 서비스 URL이 API 키를 설정할 때 사용한 지역과 일치하는지 확인합니다. 평가판 키를 사용하는 경우 아무 것도 변경할 필요가 없습니다.


```python
emotion_recognition_url = "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize"
```

이 연습에서는 디스크에 저장된 이미지를 사용합니다. 공개적으로 액세스할 수 있는 URL을 통해 제공되는 이미지를 사용해도 됩니다. 자세한 내용은 [REST API 설명서](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)를 참조하세요.

이미지 데이터는 요청 본문의 일부로 전달되므로 `Content-Type` 헤더를 `application/octet-stream`으로 설정해야 합니다. URL을 통해 이미지를 전달하는 경우 다음과 같이 헤더를 설정합니다.
```python
header = {'Ocp-Apim-Subscription-Key': subscription_key }
```
해당 URL을 포함하는 사전을 만들도록 설정:
```python
data = {'url': image_url}
```
다음을 사용하여 `requests` 라이브러리에 전달:
```python
requests.post(emotion_recognition_url, headers=headers, json=image_data)
```

먼저 [Emotion API](https://azure.microsoft.com/services/cognitive-services/emotion/) 사이트에서 몇 가지 샘플 이미지를 다운로드합니다.


```bash
%%bash
mkdir -p images
curl -Ls https://aka.ms/csnb-emotion-1 -o images/emotion_1.jpg
curl -Ls https://aka.ms/csnb-emotion-2 -o images/emotion_2.jpg
```


```python
image_path = "images/emotion_1.jpg"
image_data = open(image_path, "rb").read()
```


```python
import requests
headers  = {'Ocp-Apim-Subscription-Key': subscription_key, "Content-Type": "application/octet-stream" }
response = requests.post(emotion_recognition_url, headers=headers, data=image_data)
response.raise_for_status()
analysis = response.json()
analysis
```




    [{'faceRectangle': {'height': 162, 'left': 130, 'top': 141, 'width': 162},
      'scores': {'anger': 9.29041e-06,
       'contempt': 0.000118981574,
       'disgust': 3.15619363e-05,
       'fear': 0.000589638,
       'happiness': 0.06630674,
       'neutral': 0.00555004273,
       'sadness': 7.44669524e-06,
       'surprise': 0.9273863}}]



반환된 JSON 개체에는 감지된 감정과 함께 인식된 얼굴의 경계 상자가 포함됩니다. 각 감정은 0~1 사이의 점수와 연결되며 점수가 높을수록 감정이 직설적입니다.

다음 코드 줄은 `matplotlib` 라이브러리를 사용하여 이미지의 얼굴에 드러난 감정을 감지했습니다. 혼잡을 방지하기 위해 상위 3개 감정만 표시됩니다.


```python
%matplotlib inline
import matplotlib.pyplot as plt
from matplotlib.patches import Rectangle
from io import BytesIO
from PIL import Image

plt.figure(figsize=(5,5))

image  = Image.open(BytesIO(image_data))
ax     = plt.imshow(image, alpha=0.6)

for face in analysis:
    fr = face["faceRectangle"]
    em = face["scores"]
    origin = (fr["left"], fr["top"])
    p = Rectangle(origin, fr["width"], fr["height"], fill=False, linewidth=2, color='b')
    ax.axes.add_patch(p)
    ct = "\n".join(["{0:<10s}{1:>.4f}".format(k,v) for k, v in sorted(list(em.items()),key=lambda r: r[1], reverse=True)][:3])
    plt.text(origin[0], origin[1], ct, fontsize=20)    
_ = plt.axis("off")
```

그 다음으로 표시되는 `annotate_image` 함수는 파일 시스템에 경로가 지정된 이미지 파일 위에 감정을 오버레이하는 데 사용할 수 있습니다. 앞에서 살펴본 Emotion API를 호출하는 코드를 기반으로 합니다.


```python
def annotate_image(image_path):    
    image_data = open(image_path, "rb").read()
    headers  = {'Ocp-Apim-Subscription-Key': subscription_key, "Content-Type": "application/octet-stream" }
    response = requests.post(emotion_recognition_url, headers=headers, data=image_data)
    response.raise_for_status()
    analysis = response.json()

    plt.figure()

    image  = Image.open(image_path)
    ax     = plt.imshow(image, alpha=0.6)

    for face in analysis:
        fr = face["faceRectangle"]
        em = face["scores"]
        origin = (fr["left"], fr["top"])
        p = Rectangle(origin, fr["width"], fr["height"], fill=False, linewidth=2, color='b')
        ax.axes.add_patch(p)
        ct = "\n".join(["{0:<10s}{1:>.4f}".format(k,v) for k, v in sorted(list(em.items()),key=lambda r: r[1], reverse=True)][:3])
        plt.text(origin[0], origin[1], ct, fontsize=20, va="bottom")    
    _ = plt.axis("off")
```

마지막으로, `annotate_image` 함수는 다음 줄에 보여드리는 것처럼 이미지 파일에서 호출할 수 있습니다.


```python
annotate_image("images/emotion_2.jpg")
```
