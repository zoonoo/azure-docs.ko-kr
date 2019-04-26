---
title: Azure Monitor에서 azure SQL Analytics 솔루션 | Microsoft Docs
description: Azure SQL 분석 솔루션을 통해 Azure SQL Database 관리
services: log-analytics
ms.service: log-analytics
ms.custom: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: carlrab
manager: craigg
ms.date: 12/17/2018
ms.openlocfilehash: c68c278b2a7afa8287845c452e3bec5380cf05c0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60498619"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Azure SQL 분석을 사용하여 Azure SQL Database 모니터링(미리 보기)

![Azure SQL 분석 기호](./media/azure-sql/azure-sql-symbol.png)

Azure SQL 분석은 단일 창을 통해 여러 구독 간 규모에 맞게 Azure SQL Database, Elastic Pool 및 Managed Instance의 성능을 모니터링하기 위한 고급 클라우드 모니터링 솔루션입니다. 이 솔루션은 성능 문제 해결에 대한 기본 제공 인텔리전스를 사용하여 중요한 Azure SQL Database 성능 메트릭을 수집하고 시각화합니다.

솔루션으로 수집한 메트릭을 사용하여 사용자 지정 모니터링 규칙 및 경고를 만들 수 있습니다. 솔루션은 애플리케이션 스택의 각 계층에서 문제를 식별할 수 있습니다. 단일 Log Analytics 작업 영역에서 관리 되는 인스턴스에서 모든 Azure SQL 데이터베이스, 탄력적 풀 및 데이터베이스에 대 한 데이터를 제공 하기 위해 Azure 진단 메트릭을 Azure Monitor 뷰를 함께 사용 합니다. Azure Monitor를 통해 수집, 상호 연결 및 구조적 및 비구조적 데이터를 시각화할 수 있습니다.

Azure SQL Analytics 솔루션 사용에 대한 실무 중심 개요와 일반적인 사용 시나리오에 대해서는 포함된 비디오를 참조하세요.

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>연결된 소스

Azure SQL Analytics는 단일, 풀링, Managed Instance 데이터베이스 등 Azure SQL 데이터베이스에 대한 진단 원격 분석의 스트리밍을 지원하는 클라우드 전용 모니터링 솔루션입니다. 솔루션 Azure Monitor에 연결할 에이전트를 사용 하지 않으므로, 솔루션 호스팅되는 SQL Server 온-프레미스 또는 Vm에서 모니터링을 지원, 아래 호환성 표를 참조 하지 않습니다.

| 연결된 소스 | 지원됨 | 설명 |
| --- | --- | --- |
| [Azure 진단](../platform/collect-azure-metrics-logs.md) | **예** | Azure 메트릭 및 로그 데이터는 Azure에서 직접 Azure Monitor 로그로 전송 됩니다. |
| [Azure 저장소 계정](../platform/collect-azure-metrics-logs.md) | 아닙니다. | Azure Monitor는 저장소 계정에서 데이터를 읽지 않습니다. |
| [Windows 에이전트](../platform/agent-windows.md) | 아닙니다. | 직접 Windows 에이전트는 솔루션에서 사용되지 않습니다. |
| [Linux 에이전트](../learn/quick-collect-linux-computer.md) | 아닙니다. | 직접 Linux 에이전트는 솔루션에서 사용되지 않습니다. |
| [System Center Operations Manager 관리 그룹](../platform/om-agents.md) | 아닙니다. | Operations Manager 에이전트에서 Azure Monitor로 직접 연결은 솔루션에서 사용 되지 않습니다. |

## <a name="configuration"></a>구성
설명한 프로세스를 사용 하 여 [솔루션 갤러리에서 Azure Monitor 추가 솔루션](../../azure-monitor/insights/solutions.md) Log Analytics 작업 영역에 Azure SQL Analytics (미리 보기) 솔루션을 추가 합니다.

### <a name="configure-azure-sql-databases-elastic-pools-and-managed-instances-to-stream-diagnostics-telemetry"></a>진단 원격 분석을 스트림하기 위해 Azure SQL Database, 탄력적 풀 및 Managed Instances 구성

작업 영역에 Azure SQL 분석 솔루션을 만든 후에는 모니터링하려는 각 리소스를 **구성**하여 해당 진단 원격 분석을 솔루션으로 스트리밍해야 합니다. 이 페이지에서 자세한 지침을 따르세요.

- Azure SQL Database에 대한 Azure Diagnostics를 사용하도록 설정하여 [Azure SQL 분석에 진단 원격 분석을 스트리밍](../../sql-database/sql-database-metrics-diag-logging.md)합니다.

위의 페이지에는 단일 Azure SQL 분석 작업 영역에서 여러 Azure 구독을 모니터링하도록 설정하는 지침도 제공합니다.

## <a name="using-the-solution"></a>솔루션 사용

솔루션을 작업 영역에 추가하면 Azure SQL Analytics 타일이 작업 영역에 추가되고 개요에 표시됩니다. 요약 보기 타일 콘텐츠를 로드 하는 링크를 선택 합니다.

![Azure SQL 분석 요약 타일](./media/azure-sql/azure-sql-sol-tile-01.png)

로드 되 면 타일에는 솔루션에서 진단 원격 분석을 수신 하는 관리 되는 인스턴스에서 Azure SQL 데이터베이스, 탄력적 풀에서 관리 되는 인스턴스 및 데이터베이스의 수를 표시 합니다.

![Azure SQL Analytics 타일](./media/azure-sql/azure-sql-sol-tile-02.png)

솔루션에서는 Azure SQL Database 및 탄력적 풀을 모니터링하는 보기 및 Managed Instance 및 Managed Instance의 데이터베이스를 모니터링하는 보기와 같은 두 개의 개별 보기를 제공합니다.

Azure SQL Database 및 탄력적 풀에 대한 대시보드를 모니터링하는 Azure SQL 분석을 보려면 타일의 위쪽 부분을 클릭합니다. Managed Instance 및 Managed Instance의 데이터베이스에 대한 대시보드를 모니터링하는 Azure SQL 분석을 보려면 타일의 아래쪽 부분을 클릭합니다.

### <a name="viewing-azure-sql-analytics-data"></a>Azure SQL 분석 데이터 보기

대시보드에는 서로 다른 관점을 통해 모니터링되는 모든 데이터베이스의 개요가 포함됩니다. 작동 하려면 다양 한 측면에 대 한 Log Analytics 작업 영역에 스트리밍할 수 SQL 리소스의 적절 한 메트릭이 나 로그를 활성화 해야 합니다.

몇 가지 메트릭 또는 로그는 Azure Monitor로 스트리밍되지 않습니다, 경우 솔루션의 타일이 없습니다 채워져 있는지 모니터링 정보 note 합니다.

### <a name="azure-sql-database-and-elastic-pool-view"></a>Azure SQL Database 및 탄력적 풀 보기

데이터베이스에 대한 Azure SQL 분석 타일을 선택하면 모니터링 대시보드가 표시됩니다.

![Azure SQL 분석 개요](./media/azure-sql/azure-sql-sol-overview.png)

임의의 타일을 선택하면 특정 관점으로의 드릴다운 보고서가 열립니다. 관점을 선택하면 드릴다운 보고서가 열립니다.

![Azure SQL 분석 시간 제한](./media/azure-sql/azure-sql-sol-metrics.png)

이 보기의 각 큐브 뷰에서는 구독, 서버, 탄력적 풀 및 데이터베이스 수준에 대한 요약을 제공합니다. 또한 각 관점에 따라 오른쪽에 관점별 보고서가 표시됩니다. 목록에서 구독, 서버, 풀 또는 데이터베이스를 선택하면 계속해서 드릴다운됩니다.

### <a name="managed-instance-and-databases-in-managed-instance-view"></a>Managed Instance 및 Managed Instance의 데이터베이스 보기

데이터베이스에 대한 Azure SQL 분석 타일을 선택하면 모니터링 대시보드가 표시됩니다.

![Azure SQL 분석 개요](./media/azure-sql/azure-sql-sol-overview-mi.png)

임의의 타일을 선택하면 특정 관점으로의 드릴다운 보고서가 열립니다. 관점을 선택하면 드릴다운 보고서가 열립니다.

Managed Instance 보기를 선택하면 Managed Instance 사용률, 포함된 데이터베이스 및 인스턴스 전체에서 실행된 쿼리에 대한 원격 분석의 세부 정보가 표시됩니다.

![Azure SQL 분석 시간 제한](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="perspectives"></a>큐브 뷰

아래 표에서는 Azure SQL Database 및 탄력적 풀에 대한 큐브 뷰 및 Managed Instance에 대한 큐브 뷰와 같이 두 버전의 대시보드에 지원되는 큐브 뷰에 대해 설명합니다.

| 관점 | 설명 | SQL Database 및 탄력적 풀 지원 | Managed Instance 지원 |
| --- | ------- | ----- | ----- |
| 유형별 리소스 | 모니터링되는 모든 리소스를 계산하는 관점. | 예 | 예 |
| 자세한 정보 | Intelligent Insights 성능에 대한 계층적 드릴다운을 제공합니다. | 예 | 예 |
| 오류 | 데이터베이스에서 발생한 SQL 오류에 대한 계층적 드릴다운을 제공합니다. | 예 | 예 |
| 시간 제한 | 데이터베이스에서 발생한 SQL 시간 제한에 대한 계층적 드릴다운을 제공합니다. | 예 | 아닙니다. |
| 차단 | 데이터베이스에서 발생한 SQL 차단에 대한 계층적 드릴다운을 제공합니다. | 예 | 아닙니다. |
| 데이터베이스 대기 | 데이터베이스 수준에서 발생한 SQL 대기 통계에 대한 계층적 드릴다운을 제공합니다. 총 대기 시간 및 대기 유형별 대기 시간에 대한 요약을 포함합니다. |예 | 예 |
| 쿼리 기간 | 쿼리 기간, CPU 사용량, 데이터 IO 사용량, 로그 IO 사용량과 같은 쿼리 실행 통계에 대한 계층적 드릴다운을 제공합니다. | 예 | 예 |
| 쿼리 대기 | 대기 범주별 쿼리 대기 통계에 대한 계층적 드릴다운을 제공합니다. | 예 | 예 |

### <a name="intelligent-insights-report"></a>Intelligent Insights 보고서

Azure SQL Database [Intelligent Insights](../../sql-database/sql-database-intelligent-insights.md)는 모든 Azure SQL 데이터베이스 성능을 알려줍니다. 수집된 모든 Intelligent Insights를 시각화하고 Insights 관점에서 액세스할 수 있습니다.

![Azure SQL 분석 정보](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>탄력적 풀 및 데이터베이스 보고서

탄력적 풀과 SQL Database는 모두 지정된 시간에 리소스에 대해 수집된 데이터를 모두 보여주는 고유한 특정 보고서를 포함합니다.

![Azure SQL 분석 데이터베이스](./media/azure-sql/azure-sql-sol-database.png)

![Azure SQL 탄력적 풀](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>쿼리 보고서

쿼리 기간 및 쿼리 대기 관점을 통해, 쿼리 보고서에서 쿼리 성능을 상호 연결할 수 있습니다. 이 보고서는 여러 데이터베이스 간의 쿼리 성능을 비교하고 선택한 쿼리를 원활하게 수행하는 데이터베이스와 느리게 수행하는 데이터베이스를 쉽게 찾아낼 수 있습니다.

![Azure SQL 분석 쿼리](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>권한

Azure SQL 분석을 사용하려면 Azure에서 사용자에게 적어도 읽기 권한자 역할이 부여되어야 합니다. 하지만 이 역할은 사용자가 쿼리 텍스트를 보거나 자동 튜닝 작업을 수행하는 것을 허용하지 않습니다. Azure에서 솔루션을 최대 범위까지 사용할 수 있는 보다 큰 허용 범위의 역할은 소유자, 기여자, SQL DB 기여자 또는 SQL Server 기여자입니다. 포털에서 Azure SQL 분석을 사용하는 데 필요한 권한만 있고 다른 리소스를 관리하는 데 필요한 권한은 없는 사용자 지정 역할을 만들 수도 있습니다.

### <a name="creating-a-custom-role-in-portal"></a>포털에서 사용자 지정 역할 만들기

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

일부 조직은 Azure에서 엄격하게 권한을 제어합니다. Azure Portal에서 Azure SQL 분석을 최대로 사용하는 데 필요한 최소 읽기 및 쓰기 권한만 있는 사용자 지정 역할 “SQL 분석 모니터링 운영자”를 만드는 다음 PowerShell 스크립트를 찾아보세요.

아래 스크립트의 “{SubscriptionId}”를 Azure 구독 ID로 바꾸고, Azure에서 소유자 또는 기여자 역할로 로그인된 스크립트를 실행합니다.

   ```powershell
    Connect-AzAccount
    Select-AzSubscription {SubscriptionId}
    $role = Get-AzRoleDefinition -Name Reader
    $role.Name = "SQL Analytics Monitoring Operator"
    $role.Description = "Lets you monitor database performance with Azure SQL Analytics as a reader. Does not allow change of resources."
    $role.IsCustom = $true
    $role.Actions.Add("Microsoft.SQL/servers/databases/read");
    $role.Actions.Add("Microsoft.SQL/servers/databases/topQueries/queryText/*");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/*");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Resources/deployments/write");
    $role.AssignableScopes = "/subscriptions/{SubscriptionId}"
    New-AzRoleDefinition $role
   ```

새 역할이 만들어지면 Azure SQL 분석을 사용하기 위한 사용자 지정 권한을 부여해야 하는 각 사용자에게 이 역할을 할당합니다.

## <a name="analyze-data-and-create-alerts"></a>데이터 분석 및 경고 만들기

Azure SQL Analytics의 데이터 분석은 사용자 지정 쿼리 및 보고를 위한 [Log Analytics 언어](../log-query/get-started-queries.md)를 기반으로 합니다. [사용 가능한 메트릭 및 로그](../../sql-database/sql-database-metrics-diag-logging.md#metrics-and-logs-available)에서 사용자 지정 쿼리를 위해 데이터베이스 리소스에서 수집된 사용 가능 데이터에 대한 설명을 확인하세요.

솔루션에서 자동화된 경고는 조건 충족 시 경고를 트리거하는 Log Analytics 쿼리 작성을 기반으로 합니다. 솔루션에서 경고를 설정할 수 있는 Log Analytics 쿼리에 대한 여러 예제를 아래에서 찾아보세요.

### <a name="creating-alerts-for-azure-sql-database"></a>Azure SQL Database에 대한 경고 만들기

Azure SQL Database 리소스에서 가져온 데이터와 [경고를 쉽게 만들](../platform/alerts-metric.md) 수 있습니다. 다음은 로그 경고와 함께 사용할 수 있는 몇 가지 유용한 [로그 쿼리](../log-query/log-query-overview.md)입니다.

#### <a name="high-cpu-on-azure-sql-database"></a>Azure SQL Database의 높은 CPU

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/DATABASES/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
> - 이 경고 설정에 대한 사전 요구 사항은 솔루션에 대해 모니터링된 해당 데이터베이스 스트림 진단 메트릭("모든 메트릭" 옵션)입니다.
> - 대신 높은 DTU 결과를 얻으려면 MetricName 값 cpu_percent를 dtu_consumption_percent로 바꿉니다.

#### <a name="high-cpu-on-azure-sql-database-elastic-pools"></a>Azure SQL Database 탄력적 풀의 높은 CPU

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/ELASTICPOOLS/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
> - 이 경고 설정에 대한 사전 요구 사항은 솔루션에 대해 모니터링된 해당 데이터베이스 스트림 진단 메트릭("모든 메트릭" 옵션)입니다.
> - 대신 높은 DTU 결과를 얻으려면 MetricName 값 cpu_percent를 dtu_consumption_percent로 바꿉니다.

#### <a name="azure-sql-database-storage-in-average-above-95-in-the-last-1-hr"></a>마지막 1시간에 평균 95% 이상인 Azure SQL Database 스토리지

```
let time_range = 1h;
let storage_threshold = 95;
AzureMetrics
| where ResourceId contains "/DATABASES/"
| where MetricName == "storage_percent"
| summarize max_storage = max(Average) by ResourceId, bin(TimeGenerated, time_range)
| where max_storage > storage_threshold
| distinct ResourceId
```

> [!NOTE]
> - 이 경고 설정에 대한 사전 요구 사항은 솔루션에 대해 모니터링된 해당 데이터베이스 스트림 진단 메트릭("모든 메트릭" 옵션)입니다.
> - 쿼리에서 조건이 일부 데이터베이스에 있는지를 나타내는 결과(> 0 결과)가 있는 경우 이 경고는 경고를 해제하도록 설정한 경고 규칙이 필요합니다. 출력은 정의된 time_range 내의 storage_threshold 위쪽에 있는 데이터베이스 리소스의 목록입니다.
> - 출력은 정의된 time_range 내의 storage_threshold 위쪽에 있는 데이터베이스 리소스의 목록입니다.

#### <a name="alert-on-intelligent-insights"></a>Intelligent Insights에 대한 경고

```
let alert_run_interval = 1h;
let insights_string = "hitting its CPU limits";
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| where TimeGenerated > ago(alert_run_interval)
| where rootCauseAnalysis_s contains insights_string
| distinct ResourceId
```

> [!NOTE]
> - 이 경고 설정에 대한 사전 요구 사항은 솔루션에 대해 모니터링된 해당 데이터베이스 스트림 SQLInsights 진단 로그입니다.
> - 이 쿼리는 중복 결과를 방지하기 위해 alert_run_interval과 동일한 빈도로 실행하도록 설정한 경고 규칙이 필요합니다. 규칙은 쿼리에서 결과(> 0 결과)가 있으면 경고를 해제하도록 설정해야 합니다.
> - 솔루션에 SQLInsights 로그를 스트림하도록 구성된 데이터베이스에서 조건이 발생했는지 확인하려면 시간 범위를 지정하기 위한 alert_run_interval을 사용자 지정합니다.
> - Insights 근본 원인 분석 텍스트의 출력을 캡처하려면 insights_string을 사용자 지정합니다. 기존 인사이트에서 사용할 수 있는 솔루션의 UI에 표시된 동일한 텍스트입니다. 또는 구독에서 생성된 모든 Insights의 텍스트를 보려면 아래 쿼리를 사용할 수 있습니다. 쿼리의 출력을 사용하여 Insights에 대한 경고를 설정하기 위한 고유 문자열을 수집합니다.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-managed-instance"></a>Managed Instance에 대한 경고 만들기

#### <a name="managed-instance-storage-is-above-90"></a>Managed Instance 스토리지는 90% 초과

```
let storage_percentage_threshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_threshold
```

> [!NOTE]
> - 이 경고를 설정하려면 모니터링되는 Managed Instance에서 ResourceUsageStats 로그의 스트리밍을 솔루션에 대해 사용하도록 설정해야 합니다.
> - 쿼리에 결과가 있는 경우(> 0 결과) 이 쿼리에서는 경고 규칙이 경고를 해제하도록 설정해야 합니다. 그러면 조건이 Managed Instance에 있음을 나타냅니다. Managed Instance의 저장소 비율 사용이 출력됩니다.

#### <a name="managed-instance-cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>Managed Instance CPU 평균 소비가 최근 1시간 동안 95% 초과

```
let cpu_percentage_threshold = 95;
let time_threshold = ago(1h);
AzureDiagnostics
| where Category == "ResourceUsageStats" and TimeGenerated > time_threshold
| summarize avg_cpu = max(todouble(avg_cpu_percent_s)) by ResourceId
| where avg_cpu > cpu_percentage_threshold
```

> [!NOTE]
> - 이 경고를 설정하려면 모니터링되는 Managed Instance에서 ResourceUsageStats 로그의 스트리밍을 솔루션에 대해 사용하도록 설정해야 합니다.
> - 쿼리에 결과가 있는 경우(> 0 결과) 이 쿼리에서는 경고 규칙이 경고를 해제하도록 설정해야 합니다. 그러면 조건이 Managed Instance에 있음을 나타냅니다. 출력은 Managed Instance에서 정의된 기간의 평균 CPU 사용률(%)입니다.

### <a name="pricing"></a>가격

솔루션은 무료로 사용할 수 있지만 매월 할당되는 무료 데이터 수집 단위를 초과하는 진단 원격 분석 데이터에는 요금이 청구됩니다. [Log Analytics 가격 책정](https://azure.microsoft.com/en-us/pricing/details/monitor)을 참조하세요. 제공되는 데이터 수집 무료 단위만큼 매월 여러 데이터베이스를 무료로 모니터링할 수 있습니다. 워크로드가 많은 활성 데이터는 유휴 데이터베이스보다 더 많은 데이터를 수집합니다. Azure SQL 분석의 탐색 메뉴에서 OMS 작업 영역을 선택한 다음, [사용량 및 예상 비용]을 선택하여 솔루션에서 데이터 수집 사용량을 쉽게 모니터링할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 사용 하 여 [쿼리를 로깅](../log-query/log-query-overview.md) 자세한 Azure SQL 데이터를 보려면 Azure Monitor에서.
- Azure SQL 데이터를 보여 주는 [사용자 고유의 대시보드 만들기](../learn/tutorial-logs-dashboards.md).
- 특정 Azure SQL 이벤트가 발생하는 경우의 [경고 만들기](../platform/alerts-overview.md).
