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
ROBOTS: NOINDEX
ms.openlocfilehash: 4edbfc396d4ab32b991b084a7b738f04b5205418
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704101"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>Conversation Learner 모델에서 무효화할 수 있는 엔터티를 사용하는 방법

이 자습서에서는 엔터티의 "무효화할 수 있는" 속성을 설명합니다.

## <a name="video"></a>비디오

[![무효화할 수 있는 엔터티 자습서 미리 보기](https://aka.ms/cl_Tutorial_v3_NegatableEntities_Preview)](https://aka.ms/cl_Tutorial_v3_NegatableEntities)

## <a name="requirements"></a>요구 사항
이 자습서를 수행하려면 일반 자습서 봇이 실행 중이어야 합니다.

    npm run tutorial-general

## <a name="details"></a>세부 정보
엔터티의 "Negatable" 속성을 사용 하면 엔터티의 일반 (긍정) 및 음의 인스턴스에 레이블을 지정할 수 있으며, 긍정 및 부정 모델을 기반으로 학습 하 고 기존 엔터티의 값을 지울 수 있습니다. 해당 "무효화할 수 있는" 속성이 설정된 엔터티는 Conversation Leaner에서 무효화할 수 엔터티라고 합니다.

## <a name="steps"></a>단계

Web UI의 홈페이지에서 시작합니다.

### <a name="create-the-model"></a>모델 만들기

1. **새 모델**을 선택 합니다.
2. **이름**에 **NegatableEntity** 를 입력 합니다.
3. **만들기**를 선택합니다.

### <a name="entity-creation"></a>엔터티 생성

1. 왼쪽 패널에서 **엔터티** 를 선택 하 고 **새 엔터티**를 선택 합니다.
2. **엔터티 형식**에 대해 **학습 된 사용자 지정** 을 선택 합니다.
3. **엔터티 이름**에 **이름을** 입력 합니다.
4. **Negatable** 를 선택 하 여 사용자가 엔터티 값을 제공할 수 있도록 하거나, 엔터티 값이 *아닌* 것으로 일치 하는 엔터티 값을 삭제 합니다.
5. **만들기**를 선택합니다.

![](../media/T06_entity_create.png)

### <a name="create-the-first-action"></a>첫 번째 작업 만들기

1. 왼쪽 패널에서 **동작** 을 선택 하 고 **새 작업**을 선택 합니다.
2. **이름을 알 수 없음을 입력 합니다.** **Bot Response**의 경우 ....
3. **Disqualifying 되며**의 **이름을** 입력 합니다.
4. **만들기**를 선택합니다.

![](../media/T06_action_create_1.png)

### <a name="create-the-second-action"></a>두 번째 작업 만들기

1. 왼쪽 패널에서 **동작** 을 선택 하 고 **새 작업**을 선택 합니다.
2. 이름을 **알려주세요 .를 입력 합니다. $Name입니다.** **Bot Response**의 경우 ....
3. **만들기**를 선택합니다.

> [!NOTE]
> **이름** 엔터티는 응답 utterance 참조로 자동으로 **필수 엔터티로** 추가 되었습니다.

이제 두 가지 작업이 있습니다.

![](../media/T06_action_create_2.png)

### <a name="train-the-model"></a>모델 학습

1. 왼쪽 패널에서 **학습** 대화 상자를 선택 하 고 **새 학습 대화 상자**를 선택 합니다.
2. 왼쪽 채팅 패널에서 사용자의 utterance에 대 한 **hello** 를 입력 합니다.
3. **점수 동작**을 선택 합니다.
4. **이름을 알지 못합니다 .를 선택 합니다.** 작업 목록에서 백분위 수는 제약 조건을 기반으로 하는 유일한 올바른 동작으로 100%입니다.
5. 왼쪽 채팅 패널에서 사용자의 utterance에 대 한 **이름 입력은 Frank입니다** .
6. **Frank** 를 강조 표시 한 다음 **+ 이름**을 선택 합니다. Negatable 엔터티에는 두 개의 인스턴스가 있습니다. (+)는 값을 추가 하거나 덮어씁니다. (-) 빼기 값을 제거 합니다.
7. **점수 동작**을 선택 합니다. 이제 **name** 엔터티는 모델의 메모리에서 **Frank** 로 정의 되므로 사용자의 **이름을 알 수 있습니다. $Name** 작업을 사용할 수 있습니다.
8. 이름 **확인을 선택 합니다. $Name입니다.** 작업 목록에서
9. **이름 입력은 Frank이 아닙니다.** 왼쪽 채팅 패널에서 사용자의 utterance 합니다.
10. **Frank** 를 강조 표시 하 고 **이름** 엔터티에서 값을 선택 취소 합니다.
11. **점수 동작**을 선택 합니다.
12. **이름을 알지 못합니다 .를 선택 합니다.** 작업 목록에서
13. **이름 입력은 김소미입니다.** 왼쪽 채팅 패널에서 사용자의 세 번째 utterance.
14. **김소미** 를 강조 표시 한 다음 **+ 이름** 

![](../media/T06_training.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [다중 값 엔터티](./07-multi-value-entities.md)다중 값 엔터티
