---
title: Azure Time Series Insights 미리 보기 환경 계획 | Microsoft Docs
description: Azure Time Series Insights 미리 보기 환경을 계획합니다.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 0472f53d11ec4c990fcf6face633444fe66ba937
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702340"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Azure Time Series Insights 미리 보기 환경 계획

이 문서에서는 Azure Time Series Insights 미리 보기를 사용하여 계획하고 신속하게 시작하는 방법을 설명합니다.

## <a name="best-practices-for-planning-and-preparation"></a>계획 및 준비 모범 사례

Time Series Insights를 시작하려면 다음을 이해하는 것이 좋습니다.

* Time Series Insights 미리 보기 환경을 프로비전할 때 제공되는 사항
* 시계열 ID 및 타임스탬프 속성 정의
* 새로운 시계열 모델 정의 및 사용자 고유의 시계열 모델을 빌드하는 방법
* JSON에서 이벤트를 효율적으로 보내는 방법 
* Time Series Insights 비즈니스 재해 복구 옵션

Time Series Insights는 종량제 비즈니스 모델을 사용합니다. 요금 및 용량에 대한 자세한 내용은 [Time Series Insights 가격 책정](https://azure.microsoft.com/pricing/details/time-series-insights/)을 참조하세요.

## <a name="the-time-series-insights-preview-environment"></a>Time Series Insights 미리 보기 환경

Time Series Insights 미리 보기 환경을 프로비전할 때는 다음 두 개의 Azure 리소스를 만듭니다.

* Time Series Insights 미리 보기 환경
* Azure Storage 범용 V1 계정

시작하려면 다음 세 개의 추가 항목이 필요합니다.
 
- [시계열 모델](./time-series-insights-update-tsm.md) 
- [Time Series Insights에 연결된 이벤트 원본](./time-series-insights-how-to-add-an-event-source-iothub.md) 
- 모델에 매핑되고 유효한 JSON 형식인, [이벤트 원본으로 흐르는 이벤트](./time-series-insights-send-events.md) 

## <a name="configure-your-time-series-ids-and-timestamp-properties"></a>시계열 ID 및 타임스탬프 속성 구성

새로운 Time Series Insights 환경을 만들려면 시계열 ID를 선택합니다. 이렇게 하면 데이터의 논리 파티션으로 작동합니다. 설명한 대로, 시계열 ID가 준비되었는지 확인합니다.

> [!IMPORTANT]
> 시계열 ID는 *변경 불가*이며, *나중에 변경할 수 없습니다*. 최종 선택하고 처음 사용하기 전에 각 항목을 확인합니다.

리소스를 고유하게 구별하는 키를 최대 3개까지 선택할 수 있습니다. 자세한 내용은 [시계열 ID 선택 모범 사례](./time-series-insights-update-how-to-id.md) 및 [스토리지 및 수신](./time-series-insights-update-storage-ingress.md)을 참조하세요.

타임스탬프 속성도 중요합니다. 이벤트 원본을 추가할 때 이 속성을 지정할 수 있습니다. 각 이벤트 원본에는 시간 경과에 따라 이벤트 원본을 추적하는 데 사용되는 선택적 타임스탬프 속성이 있습니다. 타임스탬프 값은 대/소문자를 구분하며 각 이벤트 원본의 개별 사양에 맞는 형식이어야 합니다.

> [!TIP]
> 이벤트 원본에 대한 형식 및 구문 분석 요구 사항을 확인합니다.

이 값을 비워 두면 이벤트 원본의 이벤트를 큐에 넣는 시간이 이벤트 타임스탬프로 사용됩니다. 기록 데이터 또는 일괄 처리 이벤트를 전송하는 경우 타임스탬프 속성을 사용자 지정하는 것이 기본 이벤트를 큐에 넣는 시간보다 더 유용합니다. 자세한 내용은 [IoT Hub에서 이벤트 원본을 추가하는 방법](./time-series-insights-how-to-add-an-event-source-iothub.md)을 참조하세요. 

## <a name="understand-the-time-series-model"></a>시계열 모델 이해

이제 Time Series Insights 환경의 시계열 모델을 구성할 수 있습니다. 새 모델을 사용하면 IoT 데이터를 쉽게 찾아서 분석할 수 있습니다. 시계열 데이터의 큐레이션, 유지 관리 및 보강을 가능하게 하며, 소비자가 바로 사용할 수 있는 데이터 세트를 준비하는 데 도움이 됩니다. 모델은 고유한 리소스를 변수(형식이라고도 함) 및 계층 구조와 연결하는 인스턴스에 매핑되는 **시계열 ID**를 사용합니다. 새로운 [시계열 모델](./time-series-insights-update-tsm.md)에 대해 읽어 보세요.

모델은 동적이므로 언제든지 빌드할 수 있습니다. 빠르게 시작하려면 데이터를 Time Series Insights에 푸시하기 전에 빌드 및 업로드합니다. 모델을 빌드하려면 [시계열 모델 사용](./time-series-insights-update-how-to-tsm.md)을 참조하세요.

많은 고객의 경우, 시계열 모델은 이미 구현된 기존 자산 모델 또는 ERP 시스템에 매핑됩니다. 기존 모델이 없는 경우, 빠르게 시작하고 실행하도록 사전 빌드된 사용자 환경이 [제공](https://github.com/Microsoft/tsiclient)됩니다. 모델이 어떻게 도움이 되는지를 확인하려면 [샘플 데모 환경](https://insights.timeseries.azure.com/preview/demo)을 살펴보세요. 

## <a name="shape-your-events"></a>이벤트 셰이핑

Time Series Insights에 이벤트를 보내는 방법을 확인할 수 있습니다. 이벤트를 효율적으로 잘 비정규화하는 것이 좋습니다.

경험상 좋은 방법:

* 시계열 모델에 메타데이터 저장
* 시계열 모드, 인스턴스 필드 및 이벤트에는 다음과 같은 필수 정보만 포함됩니다.
  * 시계열 ID
  * 타임 스탬프

자세한 내용은 [이벤트 셰이핑](./time-series-insights-send-events.md#json)을 참조하세요.

## <a name="business-disaster-recovery"></a>비즈니스 재해 복구

Time Series Insights는 Azure 지역 수준에서 중복성을 사용하는 고가용성 서비스입니다. 이러한 고유한 기능을 사용하기 위한 구성은 필요하지 않습니다. Microsoft Azure Platform에는 재해 복구 기능 또는 지역 간 가용성을 포함하는 솔루션을 빌드하도록 도와주는 기능도 포함되어 있습니다. 디바이스 또는 사용자에게 전역, 지역 간 고가용성을 제공하려면 이러한 Azure 재해 복구 기능을 활용합니다. 

BCDR(비즈니스 연속성 및 재해 복구)에 대한 Azure의 기본 제공 기능에 대한 자세한 내용은 [Azure 비즈니스 연속성 기술 지침](https://docs.microsoft.com/azure/resiliency/resiliency-technical-guidance)을 참조하세요. Azure 애플리케이션에서 고가용성 및 재해 복구를 수행하는 전략에 대한 아키텍처 지침은 [Azure 애플리케이션에 대한 재해 복구 및 고가용성](https://docs.microsoft.com/azure/architecture/resiliency/index) 문서를 참조하세요.

> [!NOTE]
> Time Series Insights에는 기본 제공 BCDR이 없습니다. 기본적으로 Azure Storage, Azure IoT Hub 및 Azure Event Hubs에서는 복구 기능이 기본 제공됩니다.

자세한 내용은 다음을 참조하세요.

* [Azure Storage 중복성](https://docs.microsoft.com/azure/storage/common/storage-redundancy)
* [IoT Hub 고가용성 재해 복구](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr)
* [이벤트 허브 정책](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)

BCDR이 필요한 경우에도 복구 전략을 구현할 수 있습니다. 백업 Azure 지역에서 두 번째 Time Series Insights 환경을 만듭니다. 기본 이벤트 원본에서 이 보조 환경으로 이벤트를 전송합니다. 두 번째 전용 소비자 그룹 및 해당 이벤트 원본의 BCDR 지침을 사용합니다.

이러한 단계에 따라 보조 Time Series Insights 환경을 만들고 사용합니다.

1. 두 번째 Azure 지역에 환경을 만듭니다. 자세한 내용은 [Time Series Insights 환경](./time-series-insights-get-started.md)을 참조하세요.
1. 이벤트 원본에 대해 두 번째 전용 소비자 그룹을 만듭니다. 해당 이벤트 원본을 새 환경에 연결합니다. 두 번째 전용 소비자 그룹을 지정했는지 확인합니다. 자세한 내용은 [IoT Hub 설명서](./time-series-insights-how-to-add-an-event-source-iothub.md) 또는 [이벤트 허브 설명서](./time-series-insights-data-access.md)를 참조하세요.
1. 주 지역이 재해 인시던트 중 영향을 받을 경우 백업 Time Series Insights 환경으로 작업 경로를 전환합니다.

> [!IMPORTANT]
> * 장애 조치(failover) 시 지연이 발생할 수 있습니다.
> * 또한 장애 조치(failover)로 인해 작업 경로가 전환되면서 메시지 처리가 일시적으로 급증할 수 있습니다.
> * 자세한 내용은 [Time Series Insights에서 대기 시간 완화](./time-series-insights-environment-mitigate-latency.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- 에 대해 자세히 알아보세요 [저장소 및 수신](./time-series-insights-update-storage-ingress.md) Time Series Insights 미리 보기에 있습니다.

- 에 대 한 자세한 [데이터 모델링](./time-series-insights-update-tsm.md) Time Series Insights 미리 보기에 있습니다.