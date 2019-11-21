---
title: Preview use cases - Azure Time Series Insights | Microsoft Docs
description: Learn about Azure Time Series Insights Preview use cases.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/19/2019
ms.custom: seodec18
ms.openlocfilehash: 92d738542076b755a26e8cff2e7fb1aa0384cb22
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227742"
---
# <a name="azure-time-series-insights-preview-use-cases"></a>Azure Time Series Insights 미리 보기 사용 사례

This article summarizes several common use cases for Azure Time Series Insights Preview. The recommendations in this article serve as a starting point to develop your applications and solutions with Time Series Insights.

Specifically, this article answers the following questions:

* Time Series Insights의 일반적인 사용 사례는 무엇인가요?
* What are the benefits of using Time Series Insights for [data exploration and visual anomaly detection](#data-exploration-and-visual-anomaly-detection)?
* What are the benefits of using Time Series Insights for [operational analysis and process efficiency](#operational-analysis-and-driving-process-efficiency)?
* What are the benefits of using Time Series Insights for [advanced analytics](#advanced-analytics)?

An overview of these use scenarios is described in the following sections.

## <a name="introduction"></a>소개

Azure Time Series Insights is an end-to-end, platform-as-a-service offering. 고도로 컨텍스트화되고 시계열에 최적화된 IoT 규모 데이터를 수집, 처리, 저장, 분석 및 쿼리하는 데 사용됩니다. Time Series Insights는 임시 데이터 탐색 및 운영 분석에 적합합니다. Time Series Insights is a uniquely extensible, customized service offering that meets the broad needs of industrial IoT deployments.

## <a name="data-exploration-and-visual-anomaly-detection"></a>데이터 검색 및 시각적 변칙 검색

수십억 개의 이벤트를 즉시 검색 및 분석하여 이상 현상을 포착하고 데이터에서 숨겨진 추세를 발견하세요. Time Series Insights는 IoT 및 DevOps 분석 워크로드의 거의 실시간 성능을 제공합니다.

[![Data explorer](media/v2-update-use-cases/data-explorer.png)](media/v2-update-use-cases/data-explorer.png#lightbox)

Most customers agree that the minimal amount of time required to gain insight is one of the standout features of Time Series Insights:

* Time Series Insights는 데이터를 미리 준비할 필요가 없습니다. 
* It works fast to connect you to billions of events in your Azure IoT Hub or Azure Event Hubs instances in minutes. 
* 연결이 설정되면 수십억 개의 이벤트를 시각화하고 분석하여 이상 현상을 포착하고 데이터에서 숨겨진 추세를 발견할 수 있습니다.

Time Series Insights는 직관적이고 사용 방법이 간단합니다. 코드를 전혀 작성하지 않고도 데이터와 상호 작용할 수 있습니다. There’s also no new language you're required to learn, although Time Series Insights provides a granular text-based querying language for advanced users who are familiar with SQL. 초보 사용자를 위한 선택-클릭 탐색도 제공합니다.

Customers can take advantage of the speed to diagnose asset-related issues quickly. They can perform DevOps analysis to get to the root cause of a bug in an IoT solution. They also can identify areas to flag for further investigation as part of their data science initiatives. 

Time Series Insights에 저장된 데이터와 상호 작용하는 세 가지 기본적인 방법이 있습니다.

* 가장 쉬운 첫 번째 시작 방법은 Time Series Insights 미리 보기 탐색기입니다. 이 탐색기를 사용하면 한 장소에서 모든 IoT 데이터를 신속하게 시각화할 수 있습니다. It provides tools like the heat map to help you spot anomalies in your data. 큐브 뷰도 제공합니다. 단일 대시보드를 통해 하나 이상의 Time Series Insights 환경에서 최대 4개의 보기를 비교할 수 있습니다. 대시보드는 모든 위치의 시계열 데이터 보기를 제공합니다. [Time Series Insights 미리 보기 탐색기](./time-series-insights-update-explorer.md)에 대해 자세히 알아보세요. Time Series Insights 환경을 계획하려면 [Time Series Insights 계획](./time-series-insights-update-plan.md)을 읽어보세요.

* The second way to start is to use the JavaScript SDK to quickly embed powerful charts and graphs in your web application. 코드 몇 줄이면 강력한 쿼리를 작성할 수 있습니다. Use them to populate line charts, pie charts, bar charts, heat maps, data grids, and more. 이 모든 요소는 SDK를 사용하여 기본적으로 존재합니다. 또한 SDK는 Time Series Insights 쿼리 API를 추상화합니다. 이러한 API를 사용하여 대시보드에 표시할 데이터를 쿼리하는 SQL과 유사한 조건자를 작성할 수 있습니다. 하이브리드 프레젠테이션 레이어 솔루션의 경우 Time Series Insights는 매개 변수가 있는 URL을 제공합니다. 이러한 URL은 데이터를 심층 분석할 수 있도록 Time Series Insights 미리 보기 탐색기와의 끊김 없는 연결점을 제공합니다.

  * Read about the [Time Series Insights JS client library](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) and the [Time Series Insights client](https://github.com/Microsoft/tsiclient) documentation to learn more about the JavaScript SDK.

  * Learn more about sharing URLs and the new UI by reviewing [Visualize data in the Azure Time Series Insights Preview explorer](time-series-insights-update-explorer.md).

* 시작하는 세 번째 방법은 Time Series Insights에 저장된 데이터를 쿼리하는 강력한 API를 사용하는 것입니다. Time Series Insights has temporal operators such as `from`, `to`, `first`, and `last`. It has aggregations and transformations such as `average`, `min`, `max`, `split by`, `order by`, and `DateHistogram`. It also has filtering operators such as `has`, `in`, `and`, `or`, `greater than`, and `REGEX`. 이 모든 연산자는 다운스트림 애플리케이션이 데이터에서 흥미로운 추세 및 패턴을 신속하게 찾을 수 있게 해줍니다. Use them to populate homegrown visualizations to spot anomalies.

## <a name="operational-analysis-and-driving-process-efficiency"></a>운영 분석 및 프로세스 효율성 확보

Time Series Insights를 사용하여 대규모 장비의 상태, 사용량 및 성능을 모니터링할 수 있습니다. Time Series Insights는 간편하게 운영 효율성을 측정할 수 있는 방법을 제공합니다. Time Series Insights에서 다양하고 예기치 않은 IoT 워크로드를 관리하면서도 수집 또는 쿼리 성능을 저하시키지 않을 수 있습니다.

[![개요](media/v2-update-use-cases/overview.png)](media/v2-update-use-cases/overview.png#lightbox)

적절한 기술 또는 솔루션과 결합할 경우 운영 프로세스에서 오는 데이터를 스트리밍하고 지속적으로 처리하여 비즈니스를 성공적으로 변환할 수 있습니다. 종종 이러한 솔루션은 여러 시스템을 조합하여 구성됩니다. They enable exploration and analysis of data that changes constantly, especially in the IoT realm, and share a common pattern.

이러한 패턴은 종종 다양한 로캘의 디바이스 및 센서에서 수십억 개의 이벤트를 수집하는 IoT 지원 플랫폼에서 시작됩니다. 이러한 시스템은 스트리밍 데이터를 처리 및 분석하여 실시간 인사이트와 작업을 끌어냅니다. Data is typically archived to warm and cold store for near real-time and batch analytics.

수집된 데이터는 일련의 처리 과정을 거쳐 다운스트림 쿼리 및 분석 시나리오에 적합하도록 정리되고 컨텍스트화됩니다. Azure는 자산 유지 관리, 제조 등의 IoT 시나리오에 적용할 수 있는 다양한 서비스를 제공합니다. 이러한 서비스로는 Time Series Insights, IoT Hub, Event Hubs, Azure Stream Analytics, Azure Functions, Azure Logic Apps, Azure Databricks, Azure Machine Learning 및 Power BI가 있습니다.

솔루션 아키텍처는 다음과 같은 방식으로 얻을 수 있습니다.

* 동급 최고의 보안, 처리량 및 대기 시간을 제공하도록 IoT Hub 또는 Event Hubs를 통해 데이터를 수집합니다.
* 데이터 처리 및 컴퓨팅을 수행합니다. Stream Analytics, Logic Apps, Azure Functions 같은 서비스를 통해 수집된 데이터를 필터링합니다. 사용하는 서비스는 구체적인 데이터 처리 요구 사항에 따라 달라집니다.
* 처리 파이프라인에서 컴퓨팅된 신호는 저장 및 분석을 위해 Time Series Insights에 푸시됩니다.

Time Series Insights는 거의 실시간으로 기록 데이터에 대한 데이터 탐색 및 자산 기반 인사이트를 제공합니다. 비즈니스 요구 사항에 따라, Time Series Insights를 Azure HDInsight에 연결하여 Time Series Insights에 저장된 데이터에서 MapReduce 및 Hive 작업을 실행할 수 있습니다. Time Series Insights에 저장된 데이터는 Time Series Insights 공개 화면 쿼리 API를 통해 Power BI 및 기타 고객 애플리케이션에서 사용할 수 있습니다. 이 데이터는 심층 비즈니스 및 운영 인텔리전스 시나리오에 사용할 수 있습니다.

## <a name="advanced-analytics"></a>고성능 분석

Machine Learning 및 Azure Databricks 같은 고급 분석 서비스와 통합할 수 있습니다. Time Series Insights는 수백만 대의 디바이스에서 원시 데이터를 수신합니다. 또한 여러 Azure 분석 서비스에서 원활하게 사용할 수 있는 상황에 맞는 데이터를 추가합니다.

[![Analytics](media/v2-update-use-cases/advanced-analytics.png)](media/v2-update-use-cases/advanced-analytics.png#lightbox)

고급 분석 및 기계 학습은 대량의 데이터를 사용하고 처리합니다. 이 데이터는 데이터에 근거한 의사 결정을 내리고 예측 분석을 수행하는 데 사용됩니다. IoT 사용 사례에서, 고급 분석 알고리즘은 수백만 개의 디바이스에서 수집된 데이터를 통해 학습합니다. 이러한 디바이스는 1초에 여러 번 데이터를 전송합니다. IoT 디바이스에서 수집된 데이터는 원시 데이터입니다. 디바이스의 위치, 센서 판독값의 단위 같은 컨텍스트 정보가 부족합니다. 따라서 원시 데이터를 고급 분석에 바로 사용하기는 어렵습니다.

Time Series Insights는 다음과 같은 간단하고 비용 효율적인 방법으로 IoT 데이터와 고급 분석 사이의 간극을 메웁니다.

* 첫째, Time Series Insights는 IoT Hub를 사용하여 수백만 대의 디바이스에서 원시 원격 분석 데이터를 수집합니다. 컨텍스트 정보를 사용하여 데이터를 보강하고 데이터를 parquet 형식으로 변환합니다. 이 형식은 Machine Learning, Azure Databricks 같은 다른 고급 분석 서비스 및 타사 애플리케이션과 쉽게 통합할 수 있습니다.

    Time Series Insights는 조직 전체에서 모든 데이터의 믿을 수 있는 소스로 사용할 수 있습니다. 다운스트림 분석 워크로드에서 사용할 중앙 리포지토리를 만듭니다. Time Series Insights는 실시간에 가까운 스토리지 서비스이므로, 고급 분석 모델이 들어오는 IoT 원격 분석 데이터를 통해 지속적으로 학습할 수 있습니다. 결과적으로 모델이 보다 정확하게 예측할 수 있습니다.

* Second, the output of machine learning and prediction models can be fed into Time Series Insights to visualize and store their results. 이 프로시저는 조직에서 모델을 최적화하고 조정하는 데 도움이 됩니다. Time Series Insights를 사용하면 스트리밍 원격 분석 데이터를 학습된 모델 출력과 동일한 평면에 간단하게 시각화할 수 있습니다. 이러한 방식으로 데이터 과학 팀이 이상 현상을 찾아내고 패턴을 식별할 수 있도록 도와줍니다.

## <a name="next-steps"></a>다음 단계

* [Time Series Insights 미리 보기 탐색기](./time-series-insights-update-explorer.md)에 대해 자세히 알아보세요.
* Read [Time Series Insights Preview planning](./time-series-insights-update-plan.md) to plan out your environment.
* [Time Series Insights 클라이언트](https://github.com/Microsoft/tsiclient) 설명서를 읽어보세요.
