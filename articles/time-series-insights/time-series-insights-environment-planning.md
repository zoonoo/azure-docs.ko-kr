---
title: Azure Time Series Insights 환경의 규모 계획 | Microsoft Docs
description: 이 문서에서는 Azure Time Series Insights 환경을 계획 하는 경우 모범 사례를 준수 하는 방법을 설명 합니다. 적용 되는 영역에 저장소 용량, 데이터 보존, 수집 용량, 모니터링 및 비즈니스 연속성 및 재해 복구 (BCDR) 포함 됩니다.
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: 9d9f9b7fe7aafa927f023e1d4e30e079a26f7fab
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431031"
---
# <a name="plan-your-azure-time-series-insights-ga-environment"></a>Azure Time Series Insights GA 환경 계획

이 문서에 예상된 수신 속도 및 데이터 보존 요구 사항에 따라 Azure Time Series Insights 일반 공급 (GA) 환경을 계획 하는 방법을 설명 합니다.

## <a name="video"></a>비디오

**이 비디오에 대 한 계획 하는 방법과 Azure Time Series Insights 데이터 보존에 자세히 알아보려면**:<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>모범 사례

Time Series Insights를 사용 하 여 시작 하려면 것이 좋습니다 얼마나 많은 데이터를 수집할지 그리고 데이터를 저장 해야 하는 시간 및 분을 알고 있는 경우.  

두 Time Series Insights SKU의 용량 및 보존에 대한 자세한 내용은 [Time Series Insights 가격](https://azure.microsoft.com/pricing/details/time-series-insights/)을 참조하세요.

장기적인 성공 위한 Time Series Insights 환경 계획 가장을 하려면 다음 특성을 고려 합니다.

- <a href="#storage-capacity">저장소 용량</a>
- <a href="#data-retention">데이터 보존 기간</a>
- <a href="#ingress-capacity">수집 용량</a>
- <a href="#shape-your-events">이벤트 셰이핑</a>
- <a href="#ensure-that-you-have-reference-data">에 참조 데이터가 있는지 확인</a>

## <a name="storage-capacity"></a>저장소 용량

기본적으로 Time Series Insights는 프로 비전 된 저장소의 양을 기준으로 데이터를 보존 (단위 &#215; 단위당 저장소의 크기) 및 수신 합니다.

## <a name="data-retention"></a>데이터 보존

변경할 수 있습니다 합니다 **데이터 보존 시간** Time Series Insights 환경에서 설정 합니다. 최대 400 일 보존을 설정할 수 있습니다. 

Time Series Insights에는 두 가지 모드가 있습니다. 사용자 환경에 최신 데이터가 있는지 확인 하는 것에 대 한 하나의 모드 최적화 합니다. 이 모드를 기본적으로 켜져 있습니다. 

다른 모드 보존 한도 충족 되도록 하기 위해 최적화 합니다. 두 번째 모드에서는 수신 환경의 전체 저장소 용량에 도달한 경우 일시 중지 됩니다. 

보존 및 Azure portal에서 환경 구성 페이지에서 두 가지 모드 사이 전환을 조정할 수 있습니다.

Time Series Insights 환경에서 최대 400일까지 데이터가 보존되도록 구성할 수 있습니다.

### <a name="configure-data-retention"></a>데이터 보존 구성하기

1. [Azure Portal](https://portal.azure.com)에서 Time Series Insights 환경을 선택합니다.

1. 에 **Time Series Insights 환경** 창 아래에 있는 **설정**를 선택 **구성**합니다.

1. 에 **데이터 보존 기간 (일)** 상자에 1과 400 사이의 값을 입력 합니다.

   [![보존 구성](media/environment-mitigate-latency/configure-retention.png)](media/environment-mitigate-latency/configure-retention.png#lightbox)

> [!TIP]
> 적절 한 데이터 보존 정책을 구현 하는 방법에 대 한 자세한 내용은 참조 하세요 [보존을 구성 하는 방법](./time-series-insights-how-to-configure-retention.md)합니다.

## <a name="ingress-capacity"></a>수집 용량

Time Series Insights 환경 계획에 대해 중점적으로 두 번째 영역은 수집 용량입니다. 수신 용량은 분당 할당의 파생 항목입니다.

제한 관점에서 패킷 크기가 32KB 수신 데이터 패킷의 각 1KB 크기의 32 이벤트로 처리 됩니다. 허용 된 최대 이벤트 크기는 32KB입니다. 32KB 보다 큰 데이터 패킷은 잘립니다.

다음 표에서 각 Time Series Insights SKU의 단위 당 수집 용량을 보여 줍니다.

|SKU  |월별 이벤트 수  |월별 이벤트 크기  |분당 이벤트 개수  |분당 이벤트 크기  |
|---------|---------|---------|---------|---------|
|S1     |   3천만     |  30GB     |  720    |  720KB   |
|S2     |   3억    |   300GB   | 7,200   | 7,200KB  |

하나의 환경에서 S1 또는 S2 SKU의 용량을 10개의 단위로 늘릴 수 있습니다. S2에서 S1 환경을 마이그레이션할 수 없습니다. S2 환경을 S1 마이그레이션할 수 없습니다.

수신 용량의 경우 먼저는 매월 필요한 총 수집을 결정 합니다. 다음으로, 분당 요구 사항도 결정 합니다. 

제한 및 대기 시간-분당 용량에서 역할을 합니다. 24 시간 미만 동안 지속 되는 데이터 수집 급증 하는 경우 Time Series Insights 수 따라잡을 앞의 표에 나열 된 요금은 두 번 수신 속도로 합니다.

예를 들어, 분당 720 이벤트의 속도로 수신 데이터가 단일 S1 SKU에 있고 데이터 속도 보다 작거나 1 시간 동안 1,440 이벤트 이하의 속도로 급증 하는 경우에 없는 경우 대기 시간은 없습니다 환경 그러나 1 시간 이상에 대 한 분당 1, 440 이벤트를 초과 하면 데이터를 시각화 하 고 사용자 환경에서 쿼리에 사용할 수 있는 대기 시간을 발생 합니다 하는 경우가 많습니다.

푸시 하려는 데이터의 양을 미리 모를 수도 있습니다. 이 경우에 대 한 원격 분석 데이터를 찾을 수 있습니다 [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) 하 고 [Azure Event Hubs](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) Azure 포털 구독에 있습니다. 원격 분석 환경을 프로 비전 하는 방법을 결정할 수 있습니다. 사용 된 **메트릭** 원격 분석을 보려는 각 이벤트 원본에 대 한 Azure 포털의 창. 이벤트 소스 메트릭을 파악하면 Time Series Insights 환경을 더 효과적으로 계획하고 프로비전하는 데 도움이 됩니다.

### <a name="calculate-ingress-requirements"></a>수집 요구 사항 계산하기

수신 요구 사항을 계산 합니다.

- 수신 용량 위에 평균 분당 요금에는 사용자 환경에 예상된 배에 해당 하는 두 번 1 시간 이내에 대 한 용량을 처리 하기에 충분 한지 확인 합니다.

- 수신 스파이크가 발생 하는 마지막 1 시간 이상, 사용 하 여 급증 속도 평균값으로 합니다. 급증 속도 처리할 수 있는 용량으로 환경을 프로 비전 합니다.

### <a name="mitigate-throttling-and-latency"></a>제한 및 대기 시간에 대응하기

제한 및 대기 시간을 방지하는 자세한 방법은 [대기 시간 및 제한 완화](time-series-insights-environment-mitigate-latency.md)를 참조하세요.

## <a name="shape-your-events"></a>이벤트 셰이핑

Time Series Insights 이벤트에 프로 비전 하는 환경의 크기를 지 원하는 보내는 방식이 되도록 두는 것이 반드시 합니다. (반대로 매핑할 수 있습니다 Time Series Insights 읽고 이벤트 개수 환경의 크기와 각 이벤트의 크기입니다.) 사용 하 여 조각화를 기준으로 필터링 하려는 특성에 대해 생각 하는 중요 한 것도 데이터를 쿼리할 때.

> [!TIP]
> JSON 셰이핑의 설명서를 검토 [이벤트를 보내는](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-send-events)합니다.

## <a name="ensure-that-you-have-reference-data"></a>참조 데이터 했는지 확인

A *데이터 집합 참조* 은 이벤트 원본의 이벤트를 보강 하는 항목의 컬렉션입니다. Time Series Insights 수신 엔진은 참조 데이터 집합의 해당 데이터 행을 사용 하 여 이벤트 원본의 각 이벤트를 조인합니다. 보강 된 이벤트는 다음 쿼리에 사용할 수 있습니다. 조인을 기반으로 합니다 **기본 키** 참조 데이터 집합에 정의 된 열입니다.

> [!NOTE]
> 참조 데이터는 소급 되어 조인 되지 않습니다. 현재 및 미래의 수신 데이터만 일치 되 고 구성 및 업로드 한 후에 참조 데이터 집합에 가입 합니다. Time Series Insights에 많은 양의 기록 데이터를 전송 및 첫 번째 업로드 하지 않거나 Time Series Insights에서 참조 데이터를 만들지 않는 하려는 것이 있으면 작업을 다시 실행 (힌트: 재미 있는 없습니다).  

만들기, 업로드 및 Time Series Insights에서 참조 데이터를 관리 하는 방법에 대 한 자세한 내용은를 참조 하세요. 당사의 [참조 데이터 집합 설명서](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set)합니다.

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>다음 단계

- 만들기 시작 [Azure portal에서 새 Time Series Insights 환경을](time-series-insights-get-started.md)합니다.

- 설명 하는 방법 [Event Hubs 이벤트 원본을 추가](time-series-insights-how-to-add-an-event-source-eventhub.md) Time Series Insights에 합니다.

- 방법에 대해 알아보세요 [IoT Hub 이벤트 원본을 구성](time-series-insights-how-to-add-an-event-source-iothub.md)합니다.
