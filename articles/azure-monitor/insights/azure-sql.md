---
title: Azure Monitor에서 Azure SQL 분석 솔루션 Microsoft Docs
description: Azure SQL 분석 솔루션을 통해 Azure SQL 데이터베이스 관리
ms.subservice: logs
ms.topic: conceptual
author: danimir
ms.author: danil
ms.date: 02/21/2020
ms.reviewer: carlrab
ms.openlocfilehash: d1f8a30145cc0d61f110c0f47459a4f1db03325b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85249418"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Azure SQL 분석을 사용하여 Azure SQL Database 모니터링(미리 보기)

![Azure SQL 분석 기호](./media/azure-sql/azure-sql-symbol.png)

Azure SQL 분석는 단일 보기의 여러 구독에서 규모에 상관 없이 모든 Azure SQL 데이터베이스의 성능을 모니터링 하기 위한 고급 클라우드 모니터링 솔루션입니다. Azure SQL 분석는 성능 문제 해결을 위한 기본 제공 인텔리전스를 사용 하 여 주요 성능 메트릭을 수집 하 고 시각화 합니다.

이러한 수집 된 메트릭을 사용 하 여 사용자 지정 모니터링 규칙 및 경고를 만들 수 있습니다. Azure SQL 분석를 사용 하면 응용 프로그램 스택의 각 계층에서 문제를 식별할 수 있습니다. Azure 진단 메트릭과 함께 Azure Monitor 보기를 사용 하 여 단일 Log Analytics 작업 영역에서 모든 Azure SQL database에 대 한 데이터를 제공 합니다. Azure Monitor는 구조화 된 데이터와 구조화 되지 않은 데이터를 수집, 상호 연결 및 시각화 하는 데 도움이 됩니다.

Azure SQL Analytics 솔루션 사용에 대한 실무 중심 개요와 일반적인 사용 시나리오에 대해서는 포함된 비디오를 참조하세요.

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>연결된 소스

Azure SQL 분석은 모든 Azure SQL database에 대 한 진단 원격 분석 스트리밍을 지 원하는 클라우드 전용 모니터링 솔루션입니다. Azure SQL 분석는 에이전트를 사용 하 여 Azure Monitor에 연결 하지 않으므로 온-프레미스 또는 가상 머신에서 호스팅되는 SQL Server 모니터링을 지원 하지 않습니다.

| 연결된 소스 | 지원됨 | Description |
| --- | --- | --- |
| [진단 설정](../platform/diagnostic-settings.md) | **예** | Azure 메트릭 및 로그 데이터는 Azure에서 직접 Azure Monitor 로그에 전송 됩니다. |
| [Azure storage 계정](../platform/collect-azure-metrics-logs.md) | 아니요 | Azure Monitor는 저장소 계정에서 데이터를 읽지 않습니다. |
| [Windows 에이전트](../platform/agent-windows.md) | 아니요 | 직접 Windows 에이전트는 Azure SQL 분석에서 사용 되지 않습니다. |
| [Linux 에이전트](../learn/quick-collect-linux-computer.md) | 아니요 | 직접 Linux 에이전트는 Azure SQL 분석에서 사용 되지 않습니다. |
| [System Center Operations Manager 관리 그룹](../platform/om-agents.md) | 아니요 | Operations Manager 에이전트에서 Azure Monitor로의 직접 연결은 Azure SQL 분석에서 사용 되지 않습니다. |

## <a name="azure-sql-analytics-options"></a>Azure SQL 분석 옵션

아래 표에서는 두 가지 버전의 Azure SQL 분석 대시보드의 지원 되는 옵션, Azure SQL Database 용 및 Azure SQL Managed Instance 데이터베이스용으로 지원 되는 옵션을 간략하게 설명 합니다.

| Azure SQL 분석 옵션 | 설명 | 지원 SQL Database | SQL Managed Instance 지원 |
| --- | ------- | ----- | ----- |
| 유형별 리소스 | 모니터링되는 모든 리소스를 계산하는 관점. | 예 | 예 |
| 자세한 정보 | Intelligent Insights 성능에 대한 계층적 드릴다운을 제공합니다. | 예 | 예 |
| 오류 | 데이터베이스에서 발생한 SQL 오류에 대한 계층적 드릴다운을 제공합니다. | 예 | 예 |
| 시간 제한 | 데이터베이스에서 발생한 SQL 시간 제한에 대한 계층적 드릴다운을 제공합니다. | 예 | 아니요 |
| 차단 | 데이터베이스에서 발생한 SQL 차단에 대한 계층적 드릴다운을 제공합니다. | 예 | 아니요 |
| 데이터베이스 대기 | 데이터베이스 수준에서 발생한 SQL 대기 통계에 대한 계층적 드릴다운을 제공합니다. 총 대기 시간 및 대기 유형별 대기 시간에 대한 요약을 포함합니다. |예 | 아니요 |
| 쿼리 기간 | 쿼리 기간, CPU 사용량, 데이터 IO 사용량, 로그 IO 사용량과 같은 쿼리 실행 통계에 대한 계층적 드릴다운을 제공합니다. | 예 | 예 |
| 쿼리 대기 | 대기 범주별 쿼리 대기 통계에 대한 계층적 드릴다운을 제공합니다. | 예 | 예 |

## <a name="configuration"></a>Configuration

[솔루션 갤러리에서 Azure Monitor 솔루션 추가](../../azure-monitor/insights/solutions.md) 에 설명 된 프로세스를 사용 하 여 Log Analytics 작업 영역에 Azure SQL 분석 (미리 보기)를 추가 합니다.

### <a name="configure-azure-sql-database-to-stream-diagnostics-telemetry"></a>진단 원격 분석을 스트리밍할 Azure SQL Database 구성

작업 영역에서 Azure SQL 분석 솔루션을 만든 후 모니터링 하려는 각 리소스를 **구성** 하 여 진단 원격 분석을 Azure SQL 분석로 스트리밍합니다. 이 페이지에서 자세한 지침을 따르세요.

- 데이터베이스에 대 한 Azure 진단를 사용 하도록 설정 하 여 [진단 원격 분석을 Azure SQL 분석로 스트리밍합니다](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md).

위의 페이지에는 단일 Azure SQL 분석 작업 영역에서 여러 Azure 구독을 모니터링하도록 설정하는 지침도 제공합니다.

## <a name="using-azure-sql-analytics"></a>Azure SQL 분석 사용

작업 영역에 Azure SQL 분석를 추가 하면 Azure SQL 분석 타일이 작업 영역에 추가 되 고 개요에 표시 됩니다. 요약 링크 보기를 선택 하 여 타일 콘텐츠를 로드 합니다.

![Azure SQL 분석 요약 타일](./media/azure-sql/azure-sql-sol-tile-01.png)

로드 되 면 타일에는 SQL Database의 데이터베이스 및 탄력적 풀 수와, Azure SQL 분석 진단 원격 분석을 받는 SQL Managed Instance의 관리 되는 인스턴스 및 인스턴스 데이터베이스가 표시 됩니다.

![Azure SQL Analytics 타일](./media/azure-sql/azure-sql-sol-tile-02.png)

Azure SQL 분석는 모니터링 SQL Database와 SQL Managed Instance 모니터링을 위한 다른 보기의 두 가지 별도 보기를 제공 합니다.

SQL Database에 대 한 Azure SQL 분석 모니터링 대시보드를 보려면 타일의 위쪽 부분을 클릭 합니다. SQL Managed Instance에 대 한 Azure SQL 분석 모니터링 대시보드를 보려면 타일의 아래쪽 부분을 클릭 합니다.

### <a name="viewing-azure-sql-analytics-data"></a>Azure SQL 분석 데이터 보기

대시보드에는 서로 다른 관점을 통해 모니터링되는 모든 데이터베이스의 개요가 포함됩니다. 다른 큐브 뷰가 작동 하려면 Log Analytics 작업 영역으로 스트리밍되는 SQL 리소스에 대 한 적절 한 메트릭 또는 로그를 사용 하도록 설정 해야 합니다.

일부 메트릭 또는 로그가 Azure Monitor로 스트리밍되는지 않은 경우 Azure SQL 분석의 타일이 모니터링 정보로 채워지지 않습니다.

### <a name="sql-database-view"></a>SQL Database 뷰

데이터베이스에 대한 Azure SQL 분석 타일을 선택하면 모니터링 대시보드가 표시됩니다.

![Azure SQL 분석 개요](./media/azure-sql/azure-sql-sol-overview.png)

임의의 타일을 선택하면 특정 관점으로의 드릴다운 보고서가 열립니다. 관점을 선택하면 드릴다운 보고서가 열립니다.

![Azure SQL 분석 시간 제한](./media/azure-sql/azure-sql-sol-metrics.png)

이 보기의 각 큐브 뷰에서는 구독, 서버, 탄력적 풀 및 데이터베이스 수준에 대 한 요약을 제공 합니다. 또한 각 관점에 따라 오른쪽에 관점별 보고서가 표시됩니다. 목록에서 구독, 서버, 풀 또는 데이터베이스를 선택하면 계속해서 드릴다운됩니다.

### <a name="sql-managed-instance-view"></a>SQL Managed Instance 뷰

데이터베이스에 대한 Azure SQL 분석 타일을 선택하면 모니터링 대시보드가 표시됩니다.

![Azure SQL 분석 개요](./media/azure-sql/azure-sql-sol-overview-mi.png)

임의의 타일을 선택하면 특정 관점으로의 드릴다운 보고서가 열립니다. 관점을 선택하면 드릴다운 보고서가 열립니다.

SQL Managed Instance 뷰를 선택 하면 인스턴스 전체에서 실행 되는 쿼리에 대 한 관리 되는 인스턴스 사용률, 데이터베이스에 포함 된 데이터베이스 및 원격 분석에 대 한 세부 정보가 표시 됩니다.

![Azure SQL 분석 시간 제한](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="intelligent-insights-report"></a>Intelligent Insights 보고서

Azure SQL Database [Intelligent Insights](../../azure-sql/database/intelligent-insights-overview.md)는 모든 Azure SQL 데이터베이스 성능을 알려줍니다. 수집된 모든 Intelligent Insights를 시각화하고 Insights 관점에서 액세스할 수 있습니다.

![Azure SQL 분석 정보](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pools-and-database-reports"></a>탄력적 풀 및 데이터베이스 보고서

탄력적 풀과 데이터베이스에는 지정 된 시간 내에 리소스에 대해 수집 되는 모든 데이터를 표시 하는 고유한 특정 보고서가 있습니다.

![Azure SQL 분석 데이터베이스](./media/azure-sql/azure-sql-sol-database.png)

![Azure SQL 탄력적 풀](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>쿼리 보고서

쿼리 기간 및 쿼리 대기 관점을 통해 쿼리 보고서를 통해 쿼리 성능을 상호 연결할 수 있습니다. 이 보고서는 여러 데이터베이스 간의 쿼리 성능을 비교하고 선택한 쿼리를 원활하게 수행하는 데이터베이스와 느리게 수행하는 데이터베이스를 쉽게 찾아낼 수 있습니다.

![Azure SQL 분석 쿼리](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>사용 권한

Azure SQL 분석을 사용하려면 Azure에서 사용자에게 적어도 읽기 권한자 역할이 부여되어야 합니다. 하지만 이 역할은 사용자가 쿼리 텍스트를 보거나 자동 튜닝 작업을 수행하는 것을 허용하지 않습니다. 최고 범위에 Azure SQL 분석를 사용할 수 있도록 하는 Azure의 더 많은 허용 역할은 소유자, 참여자, SQL DB 참여자 또는 SQL Server 기여자입니다. 포털에서 Azure SQL 분석을 사용하는 데 필요한 권한만 있고 다른 리소스를 관리하는 데 필요한 권한은 없는 사용자 지정 역할을 만들 수도 있습니다.

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

Azure SQL Analytics의 데이터 분석은 사용자 지정 쿼리 및 보고를 위한 [Log Analytics 언어](../log-query/get-started-queries.md)를 기반으로 합니다. [사용 가능한 메트릭 및 로그](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md#metrics-and-logs-available)에서 사용자 지정 쿼리를 위해 데이터베이스 리소스에서 수집된 사용 가능 데이터에 대한 설명을 확인하세요.

Azure SQL 분석의 자동화 된 경고는 조건이 충족 될 때 경고를 트리거하는 Log Analytics 쿼리를 작성 하는 것을 기반으로 합니다. Azure SQL 분석에서 경고를 설정할 수 있는 Log Analytics 쿼리의 몇 가지 예제를 찾습니다.

### <a name="creating-alerts-for-azure-sql-database"></a>Azure SQL Database에 대한 경고 만들기

Azure SQL Database 리소스에서 가져온 데이터와 [경고를 쉽게 만들](../platform/alerts-metric.md) 수 있습니다. 다음은 로그 경고와 함께 사용할 수 있는 몇 가지 유용한 [로그 쿼리](../log-query/log-query-overview.md)입니다.

#### <a name="high-cpu"></a>높은 CPU

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/DATABASES/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
>
> - 이 경고를 설정 하기 위한 사전 요구 사항은 모니터링 되는 데이터베이스에서 기본 메트릭을 Azure SQL 분석 하도록 스트림 하는 것입니다.
> - 대신 높은 DTU 결과를 얻으려면 MetricName 값 cpu_percent를 dtu_consumption_percent로 바꿉니다.

#### <a name="high-cpu-on-elastic-pools"></a>탄력적 풀의 높은 CPU

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/ELASTICPOOLS/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
>
> - 이 경고를 설정 하기 위한 사전 요구 사항은 모니터링 되는 데이터베이스에서 기본 메트릭을 Azure SQL 분석 하도록 스트림 하는 것입니다.
> - 대신 높은 DTU 결과를 얻으려면 MetricName 값 cpu_percent를 dtu_consumption_percent로 바꿉니다.

#### <a name="storage-in-average-above-95-in-the-last-1-hr"></a>지난 1 시간 동안 95% 위의 평균 저장소

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
>
> - 이 경고를 설정 하기 위한 사전 요구 사항은 모니터링 되는 데이터베이스에서 기본 메트릭을 Azure SQL 분석 하도록 스트림 하는 것입니다.
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
>
> - 이 경고를 설정 하기 위한 사전 요구 사항은 모니터링 되는 데이터베이스 스트림 SQLInsights 진단 로그를 Azure SQL 분석 하는 것입니다.
> - 이 쿼리는 중복 결과를 방지하기 위해 alert_run_interval과 동일한 빈도로 실행하도록 설정한 경고 규칙이 필요합니다. 규칙은 쿼리에서 결과(> 0 결과)가 있으면 경고를 해제하도록 설정해야 합니다.
> - SQLInsights log를 Azure SQL 분석로 스트리밍하기 위해 구성 된 데이터베이스에서 조건이 발생 했는지 확인 하는 시간 범위를 지정 하려면 alert_run_interval를 사용자 지정 합니다.
> - Insights 근본 원인 분석 텍스트의 출력을 캡처하려면 insights_string을 사용자 지정합니다. 이 텍스트는 기존 정보에서 사용할 수 있는 Azure SQL 분석의 UI에 표시 되는 텍스트와 동일 합니다. 또는 구독에서 생성된 모든 Insights의 텍스트를 보려면 아래 쿼리를 사용할 수 있습니다. 쿼리의 출력을 사용하여 Insights에 대한 경고를 설정하기 위한 고유 문자열을 수집합니다.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-sql-managed-instance"></a>SQL Managed Instance에 대 한 경고 만들기

#### <a name="storage-is-above-90"></a>저장소 90% 초과

```
let storage_percentage_threshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_threshold
```

> [!NOTE]
>
> - 이 경고를 설정 하기 위한 사전 요구 사항은 ResourceUsageStats log를 사용 하도록 설정 된 관리 되는 인스턴스를 Azure SQL 분석에 대 한 스트리밍이 있음을 모니터링 한다는 것입니다.
> - 이 쿼리를 실행 하려면 쿼리에서 해당 조건이 존재 한다는 것을 나타내는 결과 (> 0 결과)가 있을 때 경고를 발생 하도록 설정 하는 경고 규칙이 필요 합니다. 출력은 관리 되는 인스턴스의 저장소 백분율 사용량입니다.

#### <a name="cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>지난 1 시간 동안 CPU 평균 사용량이 95%를 초과 합니다.

```
let cpu_percentage_threshold = 95;
let time_threshold = ago(1h);
AzureDiagnostics
| where Category == "ResourceUsageStats" and TimeGenerated > time_threshold
| summarize avg_cpu = max(todouble(avg_cpu_percent_s)) by ResourceId
| where avg_cpu > cpu_percentage_threshold
```

> [!NOTE]
>
> - 이 경고를 설정 하기 위한 사전 요구 사항은 모니터링 되는 관리 되는 인스턴스에 Azure SQL 분석 ResourceUsageStats log를 사용 하도록 설정 된 스트리밍이 있음을 나타내는 것입니다.
> - 이 쿼리를 실행 하려면 쿼리에서 해당 조건이 존재 한다는 것을 나타내는 결과 (> 0 결과)가 있을 때 경고를 발생 하도록 설정 하는 경고 규칙이 필요 합니다. 출력은 관리 되는 인스턴스에서 정의 된 기간의 평균 CPU 사용률 사용률을 차지 합니다.

### <a name="pricing"></a>가격 책정

Azure SQL 분석 무료로 사용할 수 있지만 매달 할당 된 데이터 수집의 무료 단위 위에 진단 원격 분석을 사용 하는 것은 [Log Analytics 가격 책정](https://azure.microsoft.com/pricing/details/monitor)을 참조 하세요. 제공되는 데이터 수집 무료 단위만큼 매월 여러 데이터베이스를 무료로 모니터링할 수 있습니다. 작업 부하가 많은 활성 데이터베이스는 더 많은 데이터와 유휴 데이터베이스를 수집 합니다. Azure SQL 분석의 탐색 메뉴에서 OMS 작업 영역을 선택한 다음 사용량 및 예상 비용을 선택 하 여 Azure SQL 분석에서 데이터 수집 소비량을 쉽게 모니터링할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- Azure Monitor의 [로그 쿼리](../log-query/log-query-overview.md) 를 사용 하 여 자세한 Azure SQL 데이터를 볼 수 있습니다.
- Azure SQL 데이터를 보여 주는 [사용자 고유의 대시보드 만들기](../learn/tutorial-logs-dashboards.md).
- 특정 Azure SQL 이벤트가 발생하는 경우의 [경고 만들기](../platform/alerts-overview.md).
