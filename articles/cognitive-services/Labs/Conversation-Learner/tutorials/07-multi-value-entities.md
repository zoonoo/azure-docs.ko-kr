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
ms.openlocfilehash: 1f62def5e498f3f744beaed0cda207e1a75bfdf2
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66387955"
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

1. 선택 **새 모델**합니다.
2. 입력 **MultiValueEntities** 에 대 한 **이름**합니다.
3. **만들기**를 선택합니다.

### <a name="entity-creation"></a>엔터티 생성

1. 선택 **엔터티** 왼쪽된 패널에서 **새 엔터티**합니다.
2. 선택 **학습 하는 사용자 지정** 에 대 한 **엔터티 형식**합니다.
3. 입력 **토 핑** 에 대 한 **엔터티 이름**합니다.
4. 확인할 **다중값** 엔터티를 사용 하도록 설정 하려면 하나 이상의 값을 누적 합니다.
5. 확인할 **무효화할 수**입니다.
6. **만들기**를 선택합니다.

![](../media/T07_entity_create.png)

### <a name="create-the-first-action"></a>첫 번째 작업 만들기

1. 선택 **동작** 왼쪽된 패널에서 **새 작업**합니다.
2. 입력 **토 핑 하는 다음과 같습니다: $toppings** 에 대 한 **봇의 응답...** . 선행 달러 기호는 엔터티 참조를 나타냅니다.
3. **만들기**를 선택합니다.

![](../media/T07_action_create_1.png)

### <a name="create-the-second-action"></a>두 번째 작업 만들기

1. 선택 **동작** 왼쪽된 패널에서 **새 작업**합니다.
2. 입력 **어떤 토 핑 하 시겠습니까?** 에 대 한 **봇의 응답...** .
3. 입력 **토 핑** 에 대 한 **되며 우량**합니다.
4. **만들기**를 선택합니다.

이제 두 가지 작업이 있습니다.

![](../media/T07_action_create_2.png)

### <a name="train-the-model"></a>모델 학습

1. 선택 **학습 대화 상자** 왼쪽된 패널에서 **새 학습 대화**합니다.
2. 입력 **안녕하세요** 채팅 왼쪽된 패널에서 사용자의 utterance에 대 한 합니다.
3. 선택 **작업의 점수를 매길**합니다.
4. 선택 **어떤 토 핑 하 시겠습니까?** 작업 목록에서. 백분위 수는 제약 조건에 따라 올바른 작업으로 100%입니다.
5. 입력 **치즈 및 버섯** 채팅 왼쪽된 패널에서 사용자의 utterance에 대 한 합니다.
6. 강조 표시 **치즈** 선택한 **+ 토 핑**합니다.
7. 강조 표시 **버섯** 선택한 **+ 토 핑**합니다.
8. 선택 **작업의 점수를 매길**합니다.
9. 선택 **토 핑 하는 다음과 같습니다: $toppings** 작업 목록에서.
10. 입력 **고추를 각각 나타냅니다 추가** 채팅 왼쪽된 패널에서 사용자의 다음 utterance에 대 한 합니다.
11. 강조 표시 **고추를 각각 나타냅니다** 선택한 **+ 토 핑**합니다.
12. 선택 **작업의 점수를 매길**합니다.
13. 선택 **토 핑 하는 다음과 같습니다: $toppings** 작업 목록에서.
14. 입력 **치즈 제거** 채팅 왼쪽된 패널에서 사용자의 세 번째 utterance에 대 한 합니다.
15. 강조 표시 **치즈** 선택한 **-토 핑**합니다.
16. 선택 **작업의 점수를 매길**합니다.
17. 선택 **토 핑 하는 다음과 같습니다: $toppings** 작업 목록에서.

![](../media/T07_training.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [미리 학습된 엔터티](./08-pre-trained-entities.md)
