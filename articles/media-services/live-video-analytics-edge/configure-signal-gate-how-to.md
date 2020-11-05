---
title: 이벤트 기반 비디오 녹화를 위한 신호 게이트 구성-Azure
description: 이 문서에서는 미디어 그래프에서 신호 게이트를 구성 하는 방법에 대 한 지침을 제공 합니다.
ms.topic: how-to
ms.date: 11/3/2020
ms.openlocfilehash: 4204a43915f9c79cae7dfe82b37e741fee89fb4a
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93380244"
---
# <a name="configure-a-signal-gate-for-event-based-video-recording"></a>이벤트 기반 비디오 녹화를 위한 신호 게이트 구성

미디어 그래프 내에서 [신호 게이트 프로세서 노드](media-graph-concept.md#signal-gate-processor) 를 사용 하면 이벤트가 이벤트에 의해 트리거될 때 한 노드에서 다른 노드로 미디어를 전달할 수 있습니다. 트리거되는 경우 게이트가 열리고 지정 된 기간 동안 미디어를 통과할 수 있습니다. 게이트를 트리거하기 위한 이벤트가 없으면 게이트가 닫히고 미디어 흐름이 중단 됩니다. 신호 게이트 프로세서는 이벤트 기반 비디오 기록에 적용 됩니다.
이 문서에서는 신호 게이트 프로세서를 구성 하는 방법에 대해 자세히 설명 합니다.

## <a name="suggested-pre-reading"></a>추천 참고 자료
-   [미디어 그래프](media-graph-concept.md)
-   [이벤트 기반 비디오 녹화](event-based-video-recording-concept.md)


## <a name="problem"></a>문제
사용자가 게이트가 이벤트에 의해 트리거 되기 전이나 후에 특정 시간 기록을 시작할 수 있습니다. 사용자가 시스템 내에서 허용 되는 대기 시간을 알고 있으므로 사용자는 신호 게이트 프로세서의 대기 시간을 지정 하려고 합니다. 사용자는 수신 되는 새 이벤트 수에 관계 없이 기록 기간이 가장 짧은 시간을 지정 하려고 합니다.
 
### <a name="use-case-scenario"></a>사용 사례 시나리오
건물의 앞 도어를 열 때마다 비디오를 기록 하려고 한다고 가정 합니다. 도어를 열 때까지 **X** 초가 기록에 포함 되도록 합니다. 도어가 다시 열리지 않는 경우 마지막으로 **Y** 초 이상 기록을 원합니다. 도어가 반복 해 서 열린 경우 최근 **Z** 초에 기록을 원합니다. 도어 센서의 대기 시간이 **k** 초이 고 이벤트를 무시 하는 경우 ("후기 도착 한"), 이벤트가 도착할 때까지 최소 **K** 초를 허용 하려는 경우를 확인 합니다.


## <a name="solution"></a>해결 방법

**_신호 게이트 프로세서 매개 변수 수정_* _

신호 게이트 프로세서는 4 개의 매개 변수로 구성 됩니다.
- _ *활성화 평가 창**
- **활성화 신호 오프셋**
- **최소 활성화 기간**
- **최대 활성화 기간** 입니다. 

신호 게이트 프로세서가 트리거되면 최소 정품 인증 시간 동안 열린 상태로 유지 됩니다. 활성화 이벤트는 가장 이른 이벤트의 타임 스탬프와 활성화 신호 오프셋에서 시작 합니다. 신호 게이트 프로세서를 다시 트리거하면 타이머가 열려 있는 동안 타이머가 다시 설정 되 고 최소 최소 정품 인증 시간 동안 게이트가 계속 열려 있습니다. 신호 게이트 프로세서는 최대 활성화 시간 보다 길게 열린 상태로 유지 되지 않습니다. 미디어 타임 스탬프를 기반으로 하는 다른 이벤트 **(이벤트 2)** 이전에 발생 하는 이벤트 **(이벤트 1)** 는 시스템 지연 및 **이벤트 1** 이 **이벤트 2** 후 신호 게이트 프로세서에 도착할 경우 무시 될 수 있습니다. 이벤트 **2** 와 **활성화 평가 창의** 도착 사이에 **이벤트 1** 이 도착 하지 않으면 **이벤트 1** 은 무시 되 고 신호 게이트 프로세서를 통해 전달 되지 않습니다. 상관 관계 Id는 모든 이벤트에 대해 설정 됩니다. 이러한 Id는 초기 이벤트에서 설정 되며 다음 각 이벤트에 대해 순차적으로 수행 됩니다.

> [!IMPORTANT]
> 미디어 시간은 미디어에서 이벤트가 발생 했을 때의 미디어 타임 스탬프를 기반으로 합니다. 신호 게이트에 도착 하는 이벤트 시퀀스는 미디어 시간에 도착 하는 이벤트의 시퀀스를 반영 하지 않을 수 있습니다.


### <a name="parameters-based-on-when-events-arrive-in-physical-time-to-the-signal-gate"></a>매개 변수: (이벤트가 신호 게이트에 물리적 시간에 도착 하는 시점에 따라)

* **minimumActivationTime (기록의 가능한 최단 시간)** = **maximumActivationTime** 에 의해 중단 되지 않는 한 새 이벤트를 수신 하기 위해 트리거된 후 신호 게이트 프로세서를 열어 둘 수 있는 최소 시간 (초)입니다.
* **maximumActivationTime (기록의 가장 긴 기간)** = 받은 이벤트에 관계 없이 새 이벤트를 수신 하기 위해 트리거된 후 신호 게이트 프로세서에서 열린 상태로 유지 되는 초기 이벤트의 최대 시간 (초)입니다.
* **activationSignalOffset** = 신호 게이트 프로세서를 활성화 하는 시간 사이의 시간 (초)으로, 비디오 기록이 시작 될 때 일반적으로이 값은 음수 이며 트리거 이벤트 전에 기록을 시작 합니다.
* **activationEvaluationWindow** = 초기 트리거 이벤트에서 시작 하는 시간 (초)입니다. 초기 이벤트에서 시작 하 여 미디어 시간에 초기 이벤트 이전에 발생 한 이벤트가 무시 되 고 "지연 도착"으로 간주 되기 전에 신호 게이트 프로세서에 도착 해야 합니다.

> [!NOTE]
> 지연 도착은 활성화 평가 기간이 경과 하면 도착 하지만이 이벤트는 미디어 시간에서 초기 이벤트 이전에 도착 한 경우에 발생 하는 이벤트입니다.

### <a name="limits-of-parameters"></a>매개 변수 제한

* **activationEvaluationWindow: 0 초 ~ 10 초**

* **activationSignalOffset:-1 분 ~ 1 분**

* **minimumActivationTime: 1 초 ~ 1 시간**

* **maximumActivationTime: 1 초 ~ 1 시간**


사용 사례에 따라 매개 변수는 다음과 같이 설정 됩니다.

* **activationEvaluationWindow = K 초**
* **activationSignalOffset =-X 초**
* **minimumActivationWindow = Y 초**
* **maximumActivationWindow = Z 초**


다음은 미디어 그래프 토폴로지에서 다음과 같은 매개 변수 값에 대 한 신호 게이트 프로세서 노드 섹션의 예입니다.
* **activationEvaluationWindow = 1 초**
* **activationSignalOffset =-5 초**
* **minimumActivationTime = 20 초**
* **maximumActivationTime = 40 초**

> [!IMPORTANT]
> 각 매개 변수 값에 [ISO 8601 기간 형식이](https://en.wikipedia.org/wiki/ISO_8601#Durations
) 필요 합니다. 
**예: PT1S = 1 초**


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


다양 한 기록 시나리오에서이 신호 게이트 프로세서 구성이 동작 하는 방식을 살펴보겠습니다.


**1 원본의 이벤트 1 개 ( *일반 정품 인증* )**

하나의 이벤트를 수신 하는 신호 게이트 프로세서는 이벤트가 게이트에 도착 하기 전에 "활성화 신호 오프셋" (5) 초를 시작 하는 기록을 생성 합니다. 기록의 나머지 부분은 게이트를 다시 트리거할 위해 최소 활성화 시간이 완료 되기 전에 다른 이벤트가 도착 하지 않았기 때문에 "최소 활성화 시간" (20) 초 길이입니다.

예제 다이어그램:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/normal-activation.svg" alt-text="일반 정품 인증":::

* 기록 기간 =-offset + minimumActivationTime = [E1 + offset, E1 + minimumActivationTime]


**1 원본에서 이벤트 2 개 ( *Retriggered 활성화* )**

두 이벤트를 수신 하는 신호 게이트 프로세서는 첫 번째 이벤트가 게이트에서 도착 하기 전에 "활성화 신호 오프셋" (5) 초를 시작 하는 기록을 생성 합니다. 두 번째 이벤트는 첫 번째 이벤트 후 5 초 후에 발생 합니다 .이 이벤트는 첫 번째 이벤트에서 "최소 활성화 시간" (20) 초가 완료 되기 전에 발생 합니다. 따라서 게이트는 열린 상태로 retriggered 됩니다. 기록의 나머지 부분은 "최소 활성화 시간" (20) 초 길이 이며, 두 번째 이벤트의 최소 활성화 시간이 완료 되기 전에 다른 이벤트가 도착 하지 않으므로 다시 트리거할.

예제 다이어그램:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/retriggering-activation.svg" alt-text="Retriggered 활성화":::

* 기록 기간 =-오프셋 + (두 번째 이벤트 도착-첫 번째 이벤트 도착) + minimumActivationTime


**1 원본의 이벤트 N 개 ( *최대 활성화* )**

N 개 이벤트를 수신 하는 신호 게이트 프로세서는 첫 번째 이벤트가 게이트에서 도착 하기 전에 "활성화 신호 오프셋" (5) 초를 시작 하는 기록을 생성 합니다. 이전 이벤트에서 "최소 활성화 시간" (20) 초가 완료 되기 전에 각 이벤트가 도착 하면, 첫 번째 이벤트 다음에 "최대 활성화 시간" (40) 초 후에도 게이트가 닫히고 새 이벤트를 더 이상 수락 하지 않을 때까지 retriggered 계속 해 서 열려 있게 됩니다.

예제 다이어그램:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/maximum-activation.svg" alt-text="최대 활성화":::
 
* 기록 기간 =-오프셋 + maximumActivationTime

> [!IMPORTANT]
> 다이어그램은 모든 이벤트가 실제 및 미디어 시간에 동일한 인스턴스에 도착 한다고 가정 합니다. (늦은 도착 한 없음)

## <a name="next-steps"></a>다음 단계

### <a name="try-it-out"></a>사용해 보기

[이벤트 기반 비디오 기록 자습서](event-based-video-recording-tutorial.md)

이벤트 기반 비디오 기록 자습서를 사용 하 여 [topology.js](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json)를 편집 하 고 signalgateProcessor 노드에 대 한 매개 변수를 수정한 다음 자습서의 나머지 단계를 수행 합니다. 비디오 녹화를 검토 하 여 매개 변수의 효과를 분석 합니다.



