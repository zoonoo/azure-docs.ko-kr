---
title: 열거형 엔터티를 사용 하 고 Conversation Learner 모델을 사용 하 여 엔터티 작업을 설정 하는 경우-Azure Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner 모델을 사용 하 여 열거형 엔터티를 사용 하 고 엔터티 작업을 설정 하는 것이 적절 한 경우에 대해 알아봅니다.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nolachar
ROBOTS: NOINDEX
ms.openlocfilehash: 5443b97febd6bf3831690531bceb540181e7676c
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706987"
---
# <a name="when-to-use-enum-entities-and-set-entity-actions"></a>열거형 엔터티를 사용 하 고 엔터티 작업을 설정 하는 경우

이 자습서에서는 열거형 (열거) 엔터티와 SET_ENTITY 작업을 사용 해야 하는 경우에 대해 설명 합니다.

## <a name="video"></a>비디오

[![엔터티 자습서 미리 보기 설정](https://aka.ms/cl_Tutorial_v3_SetEntity_Preview)](https://aka.ms/cl_Tutorial_v3_SetEntity)



## <a name="what-is-covered"></a>포함된 내용

이 자습서에서는 두 가지 새로운 기능을 소개 합니다. 열거형 (열거형의 경우 short) 이라는 새 형식의 엔터티와 이러한 열거형 값 중 하나를 참조할 수 있는 SET_ENTITY 이라는 새로운 동작 형식이 며, 이름에서 알 수 있듯이 엔터티를이 값으로 설정 합니다. 아래에서 설명 하는 것 처럼 이러한 새로운 기능을 함께 사용 하 고,이 기능을 설명 하 고 아래에서 사용 하는 방법을 설명 합니다. 세부 정보를 얻기 전에 이러한 기능이 해결 하는 데 도움이 되는 문제를 이해 하는 것이 중요 합니다.

![enum_entity_type.png](../media/tutorial-enum-set-entity/enum_entity_type.png)
![action_set_entity_type.png](../media/tutorial-enum-set-entity/action_set_entity_type.png)

## <a name="problem"></a>문제점

대화에는 단어의 의미가 컨텍스트에 따라 달라 지는 경우가 있습니다.  일반적으로 레이블이 지정 된 주요 단어는 언어 이해 서비스를 사용 하 여 학습 되 고 추출 되지만, 이러한 경우 레이블이 지정 된 예를 사용 하 여 학습 하지 못할 수 있습니다.

가까운 사람들 사이에서 대화의 일부를 지나치게 듣고 있습니다. "예" 라는 단어만 있습니다. "예"가 동의 하는 것을 알 수 없으며 이전에 요청한 질문을 듣지 못했습니다. 앞에서 요청한 질문은 응답에 의미를 부여 하는 컨텍스트입니다. 마찬가지로, "예"는 [사용자 지정 학습](04-introduction-to-entities.md) 된 엔터티를 사용 하는 것과 같은 방법으로 예제를 제공 하 여 알 수 없는 다양 한 질문에 대 한 일반적인 응답입니다.

### <a name="example"></a>예제

다음 예제를 사용 하 여 더 명확 하 게 살펴보겠습니다.

Boe-bot Azure Cognitive Services 하시 겠어요?
사용자: 예 봇: 아이스크림 하시 겠어요?
사용자: 예

이전 자습서에서는 [사용자 지정 학습](04-introduction-to-entities.md) 된 엔터티를 살펴보고 처음에는 "likesCogServices" 라는 엔터티를 만들고 첫 번째 "예"를이 엔터티로 레이블을 지정 하는 것이 있습니다.  그러나 시스템은 두 번째 "예"에도 레이블을 추가할 수 있습니다. 두 번째 "Yes"의 레이블을 "likesIceCream"로 수정 하려고 할 때 두 가지 동일한 입력의 충돌을 만듭니다. "예"는 서로 다른 작업을 의미 하며 중단 됩니다.

이러한 경우 열거형 엔터티와 SET_ENTITY 작업을 사용 해야 합니다.

## <a name="when-to-use-enums-or-set_entity-actions"></a>열거형 또는 SET_ENTITY 작업을 사용 하는 경우

아래 규칙을 사용 하 여 열거형 엔터티와 SET_ENTITY 작업을 사용 하는 시기를 확인할 수 있습니다.

- 엔터티를 검색 하거나 설정 하는 것은 상황에 따라 다릅니다.
- 가능한 값의 수가 고정 되어 있습니다 (예, 아니요는 두 값이 됨).

즉, 항상 예 또는 아니요를 사용 하는 확인 질문과 같은 종결 되지 않은 프롬프트에 이러한 메시지를 사용 합니다.

> [!NOTE]
> 현재 열거형 엔터티 당 최대 5 개의 값을 제한 합니다. 각 값은 현재 64 제한의 슬롯 중 하나를 사용 합니다. [Cl-값-및 경계를](../cl-values-and-boundaries.md) 참조 하세요.

예제: Boe-bot 주문이 정확한 가요?
사용자: 예

엔터티의 가능한 값이 열려 있고 고정 되지 않은 경우 [예상 엔터티](05-expected-entity.md)와 같은 대체 기능을 사용 해야 합니다.

예제: Boe-bot 이름이 뭐에요?
사용자: Matt 봇: 선호 하는 색은 무엇 인가요?
사용자: Silver

이러한 프롬프트는 임의의 값으로 대답할 수 있으므로 열려 있는 것으로 간주 됩니다.

## <a name="what"></a>내용:

### <a name="enum-entities"></a>엔터티 열거

열거형 엔터티는 다른 엔터티와 마찬가지로 생성 됩니다. "프로그래밍" 엔터티와 마찬가지로 단어 레이블을 이러한 엔터티로 지정할 수 없습니다. 대신 code 또는 SET_ENTITY 작업을 통해 설정 해야 합니다.

![enum_entity_creation.png](../media/tutorial-enum-set-entity/enum_entity_creation.png)

### <a name="set-entity-actions"></a>엔터티 작업 설정

위에서 설명한 것 처럼 "엔터티 설정" 작업은 단순히 엔터티를 알려진 열거형 값으로 설정 합니다. API 콜백 작업을 만들고 메모리 관리자를 사용 하 여 엔터티를 값으로 설정 하 여 동일한 결과를 달성할 수 있습니다. 예를 들어 `memory.Set(entityName, entityValue)`. 이 코드를 작성 하 고 이러한 작업을 만드는 작업은 지루한 작업이 며 관리가 어렵습니다. 따라서 Conversation Learner이 작업을 용이 하 게 하 고 사용 될 때 이러한 작업을 자동으로 생성 하는 특별 한 작업이 있습니다. 이러한 작업을 독립적인 작업으로 유지 하면 bot의 다른 동작 또는 코드와 연계 하지 않고도 이러한 작업을 작성할 수 있습니다.

- Set 엔터티 작업은 열거형 엔터티 값을 참조 하는 경우에만 만들 수 있으므로 열거형 엔터티를 먼저 만들어야 합니다.
- 또한 설정 엔터티 작업은 출력이 표시 되지 않으며 사용자가 볼 수 있는 "대기" 작업을 따라야 하므로 "대기 안 함"입니다.
- 엔터티 설정 작업은 변경할 수 없습니다. 즉, 만든 후에는 편집할 수 없습니다.

![action_set_entity_creation.png](../media/tutorial-enum-set-entity/action_set_entity_creation.png)

### <a name="automatic-action-generation"></a>자동 작업 생성

모델에 열거형 엔터티가 있으면 가능한 각 값에 대 한 자리 표시자 동작을 만들고 학습 중에 선택할 수 있도록 Conversation Learner 합니다. 선택 하면 작업이 자동으로 생성 됩니다.

예를 들어 "Yes" 및 "No" 값을 사용 하 여 열거형 엔터티를 만드는 경우:

![enum_entity_order_confirmation.png](../media/tutorial-enum-set-entity/enum_entity_order_confirmation.png)

이 새 열거형에 대 한 작업을 명시적으로 만들지 않아도 학습 중에 두 가지 새 작업을 사용할 수 있습니다.

![action_set_entity_sample.png](../media/tutorial-enum-set-entity/action_set_entity_sample.png)


## <a name="create-a-bot-using-these-new-features"></a>이러한 새 기능을 사용 하 여 봇 만들기

### <a name="requirements"></a>요구 사항

이 자습서를 수행하려면 일반 자습서 봇이 실행 중이어야 합니다.

    npm run tutorial-general

빠른 음식 주문을 시뮬레이트하는 봇을 만듭니다. 청량 음료 및 튀김 (SMALL/MEDIUM/LARGE) 크기에 대 한 불연속 값과 YES/NO 답변을 사용 하는 확인 질문이 있습니다. 이러한 두 엔터티는 모두 컨텍스트 종속 대답 및 고정 값의 두 가지 규칙을 만족 합니다.

### <a name="create-the-model"></a>모델 만들기

1. 웹 UI에서 "가져오기"를 클릭 합니다.
2. "Tutorial-Enum-Entity" 라는 자습서를 선택 합니다.

그러면 모델 관리 페이지로 이동 합니다.
모델에는 이미 몇 개의 열거 엔터티와 엔터티 작업 설정이 포함 되어 있습니다.

### <a name="create-the-first-dialog"></a>첫 번째 대화 만들기

1. 왼쪽 탐색 패널에서 "대화 상자 학습"을 클릭 한 다음 "새 학습 대화 상자" 단추를 클릭 합니다.
2. 사용자의 입력에 따라 "안녕하세요, 튀김을 주문 하 고 싶습니다."
3. "동작 점수 매기기" 단추를 클릭 합니다.

   > 사용자는 음료 또는 튀김의 크기를 지정 하지 않았으므로 요청 해야 합니다.

4. 응답이 있는 작업을 선택 합니다. "어떤 크기의 음료를 원하는가?"
5. 사용자 유형으로 "큼"
6. "동작 점수 매기기" 단추를 클릭 합니다.
7. SET_ENTITY-"drinkSize:를 선택 합니다. 큰
8. 응답이 있는 작업을 선택 합니다. "원하는 크기 튀김?"
9. "Um"에서 사용 형식으로 해당 미디어를 만듭니다.
10. "동작 점수 매기기" 단추를 클릭 합니다.
11. 작업 SET_ENTITY-"friesSize: 중간
12. 응답이 있는 작업을 선택 합니다. "마음에 조미료?"
13. "Yes"의 사용 유형으로
14. "동작 점수 매기기" 단추를 클릭 합니다.
15. 작업 SET_ENTITY-"condimentsConfirmation: 예로
16. 응답이 있는 작업을 선택 합니다. "확인, 매우 많은 음료와 중형 튀김에 대 한 주문이 있습니다. 올바른 가요? "
17. "Yes"의 사용 유형으로
18. "동작 점수 매기기" 단추를 클릭 합니다.
19. 작업 SET_ENTITY-"orderConfirmation: 예로
20. 응답이 있는 작업을 선택 합니다. "확인, 주문 번호는 58입니다. 잠시 기다려 주세요. "
21. "저장"을 클릭 하 여 대화 상자를 닫습니다.

방금 ENUM entities 및 SET_ENTITY 작업을 사용 하 여 첫 번째 대화 상자를 만들었습니다. 부분 정보를 지정 하거나 다른 유형의 종결 되지 않은 질문을 시험해 볼 수 있는 사용자의 많은 조합을 만들 수 있습니다.

> [!NOTE]
> 학습 중에 SET_ENTITY 작업에 대 한 자리 표시 자가 표시 됩니다.
>
> ![action_set_entity_training.png](../media/tutorial-enum-set-entity/action_set_entity_training.png)
>
> 그러나 로그 대화 상자를 만들거나 배포 된 봇 사용자를 사용 하는 경우 이러한 사용자가 표시 되지 않습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [대체 입력](./10-alternative-inputs.md)
