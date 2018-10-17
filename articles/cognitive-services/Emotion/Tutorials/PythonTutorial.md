---
title: '자습서: 이미지에 있는 얼굴에서 감정 인식 - Emotion API, Python'
titlesuffix: Azure Cognitive Services
description: Jupyter Notebook을 사용하여 Python으로 Emotion API를 사용하는 방법을 알아봅니다. 유명 라이브러리를 사용하여 결과를 시각화합니다.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: tutorial
ms.date: 05/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: 31e346cd9a3f43f8181ebee4474ae6c9ee2cc6fc
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237860"
---
# <a name="tutorial-use-the-emotion-api-with-a-jupyter-notebook--python"></a>자습서: Jupyter Notebook 및 Python과 함께 Emotion API를 사용합니다.

> [!IMPORTANT]
> Emotion API는 2019년 2월 15일부터 더 이상 사용되지 않습니다. 이제 감정 인식 기능은 [Face API](https://docs.microsoft.com/azure/cognitive-services/face/)의 일부로 일반 공급됩니다. 

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
