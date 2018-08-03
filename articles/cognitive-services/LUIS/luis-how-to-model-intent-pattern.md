---
title: LUIS 앱에서 패턴 템플릿 추가 | Microsoft Docs
titleSuffix: Azure
description: LUIS(Language Understanding) 응용 프로그램에서 패턴 템플릿을 추가하여 예측 정확도를 향상시키는 방법에 대해 알아봅니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: diberry;
ms.openlocfilehash: bf1931355fd873eaeac6c313b70717dfa99814c6
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39222599"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>패턴을 추가하여 예측 정확도를 개선하는 방법
LUIS 앱이 끝점 발화를 수신한 후 패턴의 [개념](luis-concept-patterns.md)을 사용하여 단어 순서 및 단어 선택에 패턴을 노출하는 발화에 대한 예측 정확도를 개선합니다. 패턴은 [엔터티](luis-concept-entity-types.md) 및 해당 역할을 사용하여 특정 패턴 구문을 통해 데이터를 추출합니다. 

## <a name="add-template-utterance-to-create-pattern"></a>템플릿 발화를 추가하여 패턴 만들기
1. **내 앱** 페이지에서 이름을 선택하여 앱을 열고 왼쪽 패널의 **앱 성능 개선** 아래에서 **패턴**을 선택합니다.

    ![패턴 목록 스크린샷](./media/luis-how-to-model-intent-pattern/patterns-1.png)

2. 패턴에 대한 올바른 의도를 선택합니다. 

    ![의도 선택](./media/luis-how-to-model-intent-pattern/patterns-2.png)

3. 템플릿 텍스트 상자에 템플릿 발화를 입력하고 Enter 키를 선택합니다. 엔터티 이름을 입력하려면 올바른 패턴 엔터티 구문을 사용합니다. `{`를 사용하여 엔터티 구문을 시작합니다. 엔터티 목록이 표시됩니다. 올바른 엔터티를 선택한 후 Enter 키를 선택합니다. 

    ![패턴에 대한 엔터티 스크린샷](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    엔터티에 역할이 포함된 경우 `{Location:Origin}`와 같은 엔터티 이름 뒤에 단일 콜론 `:`으로 역할을 표시합니다. 엔터티 역할 목록이 목록에 표시됩니다. 역할을 선택한 후 Enter 키를 선택합니다. 

    ![역할이 있는 엔터티 스크린샷](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    올바른 엔터티를 선택한 후 패턴 입력을 완료하고 Enter 키를 선택합니다. 패턴 입력을 완료하면 앱을 [학습](luis-how-to-train.md)시킵니다.

    ![두 유형의 엔터티가 모두 포함된 입력된 패턴 스크린샷](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="search-patterns"></a>패턴 검색
검색을 통해 일부 제공된 텍스트가 포함된 패턴을 찾을 수 있습니다.  

1. 돋보기 아이콘을 선택합니다.

    ![검색 도구 아이콘이 강조 표시된 패턴 페이지의 스크린샷](./media/luis-how-to-model-intent-pattern/search-icon.png)

    패턴 목록의 오른쪽 위 모서리에 있는 검색 상자에 검색 텍스트를 입력하고 Enter 키를 선택합니다. 패턴 목록이 업데이트되어 검색 텍스트를 포함한 패턴만 표시됩니다.

    ![검색 상자의 검색 텍스트가 강조 표시된 패턴 페이지의 스크린샷](./media/luis-how-to-model-intent-pattern/search-text.png)

    검색을 취소하고 전체 패턴 목록을 복원하려면 입력한 검색 텍스트를 삭제합니다.

<!-- TBD: should I be able to click on the magnifying glass again to close the search box? It doesn't reset the list. -->

## <a name="edit-a-pattern"></a>패턴 편집
1. 패턴을 편집하려면 해당 패턴 줄의 오른쪽 끝에 있는 줄임표(***...***) 단추를 선택하고 **편집**을 선택합니다. 

    ![패턴 행의 편집 메뉴 항목 스크린샷](./media/luis-how-to-model-intent-pattern/patterns-three-dots.png) 

2. 텍스트 상자에 변경 내용을 입력합니다. 완료되면 Enter 키를 선택합니다. 패턴 편집을 완료하면 앱을 [학습](luis-how-to-train.md)시킵니다.

    ![패턴 편집 스크린샷](./media/luis-how-to-model-intent-pattern/edit-pattern.png)

## <a name="reassign-individual-pattern-to-different-intent"></a>개별 패턴을 다른 의도에 다시 할당

단일 패턴을 다른 의도에 다시 할당하려면 패턴 텍스트 오른쪽에 있는 의도 목록 상자를 선택하고 다른 의도를 선택합니다.

![개별 패턴을 다른 의도에 다시 할당하는 작업의 스크린샷](./media/luis-how-to-model-intent-pattern/reassign-individual-pattern.png)

## <a name="reassign-several-patterns-to-different-intent"></a>여러 패턴을 다른 의도에 다시 할당

여러 패턴을 다른 의도에 다시 할당하려면 각 패턴 왼쪽에 있는 확인란을 선택하거나 맨 위 확인란을 선택합니다. **의도 다시 할당** 옵션은 도구 모음에 표시됩니다. 패턴에 대한 올바른 의도를 선택합니다. 

![여러 패턴을 다른 의도에 다시 할당하는 작업의 스크린샷](./media/luis-how-to-model-intent-pattern/reassign-many-patterns.png)

## <a name="delete-a-single-pattern"></a>단일 패턴 삭제

1. 패턴을 삭제하려면 해당 패턴 줄의 오른쪽 끝에 있는 줄임표(***...***) 단추를 선택하고 **삭제**를 선택합니다. 

    ![발화 삭제 스크린샷](./media/luis-how-to-model-intent-pattern/patterns-three-dots-ddl.png)

2. **확인**을 선택하여 삭제를 확인합니다.

    ![삭제 확인 스크린샷](./media/luis-how-to-model-intent-pattern/confirm-delete.png)

## <a name="delete-several-patterns"></a>여러 패턴 삭제

1. 여러 패턴을 삭제하려면 각 패턴 왼쪽에 있는 확인란을 선택하거나 맨 위 확인란을 선택합니다. **패턴 삭제** 옵션은 도구 모음에 표시됩니다. **패턴 삭제**를 선택합니다.  

    ![여러 패턴 삭제 스크린샷](./media/luis-how-to-model-intent-pattern/delete-many-patterns.png)

2. **패턴 삭제** 확인 대화 상자가 표시됩니다. **확인**을 선택하여 삭제를 완료합니다.

    ![여러 패턴 삭제 스크린샷](./media/luis-how-to-model-intent-pattern/delete-many-patterns-confirmation.png)

## <a name="filter-pattern-list-by-entity"></a>엔터티별 패턴 목록 필터링

특정 엔터티별로 패턴 목록을 필터링하려면 패턴 위의 도구 모음에서 **엔터티 필터**를 선택합니다. 

![엔터티별 패턴 필터링 스크린샷](./media/luis-how-to-model-intent-pattern/filter-entities-1.png)

필터가 적용된 후 엔터티 이름이 도구 모음 아래에 표시됩니다. 

## <a name="filter-pattern-list-by-intent"></a>의도별 패턴 목록 필터링

특정 의도별로 패턴 목록을 필터링하려면 패턴 위의 도구 모음에서 **의도 필터**를 선택합니다. 

![의도별 패턴 필터링 스크린샷](./media/luis-how-to-model-intent-pattern/filter-intents-1.png)

필터가 적용된 후 의도 이름이 도구 모음 아래에 표시됩니다. 

## <a name="remove-entity-or-intent-filter"></a>엔터티 또는 의도 필터 제거
패턴 목록이 필터링되면 엔터티 또는 의도 이름이 도구 모음 아래에 표시됩니다. 필터를 제거하려면 이름을 선택합니다.

![엔터티별로 필터링된 패턴 스크린샷](./media/luis-how-to-model-intent-pattern/filter-entities-2.png)

필터가 제거되고 모든 패턴이 표시됩니다. 

## <a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>의도 또는 엔터티 페이지의 기존 발화에서 패턴 추가
**의도** 또는 **엔터티** 페이지의 기존 발화에서 패턴을 만들 수 있습니다. 의도 또는 엔터티 페이지의 모든 발화가 목록에 표시되고 오른쪽 열에서 **편집**, **삭제** 및 **패턴으로 추가**와 같은 발화 수준 옵션에 액세스할 수 있습니다.

1. 선택한 발언 행에서 발언 오른쪽에 있는 줄임표(***...***) 단추를 선택하고 **패턴으로 추가**를 선택합니다.

    [![](./media/luis-how-to-model-intent-pattern/add-pattern-from-utterance.png "옵션 메뉴에서 패턴 추가가 강조 표시된 발화 테이블의 스크린샷")](./media/luis-how-to-model-intent-pattern/add-pattern-from-utterance.png)

2. [구문 규칙](luis-concept-patterns.md#pattern-syntax)에 따라 패턴을 수정합니다. 선택한 발화에 엔터티로 레이블이 지정되면 해당 엔터티는 이미 올바른 구문이 있는 패턴에 포함된 것입니다.

    ![엔터티별로 필터링된 패턴 스크린샷](./media/luis-how-to-model-intent-pattern/confirm-patterns-modal.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>패턴이 포함된 모델을 변경한 후 앱 학습
패턴을 추가, 편집, 제거 또는 다시 할당한 후 변경 내용을 끝점 쿼리에 적용하려면 앱을 [학습](luis-how-to-train.md)시키고 [게시](luis-how-to-publish-app.md)합니다. 

## <a name="next-steps"></a>다음 단계

* pattern.any 및 역할을 사용하여 [패턴을 빌드](luis-tutorial-pattern.md)하는 방법을 알아봅니다.
* 앱을 [학습](luis-how-to-train.md)시키는 방법에 대해 알아봅니다.