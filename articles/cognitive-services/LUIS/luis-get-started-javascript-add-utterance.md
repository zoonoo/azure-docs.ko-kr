---
title: JavaScript를 사용하여 LUIS 앱에 발언을 추가하는 방법을 알아보는 빠른 시작 - Azure Cognitive Services| Microsoft Docs
description: 이 빠른 시작에서는 JavaScript를 사용하여 LUIS 앱을 호출하는 방법을 알아봅니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/24/2018
ms.author: diberry
ms.openlocfilehash: ffc19d12c1d3fbb24c514ac87f298d1a52d23eb8
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43750593"
---
# <a name="quickstart-change-model-using-javascript"></a>빠른 시작: JavaScript를 사용하여 모델 변경

[!include[Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>필수 조건

[!include[Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* [Visual Studio Code](https://code.visualstudio.com/)

[!include[Code is available in LUIS-Samples Github repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>예제 발언 JSON 파일

[!include[Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]


## <a name="create-quickstart-code"></a>빠른 시작 코드 만들기

`add-utterances.html`을 만들고 다음 코드를 추가합니다.

   [!code-html[Html code](~/samples-luis/documentation-samples/quickstarts/change-model/javascript/add-utterance.html "Javascript code")]

## <a name="run-code"></a>코드 실행

1. 브라우저에서 파일을 엽니다.

2. LUIS 작성 ID, LUIS 응용 프로그램 ID를 추가합니다.

3. 응용 프로그램에 추가할 **발화 배열**을 수정합니다. 이러한 발화는 utteranceJSON 변수에 저장됩니다. 사용자 고유의 도메인 및 발화 요구 사항에 맞게 이러한 값을 변경합니다. 

    ```json
    // example batch utterances
    var utteranceJSON = [
        {
            "text": "go to Seattle",
            "intentName": "BookFlight",
            "entityLabels": [
                {
                    "entityName": "Location::LocationTo",
                    "startCharIndex": 6,
                    "endCharIndex": 12
                }
            ]
        }
    ,
        {
            "text": "book a flight",
            "intentName": "BookFlight",
            "entityLabels": []
        }
    ];
    ```

4. `Upload utterance` 단추를 선택합니다. LUIS 결과가 단추 아래에 표시됩니다.

5. 이러한 새 발화를 사용하여 응용 프로그램을 학습시키려면 `Train model` 단추를 선택합니다.

6. 학습 상태를 확인하려면 `Train Status` 단추를 선택합니다. 

    ![Add-utterances.html](./media/luis-quickstart-javascript-add-utterance/add-utterance.png)

## <a name="clean-up-resources"></a>리소스 정리
빠른 시작을 완료하면 이 빠른 시작에서 생성된 모든 파일을 제거하세요. 

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [봇과 LUIS 통합](luis-csharp-tutorial-build-bot-framework-sample.md)
