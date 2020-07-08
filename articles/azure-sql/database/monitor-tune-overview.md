---
title: 모니터링 및 성능 튜닝
description: Azure SQL Database 및 Azure SQL Managed Instance의 모니터링 및 성능 조정 기능 및 방법에 대 한 개요입니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 6e17e2a6e5c9151080facc3a2dd8c1a18c0580fe
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85982171"
---
# <a name="monitoring-and-performance-tuning-in-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL Database 및 Azure SQL Managed Instance의 모니터링 및 성능 튜닝
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database 및 Azure SQL Managed Instance에서 데이터베이스의 성능을 모니터링 하려면 먼저 특정 서비스 계층 및 성능 수준을 선택 하 여 선택한 데이터베이스 성능 수준을 기준으로 워크 로드에서 사용 하는 CPU 및 IO 리소스를 모니터링 합니다. 이를 위해 Azure SQL Database 및 Azure SQL Managed Instance는 Azure Portal에서 볼 수 있는 리소스 메트릭과 [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) 또는 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) SQL Server 관리 도구 중 하나를 사용 하 여 내보낼 수 있습니다.

Azure SQL Database는 지능형 성능 튜닝 권장 사항 및 성능 향상을 위한 자동 조정 옵션을 제공 하기 위한 다양 한 데이터베이스 관리자를 제공 합니다. 또한 Query Performance Insight에는 단일 및 풀링된 데이터베이스에 대 한 가장 많은 CPU 및 IO 사용을 담당 하는 쿼리에 대 한 세부 정보가 표시 됩니다.

Azure SQL Database 및 Azure SQL Managed Instance 인공 지능을 통해 지원 되는 고급 모니터링 및 튜닝 기능을 제공 하 여 데이터베이스 및 솔루션의 성능 문제를 해결 하 고이를 최대화 합니다. 이러한 [Intelligent Insights](intelligent-insights-overview.md) 및 기타 데이터베이스 리소스 로그 및 메트릭의 [스트리밍 내보내기를](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) 사용 및 분석을 위해 특히 [SQL 분석](../../azure-monitor/insights/azure-sql.md)을 사용 하 여 여러 대상 중 하나로 구성 하도록 선택할 수 있습니다. Azure SQL 분석는 단일 보기의 여러 구독에서 규모에 상관 없이 모든 데이터베이스의 성능을 모니터링 하기 위한 고급 클라우드 모니터링 솔루션입니다. 내보낼 수 있는 로그 및 메트릭 목록은 [내보내기에 대 한 진단 원격 분석](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#diagnostic-telemetry-for-export) 을 참조 하세요.

마지막으로 SQL Server에는 [쿼리 저장소](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) 및 [dmv (동적 관리 뷰)](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views)와 같이 SQL Database 및 SQL Managed Instance 활용 하는 자체 모니터링 및 진단 기능이 있습니다. 다양 한 성능 문제를 모니터링 하려면 스크립트에 대해 [dmv를 사용 하 여 모니터링](monitoring-with-dmvs.md) 을 참조 하세요.

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>Azure Portal의 모니터링 및 튜닝 기능

Azure Portal에서 Azure SQL Database 및 Azure SQL Managed Instance 리소스 메트릭 모니터링을 제공 합니다. 또한 Azure SQL Database는 데이터베이스 관리자와 쿼리 튜닝 권장 사항 및 쿼리 성능 분석을 제공 하는 Query Performance Insight 제공 합니다. 마지막으로, Azure Portal에서 [논리 SQL server](logical-servers.md) 와 해당 단일 및 풀링된 데이터베이스에 대해 자동을 사용 하도록 설정할 수 있습니다.

### <a name="azure-sql-database-and-azure-sql-managed-instance-resource-monitoring"></a>Azure SQL Database 및 Azure SQL Managed Instance 리소스 모니터링

**메트릭** 보기에서 Azure Portal의 다양 한 리소스 메트릭을 신속 하 게 모니터링할 수 있습니다. 이러한 메트릭을 사용 하면 데이터베이스가 프로세서, 메모리 또는 IO 리소스의 100%에 도달 하 고 있는지 확인할 수 있습니다. 높은 DTU 또는 프로세서 비율과 높은 IO 백분율은 워크 로드에 더 많은 CPU 또는 IO 리소스가 필요할 수 있음을 나타냅니다. 최적화 해야 하는 쿼리를 나타낼 수도 있습니다.

  ![리소스 메트릭](./media/monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors-in-azure-sql-database"></a>Azure SQL Database의 데이터베이스 관리자

Azure SQL Database는 단일 및 풀링된 데이터베이스에 대 한 성능 튜닝 권장 사항을 제공 하는 [데이터베이스](database-advisor-implement-performance-recommendations.md) 관리자를 포함 합니다. 이러한 권장 사항은 [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseadvisor)사용 뿐만 아니라 Azure Portal 에서도 사용할 수 있습니다. [자동 조정](automatic-tuning-overview.md) 기능을 사용 하도록 설정 하 여 Azure SQL Database 이러한 튜닝 권장 사항을 자동으로 구현할 수 있습니다.

### <a name="query-performance-insight-in-azure-sql-database"></a>Azure SQL Database Query Performance Insight

[Query Performance Insight](query-performance-insight-use.md) 는 단일 및 풀링된 데이터베이스에 대해 가장 많이 사용 되 고 가장 오래 실행 되는 쿼리의 Azure Portal 성능을 보여 줍니다.

## <a name="generate-intelligent-assessments-of-performance-issues"></a>성능 문제에 대 한 지능형 평가 생성

Azure SQL Database 및 Azure SQL Managed Instance [Intelligent Insights](intelligent-insights-overview.md) 는 기본 제공 인텔리전스를 사용 하 여 인공 지능을 통해 데이터베이스 사용을 지속적으로 모니터링 하 고 성능 저하를 야기 하는 중단 이벤트를 검색 합니다. Intelligent Insights는 쿼리 실행 대기 시간, 오류 또는 제한 시간을 기준으로 데이터베이스의 성능 문제를 자동으로 검색 합니다. 검색 되 면 [문제를 지능적으로 평가](intelligent-insights-troubleshoot-performance.md)하 여 리소스 로그 (SQLInsights 라고 함)를 생성 하는 자세한 분석이 수행 됩니다. 이 평가는 데이터베이스 성능 문제의 근본 원인 분석과 성능 개선을 위한 가능한 권장 사항이 있는 위치로 구성되어 있습니다.

Intelligent Insights는 다음 값을 제공하는 Azure의 기본 제공 인텔리전스의 고유 기능입니다.

- 사전 모니터링
- 맞춤형 성능 정보
- 데이터베이스 성능 저하의 초기 검색
- 검색된 문제의 근본 원인 분석
- 성능 개선 권장 사항
- 수십만 개의 데이터베이스에서 기능 확장
- DevOps 리소스와 총 소유 비용에 미치는 긍정적인 영향

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>메트릭 및 리소스 로그의 스트리밍 내보내기를 사용 하도록 설정

Intelligent Insights 리소스 로그를 포함 하 여 여러 대상 중 하나로 [진단 원격 분석의 스트리밍 내보내기를](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) 사용 하도록 설정 하 고 구성할 수 있습니다. [SQL 분석](../../azure-monitor/insights/azure-sql.md) 및 기타 기능을 사용 하 여이 추가 진단 원격 분석을 사용 하 여 성능 문제를 식별 하 고 해결 합니다.

단일 데이터베이스, 풀링된 데이터베이스, 탄력적 풀, 관리 되는 인스턴스 및 인스턴스 데이터베이스에 대 한 메트릭 및 리소스 로그의 범주를 다음 Azure 리소스 중 하나로 스트리밍하려면 진단 설정을 구성 합니다.

### <a name="log-analytics-workspace-in-azure-monitor"></a>Azure Monitor Log Analytics 작업 영역

[Azure Monitor에서 Log Analytics 작업 영역](../../azure-monitor/platform/resource-logs-collect-workspace.md)으로 메트릭 및 리소스 로그를 스트리밍할 수 있습니다. 여기에서 스트리밍된 데이터는 성능 보고서, 경고 및 완화 권장 사항을 포함 하는 데이터베이스에 대 한 지능형 모니터링을 제공 하는 클라우드 전용 모니터링 솔루션인 [SQL Analytics](../../azure-monitor/insights/azure-sql.md)에서 사용할 수 있습니다. Log Analytics 작업 영역으로 스트리밍되는 데이터는 수집 된 다른 모니터링 데이터를 사용 하 여 분석할 수 있으며 경고 및 시각화와 같은 다른 Azure Monitor 기능을 활용할 수도 있습니다.

### <a name="azure-event-hubs"></a>Azure Event Hubs

[Azure Event Hubs](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)에 대 한 메트릭 및 리소스 로그를 스트리밍할 수 있습니다. 진단 원격 분석을 event hubs로 스트리밍 하 여 다음 기능을 제공 합니다.

- **타사 로깅 및 원격 분석 시스템으로 로그 스트림**

  모든 메트릭 및 리소스 로그를 단일 이벤트 허브로 스트림 하 여 로그 데이터를 타사 SIEM 또는 log analytics 도구로 파이프 합니다.
- **사용자 지정 원격 분석 및 로깅 플랫폼 빌드**

  Event hubs의 확장성이 뛰어난 게시-구독 특성을 통해 메트릭 및 리소스 로그를 사용자 지정 원격 분석 플랫폼으로 유연 하 게 수집할 수 있습니다. 자세한 내용은 [Event Hubs Azure에서 글로벌 확장 원격 분석 플랫폼 디자인 및 크기 조정](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) 을 참조 하세요.
- **Power BI로 데이터를 스트리밍하 여 서비스 상태 보기**

  Event Hubs, Stream Analytics 및 Power BI를 사용 하 여 Azure 서비스에서 진단 데이터를 거의 실시간 정보로 변환 합니다. 이 솔루션에 대 한 자세한 내용은 [Stream Analytics 및 Power BI: 스트리밍 데이터에 대 한 실시간 분석 대시보드를](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard) 참조 하세요.

### <a name="azure-storage"></a>Azure Storage

[Azure Storage](../../azure-monitor/platform/resource-logs-collect-storage.md)에 대 한 스트림 메트릭 및 리소스 로그입니다. Azure storage를 사용 하 여 이전의 두 스트리밍 옵션에 대 한 비용을 절감 하기 위해 방대한 양의 진단 원격 분석을 보관 합니다.

## <a name="use-extended-events"></a>확장 이벤트 사용 

또한 고급 모니터링 및 문제 해결을 위해 SQL Server [확장 이벤트](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) 를 사용할 수 있습니다. 사용자는 확장 이벤트 아키텍처를 사용 하 여 문제를 해결 하거나 성능 문제를 식별 하는 데 필요한 만큼의 데이터를 수집할 수 있습니다. Azure SQL Database에서 확장 이벤트를 사용 하는 방법에 대 한 자세한 내용은 [Azure SQL Database 확장 이벤트](xevent-db-diff-from-svr.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- 단일 및 풀링된 데이터베이스에 대 한 지능형 성능 권장 사항에 대 한 자세한 내용은 [Database advisor 성능 권장 사항](database-advisor-implement-performance-recommendations.md)을 참조 하세요.
- 자동화된 진단을 사용하여 데이터베이스 성능을 자동으로 모니터링하는 방법 및 성능 문제의 근본 원인 분석에 대한 자세한 내용은 [Azure SQL Intelligent Insights](intelligent-insights-overview.md)를 참조하세요.
