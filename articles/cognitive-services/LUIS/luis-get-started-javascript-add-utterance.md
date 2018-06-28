---
title: JavaScript를 사용하여 LUIS 앱에 발화를 추가하는 방법을 알아보는 자습서 | Microsoft Docs
description: 이 자습서에서는 JavaScript를 사용하여 LUIS 앱을 호출하는 방법을 알아봅니다.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/18/2017
ms.author: v-geberr
ms.openlocfilehash: b6d021dcfdddb5449aa989c6aa06d7faf326befb
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265462"
---
# <a name="tutorial-add-utterances-to-app-using-javascript"></a>자습서: JavaScript를 사용하여 앱에 발화 추가
이 자습서에서는 Javascript에서 제작 API를 사용하여 의도에 발화를 추가하는 프로그램을 작성합니다.

<!-- green checkmark -->
> [!div class="checklist"]
> * Visual Studio 콘솔 프로젝트 만들기 
> * LUIS API를 호출하여 발화를 추가하는 메서드 추가 및 앱 학습
> * BookFlight 의도에 대한 발화 예제가 있는 JSON 파일 추가
> * 콘솔 실행 및 발화 학습 상태 확인

자세한 내용은 [의도에 발화 예제 추가](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08), [학습](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) 및 [학습 상태](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) API에 대한 기술 설명서를 참조하세요.

이 문서에서는 LUIS 앱을 작성하기 위해 체험 [LUIS][LUIS] 계정이 필요합니다.

## <a name="prerequisites"></a>필수 조건
* LUIS [**제작 키**](luis-concept-keys.md#authoring-key). 
* 기존 LUIS **응용 프로그램 ID** 및 **버전 ID**. 
* VSCode의 `add-utterances.html`이라는 새 파일

> [!NOTE] 
> 완전한 `add-utterances.html` 파일은 [**LUIS-Samples** Github 리포지토리](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/authoring-api-samples/javascript/add-utterance.html)에서 사용할 수 있습니다.


## <a name="write-the-code"></a>코드 작성
`add-utterances.html`을 만들고 다음 코드를 추가합니다.

   [!code-javascript[Java Dependencies](~/samples-luis/documentation-samples/authoring-api-samples/javascript/add-utterance.html "Java Dependencies")]

## <a name="view-in-browser"></a>브라우저에서 보기
1. 브라우저에서 파일을 엽니다.

2. LUIS 제작 ID와 LUIS 응용 프로그램 ID를 추가하고 `0.1` 버전이 아닌 경우 해당 버전을 변경합니다.

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
자습서를 완료하여 더 이상 필요하지 않은 경우 Visual Studio 및 콘솔 응용 프로그램을 제거합니다. 

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [봇과 LUIS 통합](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website