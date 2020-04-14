---
title: GA 환경 계획 - Azure 타임시리즈 인사이트 | 마이크로 소프트 문서
description: Azure Time Series Insights GA 환경을 준비, 구성 및 배포하는 모범 사례입니다.
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/13/2020
ms.custom: seodec18
ms.openlocfilehash: 85910ee5467ecc9f4fe3c1a8bc13110b6f218e5c
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272715"
---
# <a name="plan-your-azure-time-series-insights-ga-environment"></a>Azure 타임시리즈 인사이트 GA 환경 계획

이 문서에서는 예상되는 받는 속도 및 데이터 보존 요구 사항에 따라 Azure Time Series Insights 일반 가용성(GA) 환경을 계획하는 방법을 설명합니다.

## <a name="video"></a>비디오

**Azure 시계열 인사이트내 데이터 보존 및 계획 방법에 대해 자세히 알아보려면 이 비디오를 시청하십시오.**<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>모범 사례

Azure Time Series Insights를 시작하려면 분 단위로 푸시할 데이터의 양과 데이터를 저장해야 하는 기간을 아는 것이 가장 좋습니다.  

두 타임시리즈 인사이트 SCO의 용량 및 보존에 대한 자세한 내용은 [타임시리즈 인사이트 가격 책정을](https://azure.microsoft.com/pricing/details/time-series-insights/)참조하십시오.

장기적인 성공을 위해 타임시리즈 인사이트 환경을 가장 잘 계획하려면 다음 특성을 고려하십시오.

- [스토리지 용량](#storage-capacity)
- [데이터 보존 기간](#data-retention)
- [수집 용량](#ingress-capacity)
- [이벤트 셰이핑](#shape-your-events)
- [참조 데이터가 제자리에 있는지 확인](#ensure-that-you-have-reference-data)

## <a name="storage-capacity"></a>스토리지 용량

기본적으로 Time Series Insights는 프로비저닝한 저장소 양(단위당 저장소 양&#215; 단위) 및 침투에 따라 데이터를 유지합니다.

## <a name="data-retention"></a>데이터 보존

Azure Time Series Insights 환경에서 **데이터 보존 시간** 설정을 변경할 수 있습니다. 최대 400일의 보존을 활성화할 수 있습니다. 

Azure 타임시리즈 인사이트에는 두 가지 모드가 있습니다.

* 하나의 모드는 최신 데이터에 대해 최적화됩니다. 인스턴스에서 사용할 수 있는 최근 데이터를 남기는 **이전 데이터를 지우는** 정책을 적용합니다. 이 모드는 기본적으로 설정되어 있습니다. 
* 다른 하나는 구성된 보존 한도 이하로 유지하도록 데이터를 최적화합니다. **일시 중지 는** **저장소 제한을 초과한 동작으로**선택될 때 새 데이터가 수집되는 것을 방지합니다.

Azure 포털에서 환경의 구성 페이지에서 두 모드 간에 보존 및 전환을 조정할 수 있습니다.

> [!IMPORTANT]
> Azure Time Series Insights GA 환경에서 최대 400일의 데이터 보존을 구성할 수 있습니다.

### <a name="configure-data-retention"></a>데이터 보존 구성하기

1. [Azure Portal](https://portal.azure.com)에서 Time Series Insights 환경을 선택합니다.

1. **[타임시리즈 인사이트] 환경** 창에서 **설정에서** **저장소 구성을**선택합니다.

1. 데이터 **보존 시간(일 수)** 상자에 1에서 400 사이의 값을 입력합니다.

   [![보존 구성하기](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

> [!TIP]
> 적절한 데이터 보존 정책을 구현하는 방법에 대한 자세한 내용은 [보존 구성 방법을](./time-series-insights-how-to-configure-retention.md)참조하십시오.

## <a name="ingress-capacity"></a>수집 용량

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

### <a name="environment-planning"></a>환경 계획

타임시리즈 인사이트 환경 계획에 중점을 두 번째로 중점을 두 번째는 용량 을 입력하는 것입니다. 일일 침투 스토리지 및 이벤트 용량은 분당 1KB 블록으로 측정됩니다. 허용되는 최대 패킷 크기는 32KB입니다. 32KB보다 큰 데이터 패킷은 잘립니다.

하나의 환경에서 S1 또는 S2 SKU의 용량을 10개의 단위로 늘릴 수 있습니다. S1 환경에서 S2로 마이그레이션할 수 없습니다. S2 환경에서 S1로 마이그레이션할 수 없습니다.

침투 용량의 경우 먼저 월별 기준으로 필요한 총 침투를 결정합니다. 다음으로 분당 요구 사항을 결정합니다. 

제한 및 대기 시간은 분당 용량에서 역할을 합니다. 24시간 미만의 데이터 수집이 급증한 경우, [패계]는 이전 표에 나열된 속도의 2배에 달하는 인적률로 "따라잡을" 수 있습니다.

예를 들어 단일 S1 SKU가 있는 경우 분당 720개의 이벤트 속도로 데이터를 수집하고 데이터 속도는 1,440개 이하의 이벤트 속도로 1시간 미만 동안 급증하므로 환경에 눈에 띄는 대기 시간이 없습니다. 그러나 1시간 이상 분당 1,440개의 이벤트를 초과하는 경우 시각화되고 사용자 환경에서 쿼리에 사용할 수 있는 데이터의 대기 시간이 발생할 수 있습니다.

푸시할 데이터의 양을 미리 알지 못할 수도 있습니다. 이 경우 Azure 포털 구독에서 [Azure IoT Hub](../iot-hub/iot-hub-metrics.md) 및 Azure 이벤트 [허브에](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) 대한 데이터 원격 분석을 찾을 수 있습니다. 원격 분석을 통해 환경을 프로비전하는 방법을 결정할 수 있습니다. 각 이벤트 원본에 대해 Azure 포털의 **메트릭** 창을 사용하여 원격 분석을 봅니다. 이벤트 소스 메트릭을 파악하면 Time Series Insights 환경을 더 효과적으로 계획하고 프로비전하는 데 도움이 됩니다.

### <a name="calculate-ingress-requirements"></a>수집 요구 사항 계산하기

인서스 요구 사항을 계산하려면 다음을 수행하십시오.

- 인그리스 용량이 분당 평균 속도보다 높으며, 1시간 미만의 용량의 2배에 해당하는 예상 인그리스를 처리할 수 있을 만큼 환경이 충분히 큰지 확인합니다.

- 1시간 이상 지속되는 침투 스파이크가 발생하는 경우 스파이크 속도를 평균으로 사용합니다. 스파이크 속도를 처리할 수 있는 용량을 가진 환경을 프로비전합니다.

### <a name="mitigate-throttling-and-latency"></a>제한 및 대기 시간에 대응하기

제한 및 대기 시간을 방지하는 방법에 대한 자세한 내용은 [대기 시간 및 제한 완화를](time-series-insights-environment-mitigate-latency.md)참조하십시오.

## <a name="shape-your-events"></a>이벤트 셰이핑

Time Series Insights에 이벤트를 보내는 방식이 프로비저닝하려는 환경의 크기를 지원하는지 확인하는 것이 중요합니다. 반대로 환경의 크기를 [Time Series Insights]가 읽는 이벤트 수와 각 이벤트의 크기에 매핑할 수 있습니다. 또한 데이터를 쿼리할 때 슬라이스하고 필터링하는 데 사용할 수 있는 특성을 생각해 보십시오.

> [!TIP]
> [이벤트 보내기에서](time-series-insights-send-events.md)JSON 형성 설명서를 검토합니다.

## <a name="ensure-that-you-have-reference-data"></a>참조 데이터가 있는지 확인

*참조 데이터 집합은* 이벤트 소스에서 이벤트를 보강하는 항목의 모음입니다. 타임 시리즈 Insights 는 이벤트 소스의 각 이벤트를 참조 데이터 집합의 해당 데이터 행과 조인합니다. 그런 다음 보강된 이벤트를 쿼리에 사용할 수 있습니다. 조인은 참조 데이터 집합에 정의된 **기본 키** 열을 기반으로 합니다.

> [!NOTE]
> 참조 데이터는 소급하여 조인되지 않습니다. 현재 및 미래의 수집 데이터만 일치하고 구성 및 업로드한 후 참조 데이터 집합에 조인됩니다. 많은 양의 기록 데이터를 시계열 인사이트로 보내려고 하고 시계열 인사이트에서 참조 데이터를 먼저 업로드하거나 만들지 않으려면 작업을 다시 수행해야 할 수 있습니다(힌트: 재미 있음).  

시계열 인사이트에서 참조 데이터를 생성, 업로드 및 관리하는 방법에 대해 자세히 알아보려면 [참조 데이터 집합 설명서를](time-series-insights-add-reference-data-set.md)참조하십시오.

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>다음 단계

- [Azure 포털에서 새 타임시리즈 인사이트 환경을](time-series-insights-get-started.md)만들어 시작하십시오.

- [이벤트 허브 이벤트 소스를](time-series-insights-how-to-add-an-event-source-eventhub.md) 타임시리즈 인사이트에 추가하는 방법을 알아봅니다.

- [IoT Hub 이벤트 소스를 구성하는](time-series-insights-how-to-add-an-event-source-iothub.md)방법에 대해 읽어보십시오.
