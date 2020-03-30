---
ms.topic: include
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 02/03/2020
ms.openlocfilehash: 6a3837d01815306e469a684404ab76506f547f43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77013819"
---
## <a name="business-disaster-recovery"></a>비즈니스 재해 복구

이 섹션에서는 재해가 발생하더라도 앱과 서비스를 계속 실행하는 Azure 시계열 인사이트 *기능(비즈니스 재해 복구)에*대해 설명합니다.

### <a name="high-availability"></a>고가용성

Azure 서비스로서, 타임시리즈 인사이트는 Azure 지역 수준에서 중복을 사용하여 특정 *고가용성* 기능을 제공합니다. 예를 들어 Azure는 Azure의 지역 *간 가용성* 기능을 통해 재해 복구 기능을 지원합니다.

Azure를 통해 제공되는 추가 고가용성 기능(및 모든 열렬 인사이트 인스턴스에서도 사용 가능)은 다음과 같습니다.

- **장애 조치**: Azure는 [지역 복제 및 로드 분산을](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)제공합니다.
- **데이터 복원** 및 **저장소 복구**: Azure는 [데이터를 보존하고 복구하는 몇 가지 옵션을](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption)제공합니다.
- **Azure 사이트 복구**: Azure는 [Azure 사이트](https://docs.microsoft.com/azure/site-recovery/)복구를 통해 사이트 복구 기능을 제공합니다.
- **Azure 백업**: [Azure Backup은](https://docs.microsoft.com/azure/backup/backup-architecture) Azure VM의 온-프레미스 및 클라우드 내 백업을 모두 지원합니다.

장치 및 사용자에 대한 글로벌 지역 간 고가용성을 제공하기 위해 관련 Azure 기능을 사용하도록 설정해야 합니다.

> [!NOTE]
> Azure가 지역 간 가용성을 사용하도록 구성된 경우 Azure Time Series Insights에서 추가 지역 간 가용성 구성이 필요하지 않습니다.

### <a name="iot-and-event-hubs"></a>IoT 및 이벤트 허브

일부 Azure IoT 서비스에는 기본 제공 비즈니스 재해 복구 기능도 포함됩니다.

- 지역 내 중복성을 포함하는 [Azure IoT Hub 고가용성 재해 복구](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr)
- [Azure 이벤트 허브 정책](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)
- [Azure 스토리지 중복성](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

Time Series Insights를 다른 서비스와 통합하면 추가적인 재해 복구 기회를 얻을 수 있습니다. 예를 들어 이벤트 허브로 전송된 원격 분석이 백업 Azure Blob 저장소 데이터베이스에 유지될 수 있습니다.

### <a name="time-series-insights"></a>Time Series Insights

시계열 인사이트 데이터, 앱 및 서비스가 중단되더라도 여러 가지 방법으로 실행상태를 유지할 수 있습니다. 

그러나 다음과 같은 목적으로 Azure Time Series 환경의 전체 백업 복사본도 필요하다고 판단할 수 있습니다.

- 데이터 및 트래픽을 리디렉션하기 위해 특별히 타임시리즈 인사이트를 위한 *장애 조치 인스턴스로*
- 데이터 및 감사 정보 보존

일반적으로 타임시리즈 인사이트 환경을 복제하는 가장 좋은 방법은 백업 Azure 리전에서 두 번째 열렬 인사이트 환경을 만드는 것입니다. 이벤트는 기본 이벤트 원본에서 이 보조 환경으로도 전송됩니다. 두 번째 전용 소비자 그룹을 사용해야 합니다. 앞서 설명한 대로 해당 원본의 비즈니스 재해 복구 지침을 따릅니다.

중복 환경을 만들려면 다음을 수행합니다.

1. 두 번째 Azure 지역에 환경을 만듭니다. 자세한 내용은 [Azure 포털에서 새 타임시리즈 인사이트 환경 만들기를](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)참조하십시오.
1. 이벤트 원본에 대해 두 번째 전용 소비자 그룹을 만듭니다.
1. 해당 이벤트 원본을 새 환경에 연결합니다. 두 번째 전용 소비자 그룹을 지정해야 합니다.
1. 타임시리즈 인사이트 [IoT 허브](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) 및 이벤트 허브 설명서를 [검토합니다.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access)

이벤트가 발생하는 경우:

1. 주 지역이 재해 인시던트 중 영향을 받을 경우 백업 Time Series Insights 환경으로 작업 경로를 전환합니다.
1. 두 번째 리전을 사용하여 모든 Time Series Insights 원격 분석 및 쿼리 데이터를 백업하고 복구합니다.

> [!IMPORTANT]
> 장애 조치(failover)가 발생하는 경우:
> 
> * 지연이 발생할 수도 있습니다.
> * 작업 경로가 변경되면 메시지 처리가 순간적으로 급증할 수 있습니다.
> 
> 자세한 내용은 [타임시리즈 인사이트 내 대기 시간 완화를](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency)참조하십시오.

