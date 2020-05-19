---
title: 패턴 정확도 추가-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS (Language Understanding) 응용 프로그램에서 예측 정확도를 향상 시키기 위해 패턴 템플릿을 추가 합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: diberry
ms.openlocfilehash: 077e29856842972fae2c723d4a2c368cbb80df06
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593259"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>예측 정확도를 향상 시키기 위해 패턴을 추가 하는 방법
LUIS 앱이 끝점 길이 발언를 받은 후 [패턴](luis-concept-patterns.md) 을 사용 하 여 단어 순서 및 단어 선택의 패턴을 표시 하는 길이 발언에 대 한 예측 정확도를 향상 시킵니다. 패턴은 특정 [구문을](luis-concept-patterns.md#pattern-syntax) 사용 하 여 [엔터티](luis-concept-entity-types.md), 엔터티 [역할](luis-concept-roles.md)및 선택적 텍스트의 위치를 지정 합니다.

> [!CAUTION]
> 패턴에는 자식 엔터티가 아닌 컴퓨터에서 학습 한 엔터티 부모만 포함 됩니다.

## <a name="adding-example-utterances-as-pattern"></a>패턴으로 예제 길이 발언 추가

엔터티에 대 한 패턴을 추가 하려면 _가장 쉬운_ 방법은 의도 세부 정보 페이지에서 패턴을 만드는 것입니다. 이렇게 하면 구문이 예제 utterance와 일치 하 게 됩니다.

1. [LUIS 포털](https://www.luis.ai)에 로그인 하 고 **구독** 및 **제작 리소스** 를 선택 하 여 해당 제작 리소스에 할당 된 앱을 확인 합니다.
1. **내 앱** 페이지에서 해당 이름을 선택 하 여 앱을 엽니다.
1. **의도** 목록 페이지에서 utterance 템플릿을 만들 utterance의 의도 이름을 선택 합니다.
1. 의도 세부 정보 페이지에서 템플릿 utterance 사용할 예제 utterance의 행을 선택 하 고, 상황에 맞는 도구 모음에서 **+ 추가 패턴** 을 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![의도 세부 정보 페이지에서 예제 utterance를 템플릿 패턴으로 선택 하는 스크린샷](./media/luis-how-to-model-intent-pattern/add-example-utterances-as-pattern-template-utterance-from-intent-detail-page.png)

1. 팝업 상자의 **패턴 확인** 페이지에서 **완료** 를 선택 합니다. 엔터티 하위 엔터티 또는 기능을 정의할 필요가 없습니다. 컴퓨터에서 학습 한 엔터티를 나열 하기만 하면 됩니다.

    > [!div class="mx-imgBorder"]
    > ![의도 세부 정보 페이지에서 예제 utterance를 템플릿 패턴으로 확인 하는 스크린샷](./media/luis-how-to-model-intent-pattern/confirm-patterns-from-example-utterance-intent-detail-page.png)

1. (사각형) 대괄호를 사용 하 여 텍스트를 선택적으로 선택 하는 것과 같이 템플릿을 편집 해야 하는 경우 `[]` **패턴** 페이지에서이 편집 작업을 수행 해야 합니다.

1. 탐색 모음에서 **학습** 을 선택 하 여 새 패턴으로 앱을 학습 합니다.

## <a name="add-template-utterance-using-correct-syntax"></a>올바른 구문을 사용 하 여 template utterance 추가
1. [LUIS 포털](https://www.luis.ai)에 로그인 하 고 **구독** 및 **제작 리소스** 를 선택 하 여 해당 제작 리소스에 할당 된 앱을 확인 합니다.
1. **내 앱** 페이지에서 해당 이름을 선택 하 여 앱을 엽니다.
1. 왼쪽 패널에서 **응용 프로그램 성능 향상**의 **패턴** 을 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![패턴 목록 스크린샷](./media/luis-how-to-model-intent-pattern/patterns-1.png)

1. 패턴에 대한 올바른 의도를 선택합니다.

1. 템플릿 텍스트 상자에 템플릿 발화를 입력하고 Enter 키를 선택합니다. 엔터티 이름을 입력하려면 올바른 패턴 엔터티 구문을 사용합니다. `{`를 사용하여 엔터티 구문을 시작합니다. 엔터티 목록이 표시됩니다. 올바른 엔터티를 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![패턴에 대한 엔터티 스크린샷](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    엔터티에 [역할](luis-concept-roles.md)을 포함 하는 경우 엔터티 이름 뒤에 단일 콜론을 사용 하 여 역할을 지정 `:` 합니다 (예:) `{Location:Origin}` . 엔터티 역할 목록이 목록에 표시됩니다. 역할을 선택한 후 Enter 키를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![역할이 있는 엔터티 스크린샷](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    올바른 엔터티를 선택한 후 패턴 입력을 완료하고 Enter 키를 선택합니다. 패턴 입력을 완료하면 앱을 [학습](luis-how-to-train.md)시킵니다.

    > [!div class="mx-imgBorder"]
    > ![두 유형의 엔터티가 모두 포함된 입력된 패턴 스크린샷](./media/luis-how-to-model-intent-pattern/patterns-5.png)

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

패턴 목록 위의 상황별 도구 모음을 사용 하면 다음을 수행할 수 있습니다.

* 패턴 검색
* 패턴 편집
* 개별 패턴을 다른 의도에 다시 할당
* 여러 패턴을 다른 의도에 다시 할당
* 단일 패턴 삭제
* 여러 패턴 삭제
* 엔터티별 패턴 목록 필터링
* 필터-패턴 별 목록
* 엔터티 또는 의도 필터 제거
* 의도 또는 엔터티 페이지의 기존 발화에서 패턴 추가

## <a name="next-steps"></a>다음 단계

* 패턴을 사용 하 여 [패턴을 빌드하](luis-tutorial-pattern.md) 는 방법에 대해 알아봅니다. 자습서를 사용 하는 모든 및 역할입니다.
* 앱을 [학습](luis-how-to-train.md)시키는 방법에 대해 알아봅니다.
