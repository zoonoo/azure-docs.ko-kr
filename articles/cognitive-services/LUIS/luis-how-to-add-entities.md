---
title: 엔터티 추가-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS (user 길이 발언 in Language Understanding) 앱에서 키 데이터를 추출 하는 엔터티를 만듭니다. 추출 된 엔터티 데이터는 클라이언트 응용 프로그램에서 고객 요청을 fullfil 하는 데 사용 됩니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 7de1a1e24c2863b90fe5f1f3ff19124318912cff
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132680"
---
# <a name="add-entities-to-extract-data"></a>데이터를 추출 하는 엔터티 추가 

LUIS (user 길이 발언 in Language Understanding) 앱에서 키 데이터를 추출 하는 엔터티를 만듭니다. 추출 된 엔터티 데이터는 클라이언트 응용 프로그램에서 고객 요청을 fullfil 하는 데 사용 됩니다.

엔터티는 추출하려는 발화 내의 단어 또는 구를 나타냅니다. 엔터티는 의도와 관련된 정보를 설명하며 앱이 작업을 수행하는 데 꼭 필요한 경우도 있습니다. 의도에 예제 utterance를 추가 하거나 (before 또는 after) utterance 예제를 의도에 추가 하면 엔터티를 만들 수 있습니다.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="plan-entities-then-create-and-label"></a>엔터티 계획, 만들기 및 레이블 만들기

컴퓨터에서 배운 엔터티는 예제 길이 발언에서 만들거나 **엔터티** 페이지에서 만들 수 있습니다. 

일반적으로 포털에서 컴퓨터에서 학습 한 엔터티를 만들기 전에 엔터티를 계획 하는 데 시간을 할애 하는 것이 가장 좋습니다. 그런 다음 utterance 예제에서 컴퓨터에서 학습 한 엔터티를 만들고, 해당 시간에 알고 있는 대로 하위 구성 요소 및 설명자와 제약 조건에 자세히 설명 합니다. [없습니다 entity 자습서](tutorial-machine-learned-entity.md) 에서는이 메서드를 사용 하는 방법을 보여 줍니다. 

엔터티를 계획 하는 과정에서 텍스트 일치 엔터티 (예: 미리 작성 된 엔터티, 정규식 엔터티 또는 목록 엔터티)가 필요 하다는 것을 알 수 있습니다. 예 길이 발언에서 레이블을 지정 하기 전에 **엔터티** 페이지에서 만들 수 있습니다. 

레이블을 지정 하는 경우 개별 엔터티의 레이블을 지정 하 고 부모 컴퓨터에서 학습 한 엔터티까지 빌드할 수 있습니다. 또는 부모 컴퓨터에서 학습 한 엔터티로 시작 하 고 자식 엔터티로 분해할 수 있습니다. 

> [!TIP] 
>클라이언트 응용 프로그램에서 추출할 때 단어가 사용 되지 않더라도 엔터티를 나타낼 수 있는 모든 단어에 레이블을 지정 합니다. 

## <a name="creating-an-entity-before-or-with-labeling"></a>레이블 앞에 또는 레이블 지정을 사용 하 여 엔터티 만들기

다음 표를 사용 하 여 각 엔터티를 만들거나 앱에 추가할 엔터티를 파악 합니다. 

|엔터티 형식|LUIS 포털에서 엔터티를 만들 위치|
|--|--|
|기계 학습된 엔터티|엔터티 또는 의도 세부 정보|
|목록 엔터티|엔터티 또는 의도 세부 정보|
|정규식 엔터티|엔터티|
|Pattern.any 엔터티|엔터티|
|미리 빌드된 엔터티|엔터티|
|미리 빌드된 도메인 엔터티|엔터티|

**엔터티 페이지에서** 모든 엔터티를 만들거나, **의도 세부 정보** 페이지의 예제 utterance에서 엔터티 레이블 지정의 일부로 두 엔터티를 만들 수 있습니다. **의도 세부 정보** 페이지의 utterance 예에서는 엔터티의 _레이블을_ 지정할 수 있습니다. 

## <a name="create-a-machine-learned-entity"></a>컴퓨터에서 학습 한 엔터티 만들기

[!INCLUDE [Create and label entities in machine-learned tutorial](includes/decomposable-tutorial-links.md)]

## <a name="create-a-text-matching-entity"></a>텍스트 일치 엔터티 만들기

텍스트 일치 엔터티를 사용 하 여 데이터를 추출 하는 여러 가지 방법을 제공 합니다.

|텍스트 일치 엔터티|목적|
|--|--|
|[엔터티 나열](#add-list-entities-for-exact-matches)|다른 형태의 동의어와 함께 정식 이름 목록|
|정규식 엔터티|정규식 엔터티를 사용 하 여 텍스트 일치|
|[미리 작성 한 엔터티](tutorial-machine-learned-entity.md#add-prebuilt-number-to-app-to-help-extract-data)|숫자, 전자 메일, 날짜 등의 공통 데이터 형식 일치|
|미리 빌드된 도메인 엔터티|선택한 주체 도메인을 사용한 일치|
|[Pattern.any](#add-a-patternany-entity)| 주변 텍스트와 쉽게 혼동 될 수 있는 엔터티를 일치 시키려면|  

미리 작성 된 엔터티는 사용자 지정 학습 데이터를 제공 하지 않고 작동 합니다. 다른 엔터티에는 고객 학습 데이터 (예: 엔터티 항목 나열) 또는 식 (예: 정규식 또는 패턴)을 제공 해야 합니다.

<a name="add-list-entities"></a>

### <a name="how-to-create-a-new-custom-entity"></a>새 사용자 지정 엔터티를 만드는 방법

1. LUIS 포털에서 **관리** 섹션, **엔터티** 페이지로 이동 합니다. 
1. **+ 만들기**를 선택한 다음 엔터티 형식을 선택 합니다. 
1. 엔터티를 계속 구성 하 고 완료 되 면 **만들기** 를 선택 합니다. 

### <a name="add-list-entities-for-exact-matches"></a>정확히 일치 하는 항목에 대 한 목록 엔터티 추가

목록 엔터티는 관련 단어의 고정된 폐쇄형 집합을 나타냅니다. 작성자가 목록을 변경할 수 있는 반면 LUIS는 목록을 확장 하거나 축소할 수 없습니다. [List entity. json 형식 (참조)을 사용 하 여 기존 목록 엔터티로 가져올 수도 있습니다 (예: json--목록-엔터티). 

다음 목록에서는 정식 이름과 동의어를 보여 줍니다. 

|색 목록 항목 이름|색-동의어|
|--|--|
|빨강|크림슨, 피, apple, 화재-엔진|
|파랑|하늘, azure, 코발트|
|녹색|최소라, 라임|

목록 엔터티를 만들려면 절차를 사용 합니다. 목록 엔터티를 만든 후에는 길이 발언 예제에 레이블을 추가할 필요가 없습니다. 목록 항목과 동의어가 정확히 일치 하는 텍스트를 사용 하 여 일치 합니다. 

1. **빌드** 섹션에서 왼쪽 패널의 **엔터티** 를 선택 하 고 **+ 만들기**를 선택 합니다.

1. **엔터티 형식 만들기** 대화 상자에서 `Colors`와 같은 엔터티 이름을 입력 하 고 **목록**을 선택 합니다.
1. **목록 엔터티 만들기** 대화 상자의 새 하위 목록 **추가 ...** 에서 `Green`와 같은 목록 항목 이름을 입력 한 다음 동의어를 추가 합니다.

    > [!div class="mx-imgBorder"]
    > ![엔터티 세부 정보 페이지에서 목록 엔터티로 색 목록을 만듭니다.](media/how-to-add-entities/create-list-entity-of-colors.png) 

1. 목록 항목과 동의어를 모두 추가 했으면 **만들기**를 선택 합니다.

    앱에 대 한 변경 그룹을 완료 한 후에는 앱을 **학습** 해야 합니다. 단일 변경 후에는 앱을 학습 하지 마십시오. 

    > [!NOTE]
    > 이 절차에서는 **의도 세부 정보** 페이지의 예제 utterance에서 목록 엔터티를 만들고 레이블을 지정 하는 방법을 보여 줍니다. **엔터티** 페이지에서 동일한 엔터티를 만들 수도 있습니다.

## <a name="add-a-role-for-an-entity"></a>엔터티에 대 한 역할 추가

역할은 컨텍스트에 따라 엔터티의 이름이 지정 된 하위 형식입니다. 

### <a name="add-a-role-to-distinguish-different-contexts"></a>다른 컨텍스트를 구분 하는 역할 추가

다음 utterance에는 두 가지 위치가 있으며 각 위치는 `to` 및 `from`와 같은 주변 단어로 지정 됩니다. 

`Pick up the package from Seattle and deliver to New York City.`

이 절차에서는 `origin` 및 `destination` 역할을 미리 작성 된 geographyV2 엔터티에 추가 합니다.

1. **빌드** 섹션에서 왼쪽 패널의 **엔터티**를 선택합니다.

1. **+ 미리 작성 한 엔터티 추가**를 선택 합니다. **GeographyV2** 를 선택한 다음 **완료**를 선택 합니다. 이렇게 하면 미리 빌드된 엔터티가 앱에 추가 됩니다.
    
    Pattern.any가 포함된 패턴이 엔터티를 잘못 추출한 것을 발견하면 [명시적 목록](reference-pattern-syntax.md#explicit-lists)을 사용하여 이 문제를 정정합니다. 

1. **엔터티 페이지 엔터티** 목록에서 새로 추가 된 미리 작성 된 geographyV2 엔터티를 선택 합니다. 
1. 새 역할을 추가 하려면 **역할 추가 안 함**옆의 **+** 을 선택 합니다.
1. **유형 역할 ...** 텍스트 상자에 역할의 이름을 입력 하 `Origin` 다음을 입력 합니다. `Destination`의 두 번째 역할 이름을 추가 하 고 enter 키를 누릅니다. 

    > [!div class="mx-imgBorder"]
    > 위치 엔터티에 원본 역할을 추가 하는 ![스크린샷](media/how-to-add-entities//add-role-to-prebuilt-geographyv2-entity.png)

    이 역할은 미리 작성 된 엔터티에 추가 되지만 해당 엔터티를 사용 하는 길이 발언에는 추가 되지 않습니다. 

### <a name="label-text-with-a-role-in-an-example-utterance"></a>예 utterance에서 역할을 사용 하 여 텍스트 레이블

1. 역할을 사용 하는 예제 길이 발언를 포함 하는 의도 세부 정보 페이지로 이동 합니다. 
1. 역할을 사용 하 여 레이블을 적용 하려면 예제 utterance에서 엔터티 레이블 (텍스트 아래 실선)을 선택 하 고 드롭다운 목록에서 **엔터티 팔레트에서 보기** 를 선택 합니다. 

    > [!div class="mx-imgBorder"]
    > 엔터티 팔레트에서 보기를 선택 하는 ![스크린샷](media/how-to-add-entities/select-text-label-with-entity-palette-for-role.png)   

    엔터티 팔레트가 오른쪽에 열립니다. 

1. 엔터티를 선택 하 고 팔레트 아래쪽으로 이동 하 여 역할을 선택 합니다. 

    > [!div class="mx-imgBorder"]
    > 엔터티 팔레트에서 보기를 선택 하는 ![스크린샷](media/how-to-add-entities/select-role-from-entity-palette-entity-inspector.png)

<a name="add-pattern-any-entities"></a>

## <a name="add-a-patternany-entity"></a>패턴 추가. 모든 엔터티

[패턴. 모든](luis-concept-entity-types.md) 엔터티는 의도 하지 않은 ' 예 길이 발언 ' [패턴](luis-how-to-model-intent-pattern.md)에서만 유효 합니다. 이 유형의 엔터티는 LUIS가 다양한 길이 및 단어 선택이 포함된 엔터티의 끝을 찾는 데 도움을 줍니다. 이 엔터티는 패턴에 사용되므로 LUIS는 발화 템플릿에서 엔터티의 끝 위치를 알 수 있습니다.

### <a name="steps-to-create-a-patternany-entity"></a>패턴을 만드는 단계. 모든 엔터티

1. **빌드** 섹션에서 왼쪽 패널의 **엔터티** 를 선택 하 고 **+ 만들기**를 선택 합니다.

1. **엔터티 형식 선택** 대화 상자에서 **이름** 상자에 엔터티 이름을 입력 하 고 **패턴. Any** 를 **형식** 으로 선택한 다음 **만들기**를 선택 합니다.

    이 엔터티를 사용 하 여 [utterance 패턴을 만든](luis-how-to-model-intent-pattern.md) 후에는 결합 된 컴퓨터에서 배운 텍스트 일치 알고리즘을 사용 하 여 엔터티를 추출 합니다. 

### <a name="create-a-pattern-template-utterance-to-use-patternany-entity"></a>패턴을 사용할 패턴 템플릿 utterance를 만듭니다. 모든 엔터티

pattern.any 엔터티를 사용하려면 올바른 중괄호 구문(예: **)을 사용하여** 앱 성능 개선**섹션의**패턴`Where is **{HumanResourcesFormTitle}** on the server?` 페이지에서 패턴을 추가합니다.

Pattern.any가 포함된 패턴이 엔터티를 잘못 추출한 것을 발견하면 [명시적 목록](reference-pattern-syntax.md#explicit-lists)을 사용하여 이 문제를 정정합니다. 

## <a name="do-not-change-entity-type"></a>엔터티 형식 변경 안 함

LUIS에서는 해당 엔터티를 생성하기 위해 추가 또는 제거할 항목을 잘 모르기 때문에 엔터티의 형식을 변경하도록 허용하지 않습니다. 형식을 변경하기 위해서는 약간 다른 이름을 사용하여 올바른 형식의 새 엔터티를 만드는 것이 좋습니다. 엔터티가 만들어지면 각 발언에서 이전 레이블이 지정된 엔터티 이름을 제거한 후 새 엔터티 이름을 추가합니다. 모든 발언에 레이블이 다시 지정되면 이전 엔터티를 삭제합니다. 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="next-steps"></a>다음 단계

패턴에 대해 자세히 알아보세요.

* [패턴 개념](luis-concept-patterns.md)
* [패턴 구문](reference-pattern-syntax.md)

미리 빌드된 엔터티에 대한 자세한 내용은 [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) 프로젝트를 참조하세요. 

JSON 엔드포인트 쿼리 응답에 엔터티가 표시되는 방법에 대한 자세한 내용은 [데이터 추출](luis-concept-data-extraction.md) 참조

의도, 발언 및 엔터티가 추가되어 기본 LUIS 앱이 구성되었습니다. 앱을 [학습](luis-how-to-train.md), [테스트](luis-interactive-test.md) 및 [게시](luis-how-to-publish-app.md)하는 방법을 알아봅니다.
 
