---
title: 패턴 및 정확도 - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS(Language Understanding) 애플리케이션에서 예측 정확도를 향상시키기 위해 패턴 템플릿을 추가합니다.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: c5e1cc5cdda921e63315c2904d560b7ad3939776
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "95018857"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>패턴을 추가하여 예측 정확도를 개선하는 방법
LUIS 앱이 엔드포인트 발화를 수신한 후 [패턴](luis-concept-patterns.md)을 사용하여 단어 순서 및 단어 선택에 패턴을 노출하는 발화에 대한 예측 정확도를 개선합니다. 패턴은 특정 [구문](luis-concept-patterns.md#pattern-syntax)을 사용하여 [엔터티](luis-concept-entity-types.md), 엔터티 [역할](./luis-concept-entity-types.md) 및 선택적 텍스트의 위치를 지정합니다.

> [!CAUTION]
> 패턴에는 하위 엔터티가 아닌 기계 학습 엔터티 부모만 포함됩니다.

## <a name="add-template-utterance-using-correct-syntax"></a>올바른 구문을 사용하여 템플릿 발화 추가

1. [LUIS 포털](https://www.luis.ai)에 로그인하고 **구독** 및 **제작 리소스** 를 선택하여 해당 제작 리소스에 할당된 앱을 확인합니다.
1. **내 앱** 페이지에서 해당 이름을 선택하여 앱을 엽니다.
1. 왼쪽 패널에서 **애플리케이션 성능 향상** 의 **패턴** 을 선택합니다.

1. 패턴에 대한 올바른 의도를 선택합니다.

1. 템플릿 텍스트 상자에 템플릿 발화를 입력하고 Enter 키를 선택합니다. 엔터티 이름을 입력하려면 올바른 패턴 엔터티 구문을 사용합니다. `{`를 사용하여 엔터티 구문을 시작합니다. 엔터티 목록이 표시됩니다. 올바른 엔터티를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![패턴에 대한 엔터티 스크린샷](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    엔터티에 [역할](./luis-concept-entity-types.md)이 포함된 경우 `{Location:Origin}`와 같은 엔터티 이름 뒤에 단일 콜론(`:`)으로 역할을 표시합니다. 엔터티 역할 목록이 목록에 표시됩니다. 역할을 선택한 후 Enter 키를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![역할이 있는 엔터티 스크린샷](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    올바른 엔터티를 선택한 후 패턴 입력을 완료하고 Enter 키를 선택합니다. 패턴 입력을 완료하면 앱을 [학습](luis-how-to-train.md)시킵니다.

    > [!div class="mx-imgBorder"]
    > ![두 유형의 엔터티가 모두 포함된 입력된 패턴 스크린샷](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="create-a-patternany-entity"></a>pattern.any 엔터티 만들기

[Pattern.any](luis-concept-entity-types.md) 엔터티는 의도의 예제 발화가 아닌 [패턴](luis-how-to-model-intent-pattern.md)에서만 유효합니다. 이 유형의 엔터티는 LUIS가 다양한 길이 및 단어 선택이 포함된 엔터티의 끝을 찾는 데 도움을 줍니다. 이 엔터티는 패턴에 사용되므로 LUIS는 발화 템플릿에서 엔터티의 끝 위치를 알 수 있습니다.

1. [LUIS 포털](https://www.luis.ai)에 로그인하고 **구독** 및 **제작 리소스** 를 선택하여 해당 제작 리소스에 할당된 앱을 확인합니다.
1. **내 앱** 페이지에서 해당 이름을 선택하여 앱을 엽니다.
1. **빌드** 섹션에서 왼쪽 패널의 **엔터티** 를 선택한 다음, **+ 만들기** 를 선택합니다.

1. **엔터티 형식** 대화 상자에서 **이름** 상자에 엔터티 이름을 입력하고 **Pattern.Any** 를 **형식** 으로 선택한 다음 **만들기** 를 선택합니다.

    이 엔터티를 사용하여 [패턴 발화를 만들면](luis-how-to-model-intent-pattern.md) 결합된 기계 학습 및 텍스트 일치 알고리즘을 사용하여 엔터티가 추출됩니다.

## <a name="adding-example-utterances-as-pattern"></a>예제 발화를 패턴으로 추가

엔터티에 대한 패턴을 추가하는 _가장 쉬운_ 방법은 의도 세부 정보 페이지에서 패턴을 만드는 것입니다. 이렇게 하면 구문이 예제 발화와 일치하게 됩니다.

1. [LUIS 포털](https://www.luis.ai)에 로그인하고 **구독** 및 **제작 리소스** 를 선택하여 해당 제작 리소스에 할당된 앱을 확인합니다.
1. **내 앱** 페이지에서 해당 이름을 선택하여 앱을 엽니다.
1. **의도** 목록 페이지에서 템플릿 발화를 만들기 위한 예제 발화의 의도 이름을 선택합니다.
1. 의도 세부 정보 페이지에서 템플릿 발화로 사용할 예제 발화의 행을 선택하고, 상황에 맞는 도구 모음에서 **+ 패턴으로 추가** 를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![의도 세부 정보 페이지에서 예제 발화를 템플릿 패턴으로 선택하는 스크린샷](./media/luis-how-to-model-intent-pattern/add-example-utterances-as-pattern-template-utterance-from-intent-detail-page.png)

    발화는 발화에서 패턴을 만들기 위해 엔터티를 포함해야 합니다.

1. 팝업 상자의 **패턴 확인** 페이지에서 **완료** 를 선택합니다. 엔터티의 하위 엔터티 또는 기능을 정의할 필요가 없습니다. 기계 학습 엔터티를 나열하기만 하면 됩니다.

    > [!div class="mx-imgBorder"]
    > ![의도 세부 정보 페이지에서 예제 발화를 템플릿 패턴으로 확인하는 스크린샷](./media/luis-how-to-model-intent-pattern/confirm-patterns-from-example-utterance-intent-detail-page.png)

1. 텍스트를 선택적으로 선택하는 등 대괄호 `[]`를 사용하여 템플릿을 편집해야 하는 경우 **패턴** 페이지에서 이 편집 작업을 수행해야 합니다.

1. 탐색 모음에서 **학습** 을 선택하여 새 패턴으로 앱을 학습합니다.

## <a name="train-your-app-after-changing-model-with-patterns"></a>패턴이 포함된 모델을 변경한 후 앱 학습
패턴을 추가, 편집, 제거 또는 다시 할당한 후 변경 내용을 엔드포인트 쿼리에 적용하려면 앱을 [학습](luis-how-to-train.md)시키고 [게시](luis-how-to-publish-app.md)합니다.

<a name="search-patterns"></a>
<a name="edit-a-pattern"></a>
<a name="reassign-individual-pattern-to-different-intent"></a>
<a name="reassign-several-patterns-to-different-intent"></a>
<a name="delete-a-single-pattern"></a>
<a name="delete-several-patterns"></a>
<a name="filter-pattern-list-by-entity"></a>
<a name="filter-pattern-list-by-intent"></a>
<a name="remove-entity-or-intent-filter"></a>
<a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>

## <a name="use-contextual-toolbar"></a>상황별 도구 모음 사용

패턴 목록 위의 상황별 도구 모음을 사용하면 다음을 수행할 수 있습니다.

* 패턴 검색
* 패턴 편집
* 개별 패턴을 다른 의도에 다시 할당
* 여러 패턴을 다른 의도에 다시 할당
* 단일 패턴 삭제
* 여러 패턴 삭제
* 엔터티별 패턴 목록 필터링
* 의도별 패턴 목록 필터링
* 엔터티 또는 의도 필터 제거
* 의도 또는 엔터티 페이지의 기존 발화에서 패턴 추가

## <a name="next-steps"></a>다음 단계

* pattern.any 및 역할을 사용하여 [패턴을 빌드](luis-tutorial-pattern.md)하는 방법을 자습서에서 알아봅니다.
* 앱을 [학습](luis-how-to-train.md)시키는 방법에 대해 알아봅니다.
