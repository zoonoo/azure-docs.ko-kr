---
title: 미리 학습된 엔터티를 Conversation Learner 모델에 추가하는 방법 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: 미리 학습된 엔터티를 Conversation Learner 모델에 추가하는 방법을 알아봅니다.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: afa927009e684fa7f8c6217c91dcb589b331b5f5
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55224176"
---
# <a name="how-to-add-pre-trained-entities"></a>미리 학습된 엔터티를 추가하는 방법
이 자습서에서는 미리 학습된 엔터티를 Conversation Learner 모델에 추가하는 방법을 보여 줍니다.

## <a name="video"></a>비디오

[![미리 학습된 엔터티 자습서 미리 보기](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities_Preview)](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities)

## <a name="requirements"></a>요구 사항
이 자습서를 수행하려면 일반 자습서 봇이 실행 중이어야 합니다.

    npm run tutorial-general

## <a name="details"></a>세부 정보

미리 학습된 엔터티는 숫자, 날짜, 금액 등과 같은 일반적인 형식의 엔터티를 인식합니다.  이 엔터티는 "즉시" 작동하고 모든 학습을 필요로 하지 않으며, 사용자 지정 엔터티와 달리 해당 동작을 변경할 수 없습니다.  기본적으로 미리 학습된 엔터티는 다중값이며, 엔터티의 식별된 모든 인스턴스를 누적합니다.

## <a name="steps"></a>단계

### <a name="create-the-model"></a>모델 만들기

1. Web UI에서 "새 모델"을 클릭합니다.
2. "이름" 필드에 "PretrainedEntities"를 입력하고 Enter 키를 누릅니다.
3. "만들기" 단추를 클릭합니다.

### <a name="entity-creation"></a>엔터티 생성

1. 왼쪽 패널에서 “엔터티”를 클릭한 다음, “새 엔터티” 단추를 클릭합니다.
2. "엔터티 형식"에 대한 "Pre-Trained/datetimeV2"를 선택합니다.
3. “다중값” 확인란을 선택합니다.
    - 다중값 엔터티는 엔터티에 하나 이상의 값을 누적합니다.
    - 무효화할 수 있는 속성은 미리 학습된 엔터티에 대해 사용하지 않도록 설정됩니다.
4. "만들기" 단추를 클릭합니다.

![](../media/tutorial7_entities_a.PNG)

### <a name="create-the-first-action"></a>첫 번째 작업 만들기

1. 왼쪽 패널에서 “작업”을 클릭한 다음, “새 작업” 단추를 클릭합니다.
2. "봇 응답..." 필드에 "The date is $builtin-datetimev2" 입력
3. "만들기" 단추를 클릭합니다.

![](../media/tutorial7_actions_a.PNG)

### <a name="create-the-second-action"></a>두 번째 작업 만들기

1. 왼쪽 패널에서 “작업”을 클릭한 다음, “새 작업” 단추를 클릭합니다.
2. "봇 응답..." 필드에 "날짜는?" 입력
    - 미리 학습된 엔터티는 기본적으로 모든 사용자 표현에 대해 인식되는 경우 필수 엔터티일 수 없습니다.
3. "비우량 엔터티" 필드에 "builtin-datetimev2"를 입력합니다.
4. "만들기" 단추를 클릭합니다.

![](../media/tutorial7_actions2_a.PNG)

### <a name="train-the-model"></a>모델 학습

1. 왼쪽 패널에서 “학습 대화 상자”를 클릭한 다음, “새 학습 대화 상자” 단추를 클릭합니다.
2. "메시지를 입력하세요..."가 표시된 채팅 패널에 "안녕하세요"를 입력합니다.
3. “작업 점수 매기기” 단추를 클릭합니다.
4. "날짜는?"이란 응답을 선택합니다.
5. “메시지를 입력하세요...”가 표시된 채팅 패널에 “오늘” 입력
    - 오늘 발언은 LUIS의 미리 학습된 모델에서 자동으로 인식됩니다.
    - 미리 학습된 엔터티의 값을 마우스로 가리키면 LUIS에서 제공되는 추가 데이터가 표시됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [엔터티 해결 프로그램](./09-entity-resolvers.md)
