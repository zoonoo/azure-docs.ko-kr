---
title: 주요 구문을 반환하는 LUIS 앱을 만드는 자습서 - Azure | Microsoft Docs
description: 이 자습서에서는 LUIS 앱에 keyPhrase 엔터티를 추가하고 반환하여 주요 주제 문제에 대한 발언을 분석하는 방법을 알아봅니다.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/27/2018
ms.author: v-geberr
ms.openlocfilehash: f3808620c4527f2971d8eb6d53a09c893b162b59
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2018
ms.locfileid: "37340953"
---
# <a name="tutorial-7-add-keyphrase-entity"></a>자습서: 7. keyPhrase 엔터티 추가 
이 자습서에서는 발화에서 핵심 주제를 추출하는 방법을 보여 주는 앱을 사용합니다.

<!-- green checkmark -->
> [!div class="checklist"]
> * keyPhrase 엔터티 이해 
> * HR(인사 관리) 도메인에서 LUIS 앱 사용 
> * keyPhrase 엔터티를 추가하여 발언에서 콘텐츠 추출
> * 앱 학습 및 게시
> * 앱의 엔드포인트를 쿼리하여 핵심 구가 포함된 LUIS JSON 응답 확인

이 문서에서는 LUIS 응용 프로그램을 작성하기 위해 체험 [LUIS](luis-reference-regions.md#publishing-regions) 계정을 사용할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에
[단순 엔터티](luis-quickstart-primary-and-secondary-data.md) 자습서의 인사 관리 앱이 없으면 JSON을 [LUIS](luis-reference-regions.md#luis-website) 웹 사이트의 새 앱으로 [가져옵니다](create-new-app.md#import-new-app). 가져올 앱은 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-simple-HumanResources.json) Github 리포지토리에 있습니다.

원래의 인사 관리 앱을 유지하려면 [설정](luis-how-to-manage-versions.md#clone-a-version) 페이지에서 버전을 복제하고 해당 이름을 `keyphrase`로 지정합니다. 복제는 원래 버전에 영향을 주지 않고도 다양한 LUIS 기능을 사용할 수 있는 좋은 방법입니다. 

## <a name="keyphrase-entity-extraction"></a>keyPhrase 엔터티 추출
미리 작성된 **keyPhrase** 엔터티에서 핵심 주제를 제공합니다. 이 엔터티는 발화의 핵심 주제를 반환합니다.

다음 발언에서는 주요 구문의 예제를 보여줍니다.

|발언|keyPhrase 엔터티 값|
|--|--|
|내년에 제공되는 낮은 공제 가능한 새 의료 계획이 있나요?|"낮은 공제 가능"<br>"새 의료 계획"<br>"연도"|
|높은 공제 가능한 의료 계획에서 시기능 치료를 다루나요?|"높은 공제 가능한 의료 계획"<br>"시기능 치료"|

클라이언트 응용 프로그램은 추출된 다른 엔터티와 함께 이러한 값을 사용하여 대화의 다음 단계를 결정할 수 있습니다.

## <a name="add-keyphrase-entity"></a>keyPhrase 엔터티 추가 
keyPhrase 미리 빌드된 엔터티를 추가하여 발언에서 주제 문제를 추출합니다.

1. 인사 관리 앱이 LUIS의 **빌드** 섹션에 있는지 확인합니다. 오른쪽 위의 메뉴 표시줄에서 **빌드**를 선택하여 이 섹션으로 변경할 수 있습니다. 

    [ ![오른쪽 위의 탐색 모음에서 강조 표시된 빌드가 있는 LUIS 앱의 스크린샷](./media/luis-quickstart-intent-and-key-phrase/hr-first-image.png)](./media/luis-quickstart-intent-and-key-phrase/hr-first-image.png#lightbox)

2. 왼쪽 메뉴에서 **엔터티**를 선택합니다.

    [ ![빌드 섹션의 왼쪽 탐색 창에서 강조 표시된 엔터티 스크린샷](./media/luis-quickstart-intent-and-key-phrase/hr-select-entities-button.png)](./media/luis-quickstart-intent-and-key-phrase/hr-select-entities-button.png#lightbox)

3. **미리 빌드된 엔터티 관리**를 선택합니다.

    [ ![엔터티 목록 팝업 대화 상자 스크린샷](./media/luis-quickstart-intent-and-key-phrase/hr-manage-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/hr-manage-prebuilt-entities.png#lightbox)

4. 팝업 대화 상자에서 **keyPhrase**를 선택한 다음, **완료**를 선택합니다. 

    [ ![엔터티 목록 팝업 대화 상자 스크린샷](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png#lightbox)

    <!-- TBD: asking Carol
    You won't see these entities labeled in utterances on the intents pages. 
    -->
5. 왼쪽 메뉴에서 **의도**를 선택한 다음, **Utilities.Confirm** 의도를 선택합니다. keyPhrase 엔터티의 레이블이 여러 발화에서 지정되어 있습니다. 

    [ ![발화에서 keyPhrases 레이블이 지정된 Utilities.Confirm 의도의 스크린샷](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png)](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png#lightbox)

## <a name="train-the-luis-app"></a>LUIS 앱 학습
새 `keyphrase` 버전의 앱을 학습해야 합니다.  

1. LUIS 웹 사이트의 오른쪽 위에서 **학습** 단추를 선택합니다.

    ![앱 학습](./media/luis-quickstart-intent-and-key-phrase/train-button.png)

2. 웹 사이트의 위쪽에 성공이 확인된 녹색 상태 표시줄이 표시되면 학습이 완료됩니다.

    ![학습 성공](./media/luis-quickstart-intent-and-key-phrase/trained.png)

## <a name="publish-app-to-endpoint"></a>엔드포인트에 앱 게시

1. 오른쪽 맨 위 탐색 창에서 **게시**를 선택합니다.

    [![](media/luis-quickstart-intent-and-key-phrase/hr-publish-button-top-nav.png "프로덕션 슬롯에 게시 단추가 강조 표시된 게시 페이지의 스크린샷")](media/luis-quickstart-intent-and-key-phrase/hr-publish-button-top-nav.png#lightbox)

2. 프로덕션 슬롯과 **게시** 단추를 선택합니다.

    [![](media/luis-quickstart-intent-and-key-phrase/hr-publish-to-production-expanded.png "프로덕션 슬롯에 게시 단추가 강조 표시된 게시 페이지의 스크린샷")](media/luis-quickstart-intent-and-key-phrase/hr-publish-to-production-expanded.png#lightbox)

3. 웹 사이트의 위쪽에 성공이 확인된 녹색 상태 표시줄이 표시되면 게시가 완료됩니다.

## <a name="query-the-endpoint-with-an-utterance"></a>발언을 사용하여 엔드포인트 쿼리

1. **게시** 페이지의 아래쪽에서 **엔드포인트** 링크를 선택합니다. 그러면 주소 표시줄에 엔드포인트 URL이 있는 다른 브라우저 창이 열립니다. 

    ![엔드포인트 URL이 강조 표시된 게시 페이지 스크린샷](media/luis-quickstart-intent-and-key-phrase/hr-endpoint-url-inline.png )

2. 주소의 URL 끝으로 이동하고 `does form hrf-123456 cover the new dental benefits and medical plan`를 입력합니다. 마지막 쿼리 문자열 매개 변수는 발언 **쿼리**를 나타내는 `q`입니다. 

```
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

## <a name="what-has-this-luis-app-accomplished"></a>이 LUIS 앱에서 수행한 작업은?
keyPhrase 엔터티 검색이 포함된 이 앱은 자연어 쿼리 의도를 식별하고 기본 주제 문제를 비롯한 추출된 데이터를 반환했습니다. 

이제 챗봇에는 대화의 다음 단계를 확인할 충분한 정보가 있습니다. 

## <a name="where-is-this-luis-data-used"></a>이 LUIS 데이터가 사용되는 위치는? 
LUIS는 이 요청을 통해 수행됩니다. 챗봇과 같은 호출 응용 프로그램은 발언에서 topScoringIntent 결과 및 keyPhrase 데이터를 사용하여 다음 단계를 진행할 수 있습니다. LUIS는 봇 또는 호출 응용 프로그램에 대해 프로그래밍 방식 작업을 수행하지 않습니다. LUIS는 사용자의 의도가 무엇인지만 결정합니다. 

## <a name="clean-up-resources"></a>리소스 정리
더 이상 필요하지 않은 경우 LUIS 앱을 삭제합니다. 왼쪽 위 메뉴에서 **내 앱**을 선택합니다. 앱 목록에서 앱 이름 오른쪽에 있는 3개 점 메뉴(...)를 선택하고 **삭제**를 선택합니다. **앱을 삭제하시겠습니까?** 팝업 대화 상자에서 **확인**을 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱에 감정 분석 추가](luis-quickstart-intent-and-sentiment-analysis.md)

