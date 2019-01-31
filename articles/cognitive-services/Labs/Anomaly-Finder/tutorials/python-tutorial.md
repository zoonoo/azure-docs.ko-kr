---
title: '자습서: 변칙 검색, Python'
titlesuffix: Azure Cognitive Services
description: 변칙 검색 API를 사용하는 Python 노트북을 탐색합니다. 원래 데이터 요소를 API에 전송하고 예상 값과 변칙 요소를 가져옵니다.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: tutorial
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 2df1e2c261069473a02b40e4a7e45895eadea42d
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55219399"
---
# <a name="tutorial-anomaly-detection-with-python-application"></a>자습서: Python 애플리케이션을 사용하여 변칙 검색

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

이 자습서는 Python에서 변칙 검색 API를 사용하는 방법 및 인기 라이브러리를 사용하여 결과를 시각화하는 방법을 보여 줍니다. Jupyter를 사용하여 자습서를 실행하고 구독 키로 사용자 고유 데이터를 시도합니다. 대화형 Jupyter 노트북을 시작하는 방법을 알아보려면 [Jupyter 문서](http://jupyter.readthedocs.io/en/latest/index.html)를 참조하세요. 

## <a name="prerequisites"></a>필수 조건

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>변칙 검색을 구독하고 구독 키 받기 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="download-the-example-code"></a>예제 코드 다운로드

1. [GitHub에서 자습서 노트북](https://github.com/MicrosoftAnomalyDetection/python-sample)으로 이동합니다.
2. 녹색 단추를 클릭하여 자습서를 복제하거나 다운로드합니다. 

## <a name="opening-the-tutorial-notebook-in-jupyter"></a>Jupyter에서 자습서 노트북 열기

1. 명령 프롬프트를 열고 python-sample 폴더로 이동합니다.
2. 명령 프롬프트에서 Jupyter notebook 명령을 실행하여 Jupyter를 시작합니다.
3. Jupyter 창에서 <em>변칙 검색 API Example.ipynb</em>를 클릭하여 자습서 노트북을 엽니다.   

## <a name="running-the-tutorial"></a>자습서 실행

이 노트북을 사용하려면 변칙 검색 API에 대한 구독 키가 필요합니다. 등록하려면 구독 페이지를 방문합니다. “로그인” 페이지에서 Microsoft 계정을 사용하여 로그인하면 키를 구독하고 가져올 수 있습니다. 등록 프로세스를 완료한 후 해당 키를 노트북의 변수 섹션에 붙여넣습니다(아래 재현됨). 기본 키 또는 보조 키 중 하나가 작동합니다. 키를 문자열로 만들려면 따옴표로 묶어야 합니다.

```Python

            # Variables
            endpoint = 'https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection'
            subscription_key = None #Here you have to paste your primary key

```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [REST API 참조](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
