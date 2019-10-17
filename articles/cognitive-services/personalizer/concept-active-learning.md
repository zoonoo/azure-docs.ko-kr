---
title: 활성 및 비활성 이벤트-Personalizer
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: diberry
ms.openlocfilehash: aa6f53901f21dcb0726454d641a4a2a66007f9e0
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72429044"
---
# <a name="active-and-inactive-events"></a>활성 및 비활성 이벤트

응용 프로그램이 Rank API를 호출 하면 응용 프로그램이 rewardActionId 필드에 표시 해야 하는 작업을 받게 됩니다.  이 순간부터 Personalizer는 동일한 eventId를 사용 하 여 보상 호출을 기대 합니다. 보상 점수는 이후 순위 호출에 사용 될 모델을 학습 하는 데 사용 됩니다. EventId에 대해 보상 호출이 수신 되지 않으면 기본 보상이 적용 됩니다. 기본 보상은 Azure Portal에서 설정 됩니다.

경우에 따라 응용 프로그램에서 순위 보어를 호출 해야 할 수 있습니다 .이는 결과가 사용 되는지 사용자에 게 displayedn를 알고 있습니다. 이는 예를 들어 승격 된 콘텐츠의 페이지 렌더링을 마케팅 캠페인으로 덮어쓰는 경우에 발생할 수 있습니다. Rank 호출 결과를 사용 하지 않은 경우 사용자가 해당 결과를 볼 수 없는 경우에는 0 또는 그 밖의 보상으로 학습 하는 것이 잘못 된 것입니다.
일반적으로이 작업은 다음과 같은 경우에 발생 합니다.

* 사용자가 볼 수 있거나 볼 수 없는 일부 UI를 미리 렌더링할 수 있습니다. 
* 애플리케이션은 실시간 특성이 적은 상황에서 Rank를 호출하는 예상 개인 설정을 수행할 수 있으며 해당 출력을 애플리케이션에서 사용할 수도 있고 사용하지 않을 수도 있습니다. 

이러한 경우 Personalizer를 사용 하는 올바른 방법은 이벤트를 _비활성_으로 요청 하는 순위를 호출 하는 것입니다. Personalizer는이 이벤트에 대 한 보상을 필요로 하지 않으며 기본 보상을 적용 하지 않습니다. 비즈니스 논리에서 r을 사용 합니다. 응용 프로그램에서 순위 호출의 정보를 사용 하는 경우에는 이벤트를 _활성화_ 하기만 하면 됩니다. 이벤트가 활성화 되는 순간부터 Personalizer는 이벤트에 대 한 보상을 요구 하거나 보상 API에 대 한 명시적 호출이 수행 되지 않은 경우 기본 보상을 적용 합니다.

## <a name="get-inactive-events"></a>비활성 이벤트 가져오기

이벤트에 대 한 학습을 사용 하지 않도록 설정 하려면 `learningEnabled = False`으로 순위를 호출 합니다.

EventId에 대해 보상을 보내거나 해당 eventId에 대해 `activate` API를 호출 하면 비활성 이벤트에 대 한 학습이 암시적으로 활성화 됩니다.

## <a name="learning-settings"></a>학습 설정

학습 설정은 모델 학습의 특정 하이퍼 *매개 변수* 를 결정 합니다. 서로 다른 학습 설정에 대해 학습 된 동일한 데이터의 두 모델은 서로 다릅니다.

### <a name="import-and-export-learning-policies"></a>학습 정책 가져오기 및 내보내기

Azure Portal에서 학습 정책 파일을 가져오고 내보낼 수 있습니다. 이렇게 하면 기존 정책을 저장하고 테스트하고 바꾸고 나중에 참조 및 감사하기 위한 아티팩트로 소스 코드 컨트롤에 보관할 수 있습니다.

### <a name="learning-policy-settings"></a>학습 정책 설정

**학습 정책**의 설정은 변경할 수 없습니다. Personalizer에 미치는 영향을 이해하고 있는 경우에만 설정을 변경하세요. 이러한 지식 없이 설정을 변경하면 Personalizer 모델 무효화를 포함한 부작용이 발생합니다.

### <a name="comparing-effectiveness-of-learning-policies"></a>학습 정책의 유효성 비교

[오프라인 평가](concepts-offline-evaluation.md)를 수행하여 Personalizer 로그의 과거 데이터를 기준으로 서로 다른 학습 정책이 수행된 방법을 비교할 수 있습니다.

현재 학습 정책과 비교하려면 [자신의 고유한 학습 정책](how-to-offline-evaluation.md)을 업로드합니다.

### <a name="discovery-of-optimized-learning-policies"></a>최적화된 학습 정책 검색

Personalizer는 [오프라인 평가](how-to-offline-evaluation.md)를 수행할 때 더 최적화된 학습 정책을 만들 수 있습니다. 더 최적화된 학습 정책은 오프라인 평가에 더 나은 보상을 받도록 표시되며, Personalizer에서 온라인으로 사용되는 경우 더 나은 결과를 얻습니다.

최적화된 학습 정책이 만들어진 후 해당 정책을 Personalizer에 직접 적용하여 현재 정책을 즉시 바꾸거나 나중에 평가하기 위해 저장하고 나중에 제거, 저장 또는 적용 여부를 결정할 수 있습니다.
