---
title: LUIS 응용 프로그램에서 기능 추가 | Microsoft Docs
description: LUIS(Language Understanding)를 사용하여 의도 및 엔터티의 감지 또는 예측을 향상시킬 수 있는 앱 기능을 해당 범주 및 패턴에 추가
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/30/2018
ms.author: diberry
ms.openlocfilehash: 5ec75436c7df5c08f5507794229bec1f9adb2804
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39222956"
---
# <a name="use-features-to-improve-your-luis-apps-performance"></a>기능을 사용하여 LUIS 앱 성능 향상  

정확성을 개선하기 위해 LUIS 앱에 기능을 추가할 수 있습니다. 기능은 특정 단어와 구가 범주에 속한다는 힌트를 제공하여 LUIS를 지원합니다. LUIS가 범주의 한 멤버를 인식하는 방법을 학습하면 다른 멤버도 비슷하게 처리할 수 있습니다.

## <a name="add-phrase-list"></a>구 목록 추가

1. **내 앱** 페이지에서 해당 이름을 클릭하여 앱을 열고 **빌드**를 클릭한 후 앱 왼쪽 패널에서 **구 목록**을 클릭합니다. 

    ![구 목록 탐색](./media/luis-add-features/phrase-list-nav.png)

2. **구 목록** 페이지에서 **새 구 목록 만들기**를 클릭합니다. 
 
    ![새 구 목록 만들기](./media/luis-add-features/create-new-phrase-list.png)
    
3. **구 목록 추가** 대화 상자에서 구 목록 이름으로 "Cities"를 입력합니다. **값** 상자에 구 목록의 값을 입력합니다. 한 번에 하나의 값을 입력하거나 쉼표로 구분해서 여러 개의 값을 입력한 후 **Enter** 키를 누릅니다.

    ![구 목록 Cities 추가](./media/luis-add-features/add-phrase-list-cities.png)

4. LUIS는 구 목록에 추가할 관련 값을 제안할 수 있습니다. **권장**을 클릭하여 추가된 값과 의미 체계가 관련된 제안된 값 그룹을 표시합니다. 제안된 값 중 하나를 클릭하거나 **모두 추가**를 클릭하여 모두 추가할 수 있습니다.

    ![구 목록 제안된 값](./media/luis-add-features/related-values.png)

5. 추가된 구 목록 값이 서로 교환해서 사용할 수 있는 대체 값이면 **These values are interchangeable**(서로 교환 가능한 값)을 클릭합니다.

    ![구 목록 제안된 값](./media/luis-add-features/interchangeable.png)

6. **저장**을 클릭합니다. "Cities" 구 목록이 **구 목록** 페이지에 추가됩니다.

    ![구 목록 추가됨](./media/luis-add-features/phrase-list-cities.png)

## <a name="edit-phrase-list"></a>구 목록 편집

**구 목록** 페이지에서 구 목록의 이름을 클릭합니다. 표시되는 **구 목록 편집** 대화 상자에서 필요한 대로 편집한 후 **저장**을 클릭합니다.

 ![구 목록 추가됨](./media/luis-add-features/edit-phrase-list.png)

## <a name="delete-phrase-list"></a>구 목록 삭제 

행 끝에 있는 줄임표(***...***) 단추를 클릭하고 **삭제**를 선택합니다.

 ![추가된 목록 삭제](./media/luis-add-features/delete-phrase-list.png)

## <a name="deactivate-phrase-list"></a>구 목록 비활성화 

행 끝에 있는 줄임표(***...***) 단추를 클릭하고 **비활성화**를 선택합니다.

 ![추가된 목록 비활성화](./media/luis-add-features/deactivate-phrase-list.png)

## <a name="pattern-regular-expression-feature"></a>패턴(정규식) 기능 
**이 기능은 더 이상 사용되지 않습니다**. 새 패턴 기능은 LUIS에 추가할 수 없습니다. 모든 기존 패턴 기능은 2018년 5월까지 지원됩니다. PR을 사용한 표준 LUIS 정규식 일치의 경우 [Recognizers-Text Github 리포지토리](https://github.com/Microsoft/Recognizers-Text)에서 도움을 주세요. 

## <a name="next-steps"></a>다음 단계

구 목록을 추가, 편집, 삭제 또는 비활성화한 후에 다시 [앱을 학습하고 테스트](luis-interactive-test.md)하여 성능이 개선되는지 확인합니다.
