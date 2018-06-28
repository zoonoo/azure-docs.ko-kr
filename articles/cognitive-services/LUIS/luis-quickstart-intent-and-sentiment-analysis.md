---
title: 감정 분석을 반환하는 LUIS 앱을 만드는 자습서 - Azure | Microsoft Docs
description: 이 자습서에서 감정 분석을 LUIS 앱에 추가하여 긍정, 부정 및 중립 감정에 대한 발언을 분석하는 방법을 설명합니다.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: d000637312619fc493e2f7bad8e8edf0d8d0d94b
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265337"
---
# <a name="tutorial-create-app-that-returns-sentiment-along-with-intent-prediction"></a>자습서: 의도 예측과 함께 감정을 반환하는 앱 만들기
이 자습서에서는 긍정, 부정 및 중립 감정을 발언에서 추출하는 방법을 보여주는 앱을 만듭니다.

<!-- green checkmark -->
> [!div class="checklist"]
> * 계층적 엔터티 및 컨텍스트 상 얻은 자식 이해 
> * Bookflight 의도를 사용하여 여행 도메인에 대한 새 LUIS 앱 만들기
> * _없음_ 의도 추가 및 예제 발언 추가
> * 원본 및 대상 자식이 있는 위치 계층적 엔터티 추가
> * 앱 학습 및 게시
> * 계층적 자식을 비롯한 LUIS JSON 응답을 보기 위해 앱의 엔드포인트 쿼리 

이 아티클에서는 LUIS 앱을 작성하기 위해 체험 [LUIS][LUIS] 계정이 필요합니다.

## <a name="sentiment-analysis"></a>정서 분석
감정 분석은 사용자의 발언이 긍정, 부정 또는 중립인지를 확인하는 기능입니다. 

다음 발언에서는 감정의 예제를 보여줍니다.

|감정 및 점수|발언|
|:--|--|
|긍정 - 0.89 |스프와 샐러드 콤보는 훌륭했습니다.|
|부정 - 0.07 |저녁 식사 중에 애피타이저가 별로였습니다.|

감정 분석은 모든 발언에 적용되는 앱 설정입니다. 발언에서 감정을 나타내는 단어를 찾고 레이블을 지정하지 않아도 됩니다. LUIS가 이 작업을 수행합니다.

## <a name="create-a-new-app"></a>새 앱 만들기
1. [LUIS][LUIS] 웹 사이트에 로그인합니다. LUIS 엔드포인트를 게시해야 하는 [지역][LUIS-regions]에 로그인해야 합니다.

2. [LUIS][LUIS] 웹 사이트에서 **새 앱 만들기**를 선택합니다. 

    [![](media/luis-quickstart-intent-and-sentiment-analysis/app-list.png "앱 목록 페이지 스크린샷")](media/luis-quickstart-intent-and-sentiment-analysis/app-list.png#lightbox)

3. **새 앱 만들기** 대화 상자에서 앱의 이름 `Restaurant Reservations With Sentiment`를 지정하고 **완료**를 선택합니다. 

    ![새 앱 대화 상자 만들기 이미지](./media/luis-quickstart-intent-and-sentiment-analysis/create-app-ddl.png)

    앱 생성 프로세스가 완료되면 LUIS에서는 없음 의도를 포함한 의도 목록을 표시합니다.

    [![](media/luis-quickstart-intent-and-sentiment-analysis/intents-list.png "의도 목록 페이지 스크린샷")](media/luis-quickstart-intent-and-sentiment-analysis/intents-list.png#lightbox)

## <a name="add-a-prebuilt-domain"></a>미리 빌드된 도메인 추가
의도, 엔터티 및 레이블이 지정된 발언을 신속하게 추가하려면 미리 빌드된 도메인을 추가합니다.

1. 왼쪽 메뉴에서 **미리 빌드된 도메인**을 선택합니다.

    [ ![미리 빌드된 도메인 단추 스크린샷](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-button-inline.png)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-button-expanded.png#lightbox)

2. **RestaurantReservation** 미리 빌드된 도메인에서 **도메인 추가**를 선택합니다. 도메인이 추가될 때까지 대기합니다.

    [ ![미리 빌드된 도메인 목록 스크린샷](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-inline.png)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-expanded.png#lightbox)

3. 왼쪽 탐색 영역에서 **의도**를 선택합니다. 이 미리 빌드된 도메인에는 하나의 의도가 포함됩니다.

    [ ![왼쪽 탐색 창에서 의도가 강조 표시된 미리 빌드된 도메인 목록 스크린샷](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-domain-added-expanded.png)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-domain-added-expanded.png#lightbox)

4.  **RestaurantReservation.Reserve** 의도를 선택합니다. 

    [ ![RestaurantReservation.Reserve가 강조 표시된 의도 목록 스크린샷](./media/luis-quickstart-intent-and-sentiment-analysis/select-intent.png)](./media/luis-quickstart-intent-and-sentiment-analysis/select-intent.png#lightbox)

5. **엔터티 보기**를 설정/해제하여 도메인 특정 엔터티의 레이블이 지정되어 제공된 많은 발언을 확인합니다.

    [ ![엔터티 보기가 강조 표시된 토큰 보기로 전환된 RestaurantReservation.Reserve 의도 스크린샷](./media/luis-quickstart-intent-and-sentiment-analysis/utterance-list-inline.png)](./media/luis-quickstart-intent-and-sentiment-analysis/utterance-list-expanded.png#lightbox)

## <a name="train-the-luis-app"></a>LUIS 앱 학습
LUIS는 학습될 때까지 의도와 엔터티(모델)에 대한 변경 내용을 인식하지 못합니다. 

1. LUIS 웹 사이트의 오른쪽 위에서 **학습** 단추를 선택합니다.

    ![학습 단추가 강조 표시된 스크린샷](./media/luis-quickstart-intent-and-sentiment-analysis/train-button-expanded.png)

2. 웹 사이트의 위쪽에 성공이 확인된 녹색 상태 표시줄이 표시되면 학습이 완료됩니다.

    ![학습 성공 알림 표시줄 스크린샷 ](./media/luis-quickstart-intent-and-sentiment-analysis/trained-expanded.png)

## <a name="configure-app-to-include-sentiment-analysis"></a>감정 분석을 포함하도록 앱 구성
감정 분석은 **게시** 페이지에서 사용됩니다. 

1. 오른쪽 맨 위 탐색 창에서 **게시**를 선택합니다.

    ![게시 단추가 확장된 의도 페이지 스크린샷 ](./media/luis-quickstart-intent-and-sentiment-analysis/publish-expanded.png)

2. **감정 분석 사용**을 선택합니다.

    ![감정 분석 사용이 강조 표시된 게시 페이지 스크린샷 ](./media/luis-quickstart-intent-and-sentiment-analysis/enable-sentiment-expanded.png)

3. 프로덕션 슬롯과 **게시** 단추를 선택합니다.

    [![](media/luis-quickstart-intent-and-sentiment-analysis/publish-to-production-inline.png "프로덕션 슬롯에 게시 단추가 강조 표시된 게시 페이지의 스크린샷")](media/luis-quickstart-intent-and-sentiment-analysis/publish-to-production-expanded.png#lightbox)

4. 웹 사이트의 위쪽에 성공이 확인된 녹색 상태 표시줄이 표시되면 게시가 완료됩니다.

## <a name="query-the-endpoint-with-an-utterance"></a>발언을 사용하여 엔드포인트 쿼리

1. **게시** 페이지의 아래쪽에서 **엔드포인트** 링크를 선택합니다. 그러면 주소 표시줄에 엔드포인트 URL이 있는 다른 브라우저 창이 열립니다. 

    !["엔드포인트 URL이 강조 표시된 게시 페이지 스크린샷](media/luis-quickstart-intent-and-sentiment-analysis/endpoint-url-inline.png)

2. 주소의 URL 끝으로 이동하고 `Reserve table for  10 on upper level away from kitchen`을 입력합니다. 마지막 쿼리 문자열 매개 변수는 발언 **쿼리**를 나타내는 `q`입니다. 이 발언은 레이블이 지정된 발언과 같지 않으므로 유용한 테스트이며 감정 분석이 추출된 `RestaurantReservation.Reserve` 의도가 반환되어야 합니다.

```
{
  "query": "Reserve table for 10 on upper level away from kitchen",
  "topScoringIntent": {
    "intent": "RestaurantReservation.Reserve",
    "score": 0.9926384
  },
  "intents": [
    {
      "intent": "RestaurantReservation.Reserve",
      "score": 0.9926384
    },
    {
      "intent": "None",
      "score": 0.00961109251
    }
  ],
  "entities": [],
  "sentimentAnalysis": {
    "label": "neutral",
    "score": 0.5
  }
}
```

## <a name="what-has-this-luis-app-accomplished"></a>이 LUIS 앱에서 수행한 작업은?
감정 분석이 사용된 이 앱은 자연어 쿼리 의도를 식별하고 전반적인 감정을 점수로 포함하는 추출된 데이터를 반환했습니다. 

이제 챗봇에는 대화의 다음 단계를 확인할 충분한 정보가 있습니다. 

## <a name="where-is-this-luis-data-used"></a>이 LUIS 데이터가 사용되는 위치는? 
LUIS는 이 요청을 통해 수행됩니다. 챗봇과 같은 호출 응용 프로그램은 발언에서 topScoringIntent 결과 및 감정 데이터를 사용하여 다음 단계를 진행할 수 있습니다. LUIS는 봇 또는 호출 응용 프로그램에 대해 프로그래밍 방식 작업을 수행하지 않습니다. LUIS는 사용자의 의도가 무엇인지만 결정합니다. 

## <a name="clean-up-resources"></a>리소스 정리
더 이상 필요하지 않은 경우 LUIS 앱을 삭제합니다. 이렇게 하려면 앱 목록에서 앱 이름 오른쪽에 있는 3개 점 메뉴(...)를 선택하고 **삭제**를 선택합니다. **앱을 삭제하시겠습니까?** 팝업 대화 상자에서 **확인**을 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"] 
> [C#을 사용하여 LUIS 엔드포인트 API 호출](luis-get-started-cs-get-intent.md) 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
