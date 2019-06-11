---
title: 대화 학습자 모델-Azure Cognitive Services를 사용 하 여 열거형 엔터티 및 엔터티 집합 작업을 사용 하는 경우 | Microsoft Docs
titleSuffix: Azure
description: 대화 학습자 모델 열거형 엔터티 및 엔터티 집합 작업에서 사용 하기에 적합 하는 경우에 대해 알아봅니다.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 1393d0bd1c31a2c9c24652e260ef7f3182d91367
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480804"
---
# <a name="when-to-use-enum-entities-and-set-entity-actions"></a>열거형 엔터티 및 엔터티 집합 작업을 사용 하는 경우

이 자습서에서는 (열거형) 엔터티와 SET_ENTITY 작업을 사용 해야 하는 경우를 설명 합니다.

## <a name="video"></a>비디오

[![Entity 자습서 미리 보기 설정](https://aka.ms/cl_Tutorial_v3_SetEntity_Preview)](https://aka.ms/cl_Tutorial_v3_SetEntity)



## <a name="what-is-covered"></a>포함된 내용

이 자습서에서는 두 가지 새로운 기능을 소개 합니다. 새로운 유형의 엔터티 (짧은 열거형)를 호출 하 고 이라는 SET_ENTITY 다음 열거형 값 중 하나를 참조할 수 있으며 이름에서 알 수 있듯이 엔터티를이 값으로 설정 하는 작업의 새 형식입니다. 아래 배웁니다 이러한 새 기능을 함께 사용 되 고 무엇 이며 아래 사용 하는 방법을 알아보겠습니다. 세부 정보를 시작 하기 전에 이러한 기능 해결할 문제를 이해 해야 됩니다.

![enum_entity_type.png](../media/tutorial-enum-set-entity/enum_entity_type.png)
![action_set_entity_type.png](../media/tutorial-enum-set-entity/action_set_entity_type.png)

## <a name="problem"></a>문제

단어의 의미는 컨텍스트에 따라 달라 집니다 대화에는 경우가 있습니다.  일반적으로 레이블이 지정 된 키 단어를 학습 언어 이해 서비스를 사용 하 여 추출 하며 이러한 경우에 이러한 시스템 못할 레이블이 지정 된 예제를 사용 하 여 자세한 합니다.

Imagine 주변 사람 간의 대화의 일부를 없을지도 고만 단어 "Yes"로 지정 합니다. 에 동의 하는 "예"는 알고는 또는 하기 전에 묻는 질문을 줄여놓았기 때문를 확인 합니다. 이전에 요청 하는 질문에 답변 의미를 제공 하는 컨텍스트로 사용 됩니다. "Yes" 이후 마찬가지로 이러한에 대 한 일반적인 응답 다양 한 질문을 사용 하 여 예제를 제공 하 여 학습할 수 없습니다 [숙련 된 사용자 지정](04-introduction-to-entities.md) 엔터티 때문에 모든 "Yes"는 엔터티로 레이블을 배웁니다 됩니다.

### <a name="example"></a>예

다음 예제를 사용 하 여 더 자세하게 설명 해 보겠습니다.

봇: Azure Cognitive Services을 원하십니까?
사용자: 봇 예: 아이스크림을 원하십니까?
사용자: 예

이전 자습서에서는 살펴보았습니다 [사용자 지정 학습](04-introduction-to-entities.md) 엔터티 및 초기 말하려는 "likesCogServices" 라는 엔터티를 만들고 하는 첫 번째 "Yes"이 엔터티로 레이블 수 있습니다.  그러나 시스템은 레이블을 두 번째 "Yes". "LikesIceCream"을 "Yes" 두 번째 레이블의 수정 하려고 할 때는 다음 두 동일한 입력의 충돌 "Yes" 의미가 다릅니다 만들고 멈춰 있을 것입니다.

이러한 경우 열거형 엔터티와 SET_ENTITY 작업을 사용 해야 하는 중입니다.

## <a name="when-to-use-enums-or-setentity-actions"></a>열거형을 사용 하는 경우 또는 SET_ENTITY 작업

열거형 엔터티와 SET_ENTITY 작업을 사용 하는 경우 알아야 아래 이러한 규칙을 사용 합니다.

- 검색 또는 설정 엔터티의 상황에 맞는 종속
- 가능한 값의 수가 고정 되어 (예 및 두 개의 값 것 이상)

즉, 이러한는 항상 no 확인 질문 등 닫기 종료 프롬프트를 사용 하 여

> [!NOTE]
> 현재 열거형 엔터티 마다 최대 5 개의 값 제한을 했습니다. 각 값 현재 64 제한에 슬롯 중 하나를 사용 합니다. 참조 [cl-값-및-경계](../cl-values-and-boundaries.md)

예제: 봇: 올바른 주문 기능
사용자: 예

엔터티는 가능한 값은 개방형 및 수정 되지 않음, 하는 경우와 같은 다른 기능을 사용 하 여 해야 [엔터티를 예상](05-expected-entity.md)합니다.

예제: 봇: 이름이 뭐에요?
사용자: Matt 봇의 경우: 원하는 색 이란?
사용자: Silver

이 메시지는 임의의 값을 사용 하 여 응답할 수 없습니다 때문에 개방형으로 간주 됩니다.

## <a name="what"></a>대상

### <a name="enum-entities"></a>열거형 엔터티

다른 엔터티와 마찬가지로 열거형 엔터티가 만들어집니다. "프로그래밍 방식 으로" 엔터티와 마찬가지로 있습니다 수 없습니다. 레이블 단어 이러한 엔터티로입니다. 대신, 코드 또는 SET_ENTITY 작업을 통해 설정 되어야 합니다.

![enum_entity_creation.png](../media/tutorial-enum-set-entity/enum_entity_creation.png)

### <a name="set-entity-actions"></a>엔터티 작업 설정

위에서 설명한 대로 "엔터티 설정" 작업은 단순히 엔터티 알려진된 열거형 값으로 설정 합니다. API 콜백 작업 만들기 및 메모리 관리자를 사용 하 여 엔터티를 값으로 설정 하 여 동일한 결과 얻을 수 있습니다. 예: `memory.Set(entityName, entityValue)`. 이러한 작업을 만들고이 코드를 작성 하지 않아도 됩니다 될 번거롭고-관리 하기가 있으므로 대화 학습자의 특별 한 동작을 사용할 때 이러한 작업을 자동으로 생성 하 고이 작업을 용이 하 게 합니다. 독립적인 작업으로 이러한 다른 작업 또는 봇의 코드를 사용 하 여 결합 되지 않고 이러한 작성 하는 기능을 유지 합니다.

- 엔터티 작업 집합 열거형 엔터티를 먼저 만들어야 하므로 열거형 엔터티의 값으로 참조 하는 경우에 만들 수 있습니다.
- 집합 엔터티 작업은 또한 "비-await" 출력 하지 않도록 표시 하며 사용자가 볼 수 "대기" 작업으로 수행 해야 하므로입니다.
- 엔터티 작업 집합을 만든 후 편집할 수는 없습니다 의미 변경할 수 없는.입니다.

![action_set_entity_creation.png](../media/tutorial-enum-set-entity/action_set_entity_creation.png)

### <a name="automatic-action-generation"></a>자동 작업 생성

열거형 엔터티 모델에 있는 경우 대화 학습자를 자리 표시자 가능한 값 각각에 대 한 작업을 만들고 학습 중 선택에 사용할 수 있도록 합니다. 선택 작업은 자동으로 생성 합니다.

예를 들어 "Yes" 값으로 열거형 엔터티를 만들 경우 "No":

![enum_entity_order_confirmation.png](../media/tutorial-enum-set-entity/enum_entity_order_confirmation.png)

이 새 열거형에 대 한 작업을 명시적으로 만들지 않고도 학습 하는 동안 사용할 수 있는 두 가지 새 작업을 볼 수 있습니다.

![action_set_entity_sample.png](../media/tutorial-enum-set-entity/action_set_entity_sample.png)


## <a name="create-a-bot-using-these-new-features"></a>이러한 새 기능을 사용 하는 봇 만들기

### <a name="requirements"></a>요구 사항

이 자습서를 수행하려면 일반 자습서 봇이 실행 중이어야 합니다.

    npm run tutorial-general

패스트 푸드 순서를 시뮬레이션 하기 위해 bot을 만들겠습니다. 음료 및 튀김과 (작은/MEDIUM/LARGE)의 크기에 대 한 불연속 값을 갖습니다 하 고 확인 예 사용 하 여 질문 / 전혀 응답 하지 않습니다. 이러한 엔터티 둘 다의 컨텍스트에 종속 답변과 고정된 값 위의 두 규칙을 만족 합니다.

### <a name="create-the-model"></a>모델 만들기

1. 웹 UI에서 "가져오기" 클릭
2. 명명 된 "자습서-열거형-집합-엔터티" 자습서를 선택 합니다.

이 모델 관리 페이지를 탐색 합니다.
몇 가지 열거형 엔터티를 이미 포함 하는 모델 및 엔터티 작업을 설정 합니다.

### <a name="create-the-first-dialog"></a>첫 번째 대화 만들기

1. 왼쪽된 탐색 패널에서 "학습 대화 상자"를 클릭 한 다음 "새 학습 Dialog" 단추를 클릭 합니다.
2. 사용자 유형으로, "안녕하세요 싶습니다는 coke 주문과 튀김과 하세요"입니다.
3. "점수 매기기 작업" 단추를 클릭 합니다.

   > 음료에 대 한 크기를 지정 하지 않은 사용자나 튀김과 있으므로 지우도록 해야 합니다.

4. 응답을 사용 하 여 작업을 선택 합니다. "어떤 크기 음료 하시 겠어요"?
5. 사용자 유형으로, "큰"
6. "점수 매기기 작업" 단추를 클릭 합니다.
7. 작업-SET_ENTITY 선택 "drinkSize: 큰 "
8. 응답을 사용 하 여 작업을 선택 합니다. "크기 튀김과 시겠습니까"?
9. 사용 하 여 유형으로 "음, 확인 된 중간 합니다.
10. "점수 매기기 작업" 단추를 클릭 합니다.
11. 작업-SET_ENTITY 선택 "friesSize: MEDIUM"
12. 응답을 사용 하 여 작업을 선택 합니다. "시겠습니까 모든 입력 하면 조미료"?
13. "Yes"에서 사용 하 여 형식으로
14. "점수 매기기 작업" 단추를 클릭 합니다.
15. 작업-SET_ENTITY 선택 "condimentsConfirmation: YES "
16. 응답을 사용 하 여 작업을 선택 합니다. ", 보통 튀김과 큰 음료를 주문을 했습니다. 이것이 올바릅니까 "?
17. "Yes"에서 사용 하 여 형식으로
18. "점수 매기기 작업" 단추를 클릭 합니다.
19. 작업-SET_ENTITY 선택 "orderConfirmation: YES "
20. 응답을 사용 하 여 작업을 선택 합니다. "확인, 주문 번호는 58 합니다. 잠시 기다려 주십시오 나와 있습니다. "
21. "저장" 대화 상자를 닫으려면 클릭

지금까지 열거형 엔터티와 SET_ENTITY 작업을 사용 하 여 첫 번째 대화 상자를 만들었습니다. 부분 정보를 지정 하거나 다른 유형의 질문 닫기 종료를 사용 하 여 실험 사용자의 많은 조합이 가능 합니다.

> [!NOTE]
> 학습 하는 동안 나타납니다 SET_ENTITY 작업에 대 한 자리 표시자와 같은
>
> ![action_set_entity_training.png](../media/tutorial-enum-set-entity/action_set_entity_training.png)
>
> 하지만 만들기 대화 상자 또는 배포를 사용 하 여 로그인 할 때 봇 사용자에 게는 보이지 이러한 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [대체 입력](./10-alternative-inputs.md)
