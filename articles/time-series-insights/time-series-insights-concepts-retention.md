---
title: 사용자 환경에서 데이터 보존 이해-Azure Time Series insights | Microsoft Docs
description: 이 아티클에서는 Azure Time Series Insights 환경의 데이터 보존 기간을 제어하는 두 가지 설정을 설명합니다.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/21/2019
ms.custom: seodec18
ms.openlocfilehash: 2c68c64202efec6d7dab745b6e1bdc029fa3976f
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561290"
---
# <a name="understand-data-retention-in-azure-time-series-insights"></a>Azure Time Series Insights의 데이터 보존 이해

이 문서에서는 Azure Time Series Insights 환경의 데이터 보존에 영향을 주는 두 가지 기본 설정을 설명 합니다.

## <a name="video"></a>비디오

### <a name="the-following-video-summarizes-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>다음 비디오는 Time Series Insights 데이터 보존 및이를 계획 하는 방법을 요약 합니다.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

각 Azure Time Series Insights 환경에는 **데이터 보존 시간**을 제어 하는 설정이 있습니다. 값의 범위는 1~400일입니다. 데이터는 환경 저장소 용량 또는 보존 기간 중 먼저 도달 하는 기간에 따라 삭제 됩니다.

또한 Azure Time Series Insights 환경에는 **저장소 제한 초과 동작** 설정이 있습니다. 환경의 최대 용량에 도달 하면 수신 및 제거 동작을 제어 합니다. 구성할 때 선택할 수 있는 두 가지 동작이 있습니다.

- **이전 데이터 삭제**(기본값)  
- **수신 일시 중지**

> [!NOTE]
> 기본적으로 새 환경을 만들면 보존 기간이 **이전 데이터 삭제**로 구성됩니다. Time Series Insights 환경의 **구성** 페이지에서 Azure Portal를 사용 하 여 만든 후 필요에 따라이 설정을 전환할 수 있습니다.
> * 보존 정책을 구성 하는 방법에 대 한 자세한 내용은 [Time Series Insights에서 보존 구성](time-series-insights-how-to-configure-retention.md)을 참조 하세요.

데이터 보존 정책에 대 한 자세한 내용은 아래에 자세히 설명 되어 있습니다.

## <a name="purge-old-data"></a>이전 데이터 삭제

- **이전 데이터 제거** 는 Azure Time Series Insights 환경의 기본 설정입니다.  
- 사용자가 Time Series Insights 환경에서 항상 *최신 데이터* 를 볼 수 있도록 하려면 **이전 데이터 제거** 를 선택 하는 것이 좋습니다.
- **이전 데이터 제거** 설정은 환경의 제한 (보존 시간, 크기 또는 개수 중 먼저 도달 하는 경우)에 도달 하면 데이터를 *제거* 합니다. 기본적으로 보존 기간은 30일로 설정됩니다.
- 가장 오래 된 수집 데이터가 먼저 제거 됩니다 ("처음부터 시작" 접근 방식).

### <a name="example-one"></a>예 1

보존 동작 **수신을 계속하고 이전 데이터 삭제**가 지정된 예제 환경을 고려해 보세요.

**데이터 보존 시간은** 400 일로 설정 됩니다. **용량**은 30GB의 총 용량을 포함하는 S1 단위로 설정됩니다. 인바운드 데이터가 매일 평균 500MB 누적된다고 가정하겠습니다. 인바운드 데이터의 속도에 따르면 60일째에 최대 용량에 도달하므로 이 환경은 데이터를 60일만 보존할 수 있습니다. 이 기간 동안 인바운드 데이터의 누적량을 계산해 보면 다음과 같습니다. 500MB x 60일 = 30GB.

6 일의 경우 환경은 최신 데이터를 표시 하지만 60 일 보다 오래 된 가장 오래 된 데이터를 제거 합니다. 삭제를 통해 스트리밍되는 새 데이터를 위한 공간을 확보하여 새 데이터를 계속 탐색할 수 있습니다. 데이터를 더 오래 보존하려면 단위를 추가하여 환경의 규모를 늘리거나 푸시되는 데이터의 양을 줄여야 합니다.  

### <a name="example-two"></a>예 2

보존 동작이 **수신을 계속하고 이전 데이터 삭제**로 구성되어 있는 환경도 생각해 보세요. 이 예제에서는 **데이터 보존 시간**이 더 낮은 값인 180일로 설정됩니다. **용량**은 30GB의 총 용량을 포함하는 S1 단위로 설정됩니다. 180일 내내 데이터를 저장하려면 일일 수신 규모가 0.166GB(166MB)를 초과하지 않아야 합니다.  

이 환경의 일일 수신 속도가 0.166GB를 초과할 때마다 일부 데이터가 삭제되므로 데이터가 180일 동안 보관되지 않습니다. 이와 동일한 환경을 바쁜 시간대에 사용한다고 생각해 보세요. 환경의 수신 속도가 일 평균 0.189GB로 증가할 수 있다고 가정해 보겠습니다. 이러한 바쁜 시간대에는 데이터가 약 158일 동안 보존됩니다(30GB/0.189 = 158.73일). 원하는 데이터 보존 기간보다 줄어든 기간입니다.

## <a name="pause-ingress"></a>수신 일시 중지

- **일시 중지 수신** 설정은 보존 기간 전에 크기 및 개수 제한에 도달한 경우 데이터가 제거 되지 않도록 설계 되었습니다.  
- **일시 중지 수신** 은 보존 기간을 위반 때문에 데이터가 제거 되기 전에 사용자가 환경의 용량을 늘릴 수 있는 추가 시간을 제공 합니다.
- 데이터 손실 로부터 보호 하는 데 도움이 되지만 이벤트 원본의 보존 기간을 초과 하 여 수신이 일시 중지 된 경우 가장 최근의 데이터가 손실 될 기회가 발생할 수 있습니다.
- 그러나 환경의 최대 용량에 도달 하면 다음 추가 작업이 발생할 때까지 환경이 데이터 수신을 일시 중지 합니다.

   - [Time Series Insights 환경의 크기를 조정 하는 방법](time-series-insights-how-to-scale-your-environment.md)에 설명 된 대로 환경의 최대 용량을 늘려 배율 단위를 추가 합니다.
   - 데이터 보존 기간에 도달 하 고 데이터를 제거 하 여 환경을 최대 용량 아래로 전환 합니다.

### <a name="example-three"></a>예 3

보존 동작이 **수신 일시 중지**로 구성된 환경을 생각해 보세요. 이 예제에서는 **데이터 보존 기간** 60일로 구성됩니다. **용량은** S1의 3 개 단위로 설정 됩니다. 이 환경에 매일 2GB 데이터가 수신된다고 가정하겠습니다. 이 환경에서 최대 용량에 도달하면 수신이 일시 중지됩니다.

이때 수신이 다시 시작 되거나 **계속 수신** 될 때까지 (이전 데이터를 제거 하 여 새 데이터를 위한 공간을 확보 함) 환경에 동일한 데이터 집합이 표시 됩니다.

수신이 다시 시작되면:

- 이벤트 원본이 수신한 순서대로 데이터가 흐릅니다.
- 이벤트 원본의 보존 정책을 초과하지 않은 경우, 타임스탬프에 따라 이벤트가 인덱싱됩니다. 이벤트 원본 보존 기간 구성에 대한 자세한 내용은 [Event Hubs FAQ](../event-hubs/event-hubs-faq.md)를 참조하세요.

> [!IMPORTANT]
> 수신이 일시 중지되는 것을 방지하는 데 도움이 되는 통지를 제공하도록 경고를 설정해야 합니다. Azure 이벤트 원본의 기본 보존 기간은 1일이므로 데이터가 손실될 수 있습니다. 따라서 수신이 일시 중지된 후 추가 조치를 취하지 않을 경우 가장 최근의 데이터가 손실될 수 있습니다. 데이터 손실 가능성을 피하려면 용량을 늘리거나 **이전 데이터 삭제**로 동작을 전환해야 합니다.

Time Series Insights에서 수신 일시 중지가 발생할 경우 데이터 손실을 최소화하기 위해 영향을 받는 Event Hubs에서 **메시지 보존** 속성을 조정하는 것이 좋습니다.

[이벤트 허브 메시지 보존을 ![합니다.](media/time-series-insights-concepts-retention/event-hub-retention.png)](media/time-series-insights-concepts-retention/event-hub-retention.png#lightbox)

이벤트 원본에 구성 된 속성이 없는 경우 (`timeStampPropertyName`) Time Series Insights 기본값은 X 축으로 이벤트 허브에 도착 타임 스탬프입니다. `timeStampPropertyName` 다른 항목으로 구성 된 경우에는 이벤트가 구문 분석 될 때 환경에서 데이터 패킷에 구성 된 `timeStampPropertyName`를 찾습니다.

[Time Series Insights 환경의 크기를 조정 하는 방법](time-series-insights-how-to-scale-your-environment.md) 을 참조 하 여 추가 용량을 수용할 수 있도록 환경을 확장 하거나 보존 기간을 늘립니다.

## <a name="next-steps"></a>다음 단계

- 데이터 보존 설정을 구성 하거나 변경 하는 방법에 대 한 자세한 내용은 [Time Series Insights에서 보존 구성](time-series-insights-how-to-configure-retention.md)을 참조 하세요.

- [Azure Time Series Insights의 대기 시간 완화](time-series-insights-environment-mitigate-latency.md)에 대해 알아봅니다.
