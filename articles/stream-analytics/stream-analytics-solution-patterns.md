---
title: Azure Stream Analytics 솔루션 패턴
description: 대시보드, 이벤트 메시징, 데이터 저장소, 참조 데이터 보강 및 모니터링과 같은 Azure Stream Analytics의 일반적인 솔루션 패턴에 대해 알아봅니다.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 3b95863c1ae53bd0642aec356f55aba1faf8ef09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535785"
---
# <a name="azure-stream-analytics-solution-patterns"></a>Azure Stream Analytics 솔루션 패턴

Azure의 다른 많은 서비스와 마찬가지로 Stream Analytics는 다른 서비스와 함께 더 큰 종단 간 솔루션을 만드는 데 가장 적합합니다. 이 문서에서는 간단한 Azure Stream Analytics 솔루션 및 다양한 아키텍처 패턴에 대해 설명합니다. 이러한 패턴을 기반으로 하여 보다 복잡한 솔루션을 개발할 수 있습니다. 이 문서에서 설명하는 패턴은 다양한 시나리오에서 사용할 수 있습니다. 시나리오별 패턴의 예는 [Azure 솔루션 아키텍처에서](https://azure.microsoft.com/solutions/architecture/?product=stream-analytics)사용할 수 있습니다.

## <a name="create-a-stream-analytics-job-to-power-real-time-dashboarding-experience"></a>실시간 대시보드 환경을 지원하는 스트림 분석 작업 만들기

Azure Stream Analytics를 사용하면 실시간 대시보드 및 경고를 신속하게 확인할 수 있습니다. 간단한 솔루션은 이벤트 허브 또는 IoT Hub에서 이벤트를 수집하고 [스트리밍 데이터 집합을 사용하여 Power BI 대시보드를 공급합니다.](/power-bi/service-real-time-streaming) 자세한 내용은 자세한 자습서를 [참조Stream Analytics를 사용하여 전화 통화 데이터 분석 및 Power BI 대시보드에서 결과를 시각화합니다.](stream-analytics-manage-job.md)

![ASA 파워 BI 대시보드](media/stream-analytics-solution-patterns/pbidashboard.png)

이 솔루션은 Azure 포털에서 몇 분 만에 빌드할 수 있습니다. 광범위한 코딩이 필요하지 않으며 SQL 언어는 비즈니스 논리를 표현하는 데 사용됩니다.

이 솔루션 패턴은 이벤트 소스에서 브라우저의 Power BI 대시보드까지 가장 낮은 대기 시간을 제공합니다. Azure 스트림 분석은 이 기본 제공 기능을 갖춘 유일한 Azure 서비스입니다.

## <a name="use-sql-for-dashboard"></a>대시보드에 SQL 사용

Power BI 대시보드는 짧은 대기 시간을 제공하지만 본격적인 Power BI 보고서를 생성하는 데 사용할 수는 없습니다. 일반적인 보고 패턴은 데이터를 먼저 SQL 데이터베이스에 출력하는 것입니다. 그런 다음 Power BI의 SQL 커넥터를 사용하여 SQL에서 최신 데이터를 쿼리합니다.

![ASA SQL 대시보드](media/stream-analytics-solution-patterns/sqldashboard.png)

SQL 데이터베이스를 사용하면 유연성을 높일 수 있지만 대기 시간이 약간 더 많이 듭니다. 이 솔루션은 대기 시간 요구 사항이 1초보다 큰 작업에 최적입니다. 이 방법을 사용하면 Power BI 기능을 최대화하여 보고서에 대한 데이터를 더 잘라내고 분석할 수 있으며 훨씬 더 많은 시각화 옵션을 사용할 수 있습니다. 또한 Tableau와 같은 다른 대시보드 솔루션을 유연하고 유연게 사용할 수 있습니다.

SQL은 처리량이 높은 데이터 저장소가 아닙니다. Azure Stream Analytics의 SQL 데이터베이스에 대한 최대 처리량은 현재 약 24MB/s입니다. 솔루션의 이벤트 원본이 더 높은 속도로 데이터를 생성하는 경우 Stream Analytics의 처리 논리를 사용하여 SQL로의 출력 속도를 줄여야 합니다. 필터링, 창이 있는 집계, 임시 조인과 패턴 일치 및 분석 함수와 같은 기술을 사용할 수 있습니다. Azure 스트림 분석 출력에서 Azure SQL Database에 설명된 기술을 사용하여 SQL에 대한 출력 속도를 [추가로 최적화할](stream-analytics-sql-output-perf.md)수 있습니다.

## <a name="incorporate-real-time-insights-into-your-application-with-event-messaging"></a>이벤트 메시징을 통해 애플리케이션에 실시간 인사이트 통합

Stream Analytics에서 두 번째로 가장 많이 사용하는 것은 실시간 알림을 생성하는 것입니다. 이 솔루션 패턴에서 Stream Analytics의 비즈니스 로직을 사용하여 [시간 적 및 공간 적 패턴](stream-analytics-geospatial-functions.md) 또는 [이상을](stream-analytics-machine-learning-anomaly-detection.md)검색한 다음 경고 신호를 생성할 수 있습니다. 그러나 Stream Analytics에서 Power BI를 기본 엔드포인트로 사용하는 대시보드 솔루션과 달리 여러 중간 데이터 싱크를 사용할 수 있습니다. 이러한 싱크에는 이벤트 허브, 서비스 버스 및 Azure 함수가 포함됩니다. 응용 프로그램 작성기는 시나리오에 가장 적합한 데이터 싱크를 결정해야 합니다.

기존 비즈니스 워크플로에서 경고를 생성하려면 다운스트림 이벤트 소비자 논리를 구현해야 합니다. Azure Functions에서 사용자 지정 논리를 구현할 수 있으므로 Azure Functions는 이 통합을 수행할 수 있는 가장 빠른 방법입니다. Azure 함수를 스트림 분석 작업의 출력으로 사용하는 자습서는 [Azure Stream Analytics 작업의 Azure 함수 실행에서](stream-analytics-with-azure-functions.md)찾을 수 있습니다. Azure Functions는 텍스트 및 전자 메일을 비롯한 다양한 유형의 알림도 지원합니다. 로직 앱은 스트림 분석과 로직 앱 간의 이벤트 허브와 함께 이러한 통합에도 사용할 수 있습니다.

![ASA 이벤트 메시징 앱](media/stream-analytics-solution-patterns/eventmessagingapp.png)

반면 이벤트 허브는 가장 유연한 통합 지점을 제공합니다. Azure 데이터 탐색기 및 타임시리즈 인사이트와 같은 다른 많은 서비스는 이벤트 허브의 이벤트를 사용할 수 있습니다. 서비스를 Azure Stream Analytics의 이벤트 허브 싱크에 직접 연결하여 솔루션을 완료할 수 있습니다. 이벤트 허브는 이러한 통합 시나리오에 대해 Azure에서 사용할 수 있는 가장 높은 처리량 메시징 브로커이기도 합니다.

## <a name="dynamic-applications-and-websites"></a>동적 응용 프로그램 및 웹 사이트

Azure Stream Analytics 및 Azure SignalR 서비스를 사용하여 대시보드 또는 맵 시각화와 같은 사용자 지정 실시간 시각화를 만들 수 있습니다. SignalR을 사용하여 웹 클라이언트를 업데이트하고 동적 콘텐츠를 실시간으로 표시할 수 있습니다.

![ASA 동적 응용 프로그램](media/stream-analytics-solution-patterns/dynamicapp.png)

## <a name="incorporate-real-time-insights-into-your-application-through-data-stores"></a>데이터 저장소를 통해 애플리케이션에 실시간 인사이트 통합

오늘날 대부분의 웹 서비스 및 웹 응용 프로그램은 프레젠테이션 계층을 제공하기 위해 요청/응답 패턴을 사용합니다. 요청/응답 패턴은 간단하게 빌드할 수 있으며 Cosmos DB와 같은 상태 비저장 프런트 엔드 및 확장 가능한 저장소를 사용하여 낮은 응답 시간으로 쉽게 확장할 수 있습니다.

데이터 볼륨이 높음으로 CRUD 기반 시스템에서 성능 병목 현상이 발생하는 경우가 많습니다. [이벤트 소싱 솔루션 패턴은](/azure/architecture/patterns/event-sourcing) 성능 병목 현상을 해결하는 데 사용됩니다. 임시 패턴과 통찰력은 기존 데이터 저장소에서 추출하기가 어렵고 비효율적입니다. 최신 대용량 데이터 기반 애플리케이션은 종종 데이터 흐름 기반 아키텍처를 채택합니다. Azure Stream Analytics는 모션 데이터의 계산 엔진으로서 해당 아키텍처의 핵심입니다.

![ASA 이벤트 소싱 앱](media/stream-analytics-solution-patterns/eventsourcingapp.png)

이 솔루션 패턴에서 이벤트는 Azure Stream Analytics에서 처리되고 데이터 저장소로 집계됩니다. 응용 프로그램 계층은 기존 요청/응답 패턴을 사용하여 데이터 저장소와 상호 작용합니다. Stream Analytics가 실시간으로 많은 이벤트를 처리할 수 있기 때문에 데이터 저장소 계층을 일괄 처리할 필요 없이 응용 프로그램의 확장성이 매우 높습니다. 데이터 저장소 계층은 기본적으로 시스템에서 구체화된 뷰입니다. [Azure 코스모스 DB에 대한 Azure 스트림 분석 출력은](stream-analytics-documentdb-output.md) Cosmos DB가 스트림 분석 출력으로 사용되는 방법을 설명합니다.

처리 논리가 복잡하고 논리의 특정 부분을 독립적으로 업그레이드해야 하는 실제 응용 프로그램에서는 여러 Stream Analytics 작업을 이벤트 허브와 함께 중개 이벤트 브로커로 구성할 수 있습니다.

![ASA 복잡한 이벤트 소싱 앱](media/stream-analytics-solution-patterns/eventsourcingapp2.png)

이 패턴은 시스템의 복원력과 관리 성을 향상시킵니다. 그러나 Stream Analytics가 정확히 한 번 처리를 보장하더라도 중복 이벤트가 중개 이벤트 허브에 발생할 가능성은 적습니다. 다운스트림 스트림 애널리틱스 작업은 룩백 창에서 논리 키를 사용하여 이벤트를 삭제하는 것이 중요합니다. 이벤트 배달에 대한 자세한 내용은 [이벤트 배달 보장 참조를](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) 참조하십시오.

## <a name="use-reference-data-for-application-customization"></a>응용 프로그램 사용자 지정에 참조 데이터 사용

Azure Stream Analytics 참조 데이터 기능은 경고 임계값, 처리 규칙 및 [지오펜스와](geospatial-scenarios.md)같은 최종 사용자 사용자 지정을 위해 특별히 설계되었습니다. 응용 프로그램 계층은 매개 변수 변경 내용을 수락하고 SQL 데이터베이스에 저장할 수 있습니다. Stream Analytics 작업은 주기적으로 데이터베이스의 변경 내용을 쿼리하고 참조 데이터 조인을 통해 사용자 지정 매개 변수에 액세스할 수 있도록 합니다. 응용 프로그램 사용자 지정에 참조 데이터를 사용하는 방법에 대한 자세한 내용은 [SQL 참조 데이터](sql-reference-data.md) 및 참조 데이터 [조인을](/stream-analytics-query/reference-data-join-azure-stream-analytics)참조하십시오.

이 패턴을 사용하여 참조 데이터에서 규칙의 임계값이 정의되는 규칙 엔진을 구현할 수도 있습니다. 규칙에 대한 자세한 내용은 [Azure Stream Analytics의 프로세스 구성 가능한 임계값 기반 규칙을](stream-analytics-threshold-based-rules.md)참조하십시오.

![ASA 참조 데이터 앱](media/stream-analytics-solution-patterns/refdataapp.png)

## <a name="add-machine-learning-to-your-real-time-insights"></a>실시간 인사이트에 머신 러닝 추가

Azure Stream Analytics의 기본 제공 [변칙 검색 모델은](stream-analytics-machine-learning-anomaly-detection.md) 실시간 응용 프로그램에 기계 학습을 도입하는 편리한 방법입니다. 광범위한 기계 학습 요구 사항에 대한 자세한 내용은 [Azure Stream Analytics가 Azure 기계 학습의 채점 서비스와 통합됩니다.](stream-analytics-machine-learning-integration-tutorial.md)

동일한 Stream Analytics 파이프라인에 온라인 교육 및 점수를 통합하려는 고급 사용자의 경우 [선형 회귀를](stream-analytics-high-frequency-trading.md)사용하여 이를 수행하는 방법에 대한 이 예제를 참조하십시오.

![ASA 기계 학습 응용 프로그램](media/stream-analytics-solution-patterns/mlapp.png)

## <a name="near-real-time-data-warehousing"></a>거의 실시간 데이터 웨어하우징

또 다른 일반적인 패턴은 실시간 데이터 웨어하우징(스트리밍 데이터 웨어하우스라고도 함)입니다. 애플리케이션에서 이벤트 허브 및 IoT Hub에 도착하는 이벤트 외에도 [IoT Edge에서 실행되는 Azure Stream Analytics를](stream-analytics-edge.md) 사용하여 데이터 정리, 데이터 감소, 데이터 저장 및 전달 요구 사항을 충족할 수 있습니다. IoT Edge에서 실행되는 스트림 애널리틱스는 시스템의 대역폭 제한 및 연결 문제를 정상적으로 처리할 수 있습니다. SQL 출력 어댑터를 사용하여 SQL 데이터 웨어하우스로 출력할 수 있습니다. 그러나 최대 처리량은 10MB/s로 제한됩니다.

![ASA 데이터 웨어하우징](media/stream-analytics-solution-patterns/datawarehousing.png)

일부 대기 시간 장단점을 통해 처리량을 개선하는 한 가지 방법은 이벤트를 Azure Blob 저장소에 보관한 다음 [Polybase를 사용하여 SQL 데이터 웨어하우스로 가져오는 것입니다.](../synapse-analytics/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md) [타임스탬프별로 데이터를 보관하고](stream-analytics-custom-path-patterns-blob-storage-output.md) 주기적으로 가져와서 Stream Analytics에서 Blob 저장소에 대한 출력과 Blob 저장소에서 SQL 데이터 웨어하우스로의 입력에 수동으로 연결해야 합니다.

이 사용 패턴에서 Azure Stream Analytics는 거의 실시간 ETL 엔진으로 사용됩니다. 새로 도착하는 이벤트는 다운스트림 분석 서비스 소비를 위해 지속적으로 변환되고 저장됩니다.

![ASA 높은 처리량 데이터 웨어하우징](media/stream-analytics-solution-patterns/datawarehousing2.png)

## <a name="archiving-real-time-data-for-analytics"></a>분석을 위한 실시간 데이터 보관

대부분의 데이터 과학 및 분석 활동은 여전히 오프라인에서 발생합니다. Azure Data Lake Store Gen2 출력 및 마루 출력 형식을 통해 Azure Stream Analytics에서 데이터를 보관할 수 있습니다. 이 기능은 Azure 데이터 레이크 분석, Azure Databricks 및 Azure HDInsight에 직접 데이터를 공급하는 마찰을 제거합니다. Azure 스트림 분석은 이 솔루션에서 거의 실시간 ETL 엔진으로 사용됩니다. 다양한 계산 엔진을 사용하여 Data Lake에서 보관된 데이터를 탐색할 수 있습니다.

![ASA 오프라인 분석](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="use-reference-data-for-enrichment"></a>보강을 위해 참조 데이터 사용

ETL 엔진의 경우 데이터 보강이 요구되는 경우가 많습니다. Azure Stream Analytics는 SQL 데이터베이스와 Azure Blob 저장소의 [참조 데이터로](stream-analytics-use-reference-data.md) 데이터 보강을 지원합니다. Azure 데이터 레이크와 SQL 데이터 웨어하우스 모두에서 데이터 방문에 대해 데이터 보강을 수행할 수 있습니다.

![데이터 보강을 통해 ASA 오프라인 분석](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="operationalize-insights-from-archived-data"></a>보관된 데이터에서 인사이트 운영

오프라인 분석 패턴을 거의 실시간 응용 프로그램 패턴과 결합하는 경우 피드백 루프를 만들 수 있습니다. 피드백 루프를 사용하면 응용 프로그램이 데이터의 패턴 변경에 맞게 자동으로 조정할 수 있습니다. 이 피드백 루프는 경고를 위한 임계값을 변경하는 것만큼 간단하거나 기계 학습 모델을 재교육하는 것만큼 복잡할 수 있습니다. 클라우드와 IoT Edge에서 실행되는 ASA 작업에 동일한 솔루션 아키텍처를 적용할 수 있습니다.

![ASA 인사이트 운영화](media/stream-analytics-solution-patterns/insightsoperationalization.png)

## <a name="how-to-monitor-asa-jobs"></a>ASA 작업을 모니터링하는 방법

Azure Stream Analytics 작업은 24/7을 실행하여 들어오는 이벤트를 실시간으로 처리할 수 있습니다. 가동 시간 보장은 전체 응용 프로그램의 건강에 매우 중요합니다. Stream Analytics는 [99.9%의 가용성 보장을](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/)제공하는 업계 유일의 스트리밍 분석 서비스이지만 여전히 일정 수준의 가동 중지 시간이 발생할 수 있습니다. 수년에 걸쳐 Stream Analytics는 작업의 상태를 반영하기 위해 메트릭, 로그 및 작업 상태를 도입했습니다. 모두 Azure Monitor 서비스를 통해 표시되며 OMS로 추가로 내보낼 수 있습니다. 자세한 내용은 [스트림 분석 작업 모니터링 이해 및 쿼리 모니터링 방법을 참조하세요.](stream-analytics-monitoring.md)

![ASA 모니터링](media/stream-analytics-solution-patterns/monitoring.png)

모니터링해야 할 두 가지 주요 사항이 있습니다.

- [작업 실패 상태](job-states.md)

    무엇보다도 작업이 실행되고 있는지 확인해야 합니다. 실행 중인 상태에서 작업이 없으면 새 메트릭이나 로그가 생성되지 않습니다. SU 사용률이 높은 경우(즉, 리소스 부족)를 포함하여 여러 가지 이유로 작업이 실패한 상태로 변경될 수 있습니다.

- [워터마크 지연 메트릭](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

    이 메트릭은 처리 파이프라인이 벽 시계 시간(초)에 얼마나 뒤처져 있는지를 반영합니다. 지연 중 일부는 고유한 처리 논리에 기인합니다. 따라서 절대 값을 모니터링하는 것보다 증가하는 추세를 모니터링하는 것이 훨씬 더 중요합니다. 정상 상태 지연은 모니터링이나 경고가 아니라 응용 프로그램 디자인에 의해 해결되어야 합니다.

오류가 발생하면 활동 로그 및 [진단 로그가](stream-analytics-job-diagnostic-logs.md) 오류를 찾기 가장 좋은 장소입니다.

## <a name="build-resilient-and-mission-critical-applications"></a>복원력 및 미션 크리티컬 애플리케이션 구축

Azure Stream Analytics의 SLA 보장 및 종단 간 응용 프로그램을 얼마나 신중하게 실행하든 중단이 발생합니다. 응용 프로그램이 미션 크리티컬한 경우 정상적으로 복구하려면 가동 중단에 대비해야 합니다.

응용 프로그램에 경고하는 가장 중요한 것은 다음 경고를 감지하는 것입니다. 과거 경고를 무시하고 복구할 때 현재 시간부터 작업을 다시 시작하도록 선택할 수 있습니다. 작업 시작 시간 의미체계는 첫 번째 입력 시간이 아니라 첫 번째 출력 시간까지입니다. 입력은 지정된 시간에 첫 번째 출력이 완전하고 정확하다는 것을 보장하기 위해 적절한 시간을 뒤로 되감습니다. 결과적으로 부분 집계 및 트리거 경고를 예기치 않게 얻을 수 없습니다.

과거에 어느 정도 시간에서 출력을 시작하도록 선택할 수도 있습니다. 이벤트 허브와 IoT Hub의 보존 정책은 모두 과거의 처리를 허용하는 적절한 양의 데이터를 보유합니다. 장단점은 현재 시간을 따라잡고 적시에 새로운 경고를 생성하기 시작할 수 있는 속도입니다. 데이터는 시간이 지남에 따라 빠르게 가치를 상실하므로 현재 시간을 신속하게 따라잡는 것이 중요합니다. 두 가지 방법으로 신속하게 따라잡을 수 있습니다.

- 따라잡을 때 더 많은 리소스(SU)를 프로비전합니다.
- 현재 시간에서 다시 시작합니다.

현재 시간에서 다시 시작하는 것은 처리 하는 동안 간격을 떠나는 절충오프와 함께 할 간단 합니다. 이 방법을 다시 시작하면 경고 시나리오에 대해 괜찮을 수 있지만 대시보드 시나리오에는 문제가 될 수 있으며 보관 및 데이터 웨어하우징 시나리오에는 문제가 될 수 있습니다.

더 많은 리소스를 프로비저닝하면 프로세스 속도가 빨라질 수 있지만 처리 속도 급증의 효과는 복잡합니다.

- 작업이 더 많은 수의 구로 확장 가능한지 테스트합니다. 모든 쿼리를 확장할 수 있는 것은 아닙니다. 쿼리가 [병렬화되어](stream-analytics-parallelization.md)있는지 확인해야 합니다.

- 업스트림 이벤트 허브 또는 IoT Hub에 입력 처리량을 조정하기 위해 처리량 단위(T)를 더 추가할 수 있는 파티션이 충분한지 확인합니다. 각 이벤트 허브 TU는 2MB/s의 출력 속도로 최대출력됩니다.

- 출력 싱크(예: SQL Database, Cosmos DB)에 충분한 리소스를 프로비전하여 출력의 급증을 제한하지 않도록 하여 시스템이 잠기는 경우가 있습니다.

가장 중요한 것은 처리 속도 변화를 예측하고, 프로덕션에 들어가기 전에 이러한 시나리오를 테스트하고, 오류 복구 시간 동안 처리를 올바르게 확장할 준비가 되는 것입니다.

들어오는 이벤트가 모두 지연되는 극단적인 시나리오에서는 작업에 늦게 도착하는 창을 적용한 경우 [지연된 모든 이벤트가 삭제될 수](stream-analytics-time-handling.md) 있습니다. 이벤트의 삭제는 처음에 신비한 동작으로 보일 수 있습니다. 그러나 Stream Analytics가 실시간 처리 엔진이라는 점을 고려하면 들어오는 이벤트가 벽 시계 시간에 가까워질 것으로 예상됩니다. 이러한 제약 조건을 위반하는 이벤트를 삭제해야 합니다.

### <a name="lambda-architectures-or-backfill-process"></a>람다 아키텍처 또는 백필 프로세스

다행히 이전 데이터 보관 패턴을 사용하여 이러한 늦은 이벤트를 정상적으로 처리할 수 있습니다. 아이디어는 보관 작업이 도착 시간에 들어오는 이벤트를 처리하고 이벤트 시간을 따라 Azure Blob 또는 Azure Data Lake Store의 적절한 시간 버킷에 이벤트를 보관한다는 것입니다. 이벤트가 얼마나 늦게 도착하는지는 중요하지 않으며, 절대 삭제되지 않습니다. 그것은 항상 적절한 시간 버킷에 착륙합니다. 복구하는 동안 보관된 이벤트를 다시 처리하고 결과를 선택한 저장소로 채울 수 있습니다. 이는 람다 패턴이 구현되는 방식과 유사합니다.

![ASA 백필](media/stream-analytics-solution-patterns/backfill.png)

채우기 프로세스는 Azure Stream Analytics와 다른 프로그래밍 모델을 가진 오프라인 일괄 처리 시스템으로 수행해야 합니다. 즉, 전체 처리 논리를 다시 구현해야 합니다.

채우기의 경우 정상 상태 처리 요구보다 더 높은 처리량을 처리하기 위해 출력 싱크에 더 많은 리소스를 일시적으로 프로비저닝하는 것이 중요합니다.

|시나리오  |지금부터 만 다시 시작  |마지막으로 중지된 시간에서 다시 시작 |지금부터 다시 시작 + 보관된 이벤트로 채우기|
|---------|---------|---------|---------|
|**대시보드**   |간격 을 만듭니다.    |짧은 정전시 확인    |긴 정전 시 사용 |
|**경고**   |용인되는 |짧은 정전시 확인    |필요하지 않음 |
|**이벤트 소싱 앱** |용인되는 |짧은 정전시 확인    |긴 정전 시 사용 |
|**데이터 웨어하우징**   |데이터 손실  |용인되는 |필요하지 않음 |
|**오프라인 분석**  |데이터 손실  |용인되는 |필요하지 않음|

## <a name="putting-it-all-together"></a>모든 항목 요약

위에서 언급한 모든 솔루션 패턴을 복잡한 종단 간 시스템에서 함께 결합할 수 있다는 것은 상상하기 어렵지 않습니다. 결합된 시스템에는 대시보드, 경고, 이벤트 소싱 응용 프로그램, 데이터 웨어하우징 및 오프라인 분석 기능이 포함될 수 있습니다.

핵심은 구성 가능한 패턴으로 시스템을 설계하여 각 하위 시스템을 구축, 테스트, 업그레이드 및 독립적으로 복구할 수 있도록 하는 것입니다.

## <a name="next-steps"></a>다음 단계

이제 Azure Stream Analytics를 사용하여 다양한 솔루션 패턴을 볼 수 있습니다. 다음으로 Stream Analytics 작업을 심층적으로 알아보고 첫 번째 Stream Analytics 작업을 만들 수 있습니다.

* [Azure 포털을 사용하여 스트림 분석 작업을 만듭니다.](stream-analytics-quick-create-portal.md)
* [Azure PowerShell 을 사용하여 스트림 분석 작업을 만듭니다.](stream-analytics-quick-create-powershell.md)
* [Visual Studio를 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-vs.md)
