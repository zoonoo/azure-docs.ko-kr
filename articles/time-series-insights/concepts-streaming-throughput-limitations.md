---
title: 스트리밍 수집 처리량 제한-Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights의 수신 처리량 제한에 대해 알아봅니다.
author: lyrana
ms.author: lyhughes
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: baa132589f5242ab480c9caa55ca793e0652ad6e
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135007"
---
# <a name="streaming-ingestion-throughput-limits"></a>스트리밍 수집 처리량 제한

Azure Time Series Insights 스트리밍 데이터 수신 제한 사항은 아래에 설명 되어 있습니다.

> [!TIP]
> 모든 미리 보기 제한 사항에 대한 포괄적인 목록은 [미리 보기 환경 계획](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits)을 참조하세요.

## <a name="per-environment-limitations"></a>환경당 제한

일반적으로 수신 속도는 조직 내 디바이스 수, 이벤트 내보내기 빈도 및 각 이벤트의 크기에 대한 요소로 표시됩니다.

*  **디바이스 수** × **이벤트 내보내기 빈도** × **각 이벤트의 크기**

기본적으로 Time Series Insights는 **Time Series Insights 환경 당 최대 1mb (초당 메가바이트)** 의 속도로 들어오는 데이터를 수집할 수 있습니다. [허브 파티션](concepts-streaming-throughput-limitations.md#hub-partitions-and-per-partition-limits)마다 추가 제한이 있습니다.

> [!TIP]
>
> * 요청에 따라 최대 16MBps의 수집 속도에 맞는 환경을 지원할 수 있습니다.
> * Azure Portal을 통해 지원 티켓을 제출하여 처리량을 늘려야 하는 경우 문의해 주세요.
 
* **예제 1:**

    Contoso Shipping에는 이벤트를 분당 3회 내보내는 100,000개의 디바이스가 있습니다. 이벤트의 크기는 200바이트입니다. 4개의 파티션이 있는 IoT Hub를 Time Series Insights 이벤트 원본으로 사용하고 있습니다.

    * Time Series Insights 환경의 수집 속도는 다음과 같습니다. **100,000개 디바이스 * 200바이트/이벤트 * (3/60 이벤트/초) = 1MBps**
    * 파티션당 수집 속도는 0.25MBps입니다.
    * Contoso 배송료의 수집 률은 크기 제한 내에 있습니다.

* **예 2:**

    Contoso Fleet Analytics에는 1초마다 이벤트를 내보내는 60,000개의 디바이스가 있습니다. 파티션 수가 4개인 Event Hub를 Time Series Insights 이벤트 원본으로 사용하고 있습니다. 이벤트의 크기는 200바이트입니다.

    * 환경 수집 속도는 다음과 같습니다. **60,000개 디바이스 * 200바이트/이벤트 * 1개 이벤트/초 = 12MBps**
    * 파티션당 속도는 3MBps입니다.
    * Contoso Fleet Analytics의 수집 속도는 환경 및 파티션 제한을 초과합니다. Azure Portal을 통해 요청을 Time Series Insights에 제출하여 해당 환경의 수집 속도를 높이고, 미리 보기 제한 내에 더 많은 파티션이 있는 Event Hub를 만들 수 있습니다.

## <a name="hub-partitions-and-per-partition-limits"></a>허브 파티션 및 파티션당 제한

Time Series Insights 환경을 계획하는 경우 Time Series Insights에 연결할 이벤트 원본의 구성을 고려해야 합니다. Azure IoT Hub 및 Event Hubs는 모두 파티션을 활용하여 이벤트 처리를 위한 수평 크기 조정을 사용하도록 설정합니다. 

*파티션*은 허브에서 저장되는 순서가 지정된 이벤트 시퀀스입니다. 파티션 수는 허브 만들기 중에 설정되며 변경할 수 없습니다.

Event Hubs 분할 모범 사례는 [필요한 파티션은 몇 개인가요?](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need)를 검토하세요.

> [!NOTE]
> Azure Time Series Insights에서 사용되는 대부분의 IoT Hub에는 4개의 파티션만 필요합니다.

Time Series Insights 환경의 새 허브를 만들거나 기존 허브를 사용 하 고 있는지 여부에 관계 없이 파티션 수집 율을 계산 하 여 제한 내에 있는지 확인 해야 합니다. 

Azure Time Series Insights 미리 보기에는 현재 일반적인 **파티션당 0.5MBps 제한**이 있습니다.

### <a name="iot-hub-specific-considerations"></a>IoT Hub 관련 고려 사항

IoT Hub에서 디바이스가 만들어지면 파티션에 영구적으로 할당됩니다. 이렇게 하면 할당이 변경되지 않으므로 IoT Hub에서 이벤트 순서를 보장할 수 있습니다.

고정 파티션 할당은 IoT Hub 다운스트림에서 보낸 데이터를 수집하는 Time Series Insights 인스턴스에도 영향을 줍니다. 여러 디바이스에서 동일한 게이트웨이 디바이스 ID를 사용하여 메시지를 허브로 전달하는 경우 동일한 파티션에 동시에 도달하여 파티션당 크기 제한을 초과할 수 있습니다.

**영향**:

* 단일 파티션에서 미리 보기 제한을 초과하는 수집 속도가 지속적으로 발생하는 경우 IoT Hub 데이터 보존 기간이 초과되기 전에 Time Series Insights에서 모든 디바이스 원격 분석을 동기화하지 않을 수 있습니다. 따라서 수집 제한이 지속적으로 초과되면 전송된 데이터가 손실될 수 있습니다.

이러한 상황을 완화하려면 다음과 같은 모범 사례를 따르는 것이 좋습니다.

* 솔루션을 배포하기 전에 환경당 및 파티션당 수집 속도를 계산합니다.
* IoT Hub 디바이스가 가능한 최대의 범위까지 부하 분산되었는지 확인합니다.

> [!IMPORTANT]
> IoT Hub를 이벤트 원본으로 사용 하는 환경의 경우 사용 중인 허브 장치 수를 사용 하 여 수집 율을 계산 하 여 해당 비율이 파티션 제한 당 0.5 MBps 미만인 지 확인할 수 있습니다.
>
> * 여러 이벤트가 동시에 도착하는 경우에도 미리 보기 제한이 초과되지 않습니다.

  ![IoT Hub 파티션 다이어그램](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

허브 처리량 및 파티션을 최적화하는 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [IoT Hub 크기 조정](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Event Hub 크기 조정](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Event Hub 파티션](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

## <a name="next-steps"></a>다음 단계

* 데이터 [저장소](concepts-storage.md) 에 대해 읽기