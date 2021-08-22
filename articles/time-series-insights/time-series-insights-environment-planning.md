---
title: Gen1 환경 계획 - Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights Gen1 환경 준비, 구성 및 배포에 대한 모범 사례입니다.
services: time-series-insights
ms.service: time-series-insights
author: tedvilutis
ms.author: tvilutis
manager: cnovak
ms.reviewer: orspodek
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: e93c415e464b344a24593428c267fb09e6566e08
ms.sourcegitcommit: 8942cdce0108372d6fc5819c71f7f3cf2f02dc60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2021
ms.locfileid: "113136835"
---
# <a name="plan-your-azure-time-series-insights-gen1-environment"></a>Azure Time Series Insights Gen1 환경을 계획해 보세요

> [!CAUTION]
> 이는 Gen1 문서입니다.

이 문서에서는 예상되는 수신 속도와 데이터 보존 요구 사항에 따라 Azure Time Series Insights Gen1 환경을 계획하는 방법을 설명합니다.

## <a name="video"></a>비디오

**이 비디오를 시청하여 Azure Time Series Insights 데이터 보존 및 계획 방법에 대해 자세히 알아보세요**.<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>모범 사례

Azure Time Series Insights를 시작하려면 분당 푸시할 데이터의 양과 데이터를 저장해야 하는 기간을 아는 것이 가장 중요합니다.

두 Azure Time Series Insights SKU의 용량 및 보존에 대한 자세한 내용은 [Azure Time Series Insights 가격 책정](https://azure.microsoft.com/pricing/details/time-series-insights/)을 참조하세요.

장기적인 성공을 위해 Azure Time Series Insights 환경을 가장 잘 계획하려면 다음 특성을 고려하세요.

- [스토리지 용량](#storage-capacity)
- [데이터 보존 기간](#data-retention)
- [수집 용량](#ingress-capacity)
- [이벤트 셰이핑](#shape-your-events)
- [참조 데이터가 제대로 있는지 확인](#ensure-that-you-have-reference-data)

## <a name="storage-capacity"></a>스토리지 용량

기본적으로 Azure Time Series Insights는 사용자가 프로비전한 스토리지 용량(단위 곱하기 단위당 스토리지의 시간)과 수신 용량을 기준으로 데이터를 보존합니다.

## <a name="data-retention"></a>데이터 보존

Azure Time Series Insights 환경에서 **데이터 보존 시간** 설정을 변경할 수 있습니다. 최대 400일 동안 보존하도록 설정할 수 있습니다.

Azure Time Series Insights에는 두 가지 모드가 있습니다.

- 한 모드는 최신 데이터에 최적화됩니다. 인스턴스에서 사용할 수 있는 최신 데이터를 그대로 두는 **이전 데이터 제거** 정책을 적용합니다. 이 모드는 기본적으로 설정되어 있습니다.
- 다른 하나는 구성된 보존 한도 미만으로 유지되도록 데이터를 최적화합니다. **수신 일시 중지** 는 **스토리지 한도가 동작을 초과하여** 선택된 경우 새 데이터가 수신되지 않도록 방지합니다.

Azure Portal의 환경 구성 페이지에서 두 모드 간에 보존과 토글을 조정할 수 있습니다.

> [!IMPORTANT]
> Azure Time Series Insights Gen1 환경에서 최대 400일의 데이터 보존을 구성할 수 있습니다.

### <a name="configure-data-retention"></a>데이터 보존 구성

1. [Azure Portal](https://portal.azure.com)에서 Time Series Insights 환경을 선택합니다.

1. **Time Series Insights 환경** 창의 **설정** 에서 **스토리지 구성** 을 선택합니다.

1. **데이터 보존 시간(일)** 상자에 1~400 사이의 값을 입력합니다.

   [![보존 구성하기](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

> [!TIP]
> 적절한 데이터 보존 정책을 구현하는 방법에 대한 자세한 내용은 [보존을 구성하는 방법](./time-series-insights-how-to-configure-retention.md)을 읽어보세요.

## <a name="ingress-capacity"></a>수집 용량

[!INCLUDE [Azure Time Series Insights Gen1 limits](../../includes/time-series-insights-ga-limits.md)]

### <a name="environment-planning"></a>환경 계획

Azure Time Series Insights 환경을 계획하는 데 중요한 두 번째 영역은 수신 용량입니다. 일별 수신 스토리지 및 이벤트 용량은 분당 1KB 블록 단위로 측정됩니다. 허용되는 최대 패킷 크기는 32KB입니다. 32KB보다 큰 데이터 패킷은 잘립니다.

하나의 환경에서 S1 또는 S2 SKU의 용량을 10개의 단위로 늘릴 수 있습니다. S1 환경에서 S2로 마이그레이션할 수 없습니다. S2 환경에서 S1으로 마이그레이션할 수 없습니다.

수신 용량의 경우 먼저 월별 기준으로 필요한 총 수신을 결정합니다. 그런 다음 분 단위 요구 사항을 확인합니다.

제한 및 대기 시간은 분당 용량으로 역할을 수행합니다. 데이터 수신에 24시간 미만으로 지속되는 데이터 급증 현상이 있다면, Azure Time Series Insights는 앞의 테이블에 나열된 속도의 2배로 수신 속도를 “따라잡을” 수 있습니다.

예를 들어 S1 SKU가 하나이고 수신 데이터 속도는 분당 이벤트 720개인데 1시간 미만의 시간 동안 수신 데이터가 급증하는 경우(속도: 이벤트 1,440개 이하) 환경의 대기 시간은 현저하게 길어지지 않습니다. 하지만 1시간이 넘도록 분당 이벤트 수가 1,440개를 초과하는 경우에는 데이터 대기 시간이 길어질 가능성이 높으며, 이러한 상태가 환경에서 시각화되어 쿼리가 가능해집니다.

푸시하는 데 필요한 데이터의 양을 미리 알 수 없습니다. 이 경우 Azure Portal 구독에서 [Azure IoT Hub](../iot-hub/monitor-iot-hub.md) 및 [Azure Event Hubs](/archive/blogs/cloud_solution_architect/using-the-azure-rest-apis-to-retrieve-event-hub-metrics) 에 대한 데이터 원격 분석을 찾을 수 있습니다. 원격 분석을 바탕으로 환경을 어떻게 프로비전해야 할지 판단할 수 있습니다. Azure Portal의 **메트릭** 창에서 개별 이벤트 원본의 원격 분석을 확인할 수 있습니다. 이벤트 원본 메트릭을 파악하면 Azure Time Series Insights 환경을 더 효과적으로 계획하고 프로비전하는 데 도움이 됩니다.

### <a name="calculate-ingress-requirements"></a>수집 요구 사항 계산하기

수신 요구 사항을 계산하려면:

- 수신 용량이 분당 평균 속도보다 높고, 1시간 미만 동안 용량의 2배에 해당하는 예상 수신 용량을 처리할 만큼 환경의 규모가 커야 합니다.

- 수신 용량 급증이 1시간 미만으로 지속되는 경우, 급증한 속도를 평균값으로 사용합니다. 급증 속도를 처리할 수 있는 용량으로 환경을 프로비전합니다.

### <a name="mitigate-throttling-and-latency"></a>제한 및 대기 시간에 대응하기

제한 및 대기 시간을 방지하는 자세한 방법은 [대기 시간 및 제한 완화](time-series-insights-environment-mitigate-latency.md)를 참조하세요.

## <a name="shape-your-events"></a>이벤트 셰이핑

Azure Time Series Insights에 이벤트를 전송하는 방법이 프로비전하는 환경의 크기를 지원하는지 확인하는 것이 중요합니다. (반대로, 환경의 크기를 Azure Time Series Insights가 읽은 이벤트 수와 각 이벤트의 크기에 매핑할 수 있습니다.) 또한 데이터를 쿼리할 때 슬라이스하고 필터링하려는 특성에 대해 고려해야 합니다.

> [!TIP]
> [이벤트 전송](time-series-insights-send-events.md)에서 JSON 셰이핑 설명서를 검토합니다.

## <a name="ensure-that-you-have-reference-data"></a>참조 데이터가 있는지 확인

*참조 데이터 집합* 은 이벤트 원본의 이벤트로 확장된 항목의 컬렉션입니다. Azure Time Series Insights 수신 엔진은 이벤트 원본의 각 이벤트와 참조 데이터 집합의 해당 데이터 행을 조인합니다. 그런 다음 쿼리에 확대 이벤트를 사용할 수 있습니다. 이 조인은 참조 데이터 집합에 정의된 **기본 키** 열을 기반으로 합니다.

> [!NOTE]
> 참조 데이터는 소급되어 조인되지 않습니다. 현재 및 이후의 수신 데이터만 일치하고 구성 및 업로드 후에 참조 데이터 집합에 조인됩니다. 많은 양의 기록 데이터를 Azure Time Series Insights로 보내려고 하고 Azure Time Series Insights 참조 데이터를 먼저 업로드하거나 만들지 않는 경우 작업을 다시 수행해야 할 수 있습니다(힌트: 재미있지 않습니다).

Azure Time Series Insights 참조 데이터를 만들고 업로드하고 관리하는 방법에 대한 자세한 내용은 [데이터 집합 설명서 참조 사항](time-series-insights-add-reference-data-set.md)을 읽어보세요.

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>다음 단계

- [Azure Portal에 새 Azure Time Series Insights 환경](time-series-insights-get-started.md)을 만들어 시작합니다.

- Azure Time Series Insights에 [Event Hubs 이벤트 원본 추가](./how-to-ingest-data-event-hub.md) 방법을 알아봅니다.

- [IoT Hub 이벤트 원본을 구성](./how-to-ingest-data-iot-hub.md)하는 방법에 대해 읽어봅니다.
