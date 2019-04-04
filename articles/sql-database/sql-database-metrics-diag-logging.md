---
title: Azure SQL Database 메트릭 및 진단 로깅 | Microsoft Docs
description: 리소스 사용 및 쿼리 실행 통계를 저장하도록 Azure SQL Database를 구성하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 3004f073100b45de25655fc6dee6a96c90612c46
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905206"
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Azure SQL Database 메트릭 및 진단 로깅

단일 데이터베이스, 탄력적 풀의 풀링된 데이터베이스 및 관리되는 인스턴스의 인스턴스 데이터베이스는 성능을 더 쉽게 모니터링할 수 있도록 메트릭 및 진단 로그를 스트리밍할 수 있습니다. 리소스 사용량, 작업자와 세션 및 연결을 다음 Azure 리소스 중 하나로 전송하도록 데이터베이스를 구성할 수 있습니다.

- **Azure SQL 분석**: 성능 보고서, 경고 및 완화 권장 사항을 포함하는 Azure SQL Database의 인텔리전트 모니터링을 가져옵니다.
- **Azure Event Hubs**: 사용자 지정 모니터링 솔루션 또는 핫 파이프라인과 SQL Database 원격 분석을 통합합니다.
- **Azure Storage**: 적은 비용으로 방대한 양의 원격 분석 데이터를 보관합니다.

    ![아키텍처](./media/sql-database-metrics-diag-logging/architecture.png)

다양한 Azure 서비스에서 지원하는 메트릭 및 로그 범주에 대한 자세한 내용은 다음을 참조하세요.

- [Microsoft Azure의 메트릭 개요](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Azure 진단 로그 개요](../azure-monitor/platform/diagnostic-logs-overview.md)

이 문서에서는 Azure SQL Database, 탄력적 풀 및 관리되는 인스턴스에 대한 진단 원격 분석을 사용하도록 설정하는 데 도움이 되는 지침을 제공합니다. 또한 이를 통해 데이터베이스 진단 원격 분석을 보기 위한 모니터링 도구로 Azure SQL 분석을 구성하는 방법을 이해할 수 있습니다.

## <a name="enable-logging-of-diagnostics-telemetry"></a>진단 원격 분석의 로깅 사용

다음 방법 중 하나를 사용하여 메트릭 및 진단 원격 분석 로깅을 사용하도록 설정하고 관리할 수 있습니다.

- Azure portal
- PowerShell
- Azure CLI
- Azure Monitor REST API
- Azure Resource Manager 템플릿

메트릭 및 진단 로깅을 사용 하도록 설정 하면 진단 원격 분석 수집에 대 한 Azure 리소스 대상을 지정 해야 합니다. 사용 가능한 옵션은 다음과 같습니다.

- Azure SQL Analytics
- Azure Event Hubs
- Azure Storage

새 Azure 리소스를 프로비전하거나 기존 리소스를 선택할 수 있습니다. **진단 설정** 옵션을 사용하여 리소스를 선택한 후 수집할 데이터를 지정합니다.

## <a name="supported-diagnostic-logging-for-azure-sql-databases-and-instance-databases"></a>Azure SQL 데이터베이스 및 인스턴스 데이터베이스에 대 한 진단 로깅 지원

SQL Database에 대한 메트릭과 진단 로깅을 사용하도록 설정합니다. 이러한 항목은 기본적으로 사용되지 않습니다.

다음 진단 원격 분석을 수집 하도록 Azure SQL 데이터베이스 및 인스턴스 데이터베이스를 설정할 수 있습니다.

| 데이터베이스에 대한 원격 분석 모니터링 | 단일 데이터베이스 및 풀링된 데이터베이스 지원 | 인스턴스 데이터베이스 지원 |
| :------------------- | ----- | ----- |
| [모든 메트릭](#all-metrics): DTU/CPU 백분율, DTU/CPU 제한, 물리 데이터 읽기 백분율, 로그 쓰기 백분율, 방화벽에서 성공/실패/차단된 연결, 세션 백분율, 작업자 백분율, 스토리지, 스토리지 백분율, XTP 스토리지 백분율을 포함합니다. | 예 | 아닙니다. |
| [QueryStoreRuntimeStatistics](#query-store-runtime-statistics): CPU 사용량 및 쿼리 기간 통계와 같은 쿼리 런타임 통계에 대한 정보를 포함합니다. | 예 | 예 |
| [QueryStoreWaitStatistics](#query-store-wait-statistics): (새로운 쿼리 대기) 쿼리 대기 통계에 대 한 정보가 등은 CPU, 로그 및 잠금. | 예 | 예 |
| [Errors](#errors-dataset): 데이터베이스에서 SQL 오류에 대 한 정보를 포함합니다. | 예 | 예 |
| [DatabaseWaitStatistics](#database-wait-statistics-dataset): 데이터베이스 대기 형식에 대기 하는 데 소요 된 시간에 대 한 정보를 포함 합니다. | 예 | 아닙니다. |
| [Timeouts](#time-outs-dataset): 데이터베이스에 대 한 정보를 포함합니다. | 예 | 아닙니다. |
| [Blocks](#blockings-dataset): 데이터베이스에서 이벤트를 차단 하는 방법에 대 한 정보를 포함 합니다. | 예 | 아닙니다. |
| [교착 상태](#deadlocks-dataset): 데이터베이스에서 교착 상태 이벤트에 대 한 정보를 포함합니다. | 예 | 아닙니다. |
| [AutomaticTuning](#automatic-tuning-dataset): 데이터베이스에 대 한 자동 조정 권장 사항에 대 한 정보를 포함합니다. | 예 | 아닙니다. |
| [SQLInsights](#intelligent-insights-dataset): Intelligent Insights 데이터베이스 성능에 포함 되어 있습니다. 자세한 내용은 [Intelligent Insights](sql-database-intelligent-insights.md)를 참조하세요. | 예 | 예 |

> [!IMPORTANT]
> 탄력적 풀 및 관리 되는 인스턴스에 포함 된 데이터베이스에서 별도 진단 원격 분석을 사용 하는 자체에 있습니다. 이 아래 설명 된 대로 진단 원격 분석 이러한 리소스를 각각에 대해 별도로 구성 된 것을 명심 해야 합니다.

> [!NOTE]
> 보안 감사 및 SQLSecurityAuditEvents 로그 데이터베이스 진단 설정에서 사용할 수 없습니다. 감사 로그 스트리밍을 사용 하려면 [데이터베이스에 대 한 감사 설정](sql-database-auditing.md#subheading-2), 및 [Azure Monitor 로그 및 Azure Event Hubs에서 로그 감사](https://blogs.msdn.microsoft.com/sqlsecurity/2018/09/13/sql-audit-logs-in-azure-log-analytics-and-azure-event-hubs/)합니다.

## <a name="azure-portal"></a>Azure portal

사용할 수 있습니다 **진단 설정** 메뉴에 대 한 각 단일, 풀링된 또는 진단 원격 분석의 스트리밍을 구성 하려면 Azure portal에서 데이터베이스를 인스턴스. 또한 진단 원격 분석이 구성할 수도 있습니다 별도로 데이터베이스 컨테이너용: 탄력적 풀 및 관리 되는 인스턴스. 진단 원격 분석 데이터를 스트리밍하려면 다음 대상을 설정할 수 있습니다. Azure Storage, Azure Event Hubs 및 Azure Monitor 로그 합니다.

### <a name="configure-streaming-of-diagnostics-telemetry-for-elastic-pools"></a>탄력적 풀에 대한 진단 원격 분석 스트리밍 구성

   ![탄력적 풀 아이콘](./media/sql-database-metrics-diag-logging/icon-elastic-pool-text.png)

다음 진단 원격 분석을 수집하도록 탄력적 풀 리소스를 설정할 수 있습니다.

| 리소스 | 모니터링 원격 분석 |
| :------------------- | ------------------- |
| **탄력적 풀** | [모든 메트릭](sql-database-metrics-diag-logging.md#all-metrics)은 eDTU/CPU 백분율, eDTU/CPU 제한, 물리 데이터 읽기 백분율, 로그 쓰기 백분율, 세션 백분율, 작업자 백분율, 저장소, 저장소 백분율, 저장소 제한 및 XTP 저장소 백분율을 포함합니다. |

탄력적 풀 및 탄력적 풀의 데이터베이스에 대 한 진단 원격 분석의 스트리밍을 구성 하려면 개별적으로 구성 해야 **둘 다** 중:

- 탄력적 풀에 대 한 진단 원격 분석의 스트리밍을 사용 하도록 설정 **및**
- 탄력적 풀의 각 데이터베이스에 대 한 진단 원격 분석의 스트리밍을 사용 하도록 설정

탄력적 풀은 되는 개별 데이터베이스 원격 분석에서 별도 고유한 원격 분석을 사용 하 여 데이터베이스 컨테이너 때문입니다.

탄력적 풀 리소스에 진단 원격 분석 스트리밍을 사용하도록 설정하려면 다음 단계를 따릅니다.

1. 로 이동 합니다 **탄력적 풀** Azure portal에서 리소스입니다.
1. **진단 설정**을 선택합니다.
1. 이전 설정이 없으면 **진단 켜기**를 선택하고, 이전 설정이 있으면 **설정 편집**을 선택하여 이전 설정을 편집합니다.

   ![탄력적 풀에 대해 진단 사용](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-enable.png)

1. 고유한 참조의 설정 이름을 입력합니다.
1. 스트리밍 진단 데이터의 대상 리소스를 선택합니다. **스토리지 계정에 보관**, **이벤트 허브로의 스트림** 또는 **Log Analytics에 보내기**.
1. Log analytics에 대 한 선택 **구성** 선택 하 여 새 작업 영역을 만들어 **+ 새 작업 영역 만들기**, 또는 기존 작업 영역을 선택 합니다.
1. 탄력적 풀 진단 원격 분석에 대해 다음 확인란을 선택합니다. **AllMetrics**.
   ![탄력적 풀에 대해 진단 구성](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-selection.png)
1. **저장**을 선택합니다.
1. 또한 다음 섹션에 설명 된 단계에 따라 여 모니터링 하려는 탄력적 풀 내의 각 데이터베이스에 대 한 진단 원격 분석의 스트리밍을 구성 합니다.

> [!IMPORTANT]
> 탄력적 풀에 대 한 진단 원격 분석을 구성 하는 것 외에도 필요한 구성한 각 데이터베이스에 대 한 진단 원격 분석 탄력적 풀의 아래 설명 된 대로 합니다. 

### <a name="configure-streaming-of-diagnostics-telemetry-for-single-database-or-database-in-elastic-pool"></a>단일 데이터베이스 또는 탄력적 풀의 데이터베이스에 대 한 진단 원격 분석의 스트리밍을 구성합니다

   ![SQL Database 아이콘](./media/sql-database-metrics-diag-logging/icon-sql-database-text.png)

단일 또는 풀링된 데이터베이스에 대 한 진단 원격 분석의 스트리밍을 사용 하려면 다음이 단계를 수행 합니다.

1. Azure로 이동 **SQL database** 리소스입니다.
1. **진단 설정**을 선택합니다.
1. 이전 설정이 없으면 **진단 켜기**를 선택하고, 이전 설정이 있으면 **설정 편집**을 선택하여 이전 설정을 편집합니다.
   - 진단 원격 분석을 스트림하는 병렬 연결을 3개까지 만들 수 있습니다.
   - **+진단 설정 추가**를 선택하여 진단 데이터를 여러 리소스로 병렬 스트림하도록 구성합니다.

   ![단일, 풀링된 또는 인스턴스 데이터베이스에 대한 진단을 사용하도록 설정](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-enable.png)
1. 고유한 참조의 설정 이름을 입력합니다.
1. 스트리밍 진단 데이터의 대상 리소스를 선택합니다. **스토리지 계정에 보관**, **이벤트 허브로의 스트림** 또는 **Log Analytics에 보내기**.
1. 표준 이벤트 기반 모니터링 환경의 경우 데이터베이스 진단 로그 원격 분석에 대해 다음 확인란을 선택합니다. **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**, **Errors**, **DatabaseWaitStatistics**, **Timeouts**, **Blocks** 및 **Deadlocks**.
1. 고급 1분 기간 모니터링 환경의 경우 **AllMetrics** 확인란을 선택합니다.
   ![단일, 풀링된 또는 인스턴스 데이터베이스에 대한 진단 구성](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-selection.png)
1. **저장**을 선택합니다.
1. 모니터링 하려는 각 데이터베이스에 대해 이러한 단계를 반복 합니다.

> [!NOTE]
> 보안 감사 및 SQLSecurityAuditEvents 로그 데이터베이스 진단 설정에서 사용할 수 없습니다. 감사 로그 스트리밍을 사용 하려면 [데이터베이스에 대 한 감사 설정](sql-database-auditing.md#subheading-2), 및 [Azure Monitor 로그 및 Azure Event Hubs에서 로그 감사](https://blogs.msdn.microsoft.com/sqlsecurity/2018/09/13/sql-audit-logs-in-azure-log-analytics-and-azure-event-hubs/)합니다.
> [!TIP]
> 모니터링하려는 각 Azure SQL Database에 대해 이러한 단계를 반복합니다.

### <a name="configure-streaming-of-diagnostics-telemetry-for-managed-instances"></a>관리되는 인스턴스에 대한 진단 원격 분석 스트리밍 구성

   ![관리되는 인스턴스 아이콘](./media/sql-database-metrics-diag-logging/icon-managed-instance-text.png)

다음 진단 원격 분석을 수집하도록 관리되는 인스턴스 리소스를 설정할 수 있습니다.

| 리소스 | 모니터링 원격 분석 |
| :------------------- | ------------------- |
| **Managed Instance** | [ResourceUsageStats](#resource-usage-stats-for-managed-instance)는 vCore 수, 평균 CPU 백분율, IO 요청 수, 읽은/쓴 바이트, 예약된 스토리지 공간 및 사용된 스토리지 공간을 포함합니다. |

관리 되는 인스턴스 및 인스턴스 데이터베이스에 대 한 진단 원격 분석의 스트리밍을 구성 하려면 개별적으로 구성 해야 **둘 다** 중:

- 관리 되는 인스턴스에 대 한 진단 원격 분석의 스트리밍을 사용 하도록 설정 **및**
- 각 인스턴스 데이터베이스에 대 한 진단 원격 분석의 스트리밍을 사용 하도록 설정

관리 되는 인스턴스는 개별 인스턴스 database 원격 분석에서 분리 되는 고유한 원격 분석을 사용 하 여 데이터베이스 컨테이너인 때문입니다.

관리되는 인스턴스 리소스에 대한 진단 원격 분석 스트리밍을 사용하도록 설정하려면 다음 단계를 따릅니다.

1. 로 이동 합니다 **관리 되는 인스턴스** Azure portal에서 리소스입니다.
1. **진단 설정**을 선택합니다.
1. 이전 설정이 없으면 **진단 켜기**를 선택하고, 이전 설정이 있으면 **설정 편집**을 선택하여 이전 설정을 편집합니다.

   ![관리되는 인스턴스에 대해 진단 사용](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-enable.png)

1. 고유한 참조의 설정 이름을 입력합니다.
1. 스트리밍 진단 데이터의 대상 리소스를 선택합니다. **스토리지 계정에 보관**, **이벤트 허브로의 스트림** 또는 **Log Analytics에 보내기**.
1. Log analytics에 대 한 선택 **구성** 선택 하 여 새 작업 영역을 만들고 **+ 새 작업 영역 만들기**, 또는 기존 작업 영역을 사용 합니다.
1. 인스턴스 진단 원격 분석에 대해 다음 확인란을 선택합니다. **ResourceUsageStats**.
   ![관리되는 인스턴스에 대한 진단 구성](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-selection.png)
1. **저장**을 선택합니다.
1. 또한 다음 섹션에 설명 된 단계에 따라 여 모니터링 하려는 관리 되는 인스턴스 내의 각 인스턴스 데이터베이스에 대 한 진단 원격 분석의 스트리밍을 구성 합니다.

> [!IMPORTANT]
> 관리 되는 인스턴스에 대 한 진단 원격 분석을 구성 하는 것 외에도 해야 각 인스턴스 데이터베이스에 대 한 진단 원격 분석을 구성 하려면 아래 설명 된 대로 합니다. 

### <a name="configure-streaming-of-diagnostics-telemetry-for-instance-databases"></a>진단 원격 분석의 예를 들어 데이터베이스 스트리밍을 구성합니다

   ![관리되는 인스턴스의 인스턴스 데이터베이스 아이콘](./media/sql-database-metrics-diag-logging/icon-mi-database-text.png)

진단 원격 분석의 예를 들어 데이터베이스 스트리밍을 사용 하려면 다음이 단계를 수행 합니다.

1. 로 이동 **인스턴스 데이터베이스** 관리 되는 인스턴스 내의 리소스입니다.
1. **진단 설정**을 선택합니다.
1. 이전 설정이 없으면 **진단 켜기**를 선택하고, 이전 설정이 있으면 **설정 편집**을 선택하여 이전 설정을 편집합니다.
   - 진단 원격 분석을 스트림하는 병렬 연결을 3개까지 만들 수 있습니다.
   - **+진단 설정 추가**를 선택하여 진단 데이터를 여러 리소스로 병렬 스트림하도록 구성합니다.

   ![인스턴스 데이터베이스에 대해 진단 사용](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

1. 고유한 참조의 설정 이름을 입력합니다.
1. 스트리밍 진단 데이터의 대상 리소스를 선택합니다. **스토리지 계정에 보관**, **이벤트 허브로의 스트림** 또는 **Log Analytics에 보내기**.
1. 데이터베이스 진단 원격 분석에 대해 다음 확인란을 선택합니다. **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics** 및 **Errors**.
   ![인스턴스 데이터베이스에 대한 진단 구성](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)
1. **저장**을 선택합니다.
1. 모니터링 하려는 각 인스턴스 데이터베이스에 대해 이러한 단계를 반복 합니다.

> [!TIP]
> 모니터링 하려는 각 인스턴스 데이터베이스에 대해 이러한 단계를 반복 합니다.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL Database, Azure Resource Manager PowerShell 모듈은 계속 지원 하지만 Az.Sql 모듈에 대 한 모든 향후 개발 됩니다. 이러한 cmdlet에 대 한 참조 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)합니다. Az 모듈에는 AzureRm 모듈의 명령에 대 한 인수를 실질적으로 동일합니다.

PowerShell을 사용하여 메트릭 및 진단 로깅을 사용하도록 설정할 수 있습니다.

- 저장소 계정에서 진단 로그의 저장소를 사용하도록 설정하려면 다음 명령을 사용합니다.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   스토리지 계정 ID는 대상 스토리지 계정의 리소스 ID입니다.

- 이벤트 허브로의 진단 로그 스트리밍을 활성화하려면 다음 명령을 사용합니다.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Azure Service Bus 규칙 ID는 다음 형식의 문자열입니다.

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ```

- 진단 로그를 Log Analytics 작업 영역으로 보낼 수 있게 하려면 이 명령을 사용합니다.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- 다음 명령을 사용하여 Log Analytics 작업 영역의 리소스 ID를 가져올 수 있습니다.

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

이러한 매개 변수를 결합하여 여러 출력 옵션을 활성화할 수 있습니다.

### <a name="to-configure-multiple-azure-resources"></a>Azure 리소스를 여러 개 구성하려면

여러 구독을 지원하려면 [PowerShell을 사용하여 Azure 리소스 메트릭 로깅 사용](https://blogs.technet.microsoft.com/msoms/20../../enable-azure-resource-metrics-logging-using-powershell/)에서 PowerShell 스크립트를 사용합니다.

여러 리소스의 진단 데이터를 작업 영역으로 보내는 스크립트 `Enable-AzureRMDiagnostics.ps1`을 실행할 때 작업 영역 리소스 ID \<$WSID\>를 매개 변수로 제공합니다.

- 진단 데이터 대상의 작업 영역 ID \<$WSID\>를 가져오려면 다음 스크립트를 사용합니다.

    ```powershell
    PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
    PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```
   \<subID\>를 구독 ID로, \<RG_NAME\>을 리소스 그룹 이름으로, \<WS_NAME\>을 작업 영역 이름으로 바꿉니다.

### <a name="azure-cli"></a>Azure CLI

Azure CLI를 사용하여 메트릭 및 진단 로깅을 사용하도록 설정할 수 있습니다.

> [!NOTE]
> 진단 로깅을 사용 하도록 설정 하는 스크립트는 Azure CLI v1.0에 대 한 지원 됩니다. CLI v2.0 아님을 지원 되는 지금은 note 하십시오.

- 스토리지 계정에서 진단 로그의 스토리지를 사용하도록 설정하려면 다음 명령을 사용합니다.

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   스토리지 계정 ID는 대상 스토리지 계정의 리소스 ID입니다.

- 이벤트 허브로 진단 로그 스트리밍을 사용하도록 설정하려면 다음 명령을 사용합니다.

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   Service Bus 규칙 ID는 이 형식의 문자열입니다.

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- 진단 로그를 Log Analytics 작업 영역으로 보낼 수 있게 하려면 다음 명령을 사용합니다.

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

이러한 매개 변수를 결합하여 여러 출력 옵션을 활성화할 수 있습니다.

### <a name="rest-api"></a>REST API

[Azure Monitor REST API를 사용하여 진단 설정 변경](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)을 참조하세요.

### <a name="resource-manager-template"></a>Resource Manager 템플릿

[Resource Manager 템플릿을 사용하여 리소스 생성 시 진단 설정을 활성화하는 방법](../azure-monitor/platform/diagnostic-logs-stream-template.md)을 참조하세요. 

## <a name="stream-into-azure-sql-analytics"></a>Azure SQL Analytics로 스트리밍

Azure SQL 분석은 여러 구독 간에 대규모로 Azure SQL Database, 탄력적 풀 및 관리되는 인스턴스의 성능을 모니터링하는 클라우드 솔루션입니다. Azure SQL Database 성능 메트릭을 수집 및 시각화하는 데 도움이 될 수 있고 성능 문제 해결을 위한 기본 제공 인텔리전스를 제공합니다.

![Azure SQL 분석 개요](../azure-monitor/insights/media/azure-sql/azure-sql-sol-overview.png)

SQL Database 메트릭 및 진단 로그는 포털의 [진단 설정] 탭에 기본 제공되는 **Log Analytics에 보내기** 옵션을 사용하여 Azure SQL 분석으로 스트림될 수 있습니다. 또한 PowerShell cmdlet, Azure CLI 또는 Azure Monitor REST API를 통해 진단 설정을 사용 하 여 log analytics를 사용할 수 있습니다.

### <a name="installation-overview"></a>설치 개요

Azure SQL 분석을 사용하여 SQL Database 세트를 모니터링할 수 있습니다. 다음 단계를 수행합니다.

1. Azure Marketplace에서 Azure SQL 분석 솔루션을 만듭니다.
2. 솔루션에서 모니터링 작업 영역을 만듭니다.
3. 작업 영역으로 진단 원격 분석을 스트림하도록 데이터베이스를 구성합니다.

또한 탄력적 풀 또는 관리되는 인스턴스를 사용하는 경우 이러한 리소스에서 진단 원격 분석 스트리밍을 구성해야 합니다.

### <a name="create-azure-sql-analytics-resource"></a>Azure SQL 분석 리소스 만들기

1. Azure Marketplace에서 Azure SQL 분석을 검색하고 선택합니다.

   ![포털에서 Azure SQL 분석 검색](./media/sql-database-metrics-diag-logging/sql-analytics-in-marketplace.png)

2. 솔루션의 개요 화면에서 **만들기**를 선택합니다.

3. 필요한 추가 정보(작업 영역 이름, 구독, 리소스 그룹, 위치 및 가격 책정 계층)를 Azure SQL 분석 양식에 입력합니다.

   ![포털에서 Azure SQL 분석 구성](./media/sql-database-metrics-diag-logging/sql-analytics-configuration-blade.png)

4. **확인**을 선택하여 확인한 후 **만들기**를 선택합니다.

### <a name="configure-databases-to-record-metrics-and-diagnostics-logs"></a>메트릭 및 진단 로그를 기록하도록 데이터베이스 구성

데이터베이스 레코드 메트릭의 위치를 구성하는 가장 쉬운 방법은 Azure Portal을 사용하는 것입니다. 앞에서 설명한 대로 Azure Portal의 SQL Database 리소스로 이동하고 **진단 설정**을 선택합니다.

또한 탄력적 풀 또는 관리되는 인스턴스를 사용하는 경우 진단 원격 분석이 작업 영역으로 스트리밍될 수 있도록 이러한 리소스에서 진단 설정을 구성해야 합니다.

### <a name="use-the-sql-analytics-solution"></a>SQL Analytics 솔루션 사용

SQL 분석을 계층 구조 대시보드로 사용하여 SQL Database 리소스를 볼 수 있습니다. SQL Analytics 솔루션 사용 방법은 [SQL Analytics 솔루션을 사용한 SQL Database 모니터링](../log-analytics/log-analytics-azure-sql.md)을 참조하세요.

## <a name="stream-into-event-hubs"></a>Event Hubs로 스트림

Azure Portal에서 기본 제공되는 **이벤트 허브로 스트림** 옵션을 사용하여 SQL Database 메트릭 및 진단 로그를 Event Hubs로 스트림할 수 있습니다. 또한 PowerShell cmdlet, Azure CLI 또는 Azure Monitor REST API를 통해 진단 설정을 사용하여 Service Bus 규칙 ID를 사용하도록 설정할 수도 있습니다.

### <a name="what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs"></a>Event Hubs에서 메트릭 및 진단 로그를 사용하여 수행할 수 있는 작업

선택한 데이터가 Event Hubs로 스트리밍되면 고급 모니터링 시나리오를 사용할 수 있는 단계에 한 단계 더 가까워집니다. Event Hubssms 이벤트 파이프라인의 프런트 도어 역할을 합니다. 데이터는 이벤트 허브로 수집된 후 실시간 분석 공급자 또는 스토리지 어댑터를 사용하여 변환 및 저장될 수 있습니다. Event Hubs는 해당 이벤트 소비에서 이벤트 스트림 프로덕션을 분리합니다. 이러한 방식으로 이벤트 소비자는 자체 일정에 따라 이벤트를 액세스할 수 있습니다. Event Hubs에 대한 자세한 내용은 다음을 참조하세요.

- [Azure Event Hubs 란?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Event Hubs 시작](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Event Hubs의 스트림된 메트릭을 사용하여 다음을 수행할 수 있습니다.

- **Power BI로 핫 패스 데이터를 스트림하여 서비스 상태를 확인합니다**. Event Hubs, Stream Analytics 및 PowerBI를 사용하여 Azure 서비스에서 메트릭 및 진단 데이터를 거의 실시간 정보로 간편하게 변환할 수 있습니다. Event Hubs를 설정하고 Stream Analytics로 데이터를 처리하며 출력으로 PowerBI를 사용하는 방법에 대한 개요는 [Stream Analytics 및 Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)를 참조하세요.

- **제3자 로깅 및 원격 분석에 로그를 스트림합니다**. Event Hubs 스트리밍을 사용하여 메트릭 및 진단 로그를 다양한 타사 모니터링 및 로그 분석 솔루션으로 가져올 수 있습니다.

- **사용자 지정 원격 분석 및 로깅 플랫폼을 빌드합니다**. 사용자 지정 빌드 원격 분석 플랫폼이 이미 있거나 빌드를 고려하고 있나요? Event Hubs의 확장성 높은 게시-구독 특성을 통해 진단 로그를 유연하게 수집할 수 있습니다. [글로벌 확장 원격 분석 플랫폼에 Event Hubs 사용에 대한 Dan Rosanova의 가이드](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)를 참조하세요.

## <a name="stream-into-storage"></a>Storage에 스트림

Azure Portal에서 기본 제공되는 **스토리지 계정에 보관** 옵션을 사용하여 SQL Database 메트릭 및 진단 로그를 Azure Storage에 저장할 수 있습니다. PowerShell cmdlet, Azure CLI 또는 Azure Monitor REST API를 통해 진단 설정을 사용하여 Storage를 사용하도록 설정할 수도 있습니다.

### <a name="schema-of-metrics-and-diagnostics-logs-in-the-storage-account"></a>저장소 계정의 메트릭 및 진단 로그 스키마

메트릭 및 진단 로그 수집을 설정하면 첫 번째 데이터 행을 사용할 수 있을 때 선택한 저장소 계정에 저장소 컨테이너가 만들어집니다. Blob의 구조는 다음과 같습니다.

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

탄력적 풀의 데이터를 저장하기 위한 BLOB 이름은 다음과 같습니다.

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

### <a name="download-metrics-and-logs-from-storage"></a>Storage에서 메트릭 및 로그 다운로드

[Storage에서 메트릭 및 진단 로그 다운로드](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application)를 참조하세요.

## <a name="data-retention-policy-and-pricing"></a>데이터 보존 정책 및 가격 책정

Event Hubs 또는 스토리지 계정을 선택하면 보존 정책을 지정할 수 있습니다. 이 정책은 선택한 기간보다 오래된 데이터를 삭제합니다. Log Analytics를 지정한 경우 선택한 가격 책정 계층에 따라 보존 정책이 달라집니다. 이 경우 제공되는 데이터 수집 무료 단위만큼 매월 여러 데이터베이스를 무료로 모니터링할 수 있습니다. 무료 단위를 초과하는 진단 원격 분석 사용에는 요금이 부과될 수 있습니다. 워크로드가 많은 활성 데이터는 유휴 데이터베이스보다 더 많은 데이터를 수집합니다. 자세한 내용은 [Log analytics 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)합니다.

Azure SQL 분석을 사용하는 경우 Azure SQL 분석의 탐색 메뉴에서 **OMS 작업 영역**을 선택한 다음, **사용량** 및 **예상 비용**을 선택하여 솔루션에서 데이터 수집 사용량을 모니터링할 수 있습니다.

## <a name="metrics-and-logs-available"></a>사용 가능한 메트릭 및 로그

Azure SQL Database에 대 한 사용 가능한 원격 분석을 모니터링, 탄력적 풀 및 관리 되는 인스턴스 아래 설명 되어 있습니다. SQL Analytics 내에서 수집 된 모니터링 원격 분석 사용자 고유의 사용자 지정 분석 및 사용 하 여 응용 프로그램 개발에 사용할 수 있습니다 [Azure Monitor 로그 쿼리](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) 언어입니다.

## <a name="all-metrics"></a>모든 메트릭

리소스별 모든 메트릭에 대한 자세한 내용은 다음 표를 참조하세요.

### <a name="all-metrics-for-elastic-pools"></a>탄력적 풀에 대한 모든 메트릭

|**리소스**|**메트릭**|
|---|---|
|탄력적 풀|eDTU 백분율, 사용된 eDTU, eDTU 제한, CPU 백분율, 실제 데이터 읽기 백분율, 로그 쓰기 백분율, 세션 백분율, 작업자 백분율, 저장소, 저장소 백분율, 저장소 용량 한도, XTP 저장소 백분율을 포함합니다. |

### <a name="all-metrics-for-azure-sql-databases"></a>Azure SQL Database의 모든 메트릭

|**리소스**|**메트릭**|
|---|---|
|Azure SQL 데이터베이스|DTU 백분율, 사용된 DTU, DTU 제한, CPU 백분율, 실제 데이터 읽기 백분율, 로그 쓰기 백분율, 성공/실패/방화벽 연결에 의해 차단됨, 세션 백분율, 작업자 백분율, 저장소, 저장소 백분율, XTP 저장소 백분율, 교착 상태를 포함합니다. |

## <a name="all-logs"></a>모든 로그

모든 로그에 대 한 사용 가능한 원격 분석의 세부 정보는 아래 테이블에서 공개 됩니다. 참조 하세요 [진단 로깅 지원](#supported-diagnostic-logging-for-azure-sql-databases-and-instance-databases) 풀링된으로 로그는 특정 데이터베이스 버전-단일 Azure SQL에 대 한 지 이해 하거나 데이터베이스 인스턴스.

### <a name="resource-usage-stats-for-managed-instance"></a>관리 되는 인스턴스에 대 한 리소스 사용 통계

|자산|설명|
|---|---|
|TenantId|테넌트 ID |
|SourceSystem|항상: Azure|
|TimeGenerated [UTC]|로그가 기록된 때의 타임스탬프 |
|Type|항상: AzureDiagnostics |
|ResourceProvider|리소스 공급자의 이름. 항상: MICROSOFT.SQL |
|Category|범주 이름. 항상: ResourceUsageStats |
|리소스|리소스 이름 |
|ResourceType|리소스 형식 이름. 항상: MANAGEDINSTANCES |
|SubscriptionId|데이터베이스의 구독 GUID |
|ResourceGroup|데이터베이스의 리소스 그룹 이름 |
|LogicalServerName_s|관리되는 인스턴스의 이름 |
|ResourceId|리소스 URI |
|SKU_s|관리되는 인스턴스 제품 SKU |
|virtual_core_count_s|사용 가능한 vCore 수 |
|avg_cpu_percent_s|평균 CPU 비율 |
|reserved_storage_mb_s|관리되는 인스턴스의 예약된 스토리지 용량 |
|storage_space_used_mb_s|관리되는 인스턴스의 사용된 스토리지 |
|io_requests_s|IOPS 수 |
|io_bytes_read_s|읽은 IOPS 바이트 수 |
|io_bytes_written_s|쓴 IOPS 바이트 수 |

### <a name="query-store-runtime-statistics"></a>쿼리 저장소 런타임 통계

|자산|설명|
|---|---|
|TenantId|테넌트 ID |
|SourceSystem|항상: Azure |
|TimeGenerated [UTC]|로그가 기록된 때의 타임스탬프 |
|Type|항상: AzureDiagnostics |
|ResourceProvider|리소스 공급자의 이름. 항상: MICROSOFT.SQL |
|Category|범주 이름. 항상: QueryStoreRuntimeStatistics |
|OperationName|작업의 이름입니다. 항상: QueryStoreRuntimeStatisticsEvent |
|리소스|리소스 이름 |
|ResourceType|리소스 형식 이름. 항상: SERVERS/DATABASES |
|SubscriptionId|데이터베이스의 구독 GUID |
|ResourceGroup|데이터베이스의 리소스 그룹 이름 |
|LogicalServerName_s|데이터베이스의 서버 이름 |
|ElasticPoolName_s|데이터베이스에 대한 탄력적 풀 이름(있는 경우) |
|DatabaseName_s|데이터베이스 이름 |
|ResourceId|리소스 URI |
|query_hash_s|쿼리 해시 |
|query_plan_hash_s|쿼리 계획 해시 |
|statement_sql_handle_s|문 SQL 핸들 |
|interval_start_time_d|1900-1-1부터 틱 수로 간격의 시작 datetimeoffset |
|interval_end_time_d|1900-1-1부터 틱 수로 간격의 종료 datetimeoffset |
|logical_io_writes_d|총 논리적 IO 쓰기 수 |
|max_logical_io_writes_d|실행당 최대 논리적 IO 쓰기 수 |
|physical_io_reads_d|총 물리적 IO 읽기 수 |
|max_physical_io_reads_d|실행당 최대 논리적 IO 읽기 수 |
|logical_io_reads_d|총 논리적 IO 읽기 수 |
|max_logical_io_reads_d|실행당 최대 논리적 IO 읽기 수 |
|execution_type_d|실행 형식 |
|count_executions_d|쿼리의 실행 수 |
|cpu_time_d|쿼리에 사용된 총 CPU 시간(밀리초) |
|max_cpu_time_d|단일 실행에 사용된 최대 CPU 시간 소비자(밀리초) |
|dop_d|병렬 처리 수준 합계 |
|max_dop_d|단일 실행에 사용된 최대 병렬 처리 수준 |
|rowcount_d|반환된 총 행 수 |
|max_rowcount_d|단일 실행으로 반환된 최대 행 수 |
|query_max_used_memory_d|사용된 총 메모리 양(KB) |
|max_query_max_used_memory_d|단일 실행에 사용된 최대 메모리 양(KB) |
|duration_d|총 실행 시간(밀리초) |
|max_duration_d|단일 실행의 최대 실행 시간 |
|num_physical_io_reads_d|총 물리적 읽기 수 |
|max_num_physical_io_reads_d|실행당 최대 물리적 읽기 수 |
|log_bytes_used_d|사용된 총 로그 바이트 양 |
|max_log_bytes_used_d|실행당 사용된 최대 로그 바이트 양 |
|query_id_d|쿼리 저장소의 쿼리 ID |
|plan_id_d|쿼리 저장소의 계획 ID |

[쿼리 저장소 런타임 통계 데이터에 대해 자세히 알아봅니다.](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql)

### <a name="query-store-wait-statistics"></a>쿼리 저장소 대기 통계

|자산|설명|
|---|---|
|TenantId|테넌트 ID |
|SourceSystem|항상: Azure |
|TimeGenerated [UTC]|로그가 기록된 때의 타임스탬프 |
|Type|항상: AzureDiagnostics |
|ResourceProvider|리소스 공급자의 이름. 항상: MICROSOFT.SQL |
|Category|범주 이름. 항상: QueryStoreWaitStatistics |
|OperationName|작업의 이름입니다. 항상: QueryStoreWaitStatisticsEvent |
|리소스|리소스 이름 |
|ResourceType|리소스 형식 이름. 항상: SERVERS/DATABASES |
|SubscriptionId|데이터베이스의 구독 GUID |
|ResourceGroup|데이터베이스의 리소스 그룹 이름 |
|LogicalServerName_s|데이터베이스의 서버 이름 |
|ElasticPoolName_s|데이터베이스에 대한 탄력적 풀 이름(있는 경우) |
|DatabaseName_s|데이터베이스 이름 |
|ResourceId|리소스 URI |
|wait_category_s|대기 범주 |
|is_parameterizable_s|매개 변수화할 수 있는 쿼리 |
|statement_type_s|문 형식 |
|statement_key_hash_s|문 키 해시 |
|exec_type_d|실행 형식 |
|total_query_wait_time_ms_d|특정 대기 범주의 총 쿼리 대기 시간 |
|max_query_wait_time_ms_d|특정 대기 범주 개별 실행의 최대 쿼리 대기 시간. |
|query_param_type_d|0 |
|query_hash_s|쿼리 저장소의 쿼리 해시 |
|query_plan_hash_s|쿼리 저장소의 쿼리 계획 해시 |
|statement_sql_handle_s|쿼리 저장소의 문 핸들 |
|interval_start_time_d|1900-1-1부터 틱 수로 간격의 시작 datetimeoffset |
|interval_end_time_d|1900-1-1부터 틱 수로 간격의 종료 datetimeoffset |
|count_executions_d|쿼리 실행 수 |
|query_id_d|쿼리 저장소의 쿼리 ID |
|plan_id_d|쿼리 저장소의 계획 ID |

[쿼리 저장소 대기 통계 데이터를 자세히 알아봅니다.](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql)

### <a name="errors-dataset"></a>오류 데이터 세트

|자산|설명|
|---|---|
|TenantId|테넌트 ID |
|SourceSystem|항상: Azure |
|TimeGenerated [UTC]|로그가 기록된 때의 타임스탬프 |
|Type|항상: AzureDiagnostics |
|ResourceProvider|리소스 공급자의 이름. 항상: MICROSOFT.SQ |
|Category|범주 이름. 항상: 오류 |
|OperationName|작업의 이름입니다. 항상: ErrorEvent |
|리소스|리소스 이름 |
|ResourceType|리소스 형식 이름. 항상: SERVERS/DATABASES |
|SubscriptionId|데이터베이스의 구독 GUID |
|ResourceGroup|데이터베이스의 리소스 그룹 이름 |
|LogicalServerName_s|데이터베이스의 서버 이름 |
|ElasticPoolName_s|데이터베이스에 대한 탄력적 풀 이름(있는 경우) |
|DatabaseName_s|데이터베이스 이름 |
|ResourceId|리소스 URI |
|Message|일반 텍스트의 오류 메시지 |
|user_defined_b|오류 사용자 정의 비트 |
|error_number_d|오류 코드 |
|심각도|오류의 심각도 |
|state_d|오류 상태 |
|query_hash_s|실패한 쿼리의 쿼리 해시(있는 경우) |
|query_plan_hash_s|실패한 쿼리의 쿼리 계획 해시(있는 경우) |

[SQL Server 오류 메시지에 대해 자세히 알아봅니다](https://msdn.microsoft.com/library/cc645603.aspx).

### <a name="database-wait-statistics-dataset"></a>데이터베이스 대기 통계 데이터 세트

|자산|설명|
|---|---|
|TenantId|테넌트 ID |
|SourceSystem|항상: Azure |
|TimeGenerated [UTC]|로그가 기록된 때의 타임스탬프 |
|Type|항상: AzureDiagnostics |
|ResourceProvider|리소스 공급자의 이름. 항상: MICROSOFT.SQL |
|Category|범주 이름. 항상: DatabaseWaitStatistics |
|OperationName|작업의 이름입니다. 항상: DatabaseWaitStatisticsEvent |
|리소스|리소스 이름 |
|ResourceType|리소스 형식 이름. 항상: SERVERS/DATABASES |
|SubscriptionId|데이터베이스의 구독 GUID |
|ResourceGroup|데이터베이스의 리소스 그룹 이름 |
|LogicalServerName_s|데이터베이스의 서버 이름 |
|ElasticPoolName_s|데이터베이스에 대한 탄력적 풀 이름(있는 경우) |
|DatabaseName_s|데이터베이스 이름 |
|ResourceId|리소스 URI |
|wait_type_s|대기 형식 이름 |
|start_utc_date_t [UTC]|측정 기간 시작 시간 |
|end_utc_date_t [UTC]|측정 기간 종료 시간 |
|delta_max_wait_time_ms_d|실행당 최대 대기 시간 |
|delta_signal_wait_time_ms_d|총 신호 대기 시간 |
|delta_wait_time_ms_d|기간의 총 대기 시간 |
|delta_waiting_tasks_count_d|대기 작업 수 |

[데이터베이스 대기 통계에 대해 자세히 알아봅니다.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)

### <a name="time-outs-dataset"></a>제한 시간 데이터 세트

|자산|설명|
|---|---|
|TenantId|테넌트 ID |
|SourceSystem|항상: Azure |
|TimeGenerated [UTC]|로그가 기록된 때의 타임스탬프 |
|Type|항상: AzureDiagnostics |
|ResourceProvider|리소스 공급자의 이름. 항상: MICROSOFT.SQL |
|Category|범주 이름. 항상: 시간 제한 |
|OperationName|작업의 이름입니다. 항상: TimeoutEvent |
|리소스|리소스 이름 |
|ResourceType|리소스 형식 이름. 항상: SERVERS/DATABASES |
|SubscriptionId|데이터베이스의 구독 GUID |
|ResourceGroup|데이터베이스의 리소스 그룹 이름 |
|LogicalServerName_s|데이터베이스의 서버 이름 |
|ElasticPoolName_s|데이터베이스에 대한 탄력적 풀 이름(있는 경우) |
|DatabaseName_s|데이터베이스 이름 |
|ResourceId|리소스 URI |
|error_state_d|오류 상태 코드 |
|query_hash_s|쿼리 해시(있는 경우) |
|query_plan_hash_s|쿼리 계획 해시(있는 경우) |

### <a name="blockings-dataset"></a>차단 데이터 세트

|자산|설명|
|---|---|
|TenantId|테넌트 ID |
|SourceSystem|항상: Azure |
|TimeGenerated [UTC]|로그가 기록된 때의 타임스탬프 |
|Type|항상: AzureDiagnostics |
|ResourceProvider|리소스 공급자의 이름. 항상: MICROSOFT.SQL |
|Category|범주 이름. 항상: 블록 |
|OperationName|작업의 이름입니다. 항상: BlockEvent |
|리소스|리소스 이름 |
|ResourceType|리소스 형식 이름. 항상: SERVERS/DATABASES |
|SubscriptionId|데이터베이스의 구독 GUID |
|ResourceGroup|데이터베이스의 리소스 그룹 이름 |
|LogicalServerName_s|데이터베이스의 서버 이름 |
|ElasticPoolName_s|데이터베이스에 대한 탄력적 풀 이름(있는 경우) |
|DatabaseName_s|데이터베이스 이름 |
|ResourceId|리소스 URI |
|lock_mode_s|쿼리에 사용된 잠금 모드 |
|resource_owner_type_s|잠금 소유자 |
|blocked_process_filtered_s|차단된 프로세스 보고서 XML |
|duration_d|잠금 기간(밀리초) |

### <a name="deadlocks-dataset"></a>교착 상태 데이터 세트

|자산|설명|
|---|---|
|TenantId|테넌트 ID |
|SourceSystem|항상: Azure |
|TimeGenerated [UTC] |로그가 기록된 때의 타임스탬프 |
|Type|항상: AzureDiagnostics |
|ResourceProvider|리소스 공급자의 이름. 항상: MICROSOFT.SQL |
|Category|범주 이름. 항상: 교착 상태 |
|OperationName|작업의 이름입니다. 항상: DeadlockEvent |
|리소스|리소스 이름 |
|ResourceType|리소스 형식 이름. 항상: SERVERS/DATABASES |
|SubscriptionId|데이터베이스의 구독 GUID |
|ResourceGroup|데이터베이스의 리소스 그룹 이름 |
|LogicalServerName_s|데이터베이스의 서버 이름 |
|ElasticPoolName_s|데이터베이스에 대한 탄력적 풀 이름(있는 경우) |
|DatabaseName_s|데이터베이스 이름 |
|ResourceId|리소스 URI |
|deadlock_xml_s|교착 상태 보고서 XML |

### <a name="automatic-tuning-dataset"></a>자동 조정 데이터 세트

|자산|설명|
|---|---|
|TenantId|테넌트 ID |
|SourceSystem|항상: Azure |
|TimeGenerated [UTC]|로그가 기록된 때의 타임스탬프 |
|Type|항상: AzureDiagnostics |
|ResourceProvider|리소스 공급자의 이름. 항상: MICROSOFT.SQL |
|Category|범주 이름. 항상: AutomaticTuning |
|리소스|리소스 이름 |
|ResourceType|리소스 형식 이름. 항상: SERVERS/DATABASES |
|SubscriptionId|데이터베이스의 구독 GUID |
|ResourceGroup|데이터베이스의 리소스 그룹 이름 |
|LogicalServerName_s|데이터베이스의 서버 이름 |
|LogicalDatabaseName_s|데이터베이스 이름 |
|ElasticPoolName_s|데이터베이스에 대한 탄력적 풀 이름(있는 경우) |
|DatabaseName_s|데이터베이스 이름 |
|ResourceId|리소스 URI |
|RecommendationHash_s|자동 조정 권장 사항의 고유 해시 |
|OptionName_s|자동 조정 작업 |
|Schema_s|데이터베이스 스키마 |
|Table_s|영향을 받는 테이블 |
|IndexName_s|인덱스 이름 |
|IndexColumns_s|열 이름 |
|IncludedColumns_s|포함된 열 |
|EstimatedImpact_s|자동 조정 권장 사항 JSON의 예상되는 영향 |
|Event_s|자동 조정 이벤트의 형식 |
|Timestamp_t|마지막으로 업데이트한 타임스탬프 |

### <a name="intelligent-insights-dataset"></a>Intelligent Insights 데이터 세트

[Intelligent Insights 로그 형식에 대해 자세히 알아봅니다.](sql-database-intelligent-insights-use-diagnostics-log.md)

## <a name="next-steps"></a>다음 단계

로깅을 사용하도록 설정하는 방법을 알아보고 여러 Azure 서비스에서 지원되는 메트릭과 로그 범주를 이해하려면 다음을 참조하세요.

- [Microsoft Azure의 메트릭 개요](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Azure 진단 로그 개요](../azure-monitor/platform/diagnostic-logs-overview.md)

Event Hubs에 대해 알아보려면 다음을 확인합니다.

- [Azure Event Hubs 정의](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Event Hubs 시작](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Azure Storage에 대해 자세히 알아보려면 [Storage에서 메트릭 및 진단 로그 다운로드하는 방법](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application)을 참조하세요.
