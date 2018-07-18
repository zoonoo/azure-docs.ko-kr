---
title: 복잡한 데이터를 추출하는 복합 엔터티 만들기 - Azure | Microsoft Docs
description: LUIS 앱에서 복합 엔터티를 만들어 다른 형식의 엔터티 데이터를 추출하는 방법에 대해 알아봅니다.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 03/28/2018
ms.author: v-geberr
ms.openlocfilehash: cb581ee60dea2b0810332933455a03a8b68e16ea
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264388"
---
# <a name="use-composite-entity-to-extract-complex-data"></a>복합 엔터티를 사용하여 복잡한 데이터 추출
이 간단한 앱에는 두 개의 [의도](luis-concept-intent.md)와 여러 개의 엔터티가 포함됩니다. 이 앱의 목적은 ‘금요일에 시애틀에서 카이로로 가는 티켓 1매’와 같은 항공편을 예약하고 예약의 모든 세부 정보를 단일 조각의 데이터로 반환하는 것입니다. 

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
* 미리 빌드된 엔터티 datetimeV2 및 number 추가
* 복합 엔터티 만들기
* LUIS 쿼리 및 복합 엔터티 데이터 수신

## <a name="before-you-begin"></a>시작하기 전에
* **[계층 구조 빠른 시작](luis-tutorial-composite-entity.md)** 의 LUIS 앱. 

> [!Tip]
> 아직 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)으로 등록할 수 있습니다.

## <a name="composite-entity-is-a-logical-grouping"></a>복합 엔터티는 논리 그룹임 
엔터티의 목적은 발화에서 텍스트 부분을 찾고 분류하기 위한 것입니다. [복합](luis-concept-entity-types.md) 엔터티는 컨텍스트에서 학습된 기타 엔터티 형식으로 구성됩니다. 항공편 예약을 사용하는 이 여행 앱의 경우, 여러 가지 정보(예: 날짜, 위치 및 좌석 수)가 있습니다. 

복합이 만들어지기 전에 정보가 개별 엔터티로 존재합니다. 개별 엔터티를 논리적으로 그룹화하고 이 논리 그룹이 챗봇 또는 기타 LUIS 사용 응용 프로그램에 유용할 경우 복합 엔터티를 만듭니다. 

사용자의 간단한 예제 발화는 다음과 같습니다.

```
Book a flight to London for next Monday
2 tickets from Dallas to Dublin this weekend
Reserve a seat from New York to Paris on the first of April
```
 
복합 엔터티가 좌석 수, 출발지 위치, 도착지 위치 및 날짜와 일치합니다. 

## <a name="what-luis-does"></a>LUIS에서 수행하는 작업
발화의 의도 및 엔터티가 식별되고, [추출되고](luis-concept-data-extraction.md#list-entity-data), [엔드포인트](https://aka.ms/luis-endpoint-apis)의 JSON에서 반환되면 LUIS가 수행됩니다. 호출 응용 프로그램 또는 챗봇에서 해당 JSON 응답을 사용하고, 응용 프로그램 또는 챗봇에서 수행하도록 설계된 방식으로 요청을 수행합니다. 

## <a name="add-prebuilt-entities-number-and-datetimev2"></a>미리 빌드된 엔터티 number 및 datetimeV2 추가
1. [LUIS][LUIS] 웹 사이트의 앱 목록에서 `MyTravelApp` 앱을 선택합니다.

2. 앱이 열리면 **엔터티** 왼쪽 탐색 링크를 선택합니다.

    ![엔터티 단추 선택](./media/luis-tutorial-composite-entity/intents-page-select-entities.png)    

3. **미리 빌드된 엔터티 관리**를 선택합니다.

    ![엔터티 단추 선택](./media/luis-tutorial-composite-entity/manage-prebuilt-entities-button.png)

4. 팝업 상자에서 **number** 및 **datetimeV2**를 선택합니다.

    ![엔터티 단추 선택](./media/luis-tutorial-composite-entity/prebuilt-entity-ddl.png)

5. 새 엔터티를 추출하려면 맨 위 탐색 모음에서 **학습**을 선택합니다.

    ![학습 단추 선택](./media/luis-tutorial-composite-entity/train.png)

## <a name="use-existing-intent-to-create-composite-entity"></a>기존 의도를 사용하여 복합 엔터티를 만듭니다.
1. 왼쪽 탐색에서 **의도**를 선택합니다. 

    ![의도 선택 페이지](./media/luis-tutorial-composite-entity/intents-from-entities-page.png)

2. **의도** 목록에서 `BookFlight`를 선택합니다.  

    ![목록에서 BookFlight 의도 선택](./media/luis-tutorial-composite-entity/intent-page-with-prebuilt-entities-labeled.png)

    number 및 datetimeV2 미리 빌드된 엔터티는 발화에서 레이블이 지정됩니다.

3. 발화 `book 2 flights from seattle to cairo next monday`의 경우, 파란색 `number` 엔터티를 선택한 다음, 목록에서 **복합 엔터티로 래핑**을 선택합니다. 단어 아래 녹색 선은 커서가 오른쪽으로 이동할 때 커서를 따라 복합 엔터티를 나타냅니다. 그런 다음, 오른쪽으로 이동하여 마지막 미리 빌드된 엔터티 `datetimeV2`를 선택한 다음, 팝업 창의 텍스트 상자에 `FlightReservation`을 입력하고 **새 복합 만들기**를 선택합니다. 

    ![의도 페이지에서 복합 엔터티 만들기](./media/luis-tutorial-composite-entity/create-new-composite.png)

4. 복합 엔터티 자식을 확인할 수 있는 팝업 대화 상자가 표시됩니다. **완료**를 선택합니다.

    ![의도 페이지에서 복합 엔터티 만들기](./media/luis-tutorial-composite-entity/validate-composite-entity.png)

## <a name="wrap-the-entities-in-the-composite-entity"></a>복합 엔터티로 엔터티 래핑
복합 엔터티가 만들어지면 복합 엔터티의 나머지 발화에 레이블을 지정합니다. 구문을 복합 엔터티로 래핑하려면 맨 왼쪽 단어를 선택하고, 표시되는 목록에서 **복합 엔터티로 래핑**을 선택한 다음, 맨 오른쪽 단어를 선택하고, 명명된 복합 엔터티 `FlightReservation`을 선택합니다. 이는 다음 단계로 구분된 빠르고 원활한 선택 단계입니다.

1. 발화 `schedule 4 seats from paris to london for april 1`에서 number 미리 빌드된 엔터티로 4를 선택합니다.

    ![맨 왼쪽 단어 선택](./media/luis-tutorial-composite-entity/wrap-composite-step-1.png)

2. 표시되는 목록에서 **복합 엔터티로 래핑**을 선택합니다.

    ![목록에서 래핑 선택](./media/luis-tutorial-composite-entity/wrap-composite-step-2.png)

3. 맨 오른쪽 단어를 선택합니다. 구문 아래에 복합 엔터티를 나타내는 녹색 선이 표시됩니다.

    ![맨 오른쪽 단어 선택](./media/luis-tutorial-composite-entity/wrap-composite-step-3.png)

4. 표시되는 목록에서 복합 이름 `FlightReservation`을 선택합니다.

    ![명명된 복합 엔터티 선택](./media/luis-tutorial-composite-entity/wrap-composite-step-4.png)

    마지막 발화의 경우, 동일한 지침을 사용하여 복합 엔터티로 `London` 및 `tomorrow`를 래핑합니다. 

## <a name="train-the-luis-app"></a>LUIS 앱 학습
LUIS는 학습될 때까지 의도와 엔터티(모델)에 대한 변경 내용을 인식하지 못합니다. 

1. LUIS 웹 사이트의 오른쪽 위에서 **학습** 단추를 선택합니다.

    ![앱 학습](./media/luis-tutorial-composite-entity/train-button.png)

2. 웹 사이트의 위쪽에 성공이 확인된 녹색 상태 표시줄이 표시되면 학습이 완료됩니다.

    ![학습 성공](./media/luis-tutorial-composite-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>앱을 게시하여 엔드포인트 URL 가져오기
챗봇 또는 다른 응용 프로그램에서 LUIS 예측을 얻으려면 앱을 게시해야 합니다. 

1. LUIS 웹 사이트의 오른쪽 위에서 **게시** 단추를 선택합니다. 

2. 프로덕션 슬롯과 **게시** 단추를 선택합니다.

    ![앱 게시](./media/luis-tutorial-composite-entity/publish-to-production.png)

3. 웹 사이트의 위쪽에 성공이 확인된 녹색 상태 표시줄이 표시되면 게시가 완료됩니다.

## <a name="query-the-endpoint-with-a-different-utterance"></a>다른 발화를 사용하여 엔드포인트 쿼리
1. **게시** 페이지의 아래쪽에서 **엔드포인트** 링크를 선택합니다. 그러면 주소 표시줄에 끝점 URL이 표시된 다른 브라우저 창이 열립니다. 

    ![끝점 URL 선택](./media/luis-tutorial-composite-entity/publish-select-endpoint.png)

2. 주소의 URL 끝으로 이동하고 `reserve 3 seats from London to Cairo on Sunday`를 입력합니다. 마지막 쿼리 문자열 매개 변수는 `q`, 발화 쿼리입니다. 이 발화는 레이블이 있는 발화와 같지 않으므로 좋은 테스트이므로 추출된 계층적 엔터티와 함께 `BookFlight` 의도를 반환해야 합니다.

```
{
  "query": "reserve 3 seats from London to Cairo on Sunday",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.999999046
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 0.999999046
    },
    {
      "intent": "None",
      "score": 0.227036044
    }
  ],
  "entities": [
    {
      "entity": "sunday",
      "type": "builtin.datetimeV2.date",
      "startIndex": 40,
      "endIndex": 45,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-WXX-7",
            "type": "date",
            "value": "2018-03-25"
          },
          {
            "timex": "XXXX-WXX-7",
            "type": "date",
            "value": "2018-04-01"
          }
        ]
      }
    },
    {
      "entity": "3 seats from london to cairo on sunday",
      "type": "flightreservation",
      "startIndex": 8,
      "endIndex": 45,
      "score": 0.6892485
    },
    {
      "entity": "cairo",
      "type": "Location::Destination",
      "startIndex": 31,
      "endIndex": 35,
      "score": 0.557570755
    },
    {
      "entity": "london",
      "type": "Location::Origin",
      "startIndex": 21,
      "endIndex": 26,
      "score": 0.8933808
    },
    {
      "entity": "3",
      "type": "builtin.number",
      "startIndex": 8,
      "endIndex": 8,
      "resolution": {
        "value": "3"
      }
    }
  ],
  "compositeEntities": [
    {
      "parentType": "flightreservation",
      "value": "3 seats from london to cairo on sunday",
      "children": [
        {
          "type": "builtin.datetimeV2.date",
          "value": "sunday"
        },
        {
          "type": "Location::Destination",
          "value": "cairo"
        },
        {
          "type": "builtin.number",
          "value": "3"
        },
        {
          "type": "Location::Origin",
          "value": "london"
        }
      ]
    }
  ]
}
```

이 발화는 추출된 데이터와 함께 **flightreservation** 개체를 포함하는 복합 엔터티 배열을 반환합니다.  

## <a name="what-has-this-luis-app-accomplished"></a>이 LUIS 앱에서 수행한 작업은?
이 앱에서는 단지 2개의 의도와 복합 엔터티를 사용하여 자연어 쿼리 의도를 확인하고 추출된 데이터를 반환했습니다. 

챗봇에는 이제 `BookFlight` 기본 작업 및 발화에서 찾은 예약 정보를 결정하는 데 충분한 정보가 있습니다. 

## <a name="where-is-this-luis-data-used"></a>이 LUIS 데이터가 사용되는 위치는? 
LUIS는 이 요청을 통해 수행됩니다. 챗봇과 같은 호출 응용 프로그램에서는 topScoringIntent 결과와 엔터티의 데이터를 사용하여 다음 단계를 수행할 수 있습니다. LUIS는 봇 또는 호출 응용 프로그램에 대해 프로그래밍 방식으로 작동하지 않습니다. LUIS는 사용자의 의도가 무엇인지만 결정합니다. 

## <a name="next-steps"></a>다음 단계

[엔터티에 대해 자세히 알아봅니다](luis-concept-entity-types.md). 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
