---
title: 데모 Conversation Learner 모델, 암호 재설정 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: 데모 Conversation Learner 모델을 만드는 방법을 알아봅니다.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: f633dd375d690a1c3e66a2a6e02ae69665dbe960
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39170636"
---
# <a name="demo-password-reset"></a>데모: 암호 재설정
이 데모에서는 암호 재설정에 도움이 되는 간단한 기술 지원 봇을 보여 줍니다. 

Conversation Learner가 도메인 외부 클래스를 포함하여 특수한 대화 흐름, 다중 턴 시퀀스를 어떻게 학습할 수 있는지를 보여 줍니다. 이 데모에서는 코드 또는 엔터티를 사용하지 않습니다.

## <a name="video"></a>비디오

[![데모 암호 미리 보기](http://aka.ms/cl-demo-password-preview)](http://aka.ms/blis-demo-password)

## <a name="requirements"></a>요구 사항
이 자습서를 수행하려면 암호 재설정 봇이 실행 중이어야 합니다.

    npm run demo-password

### <a name="open-the-demo"></a>데모 열기

Web UI의 모델 목록에서 자습서 데모 암호 재설정을 클릭합니다. 

### <a name="actions"></a>작업

해결 방법을 포함하여 사용자가 암호와 관련된 도움을 요청하는 작업 집합을 만들었습니다.

![](../media/tutorial_pw_reset_actions.PNG)

### <a name="training-dialogs"></a>학습 대화

많은 학습 대화가 있습니다. 도메인 외부 클래스에 대한 데모도 있습니다. 예를 들어 ‘자가용 길 찾기’와 같은 사용자 요청은 도메인 외부입니다. 봇에 몇 가지 도메인 외부 요청의 예가 제공되었으며 ‘I can't help with that.’으로 응답할 수 있습니다.

![](../media/tutorial_pw_reset_entities.PNG)

예를 들어 학습 세션을 시도해 보겠습니다.

1. Train Dialogs(학습 대화), New Train Dialog(새 학습 대화)를 차례로 클릭합니다.
1. ‘I lost my password’를 입력합니다.
2. Score Action(작업에 점수 지정)을 클릭합니다.
3. ‘Is that for your local account or Microsoft account?’를 클릭하여 선택합니다.
4. ‘Local account’를 입력합니다.
5. Score Actions(작업에 점수 지정)를 클릭합니다.
3. ‘Which version of Windows do you have?’를 클릭하여 선택합니다.
4. ‘Windows 8’을 입력합니다.
5. Score Actions(작업에 점수 지정)를 클릭합니다.
6. ‘SOLUTION: how to reset password on Windows 8.’(해결 방법: Windows 8에서 암호를 재설정하는 방법)을 선택합니다.
4. 학습 완료를 클릭합니다.

봇이 도메인 외부 클래스를 학습할 수 있는 방법을 알아보겠습니다.

1. Train Dialogs(학습 대화), New Train Dialog(새 학습 대화)를 차례로 클릭합니다.
1. ‘web search’를 입력합니다.
    - 도메인 외부 클래스의 예입니다. 
2. Score Action(작업에 점수 지정)을 클릭합니다.
3. ‘Sorry, I can't help with that.’을 클릭하여 선택합니다.
    - 이 옵션의 점수는 현재 낮습니다. 그러나 학습을 좀 더 수행하면 점수가 높아집니다.
4. 학습 완료를 클릭합니다.

지금까지 기본 기술 지원 데모를 만드는 방법과 해결 방법을 제공하기 위해 학습하고 샘플 외부 쿼리를 처리하는 방법도 살펴보았습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [데모 - 피자 주문](./demo-pizza-order.md)
