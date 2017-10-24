---
title: "Intelligent Insights를 사용하여 데이터베이스 사용량 모니터링 - Azure SQL Database | Microsoft Docs"
description: "Azure SQL Database Intelligent Insights는 기본 제공 인텔리전스를 사용하여 지속적으로 인공 지능을 통해 데이터베이스 사용량을 모니터링하고 성능 저하의 원인이 되는 중단 이벤트를 검색합니다."
services: sql-database
documentationcenter: 
author: danimir
manager: drasumic
editor: carlrab
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: NA
ms.date: 09/25/2017
ms.author: v-daljep
ms.openlocfilehash: 8e55fa02d5edda21fbf547397d63f9d1cf987a60
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="intelligent-insights"></a>인텔리전스 Insights

***&#8220;인텔리전스 Insights를 통해 데이터베이스 성능 상황을 알 수 있습니다.&#8221;***

Azure SQL Database Intelligent Insights는 기본 제공 인텔리전스를 사용하여 지속적으로 인공 지능을 통해 데이터베이스 사용량을 모니터링하고 성능 저하의 원인이 되는 중단 이벤트를 검색합니다. 검색되면 이를 상세하게 분석하여 문제에 대한 지능형 평가와 함께 진단 로그를 생성합니다. 이 평가는 데이터베이스 성능 문제의 근본 원인 분석과 성능 개선을 위한 가능한 권장 사항이 있는 위치로 구성되어 있습니다. 이것이 바로 ***&#8220;인텔리전스 Insights입니다.&#8221;*** 

## <a name="what-can-intelligent-insights-do-for-you"></a>인텔리전스 Insights가 할 수 있는 일은 무엇입니까?

인텔리전스 Insights는 다음 값을 제공하는 Azure의 기본 제공 인텔리전스의 고유 기능입니다.

- 사전 모니터링
- 맞춤형 성능 정보
- 데이터베이스 성능 저하의 초기 검색
- 검색된 문제의 RCA(근본 원인 분석)
- 성능 개선 권장 사항
- 수십만 개의 데이터베이스에서 기능 확장
- DevOps 리소스와 총 소유 비용에 미치는 긍정적인 영향

## <a name="how-does-intelligent-insights-work"></a>인텔리전스 Insights는 어떻게 작동합니까?

인텔리전스 Insights는 마지막 1시간의 데이터베이스 워크로드와 지난 7일의 기준 워크로드를 비교하여 Azure SQL Database 성능을 분석합니다. 데이터베이스 워크로드는 가장 많이 반복되고 가장 큰 쿼리와 같이 데이터베이스 성능에 가장 중요하다고 판단되는 쿼리로 구성됩니다. 생성된 각 워크로드 기준이 개별 인스턴스에 대해 특정적이고 고유하기 때문에 각 데이터베이스는 구조, 데이터, 사용량 및 응용 프로그램에 따라 고유합니다. 워크로드 기준과는 별도로 인텔리전스 Insights는 또한 절대 작업 임계값을 모니터링하고 과도한 대기 시간 문제, 심각한 예외, 성능에 영향을 줄 수 있는 쿼리 매개 변수화의 문제를 감지합니다.

인공 지능을 통해 다수의 관찰된 메트릭으로부터 성능 저하 문제가 감지되면, 데이터베이스의 상황에 대한 인텔리전스 Insights를 포함한 진단 로그를 출력합니다. 인텔리전스 Insights를 사용하면 데이터베이스 성능 문제가 처음 출현할 때부터 해결될 때까지 쉽게 추적할 수 있습니다. 이는 최초 문제 감지부터, 성능 개선 확인, 완료 등의 주기에서 검색된 각 문제의 상태를 추적하여 이루어집니다. 업데이트는 15분마다 진단 로그에서 제공됩니다. 

<center>![서버](./media/sql-database-intelligent-insights/intelligent-insights-concept.png)</center>

데이터베이스 성능 문제를 측정하고 검색하는 데 사용되는 메트릭은 쿼리 기간, 시간 제한 요청, 과도한 대기 시간, 오류 발생 요청을 기반으로 하고 있으며, 이 문서의 [검색 메트릭](sql-database-intelligent-insights.md#detection-metrics) 섹션에서 자세히 설명되어 있습니다.

## <a name="degradations-detected"></a>성능 저하 검색

확인된 Azure SQL Database 성능 저하는 다음 속성으로 구성된 지능형 항목과 함께 진단 로그에 기록됩니다.

| 속성             | 세부 정보              |
| :------------------- | ------------------- |
| 데이터베이스 정보  | 리소스 URI와 같이 정보가 검색된 데이터베이스에 대한 메타데이터 |
| 관찰된 시간 범위 | 검색된 정보 기간에 대한 시작 및 종료 시간 |
| 영향을 받는 메트릭 | 정보를 생성하는 메트릭: <ul><li>쿼리 기간 증가[초]</li><li>과도한 대기[초]</li><li>시간 제한 요청[백분율]</li><li>오류 발생 요청[백분율]</li></ul>|
| 영향 값 | 측정된 메트릭 값 |
| 영향 받는 쿼리 및 오류 코드 | 쿼리 해시 또는 오류 코드입니다. 영향을 받는 쿼리에 상관 관계를 적용하는 데 쉽게 사용할 수 있습니다. 쿼리 기간 증가, 대기 시간, 시간 제한 수 또는 오류 코드로 구성된 메트릭이 제공됩니다. |
| 검색 | 이벤트의 시간 동안 데이터베이스에서 식별된 검색합니다. 15가지 검색 패턴이 있습니다. [인텔리전스 Insights를 사용하여 데이터베이스 성능 문제를 해결](sql-database-intelligent-insights-troubleshoot-performance.md)을 참조하세요. |
| 근본 원인 분석 | 인간이 읽을 수 있는 형식으로 식별된 문제의 RCA(근본 원인 분석)입니다. 가능한 경우 일부 정보에는 성능 개선 권장 사항이 있을 수 있습니다. |
|||

## <a name="issues-state-lifecycle-active-verifying-and-completed"></a>문제 상태 수명 주기: 활성, 확인 중, 완료됨

진단 로그에 기록된 성능 문제는 세 가지 문제 상태 수명 주기(활성, 확인 중 및 완료됨) 중 하나와 함께 플래그 지정됩니다. 성능 문제가 검색되고 Azure SQL 기본 제공 인텔리전스가 이를 present로 간주하는 한, 해당 문제는 &#8220;***활성***&#8221;으로 플래그 지정됩니다. 문제가 완화됨으로 간주될 때 이 문제는 확인되고 문제 상태는 &#8220;***확인중***&#8221;으로 변경됩니다. Azure SQL 기본 제공 인텔리전스가 문제를 해결됨으로 간주하면, 문제 상태는 &#8220;***완료됨***&#8221;으로 플래그 지정됩니다.

## <a name="using-intelligent-insights"></a>인텔리전스 Insights 사용

[Azure SQL Database 메트릭 및 진단 로깅](sql-database-metrics-diag-logging.md)에서 설명된 대로 인텔리전스 Insights 진단 로그를 Azure Log Analytics, Azure Event Hub, Azure Storage로 보낼 수 있습니다. 로그가 이러한 대상 중 하나로 보내지면 이 로그는 Microsoft 또는 타사 도구를 사용하여 사용자 지정 경고 및 모니터링 개발을 위해 사용될 수 있습니다. 인텔리전스 Insights를 사용하여 Azure SQL Database 성능 문제 해결 방법에 대한 자세한 내용은 [인텔리전스 Insights를 사용하여 Azure SQL Database 성능 문제 해결](sql-database-intelligent-insights-troubleshoot-performance.md)을 참조하세요.

## <a name="built-in-intelligent-insights-analytics-with-azure-log-analytics"></a>Azure Log Analytics을 사용한 기본 제공 인텔리전스 Insights 분석 

Azure Log Analytics 솔루션은 인텔리전스 Insights 진단 로그 데이터를 바탕으로 보고 및 경고 기능을 제공합니다. 아래는 Azure SQL Analytics 인텔리전스 Insights 보고서의 예입니다.

![서버](./media/sql-database-intelligent-insights/intelligent-insights-azure-sql-analytics.png)

인텔리전스 Insights 진단 로그가 Azure SQL Analytics에 데이터를 스트리밍하도록 구성되면 [Azure SQL Analytics을 사용하여 Azure SQL Database를 모니터링](../log-analytics/log-analytics-azure-sql.md)할 수 있습니다.

## <a name="custom-integrations-of-intelligent-insights-log"></a>인텔리전스 Insights 로그의 사용자 지정 통합

Microsoft 또는 타사 도구를 사용하여 사용자 지정 경고 및 모니터링 개발에 대한 자세한 내용은 [인텔리전스 Insights 데이터베이스 성능 진단 로그 사용](sql-database-intelligent-insights-use-diagnostics-log.md)을 참조하세요.

## <a name="how-to-set-up-intelligent-insights-with-azure-event-hub"></a>Azure Event Hub를 사용하여 인텔리전스 Insights를 설정하는 방법

- [Azure Diagnostic Logs를 Event Hubs에 스트리밍](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md)을 참조하여 인텔리전스 Insights를 구성하여 로그 이벤트를 Azure Event Hub에 스트리밍합니다.
- [Event Hub에서 메트릭 및 진단 로그로 할 수 있는 일](sql-database-metrics-diag-logging.md#stream-into-azure-storage)을 참조하여 사용자 지정 모니터링 및 경고에 대해 Azure Event Hub를 사용합니다. 

## <a name="how-to-set-up-intelligent-insights-with-azure-storage"></a>Azure Storage를 사용하여 인텔리전스 Insights를 설정하는 방법

- [Azure Storage로 스트리밍](sql-database-metrics-diag-logging.md#stream-into-azure-storage)을 참조하여 Azure Storage를 사용하여 저장할 인텔리전스 Insights를 구성합니다.

## <a name="detection-metrics"></a>검색 메트릭

인텔리전스 Insights를 생성하는 검색 모델에 사용되는 메트릭은 다음과 같은 모니터링을 기반으로 합니다.

- 쿼리 기간
- 시간 제한 요청
- 과도한 대기 시간 
- 오류 발생 요청

쿼리 기간 및 시간 제한 요청은 데이터베이스 워크로드 성능 문제를 검색하는 데 기본 모델로서 사용됩니다. 이는 워크로드의 상황을 바로 측정하기 때문입니다. 워크로드 성능 저하의 모든 가능한 사례를 검색하기 위해 과도한 대기 시간 및 오류 발생 요청이 워크로드 성능에 영향을 주는 문제를 나타내는 추가 모델로 사용됩니다.

시스템은 워크로드의 변화와 데이터베이스에 적용된 쿼리 요청 수의 변경을 자동으로 고려하여 데이터베이스 성능 임계값이 정상적인지 범위를 벗어났는지를 동적으로 판단합니다.

과학적으로 파생된 데이터 모델을 통해 모든 메트릭을 다양한 관계에서 고려하여 각 성능 문제를 검색하여 분류합니다. 인텔리전스 Insights를 통해 제공되는 정보에는 검색된 성능 문제의 세부 정보, 문제 발생의 근본 원인 분석, 그리고 가능한 경우 모니터링된 Azure SQL Database의 성능을 개선하는 방법에 대한 권장 사항이 있습니다.

## <a name="query-duration"></a>쿼리 기간

쿼리 기간 저하 모델은 개별 쿼리를 분석하고 성능 기준과 비교하여 쿼리를 컴파일하고 실행하는 데 걸리는 시간의 증가를 검색합니다.

Azure SQL Database 기본 제공 인텔리전스가 워크로드 성능에 영향을 주는 쿼리 컴파일 또는 쿼리 실행 시간이 크게 증가한 것을 검색한 경우, 이러한 쿼리는 쿼리 기간 성능 저하 문제가 있음을 나타내기 위해 플래그 지정됩니다. 

인텔리전스 Insights 진단 로그는 성능이 저하된 쿼리의 쿼리 해시, 성능 저하가 쿼리 컴파일 또는 실행 시간 증가와 관련이 있는지 여부 표시, 그리고 그 시간 동안 증가한 쿼리를 출력합니다.

## <a name="timeout-requests"></a>시간 제한 요청

시간 제한 요청 저하 모델은 개별 쿼리를 분석하고 쿼리 실행 수준에서 시간 제한의 증가, 그리고 성능 기준 기간과 비교하여 데이터베이스 수준에서 전체 요청 시간 제한을 감지합니다.

일부 쿼리는 실행 단계에 도달하기 전이라도 시간이 종료될 수 있으므로, Azure SQL Database 기본 제공 인텔리전스는 또한 중단된 작업자와 요청을 비교하여 실행 단계 도달 여부에 관계 없이 데이터베이스에 도달한 모든 쿼리를 평가하고 분석합니다. 

실행된 쿼리에 대한 시간 제한 수 또는 중단된 요청 작업자의 수가 시스템 관리 임계값보다 크면 진단 로그는 인텔리전스 Insights로 채워집니다.

생성된 정보에는 시간 제한 요청의 수, 시간 제한 쿼리의 수, 그리고 성능 저하가 실행 단계에서 또는 전체 데이터베이스 수준에서 시간 제한 증가와 관련이 있는지 여부 표시가 있습니다. 시간 제한의 증가가 데이터베이스 성능에 중요한 것으로 간주되는 경우 이러한 쿼리는 시간 제한 성능 저하 문제가 있음을 나타내기 위해 플래그 지정됩니다. 

## <a name="excessive-wait-times"></a>과도한 대기 시간

과도한 대기 시간 모델은 개별 데이터베이스 쿼리를 모니터링하고 시스템 관리 절대 임계값 이상의 비정상적으로 높은 쿼리 대기 통계를 검색합니다. 새로운 SQL Server 기능인 쿼리 저장소 대기 통계(sys.query_store_wait_stats) 기능을 활용하면 다음과 같은 쿼리의 과도한 대기 시간 메트릭이 관찰됩니다.

- 리소스 제한에 도달
- 탄력적 풀 리소스 제한에 도달
- 과도한 작업자 또는 세션 스레드 수
- 과도한 데이터베이스 잠금
- 메모리 압력
- 다른 대기 통계

리소스 제한이나 탄력적 풀 리소스 제한에 도달하면 구독 또는 탄력적인 풀에서 사용할 수 있는 리소스 소모량이 절대 임계값 이상으로 증가했음을 표시하고, 이는 워크로드 성능 저하를 나타냅니다. 과도한 작업자 또는 세션 스레드 수는 시작된 작업자 스레드 또는 세션 수가 절대 임계값 이상에 도달했다는 조건을 표시하고, 이는 워크로드 성능 저하를 나타냅니다.

과도한 데이터베이스 잠금은 데이터베이스에서 잠금 수가 절대는 임계값 이상에 도달했다는 조건을 표시하고, 이는 워크로드 성능 저하를 나타냅니다. 메모리 압력은 메모리 부여를 요청하는 스레드 수가 절대 임계값 이상으로 증가했다는 조건이며, 이는 워크로드 성능 저하를 나타냅니다.

다른 대기 통계 검색은 쿼리 저장소 대기 통계를 통해 측정된 기타 메트릭이 절대 임계값 이상에 도달했다는 조건을 나타내며, 이는 워크로드 성능 저하를 나타냅니다.

과도한 대기 시간이 감지되면 인텔리전스 Insights 진단 로그가 출력되며, 이는 사용할 수 있는 데이터, 성능 저하에 영향을 주거나 영향을 받는 쿼리의 해시, 실행 시 쿼리가 대기하도록 하는 메트릭의 세부 정보, 측정된 대기 시간에 따라 달라집니다.

## <a name="errored-requests"></a>오류 발생 요청

오류 발생 요청 저하 모델은 개별 쿼리를 모니터링하고 기준 기간과 비교하여 오류가 발생한 쿼리 수의 증가를 검색합니다. 이 모델은 또한 Azure Database 기본 제공 인텔리전스에서 관리되는 절대 임계값에 도달한 중요 예외를 모니터링합니다. 시스템은 모니터링되는 기간에 데이터베이스에 적용된 쿼리 요청과 워크로드의 변화에 대한 계정의 수를 자동으로 고려합니다.

적용된 전체 요청 수와 관련하여 측정된 오류 발생 요청의 증가가 워크로드 성능에 중요한 것으로 간주되는 경우 영향을 받는 쿼리는 오류 발생 요청 성능 저하 문제가 있음을 나타내기 위해 플래그 지정됩니다.

인텔리전스 Insights 로그는 오류 요청 횟수, 성능 저하가 오류 발생 요청의 증가 또는 모니터링된 중요 예외 임계값과 관련이 있는지 여부 표시, 성능 저하의 측정 시간을 출력합니다. 

모니터링 중요 예외가 시스템에서 관리되는 절대 임계값 이상에 도달하는 경우 인텔리전스 Insights가 중요 예외 정보와 함께 생성됩니다.

## <a name="next-steps"></a>다음 단계
* [인텔리전스 Insights를 사용하여 Azure SQL Database 성능 문제 해결](sql-database-intelligent-insights-troubleshoot-performance.md)하는 방법 알아보기
* [인텔리전스 Insights Azure SQL Database 성능 진단 로그](sql-database-intelligent-insights-use-diagnostics-log.md) 사용
* [Azure SQL Analytics를 사용하여 Azure SQL Database를 모니터링](../log-analytics/log-analytics-azure-sql.md)하는 방법 알아보기
* [Azure 리소스에서 로그 데이터 수집 및 소비](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)하는 방법 알아보기


