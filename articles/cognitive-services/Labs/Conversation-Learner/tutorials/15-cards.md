---
title: Conversation Learner 모델에서 카드를 사용하는 방법, 1부 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner 모델에서 카드를 사용하는 방법을 알아봅니다.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: d0b87597c506aca5c3b4f6f3815f58656203ac3c
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389514"
---
# <a name="how-to-use-cards-part-1-of-2"></a>카드 사용 방법(2부 중 1부)

이 자습서는 봇에서 간단한 카드를 추가하고 사용하는 방법을 보여 줍니다.

> [!NOTE]
> Conversation Learner는 현재 봇이 시작된 디렉터리에 있는 “카드” 디렉터리에 카드 정의 파일이 있을 것으로 예상합니다.

## <a name="video"></a>비디오

[![카드 자습서 미리 보기](https://aka.ms/cl_Tutorial_v3_Cards_Preview)](https://aka.ms/cl_Tutorial_v3_Cards)

## <a name="requirements"></a>요구 사항
이 자습서를 수행하려면 일반 자습서 봇이 실행 중이어야 합니다.

    npm run tutorial-general

## <a name="details"></a>세부 정보

카드는 사용자가 대화에서 옵션을 선택할 수 있도록 하는 UI 요소입니다. 

### <a name="open-the-demo"></a>데모 열기

웹 UI에서 "자습서 가져오기"를 클릭하고 "Tutorial-15-Cards"라는 모델을 선택합니다.

### <a name="the-card"></a>카드

카드 정의는 다음 위치에 있습니다. C:\<installedpath\>\src\cards\prompt.json.

시스템은 이 "카드" 디렉터리에서 카드 정의를 찾을 것으로 예상합니다.

![](../media/tutorial13_prompt.PNG)

> [!NOTE]
> 텍스트 필드에서 본문 형식 "TextBlock" 및 "{{question}}" 자리 표시자를 확인합니다.
> 두 개의 제출 단추와 각 단추에 대해 제출되는 텍스트가 있습니다.

### <a name="actions"></a>작업

세 가지 작업을 만들었습니다. 아래와 같이 첫 번째 작업은 카드입니다.

![](../media/tutorial13_actions.PNG)

카드 작업 형식이 어떻게 생성되었는지 살펴보겠습니다.

![](../media/tutorial13_cardaction.PNG)

> [!NOTE]
> 카드에는 세 개의 서로 다른 매개 변수인 질문 입력, 단추 1 및 단추 2가 포함됩니다. 이러한 요소는 카드에서 질문과 해당 답변을 입력하는 템플릿 참조입니다. 엔터티 또는 텍스트와 엔터티 혼합을 참조하고 사용할 수도 있습니다.

눈 아이콘은 카드 모양을 보여 줍니다.

### <a name="practicing-creating-card-actions"></a>카드 만들기 작업 실습

1. 왼쪽 패널에서 “작업”을 클릭한 다음, “새 작업” 단추를 클릭합니다.
2. "작업 유형"에 "카드"를 선택합니다.
3. "템플릿" 목록에서 "프롬프트"를 선택합니다.
4. "질문" 필드에 "왼쪽 또는 오른쪽으로 이동" 입력
5. "button1" 필드에 "왼쪽" 입력
6. "button2" 필드에 "오른쪽" 입력
7. "취소" 단추를 클릭합니다.

### <a name="train-dialog-using-an-adaptive-card"></a>적응형 카드를 사용하여 대화 학습

1. 왼쪽 패널에서 “학습 대화 상자”를 클릭한 다음, “새 학습 대화 상자” 단추를 클릭합니다.
2. "메시지를 입력하세요..."가 표시된 채팅 패널에 "안녕하세요" 입력
3. “작업 점수 매기기” 단추를 클릭합니다.
4. "프롬프트: 질문: 왼쪽 또는 오른쪽으로 이동?"이란 응답을 선택합니다.
    - 카드를 미리 보기하려면 눈 아이콘을 사용할 수 있습니다.
5. 채팅 패널의 렌더링된 프롬프트에서 "왼쪽" 단추를 클릭합니다.
6. “작업 점수 매기기” 단추를 클릭합니다.
7. "왼쪽"이라는 응답 선택
8. "저장" 단추를 클릭합니다.
9. "프롬프트: 질문: 왼쪽 또는 오른쪽으로 이동?"이란 응답을 선택합니다.
10. 채팅 패널의 렌더링된 프롬프트에서 "오른쪽" 단추를 클릭합니다.
11. “작업 점수 매기기” 단추를 클릭합니다.
12. "오른쪽"이라는 응답 선택

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [하이브리드 봇](./16-hybrid-bots.md)
