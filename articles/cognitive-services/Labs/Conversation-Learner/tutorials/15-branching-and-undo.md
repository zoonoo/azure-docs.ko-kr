---
title: Conversation Learner 모델에서 분기 및 실행 취소 작업을 사용하는 방법 - Microsoft Cognitive Services| Microsoft Docs
titleSuffix: Azure
description: Conversation Learner 모델에서 분기 및 실행 취소 작업을 사용하는 방법을 알아봅니다.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 05140693026e21a73b756ed0ea7bc9936bef067e
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173301"
---
# <a name="how-to-use-branching-and-undo-operations"></a>분기 및 실행 취소 작업을 사용하는 방법
이 자습서에서는 실행 취소 및 분기 작업을 설명합니다.


## <a name="details"></a>세부 정보
- 실행 취소: 개발자가 사용자 입력 또는 작업 선택을 "실행 취소"할 수 있습니다. 내부적으로 "실행 취소"는 실제로 새 대화 상자를 만들고 이전 단계까지 다시 수행합니다.  즉, 대화 상자에서 엔터티 검색 콜백 및 API 호출을 다시 호출합니다.

- 분기: 기존 학습 대화 상자와 같은 방식으로 시작하는 새 학습 대화 상자를 만듭니다. 그러면 대화 상자를 수동으로 다시 입력할 필요가 없습니다. 내부적으로 "분기"는 새 대화 상자 만들고 선택한 단계까지 기존 학습 대화 상자를 다시 수행합니다.  즉, 대화 상자에서 엔터티 검색 콜백 및 API 호출을 다시 호출합니다.


## <a name="requirements"></a>요구 사항
이 자습서를 수행하려면 피자 주문 봇이 실행 중이어야 합니다.

    npm run demo-pizza

### <a name="open-the-demo"></a>데모 열기

Web UI의 모델 목록에서 TutorialDemo 피자 주문을 클릭합니다. 

피자 주문 데모에 대한 자세한 내용은 피자 주문 자습서를 참조하세요.

## <a name="undo"></a>실행 취소

대화 상자의 일부를 실행 취소하고 해당 단계에서 다시 만듭니다.

### <a name="training-dialogs"></a>학습 대화 상자
학습 세션을 시작하겠습니다. 

1. 학습 대화 상자, 새 학습 대화 상자를 차례로 클릭합니다.
1. '피자 주문'을 입력합니다.
2. 작업에 점수 지정을 클릭합니다.
3. '어떤 피자를 원하나요?'를 선택하도록 클릭합니다.
4. '버섯과 치즈'를 입력합니다.
5. 작업에 점수 지정을 클릭합니다.
3. '피자에 $Toppings이 있습니다'를 선택하도록 클릭합니다.
6. '추가로 주문하시겠습니까?'를 선택합니다.
7. '버섯 빼고 피망 추가'를 입력합니다.
    - 버섯을 선택하고 토핑 엔터티의 선택을 취소합니다. 실행 취소할 작업을 만듭니다.
2. 실행 취소 단계를 클릭합니다.
    - 마지막 항목이 제거되고 '추가로 주문하시겠습니까?'로 돌아갑니다.  (아래 스크린샷)
2. '버섯 빼고 피망 추가'를 입력합니다.
8. '피자에 $Toppings이 있습니다'를 선택하도록 클릭합니다.
    - 두 엔터티를 올바르게 선택했는지 확인합니다.
2. 작업에 점수 지정을 클릭합니다. 이제 수정된 대화 상자를 계속할 수 있습니다.
4. 학습 완료를 클릭합니다.

실행 취소를 사용하여 사용자 입력 및 작업을 제거하는 방법을 살펴보았습니다.

![](../media/tutorial15_undo.PNG)

## <a name="branch"></a>Branch

예를 들어 기존 학습 대화 상자를 열고 분기별로 또 다른 학습 대화 상자를 만들겠습니다.

1. 학습 대화 상자, '새 주문'을 차례로 클릭하고 기존 대화 상자를 엽니다. 
2. 대화 상자에서 마지막 '아니요'를 클릭합니다(아래 스크린샷 참조).
3. 분기를 클릭합니다.
    - '아니요'가 제거되고 해당 시점까지 전체 대화 상자가 새로운 대화 상자에 복사됩니다. 
    - 그러면 이 지점에서 새 "분기"를 탐색하기 위해 위의 순서를 다시 입력할 필요가 없습니다.
1. '예'를 입력합니다.
2. 작업에 점수 지정을 클릭합니다.
3. '피자에 $Toppings이 있습니다'를 선택합니다.
6. '추가로 주문하시겠습니까?'를 선택합니다.
7. '아니요'를 입력합니다.
4. 학습 완료를 클릭합니다.

![](../media/tutorial15_branch.PNG)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [버전 관리 및 태그 지정](./16-versioning-and-tagging.md)
