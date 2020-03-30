---
title: Azure 모니터의 Azure SQL 분석 솔루션 | 마이크로 소프트 문서
description: Azure SQL 분석 솔루션을 통해 Azure SQL 데이터베이스 관리
ms.subservice: logs
ms.topic: conceptual
author: danimir
ms.author: danil
ms.date: 02/21/2020
ms.reviewer: carlrab
ms.openlocfilehash: 921a05c4dc6c1d5cfa663ac71b469573b8f1925b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275465"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Azure SQL 분석을 사용하여 Azure SQL Database 모니터링(미리 보기)

![Azure SQL 분석 기호](./media/azure-sql/azure-sql-symbol.png)

Azure SQL Analytics는 대규모 및 단일 보기의 여러 구독에서 모든 Azure SQL 데이터베이스의 성능을 모니터링하기 위한 고급 클라우드 모니터링 솔루션입니다. Azure SQL Analytics는 성능 문제 해결을 위한 기본 제공 인텔리전스를 통해 주요 성능 메트릭을 수집하고 시각화합니다.

이러한 수집된 메트릭을 사용하여 사용자 지정 모니터링 규칙 및 경고를 만들 수 있습니다. Azure SQL Analytics를 사용하면 응용 프로그램 스택의 각 계층에서 문제를 식별할 수 있습니다. Azure Monitor 보기와 함께 Azure 진단 메트릭을 사용하여 단일 로그 분석 작업 영역에서 모든 Azure SQL 데이터베이스에 대한 데이터를 표시합니다. Azure Monitor를 사용하면 구조화및 비정형 데이터를 수집, 상관 관계 및 시각화할 수 있습니다.

Azure SQL Analytics 솔루션 사용에 대한 실무 중심 개요와 일반적인 사용 시나리오에 대해서는 포함된 비디오를 참조하세요.

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>연결된 소스

Azure SQL Analytics는 모든 Azure SQL 데이터베이스에 대한 진단 원격 분석 스트리밍을 지원하는 클라우드 모니터링 솔루션입니다. Azure SQL Analytics는 에이전트를 사용하여 Azure Monitor에 연결하지 않으므로 온-프레미스 또는 가상 컴퓨터에서 호스팅되는 SQL Server의 모니터링을 지원하지 않습니다.

| 연결된 소스 | 지원됨 | 설명 |
| --- | --- | --- |
| [진단 설정](../platform/diagnostic-settings.md) | **예** | Azure 메트릭 및 로그 데이터는 Azure에서 직접 Azure 모니터 로그로 전송됩니다. |
| [Azure 저장소 계정](../platform/collect-azure-metrics-logs.md) | 예 | Azure Monitor저장소 계정에서 데이터를 읽지 않습니다. |
| [Windows 에이전트](../platform/agent-windows.md) | 예 | 직접 Windows 에이전트는 Azure SQL Analytics에서 사용되지 않습니다. |
| [Linux 에이전트](../learn/quick-collect-linux-computer.md) | 예 | 직접 Linux 에이전트는 Azure SQL Analytics에서 사용되지 않습니다. |
| [System Center Operations Manager 관리 그룹](../platform/om-agents.md) | 예 | Azure SQL Analytics에서 운영 관리자 에이전트에서 Azure 모니터로 직접 연결하는 작업은 사용되지 않습니다. |

## <a name="azure-sql-analytics-options"></a>Azure SQL 분석 옵션

아래 표에서는 Azure SQL Analytics 대시보드의 두 버전에 대해 지원되는 옵션(하나는 단일 및 풀로 풀된 데이터베이스 및 탄력적 풀용, 다른 하나는 관리되는 인스턴스 및 인스턴스 데이터베이스용)에 대해 설명합니다.

| Azure SQL 분석 옵션 | 설명 | 단일 및 풀풀 데이터베이스 및 탄력적 풀 지원 | 관리되는 인스턴스 및 인스턴스 데이터베이스 지원 |
| --- | ------- | ----- | ----- |
| 유형별 리소스 | 모니터링되는 모든 리소스를 계산하는 관점. | yes | yes |
| 자세한 정보 | Intelligent Insights 성능에 대한 계층적 드릴다운을 제공합니다. | yes | yes |
| 오류 | 데이터베이스에서 발생한 SQL 오류에 대한 계층적 드릴다운을 제공합니다. | yes | yes |
| 시간 제한 | 데이터베이스에서 발생한 SQL 시간 제한에 대한 계층적 드릴다운을 제공합니다. | yes | 예 |
| 차단 | 데이터베이스에서 발생한 SQL 차단에 대한 계층적 드릴다운을 제공합니다. | yes | 예 |
| 데이터베이스 대기 | 데이터베이스 수준에서 발생한 SQL 대기 통계에 대한 계층적 드릴다운을 제공합니다. 총 대기 시간 및 대기 유형별 대기 시간에 대한 요약을 포함합니다. |yes | 예 |
| 쿼리 기간 | 쿼리 기간, CPU 사용량, 데이터 IO 사용량, 로그 IO 사용량과 같은 쿼리 실행 통계에 대한 계층적 드릴다운을 제공합니다. | yes | yes |
| 쿼리 대기 | 대기 범주별 쿼리 대기 통계에 대한 계층적 드릴다운을 제공합니다. | yes | yes |

## <a name="configuration"></a>Configuration

[솔루션 갤러리의 Azure Monitor 솔루션 추가에 설명된 프로세스를](../../azure-monitor/insights/solutions.md) 사용하여 로그 분석 작업 영역에 Azure SQL 분석(미리 보기)을 추가합니다.

### <a name="configure-azure-sql-databases-to-stream-diagnostics-telemetry"></a>진단 원격 분석을 스트리밍하도록 Azure SQL 데이터베이스 구성

작업 영역에서 Azure SQL Analytics 솔루션을 만든 후에는 진단 원격 분석을 Azure SQL Analytics로 스트리밍하도록 모니터링할 각 리소스를 **구성해야** 합니다. 이 페이지에서 자세한 지침을 따르세요.

- Azure SQL Database에 대한 Azure Diagnostics를 사용하도록 설정하여 [Azure SQL 분석에 진단 원격 분석을 스트리밍](../../sql-database/sql-database-metrics-diag-logging.md)합니다.

위의 페이지에는 단일 Azure SQL 분석 작업 영역에서 여러 Azure 구독을 모니터링하도록 설정하는 지침도 제공합니다.

## <a name="using-azure-sql-analytics"></a>Azure SQL 분석 사용

작업 영역에 Azure SQL Analytics를 추가하면 Azure SQL Analytics 타일이 작업 영역에 추가되고 개요에 표시됩니다. 타일 내용을 로드하려면 요약 보기 링크를 선택합니다.

![Azure SQL 분석 요약 타일](./media/azure-sql/azure-sql-sol-tile-01.png)

로드되면 타일에는 Azure SQL Analytics에서 진단 원격 분석을 수신하는 단일 및 풀루미운 데이터베이스, 탄력적 풀, 관리되는 인스턴스 및 관리되는 인스턴스 데이터베이스수가 표시됩니다.

![Azure SQL Analytics 타일](./media/azure-sql/azure-sql-sol-tile-02.png)

Azure SQL Analytics는 단일 데이터베이스 및 풀풀된 데이터베이스 및 탄력적 풀을 모니터링하기 위한 두 개의 별도 보기와 관리되는 인스턴스 및 인스턴스 데이터베이스를 모니터링하기 위한 다른 보기를 제공합니다.

단일 데이터베이스 및 풀루미지 데이터베이스 및 탄력적 풀에 대한 Azure SQL Analytics 모니터링 대시보드를 보려면 타일의 위쪽 부분을 클릭합니다. 관리되는 인스턴스 및 인스턴스 데이터베이스에 대한 Azure SQL Analytics 모니터링 대시보드를 보려면 타일의 아래쪽을 클릭합니다.

### <a name="viewing-azure-sql-analytics-data"></a>Azure SQL 분석 데이터 보기

대시보드에는 서로 다른 관점을 통해 모니터링되는 모든 데이터베이스의 개요가 포함됩니다. 서로 다른 관점에서 작동하려면 SQL 리소스에 대한 적절한 메트릭 또는 로그를 Log Analytics 작업 영역으로 스트리밍하도록 설정해야 합니다.

일부 메트릭 또는 로그가 Azure Monitor로 스트리밍되지 않으면 Azure SQL Analytics의 타일이 모니터링 정보로 채워지지 않습니다.

### <a name="single-and-pooled-databases-and-elastic-pools-view"></a>단일 및 풀풀 데이터베이스 및 탄력적 풀 보기

데이터베이스에 대한 Azure SQL 분석 타일을 선택하면 모니터링 대시보드가 표시됩니다.

![Azure SQL 분석 개요](./media/azure-sql/azure-sql-sol-overview.png)

임의의 타일을 선택하면 특정 관점으로의 드릴다운 보고서가 열립니다. 관점을 선택하면 드릴다운 보고서가 열립니다.

![Azure SQL 분석 시간 제한](./media/azure-sql/azure-sql-sol-metrics.png)

이 보기의 각 큐브 뷰는 구독, 서버, 탄력적 풀 및 데이터베이스 수준에서 요약을 제공합니다. 또한 각 관점에 따라 오른쪽에 관점별 보고서가 표시됩니다. 목록에서 구독, 서버, 풀 또는 데이터베이스를 선택하면 계속해서 드릴다운됩니다.

### <a name="managed-instance-and-instances-databases-view"></a>관리되는 인스턴스 및 인스턴스 데이터베이스 보기

데이터베이스에 대한 Azure SQL 분석 타일을 선택하면 모니터링 대시보드가 표시됩니다.

![Azure SQL 분석 개요](./media/azure-sql/azure-sql-sol-overview-mi.png)

임의의 타일을 선택하면 특정 관점으로의 드릴다운 보고서가 열립니다. 관점을 선택하면 드릴다운 보고서가 열립니다.

관리되는 인스턴스 보기를 선택하면 관리되는 인스턴스 사용률, 포함된 데이터베이스 및 인스턴스 간에 실행되는 쿼리에 대한 원격 분석에 대한 세부 정보가 표시됩니다.

![Azure SQL 분석 시간 제한](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="intelligent-insights-report"></a>Intelligent Insights 보고서

Azure SQL Database [Intelligent Insights](../../sql-database/sql-database-intelligent-insights.md)는 모든 Azure SQL 데이터베이스 성능을 알려줍니다. 수집된 모든 Intelligent Insights를 시각화하고 Insights 관점에서 액세스할 수 있습니다.

![Azure SQL 분석 정보](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pools-and-database-reports"></a>탄력적 풀 및 데이터베이스 보고서

탄력적 풀과 데이터베이스에는 지정된 시간에 리소스에 대해 수집되는 모든 데이터를 표시하는 고유한 보고서가 있습니다.

![Azure SQL 분석 데이터베이스](./media/azure-sql/azure-sql-sol-database.png)

![Azure SQL 탄력적 풀](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>쿼리 보고서

쿼리 기간 및 쿼리 대기 큐브 뷰를 통해 쿼리 보고서를 통해 모든 쿼리의 성능을 상관 관계를 지정할 수 있습니다. 이 보고서는 여러 데이터베이스 간의 쿼리 성능을 비교하고 선택한 쿼리를 원활하게 수행하는 데이터베이스와 느리게 수행하는 데이터베이스를 쉽게 찾아낼 수 있습니다.

![Azure SQL 분석 쿼리](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>사용 권한

Azure SQL 분석을 사용하려면 Azure에서 사용자에게 적어도 읽기 권한자 역할이 부여되어야 합니다. 하지만 이 역할은 사용자가 쿼리 텍스트를 보거나 자동 튜닝 작업을 수행하는 것을 허용하지 않습니다. AzureSQL Analytics를 최대한 사용할 수 있도록 하는 Azure의 더 허용적인 역할은 소유자, 기여자, SQL DB 기여자 또는 SQL 서버 기여자입니다. 포털에서 Azure SQL 분석을 사용하는 데 필요한 권한만 있고 다른 리소스를 관리하는 데 필요한 권한은 없는 사용자 지정 역할을 만들 수도 있습니다.

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

Azure SQL Analytics의 데이터 분석은 사용자 지정 쿼리 및 보고를 위한 [Log Analytics 언어](../log-query/get-started-queries.md)를 기반으로 합니다. [사용 가능한 메트릭 및 로그](../../sql-database/sql-database-metrics-diag-logging.md#metrics-and-logs-available)에서 사용자 지정 쿼리를 위해 데이터베이스 리소스에서 수집된 사용 가능 데이터에 대한 설명을 확인하세요.

Azure SQL Analytics의 자동 경고는 충족된 조건에서 경고를 트리거하는 Log Analytics 쿼리를 작성하는 것을 기반으로 합니다. Azure SQL Analytics에서 경고를 설정할 수 있는 Log Analytics 쿼리에 대한 몇 가지 예에서 아래를 참조해 보십시오.

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
>
> - 이 경고를 설정하기 위한 사전 요구 사항은 모니터링된 데이터베이스가 기본 메트릭을 Azure SQL Analytics로 스트리밍하는 것입니다.
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
>
> - 이 경고를 설정하기 위한 사전 요구 사항은 모니터링되는 데이터베이스가 기본 메트릭을 Azure SQL Analytics로 스트리밍하는 것입니다.
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
>
> - 이 경고를 설정하기 위한 사전 요구 사항은 모니터링된 데이터베이스가 기본 메트릭을 Azure SQL Analytics로 스트리밍하는 것입니다.
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
> - 이 경고를 설정하기 위한 사전 요구 사항은 모니터링된 데이터베이스가 AZURE SQL Analytics로 SQLInsights 진단 로그를 스트리밍하는 것입니다.
> - 이 쿼리는 중복 결과를 방지하기 위해 alert_run_interval과 동일한 빈도로 실행하도록 설정한 경고 규칙이 필요합니다. 규칙은 쿼리에서 결과(> 0 결과)가 있으면 경고를 해제하도록 설정해야 합니다.
> - alert_run_interval 사용자 지정하여 SQLInsights 로그를 Azure SQL Analytics로 스트리밍하도록 구성된 데이터베이스에서 조건이 발생했는지 확인하는 시간 범위를 지정합니다.
> - Insights 근본 원인 분석 텍스트의 출력을 캡처하려면 insights_string을 사용자 지정합니다. 이 텍스트는 Azure SQL Analytics의 UI에 표시되는 텍스트와 동일한 텍스트로 기존 인사이트에서 사용할 수 있습니다. 또는 구독에서 생성된 모든 Insights의 텍스트를 보려면 아래 쿼리를 사용할 수 있습니다. 쿼리의 출력을 사용하여 Insights에 대한 경고를 설정하기 위한 고유 문자열을 수집합니다.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-managed-instances"></a>관리되는 인스턴스에 대한 경고 만들기

#### <a name="managed-instance-storage-is-above-90"></a>관리되는 인스턴스 저장소가 90% 이상입니다.

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
> - 이 경고를 설정하기 위한 사전 요구 사항은 관리되는 인스턴스가 Azure SQL Analytics에 ResourceUsageStats 로그 스트리밍을 사용하도록 설정되어 모니터링한다는 것입니다.
> - 이 쿼리에서는 쿼리에서 결과(결과 > 0)가 있을 때 경고를 발생하도록 경고 규칙을 설정하여 관리되는 인스턴스에 조건이 있음을 나타냅니다. 출력은 관리되는 인스턴스의 저장소 백분율 소비량입니다.

#### <a name="managed-instance-cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>관리 인스턴스 CPU 평균 소비는 마지막 1 시간 동안 95 % 이상입니다

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
> - 이 경고를 설정하는 사전 요구 사항은 모니터링되는 관리형 인스턴스에 Azure SQL Analytics에 ResourceUsageStats 로그 스트리밍이 활성화되어 있다는 것입니다.
> - 이 쿼리에서는 쿼리에서 결과(결과 > 0)가 있을 때 경고를 발생하도록 경고 규칙을 설정하여 관리되는 인스턴스에 조건이 있음을 나타냅니다. 출력은 관리되는 인스턴스에서 정의된 기간의 평균 CPU 사용률 비율 소비량입니다.

### <a name="pricing"></a>가격 책정

Azure SQL Analytics는 무료로 사용할 수 있지만 매월 할당된 데이터 수집의 사용 단위 를 초과하는 [Log Analytics pricing](https://azure.microsoft.com/pricing/details/monitor)진단 원격 분석의 소비가 적용됩니다. 제공되는 데이터 수집 무료 단위만큼 매월 여러 데이터베이스를 무료로 모니터링할 수 있습니다. 워크로드가 많은 활성 데이터베이스는 유휴 데이터베이스보다 더 많은 데이터를 수집합니다. Azure SQL Analytics의 탐색 메뉴에서 OMS 작업 영역을 선택한 다음 사용량 및 예상 비용을 선택하여 Azure SQL Analytics에서 데이터 수집 사용량을 쉽게 모니터링할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- Azure 모니터에서 [로그 쿼리를](../log-query/log-query-overview.md) 사용하여 자세한 Azure SQL 데이터를 봅니다.
- Azure SQL 데이터를 보여 주는 [사용자 고유의 대시보드 만들기](../learn/tutorial-logs-dashboards.md).
- 특정 Azure SQL 이벤트가 발생하는 경우의 [경고 만들기](../platform/alerts-overview.md).
