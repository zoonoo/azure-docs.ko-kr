---
title: LUIS 앱에서 엔터티 추가 | Microsoft Docs
titleSuffix: Azure
description: LUIS(Language Understanding) 앱에서 엔터티(응용 프로그램 도메인의 키 데이터)를 추가합니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: diberry
ms.openlocfilehash: e97f9a5391799849983bd98db5400e0a842627b7
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224129"
---
# <a name="manage-entities"></a>엔터티 관리
앱의 [의도](luis-concept-intent.md)를 확인한 후 [엔터티](luis-concept-entity-types.md)를 사용해서 [예제 발언에 레이블을 지정](luis-concept-utterance.md)해야 합니다. 엔터티는 명령 또는 질문의 중요한 부분으로, 클라이언트 앱이 해당 작업을 수행하는 데 반드시 필요할 수 있습니다. 

**엔터티** 페이지의 **엔터티 목록**을 통해 앱에서 엔터티를 추가, 편집 또는 삭제할 수 있습니다. LUIS는 두 가지 유형의 주요 엔터티인 [미리 빌드된 엔터티](luis-reference-prebuilt-entities.md) 및 사용자 고유의 사용자 지정 엔터티를 제공합니다.

다음 섹션은 LUIS 앱 내의 **빌드** 섹션에서만 사용할 수 있습니다. **빌드** 링크는 위쪽 탐색 모음에 있습니다. 일단 **빌드** 섹션에 들어간 경우 왼쪽 탐색 메뉴에서 **엔터티**를 선택합니다. 엔터티가 응용 프로그램에 추가되고 나면, 엔터티가 기계 학습된 경우 발언 내에서 [엔터티에 레이블을 지정](luis-how-to-add-example-utterances.md)할 수 있습니다. 앱이 학습되고 게시되면 예측에서 [추출](luis-concept-data-extraction.md)된 엔터티 데이터를 받을 수 있습니다. 

## <a name="add-prebuilt-entity"></a>미리 빌드된 엔터티 추가
미리 빌드된 엔터티는 [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) 프로젝트에 정의되어 있습니다. 응용 프로그램에 추가되는 일반적인 미리 빌드된 엔터티는 *number*와 *datetimeV2*입니다. 

1. 앱의 **빌드** 섹션에서 왼쪽 패널의 **엔터티**를 클릭합니다.
 
2. **엔터티** 페이지에서 **미리 빌드된 엔터티 관리**를 선택합니다.

    ![엔터티 페이지의 미리 빌드된 엔터티 추가 스크린샷](./media/add-entities/manage-prebuilt-entities-button.png)

3. **미리 빌드된 엔터티 추가 또는 제거** 대화 상자에서 **number** 및 **datetimeV2** 미리 빌드된 엔터티를 선택합니다. 그런 후 **완료**를 선택합니다.

    ![미리 빌드된 엔터티 추가 대화 상자 스크린샷](./media/add-entities/list-of-prebuilt-entities.png)

    끝점 JSON 쿼리 응답에서 미리 빌드된 엔터티를 추출하는 방법에 대해 자세히 알아보려면 [데이터 추출](luis-concept-data-extraction.md#prebuilt-entity-data)을 참조하세요.

## <a name="add-simple-entities"></a>간단한 엔터티 추가
단순 엔터티는 단일 개념을 설명하는 일반 엔터티입니다. 

1. 앱의 **빌드** 섹션에서 왼쪽 패널의 **엔터티**를 클릭하고 **새 엔터티 만들기**를 선택합니다.

    ![새 엔터티 만들기 단추가 강조 표시된 엔터티 페이지의 스크린샷](./media/add-entities/create-new-entity-button.png)

2. 팝업 대화 상자의 **엔터티 이름** 상자에 `Airline`을 입력하고 **엔터티 형식**에서 **단순**을 선택한 후 **완료**를 선택합니다.

    ![간단한 Airline 엔터티를 만들기 위한 대화 상자 스크린샷](./media/add-entities/create-simple-airline-entity.png)

    끝점 JSON 쿼리 응답에서 단순 엔터티를 추출하는 방법에 대해 자세히 알아보려면 [데이터 추출](luis-concept-data-extraction.md#simple-entity-data)을 참조하세요. 단순 엔터티 사용 방법에 대해 자세히 알아보려면 단순 엔터티 [빠른 시작](luis-quickstart-primary-and-secondary-data.md)을 참조하세요.

## <a name="add-regular-expression-entities"></a>정규식 엔터티 추가
정규식 엔터티는 제공된 정규식에 따라 발언에서 데이터를 끌어오는 데 사용됩니다. 

1. 앱의 왼쪽 탐색 영역에서 **엔터티**를 선택하고 **새 엔터티 만들기**를 선택합니다.

2. 팝업 대화 상자의 **엔터티 이름** 상자에 `AirFrance Flight`를 입력하고 **엔터티 형식** 목록에서 **정규식**을 선택하고 정규식 `AFR[0-9]{3,4}`를 입력한 후 **완료**를 선택합니다. 

    이 AirFrance Flight 정규식에서는 3개의 문자(글자 그대로 `AFR`)와 3 또는 4자리 숫자가 나와야 합니다. 숫자는 0 ~ 9 사이의 어떤 숫자도 가능합니다. 이 정규식은 "AFR101", "ARF1302" 및 "AFR5006"과 같은 AirFrance 항공편 번호를 일치하는 항목으로 검색합니다. 끝점 JSON 쿼리 응답에서 엔터티를 추출하는 방법에 대해 자세히 알아보려면 [데이터 추출](luis-concept-data-extraction.md)을 참조하세요.

    ![정규식 엔터티 만들기 대화 상자의 이미지](./media/add-entities/regex-entity-create-dialog.png)

    끝점 JSON 쿼리 응답에서 정규식 엔터티를 추출하는 방법에 대해 자세히 알아보려면 [데이터 추출](luis-concept-data-extraction.md#regular-expression-entity-data)을 참조하세요. 정규식 엔터티 사용 방법에 대해 자세히 알아보려면 정규식 엔터티 [빠른 시작](luis-quickstart-intents-regex-entity.md)을 참조하세요.

## <a name="add-hierarchical-entities"></a>계층 구조 엔터티 추가
계층 구조 엔터티는 컨텍스트에 따라 학습되고 개념적으로 관련이 있는 엔터티의 범주입니다. 다음 예제에서는 엔터티에는 출발지 및 목적지 위치가 포함되어 있습니다. 

발언 `Book 2 tickets from Seattle to Cairo`에서 Seattle은 출발지 위치이고 Cairo는 목적지 위치입니다. 각 위치는 컨텍스트가 다르며, 발언의 단어 순서 및 단어 선택에서 학습됩니다.

계층 구조 엔터티를 추가하려면 다음 단계를 수행합니다. 

1. 앱의 왼쪽 탐색 영역에서 **엔터티**를 선택하고 **새 엔터티 만들기**를 선택합니다.

2. 팝업 대화 상자의 **엔터티 이름** 상자에 `Location`을 입력하고 **엔터티 형식** 목록에서 **계층 구조**를 선택합니다.

    ![계층적 엔터티 추가](./media/add-entities/hier-location-entity-creation.png)

3. **자식 추가**를 선택하고 **자식 #1** 상자에 "Origin"을 입력합니다. 

4. **자식 추가**를 선택하고 **자식 #2** 상자에 "Destination"을 입력합니다. **완료**를 선택합니다.
5. 
    >[!NOTE]
    >자식을 삭제하려면 옆에 있는 휴지통 아이콘을 선택합니다.

    >[!CAUTION]
    >자식 엔터티 이름은 단일 앱의 모든 엔터티에서 고유해야 합니다. 서로 다른 두 계층 구조 엔터티는 이름이 같은 자식 엔터티를 포함할 수 없습니다. 

    끝점 JSON 쿼리 응답에서 계층적 엔터티를 추출하는 방법에 대해 자세히 알아보려면 [데이터 추출](luis-concept-data-extraction.md#hierarchical-entity-data)을 참조하세요. 계층적 엔터티 사용 방법에 대해 자세히 알아보려면 계층적 엔터티 [빠른 시작](luis-quickstart-intent-and-hier-entity.md)을 참조하세요.

## <a name="add-composite-entities"></a>복합 엔터티 추가
복합 엔터티를 만들어 여러 기존 엔터티 간 관계를 정의할 수 있습니다. 다음 예제에서 엔터티에는 티켓 수, 출발지 및 목적지 위치가 포함되어 있습니다. 

발언 `Book 2 tickets from Seattle to Cairo`에서 숫자 2는 미리 빌드된 엔터티와 일치하는지 확인되며, Seattle은 출발지 위치이고 Cairo는 목적지 위치입니다. 복합 엔터티가 생성된 후 각 엔터티는 더 큰 부모 엔터티에 속하게 됩니다.

1. 앱에서 미리 빌드된 엔터티 **number**를 추가합니다. 지침을 보려면 [미리 빌드된 엔터티 추가](#add-prebuilt-entity)를 참조하세요. 

2. 하위 형식 `origin`, `destination`을 포함하는 계층 구조 엔터티 `Location`을 추가합니다. 자세한 지침은 [계층 구조 엔터티 추가](#add-hierarchical-entities)를 참조하세요. 

3. 왼쪽 탐색 영역에서 **엔터티**를 선택하고 **새 엔터티 만들기**를 선택합니다.

4. 팝업 대화 상자의 **엔터티 이름** 상자에 `TicketsOrder`을 입력하고 **엔터티 형식** 목록에서 **복합**을 선택합니다.

5. **자식 추가**를 선택하여 새 자식을 추가합니다.

6. **자식 #1**에서 목록에 있는 **number** 엔터티를 선택합니다.

7. **자식 #2**에서 목록에 있는 **Location::Origin** 엔터티를 선택합니다. 

8. **자식 #3**에서 목록에 있는 **Location::Destination** 엔터티를 선택합니다. 

9. **완료**를 선택합니다.

    ![복합 엔터티를 만드는 대화 상자 이미지](./media/add-entities/ticketsorder-composite-entity.png)

    >[!NOTE]
    >자식을 삭제하려면 옆에 있는 휴지통 단추를 선택합니다.

    끝점 JSON 쿼리 응답에서 복합 엔터티를 추출하는 방법에 대해 자세히 알아보려면 [데이터 추출](luis-concept-data-extraction.md#composite-entity-data)을 참조하세요. 복합 엔터티 사용 방법에 대해 자세히 알아보려면 복합 엔터티 [자습서](luis-tutorial-composite-entity.md)를 참조하세요.


## <a name="add-patternany-entities"></a>Pattern.any 엔터티 추가
[Pattern.any](luis-concept-entity-types.md) 엔터티는 [패턴](luis-how-to-model-intent-pattern.md)에서만 유효합니다. 이 엔터티는 LUIS가 다양한 길이 및 단어 선택 항목을 포함하는 엔터티의 끝을 찾는 데 도움을 줍니다. 이 엔터티는 패턴에서 사용되므로 LUIS는 발언에서 엔터티의 끝 위치를 알 수 있습니다.

앱에 `FindBookInfo` 의도가 있는 경우 책 제목이 의도 예측에 방해가 될 수 있습니다. 책 제목에 있는 단어를 명확하게 하기 위해 패턴 내에 Pattern.any를 사용합니다. LUIS 예측은 발언에서 시작됩니다. 먼저 발언이 확인되고 엔터티에 대해 일치된 후, 엔터티가 검색되면 패턴이 확인되고 일치됩니다. 

발언 `Who wrote the book Ask and when was it published?`에서 제목이 끝나는 위치와 발언의 나머지가 시작되는 위치가 문맥상 명확하지 않으므로 책 제목인 Ask를 알아내는 것이 어렵습니다. 책 제목은 단일 단어, 문장 부호가 포함된 복합 구 및 무의미한 단어 배열을 포함하여 어떤 순서의 단어도 될 수 있습니다. 전체 및 정확한 엔터티를 추출할 수 있는 경우 패턴을 사용하여 엔터티를 만들 수 있습니다. 책 제목이 검색되면 패턴의 의도에 해당하는 `FindBookInfo` 의도가 예측됩니다.

1. 앱의 **빌드** 섹션에서 왼쪽 패널의 **엔터티**를 클릭하고 **새 엔터티 만들기**를 선택합니다.

2. **엔터티 추가** 대화 상자에서 **엔터티 이름** 상자에 `BookTitle`을 입력하고 **엔터티 형식**으로 **Pattern.any**를 선택합니다.
 
    ![Pattern.any 엔터티 만들기 스크린샷](./media/add-entities/create-pattern-any-entity.png)

    Pattern.any 엔터티를 사용하려면 올바른 중괄호 구문(예: "For **{BookTitle}** who is the author?")을 사용하여 **패턴** 페이지(**앱 성능 개선** 섹션 아래에 있음)에서 패턴을 추가합니다.

    끝점 JSON 쿼리 응답에서 Pattern.any 엔터티를 추출하는 방법에 대해 자세히 알아보려면 [데이터 추출](luis-concept-data-extraction.md#patternany-entity-data)을 참조하세요. Pattern.any 엔터티를 사용하는 방법을 자세히 알아보려면 [패턴](luis-tutorial-pattern.md) 자습서를 진행해보세요.

Pattern.any가 포함된 패턴이 엔터티를 잘못 추출한 것을 발견하면 [명시적 목록](luis-concept-patterns.md#explicit-lists)을 사용하여 이 문제를 정정합니다. 

## <a name="add-role-to-pattern-based-entity"></a>패턴 기반 엔터티에 역할 추가
역할은 컨텍스트를 기준으로 하는 엔터티의 명명된 하위 형식입니다. [계층 구조](#add-hierarchical-entities) 엔터티와 유사하지만 역할은 [패턴](luis-how-to-model-intent-pattern.md)에서만 사용됩니다. 

예를 들어, 비행기 티켓에는 *출발지 도시* 및 *도착지 도시*가 있지만 둘 다 도시입니다. LUIS는 단어 순서 및 단어 항목 컨텍스트에 따라 둘 다 도시인지 확인하고 출발지 및 도착지 도시를 확인할 수 있습니다. 

역할 구문은 **{엔터티 이름:역할 이름}** 입니다. 즉, 엔터티 이름 뒤에 콜론이 온 후 역할 이름이 나옵니다. 예를 들면 "Book a ticket from {Location:Origin} to {Location:Destination}"과 같습니다.

1. 앱의 **빌드** 섹션에서 왼쪽 패널의 **엔터티**를 선택합니다.

2. **새 엔터티 만들기**를 선택합니다. `Location`의 이름을 입력합니다. 형식 **단순**을 선택하고 **완료**를 선택합니다.

3. 왼쪽 패널에서 **엔터티**를 선택하고 2단계에서 만든 새 엔터티 **Location**을 선택합니다.

4. **역할 이름** 텍스트 상자에 역할 이름 `Origin`을 입력한 후 Enter 키를 누릅니다. 두 번째 역할 이름 `Destination`을 추가합니다. 예를 들어 비행기 여행에는 출발지 도시와 목적지 도시가 있을 수 있습니다. 두 역할은 "Origin" 및 "Destination"입니다.

    ![Location 엔터티에 Origin 역할을 추가하는 스크린샷](./media/add-entities/roles-enter-role-name-text.png)

    끝점 JSON 쿼리 응답에서 역할을 추출하는 방법에 대해 자세히 알아보려면 [데이터 추출](luis-concept-data-extraction.md)을 참조하세요. Pattern.any 엔터티를 사용하는 방법을 자세히 알아보려면 패턴 자습서를 진행해보세요.

## <a name="add-list-entities"></a>목록 엔터티 추가
목록 엔터티는 시스템의 관련된 단어의 고정된 폐쇄형 집합을 나타냅니다. 

drinks 목록 엔터티에 대해 water와 soda pop이라는 두 가지 정규화된 값을 가질 수 있습니다. 정규화된 각 이름에는 동의어가 있습니다. water의 경우 동의어는 H20, gas, flat입니다. soda pop의 경우 동의어는 fruit, cola, ginger입니다. 엔터티를 만들 때 모든 값을 알 필요는 없습니다. 동의어를 포함하는 실제 사용자 발언을 검토한 후에 더 많은 발언을 추가할 수 있습니다.

|정규화된 이름|동의어|
|--|--|
|Water|H20, gas, flat|
|Soda pop|Fruit, cola, ginger|

1. 앱의 **빌드** 섹션에서 왼쪽 패널의 **엔터티**를 클릭하고 **새 엔터티 만들기**를 선택합니다.

2. **엔터티 추가** 대화 상자에서 **엔터티 이름** 상자에 `Drinks`을 입력하고 **엔터티 형식**으로 **List**를 선택합니다. **완료**를 선택합니다.
 
    ![Drinks 목록 엔터티 만들기 대화 상자 이미지](./media/add-entities/menu-list-dialog.png)
  
3.  목록 엔터티 페이지에서 정규화된 이름을 추가할 수 있습니다. **값** 텍스트 상자에 목록의 항목(예: drinks 목록의 경우 `Water`)을 입력한 후 키보드에서 Enter 키를 누릅니다. 

    ![텍스트 상자에 정규화된 값 water가 표시되는 Drinks 목록 엔터티의 스크린샷](./media/add-entities/entity-list-normalized-name.png)

4. 정규화된 값 **water** 오른쪽에 동의어 `h20`, `flat` 및 `gas`를 입력하고 각 항목을 입력한 후에는 키보드에서 Enter 키를 누릅니다.

    ![water에 대한 동의어가 강조 표시된 Drinks 목록 엔터티의 스크린샷](./media/add-entities/menu-list-synonyms.png)

5. 목록에 대해 정규화된 항목을 더 원할 경우 **권장**을 선택하여 [의미 사전](luis-glossary.md#semantic-dictionary)의 옵션을 표시합니다.

    ![권장된 항목이 강조 표시된 Drinks 목록 엔터티의 스크린샷](./media/add-entities/entity-list-recommended-list.png)

6. 권장 목록에서 항목을 선택하여 정규화된 값으로 추가하거나 **모두 추가**를 선택하여 모든 항목을 추가합니다. 

    ![권장된 항목 Beer와 모두 추가 단추가 강조 표시된 Drinks 목록 엔터티의 스크린샷](./media/add-entities/list-entity-add-suggestions.png)

    끝점 JSON 쿼리 응답에서 목록 엔터티를 추출하는 방법에 대해 자세히 알아보려면 [데이터 추출](luis-concept-data-extraction.md#list-entity-data)을 참조하세요. 목록 엔터티 사용 방법에 대해 자세히 알아보려면 [빠른 시작](luis-quickstart-intent-and-list-entity.md)을 참조하세요.

## <a name="import-list-entity-values"></a>목록 엔터티 값 가져오기
값을 기존 목록 엔터티로 가져올 수 있습니다.

 1. 목록 엔터티 페이지에서 **목록 가져오기**를 선택합니다.

 2. **새 항목 가져오기** 대화 상자에서 **파일 선택**을 선택하고 해당 목록을 포함하는 JSON 파일을 선택합니다.

    ![목록 엔터티 값 가져오기 팝업 대화 상자의 스크린샷](./media/add-entities/menu-list-import-json-dialog-with-file.png)

    >[!NOTE]
    >LUIS는 확장명이 ".json"인 파일만 가져옵니다.

 3. JSON에서 지원되는 목록 구문에 대해 자세히 알아보려면 **지원되는 목록 구문에 대해 자세히 알아보기**를 선택하여 대화 상자를 확장하고 허용되는 구문 예제를 표시합니다. 대화 상자를 축소하고 구문을 숨기려면 링크 제목을 다시 선택합니다.

 4. **완료**를 선택합니다.

    **Colors** 목록 엔터티에 유효한 json 예제는 다음 JSON 형식의 코드에 표시됩니다.

    ```
    [
        {
            "canonicalForm": "Blue",
            "list": [
                "navy",
                "royal",
                "baby"
            ]
        },
        {
            "canonicalForm": "Green",
            "list": [
                "kelly",
                "forest",
                "avacado"
            ]
        }
    ]  
    ```

## <a name="edit-entity-name"></a>엔터티 이름 편집
1. **엔터티** 목록 페이지의 목록에서 엔터티를 선택합니다. 이 작업을 수행하면 **엔터티** 페이지로 이동됩니다.

2. **엔터티** 페이지에서 엔터티 이름 옆의 편집 아이콘을 선택하여 엔터티 이름을 편집합니다. 엔터티 형식은 편집할 수 없습니다. 

## <a name="delete-entity"></a>엔터티 삭제

**엔터티** 페이지에서 **엔터티 삭제** 단추를 선택합니다. 그런 다음, 확인 메시지에서 **확인**을 선택하여 삭제를 확인합니다.
 
![엔터티 삭제 단추가 강조 표시된 Location 엔터티 페이지 스크린샷](./media/add-entities/entity-delete.png)

>[!NOTE]
>* 계층 구조 엔터티를 삭제하면 자식 엔터티도 모두 삭제됩니다.
>* 복합 엔터티를 삭제하면 복합 요소만 삭제되고, 복잡 관계가 끊어지지만 해당 요소를 형성하는 엔터티는 삭제되지 않습니다.

## <a name="changing-entity-type"></a>엔터티 형식 변경
LUIS에서는 해당 엔터티를 생성하기 위해 추가 또는 제거할 항목을 잘 모르기 때문에 엔터티의 형식을 변경하도록 허용하지 않습니다. 형식을 변경하기 위해서는 약간 다른 이름을 사용하여 올바른 형식의 새 엔터티를 만드는 것이 좋습니다. 엔터티가 만들어지면 각 발언에서 이전 레이블이 지정된 엔터티 이름을 제거한 후 새 엔터티 이름을 추가합니다. 모든 발언에 레이블이 다시 지정되면 이전 엔터티를 삭제합니다. 

## <a name="create-a-pattern-from-an-utterance"></a>발언에서 패턴 만들기
[의도 또는 엔터티 페이지의 기존 발언에서 패턴 추가](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page)를 참조하세요.

## <a name="search-utterances"></a>발언 검색
도구 모음의 돋보기 아이콘을 사용하여 발언을 검색하고 필터링할 수 있습니다. 

## <a name="train-your-app-after-changing-model-with-entities"></a>엔터티가 포함된 모델을 변경한 후 앱 학습
엔터티를 추가, 편집 또는 제거한 후 변경 내용을 끝점 쿼리에 적용하려면 앱을 [학습](luis-how-to-train.md)시키고 [게시](luis-how-to-publish-app.md)합니다. 

## <a name="next-steps"></a>다음 단계
의도, 발언 및 엔터티를 추가했으므로 기본 LUIS 앱이 구성되었습니다. 앱을 [학습](luis-how-to-train.md), [테스트](luis-interactive-test.md) 및 [게시](luis-how-to-publish-app.md)하는 방법을 알아봅니다.
 
