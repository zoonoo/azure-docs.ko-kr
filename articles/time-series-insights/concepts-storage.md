---
title: 스토리지 개요 - Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Time Series Insights Gen2의 데이터 스토리지에 대해 알아봅니다.
author: tedvilutis
ms.author: tvilutis
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/21/2021
ms.custom: seodec18
ms.openlocfilehash: 9c0bbf9224f8864428d46e38487f614e0c3f61f0
ms.sourcegitcommit: 5da0bf89a039290326033f2aff26249bcac1fe17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109715235"
---
# <a name="data-storage"></a>데이터 스토리지

이 문서에서는 Azure Time Series Insights Gen2의 데이터 스토리지에 대해 알아봅니다. 웜 및 콜드, 데이터 가용성 및 모범 사례를 다룹니다.

## <a name="provisioning"></a>프로비저닝

Azure Time Series Insights Gen2 환경을 만들 때 다음 옵션을 사용할 수 있습니다.

* 콜드 데이터 스토리지:
  * 사용자 환경에 대해 선택한 구독 및 지역에 새 Azure Storage 리소스를 만듭니다.
  * 기존 Azure Storage 계정을 연결합니다. 이 옵션은 Azure Resource Manager [템플릿](/azure/templates/microsoft.timeseriesinsights/allversions)에서 배포하는 경우에만 사용할 수 있으며 Azure Portal에는 표시되지 않습니다.
* 웜 데이터 스토리지:
  * 웜 저장소는 선택 사항이며 프로비저닝 중에나 이후에 활성화하거나 비활성화할 수 있습니다. 웜 저장소를 나중에 활성화할 계획이며 콜드 저장소에 이미 데이터가 있을 경우 [아래 섹션](concepts-storage.md#warm-store-behavior)을 검토하여 예상되는 동작을 파악합니다. 웜 저장소 데이터 보존 시간은 7~31일로 구성할 수 있으며 필요에 따라 조정 가능합니다.

이벤트가 수집될 때 웜 저장소(활성화된 경우)와 콜드 저장소 모두에서 인덱싱됩니다.

[![스토리지 개요](media/concepts-storage/pipeline-to-storage.png)](media/concepts-storage/pipeline-to-storage.png#lightbox)

> [!WARNING]
> 콜드 저장소 데이터가 있는 Azure Blob 스토리지 계정의 소유자는 계정의 모든 데이터에 대한 모든 액세스 권한을 갖습니다. 이 액세스 권한에는 쓰기 및 삭제 권한이 포함됩니다. 데이터 손실이 발생할 수 있으므로 Azure Time Series Insights Gen2에서 쓰는 데이터는 편집하거나 삭제하지 마세요.

## <a name="data-availability"></a>데이터 가용성

Azure Time Series Insights Gen2는 최적의 쿼리 성능을 위해 데이터를 분할하고 인덱싱합니다. 데이터가 인덱싱된 후 웜 저장소(활성화된 경우)와 콜드 저장소에서 데이터를 쿼리할 수 있습니다. 수집 중인 데이터의 양과 파티션 별 처리량 속도는 가용성에 영향을 줄 수 있습니다. 최상의 성능을 위해 이벤트 원본 [처리량 제한](./concepts-streaming-ingress-throughput-limits.md) 및 [모범 사례](./concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices)를 검토합니다. 환경에서 데이터를 처리하는 데 문제가 발생하는 경우 알림을 받도록 지연 [경고](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts)를 구성할 수도 있습니다.

> [!IMPORTANT]
> [시계열 쿼리 API](./concepts-query-overview.md)를 통해 데이터를 사용할 수 있을 때까지 최대 60초 걸릴 수 있습니다. 60초가 넘는 긴 대기 시간이 발생하면 Azure Portal을 통해 지원 티켓을 제출하세요.
> 
> Azure Time Series Insights Gen2 외부에서 Parquet 파일에 직접 액세스할 때 데이터를 사용할 수 있게 되기까지 최대 5분이 걸릴 수 있습니다. 자세한 내용은 [Parquet 파일 형식](#parquet-file-format-and-folder-structure) 섹션을 참조하세요.

## <a name="warm-store"></a>웜 저장소

웜 저장소의 데이터는 [Time Series Query API](./concepts-query-overview.md), [Azure Time Series Insights TSI 탐색기](./concepts-ux-panels.md) 또는 [Power BI 커넥터](./how-to-connect-power-bi.md)를 통해서만 사용할 수 있습니다. 웜 저장소 쿼리는 무료이며 할당량은 없지만 동시 요청 수는 [30개로 제한](/rest/api/time-series-insights/reference-api-limits#query-apis---limits)됩니다.

### <a name="warm-store-behavior"></a>웜 저장소 동작

* 활성화 시 이벤트 타임스탬프에 관계 없이 사용자 환경으로 스트리밍되는 모든 데이터가 웜 저장소로 라우팅됩니다. 스트리밍 수집 파이프라인은 거의 실시간 스트리밍을 위해 작성되었으며 수집 기록 이벤트는 [지원되지 않습니다](./concepts-streaming-ingestion-event-sources.md#historical-data-ingestion).
* 보존 기간은 이벤트 타임스탬프가 아니라 웜 저장소에서 이벤트가 인덱싱되는 시점을 기준으로 계산됩니다. 즉, 이벤트 타임스탬프가 나중을 위한 것이어도 보존 기간이 경과된 후에는 데이터를 웜 저장소에서 더 이상 사용할 수 없습니다.
  * 예: 10일간의 일기 예보가 있는 이벤트가 7일의 보존 기간으로 구성된 웜 스토리지 컨테이너에서 수집 및 인덱싱됩니다. 7일 후에는 웜 저장소에서 예측이 더 이상 액세스되지 않지만 콜드에서 쿼리할 수 있습니다.
* 이미 콜드 스토리지에서 인덱싱한 최신 데이터를 가지고 있는 기존 환경에서 웜 저장소를 사용하도록 설정하는 경우 웜 저장소는 이 데이터로 다시 채워지지 않습니다.
* 웜 저장소를 활성화하고 탐색기에서 최근 데이터를 보는 동안 문제가 발생한 경우 웜 저장소 쿼리를 일시적으로 토글할 수 있습니다.

   [![웜 쿼리 비활성화](media/concepts-storage/toggle-warm.png)](media/concepts-storage/toggle-warm.png#lightbox)

## <a name="cold-store"></a>콜드 저장소

이 섹션에서는 Azure Time Series Insights Gen2와 관련된 Azure Storage에 대해 자세히 설명합니다.

Azure Blob 스토리지에 대한 자세한 설명은 [Storage Blob 소개](../storage/blobs/storage-blobs-introduction.md)를 참조하세요.

### <a name="your-cold-storage-account"></a>콜드 스토리지 계정

Azure Time Series Insights Gen2는 Azure Storage 계정에서 각 이벤트에 대해 최대 2개의 복사본을 유지합니다. 한 복사본은 수집 시간을 기준으로 정렬된 이벤트를 저장하므로 항상 시간 순서가 지정된 시퀀스로 이벤트에 액세스할 수 있습니다. 시간이 지남에 따라 Azure Time Series Insights Gen2는 다시 분할된 데이터 복사본을 만들어 성능 쿼리를 최적화합니다.

모든 데이터는 Azure Storage 계정에 무기한 저장됩니다.

> [!WARNING]
> Time Series Insights에서 사용하는 스토리지 계정에 대한 퍼블릭 인터넷 액세스를 제한하면 안 됩니다. 필요한 연결이 끊어지게 됩니다.

#### <a name="writing-and-editing-blobs"></a>Blob 작성 및 편집

쿼리 성능 및 데이터 가용성을 보장하려면 Azure Time Series Insights Gen2에서 만드는 Blob을 편집하거나 삭제하지 않습니다.

#### <a name="accessing-cold-store-data"></a>콜드 저장소 데이터 엑세스

[Azure Time Series Insights 탐색기](./concepts-ux-panels.md) 및 [Time Series Query API](./concepts-query-overview.md)에서 데이터에 액세스하는 것 외에 콜드 저장소에 저장된 Parquet 파일에서 데이터에 직접 액세스하고 싶을 수도 있습니다. 예를 들어 Jupyter Notebook에서 데이터를 읽고 변환하고 정리한 다음, 이를 사용하여 동일한 Spark 워크플로에서 Azure Machine Learning 모델을 학습시킬 수 있습니다.

Azure Storage 계정에서 데이터에 직접 액세스하려면 Azure Time Series Insights Gen2 데이터를 저장하는 데 사용되는 계정에 대한 읽기 권한이 필요합니다. 그러면 [Parquet 파일 형식](#parquet-file-format-and-folder-structure) 섹션 아래에서 설명하는 `PT=Time` 폴더에 있는 Parquet 파일의 만든 시간을 기준으로 선택한 데이터를 읽을 수 있습니다.  스토리지 계정에 대한 읽기 액세스를 사용하도록 설정하는 방법에 대한 자세한 내용은 [스토리지 계정 리소스에 대한 액세스 관리](../storage/blobs/anonymous-read-access-configure.md)를 참조하세요.

#### <a name="data-deletion"></a>데이터 삭제

Azure Time Series Insights Gen2 파일을 삭제하지 마세요. Azure Time Series Insights Gen2 내에서만 관련 데이터를 관리합니다.

### <a name="parquet-file-format-and-folder-structure"></a>Parquet 파일 형식 및 폴더 구조

Parquet는 효율적인 스토리지와 성능을 위해 설계된 오픈 소스 열 형식 파일 형식입니다. Azure Time Series Insights Gen2는 Parquet를 사용하여 Time Series ID 기반 쿼리 성능을 규모에 맞게 사용하도록 설정합니다.

Parquet 파일 형식에 대한 자세한 내용은 [Parquet 설명서](https://parquet.apache.org/documentation/latest/)를 참조하세요.

Azure Time Series Insights Gen2는 다음과 같이 데이터 복사본을 저장합니다.

* `PT=Time` 폴더는 수집 시간을 기준으로 분할되며, 대략 도착 순서대로 데이터를 저장합니다. 이 데이터는 시간이 지나도 유지되며 Spark Notebooks 등의 Azure Time Series insights Gen2 외부에서 직접 액세스할 수 있습니다. `<YYYYMMDDHHMMSSfff>` 타임스탬프는 데이터의 수집 시간에 해당합니다. `<MinEventTimeStamp>` 및 `<MaxEventTimeStamp>`는 파일에 포함된 이벤트 타임스탬프의 범위에 해당합니다. 경로와 파일 이름의 형식은 다음과 같습니다.

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<BlobCreationTimestamp>_<MinEventTimestamp>_<MaxEventTimestamp>_<TsiInternalSuffix>.parquet`

* `PT=Live` 및 `PT=Tsid` 폴더에는 대규모 시계열 쿼리 성능을 위해 다시 분할된 데이터의 두 번째 사본이 있습니다. 이 데이터는 고정적이지 않고, 시간이 지남에 따라 최적화됩니다. 다시 분할하는 동안 일부 이벤트가 여러 BLOB에 있을 수 있고, BLOB 이름이 바뀔 수 있습니다.  이러한 폴더는 Azure Time Series Insights Gen2에서 사용되며 직접 액세스해서는 안 됩니다. 해당 용도로만 `PT=Time`을 사용해야 합니다.

> [!NOTE]
>
> 2021년 6월 이전에 `PT=Time` 폴더에 있던 데이터는 이벤트 시간 범위 없는 파일 이름 형식 `V=1/PT=Time/Y=<YYYY>/M=<MM>/<BlobCreationTimestamp>_<TsiInternalSuffix>.parquet`가 될 수 있습니다.  내부 파일 형식은 동일하며 두 명명 체계가 있는 파일을 함께 사용할 수 있습니다. 
>
> * `<YYYY>`는 4자리 연도 표현에 매핑됩니다.
> * `<MM>`은 두 자리 월 표현에 매핑됩니다.
> * 타임스탬프의 `<YYYYMMDDHHMMSSfff>` 형식은 네 자리 연도(`YYYY`), 두 자리 월(`MM`), 두 자리 일(`DD`), 두 자리 시(`HH`), 두 자리 분(`MM`), 두 자리 초(`SS`) 및 세 자리 밀리초(`fff`)에 매핑됩니다.

Azure Time Series Insights Gen2 이벤트는 다음과 같이 Parquet 파일 콘텐츠에 매핑됩니다.

* 각 이벤트는 단일 행에 매핑됩니다.
* 모든 행에는 이벤트 타임스탬프가 있는 **timestamp** 열이 포함됩니다. 타임스탬프 속성은 null이 아닙니다. 이벤트 원본에서 타임스탬프 속성이 지정되지 않은 경우 기본적으로 **이벤트 큐 삽입 시간** 이 설정됩니다. 저장된 타임스탬프는 항상 UTC 형식입니다.
* 모든 행에는 Azure Time Series Insights Gen2 환경을 만들 때 정의된 TSID(시계열 ID) 열이 포함됩니다. TSID 속성 이름에는 `_string` 접미사가 포함됩니다.
* 원격 분석 데이터로 전송되는 다른 모든 속성은 속성 유형에 따라 `_bool`(부울), `_datetime`(타임스탬프), `_long`(롱), `_double`(더블), `_string`(문자열) 또는 `_dynamic`(동적자료형)으로 끝나는 열 이름에 매핑됩니다.  자세한 내용은 [지원되는 데이터 형식](./concepts-supported-data-types.md)을 참조하세요.
* 이 매핑 스키마는 **V=1** 로 참조되고, 동일한 이름의 기본 폴더에 저장된 파일 형식의 첫 번째 버전에 적용됩니다. 이 기능이 진화함에 따라 이 매핑 스키마가 변경되고 참조 이름이 증가할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [데이터 모델링](./concepts-model-overview.md)에 대해 알아봅니다.

* [Azure Time Series Insights Gen2 환경](./how-to-plan-your-environment.md) 플랜을 짜 보세요.
