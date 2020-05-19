---
title: 수련 여 모드-Personalizer
description: ''
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 2697ab4b32edbd4841f2b11725fda46e90e7ae7e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599576"
---
# <a name="use-apprentice-mode-to-train-personalizer-without-affecting-your-existing-application"></a>기존 응용 프로그램에 영향을 주지 않고 Personalizer를 학습 하려면 수련 한 모드를 사용 합니다.

**실제** 보충 Learning의 특성으로 인해 프로덕션 환경 에서만 Personalizer 모델을 학습 시킬 수 있습니다. 새 사용 사례를 배포할 때 모델을 충분히 학습 하는 데 시간이 걸리므로 Personalizer 모델은 효율적으로 수행 되지 않습니다.  **수련 한 모드** 는 이러한 상황을 용이 하 게 하 고 개발자가 코드를 변경 하지 않고도 모델에서 확신을 얻을 수 있도록 하는 학습 동작입니다.

[!INCLUDE [Important Blue Box - Apprentice mode pricing tier](./includes/important-apprentice-mode.md)]

## <a name="what-is-apprentice-mode"></a>수련 수련 이란?

수련 생이 마스터에서 학습 하는 방식과 비슷하며 경험을 통해 더 나은 성능을 얻을 수 있습니다. 수련 Personalizer는 기존 응용 프로그램 논리에서 얻은 결과를 관찰 하 여 학습할 수 있는 _동작_ 입니다.

Personalizer는 응용 프로그램과 같은 출력을 모방 하 여 학습 합니다. 더 많은 이벤트 흐름에서 Personalizer는 기존 논리 및 결과에 영향을 주지 않고 기존 응용 프로그램을 _파악할_ 수 있습니다. Azure Portal 및 API에서 사용할 수 있는 메트릭은 모델에서 학습 하는 성능을 이해 하는 데 도움이 됩니다.

Personalizer를 학습 하 고 특정 수준의 이해를 획득 한 후에는 개발자가 해당 동작을 수련 여에서 온라인 모드로 변경할 수 있습니다. 이 시점에서 Personalizer는 Rank API의 작업에 영향을 시작 합니다.

## <a name="purpose-of-apprentice-mode"></a>수련 생 모드의 용도

수련 여 Personalizer 서비스 및 해당 기계 학습 기능에 신뢰를 제공 하 고, 온라인 트래픽 위험 없이 서비스에서 확인할 수 있는 정보를 작업 제공 합니다.

수련 생 모드를 사용 하는 두 가지 주요 이유는 다음과 같습니다.

* **콜드 시작**완화: 수련 여가 "새로운" 모델의 학습 시간에 대 한 비용을 관리 하 고 평가 하는 데 도움이 75-85 됩니다.
* **작업 및 컨텍스트 기능 유효성 검사**: 작업 및 컨텍스트에 전송 된 기능이 부적절 하거나 부정확 하거나 너무 많거나, 너무 많거나, 잘못 되었거나, 너무 Personalizer 교육 하 여 이상적인 효율성을 얻을 수 있습니다. 기능 [평가](concept-feature-evaluation.md) 를 사용 하 여 기능 관련 문제를 찾고 해결할 수 있습니다.

## <a name="when-should-you-use-apprentice-mode"></a>수련 여를 사용 해야 하는 경우

Personalizer를 학습 하 여 다음과 같은 시나리오에서 효율성을 개선 하는 동시에 Personalizer의 영향을 받지 않는 사용자 환경을 유지 하는 것입니다.

* 새 사용 사례에서 Personalizer을 구현 하 고 있습니다.
* 컨텍스트 또는 작업에서 전송 하는 기능을 크게 변경 했습니다.
* 보상을 계산 하는 방법과 방법이 크게 변경 되었습니다.

수련 여 Personalizer가 보상 점수에 미치는 영향을 측정 하는 효과적인 방법이 아닙니다. 각 순위 호출에 대해 가장 적합 한 작업을 선택 하는 것이 효과적인 Personalizer을 측정 하려면 [오프 라인 평가](concepts-offline-evaluation.md)를 사용 합니다.

## <a name="who-should-use-apprentice-mode"></a>누가 수련 생 모드를 사용 해야 하나요?

수련 한 모드는 개발자, 데이터 과학자 및 비즈니스 의사 결정자에 게 유용 합니다.

* **개발자** 는 수련 여 순위 및 보상 api가 응용 프로그램에서 올바르게 사용 되 고 있는지, 응용 프로그램에서 Personalizer로 전송 되는 기능에 버그 또는 타임 스탬프 또는 UserID 요소와 같은 관련 없는 기능이 포함 되어 있는지 확인 하기 위해 수련 여를 사용할 수 있습니다.

* **데이터 과학자** 는 수련 여가 Personalizer 모델을 학습 하는 데 효과적이 고 보상 대기 시간이 너무 길거나 짧으면 안 됨을 확인할 수 있습니다.

* **비즈니스 의사 결정권자** 는 기존 비즈니스 논리와 비교 하 여 결과 (예: 보상)를 향상 시키기 위해 Personalizer의 가능성을 평가할 수 있습니다. 이렇게 하면 실제 수익과 사용자 만족도가 stake 되는 사용자 환경에 영향을 주는 의사 결정을 내릴 수 있습니다.

## <a name="comparing-behaviors---apprentice-mode-and-online-mode"></a>동작 비교-수련 a모드 및 온라인 모드

다음과 같은 방법으로, 수련 여가 온라인 모드와 다른 경우를 학습 합니다.

|영역|수련 여 모드|온라인 모드|
|--|--|--|
|사용자 경험에 미치는 영향|기존 사용자 동작을 사용 하 여 **기본 작업이** 수행 된 것과 획득 한 보상을 관찰 (영향을 주지 않음) 하도록 하 여 Personalizer를 교육할 수 있습니다. 즉, 사용자의 경험과 비즈니스 결과는 영향을 받지 않습니다.|사용자 동작에 영향을 주기 위해 Rank 호출에서 반환 된 상위 작업을 표시 합니다.|
|학습 속도|Personalizer은 온라인 모드로 학습 하는 경우를 제외 하 고는 더 느리게 설명 합니다. 수련 여은 탐색이 수행 될 수 없으므로 학습 속도를 제한 하는 **기본 작업**에서 얻은 보상을 관찰 하 여 학습할 수 있습니다.|는 현재 모델을 활용 하 고 새로운 추세를 탐색할 수 있기 때문에 더 빠르게 학습 합니다.|
|"천장" 학습 효율성|Personalizer는 대략적으로 거의 일치 하지 않으며 기본 비즈니스 논리 (각 순위 호출의 **기본 작업** 에서 달성 한 보상 합계)의 성능을 초과할 수 없습니다.|Personalizer는 응용 프로그램 기준을 초과 해야 하며, 지연 되는 시간에 따라 모델에 대 한 향상 된 기능을 계속 사용할 수 있도록 오프 라인 평가 및 기능 평가를 수행 해야 합니다. |
|RewardActionId에 대 한 순위 API 값|_RewardActionId_ 는 항상 순위 요청에서 전송 하는 첫 번째 작업 이므로 사용자 환경이 영향을 받지 않습니다. 즉, 순위 API는 응용 프로그램에 대해 수련 여가 표시 되지 않습니다. 응용 프로그램의 보상 Api는 한 모드와 다른 모드 사이에 보상 API를 사용 하는 방법을 변경 해서는 안 됩니다.|사용자 환경은 응용 프로그램에 대해 Personalizer에서 선택 하는 _rewardActionId_ 에서 변경 됩니다. |
|평가|Personalizer는 기본 비즈니스 논리가 가져오는 보상 합계를 비교 하 고, 해당 시점에 온라인 모드에서 보상 합계 Personalizer을 가져옵니다. 비교는 해당 리소스에 대 한 Azure Portal에서 사용할 수 있습니다.|[오프 라인 평가](concepts-offline-evaluation.md)를 실행 하 여 Personalizer의 효율성을 평가 합니다 .이를 통해 Personalizer는 응용 프로그램 기준의 잠재적 보상에 대해 달성 한 총 보상을 비교할 수 있습니다.|

수련 생 모드의 효율성에 대 한 참고 사항:

* 수련 Personalizer의 효과는 응용 프로그램 기준선의 100%에 거의 도달 하지 않습니다. 그리고이를 초과 하지 않습니다.
* 모범 사례는 100% attainment;을 (를) 가져오지 않는 것입니다. 그러나 75 – 85%의 범위는 사용 사례에 따라 대상으로 지정 되어야 합니다.

## <a name="using-apprentice-mode-to-train-with-historical-data"></a>기록 데이터를 사용 하 여 학습 하기 위해 수련 생 모드 사용

많은 양의 기록 데이터가 있는 경우를 사용 하 여 Personalizer를 학습 하려는 경우에는 Personalizer를 통해 데이터를 재생 하기 위해 수련 배열을 사용할 수 있습니다.

Personalizer를 수련 여 설정 하 고 기록 데이터의 작업 및 컨텍스트 기능을 사용 하 여 Rank를 호출 하는 스크립트를 만듭니다. 이 데이터의 레코드 계산을 기반으로 보상 API를 호출 합니다. 일부 결과를 보려면 약 5만 기록 이벤트가 필요 하지만 결과에서 신뢰도를 높이기 위해 50만이 권장 됩니다.

기록 데이터에서 학습할 때 전송 되는 데이터 (컨텍스트 및 작업에 대 한 기능, 순위 요청에 사용 되는 JSON의 레이아웃 및이 학습 데이터 집합의 보상 계산)는 기존 응용 프로그램에서 사용할 수 있는 데이터 (보상의 기능 및 계산)와 일치 하는 것이 좋습니다.

오프 라인 및 사실상 데이터는 더 완전 하지 않고 noisier 되 고 형식이 다를 수 있습니다. 기록 데이터의 교육은 가능 하지만,이로 인해 결과가 불충분 하 게 되 고, 특히 이전 데이터와 기존 응용 프로그램 간에 기능이 다른 경우에는 Personalizer가 얼마나 잘 이해할 수 있는지에 대 한 좋은 예측은 아닙니다.

일반적으로 Personalizer의 경우 기록 데이터로 학습 하는 것과 비교 했을 때 동작을 수련 생 모드로 변경 하 고 기존 응용 프로그램에서 학습 하는 것이 효과적인 모델을 포함 하는 보다 효과적인 경로입니다.

## <a name="using-apprentice-mode-versus-ab-tests"></a>A/B 테스트와 비교 하 여 수련 여 사용

유효성을 검사 하 고 온라인 모드에서 학습 하는 경우 Personalizer 처리가의 A/B 테스트를 수행 하는 데만 유용 합니다. 수련 한 모드에서는 **기본 동작만** 사용 됩니다. 즉, 모든 사용자가 제어 환경을 효과적으로 볼 수 있습니다.

Personalizer만 _처리_하는 경우에도 데이터의 유효성 검사가 Personalizer 학습에 적합 한 경우에도 동일한 과제가 제공 됩니다. 대신 100%의 트래픽을 포함 하 고 모든 사용자가 제어 (영향을 받지 않음) 환경을 얻는 경우에는 수련 한 모드를 사용할 수 있습니다.

Personalizer 및 learning을 사용 하는 사용 사례가 있으면 A/B 실험을 통해 보상에 사용 되는 신호 보다 더 복잡 한 결과의 제어 된 코 호트 및 과학적 비교를 수행할 수 있습니다. A/B 테스트에서 답변할 수 있는 예제 질문은 다음과 같습니다.`In a retail website, Personalizer optimizes a layout and gets more users to _check out_ earlier, but does this reduce total revenue per transaction?`

## <a name="next-steps"></a>다음 단계

* [활성 및 비활성 이벤트](concept-active-inactive-events.md) 에 대해 알아보기