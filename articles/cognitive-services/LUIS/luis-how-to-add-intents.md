---
title: LUIS 응용 프로그램에서 의도 추가 | Microsoft Docs
description: LUIS(Language Understanding)를 사용하여 앱이 사용자 요청을 이해하고 적절하게 대응하는 데 도움이 되는 의도를 추가합니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: 0ebf15ea49467674ab3c56aa7983131593cf5c9a
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225889"
---
# <a name="manage-intents"></a>의도 관리 
LUIS 앱에 [의도](luis-concept-intent.md)를 추가하여 동일한 의도가 포함된 질문 또는 명령 그룹을 식별합니다. 

LUIS 왼쪽 패널의 **의도**에서 사용할 수 있는 **의도** 페이지에서 의도를 추가하고 관리합니다. 

다음 절차는 TravelAgent 앱에서 “Bookflight” 의도를 추가하는 방법을 보여줍니다.

## <a name="add-intent"></a>의도 추가

1. **내 앱** 페이지에서 이름을 클릭하여 앱(예: TravelAgent)을 열고 왼쪽 패널에서 **의도**를 클릭합니다. 
2. **의도** 페이지에서 **새 의도 만들기**를 클릭합니다.

    ![의도 목록](./media/luis-how-to-add-intents/IntentsList.png)
3. **새 의도 만들기** 대화 상자에서 의도 이름을 “BookFlight”로 입력하고 **완료**를 클릭합니다.

    ![의도 추가](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    새로 추가된 의도의 의도 세부 정보 페이지에서 [발화를 추가](#add-an-utterance-on-intent-page)합니다.

## <a name="rename-intent"></a>의도 이름 바꾸기

1. **의도** 페이지에서 의도 이름 옆에 있는 [이름 바꾸기] 아이콘 ![의도 이름 바꾸기](./media/luis-how-to-add-intents/Rename-Intent-btn.png)을 클릭합니다. 

2. **의도** 페이지에서 현재 의도 이름이 대화 상자에 표시됩니다. 의도 이름을 편집하고 Enter 키를 누릅니다. 새 이름이 저장되고 의도 페이지에 표시됩니다.

    ![의도 편집](./media/luis-how-to-add-intents/EditIntent-dialogbox.png)

## <a name="delete-intent"></a>의도 삭제
없음 의도 이외의 의도를 삭제할 경우 모든 발화를 없음 의도에 추가하도록 선택할 수 있습니다. 이 기능은 발화를 삭제하는 대신 이동해야 하는 경우 유용합니다.   

1. **의도** 페이지에서 의도 이름 오른쪽에 있는 **의도 삭제** 단추를 클릭합니다. 

    ![의도 삭제 단추](./media/luis-how-to-add-intents/DeleteIntent.png)

2. 확인 대화 상자에서 “확인” 단추를 클릭합니다.

<!--
    TBD: waiting for confirmation about which delete dialog is going to be in //BUILD

    ![Delete Intent Dialog](./media/luis-how-to-add-intents/DeleteIntent-Confirmation.png)
-->


## <a name="add-an-utterance-on-intent-page"></a>의도 페이지에서 발화 추가

의도 페이지에서 의도 이름 아래의 텍스트 상자에 사용자로부터 예상하는 관련 발화(예: `book 2 adult business tickets to Paris tomorrow on Air France`)를 입력하고 Enter 키를 누릅니다. 
 
>[!NOTE]
>LUIS는 모든 발화를 소문자로 변환합니다.

![발언이 강조 표시된 의도 세부 정보 페이지의 스크린샷](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

현재 의도에 대한 발화 목록에 발화가 추가됩니다. 발화가 추가되면 발화 내의 [모든 엔터티에 레이블을 지정](luis-how-to-add-example-utterances.md)하고 앱을 [학습](luis-how-to-train.md)시킵니다. 

## <a name="create-a-pattern-from-an-utterance"></a>발화에서 패턴 만들기
[의도 또는 엔터티 페이지의 기존 발화에서 패턴 추가](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page)를 참조하세요.

## <a name="edit-an-utterance-on-intent-page"></a>의도 페이지에서 발화 편집

발언을 편집하려면 해당 발언 줄의 오른쪽 끝에 있는 줄임표(***...***) 단추를 선택하고 **편집**을 선택합니다. 텍스트를 수정한 다음, 키보드에서 Enter 키를 누릅니다.

![줄임표 단추가 강조 표시된 의도 세부 정보 페이지의 스크린샷](./media/luis-how-to-add-intents/edit-utterance.png) 

## <a name="reassign-utterances-on-intent-page"></a>의도 페이지에서 발화 다시 할당
하나 이상의 발화를 다른 의도에 다시 할당하여 해당 발화의 의도를 변경할 수 있습니다. 

발화 행의 오른쪽 끝에 있는 다른 의도에 단일 발화를 다시 할당하려면 **레이블이 지정된 의도** 열 아래에서 올바른 의도 이름을 선택합니다. 발화가 현재 의도의 발화 목록에서 제거됩니다. 

![레이블이 지정된 의도 열 아래에 발화의 의도가 선택되어 있는 BookFlight 의도 페이지의 스크린샷](./media/luis-how-to-add-intents/reassign-1-utterance.png)

여러 발화의 의도를 변경하려면 발화 왼쪽에 있는 확인란을 선택한 다음, **의도 다시 할당**을 선택합니다. 목록에서 올바른 의도를 선택합니다.

![의도가 선택되고 의도 다시 할당 단추가 강조 표시된 BookFlight 의도 페이지의 스크린샷](./media/luis-how-to-add-intents/delete-several-utterances.png) 

## <a name="delete-utterances-on-intent-page"></a>의도 페이지에서 발화 삭제

발언을 삭제하려면 해당 발언 줄의 오른쪽 끝에 있는 줄임표(***...***) 단추를 선택하고 **삭제**를 선택합니다. 발화가 목록 및 LUIS 앱에서 제거됩니다.

![삭제 옵션이 강조 표시된 의도 세부 정보 페이지의 스크린샷](./media/luis-how-to-add-intents/delete-utterance-ddl.png)

여러 발화를 삭제하려면:

1. 발화 왼쪽에 있는 확인란을 선택한 다음, **발화 삭제**를 선택합니다. 

    ![발화가 선택되고 발화 삭제 단추가 강조 표시된 의도 세부 정보 페이지의 스크린샷](./media/luis-how-to-add-intents/delete-several-utterances.png)

2. **발화 삭제?** 팝업 대화 상자에서 **완료**를 선택합니다.

## <a name="search-in-utterances-on-intent-page"></a>의도 페이지에서 발화 검색
의도에서 텍스트(단어 또는 구문)가 포함된 발화를 검색할 수 있습니다. 예를 들어, 특정 단어와 관련된 오류를 확인하고 해당 특정 단어가 포함된 모든 예제를 찾을 수 있습니다. 

1. 도구 모음에서 돋보기 아이콘을 선택합니다.

    ![돋보기 검색 아이콘이 강조 표시된 의도 페이지의 스크린샷](./media/luis-how-to-add-intents/magnifying-glass.png)

2. 검색 텍스트 상자가 나타납니다. 발화 목록의 오른쪽 위 모서리에 있는 검색 상자에 단어나 구문을 입력합니다. 발화 목록이 업데이트되어 검색 텍스트가 포함된 발화만 표시됩니다. 

    ![검색 텍스트 상자가 강조 표시된 의도 페이지의 스크린샷](./media/luis-how-to-add-intents/search-textbox.png)

    검색을 취소하고 전체 발화 목록을 복원하려면 입력한 검색 텍스트를 삭제합니다. 검색 텍스트 상자를 닫으려면 도구 모음에서 돋보기 아이콘을 다시 선택합니다.

## <a name="prediction-discrepancy-errors-on-intent-page"></a>의도 페이지의 예측 불일치 오류
선택한 의도와 예측 점수 간에 의도의 발화가 일치하지 않을 수 있습니다. LUIS는 점수 주위에 빨간색 상자로 불일치를 나타냅니다. 

![예측 불일치 점수가 강조 표시된 BookFlight 의도 페이지의 스크린샷](./media/luis-how-to-add-intents/score-discrepancy.png) 

## <a name="filter-by-intent-prediction-discrepancy-errors-on-intent-page"></a>의도 페이지에서 의도 예측 불일치 오류별로 필터링
의도 예측 불일치가 포함된 발화만으로 발화 목록을 필터링하려면 도구 모음에서 **모두 표시**에서 **오류만**으로 토글합니다. 

## <a name="filter-by-entity-type-on-intent-page"></a>의도 페이지에서 엔터티 유형별로 필터링
도구 모음의 **엔터티 필터** 드롭다운을 사용하여 엔터티별로 발화를 필터링합니다. 

![엔터티 유형 필터가 강조 표시된 의도 페이지의 스크린샷](./media/luis-how-to-add-intents/filter-by-entities.png) 

필터를 제거하려면 도구 모음 아래에서 해당 단어 또는 구문이 포함된 파란색 필터 상자를 선택합니다.  
<!-- TBD: waiting for ux fix - bug in ux of prebuit entity number -- when filtering by it, it doesn't show the list -->

## <a name="switch-to-token-view-on-intent-page"></a>의도 페이지에서 토큰 보기로 전환
엔터티 유형 이름 대신 토큰을 보려면 **토큰 보기**를 토글합니다. 키보드에서 **Ctrl+E**를 사용하여 보기를 토글할 수도 있습니다. 

![토큰 보기가 강조 표시된 BookFlight 의도의 스크린샷](./media/luis-how-to-add-intents/toggle-tokens-view.png)

## <a name="train-your-app-after-changing-model-with-intents"></a>의도가 포함된 모델을 변경한 후 앱 학습
의도를 추가, 편집 또는 제거한 후 변경 내용을 끝점 쿼리에 적용하려면 앱을 [학습](luis-how-to-train.md)시키고 [게시](luis-how-to-publish-app.md)합니다. 

## <a name="next-steps"></a>다음 단계

앱에 의도를 추가한 후, 다음 작업은 추가한 의도에 대한 [예제 발화](luis-how-to-add-example-utterances.md) 추가를 시작하는 것입니다. 
