---
title: Emotion API Python 자습서 | Microsoft Docs
description: Jupyter 노트북을 사용하여 Python으로 Cognitive Services Emotion API를 사용하는 방법을 알아봅니다. 유명 라이브러리를 사용하여 결과를 시각화합니다.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: 70c8ca48c651601f3d7cbb3717c32bfe112176fe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374094"
---
# <a name="emotion-api-using-python-tutorial"></a>Python 자습서를 사용한 Emotion API

> [!IMPORTANT]
> Video API 미리 보기는 2017년 10월 30일에 종료됩니다. 새 [Video Indexer API Preview](https://azure.microsoft.com/services/cognitive-services/video-indexer/) 미리 보기를 사용하여 비디오에서 정보를 쉽게 추출하고 말이나 얼굴, 성격, 감정을 감지하여 검색 결과를 제시하는 등 콘텐츠 검색 경험을 향상하세요. [자세히 알아보기](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Emotion API를 쉽게 시작하기 위해 아래 링크된 Jupyter 노트북이 Python에서 API를 사용하는 방법 및 인기있는 일부 라이브러리를 사용하여 결과를 시각화하는 방법을 보여줍니다. 

[GitHub에서 노트북에 연결](https://github.com/Microsoft/Cognitive-Emotion-Python/blob/master/Jupyter%20Notebook/Emotion%20Analysis%20Example.ipynb)

### <a name="using-the-jupyter-notebook"></a>Jupyter Notebook 사용

노트북을 대화형으로 사용하려면 복제하여 Jupyter에서 실행해야 합니다. 대화형 Jupyter 노트북 시작 방법을 알아보려면 http://jupyter.readthedocs.org/en/latest/install.html의 지침을 따릅니다. 

이 노트북을 사용하려면 Emotion API에 대한 구독 키가 필요합니다. 등록하려면 [구독 페이지](https://azure.microsoft.com/try/cognitive-services/)를 방문합니다. "로그인" 페이지에서 Microsoft 계정을 사용하여 로그인하면 무료 키를 구독하고 가져올 수 있습니다. 등록 프로세스를 완료한 후 사용자의 키를 아래 표시된 변수 섹션에 붙여 넣습니다. 기본 또는 보조 키 중 하나가 작동합니다.

```
Python Example 

#Variables

_url = 'https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize'
_key = None #Here you have to paste your primary key
_maxNumRetries = 10

```
