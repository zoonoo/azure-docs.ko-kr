---
title: Gen2 환경 계획 - Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights Gen2 환경을 구성, 관리, 계획, 배포하는 모범 사례입니다.
author: tedvilutis
ms.author: tvilutis
manager: cnovak
ms.reviewer: orspodek
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: b305a650637f8140cf85cb0e5e8d59a569de4d32
ms.sourcegitcommit: 8942cdce0108372d6fc5819c71f7f3cf2f02dc60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2021
ms.locfileid: "113135845"
---
# <a name="plan-your-azure-time-series-insights-gen2-environment"></a>Azure Time Series Insights Gen2 환경 계획

이 문서에서는 Azure Time Series Insights Gen2를 사용하여 빠르게 계획하고 시작하는 모범 사례를 설명합니다.

## <a name="best-practices-for-planning-and-preparation"></a>계획 및 준비 모범 사례

환경을 계획하고 준비하는 방법에 대한 모범 사례는 다음 문서에 더 자세히 설명되어 있습니다.

* [Azure Time Series Insights Gen2 환경을 프로비저닝](#the-gen2-environment)할 때 얻을 수 있는 것.
* [시계열 ID 및 타임스탬프 속성](#configure-time-series-ids-and-timestamp-properties)의 정의.
* 새 [시계열 모델의 정의](#understand-the-time-series-model)와 사용자 고유의 시계열 모델을 빌드하는 방법.
* [JSON에서 이벤트를 효율적으로 보내는](#shape-your-events) 방법.
* Azure Time Series Insights [비즈니스 재해 복구 옵션](#business-disaster-recovery).

Azure Time Series Insights는 종량제 비즈니스 모델을 채택합니다. 요금 및 용량에 대한 자세한 내용은 [Azure Time Series Insights 가격 책정](https://azure.microsoft.com/pricing/details/time-series-insights/)을 참조하세요.

## <a name="the-gen2-environment"></a>Gen2 환경

Azure Time Series Insights Gen2 환경을 프로비저닝하는 경우 두 개의 Azure 리소스를 만듭니다.

* Azure Time Series Insights Gen2 환경
* Azure Storage 계정

프로비저닝 프로세스의 일부로 웜 저장소의 사용 설정 여부를 지정합니다. 웜 저장소는 계층화된 쿼리 환경을 제공합니다. 사용하도록 설정하는 경우 7일에서 30일 사이의 보존 기간을 지정해야 합니다. 웜 저장소 보존 기간 내에 실행되는 쿼리는 일반적으로 응답 시간이 더 빠릅니다. 쿼리가 웜 저장소 보존 기간을 초과하는 경우 콜드 저장소에서 제공됩니다.

웜 저장소에 대한 쿼리는 무료이지만 콜드 저장소의 쿼리는 비용이 발생합니다. 쿼리 패턴을 이해하고 웜 저장소 구성을 적절하게 계획하는 것이 중요합니다. 최신 데이터에 대한 대화형 분석은 웜 저장소에, 패턴 분석과 장기적 추세는 콜드 저장소에 상주하는 것이 좋습니다.

> [!NOTE]
> 웜 데이터를 쿼리하는 방법에 대한 자세한 내용은 [API 참조](/rest/api/time-series-insights/dataaccessgen2/query/execute#uri-parameters)를 참조하세요.

시작하려면 다음 세 개의 추가 항목이 필요합니다.

* [시계열 모델](./concepts-model-overview.md)
* [Time Series Insights에 연결된 이벤트 원본](./concepts-streaming-ingestion-event-sources.md)
* 모델에 매핑되고 유효한 JSON 형식인, [이벤트 원본으로 흐르는 이벤트](./time-series-insights-send-events.md)

## <a name="review-azure-time-series-insights-gen2-limits"></a>Azure Time Series Insights Gen2 한도 검토

[!INCLUDE [Review Azure Time Series Insights Gen2 limits](../../includes/time-series-insights-preview-limits.md)]

## <a name="configure-time-series-ids-and-timestamp-properties"></a>시계열 ID 및 타임스탬프 속성 구성

새 Azure Time Series Insights 환경을 만들려면 시계열 ID를 선택합니다. 이렇게 하면 데이터의 논리 파티션으로 작동합니다. 설명한 대로, 시계열 ID가 준비되었는지 확인합니다.

> [!IMPORTANT]
> 시계열 ID는 ‘나중에 변경할 수 없습니다’. 최종 선택하고 처음 사용하기 전에 각 항목을 확인합니다.

최대 3개의 키를 선택하여 리소스를 고유하게 구분할 수 있습니다. 자세한 내용은 [시계열 ID 선택 모범 사례](./how-to-select-tsid.md) 및 [수집 규칙](concepts-json-flattening-escaping-rules.md)을 참조하세요.

**타임스탬프** 속성도 중요합니다. 이벤트 원본을 추가할 때 이 속성을 지정할 수 있습니다. 각 이벤트 원본에는 시간 경과에 따라 이벤트 원본을 추적하는 데 사용되는 선택적 타임스탬프 속성이 있습니다. 타임스탬프 값은 대/소문자를 구분하며 각 이벤트 원본의 개별 사양에 맞는 형식이어야 합니다.

비워 두면 이벤트가 IoT Hub 또는 Event Hub의 큐에 넣은 시간이 이벤트 타임스탬프로 사용됩니다. 일반적으로 사용자는 타임스탬프 속성을 사용자 지정하고 허브를 큐에 넣은 시간 대신 센서 또는 태그가 읽기를 생성한 시간을 사용하도록 선택해야 합니다. 자세한 내용과 표준 시간대 오프셋 읽기에 대한 내용은 [이벤트 원본 타임스탬프](./concepts-streaming-ingestion-event-sources.md#event-source-timestamp)를 참조하세요.

## <a name="understand-the-time-series-model"></a>시계열 모델 이해

이제 Azure Time Series Insights 환경의 시계열 모델을 구성할 수 있습니다. 새 모델을 사용하면 IoT 데이터를 쉽게 찾아서 분석할 수 있습니다. 시계열 데이터의 큐레이션, 유지 관리 및 보강을 가능하게 하며, 소비자가 바로 사용할 수 있는 데이터 세트를 준비하는 데 도움이 됩니다. 모델은 고유한 리소스를 변수(형식이라고도 함) 및 계층 구조와 연결하는 인스턴스에 매핑되는 시계열 ID를 사용합니다. 자세한 내용은 [시계열 모델](./concepts-model-overview.md) 개요를 참조하세요.

모델은 동적이므로 언제든지 빌드할 수 있습니다. 빠르게 시작하려면 데이터를 Azure Time Series Insights에 푸시하기 전에 빌드하고 업로드합니다. 모델을 빌드하려면 [시계열 모델 사용](./concepts-model-overview.md)을 참조하세요.

많은 고객의 경우, 시계열 모델은 이미 구현된 기존 자산 모델 또는 ERP 시스템에 매핑됩니다. 기존 모델이 없는 경우, 빠르게 시작하고 실행하도록 사전 빌드된 사용자 환경이 [제공](https://github.com/Microsoft/tsiclient)됩니다. 모델이 어떻게 도움이 되는지를 확인하려면 [샘플 데모 환경](https://insights.timeseries.azure.com/preview/demo)을 살펴보세요.

## <a name="shape-your-events"></a>이벤트 셰이핑

Azure Time Series Insights에 이벤트를 전송하는 방법을 확인할 수 있습니다. 이벤트를 효율적으로 잘 비정규화하는 것이 좋습니다.

경험상 좋은 방법:

* 시계열 모델에 메타데이터를 저장합니다.
* 시계열 모드, 인스턴스 필드, 이벤트에 시계열 ID 또는 타임스탬프 속성과 같은 필요한 정보만 포함되어 있는지 확인합니다.

자세한 내용과 이벤트를 평면화하고 저장하는 방법을 알아보려면 [JSON 평면화 및 이스케이프 규칙](./concepts-json-flattening-escaping-rules.md)을 참조하세요.

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>다음 단계

* [Azure Advisor](../advisor/advisor-overview.md)를 검토하여 비즈니스 복구 구성 옵션을 계획합니다.
* [Azure Advisor](../advisor/advisor-overview.md)를 검토하여 비즈니스 복구 구성 옵션을 계획합니다.
* Azure Time Series Insights Gen2의 [데이터 수집](./concepts-ingestion-overview.md)에 대해 자세히 알아봅니다.
* Azure Time Series Insights Gen2의 [데이터 스토리지](./concepts-storage.md)에 대한 문서를 검토합니다.
* Azure Time Series Insights Gen2의 [데이터 모델링](./concepts-model-overview.md)에 대해 알아봅니다.
