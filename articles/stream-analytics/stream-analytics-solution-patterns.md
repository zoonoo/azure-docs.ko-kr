---
title: Azure Stream Analytics 솔루션 패턴
description: 대시보드, 이벤트 메시징, 데이터 저장소, 참조 데이터 보강 및 모니터링과 같은 Azure Stream Analytics의 일반적인 솔루션 패턴에 대해 알아봅니다.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 1bd3c1099344bd266d7e3bc153613daaecfb412a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020318"
---
# <a name="azure-stream-analytics-solution-patterns"></a>Azure Stream Analytics 솔루션 패턴

Azure의 다른 많은 서비스와 마찬가지로 Stream Analytics는 다른 서비스와 함께 사용하면서 큰 엔드 투 엔드 솔루션을 만들 때 가장 적합합니다. 이 문서에서는 간단한 Azure Stream Analytics 솔루션 및 다양한 아키텍처 패턴에 대해 설명합니다. 이러한 패턴 위에 구축하여 더 복잡한 솔루션을 개발할 수 있습니다. 이 문서에서 설명하는 패턴은 다양한 시나리오에서 사용할 수 있습니다. 시나리오별 패턴 예제는 [Azure 솔루션 아키텍처](https://azure.microsoft.com/solutions/architecture/?product=stream-analytics)에서 사용할 수 있습니다.

## <a name="create-a-stream-analytics-job-to-power-real-time-dashboarding-experience"></a>실시간 대시보드 환경을 구동하는 Stream Analytics 작업 만들기

Azure Stream Analytics를 사용하면 실시간 대시보드 및 경고를 신속하게 생성할 수 있습니다. 간단한 솔루션이 Event Hubs 또는 IoT Hub에서 이벤트를 수집하며 [스트리밍 데이터 세트를 Power BI 대시보드에 공급](/power-bi/service-real-time-streaming)합니다. 자세한 내용은 상세한 자습서인 [Stream Analytics를 사용하여 사기 전화 데이터를 분석하고 Power BI 대시보드에 결과 시각화](stream-analytics-real-time-fraud-detection.md)를 참조하세요.

![ASA Power BI 대시보드](media/stream-analytics-solution-patterns/power-bi-dashboard.png)

이 솔루션은 Azure Portal에서 몇 분 안에 빌드할 수 있습니다. 포괄적인 코딩은 필요하지 않으며 SQL 언어를 사용하여 비즈니스 논리를 표현합니다.

이 솔루션 패턴은 이벤트 원본에서 브라우저의 Power BI 대시보드까지 이동하는 데 가장 짧은 대기 시간을 제공합니다. Azure Stream Analytics는 이 기본 제공 기능이 있는 유일한 Azure 서비스입니다.

## <a name="use-sql-for-dashboard"></a>대시보드에 SQL 사용

Power BI 대시보드는 짧은 대기 시간을 제공하지만 완전한 Power BI 보고서를 생성하는 데 사용할 수는 없습니다. 일반적인 보고 패턴은 먼저 데이터를 SQL Database로 출력하는 것입니다. 그런 다음, Power BI SQL 커넥터를 사용하여 SQL에서 최신 데이터를 쿼리합니다.

![ASA SQL 대시보드](media/stream-analytics-solution-patterns/sql-dashboard.png)

SQL Database를 사용하면 대기 시간은 약간 더 늘어나지만 훨씬 더 유연하게 작업할 수 있습니다. 이 솔루션은 대기 시간 요구 사항이 1초보다 긴 작업에 적합합니다. 이 방법을 사용하면 Power BI 기능을 최대화하여 보고서의 데이터를 추가로 조각화 및 분석하고 훨씬 더 많은 시각화 옵션을 사용할 수 있습니다. 또한 Tableau 등의 다른 대시보드 솔루션을 유연하게 사용할 수 있습니다.

SQL은 처리량이 높은 데이터 저장소가 아닙니다. Azure Stream Analytics에서 SQL Database로의 최대 처리량은 현재 초당 약 24MB입니다. 솔루션의 이벤트 원본에서 더 높은 속도로 데이터를 생성하는 경우 Stream Analytics의 처리 논리를 사용하여 SQL에 대한 출력 속도를 줄여야 합니다. 필터링, 기간이 지정된 집계, 일시적 조인을 사용한 패턴 일치, 분석 함수 등의 기법을 사용할 수 있습니다. SQL에 대한 출력 속도는 [Azure SQL Database에 대한 Azure Stream Analytics 출력](stream-analytics-sql-output-perf.md)에 설명된 기술을 사용하여 추가로 최적화할 수 있습니다.

## <a name="incorporate-real-time-insights-into-your-application-with-event-messaging"></a>이벤트 메시징을 사용하여 실시간 인사이트를 애플리케이션에 통합

두 번째로 널리 사용되는 Stream Analytics는 실시간 경고를 생성할 때입니다. 이 솔루션 패턴에서는 Stream Analytics의 비즈니스 논리를 사용하여 [임시 및 공간 패턴](stream-analytics-geospatial-functions.md)이나 [변칙](stream-analytics-machine-learning-anomaly-detection.md)을 검색한 다음, 경고 신호를 생성할 수 있습니다. 그러나 Stream Analytics에서 Power BI를 기본 엔드포인트로 사용하는 대시보드 솔루션과 달리, 많은 중간 데이터 싱크를 사용할 수 있습니다. 이러한 싱크에는 Event Hubs, Service Bus 및 Azure Functions가 포함됩니다. 애플리케이션 빌더는 시나리오에 가장 적합한 데이터 싱크를 결정해야 합니다.

기존 비즈니스 워크플로에서 경고를 생성하려면 다운스트림 이벤트 소비자 논리를 구현해야 합니다. Azure Functions는 사용자 지정 논리를 구현할 수 있으므로 이 통합을 수행할 수 있는 가장 빠른 방법이라고 할 수 있습니다. Azure Functions를 Stream Analytics 작업에 대한 출력으로 사용하는 방법에 대한 자습서는 [Azure Stream Analytics 작업에서 Azure Functions 실행](stream-analytics-with-azure-functions.md)에서 찾을 수 있습니다. Azure Functions는 문자 및 메일을 비롯한 다양한 유형의 알림도 지원합니다. 이러한 통합을 위해 Stream Analytics와 논리 앱 간의 Event Hubs와 함께 논리 앱을 사용할 수 있습니다.

![ASA 이벤트 메시징 앱](media/stream-analytics-solution-patterns/event-messaging-app.png)

다른 한편으로, Event Hubs는 가장 유연한 통합 지점을 제공합니다. Azure Data Explorer 및 Time Series Insights와 같은 다른 많은 서비스는 Event Hubs의 이벤트를 사용할 수 있습니다. 이 솔루션을 완료하기 위해 서비스를 Azure Stream Analytics에서 Event Hubs 싱크에 직접 연결할 수 있습니다. Event Hubs는 이러한 통합 시나리오에서 Azure에서 사용할 수 있는 가장 높은 처리량 메시징 브로커이기도 합니다.

## <a name="dynamic-applications-and-websites"></a>동적 애플리케이션 및 웹 사이트

Azure Stream Analytics 및 Azure SignalR Service를 사용하여 대시보드 또는 지도 시각화와 같은 사용자 지정 실시간 시각화를 만들 수 있습니다. SignalR을 사용하여 웹 클라이언트를 업데이트하고 동적 콘텐츠를 실시간으로 표시할 수 있습니다.

![ASA 동적 앱](media/stream-analytics-solution-patterns/dynamic-app.png)

## <a name="incorporate-real-time-insights-into-your-application-through-data-stores"></a>데이터 저장소를 통해 애플리케이션에 실시간 인사이트 통합

현재 대부분의 웹 서비스 및 웹 애플리케이션은 요청/응답 패턴을 사용하여 프레젠테이션 계층을 제공합니다. 요청/응답 패턴은 간단하게 빌드할 수 있으며, 상태 비저장 프런트 엔드 및 스케일링 가능한 저장소(예: Cosmos DB)를 사용하여 응답 시간을 낮추면서 쉽게 스케일링할 수 있습니다.

데이터 볼륨이 많을 경우 CRUD 기반 시스템에서 성능 병목 현상이 자주 발생합니다. 성능 병목 현상을 해결하는 데 [이벤트 소싱 솔루션 패턴](/azure/architecture/patterns/event-sourcing)이 사용됩니다. 임시 패턴 및 인사이트를 기존 데이터 저장소에서 추출하는 것 또한 어렵고 비효율적입니다. 최신 대용량 데이터 기반 애플리케이션은 데이터 흐름 기반 아키텍처를 채택하는 경우가 많습니다. 동작 중인 데이터에 대한 컴퓨팅 엔진으로 사용되는 Azure Stream Analytics는 해당 아키텍처에서 핵심적인 요소입니다.

![ASA 이벤트 소싱 앱](media/stream-analytics-solution-patterns/event-sourcing-app.png)

이 솔루션 패턴에서 이벤트는 Azure Stream Analytics에서 처리되고 데이터 저장소로 집계됩니다. 애플리케이션 계층은 기존의 요청/응답 패턴을 사용하여 데이터 저장소와 상호 작용합니다. 많은 수의 이벤트를 실시간으로 처리하는 Stream Analytics의 능력 때문에, 애플리케이션은 데이터 저장소 레이어를 늘리지 않아도 매우 뛰어난 스케일링 역량을 갖게 됩니다. 데이터 저장소 레이어는 기본적으로 시스템에서 구체화된 뷰입니다. [Azure Cosmos DB에 대한 Azure Stream Analytics 출력](stream-analytics-documentdb-output.md)에서는 Cosmos DB를 Stream Analytics 출력으로 사용하는 방식을 설명합니다.

처리 논리가 복잡하고 논리의 특정 부분을 독립적으로 업그레이드해야 하는 실제 애플리케이션에서는 여러 Stream Analytics 작업을 Event Hubs와 함께 중간 이벤트 브로커로 구성할 수 있습니다.

![ASA 복합 이벤트 소싱 앱](media/stream-analytics-solution-patterns/event-sourcing-app-complex.png)

이 패턴은 시스템의 복원력 및 관리 효율성을 향상시킵니다. 그러나 Stream Analytics는 정확히 한 번의 처리를 보장하지만 중복 이벤트가 중간 Event Hubs에 도달할 수 있습니다. 다운스트림 Stream Analytics 작업이 되돌아보기 기간에 논리 키를 사용하여 이벤트의 중복을 제거하는 것이 중요합니다. 이벤트 배달에 대한 자세한 내용은 [이벤트 배달 보장](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) 참조서를 참조하세요.

## <a name="use-reference-data-for-application-customization"></a>애플리케이션 사용자 지정에 참조 데이터 사용

Azure Stream Analytics 참조 데이터 기능은 경고 임계값, 처리 규칙 및 [지오펜스](geospatial-scenarios.md)와 같은 최종 사용자의 사용자 지정을 위해 특별히 설계되었습니다. 애플리케이션 계층은 매개 변수 변경 내용을 수락하고 SQL Database에 저장할 수 있습니다. Stream Analytics 작업은 정기적으로 데이터베이스의 변경 내용을 쿼리하고 참조 데이터 조인을 통해 사용자 지정 매개 변수에 액세스할 수 있도록 합니다. 애플리케이션 사용자 지정에 대한 참조 데이터를 사용하는 방법에 대한 자세한 내용은 [SQL 참조 데이터](sql-reference-data.md) 및 [참조 데이터 조인](/stream-analytics-query/reference-data-join-azure-stream-analytics)을 참조하세요.

이 패턴을 사용하여 규칙의 임계값이 참조 데이터에서 정의되는 규칙 엔진을 구현할 수도 있습니다. 규칙에 대한 자세한 내용은 [Azure Stream Analytics에서 구성 가능한 임계값 기반 규칙 처리](stream-analytics-threshold-based-rules.md)를 참조하세요.

![ASA 참조 데이터 앱](media/stream-analytics-solution-patterns/reference-data-app.png)

## <a name="add-machine-learning-to-your-real-time-insights"></a>실시간 인사이트에 Machine Learning 추가

Azure Stream Analytics 기본 제공 [변칙 검색 모델](stream-analytics-machine-learning-anomaly-detection.md)은 실시간 애플리케이션에 Machine Learning 소개하는 편리한 방법입니다. 더 광범위한 Machine Learning 요구 사항에 대해서는 [Azure Stream Analytics와 Azure Machine Learning 점수 매기기 서비스 통합](stream-analytics-machine-learning-integration-tutorial.md)을 참조하세요.

온라인 학습 및 점수 매기기를 동일한 Stream Analytics 파이프라인에 통합하려는 고급 사용자의 경우 [선형 회귀](stream-analytics-high-frequency-trading.md)를 사용하여 이 작업을 수행하는 방법의 예제를 참조하세요.

![ASA Machine Learning 앱](media/stream-analytics-solution-patterns/machine-learning-app.png)

## <a name="real-time-data-warehousing"></a>실시간 데이터 웨어하우징

또 다른 일반적인 패턴은 스트리밍 데이터 웨어하우스라고도 하는 실시간 데이터 웨어하우징입니다. 애플리케이션에서 Event Hubs 및 IoT Hub에 도달한 이벤트 외에도 [IoT Edge에서 실행되는 Azure Stream Analytics](stream-analytics-edge.md)를 사용하여 데이터 정리, 데이터 중복 제거, 데이터 저장 및 전달 요구 사항을 충족할 수 있습니다. IoT Edge에서 실행되는 Stream Analytics는 시스템의 대역폭 제한 및 연결 문제를 정상적으로 처리할 수 있습니다. Stream Analytics는 Azure Synapse Analytics에 쓰는 동안 초당 최대 200MB의 처리량 속도를 지원할 수 있습니다.

![ASA 데이터 웨어하우징](media/stream-analytics-solution-patterns/data-warehousing.png)


## <a name="archiving-real-time-data-for-analytics"></a>분석을 위한 실시간 데이터 보관

대부분의 데이터 과학 및 분석 활동은 여전히 오프라인에서 발생합니다. Azure Data Lake Store Gen2 출력 및 Parquet 출력 형식을 통해 Azure Stream Analytics에서 데이터를 보관할 수 있습니다. 이 기능은 데이터를 Azure Data Lake Analytics, Azure Databricks 및 Azure HDInsight에 직접 공급하기 위해 충돌 요인을 제거합니다. Azure Stream Analytics는 이 솔루션에서 실시간에 가까운 ETL 엔진으로 사용됩니다. 다양한 컴퓨팅 엔진을 사용하여 Data Lake에서 보관된 데이터를 탐색할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![ASA 오프라인 분석](media/stream-analytics-solution-patterns/offline-analytics.png)

## <a name="use-reference-data-for-enrichment"></a>보강을 위해 참조 데이터 사용

데이터 보강이 ETL 엔진에 대한 요구 사항인 경우가 많습니다. Azure Stream Analytics는 SQL Database 및 Azure Blob Storage의 [참조 데이터](stream-analytics-use-reference-data.md)를 통해 데이터 보강을 지원합니다. Azure Data Lake 및 Azure Synapse Analytics 둘 다에 데이터가 도달하도록 하기 위해 데이터 보강을 수행할 수 있습니다.


![데이터 보강을 사용한 ASA 오프라인 분석](media/stream-analytics-solution-patterns/offline-analytics-enriched.png)

## <a name="operationalize-insights-from-archived-data"></a>보관된 데이터에서 인사이트 운용

오프라인 분석 패턴과 실시간에 가까운 애플리케이션 패턴을 결합하는 경우 피드백 루프를 만들 수 있습니다. 피드백 루프를 통해 애플리케이션은 데이터의 패턴을 변경하기 위해 자동으로 조정할 수 있습니다. 이 피드백 루프는 경고에 대한 임계값을 변경하는 것처럼 간단할 수도 있고, Machine Learning 모델을 다시 학습하는 것처럼 복잡할 수도 있습니다. 동일한 솔루션 아키텍처를 클라우드 및 IoT Edge에서 실행되는 ASA 작업 모두에 적용할 수 있습니다.

![ASA 인사이트 운용](media/stream-analytics-solution-patterns/insights-operationalization.png)

## <a name="how-to-monitor-asa-jobs"></a>ASA 작업 모니터링 방법

Azure Stream Analytics 작업은 들어오는 이벤트를 실시간으로 계속 처리할 수 있도록 연중무휴로 실행될 수 있습니다. 작동 시간 보장은 전체 애플리케이션의 상태에 매우 중요합니다. Stream Analytics는 [99.9%의 가용성 보장](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/)을 제공하는 업계에서 유일한 스트리밍 분석 서비스이지만 여전히 약간의 가동 중단 시간을 발생시킬 수 있습니다. 수년에 걸쳐 Stream Analytics는 작업 상태를 반영하는 메트릭, 로그 및 작업 상태를 도입했습니다. 이러한 모든 항목은 Azure Monitor 서비스를 통해 표시되며 OMS로 추가로 내보낼 수 있습니다. 자세한 내용은 [Stream Analytics 작업 모니터링 및 쿼리를 모니터링하는 방법 이해](stream-analytics-monitoring.md)를 참조하세요.

![ASA 모니터링](media/stream-analytics-solution-patterns/monitoring.png)

모니터링해야 하는 두 가지 주요 항목은 다음과 같습니다.

- [작업 실패 상태](job-states.md)

    우선적으로 작업이 실행 중인지 확인해야 합니다. 작업이 실행 중 상태가 아니면 새 메트릭 또는 로그가 생성되지 않습니다. 작업은 높은 SU 사용률 수준을 포함하는 다양한 이유(즉, 리소스 부족)로 인해 실패 상태로 변경될 수 있습니다.

- [워터마크 지연 메트릭](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

    이 메트릭은 처리 파이프라인이 벽시계 시간(초)보다 얼마나 뒤처져 있는지를 반영합니다. 일부 지연은 내재된 처리 논리 때문입니다. 따라서 증가하는 추세를 모니터링하는 것이 절대값을 모니터링하는 것보다 훨씬 더 중요합니다. 안정 상태 지연은 모니터링 또는 경고가 아니라 애플리케이션 디자인을 통해 해결해야 합니다.

실패 시 활동 로그 및 [진단 로그](stream-analytics-job-diagnostic-logs.md)는 오류 검색을 시작하기에 가장 좋은 위치입니다.

## <a name="build-resilient-and-mission-critical-applications"></a>복원력 있는 중요 비즈니스용 애플리케이션 빌드

Azure Stream Analytics의 SLA 보장 여부에 관계없이 엔드투엔드 애플리케이션을 아무리 신중하게 실행해도 중단은 발생하게 됩니다. 애플리케이션이 중요 비즈니스용인 경우 정상적으로 복구하려면 중단에 대비해야 합니다.

경고 애플리케이션의 경우 가장 중요한 것은 다음 경고를 검색하는 것입니다. 복구할 때 현재 시간부터 작업을 다시 시작하여 과거 경고를 무시하도록 선택할 수 있습니다. 작업 시작 시간 의미 체계는 첫 번째 입력 시간이 아닌 첫 번째 출력 시간을 따릅니다. 입력은 지정된 시간에 첫 번째 출력이 완료되고 올바르도록 하기 위해 적절한 시간만큼 뒤로 되감기됩니다. 결과적으로 부분적인 집계를 얻거나 예기치 않게 경고를 트리거하는 일이 없어집니다.

또한 과거의 특정 기간부터 출력을 시작하도록 선택할 수도 있습니다. Event Hubs 및 IoT Hub 보존 정책은 이전 데이터 처리를 허용하기 위해 적절한 양의 데이터를 보유합니다. 관건은 얼마나 빠르게 현재 시간을 따라가면서 적시에 새 경고 생성을 시작할 수 있는가입니다. 시간이 지남에 따라 데이터의 가치가 빠르게 하락하므로 현재 시간을 빠르게 따라가는 것이 중요합니다. 다음과 같은 두 가지 방법으로 현재 시간을 빠르게 따라갈 수 있습니다.

- 따라갈 때 더 많은 리소스(SU)를 프로비저닝합니다.
- 현재 시간부터 다시 시작합니다.

현재 시간부터 다시 시작하는 것은 간단하지만 처리 중간에 간격을 발생한다는 문제가 있습니다. 이러한 방식으로 다시 시작하는 것은 경고 시나리오에서는 문제가 없지만 대시보드 시나리오에는 문제가 될 수 있으며, 보관 및 데이터 웨어하우징 시나리오의 경우에는 적절하지 않습니다.

리소스를 추가로 프로비저닝하면 프로세스의 속도가 높아질 수 있지만 처리 속도가 급증할 경우 복잡한 결과가 나타날 수 있습니다.

- 더 많은 수의 SU로 작업을 스케일링할 수 있는지 테스트합니다. 모든 쿼리를 스케일링할 수 있는 것은 아닙니다. 쿼리가 [병렬 처리되는지](stream-analytics-parallelization.md) 확인해야 합니다.

- 업스트림 Event Hubs 또는 IoT Hub에 입력 처리량을 스케일링하기 위한 더 많은 TU(처리량 단위)를 추가할 수 있는 충분한 파티션이 있는지 확인합니다. 각 Event Hubs TU는 초당 2MB의 출력 속도에서 최대치를 나타냅니다.

- 출력 싱크(예: SQL Database, Cosmos DB)에 충분한 리소스를 프로비저닝하여 때때로 시스템 잠금을 유발할 수 있는 출력 급증을 제한하지 않아도 되도록 합니다.

가장 중요한 것은 처리 비율 변경을 예상하고, 프로덕션으로 전환하기 전에 이러한 시나리오를 테스트하고, 오류 복구 시간 동안 처리를 올바르게 스케일링할 수 있도록 준비하는 것입니다.

들어오는 이벤트가 모두 지연되는 극단적인 시나리오에서 작업에 지연 도착 기간을 적용한 경우 [지연된 이벤트가 모두 삭제될 수 있습니다](stream-analytics-time-handling.md). 이벤트의 삭제는 처음에는 알 수 없는 동작처럼 보일 수 있지만 Stream Analytics를 실시간 처리 엔진으로 간주할 경우 들어오는 이벤트가 벽 시계 시간에 근접해질 것으로 예상합니다. 이러한 제약 조건을 위반하는 이벤트를 삭제해야 합니다.

### <a name="lambda-architectures-or-backfill-process"></a>람다 아키텍처 또는 백필 프로세스

다행히 이전 데이터 보관 패턴을 사용하여 이러한 지연 이벤트를 정상적으로 처리할 수 있습니다. 이 개념은 보관 작업이 도착 시간에 들어오는 이벤트를 처리하고, Azure Blob 또는 Azure Data Lake Store의 적절한 시간 버킷에 이벤트와 이벤트 시간을 보관하는 것입니다. 이벤트 발생 빈도는 중요하지 않으며 절대 삭제되지 않습니다. 항상 올바른 시간 버킷에 도착합니다. 복구하는 동안 보관된 이벤트를 다시 처리하고 선택한 저장소에 결과를 백필할 수 있습니다. 이것은 람다 패턴을 구현하는 방법과 비슷합니다.

![ASA 백필](media/stream-analytics-solution-patterns/back-fill.png)

백필 프로세스는 Azure Stream Analytics에 다른 프로그래밍 모델을 포함할 가능성이 높은 오프라인 일괄 처리 시스템에서 수행해야 합니다. 즉, 전체 처리 논리를 다시 구현해야 합니다.

백필의 경우 안정 상태 처리 요구 사항보다 더 높은 처리량을 처리하기 위해 적어도 일시적으로 출력 싱크에 더 많은 리소스를 프로비저닝하는 것이 중요합니다.

|시나리오  |지금부터 다시 시작  |마지막으로 중지된 시간에서 다시 시작 |지금부터 다시 시작 + 보관된 이벤트로 백필|
|---------|---------|---------|---------|
|**대시보드**   |간격 만들기    |짧은 중단의 경우 적절함    |장기 중단에 사용 |
|**경고**   |허용 가능 |짧은 중단의 경우 적절함    |필요하지 않음 |
|**이벤트 소싱 앱** |허용 가능 |짧은 중단의 경우 적절함    |장기 중단에 사용 |
|**데이터 웨어하우징**   |데이터 손실  |허용 가능 |필요하지 않음 |
|**오프라인 분석**  |데이터 손실  |허용 가능 |필요하지 않음|

## <a name="putting-it-all-together"></a>모든 항목 요약

위에서 언급한 모든 솔루션 패턴을 복잡한 하나의 엔드투엔드 시스템에 함께 결합할 수 있다고 충분히 생각할 수 있을 것입니다. 결합된 시스템에는 대시보드, 경고, 이벤트 소싱 애플리케이션, 데이터 웨어하우징 및 오프라인 분석 기능이 포함될 수 있습니다.

핵심은 시스템을 구성 가능한 패턴으로 디자인하여 각 하위 시스템을 독립적으로 빌드, 테스트, 업그레이드 및 복구할 수 있도록 한다는 것입니다.

## <a name="next-steps"></a>다음 단계

지금까지 Azure Stream Analytics를 사용하여 다양한 솔루션 패턴을 살펴보았습니다. 다음으로 Stream Analytics 작업을 심층적으로 알아보고 첫 번째 Stream Analytics 작업을 만들 수 있습니다.

* [Azure Portal을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-portal.md)
* [Azure PowerShell을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-powershell.md)
* [Visual Studio를 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-vs.md)
