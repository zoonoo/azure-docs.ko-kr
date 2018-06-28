---
title: 주요 구문을 반환하는 LUIS 앱을 만드는 자습서 - Azure | Microsoft Docs
description: 이 자습서에서는 LUIS 앱에 keyPhrase 엔터티를 추가하고 반환하여 주요 주제 문제에 대한 발언을 분석하는 방법을 알아봅니다.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 12c306b5199da5862302c28d1690b81c6e1edb0e
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264618"
---
# <a name="tutorial-create-app-that-returns-keyphrases-entity-data-found-in-utterances"></a>자습서: 발언에서 찾은 keyPhrase 엔터티 데이터를 반환하는 앱 만들기
이 자습서에서는 발언에서 주요 주제 문제를 추출하는 방법을 보여주는 앱을 만듭니다.

<!-- green checkmark -->
> [!div class="checklist"]
> * keyPhrase 엔터티 이해 
> * 인적 자원 도메인에 대한 새 LUIS 앱 만들기
> * _없음_ 의도 추가 및 예제 발언 추가
> * keyPhrase 엔터티를 추가하여 발언에서 콘텐츠 추출
> * 앱 학습 및 게시
> * 앱의 엔드포인트를 쿼리하여 LUIS JSON 응답 확인

이 아티클에서는 LUIS 앱을 작성하기 위해 체험 [LUIS][LUIS] 계정을 사용할 수 있습니다.

## <a name="keyphrase-entity-extraction"></a>keyPhrase 엔터티 추출
미리 빌드된 엔터티(**keyPhrase**)에서 주요 주제 문제를 제공합니다. 이 엔터티는 발언의 주요 주제 문제를 반환합니다.

다음 발언에서는 주요 구문의 예제를 보여줍니다.

|발언|keyPhrase 엔터티 값|
|--|--|
|내년에 제공되는 낮은 공제 가능한 새 의료 계획이 있나요?|"낮은 공제 가능"<br>"새 의료 계획"<br>"연도"|
|높은 공제 가능한 의료 계획에서 시기능 치료를 다루나요?|"높은 공제 가능한 의료 계획"<br>"시기능 치료"|

챗봇은 대화에서 다음 단계를 결정할 경우 추출된 다른 모든 엔터티 외에도 이러한 값을 사용할 수 있습니다.

## <a name="download-sample-app"></a>샘플 앱 다운로드
[인적 자원](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/HumanResources.json) 앱을 다운로드하고 *.json 확장명을 가진 파일에 저장합니다. 이 샘플 앱은 직원 복리 후생, 조직 차트 및 실제 자산과 관련된 발언을 인식합니다.

## <a name="create-a-new-app"></a>새 앱 만들기
1. [LUIS][LUIS] 웹 사이트에 로그인합니다. LUIS 엔드포인트를 게시해야 하는 [지역][LUIS-regions]에 로그인해야 합니다.

2. [LUIS][ LUIS] 웹 사이트에서 **새 앱 가져오기**를 선택하여 이전 섹션에서 다운로드한 인사 관리 앱을 가져옵니다. 

    [![](media/luis-quickstart-intent-and-key-phrase/app-list.png "앱 목록 페이지 스크린샷")](media/luis-quickstart-intent-and-key-phrase/app-list.png#lightbox)

3. **새 앱 가져오기** 대화 상자에서 앱 이름 `Human Resources with Key Phrase entity`를 지정합니다. 

    ![새 앱 대화 상자 만들기 이미지](./media/luis-quickstart-intent-and-key-phrase/import-new-app-inline.png)

    앱 생성 프로세스가 완료되면 LUIS에서는 의도 목록을 표시합니다.

    [![](media/luis-quickstart-intent-and-key-phrase/intents-list.png "의도 목록 페이지 스크린샷")](media/luis-quickstart-intent-and-key-phrase/intents-list.png#lightbox)

## <a name="add-keyphrase-entity"></a>keyPhrase 엔터티 추가 
keyPhrase 미리 빌드된 엔터티를 추가하여 발언에서 주제 문제를 추출합니다.

1. 왼쪽 메뉴에서 **엔터티**를 선택합니다.

    [ ![빌드 섹션의 왼쪽 탐색 창에서 강조 표시된 엔터티 스크린샷](./media/luis-quickstart-intent-and-key-phrase/select-entities.png)](./media/luis-quickstart-intent-and-key-phrase/select-entities.png#lightbox)

2. **미리 빌드된 엔터티 관리**를 선택합니다.

    [ ![엔터티 목록 팝업 대화 상자 스크린샷](./media/luis-quickstart-intent-and-key-phrase/manage-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/manage-prebuilt-entities.png#lightbox)

3. 팝업 대화 상자에서 **keyPhrase**를 선택한 다음, **완료**를 선택합니다. 

    [ ![엔터티 목록 팝업 대화 상자 스크린샷](./media/luis-quickstart-intent-and-key-phrase/add-or-remove-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/add-or-remove-prebuilt-entities.png#lightbox)

    <!-- TBD: asking Carol
    You won't see these entities labeled in utterances on the intents pages. 
    -->

## <a name="train-the-luis-app"></a>LUIS 앱 학습
LUIS는 학습할 때까지 모델에 대한 변경 내용을 알지 못합니다. 

1. LUIS 웹 사이트의 오른쪽 위에서 **학습** 단추를 선택합니다.

    ![학습 단추가 강조 표시된 스크린샷](./media/luis-quickstart-intent-and-key-phrase/train-button-expanded.png)

2. 웹 사이트의 위쪽에 성공이 확인된 녹색 상태 표시줄이 표시되면 학습이 완료됩니다.

    ![학습 성공 알림 표시줄 스크린샷 ](./media/luis-quickstart-intent-and-key-phrase/trained-inline.png)

## <a name="publish-app-to-endpoint"></a>엔드포인트에 앱 게시

1. 오른쪽 맨 위 탐색 창에서 **게시**를 선택합니다.

    ![게시 단추가 확장된 엔터티 페이지 스크린샷 ](./media/luis-quickstart-intent-and-key-phrase/publish-expanded.png)

2. 프로덕션 슬롯과 **게시** 단추를 선택합니다.

    [![](media/luis-quickstart-intent-and-key-phrase/publish-to-production-inline.png "프로덕션 슬롯에 게시 단추가 강조 표시된 게시 페이지의 스크린샷")](media/luis-quickstart-intent-and-key-phrase/publish-to-production-expanded.png#lightbox)

3. 웹 사이트의 위쪽에 성공이 확인된 녹색 상태 표시줄이 표시되면 게시가 완료됩니다.

## <a name="query-the-endpoint-with-an-utterance"></a>발언을 사용하여 엔드포인트 쿼리

1. **게시** 페이지의 아래쪽에서 **엔드포인트** 링크를 선택합니다. 그러면 주소 표시줄에 엔드포인트 URL이 있는 다른 브라우저 창이 열립니다. 

    ![엔드포인트 URL이 강조 표시된 게시 페이지 스크린샷](media/luis-quickstart-intent-and-key-phrase/endpoint-url-inline.png )

2. 주소의 URL 끝으로 이동하고 `Is there a new medical plan with a lower deductible offered next year?`를 입력합니다. 마지막 쿼리 문자열 매개 변수는 발언 **쿼리**를 나타내는 `q`입니다. 

```
{
  "query": "Is there a new medical plan with a lower deductible offered next year?",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.216838628
  },
  "entities": [
    {
      "entity": "lower deductible",
      "type": "builtin.keyPhrase",
      "startIndex": 35,
      "endIndex": 50
    },
    {
      "entity": "new medical plan",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 26
    },
    {
      "entity": "year",
      "type": "builtin.keyPhrase",
      "startIndex": 65,
      "endIndex": 68
    }
  ]
}
```

## <a name="what-has-this-luis-app-accomplished"></a>이 LUIS 앱에서 수행한 작업은?
keyPhrase 엔터티 검색이 포함된 이 앱은 자연어 쿼리 의도를 식별하고 기본 주제 문제를 비롯한 추출된 데이터를 반환했습니다. 

이제 챗봇에는 대화의 다음 단계를 확인할 충분한 정보가 있습니다. 

## <a name="where-is-this-luis-data-used"></a>이 LUIS 데이터가 사용되는 위치는? 
LUIS는 이 요청을 통해 수행됩니다. 챗봇과 같은 호출 응용 프로그램은 발언에서 topScoringIntent 결과 및 keyPhrase 데이터를 사용하여 다음 단계를 진행할 수 있습니다. LUIS는 봇 또는 호출 응용 프로그램에 대해 프로그래밍 방식 작업을 수행하지 않습니다. LUIS는 사용자의 의도가 무엇인지만 결정합니다. 

## <a name="clean-up-resources"></a>리소스 정리
더 이상 필요하지 않은 경우 LUIS 앱을 삭제합니다. 이렇게 하려면 앱 목록에서 앱 이름 오른쪽에 있는 3개 점 메뉴(...)를 선택하고 **삭제**를 선택합니다. **앱을 삭제하시겠습니까?** 팝업 대화 상자에서 **확인**을 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [의도 예측과 함께 감정을 반환하는 앱 만들기](luis-quickstart-intent-and-sentiment-analysis.md)

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
