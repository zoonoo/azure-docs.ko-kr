---
title: LUIS 앱에서 예제 발언 추가 | Microsoft Docs
titleSuffix: Azure
description: LUIS(Language Understanding) 응용 프로그램에서 발언을 추가하는 방법을 알아봅니다.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 74a4b77bd9823e5462eecd438cf4c1d863e79892
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36300641"
---
# <a name="add-example-utterances-and-label-with-entities"></a>예제 발언 추가 및 엔터티로 레이블 지정

예제 발언은 사용자 질문 또는 명령의 텍스트 예제입니다. LUIS(Language Understanding)를 학습시키려면 [의도](luis-concept-intent.md)에 [예제 발언](luis-concept-utterance.md)을 추가해야 합니다.

일반적으로 먼저 의도에 예제 발언을 추가한 후, 의도 페이지에서 엔터티를 만들고 발언에 레이블을 지정합니다. 엔터티를 먼저 만들려면 [엔터티 추가](luis-how-to-add-entities.md)를 참조하세요.

## <a name="add-an-utterance"></a>발언 추가
의도 페이지에서 의도 이름 아래의 텍스트 상자에 사용자로부터 예상하는 관련 예제 발언(예: `book 2 adult business tickets to Paris tomorrow on Air France`)을 입력하고 Enter 키를 누릅니다. 
 
>[!NOTE]
>LUIS는 모든 발언을 소문자로 변환합니다.

![발언이 강조 표시된 의도 세부 정보 페이지의 스크린샷](./media/luis-how-to-add-example-utterances/add-new-utterance-to-intent.png) 

현재 의도에 대한 발언 목록에 발언이 추가됩니다. 

## <a name="ignoring-words-and-punctuation"></a>단어 및 문장 부호 무시
예제 발언의 특정 단어 또는 문장 부호를 무시하려는 경우 _ignore_ 구문과 함께 [패턴](luis-concept-patterns.md#pattern-syntax)을 사용합니다. 

## <a name="add-simple-entity-label"></a>간단한 엔터티 레이블 추가
다음 절차에서는 의도 페이지의 다음 발언 내에 사용자 지정 엔터티를 만들고 레이블을 지정합니다.

```
book me 2 adult business tickets to Paris tomorrow on Air France
```

1. 발언에서 "Air France"를 선택하고 간단한 엔터티로 레이블을 지정합니다.

    > [!NOTE]
    > 엔터티로 레이블을 지정할 단어를 선택할 경우 다음을 수행합니다.
    > * 단어 하나일 경우에는 그냥 선택합니다. 
    > * 둘 이상의 단어 집합인 경우에는 맨 처음 단어를 선택한 후 집합의 마지막 단어를 선택합니다.

2. 나타나는 엔터티 드롭다운 상자에서 기존 엔터티를 선택하거나 새 엔터티를 추가합니다. 새 엔터티를 추가하려면 텍스트 상자에 이름을 입력한 다음, **새 엔터티 만들기**를 선택합니다. 
 
    ![간단한 엔터티 레이블 지정 옵션이 강조 표시된 의도 세부 정보 페이지의 스크린샷](./media/luis-how-to-add-example-utterances/create-airline-simple-entity.png)

3. **만들려는 엔터티 형식** 팝업 대화 상자에서 엔터티 이름을 확인하고 간단한 엔터티 형식을 선택한 다음, **완료**를 선택합니다.

    ![확인 대화 상자 이미지](./media/luis-how-to-add-example-utterances/create-simple-airline-entity.png)

    끝점 JSON 쿼리 응답에서 단순 엔터티를 추출하는 방법에 대해 자세히 알아보려면 [데이터 추출](luis-concept-data-extraction.md#simple-entity-data)을 참조하세요. 단순 엔터티 사용 방법에 대해 자세히 알아보려면 단순 엔터티 [빠른 시작](luis-quickstart-primary-and-secondary-data.md)을 참조하세요.


## <a name="add-list-entity-and-label"></a>목록 엔터티 추가 및 레이블 지정
목록 엔터티는 시스템의 관련된 단어의 고정된 폐쇄형 집합(정확한 텍스트 일치)을 나타냅니다. 

drinks 목록 엔터티에 대해 water와 soda pop이라는 두 가지 정규화된 값을 가질 수 있습니다. 정규화된 각 이름에는 동의어가 있습니다. water의 경우 동의어는 H20, gas, flat입니다. soda pop의 경우 동의어는 fruit, cola, ginger입니다. 엔터티를 만들 때 모든 값을 알 필요는 없습니다. 동의어를 포함하는 실제 사용자 발언을 검토한 후에 더 많은 발언을 추가할 수 있습니다.

|정규화된 이름|동의어|
|--|--|
|Water|H20, gas, flat|
|Soda pop|Fruit, cola, ginger|

의도 페이지에서 새 목록 엔터티를 만들 경우 명확하지 않을 수 있는 두 가지 작업을 수행하게 됩니다. 첫째, 첫 번째 목록 항목을 추가하여 새 목록을 만듭니다. 둘째, 발언에서 선택한 단어나 구를 사용해서 첫 번째 목록 항목의 이름을 지정합니다. 나중에 이러한 이름을 엔터티 페이지에서 변경할 수 있지만, 목록 항목의 이름으로 사용하려는 단어가 있는 발언을 선택하는 것이 더 빠를 수 있습니다.

예를 들어, 음료의 종류 목록을 만들려고 하며 발언에서 `h2o` 단어를 선택한 경우 목록은 해당 이름이 h20인 하나의 항목을 포함합니다. 좀 더 일반적인 이름을 원할 경우 좀 더 일반적인 이름을 사용하는 발언을 선택해야 합니다. 

1. 발언에서 목록의 첫 번째 항목에 해당하는 단어를 선택하고, 텍스트 상자에 목록의 이름을 입력한 다음, **새 엔터티 만들기**를 선택합니다.   

    ![새 엔터티 만들기가 강조 표시된 의도 세부 정보 페이지의 스크린샷](./media/luis-how-to-add-example-utterances/create-drink-list-entity.png)

2. **만들려는 엔터티 형식** 대화 상자에서 이 목록 항목의 동의어를 추가합니다. 음료 목록의 물 항목에 대해 `h20`, `perrier` 및 `waters`를 추가하고 **완료**를 선택합니다. 목록 동의어는 토큰 수준에서 일치되므로 "waters"가 추가됩니다. 하지만 영어 문화권에서는 해당 수준이 단어 수준이므로 "waters"가 목록에 없는 경우 "water"와 일치한 것으로 검색되지 않습니다. 

    ![만들려는 엔터티 형식 대화 상자 스크린샷](./media/luis-how-to-add-example-utterances/drink-list-ddl.png)

    이 음료 목록에는 한 가지 음료 유형인 water만 있습니다. 다른 발언에 레이블을 지정하거나 왼쪽 탐색 창의 **엔터티**에서 엔터티를 편집하여 더 많은 음료 유형을 추가할 수 있습니다. 엔터티를 [편집](luis-how-to-add-entities.md#add-list-entities)하면 해당 동의어가 있는 추가 항목을 입력하거나 목록을 [가져오는](luis-how-to-add-entities.md#import-list-entity-values) 목록이 제공됩니다. 

    끝점 JSON 쿼리 응답에서 목록 엔터티를 추출하는 방법에 대해 자세히 알아보려면 [데이터 추출](luis-concept-data-extraction.md#list-entity-data)을 참조하세요. 목록 엔터티 사용 방법에 대해 자세히 알아보려면 [빠른 시작](luis-quickstart-intent-and-list-entity.md)을 참조하세요.

## <a name="add-synonyms-to-the-list-entity"></a>목록 엔터티에 동의어 추가 
발언에서 단어나 구를 선택하여 목록 엔터티에 동의어를 추가합니다. Drink 목록 엔터티가 있으며 `agua`를 water의 동의어로 추가하려는 경우 다음 단계를 따릅니다.

발언에서 water의 동의어(예: `aqua`)를 선택한 후 드롭다운 목록에서 목록 엔터티 이름(예: **Drink**)을 선택하고 **동의어로 설정**을 선택한 후 동의어에 해당하는 목록 항목(예: **water**)을 선택합니다.

![새 동의어 만들기가 강조 표시된 의도 세부 정보 페이지의 스크린샷](./media/luis-how-to-add-example-utterances/set-agua-as-synonym.png)

## <a name="create-new-item-for-list-entity"></a>목록 엔터티의 새 항목 만들기
발언에서 단어나 구를 선택하여 기존 목록 엔터티에 대한 새 항목을 만듭니다. Drink 목록이 있으며 새 항목으로 `tea`를 추가하려는 경우 다음 단계를 따릅니다.

발언에서 새 목록 항목에 대한 단어(예: `tea`)를 선택하고 드롭다운 목록에서 목록 엔터티 이름(예: **Drink**)을 선택한 후 **새 동의어 만들기**를 선택합니다. 

![새 목록 항목을 추가하는 스크린샷](./media/luis-how-to-add-example-utterances/list-entity-create-new-item.png)

이 단어는 이제 파란색으로 강조 표시됩니다. 이 단어를 마우스로 가리키면 태그에 목록 항목 이름(예: tea)이 표시됩니다.

![새 목록 항목 태그 스크린샷](./media/luis-how-to-add-example-utterances/list-entity-item-name-tag.png)

## <a name="wrap-entities-in-composite-label"></a>복합 레이블에 엔터티 래핑
복합 엔터티는 **엔터티**에서 만듭니다. 의도 페이지에서는 복합 엔터티를 만들 수 없습니다. 복합 엔터티를 만든 후 의도 페이지 발언에 엔터티를 래핑할 수 있습니다. 

발언이 `book 2 tickets from Seattle to Cairo`라고 가정할 경우 복잡 발언은 단일 부모 엔터티에 티켓 수(2), 출발지(Seattle) 및 목적지(Cairo) 위치에 대한 엔터티 정보를 반환할 수 있습니다. 

다음 [단계](luis-how-to-add-entities.md#add-prebuilt-entity)에 따라 **number** 미리 빌드된 엔터티를 추가합니다. 엔터티를 만든 후에 발언의 `2`는 레이블이 지정된 엔터티임을 나타내기 위해 파란색으로 표시됩니다. 미리 빌드된 엔터티는 LUIS에서 레이블이 지정됩니다. 단일 발언에서 미리 빌드된 엔터티 레이블을 추가하거나 제거할 수 없습니다. 응용 프로그램에서 미리 빌드된 엔터티를 추가하거나 제거해야만 모든 미리 빌드된 레이블을 추가하거나 제거할 수 있습니다.

다음 [단계](#add-hierarchical-entity-and-label)에 따라 **Location** 계층 구조 엔터티를 만듭니다. 예제 발언의 출발지 및 목적지 위치에 레이블을 지정합니다. 

복합 엔터티에 엔터티를 래핑하기 전에, 모든 자식 엔터티가 발언에서 레이블이 지정되었음을 나타내기 위해 파란색으로 강조 표시되는지 확인합니다.

1. 복잡 엔터티에 개별 엔터티를 래핑하려면 복합 엔터티에 대한 발언에서 레이블이 지정된 첫 번째 엔터티를 선택합니다. 예제 발언 `book 2 tickets from Seattle to Cairo`에서 첫 번째 엔터티는 숫자 2입니다. 이 항목의 선택 옵션을 보여 주는 드롭다운 목록이 나타납니다.

    ![숫자가 선택되고 드롭다운 옵션이 강조 표시된 스크린샷](./media/luis-how-to-add-example-utterances/wrap-1.png)

2. 드롭다운 목록에서 **복합 엔터티 래핑**을 선택합니다. 

    ![복합 엔터티 래핑이 강조 표시된 복합 엔터티 래핑에 대한 드롭다운 옵션의 스크린샷](./media/luis-how-to-add-example-utterances/wrap-2.png)

3. 복합 엔터티의 마지막 단어를 선택합니다. 이 예제에서는 "Location::Destination"(Cairo를 나타냄)을 선택합니다. 이제 복합에 해당하는 발언의 엔터티 이외 단어를 비롯한 모든 단어 아래에 녹색 줄이 표시됩니다.

    ![숫자가 강조 표시된 BookFlight 의도 페이지의 스크린샷](./media/luis-how-to-add-example-utterances/wrap-composite.png)

4. 드롭다운 목록에서 복합 엔터티 이름을 선택합니다. 이 예제에서는 **TicketOrder**입니다.

    ![드롭다운 목록에서 복합 엔터티 이름이 강조 표시되고, 복합 엔터티에 단어가 래핑되는 스크린샷](./media/luis-how-to-add-example-utterances/wrap-4.png)

    엔터티를 올바르게 래핑하면 전체 구 아래에 녹색 줄이 표시됩니다.

    ![복합 엔터티가 강조 표시된 발언의 스크린샷](./media/luis-how-to-add-example-utterances/wrap-5.png)

    끝점 JSON 쿼리 응답에서 복합 엔터티를 추출하는 방법에 대해 자세히 알아보려면 [데이터 추출](luis-concept-data-extraction.md#composite-entity-data)을 참조하세요. 복합 엔터티 사용 방법에 대해 자세히 알아보려면 복합 엔터티 [자습서](luis-tutorial-composite-entity.md)를 참조하세요.

## <a name="add-hierarchical-entity-and-label"></a>계층 구조 엔터티 및 레이블 추가
계층 구조 엔터티는 컨텍스트에 따라 학습되고 개념적으로 관련이 있는 엔터티의 범주입니다. 다음 예제에서는 엔터티에는 출발지 및 목적지 위치가 포함되어 있습니다. 

발언 `Book 2 tickets from Seattle to Cairo`에서 Seattle은 출발지 위치이고 Cairo는 목적지 위치입니다. 각 위치는 컨텍스트가 다르며, 발언의 단어 순서 및 단어 선택에서 학습됩니다.

1. 의도 페이지의 발언에서 "Seattle"을 선택한 다음, 엔터티 이름으로 `Location을 입력하고 **새 엔터티 만들기**를 선택합니다.

    ![계층 구조 엔터티 레이블 만들기 대화 상자의 스크린샷](./media/luis-how-to-add-example-utterances/create-hier-ent-1.png)

2. 팝업 대화 상자에서 **엔터티 형식**으로 계층 구조를 선택하고 `Origin` 및 `Destination`을 자식으로 추가한 후 **완료**를 선택합니다.

    ![ToLocation 엔터티가 강조 표시된 의도 세부 정보 페이지의 스크린샷](./media/luis-how-to-add-example-utterances/create-location-hierarchical-entity.png)

3. 발언의 단어는 부모 계층 구조 엔터티로 레이블이 지정되었습니다. 자식 엔터티에 해당 단어를 할당해야 합니다. 의도 페이지의 해당 발언으로 돌아갑니다. 해당 단어를 선택하고 드롭다운 목록에서 만든 엔터티 이름을 선택한 후, 오른쪽 메뉴에 따라 올바른 자식 엔터티를 선택합니다.

    ![ToLocation 엔터티가 강조 표시된 의도 세부 정보 페이지의 스크린샷](./media/luis-how-to-add-example-utterances/label-tolocation.png)

    >[!CAUTION]
    >자식 엔터티 이름은 단일 앱의 모든 엔터티에서 고유해야 합니다. 서로 다른 두 계층 구조 엔터티는 이름이 같은 자식 엔터티를 포함할 수 없습니다. 

    끝점 JSON 쿼리 응답에서 계층적 엔터티를 추출하는 방법에 대해 자세히 알아보려면 [데이터 추출](luis-concept-data-extraction.md#hierarchical-entity-data)을 참조하세요. 계층적 엔터티 사용 방법에 대해 자세히 알아보려면 계층적 엔터티 [빠른 시작](luis-quickstart-intent-and-hier-entity.md)을 참조하세요.


## <a name="remove-entity-labels-from-utterances"></a>발언에서 엔터티 레이블 제거
의도 페이지의 발언에서 기계 학습된 엔터티 레이블을 제거할 수 있습니다. 엔터티가 기계 학습되지 않은 경우 발언에서 제거할 수 없습니다. 발언에서 기계 학습되지 않은 엔터티를 제거해야 할 경우에는 전체 앱에서 해당 엔터티를 삭제해야 합니다. 

발언에서 기계 학습된 엔터티 레이블을 제거하려면 발언에서 해당 엔터티를 선택합니다. 그런 후 나타나는 엔터티 드롭다운 상자에서 **레이블 제거**를 선택합니다.

![레이블 제거가 강조 표시된 의도 세부 정보 페이지의 스크린샷](./media/luis-how-to-add-example-utterances/remove-label.png) 

## <a name="add-prebuilt-entity-label"></a>미리 빌드된 엔터티 레이블 추가
LUIS 앱에 미리 빌드된 엔터티를 추가하는 경우 이러한 엔터티를 사용하여 발언에 레이블을 지정할 필요는 없습니다. 미리 빌드된 엔터티 및 추가 방법에 대한 자세한 내용은 [엔터티 추가](luis-how-to-add-entities.md#add-prebuilt-entity)를 참조하세요.

## <a name="add-regular-expression-entity-label"></a>정규식 엔터티 레이블 추가
LUIS 앱에 정규식 엔터티를 추가하는 경우 이러한 엔터티를 사용하여 발언에 레이블을 지정할 필요는 없습니다. 정규식 엔터티 및 추가 방법에 대한 자세한 내용은 [엔터티 추가](luis-how-to-add-entities.md#add-regular-expression-entities)를 참조하세요.

## <a name="create-a-pattern-from-an-utterance"></a>발언에서 패턴 만들기
[의도 또는 엔터티 페이지의 기존 발언에서 패턴 추가](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page)를 참조하세요.

## <a name="add-patternany-entity-label"></a>pattern.any 엔터티 레이블 추가
LUIS 앱에 pattern.any 엔터티를 추가하는 경우 이러한 엔터티를 사용하여 발언에 레이블을 지정할 수 없습니다. 이 레이블은 패턴에서만 유효합니다. pattern.any 엔터티 및 추가 방법에 대한 자세한 내용은 [엔터티 추가](luis-how-to-add-entities.md#add-patternany-entities)를 참조하세요.

<!--
Fix this - moved to luis-how-to-add-intents.md - how ?

## Search in utterances
## Prediction discrepancy errors
## Filter by intent prediction discrepancy errors
## Filter by entity type
## Switch to token view
## Delete utterances
## Edit an utterance
## Reassign utterances

-->
## <a name="train-your-app-after-changing-model-with-utterances"></a>발언이 포함된 모델을 변경한 후 앱 학습
발언을 추가, 편집 또는 제거한 후 변경 내용을 끝점 쿼리에 적용하려면 앱을 [학습](luis-how-to-train.md)시키고 [게시](PublishApp.md)합니다. 

## <a name="next-steps"></a>다음 단계

의도에서 발언에 레이블을 지정한 후에는 [복합 엔터티](luis-how-to-add-entities.md)를 만들 수 있습니다.
