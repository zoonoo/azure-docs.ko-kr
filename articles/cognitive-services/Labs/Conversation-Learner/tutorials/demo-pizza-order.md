---
title: 데모 Conversation Learner 모델, 피자 주문 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: 데모 Conversation Learner 모델을 만드는 방법을 알아봅니다.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 052ef249f3367a562e5598b90533c0e52ed75df4
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171387"
---
# <a name="demo-pizza-order"></a>데모: 피자 주문
이 데모에서는 피자 주문 봇을 설명합니다. 이 기능으로 단일 피자의 주문을 지원합니다.

- 사용자 발언에서 피자 토핑 인식
- 피자 토핑에 재고가 있는지를 확인하고 적절하게 응답
- 이전 주문에서 피자 토핑을 기억하고 동일한 토핑의 새로운 주문을 시작하도록 제안

## <a name="video"></a>비디오

[![데모 피자 미리 보기](http://aka.ms/cl-demo-pizza-preview)](http://aka.ms/blis-demo-pizza)

## <a name="requirements"></a>요구 사항
이 자습서를 수행하려면 피자 주문 봇이 실행 중이어야 합니다.

    npm run demo-pizza

### <a name="open-the-demo"></a>데모 열기

Web UI의 모델 목록에서 TutorialDemo 피자 주문을 클릭합니다. 

## <a name="entities"></a>엔터티

세 가지 엔터티를 만들었습니다.

- Toppings: 이 엔터티는 사용자가 요청한 토핑을 누적합니다. 재고가 있는 유효한 토핑을 포함합니다. 토핑에 재고가 있는지를 확인합니다.
- OutofStock: 이 엔터티는 사용자에게 선택한 토핑에 재고가 없다는 것을 알리는 데 사용됩니다.
- LastToppings: 주문하면 이 엔터티를 사용하여 해당 주문에 대한 토핑 목록을 사용자에게 제공합니다.

![](../media/tutorial_pizza_entities.PNG)

### <a name="actions"></a>작업

피자를 어떻게 할지 사용자에게 묻고, 지금까지 추가한 주문을 알리는 등을 비롯한 작업 집합을 만들었습니다.

두 개의 API를 호출합니다.

- FinalizeOrder: 피자를 주문합니다.
- UseLastToppings: 이전 주문에서 토핑을 마이그레이션합니다. 

![](../media/tutorial_pizza_actions.PNG)

### <a name="training-dialogs"></a>학습 대화 상자
몇몇 학습 대화를 정의했습니다. 

![](../media/tutorial_pizza_dialogs.PNG)

예를 들어 학습 세션을 시도해 보겠습니다.

1. 학습 대화 상자, 새 학습 대화 상자를 차례로 클릭합니다.
1. '피자 주문'을 입력합니다.
2. 작업에 점수 지정을 클릭합니다.
3. '어떤 피자를 원하나요?'를 선택하도록 클릭합니다.
4. '버섯과 치즈'를 입력합니다.
    - LUIS는 둘 다 토핑으로 레이블을 지정합니다. 정확하지 않은 경우 강조 표시를 클릭한 다음, 수정할 수 있습니다.
    - 엔터티 옆에 있는 '+' 기호는 토핑 집합에 추가된다는 의미입니다.
5. 작업에 점수 지정을 클릭합니다.
    - `mushrooms` 및 `cheese`는 Toppings용 메모리에 없습니다.
3. '피자에 $Toppings이 있습니다'를 선택하도록 클릭합니다.
    - 이것은 비 대기 작업이므로 봇이 다음 작업을 요청합니다.
6. '추가로 주문하시겠습니까?'를 선택합니다.
7. '버섯 빼고 피망 추가'를 입력합니다.
    - `mushroom`에는 옆에 '-' 기호가 있어서 제거할 수 있습니다. `peppers`에는 옆에 '+' 기호가 있어서 토핑을 추가할 수 있습니다.
2. 작업에 점수 지정을 클릭합니다.
    - `peppers`는 새로운 항목이라 굵게 표시되었습니다. 또한 `mushrooms`에는 줄이 그어졌습니다.
8. '피자에 $Toppings이 있습니다'를 선택하도록 클릭합니다.
6. '추가로 주문하시겠습니까?'를 선택합니다.
7. '콩 추가'를 입력합니다.
    - `Peas`는 재고가 없는 토핑의 예입니다. 그렇지만 여전히 토핑으로 레이블이 지정됩니다.
2. 작업에 점수 지정을 클릭합니다.
    - `Peas`는 OutOfStock으로 표시됩니다.
    - 이러한 과정의 진행 방식을 이해하려면 `C:\<\installedpath>\src\demos\demoPizzaOrder.ts`에서 코드를 여세요. 그런 후 EntityDetectionCallback 메서드를 확인합니다. 토핑에 재고가 있는지 확인하기 위해 각 토핑 이후에 이 메서드가 호출됩니다. 그렇지 않으면 토핑 집합에서 지우고 OutOfStock 엔터티에 추가합니다. 재고 토핑 목록이 있는 해당 메서드 위에 inStock 변수가 정의됩니다.
6. '$OutOfStock이 없습니다.'를 선택합니다.
7. '추가로 주문하시겠습니까?'를 선택합니다.
8. '아니요'를 입력합니다.
9. 작업에 점수 지정을 클릭합니다.
10. 'FinalizeOrder' API 호출을 선택합니다. 
    - 그러면 코드에 정의된 'FinalizeOrder' 함수를 호출합니다. 토핑을 지우고 '주문 중입니다.'를 반환합니다. 
2. '다른 주문'을 입력합니다. 새 주문을 시작합니다.
9. 작업에 점수 지정을 클릭합니다.
    - ‘치즈’와 ‘피망’은 최근 주문의 토핑으로 메모리에 포함됩니다.
1. '$LastToppings를 주문하시겠습니까'를 선택합니다.
2. '예'를 입력합니다.
3. 작업에 점수 지정을 클릭합니다.
    - 봇은 UseLastToppings 작업을 수행하려고 합니다. 두 콜백 메서드의 두 번째입니다. 그러면 최근 주문의 토핑을 토핑에 복사하고 마지막 토핑을 지웁니다. 이 마지막 주문을 기억하고 사용자가 다른 피자를 주문하는 경우 해당 토핑을 옵션으로 제공하는 방법입니다.
2. '피자에 $Toppings이 있습니다'를 선택하도록 클릭합니다.
3. '추가로 주문하시겠습니까?'를 선택합니다.
8. '아니요'를 입력합니다.
4. 학습 완료를 클릭합니다.

![](../media/tutorial_pizza_callbackcode.PNG)

![](../media/tutorial_pizza_apicalls.PNG)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [데모 - VR 앱 시작 관리자](./demo-vr-app-launcher.md)
