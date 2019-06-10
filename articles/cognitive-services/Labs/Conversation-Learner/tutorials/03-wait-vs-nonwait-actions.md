---
title: Conversation Learner 모델에서 대기 및 비대기 작업을 사용하는 방법 - Microsoft Cognitive Services| Microsoft Docs
titleSuffix: Azure
description: Conversation Learner 모델에서 대기 및 비대기 작업을 사용하는 방법을 알아봅니다.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 57a58b008792130c82a86ff4a5801f4bb39aabad
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66387898"
---
# <a name="wait-and-non-wait-actions"></a>대기 및 비대기 작업

이 자습서는 Conversation Learner에서 대기 작업과 비대기 작업 간의 차이를 보여 줍니다.

## <a name="video"></a>비디오

[![대기 및 비대기 자습서 미리 보기](https://aka.ms/cl_Tutorial_v3_WaitnonWait_Preview)](https://aka.ms/cl_Tutorial_v3_WaitnonWait)

## <a name="requirements"></a>요구 사항
이 자습서를 수행하려면 일반 자습서 봇이 실행 중이어야 합니다.

    npm run tutorial-general

## <a name="details"></a>세부 정보

- 대기 작업: 시스템이 “대기” 작업을 수행한 후 작업 수행을 중지하고 사용자 입력을 기다립니다.
- 비대기 작업: 시스템에서 "비대기" 작업을 수행한 후 즉시 사용자 입력을 기다리지 않고 다른 작업을 선택합니다.

## <a name="steps"></a>단계

### <a name="create-a-new-model"></a>새 모델 만들기

1. Web UI에서 새 모델을 클릭합니다.
2. "Name" 필드에 "대기 비대기"를 입력하고, Enter 키를 누르거나 "만들기" 단추를 클릭합니다.

### <a name="create-the-first-two-wait-actions"></a>처음 두 개의 대기 작업 만들기

1. 왼쪽 패널에서 “작업”을 클릭한 다음, “새 작업” 단추를 클릭합니다.
2. "봇의 응답..." 필드에 "어떤 피자 하시겠습니까?"를 입력합니다.
    - 대기 작업이므로 "응답 대기" 확인란을 선택된 상태로 둡니다.
3. "만들기" 단추를 클릭합니다.
4. 이러한 단계를 반복하면서 "피자 배당 중!"이란 봇의 응답을 사용하여 다른 작업을 만듭니다.

### <a name="train-using-those-wait-actions"></a>이러한 대기 작업을 사용한 학습

1. 왼쪽 패널에서 “학습 대화 상자”를 클릭한 다음, “새 학습 대화 상자” 단추를 클릭합니다.
2. "메시지를 입력하세요..."가 표시된 채팅 패널에 "Hi"를 입력합니다. 
    - 이렇게 하면 사용자의 쪽의 대화를 시뮬레이션합니다.
3. “작업 점수 매기기” 단추를 클릭합니다.
4. "어떤 피자 하시겠습니까?"라는 응답을 선택합니다.
5. 사용자로서 "Margherita"라고 응답합니다.
6. “작업 점수 매기기” 단추를 클릭합니다.
7. "피자 배달 중!"이란 응답을 선택합니다.
8. "저장" 단추를 클릭합니다.

### <a name="create-a-non-wait-action-while-training"></a>학습 중 비대기 작업 만들기
전에 했던 것처럼 비대기 작업을 만들 수 있다고해도 학습 세션 내에서 해당 작업을 만들 수 있습니다.
1. "새 학습 대화 상자" 단추를 클릭합니다.
2. 사용자로서 "Hello"를 입력합니다.
3. “작업 점수 매기기” 단추를 클릭합니다.
4. "+ 작업" 단추를 클릭합니다. 
    - 이렇게 하면 익숙한 "작업 만들기" 대화 상자로 이동합니다.
5. 봇의 응답으로 "피자 봇에 오신 것을 환영합니다!"를 입력합니다.
6. "응답 대기" 확인란의 선택을 취소합니다.
7. "만들기" 단추를 클릭합니다.
    - 봇은 즉시 "피자 봇에 오신 것을 환영합니다!"라고 응답합니다. 그러면 다른 봇 응답에 대한 메시지가 다시 나타납니다. 봇의 응답이 방금 만든 비대기 작업이기 때문입니다.
9. "어떤 피자 하시겠습니까?"라는 응답을 선택합니다.
10. 사용자로서 "Margherita"라고 응답합니다.
11. “작업 점수 매기기” 단추를 클릭합니다.
12. "피자 배달 중!"이란 응답을 선택합니다.
13. "저장" 단추를 클릭합니다.

> [!NOTE]
> 대기 및 비대기 작업과 관련해서 봇 응답의 시퀀스를 확인합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [엔터티 소개](./04-introduction-to-entities.md)
