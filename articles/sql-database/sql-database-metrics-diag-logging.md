---
title: "Azure SQL Database 메트릭 및 진단 로깅 | Microsoft Docs"
description: "리소스 사용, 연결 및 쿼리 실행 통계를 저장하도록 Azure SQL Database를 구성하는 방법을 알아봅니다."
services: sql-database
documentationcenter: 
author: vvasic
manager: jhubbard
editor: 
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2017
ms.author: vvasic
ms.translationtype: Human Translation
ms.sourcegitcommit: 3bbc9e9a22d962a6ee20ead05f728a2b706aee19
ms.openlocfilehash: 71c286061e214898a7f10fa8dfd6c6d86af8db39
ms.contentlocale: ko-kr
ms.lasthandoff: 06/10/2017


---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Azure SQL Database 메트릭 및 진단 로깅 
Azure SQL Database는 쉬운 모니터링을 위해 메트릭 및 진단 로그를 내보낼 수 있습니다. 리소스 사용량, 작업자와 세션 및 연결을 이러한 Azure 리소스 중 하나에 저장하도록 Azure SQL Database를 구성할 수 있습니다.
- **Azure Storage**: 작은 가격으로 방대한 양의 원격 분석을 보관하는 경우
- **Azure Event Hub**: 사용자 지정 모니터링 솔루션 또는 핫 파이프라인과 Azure SQL Database 원격 분석을 통합하는 경우
- **Azure Log Analytics**: 보고, 경고 및 완화 기능을 사용하는 기본 제공 모니터링 솔루션의 경우 

    ![아키텍처](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="enable-logging"></a>로깅 사용

메트릭 및 진단 로깅은 기본적으로 사용되지 않습니다. 다음 방법 중 하나를 사용하여 메트릭 및 진단 로깅을 사용하도록 설정하고 관리할 수 있습니다.
- Azure 포털
- PowerShell
- Azure CLI
- REST API 
- Resource Manager 템플릿

메트릭 및 진단 로깅을 사용하려면 선택한 데이터가 수집되는 Azure 리소스를 지정해야 합니다. 사용 가능한 옵션은 다음과 같습니다.
- Log Analytics
- 이벤트 허브
- Azure 저장소 

새 Azure 리소스를 프로비전하거나 기존 리소스를 선택할 수 있습니다. 저장소 리소스를 선택한 후에는 수집할 데이터를 지정해야 합니다. 사용 가능한 옵션은 다음과 같습니다.

- **[1분 메트릭](sql-database-metrics-diag-logging.md#1-minute-metrics)** - DTU 백분율, DTU 제한, CPU 백분율, 실제 데이터 읽기 백분율, 로그 쓰기 백분율, 성공/실패/방화벽 연결에 의해 차단됨, 세션 백분율, 작업자 백분율, 저장소, 저장소 백분율, XTP 저장소 백분율을 포함합니다.

이벤트 허브 또는 AzureStorage 계정을 지정하면 보존 정책을 지정하여 선택한 기간보다 오래된 데이터를 삭제할 수 있습니다. Log Analytics를 지정한 경우 선택한 가격 책정 계층에 따라 보존 정책이 달라집니다. 자세한 내용은 [Log Analytics 가격 책정](https://azure.microsoft.com/pricing/details/log-analytics/)을 참조하세요. 

[Microsoft Azure의 메트릭 개요](../monitoring-and-diagnostics/monitoring-overview-metrics.md) 및 [Azure 진단 로그 개요](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) 문서를 읽고 로깅 사용뿐 아니라 여러 Azure 서비스에서 지원하는 메트릭 및 로그 카테고리에 대해서도 파악하는 것이 좋습니다.

### <a name="azure-portal"></a>Azure 포털

Azure Portal에서 메트릭 및 진단 로그 수집을 사용하려면 Azure SQL Database 또는 탄력적 풀 페이지로 이동하여 **진단 설정**을 클릭합니다.

   ![Azure Portal에서 사용](./media/sql-database-metrics-diag-logging/enable-portal.png)

### <a name="powershell"></a>PowerShell

PowerShell을 사용하여 메트릭 및 진단 로깅을 사용하도록 설정하려면 다음 명령을 사용합니다.

- 저장소 계정에서 진단 로그의 저장소를 사용하도록 설정하려면 다음 명령을 사용합니다.

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   저장소 계정 ID는 로그를 보낼 저장소 계정에 대한 리소스 ID입니다.

- 이벤트 허브로 진단 로그의 스트리밍을 사용하도록 설정하려면 다음 명령을 사용합니다.

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   서비스 버스 규칙 ID는 다음 형식의 문자열입니다.

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- 진단 로그를 Log Analytics 작업 영역으로 보낼 수 있게 하려면 다음 명령을 사용합니다.

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- 다음 명령을 사용하여 Log Analytics 작업 공간의 리소스 ID를 가져올 수 있습니다.

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

이러한 매개 변수를 결합하여 여러 출력 옵션을 활성화할 수 있습니다.

### <a name="cli"></a>CLI

Azure CLI를 사용하여 메트릭 및 진단 로깅을 사용하도록 설정하려면 다음 명령을 사용합니다.

- 저장소 계정에서 진단 로그의 저장소를 사용하도록 설정하려면 다음 명령을 사용합니다.

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   저장소 계정 ID는 로그를 보낼 저장소 계정에 대한 리소스 ID입니다.

- 이벤트 허브로 진단 로그의 스트리밍을 사용하도록 설정하려면 다음 명령을 사용합니다.

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   서비스 버스 규칙 ID는 다음 형식의 문자열입니다.

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- 진단 로그를 Log Analytics 작업 영역으로 보낼 수 있게 하려면 다음 명령을 사용합니다.

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

이러한 매개 변수를 결합하여 여러 출력 옵션을 활성화할 수 있습니다.

### <a name="rest-api"></a>REST API

자세한 내용은 [Azure Monitor REST API를 사용하여 진단 설정 변경](https://msdn.microsoft.com/library/azure/dn931931.aspx)을 참조하세요. 

### <a name="resource-manager-template"></a>Resource Manager 템플릿

자세한 내용은 [Resource Manager 템플릿을 사용하여 리소스 생성 시 진단 설정 자동 활성화](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md)를 참조하세요. 

## <a name="stream-into-log-analytics"></a>Log Analytics에 스트림 
포털에서 기본 제공되는 "Log Analytics에 보내기" 옵션을 사용하거나 Azure PowerShell cmdlet, Azure CLI 또는 Azure Monitor REST API를 통해 진단 설정에서 Log Analytics를 사용하도록 설정하여 Azure SQL Database 메트릭 및 진단 로그를 Log Analytics로 스트리밍할 수 있습니다.

### <a name="installation-overview"></a>설치 개요

Log Analytics를 사용하여 Azure SQL Database를 간편하게 모니터링할 수 있습니다. 세 단계를 수행해야 합니다.

1.  Log Analytics 리소스 만들기
2.  생성된 Log Analytics에 메트릭 및 진단 로그를 기록하도록 데이터베이스 구성
3.  Log Analytics의 갤러리에서 **Azure SQL 분석** 솔루션 설치

### <a name="create-log-analytics-resource"></a>Log Analytics 리소스 만들기

1. 왼쪽 메뉴에서 **새로 만들기**를 클릭합니다.
2. **모니터링 + 관리**를 클릭합니다.
3. **Log Analytics**를 클릭합니다.
4. 필요한 추가 정보(작업 영역 이름, 구독, 리소스 그룹, 위치 및 가격 책정 계층)를 Log Analytics 양식에 입력합니다.

   ![Log Analytics](./media/sql-database-metrics-diag-logging/log-analytics.png)

### <a name="configure-databases-to-record-metrics-and-diagnostic-logs"></a>메트릭 및 진단 로그를 기록하도록 데이터베이스 구성

데이터베이스에서 메트릭을 기록하는 위치를 구성하는 가장 쉬운 방법은 Azure Portal을 사용하는 것입니다. Azure Portal에서 Azure SQL Database 리소스로 이동하여 **진단 설정**을 클릭합니다. 

### <a name="install-the-azure-sql-analytics-solution-from-gallery"></a>갤러리에서 Azure SQL 분석 솔루션 설치  

1. Log Analytics 리소스를 만들고 데이터 흐름이 시작되면 Azure SQL 분석 솔루션을 설치합니다. OMS 홈 페이지 및 측면 메뉴에서 찾을 수 있는 **솔루션 갤러리**를 통해 이 작업을 수행할 수 있습니다. 갤러리에서 **Azure SQL 분석** 솔루션을 찾아서 클릭한 다음 **추가**를 클릭합니다.

   ![모니터링 솔루션](./media/sql-database-metrics-diag-logging/monitoring-solution.png)

2. OMS 홈 페이지에 **Azure SQL 분석**이라는 새 타일이 표시됩니다. 이 타일을 선택하면 Azure SQL 분석 대시보드가 열립니다.

### <a name="using-azure-sql-analytics-solution"></a>Azure SQL Analytics 솔루션 사용

Azure SQL 분석은 Azure SQL Database 리소스의 계층을 탐색할 수 있는 계층적 대시보드입니다. 이 기능을 사용하면 상위 수준 모니터링을 수행할 뿐 아니라 올바른 리소스 집합으로 모니터링 범위를 설정할 수 있습니다.
대시보드에는 선택한 리소스 아래의 여러 리소스 목록이 포함됩니다. 예를 들어 선택한 구독에 대해 해당 구독에 속한 모든 서버, 탄력적 풀 및 데이터베이스를 볼 수 있습니다. 또한 탄력적 풀 및 데이터베이스에 대해 해당 리소스의 리소스 사용 메트릭을 볼 수 있습니다. 여기에는 DTU, CPU, IO, 로그, 세션, 작업자, 연결 및 저장소(GB)에 대한 차트가 포함됩니다.

## <a name="stream-into-azure-event-hub"></a>Azure 이벤트 허브에 스트림

포털에서 기본 제공되는 "이벤트 허브로의 보내기" 옵션을 사용하거나 Azure PowerShell cmdlet, Azure CLI 또는 Azure Monitor REST API를 통해 진단 설정에서 Service Bus 규칙 ID를 사용하도록 설정하여 Azure SQL Database 메트릭 및 진단 로그를 이벤트 허브로 스트리밍할 수 있습니다. 

### <a name="what-to-do-with-metrics-and-diagnostic-logs-in-event-hub"></a>이벤트 허브에서 메트릭 및 진단 로그를 사용하여 수행할 수 있는 작업
선택한 데이터가 이벤트 허브로 스트리밍되면 고급 모니터링 시나리오를 사용할 수 있는 단계에 한 단계 더 가까워집니다. 이벤트 허브는 이벤트 파이프라인에 대한 "현관"의 역할을 하고 데이터가 이벤트 허브에 수집되면 실시간 분석 공급자 또는 일괄 처리/저장소 어댑터를 사용하여 변환 및 저장될 수 있습니다. 이벤트 허브는 이러한 이벤트를 소비하는 데에서 이벤트 스트림의 프로덕션을 분리하므로 이벤트 소비자가 자신의 개인 일정에 이벤트를 액세스할 수 있습니다. 이벤트 허브에 대한 자세한 내용은 다음을 참조하세요.

- [Azure Event Hubs란](../event-hubs/event-hubs-what-is-event-hubs.md)?
- [이벤트 허브 시작](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)


스트리밍 기능을 사용할 수 있는 몇 가지 방법은 다음과 같습니다.

-   "실행 부하 과다 경로" 데이터를 PowerBI로 스트리밍하여 서비스 상태 보기 – 이벤트 허브, Stream Analytics 및 PowerBI를 사용하여 Azure 서비스에서 메트릭 및 진단 데이터를 거의 실시간 정보로 간편하게 변환할 수 있습니다. 이벤트 허브를 설정하고 Stream Analytics로 데이터를 처리하며 출력으로 PowerBI를 사용하는 방법에 대한 개요는 [Stream Analytics 및 Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)를 참조하세요.
-   타사 로깅 및 원격 분석 스트림으로 로그 스트림 – Event Hubs 스트리밍을 사용하여 메트릭 및 진단 로그를 다른 타사 모니터링 및 로그 분석 솔루션으로 가져올 수 있습니다. 
-   사용자 지정 원격 분석 및 로깅 플랫폼 빌드 – 사용자 지정 빌드 원격 분석 플랫폼이 이미 있거나 플랫폼 빌드에 대해 생각하고 있는 경우 이벤트 허브의 확장성 높은 게시-구독 특성을 통해 진단 로그를 유연하게 수집할 수 있습니다. [글로벌 확장 원격 분석 플랫폼에 이벤트 허브 사용에 대해서는 Dan Rosanova의 가이드](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)를 참조하세요.

## <a name="stream-into-azure-storage"></a>Azure Storage에 스트림

Azure Portal에서 기본 제공되는 "저장소 계정에 보관" 옵션을 사용하거나 Azure PowerShell cmdlet, Azure CLI 또는 Azure Monitor REST API를 통해 진단 설정에서 Azure Storage를 사용하도록 설정하여 Azure SQL Database 메트릭 및 진단 로그를 Azure Storage에 저장할 수 있습니다.

### <a name="schema-of-metrics-and-diagnostic-logs-in-the-storage-account"></a>저장소 계정의 메트릭 및 진단 로그 스키마

메트릭 및 진단 로그 수집을 설정하면 첫 번째 데이터 행을 사용할 수 있을 때 선택한 저장소 계정에 저장소 컨테이너가 만들어집니다. 해당 Blob의 구조는 다음과 같습니다.

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
    
또는 더 간단하게 다음과 같습니다.

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

예를 들어 1분 메트릭에 대한 Blob 이름은 다음과 같을 수 있습니다.

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

탄력적 풀에서 데이터를 기록하려는 경우에는 Blob 이름이 약간 다릅니다.

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

### <a name="download-metrics-and-logs-from-azure-storage"></a>Azure Storage에서 메트릭 및 로그 다운로드

[Azure Storage에서 메트릭 및 진단 로그 다운로드](../storage/storage-dotnet-how-to-use-blobs.md#download-blobs)를 참조하세요.

## <a name="1-minute-metrics"></a>1분 메트릭

| |  |
|---|---|
|**리소스**|**metrics**|
|데이터베이스|DTU 백분율, 사용된 DTU, DTU 제한, CPU 백분율, 실제 데이터 읽기 백분율, 로그 쓰기 백분율, 성공/실패/방화벽 연결에 의해 차단됨, 세션 백분율, 작업자 백분율, 저장소, 저장소 백분율, XTP 저장소 백분율, 교착 상태를 포함합니다. |
|탄력적 풀|eDTU 백분율, 사용된 eDTU, eDTU 제한, CPU 백분율, 실제 데이터 읽기 백분율, 로그 쓰기 백분율, 세션 백분율, 작업자 백분율, 저장소, 저장소 백분율, 저장소 용량 한도, XTP 저장소 백분율을 포함합니다. |
|||

## <a name="next-steps"></a>다음 단계

- [Microsoft Azure의 메트릭 개요](../monitoring-and-diagnostics/monitoring-overview-metrics.md) 및 [Azure 진단 로그 개요](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) 문서를 읽고 로깅 사용뿐 아니라 여러 Azure 서비스에서 지원하는 메트릭 및 로그 카테고리에 대해서도 파악합니다.
- Event Hubs에 대한 자세한 내용은 다음 문서를 참조하세요.
   - [Azure Event Hubs란](../event-hubs/event-hubs-what-is-event-hubs.md)?
   - [이벤트 허브 시작](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- [Azure Storage에서 메트릭 및 진단 로그 다운로드](../storage/storage-dotnet-how-to-use-blobs.md#download-blobs)를 참조하세요.

