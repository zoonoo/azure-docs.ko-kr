---
title: Python 빠른 시작 - 의도 예측 - LUIS
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Python을 사용하여 LUIS 앱을 호출하는 방법을 알아봅니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 8fe39a2876d37e4897c03a5654e500e2e2d3cb9f
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51276953"
---
# <a name="quickstart-get-intent-using-python"></a>빠른 시작: Python을 사용하여 의도 가져오기
이 빠른 시작에서는 발언을 LUIS 엔드포인트로 전달하고 의도와 엔터티를 다시 가져옵니다.

[!INCLUDE[Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>필수 조건

* [Python 3.6](https://www.python.org/downloads/) 이상
* [Visual Studio Code](https://code.visualstudio.com/)

[!INCLUDE[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>LUIS 키 가져오기

[!INCLUDE[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>브라우저를 사용하여 의도 가져오기

[!INCLUDE[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent--programmatically"></a>프로그래밍 방식으로 의도 가져오기

Python을 사용하여 이전 단계의 브라우저 창에서 본 것과 동일한 결과에 액세스할 수 있습니다.

1. 다음 코드 조각 중 하나를 `quickstart-call-endpoint.py` 파일에 복사합니다.

   [!code-python[Console app code that calls a LUIS endpoint for Python 2.7](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/2.x/quickstart-call-endpoint-2-7.py)]

   [!code-python[Console app code that calls a LUIS endpoint for Python 3.6](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/3.x/quickstart-call-endpoint-3-6.py)]

2. `Ocp-Apim-Subscription-Key` 필드 값을 LUIS 엔드포인트 키로 바꿉니다.

3. `pip install requests`를 사용하여 종속성을 설치합니다.

4. `python ./quickstart-call-endpoint.py`를 사용하여 스크립트를 실행합니다. 브라우저 창에서 앞서 본 것과 동일한 JSON을 표시합니다.

## <a name="luis-keys"></a>LUIS 키

[!INCLUDE[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>리소스 정리
Python 파일을 삭제합니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [발언 추가](luis-get-started-python-add-utterance.md)