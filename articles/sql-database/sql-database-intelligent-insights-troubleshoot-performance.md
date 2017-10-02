---
title: "Intelligent Insights를 사용하여 Azure SQL Database 성능 문제 해결 | Microsoft Docs"
description: "Intelligent Insights는 Azure SQL Database 성능 문제를 해결하는 데 도움이 됩니다."
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
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: edd8c526a681c9cd5226ede6110f21c3362aaef6
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---
# <a name="troubleshoot-azure-sql-database-performance-issues-with-intelligent-insights"></a>Intelligent Insights를 사용하여 Azure SQL Database 성능 문제 해결

이 페이지에서는 [Intelligent Insights](sql-database-intelligent-insights.md) 데이터베이스 성능 진단 로그를 통해 감지되는 Azure SQL Database 성능 문제에 대한 정보를 제공합니다. 이 진단 로그는 사용자 지정 DevOps 경고 및 보고 기능을 위해 [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md), [Azure Event Hub](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md), [Azure Storage](sql-database-metrics-diag-logging.md#stream-into-azure-storage) 또는 타사 솔루션에 보낼 수 있습니다.

> [!NOTE]
> Intelligent Insights를 통한 빠른 Azure SQL Database 성능 문제 해결 가이드는 이 문서의 [권장되는 문제 해결 흐름](sql-database-intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow) 순서도를 참조하세요.
>

## <a name="detectable-database-performance-patterns"></a>검색 가능한 데이터베이스 성능 패턴

Intelligent Insights는 쿼리 실행 대기 시간, 오류 또는 시간 제한에 기반하여 Azure SQL Database의 성능 문제를 자동으로 감지하여 검색된 성능 패턴을 진단 로그에 출력합니다. 검색 가능한 성능 패턴은 다음 표에 요약되어 있습니다.

| 검색 가능한 성능 패턴 | 출력되는 세부 정보 |
| :------------------- | ------------------- |
| [리소스 제한에 도달](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) | 모니터링된 구독에서 사용할 수 있는 사용 가능한 리소스(DTU), 데이터베이스 작업자 스레드 또는 데이터베이스 로그인 세션의 사용량이 제한에 도달하면 Azure SQL Database 성능 문제가 발생했습니다. |
| [워크로드 증가](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase) | Azure SQL Database 성능 문제로 인해 데이터베이스의 워크로드가 증가하거나 지속적으로 누적되었음이 발견되었습니다. |
| [메모리 압력](sql-database-intelligent-insights-troubleshoot-performance.md#memory-pressure) | 메모리 부여를 요청하는 작업자에서 통계적으로 중요한 시간 동안 메모리 할당을 기다리고 있거나, Azure SQL Database 성능에 영향을 주는 메모리 부여를 요청하는 작업자가 증가하여 누적되었습니다. |
| [잠금](sql-database-intelligent-insights-troubleshoot-performance.md#locking) | Azure SQL Database 성능에 영향을 주는 과도한 데이터베이스 잠금이 발견되었습니다. |
| [MAXDOP 증가](sql-database-intelligent-insights-troubleshoot-performance.md#increased-maxdop) | MAXDOP(최대 병렬 처리 수준 옵션)이 변경되어 쿼리 실행 효율성에 영향을 주고 있습니다.  |
| [페이지 래치 경합](sql-database-intelligent-insights-troubleshoot-performance.md#pagelatch-contention) | Azure SQL Database 성능에 영향을 주는 페이지 래치 경합이 발견되었습니다. 여러 스레드에서 동일한 메모리 내 데이터 버퍼 페이지에 동시에 액세스하려고 하여 대기 시간이 늘어나고, 이로 인해 Azure SQL Database 성능에 영향을 주고 있습니다. |
| [인덱스 누락](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index) | Azure SQL Database 성능에 영향을 주는 인덱스 누락 문제가 발견되었습니다. |
| [새 쿼리](sql-database-intelligent-insights-troubleshoot-performance.md#new-query) | Azure SQL Database의 전체 성능에 영향을 주는 새 쿼리가 발견되었습니다.  |
| [비정상적 대기 통계](sql-database-intelligent-insights-troubleshoot-performance.md#unusual-wait-statistic) | Azure SQL Database 성능에 영향을 주는 비정상적인 데이터베이스 대기 시간이 발견되었습니다. |
| [TempDB 경합](sql-database-intelligent-insights-troubleshoot-performance.md#tempdb-contention) | 여러 스레드에서 동일한 TempDB 리소스에 액세스하려고 하여 Azure SQL Database 성능에 영향을 주는 병목 현상이 발생하고 있습니다. |
| [탄력적 풀 DTU 부족](sql-database-intelligent-insights-troubleshoot-performance.md#elastic-pool-dtu-shortage) | 탄력적 풀에서 사용 가능한 eDTUs가 부족하여 Azure SQL Database 성능에 영향을 주고 있습니다. |
| [계획 회귀](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression) | 새 계획 또는 기존 계획의 워크로드 변경으로 인해 영향을 받는 SQL Azure SQL Database 성능이 발견되었습니다. |
| [데이터베이스 범위 구성 값 변경](sql-database-intelligent-insights-troubleshoot-performance.md#database-scoped-configuration-value-change) | 데이터베이스의 구성 변경으로 인해 Azure SQL Database 성능에 영향을 주고 있습니다. |
| [느린 클라이언트](sql-database-intelligent-insights-troubleshoot-performance.md#slow-client) | 느린 응용 프로그램 클라이언트에서 Azure SQL Database의 출력을 충분히 빨리 사용할 수 없어 영향을 받는 Azure SQL Database 성능이 발견되었습니다. |
| [가격 책정 계층 다운그레이드](sql-database-intelligent-insights-troubleshoot-performance.md#pricing-tier-downgrade) | 가격 책정 계층 다운그레이드 작업으로 인해 Azure SQL Database 성능에 영향을 주는 사용 가능한 리소스가 감소되었습니다. |

> [!TIP]
> Azure SQL Database 성능을 지속적으로 최적화하려면 [Azure SQL Database 자동 튜닝](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-automatic-tuning)을 사용하는 것이 좋습니다. 이는 Azure SQL Database를 지속적으로 모니터링하고, 인덱스를 자동으로 튜닝하고, 쿼리 실행 계획 수정 사항을 적용하는 Azure SQL의 고유한 기본 제공 인텔리전스 기능입니다.
>

다음 섹션에서는 앞에서 나열한 검색 가능한 성능 패턴에 대해 자세히 설명합니다.

## <a name="reaching-resource-limits"></a>리소스 제한에 도달

### <a name="what-is-happening"></a>설명

이 검색 가능한 성능 패턴은 사용 가능한 리소스 제한, 작업자 제한 및 세션 제한에 도달하는 것과 관련된 성능 문제를 결합합니다. 이 성능 문제가 감지되면 진단 로그의 설명 필드에서 성능 문제가 리소스, 작업자 또는 세션 제한과 관련되는지를 나타냅니다.

Azure SQL Database의 리소스는 일반적으로 CPU 및 I/O(데이터 및 트랜잭션 로그 I/O) 리소스가 복합된 측정값으로 구성되는 [DTU 리소스](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-what-is-a-dtu)라고 합니다. 리소스 제한 도달 패턴은 측정된 리소스 제한 중 하나에 도달하여 발견된 쿼리 성능 저하가 발생할 때 인식됩니다.

세션 제한 리소스는 Azure SQL Database에 사용할 수 있는 동시 로그인 수를 나타냅니다. 이 성능 패턴은 Azure SQL Database에 연결하는 응용 프로그램이 데이터베이스에 사용 가능한 동시 로그인 수에 도달한 경우에 인식됩니다. 응용 프로그램이 데이터베이스에서 사용할 수 있는 것보다 많은 세션을 사용하려고 하면 쿼리 성능에 영향을 줍니다.

사용 가능한 작업자는 DTU 사용량에 포함되지 않으므로 작업자 제한에 도달하는 것은 리소스 제한 도달과 관련된 경우입니다. 데이터베이스에서 작업자 제한에 도달하면 리소스별 대기 시간이 증가하여 쿼리 성능이 저하될 수 있습니다.

### <a name="troubleshooting"></a>문제 해결

진단 로그는 성능 및 리소스 사용률에 영향을 준 쿼리의 쿼리 해시를 출력합니다. 이 정보는 데이터베이스 워크로드를 최적화하기 위한 시작점으로 사용할 수 있습니다. 특히 성능 저하에 영향을 주는 쿼리를 최적화하거나, 인덱스를 추가하거나, 더욱 균등한 워크로드 배포로 응용 프로그램을 최적화하는 것이 좋습니다. 워크로드를 줄이거나 최적화할 수 없는 경우 Azure SQL Database 구독의 가격 책정 계층을 높여 사용 가능한 리소스의 양을 늘리는 것이 좋습니다.

사용 가능한 세션 제한에 도달한 경우 데이터베이스에 대한 로그인 수를 줄이는 관점에서 응용 프로그램을 최적화하는 것이 좋습니다. 응용 프로그램에서 데이터베이스에 대한 로그인 수를 줄일 수 없는 경우 데이터베이스의 가격 책정 계층을 높이거나 부하 분산된 워크로드 배포를 위해 데이터베이스를 여러 데이터베이스로 분할 및 이동하는 하는 것이 좋습니다.

세션 제한 해결에 대한 자세한 제안 사항은 [Azure SQL Database 최대 로그인 수 제한을 처리하는 방법(영문)](https://blogs.technet.microsoft.com/latam/2015/06/01/how-to-deal-with-the-limits-of-azure-sql-database-maximum-logins/)을 참조하세요. 구독 계층에 사용할 수 있는 리소스 제한을 확인하려면 [Azure SQL Database 리소스 제한](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-resource-limits)을 참조하세요.

## <a name="workload-increase"></a>워크로드 증가

### <a name="what-is-happening"></a>설명

이 성능 패턴은 워크로드 증가 또는 매우 심각한 형태의 워크로드 누적 문제를 식별합니다.

이에 따라 여러 메트릭을 조합하여 검색을 수행합니다. 측정되는 기본 메트릭은 과거 워크로드 기준과 비교하여 워크로드의 증가를 검색합니다. 다른 형태의 검색은 쿼리 성능에 영향을 줄 만큼 심각한 대량의 활성 작업자 스레드 증가를 측정하는 것을 기반으로 합니다.

Azure SQL Database에서 워크로드를 처리할 수 없기 때문에 워크로드가 계속 누적되는 매우 심각한 형태의 경우입니다. 결과적으로 워크로드 크기, 즉 워크로드 누적 상태가 지속적으로 증가합니다. 이 상태로 인해 시간 워크로드에서 실행이 증가하기를 대기하고 있고, 이는 가장 심각한 데이터베이스 성능 문제 중 하나를 나타냅니다. 이 문제는 중단된 작업자 스레드 수의 증가를 모니터링하여 검색됩니다. 

### <a name="troubleshooting"></a>문제 해결

진단 로그는 실행이 증가한 쿼리의 수 및 워크로드 증가에 가장 많이 기여한 쿼리의 쿼리 해시를 출력합니다. 이 정보는 워크로드를 최적화하기 위한 시작점으로 사용할 수 있으며, 특히 워크로드 증가에 대한 가장 큰 기여자로 식별된 쿼리에 사용할 수 있습니다.

데이터베이스에 워크로드를 더 균등하게 분산하는 것이 좋습니다. 또한 성능에 영향을 주는 쿼리를 최적화하고 인덱스를 추가하는 것이 좋습니다. 고려해야 할 또 다른 방법은 여러 데이터베이스 간에 워크로드를 분산하는 것입니다. 이 방법을 사용할 수 없는 경우 Azure SQL Database 구독의 가격 책정 계층을 높여 사용 가능한 리소스의 양을 늘리는 것이 좋습니다.

## <a name="memory-pressure"></a>메모리 압력

### <a name="what-is-happening"></a>설명

이 성능 패턴은 메모리 압력으로 인해 현재 데이터베이스 성능이 저하되었거나 지난 7일 간의 성능 기준과 비교하여 매우 심각한 메모리 누적 상태에 있음을 나타냅니다.

메모리 압력은 Azure SQL Database에서 메모리 부여를 요청하는 작업자 스레드가 매우 많이 있는 성능 상태를 나타냅니다. 이로 인해 Azure SQL Database에서 메모리를 요청하는 모든 작업자에 메모리를 효율적으로 할당할 수 없는 높은 메모리 사용률 상태가 발생합니다. 이 문제의 가장 일반적인 이유 중 하나는 Azure SQL Database에서 사용할 수 있는 메모리 양과 관련이 있는 한편, 작업자 스레드와 메모리 압력이 증가하는 워크로드가 증가한다는 것입니다.

더 심각한 메모리 압력은 메모리를 해제하는 쿼리의 수보다 메모리 부여를 요청하는 작업자 스레드의 수가 더 많음을 나타내는 메모리 누적 상태입니다. 엔진에서 요구 사항이 충족될 만큼 효율적으로 메모리를 할당할 수 없기 때문에 메모리 부여를 요청하는 이 작업자 스레드의 수는 지속적으로 증가하고 있습니다(즉, 누적되고 있음). 메모리 누적 상태는 가장 심각한 데이터베이스 성능 문제 중 하나를 나타냅니다.

### <a name="troubleshooting"></a>문제 해결

진단 로그는 높은 메모리 사용량 및 관련 타임스탬프에 대한 가장 큰 이유로 표시된 클럭(즉, 작업자 스레드)과 함께 메모리 개체 저장소 세부 정보를 출력합니다. 이 정보는 문제 해결의 기초로 사용할 수 있습니다. 

가장 높은 메모리 사용량이 있는 클럭과 관련된 쿼리를 최적화하거나 제거하는 것이 좋습니다. 사용하지 않을 데이터를 쿼리하지 않는 것도 좋습니다. 쿼리에서 항상 WHERE 절을 사용하는 것이 좋습니다. 또한 데이터를 검색하는 대신 클러스터되지 않은 인덱스를 만들어 데이터를 검색하는 것이 좋습니다.

또한 워크로드를 줄이거나 여러 데이터베이스를 통해 최적화하거나 배포할 수도 있습니다. 고려해야 할 또 다른 방법은 여러 데이터베이스 간에 워크로드를 분산하는 것입니다. 이 방법을 사용할 수 없는 경우 Azure SQL Database 구독의 가격 책정 계층을 높여 데이터베이스에서 사용 가능한 메모리 리소스의 양을 늘리는 것이 좋습니다.

문제 해결에 대한 자세한 제안 사항은 [메모리 부여에 대한 명상: 다양한 이름으로 정체를 알 수 없는 SQL Server 메모리 소비자(영문)](https://blogs.msdn.microsoft.com/sqlmeditation/2013/01/01/memory-meditation-the-mysterious-sql-server-memory-consumer-with-many-names/)를 참조하세요.

## <a name="locking"></a>잠금

### <a name="what-is-happening"></a>설명

이 성능 패턴은 지난 7일 간의 성능 기준과 비교하여 과도한 데이터베이스 잠금이 감지된 현재 데이터베이스의 성능 저하를 나타냅니다. 

최신 RDBMS 잠금은 여러 동시 작업자와 가능한 경우 병렬 데이터베이스 트랜잭션을 실행하여 성능이 최대화되는 다중 스레드 시스템을 구현하는 데 필수적입니다. 간단히 말해, 이 컨텍스트의 잠금은 단일 트랜잭션만 단독으로 필요한 행, 페이지, 테이블 및 파일에 액세스할 수 있고, 리소스에 대한 다른 트랜잭션과 경쟁하지 않는 기본 제공 액세스 메커니즘을 의미합니다. 사용할 리소스를 잠근 트랜잭션이 완료되면 해당 리소스에 대한 잠금이 해제되어 다른 트랜잭션에서 필요한 리소스에 액세스할 수 있습니다. 잠금에 대한 자세한 내용은 [데이터베이스 엔진에서의 잠금](https://msdn.microsoft.com/library/ms190615.aspx)을 참조하세요.

SQL 엔진에서 실행되는 트랜잭션에서 잠긴 리소스에 액세스하여 사용하는 데 오랫동안 대기하는 경우 이 대기 시간은 워크로드 실행 성능의 저하에 영향을 줍니다. 

### <a name="troubleshooting"></a>문제 해결

진단 로그는 문제 해결의 기초로 사용할 수 있는 잠금 세부 정보를 출력합니다. 보고된 차단 쿼리, 즉 잠금 성능 저하가 발생하는 쿼리를 분석하여 제거하는 것이 좋습니다. 경우에 따라 차단 쿼리를 성공적으로 최적화할 수 있습니다.
이 문제를 완화하는 가장 간단하고 안전한 방법은 트랜잭션을 짧게 유지하고 비용이 가장 많이 드는 쿼리의 잠금 범위를 좁히는 것입니다. 대규모 일괄 처리 작업을 더 작은 작업으로 분할하는 것이 좋습니다. 올바른 방법은 쿼리를 최대한 효율적으로 만들어 쿼리 잠금 범위를 좁히는 것이 좋습니다. 교착 상태가 발생할 가능성이 높아지고 전체 데이터베이스 성능에 부정적인 영향을 줄 수 있으므로 대량 스캔을 줄이는 것이 좋습니다. 잠금을 유발하는 식별된 쿼리의 경우 새 인덱스를 만들거나 기존 인덱스에 열을 추가하여 테이블 스캔을 방지하는 것이 좋습니다. 자세한 제안 사항은 [SQL Server 잠금 에스컬레이션으로 인해 발생하는 차단 문제를 해결하는 방법](https://support.microsoft.com/en-us/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in)을 참조하세요.

## <a name="increased-maxdop"></a>MAXDOP 증가

### <a name="what-is-happening"></a>설명

이 검색 가능한 성능 패턴은 선택한 쿼리 실행 계획이 예상보다 더 많이 병렬화되었다는 상태를 나타냅니다. Azure SQL Database 쿼리 최적화 프로그램은 가능한 경우 작업 속도를 높이기 위해 쿼리를 병렬로 실행하도록 결정함으로써 워크로드 성능을 향상시킵니다. 그러나 동일한 쿼리를 더 적은 수의 병렬 작업자 또는 경우에 따라 단일 작업자 스레드로 실행하는 것과 비교해 볼 때, 쿼리를 처리하는 병렬 작업자에서 결과를 동기화하고 병합하기 위해 서로 대기하는 데 더 많은 시간을 소비하는 경우가 있습니다.

쿼리 실행 계획이 예상보다 더 많이 병렬화되어야 하므로 전문가 시스템에서 기준 기간과 비교하여 현재 데이터베이스 성능을 분석하고 이전에 실행 중인 쿼리가 이전보다 더 느리게 실행되는지 확인합니다.

Azure SQL Database의 MAXDOP 서버 구성 옵션은 동일한 쿼리를 병렬로 실행하는 데 사용할 수 있는 CPU 코어 수를 제어하는 데 사용됩니다. 

### <a name="troubleshooting"></a>문제 해결

진단 로그는 예상보다 더 많이 병렬화됨으로써 실행 기간이 증가한 쿼리와 관련된 쿼리 해시를 출력합니다. 또한 CXP 대기 시간도 출력합니다. 이 시간은 단일 구성 도우미/코디네이터 스레드(스레드 0)에서 결과를 병합하고 예정대로 진행하기 전에 다른 모든 스레드가 완료될 때까지 대기하는 시간을 나타냅니다. 또한 진단 로그는 성능 저하 쿼리가 실행 전체에서 대기한 대기 시간도 출력합니다. 이 정보는 문제 해결의 기초가 됩니다.

먼저 복잡한 쿼리를 최적화하거나 단순화할 수 있는 가능성을 확인하는 것이 좋습니다. 시간이 오래 걸리는 일괄 처리 작업을 더 작은 작업으로 분할하는 것이 좋습니다. 또한 쿼리를 지원하는 인덱스가 만들어졌는지 확인합니다. 성능 저하 플래그가 지정된 특정 쿼리에 대해 MAXDOP(최대 병렬 처리 수준)를 수동으로 적용하는 것도 고려할 수 있습니다. 이 작업은 T-SQL을 사용하여 구성되며, [max degree of parallelism 서버 구성 옵션 구성](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option)을 참조하세요.

MAXDOP 서버 구성 옵션을 기본값인 0으로 설정하면 Azure SQL Database에서 사용 가능한 모든 논리적 CPU 코어를 사용하여 단일 쿼리를 실행하는 스레드를 병렬화할 수 있음을 나타냅니다. MAXDOP를 1로 설정하면 단일 쿼리 실행에 하나의 코어만 사용할 수 있음을 나타내며, 실제로 병렬 처리가 사용되지 않음을 의미합니다. 사례 기준별 사례, 데이터베이스에 사용 가능한 코어 및 진단 로그 정보에 따라 MAXDOP 옵션을 해당 문제를 해결한 병렬 쿼리 실행에 대한 코어 수로 조정하는 것이 좋습니다.

## <a name="pagelatch-contention"></a>페이지 래치 경합

### <a name="what-is-happening"></a>설명

이 성능 패턴은 지난 7일 간의 워크로드 기준과 비교하여 페이지 래치 경합으로 인해 발생하는 현재 데이터베이스의 워크로드 성능 저하를 나타냅니다.

래치는 다중 스레딩을 사용할 수 있게 하고 인덱스, 데이터 페이지 및 기타 내부 구조를 포함한 메모리 내 구조의 일관성을 보장하기 위해 Azure SQL Database에서 사용되는 경량 동기화 메커니즘입니다.

Azure SQL Database에서 사용할 수 있는 다양한 유형의 래치가 있습니다. 단순성을 위해 버퍼 래치는 버퍼 풀의 메모리 내 페이지를 보호하는 데 사용되고, I/O 래치는 아직 버퍼 풀에 로드되지 않은 페이지를 보호하는 데 사용됩니다. 버퍼 풀의 페이지에서 데이터를 쓰거나 읽을 때마다 작업자 스레드에서 먼저 페이지에 대한 버퍼 래치를 얻어야 합니다. 작업자 스레드가 메모리 내 버퍼 풀에서 이미 사용할 수 없는 페이지에 액세스하려고 할 때마다 I/O 요청은 저장소에서 필요한 정보를 로드하여 매우 심각한 형태의 성능 저하를 나타냅니다.

여러 스레드가 동일한 메모리 내 구조에서 래치를 동시에 얻으려고 하여 쿼리 실행에 대한 대기 시간이 증가할 때 페이지 래치 경합이 발생합니다. 저장소에서 데이터에 액세스해야 하는 페이지 래치 I/O 경합이 발생하는 경우 이 대기 시간이 훨씬 길어 워크로드 성능에 상당한 영향을 줄 수 있습니다. 페이지 래치 경합은 서로를 대기하며 여러 CPU 시스템에서 리소스를 대상으로 경쟁하는 가장 일반적인 시나리오입니다.

### <a name="troubleshooting"></a>문제 해결

진단 로그는 문제 해결의 기초로 사용할 수 있는 페이지 래치 경합 세부 정보를 출력합니다.

페이지 래치는 Azure SQL Database의 내부 제어 메커니즘이므로 세부 정보를 사용할 시기를 자동으로 결정합니다. 스키마 디자인을 포함한 응용 프로그램 결정은 래치의 결정적 동작으로 인해 페이지 래치 동작에 영향을 줄 수 있습니다.
래치 경합을 처리하는 한 가지 방법은 순차적 인덱스 키를 순차적이지 않은 키로 바꿔 인덱스 범위에서 삽입을 균등하게 분산하는 것입니다. 일반적으로 이 작업은 인덱스에 워크로드를 비례적으로 배포하는 선행 열을 배치함으로써 수행됩니다. 고려해야 할 다른 방법은 테이블 분할입니다. 과도한 래치 경합을 완화하기 위한 일반적인 방법은 분할된 테이블에 계산 열이 있는 해시 분할 스키마를 만드는 것입니다. 페이지 래치 I/O 경합이 발생하는 경우 인덱스를 사용하는 것이 이 성능 문제를 완화하는 데 유용합니다. 자세한 내용은 [SQL Server에서 래치 경합 진단 및 해결](http://download.microsoft.com/download/B/9/E/B9EDF2CD-1DBF-4954-B81E-82522880A2DC/SQLServerLatchContention.pdf)(PDF 다운로드)을 참조하세요.

## <a name="missing-index"></a>인덱스 누락

### <a name="what-is-happening"></a>설명

이 성능 패턴은 지난 7일 간의 워크로드 기준과 비교하여 인덱스 누락으로 인해 발생하는 현재 데이터베이스의 워크로드 성능 저하를 나타냅니다.

인덱스는 쿼리 성능의 속도를 향상시키는 데 사용됩니다. 방문하거나 검색해야 하는 데이터 집합 페이지의 수를 줄여 테이블 데이터에 빠르게 액세스할 수 있습니다.

성능 저하를 초래하는 특정 쿼리는 성능에 도움이 되는 인덱스를 만드는 이 검색을 통해 식별됩니다.

### <a name="troubleshooting"></a>문제 해결

진단 로그는 워크로드 성능에 영향을 주는 것으로 식별된 쿼리의 쿼리 해시를 출력합니다. 이러한 쿼리에 대한 인덱스를 작성하는 것이 좋습니다. 필요하지 않은 경우 이러한 쿼리를 최적화하거나 제거하는 것도 좋습니다. 성능을 위해 사용하지 않는 데이터를 쿼리하지 않도록 방지하는 것이 좋습니다.

> [!TIP]
> 기본 제공 Azure 인텔리전스를 통해 데이터베이스에 대한 성능 지수를 자동으로 관리할 수 있다는 것을 알고 계신가요?
>
> Azure SQL Database 성능을 지속적으로 최적화하려면 [Azure SQL Database 자동 튜닝](sql-database-automatic-tuning.md)을 사용하는 것이 좋습니다. 이는 Azure SQL Database를 지속적으로 모니터링하고, 데이터베이스에 대한 인덱스를 자동으로 튜닝하고 만드는 Azure SQL의 고유한 기본 제공 인텔리전스 기능입니다.
>

## <a name="new-query"></a>새 쿼리

### <a name="what-is-happening"></a>설명

이 검색 가능한 성능 패턴은 성능이 저하되고 7일 간의 성능 기준과 비교하여 워크로드 성능에 영향을 주는 새로운 쿼리가 감지되었음을 나타냅니다.

때로는 적절히 수행하는 쿼리를 작성하는 것이 어려운 과제일 수 있습니다. 쿼리 작성에 대한 자세한 내용은 [SQL 쿼리 작성(영문)](https://msdn.microsoft.com/library/bb264565.aspx)을 참조하고, 기존 쿼리 성능을 최적화하려면 [쿼리 튜닝](https://msdn.microsoft.com/library/ms176005.aspx)을 참조하세요.

### <a name="troubleshooting"></a>문제 해결

진단 로그는 쿼리 해시를 포함하여 대부분 CPU를 사용하는 최대 2개의 새 쿼리를 출력합니다. 검색된 쿼리가 워크로드 성능에 영향을 주기 때문에 쿼리를 최적화하는 것이 좋습니다. 사용하지 않는 데이터는 검색하지 않는 것이 좋습니다. WHERE 절과 함께 쿼리를 사용하는 것도 좋습니다. 복잡한 쿼리를 단순화하고 더 작은 쿼리로 분할하는 것도 좋습니다. 큰 일괄 처리 쿼리를 더 작은 일괄 처리 쿼리로 분할하는 것도 좋은 방법입니다. 이 성능 문제를 완화하려면 일반적으로 새 쿼리에 대해 인덱스를 도입하는 것이 좋습니다.

[Azure SQL Database Query Performance Insight](sql-database-query-performance.md)를 사용하는 것이 좋습니다.

## <a name="unusual-wait-statistic"></a>비정상적 대기 통계

### <a name="what-is-happening"></a>설명

이 검색 가능한 성능 패턴은 지난 7일 간의 워크로드 기준과 비교하여 성능 저하 쿼리가 식별되는 워크로드 성능 저하를 나타냅니다.

이 경우 시스템에서 검색 가능한 다른 표준 성능 범주에서 성능 저하 쿼리를 분류할 수 없지만, 회귀를 담당하는 대기 통계를 검색하여 &#8220;*비정상적 대기 통계*&#8221가 있는 쿼리로 간주합니다. 여기서는 회귀를 담당하는 비정상적 대기 통계도 노출됩니다. 

### <a name="troubleshooting"></a>문제 해결

진단 로그는 비정상적 대기 시간 세부 정보, 영향을 받은 쿼리의 쿼리 해시 및 대기 시간에 대한 정보를 출력합니다.

이 경우 시스템에서 성능 저하 쿼리의 근본 원인을 성공적으로 확인할 수 없으므로 성능 저하 쿼리에 대한 진단 정보는 수동 문제 해결을 위한 좋은 시작점으로 사용할 수 있습니다. 이러한 쿼리의 성능을 최적화하는 것이 좋습니다. 일반적으로 사용하지 않는 데이터를 가져오지 않고 복잡한 쿼리를 단순화하고 더 작은 쿼리로 분할하는 것이 좋습니다. 쿼리 성능 최적화에 대한 자세한 내용은 [쿼리 튜닝](https://msdn.microsoft.com/library/ms176005.aspx)을 참조하세요.

## <a name="tempdb-contention"></a>TempDB 경합

### <a name="what-is-happening"></a>설명

이 검색 가능한 성능 패턴은 TempDB 리소스에 액세스하려는 스레드의 병목 현상이 있는 데이터베이스 성능 상태를 나타냅니다(이 상태는 I/O와 관련 없음). 이 성능 문제에 대한 일반적인 시나리오는 작은 TempDB 테이블(TempDB 테이블)을 모두 만들고, 사용하고, 삭제하는 수백 개의 동시 쿼리입니다. 시스템에서 지난 7일 간의 성능 기준과 비교하여 동일한 TempDB 테이블을 사용하는 많은 수의 동시 쿼리가 통계적으로 증가함으로써 데이터베이스 성능에 영향을 주고 있음을 감지했습니다.

### <a name="troubleshooting"></a>문제 해결

진단 로그는 성능 문제를 해결할 때 시작점으로 사용할 수 있는 TempDB 경합 세부 정보를 출력합니다. 이러한 종류의 경합을 완화하고 전체 워크로드의 처리량을 증가시키기 위해 추구해야 할 두 가지 작업이 있습니다. 임시 테이블 사용을 중지하는 것이 좋습니다. 메모리 최적화 테이블을 사용하는 것도 좋습니다. [메모리 액세스에 최적화된 테이블 소개](https://docs.microsoft.com/en-us/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables)를 참조하세요. 

## <a name="elastic-pool-dtu-shortage"></a>탄력적 풀 DTU 부족

### <a name="what-is-happening"></a>설명

이 검색 가능한 성능 패턴은 구독의 탄력적 풀에서 사용 가능한 DTU가 부족하기 때문에 지난 7일 간의 기준과 비교하여 현재 데이터베이스의 워크로드 성능이 저하되었음을 나타냅니다. 

Azure SQL Database의 리소스는 일반적으로 CPU 및 I/O(데이터 및 트랜잭션 로그 I/O) 리소스가 복합된 측정값으로 구성되는 [DTU 리소스](sql-database-what-is-a-dtu.md)라고 합니다. [Azure 탄력적 풀 리소스](sql-database-elastic-pool.md)은 크기 조정을 위해 여러 데이터베이스 간에 공유되는 사용 가능한 eDTU 리소스의 풀로 사용됩니다. 탄력적 풀에서 사용 가능한 eDTU 리소스가 충분하지 않아 풀의 모든 데이터베이스를 지원할 수 없는 경우 시스템에서 탄력적 풀 DTU 부족 성능 문제가 감지됩니다.

### <a name="troubleshooting"></a>문제 해결

진단 로그는 탄력적 풀에 대한 정보를 출력하고, 상위 DTU 사용 데이터베이스를 나열하고, 상위 DTU 사용 데이터베이스에서 사용되는 풀의 DTU 백분율을 제공합니다.

이 성능 상태는 탄력적 풀에서 동일한 eDTU 풀을 사용하는 여러 데이터베이스와 관련되므로 문제 해결 단계에서는 상위 DTU 사용 데이터베이스에 집중해야 합니다. 이러한 데이터베이스에서 가장 많이 사용되는 쿼리의 최적화를 포함하여 가장 많이 사용하는 데이터베이스의 워크로드를 줄이는 것이 좋습니다. 사용하지 않는 데이터를 쿼리하지 않는 것도 좋습니다. 또 다른 방법은 상위 DTU 사용 데이터베이스를 사용하여 응용 프로그램을 최적화하고 여러 데이터베이스 간에 워크로드를 다시 배포하는 것입니다.

상위 DTU 사용 데이터베이스에서 현재 워크로드를 줄이고 최적화할 수 없는 경우 탄력적 풀 가격 책정 계층을 높이는 것이 좋습니다. 이러한 증가는 탄력적 풀에서 사용 가능한 DTU를 늘려줍니다.

## <a name="plan-regression"></a>계획 회귀

### <a name="what-is-happening"></a>설명

이 검색 가능한 성능 패턴은 Azure SQL Database에서 최적이 아닌 쿼리 실행 계획을 활용하기 시작하는 상태를 나타냅니다. 최적이 아닌 계획은 일반적으로 쿼리 실행이 증가하여 현재 쿼리와 다른 쿼리의 대기 시간이 더 오래 걸리게 합니다.

Azure SQL Database는 쿼리 실행 비용을 최소화하는 쿼리 실행 계획을 결정합니다. 그러나 쿼리 및 워크로드 유형이 변경되면 기존 계획이 더 이상 효율적이지 않거나 Azure SQL Database에서 효율적인 평가를 수행하지 못하는 경우가 있습니다. 수정의 문제로서 쿼리 실행 계획은 수동으로 적용할 수 있습니다.

이 검색 가능한 성능 패턴은 계획 회귀의 서로 다른 세 가지 경우, 즉 새 계획 회귀, 이전 계획 회귀 및 기존 계획 변경 워크로드를 결합합니다. 특정 계획 회귀가 발생한 유형은 진단 로그의 &#8220;*세부 정보*&#8221; 속성에 제공됩니다.

새 계획 회귀 상태는 Azure SQL Database에서 이전 계획만큼 효율적이지 않은 새 쿼리 실행 계획을 실행하기 시작하는 상태를 나타냅니다. 이전 계획 회귀 상태는 Azure SQL Database에서 더 효율적인 새 계획을 사용하는 것으로부터 새 계획만큼 효율적이지 않은 이전 계획으로 전환하는 상태를 나타냅니다. 기존 계획 변경 워크로드 회귀는 두 계획, 즉 이전 계획과 새 계획이 성능 저하 계획으로 진행되는 부하를 분산하면서 지속적으로 교대로 진행되는 상태를 나타냅니다.

계획 회귀에 대한 자세한 내용은 [SQL 서버의 계획 회귀란?(영문)](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/06/09/what-is-plan-regression-in-sql-server/)을 참조하세요. 

### <a name="troubleshooting"></a>문제 해결

진단 로그는 이 상태의 성능 문제 해결을 위한 기초로 사용할 수 있는 쿼리 해시, 효율적인 계획 ID, 잘못된 계획 ID 및 쿼리 ID를 출력합니다.

제공된 쿼리 해시로 식별할 수 있는 특정 쿼리에 대해 성능을 향상시킬 수 있는 계획을 분석하는 것이 좋습니다. 쿼리에 대해 더 효율적으로 작동하는 계획을 결정하면 수동으로 적용할 수 있습니다. 자세한 내용은 [SQL Server에서 계획 회귀를 방지하는 방법(영문)](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/04/25/you-shall-not-regress-how-sql-server-2017-prevents-plan-regressions/)을 참조하세요.

> [!TIP]
> 기본 제공 Azure 인텔리전스를 통해 데이터베이스에 대한 최적 성능의 쿼리 실행 계획을 자동으로 관리할 수 있다는 것을 알고 계신가요?
>
> Azure SQL Database 성능을 지속적으로 최적화하려면 [Azure SQL Database 자동 튜닝](sql-database-automatic-tuning.md)을 사용하는 것이 좋습니다. 이는 Azure SQL Database를 지속적으로 모니터링하고, 데이터베이스에 대한 최적 성능의 쿼리 실행 계획을 자동으로 튜닝하고 만드는 Azure SQL의 고유한 기본 제공 인텔리전스 기능입니다.
>

## <a name="database-scoped-configuration-value-change"></a>데이터베이스 범위 구성 값 변경

### <a name="what-is-happening"></a>설명

이 검색 가능한 성능 패턴은 지난 7일 간의 데이터베이스 워크로드 동작과 비교하여 데이터베이스 범위 구성이 변경됨으로써 성능 회귀가 감지된 상태를 나타냅니다. 이는 최근에 변경한 데이터베이스 범위 구성이 데이터베이스 성능에 도움이 되지 않는 것을 나타냅니다.

데이터베이스 범위 구성 변경은 각 개별 데이터베이스에 대해 설정할 수 있습니다. 이 구성은 데이터베이스의 개별 성능을 최적화하기 위해 상황별로 사용됩니다. 각각의 개별 데이터베이스에 대해 MAXDOP, LEGACY_CARDINALITY_ESTIMATION, PARAMETER_SNIFFING, QUERY_OPTIMIZER_HOTFIXES 및 CLEAR PROCEDURE_CACHE와 같은 옵션을 구성할 수 있습니다.

### <a name="troubleshooting"></a>문제 해결

진단 로그는 지난 7일 간의 워크로드 동작과 비교하여 성능 저하를 초래한 데이터베이스 범위 구성 변경 내용을 출력합니다. 원하는 구성 수준에 도달할 때까지 구성 변경을 이전 값으로 되돌리거나 값 기준으로 값을 튜닝하는 것이 좋습니다. 만족할 만한 성능과 유사한 데이터베이스의 데이터베이스 범위 구성 값을 복사하는 것도 좋습니다. 성능 문제를 해결할 수 없으면 기본 Azure SQL Database의 기본값으로 되돌리고 이 기준에서 시작하여 자세히 튜닝하는 것이 좋습니다.

구성 변경에 대한 데이터베이스 범위 구성 및 T-SQL 구문을 최적화하는 방법에 대한 자세한 내용은 [데이터베이스 범위 구성 변경(Transact-SQL)](https://msdn.microsoft.com/en-us/library/mt629158.aspx)을 참조하세요.

## <a name="slow-client"></a>느린 클라이언트

### <a name="what-is-happening"></a>설명

이 검색 가능한 성능 패턴은 Azure SQL Database를 사용하는 클라이언트가 데이터베이스에서 결과를 보낼 수 있을 만큼 빠르게 데이터베이스의 출력을 사용할 수 없는 상태를 나타냅니다. Azure SQL Database는 버퍼에서 실행된 쿼리의 결과를 확인하지 않기 때문에 속도가 저하되며 진행하기 전에 클라이언트에서 전송된 쿼리 출력을 사용하기를 기다리고 있습니다. 또한 이 상태는 Azure SQL Database의 출력을 사용 중인 클라이언트로 충분히 빠르게 전송할 수 없는 느린 네트워크와 관련되었을 수도 있습니다.

이 상태는 지난 7일 간의 데이터베이스 워크로드 동작과 비교하여 성능 회귀가 감지된 경우에만 생성됩니다. 이렇게 하면 이전 성능 동작에 비해 통계적으로 중요한 성능 저하가 있는 경우에만 이 성능 문제가 감지됩니다.

### <a name="troubleshooting"></a>문제 해결

이 검색 가능한 성능 패턴은 클라이언트 쪽 응용 프로그램 또는 클라이언트 쪽 네트워크에서 필요한 문제 해결이 있는 클라이언트 쪽 상태를 나타냅니다. 진단 로그는 지난 2시간 이내에 클라이언트에서 사용하기를 가장 많이 기다리는 것처럼 보이는 쿼리 해시와 대기 시간을 출력합니다. 이러한 정보는 문제 해결을 위한 기초로 사용할 수 있습니다.

이러한 쿼리를 사용하기 위해 응용 프로그램의 성능을 최적화하는 것이 좋습니다. 발생 가능한 네트워크 대기 시간 문제를 고려해야 할 수도 있습니다. 성능 저하 문제는 지난 7일 간의 성능 기준 변경에 기반한 것이므로 이 성능 회귀 이벤트를 초래할 수 있는 응용 프로그램 변경 또는 네트워크 조건 변경이 최근 기간에 있었는지 조사하는 것이 좋습니다. 

## <a name="pricing-tier-downgrade"></a>가격 책정 계층 다운그레이드

### <a name="what-is-happening"></a>설명

이 검색 가능한 성능 패턴은 Azure SQL Database의 가격 책정 계층이 다운그레이드된 상태를 나타냅니다. 데이터베이스에서 사용할 수 있는 리소스(DTU)의 감소로 인해 시스템에서 지난 7일 간의 기준으로 측정된 현재 데이터베이스 성능이 저하되었음을 감지했습니다.

또한 Azure SQL Database 구독의 가격 책정 계층이 다운그레이드된 후 단기간 내에 상위 계층으로 업그레이드된 상태가 있었을 수도 있습니다. 이는 가격 책정 계층 다운그레이드 및 업그레이드와 같이 진단 로그의 세부 정보 섹션에 출력되는 임시 성능 저하 감지를 나타냅니다.

### <a name="troubleshooting"></a>문제 해결

가격 책정 계층을 낮추고 이에 따라 Azure SQL Database에서 사용할 수 있는 DTU를 줄여 성능이 만족되면 이 시점에서 수행해야 할 작업은 아무 것도 없습니다. 가격 책정 계층을 낮춘 후 Azure SQL Database 성능에 만족하지 않는 경우 데이터베이스 워크로드를 줄이거나 가격 책정 계층을 더 높은 수준으로 높이는 것이 좋습니다.

## <a name="recommended-troubleshooting-flow"></a>권장되는 문제 해결 흐름

Intelligent Insights를 사용하여 성능 문제를 해결하는 데 권장되는 방법은 아래의 순서도를 따르세요.

Azure SQL 분석으로 이동하여 Azure Portal을 통해 Intelligent Insight에 액세스합니다. 들어오는 성능 경고를 찾아서 클릭합니다. 검색 페이지에서 진행되는 상황을 확인합니다. 제공되는 문제의 근본 원인 분석, 쿼리 텍스트, 쿼리 시간 추세 및 인시던트 진화를 관찰합니다. 성능 문제 완화에 대한 Intelligent Insights 권장 사항을 사용하여 성능 문제를 해결합니다. 

[![문제 해결 순서도](./media/sql-database-intelligent-insights/intelligent-insights-troubleshooting-flowchart.png)](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/intelligent-insight/Troubleshoot%20Azure%20SQL%20Database%20performance%20issues%20using%20Intelligent%20Insight.pdf)

> [!TIP]
> 이 순서도의 PDF 버전을 다운로드하려면 순서도를 클릭하세요.

Intelligent Insights는 일반적으로 성능 문제에 대한 근본 원인 분석을 수행하는 데 1시간이 필요합니다. Intelligent Insights에서 문제를 찾을 수 없는 경우(대부분 1시간 미만의 문제임) 및 이 문제가 중요한 경우 QDS(쿼리 데이터 저장소)를 사용하여 성능 문제에 대한 근본 원인을 수동으로 식별합니다. 자세한 내용은 [쿼리 저장소를 사용하여 성능 모니터링](https://docs.microsoft.com/en-us/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)을 참조하세요.

## <a name="next-steps"></a>다음 단계
- [Intelligent Insights](sql-database-intelligent-insights.md) 개념을 알아봅니다.
- [Intelligent Insights의 Azure SQL Database 성능 진단 로그](sql-database-intelligent-insights-use-diagnostics-log.md)를 사용합니다.
- [Azure SQL 분석을 사용하여 Azure SQL Database](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-sql)를 모니터링합니다.
- [Azure 리소스에서 로그 데이터 수집 및 소비](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)하는 방법을 알아봅니다.

