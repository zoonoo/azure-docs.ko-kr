---
title: Azure Time Series Insights 미리 보기 사용 사례 | Microsoft Docs
description: Azure Time Series Insights 미리 보기 사용 사례를 이해합니다.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: a6ee03197a78a75ca57d0ed049cdd6a8df8b2149
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53272969"
---
# <a name="azure-time-series-insights-preview-use-cases"></a>Azure Time Series Insights 미리 보기 사용 사례

이 문서에서는 Azure Time Series Insights 미리 보기의 몇 가지 일반적인 사용 사례를 간략하게 설명합니다. 이 문서의 추천은 Time Series Insights로 애플리케이션 및 솔루션을 개발할 때 시작점 역할을 합니다.

이 문서를 읽은 후에는 다음 질문에 대답할 수 있습니다.

* Time Series Insights의 일반적인 사용 사례는 무엇인가요?
* 데이터 탐색 및 시각적 변칙 검색에 Time Series Insights를 사용하면 어떤 장점이 있나요?
* 운영 분석 및 프로세스 효율성에 Time Series Insights를 사용하면 어떤 장점이 있나요?
* 고급 분석에 Time Series Insights를 사용하면 어떤 장점이 있나요?

이 문서에서는 Azure Time Series Insights 미리 보기를 설계한 목적에 해당하는 사용 사례를 간략하게 설명합니다.

## <a name="introduction"></a>소개

Time Series Insights는 엔드투엔드 PaaS(Platform as a Service) 제품입니다. 고도로 컨텍스트화되고 시계열에 최적화된 IoT 규모 데이터를 수집, 처리, 저장 및 쿼리하는 데 사용됩니다. Time Series Insights는 임시 데이터 탐색 및 운영 분석에 적합합니다. Time Series Insights는 산업 IoT 배포의 광범위한 요구를 충족하는 고유하게 확장 가능하고 사용자 지정된 서비스 제품입니다.

## <a name="data-exploration-and-visual-anomaly-detection"></a>데이터 탐색 및 시각적 변칙 검색

수십억 개의 이벤트를 즉시 살펴보고 분석하여 이상 현상을 포착하고 데이터에서 숨겨진 추세를 발견하세요. Time Series Insights는 IoT 및 DevOps 분석 워크로드에 대한 거의 실시간에 가까운 성능을 제공합니다.

![데이터 탐색기][1]

대부분의 고객은 인사이트 시간이 Time Series Insights의 가장 강력한 자산 중 하나라는 것에 동의합니다. Time Series Insights는 데이터를 미리 준비할 필요가 없습니다. 몇 분 안에 고객을 Azure IoT Hub 또는 Azure Event Hubs의 수십억 개 이벤트에 신속하게 연결합니다. 연결이 설정되면 수십억 개의 이벤트를 시각화하고 분석하여 이상 현상을 포착하고 데이터에서 숨겨진 추세를 발견할 수 있습니다. 

Time Series Insights는 직관적이고 사용 방법이 간단합니다. 코드를 전혀 작성하지 않고도 데이터와 상호 작용할 수 있습니다. 새로운 언어를 배울 필요도 없습니다. Time Series Insights는 SQL에 익숙한 고급 사용자에게 세밀한 텍스트 기반 쿼리를 제공합니다. 초보 사용자를 위한 선택-클릭 탐색도 제공합니다.

고객이 자산 관련 문제를 신속하게 진단할 수 있다는 장점이 있습니다. 고객은 DevOps를 수행하여 IoT 솔루션에 발생하는 버그의 근본 원인을 파악할 수 있습니다. 또한 데이터 과학 이니셔티브를 조사해야 하는 영역을 식별할 수 있습니다.  

Time Series Insights에 저장된 데이터와 상호 작용하는 세 가지 기본적인 방법이 있습니다.

- 가장 쉬운 첫 번째 시작 방법은 Time Series Insights 미리 보기 탐색기입니다. 이 탐색기를 사용하면 한 장소에서 모든 IoT 데이터를 신속하게 시각화할 수 있습니다. 열 지도처럼 데이터에서 이상 현상을 포착할 수 있는 도구를 제공합니다. 큐브 뷰도 제공합니다. 단일 대시보드를 통해 하나 이상의 Time Series Insights 환경에서 최대 4개의 보기를 비교할 수 있습니다. 대시보드는 모든 위치의 시계열 데이터 보기를 제공합니다. [Time Series Insights 미리 보기 탐색기](./time-series-insights-update-explorer.md)에 대해 자세히 알아보세요. Time Series Insights 환경을 계획하려면 [Time Series Insights 계획](./time-series-insights-update-plan.md)을 읽어보세요.

- 시작하는 두 번째 방법은 JavaScript SDK를 사용하여 신속하게 웹 애플리케이션에 강력한 차트와 그래프를 포함하는 것입니다. 코드 몇 줄이면 강력한 쿼리를 작성할 수 있습니다. 꺾은선형 차트, 원형 차트, 가로 막대형 차트, 열 지도, 데이터 표 등을 채우는 데 사용됩니다. 이 모든 요소는 SDK를 사용하여 기본적으로 존재합니다. 또한 SDK는 Time Series Insights 쿼리 API를 추상화합니다. 이러한 API를 사용하여 대시보드에 표시할 데이터를 쿼리하는 SQL과 유사한 조건자를 작성할 수 있습니다. 하이브리드 프레젠테이션 레이어 솔루션의 경우 Time Series Insights는 매개 변수가 있는 URL을 제공합니다. 이러한 URL은 데이터를 심층 분석할 수 있도록 Time Series Insights 미리 보기 탐색기와의 끊김 없는 연결점을 제공합니다. JavaScript SDK에 대한 자세한 내용은 [Time Series Insights JS 클라이언트 라이브러리](https://docs.microsoft.com/azure/time-series-insights/tutorial-explore-js-client-lib) 및 [Time Series Insights 클라이언트](https://github.com/Microsoft/tsiclient) 설명서를 참조하세요. 매개 변수가 있는 URL에 대한 자세한 내용은 [매개 변수가 있는 URL](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-parameterized-urls)을 참조하세요.

- 시작하는 세 번째 방법은 Time Series Insights에 저장된 데이터를 쿼리하는 강력한 API를 사용하는 것입니다. Time Series Insights에는 from, to, first, last 등의 임시 연산자가 있습니다. average, min, max, split by, order by, DateHistogram 등의 집계 및 변환 연산자가 있습니다. has, in and, or, greater than, REGEX 등의 필터링 연산자도 있습니다. 이 모든 연산자는 다운스트림 애플리케이션이 데이터에서 흥미로운 추세 및 패턴을 신속하게 찾을 수 있게 해줍니다. 이러한 연산자를 사용하여 자체 제작 시각화를 채우고 이상 현상을 발견할 수 있습니다.

## <a name="operational-analysis-and-driving-process-efficiency"></a>운영 분석 및 프로세스 효율성 확보

Time Series Insights를 사용하여 대규모 장비의 상태, 사용량 및 성능을 모니터링할 수 있습니다. Time Series Insights는 간편하게 운영 효율성을 측정할 수 있는 방법을 제공합니다. Time Series Insights는 수집 또는 쿼리 성능의 저하 없이 다양하고 예기치 않은 IoT 워크로드를 관리할 수 있습니다.

![개요][2]

적절한 기술 또는 솔루션과 결합할 경우 운영 프로세스에서 오는 데이터를 스트리밍하고 지속적으로 처리하여 비즈니스를 성공적으로 변환할 수 있습니다. 종종 이러한 솔루션은 여러 시스템을 조합하여 구성됩니다. 계속해서 변화하고(특히 IoT 영역에서) 공통 패턴을 공유하는 데이터를 탐색하고 분석할 수 있습니다.

이러한 패턴은 종종 다양한 로캘의 디바이스 및 센서에서 수십억 개의 이벤트를 수집하는 IoT 지원 플랫폼에서 시작됩니다. 이러한 시스템은 스트리밍 데이터를 처리 및 분석하여 실시간 인사이트와 작업을 끌어냅니다. 데이터는 실시간에 가까운 일괄 처리 분석이 가능하도록 일반적으로 웜 및 콜드 스토리지에 보관됩니다. 

수집된 데이터는 일련의 처리 과정을 거쳐 다운스트림 쿼리 및 분석 시나리오에 적합하도록 정리되고 컨텍스트화됩니다. Azure는 자산 유지 관리, 제조 등의 IoT 시나리오에 적용할 수 있는 다양한 서비스를 제공합니다. 이러한 서비스로는 Time Series Insights, IoT Hub, Event Hubs, Azure Stream Analytics, Azure Functions, Azure Logic Apps, Azure Databricks, Azure Machine Learning 및 Power BI가 있습니다.

솔루션 아키텍처는 다음과 같은 방식으로 얻을 수 있습니다.

- 동급 최고의 보안, 처리량 및 대기 시간을 제공하도록 IoT Hub 또는 Event Hubs를 통해 데이터를 수집합니다. 
- 데이터 처리 및 컴퓨팅을 수행합니다. Stream Analytics, Logic Apps, Azure Functions 같은 서비스를 통해 수집된 데이터를 필터링합니다. 사용하는 서비스는 구체적인 데이터 처리 요구 사항에 따라 달라집니다. 
- 처리 파이프라인에서 컴퓨팅된 신호는 저장 및 분석을 위해 Time Series Insights에 푸시됩니다. 

Time Series Insights는 거의 실시간으로 기록 데이터에 대한 데이터 탐색 및 자산 기반 인사이트를 제공합니다. 비즈니스 요구 사항에 따라, Time Series Insights를 Azure HDInsight에 연결하여 Time Series Insights에 저장된 데이터에서 MapReduce 및 Hive 작업을 실행할 수 있습니다. Time Series Insights에 저장된 데이터는 Time Series Insights 공개 화면 쿼리 API를 통해 Power BI 및 기타 고객 애플리케이션에서 사용할 수 있습니다. 이 데이터는 심층 비즈니스 및 운영 인텔리전스 시나리오에 사용할 수 있습니다.

## <a name="advanced-analytics"></a>고급 분석

Machine Learning 및 Azure Databricks 같은 고급 분석 서비스와 통합할 수 있습니다. Time Series Insights는 수백만 대의 디바이스에서 원시 데이터를 수신합니다. 또한 여러 Azure 분석 서비스에서 원활하게 사용할 수 있는 상황에 맞는 데이터를 추가합니다.

![분석][3]

고급 분석 및 기계 학습은 대량의 데이터를 사용하고 처리합니다. 이 데이터는 데이터에 근거한 의사 결정을 내리고 예측 분석을 수행하는 데 사용됩니다. IoT 사용 사례에서, 고급 분석 알고리즘은 수백만 개의 디바이스에서 수집된 데이터를 통해 학습합니다. 이러한 디바이스는 1초에 여러 번 데이터를 전송합니다. IoT 디바이스에서 수집된 데이터는 원시 데이터입니다. 디바이스의 위치, 센서 판독값의 단위 같은 컨텍스트 정보가 부족합니다. 따라서 원시 데이터를 고급 분석에 바로 사용하기는 어렵습니다.

Time Series Insights는 다음과 같은 간단하고 비용 효율적인 방법으로 IoT 데이터와 고급 분석 사이의 간극을 메웁니다. 

- 첫째, Time Series Insights는 IoT Hub를 사용하여 수백만 대의 디바이스에서 원시 원격 분석 데이터를 수집합니다. 컨텍스트 정보를 사용하여 데이터를 보강하고 데이터를 parquet 형식으로 변환합니다. 이 형식은 Machine Learning, Azure Databricks 같은 다른 고급 분석 서비스 및 타사 애플리케이션과 쉽게 통합할 수 있습니다. 

    Time Series Insights는 조직 전체에서 모든 데이터의 믿을 수 있는 소스로 사용할 수 있습니다. 다운스트림 분석 워크로드에서 사용할 중앙 리포지토리를 만듭니다. Time Series Insights는 실시간에 가까운 스토리지 서비스이므로, 고급 분석 모델이 들어오는 IoT 원격 분석 데이터를 통해 지속적으로 학습할 수 있습니다. 결과적으로 모델이 보다 정확하게 예측할 수 있습니다.

- 둘째, Time Series Insights에 기계 학습 및 예측 모델의 출력을 제공하여 결과를 시각화하고 저장할 수 있습니다. 이 프로시저는 조직에서 모델을 최적화하고 조정하는 데 도움이 됩니다. Time Series Insights를 사용하면 스트리밍 원격 분석 데이터를 학습된 모델 출력과 동일한 평면에 간단하게 시각화할 수 있습니다. 이러한 방식으로 데이터 과학 팀이 이상 현상을 찾아내고 패턴을 식별할 수 있도록 도와줍니다.  

## <a name="next-steps"></a>다음 단계

- [Time Series Insights 미리 보기 탐색기](./time-series-insights-update-explorer.md)에 대해 자세히 알아보세요.
- 환경을 계획하려면 [Time Series Insights 미리 보기 계획](./time-series-insights-update-plan.md)을 읽어보세요.
- [Time Series Insights 클라이언트](https://github.com/Microsoft/tsiclient) 설명서를 읽어보세요.

<!-- Images -->
[1]: media/v2-update-use-cases/data-explorer.svg
[2]: media/v2-update-use-cases/overview.svg
[3]: media/v2-update-use-cases/advanced-analytics.svg
