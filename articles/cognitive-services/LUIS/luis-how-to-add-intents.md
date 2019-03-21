---
title: 의도 추가
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS 앱에 의도를 추가하여 동일한 의도가 포함된 질문 또는 명령 그룹을 식별합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/19/2019
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: 0d6813c11b5b9ed4aef666f5eb3dc75d8c9de770
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2019
ms.locfileid: "56593425"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>의도를 추가하여 발화에 대한 사용자 의도 결정

LUIS 앱에 [의도](luis-concept-intent.md)를 추가하여 동일한 의도가 포함된 질문 또는 명령 그룹을 식별합니다. 

의도는 맨 위 탐색 모음의 **빌드** 섹션에서 관리된 다음, 왼쪽 패널의 **의도**에서 관리됩니다. 

## <a name="add-intent"></a>의도 추가

1. **의도** 페이지에서 **새 의도 만들기**를 선택합니다.

1. **새 의도 만들기** 대화 상자에서 의도 이름을 `GetEmployeeInformation`으로 입력하고 **완료**를 클릭합니다.

    ![의도 추가](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

## <a name="add-an-example-utterance"></a>예제 발화 추가

예제 발언은 사용자 질문 또는 명령의 텍스트 예제입니다. LUIS(Language Understanding)를 학습하려면 의도에 예제 발화를 추가해야 합니다.

1. **GetEmployeeInformation** 의도 정보 페이지에서 의도 이름 아래의 텍스트 상자에 사용자로부터 예상하는 관련 발화(예: `Does John Smith work in Seattle?`)를 입력하고 Enter 키를 누릅니다.
 
    ![발언이 강조 표시된 의도 세부 정보 페이지의 스크린샷](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    LUIS는 모든 발화를 소문자로 변환하고 하이픈과 같은 토큰 주위에 공백을 추가합니다.

## <a name="intent-prediction-discrepancy-errors"></a>의도 예측 불일치 오류 

의도의 발화에서 선택한 의도와 예측 점수 간의 의도 예측 불일치가 발생할 수 있습니다. LUIS는 예제 발화 행의 **레이블 지정 의도** 주위에 빨간색 상자를 표시하여 이러한 불일치를 나타냅니다. 

![발언 예측 불일치 오류가 표시된 의도 세부 정보 페이지의 스크린샷](./media/luis-how-to-add-intents/prediction-discrepancy-intent.png) 

맨 위 탐색에서 **학습**을 선택합니다. 이제 예측 불일치가 사라졌습니다.

> [!Note]
> 빨간색 선은 단어나 구를 예제 utterance에서 관리 되는 경우는 [엔터티 예측 오차](luis-how-to-add-example-utterances.md#entity-status-predictions) 발생 했습니다. 수정 해야 합니다. 

## <a name="add-a-custom-entity"></a>사용자 지정 엔터티 추가

발화가 의도에 추가되면 발화 내의 텍스트를 선택하여 사용자 지정 엔터티를 만들 수 있습니다. 사용자 지정 엔터티는 올바른 의도와 함께 추출할 텍스트에 태그를 지정하는 방법입니다. 

참조 [utterance에 엔터티 추가](luis-how-to-add-example-utterances.md) 자세한 합니다.

## <a name="entity-prediction-discrepancy-errors"></a>엔터티 예측 불일치 오류 

엔터티에 빨간색 밑줄이 표시되어 [엔터티 예측 불일치](luis-how-to-add-example-utterances.md#entity-status-predictions)를 나타냅니다. 이는 엔터티의 첫 번째 발생이므로, LUIS에서 이 텍스트에 올바른 엔터티로 태그가 지정될 것을 확신할 수 있는 충분한 예가 없습니다. 앱을 학습하고 나면 이 불일치가 제거됩니다. 

![의도 세부 정보 페이지, 사용자 지정 엔터티 이름이 파란색으로 강조 표시된 스크린샷](./media/luis-how-to-add-intents/create-custom-entity-name-blue-highlight.png) 

텍스트가 파란색으로 강조 표시되어 엔터티를 나타냅니다.  

## <a name="add-a-prebuilt-entity"></a>미리 빌드된 엔터티 추가

자세한 내용은 [미리 빌드된 엔터티](luis-how-to-add-entities.md#add-prebuilt-entity)를 참조하세요.

## <a name="using-the-contextual-toolbar"></a>상황에 맞는 도구 모음 사용

목록에서를 utterance의 왼쪽에 있는 확인란에서 하나 이상의 예제 길이 발언을 선택할 때 utterance 목록 위의 도구 모음을 사용 하면 다음 작업을 수행할 수 있습니다.

* 의도 다시 할당: 발화를 다른 의도로 이동합니다.
* 발화 삭제
* 엔터티 필터: 필터링된 엔터티를 포함하는 발화만 표시합니다.
* 모두 표시/오류만 표시: 예측 오류가 있는 발화만 표시하거나 모든 발화를 표시합니다.
* 엔터티/토큰 보기: 엔터티 이름이 있는 엔터티 보기를 표시하거나 발화의 원시 텍스트를 표시합니다.
* 돋보기: 특정 텍스트가 포함된 발화를 검색합니다.

## <a name="working-with-an-individual-utterance"></a>개별 발화 작업

발화 오른쪽에 있는 줄임표 메뉴에서 개별 발화에 대해 다음 작업을 수행할 수 있습니다.

* 편집: 발화 텍스트를 변경합니다.
* 삭제: 의도에서 발화를 제거합니다. 발화를 유지하려는 경우, 더 나은 방법은 **없음** 의도로 이동하는 것입니다. 
* 패턴 추가: 패턴을 사용하면 일반적인 발언을 만들고 대체 가능한 텍스트 및 무시 가능한 텍스트를 표시할 수 있으므로 의도에 필요한 발화 수를 줄일 수 있습니다. 

**레이블 지정 의도** 열에서 발화의 의도를 변경할 수 있습니다.

## <a name="train-your-app-after-changing-model-with-intents"></a>의도가 포함된 모델을 변경한 후 앱 학습

의도를 추가, 편집 또는 제거한 후 변경 내용이 엔드포인트 쿼리에 적용되도록 앱을 [학습](luis-how-to-train.md) 및 [게시](luis-how-to-publish-app.md)합니다. 

## <a name="next-steps"></a>다음 단계

엔터티와 함께 [예제 발화](luis-how-to-add-example-utterances.md)를 추가하는 방법을 자세히 알아봅니다. 
