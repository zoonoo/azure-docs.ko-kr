---
title: 사용자 환경의 데이터 보존 이해 - Azure 타임시리즈 인사이트 | 마이크로 소프트 문서
description: 이 아티클에서는 Azure Time Series Insights 환경의 데이터 보존 기간을 제어하는 두 가지 설정을 설명합니다.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/10/2020
ms.custom: seodec18
ms.openlocfilehash: 34cf1e91b1fe5aae516c77bf2c280dfe70000611
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894747"
---
# <a name="understand-data-retention-in-azure-time-series-insights"></a>Azure 서열 인사이트내 데이터 보존 이해

이 문서에서는 Azure Time Series Insights 환경에서 데이터 보존에 영향을 주는 두 가지 기본 설정에 대해 설명합니다.

## <a name="video"></a>비디오

### <a name="the-following-video-summarizes-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>다음 비디오에서는 타임시리즈 인사이트 데이터 보존 및 계획 방법을 요약합니다.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

각 Azure Time Series Insights 환경에는 **데이터 보존 시간을**제어하는 설정이 있습니다. 값의 범위는 1~400일입니다. 데이터는 환경 저장소 용량 또는 보존 기간 중 먼저 발생 여부에 따라 삭제됩니다.

또한 Azure Time Series Insights **환경에는 저장소 제한 초과 동작** 설정이 있습니다. 환경의 최대 용량에 도달하면 인드레및 제거 동작을 제어합니다. 구성 할 때 선택할 수있는 두 가지 동작이 있습니다.

- **이전 데이터 삭제**(기본값)  
- **수신 일시 중지**

> [!NOTE]
> 기본적으로 새 환경을 만들면 보존 기간이 **이전 데이터 삭제**로 구성됩니다. 이 설정은 열렬 인사이트 환경의 **구성** 페이지에서 Azure 포털을 사용하여 생성 시간 후에 필요에 따라 전환할 수 있습니다.
> * 보존 정책을 구성하는 방법에 대한 자세한 내용은 [[열렬 인사이트]의 보존 구성을](time-series-insights-how-to-configure-retention.md)참조하십시오.

두 데이터 보존 정책은 아래에 자세히 설명되어 있습니다.

## <a name="purge-old-data"></a>이전 데이터 삭제

- **이전 데이터는** Azure Time Series Insights 환경의 기본 설정입니다.  
- 사용자가 항상 자신의 타임 시리즈 인사이트 환경에서 *가장 최근의 데이터를* 유지하려는 경우 이전 **데이터를 제거하는** 것이 좋습니다.
- **제거 이전 데이터** 설정은 환경의 제한(보존 시간, 크기 또는 개수 중 먼저 오는 값)에 도달하면 데이터를 *제거합니다.* 기본적으로 보존 기간은 30일로 설정됩니다.
- 가장 오래된 수집된 데이터가 먼저 제거됩니다("첫 번째 아웃" 접근 방식).

### <a name="example-one"></a>예 1

보존 동작 **수신을 계속하고 이전 데이터 삭제**가 지정된 예제 환경을 고려해 보세요.

**데이터 보존 시간은** 400일로 설정됩니다. **용량**은 30GB의 총 용량을 포함하는 S1 단위로 설정됩니다. 인바운드 데이터가 매일 평균 500MB 누적된다고 가정하겠습니다. 인바운드 데이터의 속도에 따르면 60일째에 최대 용량에 도달하므로 이 환경은 데이터를 60일만 보존할 수 있습니다. 이 기간 동안 인바운드 데이터의 누적량을 계산해 보면 다음과 같습니다. 500MB x 60일 = 30GB.

61일째에 환경은 가장 최신 데이터를 표시하지만 가장 오래된 데이터는 60일이 넘은 것으로 제거합니다. 삭제를 통해 스트리밍되는 새 데이터를 위한 공간을 확보하여 새 데이터를 계속 탐색할 수 있습니다. 데이터를 더 오래 보존하려면 단위를 추가하여 환경의 규모를 늘리거나 푸시되는 데이터의 양을 줄여야 합니다.  

### <a name="example-two"></a>예제 2

보존 동작이 **수신을 계속하고 이전 데이터 삭제**로 구성되어 있는 환경도 생각해 보세요. 이 예제에서는 **데이터 보존 시간**이 더 낮은 값인 180일로 설정됩니다. **용량**은 30GB의 총 용량을 포함하는 S1 단위로 설정됩니다. 180일 내내 데이터를 저장하려면 일일 수신 규모가 0.166GB(166MB)를 초과하지 않아야 합니다.  

이 환경의 일일 수신 속도가 0.166GB를 초과할 때마다 일부 데이터가 삭제되므로 데이터가 180일 동안 보관되지 않습니다. 이와 동일한 환경을 바쁜 시간대에 사용한다고 생각해 보세요. 환경의 수신 속도가 일 평균 0.189GB로 증가할 수 있다고 가정해 보겠습니다. 이러한 바쁜 시간대에는 데이터가 약 158일 동안 보존됩니다(30GB/0.189 = 158.73일). 원하는 데이터 보존 기간보다 줄어든 기간입니다.

## <a name="pause-ingress"></a>수신 일시 중지

- **일시 중지 수집** 설정은 보존 기간 이전에 크기 및 개수 제한에 도달하면 데이터가 제거되지 않도록 설계되었습니다.  
- **일시 중지 는** 보존 기간 위반으로 인해 데이터가 제거되기 전에 사용자가 환경의 용량을 늘릴 수 있는 추가 시간을 제공합니다.
- 데이터 손실로부터 사용자를 보호하는 데 도움이 되지만 이벤트 원본의 보존 기간을 초과하여 인서스가 일시 중지된 경우 가장 최근 데이터가 손실될 수 있습니다.
- 그러나 환경의 최대 용량에 도달하면 환경은 다음과 같은 추가 작업이 발생할 때까지 데이터 수집을 일시 중지합니다.

   - 환경의 최대 용량을 늘려 [서열 인사이트 환경의 확장 방법에](time-series-insights-how-to-scale-your-environment.md)설명된 대로 배율 단위를 더 추가합니다.
   - 데이터 보존 기간에 도달하고 데이터가 제거되어 환경을 최대 용량 이하로 유지합니다.

### <a name="example-three"></a>실시예 3

보존 동작이 **수신 일시 중지**로 구성된 환경을 생각해 보세요. 이 예제에서는 **데이터 보존 기간** 60일로 구성됩니다. **용량은** S1의 3개(3) 단위로 설정됩니다. 이 환경에 매일 2GB 데이터가 수신된다고 가정하겠습니다. 이 환경에서 최대 용량에 도달하면 수신이 일시 중지됩니다.

이 때 환경은 시작이 다시 시작될 때까지 또는 계속 받는 사람이 활성화될 때까지 동일한 데이터 **집합을** 표시합니다(새 데이터를 위한 공간을 확보하기 위해 이전 데이터를 제거합니다).

수신이 다시 시작되면:

- 이벤트 원본이 수신한 순서대로 데이터가 흐릅니다.
- 이벤트 원본의 보존 정책을 초과하지 않은 경우, 타임스탬프에 따라 이벤트가 인덱싱됩니다. 이벤트 원본 보존 기간 구성에 대한 자세한 내용은 [Event Hubs FAQ](../event-hubs/event-hubs-faq.md)를 참조하세요.

> [!IMPORTANT]
> 수신이 일시 중지되는 것을 방지하는 데 도움이 되는 통지를 제공하도록 경고를 설정해야 합니다. Azure 이벤트 원본의 기본 보존 기간은 1일이므로 데이터가 손실될 수 있습니다. 따라서 수신이 일시 중지된 후 추가 조치를 취하지 않을 경우 가장 최근의 데이터가 손실될 수 있습니다. 데이터 손실 가능성을 피하려면 용량을 늘리거나 **이전 데이터 삭제**로 동작을 전환해야 합니다.

Time Series Insights에서 수신 일시 중지가 발생할 경우 데이터 손실을 최소화하기 위해 영향을 받는 Event Hubs에서 **메시지 보존** 속성을 조정하는 것이 좋습니다.

[![이벤트 허브 메시지 보존.](media/time-series-insights-concepts-retention/event-hub-retention.png)](media/time-series-insights-concepts-retention/event-hub-retention.png#lightbox)

이벤트 소스()`timeStampPropertyName`에서 속성이 구성되지 않은 경우, [열렬 Insights]는 기본적으로 이벤트 허브에 X축으로 도착하는 타임스탬프를 기본값으로 설정합니다. 다른 `timeStampPropertyName` 것으로 구성된 경우 환경은 이벤트가 구문 `timeStampPropertyName` 분석될 때 데이터 패킷에서 구성된 구성을 찾습니다.

추가 용량을 수용하거나 보존 기간을 늘리기 위해 [환경을 확장하도록 타임시리즈 인사이트 환경을 확장하는 방법을](time-series-insights-how-to-scale-your-environment.md) 읽어보십시오.

## <a name="next-steps"></a>다음 단계

- 데이터 보존 설정 구성 또는 변경에 대한 자세한 내용은 [[열계 인사이트]의 보존 구성을](time-series-insights-how-to-configure-retention.md)검토한다.

- Azure [연산 정보 에서 대기 시간 완화에](time-series-insights-environment-mitigate-latency.md)대해 알아봅니다.
