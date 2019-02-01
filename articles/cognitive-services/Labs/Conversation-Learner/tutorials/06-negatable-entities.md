---
title: Conversation Learner 모델에서 무효화할 수 있는 엔터티를 사용하는 방법 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner 모델에서 무효화할 수 있는 엔터티를 사용하는 방법을 알아봅니다.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: fea950e2c13d9b5ce0c3619990961e611edd6626
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55207380"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>Conversation Learner 모델에서 무효화할 수 있는 엔터티를 사용하는 방법

이 자습서에서는 엔터티의 "무효화할 수 있는" 속성을 설명합니다.

## <a name="video"></a>비디오

[![무효화할 수 있는 엔터티 자습서 미리 보기](https://aka.ms/cl_Tutorial_v3_NegatableEntities_Preview)](https://aka.ms/cl_Tutorial_v3_NegatableEntities)

## <a name="requirements"></a>요구 사항
이 자습서를 수행하려면 일반 자습서 봇이 실행 중이어야 합니다.

    npm run tutorial-general

## <a name="details"></a>세부 정보
엔터티의 "무효화할 수 있는" 속성을 사용하면 엔터티의 정상(긍정) 및 부정 인스턴스 모두에 레이블을 지정하고, 긍정 및 부정 모델에 따라 가르치고, 기존 엔터티의 값을 지울 수 있습니다. 해당 "무효화할 수 있는" 속성이 설정된 엔터티는 Conversation Leaner에서 무효화할 수 엔터티라고 합니다.

## <a name="steps"></a>단계

### <a name="create-the-model"></a>모델 만들기

1. Web UI에서 "새 모델"을 클릭합니다.
2. "이름" 필드에 "NegatableEntity"를 입력하고 Enter 키를 누릅니다.
3. "만들기" 단추를 클릭합니다.

### <a name="entity-creation"></a>엔터티 생성

1. 왼쪽 패널에서 “엔터티”를 클릭한 다음, “새 엔터티” 단추를 클릭합니다.
2. “엔터티 형식”에 대해 “사용자 지정”을 선택합니다.
3. "엔터티 이름"에 "이름"을 입력합니다.
4. “무효화 가능” 확인란을 선택합니다.
    - 이 속성을 확인하면 사용자가 엔터티 값을 제공하거나 어떤 것이 엔터티 값이 *아니*라고 말할 수 있습니다. 후자의 경우 결과는 일치하는 엔터티 값의 삭제입니다.
5. "만들기" 단추를 클릭합니다.

![](../media/tutorial5_entities.PNG)

### <a name="create-the-first-action"></a>첫 번째 작업 만들기

1. 왼쪽 패널에서 “작업”을 클릭한 다음, “새 작업” 단추를 클릭합니다.
2. "봇의 응답..." 필드에 "귀하의 이름을 모릅니다"를 입력합니다.
3. “실격 엔터티” 필드에서 “이름”을 입력합니다.
4. "만들기" 단추를 클릭합니다.

### <a name="create-the-second-action"></a>두 번째 작업 만들기

1. 왼쪽 패널에서 “작업”을 클릭한 다음, “새 작업” 단추를 클릭합니다.
2. "봇의 응답..." 필드에 "귀하의 이름을 압니다. $name입니다"를 입력합니다.
3. "만들기" 단추를 클릭합니다.

> [!NOTE]
> "이름" 엔터티를 응답에서 참조하여 "필수 엔터티"로 자동으로 추가했습니다.

이제 두 가지 작업이 있습니다.

![](../media/tutorial5_actions.PNG)

### <a name="train-the-model"></a>모델 학습

1. 왼쪽 패널에서 “학습 대화 상자”를 클릭한 다음, “새 학습 대화 상자” 단추를 클릭합니다.
2. "메시지를 입력하세요..."가 표시된 채팅 패널에 "안녕하세요"를 입력합니다.
3. “작업 점수 매기기” 단추를 클릭합니다.
4. "귀하의 이름을 모릅니다"라는 응답을 선택합니다.
    - 제약 조건에 따라 유일한 유효 작업이므로 백분위수는 100%입니다.
5. "메시지를 입력하세요..."가 표시된 채팅 패널에 "제 이름은 Frank입니다"를 입력합니다.
6. 'Frank'를 선택하고 "+이름" 레이블 선택
    - "이름" 엔터티는 "+이름" 및 "-이름"의 두 인스턴스가 있습니다.  (+) 더하기는 값을 추가하거나 덮어씁니다. (-) 빼기는 값을 제거합니다.
7. “작업 점수 매기기” 단추를 클릭합니다.
    - 이제 "이름" 엔터티가 모델의 메모리에서 "Frank"로 정의되므로 "귀하의 이름을 압니다. $Name입니다" 작업을 사용할 수 있습니다.
8. "귀하의 이름을 압니다. $name입니다"라는 응답을 선택합니다.
9. "메시지를 입력하세요..."가 표시된 채팅 패널에 "제 이름은 Frank가 아닙니다"를 입력합니다.
10. "Frank" 선택 및 "-이름" 레이블 선택
    - "-이름"을 선택하여 엔터티의 현재 값을 지웁니다.
11. “작업 점수 매기기” 단추를 클릭합니다.
12. "귀하의 이름을 모릅니다"라는 응답을 선택합니다.
13. "메시지를 입력하세요..."가 표시된 채팅 패널에 "제 이름은 Susan입니다"를 입력합니다.
14. 'Susan' 선택 및 "+이름" 레이블 선택

![](../media/tutorial5_dialogs.PNG)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [다중 값 엔터티](./07-multi-value-entities.md)다중 값 엔터티
