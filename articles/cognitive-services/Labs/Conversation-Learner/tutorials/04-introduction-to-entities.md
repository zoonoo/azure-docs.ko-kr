---
title: Conversation Learner 모델에서 엔터티를 사용하는 방법 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner 모델에서 엔터티를 사용하는 방법을 알아봅니다.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 3d9e2498a23ad49eb014cb0f81c819f3f63eef5c
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66387817"
---
# <a name="introduction-to-entities"></a>엔터티 소개

이 자습서에서는 엔터티, 실격 엔터티, 필수 엔터티 및 Conversation Learner 내의 사용량을 소개합니다.

## <a name="video"></a>비디오

[![엔터티 자습서 미리 보기 소개](https://aka.ms/cl_Tutorial_v3_IntroEntities_Preview)](https://aka.ms/cl_Tutorial_v3_IntroEntities)

## <a name="requirements"></a>요구 사항

이 자습서를 수행하려면 일반 자습서 봇이 실행 중이어야 합니다.

    npm run tutorial-general

## <a name="details"></a>세부 정보

엔터티는 봇이 사용자 발언에서 추출하거나 사용자 지정 코드에서 할당하여 해당 작업을 수행해야 하는 정보의 부분을 캡처합니다. 엔터티 자체는 명시적으로 "필수" 또는 "실격"으로 분류하여 작업 가용성을 제한할 수도 있습니다.

- 작업을 사용할 수 있으려면 필수 엔터티가 봇의 메모리에 있어야 합니다.
- 작업을 사용할 수 있으려면 실격 엔터티가 모델의 메모리에 *없어야* 합니다.

이 자습서는 사용자 지정 엔터티에 중점을 둡니다. 미리 학습된 무효화할 수 있는 다중 값 엔터티 및 프로그래밍 방식 엔터티를 다른 자습서에서 소개합니다.

## <a name="steps"></a>단계

### <a name="create-the-model"></a>모델 만들기

1. Web UI에서 "새 모델"을 클릭합니다.
2. "이름" 필드에 "IntroToEntities"를 입력하고 Enter 키를 누릅니다.
3. "만들기" 단추를 클릭합니다.

### <a name="entity-creation"></a>엔터티 생성

1. 왼쪽 패널에서 “엔터티”를 클릭한 다음, “새 엔터티” 단추를 클릭합니다.
2. “엔터티 형식”에 대해 “사용자 지정 학습”을 선택합니다.
3. "엔터티 이름"에 "도시"를 입력합니다.
4. "만들기" 단추를 클릭합니다.

> [!NOTE]
> '사용자 지정 학습' 엔터티 형식은 다른 엔터티 유형과 달리 이 엔터티를 학습할 수 있다는 뜻입니다.

### <a name="create-the-actions"></a>작업 만들기

1. 왼쪽 패널에서 “작업”을 클릭한 다음, “새 작업” 단추를 클릭합니다.
2. "봇의 응답..." 필드에 "어떤 도시를 원하는지 모르겠습니다"를 입력합니다.
3. “실격 엔터티” 필드에 “도시”를 입력합니다.
4. "만들기" 단추를 클릭합니다.

> [!NOTE]
> "도시" 엔터티를 "실격 엔터티"에 추가하면 "도시" 엔터티가 봇의 메모리에서 정의되는 경우 봇의 고려 사항에서 이 작업의 자격을 취소할 수 있습니다.

이제 두 번째 작업을 만듭니다.

1. 왼쪽 패널에서 “작업”을 클릭한 다음, “새 작업” 단추를 클릭합니다.
2. "봇의 응답..." 필드에 "$city의 날씨는 아마도 맑습니다"를 입력합니다.
3. "만들기" 단추를 클릭합니다.

> [!NOTE]
> "도시" 엔터티가 응답을 참조하여 필수 엔터티에 자동으로 추가되었습니다.

![](../media/tutorial3_actions.PNG)

### <a name="train-the-model"></a>모델 학습

1. 왼쪽 패널에서 “학습 대화 상자”를 클릭한 다음, “새 학습 대화 상자” 단추를 클릭합니다.
2. "메시지를 입력하세요..."가 표시된 채팅 패널에 "안녕하세요"를 입력합니다.
    - 이렇게 하면 사용자의 쪽의 대화를 시뮬레이션합니다.
3. “작업 점수 매기기” 단추를 클릭합니다.
4. "어떤 도시를 원하는지 모르겠습니다"라는 응답을 선택합니다.
5. 사용자로서 "Seattle"이라고 응답합니다.
6. “작업 점수 매기기” 단추를 클릭합니다.
7. “$city의 날씨는 아마도 맑습니다”라는 응답을 선택합니다.
8. "저장" 단추를 클릭합니다.

![](../media/tutorial3_entities.PNG)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [예상 엔터티](./05-expected-entity.md)
