---
title: Conversation Learner 작업의 "예상 엔터티" 속성을 사용하는 방법 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner 모델의 "예상 엔터티" 속성을 사용하는 방법을 알아봅니다.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 48a8abd401ff7191da4709c726042b566d140b78
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66387812"
---
# <a name="how-to-use-the-expected-entity-property-of-actions"></a>작업의 "예상 엔터티" 속성을 사용하는 방법

이 자습서에서는 작업의 "예상 엔터티" 속성을 설명합니다.

## <a name="video"></a>비디오

[![예상 엔터티 자습서 미리 보기](https://aka.ms/cl_Tutorial_v3_ExpectedEntity_Preview)](https://aka.ms/cl_Tutorial_v3_ExpectedEntity)

## <a name="requirements"></a>요구 사항
이 자습서를 수행하려면 일반 자습서 봇이 실행 중이어야 합니다.

    npm run tutorial-general

## <a name="details"></a>세부 정보
"예상 엔터티" 속성을 사용하여 이 작업에 대한 사용자 응답을 엔터티로 저장합니다.

엔터티를 작업의 "예상 엔터티" 속성에 추가할 때 시스템에서는 다음을 수행합니다.

1. 기계 학습 기반 엔터티 추출 모델을 사용하여 엔터티와 일치하도록 시도하여 시작
2. 엔터티가 없는 경우 추론에 따라 전체 사용자 발언을 $entity에 할당합니다.
3. `EntityDetectionCallback`을 호출하고 작업 선택 영역을 계속 진행합니다.

## <a name="steps"></a>단계

### <a name="create-the-model"></a>모델 만들기

1. Web UI에서 "새 모델"을 클릭합니다.
2. "이름" 필드에 "ExpectedEntities"를 입력하고 Enter 키를 누릅니다.
3. "만들기" 단추를 클릭합니다.

### <a name="entity-creation"></a>엔터티 생성

1. 왼쪽 패널에서 “엔터티”를 클릭한 다음, “새 엔터티” 단추를 클릭합니다.
2. “엔터티 형식”에 대해 “사용자 지정 학습”을 선택합니다.
3. "엔터티 이름"에 "이름"을 입력합니다.
4. "만들기" 단추를 클릭합니다.

> [!NOTE]
> '사용자 지정 학습' 엔터티 형식은 다른 엔터티 유형과 달리 이 엔터티를 학습할 수 있다는 뜻입니다.

![](../media/tutorial4_entities.PNG)

### <a name="create-the-first-action"></a>첫 번째 작업 만들기

1. 왼쪽 패널에서 “작업”을 클릭한 다음, “새 작업” 단추를 클릭합니다.
2. "봇 응답..." 필드에 "이름이 어떻게 되나요?"를 입력합니다.
3. "예상 엔터티" 필드에 "이름"을 입력합니다.
4. "만들기" 단추를 클릭합니다.

> [!NOTE]
> 이 작업을 선택하면 사용자의 응답에서 검색되고 추출된 엔터티가 "이름" 엔터티에 저장됩니다. 엔터티가 검색되지 않으면 전체 응답이 이 엔터티에 저장됩니다.

### <a name="create-the-second-action"></a>두 번째 작업 만들기

1. 왼쪽 패널에서 “작업”을 클릭한 다음, “새 작업” 단추를 클릭합니다.
2. "봇의 응답..." 필드에 "Hi $name!"을 입력합니다.
3. "만들기" 단추를 클릭합니다.

> [!NOTE]
> "이름" 엔터티를 응답에서 참조하여 "필수 엔터티"로 자동으로 추가했습니다.

이제 두 가지 작업이 있습니다.

![](../media/tutorial4_actions.PNG)

### <a name="train-the-model"></a>모델 학습

1. 왼쪽 패널에서 “학습 대화 상자”를 클릭한 다음, “새 학습 대화 상자” 단추를 클릭합니다.
2. "메시지를 입력하세요..."가 표시된 채팅 패널에 "안녕하세요"를 입력합니다.
    - 이렇게 하면 사용자의 쪽의 대화를 시뮬레이션합니다.
3. “작업 점수 매기기” 단추를 클릭합니다.
4. "이름이 어떻게 되나요?"란 응답을 선택합니다.
    - "Hi $name!" 응답은 이 응답이 이제 모델의 메모리에서 "이름" 엔터티를 정의할 것을 요구하는 경우 선택할 수 없습니다.
5. "메시지를 입력하세요..."가 표시된 채팅 패널에 "Frank"를 입력합니다.
    - "Frank"가 전에 설정한 추론에 따라 엔터티로 강조 표시되어 해당 응답을 엔터티로 저장합니다.
6. “작업 점수 매기기” 단추를 클릭합니다.
    - "Hello $name" 작업을 작업으로 선택할 수 있도록 이제 "이름" 엔터티가 모델의 메모리에서 "Frank"로 정의됩니다.
7. "Hi $name!" 응답을 선택합니다.
8. "저장" 단추를 클릭합니다.

대체 입력을 추가하면 모델을 더 학습합니다.

1. "대체 입력 추가..." 필드에 "Jose입니다"를 입력합니다.
    - 모델이 이름을 엔터티로 인식하지 않으므로 전체 텍스트 블록을 엔터티의 값으로 선택합니다.
2. "Jose입니다" 구문을 클릭한 다음, 휴지통 아이콘을 클릭합니다.
3. "Jose"를 클릭한 다음, 엔터티 목록에서 "이름"을 클릭합니다.
4. 작업에 점수 지정을 클릭합니다.
5. "안녕 Frank!"란 응답을 선택합니다.
6. "저장" 단추를 클릭합니다.

![](../media/tutorial4_dialogs.PNG)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [무효화할 수 엔터티](./06-negatable-entities.md)
