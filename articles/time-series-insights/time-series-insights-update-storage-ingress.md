---
title: 미리 보기의 데이터 저장 및 수집 - Azure 타임시리즈 인사이트 | 마이크로 소프트 문서
description: Azure Time Series 인사이트 미리 보기에서 데이터 저장소 및 수집에 대해 알아봅니다.
author: lyrana
ms.author: lyhughes
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/10/2020
ms.custom: seodec18
ms.openlocfilehash: 2f12cf303c58f0fa614c59ffe643c6c2ee5d2415
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78246197"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Azure Time Series Insights 미리 보기의 데이터 스토리지 및 수신

이 문서에서는 Azure Time Series Insights 미리 보기에 대한 데이터 저장소 및 수집에 대한 업데이트에 대해 설명합니다. 기본 저장소 구조, 파일 형식 및 타임시리즈 ID 속성에 대해 설명합니다. 기본 시작 프로세스, 모범 사례 및 현재 미리 보기 제한사항에 대해서도 설명합니다.

## <a name="data-ingress"></a>데이터 수집

Azure Time Series Insights 환경에는 패계 데이터를 수집, 처리 및 저장하는 *수집 엔진이* 포함되어 있습니다. 

환경을 계획할 때 들어오는 모든 데이터가 처리되고, 높은 수신 규모를 달성하고, *수집 대기 시간(타임시리즈* Insights에서 이벤트 소스에서 데이터를 읽고 처리하는 데 걸린 시간)을 최소화하기 위해 염두에 두어야 할 몇 가지 고려 사항이 [있습니다.](time-series-insights-update-plan.md)

Time Series Insights 데이터 인서빙 정책은 데이터를 어디에서 sourced할 수 있는지, 데이터의 형식을 결정합니다.

### <a name="ingress-policies"></a>정책

*데이터 수집에는* 데이터가 Azure Time Series Insights 미리 보기 환경으로 전송되는 방식이 포함됩니다. 

주요 구성, 서식 및 모범 사례는 아래에 요약되어 있습니다.

#### <a name="event-sources"></a>이벤트 원본

Azure Time Series 인사이트 미리 보기는 다음 이벤트 소스를 지원합니다.

- [Azure IoT 허브](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Azure Time Series Insights 미리 보기는 인스턴스당 최대 두 개의 이벤트 소스를 지원합니다.

> [!IMPORTANT] 
> * 미리 보기 환경에 이벤트 소스를 연결할 때 초기 대기 시간이 매우 높을 수 있습니다. 
> 이벤트 소스 대기 시간은 현재 IoT Hub 또는 이벤트 허브에 있는 이벤트 수에 따라 다릅니다.
> * 이벤트 원본 데이터를 처음 수집한 후 대기 시간이 길어집니다. 대기 시간이 계속 되는 경우 Azure 포털을 통해 지원 티켓을 제출합니다.

#### <a name="supported-data-format-and-types"></a>지원되는 데이터 형식 및 유형

Azure Time Series Insights는 Azure IoT 허브 또는 Azure 이벤트 허브에서 보낸 UTF-8 인코딩된 JSON을 지원합니다. 

지원되는 데이터 형식은 다음과 같습니다.

| 데이터 형식 | 설명 |
|---|---|
| **bool** | 두 가지 상태 중 하나를 `true` `false`갖는 데이터 형식: 또는 . |
| **Datetime** | 일반적으로 날짜와 시간으로 표시된 시간을 나타냅니다. [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) 형식으로 표현되었습니다. |
| **더블** | 이중 정밀도 64비트 [IEEE 754](https://ieeexplore.ieee.org/document/8766229) 부동 점. |
| **문자열** | 유니코드 문자로 구성되는 텍스트 값입니다.          |

#### <a name="objects-and-arrays"></a>개체 및 배열

이벤트 페이로드의 일부로 개체 및 배열과 같은 복잡한 형식을 보낼 수 있지만 데이터가 저장되면 병합 프로세스가 진행됩니다. 

JSON 이벤트 의 형성, 복잡한 형식 전송 및 중첩 된 개체 병합 방법을 설명하는 자세한 정보는 계획 및 최적화를 지원하기 [위해 수신 및 쿼리에 대한 JSON을 셰이핑하는 방법에서](./time-series-insights-update-how-to-shape-events.md) 사용할 수 있습니다.

### <a name="ingress-best-practices"></a>수레 모범 사례

다음과 같은 모범 사례를 사용하는 것이 좋습니다.

* 잠재적인 대기 시간을 줄이기 위해 동일한 리전에서 Azure Time Series Insights 및 IoT 허브 또는 이벤트 허브를 구성합니다.

* 예상 섭취 율을 계산하고 아래에 나열된 지원 되는 비율에 속하는지 확인 하여 [규모 요구에 대 한 계획](time-series-insights-update-plan.md) 합니다.

* [인트레스와 쿼리에 대한 JSON 을 형성하는 방법을](./time-series-insights-update-how-to-shape-events.md)읽어 서 미리 보기의 현재 제한 사항뿐만 아니라 JSON 데이터를 최적화하고 형성하는 방법을 이해합니다.

### <a name="ingress-scale-and-preview-limitations"></a>인서 스케일 및 미리 보기 제한 

Azure Time Series 인사이트 미리 보기 시작 제한사항은 아래에 설명되어 있습니다.

> [!TIP]
> 모든 미리 보기 제한에 대한 포괄적인 목록은 [미리 보기 환경 계획을](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits) 읽어보십시오.

#### <a name="per-environment-limitations"></a>환경별 제한 사항

일반적으로 침투 속도는 조직에 있는 장치 수, 이벤트 방출 빈도 및 각 이벤트의 크기에 대한 요인으로 간주됩니다.

*  **장치 수** × **이벤트 방출 빈도** × 각 이벤트의 **크기.**

기본적으로 타임시리즈 인사이트 미리 보기는 **시간시리즈 인사이트 환경당 최대 1MB(초당 MBps)의**속도로 들어오는 데이터를 수집할 수 있습니다.

> [!TIP] 
> * 요청시 최대 16MBps의 속도를 섭취하기 위한 환경 지원을 제공할 수 있습니다.
> * Azure 포털을 통해 지원 티켓을 제출하여 더 높은 처리량이 필요한 경우 문의하십시오.
 
* **예 1:**

    Contoso Shipping에는 분당 3회 이벤트를 방출하는 100,000개의 장치가 있습니다. 이벤트 크기는 200바이트입니다. 4개의 파티션이 있는 이벤트 허브를 타임시리즈 Insights 이벤트 소스로 사용하고 있습니다.

    * 자신의 타임 시리즈 인사이트 환경에 대한 섭취 속도는 다음과 될 것입니다 : **100,000 장치 * 200 바이트 / 이벤트 * (3/60 이벤트 / 초) = 1 MBps.**
    * 파티션당 섭취 속도는 0.25MBps입니다.
    * Contoso Shipping의 섭취 율은 미리 보기 눈금 제한 내에 있습니다.

* **예제 2:**

    Contoso 플릿 애널리틱스에는 매초 이벤트를 방출하는 60,000개의 장치가 있습니다. 그들은 타임 시리즈 인사이트 이벤트 소스로 4의 IoT Hub 24 파티션 수를 사용하고 있습니다. 이벤트 크기는 200바이트입니다.

    * 환경 사용률은 **20,000개 장치 * 200바이트/이벤트 * 이벤트/초 1회 = 4MBps입니다.**
    * 파티션당 속도는 1MBps입니다.
    * Contoso 플릿 애널리틱스는 Azure 포털을 통해 열렬 인사이트에 요청을 제출하여 해당 환경에 대한 수집 속도를 높일 수 있습니다.

#### <a name="hub-partitions-and-per-partition-limits"></a>허브 파티션 및 파티션당 제한

타임시리즈 인사이트 환경을 계획할 때는 타임시리즈 인사이트에 연결할 이벤트 소스의 구성을 고려해야 합니다. Azure IoT Hub와 이벤트 허브 모두 파티션을 사용하여 이벤트 처리를 위한 수평 배율을 활성화합니다. 

*파티션은* 허브에서 열리는 이벤트의 순서가 지정된 시퀀스입니다. 파티션 수는 허브 생성 단계에서 설정되며 변경할 수 없습니다. 

모범 사례를 분할하는 이벤트 허브의 경우 [필요한 파티션 수를 검토하십시오.](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need)

> [!NOTE]
> Azure Time Series Insights와 함께 사용되는 대부분의 IoT 허브에는 4개의 파티션만 필요합니다.

Time Series Insights 환경에 대한 새 허브를 만들든 기존 허브를 사용하든 파티션당 생성 속도를 계산하여 미리 보기 한도 내에 있는지 확인해야 합니다. 

Azure Time Series 인사이트 미리 보기에는 현재 **파티션당 일반 제한이 0.5MBps입니다.**

#### <a name="iot-hub-specific-considerations"></a>IoT 허브관련 고려 사항

장치가 IoT Hub에서 만들어지면 파티션에 영구적으로 할당됩니다. 이렇게 하면 IoT Hub는 할당이 변경되지 않기 때문에 이벤트 순서를 보장할 수 있습니다.

고정 파티션 할당은 IoT Hub 다운스트림에서 전송된 데이터를 수집하는 Time Series Insights 인스턴스에도 영향을 미칩니다. 동일한 게이트웨이 장치 ID를 사용하여 여러 장치의 메시지가 허브로 전달되면 파티션별 확장 제한을 초과할 수 있는 동시에 동일한 파티션에 도착할 수 있습니다. 

**영향**:

* 단일 파티션에서 미리 보기 제한을 통해 지속적인 수집 속도가 발생하는 경우 IT Hub 데이터 보존 기간이 초과되기 전에 Time Series Insights가 모든 장치 원격 분석을 동기화하지 않을 수 있습니다. 따라서 수집 한도가 지속적으로 초과되면 전송된 데이터가 손실될 수 있습니다.

이러한 상황을 완화하려면 다음 모범 사례를 권장합니다.

* 솔루션을 배포하기 전에 환경당 및 파티션 별 구성 비율을 계산합니다.
* IoT Hub 장치가 가능한 한 가장 먼 범위까지 부하 균형을 이루고 있는지 확인합니다.

> [!IMPORTANT]
> 이벤트 소스로 IoT Hub를 사용하는 환경의 경우 사용 중허브 장치 수를 사용하여 사용 속도를 계산하여 미리 보기에서 파티션당 0.5MBps 미만으로 떨어지는지 확인합니다.
> * 여러 이벤트가 동시에 도착하더라도 미리 보기 한도를 초과하지 않습니다.

  ![IoT 허브 파티션 다이어그램](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

허브 처리량 및 파티션 최적화에 대해 자세히 알아보려면 다음 리소스를 참조하십시오.

* [IoT 허브 스케일](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [이벤트 허브 스케일](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [이벤트 허브 파티션](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>데이터 스토리지

페이그(PAYG) SKU 환경의 타임시리즈 인사이트 미리 보기를 만들 면 두 개의 Azure 리소스를 만듭니다. *pay-as-you-go*

* 웜 데이터 저장소에 대해 구성할 수 있는 Azure Time Series 인사이트 미리 보기 환경입니다.
* Azure 저장소 범용 V1 Blob 콜드 데이터 저장소에 대 한 계정입니다.

웜 저장소의 데이터는 [열렬 쿼리](./time-series-insights-update-tsq.md) 및 [Azure Time Series 인사이트 미리 보기 탐색기를](./time-series-insights-update-explorer.md)통해서만 사용할 수 있습니다. 웜 저장소에는 타임시리즈 인사이트 환경을 만들 때 선택한 [보존 기간](./time-series-insights-update-plan.md#the-preview-environment) 내의 최근 데이터가 포함됩니다.

타임시리즈 인사이트 미리 보기는 콜드 스토어 데이터를 [Parquet 파일 형식의](#parquet-file-format-and-folder-structure)Azure Blob 저장소에 저장합니다. 타임 시리즈 인사이트 미리 보기는 이 콜드 스토어 데이터를 독점적으로 관리하지만 표준 마루 파일로 직접 읽을 수 있습니다.

> [!WARNING]
> 콜드 저장소 데이터가 있는 Azure Blob 저장소 계정의 소유자는 계정의 모든 데이터에 대한 전체 액세스 권한을 갖습니다. 이 액세스에는 쓰기 및 삭제 권한이 포함됩니다. 데이터 손실을 초래할 수 있으므로 타임시리즈 인사이트 미리 보기에서 작성하는 데이터를 편집하거나 삭제하지 마십시오.

### <a name="data-availability"></a>데이터 가용성

Azure Time Series Insights 는 최적의 쿼리 성능을 위해 데이터를 분할하고 인덱싱합니다. 데이터는 인덱싱된 후 웜(활성화된 경우)과 콜드 스토어모두에서 쿼리할 수 있습니다. 수집되는 데이터의 양은 이 가용성에 영향을 줄 수 있습니다.

> [!IMPORTANT]
> 미리 보기 중에 데이터를 사용할 수 있게 되기 까지 최대 60초의 기간이 발생할 수 있습니다. 60초를 초과하는 상당한 지연 시간이 발생하는 경우 Azure 포털을 통해 지원 티켓을 제출하십시오.

## <a name="azure-storage"></a>Azure Storage

이 섹션에서는 Azure Time Series 인사이트 미리 보기와 관련된 Azure 저장소 세부 정보를 설명합니다.

Azure Blob 저장소에 대한 자세한 설명은 [저장소 Blob 소개를](../storage/blobs/storage-blobs-introduction.md)참조하십시오.

### <a name="your-storage-account"></a>스토리지 계정

Azure Time Series 인사이트 미리 보기 PAYG 환경을 만들 면 Azure 저장소 범용 V1 Blob 계정이 장기 콜드 저장소로 만들어집니다.  

Azure Time Series 인사이트 미리 보기는 Azure Storage 계정에 각 이벤트의 복사본을 최대 2개까지 유지합니다. 한 복사본은 섭취 시간에 따라 정렬된 이벤트를 저장하므로 항상 시간 정렬된 순서의 시퀀스에서 이벤트에 액세스할 수 있습니다. 시간이 지남에 따라 타임시리즈 인사이트 미리 보기는 수행되는 Time Series Insights 쿼리에 최적화할 수 있도록 데이터의 재분할된 복사본을 만듭니다. 

공개 미리 보기 중에 데이터는 Azure Storage 계정에 무기한 저장됩니다.

#### <a name="writing-and-editing-time-series-insights-blobs"></a>Time Series Insights blob 작성 및 편집

쿼리 성능 및 데이터 가용성을 보장하려면 [열계 인사이트 미리 보기]에서 만드는 Blob을 편집하거나 삭제하지 마세요.

#### <a name="accessing-time-series-insights-preview-cold-store-data"></a>열렬 인사이트 미리 보기 콜드 스토어 데이터 액세스 

[타임시리즈 인사이트 미리 보기 탐색기](./time-series-insights-update-explorer.md) 및 [타임시리즈 쿼리에서](./time-series-insights-update-tsq.md)데이터에 액세스하는 것 외에도 콜드 스토어에 저장된 마루 파일에서 직접 데이터에 액세스할 수도 있습니다. 예를 들어 Jupyter 노트북에서 데이터를 읽고 변환하고 정리한 다음 이 데이터를 사용하여 동일한 Spark 워크플로에서 Azure 기계 학습 모델을 학습할 수 있습니다.

Azure Storage 계정에서 직접 데이터에 액세스하려면 타임시리즈 인사이트 미리 보기 데이터를 저장하는 데 사용되는 계정에 대한 읽기 액세스가 필요합니다. 그런 다음 마루 파일 형식 섹션에 설명된 `PT=Time` 폴더에 있는 Parquet [파일의](#parquet-file-format-and-folder-structure) 생성 시간을 기준으로 선택한 데이터를 읽을 수 있습니다.  저장소 계정에 대한 읽기 액세스 활성화에 대한 자세한 내용은 [저장소 계정 리소스에 대한 액세스 관리를](../storage/blobs/storage-manage-access-to-resources.md)참조하십시오.

#### <a name="data-deletion"></a>데이터 삭제

타임시리즈 인사이트 미리 보기 파일을 삭제하지 마세요. 열렬 인사이트 미리 보기 내에서만 관련 데이터를 관리합니다.

### <a name="parquet-file-format-and-folder-structure"></a>마루 파일 형식 및 폴더 구조

마루는 효율적인 저장 및 성능을 위해 설계된 오픈 소스 컬럼 파일 형식입니다. 타임시리즈 인사이트 미리 보기는 마루를 사용하여 열계 ID 기반 쿼리 성능을 대규모로 활성화합니다.  

마루 파일 형식에 대한 자세한 내용은 [Parquet 설명서를](https://parquet.apache.org/documentation/latest/)참조하십시오.

타임시리즈 인사이트 미리 보기는 다음과 같이 데이터의 복사본을 저장합니다.

* 첫 번째 초기 복사본은 수집 시간으로 분할되고 도착 순서대로 대략 데이터를 저장합니다. 이 데이터는 폴더에 `PT=Time` 있습니다.

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* 다시 분할된 두 번째 복사본은 타임시리즈 아이디로 그룹화되어 폴더에 `PT=TsId` 있습니다.

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

두 경우 모두 Parquet 파일의 시간 속성은 Blob 생성 시간에 해당합니다. 폴더의 `PT=Time` 데이터는 파일에 기록되면 변경 없이 보존됩니다. 폴더의 `PT=TsId` 데이터는 시간이 지남에 따라 쿼리에 최적화되며 정적이지 않습니다.

> [!NOTE]
> * `<YYYY>`4자리 연도 표현에 매핑됩니다.
> * `<MM>`두 자리 월 표현에 매핑됩니다.
> * `<YYYYMMDDHHMMSSfff>`4자리 연도 (), 두 자리 월`YYYY`(),`MM`두 자리 일 (),`DD`두 자리 시간`HH`(), 두`MM`자리 시간 (),`SS`두 자리 초 (), 두 자리 초 () 및 세 자리 밀리초 ()와`fff`타임 스탬프 표현에 매핑됩니다.

타임시리즈 인사이트 미리 보기 이벤트는 다음과 같이 Parquet 파일 내용에 매핑됩니다.

* 각 이벤트는 단일 행에 매핑됩니다.
* 모든 행에는 이벤트 **타임스탬프가** 있는 타임스탬프 열이 포함됩니다. 타임스탬프 속성은 null이 없습니다. 타임스탬프 속성이 이벤트 원본에 지정되지 않은 경우 이벤트 **큐에 대기된 시간으로** 기본설정됩니다. 저장된 타임스탬프는 항상 UTC에 있습니다.
* 모든 행에는 타임시리즈 인사이트 환경이 생성될 때 정의된 대로 TSID(타임시리즈 ID) 열이 포함됩니다. TSID 속성 이름에는 `_string` 접미사가 포함됩니다.
* 원격 분석 데이터로 전송된 다른 모든 속성은 속성 `_string` 유형에 `_bool` 따라 (문자열), (부울), `_datetime` (datetime) 또는 `_double` (double)로 끝나는 열 이름에 매핑됩니다.
* 이 매핑 스키마는 **V=1로** 참조되고 동일한 이름의 기본 폴더에 저장된 파일 형식의 첫 번째 버전에 적용됩니다. 이 기능이 발전함에 따라 이 매핑 스키마가 변경될 수 있으며 참조 이름이 증가합니다.

## <a name="next-steps"></a>다음 단계

- [받는 글및 쿼리에 대한 JSON을 형성하는 방법을](./time-series-insights-update-how-to-shape-events.md)읽어보십시오.

- 새 데이터 [모델링에](./time-series-insights-update-tsm.md)대해 읽어보십시오.
