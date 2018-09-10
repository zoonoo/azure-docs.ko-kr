---
title: 위치 데이터를 가져오는 LUIS 앱을 만드는 자습서 - Azure | Microsoft Docs
description: 이 자습서에서는 의도를 사용하는 간단한 LUIS 앱 및 데이터를 추출하는 계층적 엔터티를 만드는 방법을 알아봅니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: 65c7aabb984ad0a6b3e77d0f98003803821e06cc
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44158622"
---
# <a name="tutorial-5-add-hierarchical-entity"></a>자습서: 5. 계층적 엔터티 추가
이 자습서에서는 컨텍스트에 따라 관련 데이터 부분을 찾는 방법을 보여 주는 앱을 만듭니다. 

<!-- green checkmark -->
> [!div class="checklist"]
> * 계층적 엔터티 및 컨텍스트에 따라 학습된 자식에 대한 이해 
> * HR(인사 관리) 도메인에서 LUIS 앱 사용 
> * 원본 및 대상 자식이 있는 위치 계층적 엔터티 추가
> * 앱 학습 및 게시
> * 앱의 엔드포인트를 쿼리하여 계층적 자식이 포함된 LUIS JSON 응답 확인 

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>시작하기 전에
[목록 엔터티](luis-quickstart-intent-and-list-entity.md) 자습서의 인사 관리 앱이 없는 경우 JSON을 [LUIS](luis-reference-regions.md#luis-website) 웹 사이트의 새 앱으로 [가져옵니다](luis-how-to-start-new-app.md#import-new-app). 가져올 앱은 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-list-HumanResources.json) Github 리포지토리에 있습니다.

원래의 인사 관리 앱을 유지하려면 [설정](luis-how-to-manage-versions.md#clone-a-version) 페이지에서 버전을 복제하고 해당 이름을 `hier`로 지정합니다. 복제는 원래 버전에 영향을 주지 않고도 다양한 LUIS 기능을 사용할 수 있는 좋은 방법입니다. 

## <a name="purpose-of-the-app-with-this-entity"></a>이 엔터티를 사용하는 앱의 목적
이 앱은 원래 위치(건물 및 사무실)에서 대상 위치(건물 및 사무실)로 직원을 이동할 위치를 결정합니다. 계층적 엔터티를 사용하여 발언 내의 위치를 확인합니다. 

계층적 엔터티는 데이터의 다음 두 부분으로 인해 이 형식의 데이터에 적합합니다.

* 발언의 컨텍스트에서 서로 관련되어 있습니다.
* 특정 단어를 사용하여 각 위치를 나타냅니다. 이러한 단어의 예로 에서/(으)로, 떠나/~을(를) 향해, ~에서 꺼내/방향으로 등이 있습니다.
* 두 위치 모두는 종종 동일한 발화에 있습니다. 

**계층적** 개체의 목적은 컨텍스트에 따라 발화 내에서 관련 데이터를 찾는 것입니다. 다음 발화를 살펴보겠습니다.

```JSON
mv Jill Jones from a-2349 to b-1298
```
발언에는 두 위치 `a-2349` 및 `b-1298`이 있습니다. 문자는 건물 이름과 일치하고 숫자는 건물 내 사무실을 나타낸다고 가정합니다. 둘 다 계층적 엔터티의 자식인 `Locations`로 그룹화할 수 있습니다. 두 데이터 모두 발언에서 추출해야 하고 서로 관련되어 있기 때문입니다. 
 
계층적 엔터티의 자식(원본 또는 대상)이 하나만 있는 경우에도 추출됩니다. 하나 또는 일부를 추출하기 위해 모든 자식을 검색할 필요가 없습니다. 

## <a name="remove-prebuilt-number-entity-from-app"></a>앱에서 미리 작성된 숫자 엔터티를 제거합니다.
전체 발언을 살펴보고 계층적 자식을 표시하려면 미리 작성된 숫자 엔터티를 일시적으로 제거해야 합니다.

1. 인사 관리 앱이 LUIS의 **빌드** 섹션에 있는지 확인합니다. 오른쪽 위의 메뉴 표시줄에서 **빌드**를 선택하여 이 섹션으로 변경할 수 있습니다. 

2. 왼쪽 메뉴에서 **엔터티**를 선택합니다.

3. 목록에서 숫자 엔터티 오른쪽에 있는 줄임표(***...***) 단추를 선택합니다. **삭제**를 선택합니다. 

    [ ![숫자 미리 작성된 엔터티의 삭제 단추가 강조 표시된 엔터티 목록 페이지의 LUIS 앱 스크린샷](./media/luis-quickstart-intent-and-hier-entity/hr-delete-number-prebuilt.png)](./media/luis-quickstart-intent-and-hier-entity/hr-delete-number-prebuilt.png#lightbox)


## <a name="add-utterances-to-moveemployee-intent"></a>MoveEmployee 의도에 발언 추가

1. 왼쪽 메뉴에서 **의도**를 선택합니다.

2. 의도 목록에서 **MoveEmployee**를 선택합니다.

    [ ![왼쪽 메뉴에서 MoveEmployee 의도가 강조 표시된 LUIS 앱의 스크린 샷](./media/luis-quickstart-intent-and-hier-entity/hr-intents-list-moveemployee.png)](./media/luis-quickstart-intent-and-hier-entity/hr-intents-list-moveemployee.png#lightbox)

3. 다음 예제 발언을 추가합니다.

    |예제 발화|
    |--|
    |John W. Smith를 a-2345**로** 이동|
    |Jill Jones를 b-3499**로** 이동|
    |x23456을 hh-2345**에서** e-0234**로** 이동하도록 구성|
    |x12345가 a-3459를 **떠나서** f-34567로 **향하도록** 설정하는 서류 작업 시작|
    |425-555-0000을 g-2323**에서 꺼내** hh-2345 **방향으로** 배치|

    [ ![MoveEmployee 의도에 새 발언이 있는 LUIS의 스크린샷](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png)](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png#lightbox)

    [엔터티 목록](luis-quickstart-intent-and-list-entity.md) 자습서에서 직원 이름, 이메일 주소, 내선 번호, 휴대폰 번호 또는 미국 사회 보장 번호를 사용하여 직원을 지정할 수 있습니다. 이러한 직원 번호는 발언에 사용됩니다. 이전 예제 발언에는 원래 위치와 대상 위치를 알려주는 여러 방법이 포함되어 있으며, 볼드로 표시되어 있습니다. 일부 발언에는 대상 위치만 있으며, 이는 의도된 것입니다. 이렇게 하면 원래 위치가 지정되지 않았을 때 이러한 위치를 발언에 배치하는 방법을 LUIS가 쉽게 이해할 수 있습니다.     

## <a name="create-a-location-entity"></a>위치 엔터티 만들기
LUIS는 발언의 원래 위치와 대상 위치에 레이블을 지정하여 위치를 이해해야 합니다. 토큰(원시) 보기의 발언을 살펴보아야 하는 경우 **엔터티 보기**라는 레이블이 붙은 발언 위에 있는 표시줄에서 토글을 선택합니다. 스위치를 토글하면 컨트롤에 **토큰 보기**라는 레이블이 지정됩니다.

1. `Displace 425-555-0000 away from g-2323 toward hh-2345` 발화에서 `g-2323`이라는 단어를 선택합니다. 텍스트 상자가 맨 위에 있는 드롭다운 메뉴가 표시됩니다. 텍스트 상자에서 `Locations`이라는 엔터티 이름을 입력한 다음, 드롭다운 메뉴에서 **새 엔터티 만들기**를 선택합니다. 

    [![](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-1.png "의도 페이지에서 새 엔터티 만들기의 스크린샷")](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-1.png#lightbox)

2. 팝업 창에서 자식 엔터티로 `Origin` 및 `Destination`이 있는 **계층적** 엔터티 형식을 선택합니다. **완료**를 선택합니다.

    ![](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-2.png "새 위치 엔터티에 대한 엔터티 만들기 팝업 대화 상자의 스크린샷")

3. LUIS에서 용어가 원본, 대상 또는 둘 다인지 인식하지 못하므로 `g-2323`에 대한 레이블은 `Locations`으로 표시됩니다. `g-2323`을 선택하고, **위치**를 선택하고, 오른쪽 메뉴를 따라 `Origin`을 선택합니다.

    [![](media/luis-quickstart-intent-and-hier-entity/hr-label-entity.png "위치 엔터티 자식을 변경하는 엔터티 레이블 지정 팝업 대화 상자의 스크린샷")](media/luis-quickstart-intent-and-hier-entity/hr-label-entity.png#lightbox)

5. 발언에서 건물 및 사무실을 선택하고, 위치를 선택하고, 오른쪽 메뉴를 따라 `Origin` 또는 `Destination`을 선택하여 다른 모든 발언의 다른 위치에 레이블을 지정합니다. 모든 위치에 레이블이 지정되면 **토큰 보기**의 발언이 패턴처럼 보이기 시작합니다. 

    [![](media/luis-quickstart-intent-and-hier-entity/hr-entities-labeled.png "발언에서 레이블이 지정된 위치 엔터티의 스크린샷")](media/luis-quickstart-intent-and-hier-entity/hr-entities-labeled.png#lightbox)

## <a name="add-prebuilt-number-entity-to-app"></a>미리 작성된 숫자 엔터티를 앱에 추가
미리 작성된 숫자 엔터티를 다시 응용 프로그램에 추가합니다.

1. 왼쪽 탐색 메뉴에서 **엔터티**를 선택합니다.

2. **미리 빌드된 엔터티 관리** 단추를 선택합니다.

    [ ![미리 작성된 엔터티 관리가 강조 표시된 엔터티 목록의 스크린샷](./media/luis-quickstart-intent-and-hier-entity/hr-manage-prebuilt-button.png)](./media/luis-quickstart-intent-and-hier-entity/hr-manage-prebuilt-button.png#lightbox)

3. 미리 작성된 엔터티 목록에서 **번호**를 선택하고 **완료**를 선택합니다.

    ![number가 선택된 미리 빌드된 엔터티 대화 상자의 스크린샷](./media/luis-quickstart-intent-and-hier-entity/hr-add-number-back-ddl.png)

## <a name="train-the-luis-app"></a>LUIS 앱 학습

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>앱을 게시하여 엔드포인트 URL 가져오기

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>다른 발화를 사용하여 엔드포인트 쿼리

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


2. 주소 표시줄의 URL 끝으로 이동하여 `Please relocation jill-jones@mycompany.com from x-2345 to g-23456`를 입력합니다. 마지막 쿼리 문자열 매개 변수는 발언 **쿼리**를 나타내는 `q`입니다. 이 발화는 레이블이 있는 발화와 같지 않으므로 좋은 테스트이므로 추출된 계층적 엔터티와 함께 `MoveEmployee` 의도를 반환해야 합니다.

  ```JSON
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

## <a name="could-you-have-used-a-regular-expression-for-each-location"></a>각 위치에 정규식을 사용할 수 있었나요?
예, 원본 및 대상 역할을 사용하여 정규식을 만들고 패턴에 사용합니다.

`a-1234` 같은 이 예제의 위치는 문자 하나 또는 두 개 뒤에 대시를 붙이고 그 뒤에 일련의 4자리 또는 5자리 숫자를 붙이는 특정 형식을 따릅니다. 이 데이터를 각 위치에 대한 역할이 있는 정규식 엔터티로 설명할 수 있습니다. 역할은 패턴에 사용할 수 있습니다. 이러한 발언을 기반으로 패턴을 만든 다음, 위치 형식에 대한 정규식 만들어 패턴에 추가할 수 있습니다. <!-- Go to this tutorial to see how that is done -->

## <a name="patterns-with-roles"></a>역할을 사용하는 패턴

[!INCLUDE [LUIS Compare hierarchical entities to patterns with roles](../../../includes/cognitive-services-luis-hier-roles.md)]

## <a name="what-has-this-luis-app-accomplished"></a>이 LUIS 앱에서 수행한 작업은?
이 앱은 고작 몇 개의 의도와 계층적 엔터티를 사용하여 자연어 쿼리 의도를 확인하고 추출된 데이터를 반환했습니다. 

챗봇에는 이제 `MoveEmployee` 기본 작업 및 발화에서 찾은 위치 정보를 결정하는 데 충분한 정보가 있습니다. 

## <a name="where-is-this-luis-data-used"></a>이 LUIS 데이터가 사용되는 위치는? 
LUIS는 이 요청을 통해 수행됩니다. 챗봇과 같은 호출 응용 프로그램에서는 topScoringIntent 결과와 엔터티의 데이터를 사용하여 다음 단계를 수행할 수 있습니다. LUIS는 봇 또는 호출 응용 프로그램에 대해 프로그래밍 방식으로 작동하지 않습니다. LUIS는 사용자의 의도가 무엇인지만 결정합니다. 

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"] 
> [복합 엔터티를 추가하는 방법 알아보기](luis-tutorial-composite-entity.md) 