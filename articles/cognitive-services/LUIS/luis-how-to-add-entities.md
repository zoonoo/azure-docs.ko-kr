---
title: 엔터티 추가 - LUIS
titleSuffix: Azure Cognitive Services
description: 개체를 만들어 LUIS(언어 이해) 앱에서 사용자 발언에서 주요 데이터를 추출합니다. 추출된 엔터티 데이터는 클라이언트 응용 프로그램에서 전체 정보 고객 요청에 사용됩니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 1f2b293acdc77e25e6b932c47d466cc28a04a2b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221496"
---
# <a name="add-entities-to-extract-data"></a>데이터 추출에 엔터티 추가 

개체를 만들어 LUIS(언어 이해) 앱에서 사용자 발언에서 주요 데이터를 추출합니다. 추출된 엔터티 데이터는 클라이언트 응용 프로그램에서 전체 고객 요청에 사용됩니다.

엔터티는 추출하려는 발화 내의 단어 또는 구를 나타냅니다. 엔터티는 의도와 관련된 정보를 설명하며 앱이 작업을 수행하는 데 꼭 필요한 경우도 있습니다. 의도에 예제 발언을 추가하거나 의도에 예제 발언을 추가하는 경우(이전 또는 이후) 엔터티를 만들 수 있습니다.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="plan-entities-then-create-and-label"></a>엔터티 를 계획한 다음 작성 및 레이블 지정

예제 발언에서 컴퓨터 학습된 엔터티를 만들거나 **엔터티** 페이지에서 만들 수 있습니다. 

일반적으로 가장 좋은 방법은 포털에서 컴퓨터 학습 엔터티를 만들기 전에 엔터티를 계획하는 데 시간을 소비하는 것입니다. 그런 다음 하위 구성 요소 및 설명자 및 제약 조건에 대해 당시에 알고 있는 만큼 자세히 설명하여 예제 발언에서 기계 학습된 엔터티를 만듭니다. [분해 가능한 엔터티 자습서에서는](tutorial-machine-learned-entity.md) 이 메서드를 사용하는 방법을 보여 줍니다. 

엔터티 를 계획하는 과정에서 텍스트 일치 엔터티(예: 미리 빌드된 엔터티, 정규식 엔터티 또는 목록 엔터티)가 필요하다는 것을 알 수 있습니다. 엔터티 **페이지에서** 이러한 레이블이 예제 발언에 표시되기 전에 만들 수 있습니다. 

레이블을 지정할 때 개별 엔터티에 레이블을 지정한 다음 상위 컴퓨터 학습 엔터티로 빌드할 수 있습니다. 또는 컴퓨터 학습된 상위 엔터티로 시작하여 자식 엔터티로 분해할 수 있습니다. 

> [!TIP] 
>클라이언트 응용 프로그램에서 추출할 때 단어가 사용되지 않더라도 엔터티를 나타낼 수 있는 모든 단어에 레이블을 지정합니다. 

## <a name="creating-an-entity-before-or-with-labeling"></a>레이블 지정 전또는 레이블을 지정하여 엔터티 만들기

다음 표를 사용하여 앱에 각 엔터티를 만들거나 추가할 엔터티를 이해해야 합니다. 

|엔터티 유형|LUIS 포털에서 엔터티를 만드는 위치|
|--|--|
|기계 학습된 엔터티|엔터티 또는 의도 세부 정보|
|목록 엔터티|엔터티 또는 의도 세부 정보|
|정규식 엔터티|엔터티|
|Pattern.any 엔터티|엔터티|
|미리 빌드된 엔터티|엔터티|
|미리 빌드된 도메인 엔터티|엔터티|

**엔터티** 페이지에서 모든 엔터티를 만들거나 **의도 세부 정보** 페이지의 예제 발언에 엔터티에 레이블을 지정하는 일부로 엔터티 몇 개를 만들 수 있습니다. **의도 세부 정보** 페이지에서 예제 발언에있는 엔터티에만 _레이블을_ 지정할 수 있습니다. 

## <a name="create-a-machine-learned-entity"></a>기계 학습 엔터티 만들기

[!INCLUDE [Create and label entities in machine-learned tutorial](includes/decomposable-tutorial-links.md)]

## <a name="create-a-text-matching-entity"></a>텍스트 일치 엔터티 만들기

텍스트 일치 엔터티를 사용하면 데이터를 추출하는 몇 가지 방법을 제공합니다.

|텍스트 일치 엔터티|목적|
|--|--|
|[목록 엔터티](#add-list-entities-for-exact-matches)|대체 양식으로 동의어와 함께 표준 이름 목록|
|정규식 엔터티|정규식 엔터티를 사용하여 텍스트 일치|
|[미리 빌드된 엔터티](tutorial-machine-learned-entity.md#add-prebuilt-number-to-help-extract-data)|숫자, 이메일, 날짜 와 같은 일반적인 데이터 형식과 일치|
|미리 빌드된 도메인 엔터티|선택한 제목 도메인을 사용하여 일치|
|[Pattern.any](#add-a-patternany-entity)| 주변 텍스트와 쉽게 혼동될 수 있는 엔터티를 일치시려면|  

미리 빌드된 엔터티는 사용자 지정 학습 데이터를 제공하지 않고 작동합니다. 다른 엔터티는 고객 교육 데이터(예: 목록 엔터티의 항목) 또는 식(예: 정규식 또는 pattern.any)을 제공해야 합니다.

<a name="add-list-entities"></a>

### <a name="how-to-create-a-new-custom-entity"></a>새 사용자 지정 엔터티를 만드는 방법

1. LUIS 포털에서 **관리** 섹션으로 이동한 다음 **엔터티** 페이지를 이동합니다. 
1. **+ 만들기를**선택한 다음 엔터티 유형을 선택합니다. 
1. 엔터티 구성을 계속 한 다음 완료되면 **만들기를** 선택합니다. 

### <a name="add-list-entities-for-exact-matches"></a>정확한 일치에 대한 목록 엔터티 추가

목록 엔터티는 관련 단어의 고정된 폐쇄형 집합을 나타냅니다. 작성자가 목록을 변경할 수 있지만 LUIS는 목록을 늘리거나 축소하지 않습니다. [목록 엔터티 .json 형식(참조 엔터티-list.md.md#예제 json-import-to-list-entity 엔터티)을 사용하여 기존 목록 엔터티로 가져올 수도 있습니다. 

다음 목록은 표준 이름과 동의어를 보여 줍니다. 

|색상 - 목록 항목 이름|색상 - 동의어|
|--|--|
|빨강|진홍색, 혈액, 사과, 소방차|
|파랑|하늘, 푸른, 코발트|
|녹색|켈리, 라임|

프로시저를 사용하여 목록 엔터티를 만듭니다. 목록 엔터티가 만들어지면 의도에서 예제 발언에 레이블을 지정할 필요가 없습니다. 목록 항목과 동의어는 정확한 텍스트를 사용하여 일치합니다. 

1. **빌드** 섹션에서 왼쪽 패널에서 **엔터티를** 선택한 다음 **+ 만들기를**선택합니다.

1. 엔터티 유형 대화 상자 **만들기에서** 엔터티 이름을 `Colors` 입력하고 **목록**을 선택합니다.
1. 목록 **엔터티** 만들기 대화 상자에서 **새 하위 목록 추가에서**목록 항목 이름을 입력합니다. `Green`

    > [!div class="mx-imgBorder"]
    > ![엔터티 세부 정보 페이지에서 목록 엔터티로 색상 목록을 만듭니다.](media/how-to-add-entities/create-list-entity-of-colors.png) 

1. 목록 항목 및 동의어 추가가 완료되면 **에서 만들기를**선택합니다.

    앱에 대한 변경 그룹이 완료되면 앱을 **학습해야** 합니다. 한 번의 변경 후에 앱을 학습하지 마십시오. 

    > [!NOTE]
    > 이 프로시저는 **의도 세부 정보** 페이지의 예제 발언에서 목록 엔터티를 만들고 레이블을 지정하는 방법을 보여 줍니다. **엔터티** 페이지에서 동일한 엔터티를 만들 수도 있습니다.

## <a name="add-a-role-for-an-entity"></a>엔터티에 대한 역할 추가

역할은 컨텍스트에 따라 엔터티의 명명된 하위 유형입니다. 

### <a name="add-a-role-to-distinguish-different-contexts"></a>다른 컨텍스트를 구분하는 역할 추가

다음 발언에는 두 위치가 있으며 각 위치는 다음과 같이 `to` `from`주변의 단어에 의해 시맨티컬으로 지정됩니다. 

`Pick up the package from Seattle and deliver to New York City.`

이 절차에서는 `origin` 미리 `destination` 빌드된 지리V2 엔터티에 및 역할을 추가합니다.

1. **빌드** 섹션에서 왼쪽 패널의 **엔터티**를 선택합니다.

1. **선택 + 미리 빌드된 엔터티 추가**. **지리V2를** 선택한 다음 완료 를 **선택합니다.** 이렇게 하면 미리 빌드된 엔터티가 앱에 추가됩니다.
    
    Pattern.any가 포함된 패턴이 엔터티를 잘못 추출한 것을 발견하면 [명시적 목록](reference-pattern-syntax.md#explicit-lists)을 사용하여 이 문제를 정정합니다. 

1. **엔터티** 페이지 엔터티 목록에서 새로 추가된 미리 빌드된 지리V2 엔터티를 선택합니다. 
1. 새 역할을 추가하려면 **+** **추가된 역할 없음**옆을 선택합니다.
1. 텍스트 상자 **유형에서** 역할 `Origin` 이름을 입력한 다음 입력합니다. 다음 enter의 `Destination` 두 번째 역할 이름을 추가합니다. 

    > [!div class="mx-imgBorder"]
    > ![Location 엔터티에 Origin 역할을 추가하는 스크린샷](media/how-to-add-entities//add-role-to-prebuilt-geographyv2-entity.png)

    역할은 미리 빌드된 엔터티에 추가되지만 해당 엔터티를 사용하는 모든 발언에는 추가되지 않습니다. 

### <a name="label-text-with-a-role-in-an-example-utterance"></a>예제 발언에서 역할이 있는 텍스트 레이블 지정

1. 역할을 사용하는 예제 발언이 있는 의도 세부 정보 페이지로 이동합니다. 
1. 역할에 레이블을 지정하려면 예제 발화에서 엔터티 레이블(텍스트 아래의 실선)을 선택한 다음 드롭다운 목록에서 **엔터티 팔레트에서 보기를** 선택합니다. 

    > [!div class="mx-imgBorder"]
    > ![엔터티 팔레트에서 보기를 선택하는 스크린샷](media/how-to-add-entities/select-text-label-with-entity-palette-for-role.png)   

    엔터티 팔레트가 오른쪽으로 열립니다. 

1. 엔터티를 선택한 다음 팔레트 의 맨 아래로 이동하여 역할을 선택합니다. 

    > [!div class="mx-imgBorder"]
    > ![엔터티 팔레트에서 보기를 선택하는 스크린샷](media/how-to-add-entities/select-role-from-entity-palette-entity-inspector.png)

<a name="add-pattern-any-entities"></a>

## <a name="add-a-patternany-entity"></a>패턴 추가.모든 엔터티

[pattern.any](luis-concept-entity-types.md) 엔터티는 의도의 예제 발언이 아닌 [패턴에서만](luis-how-to-model-intent-pattern.md)유효합니다. 이 유형의 엔터티는 LUIS가 다양한 길이 및 단어 선택이 포함된 엔터티의 끝을 찾는 데 도움을 줍니다. 이 엔터티는 패턴에 사용되므로 LUIS는 발화 템플릿에서 엔터티의 끝 위치를 알 수 있습니다.

### <a name="steps-to-create-a-patternany-entity"></a>pattern.any 엔터티를 만드는 단계

1. **빌드** 섹션에서 왼쪽 패널에서 **엔터티를** 선택한 다음 **+ 만들기를**선택합니다.

1. 엔터티 유형 대화 상자 **선택에서** **이름** 상자에 엔터티 이름을 입력하고 **Pattern.Any를** **유형으로** 선택한 다음 **을**선택합니다.

    이 [엔터티를](luis-how-to-model-intent-pattern.md) 사용하여 패턴 발언을 만들면 엔터티는 결합된 컴퓨터 학습 및 텍스트 일치 알고리즘으로 추출됩니다. 

### <a name="create-a-pattern-template-utterance-to-use-patternany-entity"></a>pattern.any 엔터티를 사용하도록 패턴 템플릿 발화 만들기

pattern.any 엔터티를 사용하려면 올바른 중괄호 구문(예: `Where is **{HumanResourcesFormTitle}** on the server?`)을 사용하여 **앱 성능 개선** 섹션의 **패턴** 페이지에서 패턴을 추가합니다.

Pattern.any가 포함된 패턴이 엔터티를 잘못 추출한 것을 발견하면 [명시적 목록](reference-pattern-syntax.md#explicit-lists)을 사용하여 이 문제를 정정합니다. 

## <a name="do-not-change-entity-type"></a>엔터티 형식을 변경하지 않음

LUIS에서는 해당 엔터티를 생성하기 위해 추가 또는 제거할 항목을 잘 모르기 때문에 엔터티의 형식을 변경하도록 허용하지 않습니다. 형식을 변경하기 위해서는 약간 다른 이름을 사용하여 올바른 형식의 새 엔터티를 만드는 것이 좋습니다. 엔터티가 만들어지면 각 발언에서 이전 레이블이 지정된 엔터티 이름을 제거한 후 새 엔터티 이름을 추가합니다. 모든 발언에 레이블이 다시 지정되면 이전 엔터티를 삭제합니다. 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"] 
> [미리 작성된 모델 사용](howto-add-prebuilt-models.md) 

다음에 대해 자세히 알아봅니다.
* [훈련](luis-how-to-train.md) 방법
* [테스트](luis-interactive-test.md) 방법
* [게시](luis-how-to-publish-app.md) 방법
* 패턴:
    * [개념](luis-concept-patterns.md)
    * [구문](reference-pattern-syntax.md)
* [미리 빌드된 엔터티 GitHub 리포지토리](https://github.com/Microsoft/Recognizers-Text)
* [데이터 추출 개념](luis-concept-data-extraction.md)


 
