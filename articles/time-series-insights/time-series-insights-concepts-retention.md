---
title: Azure Time Series Insights 환경의 데이터 보존 기간 이해 | Microsoft Docs
description: 이 아티클에서는 Azure Time Series Insights 환경의 데이터 보존 기간을 제어하는 두 가지 설정을 설명합니다.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: e3336df30873b40d2b8a464d1f866b524f76776d
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237004"
---
# <a name="understand-data-retention-in-azure-time-series-insights"></a>Azure Time Series Insights 데이터 보존 기간 이해

이 문서에서는 Azure Time Series Insights 환경의 데이터 보존 기간에 영향을 주는 두 가지 설정을 설명 합니다.

## <a name="video"></a>비디오

### <a name="the-following-video-summarizes-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>다음 비디오는 Time Series Insights 데이터 보존 및에 대 한 계획 하는 방법을 요약 합니다.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

각 Azure Time Series 환경에 제어 하는 설정이 **데이터 보존 시간**합니다. 값의 범위는 1~400일입니다. 먼저 도달 하는 환경 저장소 용량 또는 보존 기간에 따라 데이터가 삭제 됩니다.

또한 Azure Time Series 환경에는 **저장소 용량 한도 초과 하는 동작** 설정 합니다. 수신을 제어 하 고 환경의 최대 용량에 도달한 경우 동작을 제거 합니다. 이 구성할 때 선택할 수 있는 두 가지 동작 가지가 있습니다.

- **이전 데이터 삭제**(기본값)  
- **수신 일시 중지**

> [!NOTE]
> 기본적으로 새 환경을 만들면 보존 기간이 **이전 데이터 삭제**로 구성됩니다. 이 설정은 Azure portal에서 사용 하 여 생성 후 필요에 따라 전환할 수는 **구성** Time Series Insights 환경 페이지입니다.

보존 동작을 전환하는 방법에 대한 자세한 내용은 [Time Series Insights의 보존 기간 구성](time-series-insights-how-to-configure-retention.md)을 참조하세요.

데이터 보존 동작 비교:

## <a name="purge-old-data"></a>이전 데이터 삭제

- 이 동작은 Time Series Insights 환경에 대 한 기본 동작에 설명 합니다.  
- 이 동작은 기본 사용자가 항상 표시 하려는 경우 해당 *가장 최근의 데이터* Time Series Insights 환경에서.
- 이 동작은 환경의 한도(보존 시간, 크기 또는 개수 중 가장 처음으로 도달한 한도)에 도달한 경우 데이터를 *삭제*합니다. 기본적으로 보존 기간은 30일로 설정됩니다.
- 가장 오래 전에 수집된 데이터부터 먼저 삭제됩니다(FIFO 방식).

### <a name="example-one"></a>예제 1

보존 동작 **수신을 계속하고 이전 데이터 삭제**가 지정된 예제 환경을 고려해 보세요.

**데이터 보존 시간** 400 일로 설정 됩니다. **용량**은 30GB의 총 용량을 포함하는 S1 단위로 설정됩니다.   인바운드 데이터가 매일 평균 500MB 누적된다고 가정하겠습니다. 인바운드 데이터의 속도에 따르면 60일째에 최대 용량에 도달하므로 이 환경은 데이터를 60일만 보존할 수 있습니다. 인바운드 데이터 누적: 매일 500MB x 60일 = 30GB

61 일째에 환경을 최신 데이터를 보여주지만 60 일 보다 오래 된 데이터를 제거 합니다. 삭제를 통해 스트리밍되는 새 데이터를 위한 공간을 확보하여 새 데이터를 계속 탐색할 수 있습니다. 데이터를 더 오래 보존하려면 단위를 추가하여 환경의 규모를 늘리거나 푸시되는 데이터의 양을 줄여야 합니다.  

### <a name="example-two"></a>예제 2

보존 동작이 **수신을 계속하고 이전 데이터 삭제**로 구성되어 있는 환경도 생각해 보세요. 이 예제에서는 **데이터 보존 시간**이 더 낮은 값인 180일로 설정됩니다. **용량**은 30GB의 총 용량을 포함하는 S1 단위로 설정됩니다. 180일 내내 데이터를 저장하려면 일일 수신 규모가 0.166GB(166MB)를 초과하지 않아야 합니다.  

이 환경의 일일 수신 속도가 0.166GB를 초과할 때마다 일부 데이터가 삭제되므로 데이터가 180일 동안 보관되지 않습니다. 이와 동일한 환경을 바쁜 시간대에 사용한다고 생각해 보세요. 환경의 수신 속도가 일 평균 0.189GB로 증가할 수 있다고 가정해 보겠습니다. 이러한 바쁜 시간대에는 데이터가 약 158일 동안 보존됩니다(30GB/0.189 = 158.73일). 원하는 데이터 보존 기간보다 줄어든 기간입니다.

## <a name="pause-ingress"></a>수신 일시 중지

- 합니다 **수신 일시 중지** 설정은 보존 기간 전에 크기와 개수 한도 도달 하면 데이터를 제거 하지 않으면 되도록 디자인 되었습니다.  
- **수신 일시 중지** 보존 기간 위반으로 인해 데이터가 삭제 되기 전에 해당 환경의 용량을 늘릴 사용자에 대 한 추가 시간을 제공 합니다.
- 데이터 손실 로부터 보호할 수 있지만 이벤트 원본의 보존 기간이 지나서 수신이 일시 중지 하는 경우 가장 최근의 데이터가 손실 될 가능성이 기회를 만들 수 있습니다.
- 하지만 환경의 최대 용량에 도달 환경을 일시 중지 데이터 수신 다음 추가 작업이 수행 될 때까지:

   - 에 설명 된 대로 배율 단위를 더 추가 하는 환경의 최대 용량을 늘려야 [Time Series Insights 환경의 크기를 조정 하는 방법을](time-series-insights-how-to-scale-your-environment.md)합니다.
   - 데이터 보존 기간에 도달 하 고 데이터 가져오기 환경을 최대 용량 아래에서 제거 됩니다.

### <a name="example-three"></a>예제 3

보존 동작이 **수신 일시 중지**로 구성된 환경을 생각해 보세요. 이 예제에서는 **데이터 보존 기간** 60일로 구성됩니다. **용량** S1의 3 명의 단위로 설정 됩니다. 이 환경에 매일 2GB 데이터가 수신된다고 가정하겠습니다. 이 환경에서 최대 용량에 도달하면 수신이 일시 중지됩니다.

이때 환경 같은 데이터 집합이 표시 될 때까지 또는 수신이 다시 시작 될 때까지 **수신을 계속** 사용 됨 (새 데이터에 대 한 공간을 만들기 위해 오래 된 데이터는 제거 됩니다).

수신이 다시 시작되면:

- 이벤트 원본이 수신한 순서대로 데이터가 흐릅니다.
- 이벤트 원본의 보존 정책을 초과하지 않은 경우, 타임스탬프에 따라 이벤트가 인덱싱됩니다. 이벤트 원본 보존 기간 구성에 대한 자세한 내용은 [Event Hubs FAQ](../event-hubs/event-hubs-faq.md)를 참조하세요.

> [!IMPORTANT]
> 수신이 일시 중지되는 것을 방지하는 데 도움이 되는 통지를 제공하도록 경고를 설정해야 합니다. Azure 이벤트 원본의 기본 보존 기간은 1일이므로 데이터가 손실될 수 있습니다. 따라서 수신이 일시 중지된 후 추가 조치를 취하지 않을 경우 가장 최근의 데이터가 손실될 수 있습니다. 데이터 손실 가능성을 피하려면 용량을 늘리거나 **이전 데이터 삭제**로 동작을 전환해야 합니다.

Time Series Insights에서 수신 일시 중지가 발생할 경우 데이터 손실을 최소화하기 위해 영향을 받는 Event Hubs에서 **메시지 보존** 속성을 조정하는 것이 좋습니다.

[![이벤트 허브 메시지 보존입니다.](media/time-series-insights-contepts-retention/event-hub-retention.png)](media/time-series-insights-contepts-retention/event-hub-retention.png#lightbox)

이벤트 원본에 구성 된 속성이 없는 경우 (`timeStampPropertyName`), Time Series Insights가 x 축을 기준으로 이벤트 허브에 도착 한 타임 스탬프를 기본값으로 합니다. 하는 경우 `timeStampPropertyName` 구성 된 다른 항목에 구성 된 환경 찾습니다 `timeStampPropertyName` 데이터 패킷에서 이벤트가 구문 분석 하는 경우.

추가 용량을 확보하거나 보존 기간을 늘리기 위해 환경의 규모를 확장해야 하는 경우 [Time Series Insights 환경의 규모를 조정하는 방법](time-series-insights-how-to-scale-your-environment.md)에서 자세한 내용을 참조하세요.  

## <a name="next-steps"></a>다음 단계

- 구성 하거나 데이터 보존 설정 변경에 대 한 내용은 검토 [Time Series Insights의 보존 기간 구성](time-series-insights-how-to-configure-retention.md)합니다.
