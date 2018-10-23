---
title: '자습서: Computer Vision API Python'
titlesuffix: Azure Cognitive Services
description: Jupyter 노트북을 사용하여 Computer Vision API Python을 사용하는 방법을 알아봅니다. 유명 라이브러리를 사용하여 결과를 시각화합니다.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: tutorial
ms.date: 02/25/2017
ms.author: kefre
ms.openlocfilehash: 046250d3d2142badaac35490eff27bcac220fea9
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344900"
---
# <a name="tutorial-computer-vision-api-python"></a>자습서: Computer Vision API Python

이 자습서는 Python에서 Computer Vision API를 사용하는 방법 및 몇몇 유명 라이브러리를 이용해 결과를 시각화하는 방법을 보여줍니다. Jupyter를 사용하여 자습서를 실행합니다. 대화형 Jupyter 노트북 시작 방법을 알아보려면 [Jupyter 설명서](http://jupyter.readthedocs.io/en/latest/index.html)를 참조합니다. 

## <a name="open-the-tutorial-notebook-in-jupyter"></a>Jupyter에서 자습서 Notebook 열기 

1. [GitHub에서 자습서 노트북](https://github.com/Microsoft/Cognitive-Vision-Python)으로 이동합니다. 
2. 자습서를 복제하거나 다운로드하려면 녹색 단추를 클릭합니다. 
3. 명령 프롬프트를 열고 폴더(_Cognitive-Vision-Python-master\Jupyter Notebook_)로 이동합니다. 
4. 명령 프롬프트에서 **Jupyter 노트북** 명령을 실행합니다. 이렇게 하면 Jupyter를 시작합니다.
5. Jupyter 창에서 _Computer Vision API Example.ipynb_를 클릭해 자습서 노트북 열기 

## <a name="run-the-tutorial"></a>자습서 실행

이 노트북을 사용하려면 Computer Vision API에 대한 구독 키가 필요합니다. 등록하려면 [구독 페이지](https://azure.microsoft.com/try/cognitive-services/)를 방문합니다. "로그인" 페이지에서 Microsoft 계정을 사용하여 로그인하면 무료 키를 구독하고 가져올 수 있습니다. 등록 프로세스를 완료한 후 사용자의 키를 노트북의 변수 섹션에 붙여 넣습니다(아래 재현됨). 기본 키 또는 보조 키 중 하나가 작동합니다. 키를 문자열로 만들려면 따옴표로 묶어야 합니다.

```python
# Variables

_url = 'https://westcentralus.api.cognitive.microsoft.com/vision/v1/analyses'
_key = None #Here you have to paste your primary key
_maxNumRetries = 10
```
