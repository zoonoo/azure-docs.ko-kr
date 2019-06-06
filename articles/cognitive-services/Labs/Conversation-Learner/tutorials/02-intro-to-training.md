---
title: Conversation Learner 모델 학습 방법 소개 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner를 통해 이전 학습의 편집 및 분기를 포함하여 모델을 학습하는 방법을 알아봅니다.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 0bf5b71a4b0f51a586febbdaeaf8caba03c5b25a
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66387926"
---
# <a name="introduction-to-training"></a>학습 소개

이 자습서에서는 모델을 학습하고, 이전 학습을 기반으로 한 새 학습을 분기하고, 봇 응답을 변경하기 위해 편집하는 기본 사항을 보여줍니다.

## <a name="video"></a>비디오

[![학습 자습서 미리 보기 소개](https://aka.ms/cl_Tutorial_v3_IntroTraining_Preview)](https://aka.ms/cl_Tutorial_v3_IntroTraining)

## <a name="requirements"></a>요구 사항
이 자습서를 수행하려면 일반 자습서 봇이 실행 중이어야 합니다.

    npm run tutorial-general

## <a name="details"></a>세부 정보

- 작업: 사용자 입력에 대한 봇 응답입니다.
- 학습: 봇이 사용자 입력에 응답하도록 가르치는 방법입니다.
- 분기: 원본과 동일하게 시작하지만 대화를 다른 방향으로 이끄는 새 학습 대화 상자를 만들 목적으로 저장된 학습 대화 상자 내에서 사용자 입력을 수정합니다.

## <a name="steps"></a>단계

### <a name="create-a-new-model"></a>새 모델 만들기

1. Web UI에서 새 모델을 클릭합니다.
2. "이름"으로 "Inspire Bot"을 입력합니다. 그런 다음, 만들기를 클릭합니다.

### <a name="create-an-action"></a>작업 만들기

1. 왼쪽 패널에서 “작업”을 클릭한 다음, “새 작업” 단추를 클릭합니다.
2. "봇의 응답" 필드에 "안녕하세요! 지금 영감을 받으시겠습니까?"를 입력합니다.
    - 다른 모든 필드와 확인란을 해당 기본 설정 그대로 둡니다.
3. 만들기를 클릭합니다.

### <a name="first-training-and-creating-another-action-while-training"></a>먼저 학습 동안 다른 작업 학습 및 만들기

1. 왼쪽 패널에서 “학습 대화 상자”를 클릭한 다음, “새 학습 대화 상자” 단추를 클릭합니다.
2. "메시지를 입력하세요..."가 표시된 채팅 패널에 "안녕하세요"를 입력합니다. 
    - 이렇게 하면 사용자의 쪽의 대화를 시뮬레이션합니다.
3. "작업 점수 매기기"를 클릭합니다.
4. "안녕하세요! 지금 영감을 받으시겠습니까?"라는 응답을 선택합니다.
5. 사용자로서 "예"라고 응답합니다.
6. "작업 점수 매기기"를 클릭합니다.
7. "+ 작업" 단추를 클릭합니다. 
    - 이렇게 하면 익숙한 "작업 만들기" 대화 상자로 이동합니다.
8. 봇의 응답에 "멋지십니다!"를 입력합니다.
9. 만들기를 클릭합니다.
10. 봇이 즉시 응답하는지 확인합니다.
11. "저장" 단추를 클릭합니다.

### <a name="branch-a-second-training-off-of-the-first-training"></a>첫 번째 학습에서 두 번째 학습 분기
1. 첫 번째 학습을 요약하는 그리드 행을 클릭합니다. 
    - 그러면 기존 학습을 확인하고 편집할 수 있습니다.
2. "예"라는 사용자 응답을 클릭합니다. 
    - 그러면 편집 컨트롤이 노출됩니다.
3. 분기 아이콘을 클릭합니다. 
    - 그러면 새 대화에 다른 사용자 입력을 요청하는 메시지가 표시됩니다.
4. "아니요"를 입력한 다음, Enter 키를 누르거나 "만들기" 단추를 클릭합니다. 
    - 이 시점에서 학습 대화 상자의 새 인스턴스가 포함되지만 원본 인스턴스는 그대로 유지됩니다.
5. "작업 점수 매기기"를 클릭합니다.
6. 방금 표시된 봇의 잘못된 응답을 클릭합니다.
7. "+ 작업" 단추를 클릭하여 
    - 봇이 응답할 새 작업을 만들 수 있습니다.
8. 봇의 응답에 "걱정할 필요가 없습니다. 좋은 하루 보내세요!"를 입력합니다.
9. 만들기 클릭
10. 봇이 즉시 응답하는지 확인합니다.
11. "저장" 단추를 클릭합니다.

### <a name="test-the-trainings"></a>학습 테스트
1. 왼쪽 패널에서 "로그 대화 상자" 및 "새 로그 대화상자"를 차례로 클릭합니다.
2. 메시지에 "안녕하세요"를 입력합니다. 
3. 봇이 학습한 방식에 따라 자동으로 응답하는지 확인합니다.
4. 사용자 응답에 "예"를 입력합니다.
5. 봇의 응답을 확인하면 첫 번째 학습이 제대로 작동하는지 표시됩니다.
6. "세션 시간 제한" 단추를 클릭합니다. 그러면 Conversation Learner에게 방금 발생한 대화형 턴을 무시하고 다시 시작하도록 지시합니다.
7. 메시지에 "안녕하세요"를 입력합니다. 
8. 봇이 학습한 방식에 따라 자동으로 응답하는지 확인합니다.
9. 사용자 응답에 "아니요"를 입력합니다.
10. 봇의 응답을 확인하면 두 번째 학습이 제대로 작동하는지 표시됩니다.
11. "테스트 완료" 단추를 클릭합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [대기 및 비대기 작업](./03-wait-vs-nonwait-actions.md)
