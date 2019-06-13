---
title: 데모 Conversation Learner 모델, 피자 주문 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: 데모 Conversation Learner 모델을 만드는 방법을 알아봅니다.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 010245480d8e1f59d5c1b92a9e717f73b5ba7f4c
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389151"
---
# <a name="demo-pizza-order"></a>데모: 피자 주문
이 데모에서는 다음을 통해 피자 한 판 주문을 지원하는 피자 주문 봇을 설명합니다.

- 사용자 발언에서 피자 토핑 인식
- 토핑 인벤토리 관리 및 적절하게 응답
- 이전 순서 기억 및 동일한 피자를 빠르게 재주문

## <a name="video"></a>비디오

[![데모 피자 미리 보기](https://aka.ms/cl_Tutorial_v3_DemoPizzaOrder_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPizzaOrder)

## <a name="requirements"></a>요구 사항
이 자습서를 수행하려면 피자 주문 봇이 실행 중이어야 합니다.

    npm run demo-pizza

### <a name="open-the-demo"></a>데모 열기

Web UI의 모델 목록에서 TutorialDemo 피자 주문을 클릭합니다. 

## <a name="entities"></a>엔터티

모델에는 세 개의 엔터티가 포함됩니다.

- “Toppings”는 사용 가능한 경우 사용자의 지정한 토핑을 누적합니다.
- “OutofStock”은 선택한 토핑이 품절되었음을 사용자에게 알립니다.
- “LastToppings”에는 이전 주문의 토핑 기록이 포함됩니다.

![](../media/tutorial_pizza_entities.PNG)

### <a name="actions"></a>작업

모델은 사용자의 토핑 선택, 누적된 토핑 등을 묻는 작업 세트를 포함합니다.

다음과 같은 두 개의 API 호출도 함께 제공됩니다.

- “FinalizeOrder”: 주문 완료를 처리
- “UseLastToppings”: 토핑 기록 정보를 처리

![](../media/tutorial_pizza_actions.PNG)

### <a name="training-dialogs"></a>학습 대화 상자

모델에는 몇 가지 학습 대화 상자가 있습니다.

![](../media/tutorial_pizza_dialogs.PNG)

다른 학습 대화 상자를 만들어 모델을 더 학습해보겠습니다.

1. 왼쪽 패널에서 “학습 대화 상자”를 클릭한 다음, “새 학습 대화 상자” 단추를 클릭합니다.
2. “메시지를 입력하세요...”가 표시된 채팅 패널에 “치즈를 넣은 피자 주문”을 입력합니다.
    - “치즈”란 단어는 엔터티 추출기에서 추출되었습니다.
3. “작업 점수 매기기” 단추를 클릭합니다.
4. “피자에 치즈가 올라갑니다”란 응답을 선택합니다.
5. “더 필요하신 것 있으신가요?”란 응답을 선택합니다.
6. “메시지를 입력하세요...”가 표시된 채팅 패널에 “버섯과 피망 추가”를 입력합니다.
7. “작업 점수 매기기” 단추를 클릭합니다.
8. “피자에 치즈, 버섯 및 피망이 올라갑니다”란 응답을 선택합니다.
9. “더 필요하신 것 있으신가요?”란 응답을 선택합니다.
10. “메시지를 입력하세요...”가 표시된 채팅 패널에 “피망 빼고 소세지 추가”를 입력합니다.
11. “작업 점수 매기기” 단추를 클릭합니다.
12. “피자에 치즈, 버섯 및 소세지가 올라갑니다”란 응답을 선택합니다.
13. “더 필요하신 것 있으신가요?”란 응답을 선택합니다.
14. “메시지를 입력하세요...”가 표시된 채팅 패널에 “얌 추가”를 입력합니다.
15. “작업 점수 매기기” 단추를 클릭합니다.
    - 해당 텍스트는 지원되는 재료와 일치하지 않으므로 엔터티 감지 콜백 코드에 의해 “얌” 값이 “OutofStock”에 추가되었습니다.
16. “OutOfStock” 응답을 선택합니다.
17. “더 필요하신 것 있으신가요?”란 응답을 선택합니다.
18. “메시지를 입력하세요...”가 표시된 채팅 패널에 “아니요”를 입력합니다.
    - “아니요”는 어떠한 의도 유형으로도 표시되지 않습니다. 대신, 현재 컨텍스트를 기준으로 관련 작업을 선택하게 됩니다.
19. “작업 점수 매기기” 단추를 클릭합니다.
20. “FinalizeOrder” 응답을 선택합니다.
    - 이 작업을 선택하면 FinalizeOrder 콜백 코드에 의해 고객의 현재 토핑이 “LastToppings” 엔터티에 저장되고 “Toppings” 엔터티가 삭제됩니다.
21. “메시지를 입력하세요...”가 표시된 채팅 패널에 “다른 주문”을 입력합니다.
22. “작업 점수 매기기” 단추를 클릭합니다.
23. “치즈, 버섯 및 소세지를 하시겠습니까?”란 응답을 선택합니다.
    - “LastToppings” 엔터티가 설정되었으므로 현재 이 작업을 사용할 수 있습니다.
24. “메시지를 입력하세요...”가 표시된 채팅 패널에 “예”를 입력합니다.
25. “작업 점수 매기기” 단추를 클릭합니다.
26. “UseLastToppings” 응답을 선택합니다.
27. “피자에 치즈, 버섯 및 소세지가 올라갑니다”란 응답을 선택합니다.
28. “더 필요하신 것 있으신가요?”란 응답을 선택합니다.

![](../media/tutorial_pizza_callbackcode.PNG)

![](../media/tutorial_pizza_apicalls.PNG)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Conversation Learner 봇 배포](../deploy-to-bf.md)
