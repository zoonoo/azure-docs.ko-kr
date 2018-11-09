---
title: Azure SQL Database 메트릭 및 진단 로깅 | Microsoft Docs
description: 리소스 사용, 연결 및 쿼리 실행 통계를 저장하도록 Azure SQL Database를 구성하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: v-daljep
ms.reviewer: carlrab
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: 8f66c95202e0ccdef86f9630f7a98c20023a8955
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50087749"
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Azure SQL Database 메트릭 및 진단 로깅 

Azure SQL Database, 탄력적 풀, Managed Instance 및 Managed Instance의 데이터베이스는 성능을 더 쉽게 모니터링할 수 있도록 메트릭 및 진단 로그를 내보냅니다. 리소스 사용량, 작업자와 세션 및 연결을 이러한 Azure 리소스 중 하나로 스트림하도록 데이터베이스를 구성할 수 있습니다.

* **Azure SQL 분석**: 보고, 경고 및 완화 기능을 갖춘 통합 Azure 데이터베이스 지능형 성능 모니터링 솔루션으로 사용됩니다.
* **Azure Event Hub**: 사용자 지정 모니터링 솔루션 또는 핫 파이프라인과 SQL Database 원격 분석을 통합하는 경우 사용됩니다.
* **Azure Storage**: 적은 비용으로 방대한 양의 원격 분석 데이터를 보관하는 데 사용됩니다.

    ![아키텍처](./media/sql-database-metrics-diag-logging/architecture.png)

다양한 Azure 서비스에서 지원되는 메트릭 및 로그 범주를 이해하기 위해 다음 항목을 읽어볼 수 있습니다.

* [Microsoft Azure의 메트릭 개요](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
* [Azure 진단 로그 개요](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) 

## <a name="enable-logging-of-diagnostics-telemetry"></a>진단 원격 분석의 로깅 사용

데이터베이스에 대해 진단 원격 분석을 사용하려면 이 문서의 첫 번째 섹션을 사용하고, 탄력적 풀 또는 Managed Instance에 대해 진단 원격 분석을 사용하려면 문서의 두 번째 부분을 사용합니다. Azure SQL Analytics를 스트리밍된 데이터베이스 진단 원격 분석을 볼 수 있는 모니터링 도구로 구성하려면 이 문서의 뒷부분에 나오는 섹션을 사용합니다.

> [!NOTE]
> 데이터베이스에 대해 진단 원격 분석을 사용하도록 설정하는 것 외에, 탄력적 풀 또는 Managed Instance를 사용하는 경우 이러한 리소스에 대해 진단 원격 분석을 사용하도록 설정하는 것이 좋습니다. 데이터베이스 컨테이너의 역할에서 탄력적 풀 및 Managed Instance는 개별 데이터베이스 진단 원격 분석과는 별개인 자체 진단 원격 분석을 제공하기 때문입니다. 
>

다음 방법 중 하나를 사용하여 메트릭 및 진단 원격 분석 로깅을 사용하도록 설정하고 관리할 수 있습니다.

- Azure portal
- PowerShell
- Azure CLI
- Azure Monitor REST API 
- Azure Resource Manager 템플릿

메트릭 및 진단 로깅을 사용하도록 설정하는 경우 선택한 데이터가 수집되는 Azure 리소스 대상을 지정해야 합니다. 사용 가능한 옵션은 다음과 같습니다.

- Azure SQL Analytics
- Azure Event Hubs
- Azure Storage

새 Azure 리소스를 프로비전하거나 기존 리소스를 선택할 수 있습니다. 리소스를 선택한 후에는 진단 설정 옵션을 사용하여 수집할 데이터를 지정해야 합니다.

## <a name="enable-logging-for-azure-sql-database-or-databases-in-managed-instance"></a>Azure SQL Database 또는 Managed Instance의 데이터베이스에 대해 로깅 사용

SQL Database 및 Managed Instance의 데이터베이스에 대한 메트릭과 진단 로깅은 기본적으로 사용되지 않습니다.

다음 진단 원격 분석은 Azure SQL Databases 및 Managed Instance 데이터베이스 수집에 사용할 수 있습니다.

| 데이터베이스에 대한 원격 분석 모니터링 | Azure SQL Database 지원 | Managed Instance 지원 데이터베이스 |
| :------------------- | ------------------- | ------------------- |
| [모든 메트릭](sql-database-metrics-diag-logging.md#all-metrics): DTU/CPU 백분율, DTU/CPU 제한, 물리 데이터 읽기 백분율, 로그 쓰기 백분율, 방화벽에서 성공/실패/차단된 연결, 세션 백분율, 작업자 백분율, 저장소, 저장소 백분율, XTP 저장소 백분율을 포함합니다. | yes | 아니요 |
| [QueryStoreRuntimeStatistics](sql-database-metrics-diag-logging.md#query-store-runtime-statistics): CPU 사용량 및 쿼리 기간 통계와 같은 쿼리 런타임 통계에 대한 정보를 포함합니다. | yes | yes |
| [QueryStoreWaitStatistics](sql-database-metrics-diag-logging.md#query-store-wait-statistics): CPU, LOG, LOCKING 등 쿼리가 대기된 항목을 알리는 쿼리 대기 통계에 대한 정보를 포함합니다. | yes | yes |
| [Errors](sql-database-metrics-diag-logging.md#errors-dataset): 이 데이터베이스에서 발생한 SQL 오류에 대한 정보를 포함합니다. | yes | 아니요 |
| [DatabaseWaitStatistics](sql-database-metrics-diag-logging.md#database-wait-statistics-dataset): 대기 형식에 따라 데이터베이스가 대기하는 데 사용된 시간에 대한 정보를 포함합니다. | yes | 아니요 |
| [Timeouts](sql-database-metrics-diag-logging.md#time-outs-dataset): 데이터베이스에서 발생한 시간 제한에 대한 정보를 포함합니다. | yes | 아니요 |
| [Blocks](sql-database-metrics-diag-logging.md#blockings-dataset): 데이터베이스에서 발생한 차단 이벤트에 대한 정보를 포함합니다. | yes | 아니요 |
| [SQLInsights](sql-database-metrics-diag-logging.md#intelligent-insights-dataset): 성능에 대한 Intelligent Insights를 포함합니다. [Intelligent Insights에 대해 자세히 알아보세요.](sql-database-intelligent-insights.md) | yes | yes |

### <a name="azure-portal"></a>Azure portal

Azure SQL Database 및 Managed Instance의 데이터베이스에 대한 진단 원격 분석을 Azure 저장소, 이벤트 허브 또는 Log Analytics로 스트리밍하는 작업은 Azure Portal에서 각 데이터베이스에 대한 진단 설정 메뉴를 통해 수행됩니다.

### <a name="configure-streaming-of-diagnostics-telemetry-for-azure-sql-database"></a>Azure SQL Database에 대한 진단 원격 분석 스트리밍 구성

   ![SQL Database 아이콘](./media/sql-database-metrics-diag-logging/icon-sql-database-text.png)

**Azure SQL Database**에 진단 원격 분석 스트리밍을 사용하도록 설정하려면 다음 단계를 따릅니다.

1. Azure SQL Database 리소스로 이동
2. **진단 설정**을 선택합니다.
3. 이전 설정이 없으면 **진단 켜기**를 선택하고, 이전 설정이 있으면 **설정 편집**을 선택하여 이전 설정을 편집합니다.
- 진단 원격 분석을 스트림하는 병렬 연결을 3개까지 만들 수 있습니다. 진단 데이터를 여러 리소스로 병렬 스트리밍하도록 구성하려면 **+진단 설정 추가**를 선택하여 추가 설정을 만듭니다.

   ![SQL Database에 대해 진단 사용](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-enable.png)

4. 본인이 참조할 설정 이름을 입력합니다.
5. 데이터베이스에서 진단 데이터를 스트리밍할 리소스를 선택합니다(**저장소 계정에 보관**, **이벤트 허브로 스트림**, **Log Analytics에 보내기** 중에 선택).
6. 표준 모니터링 환경의 경우 데이터베이스 진단 로그 원격 분석의 확인란 선택: **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**, **오류**, **DatabaseWaitStatistics**, **시간 제한**, **블록**, **교착 상태**. 이 원격 분석은 이벤트 기반이며 표준 모니터링 환경을 제공합니다.
7. 고급 모니터링 환경을 원하는 경우 **AllMetrics** 확인란을 선택합니다. 이것은 위의 설명처럼 데이터베이스 진단 원격 분석을 위한 1분 기준 원격 분석입니다. 
8. 설정 메뉴에서 **저장**

   ![SQL Database에 대해 진단 구성](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-selection.png)

> [!NOTE]
> 데이터베이스 진단 설정에서 감사 로그를 사용하도록 설정할 수 없습니다. 감사 로그 스트리밍을 사용하도록 설정하려면 [데이터베이스에 대해 감사 설정](sql-database-auditing.md#subheading-2) 및 [Azure Log Analytics 및 Azure Event Hubs의 SQL 감사 로그](https://blogs.msdn.microsoft.com/sqlsecurity/2018/09/13/sql-audit-logs-in-azure-log-analytics-and-azure-event-hubs/)를 참조하세요.
>

> [!TIP]
> 모니터링하려는 각 Azure SQL Database에 대해 위의 단계를 반복. 
>

### <a name="configure-streaming-of-diagnostics-telemetry-for-databases-in-managed-instance"></a>Managed Instance의 데이터베이스에 대한 진단 원격 분석 스트리밍 구성

   ![Managed Instance의 데이터베이스 아이콘](./media/sql-database-metrics-diag-logging/icon-mi-database-text.png)

**Managed Instance의 데이터베이스**에 진단 원격 분석 스트리밍을 사용하도록 설정하려면 다음 단계를 따릅니다.

1. Managed Instance의 데이터베이스로 이동합니다.
2. **진단 설정**을 선택합니다.
3. 이전 설정이 없으면 **진단 켜기**를 선택하고, 이전 설정이 있으면 **설정 편집**을 선택하여 이전 설정을 편집합니다.
- 진단 원격 분석을 스트림하는 병렬 연결을 3개까지 만들 수 있습니다. 진단 데이터를 여러 리소스로 병렬 스트리밍하도록 구성하려면 **+진단 설정 추가**를 선택하여 추가 설정을 만듭니다.

   ![Managed Instance 데이터베이스에 대해 진단 사용](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

4. 본인이 참조할 설정 이름을 입력합니다.
5. 데이터베이스에서 진단 데이터를 스트리밍할 리소스를 선택합니다(**저장소 계정에 보관**, **이벤트 허브로 스트림**, **Log Analytics에 보내기** 중에 선택).
6. 데이터베이스 진단 원격 분석 **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics** 및 **오류**에 대한 확인란을 선택합니다.
7. 설정 메뉴에서 **저장**

   ![Managed Instance 데이터베이스에 대해 진단 구성](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)

> [!TIP]
> 모니터링하려는 Managed Instance의 각 데이터베이스에 대해 위의 단계를 반복합니다.
>

## <a name="enable-logging-for-elastic-pools-or-managed-instance"></a>탄력적 풀 또는 Managed Instance에 대한 로깅 사용

데이터베이스 컨테이너로 사용되는 탄력적 풀 및 Managed Instance는 데이터베이스와는 별개로 고유의 진단 원격 분석을 제공합니다. 이 진단 원격 분석은 기본적으로 사용하지 않도록 설정됩니다. 

### <a name="configure-streaming-of-diagnostics-telemetry-for-elastic-pools"></a>탄력적 풀에 대한 진단 원격 분석 스트리밍 구성

   ![탄력적 풀 아이콘](./media/sql-database-metrics-diag-logging/icon-elastic-pool-text.png)

다음 진단 원격 분석은 탄력적 풀 리소스 수집에 사용할 수 있습니다.

| 리소스 | 모니터링 원격 분석 |
| :------------------- | ------------------- |
| **탄력적 풀** | [모든 메트릭](sql-database-metrics-diag-logging.md#all-metrics)은 eDTU/CPU 백분율, eDTU/CPU 제한, 물리 데이터 읽기 백분율, 로그 쓰기 백분율, 세션 백분율, 작업자 백분율, 저장소, 저장소 백분율, 저장소 제한 및 XTP 저장소 백분율을 포함합니다. |

**탄력적 풀 리소스**에 진단 원격 분석 스트리밍을 사용하도록 설정하려면 다음 단계를 따릅니다.

1. Azure Portal에서 탄력적 풀 리소스로 이동합니다.
2. **진단 설정**을 선택합니다.
3. 이전 설정이 없으면 **진단 켜기**를 선택하고, 이전 설정이 있으면 **설정 편집**을 선택하여 이전 설정을 편집합니다.

   ![탄력적 풀에 대해 진단 사용](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-enable.png)

4. 본인이 참조할 설정 이름을 입력합니다.
5. 탄력적 풀에서 진단 데이터를 스트리밍할 리소스를 선택합니다(**저장소 계정에 보관**, **이벤트 허브로 스트림**, **Log Analytics에 보내기**).
6. Log Analytics를 선택하는 경우 **+새 작업 영역 만들기**를 선택하여 **구성**을 선택하고 새 작업 영역을 만들거나 기존 작업 영역을 선택합니다
7. 탄력적 풀 진단 원격 분석 **AllMetrics**의 확인란을 선택합니다.
8. 페이지 맨 아래에 있는 **저장**

   ![탄력적 풀에 대해 진단 구성](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-selection.png)

> [!TIP]
> 모니터링하려는 각 탄력적 풀에 대해 위의 단계를 반복합니다.
>

### <a name="configure-streaming-of-diagnostics-telemetry-for-managed-instance"></a>Managed Instance에 대한 진단 원격 분석 스트리밍 구성

   ![Managed Instance 아이콘](./media/sql-database-metrics-diag-logging/icon-managed-instance-text.png)

다음 진단 원격 분석은 Managed Instance 리소스 수집에 사용할 수 있습니다.

| 리소스 | 모니터링 원격 분석 |
| :------------------- | ------------------- |
| **Managed Instance** | [ResourceUsageStats](sql-database-metrics-diag-logging.md#resource-usage-stats)는 vCore 수, 평균 CPU 백분율, IO 요청 수, 읽은/쓴 바이트, 예약된 저장소 공간, 사용된 저장소 공간을 포함합니다. |

**Managed Instance 리소스**에 진단 원격 분석 스트리밍을 사용하도록 설정하려면 다음 단계를 따릅니다.

1. Azure Portal에서 Managed Instance 리소스로 이동합니다.
2. **진단 설정**을 선택합니다.
3. 이전 설정이 없으면 **진단 켜기**를 선택하고, 이전 설정이 있으면 **설정 편집**을 선택하여 이전 설정을 편집합니다.

   ![Managed Instance에 대해 진단 사용](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

4. 본인이 참조할 설정 이름을 입력합니다.
5. 탄력적 풀에서 진단 데이터를 스트리밍할 리소스를 선택합니다(**저장소 계정에 보관**, **이벤트 허브로 스트림**, **Log Analytics에 보내기**).
6. Log Analytics를 선택하는 경우 작업 영역을 새로 만들거나 기존 작업 영역을 선택합니다.
7. 인스턴스 진단 원격 분석 **ResourceUsageStats**의 확인란을 선택합니다.
8. 페이지 맨 아래에 있는 **저장**

   ![Managed Instance에 대해 진단 구성](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)

> [!TIP]
> 모니터링하려는 각 Managed Instance에 대해 위의 단계를 반복합니다.
>

### <a name="powershell"></a>PowerShell

PowerShell을 사용하여 메트릭 및 진단 로깅을 사용하도록 설정하려면 다음 명령을 사용합니다.

- 저장소 계정에서 진단 로그의 저장소를 사용하도록 설정하려면 다음 명령을 사용합니다.

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   저장소 계정 ID는 로그를 보낼 저장소 계정에 대한 리소스 ID입니다.

- 이벤트 허브로의 진단 로그 스트리밍을 활성화하려면 다음 명령을 사용합니다.

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Azure Service Bus 규칙 ID는 다음 형식의 문자열입니다.

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- 진단 로그를 Log Analytics 작업 영역으로 보낼 수 있게 하려면 이 명령을 사용합니다.

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- 다음 명령을 사용하여 Log Analytics 작업 영역의 리소스 ID를 가져올 수 있습니다.

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

이러한 매개 변수를 결합하여 여러 출력 옵션을 활성화할 수 있습니다.

### <a name="to-configure-multiple-azure-resources"></a>Azure 리소스를 여러 개 구성하려면

여러 구독을 지원하려면 [PowerShell을 사용하여 Azure 리소스 메트릭 로깅 사용](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/)에서 PowerShell 스크립트를 사용합니다.

여러 리소스의 진단 데이터를 작업 영역으로 보내는 스크립트(Enable-AzureRMDiagnostics.ps1)를 실행할 때 작업 영역 리소스 ID &lt;$WSID&gt;를 매개 변수로 제공합니다. 진단 데이터를 보내려는 작업 영역 ID &lt;$WSID&gt;를 가져오려면 다음 스크립트에서 &lt;subID&gt;를 구독 ID, &lt;RG_NAME&gt;을 리소스 그룹 이름, &lt;WS_NAME&gt;을 작업 영역 이름으로 바꿉니다.

- Azure 리소스를 여러 개 구성하려면 다음 명령을 사용하세요.

    ```powershell
    PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
    PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```

### <a name="azure-cli"></a>Azure CLI

Azure CLI를 사용하여 메트릭 및 진단 로깅을 사용하도록 설정하려면 다음 명령을 사용합니다.

- 저장소 계정에서 진단 로그의 저장소를 사용하도록 설정하려면 다음 명령을 사용합니다.

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   저장소 계정 ID는 로그를 보낼 저장소 계정에 대한 리소스 ID입니다.

- 이벤트 허브로의 진단 로그 스트리밍을 활성화하려면 다음 명령을 사용합니다.

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   Service Bus 규칙 ID는 이 형식의 문자열입니다.

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- 진단 로그를 Log Analytics 작업 영역으로 보낼 수 있게 하려면 이 명령을 사용합니다.

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

이러한 매개 변수를 결합하여 여러 출력 옵션을 활성화할 수 있습니다.

### <a name="rest-api"></a>REST API

[Azure Monitor REST API를 사용하여 진단 설정 변경](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)을 참조하세요. 

### <a name="resource-manager-template"></a>Resource Manager 템플릿

[Resource Manager 템플릿을 사용하여 리소스 생성 시 진단 설정을 활성화하는 방법](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md)을 참조하세요.  

## <a name="stream-into-azure-sql-analytics"></a>Azure SQL Analytics로 스트리밍 

Azure SQL 분석은 단일 창을 통해 여러 구독 간 규모에 맞게 Azure SQL Database, 탄력적 풀 및 Managed Instances의 성능을 모니터링하기 위한 클라우드 모니터링 솔루션입니다. 이 솔루션은 성능 문제 해결에 대한 기본 제공 인텔리전스를 사용하여 중요한 Azure SQL Database 성능 메트릭을 수집하고 시각화합니다.

![Azure SQL 분석 개요](../log-analytics/media/log-analytics-azure-sql/azure-sql-sol-overview.png)

SQL Database 메트릭 및 진단 로그는 포털의 진단 설정 블레이드에 기본 제공되는 **Log Analytics로 보내기** 옵션을 사용하여 Azure SQL 분석으로 스트리밍될 수 있습니다. 또한 PowerShell cmdlet, Azure CLI 또는 Azure Monitor REST API를 통해 진단 설정을 사용하여 Log Analytics를 사용하도록 설정할 수도 있습니다.

### <a name="installation-overview"></a>설치 개요

Azure SQL 문석을 사용하여 SQL Database를 간편하게 모니터링할 수 있습니다. 세 단계를 수행해야 합니다.

1. Azure Marketplace에서 Azure SQL 분석 솔루션 만들기
2. 솔루션에서 모니터링 작업 영역 만들기
3. 사용자가 만든 작업 영역으로 진단 원격 분석을 스트리밍하도록 데이터베이스를 구성합니다.

데이터베이스 진단 원격 분석을 구성하는 것 외에, 탄력적 풀 또는 Managed Instance를 사용하는 경우 이러한 리소스에서 진단 원격 분석 스트리밍도 구성합니다.

### <a name="create-azure-sql-analytics-resource"></a>Azure SQL 분석 리소스 만들기

1. Azure Marketplace에서 Azure SQL 분석을 검색한 후 선택

   ![포털에서 Azure SQL 분석 검색](./media/sql-database-metrics-diag-logging/sql-analytics-in-marketplace.png)
   
2. 솔루션의 개요 화면에서 **만들기** 선택

3. 필요한 추가 정보(작업 영역 이름, 구독, 리소스 그룹, 위치 및 가격 책정 계층)를 Azure SQL 분석 양식에 입력합니다.
 
   ![포털에서 Azure SQL 분석 구성](./media/sql-database-metrics-diag-logging/sql-analytics-configuration-blade.png)

4. **확인**을 선택하여 확인하고 **만들기**를 선택하여 완료

### <a name="configure-databases-to-record-metrics-and-diagnostics-logs"></a>메트릭 및 진단 로그를 기록하도록 데이터베이스 구성

데이터베이스에서 메트릭을 기록하는 위치를 구성하는 가장 쉬운 방법은 위에 설명된 것처럼 Azure Portal을 사용하는 것입니다. 포털에서 SQL Database 리소스로 이동하 고 **진단 설정**을 선택합니다.

탄력적 풀 또는 Managed Instance를 사용하는 경우, 사용자가 만든 작업 영역으로 고유한 진단 원격 분석을 스트리밍할 뿐만 아니라 이러한 리소스에서 진단 설정을 구성해야 합니다.

### <a name="use-the-sql-analytics-solution"></a>SQL Analytics 솔루션 사용

SQL Analytics는 SQL Database 리소스의 계층을 탐색할 수 있는 계층적 대시보드입니다. SQL Analytics 솔루션 사용 방법은 [SQL Analytics 솔루션을 사용한 SQL Database 모니터링](../log-analytics/log-analytics-azure-sql.md)을 참조하세요.

## <a name="stream-into-event-hubs"></a>Event Hubs로 스트림

SQL Database 메트릭 및 진단 로그는 포털에서 기본 제공되는 **Event Hubs로 스트림** 옵션을 사용하여 Event Hubs로 스트림할 수 있습니다. 또한 PowerShell cmdlet, Azure CLI 또는 Azure Monitor REST API를 통해 진단 설정을 사용하여 Service Bus 규칙 ID를 사용하도록 설정할 수도 있습니다. 

### <a name="what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs"></a>Event Hubs에서 메트릭 및 진단 로그를 사용하여 수행할 수 있는 작업
선택한 데이터가 Event Hubs로 스트리밍되면 고급 모니터링 시나리오를 사용할 수 있는 단계에 한 단계 더 가까워집니다. Event Hubssms 이벤트 파이프라인의 프런트 도어 역할을 합니다. 데이터가 Event Hub에 수집되면 실시간 분석 공급자 또는 일괄 처리/저장소 어댑터를 사용하여 변환하고 저장할 수 있습니다. Event Hubs는 해당 이벤트 소비에서 이벤트 스트림 프로덕션을 분리합니다. 이러한 방식으로 이벤트 소비자는 자체 일정에 따라 이벤트를 액세스할 수 있습니다. Event Hubs에 대한 자세한 내용은 다음을 참조하세요.

- [Azure Event Hubs란?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Event Hubs 시작](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

스트림 기능을 사용할 수 있는 몇 가지 방법은 다음과 같습니다.

* **Power BI로 핫 패스 데이터를 스트림하여 서비스 상태를 확인합니다**. Event Hubs, Stream Analytics 및 PowerBI를 사용하여 Azure 서비스에서 메트릭 및 진단 데이터를 거의 실시간 정보로 간편하게 변환할 수 있습니다. Event Hubs를 설정하고 Stream Analytics로 데이터를 처리하며 출력으로 PowerBI를 사용하는 방법에 대한 개요는 [Stream Analytics 및 Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)를 참조하세요.

* **제3자 로깅 및 원격 분석에 로그를 스트림합니다**. Event Hubs 스트림을 사용하여 메트릭 및 진단 로그를 다른 타사 모니터링 및 로그 분석 솔루션으로 가져올 수 있습니다. 

* **사용자 지정 원격 분석 및 로깅 플랫폼을 빌드합니다**. 사용자 지정 빌드 원격 분석 플랫폼이 이미 있거나 플랫폼 빌드에 대해 생각하고 있는 경우 Event Hubs의 확장성 높은 게시-구독 특성을 통해 진단 로그를 유연하게 수집할 수 있습니다. [글로벌 확장 원격 분석 플랫폼에 Event Hubs 사용에 대한 Dan Rosanova의 가이드](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)를 참조하세요.

## <a name="stream-into-storage"></a>Storage에 스트림

포털에서 기본 제공되는 **저장소 계정에 보관** 옵션을 사용하여 SQL Database 메트릭 및 진단 로그를 Storage에 저장할 수 있습니다. 또한 PowerShell cmdlet, Azure CLI 또는 Azure Monitor REST API를 통해 진단 설정을 사용하여Storage를 사용하도록 설정할 수도 있습니다.

### <a name="schema-of-metrics-and-diagnostics-logs-in-the-storage-account"></a>저장소 계정의 메트릭 및 진단 로그 스키마

메트릭 및 진단 로그 수집을 설정하면 첫 번째 데이터 행을 사용할 수 있을 때 선택한 저장소 계정에 저장소 컨테이너가 만들어집니다. 해당 Blob의 구조는 다음과 같습니다.

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
    
또는 더 간단하게 다음과 같습니다.

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

예를 들어 모든 메트릭에 대한 Blob 이름은 다음과 같을 수 있습니다.

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

탄력적 풀에서 데이터를 기록하려는 경우에는 Blob 이름이 약간 다릅니다.

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

### <a name="download-metrics-and-logs-from-storage"></a>Storage에서 메트릭 및 로그 다운로드

[Storage에서 메트릭 및 진단 로그 다운로드](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application)를 참조하세요.

## <a name="data-retention-policy-and-pricing"></a>데이터 보존 정책 및 가격 책정

Event Hubs 또는 저장소 계정을 선택하면 보존 정책을 지정할 수 있습니다. 이 정책은 선택한 기간보다 오래된 데이터를 삭제합니다. Log Analytics를 지정한 경우 선택한 가격 책정 계층에 따라 보존 정책이 달라집니다. 매월 할당되는 무료 데이터 수집 단위를 초과하는 진단 원격 분석 사용량이 적용됩니다. 제공되는 데이터 수집 무료 단위만큼 매월 여러 데이터베이스를 무료로 모니터링할 수 있습니다. 워크로드가 많은 활성데이터는 유휴 데이터베이스보다 더 많은 데이터를 수집합니다. 자세한 내용은 [Log Analytics 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)을 참조하세요. 

Azure SQL Analytics를 사용하는 경우 Azure SQL 분석의 탐색 메뉴에서 OMS 작업 영역을 선택한 다음, [사용량 및 예상 비용]을 선택하여 솔루션에서 데이터 수집 사용량을 쉽게 모니터링할 수 있습니다.

## <a name="metrics-and-logs-available"></a>사용 가능한 메트릭 및 로그

수집한 모니터링 원격 분석을 [SQL Analytics 언어](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)를 사용하여 사용자 고유의 **사용자 지정 분석** 및 **응용 프로그램 개발**에 사용할 수 있습니다. 수집된 데이터, 메트릭 및 로그의 구조는 아래에 나와 있습니다.

## <a name="all-metrics"></a>모든 메트릭

### <a name="all-metrics-for-elastic-pools"></a>탄력적 풀에 대한 모든 메트릭

|**리소스**|**Metrics**(메트릭)|
|---|---|
|탄력적 풀|eDTU 백분율, 사용된 eDTU, eDTU 제한, CPU 백분율, 실제 데이터 읽기 백분율, 로그 쓰기 백분율, 세션 백분율, 작업자 백분율, 저장소, 저장소 백분율, 저장소 용량 한도, XTP 저장소 백분율을 포함합니다. |

### <a name="all-metrics-for-azure-sql-database"></a>Azure SQL Database에 대한 모든 메트릭

|**리소스**|**Metrics**(메트릭)|
|---|---|
|Azure SQL Database|DTU 백분율, 사용된 DTU, DTU 제한, CPU 백분율, 실제 데이터 읽기 백분율, 로그 쓰기 백분율, 성공/실패/방화벽 연결에 의해 차단됨, 세션 백분율, 작업자 백분율, 저장소, 저장소 백분율, XTP 저장소 백분율, 교착 상태를 포함합니다. |

## <a name="logs"></a>로그

### <a name="logs-for-managed-instance"></a>Managed Instance에 대한 로그

### <a name="resource-usage-stats"></a>리소스 사용량 통계

|자산|설명|
|---|---|
|TenantId|테넌트 ID|
|SourceSystem|항상: Azure|
|TimeGenerated [UTC]|로그가 기록된 때의 타임스탬프|
|type|항상: AzureDiagnostics|
|ResourceProvider|리소스 공급자의 이름. 항상: MICROSOFT.SQL|
|Category|범주 이름. 항상: ResourceUsageStats|
|리소스|리소스의 이름입니다.|
|ResourceType|리소스 형식 이름. 항상: MANAGEDINSTANCES|
|SubscriptionId|데이터베이스가 속한 구독 GUID|
|ResourceGroup|데이터베이스가 속한 리소스 그룹의 이름|
|LogicalServerName_s|Managed Instance의 이름|
|ResourceId|리소스 URI |
|SKU_s|Managed Instance 제품 SKU|
|virtual_core_count_s|사용 가능한 vCore 수|
|avg_cpu_percent_s|평균 CPU 비율|
|reserved_storage_mb_s|Managed Instance의 예약된 저장소 용량|
|storage_space_used_mb_s|Managed Instance의 사용된 저장소|
|io_requests_s|IOPS 수|
|io_bytes_read_s|읽은 IOPS 바이트 수|
|io_bytes_written_s|쓴 IOPS 바이트 수|

### <a name="logs-for-azure-sql-database-and-managed-instance-database"></a>Azure SQL Database 및 Managed Instance 데이터베이스에 대한 로그

### <a name="query-store-runtime-statistics"></a>쿼리 저장소 런타임 통계

|자산|설명|
|---|---|
|TenantId|테넌트 ID|
|SourceSystem|항상: Azure|
|TimeGenerated [UTC]|로그가 기록된 때의 타임스탬프|
|type|항상: AzureDiagnostics|
|ResourceProvider|리소스 공급자의 이름. 항상: MICROSOFT.SQL|
|Category|범주 이름. 항상: QueryStoreRuntimeStatistics|
|OperationName|작업의 이름입니다. 항상: QueryStoreRuntimeStatisticsEvent|
|리소스|리소스의 이름입니다.|
|ResourceType|리소스 형식 이름. 항상: SERVERS/DATABASES|
|SubscriptionId|데이터베이스가 속한 구독 GUID|
|ResourceGroup|데이터베이스가 속한 리소스 그룹의 이름|
|LogicalServerName_s|데이터베이스가 속한 서버의 이름|
|ElasticPoolName_s|데이터베이스가 속한 탄력적 풀의 이름의 이름(있는 경우)|
|DatabaseName_s|데이터베이스 이름|
|ResourceId|리소스 URI |
|query_hash_s|쿼리 해시|
|query_plan_hash_s|쿼리 계획 해시|
|statement_sql_handle_s|문 SQL 핸들|
|interval_start_time_d|1900-1-1부터 틱 수로 간격의 시작 datetimeoffset.|
|interval_end_time_d|1900-1-1부터 틱 수로 간격의 종료 datetimeoffset.|
|logical_io_writes_d|총 논리적 IO 쓰기 수.|
|max_logical_io_writes_d|실행당 최대 논리적 IO 쓰기 수.|
|physical_io_reads_d|총 물리적 IO 읽기 수.|
|max_physical_io_reads_d|실행당 최대 논리적 IO 읽기 수.|
|logical_io_reads_d|총 논리적 IO 읽기 수.|
|max_logical_io_reads_d|실행당 최대 논리적 IO 읽기 수.|
|execution_type_d|실행 형식|
|count_executions_d|쿼리의 실행 수.|
|cpu_time_d|쿼리에 사용된 총 CPU 시간(밀리초).|
|max_cpu_time_d|단일 실행에 사용된 최대 CPU 시간(밀리초).|
|dop_d|병렬 처리 수준 합계.|
|max_dop_d|단일 실행에 사용된 최대 병렬 처리 수준.|
|rowcount_d|반환된 총 행 수.|
|max_rowcount_d|단일 실행으로 반환된 최대 행 수.|
|query_max_used_memory_d|사용된 총 메모리 양(KB).|
|max_query_max_used_memory_d|단일 실행에 사용된 최대 메모리 양(KB).|
|duration_d|총 실행 시간(밀리초).|
|max_duration_d|단일 실행의 최대 실행 시간.|
|num_physical_io_reads_d|총 물리적 읽기 수.|
|max_num_physical_io_reads_d|실행당 최대 물리적 읽기 수.|
|log_bytes_used_d|사용된 총 로그 바이트 양.|
|max_log_bytes_used_d|실행당 사용된 최대 로그 바이트 양.|
|query_id_d|쿼리 저장소의 쿼리 ID|
|plan_id_d|쿼리 저장소의 계획 ID|

[쿼리 저장소 런타임 통계 데이터에 대해 자세히 알아봅니다.](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql)

### <a name="query-store-wait-statistics"></a>쿼리 저장소 대기 통계

|자산|설명|
|---|---|
|TenantId|테넌트 ID|
|SourceSystem|항상: Azure|
|TimeGenerated [UTC]|로그가 기록된 때의 타임스탬프|
|type|항상: AzureDiagnostics|
|ResourceProvider|리소스 공급자의 이름. 항상: MICROSOFT.SQL|
|Category|범주 이름. 항상: QueryStoreWaitStatistics|
|OperationName|작업의 이름입니다. 항상: QueryStoreWaitStatisticsEvent|
|리소스|리소스 이름|
|ResourceType|리소스 형식 이름. 항상: SERVERS/DATABASES|
|SubscriptionId|데이터베이스가 속한 구독 GUID|
|ResourceGroup|데이터베이스가 속한 리소스 그룹의 이름|
|LogicalServerName_s|데이터베이스가 속한 서버의 이름|
|ElasticPoolName_s|데이터베이스가 속한 탄력적 풀의 이름의 이름(있는 경우)|
|DatabaseName_s|데이터베이스 이름|
|ResourceId|리소스 URI |
|wait_category_s|대기 범주.|
|is_parameterizable_s|매개 변수화할 수 있는 쿼리.|
|statement_type_s|문 형식.|
|statement_key_hash_s|문 키 해시.|
|exec_type_d|실행 형식|
|total_query_wait_time_ms_d|특정 대기 범주의 총 쿼리 대기 시간.|
|max_query_wait_time_ms_d|특정 대기 범주 개별 실행의 최대 쿼리 대기 시간.|
|query_param_type_d|0|
|query_hash_s|쿼리 저장소의 쿼리 해시.|
|query_plan_hash_s|쿼리 저장소의 쿼리 계획 해시|
|statement_sql_handle_s|쿼리 저장소의 문 핸들|
|interval_start_time_d|1900-1-1부터 틱 수로 간격의 시작 datetimeoffset.|
|interval_end_time_d|1900-1-1부터 틱 수로 간격의 종료 datetimeoffset.|
|count_executions_d|쿼리 실행 수|
|query_id_d|쿼리 저장소의 쿼리 ID|
|plan_id_d|쿼리 저장소의 계획 ID|

[쿼리 저장소 대기 통계 데이터를 자세히 알아봅니다.](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql)

### <a name="errors-dataset"></a>오류 데이터 집합

|자산|설명|
|---|---|
|TenantId|테넌트 ID|
|SourceSystem|항상: Azure|
|TimeGenerated [UTC]|로그가 기록된 때의 타임스탬프|
|type|항상: AzureDiagnostics|
|ResourceProvider|리소스 공급자의 이름. 항상: MICROSOFT.SQL|
|Category|범주 이름. 항상: Errors|
|OperationName|작업의 이름입니다. 항상: ErrorEvent|
|리소스|리소스 이름|
|ResourceType|리소스 형식 이름. 항상: SERVERS/DATABASES|
|SubscriptionId|데이터베이스가 속한 구독 GUID|
|ResourceGroup|데이터베이스가 속한 리소스 그룹의 이름|
|LogicalServerName_s|데이터베이스가 속한 서버의 이름|
|ElasticPoolName_s|데이터베이스가 속한 탄력적 풀의 이름의 이름(있는 경우)|
|DatabaseName_s|데이터베이스 이름|
|ResourceId|리소스 URI |
|Message|일반 텍스트의 오류 메시지|
|user_defined_b|오류 사용자 정의 비트|
|error_number_d|오류 코드|
|심각도|오류의 심각도|
|state_d|오류 상태|
|query_hash_s|실패한 쿼리의 쿼리 해시(있는 경우)|
|query_plan_hash_s|실패한 쿼리의 쿼리 계획 해시(있는 경우).|

[SQL Server 오류 메시지에 대해 자세히 알아봅니다](https://msdn.microsoft.com/library/cc645603.aspx).

### <a name="database-wait-statistics-dataset"></a>데이터베이스 대기 통계 데이터 집합

|자산|설명|
|---|---|
|TenantId|테넌트 ID|
|SourceSystem|항상: Azure|
|TimeGenerated [UTC]|로그가 기록된 때의 타임스탬프|
|type|항상: AzureDiagnostics|
|ResourceProvider|리소스 공급자의 이름. 항상: MICROSOFT.SQL|
|Category|범주 이름. 항상: DatabaseWaitStatistics|
|OperationName|작업의 이름입니다. 항상: DatabaseWaitStatisticsEvent|
|리소스|리소스 이름|
|ResourceType|리소스 형식 이름. 항상: SERVERS/DATABASES|
|SubscriptionId|데이터베이스가 속한 구독 GUID|
|ResourceGroup|데이터베이스가 속한 리소스 그룹의 이름|
|LogicalServerName_s|데이터베이스가 속한 서버의 이름|
|ElasticPoolName_s|데이터베이스가 속한 탄력적 풀의 이름의 이름(있는 경우)|
|DatabaseName_s|데이터베이스 이름|
|ResourceId|리소스 URI |
|wait_type_s|대기 형식 이름|
|start_utc_date_t [UTC]|측정 기간 시작 시간.|
|end_utc_date_t [UTC]|측정 기간 종료 시간.|
|delta_max_wait_time_ms_d|실행당 최대 대기 시간|
|delta_signal_wait_time_ms_d|총 신호 대기 시간|
|delta_wait_time_ms_d|기간의 총 대기 시간|
|delta_waiting_tasks_count_d|대기 작업 수|

[데이터베이스 대기 통계에 대해 자세히 알아봅니다.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)

### <a name="time-outs-dataset"></a>제한 시간 데이터 집합 

|자산|설명|
|---|---|
|TenantId|테넌트 ID|
|SourceSystem|항상: Azure|
|TimeGenerated [UTC]|로그가 기록된 때의 타임스탬프|
|type|항상: AzureDiagnostics|
|ResourceProvider|리소스 공급자의 이름. 항상: MICROSOFT.SQL|
|Category|범주 이름. 항상: Timeouts|
|OperationName|작업의 이름입니다. 항상: TimeoutEvent|
|리소스|리소스 이름|
|ResourceType|리소스 형식 이름. 항상: SERVERS/DATABASES|
|SubscriptionId|데이터베이스가 속한 구독 GUID|
|ResourceGroup|데이터베이스가 속한 리소스 그룹의 이름|
|LogicalServerName_s|데이터베이스가 속한 서버의 이름|
|ElasticPoolName_s|데이터베이스가 속한 탄력적 풀의 이름의 이름(있는 경우)|
|DatabaseName_s|데이터베이스 이름|
|ResourceId|리소스 URI |
|error_state_d|오류 상태 코드|
|query_hash_s|쿼리 해시(있는 경우)|
|query_plan_hash_s|쿼리 계획 해시(있는 경우)|

### <a name="blockings-dataset"></a>차단 데이터 집합

|자산|설명|
|---|---|
|TenantId|테넌트 ID|
|SourceSystem|항상: Azure|
|TimeGenerated [UTC]|로그가 기록된 때의 타임스탬프|
|type|항상: AzureDiagnostics|
|ResourceProvider|리소스 공급자의 이름. 항상: MICROSOFT.SQL|
|Category|범주 이름. 항상: Blocks|
|OperationName|작업의 이름입니다. 항상: BlockEvent|
|리소스|리소스 이름|
|ResourceType|리소스 형식 이름. 항상: SERVERS/DATABASES|
|SubscriptionId|데이터베이스가 속한 구독 GUID|
|ResourceGroup|데이터베이스가 속한 리소스 그룹의 이름|
|LogicalServerName_s|데이터베이스가 속한 서버의 이름|
|ElasticPoolName_s|데이터베이스가 속한 탄력적 풀의 이름의 이름(있는 경우)|
|DatabaseName_s|데이터베이스 이름|
|ResourceId|리소스 URI |
|lock_mode_s|쿼리에 사용된 잠금 모드|
|resource_owner_type_s|잠금 소유자.|
|blocked_process_filtered_s|차단된 프로세스 보고서 XML.|
|duration_d|잠금 기간(밀리초).|

### <a name="deadlocks-dataset"></a>교착 상태 데이터 집합

|자산|설명|
|---|---|
|TenantId|테넌트 ID|
|SourceSystem|항상: Azure|
|TimeGenerated [UTC] |로그가 기록된 때의 타임스탬프|
|type|항상: AzureDiagnostics|
|ResourceProvider|리소스 공급자의 이름. 항상: MICROSOFT.SQL|
|Category|범주 이름. 항상: 교착 상태|
|OperationName|작업의 이름입니다. 항상: DeadlockEvent|
|리소스|리소스의 이름입니다.|
|ResourceType|리소스 형식 이름. 항상: SERVERS/DATABASES|
|SubscriptionId|데이터베이스가 속한 구독 GUID|
|ResourceGroup|데이터베이스가 속한 리소스 그룹의 이름|
|LogicalServerName_s|데이터베이스가 속한 서버의 이름|
|ElasticPoolName_s|데이터베이스가 속한 탄력적 풀의 이름의 이름(있는 경우)|
|DatabaseName_s|데이터베이스 이름 |
|ResourceId|리소스 URI |
|deadlock_xml_s|교착 상태 보고서 XML.|

### <a name="automatic-tuning-dataset"></a>자동 조정 데이터 집합

|자산|설명|
|---|---|
|TenantId|테넌트 ID|
|SourceSystem|항상: Azure|
|TimeGenerated [UTC]|로그가 기록된 때의 타임스탬프|
|type|항상: AzureDiagnostics|
|ResourceProvider|리소스 공급자의 이름. 항상: MICROSOFT.SQL|
|Category|범주 이름. 항상: AutomaticTuning|
|리소스|리소스의 이름입니다.|
|ResourceType|리소스 형식 이름. 항상: SERVERS/DATABASES|
|SubscriptionId|데이터베이스가 속한 구독 GUID|
|ResourceGroup|데이터베이스가 속한 리소스 그룹의 이름|
|LogicalServerName_s|데이터베이스가 속한 서버의 이름|
|LogicalDatabaseName_s|데이터베이스 이름|
|ElasticPoolName_s|데이터베이스가 속한 탄력적 풀의 이름의 이름(있는 경우)|
|DatabaseName_s|데이터베이스 이름|
|ResourceId|리소스 URI |
|RecommendationHash_s|자동 조정 권장 사항의 고유 해시.|
|OptionName_s|자동 조정 작업.|
|Schema_s|데이터베이스 스키마.|
|Table_s|영향을 받는 테이블.|
|IndexName_s|인덱스 이름.|
|IndexColumns_s|열 이름.|
|IncludedColumns_s|포함된 열.|
|EstimatedImpact_s|자동 조정 권장 사항 JSON의 예상되는 영향.|
|Event_s|자동 조정 이벤트의 형식.|
|Timestamp_t|마지막으로 업데이트한 타임스탬프.|

### <a name="intelligent-insights-dataset"></a>Intelligent Insights 데이터 집합
[Intelligent Insights 로그 형식에 대해 자세히 알아봅니다.](sql-database-intelligent-insights-use-diagnostics-log.md)

## <a name="next-steps"></a>다음 단계

로깅을 사용하도록 설정하는 방법을 학습하고 여러 Azure 서비스에서 지원되는 메트릭과 로그 범주를 이해하려면 다음을 확인합니다.

 * [Microsoft Azure의 메트릭 개요](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
 * [Azure 진단 로그 개요](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)

Event Hubs에 대해 알아보려면 다음을 확인합니다.

* [Azure Event Hubs 정의](../event-hubs/event-hubs-what-is-event-hubs.md)
* [Event Hubs 시작](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Storage에 대해 자세히 알아보려면 [Storage에서 메트릭 및 진단 로그 다운로드하는 방법](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application)을 참조하세요.
