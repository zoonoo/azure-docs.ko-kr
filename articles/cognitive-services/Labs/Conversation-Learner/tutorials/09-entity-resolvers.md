---
title: 엔터티 확인자 대화 학습자 모델-Azure Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner 모델의 엔터티 해결 프로그램을 사용하는 방법을 알아봅니다.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 51f74f504f0ad70c8c7f73be8ee6a05add685824
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475745"
---
# <a name="entity-resolvers"></a>엔터티 해결 프로그램

이 자습서에서는 Conversation Learner의 엔터티 해결 프로그램을 사용하는 방법을 알아봄

## <a name="video"></a>비디오

[![엔터티 해결 프로그램 자습서 미리 보기](https://aka.ms/cl_Tutorial_v3_EntityResolvers_Preview)](https://aka.ms/cl_Tutorial_v3_EntityResolvers)

## <a name="requirements"></a>요구 사항
이 자습서를 수행하려면 일반 자습서 봇이 실행 중이어야 합니다.

    npm run tutorial-general

## <a name="details"></a>세부 정보

- 해결 프로그램은 사용자 지정 엔터티의 선택적 속성입니다.
- 엔터티 해결 프로그램은 사용자 지정 엔터티에 대한 추가 세부 정보 및 명확성을 제공하도록 LUIS에서 미리 학습된 엔터티 인식기의 기능을 활용합니다.

## <a name="steps"></a>단계

Web UI의 홈페이지에서 시작합니다.

### <a name="create-the-model"></a>모델 만들기

1. 선택 **새 모델**합니다.
2. 입력 **엔터티 확인자** 에 대 한 **이름**합니다.
3. **만들기**를 선택합니다.

### <a name="create-a-pair-of-entities"></a>엔터티 쌍 만들기

1. 선택 **엔터티** 왼쪽된 패널에서 **새 엔터티**합니다.
2. 입력 **출발** 에 대 한 **엔터티 이름**합니다.
3. 선택 **datetimeV2** 에 대 한 **해결 프로그램 유형이**합니다.
4. **만들기**를 선택합니다. 선택 하 여 정보 팝업을 해제 **확인**합니다.
5. 1-4 라는 두 번째 엔터티를 만드는 단계를 반복 **반환** 사용 하 여 **datetimeV2** 해결 프로그램 유형이 있습니다.

![](../media/T09_entities.png)

### <a name="create-a-pair-of-actions"></a>작업 쌍 만들기

1. 선택 **동작** 왼쪽된 패널에서 **새 작업**합니다.
2. 입력 **$departure에 남게 되며 $return 반환** 에 대 한 **봇의 응답...** .
    - 입력에 도달 해야 하는 $[entityName]에 입력 하거나 드롭다운이 고 그렇지 대화 학습자의 엔터티에 클릭은이 엔터티 대신 텍스트를 고려 하는 경우 중요 합니다.
    - 다음에 유의 합니다 **필요한 엔터티** 필드는 또한 이러한 엔터티를 가져오고 제거할 수 없습니다. 이렇게 하면 필수 엔터티가 모두 존재할 때까지 이 작업이 사용되는 것을 방지합니다.
3. **만들기**를 선택합니다.
4. 선택 **새 작업** 두 번째 작업을 만듭니다.
5. 입력 **위치로 이동할 때 계획 인가요?** 에 대 한 **봇의 응답...** .
6. 입력 **출발** 하 고 **반환** 에 대 한 **우량 엔터티**합니다. 이렇게 하면 이러한 엔터티 중 하나에 값이 포함된 경우 봇이 이 작업을 사용하지 않도록 지시합니다.
7. **만들기**를 선택합니다.

![](../media/T09_actions.png)

### <a name="training"></a>교육

1. 조사식 합니다 **학습: [Status]** 에 대 한 왼쪽된 위 모서리에서 **완료**합니다.
    - 이 시간이 너무 오래 걸리는 경우 "새로 고침" 링크를 클릭할 수 있습니다.
    - 모델을 학습할 때 엔터티 해결 프로그램이 작동하도록 "완료됨" 학습 상태가 필요합니다.

2. 왼쪽 패널에서 “학습 대화 상자”를 클릭한 다음, “새 학습 대화 상자” 단추를 클릭합니다.
3. 첫 번째 사용자 발언에 "항공권을 예약해 주세요"를 입력합니다. 
4. “작업 점수 매기기” 단추를 클릭합니다.
5. "언제 여행할 계획입니까?"라는 응답을 선택합니다.
6. 사용자로서 "오늘 출발해 다음 주 일요일에 돌아옴"이라고 응답합니다.
    - Conversation Learner가 해당 사용자 턴에서 두 개의 "미리 학습된 날짜"를 검색한 방법을 확인합니다.
7. "엔터티 검색" 패널에서 "내일" 텍스트를 선택하고 "출발"로 레이블 지정
8. 또한 "다음 주 일요일" 텍스트를 "돌아오기"로 레이블 지정
9. “작업 점수 매기기” 단추를 클릭합니다.
    - "메모리" 창에 출발 및 돌아오기 날짜가 포함되는 방법을 확인합니다.
    - 각각 위에 놓고 어떻게 엔터티는 "내일"을 "Sunday" 대신 실제 날짜를 명확 하 게 캡처하는 날짜 개체를 관찰 합니다.
10. 봇 응답 "...에 출발합니다"를 선택합니다.
11. "저장" 단추를 클릭합니다.

![](../media/T09_training.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [열거형 엔터티](./tutorial-enum-set-entity.md)
