---
title: 활성 학습 - Personalizer
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: edjez
ms.openlocfilehash: c44afc81a7ec9d05cdc04cc8bc46c77cd51ceaf8
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722522"
---
# <a name="active-learning-and-learning-policies"></a>활성 학습 및 학습 정책 

애플리케이션이 Rank API를 호출하는 경우 콘텐츠의 순위를 받습니다. 비즈니스 논리는 이 순위를 사용하여 콘텐츠가 사용자에게 표시되어야 하는지 여부를 결정할 수 있습니다. 순위 지정 콘텐츠를 표시하면 이는 _활성_ 순위 이벤트입니다. 애플리케이션이 해당 순위 지정 콘텐츠를 표시하지 않으면 이는 _비활성_ 순위 이벤트입니다. 

활성 순위 이벤트 정보는 Personalizer에 반환됩니다. 이 정보는 현재 학습 정책을 통해 모델 교육을 계속하기 위해 사용됩니다.

## <a name="active-events"></a>활성 이벤트

활성 이벤트는 항상 사용자에게 표시되어야 하며 학습 루프를 닫으려면 보상 호출을 반환해야 합니다. 

### <a name="inactive-events"></a>비활성 이벤트 

비활성 이벤트는 사용자에게 순위 지정 콘텐츠에서 선택할 기회가 주어지지 않았기 때문에 기본 모델을 변경하지 않아야 합니다.

## <a name="dont-train-with-inactive-rank-events"></a>비활성 순위 이벤트를 사용하여 학습하지 마세요. 

일부 애플리케이션의 경우 애플리케이션이 결과를 사용자에게 표시할지 여부를 아직 알지 못한 상태에서 Rank API를 호출해야 할 수 있습니다. 

이런 상황은 다음과 같은 경우에 발생합니다.

* 사용자가 볼 수 있거나 볼 수 없는 일부 UI를 미리 렌더링할 수 있습니다. 
* 애플리케이션은 실시간 특성이 적은 상황에서 Rank를 호출하는 예상 개인 설정을 수행할 수 있으며 해당 출력을 애플리케이션에서 사용할 수도 있고 사용하지 않을 수도 있습니다. 

### <a name="disable-active-learning-for-inactive-rank-events-during-rank-call"></a>Rank 호출 중에 비활성 순위 이벤트에 대해 활성 학습을 사용하지 않도록 설정

자동 학습을 사용하지 않도록 설정하려면 `learningEnabled = False`를 사용하여 Rank를 호출합니다.

비활성 이벤트에 대한 학습은 Rank에 대해 보상을 보내는 경우 묵시적으로 활성화됩니다.

## <a name="learning-policies"></a>학습 정책

학습 정책은 모델 학습의 특정 *하이퍼 매개 변수*를 결정합니다. 서로 다른 학습 정책에 따라 학습한 같은 데이터의 두 모델은 서로 다르게 작동합니다.

### <a name="importing-and-exporting-learning-policies"></a>학습 정책 가져오기 및 내보내기

Azure Portal에서 학습 정책 파일을 가져오고 내보낼 수 있습니다. 이렇게 하면 기존 정책을 저장하고 테스트하고 바꾸고 나중에 참조 및 감사하기 위한 아티팩트로 소스 코드 컨트롤에 보관할 수 있습니다.

### <a name="learning-policy-settings"></a>학습 정책 설정

**학습 정책**의 설정은 변경할 수 없습니다. Personalizer에 미치는 영향을 이해하고 있는 경우에만 설정을 변경하세요. 이러한 지식 없이 설정을 변경하면 Personalizer 모델 무효화를 포함한 부작용이 발생합니다.

### <a name="comparing-effectiveness-of-learning-policies"></a>학습 정책의 유효성 비교

[오프라인 평가](concepts-offline-evaluation.md)를 수행하여 Personalizer 로그의 과거 데이터를 기준으로 서로 다른 학습 정책이 수행된 방법을 비교할 수 있습니다.

현재 학습 정책과 비교하려면 [자신의 고유한 학습 정책](how-to-offline-evaluation.md)을 업로드합니다.

### <a name="discovery-of-optimized-learning-policies"></a>최적화된 학습 정책 검색

Personalizer는 [오프라인 평가](how-to-offline-evaluation.md)를 수행할 때 더 최적화된 학습 정책을 만들 수 있습니다. 더 최적화된 학습 정책은 오프라인 평가에 더 나은 보상을 받도록 표시되며, Personalizer에서 온라인으로 사용되는 경우 더 나은 결과를 얻습니다.

최적화된 학습 정책이 만들어진 후 해당 정책을 Personalizer에 직접 적용하여 현재 정책을 즉시 바꾸거나 나중에 평가하기 위해 저장하고 나중에 제거, 저장 또는 적용 여부를 결정할 수 있습니다.