---
title: Conversation Learner 모델에서 다중값 엔터티를 사용하는 방법 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner 모델에서 다중값 엔터티를 사용하는 방법을 알아봅니다.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 7911dd561da0f1e7b62b1b457ae5b059d5d54767
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704097"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>Conversation Learner 모델에서 다중값 엔터티를 사용하는 방법
이 자습서는 엔터티의 다중값 속성을 보여줍니다.

## <a name="video"></a>비디오

[![다중 값 엔터티 자습서 미리 보기](https://aka.ms/cl_Tutorial_v3_MultiValued_Preview)](https://aka.ms/cl_Tutorial_v3_MultiValued)

## <a name="requirements"></a>요구 사항
이 자습서를 수행하려면 일반 자습서 봇이 실행 중이어야 합니다.

    npm run tutorial-general

## <a name="details"></a>세부 정보
다중값 엔터티는 단일 값을 저장하지 않고 목록에 값을 누적합니다.  이러한 엔터티는 사용자가 둘 이상의 값을 지정할 수 있는 경우 유용합니다. 피자의 토핑을 예를 들어보겠습니다.

다중값으로 표시된 엔터티는 각 인식된 엔터티의 인스턴스를 봇의 메모리에 있는 목록에 추가하게 됩니다. 후속 인식은 엔터티의 값을 덮어쓰지 않고 추가합니다.

## <a name="steps"></a>단계

Web UI의 홈페이지에서 시작합니다.

### <a name="create-the-model"></a>모델 만들기

1. **새 모델**을 선택 합니다.
2. **이름**에 **multivalueentities** 를 입력 합니다.
3. **만들기**를 선택합니다.

### <a name="entity-creation"></a>엔터티 생성

1. 왼쪽 패널에서 **엔터티** 를 선택 하 고 **새 엔터티**를 선택 합니다.
2. **엔터티 형식**에 대해 **학습 된 사용자 지정** 을 선택 합니다.
3. **엔터티 이름**에 **토 핑** 를 입력 합니다.
4. **다중** 값을 확인 하 여 엔터티가 하나 이상의 값을 누적 하도록 설정 합니다.
5. **Negatable**를 확인 합니다.
6. **만들기**를 선택합니다.

![](../media/T07_entity_create.png)

### <a name="create-the-first-action"></a>첫 번째 작업 만들기

1. 왼쪽 패널에서 **동작** 을 선택 하 고 **새 작업**을 선택 합니다.
2. **토 핑:** **봇의 응답**에 대 한 $toppings를 입력 합니다. 선행 달러 기호는 엔터티 참조를 나타냅니다.
3. **만들기**를 선택합니다.

![](../media/T07_action_create_1.png)

### <a name="create-the-second-action"></a>두 번째 작업 만들기

1. 왼쪽 패널에서 **동작** 을 선택 하 고 **새 작업**을 선택 합니다.
2. **봇의 응답**에 **어떤 토 핑?** 를 입력 합니다.
3. **Disqualifying 되며**에 **토 핑** 를 입력 합니다.
4. **만들기**를 선택합니다.

이제 두 가지 작업이 있습니다.

![](../media/T07_action_create_2.png)

### <a name="train-the-model"></a>모델 학습

1. 왼쪽 패널에서 **학습** 대화 상자를 선택 하 고 **새 학습 대화 상자**를 선택 합니다.
2. 왼쪽 채팅 패널에서 사용자의 utterance으로 **hi** 를 입력 합니다.
3. **점수 동작**을 선택 합니다.
4. 작업 목록에서 원하는 **토 핑 무엇** 을 선택 하세요. 백분위 수는 제약 조건을 기반으로 하는 유일한 올바른 동작으로 100%입니다.
5. 왼쪽 채팅 패널에서 사용자의 utterance에 **치즈와 버섯** 을 입력 합니다.
6. **치즈** 를 강조 표시 한 다음 **+ 토 핑**를 선택 합니다.
7. **버섯** 을 강조 표시 하 고 **+ 토 핑**를 선택 합니다.
8. **점수 동작**을 선택 합니다.
9. 작업 목록에서 **토 핑: $toppings** 를 선택 합니다.
10. 왼쪽 채팅 패널에서 사용자의 다음 utterance에 대 한 **고추 추가** 를 입력 합니다.
11. **고추** 를 강조 표시 한 다음 **+ 토 핑**를 선택 합니다.
12. **점수 동작**을 선택 합니다.
13. 작업 목록에서 **토 핑: $toppings** 를 선택 합니다.
14. 왼쪽 채팅 패널에서 사용자의 세 번째 utterance에 대 한 **치즈 제거** 를 입력 합니다.
15. **치즈** 를 강조 표시 하 고 **토 핑**를 선택 합니다.
16. **점수 동작**을 선택 합니다.
17. 작업 목록에서 **토 핑: $toppings** 를 선택 합니다.

![](../media/T07_training.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [미리 학습된 엔터티](./08-pre-trained-entities.md)
