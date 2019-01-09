---
title: 의도 가져오기, Node.js
titleSuffix: Language Understanding - Azure Cognitive Services
description: 이 빠른 시작에서는 사용 가능한 공용 LUIS 앱을 통해 대화형 텍스트에서 사용자의 의도를 판단합니다. Node.js를 사용하여 공용 앱의 HTTP 예측 엔드포인트에 사용자의 의도를 텍스트로 보냅니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: 3a16c87e96a94fcf95694d3cbfd6f8f07adedb74
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53717078"
---
# <a name="quickstart-get-intent-using-nodejs"></a>빠른 시작: Node.js를 사용하여 의도 가져오기

이 빠른 시작에서는 발언을 LUIS 엔드포인트로 전달하고 의도와 엔터티를 다시 가져옵니다.

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>필수 조건

* [Node.js](https://nodejs.org/) 프로그래밍 언어 
* [Visual Studio Code](https://code.visualstudio.com/)
* 공용 앱 ID: df67dcdb-c37d-46af-88e1-8b97951ca1c2


> [!NOTE] 
> 전체 Node.js 솔루션은 [**Azure-Samples** GitHub 리포지토리](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/analyze-text/node)에서 사용할 수 있습니다.

## <a name="get-luis-key"></a>LUIS 키 가져오기

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>브라우저를 사용하여 의도 가져오기

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>프로그래밍 방식으로 의도 가져오기

Node.js를 사용하여 이전 단계의 브라우저 창에서 본 것과 동일한 결과에 액세스할 수 있습니다.

1. 다음 코드 조각을 복사합니다.

   [!code-nodejs[Console app code that calls a LUIS endpoint for Node.js](~/samples-luis/documentation-samples/quickstarts/analyze-text/node/call-endpoint.js)]

2. 다음 텍스트로 `.env` 파일을 만들거나 시스템 환경에서 해당하는 값을 설정합니다.

    ```CMD
    LUIS_APP_ID=df67dcdb-c37d-46af-88e1-8b97951ca1c2
    LUIS_ENDPOINT_KEY=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
    ```

3. `LUIS_ENDPOINT_KEY` 환경 변수를 키로 설정합니다.

4. 명령줄에서 다음 명령을 실행하여 종속성을 설치합니다. `npm install`.

5. `npm start`로 코드를 실행합니다. 브라우저 창에서 앞서 본 것과 동일한 값을 표시합니다.

## <a name="luis-keys"></a>LUIS 키

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>리소스 정리

Node.js 파일을 삭제합니다.

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [발언 추가](luis-get-started-node-add-utterance.md)
