---
title: Conversation Learner 모델에서 대기 및 비대기 작업을 사용하는 방법 - Microsoft Cognitive Services| Microsoft Docs
titleSuffix: Azure
description: Conversation Learner 모델에서 대기 및 비대기 작업을 사용하는 방법을 알아봅니다.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: a8f7ccf79e750c9f3c21c25c50c3e275db7e4195
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173087"
---
# <a name="wait-and-non-wait-actions"></a>대기 및 비대기 작업

이 자습서는 Conversation Learner에서 대기 작업과 비대기 작업 간의 차이를 보여 줍니다.

## <a name="video"></a>비디오

[![자습서 2 미리 보기](http://aka.ms/cl-tutorial-02-preview)](http://aka.ms/blis-tutorial-02)

## <a name="requirements"></a>요구 사항
이 자습서를 수행하려면 일반 자습서 봇이 실행 중이어야 합니다.

    npm run tutorial-general

## <a name="details"></a>세부 정보

- 대기 작업: 시스템이 “대기” 작업을 수행한 후 작업 수행을 중지하고 사용자 입력을 기다립니다.
- 비대기 작업: 시스템이 “비대기” 작업을 수행한 후 즉시 다른 작업을 선택합니다(먼저 사용자 입력을 기다리지 않음).

## <a name="steps"></a>단계

### <a name="create-a-new-model"></a>새 모델 만들기

1. Web UI에서 새 모델을 클릭합니다.
2. 이름에 WaitNonWait를 입력합니다. 만들기를 클릭합니다.

### <a name="create-the-first-wait-action"></a>첫 번째 대기 작업 만들기

1. 작업, 새 작업을 차례로 클릭합니다.
2. 응답에 ‘Which animal do you want?’를 입력합니다.
    - 대기 작업이므로 응답 대기 확인란을 선택된 상태로 둡니다.
3. 만들기를 클릭합니다.

### <a name="create-a-non-wait-action"></a>비대기 작업 만들기

1. 새 작업을 클릭합니다.
2. 응답에 ‘Cows say moo’를 입력합니다.
3. 응답 대기 확인란의 선택을 취소합니다.
4. 만들기 클릭

### <a name="create-a-second-non-wait-action"></a>두 번째 비대기 작업 만들기

1. 새 작업을 클릭합니다.
2. 응답에 ‘Ducks say quack’을 입력합니다.
3. 응답 대기 확인란의 선택을 취소합니다.
4. 만들기 클릭

![](../media/tutorial2_actions.PNG)

### <a name="train-the-bot"></a>봇 학습

1. Train Dialogs(학습 대화), New Train Dialog(새 학습 대화)를 차례로 클릭합니다.
2. ‘hello’를 입력합니다.
3. Score Actions(작업에 점수 지정)를 클릭하고 ‘Which animal do you want?’를 선택합니다.
4. ‘cow’를 입력합니다.
5. Score Actions(작업에 점수 지정)를 클릭하고 ‘Cows say moo’를 선택합니다.
    - 봇이 입력을 기다리지 않고 다음 작업을 수행합니다.
2. ‘Which animal do you want?’를 선택합니다.
3. ‘duck’을 입력합니다.
5. Score Actions(작업에 점수 지정)를 클릭하고 ‘Ducks say quack’을 선택합니다.

![](../media/tutorial2_dialogs.PNG)

> [!NOTE]
> 대기 및 비대기 작업과 관련해서 봇 응답의 시퀀스를 확인합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [엔터티 소개](./3-introduction-to-entities.md)
