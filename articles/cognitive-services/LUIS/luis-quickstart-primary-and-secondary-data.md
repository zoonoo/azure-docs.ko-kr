---
title: 데이터를 추출하는 LUIS 앱 만들기 자습서 - Azure | Microsoft Docs
description: 이 자습서에서는 의도를 사용하는 간단한 LUIS 앱 및 기계 학습 데이터를 추출하는 단순 엔터티를 만드는 방법을 알아봅니다.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 03/29/2018
ms.author: v-geberr
ms.openlocfilehash: 1e8647e34da3d34946a4f6ac298017f6d4c99de6
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265363"
---
# <a name="tutorial-create-app-that-uses-simple-entity"></a>자습서: 단순 엔터티를 사용하는 앱 만들기
이 자습서에서는 **단순** 엔터티를 사용하여 발화에서 기계 학습 데이터를 추출하는 방법을 보여 주는 앱을 만듭니다.

<!-- green checkmark -->
> [!div class="checklist"]
> * 단순 엔터티 이해 
> * SendMessage 의도를 사용하여 통신 도메인에 대한 새 LUIS 앱 만들기
> * _없음_ 의도 및 발화 예제 추가
> * 단순 엔터티를 추가하여 발화에서 메시지 내용 추출
> * 앱 학습 및 게시
> * 앱의 엔드포인트를 쿼리하여 LUIS JSON 응답 확인

이 문서에서는 LUIS 앱을 작성하기 위해 체험 [LUIS][LUIS] 계정이 필요합니다.

## <a name="purpose-of-the-app"></a>앱의 용도
이 앱은 발화에서 데이터를 가져오는 방법을 보여 줍니다. 챗봇의 다음 발화를 살펴보겠습니다.

```JSON
Send a message telling them to stop
```

의도는 메시지를 보내는 것입니다. 발화에서 중요한 데이터는 메시지 자체(`telling them to stop`)입니다.  

## <a name="purpose-of-the-simple-entity"></a>단순 엔터티의 목적
단순 엔터티는 메시지가 무엇인지와 발화 중 어디에서 메시지를 찾을 수 있는지를 LUIS에 가르치기 위한 것입니다. 메시지에 해당하는 발화 부분은 단어 선택과 발화 길이에 따라 발화 간에 바뀔 수 있습니다. LUIS에는 모든 의도에서 모든 발화의 메시지 예제가 필요합니다.  

이 간단한 앱의 경우 메시지는 발화의 끝에 있습니다. 

## <a name="create-a-new-app"></a>새 앱 만들기
1. [LUIS][LUIS] 웹 사이트에 로그인합니다. LUIS 엔드포인트를 게시해야 하는 지역에 로그인해야 합니다.

2. [LUIS][LUIS] 웹 사이트에서 **새 앱 만들기**를 선택합니다.  

    ![LUIS 앱 목록](./media/luis-quickstart-primary-and-secondary-data/app-list.png)

3. 팝업 대화 상자에서 `MyCommunicator`라는 이름을 입력합니다. 

    ![LUIS 앱 목록](./media/luis-quickstart-primary-and-secondary-data/create-new-app-dialog.png)

4. 해당 프로세스가 완료되면 앱에 **없음** 의도가 있는 **의도** 페이지가 표시됩니다. 

    [![](media/luis-quickstart-primary-and-secondary-data/intents-list.png "없음 의도가 있는 LUIS 의도 페이지의 스크린샷")](media/luis-quickstart-primary-and-secondary-data/intents-list.png#lightbox)

## <a name="create-a-new-intent"></a>새 의도 만들기

1. **의도** 페이지에서 **새 의도 만들기**를 선택합니다. 

    [![](media/luis-quickstart-primary-and-secondary-data/create-new-intent-button.png "'새 의도 만들기' 단추가 강조 표시된 LUIS의 스크린샷")](media/luis-quickstart-primary-and-secondary-data/create-new-intent-button.png#lightbox)

2. `SendMessage`라는 새 의도 이름을 입력합니다. 이 의도는 사용자가 메시지를 보내려고 할 때마다 선택해야 합니다.

    의도를 만들면 식별하려는 정보의 기본 범주가 만들어집니다. 범주 이름을 지정하면 LUIS 쿼리 결과를 사용하는 다른 응용 프로그램에서 해당 범주 이름을 사용하여 적절한 대답을 찾거나 적절한 작업을 수행할 수 있습니다. LUIS는 이러한 질문에 답변하지 않으며, 자연어로 요청되는 정보의 유형만 식별합니다. 

    ![SendMessage 의도 이름 입력](./media/luis-quickstart-primary-and-secondary-data/create-new-intent-popup-dialog.png)

3. 사용자가 요청해야 하는 `SendMessage` 의도에 다음 7개 발화를 추가합니다.

    | 발화 예제|
    |--|
    |당신의 메시지를 받은 후에 회신했습니다. 내일 답변을 받을 수 있을 것입니다.|
    |"당신이 언제 집에 있을까요?"라고 묻는 메시지를 보냅니다.|
    |"제가 바빠요!"라는 텍스트|
    |"오늘 끝내야 합니다."라고 알려줍니다.|
    |"내가 운전 중이므로 나중에 응답할게!"라는 IM(메신저)|
    |David에게 "그게 언제였지?"라고 말하는 메시지를 작성합니다.|
    |"안녕, Greg!"라고 말합니다.|

    [![](media/luis-quickstart-primary-and-secondary-data/enter-utterances-on-intent-page.png "입력된 발화가 있는 LUIS의 스크린샷")](media/luis-quickstart-primary-and-secondary-data/enter-utterances-on-intent-page.png#lightbox)

## <a name="add-utterances-to-none-intent"></a>없음 의도에 발화 추가

LUIS 앱에는 현재 **없음** 의도에 대한 발화가 없습니다. 앱에서 답변하지 않도록 하려는 발화가 필요하므로 **없음** 의도에 발화가 있어야 합니다. 이 의도를 비워 두지 마세요. 
    
1. 왼쪽 패널에서 **의도**를 선택합니다. 

    [![](media/luis-quickstart-primary-and-secondary-data/select-intent-link.png "'의도' 단추가 강조 표시된 LUIS의 스크린샷")](media/luis-quickstart-primary-and-secondary-data/select-intent-link.png#lightbox)

2. **없음** 의도를 선택합니다. 

    [![](media/luis-quickstart-primary-and-secondary-data/select-none-intent.png "없음 의도가 선택된 스크린샷")](media/luis-quickstart-primary-and-secondary-data/select-none-intent.png#lightbox)

3. 사용자가 입력할 수 있지만 앱과 관련이 없는 세 개의 발화를 추가합니다. 몇 가지 유용한 **없음** 발화는 다음과 같습니다.

    | 발화 예제|
    |--|
    |취소!|
    |안녕!|
    |무슨 일이 일어나고 있나요?|
    
    챗봇과 같은 LUIS 호출 응용 프로그램에서 LUIS가 발화에 대해 **없음** 의도를 반환하는 경우, 봇에서 사용자가 대화를 종료하려는지 물어볼 수 있습니다. 사용자가 종료하지 않으려고 하는 경우, 봇에서 대화를 계속하기 위해 더 많은 지침을 제공할 수도 있습니다. 

    [![](media/luis-quickstart-primary-and-secondary-data/utterances-for-none-intent.png "없음 의도에 대한 발화가 있는 LUIS의 스크린샷")](media/luis-quickstart-primary-and-secondary-data/utterances-for-none-intent.png#lightbox)

## <a name="create-a-simple-entity-to-extract-message"></a>메시지를 추출하는 단순 엔터티 만들기 
1. 왼쪽 메뉴에서 **의도**를 선택합니다.

    ![의도 링크 선택](./media/luis-quickstart-primary-and-secondary-data/select-intents-from-none-intent.png)

2. 의도 목록에서 `SendMessage`를 선택합니다.

    ![SendMessage 의도 선택](./media/luis-quickstart-primary-and-secondary-data/select-sendmessage-intent.png)

3. 발화(`Reply with I got your message, I will have the answer tomorrow`)에서 메시지 본문의 첫 번째 단어(`I`)와 마지막 단어(`tomorrow`)를 선택합니다. 메시지에 대해 이러한 단어가 모두 선택되고 위쪽에 텍스트 상자가 있는 드롭다운 메뉴가 표시됩니다.

    [![](media/luis-quickstart-primary-and-secondary-data/select-words-in-utterance.png "발화에서 메시지에 대한 단어가 선택된 스크린샷")](media/luis-quickstart-primary-and-secondary-data/select-words-in-utterance.png#lightbox)

4. 텍스트 상자에서 `Message` 엔터티 이름을 입력합니다.

    [![](media/luis-quickstart-primary-and-secondary-data/enter-entity-name-in-box.png "텍스트 상자에서 엔터티 이름이 입력된 스크린샷")](media/luis-quickstart-primary-and-secondary-data/enter-entity-name-in-box.png#lightbox)

5. 드롭다운 메뉴에서 **새 엔터티 만들기**를 선택합니다. 엔터티의 목적은 메시지 본문인 텍스트를 추출하는 것입니다. 이 LUIS 앱에서 텍스트 메시지는 발화 끝에 있지만, 발화와 메시지의 길이는 제한이 없습니다. 

    [![](media/luis-quickstart-primary-and-secondary-data/create-message-entity.png "발화에서 새 엔터티 만들기의 스크린샷")](media/luis-quickstart-primary-and-secondary-data/create-message-entity.png#lightbox)

6. 팝업 창에서 기본 엔터티 형식은 **단순**이고 엔터티 이름은 `Message`입니다. 이러한 설정을 유지하고 **완료**를 선택합니다.

    ![엔터티 형식 확인](./media/luis-quickstart-primary-and-secondary-data/entity-type.png)

7. 엔터티가 만들어지고 하나의 발화에 대한 레이블이 지정되었으므로 해당 엔터티를 사용하여 나머지 발화에 대한 레이블을 지정합니다. 발화를 선택한 다음, 메시지의 첫 번째 및 마지막 단어를 선택합니다. 드롭다운 메뉴에서 `Message` 엔터티를 선택합니다. 메시지가 이제 엔터티의 이제 레이블로 지정됩니다. 나머지 발화의 모든 메시지 문구에 대한 레이블을 계속 지정합니다.

    [![](media/luis-quickstart-primary-and-secondary-data/all-labeled-utterances.png "레이블이 지정된 모든 메시지 발화에 대한 스크린샷")](media/luis-quickstart-primary-and-secondary-data/all-labeled-utterances.png#lightbox)

    발화의 기본 보기는 **엔터티 보기**입니다. 발화 위쪽에 있는 **엔터티 보기** 컨트롤을 선택합니다. **토큰 보기**에서는 발화 텍스트를 표시합니다. 

    [![](media/luis-quickstart-primary-and-secondary-data/tokens-view-of-utterances.png "토큰 보기의 발화에 대한 스크린샷")](media/luis-quickstart-primary-and-secondary-data/tokens-view-of-utterances.png#lightbox)

## <a name="train-the-luis-app"></a>LUIS 앱 학습
LUIS는 학습될 때까지 의도와 엔터티(모델)에 대한 변경 내용을 인식하지 못합니다. 

1. LUIS 웹 사이트의 오른쪽 위에서 **학습** 단추를 선택합니다.

    ![학습 단추 선택](./media/luis-quickstart-primary-and-secondary-data/train-button.png)

2. 웹 사이트의 위쪽에 성공이 확인된 녹색 상태 표시줄이 표시되면 학습이 완료됩니다.

    ![학습 성공 알림](./media/luis-quickstart-primary-and-secondary-data/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>앱을 게시하여 엔드포인트 URL 가져오기
챗봇 또는 다른 응용 프로그램에서 LUIS 예측을 얻으려면 앱을 게시해야 합니다. 

1. LUIS 웹 사이트의 오른쪽 위에서 **게시** 단추를 선택합니다. 

2. 프로덕션 슬롯과 **게시** 단추를 선택합니다.

    [![](media/luis-quickstart-primary-and-secondary-data/publish-to-production.png "프로덕션 슬롯에 게시 단추가 강조 표시된 게시 페이지의 스크린샷")](media/luis-quickstart-primary-and-secondary-data/publish-to-production.png#lightbox)

3. 웹 사이트의 위쪽에 성공이 확인된 녹색 상태 표시줄이 표시되면 게시가 완료됩니다.

## <a name="query-the-endpoint-with-a-different-utterance"></a>다른 발화를 사용하여 엔드포인트 쿼리
**게시** 페이지의 아래쪽에서 **엔드포인트** 링크를 선택합니다. 

[![](media/luis-quickstart-primary-and-secondary-data/publish-select-endpoint.png "엔드포인트가 강조 표시된 게시 페이지의 스크린샷")](media/luis-quickstart-primary-and-secondary-data/publish-select-endpoint.png#lightbox)

그러면 주소 표시줄에 엔드포인트 URL이 있는 다른 브라우저 창이 열립니다. 주소의 URL 끝으로 이동하고 `text I'm driving and will be 30 minutes late to the meeting`을 입력합니다. 마지막 쿼리 문자열 매개 변수는 발화 **쿼리**를 나타내는 `q`입니다. 이 발화는 레이블이 지정된 발화와 같지 않으므로 유용한 테스트이며, `SendMessage` 발화가 반환되어야 합니다.

```
{
  "query": "text I'm driving and will be 30 minutes late to the meeting",
  "topScoringIntent": {
    "intent": "SendMessage",
    "score": 0.987501
  },
  "intents": [
    {
      "intent": "SendMessage",
      "score": 0.987501
    },
    {
      "intent": "None",
      "score": 0.111048922
    }
  ],
  "entities": [
    {
      "entity": "i ' m driving and will be 30 minutes late to the meeting",
      "type": "Message",
      "startIndex": 5,
      "endIndex": 58,
      "score": 0.162995353
    }
  ]
}
```

## <a name="what-has-this-luis-app-accomplished"></a>이 LUIS 앱에서 수행한 작업은?
이 앱에서는 단지 2개의 의도와 하나의 엔터티를 사용하여 자연어 쿼리 의도를 확인하고 메시지 데이터를 반환했습니다. 

JSON 결과는 0.987501의 점수를 갖는 최고 점수 매기기 의도(`SendMessage`)를 나타냅니다. 모든 점수는 1에서 0 사이이며, 1에 가까울수록 더 좋은 점수입니다. `None` 의도의 점수는 0.111048922이며, 0에 매우 가깝습니다. 

메시지 데이터의 형식은 `Message`이고 값은 `i ' m driving and will be 30 minutes late to the meeting`입니다. 

챗봇에는 이제 `SendMessage` 기본 작업 및 메시지 텍스트인 해당 작업의 매개 변수를 결정하는 데 충분한 정보가 있습니다. 

## <a name="where-is-this-luis-data-used"></a>이 LUIS 데이터가 사용되는 위치는? 
LUIS는 이 요청을 통해 수행됩니다. 챗봇과 같은 호출 응용 프로그램에서는 topScoringIntent 결과와 엔터티의 데이터를 사용하여 타사 API를 통해 메시지를 보낼 수 있습니다. 봇 또는 호출 응용 프로그램에 대한 다른 프로그래밍 옵션이 있는 경우 LUIS는 이러한 작업을 수행하지 않습니다. LUIS는 사용자의 의도가 무엇인지만 결정합니다. 

## <a name="clean-up-resources"></a>리소스 정리
더 이상 필요하지 않은 경우 LUIS 앱을 삭제합니다. 이렇게 하려면 앱 목록에서 앱 이름 오른쪽에 있는 3개 점 메뉴(...)를 선택하고 **삭제**를 선택합니다. **앱을 삭제하시겠습니까?** 팝업 대화 상자에서 **확인**을 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [계층적 엔터티를 추가하는 방법 알아보기](luis-quickstart-intent-and-hier-entity.md)


<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
