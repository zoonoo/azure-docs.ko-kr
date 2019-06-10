---
title: Conversation Learner 모델에서 엔터티 검색 콜백을 사용하는 방법 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner 모델에서 엔터티 검색 콜백을 사용하는 방법을 알아봅니다.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: ed254ea3db1c8f0e680cba71185e9b4505d8f200
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66387645"
---
# <a name="how-to-use-entity-detection-callback"></a>엔터티 검색 콜백을 사용하는 방법

이 자습서에서는 엔터티 검색 콜백 보여주며, 엔터티를 확인하기 위한 일반적인 패턴을 보여줍니다.

## <a name="video"></a>비디오

[![엔터티 검색 콜백 자습서 미리 보기](https://aka.ms/cl_Tutorial_v3_EntityDetection_Preview)](https://aka.ms/cl_Tutorial_v3_EntityDetection)

## <a name="requirements"></a>요구 사항
이 자습서를 수행하려면 `tutorialEntityDetectionCallback` 봇이 실행 중이어야 합니다.

    npm run tutorial-entity-detection

## <a name="details"></a>세부 정보
엔터티 검색 콜백을 사용하면 코드를 통해 엔터티 인식 동작 수정 및 엔터티 조작을 할 수 있습니다. 일반적인 엔터티 검색 콜백 디자인 패턴을 탐색하여 이 기능을 보여주겠습니다. 예를 들어 "big apple"을 "뉴욕"으로 확인합니다.

## <a name="steps"></a>단계

### <a name="create-the-model"></a>모델 만들기

1. Web UI에서 "새 모델"을 클릭합니다.
2. "이름" 필드에 "EntityDetectionCallback"를 입력하고 Enter 키를 누릅니다.
3. "만들기" 단추를 클릭합니다.

이 예제에는 세 개의 엔터티가 필요하므로 이 세 개를 만들어 보겠습니다.

### <a name="create-the-custom-trained-entity"></a>사용자 지정 학습 엔터티 만들기

1. 왼쪽 패널에서 “엔터티”를 클릭한 다음, “새 엔터티” 단추를 클릭합니다.
2. “엔터티 형식”에 대해 “사용자 지정 학습”을 선택합니다.
3. "엔터티 이름"에 "도시"를 입력합니다.
4. "만들기" 단추를 클릭합니다.

### <a name="create-the-first-programmatic-entity"></a>첫 번째 프로그래밍 방식 엔터티 만들기

1. "새 엔터티" 단추를 클릭합니다.
2. "엔터티 형식"에 대해 "프로그래밍 방식"을 선택합니다.
3. "엔터티 이름"에 "CityUnknown"을 입력합니다.
4. "만들기" 단추를 클릭합니다.

### <a name="create-the-first-programmatic-entity"></a>첫 번째 프로그래밍 방식 엔터티 만들기

1. "새 엔터티" 단추를 클릭합니다.
2. "엔터티 형식"에 대해 "프로그래밍 방식"을 선택합니다.
3. "엔터티 이름"에 "CityResolved"를 입력합니다.
4. "만들기" 단추를 클릭합니다.

이제 세 개의 작업을 만듭니다.

### <a name="action-creation"></a>작업 만들기

1. 왼쪽 패널에서 “작업”을 클릭한 다음, “새 작업” 단추를 클릭합니다.
2. "봇 응답..." 필드에 "어떤 도시를 원하나요?"를 입력합니다.
3. "사용자의 예상 엔터티 회신..." 필드에 "도시"를 입력합니다.
4. “실격 엔터티” 필드에 “도시”를 입력합니다.
5. "만들기" 단추를 클릭합니다.
6. "새 작업" 단추를 클릭합니다.
7. "봇 응답..." 필드에 "어떤 도시를 원하나요?"를 입력합니다.
8. "사용자의 예상 엔터티 회신..." 필드에 "이 도시를 모르겠습니다"를 입력합니다.
9. "만들기" 단추를 클릭합니다.
10. "새 작업" 단추를 클릭합니다.
11. "봇의 응답..." 필드에 "$CityResolved가 매우 좋습니다"를 입력합니다.
12. "만들기" 단추를 클릭합니다.

콜백 코드는 다음과 같습니다.

![](../media/tutorial10_callbackcode.PNG)

### <a name="train-the-model"></a>모델 학습

1. 왼쪽 패널에서 “학습 대화 상자”를 클릭한 다음, “새 학습 대화 상자” 단추를 클릭합니다.
2. "메시지를 입력하세요..."가 표시된 채팅 패널에 "안녕하세요" 입력
3. “작업 점수 매기기” 단추를 클릭합니다.
4. "어떤 도시를 원하나요?"라는 응답을 선택합니다.
5. “메시지를 입력하세요...”가 표시된 채팅 패널에 “big apple”을 입력합니다.
6. “작업 점수 매기기” 단추를 클릭합니다.
    - 단추를 클릭하면 엔터티 검색 콜백이 트리거됨
    - 콜백 코드는 CityResolved 엔터티 값을 "뉴욕"으로 올바르게 설정
7. "뉴욕이 아주 좋습니다"라는 응답을 선택합니다.

이 패턴은 많은 봇 시나리오에 일반적입니다. 사용자 발언 및 추출된 엔터티를 비즈니스 논리에 제공하고 해당 논리는 발언을 정규 형식으로 변환한 다음, 후속 턴의 대화 상자의 경우 프로그래밍 방식 엔터티에 저장됩니다.

![](../media/tutorial10_bigapple.PNG)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [세션 콜백](./13-session-callbacks.md)
