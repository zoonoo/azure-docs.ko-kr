---
title: Conversation Learner 모델에서 무효화할 수 있는 엔터티를 사용하는 방법 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner 모델에서 무효화할 수 있는 엔터티를 사용하는 방법을 알아봅니다.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 2c758d1dd5d4d1e7ab25faccd5280963211181d1
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388798"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>Conversation Learner 모델에서 무효화할 수 있는 엔터티를 사용하는 방법

이 자습서에서는 엔터티의 "무효화할 수 있는" 속성을 설명합니다.

## <a name="video"></a>비디오

[![무효화할 수 있는 엔터티 자습서 미리 보기](https://aka.ms/cl_Tutorial_v3_NegatableEntities_Preview)](https://aka.ms/cl_Tutorial_v3_NegatableEntities)

## <a name="requirements"></a>요구 사항
이 자습서를 수행하려면 일반 자습서 봇이 실행 중이어야 합니다.

    npm run tutorial-general

## <a name="details"></a>세부 정보
엔터티의 "Negatable" 속성을 사용 하면 모두 정상 (긍정) 레이블을 엔터티의 인스턴스 음수, 양수 및 음수 모델을 기반으로 학습 및 기존 엔터티의 값을 지웁니다. 해당 "무효화할 수 있는" 속성이 설정된 엔터티는 Conversation Leaner에서 무효화할 수 엔터티라고 합니다.

## <a name="steps"></a>단계

Web UI의 홈페이지에서 시작합니다.

### <a name="create-the-model"></a>모델 만들기

1. 선택 **새 모델**합니다.
2. 입력 **NegatableEntity** 에 대 한 **이름**합니다.
3. **만들기**를 선택합니다.

### <a name="entity-creation"></a>엔터티 생성

1. 선택 **엔터티** 왼쪽된 패널에서 **새 엔터티**합니다.
2. 선택 **학습 하는 사용자 지정** 에 대 한 **엔터티 형식**합니다.
3. 입력 **이름을** 에 대 한 **엔터티 이름**합니다.
4. 확인 **Negatable** 무엇 인가 엔터티 값을 제공 또는 사용자가 사용할 수 있도록 *하지* 엔터티의 값 하므로 일치 하는 엔터티 값을 삭제 합니다.
5. **만들기**를 선택합니다.

![](../media/T06_entity_create.png)

### <a name="create-the-first-action"></a>첫 번째 작업 만들기

1. 선택 **동작** 왼쪽된 패널에서 **새 작업**합니다.
2. 입력 **이름을 잘 모르겠습니다.** 에 대 한 **봇의 응답...** .
3. 입력 **이름을** 에 대 한 **되며 우량**합니다.
4. **만들기**를 선택합니다.

![](../media/T06_action_create_1.png)

### <a name="create-the-second-action"></a>두 번째 작업 만들기

1. 선택 **동작** 왼쪽된 패널에서 **새 작업**합니다.
2. 입력 **이름을 알 수 있습니다. $Name 것입니다.** 에 대 한 **봇의 응답...** .
3. **만들기**를 선택합니다.

> [!NOTE]
> 합니다 **이름을** 엔터티를 자동으로 추가 된를 **필요한 엔터티** 응답 utterance에서 참조 하 여 합니다.

이제 두 가지 작업이 있습니다.

![](../media/T06_action_create_2.png)

### <a name="train-the-model"></a>모델 학습

1. 선택 **학습 대화 상자** 왼쪽된 패널에서 **새 학습 대화**합니다.
2. 입력 **hello** 채팅 왼쪽된 패널에서 사용자의 utterance에 대 한 합니다.
3. 선택 **작업의 점수를 매길**합니다.
4. 선택 **이름을 잘 모르겠습니다.** 작업 목록입니다. 백분위 수는 제약 조건에 따라 올바른 작업으로 100%입니다.
5. 입력 **내 이름은 Frank** 채팅 왼쪽된 패널에서 사용자의 utterance에 대 한 합니다.
6. 강조 표시 **Frank** 선택한 **+ 이름**합니다. 무효화할 수 엔터티 인스턴스 두 개: (+)와 추가 또는 값을 덮어씁니다. (-) 빼기 값을 제거 합니다.
7. 선택 **작업의 점수를 매길**합니다. **이름** 이제 엔터티도 정의 됩니다 **Frank** 모델의 메모리에 있으므로 **필자는 사용자 이름입니다. $Name 것** 동작은 사용할 수 있습니다.
8. 선택 **이름을 알 수 있습니다. $Name 것입니다.** 작업 목록입니다.
9. 입력 **내 이름은 Frank 없습니다.** 채팅 왼쪽된 패널에서 사용자의 utterance에 대 한.
10. 강조 표시 **Frank** 선택한 **-이름** 에서 값을 지울 수는 **이름** 엔터티.
11. 선택 **작업의 점수를 매길**합니다.
12. 선택 **이름을 잘 모르겠습니다.** 작업 목록입니다.
13. 입력 **Susan입니다.** 에 대 한 사용자의 채팅 왼쪽된 패널에서 세 번째 utterance입니다.
14. 강조 표시 **Susan** 한 다음 **+ 이름** 

![](../media/T06_training.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [다중 값 엔터티](./07-multi-value-entities.md)다중 값 엔터티
