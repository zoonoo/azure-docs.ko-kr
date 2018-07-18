---
title: “Hello World” Conversation Learner 응용 프로그램을 만드는 방법 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: “Hello World” Conversation Learner 응용 프로그램을 만드는 방법을 알아봅니다.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 30026285ac6dda45d2f5e3718aae741b928cf242
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376359"
---
# <a name="how-to-create-a-hello-world-application-with-conversation-learner"></a>Conversation Learner에서 “Hello World” 응용 프로그램을 만드는 방법

이 자습서는 작업 만들기, 대화형 학습, 최종 사용자로부터 기록된 대화 수정을 포함하여 Conversation Learner로 시작하는 방법을 보여 줍니다.

## <a name="requirements"></a>요구 사항
아직 완료하지 않은 경우, 먼저 LUIS 작성 키를 사용하여 `.env` 파일 만들기를 비롯한 모든 설정 단계가 완료되었는지 확인합니다.  자세한 내용은 [빠른 시작](https://github.com/Microsoft/ConversationLearner-Samples)을 참조하세요.

이 자습서를 수행하려면 일반 자습서 봇이 실행 중이어야 합니다.

    npm run tutorial-general

## <a name="steps"></a>단계

Web UI의 홈페이지에서 시작합니다.

### <a name="create-the-app"></a>앱 만들기
1. 새 앱을 클릭합니다.
2. 이름 필드에 Hello World를 입력합니다.
3. 만들기 클릭

### <a name="create-an-action"></a>작업 만들기

1. Hello World 앱을 클릭하여 시작합니다.
2. 작업, 새 작업을 차례로 클릭합니다.
    - 작업은 Conversation Learner가 사용자에게 반환하는 텍스트 메시지, API 호출 또는 카드일 수 있습니다.
3. 응답에 ‘Hello World!’를 입력합니다.
    - 봇이 반환할 응답입니다.
4. 만들기 클릭

봇이 수행할 수 있는 첫 번째 작업인 텍스트 응답 반환을 만들었습니다.

### <a name="train-the-bot"></a>봇 학습

#### <a name="create-the-first-dialog"></a>첫 번째 대화 만들기

1. Train Dialogs(학습 대화), New Train Dialog(새 학습 대화)를 차례로 클릭합니다.
2. 대화의 시작 부분에 사용자가 말할 내용의 예를 입력합니다(예: ‘hello’).
3. Score Actions(작업에 점수 지정)를 클릭합니다.
4. ‘Hello World!’를 선택합니다.
    - 이렇게 하면 한 턴 예제 대화가 생성됩니다. 
2. ‘goodbye’를 입력합니다.
3. Score Actions(작업에 점수 지정)를 클릭합니다.
4. 작업 추가를 클릭하고 ‘Goodbye!’를 입력합니다. 응답에서 ‘Create’를 클릭합니다.
5. 학습 완료를 클릭합니다. 이 학습 대화가 종료됩니다.

이제 시스템에 하나의 학습 대화가 있습니다.

#### <a name="continue-teaching-the-bot"></a>봇 학습 계속
학습을 하나 더 수행하고 봇이 어떻게 응답하는지 살펴보겠습니다.

1. 새 학습 대화를 클릭합니다.
2. ‘hi there’를 입력합니다.
    - 첫 번째 대화와 유사하므로, 봇에서 좋은 점수를 얻을 것으로 예상합니다.
2. Score Action(작업에 점수 지정)을 클릭합니다.
    - 그래도 위치와 점수가 충분히 정확하지 않아 추가 학습이 필요할 수 있습니다.
3. ‘Hello World!’ 옆에 있는 선택을 클릭합니다.
4. ‘bye’를 입력합니다.
5. Score Actions(작업에 점수 지정)를 클릭합니다.
6. ‘Goodbye!’를 선택합니다.
7. 학습 완료를 클릭합니다.

![](../media/tutorial1_actions.PNG)

다른 학습 세션을 수행하여 봇이 작동하는지 살펴보겠습니다.

‘hi’ 및 ‘byebye’를 사용하여 위의 단계를 반복하고, Score Actions(작업에 점수 지정)를 클릭할 때 봇 응답의 위치와 점수가 어떻게 변경되는지 확인합니다.

이제 ‘howdy’ 및 ‘good bye’를 사용하여 단계를 반복하고, 점수 지정에서 봇이 이 조작을 학습했음을 나타내는 점수 향상을 확인합니다.

![](../media/tutorial1_dialogs.PNG)

### <a name="test-the-bot-as-an-end-user"></a>최종 사용자로 봇 테스트

1. 로그 대화, 새 로그 대화를 차례로 클릭합니다.
2. ‘hello there’를 입력합니다.
3. ‘bye’를 입력합니다.

‘bye’로 대화를 시작하고 봇의 응답을 확인할 수도 있습니다.

### <a name="view-conversations-in-the-log-dialogs"></a>로그 대화에서 대화 보기

로그 대화에서 대화 목록을 보고, 업데이트하고, 조작을 학습 대화로 저장할 수 있습니다. 이러한 작업을 하려면 다음을 수행합니다.

1. 대화 로그를 클릭합니다.
2. 대화가 양호하면 마지막 작업(예: ‘Goodbye’)을 클릭합니다.
3. 제안된 응답을 클릭하여 선택합니다. 
    - 다른 작업을 선택하거나 추가할 수도 있습니다.
4. 완료를 클릭하여 학습 대화로 저장합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [대기 및 비대기 작업](./2-wait-vs-nonwait-actions.md)