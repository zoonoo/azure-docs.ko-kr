---
title: Conversation Learner 모델의 엔터티 해결 프로그램 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner 모델의 엔터티 해결 프로그램을 사용하는 방법을 알아봅니다.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: cc1f9ea18d425679a4db9c7b91a1c0a3c4451d4b
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/27/2018
ms.locfileid: "53797038"
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

### <a name="create-a-new-model"></a>새 모델 만들기

1. Web UI에서 "새 모델" 단추를 클릭합니다.
2. "이름" 필드에 "엔터티 해결 프로그램"을 입력하고, Enter 키를 누르거나 "만들기" 단추를 클릭합니다.

### <a name="create-a-pair-of-entities"></a>엔터티 쌍 만들기

1. 왼쪽 패널에서 “엔터티”를 클릭한 다음, “새 엔터티” 단추를 클릭합니다.
2. "엔터티 이름" 필드에 "출발"을 입력합니다.
3. "해결 프로그램 유형" 드롭 다운 메뉴에서 "datetimeV2"를 선택합니다.
4. "만들기" 단추를 클릭합니다.
5. 정보 팝업 메시지를 읽은 후에 "확인" 단추를 클릭합니다.
6. 동일한 단계를 따라 "datetimeV2" 해결 프로그램 유형도 있는 "돌아오기"라는 다른 엔터티를 만듭니다.

### <a name="create-a-pair-of-actions"></a>작업 쌍 만들기

1. 왼쪽 패널에서 “작업”을 클릭한 다음, “새 작업” 단추를 클릭합니다.
2. "봇의 응답"에 필드에 형식 "$departure를 떠나 $return으로 돌아옵니다"를 입력합니다.
    - 중요 - "$ [entityName]"을 입력할 경우 Enter 키를 누르거나 드롭 다운 메뉴에서 엔터티를 클릭해야 합니다. 그렇지 않으면 Conversation Learner는 이를 엔터티가 아닌 텍스트로 간주하게 됩니다.
    - "필수 엔터티" 필드에 이러한 엔터티가 보유되고 제거될 수 없는지 확인합니다. 이렇게 하면 필수 엔터티가 모두 존재할 때까지 이 작업이 사용되는 것을 방지합니다.
3. "만들기" 단추를 클릭합니다.
4. 두 번째 작업을 만들려면 다시 "새 작업" 단추를 클릭합니다.
5. "봇의 응답" 필드에 "언제 여행할 계획입니까?"를 입력합니다.
6. "실격 엔터티" 필드에 "출발" 및 "돌아오기"를 차례로 입력합니다.
    - 이렇게 하면 이러한 엔터티 중 하나에 값이 포함된 경우 봇이 이 작업을 사용하지 않도록 지시합니다.
7. "만들기" 단추를 클릭합니다.


### <a name="training"></a>교육

1. 페이지의 왼쪽 위에 있는 "학습: [상태]"을 지켜보면서 "완료"되기를 기다립니다.
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
    - 각 날짜를 마우스로 가리키고 어떻게 엔터티가 "일요일" 또는 "내일"이 아닌 실제 달력 날짜를 명확하게 캡처하는 날짜 개체인지를 관찰합니다.
10. 봇 응답 "...에 출발합니다"를 선택합니다.
11. "저장" 단추를 클릭합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [대체 입력](./10-alternative-inputs.md)
