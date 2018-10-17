---
title: Azure Time Series Insights 환경의 규모 계획 | Microsoft Docs
description: 이 문서에서는 저장소 용량, 데이터 보존, 수집 용량, 모니터링 및 BCDR(비즈니스 재해 복구) 모범 사례에 따라 Azure Time Series Insights 환경을 계획하는 방법을 설명합니다.
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.openlocfilehash: fa178efadf001b70501b132ede67686ae5c06363
ms.sourcegitcommit: 42405ab963df3101ee2a9b26e54240ffa689f140
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47422561"
---
# <a name="plan-your-azure-time-series-insights-environment"></a>Azure Time Series Insights 환경 계획

이 문서에서는 예상되는 수집 속도와 데이터 보존 요구 사항에 따라 Azure Time Series Insights 환경을 계획하는 방법을 설명합니다.

## <a name="video"></a>비디오: 

### <a name="in-this-video-we-cover-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>이 비디오에서는 Time Series Insights 데이터 보존 및 데이터 보존을 계획하는 방법을 설명합니다.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>모범 사례

Time Series Insights를 시작하려면 분당 얼마만큼의 데이터를 수집할지 그리고 데이터를 얼마나 오래 저장해야 하는지 사전에 파악하는 것이 좋습니다.  

두 Time Series Insights SKU의 용량 및 보존에 대한 자세한 내용은 [Time Series Insights 가격](https://azure.microsoft.com/pricing/details/time-series-insights/)을 참조하세요.

장기적인 안목에서 최상의 환경을 계획하려면 다음을 고려해야 합니다. 
- Storage 용량
- 데이터 보존 기간
- 수집 용량 
- 이벤트 셰이핑
- 참조 데이터가 제대로 준비되어 있는지 확인

## <a name="understand-storage-capacity"></a>저장소 용량의 이해
기본적으로 Time Series Insights는 사용자가 프로비전한 저장소 용량(단위 곱하기 단위당 저장소의 시간)과 수집 용량을 기준으로 데이터를 보존합니다.

## <a name="understand-data-retention"></a>데이터 보존의 이해
Time Series Insights 환경의 **데이터 보존 시간** 설정을 구성하여 최대 400일 동안 보존할 수 있습니다.  Time Series Insights에는 두 가지 모드가 있습니다. 하나는 환경에 최신 데이터가 있도록 최적화하는 것(기본값)이고, 다른 하나는 보존 한도에 맞도록 최적화하는 것입니다. 후자의 경우 환경에서 전체 저장소 용량에 도달하면 수집이 일시 중지됩니다.  Azure Portal의 환경 구성 페이지에서 보존 방법을 조정하고 두 가지 모드 중 하나를 선택할 수 있습니다.

Time Series Insights 환경에서 최대 400일까지 데이터가 보존되도록 구성할 수 있습니다.

## <a name="configure-data-retention"></a>데이터 보존 구성하기

1. [Azure Portal](https://portal.azure.com)에서 Time Series Insights 환경을 선택합니다.

2. **Time Series Insights 환경 페이지**의 **설정** 아래에서 **구성**을 선택합니다. 

3. **데이터 보존 시간(일)** 상자에 1~400 사이의 값을 입력합니다.

   ![보존 구성하기](media/environment-mitigate-latency/configure-retention.png)

## <a name="understand-ingress-capacity"></a>수집 용량의 이해

계획 시 주의해야 할 또 다른 영역은 수집 용량입니다. 수집 용량은 분당 할당의 파생 항목입니다. 

제한 관점에서, 패킷 크기가 32KB인 수신 데이터 패킷은 각각 1KB 크기의 32개 이벤트로 처리됩니다. 최대 허용 이벤트 크기는 32KB입니다. 32KB보다 큰 데이터 패킷은 잘립니다.

다음 표에서 각 SKU의 수집 용량을 확인할 수 있습니다.

|SKU  |단위당 월별 이벤트 개수  |단위당 월별 이벤트 크기  |단위당 분당 이벤트 개수  | 단위당 분당 크기   |
|---------|---------|---------|---------|---------|
|S1     |   3천만     |  30GB     |  720    |  720KB   |
|S2     |   3억    |   300GB   | 7,200   | 7,200KB  |

하나의 환경에서 S1 또는 S2 SKU의 용량을 10개의 단위로 늘릴 수 있습니다. S1 환경을 S2 환경으로 또는 S2 환경을 S1 환경으로 마이그레이션할 수는 없습니다.

수집 용량의 경우 먼저 매월 필요한 총 수집 용량을 결정합니다. 그런 다음 제한 및 대기 시간이 중요하게 작용하는 분당 용량을 결정합니다.

데이터 수집 용량의 급증이 24시간 미만 지속되는 경우, Time Series Insights가 위에 표시된 속도보다 2배 높은 수집 속도로 이를 따라잡을 수 있습니다. 

예를 들어 S1 SKU가 하나이고 수신 데이터 속도는 분당 이벤트 720개인데 1시간 미만의 기간 동안 수신 데이터가 급증하는 경우(속도: 이벤트 1,440개 이하) 환경의 대기 시간은 현저하게 길어지지 않습니다. 하지만 1시간이 넘도록 분당 이벤트 수가 1,440개를 초과하는 경우에는 데이터 대기 시간이 길어질 가능성이 높으며, 이러한 상태가 환경에서 시각화되어 쿼리가 가능해집니다. 

예상되는 데이터 푸시 용량을 사전에 예측하기 어려울 수 있습니다. 이 경우 Azure Portal에서 [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) 및 [Azure Event Hubs](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/)의 데이터 텔레메트리를 찾을 수 있습니다. 텔레메트리를 바탕으로 환경을 어떻게 프로비전해야 할지 판단할 수 있습니다. Azure Portal의 **메트릭** 페이지에서 개별 이벤트 소스의 텔레메트리를 확인할 수 있습니다. 이벤트 소스 메트릭을 파악하면 Time Series Insights 환경을 더 효과적으로 계획하고 프로비전하는 데 도움이 됩니다.

### <a name="calculate-ingress-requirements"></a>수집 요구 사항 계산하기

- 수집 용량이 분당 평균 속도보다 높고, 1시간 미만 동안 용량의 2배에 해당하는 예상 수집 용량을 처리할 만큼 환경의 규모가 커야 합니다.

- 수집 용량 급증이 1시간 미만으로 지속되는 경우, 급증한 속도를 평균값으로 사용하여 급증 속도를 처리할 수 있는 용량으로 환경을 프로비전합니다.
 
### <a name="mitigate-throttling-and-latency"></a>제한 및 대기 시간에 대응하기

제한 및 대기 시간을 방지하는 자세한 방법은 [대기 시간 및 제한 완화](time-series-insights-environment-mitigate-latency.md)를 참조하세요.

## <a name="shaping-your-events"></a>이벤트 셰이핑
TSI에 이벤트를 보내는 방식이 프로비전하는 환경의 크기를 지원하도록 하는 것이 중요합니다(반대로 TSI가 읽는 이벤트 수와 각 이벤트의 크기에 환경 크기를 맞출 수 있음).  마찬가지로, 데이터를 쿼리할 때 조각화 및 필터링 기준으로 사용할 특성도 고려해야 합니다.  이러한 사항을 고려할 때, [이벤트 전송 설명서](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-send-events)의 JSON 셰이핑 섹션을 검토하는 것이 좋습니다.  페이지의 아래쪽에 있습니다.  

## <a name="ensuring-you-have-reference-data-in-place"></a>참조 데이터가 제대로 준비되어 있는지 확인
참조 데이터 집합은 이벤트 원본의 이벤트로 확장된 항목의 컬렉션입니다. Time Series Insights 수신 엔진은 이벤트 원본의 각 이벤트와 참조 데이터 집합의 해당 데이터 행을 조인합니다. 이렇게 보강된 이벤트는 쿼리에 사용할 수 있습니다. 이 조인은 참조 데이터 집합에서 정의된 기본 키 열을 기준으로 합니다.

참조 데이터는 소급되어 조인되지 않습니다. 즉, 참조 데이터 집합이 일단 구성되고 업로드되면, 현재 및 미래의 수신 데이터만 일치되고 참조 데이터 집합에 조인됩니다.  많은 양의 기록 데이터를 TSI로 전송하려고 하며, TSI에서 참조 데이터를 먼저 업로드하거나 만들지 않을 경우 작업을 다시 실행해야 할 수 있습니다(중요 참고 사항).  

TSI에서 참조 데이터를 만들고 업로드하고 관리하는 방법에 대한 자세한 내용은 [참조 데이터 집합 설명서](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set)를 참조하세요.

## <a name="business-disaster-recovery"></a>비즈니스 재해 복구
Azure 서비스로 Time Series Insights는 Azure 지역 수준에서 중복을 사용하여 솔루션의 추가 작업 없이도 HA(고가용성)를 제공합니다. Microsoft Azure Platform에는 재해 복구(DR) 기능 또는 지역 간 가용성을 포함하는 솔루션을 빌드하도록 도와주는 기능도 포함되어 있습니다. 장치 또는 사용자에게 전역, 지역 간 고가용성을 제공하려면 이러한 Azure DR 기능을 활용합니다. [Azure 비즈니스 연속성 기술 지침](../resiliency/resiliency-technical-guidance.md) 문서에서는 비즈니스 연속성 및 DR에 대한 Azure의 기본 제공 기능을 설명합니다. [Azure 응용 프로그램에 대한 재해 복구 및 고가용성](https://docs.microsoft.com/azure/architecture/resiliency/index) 문서는 Azure 응용 프로그램에서 HA 및 DR을 달성하기 위한 전략에 대한 아키텍처 지침을 제공합니다.

Azure Time Series Insights에서는 BCDR(비즈니스 재해 복구) 기능이 기본 제공되지 않습니다.
기본적으로 Azure IoT Hub와 Event Hubs에서는 모두 복구 기능이 기본 제공됩니다.

IoT Hub의 BCDR 정책에 대해 자세히 알려면 [여기](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr)로 이동합니다.  

Event Hub의 BCDR 정책에 대해 자세히 알려면 [여기](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)로 이동합니다.

하지만 BCDR이 필요한 고객은 다음 방법을 사용하여 복구 전략을 계속 구현할 수 있습니다.
백업 Azure 지역에서 두 번째 Time Series Insights 환경을 만든 다음 두 번째 전용 소비자 그룹과 기본 이벤트 원본의 BDCR 지침을 사용하여 기본 이벤트 원본에서 이 보조 환경으로 이벤트를 전송합니다.  

1.  두 번째 지역에서 환경을 만듭니다.  Time Series Insights 환경 리소스 만드는 방법에 대한 자세한 내용은 [여기](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)를 참조합니다.
2.  이벤트 원본에 대한 두 번째 전용 소비자 그룹을 만들고 새 환경에 해당 이벤트 원본을 연결합니다.  두 번째 전용 소비자 그룹을 지정했는지 확인합니다.  이에 대해 자세히 알려면 [IoT Hub 설명서](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) 또는 [이벤트 허브 설명서](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access) 중 하나를 수행할 수 있습니다.
3.  주 지역이 재해 사건 중 아래로 이동해야 하는 경우 백업 Time Series Insights 환경으로 작업을 전환합니다.  

모든 장애 조치(failover) 시나리오에서는 TSI가 메시지 처리를 다시 시작할 수 있게 될 때까지 시간이 지연될 수 있으며, 이로 인해 메시지 처리량이 급증할 수 있음을 **기억해야 합니다**. 자세한 내용은 [이 문서](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency)를 참조하세요.

## <a name="next-steps"></a>다음 단계
- [Event Hub 이벤트 소스를 추가하는 방법](time-series-insights-how-to-add-an-event-source-eventhub.md)
- [IoT Hub 이벤트 소스를 추가하는 방법](time-series-insights-how-to-add-an-event-source-iothub.md)
