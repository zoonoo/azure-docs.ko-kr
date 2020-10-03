---
title: Gen2 사용 사례-Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Time Series Insights Gen2 사용 사례에 대해 알아봅니다.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: b5b6233c0978f15aad94b7f66f8935c2d6f6f1c1
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2020
ms.locfileid: "91666962"
---
# <a name="azure-time-series-insights-gen2-use-cases"></a>Azure Time Series Insights Gen2 자습서

이 문서에서는 Azure Time Series Insights Gen2의 몇 가지 일반적인 사용 사례를 요약 합니다. 이 문서의 권장 사항은 Azure Time Series Insights Gen2를 사용 하 여 응용 프로그램 및 솔루션을 개발 하기 위한 시작 지점으로 사용 됩니다.

특히이 문서는 다음과 같은 질문에 대답 합니다.

* Azure Time Series Insights Gen2에 대 한 일반적인 사용 사례는 무엇 인가요?
* Azure Time Series Insights Gen2를 사용 하 여 [데이터 탐색 및 시각적 변칙 검색](#data-exploration-and-visual-anomaly-detection)의 이점은 무엇 인가요?
* [운영 분석과 프로세스 효율성](#operational-analysis-and-driving-process-efficiency)을 위해 Azure Time Series Insights Gen2을 사용할 경우의 이점은 무엇 인가요?
* [고급 분석](#advanced-analytics)에 Azure Time Series Insights Gen2를 사용 하면 어떤 이점이 있나요?

이러한 사용 시나리오에 대 한 개요는 다음 섹션에 설명 되어 있습니다.

## <a name="introduction"></a>소개

Azure Time Series Insights Gen2는 종단 간 첨단 서비스 제공입니다. 고도로 컨텍스트화되고 시계열에 최적화된 IoT 규모 데이터를 수집, 처리, 저장, 분석 및 쿼리하는 데 사용됩니다. 임시 데이터 탐색 및 운영 분석에 적합 합니다. Azure Time Series Insights Gen2는 산업 IoT 배포의 광범위 한 요구 사항을 충족 하는 고유 하 고 사용자 지정 된 서비스 제품입니다.

## <a name="data-exploration-and-visual-anomaly-detection"></a>데이터 탐색 및 시각적 변칙 검색

수십억 개의 이벤트를 즉시 살펴보고 분석하여 이상 현상을 포착하고 데이터에서 숨겨진 추세를 발견하세요. Azure Time Series Insights Gen2는 IoT 및 DevOps 분석 워크 로드에 대 한 거의 실시간 성능을 제공 합니다.

[![데이터 탐색기](media/v2-update-use-cases/data-explorer.png)](media/v2-update-use-cases/data-explorer.png#lightbox)

대부분의 고객은 Azure Time Series Insights Gen2의 두드러진 기능 중 하나인 정보를 얻는 데 필요한 최소한의 시간에 동의 합니다.

* Azure Time Series Insights Gen2에는 사전 데이터 준비가 필요 하지 않습니다.
* 몇 분 내에 Azure IoT Hub 또는 Azure Event Hubs 인스턴스에 수십억 개의 이벤트에 연결 하는 데 빠르게 작동 합니다.
* 연결이 설정되면 수십억 개의 이벤트를 시각화하고 분석하여 이상 현상을 포착하고 데이터에서 숨겨진 추세를 발견할 수 있습니다.

Azure Time Series Insights Gen2는 직관적이 고 간단 하 게 사용할 수 있습니다. 코드를 전혀 작성하지 않고도 데이터와 상호 작용할 수 있습니다. Azure Time Series Insights Gen2는 SQL을 잘 알고 있는 고급 사용자를 위한 세밀 한 텍스트 기반 쿼리 언어를 제공 하기는 하지만 학습에 필요한 새로운 언어는 없습니다. 초보 사용자를 위한 선택-클릭 탐색도 제공합니다.

고객은 속도를 활용 하 여 자산 관련 문제를 신속 하 게 진단할 수 있습니다. 이러한 사용자는 DevOps 분석을 수행 하 여 IoT 솔루션에서 버그의 근본 원인을 파악할 수 있습니다. 또한 데이터 과학 이니셔티브의 일부로 추가 조사를 위해 플래그를 지정 하는 영역을 식별할 수 있습니다.

Azure Time Series Insights Gen2에 저장 된 데이터와 상호 작용 하는 세 가지 기본 방법은 다음과 같습니다.

* 시작 하는 가장 쉽고 쉬운 방법은 Azure Time Series Insights Gen2 탐색기를 사용 하는 것입니다. 이 탐색기를 사용하면 한 장소에서 모든 IoT 데이터를 신속하게 시각화할 수 있습니다. 열 지도와 같은 도구를 제공 하 여 데이터의 잘못 된 부분을 발견할 수 있도록 합니다. 큐브 뷰도 제공합니다. 이를 사용 하 여 하나 이상의 Azure Time Series Insights Gen2 환경에서 최대 4 개의 보기를 단일 대시보드로 비교할 수 있습니다. 대시보드는 모든 위치의 시계열 데이터 보기를 제공합니다. [Azure Time Series Insights Gen2 탐색기](./time-series-insights-update-explorer.md)에 대해 자세히 알아보세요. 환경을 계획 하려면 [Gen2 계획 Azure Time Series Insights](./time-series-insights-update-plan.md)를 참조 하세요.

* 두 번째 시작 방법은 JavaScript SDK를 사용 하 여 웹 응용 프로그램에 강력한 차트 및 그래프를 빠르게 포함 하는 것입니다. 코드 몇 줄이면 강력한 쿼리를 작성할 수 있습니다. 이를 사용 하 여 꺾은선형 차트, 원형 차트, 가로 막대형 차트, 열 지도, 데이터 표 등을 채울 수 있습니다. 이 모든 요소는 SDK를 사용하여 기본적으로 존재합니다. SDK는 Azure Time Series Insights Gen2 쿼리 Api를 추상화 하기도 합니다. 이러한 API를 사용하여 대시보드에 표시할 데이터를 쿼리하는 SQL과 유사한 조건자를 작성할 수 있습니다. 하이브리드 프레젠테이션 계층 솔루션의 경우 Azure Time Series Insights Gen2는 매개 변수가 있는 Url을 제공 합니다. 이를 통해 데이터에 대 한 심층 다이브 Azure Time Series Insights Gen2 탐색기를 통해 원활한 연결 지점이 제공 됩니다.

  * JavaScript SDK에 대해 자세히 알아보려면 [JS 클라이언트 라이브러리](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) 및 [샘플 클라이언트](https://github.com/Microsoft/tsiclient) 설명서를 참조 하세요.

  * [Azure Time Series Insights Gen2 탐색기에서 데이터 시각화](time-series-insights-update-explorer.md)를 검토 하 여 url 및 새 UI를 공유 하는 방법에 대해 자세히 알아보세요.

* 세 번째 시작 방법은 강력한 Api를 사용 하 여 Azure Time Series Insights Gen2에 저장 된 데이터를 쿼리 하는 것입니다. Azure Time Series Insights Gen2에는,, 및와 같은 임시 연산자가 있습니다 `from` `to` `first` `last` . 집계 및 변형 (예:,,,,, 등)이 있습니다 `average` `sum` `min` `max` `time-weighted average` `time-weighted sum` . 또한 필터링, 산술 및 부울 연산자, 스칼라 함수 등을 수행할 수 있습니다. 이러한 모든 연산자를 통해 다운스트림 응용 프로그램은 데이터에서 흥미로운 추세 및 패턴을 신속 하 게 찾을 수 있습니다. 이러한 항목을 사용 하 여 기업 시각화를 사용 하 여 변칙을 발견할 수 있습니다.

## <a name="operational-analysis-and-driving-process-efficiency"></a>운영 분석 및 프로세스 효율성 확보

Azure Time Series Insights Gen2를 사용 하 여 대규모 장비의 상태, 사용 및 성능을 모니터링 하 고 운영 효율성을 측정 합니다. Azure Time Series Insights Gen2는 수집 또는 쿼리 성능 저하 없이 다양 하 고 예측할 수 없는 IoT 워크 로드를 관리할 수 있습니다.

[![스크린샷 Azure Time Series Insights Gen2의 장치/응용 프로그램 데이터, 스트림 처리, 운영 효율성, 인텔리전스/통찰력, 고급 분석 등을 보여 줍니다.](media/v2-update-use-cases/overview.png)](media/v2-update-use-cases/overview.png#lightbox)

적절한 기술 또는 솔루션과 결합할 경우 운영 프로세스에서 오는 데이터를 스트리밍하고 지속적으로 처리하여 비즈니스를 성공적으로 변환할 수 있습니다. 종종 이러한 솔루션은 여러 시스템을 조합하여 구성됩니다. 이를 통해 지속적으로 변경 되는 데이터를 탐색 하 고 분석할 수 있습니다. 특히 IoT 영역에서 일반적인 패턴을 공유 합니다.

이러한 패턴은 종종 다양한 로캘의 디바이스 및 센서에서 수십억 개의 이벤트를 수집하는 IoT 지원 플랫폼에서 시작됩니다. 이러한 시스템은 스트리밍 데이터를 처리 및 분석하여 실시간 인사이트와 작업을 끌어냅니다. 데이터는 일반적으로 거의 실시간 및 일괄 분석을 위해 웜 및 콜드 저장소에 보관 됩니다.

수집된 데이터는 일련의 처리 과정을 거쳐 다운스트림 쿼리 및 분석 시나리오에 적합하도록 정리되고 컨텍스트화됩니다. Azure는 자산 유지 관리, 제조 등의 IoT 시나리오에 적용할 수 있는 다양한 서비스를 제공합니다. 이러한 서비스에는 Azure Time Series Insights Gen2, IoT Hub, Event Hubs, Azure Stream Analytics, Azure Functions, Azure Logic Apps, Azure Databricks, Azure Machine Learning, Power BI이 포함 됩니다.

솔루션 아키텍처는 다음과 같은 방식으로 얻을 수 있습니다.

* 동급 최고의 보안, 처리량 및 대기 시간을 제공하도록 IoT Hub 또는 Event Hubs를 통해 데이터를 수집합니다.
* 데이터 처리 및 컴퓨팅을 수행합니다. Stream Analytics, Logic Apps, Azure Functions 같은 서비스를 통해 수집된 데이터를 필터링합니다. 사용하는 서비스는 구체적인 데이터 처리 요구 사항에 따라 달라집니다.
* 처리 파이프라인에서 계산 된 신호는 저장 및 분석을 위해 Azure Time Series Insights Gen2에 푸시됩니다.

Azure Time Series Insights Gen2 기록 데이터에 대해 거의 실시간으로 데이터 탐색 및 자산 기반 정보를 제공 합니다. 비즈니스 요구 사항에 따라 MapReduce 및 Hive 작업은 Azure Time Series Insights Gen2를 Azure HDInsight에 연결 하 여 Azure Time Series Insights Gen2에 저장 된 데이터에서 실행할 수 있습니다. Azure Time Series Insights Gen2에 저장 된 데이터는 Azure Time Series Insights Gen2 공개 surface 쿼리 Api를 통해 Power BI 및 기타 고객 응용 프로그램에서 사용할 수 있습니다. 이 데이터는 심층 비즈니스 및 운영 인텔리전스 시나리오에 사용할 수 있습니다.

## <a name="advanced-analytics"></a>고급 분석

Machine Learning 및 Azure Databricks 같은 고급 분석 서비스와 통합할 수 있습니다. 수백만 대의 장치에서 Gen2 조절기 raw 데이터를 Azure Time Series Insights 합니다. 또한 여러 Azure 분석 서비스에서 원활하게 사용할 수 있는 상황에 맞는 데이터를 추가합니다.

[![분석](media/v2-update-use-cases/advanced-analytics.png)](media/v2-update-use-cases/advanced-analytics.png#lightbox)

고급 분석 및 기계 학습은 대량의 데이터를 사용하고 처리합니다. 이 데이터는 데이터에 근거한 의사 결정을 내리고 예측 분석을 수행하는 데 사용됩니다. IoT 사용 사례에서, 고급 분석 알고리즘은 수백만 개의 디바이스에서 수집된 데이터를 통해 학습합니다. 이러한 디바이스는 1초에 여러 번 데이터를 전송합니다. IoT 디바이스에서 수집된 데이터는 원시 데이터입니다. 디바이스의 위치, 센서 판독값의 단위 같은 컨텍스트 정보가 부족합니다. 따라서 원시 데이터를 고급 분석에 바로 사용하기는 어렵습니다.

Azure Time Series Insights Gen2는 두 가지 간단 하 고 비용 효율적인 방법으로 IoT 데이터와 고급 분석 사이의 차이를 연결 합니다.

* 먼저 Azure Time Series Insights Gen2는 IoT Hub를 사용 하 여 수백만 대의 장치에서 원시 원격 분석 데이터를 수집 합니다. 컨텍스트 정보를 사용하여 데이터를 보강하고 데이터를 parquet 형식으로 변환합니다. 이 형식은 Machine Learning, Azure Databricks 같은 다른 고급 분석 서비스 및 타사 애플리케이션과 쉽게 통합할 수 있습니다.

    Azure Time Series Insights Gen2는 조직의 모든 데이터에 대 한 진위의 원본으로 사용할 수 있습니다. 다운스트림 분석 워크로드에서 사용할 중앙 리포지토리를 만듭니다. Azure Time Series Insights Gen2는 거의 실시간 저장소 서비스 이므로 고급 분석 모델은 들어오는 IoT 원격 분석 데이터에서 지속적으로 학습할 수 있습니다. 결과적으로 모델이 보다 정확하게 예측할 수 있습니다.

* 둘째, 기계 학습 및 예측 모델의 출력을 Azure Time Series Insights Gen2에 공급 하 여 결과를 시각화 하 고 저장할 수 있습니다. 이 프로시저는 조직에서 모델을 최적화하고 조정하는 데 도움이 됩니다. Azure Time Series Insights Gen2를 사용 하면 학습 된 모델 출력과 동일한 평면에서 스트리밍 원격 분석 데이터를 간단 하 게 시각화할 수 있습니다. 이러한 방식으로 데이터 과학 팀이 이상 현상을 찾아내고 패턴을 식별할 수 있도록 도와줍니다.

## <a name="next-steps"></a>다음 단계

* [Azure Time Series Insights Gen2 탐색기](./time-series-insights-update-explorer.md)에 대해 자세히 알아보세요.
* 환경을 계획 하려면 [Azure Time Series Insights Gen2 모범 사례](./time-series-insights-update-plan.md) 를 참조 하세요.
* [샘플 클라이언트](https://github.com/Microsoft/tsiclient) 설명서를 참조 하세요.
