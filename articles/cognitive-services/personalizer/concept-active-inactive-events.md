---
title: 활성 및 비활성 이벤트-Personalizer
description: 이 문서에서는 Personalizer 서비스 내에서 활성 및 비활성 이벤트를 사용 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: a8f27542208965e2b820b9fc45cfcc5353a7f193
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624321"
---
# <a name="active-and-inactive-events"></a>활성 및 비활성 이벤트

**활성** 이벤트는 고객에 게 결과를 표시 하 고 보상 점수를 확인할 수 있는 순위에 대 한 모든 호출입니다. 기본 동작입니다.

**비활성** 이벤트는 비즈니스 논리로 인해 사용자에 게 권장 되는 작업이 표시 되는지 확신할 수 없는 순위에 대 한 호출입니다. 이를 통해 Personalizer 기본 보상으로 학습 되지 않도록 이벤트를 취소할 수 있습니다. 비활성 이벤트는 보상 API를 호출 하면 안 됩니다.

학습 루프는 실제 이벤트 유형을 알고 있어야 합니다. 비활성 이벤트에는 보상 호출이 없습니다. 활성 이벤트에는 보상 호출이 있어야 하지만 API 호출을 수행 하지 않으면 기본 보상 점수가 적용 됩니다. 사용자 환경에 영향을 주는 것이 확인 되는 즉시 이벤트의 상태를 비활성에서 활성으로 변경 합니다.

## <a name="typical-active-events-scenario"></a>일반적인 활성 이벤트 시나리오

응용 프로그램이 Rank API를 호출 하면 응용 프로그램이 **rewardActionId** 필드에 표시 해야 하는 동작이 표시 됩니다.  이 순간부터 Personalizer는 동일한 eventId를 갖는 보상 점수가 있는 보상 전화를 기대 합니다. 보상 점수는 이후 순위 호출을 위해 모델을 학습 하는 데 사용 됩니다. EventId에 대해 보상 호출이 수신 되지 않으면 기본 보상을 적용 합니다. [기본 보상](how-to-settings.md#configure-rewards-for-the-feedback-loop) 은 Azure Portal에서 Personalizer 리소스에 대해 설정 됩니다.

## <a name="other-event-type-scenarios"></a>기타 이벤트 유형 시나리오

일부 시나리오에서는 응용 프로그램이 결과를 사용 하거나 사용자에 게 표시할지 여부를 파악 하기 전에 Rank를 호출 해야 할 수 있습니다. 이는 예를 들어 마케팅 캠페인에서 승격 된 콘텐츠의 페이지 렌더링을 덮어쓰는 경우에 발생할 수 있습니다. Rank 호출의 결과가 사용 되지 않은 경우 사용자가이를 확인 하지 않으면 해당 보상 통화를 보내지 않습니다.

일반적으로 이러한 시나리오는 다음과 같은 경우에 발생 합니다.

* 사용자가 표시 하거나 볼 수 없는 렌더링 하지 않는 UI입니다.
* 응용 프로그램이 거의 실시간 컨텍스트로 순위를 설정 하 고 응용 프로그램에서 출력을 사용 하거나 사용 하지 않을 때 예측 개인 설정을 수행 하 고 있습니다.

이러한 경우 Personalizer를 사용 하 여 Rank를 호출 하 고 이벤트를 _비활성화_하도록 요청 합니다. Personalizer는이 이벤트에 대 한 보상을 받을 수 없으며 기본 보상을 적용 하지 않습니다.

비즈니스 논리에서 나중에 응용 프로그램에서 순위 호출의 정보를 사용 하는 경우에는 이벤트를 _활성화_ 하기만 하면 됩니다. 이벤트가 활성화 되는 즉시 Personalizer는 이벤트 보상을 기대 합니다. 보상 API에 대 한 명시적 호출이 없으면 Personalizer는 기본 보상을 적용 합니다.

## <a name="inactive-events"></a>비활성 이벤트

이벤트에 대 한 학습을 사용 하지 않도록 설정 하려면 `learningEnabled = False`을 사용 하 여 순위를 호출 합니다.

비활성 이벤트의 경우 이벤트 Id에 대 한 보상을 보내거나 해당 eventId에 대해 `activate` API를 호출 하면 학습이 암시적으로 활성화 됩니다.

## <a name="next-steps"></a>다음 단계

* [보상 점수와 고려할 데이터를 결정 하는 방법](concept-rewards.md)에 대해 알아봅니다.
