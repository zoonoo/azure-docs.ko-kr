---
title: 계층적 엔터티
titleSuffix: Azure Cognitive Services
description: 컨텍스트를 기반으로 관련 데이터 조각을 찾습니다. 예를 들어 한 건물과 사무실에서 다른 건물과 사무실로 이사할 경우 출발지 및 목적지 위치는 서로 관련이 있습니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/05/2018
ms.author: diberry
ms.openlocfilehash: a79c0091220e2980101471abaaa0aaf4c0a898ca
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53104410"
---
# <a name="tutorial-5-extract-contextually-related-data"></a>자습서 5: 컨텍스트 관련 데이터 추출
이 자습서에서는 컨텍스트를 기반으로 관련 데이터 조각을 찾습니다. 예를 들어 한 건물과 사무실에서 다른 건물과 사무실로 이사할 경우 출발지 및 목적지 위치는 서로 관련이 있습니다. 작업 순서를 생성하려면 두 데이터 조각이 모두 필요하며, 서로 관련이 있습니다.  

이 앱은 원래 위치(건물 및 사무실)에서 대상 위치(건물 및 사무실)로 직원을 이동할 위치를 결정합니다. 계층적 엔터티를 사용하여 발언 내의 위치를 확인합니다. **계층적** 개체의 목적은 컨텍스트에 따라 발화 내에서 관련 데이터를 찾는 것입니다. 

계층적 엔터티는 데이터의 다음 두 부분으로 인해 이 형식의 데이터에 적합합니다.

* 단순 엔터티입니다.
* 발언의 컨텍스트에서 서로 관련되어 있습니다.
* 특정 단어를 사용하여 각 위치를 나타냅니다. 이러한 단어의 예로 에서/(으)로, 떠나/~을(를) 향해, ~에서 꺼내/방향으로 등이 있습니다.
* 두 위치 모두는 종종 동일한 발화에 있습니다. 
* 클라이언트 앱에서 정보 단위로 그룹화하고 처리되어야 합니다.

**이 자습서에서는 다음 방법에 대해 알아봅니다.**

> [!div class="checklist"]
> * 기존 자습서 앱 사용
> * 의도 추가 
> * 원본 및 대상 자식이 있는 위치 계층적 엔터티 추가
> * 학습
> * 게시
> * 엔드포인트에서 의도 및 엔터티 가져오기

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>기존 앱 사용
마지막 자습서에서 만든 **HumanResources**라는 앱을 사용하여 계속 진행합니다. 

이전 자습서의 HumanResources 앱이 없으면 다음 단계를 사용합니다.

1.  [앱 JSON 파일](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-list-HumanResources.json)을 다운로드하고 저장합니다.

2. JSON을 새 앱으로 가져옵니다.

3. **관리** 섹션의 **버전** 탭에서 버전을 복제하고 `hier`라는 이름을 지정합니다. 복제는 원래 버전에 영향을 주지 않고도 다양한 LUIS 기능을 사용할 수 있는 좋은 방법입니다. 버전 이름이 URL 경로의 일부로 사용되므로 이름에는 URL에 유효하지 않은 문자가 포함될 수 없습니다. 

## <a name="remove-prebuilt-number-entity-from-app"></a>앱에서 미리 작성된 숫자 엔터티를 제거합니다.
전체 발화를 살펴보고 계층적 자식을 표시하려면 [미리 작성된 숫자 엔터티를 일시적으로 제거](luis-prebuilt-entities.md#marking-entities-containing-a-prebuilt-entity-token)해야 합니다. 

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. 왼쪽 메뉴에서 **엔터티**를 선택합니다.

3. 목록에서 숫자 엔터티의 왼쪽에 있는 확인란을 선택합니다. **삭제**를 선택합니다. 

## <a name="add-utterances-to-moveemployee-intent"></a>MoveEmployee 의도에 발언 추가

1. 왼쪽 메뉴에서 **의도**를 선택합니다.

2. 의도 목록에서 **MoveEmployee**를 선택합니다.

3. 다음 예제 발언을 추가합니다.

    |예제 발화|
    |--|
    |John W. Smith를 a-2345**로** 이동|
    |Jill Jones를 b-3499**로** 이동|
    |x23456을 hh-2345**에서** e-0234**로** 이동하도록 구성|
    |x12345가 a-3459를 **떠나서** f-34567로 **향하도록** 설정하는 서류 작업 시작|
    |425-555-0000을 g-2323**에서 꺼내** hh-2345 **방향으로** 배치|

    [ ![MoveEmployee 의도에 새 발언이 있는 LUIS의 스크린샷](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png)](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png#lightbox)

    [엔터티 목록](luis-quickstart-intent-and-list-entity.md) 자습서에서 직원 이름, 이메일 주소, 내선 번호, 휴대폰 번호 또는 미국 사회 보장 번호를 사용하여 직원을 지정합니다. 이러한 직원 번호는 발언에 사용됩니다. 이전 예제 발언에는 원래 위치와 대상 위치를 알려주는 여러 방법이 포함되어 있으며, 볼드로 표시되어 있습니다. 일부 발언에는 대상 위치만 있으며, 이는 의도된 것입니다. 이렇게 하면 원래 위치가 지정되지 않았을 때 이러한 위치를 발언에 배치하는 방법을 LUIS가 쉽게 이해할 수 있습니다.     

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="create-a-location-entity"></a>위치 엔터티 만들기
LUIS는 발언의 원래 위치와 대상 위치에 레이블을 지정하여 위치를 이해해야 합니다. 토큰(원시) 보기의 발언을 살펴보아야 하는 경우 **엔터티 보기**라는 레이블이 붙은 발언 위에 있는 표시줄에서 토글을 선택합니다. 스위치를 토글하면 컨트롤에 **토큰 보기**라는 레이블이 지정됩니다.

다음 발화를 살펴보겠습니다.

```json
mv Jill Jones from a-2349 to b-1298
```

발언에는 두 위치 `a-2349` 및 `b-1298`이 있습니다. 문자는 건물 이름과 일치하고 숫자는 건물 내 사무실을 나타낸다고 가정합니다. 둘 다 계층적 엔터티의 자식인 `Locations`로 그룹화할 수 있습니다. 두 데이터 모두 발언에서 추출하여 클라이언트 애플리케이션의 요청을 완료해야 하고 서로 관련되어 있기 때문입니다. 
 
계층적 엔터티의 자식(원본 또는 대상)이 하나만 있는 경우에도 추출됩니다. 하나 또는 일부를 추출하기 위해 모든 자식을 검색할 필요가 없습니다. 

1. `Displace 425-555-0000 away from g-2323 toward hh-2345` 발화에서 `g-2323`이라는 단어를 선택합니다. 텍스트 상자가 맨 위에 있는 드롭다운 메뉴가 표시됩니다. 텍스트 상자에서 `Locations`이라는 엔터티 이름을 입력한 다음, 드롭다운 메뉴에서 **새 엔터티 만들기**를 선택합니다. 

    [![의도 페이지에서 새 엔터티 만들기의 스크린샷](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-1.png "의도 페이지에서 새 엔터티 만들기의 스크린샷")](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-1.png#lightbox)

2. 팝업 창에서 자식 엔터티로 `Origin` 및 `Destination`이 있는 **계층적** 엔터티 형식을 선택합니다. **완료**를 선택합니다.

    ![새 위치 엔터티에 대한 엔터티 만들기 팝업 대화 상자의 스크린샷](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-2.png "새 위치 엔터티에 대한 엔터티 만들기 팝업 대화 상자의 스크린샷")

3. LUIS에서 용어가 원본, 대상 또는 둘 다인지 인식하지 못하므로 `g-2323`에 대한 레이블은 `Locations`으로 표시됩니다. `g-2323`을 선택하고, **위치**를 선택하고, 오른쪽 메뉴를 따라 `Origin`을 선택합니다.

    [![위치 엔터티 자식을 변경하는 엔터티 레이블 지정 팝업 대화 상자의 스크린샷](media/luis-quickstart-intent-and-hier-entity/hr-label-entity.png "위치 엔터티 자식을 변경하는 엔터티 레이블 지정 팝업 대화 상자의 스크린샷")](media/luis-quickstart-intent-and-hier-entity/hr-label-entity.png#lightbox)

5. 발언에서 건물 및 사무실을 선택하고, 위치를 선택하고, 오른쪽 메뉴를 따라 `Origin` 또는 `Destination`을 선택하여 다른 모든 발언의 다른 위치에 레이블을 지정합니다. 모든 위치에 레이블이 지정되면 **토큰 보기**의 발언이 패턴처럼 보이기 시작합니다. 

    [![발화에서 레이블이 지정된 위치 엔터티의 스크린샷](media/luis-quickstart-intent-and-hier-entity/hr-entities-labeled.png "발화에서 레이블이 지정된 위치 엔터티의 스크린샷")](media/luis-quickstart-intent-and-hier-entity/hr-entities-labeled.png#lightbox)

## <a name="add-prebuilt-number-entity-to-app"></a>미리 작성된 숫자 엔터티를 앱에 추가
미리 작성된 숫자 엔터티를 다시 애플리케이션에 추가합니다.

1. 왼쪽 탐색 메뉴에서 **엔터티**를 선택합니다.

2. **미리 빌드된 엔터티 추가** 단추를 선택합니다.

3. 미리 작성된 엔터티 목록에서 **번호**를 선택하고 **완료**를 선택합니다.

    ![number가 선택된 미리 빌드된 엔터티 대화 상자의 스크린샷](./media/luis-quickstart-intent-and-hier-entity/hr-add-number-back-ddl.png)

## <a name="train-the-luis-app"></a>LUIS 앱 학습

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>앱을 게시하여 엔드포인트 URL 가져오기

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>다른 발화를 사용하여 엔드포인트 쿼리

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


2. 주소 표시줄의 URL 끝으로 이동하여 `Please relocation jill-jones@mycompany.com from x-2345 to g-23456`를 입력합니다. 마지막 쿼리 문자열 매개 변수는 발언 **쿼리**를 나타내는 `q`입니다. 이 발화는 레이블이 있는 발화와 같지 않으므로 좋은 테스트이므로 추출된 계층적 엔터티와 함께 `MoveEmployee` 의도를 반환해야 합니다.

    ```json
    {
      "query": "Please relocation jill-jones@mycompany.com from x-2345 to g-23456",
      "topScoringIntent": {
        "intent": "MoveEmployee",
        "score": 0.9966052
      },
      "intents": [
        {
          "intent": "MoveEmployee",
          "score": 0.9966052
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.0325253047
        },
        {
          "intent": "FindForm",
          "score": 0.006137873
        },
        {
          "intent": "GetJobInformation",
          "score": 0.00462633232
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.00415637763
        },
        {
          "intent": "ApplyForJob",
          "score": 0.00382325822
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00249120337
        },
        {
          "intent": "None",
          "score": 0.00130756292
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00119622645
        },
        {
          "intent": "Utilities.Confirm",
          "score": 1.26910036E-05
        }
      ],
      "entities": [
        {
          "entity": "jill - jones @ mycompany . com",
          "type": "Employee",
          "startIndex": 18,
          "endIndex": 41,
          "resolution": {
            "values": [
              "Employee-45612"
            ]
          }
        },
        {
          "entity": "x - 2345",
          "type": "Locations::Origin",
          "startIndex": 48,
          "endIndex": 53,
          "score": 0.8520272
        },
        {
          "entity": "g - 23456",
          "type": "Locations::Destination",
          "startIndex": 58,
          "endIndex": 64,
          "score": 0.974032
        },
        {
          "entity": "-2345",
          "type": "builtin.number",
          "startIndex": 49,
          "endIndex": 53,
          "resolution": {
            "value": "-2345"
          }
        },
        {
          "entity": "-23456",
          "type": "builtin.number",
          "startIndex": 59,
          "endIndex": 64,
          "resolution": {
            "value": "-23456"
          }
        }
      ]
    }
    ```
    
    올바른 의도가 예측되고 엔터티 배열은 해당 **entity** 속성에 원본 값과 대상 값이 모두 있습니다.
    

## <a name="could-you-have-used-a-regular-expression-for-each-location"></a>각 위치에 정규식을 사용할 수 있었나요?
예, 원본 및 대상 역할을 사용하여 정규식 엔터티를 만들고 패턴에 사용합니다.

`a-1234` 같은 이 예제의 위치는 문자 하나 또는 두 개 뒤에 대시를 붙이고 그 뒤에 일련의 4자리 또는 5자리 숫자를 붙이는 특정 형식을 따릅니다. 이 데이터를 각 위치에 대한 역할이 있는 정규식 엔터티로 설명할 수 있습니다. 역할은 패턴에만 사용할 수 있습니다. 이러한 발언을 기반으로 패턴을 만든 다음, 위치 형식에 대한 정규식 만들어 패턴에 추가할 수 있습니다. 

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="hierarchical-entities-versus-roles"></a>계층 구조 엔터티와 역할의 비교

자세한 내용은 [역할 및 계층 구조 엔터티](luis-concept-roles.md#roles-versus-hierarchical-entities)를 참조하세요.

## <a name="next-steps"></a>다음 단계
이 자습서에서는 새 의도를 만들고, 컨텍스트에 따라 학습된 원본 및 대상 위치 데이터에 대한 발언 예시를 추가했습니다. 앱이 학습되고 게시되면 클라이언트 응용 프로그램이 해당 정보를 사용하여 관련 정보가 포함된 이동 티켓을 만들 수 있습니다.

> [!div class="nextstepaction"] 
> [복합 엔터티를 추가하는 방법 알아보기](luis-tutorial-composite-entity.md) 
