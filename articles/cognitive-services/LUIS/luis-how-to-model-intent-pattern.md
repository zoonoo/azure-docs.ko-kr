---
title: 패턴 및 정확도
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS (Language Understanding) 응용 프로그램의 예측 정확도 개선 하기 위해 패턴 템플릿을 추가 합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 202b9632b7a7faaf955874a0300edbe5134b7fa1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60196706"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>예측 정확도를 향상 하는 패턴을 추가 하는 방법
LUIS 앱 끝점 길이 발언을 받은 후 사용 된 [패턴](luis-concept-patterns.md) 단어 순서 및 단어 선택에서 패턴을 표시 하는 표현에 대 한 예측 정확도를 향상 합니다. 특정 사용 패턴 [구문](luis-concept-patterns.md#pattern-syntax) 의 위치를 나타내도록: [엔터티](luis-concept-entity-types.md), 엔터티 [역할](luis-concept-roles.md), 및 선택적 텍스트입니다.

## <a name="add-template-utterance-to-create-pattern"></a>템플릿 발화를 추가하여 패턴 만들기
1. **내 앱** 페이지에서 이름을 선택하여 앱을 열고 왼쪽 패널의 **앱 성능 개선** 아래에서 **패턴**을 선택합니다.

    ![패턴 목록 스크린샷](./media/luis-how-to-model-intent-pattern/patterns-1.png)

2. 패턴에 대한 올바른 의도를 선택합니다. 

    ![의도 선택](./media/luis-how-to-model-intent-pattern/patterns-2.png)

3. 템플릿 텍스트 상자에 템플릿 발화를 입력하고 Enter 키를 선택합니다. 엔터티 이름을 입력하려면 올바른 패턴 엔터티 구문을 사용합니다. `{`를 사용하여 엔터티 구문을 시작합니다. 엔터티 목록이 표시됩니다. 올바른 엔터티를 선택한 후 Enter 키를 선택합니다. 

    ![패턴에 대한 엔터티 스크린샷](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    엔터티를 포함 하는 경우는 [역할](luis-concept-roles.md), 단일 콜론을 사용 하 여 역할을 표시할 `:`와 같은 엔터티 이름, 후, `{Location:Origin}`합니다. 엔터티 역할 목록이 목록에 표시됩니다. 역할을 선택한 후 Enter 키를 선택합니다. 

    ![역할이 있는 엔터티 스크린샷](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    올바른 엔터티를 선택한 후 패턴 입력을 완료하고 Enter 키를 선택합니다. 패턴 입력을 완료하면 앱을 [학습](luis-how-to-train.md)시킵니다.

    ![두 유형의 엔터티가 모두 포함된 입력된 패턴 스크린샷](./media/luis-how-to-model-intent-pattern/patterns-5.png)

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

## <a name="use-contextual-toolbar"></a>상황에 맞는 도구 모음 사용

위의 패턴 목록 상황에 맞는 도구 모음을 사용 하면 수 있습니다.

* 패턴 검색
* 패턴 편집
* 개별 패턴을 다른 의도에 다시 할당
* 여러 패턴을 다른 의도에 다시 할당
* 삭제는 단일 패턴
* 여러 패턴 삭제
* 엔터티별 패턴 목록 필터링
* 필터 패턴-목록-에서-전용
* 엔터티 또는 의도 필터 제거
* 의도 또는 엔터티 페이지의 기존 발화에서 패턴 추가

## <a name="next-steps"></a>다음 단계

* 에 대해 알아봅니다 하는 방법 [패턴을 빌드](luis-tutorial-pattern.md) 를 pattern.any 및 자습서를 사용 하 여 역할을 사용 하 여 합니다.
* 앱을 [학습](luis-how-to-train.md)시키는 방법에 대해 알아봅니다.
