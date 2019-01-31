---
title: Conversation Learner 모델에서 다중값 엔터티를 사용하는 방법 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner 모델에서 다중값 엔터티를 사용하는 방법을 알아봅니다.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 8544d63f38f88a0e623dff343bf8b5133931b70b
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228307"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>Conversation Learner 모델에서 다중값 엔터티를 사용하는 방법
이 자습서는 엔터티의 다중값 속성을 보여줍니다.

## <a name="video"></a>비디오

[![다중값 엔터티 자습서 미리 보기](https://aka.ms/cl_Tutorial_v3_MultiValued_Preview)](https://aka.ms/cl_Tutorial_v3_MultiValued)

## <a name="requirements"></a>요구 사항
이 자습서를 수행하려면 일반 자습서 봇이 실행 중이어야 합니다.

    npm run tutorial-general

## <a name="details"></a>세부 정보
다중값 엔터티는 단일 값을 저장하지 않고 목록에 값을 누적합니다.  이러한 엔터티는 사용자가 둘 이상의 값을 지정할 수 있는 경우 유용합니다. 피자의 토핑을 예를 들어보겠습니다.

다중값으로 표시된 엔터티는 각 인식된 엔터티의 인스턴스를 봇의 메모리에 있는 목록에 추가하게 됩니다. 후속 인식은 엔터티의 값을 덮어쓰지 않고 추가합니다.

## <a name="steps"></a>단계

### <a name="create-the-model"></a>모델 만들기

1. Web UI에서 “새 모델”을 클릭합니다.
2. “이름” 필드에 “MultiValueEntities”를 입력하고 Enter 키를 누릅니다.
3. “만들기” 단추를 클릭합니다.

### <a name="entity-creation"></a>엔터티 생성

1. 왼쪽 패널에서 “엔터티”를 클릭한 다음, “새 엔터티” 단추를 클릭합니다.
2. “엔터티 형식”에 대해 “사용자 지정 학습”을 선택합니다.
3. “엔터티 이름”에 대해 “토핑”을 입력합니다.
4. “다중값” 확인란을 선택합니다.
    - 다중값 엔터티는 엔터티에서 둘 이상의 값을 누적합니다.
5. “무효화 가능” 확인란을 선택합니다.
    - “무효화 가능” 속성은 다른 자습서에서 다루었습니다.
6. “만들기” 단추를 클릭합니다.

![](../media/tutorial6_entities.PNG)

### <a name="create-the-first-action"></a>첫 번째 작업 만들기

1. 왼쪽 패널에서 “작업”을 클릭한 다음, “새 작업” 단추를 클릭합니다.
2. “봇의 응답...” 필드에 “토핑은 다음과 같습니다. $toppings”를 입력합니다.
    - 앞에 붙은 달러 기호는 엔터티 참조를 나타냅니다.
3. “만들기” 단추를 클릭합니다.

### <a name="create-the-second-action"></a>두 번째 작업 만들기

1. 왼쪽 패널에서 “작업”을 클릭한 다음, “새 작업” 단추를 클릭합니다.
2. “봇의 응답...” 필드에 “어떤 토핑을 하시겠습니까?”를 입력합니다.
3. “비우량 엔터티” 필드에서 “토핑”을 입력합니다.
4. “만들기” 단추를 클릭합니다.

이제 두 가지 작업이 있습니다.

![](../media/tutorial6_actions.PNG)

### <a name="train-the-model"></a>모델 학습

1. 왼쪽 패널에서 “학습 대화 상자”를 클릭한 다음, “새 학습 대화 상자” 단추를 클릭합니다.
2. “메시지를 입력하세요...”가 표시된 채팅 패널에 “안녕하세요”를 입력합니다.
3. “작업 점수 매기기” 단추를 클릭합니다.
4. “어떤 토핑을 하시겠습니까?”라는 응답을 선택합니다.
    - 제약 조건에 기반한 유일한 유효 작업이므로 백분위수는 100%입니다.
5. “메시지를 입력하세요...”가 표시된 채팅 패널에 “치즈와 버섯”을 입력합니다.
6. ‘치즈’를 클릭하고 “+토핑” 레이블을 선택합니다.
7. ‘버섯’을 클릭하고 “+토핑” 레이블을 선택합니다.
8. “작업 점수 매기기” 단추를 클릭합니다.
9. “토핑은 다음과 같습니다. $toppings” 응답을 선택합니다.
10. “메시지를 입력하세요...”가 표시된 채팅 패널에 “피망 추가”를 입력합니다.
11. ‘피망’을 클릭하고 “+토핑” 레이블을 선택합니다.
12. “작업 점수 매기기” 단추를 클릭합니다.
13. “토핑은 다음과 같습니다. $toppings” 응답을 선택합니다.
14. “메시지를 입력하세요...”가 표시된 채팅 패널에 “치즈 빼기”를 입력합니다.
15. ‘치즈’를 클릭하고 “-토핑”을 선택합니다.
16. “작업 점수 매기기” 단추를 클릭합니다.
17. “토핑은 다음과 같습니다. $toppings” 응답을 선택합니다.

![](../media/tutorial5_dialogs.PNG)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [미리 학습된 엔터티](./08-pre-trained-entities.md)
