---
title: '개요: Azure Time Series Insights 미리 보기 | Microsoft Docs'
description: Azure Time Series Insights Preview 개요입니다.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: anshan
ms.workload: big-data
ms.topic: overview
ms.date: 04/26/2019
ms.custom: seodec18
ms.openlocfilehash: 6bcf0155be87a5b36c6b850fa889e23a340ac7ec
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237505"
---
# <a name="azure-time-series-insights-preview"></a>Azure Time Series Insights 미리 보기

Azure Time Series Insights 미리 보기는 엔드투엔드 PaaS(Platform as a Service) 제품입니다. 고도로 컨텍스트화되고 시계열에 최적화된 IoT 규모 데이터를 수집, 처리, 저장, 분석 및 쿼리하는 데 사용됩니다. Time Series Insights는 임시 데이터 탐색 및 운영 분석에 적합합니다. Time Series Insights는 산업 IoT 배포의 광범위한 요구를 충족하는 고유하게 확장 가능하고 사용자 지정된 서비스 제품입니다.

> [!TIP]
> 일반 공급되는 기능은 [Azure Time Series Insights GA 개요](time-series-insights-overview.md)를 읽어보세요.

## <a name="video"></a>비디오

### <a name="learn-more-about-azure-time-series-insights-preview-br"></a>Azure Time Series Insights 미리 보기에 대해 자세히 알아보세요. </br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Time-Series-Insights-e2e-solution-for-industrial-IoT-analytics/player]

## <a name="define-iot-data"></a>IoT 데이터 정의

IoT 데이터란 자산 집약적 조직에서 사용할 수 있는 모든 산업 데이터를 말합니다. IoT 데이터는 비교적 정리되지 않은 측정값을 기록하는 자산에서 전송되기 때문에 고도로 비정형인 경우가 많습니다. 이 측정값에는 온도, 동작 및 습도가 포함됩니다. 때때로 이러한 데이터 스트림은 상당한 차이, 손상된 메시지, 거짓 판독값 등의 특성이 있습니다. 분석을 수행하려면 먼저 이러한 스트림의 데이터를 정리해야 합니다.

대체로 IoT 데이터는 CRM 또는 ERP와 같은 자사 원본에서 제공되는 추가 데이터 입력의 컨텍스트에서만 의미가 있습니다. 날씨 또는 위치와 같은 타사 데이터 원본에서 입력이 제공되기도 합니다.

그 결과, 데이터의 일부만 운영 및 비즈니스 목적으로 사용됩니다. 이러한 데이터는 비즈니스 보고 및 분석에 대한 현재의 일관적이고 포괄적이며 올바른 정보를 제공합니다. 수집된 IoT 데이터를 작업 가능한 인사이트로 전환하려면 다음이 필요합니다.

* 분석을 위해 데이터를 정리, 필터링, 보간, 변환 및 준비하는 데이터 처리
* 데이터를 탐색 및 파악하는(즉, 데이터를 정규화 및 컨텍스트화하는) 구조
* 수십 년간 처리되거나 파생된 데이터 및 원시 데이터의 장기 또는 무기한 보존을 위한 경제적인 스토리지

다음 이미지에는 일반적인 IoT 데이터 흐름이 나와 있습니다.

  ![IoT 데이터 흐름][1]

## <a name="azure-time-series-insights-for-industrial-iot"></a>산업 IoT용 Azure Time Series Insights

현재 IoT 환경은 다양합니다. 제조, 자동차, 에너지, 공공 설비, 스마트 빌딩, 컨설팅 업계의 고객이 IoT 서비스를 사용하고 있습니다. IoT 시나리오로는 데이터 모양을 알 수 없는 임시 데이터 탐색을 비롯하여 스키마화된 데이터 또는 명시적으로 모델링된 데이터에 대한 운영 분석을 통해 운영 효율성을 도출하는 경우가 있습니다. 이러한 시나리오는 일반적으로 병렬로 존재하며 다양한 사용 사례를 지원합니다. 산업 IoT 엔터프라이즈 및 해당 디지털 혁신의 성공에 관건이 되는 플랫폼 기능은 다음과 같습니다.

- 다중 계층 스토리지(웜 및 콜드)
- 수십 년 분량의 시계열 데이터를 저장하는 기능
- 자산 기반의 운영 인텔리전스를 위해 쿼리를 명시적으로 모델링하고 최적화하는 기능

Time Series Insights는 IoT 데이터 탐색 및 운영 인사이트 모두를 위한 포괄적인 엔드투엔드 PaaS(Platform as a Service) 제품입니다. Time Series Insights는 IoT 규모 시계열 데이터를 분석하기 위해 완전 관리형 클라우드 서비스를 제공합니다.

스키마 없는 메모리 내 저장소에 원시 데이터를 저장할 수 있습니다. 그런 다음, 분산 쿼리 엔진 및 API를 통해 대화형 임시 쿼리를 수행할 수 있습니다. 풍부한 사용자 환경을 활용하여 수십억 개의 이벤트를 몇 초 이내에 시각화합니다. [데이터 탐색 기능](./time-series-insights-overview.md)에 대해 자세히 알아보세요.

Time Series Insights는 현재 미리 보기 상태인 운영 인사이트 기능도 제공합니다. 대화형 데이터 탐색 및 운영 인텔리전스와 함께 Time Series Insights를 사용하여 IoT 자산에서 수집된 데이터를 최대한 활용할 수 있습니다. 미리 보기 제품은 다음을 지원합니다.

* 확장성 있고, 비용에 최적화된 고성능 시계열 데이터 저장소. 이 클라우드 기반 IoT 솔루션은 수십 년 분량의 시계열 데이터 추세를 몇 초 이내에 분석할 수 있습니다.
* 자산과 디바이스의 파생 신호 및 비파생 신호와 관련된 도메인과 메타데이터를 설명하는 의미 체계 모델 지원.
* 자산 기반 데이터 인사이트와 풍부한 임시 데이터 분석을 결합하는 향상된 사용자 환경. 이 조합은 비즈니스 및 운영 인텔리전스를 제공합니다.
* 고급 기계 학습 및 분석 도구와 통합. 도구에는 Azure Databricks, Apache Spark, Azure Machine Learning, Jupyter Notebook 및 Power BI가 포함됩니다. 이러한 도구를 통해 시계열 데이터 문제를 해결하고 운영 효율성을 유도할 수 있습니다.

운영 인사이트 및 데이터 탐색은 데이터 처리, 스토리지 및 쿼리에 대한 간단한 종량제 가격 책정 모델로 제공됩니다. 이 청구 모델은 변화하는 비즈니스 요구에 적합합니다.

이 개괄적인 데이터 흐름 다이어그램은 업데이트를 보여 줍니다.

  ![주요 기능][2]

이러한 주요 산업 IoT 기능이 도입되면서, Time Series Insights는 다음과 같은 주요 혜택을 제공합니다.

| | |
| ---| ---|
| IoT 규모 시계열 데이터에 대한 다중 계층 스토리지 | 데이터 수집용 일반 데이터 처리 파이프라인을 사용하여 대화형 쿼리를 위해 데이터를 웜 스토리지에 저장할 수 있습니다. 대량 데이터의 경우 데이터를 콜드 스토리지에 저장할 수도 있습니다. 고성능 자산 기반 [쿼리](./time-series-insights-update-tsq.md)를 활용합니다. |
| 원시 원격 분석을 컨텍스트화하고 자산 기반 인사이트를 제공하는 시계열 모델 | 설명적 [시계열 모델](./time-series-insights-update-tsm.md)을 사용하여 원시 원격 분석 데이터를 컨텍스트화합니다. 비용에 최적화된, 디바이스 기반의 고성능 쿼리를 사용하여 풍부한 운영 인텔리전스를 파생합니다. |
| 다른 데이터 솔루션과의 지속적인 통합 | Time Series Insights의 데이터는 오픈 소스 Apache Parquet 파일에 [저장](./time-series-insights-update-storage-ingress.md)됩니다. 자사 솔루션이든, 타사 솔루션이든 간에 다른 데이터 솔루션과 통합되므로 엔드투엔드 시나리오에서 편리합니다. 이러한 시나리오에는 비즈니스 인텔리전스, 고급 기계 학습, 예측 분석 등이 포함됩니다. |
| 거의 실시간 데이터 검색 | [Azure Time Series Insights Preview 탐색기](./time-series-insights-update-explorer.md) 사용자 환경은 수집 파이프라인을 통한 모든 데이터 스트리밍에 대해 시각화를 제공합니다. 이벤트 원본을 연결하면 즉시 이벤트 데이터를 보고 탐색하고 쿼리할 수 있습니다. 이러한 방식으로, 디바이스에서 예상대로 데이터를 내보내는지 여부를 확인할 수 있습니다. IoT 자산의 상태, 생산성 및 전반적인 효율성을 모니터링할 수도 있습니다. |
| 근본 원인 분석 및 변칙 검색 | [Azure Time Series Insights Preview 탐색기](./time-series-insights-update-explorer.md)는 다단계 근본 원인 분석을 수행하고 저장하기 위한 패턴 및 큐브 뷰를 둘 다 지원합니다. Azure Stream Analytics와 함께 Time Series Insights를 사용하여 경고 및 변칙을 거의 실시간으로 검색할 수 있습니다. |
| Time Series Insights 플랫폼에 빌드된 사용자 지정 애플리케이션 | Time Series Insights는 [JavaScript SDK](./tutorial-explore-js-client-lib.md)를 지원합니다. SDK는 풍부한 컨트롤 및 간소화된 쿼리 액세스를 제공합니다. SDK를 사용하여 특정 비즈니스 요구에 맞는 사용자 지정 IoT 애플리케이션을 Time Series Insights에 빌드할 수 있습니다. Time Series Insights [쿼리 API](./time-series-insights-update-tsq.md)를 직접 사용하여 사용자 지정 IoT 애플리케이션에 데이터를 제공할 수도 있습니다. |

## <a name="next-steps"></a>다음 단계

Azure Time Series Insights 미리 보기 시작:

> [!div class="nextstepaction"]
> [빠른 시작 가이드를 읽기](./time-series-insights-update-quickstart.md)

사용 사례에 대한 자세한 정보:

> [!div class="nextstepaction"]
> [Azure Time Series Insights Preview 사용 사례](./time-series-insights-update-use-cases.md)

<!-- Images -->
[1]: media/v2-update-overview/overview_one.png
[2]: media/v2-update-overview/overview_two.png
