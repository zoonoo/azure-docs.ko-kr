---
title: 정확한 텍스트 일치 나열된 데이터를 가져오도록 LUIS 앱을 만드는 자습서 - Azure | Microsoft Docs
description: 이 자습서에서는 의도 및 목록 엔터티를 사용하는 간단한 LUIS 앱을 만들어서 이 빠른 시작에서 데이터를 추출하는 방법을 알아봅니다.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: v-geberr
ms.openlocfilehash: c5408d20a736f262e95ce7014c385b50521967ad
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37127858"
---
# <a name="tutorial-4-add-list-entity"></a>자습서: 4. 목록 엔터티 추가
이 자습서에서는 미리 정의된 목록과 일치하는 데이터를 가져오는 방법을 보여주는 앱을 만듭니다. 

<!-- green checkmark -->
> [!div class="checklist"]
> * 엔터티 목록 이해 
> * MoveEmployee 의도를 사용하여 HR(인사 관리) 도메인에 대한 새 LUIS 앱 만들기
> * 목록 엔터티를 추가하여 발언에서 직원 추출
> * 앱 학습 및 게시
> * 앱의 엔드포인트를 쿼리하여 LUIS JSON 응답 확인

이 문서에서는 LUIS 앱을 작성하기 위한 [LUIS](luis-reference-regions.md#luis-website) 체험 계정이 필요합니다.

## <a name="before-you-begin"></a>시작하기 전에
[regex 엔터티](luis-quickstart-intents-regex-entity.md) 자습서의 인사 관리 앱이 없으면 JSON을 [LUIS](luis-reference-regions.md#luis-website) 웹 사이트의 새 앱으로 [가져옵니다](create-new-app.md#import-new-app). 가져올 앱은 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-regex-HumanResources.json) Github 리포지토리에 있습니다.

원래의 인사 관리 앱을 유지하려면 [설정](luis-how-to-manage-versions.md#clone-a-version) 페이지에서 버전을 복제하고 해당 이름을 `list`로 지정합니다. 복제는 원래 버전에 영향을 주지 않고도 다양한 LUIS 기능을 사용할 수 있는 좋은 방법입니다. 

## <a name="purpose-of-the-list-entity"></a>목록 엔터티의 목적
이 앱은 직원을 한 건물에서 다른 건물로 이동하는 방법에 대한 발언을 예측합니다. 이 앱은 목록 엔터티를 사용하여 직원을 추출합니다. 이름, 전화 번호, 이메일 또는 미국 사회 보장 번호를 사용하여 직원을 참조할 수 있습니다. 

목록 엔터티에는 각 항목의 동의어를 사용하는 여러 항목이 포함될 수 있습니다. 소규모 회사부터 중간 규모 회사까지는 목록 엔터티를 사용하여 직원 정보를 추출합니다. 

각 항목의 정식 이름은 직원 번호입니다. 이 도메인의 동의어 예제는 다음과 같습니다. 

|동의어 용도|동의어 값|
|--|--|
|Name|John W. Smith|
|메일 주소|john.w.smith@mycompany.com|
|내선 번호|x12345|
|개인 휴대폰 번호|425-555-1212|
|미국 사회 보장 번호|123-45-6789|

다음과 같은 경우 목록 엔터티는 이러한 종류의 데이터에 적합합니다.

* 데이터 값이 알려진 집합입니다.
* 집합이 이 엔터티 형식의 최대 LUIS [경계](luis-boundaries.md)를 초과하지 않습니다.
* 발언의 텍스트는 동의어와 정확히 일치합니다. 

LUIS는 클라이언트 응용 프로그램이 직원을 이동하는 표준 순서를 만드는 방식으로 직원을 추출합니다.
<!--
## Example utterances
Simple example utterances for a `MoveEmployee` inent:

```
move John W. Smith from B-1234 to H-4452
mv john.w.smith@mycompany from office b-1234 to office h-4452

```
-->

## <a name="add-moveemployee-intent"></a>MoveEmployee 의도 추가

1. 인사 관리 앱이 LUIS의 **빌드** 섹션에 있는지 확인합니다. 오른쪽 위의 메뉴 표시줄에서 **빌드**를 선택하여 이 섹션으로 변경할 수 있습니다. 

    [ ![오른쪽 위의 탐색 모음에서 강조 표시된 빌드가 있는 LUIS 앱의 스크린샷](./media/luis-quickstart-intent-and-list-entity/hr-first-image.png)](./media/luis-quickstart-intent-and-list-entity/hr-first-image.png#lightbox)

2. **새 의도 만들기**를 선택합니다. 

    [ ![새 의도 만들기 단추가 강조 표시된 의도 페이지의 스크린샷](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-button.png) ](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-button.png#lightbox)

3. 팝업 대화 상자에서 `MoveEmployee`을 입력하고 **완료**를 선택합니다. 

    ![새 의도 만들기 대화 상자의 스크린샷](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-ddl.png)

4. 의도에 발화 예제를 추가합니다.

    |예제 발언|
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

## <a name="create-an-employee-list-entity"></a>직원 목록 엔터티 만들기
**MoveEmployee** 의도에 발언이 생겼으니, LUIS가 직원이 무엇인지 이해해야 합니다. 

1. 왼쪽 패널에서 **엔터티**를 선택합니다.

    [ ![왼쪽 탐색 창에 엔터티 단추가 강조 표시된 의도 페이지의 스크린샷](./media/luis-quickstart-intent-and-list-entity/hr-select-entity-button.png) ](./media/luis-quickstart-intent-and-list-entity/hr-select-entity-button.png#lightbox)

2. **새 엔터티 만들기**를 선택합니다.

    [ ![새 엔터티 만들기가 강조 표시된 엔터티 페이지의 스크린샷](./media/luis-quickstart-intent-and-list-entity/hr-create-new-entity-button.png) ](./media/luis-quickstart-intent-and-list-entity/hr-create-new-entity-button.png#lightbox)

3. 엔터티 팝업 대화 상자에서 엔터티 이름으로 `Employee`를 입력하고 엔터티 형식으로 **목록**을 입력합니다. **완료**를 선택합니다.  

    [![](media/luis-quickstart-intent-and-list-entity/hr-list-entity-ddl.png "새 엔터티 만들기 대화 상자의 스크린샷")](media/luis-quickstart-intent-and-list-entity/hr-list-entity-ddl.png#lightbox)

4. 직원 엔터티 페이지에서 새 값으로 `Employee-24612`를 입력합니다.

    [![](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png "값을 입력하는 스크린샷")](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png#lightbox)

5. 동의어로 다음 값을 추가합니다.

    |동의어 용도|동의어 값|
    |--|--|
    |Name|John W. Smith|
    |메일 주소|john.w.smith@mycompany.com|
    |내선 번호|x12345|
    |개인 휴대폰 번호|425-555-1212|
    |미국 사회 보장 번호|123-45-6789|

    [![](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png "동의어를 입력하는 스크린샷")](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png#lightbox)

6. 새 값으로 `Employee-45612`를 입력합니다.

7. 동의어로 다음 값을 추가합니다.

    |동의어 용도|동의어 값|
    |--|--|
    |Name|Jill Jones|
    |메일 주소|jill-jones@mycompany.com|
    |내선 번호|x23456|
    |개인 휴대폰 번호|425-555-0000|
    |미국 사회 보장 번호|234-56-7891|

## <a name="train-the-luis-app"></a>LUIS 앱 학습
LUIS는 학습될 때까지 의도와 엔터티(모델)에 대한 변경 내용을 인식하지 못합니다. 

1. LUIS 웹 사이트의 오른쪽 위에서 **학습** 단추를 선택합니다.

    ![앱 학습](./media/luis-quickstart-intent-and-list-entity/train-button.png)

2. 웹 사이트의 위쪽에 성공이 확인된 녹색 상태 표시줄이 표시되면 학습이 완료됩니다.

    ![학습 성공](./media/luis-quickstart-intent-and-list-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>앱을 게시하여 엔드포인트 URL 가져오기
챗봇 또는 다른 응용 프로그램에서 LUIS 예측을 얻으려면 앱을 게시해야 합니다. 

1. LUIS 웹 사이트의 오른쪽 위에서 **게시** 단추를 선택합니다. 

    [![](media/luis-quickstart-intent-and-list-entity/publish.png "게시 단추를 선택하는 스크린샷")](media/luis-quickstart-intent-and-list-entity/publish.png#lightbox)

2. 프로덕션 슬롯과 **게시** 단추를 선택합니다. 

    [![](media/luis-quickstart-intent-and-list-entity/publish-to-production.png "프로덕션 슬롯 단추에 대한 게시를 선택하는 스크린샷")](media/luis-quickstart-intent-and-list-entity/publish-to-production.png#lightbox)

3. 웹 사이트의 위쪽에 성공이 확인된 녹색 상태 표시줄이 표시되면 게시가 완료됩니다.

## <a name="query-the-endpoint-with-a-different-utterance"></a>다른 발화를 사용하여 엔드포인트 쿼리
1. **게시** 페이지의 아래쪽에서 **엔드포인트** 링크를 선택합니다. 그러면 주소 표시줄에 엔드포인트 URL이 있는 다른 브라우저 창이 열립니다. 

    [![](media/luis-quickstart-intent-and-list-entity/publish-select-endpoint.png "게시 페이지에서 엔드포인트 URL 스크린샷")](media/luis-quickstart-intent-and-list-entity/publish-select-endpoint.png#lightbox)

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

## <a name="where-is-the-natural-language-processing-in-the-list-entity"></a>목록 엔터티에서 자연어를 처리하는 위치는 어디인가요? 
목록 엔터티가 정확한 텍스트 일치 항목이기 때문에 자연어 처리(또는 기계 학습)을 사용하지 않습니다. LUIS는 자연어 처리(또는 기계 학습)를 사용하여 올바른 최상위 점수 매기기 의도를 선택합니다. 또한 발언은 둘 이상의 엔터티 또는 둘 이상의 엔터티 형식을 혼합한 것일 수 있습니다. 자연어 처리(또는 기계 학습) 엔터티를 비롯한 앱의 모든 엔터티에 대해 각 발언이 처리됩니다.

## <a name="what-has-this-luis-app-accomplished"></a>이 LUIS 앱에서 수행한 작업은?
이 앱은 목록 엔터티를 사용하여 올바른 직원을 추출했습니다. 

챗봇에는 이제 기본 작업, `MoveEmployee` 및 이동할 직원을 결정하기에 충분한 정보가 있습니다. 

## <a name="where-is-this-luis-data-used"></a>이 LUIS 데이터가 사용되는 위치는? 
LUIS는 이 요청을 통해 수행됩니다. 챗봇과 같은 호출 응용 프로그램에서는 topScoringIntent 결과와 엔터티의 데이터를 사용하여 다음 단계를 수행할 수 있습니다. LUIS는 봇 또는 호출 응용 프로그램에 대해 프로그래밍 방식으로 작동하지 않습니다. LUIS는 사용자의 의도가 무엇인지만 결정합니다. 

## <a name="clean-up-resources"></a>리소스 정리
더 이상 필요하지 않은 경우 LUIS 앱을 삭제합니다. 왼쪽 위의 메뉴에서 **내 앱**을 선택합니다. 앱 목록에서 앱 이름 오른쪽에 있는 3개 점 메뉴(...)를 선택하고 **삭제**를 선택합니다. **앱을 삭제하시겠습니까?** 팝업 대화 상자에서 **확인**을 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱에 계층적 엔터티 추가](luis-quickstart-intent-and-hier-entity.md)

