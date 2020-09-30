---
title: 제한 Azure Time Series Insights 모니터링 및 줄이기 Microsoft Docs
description: Azure Time Series Insights에서 대기 시간 및 제한을 유발 하는 성능 문제를 모니터링, 진단 및 완화 하는 방법에 대해 알아봅니다.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: e89189b22b144d9e92ee8315bc6fd9aabe699eec
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91531652"
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights-gen1"></a>Azure Time Series Insights Gen1의 대기 시간을 줄이기 위한 제한 모니터링 및 완화

> [!CAUTION]
> Gen1 문서입니다.

들어오는 데이터의 양이 사용자의 환경 구성을 초과하면 Azure Time Series Insights에서 대기 시간 또는 제한이 발생할 수 있습니다.

분석하려는 데이터의 양에 맞게 환경을 적절히 구성하여 대기 시간 및 제한을 방지할 수 있습니다.

다음과 같은 경우 대기 시간 및 제한이 발생할 수 있습니다.

- 할당 된 수신 속도를 초과할 수 있는 이전 데이터를 포함 하는 이벤트 원본을 추가 합니다 (Azure Time Series Insights를 처리 해야 함).
- 더 많은 이벤트 원본을 환경에 추가하면 추가 이벤트가 발생할 수 있습니다(환경 용량을 초과할 수 있음).
- 이벤트 원본에 많은 양의 기록 이벤트를 푸시합니다 .이로 인해 지연 시간이 발생 합니다 (Azure Time Series Insights을 처리 해야 하는 경우).
- 원격 분석을 사용하여 참조 데이터를 결합하면 이벤트 크기가 더 커집니다. 허용 되는 최대 패킷 크기는 32 KB;입니다. 32 KB 보다 큰 데이터 패킷은 잘립니다.

## <a name="video"></a>동영상

### <a name="learn-about-azure-time-series-insights-data-ingress-behavior-and-how-to-plan-for-itbr"></a>Azure Time Series Insights 데이터 수신 동작 및 계획 방법에 대해 알아봅니다.</br>

> [!VIDEO https://www.youtube.com/embed/npeZLAd9lxo]

## <a name="monitor-latency-and-throttling-with-alerts"></a>경고를 사용하여 대기 시간 및 제한 모니터링

경고를 통해 사용자 환경에서 발생 하는 대기 시간 문제를 진단 하 고 완화할 수 있습니다.

1. Azure Portal에서 Azure Time Series Insights 환경을 선택 합니다. 그런 다음 **경고**를 선택 합니다.

   [![Azure Time Series Insights 환경에 경고 추가](media/environment-mitigate-latency/mitigate-latency-add-alert.png)](media/environment-mitigate-latency/mitigate-latency-add-alert.png#lightbox)

1. **+ 새 경고 규칙**을 선택합니다. 그러면 **규칙 만들기** 패널이 표시 됩니다. **조건**아래에서 **추가** 를 선택 합니다.

   [![경고 창 추가](media/environment-mitigate-latency/mitigate-latency-add-pane.png)](media/environment-mitigate-latency/mitigate-latency-add-pane.png#lightbox)

1. 그런 다음 신호 논리에 대 한 정확한 조건을 구성 합니다.

   [![신호 논리 구성](media/environment-mitigate-latency/configure-alert-rule.png)](media/environment-mitigate-latency/configure-alert-rule.png#lightbox)

   여기에서 다음 조건 중 일부를 사용 하 여 경고를 구성할 수 있습니다.

   |메트릭  |설명  |
   |---------|---------|
   |**수신된 바이트**     | 이벤트 원본에서 읽은 원시 바이트 수입니다. 일반적으로 원시 바이트 수에는 속성 이름 및 값이 포함됩니다.  |  
   |**수신된 잘못된 메시지**     | 모든 Azure Event Hubs 또는 Azure IoT Hub 이벤트 원본에서 읽은 잘못된 메시지 수입니다.      |
   |**수신된 메시지**   | 모든 Event Hubs 또는 IoT Hub 이벤트 원본에서 읽은 메시지 수입니다.        |
   |**저장된 수신 바이트**     | 저장되어 쿼리에 사용할 수 있는 총 이벤트 크기입니다. 크기는 속성 값에 대해서만 계산됩니다.        |
   |**저장된 수신 이벤트**     |   저장되어 쿼리에 사용할 수 있는 일반 이벤트 수입니다.      |
   |**수신된 메시지 시간 지연**    |  메시지가 이벤트 원본의 큐에 대기되는 시간과 수신 처리되는 시간 간의 차이(초)입니다.      |
   |**수신된 메시지 수 지연**    |  이벤트 원본 파티션에서 마지막 큐에 대기된 메시지의 시퀀스 번호와 수신 처리되는 메시지의 시퀀스 번호 간의 차이입니다.      |

   **완료**를 선택합니다.

1. 원하는 신호 논리를 구성한 후에는 선택한 경고 규칙을 시각적으로 검토 합니다.

   [![대기 시간 보기 및 차트](media/environment-mitigate-latency/mitigate-latency-view-and-charting.png)](media/environment-mitigate-latency/mitigate-latency-view-and-charting.png#lightbox)

## <a name="throttling-and-ingress-management"></a>제한 및 수신 관리

- 제한 되는 경우 *수신 메시지의 수신 시간 지연* 값이 등록 되 고, 메시지가 이벤트 원본에 도달 하는 실제 시간에서 발생 하는 시간 (초 Azure Time Series Insights)을 사용자에 게 알려 줍니다 (appx의 인덱싱 시간이 제외 됨). 30-60초의 인덱싱 시간 제외).  

  *수신된 메시지 수 지연*에도 값이 표시되므로 메시지가 뒤에 몇 개나 더 남아 있는지 알 수 있습니다.  이러한 차이를 해소하는 가장 쉬운 방법은 작업 환경의 용량을 차이가 극복될 수 있는 크기로 늘리는 것입니다.  

  예를 들어 S1 환경에서 500만 메시지의 지연 시간을 보여 주는 경우, 사용자 환경의 크기를 하루 중 6 개 단위로 늘려 검색할 수 있습니다.  더 빠르게 따라잡기 위해 더 크게 늘릴 수도 있습니다. 캐치업 시간은 처음에 환경을 프로비전할 때 일반적으로 발생하고, 이미 이벤트가 포함된 이벤트 원본에 연결하거나 많은 기록 데이터를 대량으로 업로드할 때 특히 두드러집니다.

- 또 다른 방법은 **저장된 수신 이벤트** 경고를 2시간 동안의 전체 환경 용량보다 약간 낮게 설정하는 것입니다.  이 경고는 용량이 일정한지 이해하는 데 도움이 되며 대기 시간이 길어질 가능성이 높다는 것을 나타냅니다.

  예를 들어 3개의 S1 장치가 프로비전된 경우(또는 1분당 2100개의 이벤트를 수신하는 경우) **저장된 수신 이벤트** 경고를 2시간 동안 1900개 이벤트 이하로 설정할 수 있습니다. 이 임계값을 지속적으로 초과하여 경고를 발생시키는 경우 프로비전 부족 상태가 될 가능성이 큽니다.  

- 제한 되는 것으로 의심 되는 경우 **수신 받은 메시지** 를 이벤트 원본의 egressed 메시지와 비교할 수 있습니다.  이벤트 허브로 수신 되는 **수신 메시지**보다 큰 경우 Azure Time Series Insights 제한 될 수 있습니다.

## <a name="improving-performance"></a>성능 향상

제한 또는 대기 시간을 줄이고 싶을 때 이를 수정하는 가장 좋은 방법은 환경의 용량을 늘리는 것입니다.

분석하려는 데이터의 양에 맞게 환경을 적절히 구성하여 대기 시간 및 제한을 방지할 수 있습니다. 환경에 용량을 추가 하는 방법에 대 한 자세한 내용은 [환경 크기 조정](time-series-insights-how-to-scale-your-environment.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Time Series Insights 환경에서 문제 진단 및 해결](time-series-insights-diagnose-and-solve-problems.md)을 참조 하세요.

- [Azure Time Series Insights 환경의 크기를 조정 하는 방법을](time-series-insights-how-to-scale-your-environment.md)알아봅니다.
