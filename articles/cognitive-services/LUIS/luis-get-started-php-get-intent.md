---
title: PHP를 사용하여 LUIS(Language Understanding)에서 자연어 텍스트를 분석하기 위한 빠른 시작 - Cognitive Services - Azure Cognitive Services | Microsoft Docs
description: 이 빠른 시작에서는 사용 가능한 공용 LUIS 앱을 통해 대화형 텍스트에서 사용자의 의도를 판단합니다. PHP를 사용하여 공용 앱의 HTTP 예측 엔드포인트에 사용자의 의도를 텍스트로 보냅니다. 엔드포인트에서 LUIS는 공용 앱의 모델을 적용하여 자연어 텍스트의 의미를 분석하고 전반적인 의도를 판단하여 앱의 주체 도메인에 적절한 데이터를 추출합니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/23/2018
ms.author: diberry
ms.openlocfilehash: 14ac32ffc0f4332d0cbc0da59a55ccc500e216d7
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2018
ms.locfileid: "43750565"
---
# <a name="quickstart-analyze-text-using-php"></a>빠른 시작: PHP를 사용하여 텍스트 분석

[!include[Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>필수 조건

* [PHP](http://php.net/) 프로그래밍 언어
* [Visual Studio Code](https://code.visualstudio.com/)
* 공용 앱 ID: df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>LUIS 키 가져오기

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="analyze-text-with-browser"></a>브라우저로 텍스트 분석

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="analyze-text-with-php"></a>PHP로 텍스트 분석 

PHP를 사용하여 이전 단계의 브라우저 창에서 본 것과 동일한 결과에 액세스할 수 있습니다. 

1. 다음 코드를 복사하여 `endpoint-call.php` 파일에 저장합니다.

   [!code-php[PHP code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/php/endpoint-call.php)]

2. `"YOUR-KEY"`를 엔드포인트 키로 대체합니다.

3. `php endpoint-call.php`를 사용하여 PHP 응용 프로그램을 실행합니다. 브라우저 창에서 앞서 본 것과 동일한 JSON을 표시합니다.

## <a name="luis-keys"></a>LUIS 키

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>리소스 정리

PHP 파일을 삭제합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [발언 추가](luis-get-started-php-add-utterance.md)