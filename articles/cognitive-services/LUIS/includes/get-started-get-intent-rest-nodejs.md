---
title: Node.js에서 REST 호출을 사용하여 의도 가져오기
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: a6dfe21cd92c5bf5580d7b121f33f68fb4e135fa
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838514"
---
## <a name="prerequisites"></a>필수 조건

* [Node.js](https://nodejs.org/) 프로그래밍 언어 
* [Visual Studio Code](https://code.visualstudio.com/)
* 공용 앱 ID: df67dcdb-c37d-46af-88e1-8b97951ca1c2


> [!NOTE] 
> 전체 Node.js 솔루션은 [**Azure-Samples** GitHub 리포지토리](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/analyze-text/node)에서 사용할 수 있습니다.

## <a name="get-luis-key"></a>LUIS 키 가져오기

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

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

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작을 마치면 Visual Studio를 닫고 프로젝트 디렉터리를 파일 시스템에서 제거하세요. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Node.js로 발언 및 학습 추가](../luis-get-started-node-add-utterance.md)