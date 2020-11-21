---
title: Gen1 환경 계획-Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights Gen1 환경을 준비, 구성 및 배포 하기 위한 모범 사례입니다.
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 5e0f1ea42aa2ba888b89dd652d3397a3a2163a3e
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016210"
---
# <a name="plan-your-azure-time-series-insights-gen1-environment"></a>Azure Time Series Insights Gen1 환경 계획

> [!CAUTION]
> 이는 Gen1 문서입니다.

이 문서에서는 예상 되는 수신 요금 및 데이터 보존 요구 사항에 따라 Azure Time Series Insights Gen1 환경을 계획 하는 방법을 설명 합니다.

## <a name="video"></a>동영상

**Azure Time Series Insights의 데이터 보존 및 계획 방법에 대 한 자세한 내용은이 비디오를 시청** 하세요.<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>모범 사례

Azure Time Series Insights를 시작 하려면 분 단위로 푸시 해야 하는 데이터의 양과 데이터를 저장 하는 데 걸리는 시간을 알고 있는 것이 가장 좋습니다.  

Azure Time Series Insights Sku에 대 한 용량 및 보존에 대 한 자세한 내용은 [Azure Time Series Insights 가격 책정](https://azure.microsoft.com/pricing/details/time-series-insights/)을 참조 하세요.

장기 성공을 위해 Azure Time Series Insights 환경을 최적으로 계획 하려면 다음 특성을 고려 합니다.

- [스토리지 용량](#storage-capacity)
- [데이터 보존 기간](#data-retention)
- [수집 용량](#ingress-capacity)
- [이벤트 셰이핑](#shape-your-events)
- [참조 데이터가 준비 되었는지 확인](#ensure-that-you-have-reference-data)

## <a name="storage-capacity"></a>스토리지 용량

기본적으로 Azure Time Series Insights는 프로 비전 하는 저장소 용량 (단위당 저장소 크기 &#215; 단위) 및 수신에 따라 데이터를 보존 합니다.

## <a name="data-retention"></a>데이터 보존

Azure Time Series Insights 환경의 **데이터 보존 시간** 설정을 변경할 수 있습니다. 최대 400 일의 보존을 사용 하도록 설정할 수 있습니다.

Azure Time Series Insights에는 두 가지 모드가 있습니다.

- 한 가지 모드는 최신 데이터에 대해 최적화 됩니다. **이전 데이터를 삭제** 하는 정책을 적용 하 여 최근 데이터를 인스턴스에서 사용할 수 있습니다. 이 모드는 기본적으로 설정 되어 있습니다.
- 다른는 구성 된 보존 제한 보다 낮게 유지 되도록 데이터를 최적화 합니다. **일시 중지 수신** 은 **저장소 제한 초과 동작** 으로 선택 된 경우 새 데이터가 수신 되지 않도록 방지 합니다.

Azure Portal의 환경 구성 페이지에서 보존 기간을 조정 하 고 두 모드를 전환할 수 있습니다.

> [!IMPORTANT]
> Azure Time Series Insights Gen1 환경에서 최대 400 일의 데이터 보존을 구성할 수 있습니다.

### <a name="configure-data-retention"></a>데이터 보존 구성하기

1. [Azure Portal](https://portal.azure.com)에서 Time Series Insights 환경을 선택합니다.

1. **Time Series Insights 환경** 창의 **설정** 에서 **저장소 구성** 을 선택 합니다.

1. **데이터 보존 시간 (일)** 상자에 1에서 400 사이의 값을 입력 합니다.

   [![보존 구성하기](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

> [!TIP]
> 적절 한 데이터 보존 정책을 구현 하는 방법에 대해 자세히 알아보려면 [보존을 구성 하는 방법](./time-series-insights-how-to-configure-retention.md)을 참조 하세요.

## <a name="ingress-capacity"></a>수집 용량

[!INCLUDE [Azure Time Series Insights Gen1 limits](../../includes/time-series-insights-ga-limits.md)]

### <a name="environment-planning"></a>환경 계획

Azure Time Series Insights 환경을 계획 하는 데 중점을 두는 두 번째 영역은 수신 용량입니다. 일별 수신 저장소 및 이벤트 용량은 분당 1 k b 블록 단위로 측정 됩니다. 허용 되는 최대 패킷 크기는 32 KB입니다. 32 KB 보다 큰 데이터 패킷은 잘립니다.

하나의 환경에서 S1 또는 S2 SKU의 용량을 10개의 단위로 늘릴 수 있습니다. S1 환경에서 S2로 마이그레이션할 수 없습니다. S2 환경에서 S1로 마이그레이션할 수 없습니다.

수신 용량의 경우 먼저 월별 기준으로 필요한 총 수신을 결정 합니다. 그런 다음 분 단위 요구 사항을 확인 합니다.

제한 및 대기 시간은 분당 용량으로 역할을 수행 합니다. 24 시간 이내에 지속 되는 데이터 수신에 급증 하는 경우 앞의 표에 나열 된 속도의 두 배를 수신 속도에서 "파악" 할 수 Azure Time Series Insights.

예를 들어, 단일 S1 SKU가 있는 경우 분당 720 이벤트의 속도로 데이터를 수신 하 고, 1440 이벤트의 속도로 1 시간 이내에 데이터 속도 급증을 받은 경우, 사용자 환경에서 대기 시간이 크게 증가 하지 않습니다. 그러나 1 시간 넘게 분당 1440 이벤트를 초과 하는 경우에는 사용자 환경에서 시각화 되어 쿼리에 사용할 수 있는 데이터에서 대기 시간이 발생할 수 있습니다.

푸시하는 데 필요한 데이터의 양을 미리 알 수 없습니다. 이 경우 Azure Portal 구독에서 [Azure IoT Hub](../iot-hub/monitor-iot-hub.md) 및 [Azure Event Hubs](/archive/blogs/cloud_solution_architect/using-the-azure-rest-apis-to-retrieve-event-hub-metrics) 에 대 한 데이터 원격 분석을 찾을 수 있습니다. 원격 분석은 환경을 프로 비전 하는 방법을 결정 하는 데 도움이 될 수 있습니다. 해당 이벤트 원본에 대 한 Azure Portal의 **메트릭** 창을 사용 하 여 원격 분석을 볼 수 있습니다. 이벤트 원본 메트릭을 이해 하면 Azure Time Series Insights 환경을 보다 효과적으로 계획 하 고 프로 비전 할 수 있습니다.

### <a name="calculate-ingress-requirements"></a>수집 요구 사항 계산하기

수신 요구 사항을 계산 하려면:

- 수신 용량이 분당 평균 요금을 초과 하 고 사용자 환경이 1 시간 미만 동안 두 번의 용량에 해당 하는 예상 수신을 처리할 만큼 큰지 확인 합니다.

- 1 시간 이상 지속 된 후에 발생 하는 급증이 발생 하는 경우에는 평균 급증 률을 사용 합니다. 스파이크 요금을 처리할 수 있는 용량으로 환경을 프로 비전 합니다.

### <a name="mitigate-throttling-and-latency"></a>제한 및 대기 시간에 대응하기

제한 및 대기 시간을 방지 하는 방법에 대 한 자세한 내용은 [대기 시간 및 제한 감소](time-series-insights-environment-mitigate-latency.md)를 참조 하세요.

## <a name="shape-your-events"></a>이벤트 셰이핑

Azure Time Series Insights에 이벤트를 전송 하는 방법이 프로 비전 하는 환경의 크기를 지원 하는지 확인 하는 것이 중요 합니다. 반대로, 환경의 크기를 Azure Time Series Insights 읽은 이벤트 수와 각 이벤트의 크기에 매핑할 수 있습니다. 또한 데이터를 쿼리할 때를 사용 하 여 조각화 및 필터링 하는 데 사용할 수 있는 특성을 고려해 야 합니다.

> [!TIP]
> [이벤트 전송](time-series-insights-send-events.md)에서 JSON 셰이핑 설명서를 검토 합니다.

## <a name="ensure-that-you-have-reference-data"></a>참조 데이터가 있는지 확인

*참조 데이터 집합* 은 이벤트 원본에서 이벤트를 보강 하는 항목의 컬렉션입니다. Azure Time Series Insights 수신 엔진은 이벤트 원본의 각 이벤트를 참조 데이터 집합의 해당 데이터 행과 조인 합니다. 그런 다음 쿼리에 확대 이벤트를 사용할 수 있습니다. 이 조인은 참조 데이터 집합에 정의 된 **기본 키** 열을 기반으로 합니다.

> [!NOTE]
> 참조 데이터는 소급 조인 되지 않습니다. 현재 및 이후의 수신 데이터만 일치 하 고 구성 및 업로드 후에 참조 데이터 집합에 조인 됩니다. Azure Time Series Insights에 많은 양의 기록 데이터를 보내고 먼저 Azure Time Series Insights에 참조 데이터를 업로드 하거나 만들지 않으려는 경우 작업을 다시 실행 해야 할 수 있습니다 (힌트: 재미 있지 않음).  

Azure Time Series Insights에서 참조 데이터를 만들고, 업로드 하 고, 관리 하는 방법에 대 한 자세한 내용은 [참조 데이터 집합 설명서를 참조](time-series-insights-add-reference-data-set.md)하세요.

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>다음 단계

- [Azure Portal에서 새 Azure Time Series Insights 환경을](time-series-insights-get-started.md)만들어 시작 하세요.

- Azure Time Series Insights에 [Event Hubs 이벤트 원본을 추가](./how-to-ingest-data-event-hub.md) 하는 방법에 대해 알아봅니다.

- [IoT Hub 이벤트 원본을 구성](./how-to-ingest-data-iot-hub.md)하는 방법을 참조 하세요.