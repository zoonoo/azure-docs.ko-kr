---
title: '자습서 8: LUIS에서 핵심 구 추출'
titleSuffix: Azure Cognitive Services
description: 미리 빌드된 keyPhrase 엔터티를 사용하여 발화에서 핵심 주제를 추출합니다. 미리 빌드된 엔터티를 사용하여 발화에 레이블을 지정할 필요가 없습니다. 엔터티는 자동으로 검색됩니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 8fa183c22b9b6830c57b0a16b7f5d20ca38e3ef3
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47166523"
---
# <a name="tutorial-8-extract-key-phrases-of-utterance"></a>자습서 8: 발화의 핵심 구 추출
이 자습서에서는 미리 빌드된 keyPhrase 엔터티를 사용하여 발화에서 핵심 주제를 추출합니다. 미리 빌드된 엔터티를 사용하여 발화에 레이블을 지정할 필요가 없습니다. 엔터티는 자동으로 검색됩니다.

다음 발언에서는 주요 구문의 예제를 보여줍니다.

|발언|keyPhrase 엔터티 값|
|--|--|
|내년에 제공되는 낮은 공제 가능한 새 의료 계획이 있나요?|"낮은 공제 가능"<br>"새 의료 계획"<br>"연도"|
|높은 공제 가능한 의료 계획에서 시기능 치료를 다루나요?|"높은 공제 가능한 의료 계획"<br>"시기능 치료"|

클라이언트 응용 프로그램은 추출된 다른 엔터티와 함께 이러한 값을 사용하여 대화의 다음 단계를 결정할 수 있습니다.

**이 자습서에서 학습할 내용은 다음과 같습니다.**

<!-- green checkmark -->
> [!div class="checklist"]
> * 기존 자습서 앱 사용
> * keyPhrase 엔터티 추가 
> * 학습
> * 게시
> * 엔드포인트에서 의도 및 엔터티 가져오기

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>기존 앱 사용

마지막 자습서에서 만든 **HumanResources**라는 앱을 사용하여 계속 진행합니다. 

이전 자습서의 HumanResources 앱이 없으면 다음 단계를 사용합니다.

1.  [앱 JSON 파일](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-simple-HumanResources.json)을 다운로드하고 저장합니다.

2. JSON을 새 앱으로 가져옵니다.

3. **관리** 섹션의 **버전** 탭에서 버전을 복제하고 `keyphrase`라는 이름을 지정합니다. 복제는 원래 버전에 영향을 주지 않고도 다양한 LUIS 기능을 사용할 수 있는 좋은 방법입니다. 버전 이름이 URL 경로의 일부로 사용되므로 이름에는 URL에 유효하지 않은 문자가 포함될 수 없습니다.

## <a name="add-keyphrase-entity"></a>keyPhrase 엔터티 추가 
keyPhrase 미리 빌드된 엔터티를 추가하여 발언에서 주제 문제를 추출합니다.

1. [!include[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. 왼쪽 메뉴에서 **엔터티**를 선택합니다.

3. **미리 빌드된 엔터티 관리**를 선택합니다.

4. 팝업 대화 상자에서 **keyPhrase**를 선택한 다음, **완료**를 선택합니다. 

    [ ![엔터티 목록 팝업 대화 상자 스크린샷](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png#lightbox)

5. 왼쪽 메뉴에서 **의도**를 선택한 다음, **Utilities.Confirm** 의도를 선택합니다. keyPhrase 엔터티의 레이블이 여러 발화에서 지정되어 있습니다. 

    [ ![발화에서 keyPhrases 레이블이 지정된 Utilities.Confirm 의도의 스크린샷](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png)](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png#lightbox)

## <a name="train"></a>학습

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>게시

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>엔드포인트에서 의도 및 엔터티 가져오기

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 주소의 URL 끝으로 이동하고 `does form hrf-123456 cover the new dental benefits and medical plan`를 입력합니다. 마지막 쿼리 문자열 매개 변수는 발언 **쿼리**를 나타내는 `q`입니다. 
    
    ```JSON
    {
      "query": "does form hrf-123456 cover the new dental benefits and medical plan",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9300641
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9300641
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0359598845
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0141798034
        },
        {
          "intent": "MoveEmployee",
          "score": 0.0112197418
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.00507669244
        },
        {
          "intent": "None",
          "score": 0.00238501839
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00202810857
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00102957746
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.0008688423
        },
        {
          "intent": "Utilities.Confirm",
          "score": 3.557994E-05
        }
      ],
      "entities": [
        {
          "entity": "hrf-123456",
          "type": "HRF-number",git 
          "startIndex": 10,
          "endIndex": 19
        },
        {
          "entity": "new dental benefits",
          "type": "builtin.keyPhrase",
          "startIndex": 31,
          "endIndex": 49
        },
        {
          "entity": "medical plan",
          "type": "builtin.keyPhrase",
          "startIndex": 55,
          "endIndex": 66
        },
        {
          "entity": "hrf",
          "type": "builtin.keyPhrase",
          "startIndex": 10,
          "endIndex": 12
        },
        {
          "entity": "-123456",
          "type": "builtin.number",
          "startIndex": 13,
          "endIndex": 19,
          "resolution": {
            "value": "-123456"
          }
        }
      ]
    }
    ```

    양식을 검색하는 동안 사용자는 양식을 찾는 데 필요한 것보다 더 많은 정보를 제공했습니다. 추가 정보는 **builtin.keyPhrase**로 반환됩니다. 클라이언트 응용 프로그램에서는 "새로운 치과 혜택에 대해 인사 관리 담당자에게 말하시겠습니까?"와 같은 후속 질문에 이 추가 정보를 사용하거나, "새로운 치과 혜택 또는 의료 계획에 대한 추가 정보"를 포함한 다양한 옵션을 메뉴에 제공할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 미리 빌드된 keyPhrase 엔터티를 추가하여 발화에 레이블을 지정하지 않고도 발화의 핵심 구를 빠르게 제공했습니다. 

> [!div class="nextstepaction"]
> [앱에 감정 분석 추가](luis-quickstart-intent-and-sentiment-analysis.md)