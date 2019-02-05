---
title: '빠른 시작: 이미지에 있는 얼굴의 감정 인식 - Emotion API, cURL'
titlesuffix: Azure Cognitive Services
description: cURL로 Emotion API를 빠르게 사용하는 데 도움이 되는 정보 및 코드 샘플을 가져옵니다.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: quickstart
ms.date: 05/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: 2cd139fc47177d429bada454a5e720b7eb4f192b
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55220555"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>빠른 시작: 이미지에서 얼굴의 감정을 인식하는 앱을 빌드합니다.

> [!IMPORTANT]
> Emotion API는 2019년 2월 15일부터 더 이상 사용되지 않습니다. 이제 감정 인식 기능은 [Face API](https://docs.microsoft.com/azure/cognitive-services/face/)의 일부로 일반 공급됩니다.

이 문서에서는 cURL로 [Emotion API Recognize 메서드](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)를 빠르게 사용하여 이미지에서 한 명 이상의 사용자가 표현하는 감정을 인식하는 데 도움이 되는 정보 및 코드 샘플을 제공합니다.

## <a name="prerequisite"></a>필수 요소
* [여기](https://azure.microsoft.com/try/cognitive-services/)에서 무료 구독 키 가져오기

## <a name="recognize-emotions-curl-example-request"></a>감정 인식 cURL 예제 요청

> [!NOTE]
> 구독 키를 가져오는 데 사용한 것과 동일한 위치를 REST 호출에서 사용해야 합니다. 예를 들어, westcentralus에서 구독 키를 받은 경우 아래 URL의 "westus"를 "westcentralus"로 바꿉니다.

```json
@ECHO OFF

curl -v -X POST "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: {subscription key}"

--data-ascii "{body}"
```

## <a name="recognize-emotions-sample-response"></a>감정 인식 샘플 응답
호출이 성공하면 얼굴 사각형 크기의 내림차순으로 얼굴 항목 및 연결된 감정 점수 배열이 반환됩니다. 빈 응답은 검색된 얼굴이 없는 것을 나타냅니다. 감정 항목에는 다음 필드가 포함됩니다.
* faceRectangle - 이미지에서 얼굴의 사각형 위치입니다.
* scores - 이미지의 각 얼굴에 대한 감정 점수입니다.

```json
application/json
[
  {
    "faceRectangle": {
      "left": 68,
      "top": 97,
      "width": 64,
      "height": 97
    },
    "scores": {
      "anger": 0.00300731952,
      "contempt": 5.14648448E-08,
      "disgust": 9.180124E-06,
      "fear": 0.0001912825,
      "happiness": 0.9875571,
      "neutral": 0.0009861537,
      "sadness": 1.889955E-05,
      "surprise": 0.008229999
    }
  }
]
