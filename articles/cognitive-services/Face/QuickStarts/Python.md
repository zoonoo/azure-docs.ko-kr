---
title: '빠른 시작: Azure REST API 및 Python을 사용하여 이미지에서 얼굴 검색'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Python과 함께 Azure Face REST API를 사용하여 이미지에서 얼굴을 검색합니다.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 11/09/2018
ms.author: pafarley
ms.openlocfilehash: d2a98226895bbe5996785ca4726f20df9b98ffdd
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51683613"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-python"></a>빠른 시작: Face REST API 및 Python을 사용하여 이미지에서 얼굴 검색

이 빠른 시작에서는 Python과 함께 Azure Face REST API를 사용하여 이미지에서 사람의 얼굴을 검색합니다. 이 스크립트는 얼굴 주위에 프레임을 그리고 이미지에 성별 및 연령 정보를 겹쳐서 표시합니다.

![얼굴 주위에 사각형이 그려져 있고 이미지에 연령 및 성별이 표시된 남자와 여자](../images/labelled-faces-python.png)

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다. 


## <a name="prerequisites"></a>필수 조건

- Face API 구독 키. [Cognitive Services 사용해보기](https://azure.microsoft.com/try/cognitive-services/?api=face-api)에서 평가판 구독 키를 가져올 수 있습니다. 또는 [Cognitive Services 계정 만들기](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)의 지침에 따라 Face API 서비스를 구독하고 키를 가져옵니다.

## <a name="run-the-jupyter-notebook"></a>Jupyter Notebook 실행

[MyBinder](https://mybinder.org)의 Jupyter Notebook으로 이 빠른 시작을 실행할 수 있습니다. Binder를 시작하려면 아래 단추를 선택합니다. Notebook의 지침을 따릅니다.

[![바인더](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=FaceAPI.ipynb)

## <a name="next-steps"></a>다음 단계

다음으로, Face API 참조 설명서를 살펴보고 지원되는 시나리오에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
