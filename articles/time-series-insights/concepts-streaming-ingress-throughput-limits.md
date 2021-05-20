---
title: 스트리밍 수집 처리량 제한 - Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Time Series Insights Gen2의 수신 처리량 제한에 대해 알아봅니다.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/21/2021
ms.custom: seodec18
ms.openlocfilehash: 3c938e7fe180e6a9cf08031a2a4565486f3f77a9
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504637"
---
# <a name="streaming-ingestion-throughput-limits"></a>스트리밍 수집 처리량 제한

Azure Time Series Insights Gen2 스트리밍 데이터 수신 제한은 아래에 설명되어 있습니다.

> [!TIP]
> 모든 제한의 포괄적인 목록은 [Azure Time Series Insights Gen2 환경 계획](./how-to-plan-your-environment.md#review-azure-time-series-insights-gen2-limits)을 참조하세요.

## <a name="per-environment-limitations"></a>환경당 제한

일반적으로 수신 속도는 조직 내 디바이스 수, 이벤트 내보내기 빈도 및 각 이벤트의 크기에 대한 요소로 표시됩니다.

* **디바이스 수** × **이벤트 내보내기 빈도** × **각 이벤트의 크기**

기본적으로 Azure Time Series Insights Gen2는 **Azure Time Series Insights Gen2 환경당 최대 1MBps(초당 메가바이트)의 속도** 로 들어오는 데이터를 수집할 수 있습니다. [허브 파티션](./concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits)마다 추가 제한이 있습니다.

> [!TIP]
>
> * 요청에 따라 최대 2MBps의 수집 속도에 맞는 환경을 지원할 수 있습니다.
> * Azure Portal을 통해 지원 티켓을 제출하여 처리량을 늘려야 하는 경우 문의해 주세요.

* **예제 1:**

    Contoso Shipping에는 이벤트를 분당 3회 내보내는 100,000개의 디바이스가 있습니다. 이벤트의 크기는 200바이트입니다. 4개의 파티션이 있는 IoT Hub를 Azure Time Series Insights Gen2 이벤트 원본으로 사용하고 있습니다.

  * Azure Time Series Insights Gen2 환경의 수집 속도는 **100,000개 디바이스 * 200바이트/이벤트 * (3/60 이벤트/초) = 1MBps** 입니다.
    * 균형 파티션이라고 가정하면 파티션당 수집 속도는 0.25MBps입니다.
    * Contoso Shipping의 수집 속도는 규모 제한 내에 있습니다.

* **예 2:**

    Contoso Fleet Analytics에는 1초마다 이벤트를 내보내는 10,000개의 디바이스가 있습니다. 파티션 수가 2개인 Event Hub를 Azure Time Series Insights Gen2 이벤트 원본으로 사용하고 있습니다. 이벤트의 크기는 200바이트입니다.

  * 환경 수집 속도는 **10,000개 디바이스 * 200바이트/이벤트 * 1개 이벤트/초 = 2MBps** 입니다.
    * 균형 파티션이라고 가정하면 파티션당 수집 속도는 1MBps입니다.
    * Contoso Fleet Analytics의 수집 속도는 환경 및 파티션 제한을 초과합니다. Azure Portal을 통해 요청을 Azure Time Series Insights Gen2에 제출하여 해당 환경의 수집 속도를 높이고, 제한 내에 더 많은 파티션이 있는 Event Hub를 만들 수 있습니다.

## <a name="hub-partitions-and-per-partition-limits"></a>허브 파티션 및 파티션당 제한

Azure Time Series Insights Gen2 환경을 계획하는 경우 Azure Time Series Insights Gen2에 연결할 이벤트 원본의 구성을 고려해야 합니다. Azure IoT Hub 및 Event Hubs는 모두 파티션을 활용하여 이벤트 처리를 위한 수평 크기 조정을 사용하도록 설정합니다.

*파티션* 은 허브에서 저장되는 순서가 지정된 이벤트 시퀀스입니다. 파티션 수는 허브 만들기 중에 설정되며 변경할 수 없습니다.

Event Hubs 분할 모범 사례는 [필요한 파티션은 몇 개인가요?](../event-hubs/event-hubs-faq.md#how-many-partitions-do-i-need)를 검토하세요.

> [!NOTE]
> Azure Time Series Insights Gen2에서 사용되는 대부분의 IoT Hub에는 4개의 파티션만 필요합니다.

Azure Time Series Insights Gen2 환경에 대한 새 허브를 만드는지, 아니면 기존 허브를 사용하는지에 관계없이 파티션당 수집 속도를 계산하여 제한 내에 있는지 확인해야 합니다.

Azure Time Series Insights Gen2에는 현재 일반적인 **파티션당 0.5MBps 제한** 이 있습니다.

### <a name="iot-hub-specific-considerations"></a>IoT Hub 관련 고려 사항

IoT Hub에서 디바이스가 만들어지면 파티션에 영구적으로 할당됩니다. 이렇게 하면 할당이 변경되지 않으므로 IoT Hub에서 이벤트 순서를 보장할 수 있습니다.

고정 파티션 할당은 IoT Hub 다운스트림에서 보낸 데이터를 수집하는 Azure Time Series Insights Gen2 인스턴스에도 영향을 줍니다. 여러 디바이스에서 동일한 게이트웨이 디바이스 ID를 사용하여 메시지를 허브로 전달하는 경우 동일한 파티션에 동시에 도달하여 파티션당 크기 제한을 초과할 수 있습니다.

**영향**:

* 단일 파티션에서 제한을 초과하는 수집 속도가 지속적으로 발생하는 경우 IoT Hub 데이터 보존 기간이 초과되기 전에 Azure Time Series Insights Gen2에서 모든 디바이스 원격 분석을 동기화하지 않을 수 있습니다. 따라서 수집 제한이 지속적으로 초과되면 전송된 데이터가 손실될 수 있습니다.

이러한 상황을 완화하려면 다음과 같은 모범 사례를 따르는 것이 좋습니다.

* 솔루션을 배포하기 전에 환경당 및 파티션당 수집 속도를 계산합니다.
* IoT Hub 디바이스가 가능한 최대의 범위까지 부하 분산되었는지 확인합니다.

> [!IMPORTANT]
> IoT Hub를 이벤트 원본으로 사용하는 환경의 경우 사용 중인 허브 디바이스의 수로 수집 속도를 계산하여 파티션당 제한인 0.5MBps 미만으로 떨어지도록 합니다.
>
> * 여러 이벤트가 동시에 도착하는 경우에도 제한이 초과되지 않습니다.

  ![IoT Hub 파티션 다이어그램](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

허브 처리량 및 파티션을 최적화하는 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [IoT Hub 크기 조정](../iot-hub/iot-hub-scaling.md)
* [Event Hub 크기 조정](../event-hubs/event-hubs-scalability.md#throughput-units)
* [Event Hub 파티션](../event-hubs/event-hubs-features.md#partitions)

## <a name="next-steps"></a>다음 단계

* 데이터 [스토리지](./concepts-storage.md)에 대해 읽기
