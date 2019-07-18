---
title: 이미지 작업 수행 - Python
titlesuffix: Azure Cognitive Services
description: Jupyter 노트북을 사용하여 Computer Vision API Python을 사용하는 방법을 알아봅니다. 유명 라이브러리를 사용하여 결과를 시각화합니다.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: b18f41db772c1c214bdf48c9bb765b1ce7e6d2c2
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604152"
---
# <a name="computer-vision-api-jupyter-notebook"></a>컴퓨터 비전 API Jupyter notebook

이 가이드에서는 Python에서 Computer Vision API를 사용 하는 방법 및 인기 있는 라이브러리를 사용 하 여 결과 시각화 하는 방법을 알아봅니다. Jupyter를 사용하여 자습서를 실행할 것입니다. 대화형 Jupyter Notebook을 시작하는 방법을 알아보려면 [Jupyter 설명서](https://jupyter.readthedocs.io/en/latest/index.html)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

- [Python 2.7+ 또는 3.5+](https://www.python.org/downloads/)
- [pip](https://pip.pypa.io/en/stable/installing/) 도구
- [Jupyter Notebook](https://jupyter.org/install) 설치

## <a name="open-the-notebook-in-jupyter"></a>Jupyter에서 notebook을 열려면 

1. [Cognitive Vision Python](https://github.com/Microsoft/Cognitive-Vision-Python) GitHub 리포지토리로 이동합니다. 
2. 녹색 단추를 클릭하여 리포지토리를 복제하거나 다운로드합니다. 
3. 명령 프롬프트를 열고 **Cognitive-Vision-Python\Jupyter Notebook** 폴더로 이동합니다.
1. 명령 프롬프트에서 `pip install requests opencv-python numpy matplotlib` 명령을 실행하여 필요한 라이브러리가 모두 있는지 확인합니다.
1. 명령 프롬프트에서 `jupyter notebook` 명령을 실행하여 Jupyter를 시작합니다.
1. Jupyter 창에서 _Computer Vision API Example.ipynb_를 클릭하여 자습서 Notebook을 엽니다.

## <a name="run-the-notebook"></a>노트북 실행

이 노트북을 사용하려면 Computer Vision API에 대한 구독 키가 필요합니다. 등록하려면 [구독 페이지](https://azure.microsoft.com/try/cognitive-services/)를 방문합니다. **로그인** 페이지에서 Microsoft 계정을 사용하여 로그인하면 무료 키를 구독하고 가져올 수 있습니다. 등록 프로세스를 완료한 후에는 키를 Notebook의 `Variables` 섹션에 붙여넣습니다(아래에 재현되어 있음). 기본 키 또는 보조 키 중 하나가 작동합니다. 키를 문자열로 만들려면 따옴표로 묶어야 합니다.

또한 `_region` 필드가 구독에 해당하는 지역과 일치하는지 확인해야 합니다.

```python
# Variables
_region = 'westcentralus'  # Here you enter the region of your subscription
_url = 'https://{}.api.cognitive.microsoft.com/vision/v2.0/analyze'.format(
    _region)
_key = None  # Here you have to paste your primary key
_maxNumRetries = 10
```

자습서를 실행하면 URL 및 로컬 저장소에서 분석할 이미지를 추가할 수 있습니다. 스크립트는 Notebook에 이미지 및 분석 정보를 표시합니다.
