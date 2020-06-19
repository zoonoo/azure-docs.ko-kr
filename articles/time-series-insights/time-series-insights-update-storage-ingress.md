---
title: 미리 보기의 데이터 스토리지 및 수신 - Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights 미리 보기의 데이터 스토리지 및 수신에 대해 알아봅니다.
author: lyrana
ms.author: lyhughes
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/27/2020
ms.custom: seodec18
ms.openlocfilehash: ca5ba8d7b2d78440401e29344361538c3650ba48
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779172"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Azure Time Series Insights 미리 보기의 데이터 스토리지 및 수신

이 문서에서는 Azure Time Series Insights 미리 보기의 데이터 스토리지 및 수신 업데이트에 대해 설명합니다. 기본 스토리지 구조, 파일 형식 및 시계열 ID 속성을 설명합니다. 또한 기본 수신 프로세스, 모범 사례 및 현재 미리 보기 제한 사항도 설명합니다.

## <a name="data-ingress"></a>데이터 수신

Azure Time Series Insights 환경에는 시계열 데이터를 수집, 처리 및 저장하는 *수집 엔진*이 포함되어 있습니다.

[환경을 계획](time-series-insights-update-plan.md)하는 경우 들어오는 모든 데이터가 처리되도록 하고, 높은 수신 크기를 구현하고, *수집 대기 시간*(Time Series Insights에서 이벤트 원본으로부터 데이터를 읽고 처리하는 데 걸리는 시간)을 최소화하기 위해 고려해야 할 몇 가지 사항이 있습니다.

Time Series Insights 미리 보기의 데이터 수신 정책은 데이터의 원본이 있는 위치와 데이터 형식을 결정합니다.

### <a name="ingress-policies"></a>수신 정책

*데이터 수신*에는 데이터를 Azure Time Series Insights 미리 보기 환경으로 보내는 방법이 포함됩니다.

주요 구성, 형식 지정 및 모범 사례는 아래에 요약되어 있습니다.

#### <a name="event-sources"></a>이벤트 원본

Azure Time Series Insights 미리 보기에서 지원하는 이벤트 원본은 다음과 같습니다.

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Azure Time Series Insights 미리 보기는 인스턴스당 최대 두 개의 이벤트 원본을 지원합니다. 이벤트 원본이 연결되면 TSI 환경에서 가장 오래된 이벤트부터 시작하여 현재 IoT 또는 Event Hub에 저장된 모든 이벤트를 읽습니다.

> [!IMPORTANT]
>
> * 이벤트 원본을 미리 보기 환경에 연결할 때 초기 대기 시간이 길어질 수 있습니다.
> 이벤트 원본 대기 시간은 현재 IoT Hub 또는 Event Hub에 있는 이벤트 수에 따라 달라집니다.
> * 이벤트 원본 데이터가 처음 수집된 후에는 대기 시간이 줄어듭니다. 대기 시간이 계속 긴 경우 Azure Portal을 통해 지원 티켓을 제출하세요.

#### <a name="supported-data-format-and-types"></a>지원되는 데이터 형식 및 유형

Azure Time Series Insights는 Azure IoT Hub 또는 Azure Event Hubs에서 보내는 UTF-8로 인코딩된 JSON을 지원합니다. 

지원되는 데이터 형식은 다음과 같습니다.

| 데이터 형식 | Description |
|---|---|
| **bool** | `true` 또는 `false`의 두 가지 상태 중 하나인 데이터 형식입니다. |
| **dateTime** | 일반적으로 날짜와 시간으로 표현된 시간의 한 순간을 나타냅니다. [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) 형식으로 표현됩니다. |
| **double** | 배정밀도 64비트 [IEEE 754](https://ieeexplore.ieee.org/document/8766229) 부동 소수점입니다. |
| **string** | 유니코드 문자로 구성된 텍스트 값입니다.          |

#### <a name="objects-and-arrays"></a>개체 및 배열

이벤트 페이로드의 일부로 개체 및 배열과 같은 복합 형식을 보낼 수 있지만 데이터를 저장하면 평면화 프로세스가 수행됩니다.

JSON 이벤트를 셰이핑하는 방법, 복합 형식을 보내는 방법 및 중첩된 개체를 평면화하는 방법을 설명하는 자세한 정보는 계획 및 최적화를 지원하기 위해 [수신 및 쿼리 JSON을 셰이핑하는 방법](./time-series-insights-update-how-to-shape-events.md)에서 확인할 수 있습니다.

### <a name="ingress-best-practices"></a>수신 모범 사례

다음 모범 사례를 적용하는 것이 좋습니다.

* 잠재적 대기 시간을 줄이기 위해 Azure Time Series Insights와 IoT Hub 또는 Event Hub를 동일한 지역에 구성합니다.

* 예상 수집 속도를 계산하고 아래에 나열된 지원 속도 내에 있는지 확인하여 [크기 요구 사항에 맞게 계획](time-series-insights-update-plan.md)합니다.

* [수신 및 쿼리 JSON을 셰이핑하는 방법](./time-series-insights-update-how-to-shape-events.md)을 참조하여 JSON 데이터를 최적화하고 셰이핑하는 방법 및 현재의 미리 보기 제한 사항을 이해합니다.

* 거의 실시간 및 최근 데이터에만 스트리밍 수집을 사용합니다. 기록 데이터 스트리밍은 지원되지 않습니다.

#### <a name="historical-data-ingestion"></a>기록 데이터 수집

스트리밍 파이프라인을 사용하여 기록 데이터를 가져오는 것은 현재 Azure Time Series Insights 미리 보기에서 지원되지 않습니다. 이전 데이터를 사용자 환경으로 가져와야 하는 경우 아래 지침을 따릅니다.

* 라이브 데이터와 기록 데이터를 동시에 스트림하지 않습니다. 순서가 잘못된 데이터를 수집하면 쿼리 성능이 저하됩니다.
* 최상의 성능을 위해 기록 데이터를 지정된 시간 순서대로 수집합니다.
* 아래의 수집 처리량 속도 제한 내에서 유지합니다.
* 데이터가 웜 저장소 보존 기간보다 오래된 경우 웜 저장소를 사용하지 않도록 설정합니다.

### <a name="ingress-scale-and-preview-limitations"></a>수신 크기 조정 및 미리 보기 제한 사항

Azure Time Series Insights 미리 보기의 수신 제한 사항은 아래에 설명되어 있습니다.

> [!TIP]
> 모든 미리 보기 제한 사항에 대한 포괄적인 목록은 [미리 보기 환경 계획](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits)을 참조하세요.

#### <a name="per-environment-limitations"></a>환경당 제한

일반적으로 수신 속도는 조직 내 디바이스 수, 이벤트 내보내기 빈도 및 각 이벤트의 크기에 대한 요소로 표시됩니다.

*  **디바이스 수** × **이벤트 내보내기 빈도** × **각 이벤트의 크기**

기본적으로 Time Series Insights 미리 보기는 **Time Series Insights 환경당 최대 1MBps(초당 메가바이트)의 속도**로 들어오는 데이터를 수집할 수 있습니다. [허브 파티션](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-storage-ingress#hub-partitions-and-per-partition-limits)마다 추가 제한이 있습니다.

> [!TIP]
>
> * 요청에 따라 최대 16MBps의 수집 속도에 맞는 환경을 지원할 수 있습니다.
> * Azure Portal을 통해 지원 티켓을 제출하여 처리량을 늘려야 하는 경우 문의해 주세요.
 
* **예제 1:**

    Contoso Shipping에는 이벤트를 분당 3회 내보내는 100,000개의 디바이스가 있습니다. 이벤트의 크기는 200바이트입니다. 4개의 파티션이 있는 IoT Hub를 Time Series Insights 이벤트 원본으로 사용하고 있습니다.

    * Time Series Insights 환경의 수집 속도는 다음과 같습니다. **100,000개 디바이스 * 200바이트/이벤트 * (3/60 이벤트/초) = 1MBps**
    * 파티션당 수집 속도는 0.25MBps입니다.
    * Contoso Shipping의 수집 속도는 미리 보기 크기 제한 내에 있습니다.

* **예 2:**

    Contoso Fleet Analytics에는 1초마다 이벤트를 내보내는 60,000개의 디바이스가 있습니다. 파티션 수가 4개인 Event Hub를 Time Series Insights 이벤트 원본으로 사용하고 있습니다. 이벤트의 크기는 200바이트입니다.

    * 환경 수집 속도는 다음과 같습니다. **60,000개 디바이스 * 200바이트/이벤트 * 1개 이벤트/초 = 12MBps**
    * 파티션당 속도는 3MBps입니다.
    * Contoso Fleet Analytics의 수집 속도는 환경 및 파티션 제한을 초과합니다. Azure Portal을 통해 요청을 Time Series Insights에 제출하여 해당 환경의 수집 속도를 높이고, 미리 보기 제한 내에 더 많은 파티션이 있는 Event Hub를 만들 수 있습니다.

#### <a name="hub-partitions-and-per-partition-limits"></a>허브 파티션 및 파티션당 제한

Time Series Insights 환경을 계획하는 경우 Time Series Insights에 연결할 이벤트 원본의 구성을 고려해야 합니다. Azure IoT Hub 및 Event Hubs는 모두 파티션을 활용하여 이벤트 처리를 위한 수평 크기 조정을 사용하도록 설정합니다. 

*파티션*은 허브에서 저장되는 순서가 지정된 이벤트 시퀀스입니다. 파티션 수는 허브 만들기 중에 설정되며 변경할 수 없습니다.

Event Hubs 분할 모범 사례는 [필요한 파티션은 몇 개인가요?](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need)를 검토하세요.

> [!NOTE]
> Azure Time Series Insights에서 사용되는 대부분의 IoT Hub에는 4개의 파티션만 필요합니다.

Time Series Insights 환경에 대한 새 허브를 만드는지, 아니면 기존 허브를 사용하는지에 관계없이 파티션당 수집 속도를 계산하여 미리 보기 제한 내에 있는지 확인해야 합니다. 

Azure Time Series Insights 미리 보기에는 현재 일반적인 **파티션당 0.5MBps 제한**이 있습니다.

#### <a name="iot-hub-specific-considerations"></a>IoT Hub 관련 고려 사항

IoT Hub에서 디바이스가 만들어지면 파티션에 영구적으로 할당됩니다. 이렇게 하면 할당이 변경되지 않으므로 IoT Hub에서 이벤트 순서를 보장할 수 있습니다.

고정 파티션 할당은 IoT Hub 다운스트림에서 보낸 데이터를 수집하는 Time Series Insights 인스턴스에도 영향을 줍니다. 여러 디바이스에서 동일한 게이트웨이 디바이스 ID를 사용하여 메시지를 허브로 전달하는 경우 동일한 파티션에 동시에 도달하여 파티션당 크기 제한을 초과할 수 있습니다.

**영향**:

* 단일 파티션에서 미리 보기 제한을 초과하는 수집 속도가 지속적으로 발생하는 경우 IoT Hub 데이터 보존 기간이 초과되기 전에 Time Series Insights에서 모든 디바이스 원격 분석을 동기화하지 않을 수 있습니다. 따라서 수집 제한이 지속적으로 초과되면 전송된 데이터가 손실될 수 있습니다.

이러한 상황을 완화하려면 다음과 같은 모범 사례를 따르는 것이 좋습니다.

* 솔루션을 배포하기 전에 환경당 및 파티션당 수집 속도를 계산합니다.
* IoT Hub 디바이스가 가능한 최대의 범위까지 부하 분산되었는지 확인합니다.

> [!IMPORTANT]
> IoT Hub를 이벤트 원본으로 사용하는 환경의 경우 사용 중인 허브 디바이스의 수로 수집 속도를 계산하여 미리 보기의 파티션당 제한인 0.5MBps 미만으로 떨어지도록 합니다.
>
> * 여러 이벤트가 동시에 도착하는 경우에도 미리 보기 제한이 초과되지 않습니다.

  ![IoT Hub 파티션 다이어그램](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

허브 처리량 및 파티션을 최적화하는 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [IoT Hub 크기 조정](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Event Hub 크기 조정](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Event Hub 파티션](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>데이터 스토리지

Time Series Insights 미리 보기 *PAYG(종량제)* SKU 환경을 만들면 다음 두 가지 리소스가 만들어집니다.

* 웜 데이터 스토리지에 맞게 구성할 수 있는 Azure Time Series Insights 미리 보기 환경
* 콜드 데이터 스토리지를 위한 Azure Storage 범용 V1 Blob 계정

웜 저장소의 데이터는 [시계열 쿼리](./time-series-insights-update-tsq.md) 및 [Azure Time Series Insights 미리 보기 탐색기](./time-series-insights-update-explorer.md)를 통해서만 사용할 수 있습니다. 웜 저장소에는 Time Series Insights 환경을 만들 때 선택한 [보존 기간](./time-series-insights-update-plan.md#the-preview-environment) 내의 최신 데이터가 포함됩니다.

Time Series Insights 미리 보기는 콜드 저장소 데이터를 [Parquet 파일 형식](#parquet-file-format-and-folder-structure)으로 Azure Blob 스토리지에 저장합니다. Time Series Insights 미리 보기는 이 콜드 저장소 데이터를 단독으로 관리하지만 표준 Parquet 파일로 직접 읽을 수 있습니다.

> [!WARNING]
> 콜드 저장소 데이터가 있는 Azure Blob 스토리지 계정의 소유자는 계정의 모든 데이터에 대한 모든 액세스 권한을 갖습니다. 이 액세스 권한에는 쓰기 및 삭제 권한이 포함됩니다. 데이터 손실이 발생할 수 있으므로 Time Series Insights 미리 보기에서 쓰는 데이터는 편집하거나 삭제하지 마세요.

### <a name="data-availability"></a>데이터 가용성

Azure Time Series Insights 미리 보기는 최적의 쿼리 성능을 위해 데이터를 분할하고 인덱싱합니다. 데이터가 인덱싱된 후 웜(사용하도록 설정된 경우) 및 콜드 저장소에서 데이터를 쿼리할 수 있습니다. 수집되는 데이터의 양은 이 가용성에 영향을 줄 수 있습니다.

> [!IMPORTANT]
> 미리 보기 중에는 데이터를 사용할 수 있을 때까지 최대 60초까지 걸릴 수 있습니다. 60초가 넘는 긴 대기 시간이 발생하면 Azure Portal을 통해 지원 티켓을 제출하세요.

## <a name="azure-storage"></a>Azure Storage

이 섹션에서는 Azure Time Series Insights 미리 보기와 관련된 Azure Storage에 대해 자세히 설명합니다.

Azure Blob 스토리지에 대한 자세한 설명은 [Storage Blob 소개](../storage/blobs/storage-blobs-introduction.md)를 참조하세요.

### <a name="your-storage-account"></a>스토리지 계정

Azure Time Series Insights 미리 보기 PAYG 환경을 만들면 Azure Storage 범용 V1 Blob 계정이 장기 콜드 저장소로 만들어집니다.  

Azure Time Series Insights 미리 보기는 Azure Storage 계정에서 각 이벤트에 대한 최대 2개의 복사본을 유지합니다. 한 복사본은 수집 시간을 기준으로 정렬된 이벤트를 저장하므로 항상 시간 순서가 지정된 시퀀스로 이벤트에 액세스할 수 있습니다. 시간이 지남에 따라 Time Series Insights 미리 보기는 데이터의 다시 분할된 복사본을 만들어 성능이 향상된 Time Series Insights 쿼리를 최적화합니다.

공개 미리 보기 중에는 데이터가 Azure Storage 계정에 무기한 저장됩니다.

#### <a name="writing-and-editing-time-series-insights-blobs"></a>Time Series Insights blob 작성 및 편집

쿼리 성능 및 데이터 가용성을 보장하려면 Time Series Insights 미리 보기에서 만드는 Blob을 편집하거나 삭제하지 않습니다.

#### <a name="accessing-time-series-insights-preview-cold-store-data"></a>Time Series Insights 미리 보기 콜드 저장소 데이터에 액세스

[Time Series Insights 미리 보기 탐색기](./time-series-insights-update-explorer.md) 및 [시계열 쿼리](./time-series-insights-update-tsq.md)에서 데이터에 액세스하는 것 외에도 콜드 저장소에 저장된 Parquet 파일에서 데이터에 직접 액세스할 수도 있습니다. 예를 들어 Jupyter Notebook에서 데이터를 읽고 변환하고 정리한 다음, 이를 사용하여 동일한 Spark 워크플로에서 Azure Machine Learning 모델을 학습시킬 수 있습니다.

Azure Storage 계정에서 데이터에 직접 액세스하려면 Time Series Insights 미리 보기 데이터를 저장하는 데 사용되는 계정에 대한 읽기 액세스 권한이 필요합니다. 그러면 [Parquet 파일 형식](#parquet-file-format-and-folder-structure) 섹션 아래에서 설명하는 `PT=Time` 폴더에 있는 Parquet 파일의 만든 시간을 기준으로 선택한 데이터를 읽을 수 있습니다.  스토리지 계정에 대한 읽기 액세스를 사용하도록 설정하는 방법에 대한 자세한 내용은 [스토리지 계정 리소스에 대한 액세스 관리](../storage/blobs/storage-manage-access-to-resources.md)를 참조하세요.

#### <a name="data-deletion"></a>데이터 삭제

Time Series Insights 미리 보기 파일은 삭제하지 마세요. Time Series Insights 미리 보기 내에서만 관련 데이터를 관리합니다.

### <a name="parquet-file-format-and-folder-structure"></a>Parquet 파일 형식 및 폴더 구조

Parquet는 효율적인 스토리지와 성능을 위해 설계된 오픈 소스 열 형식 파일 형식입니다. Time Series Insights 미리 보기는 Parquet를 사용하여 시계열 ID 기반 쿼리 성능을 규모에 맞게 사용하도록 설정합니다.  

Parquet 파일 형식에 대한 자세한 내용은 [Parquet 설명서](https://parquet.apache.org/documentation/latest/)를 참조하세요.

Time Series Insights 미리 보기는 다음과 같이 데이터 복사본을 저장합니다.

* 첫 번째 초기 복사본은 수집 시간을 기준으로 분할되며, 대략 도착 순서대로 데이터를 저장합니다. 이 데이터는 `PT=Time` 폴더에 있습니다.

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* 다시 분할된 두 번째 복사본은 시계열 ID를 기준으로 그룹화되며 `PT=TsId` 폴더에 있습니다.

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

두 경우 모두에서 Parquet 파일의 시간 속성은 Blob을 만든 시간에 해당합니다. `PT=Time` 폴더의 데이터는 파일에 쓴 후에 변경 없이 유지됩니다. `PT=TsId` 폴더의 데이터는 시간이 지남에 따라 쿼리에 최적화되며 정적이지 않습니다.

> [!NOTE]
>
> * `<YYYY>`는 4자리 연도 표현에 매핑됩니다.
> * `<MM>`은 두 자리 월 표현에 매핑됩니다.
> * `<YYYYMMDDHHMMSSfff>`는 네 자리 연도(`YYYY`), 두 자리 월(`MM`), 두 자리 일(`DD`), 두 자리 시(`HH`), 두 자리 분(`MM`), 두 자리 초(`SS`) 및 세 자리 밀리초(`fff`)의 타임스탬프 표현에 매핑됩니다.

Time Series Insights 미리 보기 이벤트는 다음과 같이 Parquet 파일 콘텐츠에 매핑됩니다.

* 각 이벤트는 단일 행에 매핑됩니다.
* 모든 행에는 이벤트 타임스탬프가 있는 **timestamp** 열이 포함됩니다. 타임스탬프 속성은 null이 아닙니다. 이벤트 원본에서 타임스탬프 속성이 지정되지 않은 경우 기본적으로 **이벤트 큐 삽입 시간**이 설정됩니다. 저장된 타임스탬프는 항상 UTC 형식입니다.
* 모든 행에는 Time Series Insights 환경을 만들 때 정의된 TSID(시계열 ID) 열이 포함됩니다. TSID 속성 이름에는 `_string` 접미사가 포함됩니다.
* 원격 분석 데이터로 보내는 다른 모든 속성은 속성 유형에 따라 `_string`(문자열), `_bool`(부울), `_datetime`(날짜/시간) 또는 `_double`(배정밀도)로 끝나는 열 이름에 매핑됩니다.
* 이 매핑 스키마는 **V=1**로 참조되고, 동일한 이름의 기본 폴더에 저장된 파일 형식의 첫 번째 버전에 적용됩니다. 이 기능이 진화함에 따라 이 매핑 스키마가 변경되고 참조 이름이 증가할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [수신 및 쿼리 JSON을 셰이핑하는 방법](./time-series-insights-update-how-to-shape-events.md)을 참조합니다.

- 새 [데이터 모델링](./time-series-insights-update-tsm.md)을 참조합니다.
