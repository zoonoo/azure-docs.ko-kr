---
title: 의도 가져오기, C#
titleSuffix: Language Understanding - Azure Cognitive Services
description: 이 C# 빠른 시작에서는 사용 가능한 공용 LUIS 앱을 통해 대화형 텍스트에서 사용자의 의도를 판단합니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 0e58fb35fa29cde16b1ccf50dc20dacc693a4757
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53141767"
---
# <a name="quickstart-get-intent-using-c"></a>빠른 시작: C#을 사용하여 의도 가져오기

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>필수 조건

* [Visual Studio Community 2017 버전(Edition)](https://visualstudio.microsoft.com/vs/community/)
* C# 프로그래밍 언어(VS Community 2017에 포함됨)
* 공용 앱 ID: df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>LUIS 키 가져오기

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>브라우저를 사용하여 의도 가져오기

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>프로그래밍 방식으로 의도 가져오기

C#을 사용하여 예측 엔드포인트 GET [API](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)를 쿼리하여 이전 섹션의 브라우저 창에 표시된 것과 동일한 결과를 냅니다. 

1. Visual Studio에서 새 콘솔 애플리케이션을 만듭니다. 

    ![Visual Studio에서 새 콘솔 애플리케이션 만들기](media/luis-get-started-cs-get-intent/visual-studio-console-app.png)

2. Visual Studio 프로젝트의 솔루션 탐색기에서 **참조 추가**를 선택한 다음, 어셈블리 탭에서 **System.Web**을 선택합니다.

    ![참조 추가를 선택한 다음, 어셈블리 탭에서 System.Web 선택](media/luis-get-started-cs-get-intent/add-system-dot-web-to-project.png)

3. Program.cs를 다음 코드로 덮어씁니다.
    
   [!code-csharp[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/csharp/Program.cs)]

4. `YOUR_KEY` 값을 LUIS 키로 대체합니다.

5. 콘솔 응용 프로그램을 작성하고 실행합니다. 브라우저 창에서 앞서 본 것과 동일한 JSON을 표시합니다.

    ![콘솔 창에서는 LUIS의 JSON 결과를 표시합니다.](./media/luis-get-started-cs-get-intent/console-turn-on.png)

## <a name="luis-keys"></a>LUIS 키

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작을 마치면 Visual Studio를 닫고 프로젝트 디렉터리를 파일 시스템에서 제거하세요. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [C#으로 발언 및 학습 추가](luis-get-started-cs-add-utterance.md)