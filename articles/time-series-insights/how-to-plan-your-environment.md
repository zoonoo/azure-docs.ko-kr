---
title: Gen2 환경 계획-Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights Gen2 환경을 구성, 관리, 계획 및 배포 하는 모범 사례입니다.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: cb12777a6a4fa1e75cd65bc597c87442d592aad5
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91598114"
---
# <a name="plan-your-azure-time-series-insights-gen2-environment"></a>Azure Time Series Insights Gen2 환경 계획

이 문서에서는 Azure Time Series Insights Gen2를 사용 하 여 빠르게 계획 하 고 시작 하는 모범 사례를 설명 합니다.

## <a name="best-practices-for-planning-and-preparation"></a>계획 및 준비 모범 사례

환경을 계획 하 고 준비 하는 방법에 대 한 모범 사례는 다음 문서에 자세히 설명 되어 있습니다.

* [Azure Time Series Insights Gen2 환경을 프로 비전](#the-gen2-environment)할 때 얻을 수 있는 것입니다.
* 시계열 [id 및 타임 스탬프 속성은](#configure-time-series-ids-and-timestamp-properties)무엇 인가요?
* 새 시계열 모델의 정의 및 사용자 고유의 [시계열 모델](#understand-the-time-series-model)을 빌드하는 방법을 설명 합니다.
* [JSON에서 이벤트를 효율적으로 전송](#shape-your-events)하는 방법입니다.
* [비즈니스 재해 복구 옵션](#business-disaster-recovery)을 Azure Time Series Insights 합니다.

Azure Time Series Insights은 종 량 제 비즈니스 모델을 채택 합니다. 요금 및 용량에 대 한 자세한 내용은 [Azure Time Series Insights 가격 책정](https://azure.microsoft.com/pricing/details/time-series-insights/)을 참조 하세요.

## <a name="the-gen2-environment"></a>Gen2 환경

Azure Time Series Insights Gen2 환경을 프로 비전 할 때 두 가지 Azure 리소스를 만듭니다.

* Azure Time Series Insights Gen2 환경
* Azure Storage 계정

프로 비전 프로세스의 일부로 웜 저장소를 사용 하도록 설정할지 여부를 지정 합니다. 웜 스토어는 계층화 된 쿼리 환경을 제공 합니다. 사용 하도록 설정 하는 경우 7 일에서 30 일 사이의 보존 기간을 지정 해야 합니다. 웜 저장소 보존 기간 내에 실행 되는 쿼리는 일반적으로 더 빠른 응답 시간을 제공 합니다. 쿼리가 웜 저장소 보존 기간을 초과 하는 경우 콜드 스토어에서 제공 됩니다.

웜 저장소에 대 한 쿼리는 무료 이지만 콜드 스토어의 쿼리는 비용이 발생 합니다. 쿼리 패턴을 이해 하 고 웜 저장소 구성을 적절 하 게 계획 하는 것이 중요 합니다. 최신 데이터에 대 한 대화형 분석은 웜 저장소에 있고 패턴 분석과 장기적 추세는 콜드에 상주 하는 것이 좋습니다.

> [!NOTE]
> 웜 데이터를 쿼리 하는 방법에 대 한 자세한 내용은 [API 참조를 참조](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#uri-parameters)하세요.

시작하려면 다음 세 개의 추가 항목이 필요합니다.

* [시계열 모델](./concepts-model-overview.md)
* [Time Series Insights에 연결된 이벤트 원본](./concepts-streaming-ingestion-event-sources.md)
* 모델에 매핑되고 유효한 JSON 형식인, [이벤트 원본으로 흐르는 이벤트](./time-series-insights-send-events.md)

## <a name="review-azure-time-series-insights-gen2-limits"></a>Azure Time Series Insights Gen2 제한 검토

[!INCLUDE [Review Azure Time Series Insights Gen2 limits](../../includes/time-series-insights-preview-limits.md)]

## <a name="configure-time-series-ids-and-timestamp-properties"></a>시계열 Id 및 타임 스탬프 속성 구성

새 Azure Time Series Insights 환경을 만들려면 시계열 ID를 선택 합니다. 이렇게 하면 데이터의 논리 파티션으로 작동합니다. 설명한 대로, 시계열 ID가 준비되었는지 확인합니다.

> [!IMPORTANT]
> 시계열 Id는 *나중에 변경할 수 없습니다*. 최종 선택하고 처음 사용하기 전에 각 항목을 확인합니다.

최대 3 개의 키를 선택 하 여 리소스를 고유 하 게 구분할 수 있습니다. 자세한 내용은 시계열 ID 및 수집 [규칙](concepts-json-flattening-escaping-rules.md) [선택에 대 한 모범 사례](./time-series-insights-update-how-to-id.md) 를 참조 하세요.

**Timestamp** 속성도 중요 합니다. 이벤트 원본을 추가할 때 이 속성을 지정할 수 있습니다. 각 이벤트 원본에는 시간 경과에 따라 이벤트 원본을 추적하는 데 사용되는 선택적 타임스탬프 속성이 있습니다. 타임스탬프 값은 대/소문자를 구분하며 각 이벤트 원본의 개별 사양에 맞는 형식이어야 합니다.

이 값을 비워 두면 이벤트가 IoT Hub 또는 이벤트 허브로 큐에 들어간 시간이 이벤트 타임 스탬프로 사용 됩니다. 일반적으로 사용자는 타임 스탬프 속성을 사용자 지정 하 고, 센서 또는 태그가 허브를 큐에 넣은 시간 대신 읽기를 생성 한 시간을 사용 하도록 옵트인 해야 합니다. 자세한 내용과 표준 시간대 오프셋 읽기 [이벤트 원본 타임 스탬프](./concepts-streaming-ingestion-event-sources.md#event-source-timestamp)에 대 한 자세한 내용은을 참조 하세요.

## <a name="understand-the-time-series-model"></a>시계열 모델 이해

이제 Azure Time Series Insights 환경의 시계열 모델을 구성할 수 있습니다. 새 모델을 사용하면 IoT 데이터를 쉽게 찾아서 분석할 수 있습니다. 시계열 데이터의 큐레이션, 유지 관리 및 보강을 가능하게 하며, 소비자가 바로 사용할 수 있는 데이터 세트를 준비하는 데 도움이 됩니다. 모델은 고유한 리소스를 변수(형식이라고도 함) 및 계층 구조와 연결하는 인스턴스에 매핑되는 시계열 ID를 사용합니다. 자세한 내용은 [시계열 모델](./concepts-model-overview.md) 개요를 참조 하세요.

모델은 동적이므로 언제든지 빌드할 수 있습니다. 신속 하 게 시작 하려면 데이터를 Azure Time Series Insights 푸시 하기 전에 빌드 및 업로드 하세요. 모델을 작성 하려면 [시계열 모델 사용](/azure/time-series-insights/concepts-model-overview)을 참조 하세요.

많은 고객의 경우, 시계열 모델은 이미 구현된 기존 자산 모델 또는 ERP 시스템에 매핑됩니다. 기존 모델이 없는 경우, 빠르게 시작하고 실행하도록 사전 빌드된 사용자 환경이 [제공](https://github.com/Microsoft/tsiclient)됩니다. 모델이 어떻게 도움이 되는지를 확인하려면 [샘플 데모 환경](https://insights.timeseries.azure.com/preview/demo)을 살펴보세요.

## <a name="shape-your-events"></a>이벤트 셰이핑

Azure Time Series Insights에 이벤트를 전송 하는 방법을 확인할 수 있습니다. 이벤트를 효율적으로 잘 비정규화하는 것이 좋습니다.

경험상 좋은 방법:

* 시계열 모델에 메타 데이터를 저장 합니다.
* 시계열 모드, 인스턴스 필드 및 이벤트에 시계열 ID 또는 Timestamp 속성과 같은 필요한 정보만 포함 되어 있는지 확인 합니다.

자세한 내용 및 이벤트를 평면화 하 고 저장 하는 방법을 이해 하려면 [JSON 평면화 및 이스케이프 규칙](./concepts-json-flattening-escaping-rules.md)을 참조 하세요.

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>다음 단계

* [Azure Advisor](../advisor/advisor-overview.md) 를 검토 하 여 비즈니스 복구 구성 옵션을 계획 합니다.
* [Azure Advisor](../advisor/advisor-overview.md) 를 검토 하 여 비즈니스 복구 구성 옵션을 계획 합니다.
* Azure Time Series Insights Gen2의 [데이터](./concepts-ingestion-overview.md) 수집에 대해 자세히 알아보세요.
* Azure Time Series Insights Gen2의 [데이터 저장소](./concepts-storage.md) 에 대 한 문서를 검토 합니다.
* Azure Time Series Insights Gen2의 [데이터 모델링](./concepts-model-overview.md) 에 대해 알아봅니다.
