---
title: Conversation Learner에서 학습하는 방법 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner에서 학습하는 방법을 알아봅니다.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 9e5e6594a74219a22d67af18827bfe2b7cbd0fb8
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66385267"
---
# <a name="how-to-teach-with-conversation-learner"></a>Conversation Learner에서 학습하는 방법 

이 문서에서는 Conversation Learner가 인식하는 신호 및 학습 방법을 설명합니다.  

학습은 두 가지 개별 단계인 엔터티 추출 및 작업 선택으로 분해할 수 있습니다.

## <a name="entity-extraction"></a>엔터티 추출

내부적으로 Conversation Learner는 엔터티 추출을 위해 [LUIS](https://www.luis.ai)를 사용합니다.  LUIS에 익숙한 경우 해당 환경이 Conversation Learner의 엔터티 추출에 적용됩니다.

엔터티 추출 모델은 사용자 발언 내의 콘텐츠  및 컨텍스트  를 인식합니다.  예를 들어 “Seattle” 단어가 “What's the weather in Seattle”과 같은 하나의 발언에서 도시로 레이블이 지정된 경우 엔터티 추출은 발언이 완전히 다른 경우에도 “Population of Seattle”과 같은 다른 발언에서 동일한 콘텐츠(“Seattle”)를 도시로 인식할 수 있습니다.  반면, “Schedule a meeting with Francis”에서 “Francis”가 이름으로 인식된 경우 “Set up a meeting with Robin”과 같은 유사한 컨텍스트에서 이전에 보지 못한 새로운 이름을 인식할 수 있습니다.  Machine Learning은 학습 예제를 기준으로 콘텐츠, 컨텍스트 또는 둘 다에 적용하는 경우를 유추합니다.

현재, 엔터티 추출은 현재 발언의 콘텐츠만 인식합니다.  아래의 작업 선택과 달리, 이전 시스템 턴, 이전 사용자 턴 또는 이전에 인식된 엔터티와 같은 대화 기록은 인식하지 못합니다.  결과적으로, 엔터티 추출의 동작은 모든 발언에서 “공유”됩니다.  예를 들어 사용자 발언 “I want Apple”의 “Apple”이 한 사용자 발언에서 엔터티 형식 “Fruit”로 레이블이 지정된 경우 엔터티 추출 모델은 이 발언(“I want Apple”)의 “Apple”이 항상 “Fruit”로 레이블이 지정되어야 한다고 예상합니다.

엔터티 추출이 예상대로 작동하지 않는 경우 가능한 해결 방법은 다음과 같습니다.

- 첫 번째 방법은 학습 예제, 특히 일반적인 엔터티 컨텍스트(주변 단어) 또는 예외를 표시하는 예제를 더 추가하는 것입니다.
- 해당하는 경우 작업에 “예상 엔터티” 속성을 추가하는 것이 좋습니다.  자세한 내용은 예상 엔터티에 대한 자습서를 참조하세요.
- 코드를 사용하여 엔터티를 추출하는 수동 처리를 `EntityExtractionCallback`에 추가할 수 있지만, 시스템 성장에 따라 Machine Learning의 개선 사항을 활용할 수 없기 때문에 이 방법은 사용하지 않는 것이 좋습니다.

## <a name="action-selection"></a>작업 선택

작업 선택은 모든 대화 기록을 입력으로 사용하는 반복적인 신경망을 사용합니다.  따라서 작업 선택은 이전 사용자 발언, 엔터티 값, 시스템 발언을 인식하는 상태 저장 프로세스입니다.  

일부 신호는 학습 프로세스에서 기본적으로 선호됩니다.  즉, Conversation Learner가 “더 선호하는” 신호를 사용하여 작업 선택 결정을 설명할 수 있는 경우 해당 신호를 사용합니다. 더 선호하는 신호를 사용할 수 없는 경우에는 “덜 선호하는” 신호를 사용합니다.

다음은 Conversation Learner의 모든 신호를 보여 주는 표와 작업 선택에 사용되는 표입니다.  사용자 발언의 단어 순서는 무시됩니다.

신호 | 기본 설정(1=가장 선호하는 신호) | 메모
--- | --- | --- 
이전 턴의 시스템 작업 | 1 | 
현재 턴에 있는 엔터티 | 1 | 
첫 번째 턴인지 여부 | 1 |
현재 사용자 발언의 단어와 정확히 일치 | 2 | 
현재 사용자 발언의 유사 의미 단어 | 3 | 
이전 턴 전의 시스템 작업 | 4 |
현재 턴의 이전 턴에 있는 엔터티 | 4 | 
현재 턴 전의 사용자 발언 | 5 | 

> [!NOTE]
> 작업 선택은 텍스트, 카드 콘텐츠, API 이름 또는 동작과 같은 시스템 작업의 콘텐츠를 사용하지 않고 시스템 작업의 ID만 사용합니다.  따라서 작업의 콘텐츠를 변경해도 작업 선택 모델의 동작이 변경되지 않습니다.
>
> 또한 엔터티의 콘텐츠/값은 사용되지 않고 엔터티 있음/없음만 사용됩니다.

작업 선택이 예상대로 작동하지 않는 경우 가능한 해결 방법은 다음과 같습니다.

- 학습 대화, 특히 작업 선택에서 주의해야 하는 신호를 설명하는 대화를 더 추가합니다.  예를 들어 작업 선택이 한 신호를 다른 신호보다 선호하는 경우 동일한 상태의 선호 신호와 다른 여러 신호를 보여 주는 예제를 제공합니다.  일부 시퀀스는 몇 개의 학습 대화만 사용하여 학습할 수 있습니다.
- “필수” 및 “Disqualifying”(실격) 엔터티를 작업 정의에 추가합니다.  이렇게 하면 작업을 사용할 수 있는 경우가 제한되며, 비즈니스 규칙과 몇 가지 일반적인 패턴을 표현하는 데 유용할 수 있습니다. 

## <a name="updates-to-models"></a>모델 업데이트

UI에서 엔터티, 작업 또는 학습 대화를 추가하거나 편집할 때마다 엔터티 추출 모델과 작업 선택 모델을 둘 다 다시 학습하는 요청이 생성됩니다.  이 요청은 큐에 배치되며, 비동기적으로 다시 학습이 수행됩니다.  새 모델을 사용할 수 있게 되면 엔터티 추출 및 작업 선택에 대해 해당 시점부터 사용됩니다.  이 다시 학습 프로세스는 대체로 5초 정도 걸리지만, 모델이 복잡한 경우 또는 학습 서비스의 부하가 높은 경우 더 길어질 수 있습니다.

학습이 비동기적으로 수행되므로 편집한 내용이 즉시 반영되지 않을 수 있습니다.  마지막 5~10초 동안의 변경 내용을 기준으로 엔터티 추출 또는 작업 선택이 예상대로 작동하지 않는 경우 이러한 원인 때문일 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기본값 및 경계](./cl-values-and-boundaries.md)
