---
title: 미리 보기 환경 계획 - Azure 타임시리즈 인사이트 | 마이크로 소프트 문서
description: Azure Time Series 인사이트 미리 보기 환경을 구성, 관리, 계획 및 배포하는 모범 사례입니다.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/15/2020
ms.custom: seodec18
ms.openlocfilehash: b16d78c9670d05fcec8126c5544d1dd97f6a03bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045716"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Azure Time Series Insights 미리 보기 환경 계획

이 문서에서는 Azure Time Series Insights 미리 보기를 사용하여 빠르게 계획하고 시작하는 모범 사례에 대해 설명합니다.

> [!NOTE]
> 일반 가용성 타임시리즈 인사이트 인스턴스를 계획하는 모범 사례는 [Azure Time Series Insights 일반 가용성 환경 계획을](time-series-insights-environment-planning.md)참조하십시오.

## <a name="best-practices-for-planning-and-preparation"></a>계획 및 준비 모범 사례

환경 계획 및 준비와 관련한 모범 사례는 다음 문서에서 자세히 설명합니다.

* [[보기]- 너는 내 하는 시간대 인사이트 미리 보기 환경을 프로비전할](#the-preview-environment)때 얻는 것 .
* [타임시리즈 아이디와 타임스탬프 속성은](#configure-time-series-ids-and-timestamp-properties)어떤 것입니다.
* 새 [타임시리즈 모델이](#understand-the-time-series-model)무엇이며 직접 구축하는 방법.
* [JSON에서 이벤트를 효율적으로 보내는](#shape-your-events)방법 .
* 타임 시리즈 인사이트 [비즈니스 재해 복구 옵션](#business-disaster-recovery).

Azure Time Series Insights는 종량제 비즈니스 모델을 사용합니다. 요금 및 용량에 대한 자세한 내용은 [타임시리즈 인사이트 가격을](https://azure.microsoft.com/pricing/details/time-series-insights/)참조하십시오.

## <a name="the-preview-environment"></a>미리 보기 환경

Time Series Insights 미리 보기 환경을 프로비전할 때는 다음 두 개의 Azure 리소스를 만듭니다.

* Azure Time Series Insights 미리 보기 환경
* Azure Storage 범용 V1 계정

프로비저닝 프로세스의 일부로 웜 저장소를 사용하도록 설정할지 여부를 지정합니다. 웜 저장소는 계층형 쿼리 환경을 제공합니다. 활성화된 경우 보존 기간을 7일에서 30일 사이로 지정해야 합니다. 웜 저장소 보존 기간 내에 실행되는 쿼리는 일반적으로 응답 시간을 더 빠르게 제공합니다. 문의가 웜 스토어 보존 기간 동안 스팬되면 콜드 스토어에서 제공됩니다.

웜 스토어에 대한 쿼리는 무료이며 콜드 스토어에 대한 쿼리에는 비용이 발생합니다. 쿼리 패턴을 이해하고 그에 따라 웜 스토어 구성을 계획하는 것이 중요합니다. 가장 최근 데이터에 대한 대화형 분석은 따뜻한 저장소에 상주하고 패턴 분석 및 장기 추세는 추위에 있는 것이 좋습니다.

> [!NOTE]
> 웜 데이터를 쿼리하는 방법에 대한 자세한 내용은 [API 참조](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters)를 참조하십시오.

시작하려면 다음 세 개의 추가 항목이 필요합니다.

* [시계열 모델](./time-series-insights-update-tsm.md)
* [Time Series Insights에 연결된 이벤트 원본](./time-series-insights-how-to-add-an-event-source-iothub.md)
* 모델에 매핑되고 유효한 JSON 형식인, [이벤트 원본으로 흐르는 이벤트](./time-series-insights-send-events.md)

## <a name="review-preview-limits"></a>미리 보기 제한 검토

[!INCLUDE [Review Time Series Insights Preview limits](../../includes/time-series-insights-preview-limits.md)]

## <a name="configure-time-series-ids-and-timestamp-properties"></a>타임시리즈 아이디 및 타임스탬프 속성 구성

새로운 Time Series Insights 환경을 만들려면 시계열 ID를 선택합니다. 이렇게 하면 데이터의 논리 파티션으로 작동합니다. 설명한 대로, 시계열 ID가 준비되었는지 확인합니다.

> [!IMPORTANT]
> 나중에 열렬 아이디를 *변경할 수 없습니다.* 최종 선택하고 처음 사용하기 전에 각 항목을 확인합니다.

최대 3개의 키를 선택하여 리소스를 고유하게 구분할 수 있습니다. 자세한 내용은 [시계열 ID 선택 모범 사례](./time-series-insights-update-how-to-id.md) 및 [스토리지 및 수신](./time-series-insights-update-storage-ingress.md)을 참조하세요.

**타임스탬프** 속성도 중요합니다. 이벤트 원본을 추가할 때 이 속성을 지정할 수 있습니다. 각 이벤트 원본에는 시간 경과에 따라 이벤트 원본을 추적하는 데 사용되는 선택적 타임스탬프 속성이 있습니다. 타임스탬프 값은 대/소문자를 구분하며 각 이벤트 원본의 개별 사양에 맞는 형식이어야 합니다.

> [!TIP]
> 이벤트 원본에 대한 형식 및 구문 분석 요구 사항을 확인합니다.

이 값을 비워 두면 이벤트 원본의 이벤트를 큐에 넣는 시간이 이벤트 타임스탬프로 사용됩니다. 기록 데이터 또는 일괄 처리 이벤트를 전송하는 경우 타임스탬프 속성을 사용자 지정하는 것이 기본 이벤트를 큐에 넣는 시간보다 더 유용합니다. 자세한 내용은 [Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md)에서 이벤트 소스를 추가하는 방법에 대해 자세히 알아보기.

## <a name="understand-the-time-series-model"></a>시계열 모델 이해

이제 Time Series Insights 환경의 시계열 모델을 구성할 수 있습니다. 새 모델을 사용하면 IoT 데이터를 쉽게 찾아서 분석할 수 있습니다. 시계열 데이터의 큐레이션, 유지 관리 및 보강을 가능하게 하며, 소비자가 바로 사용할 수 있는 데이터 세트를 준비하는 데 도움이 됩니다. 모델은 고유한 리소스를 변수(형식이라고도 함) 및 계층 구조와 연결하는 인스턴스에 매핑되는 시계열 ID를 사용합니다. 새로운 [시계열 모델](./time-series-insights-update-tsm.md)에 대해 읽어 보세요.

모델은 동적이므로 언제든지 빌드할 수 있습니다. 빠르게 시작하려면 데이터를 Time Series Insights에 푸시하기 전에 빌드 및 업로드합니다. 모델을 작성하려면 [타임시리즈 모델 사용을](./time-series-insights-update-how-to-tsm.md)참조하십시오.

많은 고객의 경우, 시계열 모델은 이미 구현된 기존 자산 모델 또는 ERP 시스템에 매핑됩니다. 기존 모델이 없는 경우, 빠르게 시작하고 실행하도록 사전 빌드된 사용자 환경이 [제공](https://github.com/Microsoft/tsiclient)됩니다. 모델이 어떻게 도움이 되는지를 확인하려면 [샘플 데모 환경](https://insights.timeseries.azure.com/preview/demo)을 살펴보세요.

## <a name="shape-your-events"></a>이벤트 셰이핑

Time Series Insights에 이벤트를 보내는 방법을 확인할 수 있습니다. 이벤트를 효율적으로 잘 비정규화하는 것이 좋습니다.

경험상 좋은 방법:

* 타임시리즈 모델에 메타데이터를 저장합니다.
* 타임시리즈 모드, 인스턴스 필드 및 이벤트에는 타임시리즈 ID 또는 타임스탬프 속성과 같은 필요한 정보만 포함되어 야 합니다.

자세한 내용은 [셰이프 이벤트를](./time-series-insights-send-events.md#supported-json-shapes)참조하십시오.

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>다음 단계

- [Azure Advisor를](../advisor/advisor-overview.md) 검토하여 비즈니스 복구 구성 옵션을 계획합니다.
- 타임시리즈 인사이트 미리 보기에서 [스토리지 및 스토리지 에](./time-series-insights-update-storage-ingress.md) 대해 자세히 읽어보세요.
- 타임시리즈 인사이트 미리 보기에서 [데이터 모델링에](./time-series-insights-update-tsm.md) 대해 알아봅니다.
