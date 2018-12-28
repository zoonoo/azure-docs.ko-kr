---
title: 예제 발화 추가
titleSuffix: Language Understanding - Azure Cognitive Services
description: 예제 발언은 사용자 질문 또는 명령의 텍스트 예제입니다. LUIS(Language Understanding)를 학습하려면 의도에 예제 발화를 추가해야 합니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: 33c941f84952faca1961bb65687b4098b837a2fd
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53139174"
---
# <a name="add-an-entity-to-example-utterances"></a>예제 발언에 엔터티 추가 

예제 발언은 사용자 질문 또는 명령의 텍스트 예제입니다. LUIS(Language Understanding)를 학습시키려면 [의도](luis-concept-intent.md)에 [예제 발언](luis-concept-utterance.md)을 추가해야 합니다.

일반적으로 먼저 의도에 예제 발언을 추가한 후, 의도 페이지에서 엔터티를 만들고 발언에 레이블을 지정합니다. 엔터티를 먼저 만들려면 [엔터티 추가](luis-how-to-add-entities.md)를 참조하세요.

## <a name="marking-entities-in-example-utterances"></a>예제 발언에서 엔터티 표시

엔터티에 표시할 텍스트를 예제 발언에서 선택하면 전체 팝업 메뉴가 나타납니다. 이 메뉴를 사용하여 엔터티를 만들거나 선택합니다. 

미리 빌드한 엔터티 및 정규식 엔터티와 같은 특정 엔터티 형식은 자동으로 태그가 지정되므로 예제 발언에서 태그를 지정할 수 없습니다. 

## <a name="add-a-simple-entity"></a>단순 엔터티 추가

다음 절차에서는 의도 페이지의 다음 발언 내에 사용자 지정 엔터티를 만들고 태그를 지정합니다.

```text
Does John Smith work in Seattle?
```

1. 발언에서 `Seattle`을 선택하고 단순 엔터티로 레이블을 지정합니다.

    [![발언에서 단순 엔터티에 대한 텍스트를 선택하는 스크린샷](./media/luis-how-to-add-example-utterances/hr-create-simple-1.png)](./media/luis-how-to-add-example-utterances/hr-create-simple-1.png)

    > [!NOTE]
    > 엔터티로 태그를 지정할 단어를 선택할 때 다음을 수행합니다.
    > * 단어 하나일 경우에는 그냥 선택합니다. 
    > * 둘 이상의 단어 집합인 경우에는 맨 처음 단어를 선택한 후 집합의 마지막 단어를 선택합니다.

1. 나타나는 엔터티 드롭다운 상자에서 기존 엔터티를 선택하거나 새 엔터티를 추가합니다. 새 엔터티를 추가하려면 텍스트 상자에 이름을 입력한 다음, **새 엔터티 만들기**를 선택합니다. 

    ![엔터티 이름을 입력하는 스크린샷](./media/luis-how-to-add-example-utterances/hr-create-simple-2.png)

1. **만들려는 엔터티 형식** 팝업 상자에서 엔터티 이름을 확인하고 **단순** 엔터티 형식을 선택한 다음, **완료**를 선택합니다.

    [구 목록](luis-concept-feature.md)은 일반적으로 단순한 엔터티의 신호를 향상시키는 데 사용됩니다.

## <a name="add-a-list-entity"></a>목록 엔터티 추가

목록 엔터티는 시스템의 관련된 단어의 고정된 폐쇄형 집합(정확한 텍스트 일치)을 나타냅니다. 

회사의 부서 목록의 경우 정규화된 값 `Accounting` 및 `Human Resources`가 있을 수 있습니다. 정규화된 각 이름에는 동의어가 있습니다. 부서의 경우 이러한 동의어는 부서 머리글자어, 숫자 또는 속어를 포함할 수 있습니다. 엔터티를 만들 때 모든 값을 알 필요는 없습니다. 동의어를 포함하는 실제 사용자 발언을 검토한 후에 더 많은 발언을 추가할 수 있습니다.

1. 예제 발언 목록에서 특정 발언에 대해 새 목록에 포함할 단어 또는 구를 선택합니다. 그런 다음, 상위 텍스트 상자에 목록 이름을 입력하고 **새 엔터티 만들기**를 선택합니다.   

    ![목록 엔터티 이름을 입력하는 스크린샷](./media/luis-how-to-add-example-utterances/hr-create-list-1.png)


1. **What type of entity do you want to create?**(만들려는 엔터티 형식을 선택하세요.) 팝업 상자에서 엔터티 이름을 지정하고 형식으로 **목록**을 선택합니다. 이 목록 항목의 동의어를 추가하고 **완료**를 선택합니다. 

    ![목록 엔터티 동의어를 입력하는 스크린샷](./media/luis-how-to-add-example-utterances/hr-create-list-2.png)

    다른 발언에 레이블을 지정하거나 왼쪽 탐색 창의 **엔터티**에서 엔터티를 편집하여 더 많은 목록 항목 또는 항목 동의어를 추가할 수 있습니다. 엔터티를 [편집](luis-how-to-add-entities.md#add-list-entities)하면 해당 동의어가 있는 추가 항목을 입력하거나 목록을 가져오는 목록이 제공됩니다. 

## <a name="add-composite-entity"></a>복합 엔터티 추가

복합 엔터티는 기존 **엔터티**에서 부모 엔터티로 만들어집니다. 

발언, `Does John Smith work in Seattle?`에서 복합 발언은 직원 이름의 엔터티 정보와 위치를 단일 부모 개체로 반환할 수 있습니다. 

직원 이름인 John Smith는 미리 빌드된 [personName](luis-reference-prebuilt-person.md) 엔터티입니다. 위치인 Seatle은 사용자 지정 단순 엔터티입니다. 이러한 두 엔터티가 만들어지고 예제 발언에서 태그가 지정되면 해당 엔터티는 복합 엔터티에 래핑될 수 있습니다. 

1. 복잡 엔터티에 개별 엔터티를 래핑하려면 복합 엔터티에 대한 발언에서 레이블이 지정된 **첫 번째**(맨 왼쪽) 엔터티를 선택합니다. 이 항목의 선택 옵션을 보여 주는 드롭다운 목록이 나타납니다.

1. 드롭다운 목록에서 **복합 엔터티 래핑**을 선택합니다. 

    ![“복합 엔터티에 래핑”을 선택하는 스크린샷](./media/luis-how-to-add-example-utterances/hr-create-composite-1.png)

1. 복합 엔터티의 마지막(맨 오른쪽) 단어를 선택합니다. 녹색 선은 복합 엔터티를 따릅니다.

1. 드롭다운 목록에서 복합 엔터티 이름을 입력합니다.

    ![드롭다운 목록에서 복합 엔터티 이름을 입력하는 스크린샷](./media/luis-how-to-add-example-utterances/hr-create-composite-2.png)

    엔터티를 올바르게 래핑하면 전체 구 아래에 녹색 줄이 표시됩니다.

1. **What type of entity do you want to create?**(만들려는 엔터티 형식을 선택하세요.) 팝업 상자에서 복합 엔터티 세부 정보가 유효한지 확인하고 **완료**를 선택합니다.

    ![엔터티 세부 정보 팝업 스크린샷](./media/luis-how-to-add-example-utterances/hr-create-composite-3.png)

1. 복합 엔터티는 개별 엔터티는 파란색으로 강조되고 전체 복합 엔터티에는 녹색 밑줄이 표시됩니다. 

    ![복합 엔터티를 포함하는 의도 세부 정보 페이지 스크린샷](./media/luis-how-to-add-example-utterances/hr-create-composite-4.png)

## <a name="add-hierarchical-entity"></a>계층적 엔터티 추가

계층 구조 엔터티는 컨텍스트에 따라 학습되고 개념적으로 관련이 있는 엔터티의 범주입니다. 다음 예제에서는 엔터티에는 출발지 및 목적지 위치가 포함되어 있습니다. 

발언 `Move John Smith from Seattle to Cairo`에서 Seattle은 출발지 위치이고 Cairo는 목적지 위치입니다. 각 위치는 컨텍스트가 다르며, 발언의 단어 순서 및 단어 선택에서 학습됩니다.

1. 의도 페이지의 발언에서 `Seattle`을 선택하고 엔터티 이름 `Location`을 입력한 후 키보드에서 Enter 키를 선택합니다.

    ![계층 구조 엔터티 레이블 만들기 대화 상자의 스크린샷](./media/luis-how-to-add-example-utterances/hr-hier-1.png)

1. **What type of entity do you want to create?**(만들려는 엔터티 형식을 선택하세요.) 팝업 상자에서 **엔터티 형식**으로 _계층 구조_를 선택하고 `Origin` 및 `Destination`을 자식으로 추가한 후 **완료**를 선택합니다.

    ![ToLocation 엔터티가 강조 표시된 의도 세부 정보 페이지의 스크린샷](./media/luis-how-to-add-example-utterances/create-location-hierarchical-entity.png)

1. 발언의 단어는 부모 계층 구조 엔터티로 레이블이 지정되었습니다. 자식 엔터티에 해당 단어를 할당해야 합니다. 의도 세부 정보 페이지의 해당 발언으로 돌아갑니다. 해당 단어를 선택하고 드롭다운 목록에서 만든 엔터티 이름을 선택한 후, 오른쪽 메뉴에 따라 올바른 자식 엔터티를 선택합니다.

    ![자식 엔터티에 단어를 할당해야 하는 의도 세부 정보 페이지의 스크린샷](./media/luis-how-to-add-example-utterances/hr-hier-3.png)

    >[!CAUTION]
    >자식 엔터티 이름은 단일 앱의 모든 엔터티에서 고유해야 합니다. 서로 다른 두 계층 구조 엔터티는 이름이 같은 자식 엔터티를 포함할 수 없습니다. 

## <a name="entity-status-predictions"></a>엔터티 상태 예측

LUIS 포털에서 새 발언을 입력하면 발언에서 엔터티 예측 오류가 발생할 수 있습니다. 예측 오류는 엔터티에 레이블이 지정되는 방식과 LUIS가 엔터티를 예측하는 방법 간의 차이입니다. 

발언에 빨간색 밑줄을 사용하여 LUIS 포털에 이러한 차이가 시각적으로 표현됩니다. 빨간색 밑줄은 엔터티 대괄호 내부 또는 괄호 외부에 나타날 수 있습니다. 

![엔터티 상태 예측 불일치의 스크린샷](./media/luis-how-to-add-example-utterances/entity-prediction-error.png)

발언에서 빨간색 밑줄이 표시된 단어를 선택합니다. 

예측 불일치가 있는 경우 엔터티 상자에 빨간색 느낌표가 있는 **엔터티 상태**가 표시됩니다. 레이블이 지정된 엔터티와 예측된 엔터티 간의 차이점에 대한 정보가 포함된 엔터티 상태를 보려면 **엔터티 상태**를 선택하고 오른쪽의 항목을 선택합니다.

![예측 불일치를 해결하기 위해 올바른 항목을 선택하는 스크린샷](./media/luis-how-to-add-example-utterances/entity-status.png)

다음과 같은 경우 빨간색 선이 나타날 수 있습니다.

   * 엔터티에 레이블이 지정되기 전에 발언이 입력된 경우
   * 엔터티 레이블이 적용된 경우
   * 엔터티 레이블이 제거된 경우
   * 해당 텍스트에 대해 둘 이상의 엔터티 레이블이 예측된 경우 

다음 솔루션은 엔터티 예측 불일치를 해결하도록 도와줍니다.

|엔터티|시각적 표시기|예측|해결 방법|
|--|--|--|--|
|발언이 입력되었지만 아직 엔터티에 레이블이 지정되지 않았습니다.|빨간색 밑줄|예측이 올바릅니다.|예측 값을 사용하여 엔터티를 레이블을 지정합니다.|
|레이블이 지정되지 않은 텍스트|빨간색 밑줄|잘못된 예측|모든 의도에서 이 잘못된 엔터티를 사용하는 현재 발언을 검토해야 합니다. 현재 발언이 이 텍스트가 예측 엔터티라고 LUIS에 잘못 알려주었습니다.
|올바르게 레이블이 지정된 텍스트|파란색 엔터티 강조 표시, 빨간색 밑줄|잘못된 예측|다양한 위치 및 사용 현황에서 레이블이 올바르게 지정된 엔터티를 더 많이 제공합니다. LUIS에 이 엔터티가 바로 그 엔터티라고 알려주기에는 현재 발언이 충분하지 않거나 비슷한 엔터티가 동일한 컨텍스트에서 나타납니다. LUIS가 혼동하지 않도록 비슷한 엔터티를 단일 엔터티로 결합해야 합니다. 또 다른 방법은 단어의 중요성을 높이는 구 목록을 추가하는 것입니다. |
|레이블이 잘못 지정된 텍스트|파란색 엔터티 강조 표시, 빨간색 밑줄|올바른 예측| 다양한 위치 및 사용 현황에서 레이블이 올바르게 지정된 엔터티를 더 많이 제공합니다. 

## <a name="other-actions"></a>다른 작업

선택한 그룹 또는 개별 항목으로서 예제 발언에 대해 작업을 수행할 수 있습니다. 예제 발언 그룹을 선택하면 목록 위의 상황에 맞는 메뉴가 변경됩니다. 단일 항목이 목록 위의 상황에 맞는 메뉴와 각 발언 행 끝에 있는 상황에 맞는 개별 줄임표를 사용할 수 있습니다. 

### <a name="remove-entity-labels-from-utterances"></a>발언에서 엔터티 레이블 제거

의도 페이지의 발언에서 기계 학습된 엔터티 레이블을 제거할 수 있습니다. 엔터티가 기계 학습되지 않은 경우 발언에서 제거할 수 없습니다. 발언에서 기계 학습되지 않은 엔터티를 제거해야 할 경우에는 전체 앱에서 해당 엔터티를 삭제해야 합니다. 

발언에서 기계 학습된 엔터티 레이블을 제거하려면 발언에서 해당 엔터티를 선택합니다. 그런 후 나타나는 엔터티 드롭다운 상자에서 **레이블 제거**를 선택합니다.

![레이블 제거가 강조 표시된 의도 세부 정보 페이지의 스크린샷](./media/luis-how-to-add-example-utterances/remove-label.png) 

### <a name="add-prebuilt-entity-label"></a>미리 빌드된 엔터티 레이블 추가

LUIS 앱에 미리 빌드된 엔터티를 추가하는 경우 이러한 엔터티를 사용하여 발언에 태그를 지정할 필요는 없습니다. 미리 빌드된 엔터티 및 추가 방법에 대한 자세한 내용은 [엔터티 추가](luis-how-to-add-entities.md#add-prebuilt-entity)를 참조하세요.

### <a name="add-regular-expression-entity-label"></a>정규식 엔터티 레이블 추가

LUIS 앱에 정규식 엔터티를 추가하는 경우 이러한 엔터티를 사용하여 발언에 태그를 지정할 필요는 없습니다. 정규식 엔터티 및 추가 방법에 대한 자세한 내용은 [엔터티 추가](luis-how-to-add-entities.md#add-regular-expression-entities)를 참조하세요.


### <a name="create-a-pattern-from-an-utterance"></a>발언에서 패턴 만들기

[의도 또는 엔터티 페이지의 기존 발언에서 패턴 추가](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page)를 참조하세요.


### <a name="add-patternany-entity"></a>pattern.any 엔터티 추가

LUIS 앱에 pattern.any 엔터티를 추가하는 경우 이러한 엔터티를 사용하여 발언에 레이블을 지정할 수 없습니다. 이 레이블은 패턴에서만 유효합니다. pattern.any 엔터티 및 추가 방법에 대한 자세한 내용은 [엔터티 추가](luis-how-to-add-entities.md#add-patternany-entities)를 참조하세요.

## <a name="train-your-app-after-changing-model-with-utterances"></a>발언이 포함된 모델을 변경한 후 앱 학습

발언을 추가, 편집 또는 제거한 후 변경 내용을 엔드포인트 쿼리에 적용하려면 앱을 [학습](luis-how-to-train.md)시키고 [게시](luis-how-to-publish-app.md)합니다. 

## <a name="next-steps"></a>다음 단계

의도에서 발언에 레이블을 지정한 후에는 [복합 엔터티](luis-how-to-add-entities.md)를 만들 수 있습니다.
