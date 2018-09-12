---
title: 복잡한 데이터를 추출하는 복합 엔터티 만들기 자습서 - Azure | Microsoft Docs
description: LUIS 앱에서 복합 엔터티를 만들어 다른 형식의 엔터티 데이터를 추출하는 방법에 대해 알아봅니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: 17a8110624975d8053ad69c5bf30477e6d715ee8
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44159829"
---
# <a name="tutorial-6-add-composite-entity"></a>자습서: 6. 복합 엔터티 추가 
이 자습서에서는 복합 엔터티를 추가하여 추출된 데이터를 포함 엔터티로 그룹화합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

<!-- green checkmark -->
> [!div class="checklist"]
> * 복합 엔터티 이해 
> * 복합 엔터티를 추가하여 데이터 추출
> * 앱 학습 및 게시
> * 앱의 엔드포인트를 쿼리하여 LUIS JSON 응답 확인

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>시작하기 전에
[계층적 엔터티](luis-quickstart-intent-and-hier-entity.md) 자습서의 인사 관리 앱이 없으면 JSON을 [LUIS](luis-reference-regions.md#luis-website) 웹 사이트의 새 앱으로 [가져옵니다](luis-how-to-start-new-app.md#import-new-app). 가져올 앱은 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-hier-HumanResources.json) Github 리포지토리에 있습니다.

원래의 인사 관리 앱을 유지하려면 [설정](luis-how-to-manage-versions.md#clone-a-version) 페이지에서 버전을 복제하고 해당 이름을 `composite`로 지정합니다. 복제는 원래 버전에 영향을 주지 않고도 다양한 LUIS 기능을 사용할 수 있는 좋은 방법입니다.  

## <a name="composite-entity-is-a-logical-grouping"></a>복합 엔터티는 논리 그룹임 
복합 엔터티의 용도는 관련된 엔터티를 부모 범주 엔터티로 그룹화하것는 입니다. 복합이 만들어지기 전에 정보가 개별 엔터티로 존재합니다. 계층 구조 엔터티와 유사하지만 더 많은 형식의 엔터티를 포함할 수 있습니다. 

 개별 엔터티를 논리적으로 그룹화하고 이 논리 그룹이 클라이언트 응용 프로그램에 유용할 경우 복합 엔터티를 만듭니다. 

이 앱에서 직원 이름은 **Employee** 목록 엔터티에 정의되고 이름, 이메일 주소, 회사 전화 내선 번호, 휴대폰 번호 및 미국 연방 세금 ID의 동의어를 포함합니다. 

**MoveEmployee** 의도는 직원에게 한 건물에서 다른 건물의 사무실로 이전할 것을 요청하는 예제 발언을 포함합니다. 사무실 이름은 숫자 "1234", "13245"이지만 건물 이름은 알파벳 문자 "A", "B" 등으로 표시됩니다. 

**MoveEmployee** 의도의 예제 발언에는 다음이 포함됩니다.

|예제 발화|
|--|
|Move John W . Smith to a-2345|
|내일 x12345에서 h-1234로 이동|
 
이전 요청에는 적어도 직원(동의어 사용), 목적지 건물 및 사무실 위치가 포함되어야 합니다. 또한 요청에는 이전 날짜 뿐만 아니라 원래 사무실도 포함될 수 있습니다. 

끝점에서 추출된 데이터는 이 정보를 포함하며 `RequestEmployeeMove` 복합 엔터티에 반환해야 합니다. 

## <a name="create-composite-entity"></a>복합 엔터티 만들기
1. 인사 관리 앱이 LUIS의 **빌드** 섹션에 있는지 확인합니다. 오른쪽 위의 메뉴 표시줄에서 **빌드**를 선택하여 이 섹션으로 변경할 수 있습니다. 

2. **의도** 페이지에서 **MoveEmployee** 의도를 선택합니다. 

3. 도구 모음에서 돋보기 아이콘을 선택하여 발언 목록을 필터링합니다. 

    [![](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png "돋보기 단추가 강조 표시된 'MoveEmployee' 의도의 LUIS 스크린샷")](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png#lightbox)

4. 필터 입력란에 `tomorrow`를 입력하여 발언 `shift x12345 to h-1234 tomorrow`를 찾습니다.

    [![](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png "'tomorrow' 필터가 강조 표시된 'MoveEmployee' 의도의 LUIS 스크린샷")](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png#lightbox)

    다른 방법은 **엔터티 필터**를 선택한 후 목록에서 **datetimeV2**를 선택하여 datetimeV2를 기준으로 엔터티를 필터링하는 것입니다. 

5. 첫 번째 엔터티 `Employee`를 선택한 후 팝업 메뉴 목록에서 **복합 엔터티에 래핑**을 선택합니다. 

    [![](media/luis-tutorial-composite-entity/hr-create-entity-1.png "복합 요소에서 첫 번째 엔터티 선택이 강조 표시된 'MoveEmployee' 의도의 LUIS의 스크린 샷")](media/luis-tutorial-composite-entity/hr-create-entity-1.png#lightbox)


6. 그런 다음, 발언에서 마지막 엔터티 `datetimeV2`를 즉시 선택합니다. 선택한 단어 아래에 복합 엔터티를 나타내는 녹색 막대가 그려집니다. 팝업 메뉴에서 복합 이름 `RequestEmployeeMove`를 입력하고 팝업 메뉴에서 **새 복합 요소 만들기**를 선택합니다. 

    [![](media/luis-tutorial-composite-entity/hr-create-entity-2.png "복합 요소에서 마지막 엔터티 선택 및 엔터티 만들기가 강조 표시된 'MoveEmployee' 의도의 LUIS의 스크린 샷")](media/luis-tutorial-composite-entity/hr-create-entity-2.png#lightbox)

7. **What type of entity do you want to create?**(만들려는 엔터티 형식을 선택하세요.)에서는 필요한 거의 모든 필드가 목록에 표시됩니다. 원래 위치만 없습니다. **자식 엔터티 추가**를 선택하고 기존 엔터티 목록에서 **Locations::Origin**을 선택한 후 **완료**를 선택합니다. 

    ![팝업 창에서 다른 엔터티를 추가하는 'MoveEmployee' 의도의 LUIS 스크린샷](media/luis-tutorial-composite-entity/hr-create-entity-ddl.png)

8. 도구 모음에 있는 돋보기를 선택하여 필터를 제거합니다. 

## <a name="label-example-utterances-with-composite-entity"></a>복합 엔터티를 사용하여 예제 발언에 레이블 지정
1. 각 예제 발언에서 복합 요소로 지정되어야 하는 맨 왼쪽 엔터티를 선택합니다. 그런 후 **복합 엔터티에 래핑**을 선택합니다.

    [![](media/luis-tutorial-composite-entity/hr-label-entity-1.png "복합 요소에서 첫 번째 엔터티 선택이 강조 표시된 'MoveEmployee' 의도의 LUIS의 스크린 샷")](media/luis-tutorial-composite-entity/hr-label-entity-1.png#lightbox)

2. 복합 엔터티에서 마지막 단어를 선택한 다음, 팝업 메뉴에서 **RequestEmployeeMove**를 선택합니다. 

    [![](media/luis-tutorial-composite-entity/hr-label-entity-2.png "복합 요소에서 마지막 엔터티 선택이 강조 표시된 'MoveEmployee' 의도의 LUIS의 스크린 샷")](media/luis-tutorial-composite-entity/hr-label-entity-2.png#lightbox)

3. 의도의 모든 발언에 복합 엔터티 레이블이 지정되었는지 확인합니다. 

    [![](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png "모든 발언에 레이블이 지정된 'MoveEmployee'의 LUIS 스크린샷")](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png#lightbox)

## <a name="train-the-luis-app"></a>LUIS 앱 학습

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>앱을 게시하여 엔드포인트 URL 가져오기

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint"></a>끝점 쿼리 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 주소의 URL 끝으로 이동하고 `Move Jill Jones from a-1234 to z-2345 on March 3 2 p.m.`를 입력합니다. 마지막 쿼리 문자열 매개 변수는 `q`, 발화 쿼리입니다. 

    이 테스트는 복합 요소가 올바르게 추출되었는지 확인하기 위한 것이므로 테스트에 기존의 샘플 발언이나 새 발언이 포함될 수 있습니다. 복합 엔터티에 모든 자식 엔터티를 포함하면 테스트 효과가 좋습니다.

    ```JSON
    {
      "query": "Move Jill Jones from a-1234 to z-2345 on March 3  2 p.m",
      "topScoringIntent": {
        "intent": "MoveEmployee",
        "score": 0.9959525
      },
      "intents": [
        {
          "intent": "MoveEmployee",
          "score": 0.9959525
        },
        {
          "intent": "GetJobInformation",
          "score": 0.009858314
        },
        {
          "intent": "ApplyForJob",
          "score": 0.00728598563
        },
        {
          "intent": "FindForm",
          "score": 0.0058053555
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.005371796
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00266987388
        },
        {
          "intent": "None",
          "score": 0.00123299169
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00116407464
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00102653319
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.0006628214
        }
      ],
      "entities": [
        {
          "entity": "march 3 2 p.m",
          "type": "builtin.datetimeV2.datetime",
          "startIndex": 41,
          "endIndex": 54,
          "resolution": {
            "values": [
              {
                "timex": "XXXX-03-03T14",
                "type": "datetime",
                "value": "2018-03-03 14:00:00"
              },
              {
                "timex": "XXXX-03-03T14",
                "type": "datetime",
                "value": "2019-03-03 14:00:00"
              }
            ]
          }
        },
        {
          "entity": "jill jones",
          "type": "Employee",
          "startIndex": 5,
          "endIndex": 14,
          "resolution": {
            "values": [
              "Employee-45612"
            ]
          }
        },
        {
          "entity": "z - 2345",
          "type": "Locations::Destination",
          "startIndex": 31,
          "endIndex": 36,
          "score": 0.9690751
        },
        {
          "entity": "a - 1234",
          "type": "Locations::Origin",
          "startIndex": 21,
          "endIndex": 26,
          "score": 0.9713137
        },
        {
          "entity": "-1234",
          "type": "builtin.number",
          "startIndex": 22,
          "endIndex": 26,
          "resolution": {
            "value": "-1234"
          }
        },
        {
          "entity": "-2345",
          "type": "builtin.number",
          "startIndex": 32,
          "endIndex": 36,
          "resolution": {
            "value": "-2345"
          }
        },
        {
          "entity": "3",
          "type": "builtin.number",
          "startIndex": 47,
          "endIndex": 47,
          "resolution": {
            "value": "3"
          }
        },
        {
          "entity": "2",
          "type": "builtin.number",
          "startIndex": 50,
          "endIndex": 50,
          "resolution": {
            "value": "2"
          }
        },
        {
          "entity": "jill jones from a - 1234 to z - 2345 on march 3 2 p . m",
          "type": "requestemployeemove",
          "startIndex": 5,
          "endIndex": 54,
          "score": 0.4027723
        }
      ],
      "compositeEntities": [
        {
          "parentType": "requestemployeemove",
          "value": "jill jones from a - 1234 to z - 2345 on march 3 2 p . m",
          "children": [
            {
              "type": "builtin.datetimeV2.datetime",
              "value": "march 3 2 p.m"
            },
            {
              "type": "Locations::Destination",
              "value": "z - 2345"
            },
            {
              "type": "Employee",
              "value": "jill jones"
            },
            {
              "type": "Locations::Origin",
              "value": "a - 1234"
            }
          ]
        }
      ],
      "sentimentAnalysis": {
        "label": "neutral",
        "score": 0.5
      }
    }
    ```

  이 발언은 복합 엔터티 배열을 반환합니다. 각 엔터티에는 형식 및 값이 지정됩니다. 각 자식 엔터티를 보다 정확하게 찾으려면 복합 배열 항목의 형식 및 값을 조합해서 엔터티 배열에서 해당 항목을 찾습니다.  

## <a name="what-has-this-luis-app-accomplished"></a>이 LUIS 앱에서 수행한 작업은?
이 앱에서는 자연어 쿼리 의도를 확인하고 추출된 데이터를 명명된 그룹으로 반환했습니다. 

챗봇에는 이제 기본 작업 및 발언의 관련 세부 사항을 확인하기 위한 충분한 정보가 있습니다. 

## <a name="where-is-this-luis-data-used"></a>이 LUIS 데이터가 사용되는 위치는? 
LUIS는 이 요청을 통해 수행됩니다. 챗봇과 같은 호출 응용 프로그램에서는 topScoringIntent 결과와 엔터티의 데이터를 사용하여 다음 단계를 수행할 수 있습니다. LUIS는 봇 또는 호출 응용 프로그램에 대해 프로그래밍 방식으로 작동하지 않습니다. LUIS는 사용자의 의도가 무엇인지만 결정합니다. 

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"] 
> [구 목록을 사용하여 단순 엔터티를 추가하는 방법 알아보기](luis-quickstart-primary-and-secondary-data.md)  