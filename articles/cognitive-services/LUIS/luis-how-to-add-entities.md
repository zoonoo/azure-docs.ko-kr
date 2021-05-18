---
title: 엔터티 추가 - LUIS
description: LUIS(Language Understanding) 앱에서 사용자 발화의 키 데이터를 추출하는 엔터티를 만듭니다. 추출된 엔터티 데이터는 클라이언트 애플리케이션에서 고객 요청을 이행하는 데 사용됩니다.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: c5c6836c2d68036bf2b9c5abe191943537349b8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "91540968"
---
# <a name="add-entities-to-extract-data"></a>데이터를 추출하는 엔터티 추가

LUIS(Language Understanding) 앱에서 사용자 발화의 키 데이터를 추출하는 엔터티를 만듭니다. 추출된 엔터티 데이터는 클라이언트 애플리케이션에서 고객 요청을 이행하는 데 사용됩니다.

엔터티는 추출하려는 발화 내의 단어 또는 구를 나타냅니다. 엔터티는 의도와 관련된 정보를 설명하며 앱이 작업을 수행하는 데 꼭 필요한 경우도 있습니다. 의도에 예제 발화를 추가할 때 또는 의도에 예제 발화를 추가하는 것과 별도로(전이나 후에) 엔터티를 만들 수 있습니다.

## <a name="plan-entities-then-create-and-label"></a>엔터티 계획, 만들기 및 레이블 지정

기계 학습 엔터티는 예제 발화에서 만들거나 **엔터티** 페이지에서 만들 수 있습니다.

일반적으로 포털에서 기계 학습 엔터티를 만들기 전에 엔터티를 계획하는 데 시간을 할애하는 것이 좋습니다. 그런 다음 사용자가 알고 있는 하위 엔터티 및 기능에 대한 세부 정보를 사용하여 예제 발화에서 기계 학습 엔터티를 만듭니다. [분할 가능한 엔터티 자습서](tutorial-machine-learned-entity.md)에서 이 메서드를 사용하는 방법을 보여 줍니다.

엔터티를 계획하는 과정에서 텍스트 일치 엔터티(예: 미리 작성된 엔터티, 정규식 엔터티 또는 목록 엔터티)가 필요하다는 것을 알 수 있습니다. 이러한 엔터티는 예제 발화에서 레이블을 지정하기 전에 **엔터티** 페이지에서 만들 수 있습니다.

레이블을 지정할 때 개별 엔터티의 레이블을 지정하고 상위 기계 학습 엔터티를 만들 수 있습니다. 또는 상위 기계 학습 엔터티로 시작해서 하위 엔터티로 분해할 수 있습니다.

> [!TIP]
>클라이언트 애플리케이션에서 추출할 때 단어가 사용되지 않더라도 엔터티를 나타낼 수 있는 모든 단어에 레이블을 지정합니다.

## <a name="when-to-create-an-entity"></a>엔터티를 만들어야 하는 경우

엔터티를 계획한 후에는 기계 학습 엔터티와 하위 엔터티를 만들어야 합니다. 이렇게 하려면 미리 작성된 엔터티 또는 텍스트 일치 엔터티를 추가하여 기계 학습 엔터티에 대한 기능을 제공해야 할 수 있습니다. 이러한 작업은 레이블을 지정하기 전에 모두 수행해야 합니다.

예제 발화에 대한 레이블 지정을 시작한 후에는 기계 학습 엔터티를 만들거나 목록 엔터티를 확장할 수 있습니다.

다음 표를 사용하여 앱에 각 엔터티 형식을 만들거나 추가할 위치를 파악합니다.

|엔터티 유형|LUIS 포털에서 엔터티를 만들 위치|
|--|--|
|기계 학습 엔터티|엔터티 또는 의도 세부 정보|
|목록 엔터티|엔터티 또는 의도 세부 정보|
|정규식 엔터티|엔터티|
|Pattern.any 엔터티|엔터티|
|미리 빌드된 엔터티|엔터티|
|미리 빌드된 도메인 엔터티|엔터티|

**엔터티 페이지에서** 모든 엔터티를 만들거나, **의도 세부 정보** 페이지의 예제 발화에서 엔터티 레이블 지정의 일부로 두 엔터티를 만들 수 있습니다. **의도 세부 정보** 페이지의 예제 발화에서는 엔터티의 _레이블_ 만 지정할 수 있습니다.



## <a name="how-to-create-a-new-custom-entity"></a>새 사용자 지정 엔터티를 만드는 방법

이 프로세스는 기계 학습 엔터티, 목록 엔터티 및 정규식 엔터티에 작동합니다.

1. [LUIS 포털](https://www.luis.ai)에 로그인하고 **구독** 및 **제작 리소스** 를 선택하여 해당 제작 리소스에 할당된 앱을 확인합니다.
1. **내 앱** 페이지에서 해당 이름을 선택하여 앱을 엽니다.
1. **엔터티** 페이지를 선택합니다.
1. **+ 만들기** 를 선택한 다음 엔터티 형식을 선택합니다.
1. 엔터티를 계속 구성하고 완료되면 **만들기** 를 선택합니다.

## <a name="create-a-machine-learned-entity"></a>기계 학습 엔터티 만들기

1. [LUIS 포털](https://www.luis.ai)에 로그인하고 **구독** 및 **제작 리소스** 를 선택하여 해당 제작 리소스에 할당된 앱을 확인합니다.
1. **내 앱** 페이지에서 해당 이름을 선택하여 앱을 엽니다.
1. **빌드** 섹션에서 왼쪽 패널의 **엔터티** 를 선택한 다음, **+ 만들기** 를 선택합니다.
1. **엔터티 형식 만들기** 대화 상자에서 엔터티의 이름을 입력하고 **기계 학습** 을 선택합니다. 하위 엔터티를 추가하려면 **구조 추가** 를 선택합니다. **만들기** 를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![기계 학습 엔터티를 만드는 스크린샷](media/add-entities/machine-learned-entity-with-structure.png)

1. **하위 엔터티 추가** 에서 상위 엔터티 행의 **+** 를 선택하여 하위 엔터티를 추가합니다.

    > [!div class="mx-imgBorder"]
    > ![하위 엔터티를 추가하는 스크린샷](media/add-entities/machine-learned-entity-with-subentities.png)

1. **만들기** 를 선택하여 생성 프로세스를 완료합니다.

## <a name="add-a-feature-to-a-machine-learned-entity"></a>기계 학습 엔터티에 기능 추가

1. [LUIS 포털](https://www.luis.ai)에 로그인하고 **구독** 및 **제작 리소스** 를 선택하여 해당 제작 리소스에 할당된 앱을 확인합니다.
1. **내 앱** 페이지에서 해당 이름을 선택하여 앱을 엽니다.
1. **빌드** 섹션에서 왼쪽 패널의 **엔터티** 를 선택한 다음, 기계 학습 엔터티를 선택합니다.
1. 엔터티 또는 하위 엔터티 행에서 **+ 기능 추가** 를 선택하여 기능을 추가합니다.
1. 기존 엔터티 및 구문 목록에서 선택합니다.
1. 기능이 있는 경우에만 엔터티를 추출해야 하는 경우 해당 기능에 대해 별표 `*`를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![엔터티에 기능을 추가하는 스크린샷](media/add-entities/machine-learned-entity-schema-with-features.png)

## <a name="create-a-regular-expression-entity"></a>정규식 엔터티 만들기

1. [LUIS 포털](https://www.luis.ai)에 로그인하고 **구독** 및 **제작 리소스** 를 선택하여 해당 제작 리소스에 할당된 앱을 확인합니다.
1. **내 앱** 페이지에서 해당 이름을 선택하여 앱을 엽니다.
1. **빌드** 섹션에서 왼쪽 패널의 **엔터티** 를 선택한 다음, **+ 만들기** 를 선택합니다.

1. **엔터티 형식 만들기** 대화 상자에서 엔터티의 이름을 입력하고 **정규식** 을 선택하고 **정규식** 필드에 정규식을 입력한 다음 **만들기** 를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![정규식 엔터티를 만드는 스크린샷](media/add-entities/add-regular-expression-entity.png)


<a name="add-list-entities"></a>

## <a name="create-a-list-entity"></a>목록 엔터티 만들기

목록 엔터티는 관련 단어의 고정된 폐쇄형 집합을 나타냅니다. 작성자는 목록을 변경할 수 있지만 반면 LUIS는 목록을 확장하거나 축소할 수 없습니다. [목록 엔터티 .json 형식](reference-entity-list.md#example-json-to-import-into-list-entity)을 사용하여 기존 목록 엔터티로 가져올 수도 있습니다.

다음 목록에서는 정식 이름과 동의어를 보여 줍니다.

|색 - 목록 항목 이름|색 - 동의어|
|--|--|
|빨간색|심홍색, 핏빛, 사과색, 소방차색|
|파랑|하늘색, 코발트색|
|녹색|황록색, 라임색|

절차에 따라 목록 엔터티를 만듭니다. 목록 엔터티를 만든 후에는 의도적으로 예제 발화에 레이블을 추가할 필요가 없습니다. 정확한 텍스트를 사용하여 목록 항목과 동의어가 일치합니다.
1. [LUIS 포털](https://www.luis.ai)에 로그인하고 **구독** 및 **제작 리소스** 를 선택하여 해당 제작 리소스에 할당된 앱을 확인합니다.
1. **내 앱** 페이지에서 해당 이름을 선택하여 앱을 엽니다.
1. **빌드** 섹션에서 왼쪽 패널의 **엔터티** 를 선택한 다음, **+ 만들기** 를 선택합니다.

1. **엔터티 형식 만들기** 대화 상자에서 엔터티의 이름(예: `Colors`)을 입력하고 **목록** 을 선택합니다.
1. **목록 엔터티 만들기** 대화 상자의 **새 하위 목록 추가....** 에서 목록 항목 이름(예: `Green`)을 입력한 다음 동의어를 추가합니다.

    > [!div class="mx-imgBorder"]
    > ![엔터티 세부 정보 페이지에서 목록 엔터티로 색 목록 만들기](media/how-to-add-entities/create-list-entity-of-colors.png)

1. 목록 항목과 동의어를 모두 추가했으면 **만들기** 를 선택합니다.

    앱에 대한 그룹 변경을 완료한 후 앱을 **학습** 시켜야 합니다. 단일 변경 후에는 앱을 학습시키지 않습니다.

    > [!NOTE]
    > 이 절차에서는 **의도 세부 정보** 페이지의 예제 발화에서 목록 엔터티를 만들고 레이블을 지정하는 방법을 보여 줍니다. **엔터티** 페이지에서 동일한 엔터티를 만들 수도 있습니다.

## <a name="add-a-role-for-an-entity"></a>엔터티에 대한 역할 추가

역할은 컨텍스트를 기준으로 하는 엔터티의 명명된 하위 형식입니다.

### <a name="add-a-role-to-distinguish-different-contexts"></a>다른 컨텍스트를 구분하는 역할 추가

다음 발화에는 두 위치가 있으며 각 위치는 `to` 및 `from`과 같은 단어를 통해 의미 체계를 지정합니다.

`Pick up the package from Seattle and deliver to New York City.`

이 절차에서는 미리 빌드된 geographyV2 엔터티에 `origin` 및 `destination` 역할을 추가합니다.
1. [LUIS 포털](https://www.luis.ai)에 로그인하고 **구독** 및 **제작 리소스** 를 선택하여 해당 제작 리소스에 할당된 앱을 확인합니다.
1. **내 앱** 페이지에서 해당 이름을 선택하여 앱을 엽니다.
1. **빌드** 섹션에서 왼쪽 패널의 **엔터티** 를 선택합니다.

1. **+ 미리 빌드된 엔터티 추가** 를 선택합니다. **geographyV2** 를 선택한 다음 **완료** 를 선택합니다. 이렇게 하면 미리 빌드된 엔터티가 앱에 추가됩니다.

    Pattern.any가 포함된 패턴이 엔터티를 잘못 추출한 것을 발견하면 [명시적 목록](reference-pattern-syntax.md#explicit-lists)을 사용하여 이 문제를 정정합니다.

1. **엔터티** 페이지 엔터티 목록에서 미리 작성되어 새로 추가된 geographyV2 엔터티를 선택합니다.
1. 새 역할을 추가하려면 **추가된 역할 없음** 옆의 **+** 를 선택합니다.
1. **역할 입력...** 텍스트 상자에 `Origin` 역할의 이름을 입력한 다음 Enter 키를 누릅니다. 두 번째 역할 이름 `Destination`을 추가한 다음 Enter 키를 누릅니다.

    > [!div class="mx-imgBorder"]
    > ![Location 엔터티에 Origin 역할을 추가하는 스크린샷](media/how-to-add-entities//add-role-to-prebuilt-geographyv2-entity.png)

    이 역할은 미리 작성된 엔터티에 추가되지만 해당 엔터티를 사용하는 발화에는 추가되지 않습니다.

### <a name="label-text-with-a-role-in-an-example-utterance"></a>예제 발화에서 역할을 사용하여 텍스트 레이블 지정

> [!TIP]
> 역할은 기계 학습 엔터티의 하위 엔터티로 레이블을 지정하여 바꿀 수 있습니다.

1. [LUIS 포털](https://www.luis.ai)에 로그인하고 **구독** 및 **제작 리소스** 를 선택하여 해당 제작 리소스에 할당된 앱을 확인합니다.
1. **내 앱** 페이지에서 해당 이름을 선택하여 앱을 엽니다.
1. 역할을 사용하는 예제 발화를 포함하는 의도 세부 정보 페이지로 이동합니다.
1. 역할을 사용하여 레이블을 지정하려면 예제 발화에서 엔터티 레이블(텍스트 아래 실선)을 선택하고 드롭다운 목록에서 **엔터티 창에서 보기** 를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![엔터티 창에서 선택된 보기 메뉴 항목을 보여 주는 스크린샷](media/add-entities/view-in-entity-pane.png)

    엔터티 팔레트가 오른쪽에 열립니다.

1. 엔터티를 선택하고 팔레트 아래쪽으로 이동하여 역할을 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![역할을 선택할 수 있는 위치를 보여 주는 스크린샷](media/add-entities/select-role-in-entity-palette.png)

<a name="add-pattern-any-entities"></a>
<a name="add-a-patternany-entity"></a>
<a name="create-a-pattern-from-an-utterance"></a>

## <a name="create-a-patternany-entity"></a>pattern.any 엔터티 만들기

**Pattern.any** 엔터티는 [패턴](luis-how-to-model-intent-pattern.md)에서만 사용할 수 있습니다.


## <a name="do-not-change-entity-type"></a>엔터티 형식 변경 금지

LUIS에서는 해당 엔터티를 생성하기 위해 추가 또는 제거할 항목을 잘 모르기 때문에 엔터티의 형식을 변경하도록 허용하지 않습니다. 형식을 변경하기 위해서는 약간 다른 이름을 사용하여 올바른 형식의 새 엔터티를 만드는 것이 좋습니다. 엔터티가 만들어지면 각 발언에서 이전 레이블이 지정된 엔터티 이름을 제거한 후 새 엔터티 이름을 추가합니다. 모든 발언에 레이블이 다시 지정되면 이전 엔터티를 삭제합니다.


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [미리 작성된 모델 사용](howto-add-prebuilt-models.md)

다음에 대해 자세히 알아보세요.
* [학습](luis-how-to-train.md) 방법
* [테스트](luis-interactive-test.md) 방법
* [게시](luis-how-to-publish-app.md) 방법
* 패턴:
    * [개념](luis-concept-patterns.md)
    * [구문](reference-pattern-syntax.md)
* [미리 빌드된 엔터티 GitHub 리포지토리](https://github.com/Microsoft/Recognizers-Text)
* [데이터 추출 개념](luis-concept-data-extraction.md)



