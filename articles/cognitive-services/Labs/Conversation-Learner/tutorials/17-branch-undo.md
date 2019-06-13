---
title: Conversation Learner 모델에서 분기 및 실행 취소 작업을 사용하는 방법 - Microsoft Cognitive Services| Microsoft Docs
titleSuffix: Azure
description: Conversation Learner 모델에서 분기 및 실행 취소 작업을 사용하는 방법을 알아봅니다.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 6ffa0881df07e453c8beb175b8580deebbfc1ec9
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389886"
---
# <a name="how-to-use-branching-and-undo-operations"></a>분기 및 실행 취소 작업을 사용하는 방법
이 자습서에서는 실행 취소 및 분기 작업을 설명합니다.


## <a name="details"></a>세부 정보
### <a name="undo"></a>실행 취소
개발자가 최종 사용자 입력 또는 작업 선택을 "실행 취소"할 수 있습니다. 내부적으로 "실행 취소"는 실제로 새 대화 상자를 만들어 이전 단계까지 재생합니다.  즉, 대화 상자에서 엔터티 검색 콜백 및 API 호출을 다시 호출합니다.

### <a name="branch"></a>Branch
기존 학습 대화 상자와 같은 방식으로 시작하는 새 학습 대화 상자를 만듭니다. 그러면 대화 상자를 수동으로 다시 입력할 필요가 없습니다. 내부적으로 "분기"는 새 대화 상자를 만들고 선택한 단계까지 기존 학습 대화 상자를 재생합니다.  즉, 대화 상자에서 엔터티 검색 콜백 및 API 호출을 다시 호출합니다.


## <a name="requirements"></a>요구 사항
이 자습서를 수행하려면 피자 주문을 받는 봇이 실행 중이어야 합니다.

    npm run demo-pizza

### <a name="open-or-import-the-demo"></a>데모 열거나 가져오기

이미 피자 주문 자습서를 다 학습했으면 웹 UI의 목록에서 해당 모델을 열기만 하면 됩니다. 그렇지 않으면 "자습서 가져오기"를 클릭하고 "Demo-PizzaOrder"라는 모델을 선택해야 합니다.

## <a name="undo"></a>실행 취소

작업에서 `Undo` 기능을 참조하는 방법에 대한 예제는 다음과 같습니다.

### <a name="training-dialogs"></a>학습 대화 상자
1. 왼쪽 패널에서 “학습 대화 상자”를 클릭한 다음, `New Train Dialog` 단추를 클릭합니다.
2. "피자 주문"을 입력합니다.
3. `Score Actions` 단추를 클릭합니다.
4. 클릭하여 "어떤 피자를 원하나요?"를 선택합니다.
5. "모든 것"을 입력합니다.
6. `Undo` 단추를 클릭합니다.
    - 마지막 항목이 제거되면서 "어떤 피자를 원하나요?"라는 마지막 봇 응답이 유지됩니다.

## <a name="branch"></a>Branch

이 데모에서는 기존 학습 대화 상자를 열고 분기를 통해 새 학습 대화 상자를 만들겠습니다.

1. 왼쪽 패널에서 “학습 대화 상자”를 클릭합니다.
2. 그리드를 확인하는 경우 "새 주문"으로 시작하는 학습 하나만 표시되어야 합니다.
3. 그리드에서 "새 주문"을 클릭하여 기존 학습 대화 상자를 엽니다.
4. 대화 상자에서 마지막 "아니요"를 클릭합니다.
5. "분기" 아이콘을 클릭하면 아이콘은 이 이미지에서 빨간색 원으로 표시됩니다.
    - ![](../media/tutorial15_branch.PNG)
    - "아니요"에 앞서 전체 학습 대화 상자가 새 학습 대화 상자로 복사됩니다.
    - 그러면 이 지점에서 새 대화 "분기"를 살펴보려면 위의 순서를 다시 입력할 필요가 없습니다.
6. "예"를 입력하고 Enter 키를 누릅니다.
7. `Score Actions` 단추를 클릭합니다.
    - 봇이 응답을 자동으로 선택하는 경우 응답이 마음에 들지 않으면 응답을 변경합니다.
8. 마지막 봇 응답을 클릭합니다.
    - 그러면 다른 응답을 선택할 수 있습니다.
9. "UseLastToppings"를 선택합니다.
10. `Score Actions` 단추를 클릭합니다.
    - 다시 봇이 자동으로 응답을 선택합니다. "피자에 소시지, 치즈, 버섯이 있습니다"라고 표시되어야 합니다. 
    - 이번에는 응답이 마음에 들므로 보관하도록 하겠습니다.
11. `Score Actions` 단추를 클릭합니다.
    - 다시 봇이 자동으로 응답을 선택하면 "더 필요한 것이 있나요?"라고 표시되어야 합니다.
12. "아니요"를 입력합니다.
13. `Save Branch` 단추를 클릭합니다.
14. 이제 그리드에는 "새 주문"으로 시작하는 두 개의 학습이 있습니다.
    - 그 중 하나는 분기하는 데 사용했던 것입니다.
    - 다른 하나는 방금 저장한 분기 버전입니다.
    - 이러한 기대치를 확인하려면 각각을 클릭합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [버전 관리 및 태그 지정](./18-version-tag.md)
