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
ROBOTS: NOINDEX
ms.openlocfilehash: fceca459b80725e1a9c8b7dbc6a4387ea98dbb7b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704129"
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

1. **새 모델**을 선택 합니다.
2. **이름**에 **PretrainedEntities** 를 입력 합니다.
3. **만들기**를 선택합니다.

### <a name="entity-creation"></a>엔터티 생성

1. 왼쪽 패널에서 **엔터티** 를 선택 하 고 **새 엔터티**를 선택 합니다.
2. **엔터티 형식**에 대해 **미리 학습 된/datetimeV2** 를 선택 합니다.
3. **다중** 값을 확인 하 여 엔터티가 하나 이상의 값을 누적 하도록 설정 합니다. 미리 학습 된 엔터티는 negatable 수 없습니다.
4. **만들기**를 선택합니다.

![](../media/T08_entity_create.png)

1. 왼쪽 패널에서 **동작** 을 선택 하 고 **새 작업**을 선택 합니다.
2. **봇의 응답**에 대 한 **$builtin-datetimev2 날짜** 를 입력 합니다.
3. **만들기**를 선택합니다.

![](../media/T08_action_create_1.png)

### <a name="create-the-second-action"></a>두 번째 작업 만들기

1. 왼쪽 패널에서 **동작** 을 선택 하 고 **새 작업**을 선택 합니다.
2. **봇의 응답**에 대 한 **날짜를** 입력 하세요 .... 미리 학습 된 엔터티는 모든 길이 발언에 대해 기본적으로 인식 되므로 **필수 엔터티가** 될 수 없습니다.
3. **Disqualifying 엔터티에**대해 **builtin-datetimev2** 을 입력 합니다.
4. **만들기**를 선택합니다.

![](../media/T08_action_create_2.png)

### <a name="train-the-model"></a>모델 학습

1. 왼쪽 패널에서 **학습** 대화 상자를 선택 하 고 **새 학습 대화 상자**를 선택 합니다.
2. 왼쪽 채팅 패널에서 사용자의 utterance에 대 한 **hello** 를 입력 합니다.
3. **점수 동작**을 선택 합니다.
4. 작업 목록에서 **날짜를** 선택 합니다.
5. 왼쪽 채팅 패널에서 사용자의 utterance에 대해 **today** 를 입력 합니다.
    - **오늘** UTTERANCE는 LUIS의 미리 학습 된 모델에서 자동으로 인식 됩니다.
    - 미리 학습된 엔터티의 값을 마우스로 가리키면 LUIS에서 제공되는 추가 데이터가 표시됩니다.

![](../media/T08_training.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [엔터티 해결 프로그램](./09-entity-resolvers.md)
