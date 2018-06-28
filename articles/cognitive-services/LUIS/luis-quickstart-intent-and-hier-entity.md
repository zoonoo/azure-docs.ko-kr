---
title: 위치 데이터를 가져오는 LUIS 앱을 만드는 자습서 - Azure | Microsoft Docs
description: 이 자습서에서는 의도를 사용하는 간단한 LUIS 앱 및 데이터를 추출하는 계층적 엔터티를 만드는 방법을 알아봅니다.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: v-geberr
ms.openlocfilehash: 2547407126943161ba604fa2f5e80b9186cae57e
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266501"
---
# <a name="tutorial-create-app-that-uses-hierarchical-entity"></a>자습서: 계층적 엔터티를 사용하는 앱 만들기
이 자습서에서는 컨텍스트에 따라 관련 데이터 부분을 찾는 방법을 보여 주는 앱을 만듭니다. 

<!-- green checkmark -->
> [!div class="checklist"]
> * 계층적 엔터티 및 컨텍스트에 따라 학습된 자식에 대한 이해 
> * Bookflight 의도를 사용하여 여행 도메인에 대한 새 LUIS 앱 만들기
> * _없음_ 의도 및 발화 예제 추가
> * 원본 및 대상 자식이 있는 위치 계층적 엔터티 추가
> * 앱 학습 및 게시
> * 앱의 엔드포인트를 쿼리하여 계층적 자식이 포함된 LUIS JSON 응답 확인 

이 문서에서는 LUIS 앱을 작성하기 위해 체험 [LUIS][LUIS] 계정이 필요합니다.

## <a name="purpose-of-the-app-with-this-entity"></a>이 엔터티를 사용하는 앱의 목적
이 앱에서는 사용자가 항공편을 예약하려는지 여부를 결정합니다. 계층적 엔터티를 사용하여 사용자의 텍스트 내에서 위치, 출발 도시 및 도착 도시를 결정합니다. 

계층적 엔터티는 데이터의 다음 두 부분으로 인해 이 형식의 데이터에 적합합니다.

* 두 위치 모두는 일반적으로 도시 또는 공항 코드로 표시됩니다.
* 일반적으로 단어 주위에서 원본과 대상을 결정할 수 있는 고유한 단어를 선택할 수 있습니다. 이러한 단어에는 to(도착), headed to(행), from(출발), leave(발)가 포함됩니다.
* 두 위치 모두는 종종 동일한 발화에 있습니다. 

**계층적** 개체의 목적은 컨텍스트에 따라 발화 내에서 관련 데이터를 찾는 것입니다. 다음 발화를 살펴보겠습니다.

```JSON
1 ticket from Seattle to Cairo`
```

발화에는 지정된 두 개의 위치가 있습니다. 하나는 출발 도시로 시애틀이고, 다른 하나는 도착 도시로 카이로입니다. 이러한 도시는 항공편을 예약하는 데 중요합니다. 단순 엔터티를 사용하여 찾을 수 있지만, 서로 관련되어 있으며 동일한 발화에서 자주 발견됩니다. 따라서 둘 모두는 **"위치"** 계층적 엔터티의 자식으로 그룹화되는 것이 좋습니다. 

기계 학습 엔터티로서 앱에는 출발 도시 및 도착 도시 레이블이 지정된 발화 예제가 필요합니다. 이는 발화 내에서 엔터티가 있는 위치, 존속 기간 및 주변의 단어에 대해 LUIS를 학습시킵니다. 

## <a name="app-intents"></a>앱 의도
의도는 사용자가 원하는 항목의 범주입니다. 이 앱에는 BookFlight 및 없음이라는 두 개의 의도가 있습니다. [없음](luis-concept-intent.md#none-intent-is-fallback-for-app) 의도는 앱 외부에 있는 것을 나타내기 위한 것입니다.  

## <a name="hierarchical-entity-is-contextually-learned"></a>계층적 엔터티는 컨텍스트에 따라 학습됩니다. 
엔터티의 목적은 발화에서 텍스트 부분을 찾고 분류하기 위한 것입니다. [계층적](luis-concept-entity-types.md) 엔터티는 사용 컨텍스트를 기반으로 하는 부모-자식 엔터티입니다. 사람은 `to` 및 `from`의 사용에 따라 발화에서 출발 도시와 도착 도시를 결정할 수 있습니다. 이러한 발화는 컨텍스트 사용의 예입니다.  

이 여행 앱의 경우 LUIS는 표준 예약을 만들고 채울 수 있는 방식으로 출발 위치와 도착 위치를 추출합니다. LUIS를 사용하면 발언에 변형, 약어 및 속어가 포함될 수 있습니다. 

사용자의 간단한 발화 예제는 다음과 같습니다.

```
Book a flight to London for next Monday
2 tickets from Dallas to Dublin this weekend
Researve a seat from New York to Paris on the first of April
```

발화의 약어 또는 속어 버전은 다음과 같습니다.

```
LHR tomorrow
SEA to NYC next Monday
LA to MCO spring break
```
 
계층적 엔터티는 원본 위치 및 대상 위치와 일치합니다. 계층적 엔터티의 자식(원본 또는 대상)이 하나만 있는 경우에도 추출됩니다. 하나 또는 일부를 추출하기 위해 모든 자식을 검색할 필요가 없습니다. 

## <a name="what-luis-does"></a>LUIS에서 수행하는 작업
발화의 의도 및 엔터티가 식별되고, [추출되고](luis-concept-data-extraction.md#list-entity-data), [엔드포인트](https://aka.ms/luis-endpoint-apis)의 JSON에서 반환되면 LUIS가 수행됩니다. 호출 응용 프로그램 또는 챗봇에서 해당 JSON 응답을 사용하고, 응용 프로그램 또는 챗봇에서 수행하도록 설계된 방식으로 요청을 수행합니다. 

## <a name="create-a-new-app"></a>새 앱 만들기
1. [LUIS][LUIS] 웹 사이트에 로그인합니다. LUIS 엔드포인트를 게시해야 하는 [지역][LUIS-regions]에 로그인합니다.

2. [LUIS][LUIS] 웹 사이트에서 **새 앱 만들기**를 선택합니다.  

    [![](media/luis-quickstart-intent-and-hier-entity/app-list.png "앱 목록 페이지의 스크린샷")](media/luis-quickstart-intent-and-hier-entity/app-list.png#lightbox)

3. 팝업 대화 상자에서 `MyTravelApp`이라는 이름을 입력합니다. 

    [![](media/luis-quickstart-intent-and-hier-entity/create-new-app.png "새 앱 만들기 팝업 대화 상자의 스크린샷")](media/luis-quickstart-intent-and-hier-entity/create-new-app.png#lightbox)

4. 해당 프로세스가 완료되면 앱에 **없음** 의도가 있는 **의도** 페이지가 표시됩니다. 

    [![](media/luis-quickstart-intent-and-hier-entity/intents-page-none-only.png "없음 의도만 있는 의도 목록의 스크린샷")](media/luis-quickstart-intent-and-hier-entity/intents-page-none-only.png#lightbox)

## <a name="create-a-new-intent"></a>새 의도 만들기

1. **의도** 페이지에서 **새 의도 만들기**를 선택합니다. 

    [![](media/luis-quickstart-intent-and-hier-entity/create-new-intent-button.png "새 의도 만들기 단추가 강조 표시된 의도 목록의 스크린샷")](media/luis-quickstart-intent-and-hier-entity/create-new-intent-button.png#lightbox)

2. `BookFlight`라는 새 의도 이름을 입력합니다. 이 의도는 사용자가 항공편을 예약하려고 할 때마다 선택해야 합니다.

    의도를 만들면 식별하려는 정보의 기본 범주가 만들어집니다. 범주 이름을 지정하면 LUIS 쿼리 결과를 사용하는 다른 응용 프로그램에서 해당 범주 이름을 사용하여 적절한 대답을 찾거나 적절한 작업을 수행할 수 있습니다. LUIS는 이러한 질문에 답변하지 않으며, 자연어로 요청되는 정보의 유형만 식별합니다. 

    [![](media/luis-quickstart-intent-and-hier-entity/create-new-intent.png "새 의도 만들기 팝업 대화 상자의 스크린샷")](media/luis-quickstart-intent-and-hier-entity/create-new-intent.png#lightbox)

3. 사용자가 요청해야 하는 `BookFlight` 의도에 몇 가지 발화를 추가합니다.

    | 발화 예제|
    |--|
    |다음 주 월요일 시애틀 출발, 카이로 도착의 2개 항공편을 예약합니다.|
    |내일 런던행 항공권을 예약합니다|
    |4월 1일 파리 출발, 런던 도착의 4개 좌석을 예약합니다.|

    [![](media/luis-quickstart-intent-and-hier-entity/enter-utterances-on-intent.png "발화가 입력된 BookFlight 의도 페이지의 스크린샷")](media/luis-quickstart-intent-and-hier-entity/enter-utterances-on-intent.png#lightbox)

## <a name="add-utterances-to-none-intent"></a>없음 의도에 발화 추가

LUIS 앱에는 현재 **없음** 의도에 대한 발화가 없습니다. 앱에서 답변하지 않도록 하려는 발화가 필요하므로 **없음** 의도에 발화가 있어야 합니다. 이 의도를 비워 두지 마세요. 

1. 왼쪽 패널에서 **의도**를 선택합니다. 

    [![](media/luis-quickstart-intent-and-hier-entity/select-intents-from-bookflight-intent.png "의도 단추가 강조 표시된 BookFlight 의도 페이지의 스크린샷")](media/luis-quickstart-intent-and-hier-entity/select-intents-from-bookflight-intent.png#lightbox)

2. **없음** 의도를 선택합니다. 사용자가 입력할 수 있지만 앱과 관련이 없는 세 개의 발화를 추가합니다.

    | 발화 예제|
    |--|
    |취소!|
    |안녕!|
    |무슨 일이 일어나고 있나요?|

## <a name="when-the-utterance-is-predicted-for-the-none-intent"></a>없음 의도에 대한 발화가 예측되는 경우
챗봇과 같은 LUIS 호출 응용 프로그램에서 LUIS가 발화에 대해 **없음** 의도를 반환하는 경우, 봇에서 사용자가 대화를 종료하려는지 물어볼 수 있습니다. 사용자가 종료하지 않으려고 하는 경우, 봇에서 대화를 계속하기 위해 더 많은 지침을 제공할 수도 있습니다. 

엔터티는 **없음** 의도에서 작동합니다. 최고 점수 매기기 의도가 **없음**이지만 챗봇에 의미 있는 엔터티가 추출되면, 챗봇에서 고객의 의도에 초점을 맞춘 질문으로 계속 수행할 수 있습니다. 

## <a name="create-a-location-entity-from-the-intent-page"></a>의도 페이지에서 위치 엔터티 만들기
이제 두 개의 의도에 발화가 있으므로 LUIS에서 위치가 무엇인지 이해해야 합니다. `BookFlight` 의도로 돌아가서 다음 단계에 따라 발화에서 도시 이름에 대한 레이블을 지정(표시)합니다.

1. 왼쪽 패널에서 **의도**를 선택하여 `BookFlight` 의도로 돌아갑니다.

2. 의도 목록에서 `BookFlight`를 선택합니다.

3. `Book 2 flights from Seattle to Cairo next Monday` 발화에서 `Seattle`이라는 단어를 선택합니다. 새 엔터티를 만들기 위해 텍스트 상자가 맨 위에 있는 드롭다운 메뉴가 표시됩니다. 텍스트 상자에서 `Location`이라는 엔터티 이름을 입력한 다음, 드롭다운 메뉴에서 **새 엔터티 만들기**를 선택합니다. 

    [![](media/luis-quickstart-intent-and-hier-entity/label-seattle-in-utterance.png "선택한 텍스트에서 새 엔터티를 만드는 BookFlight 의도 페이지의 스크린샷")](media/luis-quickstart-intent-and-hier-entity/label-seattle-in-utterance.png#lightbox)

4. 팝업 창에서 자식 엔터티로 `Origin` 및 `Destination`이 있는 **계층적** 엔터티 형식을 선택합니다. **완료**를 선택합니다.

    [![](media/luis-quickstart-intent-and-hier-entity/hier-entity-ddl.png "새 위치 엔터티에 대한 엔터티 만들기 팝업 대화 상자의 스크린샷")](media/luis-quickstart-intent-and-hier-entity/hier-entity-ddl.png#lightbox)

    LUIS에서 용어가 원본, 대상 또는 둘 다인지 인식하지 못하므로 `Seattle`에 대한 레이블은 `Location`으로 표시됩니다. `Seattle`을 선택하고, [위치]를 선택한 다음, 오른쪽 메뉴에 따라 `Origin`을 선택합니다.

5. 이제 엔터티가 만들어지고, 하나의 발화에 대한 레이블이 지정되었으므로 도시 이름을 선택하고, [위치]를 선택한 다음, 오른쪽 메뉴에 따라 `Origin` 또는 `Destination`을 선택하여 다른 도시에 대한 레이블을 지정합니다.

    [![](media/luis-quickstart-intent-and-hier-entity/label-destination-in-utterance.png "선택한 엔터티에 대해 선택된 발화 텍스트가 있는 Bookflight 엔터티의 스크린샷")](media/luis-quickstart-intent-and-hier-entity/label-destination-in-utterance.png#lightbox)

## <a name="train-the-luis-app"></a>LUIS 앱 학습
LUIS는 학습될 때까지 의도와 엔터티(모델)에 대한 변경 내용을 인식하지 못합니다. 

1. LUIS 웹 사이트의 오른쪽 위에서 **학습** 단추를 선택합니다.

    ![앱 학습](./media/luis-quickstart-intent-and-hier-entity/train-button.png)

2. 웹 사이트의 위쪽에 성공이 확인된 녹색 상태 표시줄이 표시되면 학습이 완료됩니다.

    ![학습 성공](./media/luis-quickstart-intent-and-hier-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>앱을 게시하여 엔드포인트 URL 가져오기
챗봇 또는 다른 응용 프로그램에서 LUIS 예측을 얻으려면 앱을 게시해야 합니다. 

1. LUIS 웹 사이트의 오른쪽 위에서 **게시** 단추를 선택합니다. 

    [![](media/luis-quickstart-intent-and-hier-entity/publish.png "게시 단추가 강조 표시된 Bookflight 의도의 스크린샷")](media/luis-quickstart-intent-and-hier-entity/publish.png#lightbox)

2. 프로덕션 슬롯과 **게시** 단추를 선택합니다.

    [![](media/luis-quickstart-intent-and-hier-entity/publish-to-production.png "프로덕션 슬롯에 게시 단추가 강조 표시된 게시 페이지의 스크린샷")](media/luis-quickstart-intent-and-hier-entity/publish-to-production.png#lightbox)

3. 웹 사이트의 위쪽에 성공이 확인된 녹색 상태 표시줄이 표시되면 게시가 완료됩니다.

## <a name="query-the-endpoint-with-a-different-utterance"></a>다른 발화를 사용하여 엔드포인트 쿼리
1. **게시** 페이지의 아래쪽에서 **엔드포인트** 링크를 선택합니다. 그러면 주소 표시줄에 엔드포인트 URL이 있는 다른 브라우저 창이 열립니다. 

    [![](media/luis-quickstart-intent-and-hier-entity/publish-select-endpoint.png "엔드포인트 URL이 강조 표시된 게시 페이지의 스크린샷")](media/luis-quickstart-intent-and-hier-entity/publish-select-endpoint.png#lightbox)

2. 주소의 URL 끝으로 이동하고 `1 ticket to Portland on Friday`를 입력합니다. 마지막 쿼리 문자열 매개 변수는 `q`, 발화 **q** 쿼리입니다. 이 발화는 레이블이 있는 발화와 같지 않으므로 좋은 테스트이므로 추출된 계층적 엔터티와 함께 `BookFlight` 의도를 반환해야 합니다.

```
{
  "query": "1 ticket to Portland on Friday",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.9998226
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 0.9998226
    },
    {
      "intent": "None",
      "score": 0.221926212
    }
  ],
  "entities": [
    {
      "entity": "portland",
      "type": "Location::Destination",
      "startIndex": 12,
      "endIndex": 19,
      "score": 0.564448953
    }
  ]
}
```

## <a name="what-has-this-luis-app-accomplished"></a>이 LUIS 앱에서 수행한 작업은?
이 앱은 단지 두 개의 의도와 계층적 엔터티를 사용하여 자연어 쿼리 의도를 확인하고 추출된 데이터를 반환했습니다. 

챗봇에는 이제 `BookFlight` 기본 작업 및 발화에서 찾은 위치 정보를 결정하는 데 충분한 정보가 있습니다. 

## <a name="where-is-this-luis-data-used"></a>이 LUIS 데이터가 사용되는 위치는? 
LUIS는 이 요청을 통해 수행됩니다. 챗봇과 같은 호출 응용 프로그램에서는 topScoringIntent 결과와 엔터티의 데이터를 사용하여 다음 단계를 수행할 수 있습니다. LUIS는 봇 또는 호출 응용 프로그램에 대해 프로그래밍 방식으로 작동하지 않습니다. LUIS는 사용자의 의도가 무엇인지만 결정합니다. 

## <a name="clean-up-resources"></a>리소스 정리
더 이상 필요하지 않은 경우 LUIS 앱을 삭제합니다. 이렇게 하려면 앱 목록에서 앱 이름 오른쪽에 있는 3개 점 메뉴(...)를 선택하고 **삭제**를 선택합니다. **앱을 삭제하시겠습니까?** 팝업 대화 상자에서 **확인**을 선택합니다.

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"] 
> [목록 엔터티를 추가하는 방법 알아보기](luis-quickstart-intent-and-list-entity.md) 

**숫자** [미리 작성된 엔터티](luis-how-to-add-entities.md#add-prebuilt-entity)를 추가하여 번호를 추출합니다. 

**datetimeV2** [미리 작성된 엔터티](luis-how-to-add-entities.md#add-prebuilt-entity)를 추가하여 날짜 정보를 추출합니다.


<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
