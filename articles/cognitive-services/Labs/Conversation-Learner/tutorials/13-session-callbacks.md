---
title: Conversation Learner 모델에서 세션 콜백을 사용하는 방법 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner 모델에서 세션 콜백을 사용하는 방법을 알아봅니다.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: eeed0718a1feb170dbbaa783ec0a840c7829c02e
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390007"
---
# <a name="how-to-use-session-callbacks-with-a-conversation-learner-model"></a>Conversation Learner 모델에서 세션 콜백을 사용하는 방법

이 자습서에서는 세션 처리 방법 및 Conversation Learner의 onSessionStart 및 onSessionEnd 콜백 세션을 소개합니다.

## <a name="video"></a>비디오

[![세션 콜백 자습서 미리 보기](https://aka.ms/cl_Tutorial_v3_SessionCallbacks_Preview)](https://aka.ms/cl_Tutorial_v3_SessionCallbacks)

## <a name="requirements"></a>요구 사항
이 자습서를 수행하려면 “tutorialSessionCallbacks” 봇이 실행 중이어야 합니다.

    npm run tutorial-session-callbacks

## <a name="details"></a>세부 정보
이 자습서에서는 세션의 개념, 기본적으로 세션을 처리하는 방법 및 해당 동작을 재정의하는 방법을 설명합니다.

Conversation Learner에서 세션은 봇과의 중단 없는 쌍방향 교류을 나타냅니다. 세션에는 다중 턴이 있을 수 있지만 30분을 초과하는 경우 비활성으로 인해 프로그래밍 방식으로 종료됩니다.  이 기본 세션 시간 제한 기간 변경 방법은 “제한”에 대한 도움말 페이지를 참조하세요.

이 오랜 비활성 기간으로 인해 봇은 새 세션을 만들고 순환 신경망을 초기 상태로 다시 설정할 수 있습니다. 기본적으로 모든 엔터티 값이 지워집니다. 아래에 표시된 것처럼 엔터티 값을 지우는 이 기본 동작을 변경할 수 있습니다.

### <a name="load-the-demo-model"></a>데모 모델 로드

웹 UI에서 "자습서 가져오기"를 클릭하고 "Tutorial-13-SessionCallbacks"이라는 모델을 선택합니다.

### <a name="code-for-the-callbacks"></a>콜백의 코드

이 모델의 두 콜백에 대한 샘플 코드는 `c:\<installedpath>\src\demos\tutorialSessionCallbacks.ts`에서 확인할 수 있습니다.

![](../media/tutorial11_code.PNG)

- OnSessionStartCallback: 이 메서드는 BotName 엔터티를 설정합니다.
- OnSessionEndCallback: 보존할 내용을 지정할 수 있습니다. 사용자 이름과 사용자 전화 번호를 제외한 모든 엔터티가 지워집니다.

각 콜백은 선택 사항입니다.

### <a name="actions"></a>작업

모델에는 4가지 작업이 정의되어 있습니다. 기존 작업은 "작업"에 대한 그리드 보기에 표시됨

![](../media/tutorial11_actions.PNG)

### <a name="creating-an-end-session-action-for-callback-invocation"></a>종료 세션 작업(예: 콜백 호출) 만들기

1. 왼쪽 패널에서 “작업”을 클릭한 다음, “새 작업” 단추를 클릭합니다.
2. "엔터티 형식"에 대한 "ENDSESSION"을 선택합니다.
3. "데이터..." 필드에 "완료" 입력
4. "만들기" 단추를 클릭합니다.

### <a name="edit-an-existing-action"></a>기존 작업 편집

1. 그리드 보기에서 "따라서 $UserName은 $UserLocation에 있습니다" 작업을 선택합니다.
2. "응답 대기" 확인란의 선택을 취소합니다.
3. "저장" 단추를 클릭합니다.

### <a name="chaining-actions"></a>작업 연결

1. 왼쪽 패널에서 “학습 대화 상자”를 클릭한 다음, “새 학습 대화 상자” 단추를 클릭합니다.
2. “메시지를 입력하세요...”가 표시된 채팅 패널에 “안녕하세요”를 입력합니다.
3. “작업 점수 매기기” 단추를 클릭합니다.
4. "안녕하세요, Botty입니다. 이름이 어떻게 됩니까?"라는 응답을 선택합니다.
5. "메시지를 입력하세요..."가 표시된 채팅 패널에 "Lars" 입력
6. "안녕하세요, Lars. 전화 번호가 어떻게 되나요?"라는 응답을 선택합니다.
7. "메시지를 입력하세요..."가 표시된 채팅 패널에 "555-555-5555" 입력
8. “작업 점수 매기기” 단추를 클릭합니다.
9. "Botty에게 위치를 알려줄 수 있나요, Lars?"라는 응답을 선택합니다.
10. "메시지를 입력하세요..."가 표시된 채팅 패널에 "Seattle" 입력
11. “작업 점수 매기기” 단추를 클릭합니다.
12. "Lars가 Seattle에 있군요"라는 응답 선택
13. "완료"라는 응답 선택
14. "저장" 단추를 클릭합니다.

### <a name="testing-the-model"></a>모델 테스트

1. 왼쪽 패널에서 "로그 대화 상자" 및 "새 로그 대화상자" 단추를 차례로 클릭합니다.
2. "메시지를 입력하세요..."가 표시된 채팅 패널에 "안녕하세요" 입력
3. "메시지를 입력하세요..."가 표시된 채팅 패널에 "Lars" 입력
4. "메시지를 입력하세요..."가 표시된 채팅 패널에 "555-555-5555" 입력
5. "메시지를 입력하세요..."가 표시된 채팅 패널에 "Seattle" 입력
    - 이 시점에서 위치를 제외한 모든 엔터티 값을 유지해야 합니다.
6. "메시지를 입력하세요..."가 표시된 채팅 패널에 "안녕하세요" 입력
7. "메시지를 입력하세요..."가 표시된 채팅 패널에 "Detroit" 입력
8. "세션 시간 제한" 단추를 클릭합니다.
    - 이 단추를 클릭하면 봇의 응답이 장기간 비활성 상태로 전환
9. "확인" 단추를 클릭합니다.
10. "테스트 완료" 단추를 클릭합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [API 호출](./14-api-calls.md)
