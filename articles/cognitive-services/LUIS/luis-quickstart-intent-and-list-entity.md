---
title: 정확히 일치하는 텍스트
titleSuffix: Azure Cognitive Services
description: 미리 정의된 항목 목록과 일치하는 데이터를 가져옵니다. 목록의 각 항목과 정확하게 일치하는 동의어가 있을 수 있음
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: bf4fd5d2a3a9bb06882dcd1b4674ccdf8ad894ee
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/02/2019
ms.locfileid: "53971412"
---
# <a name="tutorial-get-exact-text-matched-data-from-an-utterance"></a>자습서: 발언과 텍스트가 정확히 일치하는 데이터 구하기

이 자습서에서는 미리 정의된 항목 목록과 일치하는 엔터티 데이터를 가져오는 방법을 이해합니다. 

**이 자습서에서 학습할 내용은 다음과 같습니다.**

<!-- green checkmark -->
> [!div class="checklist"]
> * 앱 만들기
> * 의도 추가
> * 목록 엔터티 추가 
> * 학습 
> * 게시
> * 엔드포인트에서 의도 및 엔터티 가져오기

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="what-is-a-list-entity"></a>목록 엔터티란?

목록 엔터티는 발언의 단어와 정확히 일치하는 텍스트입니다. 

목록의 각 항목은 동의어 목록을 포함할 수 있습니다. 인사 관리 앱의 경우 회사 부서는 여러 가지 주요 정보(예: 공식 이름, 일반 약어 및 청구 부서 코드)로 식별할 수 있습니다. 

인사 관리 앱은 직원이 이전하는 부서를 결정해야 합니다. 

다음과 같은 경우 목록 엔터티는 이러한 종류의 데이터에 적합합니다.

* 데이터 값이 알려진 집합입니다.
* 집합이 이 엔터티 형식의 최대 LUIS [경계](luis-boundaries.md)를 초과하지 않습니다.
* 발언의 텍스트가 동의어 또는 정식 이름과 정확히 일치합니다. LUIS는 정확한 텍스트 일치를 벗어나는 목록을 사용하지 않습니다. 형태소 분석, 복수형 및 기타 변형은 목록 엔터티만으로는 확인되지 않습니다. 변형을 관리하려면 선택적인 텍스트 구문이 포함된 [패턴](luis-concept-patterns.md#syntax-to-mark-optional-text-in-a-template-utterance)을 사용하는 것이 좋습니다. 

## <a name="create-a-new-app"></a>새 앱 만들기

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-transfer-employees-to-a-different-department"></a>직원을 다른 부서로 이전하려는 의도 만들기

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. **새 의도 만들기**를 선택합니다. 

3. 팝업 대화 상자에서 `TransferEmployeeToDepartment`을 입력하고 **완료**를 선택합니다. 

    ![새 의도 만들기 대화 상자의 스크린샷](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-ddl.png)

4. 의도에 발화 예제를 추가합니다.

    |예제 발화|
    |--|
    |John W. Smith를 회계 부서로 이동|
    |Jill Jones를 R&D로 이전|
    |1234 부서에 Bill Bradstreet라는 새 멤버가 있습니다.|
    |John Jackson을 엔지니어링에 배치 |
    |Debra Doughtery를 내부 판매로 이전|
    |Jill Jones를 IT로 이동|
    |Alice Anderson을 DevOps로 이동|
    |Carl Chamerlin을 재무로|
    |Steve Standish를 1234로|
    |Tanner Thompson을 3456으로|

    [![예제 발언이 있는 의도의 스크린샷](media/luis-quickstart-intent-and-list-entity/intent-transfer-employee-to-department.png "예제 발언이 있는 의도의 스크린샷")](media/luis-quickstart-intent-and-list-entity/intent-transfer-employee-to-department.png#lightbox)

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="department-list-entity"></a>부서 목록 엔터티

**TransferEmployeeToDepartment** 의도에 예제 발언이 있으므로, LUIS는 부서가 무엇인지 이해해야 합니다. 

각 항목의 주 _정식_ 이름은 부서 이름입니다. 각 정식 이름의 동의어 예제는 다음과 같습니다. 

|정식 이름|동의어|
|--|--|
|회계|acct<br>accting<br>3456|
|개발 운영|Devops<br>4949|
|공학|eng<br>enging<br>4567|
|재무|fin<br>2020|
|정보 기술|IT<br>2323|
|내부 판매|isale<br>insale<br>1414|
|연구 및 개발|R&D<br>1234|

1. 왼쪽 패널에서 **엔터티**를 선택합니다.

1. **새 엔터티 만들기**를 선택합니다.

1. 엔터티 팝업 대화 상자에서 엔터티 이름으로 `Department`를 입력하고 엔터티 형식으로 **목록**을 입력합니다. **완료**를 선택합니다.  

    [![새 엔터티 만들기 대화 상자의 스크린샷](media/luis-quickstart-intent-and-list-entity/create-new-list-entity-named-department.png "새 엔터티 만들기 대화 상자의 스크린샷")](media/luis-quickstart-intent-and-list-entity/create-new-list-entity-named-department.png#lightbox)

1. 부서 엔터티 페이지에 `Accounting`을 새 값으로 입력합니다.

    [![값을 입력하는 스크린샷](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png "값을 입력하는 스크린샷")](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png#lightbox)

1. 동의어에는 이전 테이블의 동의어를 추가합니다.

    [![동의어를 입력하는 스크린샷](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png "동의어를 입력하는 스크린샷")](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png#lightbox)

1. 모든 정식 이름과 동의어를 계속 추가합니다. 

## <a name="add-example-utterances-to-the-none-intent"></a>None 의도에 예제 발언 추가 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>의도에 대한 변경 내용을 테스트할 수 있도록 앱 학습시키기 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>학습된 모델을 엔드포인트에서 쿼리할 수 있도록 앱 게시하기

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>엔드포인트에서 의도 및 엔터티 예측 가져오기

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

1. 주소의 URL 끝으로 이동하고 `shift Joe Smith to IT`를 입력합니다. 마지막 쿼리 문자열 매개 변수는 `q`, 발화 **q** 쿼리입니다. 이 발언은 레이블이 지정된 발언과 같지 않으므로 유용한 테스트이며, `Department`가 추출된 `TransferEmployeeToDepartment`를 반환해야 합니다.

  ```json
    {
      "query": "shift Joe Smith to IT",
      "topScoringIntent": {
        "intent": "TransferEmployeeToDepartment",
        "score": 0.9775754
      },
      "intents": [
        {
          "intent": "TransferEmployeeToDepartment",
          "score": 0.9775754
        },
        {
          "intent": "None",
          "score": 0.0154493852
        }
      ],
      "entities": [
        {
          "entity": "it",
          "type": "Department",
          "startIndex": 19,
          "endIndex": 20,
          "resolution": {
            "values": [
              "Information Technology"
            ]
          }
        }
      ]
    }
  ```

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>관련 정보

* [목록 엔터티](luis-concept-entity-types.md#list-entity) 개념 정보
* [학습시키는 방법](luis-how-to-train.md)
* [게시 방법](luis-how-to-publish-app.md)
* [LUIS 포털에서 테스트하는 방법](luis-interactive-test.md)


## <a name="next-steps"></a>다음 단계
이 자습서에서는 새 의도를 만들고, 예제 발언을 추가하고, 발언에서 정확하게 일치하는 텍스트를 추출하는 목록 엔터티를 만들었습니다. 교육 및 앱 게시를 마친 후 엔드포인트 쿼리가 의도를 식별하고 추출된 데이터를 반환했습니다.

이 앱을 계속 사용하여 [복합 엔터티를 추가](luis-tutorial-composite-entity.md)합니다.

> [!div class="nextstepaction"]
> [앱에 계층적 엔터티 추가](luis-quickstart-intent-and-hier-entity.md)

