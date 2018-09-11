---
title: Node.js를 사용하여 LUIS(Language Understanding)에서 자연어 텍스트 분석 - Cognitive Services - Azure Cognitive Services | Microsoft Docs
description: 이 빠른 시작에서는 사용 가능한 공용 LUIS 앱을 통해 대화형 텍스트에서 사용자의 의도를 판단합니다. Node.js를 사용하여 공용 앱의 HTTP 예측 엔드포인트에 사용자의 의도를 텍스트로 보냅니다. 엔드포인트에서 LUIS는 공용 앱의 모델을 적용하여 자연어 텍스트의 의미를 분석하고 전반적인 의도를 판단하여 앱의 주체 도메인에 적절한 데이터를 추출합니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/23/2018
ms.author: diberry
ms.openlocfilehash: d7067041ae8e413675de3c95ca4ba0c1b987f47a
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2018
ms.locfileid: "43750566"
---
# <a name="quickstart-analyze-text-using-nodejs"></a>빠른 시작: Node.js를 사용하여 텍스트 분석

[!include[Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>필수 조건

* [Node.js](https://nodejs.org/) 프로그래밍 언어 
* [Visual Studio Code](https://code.visualstudio.com/)
* 공용 앱 ID: df67dcdb-c37d-46af-88e1-8b97951ca1c2


> [!NOTE] 
> 전체 Node.js 솔루션은 [**LUIS-Samples** Github 리포지토리](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/analyze-text/node)에서 사용할 수 있습니다.

## <a name="get-luis-key"></a>LUIS 키 가져오기

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="analyze-text-with-browser"></a>브라우저로 텍스트 분석

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="analyze-text-with-nodejs"></a>Node.js로 텍스트 분석

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

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>리소스 정리

Node.js 파일을 삭제합니다.

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [발언 추가](luis-get-started-node-add-utterance.md)