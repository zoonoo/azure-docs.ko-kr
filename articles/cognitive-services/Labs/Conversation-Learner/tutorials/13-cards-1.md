---
title: Conversation Learner 모델에서 카드를 사용하는 방법, 1부 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner 모델에서 카드를 사용하는 방법을 알아봅니다.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: da261beeec4f02dfa7c7cf9071e51dc17cf5c7cd
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51254386"
---
# <a name="how-to-use-cards-part-1-of-2"></a>카드 사용 방법(2부 중 1부)

이 자습서는 봇에서 간단한 카드를 추가하고 사용하는 방법을 보여 줍니다.

> [!NOTE]
> 현재, Conversation Learner는 봇이 시작된 디렉터리에 있는 “cards” 디렉터리에 카드 정의 파일이 있을 것으로 예상합니다. 향후에 구성 가능하게 구현될 예정입니다.

## <a name="video"></a>비디오

[![자습서 13 미리 보기](https://aka.ms/cl-tutorial-13-preview)](https://aka.ms/blis-tutorial-13)

## <a name="requirements"></a>요구 사항
이 자습서를 수행하려면 일반 자습서 봇이 실행 중이어야 합니다.

    npm run tutorial-general

## <a name="details"></a>세부 정보

카드는 사용자가 대화에서 옵션을 선택할 수 있도록 하는 UI 요소입니다. 

### <a name="open-the-demo"></a>데모 열기

Web UI의 모델 목록에서 Tutorial-13-Cards-1을 클릭합니다. 

### <a name="the-card"></a>카드

카드 정의는 다음 위치에 있습니다. C:\<installedpath\>\src\cards\prompt.json

시스템은 이 카드 디렉터리에서 카드 정의를 찾을 것으로 예상합니다.

![](../media/tutorial13_prompt.PNG)

> [!NOTE]
> 텍스트 필드에서 본문 형식 `TextBlock` 및 `{{question}}` 자리 표시자를 확인합니다.
> 두 개의 제출 단추와 각 단추에 대해 제출되는 텍스트가 있습니다.

### <a name="actions"></a>작업

세 가지 작업을 만들었습니다. 아래와 같이 첫 번째 작업은 카드입니다.

![](../media/tutorial13_actions.PNG)

카드 작업 형식이 어떻게 생성되었는지 살펴보겠습니다.

![](../media/tutorial13_cardaction.PNG)

> [!NOTE]
> 질문 입력과 단추 1, 2를 확인합니다. 카드에서 질문과 해당 답변을 입력하는 템플릿 참조입니다. 엔터티 또는 텍스트와 엔터티 혼합을 참조하고 사용할 수도 있습니다.

눈 아이콘은 카드 모양을 보여 줍니다.

### <a name="train-dialog"></a>학습 대화

학습 대화를 살펴보겠습니다.

1. Train Dialogs(학습 대화), New Train Dialog(새 학습 대화)를 차례로 클릭합니다.
1. ‘hi’를 입력합니다.
2. Score Action(작업에 점수 지정)을 클릭합니다.
3. ‘프롬프트 왼쪽 또는 오른쪽 이동’을 클릭하여 선택합니다.
    - ‘왼쪽’ 또는 ‘오른쪽’ 클릭은 사용자가 각각 ‘left’ 또는 ‘right’를 입력하는 것과 같습니다. 
4. Score Actions(작업에 점수 지정)를 클릭합니다.
4. ‘왼쪽’을 클릭하여 선택합니다. 비대기 작업입니다.
6. ‘프롬프트 왼쪽 또는 오른쪽 이동’을 클릭하여 선택합니다.
4. ‘오른쪽’을 클릭합니다.
5. Score Actions(작업에 점수 지정)를 클릭합니다.
3. ‘오른쪽’을 클릭하여 선택합니다.
6. ‘프롬프트 왼쪽 또는 오른쪽 이동’을 클릭하여 선택합니다.
4. 테스트 완료를 클릭합니다.

지금까지 카드가 어떻게 작동하는지 살펴보았습니다. 카드는 카드 디렉터리에 JSON 템플릿으로 정의됩니다. 템플릿은 문자열, 엔터티 또는 둘 다를 사용하여 채울 수 있는 UI에 표시됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [카드 2부](./14-cards-2.md)
