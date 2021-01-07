---
title: 이벤트 기반 비디오 녹화를 위한 신호 게이트 구성-Azure
description: 이 문서에서는 미디어 그래프에서 신호 게이트를 구성 하는 방법에 대 한 지침을 제공 합니다.
ms.topic: how-to
ms.date: 11/3/2020
ms.openlocfilehash: afcec7c03f1353f08b58311278f5a533e0c911bc
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410796"
---
# <a name="configure-a-signal-gate-for-event-based-video-recording"></a>이벤트 기반 비디오 녹화를 위한 신호 게이트 구성

미디어 그래프 내에서 [신호 게이트 프로세서 노드](media-graph-concept.md#signal-gate-processor) 를 사용 하 여 게이트가 이벤트에 의해 트리거될 때 한 노드에서 다른 노드로 미디어를 전달할 수 있습니다. 트리거되는 경우 게이트가 열리고 지정 된 기간 동안 미디어를 통과할 수 있습니다. 게이트를 트리거하기 위한 이벤트가 없으면 게이트가 닫히고 미디어 흐름이 중단 됩니다. 이벤트 기반 비디오 기록에는 신호 게이트 프로세서를 사용할 수 있습니다.

이 문서에서는 신호 게이트 프로세서를 구성 하는 방법을 알아봅니다.

## <a name="suggested-prereading"></a>제안 된 prereading
-   [미디어 그래프](media-graph-concept.md)
-   [이벤트 기반 비디오 녹화](event-based-video-recording-concept.md)


## <a name="problem"></a>문제
사용자가 게이트를 이벤트에 의해 트리거하기 전이나 후에 특정 시간에 기록을 시작 해야 할 수 있습니다. 사용자가 시스템 내에서 허용 되는 대기 시간을 알고 있습니다. 따라서 신호 게이트 프로세서의 대기 시간을 지정 하려고 합니다. 또한 수신 되는 새 이벤트 수에 관계 없이 기록의 최소 및 최대 기간을 지정 하려고 합니다.
 
### <a name="use-case-scenario"></a>사용 사례 시나리오
건물의 앞 도어를 열 때마다 비디오를 기록 하려고 한다고 가정 합니다. 다음에 대 한 기록을 원합니다. 

- 문이 열리기 전에 *X* 초를 포함 합니다. 
- 도어가 다시 열리지 않는 경우 마지막으로 *Y* 초 이상입니다. 
- 도어가 반복 해 서 열린 경우 마지막 *Z* 초입니다. 
 
도어 센서의 대기 시간이 *K* 초 임을 알고 있습니다. 이벤트가 지연 도착 한 무시 되는 가능성을 줄이려면 이벤트가 도착할 때까지 최소 *K* 초를 허용 하려고 합니다.


## <a name="solution"></a>해결 방법

문제를 해결 하려면 신호 게이트 프로세서 매개 변수를 수정 합니다.

신호 게이트 프로세서를 구성 하려면 다음 네 가지 매개 변수를 사용 합니다.
- 활성화 평가 창
- 활성화 신호 오프셋
- 최소 활성화 기간
- 최대 활성화 기간

신호 게이트 프로세서가 트리거되면 최소 활성화 시간 동안 열린 상태로 유지 됩니다. 활성화 이벤트는 가장 이른 이벤트의 타임 스탬프와 활성화 신호 오프셋에서 시작 됩니다. 

열린 상태에서 신호 게이트 프로세서를 다시 트리거하면 타이머가 다시 설정 되 고 최소 최소 정품 인증 시간 동안 게이트가 계속 열려 있습니다. 신호 게이트 프로세서는 최대 활성화 시간 보다 길게 열린 상태로 유지 되지 않습니다. 

시스템 지연 및 이벤트 1이 이벤트 2 후 신호 게이트 프로세서에 도착 하면 미디어 타임 스탬프를 기반으로 하 여 다른 이벤트 (이벤트 2) 이전에 발생 하는 이벤트 (이벤트 1)가 무시 될 수 있습니다. 이벤트 2와 활성화 평가 창의 도착 사이에 이벤트 1이 도착 하지 않으면 이벤트 1은 무시 됩니다. 신호 게이트 프로세서를 통해 전달 되지 않습니다. 

상관 관계 Id는 모든 이벤트에 대해 설정 됩니다. 이러한 Id는 초기 이벤트에서 설정 됩니다. 각 이벤트에 대해 순차적으로 수행 됩니다.

> [!IMPORTANT]
> 미디어 시간은 미디어에서 이벤트가 발생할 때의 미디어 타임 스탬프를 기반으로 합니다. 신호 게이트에 도착 하는 이벤트 시퀀스에는 미디어 시간에 도착 하는 이벤트 시퀀스가 반영 되지 않을 수 있습니다.


### <a name="parameters-based-on-the-physical-time-that-events-arrive-at-the-signal-gate"></a>매개 변수는 이벤트가 신호 게이트에 도착 하는 실제 시간을 기반으로 합니다.

* **minimumActivationTime (기록의 가능한 최단 시간)** : maximumActivationTime에 의해 중단 된 경우를 제외 하 고 새 이벤트를 수신 하기 위해 트리거된 후 신호 게이트 프로세서를 열어 놓은 최소 시간 (초)입니다.
* **maximumActivationTime (기록의 가장 긴 기간)** : 받은 이벤트에 관계 없이 새 이벤트를 수신 하기 위해 트리거된 후 신호 게이트 프로세서에서 열린 상태로 유지 되는 초기 이벤트의 최대 시간 (초)입니다.
* **activationSignalOffset** : 신호 게이트 프로세서를 활성화 하는 시간과 비디오 녹화 시작 사이의 시간 (초)입니다. 일반적으로이 값은 트리거 이벤트 이전에 기록을 시작 하기 때문에 음수입니다.
* **activationEvaluationWindow** : 초기 트리거 이벤트에서 시작 하 여 미디어 시간에 초기 이벤트 이전에 발생 한 이벤트가 무시 되 고 늦게 도착 한 것으로 간주 되기 전에 신호 게이트 프로세서에 도착 해야 하는 시간 (초)입니다.

> [!NOTE]
> *지연 도착* 은 활성화 평가 기간이 경과 된 후에 발생 하는 이벤트 이지만 미디어 시간에서 초기 이벤트 앞에 도착 합니다.

### <a name="limits-of-parameters"></a>매개 변수 제한

* **activationEvaluationWindow** : 0 초 ~ 10 초
* **activationSignalOffset** :-1 분 ~ 1 분
* **minimumActivationTime** : 1 초 ~ 1 시간
* **maximumActivationTime** : 1 초 ~ 1 시간


사용 사례에서 다음과 같이 매개 변수를 설정 합니다.

* **activationEvaluationWindow** : *K* 초
* **activationSignalOffset** : *-X* 초
* **minimumActivationWindow** : *Y* 초
* **maximumActivationWindow** : *Z* 초


다음은 **신호 게이트 프로세서** 노드 섹션에서 다음 매개 변수 값에 대 한 미디어 그래프 토폴로지를 확인 하는 방법의 예입니다.
* **activationEvaluationWindow** : 1 초
* **activationSignalOffset** :-5 초
* **minimumActivationTime** : 20 초
* **maximumActivationTime** : 40 초

> [!IMPORTANT]
> 각 매개 변수 값에 [ISO 8601 기간 형식이](https://en.wikipedia.org/wiki/ISO_8601#Durations
) 필요 합니다. 예를 들어 PT1S = 1 초입니다.


```
"processors":              
[
          {
            "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
            "name": "signalGateProcessor",
            "inputs": [
              {
                "nodeName": "iotMessageSource"
              },
              {
                "nodeName": "rtspSource"
              }
            ],
            "activationEvaluationWindow": "PT1S",
            "activationSignalOffset": "-PT5S",
            "minimumActivationTime": "PT20S",
            "maximumActivationTime": "PT40S"
          }
]
```


이제 다양 한 기록 시나리오에서이 신호 게이트 프로세서 구성이 동작 하는 방식을 고려 합니다.

### <a name="recording-scenarios"></a>시나리오 기록

**한 소스의 한 이벤트 ( *일반 정품 인증* )**

하나의 이벤트를 수신 하는 신호 게이트 프로세서는 이벤트가 게이트에 도착 하기 전에 5 초 (활성화 신호 = 5 초)를 시작 하는 기록을 생성 합니다. 최소 활성화 시간이 끝나기 전에 다른 이벤트가 도착 하지 않으므로 나머지 기록은 20 초 (최소 활성화 시간 = 20 초)가 됩니다.

예제 다이어그램:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/normal-activation.svg" alt-text="한 소스에서 한 이벤트의 정상적인 활성화를 보여 주는 다이어그램입니다.":::

* 기록 기간 =-offset + minimumActivationTime = [E1 + offset, E1 + minimumActivationTime]


**한 소스의 두 이벤트 ( *retriggered activation* )**

두 이벤트를 수신 하는 신호 게이트 프로세서는 이벤트가 게이트에 도착 하기 전에 5 초 (활성화 신호 오프셋 = 5 초)를 시작 하는 기록을 생성 합니다. 또한 이벤트 2는 이벤트 1 다음에 5 초 후에 도착 합니다. 이벤트 2는 이벤트 1의 최소 활성화 시간 (20 초)의 끝에 도달 하기 때문에 게이트는 retriggered 됩니다. 기록의 나머지 부분은 20 초 (최소 활성화 시간 = 20 초)입니다. 이벤트 2의 최소 활성화 시간이 끝나기 전에 다른 이벤트가 도착 하지 않으므로 다시 트리거할.

예제 다이어그램:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/retriggering-activation.svg" alt-text="한 소스에서 두 이벤트의 retriggered 활성화를 보여 주는 다이어그램입니다.":::

* 기록 기간 =-오프셋 + (이벤트 2 도착-이벤트 1 도착) + minimumActivationTime


**한 원본의 이벤트 *N* 개 ( *최대 활성화* )**

*N 개* 이벤트를 수신 하는 신호 게이트 프로세서는 첫 번째 이벤트가 게이트에서 도착 하기 전에 5 초 (활성화 신호 오프셋 = 5 초)를 시작 하는 기록을 생성 합니다. 이전 이벤트에서 최대 활성화 시간이 20 초인 후에 각 이벤트가 도착 하면이 게이트는 지속적으로 retriggered 됩니다. 첫 번째 이벤트 이후 40 초의 최대 활성화 시간이 될 때까지 열린 상태로 유지 됩니다. 그러면 게이트가 닫히고 새 이벤트를 더 이상 수락 하지 않습니다.

예제 다이어그램:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/maximum-activation.svg" alt-text="한 원본의 최대 N 개 이벤트 활성화를 보여 주는 다이어그램":::
 
* 기록 기간 =-오프셋 + maximumActivationTime

> [!IMPORTANT]
> 위의 다이어그램에서는 모든 이벤트가 동일한 순간에 물리적 시간과 미디어 시간에 도착 하는 것으로 가정 합니다. 즉, 지연 된 도착 한 없는 것으로 가정 합니다.

## <a name="next-steps"></a>다음 단계

[이벤트 기반 비디오 기록 자습서](event-based-video-recording-tutorial.md)를 사용해 보세요. [에서topology.js](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json)을 편집 하 여 시작 합니다. SignalgateProcessor 노드에 대 한 매개 변수를 수정한 다음 자습서의 나머지 단계를 수행 합니다. 비디오 녹화를 검토 하 여 매개 변수의 효과를 분석 합니다.



