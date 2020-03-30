---
title: 모니터링 및 성능 튜닝
description: Azure SQL Database의 모니터링 및 성능 튜닝 기능 및 방법론에 대한 개요입니다.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 837d88665c1fdffe902c9c478e5d6dc65a2e402a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268731"
---
# <a name="monitoring-and-performance-tuning-in-azure-sql-database"></a>Azure SQL 데이터베이스에서 모니터링 및 성능 조정

Azure SQL Database에서 데이터베이스의 성능을 모니터링하려면 특정 서비스 계층 및 성능 수준을 선택할 때 선택한 데이터베이스 성능 수준을 기준으로 워크로드에서 사용하는 CPU 및 IO 리소스를 모니터링하는 것으로 시작합니다. 이를 위해 Azure SQL Database는 Azure 포털에서 보거나 이러한 SQL 관리 도구 중 하나인 [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) 또는 [SSMS(SSMS)](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) 중 하나를 사용하여 볼 수 있는 리소스 메트릭을 내보올 수 있습니다.

단일 데이터베이스와 풀링된 데이터베이스의 경우 Azure SQL Database는 지능형 성능 튜닝 권장 사항과 성능을 향상시키기 위한 자동 튜닝 옵션을 제공하기 위해 여러 데이터베이스 관리자를 제공합니다. 또한 쿼리 성능 인사이트는 단일 및 풀화 된 데이터베이스에 대한 대부분의 CPU 및 IO 사용을 담당하는 쿼리에 대한 세부 정보를 보여 주며 있습니다.

Azure SQL Database는 인공 지능을 지원하는 추가 모니터링 및 튜닝 기능을 제공하여 데이터베이스 및 솔루션의 문제 해결 및 성능을 극대화하는 데 도움을 줍니다. 이러한 [지능형 인사이트](sql-database-intelligent-insights.md) 및 기타 SQL Database 리소스 로그 및 메트릭의 [스트리밍 내보내기를](sql-database-metrics-diag-logging.md) 특히 [SQL Analytics를](../azure-monitor/insights/azure-sql.md)사용하여 소비 및 분석을 위한 여러 대상 중 하나에 구성하도록 선택할 수 있습니다. Azure SQL Analytics는 대규모 및 단일 보기의 여러 구독에서 모든 Azure SQL 데이터베이스의 성능을 모니터링하기 위한 고급 클라우드 모니터링 솔루션입니다. 내보낼 수 있는 로그 및 메트릭 목록은 [내보내기에 대한 진단 원격 분석을](sql-database-metrics-diag-logging.md#diagnostic-telemetry-for-export-for-azure-sql-database) 참조하세요.

마지막으로 SQL에는 SQL Server 쿼리 [저장소](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) 및 동적 [관리 보기(DMV)를](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views)사용하는 자체 모니터링 및 진단 기능이 있습니다. 스크립트에 [DMV를 사용하여 다양한](sql-database-monitoring-with-dmvs.md) 성능 문제를 모니터링하는 모니터링을 참조하십시오.

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>Azure 포털의 기능 모니터링 및 튜닝

Azure 포털에서 Azure SQL Database는 모든 배포 유형에 대한 리소스 메트릭모니터링을 제공합니다. 또한 단일 데이터베이스 및 풀링된 데이터베이스의 경우 데이터베이스 관리자 및 쿼리 성능 인사이트는 쿼리 튜닝 권장 사항 및 쿼리 성능 분석을 제공합니다. 마지막으로 Azure 포털에서 논리 서버 및 단일 및 풀린 데이터베이스에 대해 자동을 사용하도록 설정할 수 있습니다.

### <a name="sql-database-resource-monitoring"></a>SQL 데이터베이스 리소스 모니터링

메트릭 보기에서 Azure 포털에서 다음 리소스 메트릭을 빠르게 모니터링할 수 **있습니다.**

- **DTU 사용**

  데이터베이스 또는 탄력적 풀이 장기간 DTU 사용량의 100%에 도달하고 있는지 확인합니다. DTU 사용량이 높음은 워크로드에 더 많은 CPU 또는 IO 리소스가 필요할 수 있음을 나타냅니다. 최적화해야 하는 쿼리를 나타낼 수도 있습니다.
- **CPU 사용량**

  데이터베이스, 탄력적 풀 또는 관리되는 인스턴스가 오랜 기간 동안 CPU 사용량의 100%에 도달하고 있는지 확인합니다. CPU가 높음은 워크로드에 더 많은 CPU 또는 IO 리소스가 필요할 수 있음을 나타냅니다. 최적화해야 하는 쿼리를 나타낼 수도 있습니다.
- **IO 사용량**

  데이터베이스, 탄력적 풀 또는 관리 인스턴스가 기본 저장소의 IO 제한에 도달하고 있는지 확인합니다. IO 사용량이 높음은 워크로드에 더 많은 CPU 또는 IO 리소스가 필요할 수 있음을 나타냅니다. 최적화해야 하는 쿼리를 나타낼 수도 있습니다.

  ![리소스 메트릭](./media/sql-database-monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors"></a>데이터베이스 어드바이저
'Azure SQL Database에는 단일 및 풀링된 데이터베이스에 대한 성능 조정 권장 사항을 제공하는 [데이터베이스 조언자가](sql-database-advisor.md) 포함되어 있습니다. 이러한 권장 사항은 Azure 포털뿐만 아니라 [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseadvisor)을 사용하여 사용할 수 있습니다. SQL Database에서 이러한 튜닝 권장 사항을 자동으로 구현할 수 있도록 [자동 튜닝을](sql-database-automatic-tuning.md) 활성화할 수도 있습니다.

### <a name="query-performance-insight"></a>쿼리

[쿼리 성능 인사이트는](sql-database-query-performance.md) 단일 및 풀린 데이터베이스에 대한 최상위 소비 및 가장 긴 실행 쿼리의 Azure 포털에서 성능을 보여 주며, 가장 긴 실행 중인 쿼리입니다.

## <a name="generate-intelligent-assessments-of-performance-issues"></a>성능 문제에 대한 지능적인 평가 생성

Azure SQL Database [Intelligent Insights는](sql-database-intelligent-insights.md) 기본 제공 인텔리전스를 사용하여 인공 지능을 통해 데이터베이스 사용량을 지속적으로 모니터링하고 성능 저하를 유발하는 파괴적인 이벤트를 감지합니다. Intelligent Insights는 쿼리 실행 대기 시간, 오류 또는 시간 시간을 기반으로 Azure SQL Database의 데이터베이스에서 성능 문제를 자동으로 감지합니다. 감지되면 문제에 대한 [지능적인 평가를](sql-database-intelligent-insights-troubleshoot-performance.md)통해 리소스 로그(SQLInsights라고 함)를 생성하는 자세한 분석이 수행됩니다. 이 평가는 데이터베이스 성능 문제의 근본 원인 분석과 성능 개선을 위한 가능한 권장 사항이 있는 위치로 구성되어 있습니다.

Intelligent Insights는 다음 값을 제공하는 Azure의 기본 제공 인텔리전스의 고유 기능입니다.

- 사전 모니터링
- 맞춤형 성능 정보
- 데이터베이스 성능 저하의 초기 검색
- 검색된 문제의 근본 원인 분석
- 성능 개선 권장 사항
- 수십만 개의 데이터베이스에서 기능 확장
- DevOps 리소스와 총 소유 비용에 미치는 긍정적인 영향

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>메트릭 및 리소스 로그의 스트리밍 내보내기 활성화

지능형 Insights 리소스 로그를 비롯한 여러 대상 중 하나에 [대한 진단 원격 분석의 스트리밍 내보내기를](sql-database-metrics-diag-logging.md) 활성화하고 구성할 수 있습니다. [SQL 분석](../azure-monitor/insights/azure-sql.md) 및 기타 기능을 사용하여 이 추가 진단 원격 분석을 사용하여 성능 문제를 식별하고 해결합니다.

단일 데이터베이스, 풀된 데이터베이스, 탄력적 풀, 관리되는 인스턴스 및 인스턴스 데이터베이스에 대한 메트릭 및 리소스 로그의 범주를 다음 Azure 리소스 중 하나로 스트리밍하도록 진단 설정을 구성합니다.

### <a name="log-analytics-workspace-in-azure-monitor"></a>Azure 모니터의 로그 분석 작업 영역

메트릭 및 리소스 로그를 Azure [모니터의 로그 분석 작업 영역으로](../azure-monitor/platform/resource-logs-collect-workspace.md)스트리밍할 수 있습니다. 여기서 스트리밍된 데이터는 성능 보고서, 경고 및 완화 권장 사항을 포함하는 데이터베이스를 지능적으로 모니터링하는 클라우드 전용 모니터링 솔루션인 [SQL Analytics에서](../azure-monitor/insights/azure-sql.md)사용할 수 있습니다. Log Analytics 작업 영역으로 스트리밍되는 데이터는 수집된 다른 모니터링 데이터로 분석할 수 있으며 경고 및 시각화와 같은 다른 Azure Monitor 기능을 활용할 수도 있습니다.

### <a name="azure-event-hubs"></a>Azure Event Hubs

메트릭 및 리소스 로그를 [Azure 이벤트 허브로](../azure-monitor/platform/resource-logs-stream-event-hubs.md)스트리밍할 수 있습니다. 다음 기능을 제공하기 위해 이벤트 허브에 대한 스트리밍 진단 원격 분석:

- **제3자 로깅 및 원격 분석 시스템으로 로그 스트리밍**

  모든 메트릭 및 리소스 로그를 단일 이벤트 허브로 스트리밍하여 로그 데이터를 타사 SIEM 또는 로그 분석 도구로 파이프합니다.
- **사용자 지정 원격 분석 및 로깅 플랫폼 구축**

  이벤트 허브의 확장성이 뛰어난 게시-구독 특성을 통해 사용자 지정 원격 분석 플랫폼에 메트릭 및 리소스 로그를 유연하게 통합할 수 있습니다. 자세한 내용은 [Azure 이벤트 허브에서 전역 규모의 원격 분석 플랫폼 설계 및 크기 조정을](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) 참조하십시오.
- **데이터를 Power BI로 스트리밍하여 서비스 상태 보기**

  이벤트 허브, 스트림 분석 및 Power BI를 사용하여 진단 데이터를 Azure 서비스에 대한 거의 실시간 인사이트로 변환합니다. 이 솔루션에 대한 자세한 내용은 [스트리밍 데이터에 대한 실시간 분석 대시보드인 스트림 분석 및 Power BI를](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard) 참조하십시오.

### <a name="azure-storage"></a>Azure Storage

메트릭 및 리소스 로그를 [Azure 저장소로](../azure-monitor/platform/resource-logs-collect-storage.md)스트리밍합니다. Azure 저장소를 사용하여 이전 두 스트리밍 옵션의 비용의 일부에 대해 방대한 양의 진단 원격 분석을 보관합니다.

## <a name="use-extended-events-in-the-sql-database-engine"></a>SQL 데이터베이스 엔진에서 확장 이벤트 사용

또한 SQL에서 [확장 된 이벤트를](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) 사용 하 여 추가 고급 모니터링 및 문제 해결에 대 한 수 있습니다. 확장 된 이벤트 아키텍처를 사용하면 사용자가 성능 문제를 해결하거나 식별하는 데 필요한 만큼 또는 적은 데이터를 수집 할 수 있습니다. SQL Database에서 확장 이벤트를 사용하는 것에 대한 자세한 내용은 [SQL Database의 확장 이벤트를](sql-database-xevent-db-diff-from-svr.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

- 단일 및 풀풀된 데이터베이스에 대한 지능형 성능 권장 사항에 대한 자세한 내용은 [데이터베이스 관리자 성능 권장 사항을](sql-database-advisor.md)참조하십시오.
- 자동화된 진단을 사용하여 데이터베이스 성능을 자동으로 모니터링하는 방법 및 성능 문제의 근본 원인 분석에 대한 자세한 내용은 [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md)를 참조하세요.
'''''''''