---
title: Azure Time Series Insights 미리 보기의 데이터 스토리지 및 수신 | Microsoft Docs
description: Azure Time Series Insights 미리 보기의 데이터 스토리지 및 수신 이해
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 05/20/2019
ms.custom: seodec18
ms.openlocfilehash: 33ca86fc2d13fb7f6e29c43e9a7c1d2dc6ef4169
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66755229"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Azure Time Series Insights 미리 보기의 데이터 스토리지 및 수신

이 문서에서는 Azure Time Series Insights 미리 보기의 데이터 스토리지 및 수신이 어떻게 변경되었는지 설명합니다. 기본 스토리지 구조, 파일 형식 및 Time Series ID 속성도 다룹니다. 또한 이 문서에서는 기본 수신 프로세스, 처리량 및 제한 사항도 살펴봅니다.

## <a name="data-storage"></a>데이터 저장소

Time Series Insights 미리 보기 종량제 SKU 환경을 만들 때 다음 두 가지 리소스를 만들게 됩니다.

* Time Series Insights 환경
* 데이터가 저장되는 Azure Storage 범용 V1 계정

Time Series Insights 미리 보기는 Parquet 파일 형식을 사용 하 여 Azure Blob 저장소를 사용합니다. Time Series Insights는 Azure Storage 계정의 Blob 생성, 인덱싱 및 데이터 분할을 비롯한 모든 데이터 작업을 관리합니다. Azure Storage 계정을 사용하여 이러한 Blob을 만듭니다.

다른 Azure Storage Blob과 달리, Time Series Insights에서 만든 Blob에서는 읽고 쓸 수 있으므로 다양한 통합 시나리오가 지원됩니다.

> [!TIP]
> Blob에서 읽기 또는 쓰기 작업을 너무 자주 수행하면 Time Series Insights 성능이 저하될 수 있습니다.

Azure Blob Storage의 개요를 보려면 [Storage Blob 소개](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)를 참조하세요.

Parquet 파일 형식에 대한 자세한 내용은 [Azure Storage의 지원되는 파일 형식](https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs#parquet-format)을 참조하세요.

## <a name="parquet-file-format"></a>Parquet 파일 형식

Parquet은 다음을 위해 디자인된 열 기반 데이터 파일 형식입니다.

* 상호 운용성
* 공간 효율성
* 쿼리 효율성

Time Series Insights는 Parquet을 선택했습니다. 이 형식은 복잡한 데이터를 대량으로 처리할 수 있는 개선된 성능으로 효율적인 데이터 압축 및 인코딩 체계를 제공하기 때문입니다.

Parquet 파일 형식을 보다 잘 이해하려면 [Parquet 설명서](https://parquet.apache.org/documentation/latest/)를 참조하세요.

### <a name="event-structure-in-parquet"></a>Parquet의 이벤트 구조

Time Series Insights는 다음 두 가지 형식으로 Blob 사본을 만들어 저장합니다.

1. 첫째, 초기 복사본은 도착 시간을 기준으로 다음과 같이 분할됩니다.

    * `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * 도착 시간을 기준으로 분할되는 Blob의 Blob 작성 시간

1. 다시 분할된 두 번째 복사본은 시계열 ID의 동적 그룹화로 다음과 같이 분할됩니다.

    * `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * 시계열 ID로 분할된 Blob에 대한 한 Blob의 최소 이벤트 타임스탬프

> [!NOTE]
> * `<YYYY>`는 4자리 연도 표시에 매핑됩니다.
> * `<MM>`은 2자리 월 표시에 매핑됩니다.
> * `<YYYYMMDDHHMMSSfff>`는 4자리 연도(`YYYY`), 2자리 월(`MM`), 2자리 일(`DD`), 2자리 시간(`HH`), 2자리 분(`MM`), 2자리 초(`SS`) 및 3자리 밀리초(`fff`)의 타임스탬프 표시에 매핑됩니다.

Time Series Insights 이벤트는 다음과 같이 Parquet 파일 콘텐츠에 매핑됩니다.

* 각 이벤트는 단일 행에 매핑됩니다.
* 이벤트 타임스탬프가 있는 기본 제공 **타임스탬프** 열. 타임스탬프 속성은 null이 아닙니다. 이벤트 원본에서 타임스탬프 속성이 지정되지 않은 경우 기본적으로 **이벤트 원본이 큐에 유지된 시간**입니다. 타임스탬프는 UTC로 나타냅니다. 
* 열에 매핑되는 다른 모든 속성은 속성 형식에 따라 `_string`(string) `_bool`(Boolean) `_datetime`(datetime) 및 `_double`(double)로 끝납니다.
* 이것이 **V=1**으로 나타내는 파일 형식의 첫 번째 버전에 대한 매핑 체계입니다. 이 기능이 계속 사용되면서 이름이 **V=2**, **V=3** 등과 같이 증분합니다.

## <a name="partitions"></a>파티션

각 Time Series Insights 미리 보기 환경에 있어야 합니다는 **시간 시리즈 ID** 속성으로 **타임 스탬프** 고유 하 게 식별 하는 속성입니다. 시계열 ID는 데이터에 대한 논리적 파티션의 역할을 하고 Time Series Insights 미리 보기 환경에 실제 파티션 간에 데이터를 분산하기 위한 자연 경계를 제공합니다. 실제 파티션은 Azure Storage 계정에서 Time Series Insights 미리 보기를 통해 관리됩니다.

Time Series Insights는 동적 분할을 통해 파티션을 삭제한 후 다시 만들어 스토리지 및 쿼리 성능을 최적화합니다. Time Series Insights 미리 보기 동적 분할 알고리즘은 단일 실제 파티션이 여러 개의 별도 논리 파티션용 데이터를 포함하지 못하도록 합니다. 즉, 이 분할 알고리즘은 모든 데이터에 다른 시계열 ID와 인터리브되지 않으면서 Parquet 파일에 배타적으로 존재하는 단일 시계열 ID를 지정합니다. 동적 분할 알고리즘 기능은 또한 단일 시계열 ID 내에서 이벤트의 원래 순서를 유지하려고 합니다.

처음에는 수신 시에 데이터가 타임스탬프를 기준으로 분할되므로, 지정된 시간 범위 내의 단일, 논리적 파티션이 여러 실제 파티션에 걸쳐 분산될 수 있습니다. 단일 실제 파티션이 여러 개 또는 모든 논리적 파티션을 포함할 수도 있습니다. 최적의 분할을 사용하더라도 Blob 크기 제한으로 인해 단일 논리 파티션이 여러 실제 파티션을 차지할 수 있습니다.

> [!NOTE]
> 기본적으로 타임스탬프 값은 구성된 이벤트 원본에서 *메시지가 큐에 유지된 시간*입니다.

기록 데이터 또는 일괄 처리 메시지를 업로드하는 경우 데이터와 함께 저장하려는 값을 적절한 타임스탬프에 매핑되는 타임스탬프 속성에 할당합니다. 타임스탬프 속성은 대/소문자를 구분합니다. 자세한 내용은 [시계열 모델](./time-series-insights-update-tsm.md)을 참조하세요.

### <a name="physical-partitions"></a>실제 파티션

실제 파티션은 스토리지 계정에 저장되는 블록 Blob입니다. Blob의 실제 크기는 푸시 속도에 좌우되므로 달라질 수 있습니다. 그러나 Blob 크기는 약 20MB~50MB 정도로 예상됩니다. 따라서 Time Series Insights 팀은 쿼리 성능을 최적화할 수 있는 크기로 20MB를 선택했습니다. 이 크기는 파일 크기 및 데이터 수신 속도에 따라 시간이 지나면서 달라질 수 있습니다.

> [!NOTE]
> * Blob 크기는 20MB로 지정됩니다.
> * Azure Blob은 경우에 따라 삭제되었다가 다시 생성됨으로써 성능 향상을 위해 서 다시 분할됩니다.
> * 또한 동일한 Time Series Insights 데이터가 두 개 이상의 Blob에 있을 수 있습니다.

### <a name="logical-partitions"></a>논리 파티션

논리 파티션은 단일 파티션 키 값과 관련된 모든 데이터를 저장하는 실제 파티션 내의 파티션입니다. Time Series Insights 미리 보기 두 속성을 기반으로 하는 각 blob을 논리적으로 분할 합니다.

* **시계열 ID**: 이벤트 스트림 및 모델 내의 모든 Time Series Insights 데이터에 대한 파티션 키입니다.
* **타임스탬프**: 초기 수신에 따른 시간입니다.

Time Series Insights 미리 보기는 이러한 두 속성을 기반으로 하는 고성능 쿼리를 제공 합니다. 또한 이러한 두 속성은 Time Series Insights 데이터를 신속하게 제공하기 위한 가장 효율적인 방법을 제공합니다.

시계열 ID는 변경할 수 없는 속성이므로 신중히 선택해야 합니다. 자세한 내용은 [시계열 ID 선택](./time-series-insights-update-how-to-id.md)을 참조하세요.

## <a name="azure-storage"></a>Azure 저장소

### <a name="your-storage-account"></a>저장소 계정

Time Series Insights 종량제 환경을 만들 때는 Time Series Insights 환경과 데이터가 저장되는 Azure Storage 범용 V1 계정의 두 리소스를 만듭니다. 상호 운용성, 가격 및 성능 때문에 Azure Storage 범용 V1을 기본 리소스로 선택했습니다. 

Time Series Insights는 Azure Storage 계정에 각 이벤트 복사본을 최대 2개 게시합니다. 초기 복사본은 다른 서비스를 사용 하 여 신속 하 게 쿼리할 수 있도록 항상 유지 됩니다. Spark, Hadoop 및 기타 친숙한 도구는 기본적인 파일 이름 필터링을 지원하므로 원시 Parquet 파일을 통해 시계열 ID 간에 쉽게 사용할 수 있습니다. Blob을 년 및 월별로 그룹화하면 사용자 지정 작업의 특정 시간 범위 내에 속하는 Blob을 나열할 때 유용합니다. 

또한 Time Series Insights는 Parquet 파일을 다시 분할하여 Time Series Insights API에 맞게 최적화합니다. 가장 최근에 다시 분할된 파일도 저장됩니다.

공개 미리 보기 기간 동안 데이터는 Azure 저장소 계정에 무기한으로 저장 됩니다.

### <a name="writing-and-editing-time-series-insights-blobs"></a>Time Series Insights blob 작성 및 편집

쿼리 성능 및 데이터 가용성을 보장하려면 Time Series Insights에서 만든 Blob을 절대 편집하거나 삭제하지 않도록 합니다.

### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Time Series Insights 미리 보기에서 데이터 액세스 및 내보내기

Time Series Insights 미리 보기 탐색기에 저장된 데이터에 액세스하여 다른 서비스와 함께 사용할 수 있습니다. 예를 들어, 데이터를 사용하여 Power BI에서 보고하거나, Azure Machine Learning Studio를 사용하여 기계 학습을 수행하거나, Jupyter 노트와 함께 노트 애플리케이션에서 사용할 수 있습니다.

다음과 같은 세 가지 일반적인 방법으로 데이터에 액세스할 수 있습니다.

* Time Series Insights 미리 보기 탐색기에서: Time Series Insights 미리 보기 탐색기에서 CSV 파일로 데이터를 내보낼 수 있습니다. 자세한 내용은 [Time Series Insights 미리 보기 탐색기](./time-series-insights-update-explorer.md)를 참조하세요.
* Time Series Insights 미리 보기 Api에서에서: API 끝점에 도달할 수 있습니다 `/getRecorded`합니다. 이 API에 대한 자세한 내용은 [시계열 쿼리](./time-series-insights-update-tsq.md)를 참조하세요.
* Azure storage 계정 (아래 참조)에서 직접.

#### <a name="from-an-azure-storage-account"></a>Azure Storage 계정에서

* Time Series Insights 데이터에 액세스하는 데 사용하는 계정에 대해 읽기 액세스 권한이 있어야 합니다. 자세한 내용은 [스토리지 계정 리소스에 대한 액세스 관리](https://docs.microsoft.com/azure/storage/blobs/storage-manage-access-to-resources)를 참조하세요.
* Azure Blob Storage에서 직접 데이터를 읽는 방법에 대한 자세한 내용은 [스토리지 계정으로/에서 데이터 이동](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)을 참조하세요.
* Azure Storage 계정에서 데이터를 내보내려면
    * 먼저 계정이 데이터를 내보내는 데 필요한 요구 사항을 충족하는지 확인합니다. 자세한 내용은 [스토리지 가져오기 및 내보내기 요구 사항](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements)을 참조하세요.
    * Azure Storage 계정에서 데이터를 내보내는 다른 방법을 알아보려면 [Blob에서 데이터 가져오기 및 내보내기](https://docs.microsoft.com/azure/storage/common/storage-import-export-data-from-blobs)를 참조하세요.

### <a name="data-deletion"></a>데이터 삭제

Blob을 삭제 하지 마십시오. 뿐 아니라 데이터 레코드를 유지 관리 및 감사에 대 한 유용한, Time Series Insights 미리 보기 각 blob에 blob 메타 데이터를 유지 합니다.

## <a name="time-series-insights-data-ingress"></a>Time Series Insights 데이터 수신

### <a name="ingress-policies"></a>수신 정책

Time Series Insights 미리 보기에는 Time Series Insights는 현재 지원 되는 동일한 이벤트 원본 및 파일 형식을 지원 합니다.

지원되는 이벤트 원본은 다음과 같습니다.

- Azure IoT Hub
- Azure Event Hubs
  
  > [!NOTE]
  > Azure Event Hub 인스턴스는 Kafka를 지원합니다.

지원되는 파일 형식은 다음과 같습니다.

* JSON: 처리할 수 있는 지원되는 JSON 셰이프에 대한 자세한 내용은 [JSON을 셰이핑하는 방법](./time-series-insights-send-events.md#json)을 참조하세요.

### <a name="data-availability"></a>데이터 가용성

Time Series Insights 미리 보기 blob 크기 최적화 전략을 사용 하 여 데이터를 인덱싱합니다. 데이터는 인덱싱된 후에 데이터가 들어오는 양과 속도를 기준으로, 쿼리에 사용할 수 있게 됩니다.

> [!IMPORTANT]
> * Time Series Insights GA(일반 공급) 릴리스는 60초의 이벤트 원본 적중 시간 내에 데이터를 사용할 수 있도록 지원합니다. 
> * 미리 보기 동안에는 데이터를 사용할 수 있게 되기까지 좀 더 긴 시간이 필요합니다.
> * 대기 시간이 늘어나면 Microsoft에 문의하세요.

### <a name="scale"></a>확장

Time Series Insights 미리 보기 환경 당 최대 1 메가 바이트 / 초 (Mbps)의 초기 수신 확장을 지원합니다. 개선된 크기 조정 지원을 제공하기 위해 작업 중입니다. 이러한 개선 사항에 맞게 설명서를 업데이트할 예정입니다.

## <a name="next-steps"></a>다음 단계

- [Azure Time Series Insights 미리 보기 스토리지 및 수신](./time-series-insights-update-storage-ingress.md)에 대해 읽어보세요.

- 새 [데이터 모델링](./time-series-insights-update-tsm.md)에 대해 읽어보세요.

<!-- Images -->
[1]: media/v2-update-storage-ingress/storage-architecture.png
[2]: media/v2-update-storage-ingress/parquet-files.png
[3]: media/v2-update-storage-ingress/blob-storage.png
