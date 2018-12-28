---
title: 복합 엔터티”
titleSuffix: Azure Cognitive Services
description: 복합 엔터티를 추가하여 다양한 형식의 추출된 데이터를 단일 포함 엔터티로 묶습니다. 데이터를 묶어서 클라이언트 응용 프로그램이 다른 데이터 형식의 관련된 데이터를 쉽게 추출할 수 있습니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: b5923d5cd4a704dda76e33ee6a2b76cfd903219d
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53079214"
---
# <a name="tutorial-6-group-and-extract-related-data"></a>자습서 6: 관련된 데이터 그룹화 및 추출
이 자습서에서는 복합 엔터티를 추가하여 다양한 형식의 추출된 데이터를 단일 포함 엔터티로 묶습니다. 데이터를 묶어서 클라이언트 응용 프로그램이 다른 데이터 형식의 관련된 데이터를 쉽게 추출할 수 있습니다.

복합 엔터티의 용도는 관련된 엔터티를 부모 범주 엔터티로 그룹화하것는 입니다. 복합이 만들어지기 전에 정보가 개별 엔터티로 존재합니다. 계층 구조 엔터티와 유사하지만 다른 형식의 엔터티를 포함할 수 있습니다. 

복합 엔터티는 다음과 같은 데이터로 인해 이 형식의 데이터에 적합합니다.

* 서로 관련이 있습니다. 
* 다양한 엔터티 형식을 사용합니다.
* 클라이언트 앱에서 정보 단위로 그룹화하고 처리되어야 합니다.

**이 자습서에서는 다음 방법에 대해 알아봅니다.**

<!-- green checkmark -->
> [!div class="checklist"]
> * 기존 자습서 앱 사용
> * 복합 엔터티 추가 
> * 학습
> * 게시
> * 엔드포인트에서 의도 및 엔터티 가져오기

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>기존 앱 사용
마지막 자습서에서 만든 **HumanResources**라는 앱을 사용하여 계속 진행합니다. 

이전 자습서의 HumanResources 앱이 없으면 다음 단계를 사용합니다.

1.  [앱 JSON 파일](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-hier-HumanResources.json)을 다운로드하고 저장합니다.

2. JSON을 새 앱으로 가져옵니다.

3. **관리** 섹션의 **버전** 탭에서 버전을 복제하고 `composite`라는 이름을 지정합니다. 복제는 원래 버전에 영향을 주지 않고도 다양한 LUIS 기능을 사용할 수 있는 좋은 방법입니다. 버전 이름이 URL 경로의 일부로 사용되므로 이름에는 URL에 유효하지 않은 문자가 포함될 수 없습니다.


## <a name="composite-entity"></a>복합 엔터티
개별 엔터티를 논리적으로 그룹화하고 이 논리 그룹이 클라이언트 응용 프로그램에 유용할 경우 복합 엔터티를 만듭니다. 

이 앱에서 직원 이름은 **Employee** 목록 엔터티에 정의되고 이름, 이메일 주소, 회사 전화 내선 번호, 휴대폰 번호 및 미국 연방 세금 ID의 동의어를 포함합니다. 

**MoveEmployee** 의도는 직원에게 한 건물에서 다른 건물의 사무실로 이전할 것을 요청하는 예제 발언을 포함합니다. 빌딩 이름은 알파벳으로 “A”, “B” 등이며 사무실은 숫자로 “1234”, “13245”입니다. 

**MoveEmployee** 의도의 예제 발언에는 다음이 포함됩니다.

|예제 발화|
|--|
|Move John W . Smith to a-2345|
|내일 x12345에서 h-1234로 이동|
 
이전 요청에는 직원(동의어 사용), 목적지 건물 및 사무실 위치가 포함되어야 합니다. 또한 요청에는 이전 날짜 뿐만 아니라 원래 사무실도 포함될 수 있습니다. 

엔드포인트에서 추출된 데이터는 이 정보를 포함하며 `RequestEmployeeMove` 복합 엔터티에 반환해야 합니다.

```json
"compositeEntities": [
  {
    "parentType": "RequestEmployeeMove",
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
]
```

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. **의도** 페이지에서 **MoveEmployee** 의도를 선택합니다. 

3. 도구 모음에서 돋보기 아이콘을 선택하여 발언 목록을 필터링합니다. 

    [![강조 표시된 돋보기 단추를 사용하는 ‘MoveEmployee’ 의도에서 LUIS의 스크린샷](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png "강조 표시된 돋보기 단추를 사용하는 ‘MoveEmployee’ 의도에서 LUIS의 스크린샷")](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png#lightbox)

4. 필터 입력란에 `tomorrow`를 입력하여 발언 `shift x12345 to h-1234 tomorrow`를 찾습니다.

    [![강조 표시된 ‘tomorrow’ 필터링을 사용한 ‘MoveEmployee’ 의도에서 LUIS의 스크린샷](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png "강조 표시된 ‘tomorrow’ 필터링을 사용한 ‘MoveEmployee’ 의도에서 LUIS의 스크린샷")](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png#lightbox)

    다른 방법은 **엔터티 필터**를 선택한 후 목록에서 **datetimeV2**를 선택하여 datetimeV2를 기준으로 엔터티를 필터링하는 것입니다. 

5. 첫 번째 엔터티 `Employee`를 선택한 후 팝업 메뉴 목록에서 **복합 엔터티에 래핑**을 선택합니다. 

    [![강조 표시된 복합 중 첫 번째 엔터티를 선택한 ‘MoveEmployee’ 의도에서 LUIS의 스크린샷](media/luis-tutorial-composite-entity/hr-create-entity-1.png "강조 표시된 복합 중 첫 번째 엔터티를 선택한 ‘MoveEmployee’ 의도에서 LUIS의 스크린샷")](media/luis-tutorial-composite-entity/hr-create-entity-1.png#lightbox)


6. 그런 다음, 발언에서 마지막 엔터티 `datetimeV2`를 즉시 선택합니다. 선택한 단어 아래에 복합 엔터티를 나타내는 녹색 막대가 그려집니다. 팝업 메뉴에서 `RequestEmployeeMove` 복합 이름을 입력한 다음, 입력을 선택합니다. 

    [![강조 표시된 복합 중 마지막 엔터티를 선택하고 엔터티를 생성하는 ‘MoveEmployee’ 의도에서 LUIS의 스크린샷](media/luis-tutorial-composite-entity/hr-create-entity-2.png "강조 표시된 복합 중 마지막 엔터티를 선택하고 엔터티를 생성하는 ‘MoveEmployee’ 의도에서 LUIS의 스크린샷")](media/luis-tutorial-composite-entity/hr-create-entity-2.png#lightbox)

7. **What type of entity do you want to create?**(만들려는 엔터티 형식을 선택하세요.)에서는 필요한 거의 모든 필드가 목록에 표시됩니다. 원래 위치만 없습니다. **자식 엔터티 추가**를 선택하고 기존 엔터티 목록에서 **Locations::Origin**을 선택한 후 **완료**를 선택합니다. 

    미리 빌드된 엔터티인 숫자가 복합 엔터티에 추가되었습니다. 미리 빌드된 엔터티가 복합 엔터티의 시작 및 끝 토큰 사이에 표시되는 경우 복합 엔터티에는 미리 빌드된 엔터티가 포함되어야 합니다. 미리 빌드된 엔터티를 포함하지 않는 경우 복합 엔터티는 올바르게 예측되지 않지만 각 개별 요소는 올바르게 예측됩니다.

    ![팝업 창에서 다른 엔터티를 추가하는 'MoveEmployee' 의도의 LUIS 스크린샷](media/luis-tutorial-composite-entity/hr-create-entity-ddl.png)

8. 도구 모음에 있는 돋보기를 선택하여 필터를 제거합니다. 

9. 필터에서 `tomorrow` 단어를 제거하므로 모든 예제 발화를 다시 표시할 수 있습니다. 

## <a name="label-example-utterances-with-composite-entity"></a>복합 엔터티를 사용하여 예제 발언에 레이블 지정


1. 각 예제 발언에서 복합 요소로 지정되어야 하는 맨 왼쪽 엔터티를 선택합니다. 그런 후 **복합 엔터티에 래핑**을 선택합니다.

    [![강조 표시된 복합 중 첫 번째 엔터티를 선택한 ‘MoveEmployee’ 의도에서 LUIS의 스크린샷](media/luis-tutorial-composite-entity/hr-label-entity-1.png "강조 표시된 복합 중 첫 번째 엔터티를 선택한 ‘MoveEmployee’ 의도에서 LUIS의 스크린샷")](media/luis-tutorial-composite-entity/hr-label-entity-1.png#lightbox)

2. 복합 엔터티에서 마지막 단어를 선택한 다음, 팝업 메뉴에서 **RequestEmployeeMove**를 선택합니다. 

    [![강조 표시된 복합 중 마지막 엔터티를 선택한 ‘MoveEmployee’ 의도에서 LUIS의 스크린샷](media/luis-tutorial-composite-entity/hr-label-entity-2.png "강조 표시된 복합 중 마지막 엔터티를 선택한 ‘MoveEmployee’ 의도에서 LUIS의 스크린샷")](media/luis-tutorial-composite-entity/hr-label-entity-2.png#lightbox)

3. 의도의 모든 발언에 복합 엔터티 레이블이 지정되었는지 확인합니다. 

    [![레이블이 지정된 모든 발언을 사용하는 ‘MoveEmployee’에서 LUIS의 스크린샷](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png "레이블이 지정된 모든 발언을 사용하는 ‘MoveEmployee’에서 LUIS의 스크린샷")](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png#lightbox)

## <a name="train"></a>학습

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>게시

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>엔드포인트에서 의도 및 엔터티 가져오기 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 주소의 URL 끝으로 이동하고 `Move Jill Jones from a-1234 to z-2345 on March 3 2 p.m.`를 입력합니다. 마지막 쿼리 문자열 매개 변수는 `q`, 발화 쿼리입니다. 

    이 테스트는 복합 요소가 올바르게 추출되었는지 확인하기 위한 것이므로 테스트에 기존의 샘플 발언이나 새 발언이 포함될 수 있습니다. 복합 엔터티에 모든 자식 엔터티를 포함하면 테스트 효과가 좋습니다.

    ```json
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
          "type": "RequestEmployeeMove",
          "startIndex": 5,
          "endIndex": 54,
          "score": 0.4027723
        }
      ],
      "compositeEntities": [
        {
          "parentType": "RequestEmployeeMove",
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
      ]
    }
    ```

  이 발언은 복합 엔터티 배열을 반환합니다. 각 엔터티에는 형식 및 값이 지정됩니다. 각 자식 엔터티를 보다 정확하게 찾으려면 복합 배열 항목의 형식 및 값을 조합해서 엔터티 배열에서 해당 항목을 찾습니다.  

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 복합 엔터티를 생성하여 기존 엔터티를 캡슐화합니다. 이렇게 하면 클라이언트 응용 프로그램이 다른 데이터 형식에서 관련된 데이터 그룹을 찾아서 대화를 계속할 수 있습니다. 이 인사 관리 앱에 대한 클라이언트 응용 프로그램은 이동이 시작하고 종료해야 하는 날짜 및 시간을 질문할 수 있습니다. 실제 휴대폰과 같은 다른 물류의 이동에 대해 질문할 수도 있습니다. 

> [!div class="nextstepaction"] 
> [구 목록을 사용하여 단순 엔터티를 추가하는 방법 알아보기](luis-quickstart-primary-and-secondary-data.md)  
