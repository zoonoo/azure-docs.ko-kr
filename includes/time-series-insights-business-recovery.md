---
ms.topic: include
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.date: 04/01/2021
ms.openlocfilehash: 6529aa49d06e64947deb5ae54db0c39ad2575569
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106288590"
---
## <a name="business-disaster-recovery"></a>비즈니스 재해 복구

이 섹션에서는 재해가 발생하더라도 앱과 서비스 실행을 유지해주는 Azure Time Series Insights 기능에 대해 설명합니다(*비즈니스 재해 복구* 라고도 함).

### <a name="high-availability"></a>고가용성

Azure 서비스 중 하나로서 Azure Time Series Insights는 Azure 지역 수준에서의 중복을 통해 *고가용성* 기능을 제공합니다. 예를 들어 Azure는 Azure의 *지역 간 가용성* 기능을 통해 재해 복구 기능을 지원합니다.

Azure를 통해 제공되는(그리고 Azure Time Series Insights 인스턴스에도 제공되는) 추가적 고가용성 기능은 다음과 같습니다.

- **장애 조치(Failover)** : Azure는 [지역 복제 및 부하 분산](/azure/architecture/resiliency/recovery-loss-azure-region)을 제공합니다.
- **데이터 복원** 및 **스토리지 복구**: Azure는 [데이터를 유지하고 복구하기 위한 몇 가지 옵션](/azure/architecture/resiliency/recovery-data-corruption)을 제공합니다.
- **Azure Site Recovery**: Azure는 [Azure Site Recovery](../articles/site-recovery/index.yml)를 통해 복구 기능을 제공합니다.
- **Azure Backup**: [Azure Backup](../articles/backup/backup-architecture.md)은 Azure VM의 온-프레미스 및 클라우드 백업을 모두 지원합니다.

관련 Azure 기능을 활성화하여 디바이스 및 사용자에 대한 글로벌한, 지역 간 고가용성을 제공하도록 합니다.

> [!NOTE]
> 지역 간 가용성을 사용하도록 Azure를 구성하는 경우 Azure Time Series Insights에 추가적인 지역 간 가용성 구성이 필요하지 않습니다.

### <a name="iot-and-event-hubs"></a>IoT 및 이벤트 허브

일부 Azure IoT 서비스에는 기본 제공 비즈니스 재해 복구 기능도 포함되어 있습니다.

- 지역 내 중복성을 포함하는 [Azure IoT Hub 고가용성 재해 복구](../articles/iot-hub/iot-hub-ha-dr.md)
- [Azure Event Hubs 정책](../articles/event-hubs/event-hubs-geo-dr.md)
- [Azure Storage 중복성](../articles/storage/common/storage-redundancy.md)

다른 서비스와 Azure Time Series Insights를 통합하면 추가적인 재해 복구 기회가 제공됩니다. 예를 들어 이벤트 허브로 전송되는 원격 분석은 백업 Azure Blob Storage 데이터베이스에 유지될 수 있습니다.

### <a name="azure-time-series-insights"></a>Azure Time Series Insights

Azure Time Series Insights 데이터, 앱 및 서비스가 중단되더라도 계속 실행시킬 수 있는 여러 가지 방법이 있습니다.

그러나 다음과 같은 목적으로 Azure Time Series 환경의 전체 백업본이 필요하다고 결정할 수도 있습니다.

- 특별히 Azure Time Series Insights에 대한 *장애 조치(failover) 인스턴스* 로, 데이터 및 트래픽을 리디렉션하는 경우
- 데이터 및 감사 정보 보존

일반적으로 Azure Time Series Insights 환경을 복제하는 가장 좋은 방법은 백업 Azure 지역에서 두 번째 Azure Time Series Insights 환경을 만드는 것입니다. 이벤트는 주 이벤트 원본에서 보조 환경으로 전송됩니다. 두 번째 전용 소비자 그룹을 사용하는지 확인합니다. 앞에서 설명한 대로 해당 원본의 비즈니스 재해 복구 지침을 따릅니다.

중복 환경 만드는 방법:

1. 두 번째 Azure 지역에 환경을 만듭니다. 자세한 내용은 [Azure Portal에서 새 Azure Time Series Insights 환경 만들기](../articles/time-series-insights/time-series-insights-get-started.md)를 참조하세요.
1. 이벤트 원본에 대해 두 번째 전용 소비자 그룹을 만듭니다.
1. 해당 이벤트 원본을 새 환경에 연결합니다. 두 번째 전용 소비자 그룹을 지정했는지 확인합니다.
1. Azure Time Series Insights [IoT Hub](../articles/time-series-insights/how-to-ingest-data-iot-hub.md) 및 [Event Hubs](../articles/time-series-insights/concepts-access-policies.md) 설명서를 검토합니다.

이벤트가 발생하면

1. 재해 인시던트 중 주 지역이 영향을 받을 경우 작업을 백업 Azure Time Series Insights 환경으로 전환합니다.
1. 장애 조치(failover) 후 허브 시퀀스 번호가 0에서 다시 시작되므로 소비자 그룹이 다른 두 지역/환경에서 이벤트 원본을 다시 만들어 중복 이벤트처럼 보이지 않도록 합니다.
1. 두 번째 지역을 사용하여 모든 Azure Time Series Insights 원격 분석 및 쿼리 데이터를 백업 및 복구합니다.

> [!IMPORTANT]
> 장애 조치(failover)가 발생하면
>
> - 연기가 발생할 수도 있습니다.
> - 작업이 다시 라우팅될 때 메시지 처리에 일시적 스파이크가 발생할 수 있습니다.
>
> 자세한 내용은 [Azure Time Series Insights의 대기 시간 완화](../articles/time-series-insights/time-series-insights-environment-mitigate-latency.md)를 참조하세요.