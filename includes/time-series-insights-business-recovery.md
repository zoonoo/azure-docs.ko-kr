---
title: 포함 파일
description: 포함 파일
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: kingdomofends
ms.author: adgera
ms.date: 04/29/2019
ms.openlocfilehash: cf07d19966c08a63b9aa50475622aa0a1e5e1600
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236528"
---
## <a name="business-disaster-recovery"></a>비즈니스 재해 복구

이 섹션에서는 Azure Time Series Insights는 앱 및 재해가 발생 한 경우에 실행 되는 서비스의 기능에 설명 합니다 (**비즈니스 재해 복구**).

### <a name="high-availability"></a>고가용성

Time Series Insights 제공 특정 Azure 서비스인 **고가용성** Azure 지역 수준에서 중복성을 사용 하 여 기능 합니다. Microsoft Azure는 Azure의 재해 복구 기능을 지원 하는 예를 들어 **지역 간 가용성** 기능입니다.

Azure (및 Time Series Insights 인스턴스에도 적용 가능)을 통해 제공 되는 추가 고가용성 기능은 다음과 같습니다.

1. **장애 조치(Failover)**: Azure에서 제공 [지역에서 복제 및 분산 로드](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)합니다.
1. **데이터 복원을** 하 고 **저장소 복구**: Azure에서 제공 [몇 가지 옵션을 유지 하 고 데이터를 복구할](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption)합니다.
1. **Site Recovery** 기능을 통해 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/)합니다.

장치 및 사용자에 대 한 전역, 지역 간, 높은 가용성을 제공 하려면 이러한 Azure 기능을 사용 하도록 설정 해야 합니다.

> [!NOTE]
> Azure를 사용 하도록 구성 하는 경우 **지역 간 가용성**, 추가 지역 간 가용성 구성은 Azure Time Series Insights 내에서 필요 하지 않습니다.

### <a name="iot-and-event-hubs"></a>IoT 및 이벤트 허브

일부 Azure IoT 서비스에는 기본 제공 비즈니스 재해 복구 기능이 포함 됩니다.

1. [IoT Hub 고가용성 재해 복구](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr) 지역 간 중복성을 포함 합니다.
1. [이벤트 허브 정책](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)합니다.
1. [Azure Storage 중복성](https://docs.microsoft.com/azure/storage/common/storage-redundancy)합니다.

추가적인 재해 복구 기회를 제공 이러한 다른 서비스를 사용 하 여 Time Series Insights를 통합 합니다. 예를 들어 이벤트 허브에 보낸 원격 분석 Azure Blob Storage 데이터베이스 백업으로 유지 될 수 있습니다.

### <a name="time-series-insights"></a>Time Series Insights

Time Series Insights 데이터, 앱 및 중단 하는 경우에 실행 되는 서비스를 유지 하는 방법은 여러 가지가 있습니다. Azure Time Series 환경 완전 하 고, 중복 된 백업 복사본 필수임을 확인할 수 있습니다.

1. TSI 별로 **장애 조치 인스턴스의** 트래픽 및 데이터를 리디렉션하는 데 있습니다.
1. 감사 및 데이터 보존 목적으로 합니다.

일반적으로 가장 좋은 방법은 TSI 환경 중복 백업이 Azure 지역에서 두 번째 TSI 환경 만들기 방법은입니다. 이벤트 주 이벤트 원본에서이 보조 환경에도 전송 됩니다. 두 번째, 전용 소비자 그룹을 사용 하는 데 해당 원본 비즈니스 재해 복구 지침 (위에 제공 된)를 수행 해야 합니다.

특히, 중복 된 환경을 만들려면:

1. 두 번째 지역에서 환경을 만들려면 ([Azure portal에서 새로운 Time Series Insights 환경 만들기](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)).
1. 이벤트 원본에 대해 두 번째 전용 소비자 그룹을 만듭니다.
1. 두 번째, 전용 소비자 그룹을 지정 하는 새 환경에 해당 이벤트 소스를 연결 합니다.
1. Time Series Insights를 검토 [IoT Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) 하 고 [이벤트 허브](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access) 설명서.

마지막으로:

* 주 지역이 재해 인시던트 중 영향을 받을 경우 백업 Time Series Insights 환경으로 작업 경로를 전환합니다.
* 백업 및 모든 TSI 원격 분석 및 쿼리 데이터를 복구 하 여 두 번째 영역을 사용 합니다.

> [!IMPORTANT]
> * 장애 조치(failover) 시 지연이 발생할 수 있습니다.
> * 또한 장애 조치 작업 다시 라우트 메시지 처리의 순간적인 급증을 발생할 수 있습니다.
> * 자세한 내용은 [Time Series Insights에서 대기 시간 완화](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency)를 참조하세요.