---
title: Conversation Learner 모델에서 세션 콜백을 사용하는 방법 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner 모델에서 세션 콜백을 사용하는 방법을 알아봅니다.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 0f51b232470e4e4da3f25d40d025dd3b09dd1204
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171918"
---
# <a name="how-to-use-session-callbacks-with-a-conversation-learner-model"></a>Conversation Learner 모델에서 세션 콜백을 사용하는 방법

이 자습서는 onSessionStart 및 onSessionEnd 콜백을 보여 줍니다.

## <a name="video"></a>비디오

[![자습서 11 미리 보기](http://aka.ms/cl-tutorial-11-preview)](http://aka.ms/blis-tutorial-11)

## <a name="requirements"></a>요구 사항
이 자습서를 수행하려면 `tutorialSessionCallbacks` 봇이 실행 중이어야 합니다.

    npm run tutorial-session-callbacks

## <a name="details"></a>세부 정보
이 자습서에서는 세션의 개념, 기본적으로 세션을 처리하는 방법 및 해당 동작을 재정의하는 방법을 설명합니다.

세션은 봇과의 대화입니다. 여러 턴이 있을 수 있지만 대화에 긴 중단(예: 30분)은 없습니다.  기본 세션 시간 제한 기간은 “한도”에 대한 도움말 페이지를 참조하세요.

긴 중단이 있는 경우 봇이 다음 세션으로 이동합니다.  새 세션을 시작하면 반복되는 신경망이 초기 상태로 바뀝니다.  기본적으로 이 동작은 모든 엔터티 값을 지우지만 변경 가능합니다(아래 그림 참조).

### <a name="open-the-demo"></a>데모 열기

모델 목록에서 Tutorial-11-SessionCallbacks를 클릭합니다. 

### <a name="entities"></a>엔터티

모델에는 4가지 엔터티가 정의됩니다.

![](../media/tutorial11_entities.PNG)

한 가지 주의할 사항은 BotName이 프로그래밍 엔터티라는 것입니다.  이 엔터티는 세션을 시작할 때 봇에서 설정됩니다.

### <a name="actions"></a>작업

모델에는 4가지 작업이 정의됩니다.

![](../media/tutorial11_actions.PNG)

첫째, 이 자습서는 세션을 시작할 때 엔터티 값을 제어하는 방법을 보여 줍니다. 예를 들어 사용자가 무엇을 말하기 전에 BotName 엔터티를 설정합니다.

둘째, 이 자습서는 한 세션의 값을 다음 세션에서도 유지하는 방법을 보여 줍니다.  이 자습서에서는 사용자 이름과 전화 번호가 세션 간에 동일하게 유지되지만 해당 위치는 변경될 수 있다고 가정합니다.  따라서 세션 간에 이름과 전화 번호를 유지하지만 사용자 위치는 지웁니다.

### <a name="train-dialog"></a>학습 대화

다음은 예제 대화입니다. 한 세션이므로 이 대화에는 긴 중단이 없습니다.

![](../media/tutorial11_traindialog.PNG)

### <a name="code-for-the-callbacks"></a>콜백의 코드

콜백 메서드의 코드는 c:\< installedpath>\src\demos\tutorialSessionCallbacks.ts 파일에 있습니다.

![](../media/tutorial11_code.PNG)

이 메서드는 둘 다 선택 사항입니다.

- OnSessionStartCallback: 이 메서드는 BotName 엔터티를 설정합니다.
- OnSessionEndCallback: 보존할 내용을 지정할 수 있습니다. 사용자 이름과 사용자 전화 번호를 제외한 모든 엔터티가 지워집니다.

### <a name="try-the-bot"></a>봇 사용해 보기

Web UI로 전환하고 로그 대화를 클릭합니다.

1. ‘hello’를 입력합니다.
2. 시스템: ‘Hi, I'm Botty. What's your name?’ 여기서 이름 Botty는 OnSessionStartCallback에서 가져옵니다.
3. ‘jason’을 입력합니다.
4. 시스템: ‘Hi jason. What's your phone number?’
5. ‘555-555-5555’를 입력합니다.
6. 시스템: ‘Can you tell Botty your location, jason?’
7. ‘Redmond’를 입력합니다.

이것이 한 세션입니다. 새 세션을 시작하려면 이 세션을 종료해야 합니다. 

1. 세션 시간 제한을 클릭합니다. 그러면 다음 세션으로 이동됩니다.
    - “세션 시간 제한” 단추는 디버깅 목적으로 제공됩니다.  실제 세션에서는 긴 일시 중지가 약 30분 동안 발생해야 합니다.  기본 세션 시간 제한 기간은 “한도”에 대한 도움말 페이지를 참조하세요.
1. ‘hi’를 입력합니다.
2. 시스템: ‘Can you tell Botty your location, jason?’
    - 시스템이 이름과 전화 번호를 기억했습니다.
2. 새 위치 ‘Seattle’을 입력합니다.
3. 시스템: ‘So, jason you are in Seattle’.
4. 테스트 완료를 클릭합니다.

로그 대화로 다시 전환하겠습니다. 각 로그 대화가 한 세션에 해당하므로 마지막 대화는 두 세션으로 분할되었습니다.  

![](../media/tutorial11_splitdialogs.PNG)

- 첫 번째 조작에서는 Botty만 설정되고 이름과 전화 번호는 설정되지 않았습니다.
- 두 번째 조작에서는 이름과 전화 번호를 보여 줍니다.

이제 세션이 기본적으로 처리되는 방법과 기본 동작을 재정의할 수 있는 방법을 살펴보았습니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [API 호출](./12-api-calls.md)
