---
title: 다른 봇 빌드 기술과 함께 Conversation Learner를 사용하는 방법 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: 다른 봇 빌드 기술과 함께 Conversation Learner를 사용하는 방법을 알아봅니다.
services: cognitive-services
author: mattm
manager: larsliden
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 07/13/2018
ms.author: nitinme
ms.openlocfilehash: d6af927e395532e43c7cc51c39665e2e42ac6781
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389972"
---
# <a name="how-to-use-conversation-learner-with-other-bot-building-technologies"></a>다른 봇 빌드 기술과 함께 Conversation Learner를 사용하는 방법

이 자습서에서는 다른 봇 빌드 기술과 함께 Conversation Learner를 사용하는 방법과 이러한 기술 간에 메모리(또는 상태)를 공유하는 방법을 알아봅니다. 

## <a name="video"></a>비디오

[![하이브리드 봇 자습서 미리 보기](https://aka.ms/cl_Tutorial_v3_Hybrid_Applications_Preview)](https://aka.ms/cl_Tutorial_v3_Hybrid_Applications)

## <a name="requirements"></a>요구 사항
이 자습서를 수행하려면 로그 대화 Web UI가 아니라 봇 에뮬레이터를 사용하여 로그 대화를 만들어야 합니다. Bot Framework Emulator에 대한 자세한 내용은 [여기](https://docs.microsoft.com/azure/bot-service/bot-service-debug-emulator?view=azure-bot-service-4.0)를 참조하세요. 

이 자습서를 수행하려면 하이브리드 자습서 봇이 실행 중이어야 합니다.

    npm run tutorial-hybrid

## <a name="details"></a>세부 정보

Conversation Learner를 제어하는 동안 Conversation Learner 세션에 관련된 모든 상태가 Conversation Learner 메모리 관리자에 저장되어야 합니다. 기계 학습이 해당 상태를 사용하여 대화를 구동하는 방법을 결정하기 때문에 이러한 과정이 필요합니다. 외부 상태는 세션이 시작될 때 호출되는 OnSessionStartCallback을 통해 Conversation Learner로 전달될 수 있습니다. 내부 상태는 세션이 종료될 때 OnSessionEndCallback을 통해 반환할 수 있습니다.

Conversation Learner를 일부 초기 상태 및 반환 값을 가져오는 함수 호출로 생각할 수 있습니다.

이 예제에서는 다음 두 가지 시스템을 사용하여 하이브리드 봇을 만듭니다.
1. Conversation Learner 모델 <br/>
    Conversation Learner 모델을 사용하여 현재 세션을 기준으로 봇의 다음 작업을 결정합니다. 봇의 이 부분은 초기 상태의 한 부분 `isOpen`(저장소가 열려 있는지 여부를 나타냄)을 가져오고, 상태의 다른 부분 `purchaseItem`(사용자가 구매한 항목의 이름)을 반환합니다.

2. 텍스트 일치 <br />
    특정 문자열의 수신 텍스트를 간단히 확인하고 응답합니다. 봇의 이 부분은 봇의 다른 스토리지 메커니즘을 관리하며 CL 세션을 시작하는 일을 담당합니다. 특히, 세 개의 변수, `usingConversationLearner`, `storeIsOpen` 및 `purchaseItem`을 관리합니다.

먼저 이 데모에 사용된 모델을 살펴보겠습니다.

### <a name="open-the-demo"></a>데모 열기

웹 UI에서 "자습서 가져오기"를 클릭하고 "Tutorial-16-HybridBot"이라는 모델을 선택합니다.

## <a name="entities"></a>엔터티

엔터티 페이지를 열면 두 개의 엔터티, `isOpen` 및 `purchaseItem`을 확인할 수 있습니다.

이러한 엔터티가 사용되는 방식을 이해하려면 파일 `C:\<installedpath>\src\demos\tutorialHybrid.ts`를 열어 콜백을 확인합니다.

`OnSessionStartCallback`의 코드는 BotBuilder 대화 저장소의 `storeIsOpen` 값을 Conversation Learner에서 사용할 수 있도록 `isOpen` 엔터티의 값으로 복사합니다. 다음 코드를 확인합니다.

![](../media/tutorial17_sessionstart.PNG)

마찬가지로 `OnSessionEndCallback`의 코드(단지 시간 초과 때문이 아니라 학습된 활동 때문에 세션이 종료된 경우)는 엔터티 `purchaseItem`의 값을 BotBuilder 저장소 `purchaseItem`으로 복사합니다. 다음 코드를 확인합니다.

![](../media/tutorial17_sessionend.PNG)

이제 작업을 살펴보겠습니다.

## <a name="actions"></a>작업

이 모델에는 4개의 작업이 있습니다.

작업의 의도된 규칙은 다음과 같습니다.

- `isOpen` 엔터티가 설정되면 봇은 "What would you like to buy?"라고 묻고 해당 내용을 `puchaseItem` 슬롯에 저장합니다.
- `isOpen`을 설정하지 않으면 봇은 "I’m sorry we’re closed"라고 말합니다.
- 다른 두 작업은 `END_SESSION` 형식을 갖습니다.
- END_SESSION 작업은 대화가 완료되었음을 Conversation Learner에 나타냅니다.

### <a name="overall-bot-logic"></a>전체 봇 논리

먼저, 봇의 상태 `usingConversationLearner` 플래그가 설정된 경우 Conversation Learner에 제어권을 전달합니다. 그렇지 않은 경우 다른 대상에 제어권을 전달합니다.  이 예제에서는 간단한 텍스트 일치를 보여 주지만, 이러한 텍스트 일치가 LUIS, QnA maker 및 Conversation Learner의 다른 인스턴스를 비롯한 기타 봇 기술일 수 있습니다.

사용자가 저장소를 열고 닫는 방법이 필요하므로, "open store" 및 "close store"와 문자열 비교를 수행하고 "storeIsOpen" 플래그를 설정합니다.

다음으로, Conversation Learner 모델로 제어권이 전달되도록 트리거하는 방법이 필요합니다. "shop" 문자열과 일치시킬 경우 다음을 수행합니다.
- 봇 메모리에서 `usingConversationLearner` 플래그를 설정합니다.
- Conversation Learner 모델에서 "StartSession" 메서드를 호출합니다.  이렇게 하면 "onSessionStartCallback"이 트리거되거 `isOpen` 엔터티 값이 초기화됩니다.

아래 내용을 참조하세요.

![](../media/tutorial17_useConversationLearner.PNG)

또한 마지막 구매 항목을 표시하는 "history"에 대해 텍스트 일치를 수행합니다.
마지막으로 다른 내용이 입력되면 사용 가능한 사용자 명령을 표시합니다.

## <a name="train-dialog"></a>학습 대화

이 자습서에서는 모델이 이미 학습된 상태입니다.  전체 봇을 테스트하여 세션 시작 및 세션 종료 콜백의 효과를 실제로 확인할 것입니다.

## <a name="testing-the-bot"></a>봇 테스트

단일 Conversation Learner 모델 봇과 달리, 이 봇은 Conversation Learner 모델에서 처리되는 결과만 표시되므로 Conversation Learner UI에서 테스트할 수 없게 됩니다.

### <a name="install-the-bot-framework-emulator"></a>Bot Framework 에뮬레이터 설치

- [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator)로 이동합니다.
- 에뮬레이터를 다운로드 및 설치합니다.

### <a name="configure-the-emulator"></a>에뮬레이터 구성

- 에뮬레이터를 열고 URL이 봇이 실행되는 동일한 포트를 대상으로 하는지 확인합니다. URL은 `http://localhost:3978/api/messages`와 비슷할 수 있습니다.

### <a name="test"></a>테스트 

#### <a name="scenario-1-store-is-closed"></a>시나리오 1: 저장소가 닫힘
1. 'shop'을 입력합니다. 텍스트 일치에 의해 처리되며, Conversation Learner 모델에 제어권이 넘어갑니다.
2. '안녕하세요'를 입력합니다.  `isOpen` 값이 설정되지 않았으므로 봇은 "I’m sorry we’re closed"라고 말하고 세션을 닫습니다.

#### <a name="scenario-2-store-is-open"></a>시나리오 2: 저장소가 열림
1. 'open store'를 입력합니다.  이렇게 하면 `isOpen`이 true로 설정됩니다.
1. 'shop'을 입력합니다.
1. '안녕하세요'를 입력합니다.  `isOpen` 값이 true로 설정되어 있으므로 봇은 "What would you like to buy?"라고 말합니다.
1. 'chair'를 입력합니다. 'chair'는 CL 메모리에 엔터티 `purchaseItem`으로 저장됩니다. 이 값을 대화 저장소로 복사하는 세션 종료 콜백이 호출됩니다.
1. 'history'를 입력합니다.  이것이 마지막 `purchaseItem`이므로 봇은 'You bought chair'라고 말합니다.

## <a name="conclusion"></a>결론

위에서 알아본 것을 토대로 Conversation Learner를 다른 봇 빌드 기술과 결합할 수 있어야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [분기 및 실행 취소](./17-branch-undo.md)
