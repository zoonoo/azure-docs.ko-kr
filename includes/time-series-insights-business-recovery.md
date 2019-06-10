---
ms.topic: include
ms.service: time-series-insights
author: kingdomofends
ms.author: adgera
ms.date: 04/29/2019
ms.openlocfilehash: 8a3c630b54ff95a9b1200e2421c787a514a0aa52
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431052"
---
## <a name="business-disaster-recovery"></a>비즈니스 재해 복구

이 섹션에서는 재해 발생 하는 경우에 앱 및 서비스를 실행 상태로 유지 하는 Azure Time Series Insights의 기능을 설명 (이라고 *비즈니스 재해 복구*).

### <a name="high-availability"></a>고가용성

Time Series Insights 제공 특정 Azure 서비스인 *고가용성* Azure 지역 수준에서 중복성을 사용 하 여 기능 합니다. Azure는 Azure의 재해 복구 기능을 지원 하는 예를 들어 *지역 간 가용성* 기능입니다.

Azure (및 Time Series Insights 인스턴스에도 적용 가능)을 통해 제공 되는 추가 고가용성 기능은 다음과 같습니다.

- **장애 조치(failover)** : Azure에서 제공 [지역에서 복제 및 부하 분산](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)합니다.
- **데이터 복원을** 하 고 **저장소 복구**: Azure에서 제공 [몇 가지 옵션을 유지 하 고 데이터를 복구할](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption)합니다.
- **사이트 복구**: Azure를 통해 사이트 복구 기능을 제공 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/)합니다.

장치 및 사용자에 대 한 전역, 지역 간 고가용성을 제공 하려면 관련 된 Azure 기능을 사용 하도록 설정 했는지 확인 합니다.

> [!NOTE]
> Azure 지역 간 가용성을 사용 하도록 구성 되었는지, 지역 간 가용성 추가 구성 없이 Azure Time Series Insights에서 필요 합니다.

### <a name="iot-and-event-hubs"></a>IoT 및 이벤트 허브

일부 Azure IoT 서비스에는 기본 제공 비즈니스 재해 복구 기능이 포함 됩니다.

- [IoT Hub 고가용성 재해 복구](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr), 지역 간 중복성을 포함 하 여
- [Event Hubs 정책](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)
- [Azure Storage 중복성](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

추가적인 재해 복구 기회를 제공 다른 서비스를 사용 하 여 Time Series Insights를 통합 합니다. 예를 들어, 이벤트 허브에 보낸 원격 분석 Azure Blob storage 데이터베이스 백업으로 유지 될 수 있습니다.

### <a name="time-series-insights"></a>Time Series Insights

중단 하는 경우에 되도록 Time Series Insights 데이터, 앱 및 서비스를 실행 하는 방법은 여러 가지가 있습니다. 

Azure Time Series 환경의 전체 백업 복사본을도 필요 하다는 다음 용도로 확인할 수 있습니다.

- 로 *장애 조치 인스턴스의* Time Series Insights 데이터를 리디렉션할 트래픽을를 위해 특별히
- 데이터 및 감사 정보를 유지 하기 위해

일반적으로 Time Series Insights 환경을 복제 하는 가장 좋은 방법은 백업 Azure 지역에에서 두 번째 Time Series Insights 환경을 만드는 방법은입니다. 이벤트 주 이벤트 원본에서이 보조 환경에도 전송 됩니다. 두 번째, 전용 소비자 그룹을 사용 하는지 확인 합니다. 앞에서 설명한 대로 해당 원본 비즈니스 재해 복구 지침을 따릅니다.

중복 된 환경을 만들려면:

1. 두 번째 Azure 지역에 환경을 만듭니다. 자세한 내용은 [Azure portal에서 새로운 Time Series Insights 환경 만들기](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)합니다.
1. 이벤트 원본에 대해 두 번째 전용 소비자 그룹을 만듭니다.
1. 해당 이벤트 원본을 새 환경에 연결합니다. 두 번째, 전용 소비자 그룹을 지정 하는 있는지 확인 합니다.
1. Time Series Insights를 검토 [IoT Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) 하 고 [Event Hubs](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access) 설명서.

이벤트가 발생 합니다.

1. 주 지역이 재해 인시던트 중 영향을 받을 경우 백업 Time Series Insights 환경으로 작업 경로를 전환합니다.
1. 백업 및 모든 Time Series Insights 원격 분석 및 쿼리 데이터를 복구 하 여 두 번째 영역을 사용 합니다.

> [!IMPORTANT]
> 장애 조치가 발생 합니다.
> 
> * 지연에도 발생할 수 있습니다.
> * 작업 다시 라우트 같이 순간적인 급증 메시지 처리에서 발생할 수 있습니다.
> 
> 자세한 내용은 [Time Series Insights에서 대기 시간 완화](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency)를 참조하세요.

