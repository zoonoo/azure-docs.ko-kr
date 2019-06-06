---
title: “Hello World” Conversation Learner 모델을 만드는 방법 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: “Hello World” Conversation Learner 모델을 만드는 방법을 알아봅니다.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: fe5d21fadef8f4452ba36259dbf89cefc78230de
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388054"
---
# <a name="how-to-create-a-hello-world-model-with-conversation-learner"></a>Conversation Learner에서 “Hello World” 모델을 만드는 방법

이 자습서에서는 작업 만들기, 봇의 대화형 학습 및 최종 사용자에게서 제공되는 기록된 대화 수정을 포함하여 Conversation Learner를 시작하는 방법을 보여줍니다.

## <a name="video"></a>비디오

[![Hello World 자습서 미리 보기](https://aka.ms/cl_Tutorial_v3_HelloWorld_Preview)](https://aka.ms/cl_tutorial_v3_helloworld)


## <a name="requirements"></a>요구 사항
아직 완료하지 않은 경우, 먼저 LUIS 작성 키를 사용하여 `.env` 파일 만들기를 비롯한 모든 설정 단계가 완료되었는지 확인합니다.  자세한 내용은 [빠른 시작](../quickstart.md)을 참조하세요.

이 자습서를 수행하려면 일반 자습서 봇이 실행 중이어야 합니다.

    npm run tutorial-general

## <a name="steps"></a>단계

Web UI의 홈페이지에서 시작합니다.

### <a name="create-the-model"></a>모델 만들기
1. "새 모델" 단추를 클릭합니다.
2. "이름" 필드에 "Hello World"를 입력합니다.
3. "만들기" 단추를 클릭합니다.

이제 만든 모델의 보기가 표시되어야 합니다.

### <a name="create-an-action"></a>작업 만들기
1. 왼쪽 패널에서 “작업”을 클릭한 다음, “새 작업” 단추를 클릭합니다.
    - 작업은 Conversation Learner가 사용자에게 반환하는 텍스트 메시지, API 호출 또는 카드일 수 있습니다.
2. "봇의 응답" 필드에 "안녕하세요"를 입력합니다.
    - 봇이 반환할 응답입니다.
3. "만들기" 단추를 클릭합니다.

봇이 수행할 수 있는(예: 텍스트 응답 반환) 첫 번째 작업을 만들었습니다.

### <a name="train-dialogs"></a>학습 대화 상자
이 학습 대화 상자에서는 사용자 발언에 응답하는 방법에 대해 모델을 학습합니다.

#### <a name="first-training-dialog"></a>첫 번째 학습 대화 상자

1. 왼쪽 패널에서 “학습 대화 상자”를 클릭한 다음, “새 학습 대화 상자” 단추를 클릭합니다.
2. "안녕하세요"를 입력하고 Enter 키를 누릅니다.
    - 사용자가 대화의 시작 부분에서 얘기할 수 있는 예제입니다.
3. “작업 점수 매기기” 단추를 클릭합니다.
4. "안녕하세요"를 선택합니다.
    - 이 예제 대화 상자에서 하나의 전체 턴을 완료했습니다. 
5. 사용자 응답에 "Goodbye"를 입력합니다.
6. “작업 점수 매기기” 단추를 클릭합니다.
7. "+ 작업" 단추를 클릭합니다.
8. "Goodbye!" 입력 "봇의 응답..." 필드에서 "만들기" 단추를 클릭합니다.
    - 봇이 방금 만든 작업으로 응답했는지 확인합니다.
9. "저장" 단추를 클릭합니다. 
    - 그러면 이 학습 대화 상자가 종료되고 저장됩니다.

이제 단일 엔터티 및 두 개의 작업과 함께 모델의 학습 대화 상자가 하나 있습니다.

#### <a name="second-training-dialog"></a>두 번째 학습 대화 상자
학습을 하나 더 수행하고 봇이 어떻게 응답하는지 살펴보겠습니다.

1. "새 학습 대화 상자" 단추를 클릭합니다.
2. "안녕하세요" 입력
    - 첫 번째 대화 상자와 유사하므로, 봇에서 좋은 점수를 얻을 것으로 예상합니다.
3. “작업 점수 매기기” 단추를 클릭합니다.
    - 그래도 위치와 점수가 충분히 정확하지 않아 추가 학습이 필요할 수 있습니다.
4. "안녕하세요"를 선택합니다.
5. 사용자 응답에 "bye"를 입력합니다.
6. “작업 점수 매기기” 단추를 클릭합니다.
7. "Goodbye!"를 선택합니다.
8. "저장" 단추를 클릭합니다.

### <a name="log-dialogs"></a>로그 대화 상자
이 대화 상자에서는 실제 사용자가 봇과 수행했던 대화를 테스트, 보기 및 수정합니다.

#### <a name="test-the-model-as-an-end-user"></a>최종 사용자로 모델 테스트
1. 왼쪽 패널에서 "로그 대화 상자" 및 "새 로그 대화상자" 단추를 차례로 클릭합니다.
2. "안녕하세요"를 입력합니다.
3. 잠시 기다리면 봇이 자동으로 "안녕하세요"라고 응답해야 합니다.
4. 'byebye'를 입력합니다.
5. 잠시 기다리면 다시 봇이 자동으로 "안녕하세요"라고 응답해야 합니다.
6. "테스트 완료" 단추를 클릭합니다.

#### <a name="view-and-correct-a-user-conversation"></a>사용자 대화 보기 및 수정
로그 대화 상자를 사용하여 사용자가 봇과 수행한 대화 목록을 확인할 수 있습니다. 대화 상자를 편집하여 봇의 응답을 수정하고 상호 작용을 학습 대화로 저장할 수 있습니다. 이러한 작업을 하려면 다음을 수행합니다.
1. 그리드에서 대화 로그를 클릭합니다.
2. 예를 들어 마지막 봇 작업을 클릭합니다. “안녕하세요”.
3. 봇을 수정하려면 "Goodbye!"를 선택합니다.
4. "학습 대화로 저장" 단추를 클릭합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [학습 소개](./02-intro-to-training.md)
