---
title: Conversation Learner 모델에서 다중값 엔터티를 사용하는 방법 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner 모델에서 다중값 엔터티를 사용하는 방법을 알아봅니다.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 6193a515f0d8136e0d420b7554cf26fee8f50953
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173104"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>Conversation Learner 모델에서 다중값 엔터티를 사용하는 방법
이 자습서는 엔터티의 “다중값” 속성을 보여 줍니다.

## <a name="video"></a>비디오

[![자습서 6 미리 보기](http://aka.ms/cl-tutorial-06-preview)](http://aka.ms/blis-tutorial-06)

##<a name="requirements"></a>요구 사항
이 자습서를 수행하려면 일반 자습서 봇이 실행 중이어야 합니다.

    npm run tutorial-general

## <a name="details"></a>세부 정보
“다중값”인 엔터티는 단일 값을 저장하지 않고 목록에 값을 누적합니다.  이 기능은 사용자가 피자의 토핑과 같이 두 개 이상의 값을 지정할 수 있는 엔터티에 유용합니다.

구체적으로, 엔터티가 “다중값”으로 표시된 경우 각 인식된 엔터티 인스턴스가 단일 엔터티 값을 덮어쓰지 않고 봇의 메모리에 있는 목록에 추가됩니다.

## <a name="steps"></a>단계

### <a name="create-the-model"></a>모델 만들기

1. Web UI에서 새 모델을 클릭합니다.
2. 이름에 MultiValueEntities를 입력합니다. 만들기를 클릭합니다.

### <a name="create-an-entity"></a>엔터티 만들기

1. 엔터티, 새 엔터티를 차례로 클릭합니다.
2. 엔터티 이름에 Toppings를 입력합니다.
3. 다중값을 선택합니다.
    - 다중값 엔터티는 엔터티에 하나 이상의 값을 누적합니다.
2. Negatable(부정 가능)을 선택합니다.  
    - 이렇게 하면 사용자가 누적된 피자 토핑 목록에서 토핑을 제거할 수 있습니다.
3. 만들기를 클릭합니다.

![](../media/tutorial6_entities.PNG)

### <a name="create-two-actions"></a>두 가지 작업 만들기

1. 작업, 새 작업을 차례로 클릭합니다.
2. 응답에 ‘What toppings do you want?’를 입력합니다.
3. Disqualifying(실격) 엔터티에 Toppings를 입력합니다.
3. 만들기 클릭

그런 다음, 두 번째 작업을 만듭니다.

1. 작업, 새 작업을 차례로 클릭하여 두 번째 작업을 만듭니다.
3. 응답에 ‘Here are your toppings: $Toppings’를 입력합니다.
4. 만들기 클릭

이제 두 가지 작업이 있습니다.

![](../media/tutorial6_actions.PNG)

### <a name="train-the-bot"></a>봇 학습

1. 학습 대화 상자, 새 학습 대화 상자를 차례로 클릭합니다.
2. ‘hello’를 입력합니다.
3. Score Actions(작업에 점수 지정)를 클릭하고 ‘What toppings do you want?’를 선택합니다.
2. ‘mushrooms and cheese’를 입력합니다. 
    - 0개, 1개 또는 2개 이상의 엔터티에 레이블을 지정할 수 있습니다.
3. ‘mushrooms’를 클릭하고 Toppings를 선택합니다.
4. ‘cheese’를 클릭하고 Toppings를 선택합니다.
5. 작업에 점수 지정을 클릭합니다.
    - 이제 두 값이 Toppings 엔터티에 표시됩니다. 
6. ‘Here are your toppings: $Toppings’를 선택합니다.

다음과 같이 이 엔터티에 토핑을 더 추가할 수 있습니다.

7. ‘add peppers’를 입력합니다.
    - 엔터티 검색 아래에서 ‘peppers’를 클릭하고 Toppings를 선택합니다.
3. 작업에 점수 지정을 클릭합니다.
    - 이제 ‘peppers’가 Toppings에 추가 값으로 표시됩니다.
6. ‘Here are your toppings: $Toppings’를 선택합니다.

토핑을 제거하고 새 토핑을 추가하겠습니다.

2. ‘remove peppers and add sausage’를 입력합니다.
1. ‘peppers’를 클릭하고 빨간색 x를 클릭하여 제거합니다.
2. ‘peppers’를 클릭하고 ‘-Toppings’를 선택합니다.
3. 작업에 점수 지정을 클릭합니다.
    - ‘peppers’가 삭제되었으며 ‘sausage’가 추가되었습니다.
6. ‘Here are your toppings: $Toppings’를 선택합니다.

이제 모든 토핑을 제거하겠습니다.

6. ‘remove mushrooms, remove cheese, and remove sausage’를 입력합니다.
7. 세 토핑을 각각 클릭하고 ‘-Toppings’를 선택합니다.
7. 작업에 점수 지정을 클릭합니다.
    - 모든 토핑이 지워집니다.
2. ‘What toppings do you want?’를 선택합니다.
3. 학습 완료를 클릭합니다.

![](../media/tutorial6_dialogs.PNG)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기본 제공 엔터티](./7-built-in-entities.md)
