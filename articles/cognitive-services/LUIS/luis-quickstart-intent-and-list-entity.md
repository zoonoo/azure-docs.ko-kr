---
title: '자습서 4: 정확한 텍스트 일치 - LUIS 목록 엔터티'
titleSuffix: Azure Cognitive Services
description: 미리 정의된 항목 목록과 일치하는 데이터를 가져옵니다. 목록의 각 항목과 정확하게 일치하는 동의어가 있을 수 있음
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: a4e294687b6c3ea2ba6ff8003e7a8f1ac69ea639
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52425074"
---
# <a name="tutorial-4-extract-exact-text-matches"></a>자습서 4: 정확하게 일치하는 텍스트 추출
이 자습서에서는 미리 정의된 항목 목록과 일치하는 데이터를 가져오는 방법을 설명합니다. 목록의 각 항목은 동의어 목록을 포함할 수 있습니다. 인사 관리 앱의 경우 이름, 이메일, 전화 번호, 미국 연방 세금 ID 등의 여러 핵심 정보 조각으로 직원을 식별할 수 있습니다. 

인사 관리 앱은 한 건물에서 다른 건물로 이동하는 직원이 누구인지 확인해야 합니다. 직원 이동에 대한 발언의 경우 LUIS는 의도를 확인하고, 직원을 이동하는 표준 순서를 클라이언트 응용 프로그램에서 만들 수 있도록 직원을 추출합니다.

이 앱은 목록 엔터티를 사용하여 직원을 추출합니다. 이름, 회사 내선 번호, 휴대폰 번호, 이메일 또는 미국 사회 보장 번호를 사용하여 직원을 참조할 수 있습니다. 

다음과 같은 경우 목록 엔터티는 이러한 종류의 데이터에 적합합니다.

* 데이터 값이 알려진 집합입니다.
* 집합이 이 엔터티 형식의 최대 LUIS [경계](luis-boundaries.md)를 초과하지 않습니다.
* 발언의 텍스트가 동의어 또는 정식 이름과 정확히 일치합니다. 

**이 자습서에서는 다음 방법에 대해 알아봅니다.**

<!-- green checkmark -->
> [!div class="checklist"]
> * 기존 자습서 앱 사용
> * MoveEmployee 의도 추가
> * 목록 엔터티 추가 
> * 학습 
> * 게시
> * 엔드포인트에서 의도 및 엔터티 가져오기

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>기존 앱 사용
마지막 자습서에서 만든 **HumanResources**라는 앱을 사용하여 계속 진행합니다. 

이전 자습서의 HumanResources 앱이 없으면 다음 단계를 사용합니다.

1.  [앱 JSON 파일](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-regex-HumanResources.json)을 다운로드하고 저장합니다.

2. JSON을 새 앱으로 가져옵니다.

3. **관리** 섹션의 **버전** 탭에서 버전을 복제하고 `list`라는 이름을 지정합니다. 복제는 원래 버전에 영향을 주지 않고도 다양한 LUIS 기능을 사용할 수 있는 좋은 방법입니다. 버전 이름이 URL 경로의 일부로 사용되므로 이름에는 URL에 유효하지 않은 문자가 포함될 수 없습니다. 


## <a name="moveemployee-intent"></a>MoveEmployee 의도

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. **새 의도 만들기**를 선택합니다. 

3. 팝업 대화 상자에서 `MoveEmployee`을 입력하고 **완료**를 선택합니다. 

    ![새 의도 만들기 대화 상자의 스크린샷](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-ddl.png)

4. 의도에 발화 예제를 추가합니다.

    |예제 발화|
    |--|
    |John W. Smith를 B-1234에서 H-4452로 이동|
    |john.w.smith@mycompany.com을 사무실 h-4452에서 사무실 b-1234로 이동|
    |내일 x12345에서 h-1234로 이동|
    |425-555-1212를 HH-2345에 배치|
    |123-45-6789를 A-4321에서 J-23456으로 이동|
    |Jill Jones를 D-2345에서 J-23456으로 이동|
    |jill-jones@mycompany.com을 M-12345로 이동|
    |x23456에서 M-12345로|
    |425-555-0000에서 h-4452로|
    |234-56-7891에서 hh-2345로|

    [ ![새 발화가 강조 표시된 의도 페이지의 스크린샷](./media/luis-quickstart-intent-and-list-entity/hr-enter-utterances.png) ](./media/luis-quickstart-intent-and-list-entity/hr-enter-utterances.png#lightbox)

    이전 자습서에서 숫자 및 datetimeV2를 추가했으며 예제 발언에서 발견되면 자동으로 레이블이 지정됩니다.

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="employee-list-entity"></a>직원 목록 엔터티
**MoveEmployee** 의도에 예제 발언이 생겼으니, LUIS가 직원이 무엇인지 이해해야 합니다. 

각 항목의 주 _정식_ 이름은 직원 번호입니다. 이 도메인에서 각 정식 이름의 동의어 예제는 다음과 같습니다. 

|동의어 용도|동의어 값|
|--|--|
|이름|John W. Smith|
|메일 주소|john.w.smith@mycompany.com|
|내선 번호|x12345|
|개인 휴대폰 번호|425-555-1212|
|미국 사회 보장 번호|123-45-6789|


1. 왼쪽 패널에서 **엔터티**를 선택합니다.

2. **새 엔터티 만들기**를 선택합니다.

3. 엔터티 팝업 대화 상자에서 엔터티 이름으로 `Employee`를 입력하고 엔터티 형식으로 **목록**을 입력합니다. **완료**를 선택합니다.  

    [![](media/luis-quickstart-intent-and-list-entity/hr-list-entity-ddl.png "새 엔터티 만들기 대화 상자의 스크린샷")](media/luis-quickstart-intent-and-list-entity/hr-list-entity-ddl.png#lightbox)

4. 직원 엔터티 페이지에서 새 값으로 `Employee-24612`를 입력합니다.

    [![](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png "값을 입력하는 스크린샷")](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png#lightbox)

5. 동의어로 다음 값을 추가합니다.

    |동의어 용도|동의어 값|
    |--|--|
    |이름|John W. Smith|
    |메일 주소|john.w.smith@mycompany.com|
    |내선 번호|x12345|
    |개인 휴대폰 번호|425-555-1212|
    |미국 사회 보장 번호|123-45-6789|

    [![](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png "동의어를 입력하는 스크린샷")](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png#lightbox)

6. 새 값으로 `Employee-45612`를 입력합니다.

7. 동의어로 다음 값을 추가합니다.

    |동의어 용도|동의어 값|
    |--|--|
    |이름|Jill Jones|
    |메일 주소|jill-jones@mycompany.com|
    |내선 번호|x23456|
    |개인 휴대폰 번호|425-555-0000|
    |미국 사회 보장 번호|234-56-7891|

## <a name="train"></a>학습

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>게시

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>엔드포인트에서 의도 및 엔터티 가져오기

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. 주소의 URL 끝으로 이동하고 `shift 123-45-6789 from Z-1242 to T-54672`를 입력합니다. 마지막 쿼리 문자열 매개 변수는 `q`, 발화 **q** 쿼리입니다. 이 발언은 레이블이 지정된 발언과 같지 않으므로 유용한 테스트이며, `Employee`가 추출된 `MoveEmployee`를 반환해야 합니다.

  ```JSON
  {
    "query": "shift 123-45-6789 from Z-1242 to T-54672",
    "topScoringIntent": {
      "intent": "MoveEmployee",
      "score": 0.9882801
    },
    "intents": [
      {
        "intent": "MoveEmployee",
        "score": 0.9882801
      },
      {
        "intent": "FindForm",
        "score": 0.016044287
      },
      {
        "intent": "GetJobInformation",
        "score": 0.007611245
      },
      {
        "intent": "ApplyForJob",
        "score": 0.007063288
      },
      {
        "intent": "Utilities.StartOver",
        "score": 0.00684710965
      },
      {
        "intent": "None",
        "score": 0.00304174074
      },
      {
        "intent": "Utilities.Help",
        "score": 0.002981
      },
      {
        "intent": "Utilities.Confirm",
        "score": 0.00212222221
      },
      {
        "intent": "Utilities.Cancel",
        "score": 0.00191026414
      },
      {
        "intent": "Utilities.Stop",
        "score": 0.0007461446
      }
    ],
    "entities": [
      {
        "entity": "123 - 45 - 6789",
        "type": "Employee",
        "startIndex": 6,
        "endIndex": 16,
        "resolution": {
          "values": [
            "Employee-24612"
          ]
        }
      },
      {
        "entity": "123",
        "type": "builtin.number",
        "startIndex": 6,
        "endIndex": 8,
        "resolution": {
          "value": "123"
        }
      },
      {
        "entity": "45",
        "type": "builtin.number",
        "startIndex": 10,
        "endIndex": 11,
        "resolution": {
          "value": "45"
        }
      },
      {
        "entity": "6789",
        "type": "builtin.number",
        "startIndex": 13,
        "endIndex": 16,
        "resolution": {
          "value": "6789"
        }
      },
      {
        "entity": "-1242",
        "type": "builtin.number",
        "startIndex": 24,
        "endIndex": 28,
        "resolution": {
          "value": "-1242"
        }
      },
      {
        "entity": "-54672",
        "type": "builtin.number",
        "startIndex": 34,
        "endIndex": 39,
        "resolution": {
          "value": "-54672"
        }
      }
    ]
  }
  ```

  직원을 찾아 `Employee` 유형으로 반환했으며 확인 값은 `Employee-24612`입니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계
이 자습서에서는 새 의도를 만들고, 예제 발언을 추가하고, 발언에서 정확하게 일치하는 텍스트를 추출하는 목록 엔터티를 만들었습니다. 교육 및 앱 게시를 마친 후 엔드포인트 쿼리가 의도를 식별하고 추출된 데이터를 반환했습니다.

> [!div class="nextstepaction"]
> [앱에 계층적 엔터티 추가](luis-quickstart-intent-and-hier-entity.md)

