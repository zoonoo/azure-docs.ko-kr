---
title: Conversation Learner 모델에서 대화를 기록하는 방법 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner 모델에서 대화를 기록하는 방법을 알아봅니다.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: f1e161fa05a77682d0b5eb1c6c21975ac87028a3
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66387657"
---
# <a name="how-to-log-dialogs-in-a-conversation-learner-model"></a>Conversation Learner 모델에서 대화를 기록하는 방법

이 자습서에서는 실제 사용자와 상호 작용 기록을 통해 Conversation Learner 모델을 더 잘 학습하기 위해 로그 대화 상자를 사용하는 방법을 보여줍니다.

## <a name="video"></a>비디오

[![로그 대화 상자 자습서 미리 보기](https://aka.ms/cl_Tutorial_v3_LogDialogs_Preview)](https://aka.ms/cl_Tutorial_v3_LogDialogs)

## <a name="requirements"></a>요구 사항
이 자습서를 수행하려면 일반 자습서 봇이 실행 중이어야 합니다.

    npm run tutorial-general

또한 날씨 모델이 이전 자습서에서 생성되어야 합니다.

## <a name="details"></a>세부 정보
로그 대화 상자는 최종 사용자와 봇의 상호 작용을 기록한 로그입니다. 모델 성능 및 전반적인 시스템 성능을 향상시키려면 이러한 로그 대화 상자를 활용하여 엔터티 레이블 및 작업 선택 영역을 수정할 수 있습니다.

## <a name="steps"></a>단계

웹 UI에서 "자습서 가져오기"를 클릭하고 "Tutorial-11-LogDialogs"라는 모델을 선택합니다.

이 모델에는 "도시"라는 한 엔터티 및 해당 도시의 날씨에 대한 조회에 응답하도록 설계된 작업이 포함되어 있습니다. 두 학습 대화 상자는 모델을 학습하는 데 사용되었으므로 성능 기대치가 약간 낮을 수 있습니다. 모델은 추가 학습 및 실제 사용자 상호 작용에 대한 노출을 통해 향상됩니다.

### <a name="create-a-new-conversation"></a>새 대화 만들기

1. 왼쪽 패널에서 "로그 대화 상자" 및 "새 로그 대화상자" 단추를 차례로 클릭합니다.
2. “메시지를 입력하세요...”가 표시된 채팅 패널에 “오스틴 일기 예보”를 입력합니다.
3. "테스트 완료" 단추를 클릭합니다.
4. 목록에서 "오스틴 일기 예보" 로그 대화 상자를 클릭합니다.
5. 채팅 패널에서 "오스틴 일기 예보" 발언을 클릭합니다.
6. "오스틴"를 클릭한 다음, 엔터티 목록에서 "도시"를 클릭합니다.
7. "변경 내용 제출" 단추를 클릭합니다.
    - 이 엔터티 값을 변경하면 새 엔터티 값이 메모리에 있으므로 대화에 대한 변경 사항을 다운스트림할 수 있습니다. 특히 "도시" 엔터티가 포함되면 후속 동작이 잘못될 가능성이 높습니다.
8. 채팅 패널에서 "어떤 도시인가요?" 발언을 클릭합니다.
9. “오스틴의 날씨는 아마도 맑습니다”라는 응답을 선택합니다.
10. "학습 대화로 저장" 단추를 클릭합니다.
    - 학습 즉시 시작

![](../media/T11_logdialog.png)

마지막 주의 사항 하나. 비즈니스 요구 사항에 따라 설정으로 이동해 "로그 대화"를 선택 취소하여 대화 로깅 기능을 해제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [엔터티 검색 콜백](./12-entity-detection-callback.md)
