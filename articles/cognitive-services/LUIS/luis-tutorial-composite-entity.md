---
title: 복합 엔터티”
titleSuffix: Azure Cognitive Services
description: 복합 엔터티를 추가하여 다양한 형식의 추출된 데이터를 단일 포함 엔터티로 묶습니다. 데이터를 묶어서 클라이언트 애플리케이션이 다른 데이터 형식의 관련된 데이터를 쉽게 추출할 수 있습니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 0d78c365b171ea80d208c447f4746fe80b965ef2
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55883307"
---
# <a name="tutorial-group-and-extract-related-data"></a>자습서: 관련된 데이터 그룹화 및 추출
이 자습서에서는 복합 엔터티를 추가하여 다양한 형식의 추출된 데이터를 단일 포함 엔터티로 묶습니다. 데이터를 묶어서 클라이언트 애플리케이션이 다른 데이터 형식의 관련된 데이터를 쉽게 추출할 수 있습니다.

복합 엔터티의 용도는 관련된 엔터티를 부모 범주 엔터티로 그룹화하것는 입니다. 복합이 만들어지기 전에 정보가 개별 엔터티로 존재합니다. 계층 구조 엔터티와 유사하지만 다른 형식의 엔터티를 포함할 수 있습니다. 

복합 엔터티는 다음과 같은 데이터로 인해 이 형식의 데이터에 적합합니다.

* 서로 관련이 있습니다. 
* 다양한 엔터티 형식을 사용합니다.
* 클라이언트 앱에서 정보 단위로 그룹화하고 처리되어야 합니다.

**이 자습서에서 학습할 내용은 다음과 같습니다.**

<!-- green checkmark -->
> [!div class="checklist"]
> * 예제 앱 가져오기
> * 의도 만들기
> * 복합 엔터티 추가 
> * 학습
> * 게시
> * 엔드포인트에서 의도 및 엔터티 가져오기

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>예제 앱 가져오기

1.  목록 엔터티 자습서에서 [앱 JSON 파일](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/tutorial_list.json)을 다운로드하여 저장합니다.

2. JSON을 새 앱으로 가져옵니다.

3. **관리** 섹션의 **버전** 탭에서 버전을 복제하고 `composite`라는 이름을 지정합니다. 복제는 원래 버전에 영향을 주지 않고도 다양한 LUIS 기능을 사용할 수 있는 좋은 방법입니다. 버전 이름이 URL 경로의 일부로 사용되므로 이름에는 URL에 유효하지 않은 문자가 포함될 수 없습니다.

## <a name="composite-entity"></a>복합 엔터티

이 앱에서는 부서 이름이 **부서** 목록 엔터티에서 정의되며 동의어를 포함합니다. 

**TransferEmployeeToDepartment** 의도에는 직원에게 새 부서로 이동할 것을 요청하는 예제 발화가 있습니다. 

이 의도의 예제 발화는 다음과 같습니다.

|예제 발화|
|--|
|John W. Smith는 회계 부서로 이동|
|Jill Jones는 R&D 부서로 이동|
 
부서 이동 요청에는 부서 이름과 직원 이름이 포함되어야 합니다. 

## <a name="add-the-personname-prebuilt-entity-to-help-with-common-data-type-extraction"></a>일반적인 데이터 형식 추출에 도움이 되도록 미리 빌드된 PersonName 엔터티 추가

LUIS는 일반적인 데이터 추출을 위해 여러 가지 미리 빌드된 엔터티를 제공합니다. 

1. 위쪽 탐색 메뉴에서 **빌드**를 선택한 후 왼쪽 탐색 메뉴에서 **엔터티**를 선택합니다.

1. **미리 빌드된 엔터티 관리** 단추를 선택합니다.

1. 미리 빌드된 엔터티 목록에서 **[PersonName](luis-reference-prebuilt-person.md)** 을 선택한 후 **완료**를 선택합니다.

    ![number가 선택된 미리 빌드된 엔터티 대화 상자의 스크린샷](./media/luis-tutorial-composite-entity/add-personname-prebuilt-entity.png)

    이 엔터티를 사용하면 클라이언트 애플리케이션에 이름 인식을 추가할 수 있습니다.

## <a name="create-composite-entity-from-example-utterances"></a>예제 발화로 복합 엔터티 만들기

1. 왼쪽 탐색에서 **의도**를 선택합니다.

1. 의도 목록에서 **TransferEmployeeToDepartment**를 선택합니다.

1. 첫 번째 발화에서 personName 엔터티 `John Jackson`을(를) 선택한 후, 다음 발화의 팝업 메뉴에서 **복합 엔터티 래핑 시작**을 선택합니다.

    `place John Jackson in engineering`

1. 그런 다음, 발언에서 마지막 엔터티 `engineering`를 즉시 선택합니다. 선택한 단어 아래에 복합 엔터티를 나타내는 녹색 막대가 그려집니다. 팝업 메뉴에서 `TransferEmployeeInfo` 복합 이름을 입력한 다음, 입력을 선택합니다. 

1. **어떤 형식의 엔터티를 만들고 싶으신가요?** 에서는 필요한 모든 필드, 즉 `personName`과(와) `Department`가(이) 목록에 표시됩니다. **완료**를 선택합니다. 

    미리 빌드된 personName 엔터티가 복합 엔터티에 추가되었습니다. 미리 빌드된 엔터티가 복합 엔터티의 시작 및 끝 토큰 사이에 표시되는 경우 복합 엔터티에는 미리 빌드된 엔터티가 포함되어야 합니다. 미리 빌드된 엔터티를 포함하지 않는 경우 복합 엔터티는 올바르게 예측되지 않지만 각 개별 요소는 올바르게 예측됩니다.

## <a name="label-example-utterances-with-composite-entity"></a>복합 엔터티를 사용하여 예제 발언에 레이블 지정


1. 각 예제 발언에서 복합 요소로 지정되어야 하는 맨 왼쪽 엔터티를 선택합니다. 그런 후 **복합 엔터티에 래핑**을 선택합니다.

1. 복합 엔터티에서 마지막 단어를 선택한 다음, 팝업 메뉴에서 **TransferEmployeeInfo**를 선택합니다. 

1. 의도의 모든 발언에 복합 엔터티 레이블이 지정되었는지 확인합니다. 

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>의도의 변경사항을 테스트할 수 있도록 앱 학습 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>학습을 거친 모델이 엔드포인트에서 쿼리할 수 있도록 앱 게시

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>엔드포인트에서 의도 및 엔터티 예측 가져오기 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 주소의 URL 끝으로 이동하고 `Move Jill Jones to DevOps`를 입력합니다. 마지막 쿼리 문자열 매개 변수는 `q`, 발화 쿼리입니다. 

    이 테스트는 복합 요소가 올바르게 추출되었는지 확인하기 위한 것이므로 테스트에 기존의 샘플 발언이나 새 발언이 포함될 수 있습니다. 복합 엔터티에 모든 자식 엔터티를 포함하면 테스트 효과가 좋습니다.

    ```json
    {
      "query": "Move Jill Jones to DevOps",
      "topScoringIntent": {
        "intent": "TransferEmployeeToDepartment",
        "score": 0.9882747
      },
      "intents": [
        {
          "intent": "TransferEmployeeToDepartment",
          "score": 0.9882747
        },
        {
          "intent": "None",
          "score": 0.00925369747
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "builtin.personName",
          "startIndex": 5,
          "endIndex": 14
        },
        {
          "entity": "devops",
          "type": "Department",
          "startIndex": 19,
          "endIndex": 24,
          "resolution": {
            "values": [
              "Development Operations"
            ]
          }
        },
        {
          "entity": "jill jones to devops",
          "type": "TransferEmployeeInfo",
          "startIndex": 5,
          "endIndex": 24,
          "score": 0.9607566
        }
      ],
      "compositeEntities": [
        {
          "parentType": "TransferEmployeeInfo",
          "value": "jill jones to devops",
          "children": [
            {
              "type": "builtin.personName",
              "value": "jill jones"
            },
            {
              "type": "Department",
              "value": "devops"
            }
          ]
        }
      ]
    }
    ```

  이 발언은 복합 엔터티 배열을 반환합니다. 각 엔터티에는 형식 및 값이 지정됩니다. 각 자식 엔터티를 보다 정확하게 찾으려면 복합 배열 항목의 형식 및 값을 조합해서 엔터티 배열에서 해당 항목을 찾습니다.  

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>관련 정보

* [목록 엔터티 자습서](luis-quickstart-intents-only.md)
* [복합 엔터티](luis-concept-entity-types.md) 개념 정보
* [학습 방법](luis-how-to-train.md)
* [게시 방법](luis-how-to-publish-app.md)
* [LUIS 포털에서 테스트하는 방법](luis-interactive-test.md)


## <a name="next-steps"></a>다음 단계

이 자습서에서는 복합 엔터티를 생성하여 기존 엔터티를 캡슐화합니다. 이렇게 하면 클라이언트 애플리케이션이 다른 데이터 형식에서 관련된 데이터 그룹을 찾아서 대화를 계속할 수 있습니다. 이 인사 관리 앱에 대한 클라이언트 애플리케이션은 이동이 시작하고 종료해야 하는 날짜 및 시간을 질문할 수 있습니다. 실물 휴대폰 같은 또 다른 물류 이동에 대해 질문할 수도 있습니다. 

> [!div class="nextstepaction"] 
> [구 목록을 사용하여 단순 엔터티를 추가하는 방법 알아보기](luis-quickstart-primary-and-secondary-data.md)  
