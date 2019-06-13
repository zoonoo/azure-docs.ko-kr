---
title: 미리 학습된 엔터티를 Conversation Learner 모델에 추가하는 방법 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: 미리 학습된 엔터티를 Conversation Learner 모델에 추가하는 방법을 알아봅니다.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: fb70983c2f9fd20368bb8c6803c9568b27141af7
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389266"
---
# <a name="how-to-add-pre-trained-entities"></a>미리 학습된 엔터티를 추가하는 방법
이 자습서에서는 미리 학습된 엔터티를 Conversation Learner 모델에 추가하는 방법을 보여 줍니다.

## <a name="video"></a>비디오

[![미리 학습된 엔터티 자습서 미리 보기](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities_Preview)](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities)

## <a name="requirements"></a>요구 사항
이 자습서를 수행하려면 일반 자습서 봇이 실행 중이어야 합니다.

    npm run tutorial-general

## <a name="details"></a>세부 정보

미리 학습된 엔터티는 숫자, 날짜, 금액 등과 같은 일반적인 형식의 엔터티를 인식합니다.  이 엔터티는 "즉시" 작동하고 모든 학습을 필요로 하지 않으며, 사용자 지정 엔터티와 달리 해당 동작을 변경할 수 없습니다.  기본적으로 미리 학습된 엔터티는 다중값이며, 엔터티의 식별된 모든 인스턴스를 누적합니다.

## <a name="steps"></a>단계

Web UI의 홈페이지에서 시작합니다.

### <a name="create-the-model"></a>모델 만들기

1. 선택 **새 모델**합니다.
2. 입력 **PretrainedEntities** 에 대 한 **이름**합니다.
3. **만들기**를 선택합니다.

### <a name="entity-creation"></a>엔터티 생성

1. 선택 **엔터티** 왼쪽된 패널에서 **새 엔터티**합니다.
2. 선택 **사전 Trained/datetimeV2** 에 대 한 **엔터티 형식**합니다.
3. 확인할 **다중값** 엔터티를 사용 하도록 설정 하려면 하나 이상의 값을 누적 합니다. 참고로 Pre-Trained 엔터티를 무효화할 수 일 수 없습니다.
4. **만들기**를 선택합니다.

![](../media/T08_entity_create.png)

1. 선택 **동작** 왼쪽된 패널에서 **새 작업**합니다.
2. 입력 **날짜가 $builtin-datetimev2** 에 대 한 **봇의 응답...** .
3. **만들기**를 선택합니다.

![](../media/T08_action_create_1.png)

### <a name="create-the-second-action"></a>두 번째 작업 만들기

1. 선택 **동작** 왼쪽된 패널에서 **새 작업**합니다.
2. 입력 **날짜 이란?** 에 대 한 **봇의 응답...** . 미리 학습 된 엔터티 수 없습니다 **필요한 엔터티** 모든 표현에 대해 기본적으로 인식 되는 대로 합니다.
3. 입력 **builtin datetimev2** 에 대 한 **우량 엔터티**합니다.
4. **만들기**를 선택합니다.

![](../media/T08_action_create_2.png)

### <a name="train-the-model"></a>모델 학습

1. 선택 **학습 대화 상자** 왼쪽된 패널에서 **새 학습 대화**합니다.
2. 입력 **hello** 채팅 왼쪽된 패널에서 사용자의 utterance에 대 한 합니다.
3. 선택 **작업의 점수를 매길**합니다.
4. 선택 **날짜 란?** 작업 목록에서
5. 입력 **오늘** 채팅 왼쪽된 패널에서 사용자의 utterance에 대 한 합니다.
    - 합니다 **오늘** utterance LUIS에서 미리 학습 된 모델에서 자동으로 인식 됩니다.
    - 미리 학습된 엔터티의 값을 마우스로 가리키면 LUIS에서 제공되는 추가 데이터가 표시됩니다.

![](../media/T08_training.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [엔터티 해결 프로그램](./09-entity-resolvers.md)
