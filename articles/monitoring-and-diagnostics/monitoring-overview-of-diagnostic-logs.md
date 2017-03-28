---
title: "Azure 진단 로그 | Microsoft Docs"
description: "Azure 진단 로그란 무엇이고 Azure 리소스 내에서 발생하는 이벤트를 파악하는 데 어떻게 사용할 수 있는지 알아봅니다."
author: johnkemnetz
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: fe8887df-b0e6-46f8-b2c0-11994d28e44f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: johnkem; magoedte
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: be27a3541caa1620af432dcff438f70cb9b1074b
ms.lasthandoff: 03/18/2017


---
# <a name="collect-and-consume-diagnostic-data-from-your-azure-resources"></a>Azure 리소스에서 진단 데이터 수집 및 소비

## <a name="what-are-azure-diagnostic-logs"></a>Azure 진단 로그란?
**Azure 진단 로그** 는 해당 리소스의 작업에 대한 풍부하고 빈번한 데이터를 제공하는 리소스에서 내보낸 로그입니다. 이러한 로그의 내용은 리소스 유형에 따라 달라집니다. 예를 들어, Windows 이벤트 시스템 로그는 VM 및 Blob에 대한 진단 로그의 범주이고 테이블 및 큐 로그는 저장소 계정에 대한 진단 로그의 범주입니다.

진단 로그는 [활동 로그(이전의 감사 로그 또는 작업 로그)](monitoring-overview-activity-logs.md)와 다릅니다. 활동 로그는 구독에 있는 리소스에서 수행된 작업에 대한 자세한 정보를 제공합니다. 진단 로그는 리소스 자체에서 수행하는 작업에 대한 정보를 제공합니다.

일부 리소스만 여기서 설명하는 새로운 형식의 진단 로그를 지원합니다. 이 문서는 리소스 유형에서 새로운 진단 로그에 지원하는 나열된 섹션을 포함합니다.

![진단 로그 및 다른 종류의 로그 ](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_vs_other_logs_v5.png)

그림 1: 진단 로그 및 다른 종류의 로그

## <a name="what-you-can-do-with-diagnostic-logs"></a>진단 로그로 수행할 수 있는 작업
진단 로그를 통해 수행할 수 있는 몇 가지 작업은 다음과 같습니다.

![진단 로그의 논리적 배치](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_Actions.png)


* 감사 또는 수동 검사를 위해 [**저장소 계정**](monitoring-archive-diagnostic-logs.md)에 저장합니다. **진단 설정**을 사용하여 보존 기간(일)을 지정할 수 있습니다.
* [타사 서비스 또는 사용자 지정 분석 솔루션(예: PowerBI)으로 수집을 위해 **Event Hubs**로 스트림](monitoring-stream-diagnostic-logs-to-event-hubs.md)합니다.
* [OMS Log Analytics](../log-analytics/log-analytics-azure-storage.md)

로그를 내보내는 것과 동일한 구독에 위치하지 않는 저장소 계정 또는 Event Hub 네임스페이스를 사용할 수 있습니다. 설정을 구성하는 사용자에게는 두 구독에 대한 적절한 RBAC 액세스가 있어야 합니다.

## <a name="diagnostic-settings"></a>진단 설정
진단 설정을 사용하여 비-계산 리소스에 대한 진단 로그를 구성합니다. **진단 설정** :

* 진단 로그를 보낼 위치(저장소 계정, 이벤트 허브 및/또는 OMS Log Analytics).
* 보낼 로그 범주.
* 각 로그 항목을 저장소 계정에 유지해야 하는 기간.
    - 보존이 0일이라는 것은 로그가 영원히 보관된다는 의미입니다. 그렇지 않은 경우 값은 1에서 2147483647 사이의 숫자일 수 있습니다.
    - 보존 정책이 설정되었지만 저장소 계정에 로그를 저장할 수 없는 경우(예를 들어 Event Hubs 또는 OMS 옵션만 선택한 경우) 보존 정책은 적용되지 않습니다.
    - 보존 정책은 매일 적용되므로 하루의 마지막에(UTC) 보존 정책이 지난 날의 로그가 삭제됩니다. 예를 들어, 하루의 보존 정책이 있는 경우 오늘 날짜가 시작될 때 하루 전의 로그가 삭제됩니다.

이러한 설정은 Azure 포털에서 리소스에 대한 진단 블레이드를 통해, Azure PowerShell 및 CLI 명령을 통하거나 [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931943.aspx)를 통해 쉽게 구성합니다.

> [!WARNING]
> 계산 리소스(예: VM 또는 서비스 패브릭)에 대한 진단 로그 및 메트릭에서는 [출력의 구성 및 선택을 위한 별도의 메커니즘](../azure-diagnostics.md)을 사용합니다.
>
>

## <a name="how-to-enable-collection-of-diagnostic-logs"></a>진단 로그의 컬렉션을 사용하도록 설정하는 방법
리소스 만들기의 일부로 또는 포털에서 리소스 블레이드를 통해 리소스를 만든 후 진단 로그의 컬렉션을 사용하도록 설정할 수 있습니다. 또한 Azure PowerShell 또는 CLI 명령을 사용하거나 Azure Monitor REST API를 사용하여 언제든지 진단 로그를 사용하도록 설정할 수도 있습니다.

> [!TIP]
> 이러한 지침은 모든 리소스에 직접 적용되지 않을 수 있습니다. 특정 리소스 형식에 적용할 수 있는 특수한 단계를 알아보려면 이 페이지 맨 아래에 있는 스키마 링크를 참조하세요.
>
>

[이 문서에서는 리소스를 만들 때 리소스 템플릿을 사용하여 진단 설정을 사용하도록 설정하는 방법을 보여 줍니다.](monitoring-enable-diagnostic-logs-using-template.md)

### <a name="enable-diagnostic-logs-in-the-portal"></a>포털에서 진단 사용
Windows 또는 Linux Azure 진단 확장을 사용하여 계산 리소스 유형을 만들 때 Azure 포털에서 진단 로그를 사용하도록 설정할 수 있습니다.

1. **새로 만들기** 로 이동한 다음 관심 있는 리소스를 선택합니다.
2. 기본 설정을 구성하고 크기를 선택한 후 **모니터링** 아래 **설정** 블레이드에서 **사용**을 선택하고 진단 로그를 저장할 저장소 계정을 선택합니다. 저장소 계정에 진단을 보내는 경우 저장소 및 트랜잭션에 대해 표준 데이터 요금이 부과됩니다.

   ![리소스 생성 중에 진단 로그 사용](./media/monitoring-overview-of-diagnostic-logs/enable-portal-new.png)
3. **확인** 을 클릭하여 리소스를 만듭니다.

비-계산 리소스에 대해, 다음을 수행하여 리소스를 만든 후 Azure 포털에서 진단 로그를 사용하도록 설정할 수 있습니다.

1. 리소스에 대한 블레이드로 이동하고 **진단** 블레이드를 엽니다.
2. **켜기** 를 클릭하고 저장소 계정 및/또는 이벤트 허브를 선택합니다.

   ![리소스 생성 후에 진단 로그 사용](./media/monitoring-overview-of-diagnostic-logs/enable-portal-existing.png)
3. **로그** 아래에서 수집 또는 스트림할 **로그 범주**를 선택합니다.
4. **Save**를 클릭합니다.

### <a name="enable-diagnostic-logs-via-powershell"></a>PowerShell을 통해 진단 로그 사용
Azure PowerShell Cmdlet을 통해 진단 로그를 사용하도록 설정하려면 다음 명령을 사용합니다.

저장소 계정에서 진단 로그의 저장소를 사용하도록 설정하려면 다음 명령을 사용합니다.

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
```

저장소 계정 ID는 로그를 보낼 저장소 계정에 대한 리소스 ID입니다.

이벤트 허브로 진단 로그의 스트리밍을 사용하도록 설정하려면 다음 명령을 사용합니다.

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
```

서비스 버스 규칙 ID는 `{service bus resource ID}/authorizationrules/{key name}`형식의 문자열입니다.

진단 로그를 Log Analytics 작업 영역으로 보낼 수 있게 하려면 다음 명령을 사용합니다.

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
```

다음 명령을 사용하여 Log Analytics 작업 공간의 리소스 ID를 가져올 수 있습니다.

```powershell
(Get-AzureRmOperationalInsightsWorkspace).ResourceId
```

이러한 매개 변수를 결합하여 여러 출력 옵션을 활성화할 수 있습니다.

### <a name="enable-diagnostic-logs-via-cli"></a>CLI를 통해 진단 로그 사용
Azure CLI를 통해 진단 로그를 사용하도록 설정하려면 다음 명령을 사용합니다.

저장소 계정에서 진단 로그의 저장소를 사용하도록 설정하려면 다음 명령을 사용합니다.

```azurecli
    azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
```

저장소 계정 ID는 로그를 보낼 저장소 계정에 대한 리소스 ID입니다.

이벤트 허브로 진단 로그의 스트리밍을 사용하도록 설정하려면 다음 명령을 사용합니다.

```azurecli
    azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
```

서비스 버스 규칙 ID는 `{service bus resource ID}/authorizationrules/{key name}`형식의 문자열입니다.

진단 로그를 Log Analytics 작업 영역으로 보낼 수 있게 하려면 다음 명령을 사용합니다.

```azurecli
    azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
```

이러한 매개 변수를 결합하여 여러 출력 옵션을 활성화할 수 있습니다.

### <a name="enable-diagnostic-logs-via-rest-api"></a>REST API를 통해 진단 로그 사용
Azure Monitor REST API를 사용하여 진단 설정을 변경하려면 [이 문서](https://msdn.microsoft.com/library/azure/dn931931.aspx)를 참조하세요.

## <a name="manage-diagnostic-settings-in-the-portal"></a>포털에서 진단 설정 관리
리소스를 모두 진단 설정으로 설정했는지 확인합니다. 포털에서 **모니터링** 블레이드로 이동하고 **진단 로그** 블레이드를 엽니다.

![포털의 진단 로그 블레이드](./media/monitoring-overview-of-diagnostic-logs/manage-portal-nav.png)

모니터링 블레이드를 찾기 위해 “더 많은 서비스"를 클릭해야 할 수 있습니다.

이 블레이드에서 진단 로그를 지원하는 모든 리소스를 보고 필터링하여 진단을 사용하도록 설정했는지 확인할 수 있습니다. 해당 로그가 흐르는 저장소 계정, Event Hub 및/또는 Log Analytics 작업 영역을 확인할 수도 있습니다.

![포털의 진단 로그 블레이드 결과](./media/monitoring-overview-of-diagnostic-logs/manage-portal-blade.png)

리소스를 클릭하면 저장소 계정에 저장된 모든 로그가 표시되며 진단 설정을 끄거나 수정할 수 있는 옵션이 제공됩니다. 다운로드 아이콘을 클릭하면 특정 기간의 로그를 다운로드할 수 있습니다.

![진단 로그 브레이드 1 리소스](./media/monitoring-overview-of-diagnostic-logs/manage-portal-logs.png)

> [!NOTE]
> 진단 로그는 이 보기에만 표시되며 저장소 계정에 로그를 저장하기 위한 진단 설정을 구성한 경우 다운로드할 수 있습니다.
>
>

**진단 설정**에 대한 링크를 클릭하면 진단 설정 블레이드가 표시되고 여기에서 선택한 리소스에 대한 진단 설정을 활성화, 비활성화 또는 수정할 수 있습니다.

## <a name="supported-services-and-schema-for-diagnostic-logs"></a>진단 로그에 대한 지원되는 서비스 및 스키마
진단 로그의 스키마는 리소스 및 로그 범주에 따라 달라집니다.   

| 부여 | 스키마 및 문서 |
| --- | --- |
| 부하 분산 장치 |[Azure Load Balancer에 대한 Log analytics](../load-balancer/load-balancer-monitor-log.md) |
| 네트워크 보안 그룹 |[NSG(네트워크 보안 그룹)에 대한 로그 분석](../virtual-network/virtual-network-nsg-manage-log.md) |
| 응용 프로그램 게이트웨이 |[응용 프로그램 게이트웨이에 대한 진단 로깅](../application-gateway/application-gateway-diagnostics.md) |
| 키 자격 증명 모음 |[Azure 키 자격 증명 모음 로깅](../key-vault/key-vault-logging.md) |
| Azure 검색 |[검색 트래픽 분석 설정 및 사용](../search/search-traffic-analytics.md) |
| 데이터 레이크 저장소 |[Azure Data Lake Store에 대한 진단 로그에 액세스](../data-lake-store/data-lake-store-diagnostic-logs.md) |
| 데이터 레이크 분석 |[Azure Data Lake Analytics에 대한 진단 로그에 액세스](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Logic Apps |[Logic Apps B2B 사용자 지정 추적 스키마](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Azure 배치 |[Azure 배치 진단 로깅](../batch/batch-diagnostics.md) |
| Azure 자동화 |[Azure Automation에 대한 Log Analytics](../automation/automation-manage-send-joblogs-log-analytics.md) |
| 이벤트 허브(영문) |[Azure Event Hubs 진단 로그](../event-hubs/event-hubs-diagnostic-logs.md) |
| Stream Analytics |[작업 진단 로그](../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Service Bus |[Azure Service Bus 진단 로그](../service-bus-messaging/service-bus-diagnostic-logs.md) |


## <a name="supported-log-categories-per-resource-type"></a>각 리소스 유형별 지원되는 로그 범주
|리소스 종류|Category|범주 표시 이름|
|---|---|---|
|Microsoft.ApiManagement/service|GatewayLogs|ApiManagement 게이트웨이 관련 로그|
|Microsoft.Automation/automationAccounts|JobLogs|작업 로그|
|Microsoft.Automation/automationAccounts|JobStreams|작업 스트림|
|Microsoft.Automation/automationAccounts|DscNodeStatus|디스크 노드 상태|
|Microsoft.Batch/batchAccounts|ServiceLog|서비스 로그|
|Microsoft.DataLakeAnalytics/accounts|감사|감사 로그|
|Microsoft.DataLakeAnalytics/accounts|요청|요청 로그|
|Microsoft.DataLakeStore/accounts|감사|감사 로그|
|Microsoft.DataLakeStore/accounts|요청|요청 로그|
|Microsoft.EventHub/namespaces|ArchiveLogs|보관 로그|
|Microsoft.EventHub/namespaces|OperationalLogs|작업 로그|
|Microsoft.EventHub/namespaces|AutoScaleLogs|자동 크기 조정 로그|
|Microsoft.KeyVault/vaults|AuditEvent|감사 로그|
|Microsoft.Logic/workflows|WorkflowRuntime|워크플로 런타임 진단 이벤트|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|통합 계정 이벤트 추적|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|네트워크 보안 그룹 이벤트|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|네트워크 보안 그룹 규칙 카운터|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupFlowEvent|네트워크 보안 그룹 규칙 흐름 이벤트|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|부하 분산 장치 경고 이벤트|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|부하 분산 장치 프로브 상태|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Application Gateway 액세스 로그|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Application Gateway 성능 로그|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Application Gateway 방화벽 로그|
|Microsoft.Network/expressRouteCircuits|GWMCountersTable|GWM 카운터 테이블|
|Microsoft.Search/searchServices|OperationLogs|작업 로그|
|Microsoft.ServerManagement/nodes|RequestLogs|요청 로그|
|Microsoft.ServiceBus/namespaces|OperationalLogs|작업 로그|
|Microsoft.StreamAnalytics/streamingjobs|실행|실행|
|Microsoft.StreamAnalytics/streamingjobs|작성|작성|

## <a name="next-steps"></a>다음 단계

* [**Event Hubs**로 진단 로그 스트림](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Azure Monitor REST API를 사용하여 진단 설정 변경](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Azure Storage에서 Log Analytics를 사용하여 로그 분석](../log-analytics/log-analytics-azure-storage.md)

