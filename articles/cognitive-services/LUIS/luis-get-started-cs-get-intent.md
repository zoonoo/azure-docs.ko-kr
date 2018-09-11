---
title: C#을 사용하여 LUIS(Language Understanding)에서 자연어 텍스트 분석 - Azure Cognitive Services | Microsoft Docs
description: 이 빠른 시작에서는 사용 가능한 공용 LUIS 앱을 통해 대화형 텍스트에서 사용자의 의도를 판단합니다. C#을 사용하여 공용 앱의 HTTP 예측 엔드포인트에 사용자의 의도를 텍스트로 보냅니다. 엔드포인트에서 LUIS는 공용 앱의 모델을 적용하여 자연어 텍스트의 의미를 분석하고 전반적인 의도를 판단하여 앱의 주체 도메인에 적절한 데이터를 추출합니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/23/2018
ms.author: diberry
ms.openlocfilehash: 676546a215bbb8964f1cb2d26ae0fb9fd2ed9289
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2018
ms.locfileid: "43750560"
---
# <a name="quickstart-analyze-text-using-c"></a>빠른 시작: C#을 사용하여 텍스트 분석

[!include[Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>필수 조건

* [Visual Studio Community 2017 버전(Edition)](https://visualstudio.microsoft.com/vs/community/)
* C# 프로그래밍 언어(VS Community 2017에 포함됨)
* 공용 앱 ID: df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>LUIS 키 가져오기

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="analyze-text-with-browser"></a>브라우저로 텍스트 분석

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="analyze-text-with-c"></a>C#으로 텍스트 분석 

C#을 사용하여 예측 엔드포인트 GET [API](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)를 쿼리하여 이전 섹션의 브라우저 창에 표시된 것과 동일한 결과를 냅니다. 

1. Visual Studio에서 새 콘솔 응용 프로그램을 만듭니다. 

    ![LUIS 사용자 설정 메뉴 액세스](media/luis-get-started-cs-get-intent/visual-studio-console-app.png)

2. Visual Studio 프로젝트의 솔루션 탐색기에서 **참조 추가**를 선택한 다음, 어셈블리 탭에서 **System.Web**을 선택합니다.

    ![LUIS 사용자 설정 메뉴 액세스](media/luis-get-started-cs-get-intent/add-system-dot-web-to-project.png)

3. Program.cs를 다음 코드로 덮어씁니다.
    
   [!code-csharp[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/csharp/Program.cs)]

4. `YOUR_KEY` 값을 LUIS 키로 대체합니다.

5. 콘솔 응용 프로그램을 작성하고 실행합니다. 브라우저 창에서 앞서 본 것과 동일한 JSON을 표시합니다.

    ![콘솔 창에서는 LUIS의 JSON 결과를 표시합니다.](./media/luis-get-started-cs-get-intent/console-turn-on.png)

## <a name="luis-keys"></a>LUIS 키

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작을 마치면 Visual Studio를 닫고 프로젝트 디렉터리를 파일 시스템에서 제거하세요. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [C#으로 발언 및 학습 추가](luis-get-started-cs-add-utterance.md)