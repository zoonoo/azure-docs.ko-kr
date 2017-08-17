---
title: "Azure 진단 로그 | Microsoft Docs"
description: "Azure 진단 로그란 무엇이고 Azure 리소스 내에서 발생하는 이벤트를 파악하는 데 어떻게 사용할 수 있는지 알아봅니다."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: fe8887df-b0e6-46f8-b2c0-11994d28e44f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: johnkem; magoedte
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: 8961676a60d922912e383937ca38c5d2f89a348a
ms.contentlocale: ko-kr
ms.lasthandoff: 08/04/2017

---
# <a name="collect-and-consume-log-data-from-your-azure-resources"></a>Azure 리소스에서 로그 데이터 수집 및 소비

## <a name="what-are-azure-resource-diagnostic-logs"></a>Azure 리소스 진단 로그란?
**Azure 리소스 수준 진단 로그**는 해당 리소스의 작업에 대한 풍부하고 빈번한 데이터를 제공하는 리소스에서 내보낸 로그입니다. 이러한 로그의 내용은 리소스 유형에 따라 달라집니다. 예를 들어 네트워크 보안 그룹 규칙 카운터와 Key Vault 감사는 리소스 로그의 두 범주입니다.

리소스 수준 진단 로그는 [활동 로그](monitoring-overview-activity-logs.md)와 다릅니다. 활동 로그는 가상 컴퓨터 만들기나 논리 앱 삭제 등, Resource Manager를 사용하여 구독에서 리소스에 대해 수행된 작업에 대한 정보를 제공합니다. 활동 로그는 구독 수준 로그입니다. 리소스 수준 진단 로그는 Key Vault에서 암호 가져오기 등과 같이 리소스 자체에서 수행된 작업에 대한 정보를 제공합니다.

리소스 수준 진단 로그도 게스트 OS 수준 진단 로그와 다릅니다. 게스트 OS 진단 로그는 가상 컴퓨터나 다른 지원되는 리소스 유형 안에서 실행되는 에이전트가 수집합니다. 리소스 수준 진단 로그는 에이전트가 필요하지 않으며 Azure 플랫폼 자체에서 리소스 특정 데이터를 수집하고, 게스트 OS 수준 진단 로그는 가상 컴퓨터에서 실행되는 운영 체제 및 응용 프로그램에서 데이터를 수집합니다.

일부 리소스만 여기서 설명하는 새로운 형식의 리소스 진단 로그를 지원합니다. 이 문서는 리소스 유형에서 새로운 리소스 수준 진단 로그에 지원하는 나열된 섹션을 포함합니다.

![리소스 진단 로그와 다른 로그 유형 비교 ](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_vs_other_logs_v5.png)

그림 1: 리소스 진단 로그와 다른 로그 유형 비교

## <a name="what-you-can-do-with-resource-level-diagnostic-logs"></a>리소스 수준 진단 로그로 수행할 수 있는 작업
리소스 수준 진단 로그를 통해 수행할 수 있는 몇 가지 작업은 다음과 같습니다.

![리소스 진단 로그의 논리적 배치](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_Actions.png)


* 감사 또는 수동 검사를 위해 [**저장소 계정**](monitoring-archive-diagnostic-logs.md)에 저장합니다. **리소스 진단 설정**을 사용하여 보존 기간(일)을 지정할 수 있습니다.
* [타사 서비스 또는 사용자 지정 분석 솔루션(예: PowerBI)으로 수집을 위해 **Event Hubs**로 스트림](monitoring-stream-diagnostic-logs-to-event-hubs.md)합니다.
* [OMS Log Analytics](../log-analytics/log-analytics-azure-storage.md)

로그를 내보내는 것과 동일한 구독에 위치하지 않는 저장소 계정 또는 Event Hubs 네임스페이스를 사용할 수 있습니다. 설정을 구성하는 사용자에게는 두 구독에 대한 적절한 RBAC 액세스가 있어야 합니다.

## <a name="resource-diagnostic-settings"></a>리소스 진단 설정
리소스 진단 설정을 사용하여 비-계산 리소스에 대한 리소스 진단 로그를 구성합니다. 리소스 제어를 위한 **리소스 진단 설정** :

* 리소스 진단 로그를 보낼 위치(Storage 계정, Event Hubs 및/또는 OMS Log Analytics).
* 보낼 로그 범주.
* 각 로그 항목을 저장소 계정에 유지해야 하는 기간.
    - 보존이 0일이라는 것은 로그가 영원히 보관된다는 의미입니다. 그렇지 않은 경우 값은 1에서 2147483647 사이의 숫자일 수 있습니다.
    - 보존 정책이 설정되었지만 저장소 계정에 로그를 저장할 수 없는 경우(예를 들어 Event Hubs 또는 OMS 옵션만 선택한 경우) 보존 정책은 적용되지 않습니다.
    - 보존 정책은 매일 적용되므로 하루의 마지막에(UTC) 보존 정책이 지난 날의 로그가 삭제됩니다. 예를 들어, 하루의 보존 정책이 있는 경우 오늘 날짜가 시작될 때 하루 전의 로그가 삭제됩니다.

이러한 설정은 Azure Portal에서 리소스에 대한 진단 블레이드를 통해, Azure PowerShell 및 CLI 명령을 통하거나 [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931943.aspx)를 통해 쉽게 구성합니다.

> [!WARNING]
> 계산 리소스(예: VM 또는 서비스 패브릭)에 대한 진단 로그 및 메트릭에서는 [출력의 구성 및 선택을 위한 별도의 메커니즘](../azure-diagnostics.md)을 사용합니다.
>
>

## <a name="how-to-enable-collection-of-resource-diagnostic-logs"></a>리소스 진단 로그의 컬렉션을 사용하도록 설정하는 방법 
[Resource Manager 템플릿에서 리소스 만들기의 일부로](./monitoring-enable-diagnostic-logs-using-template.md) 또는 포털에서 리소스 블레이드를 통해 리소스를 만든 후 리소스 진단 로그의 컬렉션을 사용하도록 설정할 수 있습니다. 또한 Azure PowerShell 또는 CLI 명령을 사용하거나 Azure Monitor REST API를 사용하여 언제든지 컬렉션을 사용하도록 설정할 수도 있습니다.

> [!TIP]
> 이러한 지침은 모든 리소스에 직접 적용되지 않을 수 있습니다. 특정 리소스 형식에 적용할 수 있는 특수한 단계를 알아보려면 이 페이지 맨 아래에 있는 스키마 링크를 참조하세요.
>
>

### <a name="enable-collection-of-resource-diagnostic-logs-in-the-portal"></a>포털에서 리소스 진단 로그 컬렉션 활성화
다음 단계를 수행하여 리소스를 만든 후 Azure Portal에서 리소스 진단 로그의 컬렉션을 활성화할 수 있습니다.

1. 리소스에 대한 블레이드로 이동하고 **진단** 블레이드를 엽니다.
2. **켜기**를 클릭하고 저장소 계정 및/또는 이벤트 허브를 선택합니다.

   ![리소스 생성 후에 진단 로그 사용](./media/monitoring-overview-of-diagnostic-logs/enable-portal-existing.png)
3. **로그** 아래에서 수집 또는 스트림할 **로그 범주**를 선택합니다.
4. **Save**를 클릭합니다.

### <a name="enable-collection-of-resource-diagnostic-logs-via-powershell"></a>PowerShell을 통한 리소스 진단 로그 컬렉션 활성화
Azure PowerShell에서 리소스 진단 로그 컬렉션을 활성화하려면 다음 명령을 사용합니다.

저장소 계정에서 진단 로그의 저장소를 활성화하려면 다음 명령을 사용합니다.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
```

저장소 계정 ID는 로그를 보낼 저장소 계정에 대한 리소스 ID입니다.

이벤트 허브로의 진단 로그 스트리밍을 활성화하려면 다음 명령을 사용합니다.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your Service Bus rule id] -Enabled $true
```

서비스 버스 규칙 ID는 `{Service Bus resource ID}/authorizationrules/{key name}` 형식의 문자열입니다.

진단 로그를 Log Analytics 작업 영역으로 보낼 수 있게 하려면 다음 명령을 사용합니다.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
```

다음 명령을 사용하여 Log Analytics 작업 공간의 리소스 ID를 가져올 수 있습니다.

```powershell
(Get-AzureRmOperationalInsightsWorkspace).ResourceId
```

이러한 매개 변수를 결합하여 여러 출력 옵션을 활성화할 수 있습니다.

### <a name="enable-collection-of-resource-diagnostic-logs-via-cli"></a>CLI를 통해 리소스 진단 로그 컬렉션 활성화
Azure CLI를 통해 리소스 진단 로그 컬렉션을 활성화하려면 다음 명령을 사용합니다.

저장소 계정에서 진단 로그의 저장소를 사용하도록 설정하려면 다음 명령을 사용합니다.

```azurecli
azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
```

저장소 계정 ID는 로그를 보낼 저장소 계정에 대한 리소스 ID입니다.

이벤트 허브로의 진단 로그 스트리밍을 활성화하려면 다음 명령을 사용합니다.

```azurecli
azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
```

서비스 버스 규칙 ID는 `{Service Bus resource ID}/authorizationrules/{key name}` 형식의 문자열입니다.

진단 로그를 Log Analytics 작업 영역으로 보낼 수 있게 하려면 다음 명령을 사용합니다.

```azurecli
azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
```

이러한 매개 변수를 결합하여 여러 출력 옵션을 활성화할 수 있습니다.

### <a name="enable-collection-of-resource-diagnostic-logs-via-rest-api"></a>REST API를 통해 리소스 진단 로그 컬렉션 활성화
Azure Monitor REST API를 사용하여 진단 설정을 변경하려면 [이 문서](https://msdn.microsoft.com/library/azure/dn931931.aspx)를 참조하세요.

## <a name="manage-resource-diagnostic-settings-in-the-portal"></a>포털에서 리소스 진단 설정 관리
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

## <a name="supported-services-and-schema-for-resource-diagnostic-logs"></a>리소스 진단 로그에 대해 지원되는 서비스 및 스키마
리소스 진단 로그의 스키마는 리소스 및 로그 범주에 따라 달라집니다.   

| 부여 | 스키마 및 문서 |
| --- | --- |
| API Management | 스키마를 사용할 수 없음 |
| 응용 프로그램 게이트웨이 |[Application Gateway에 대한 진단 로깅](../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Azure Automation에 대한 Log Analytics](../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure Batch 진단 로깅](../batch/batch-diagnostics.md) |
| Customer Insights | 스키마를 사용할 수 없음 |
| Content Delivery Network | 스키마를 사용할 수 없음 |
| CosmosDB | 스키마를 사용할 수 없음 |
| 데이터 레이크 분석 |[Azure Data Lake Analytics에 대한 진단 로그에 액세스](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| 데이터 레이크 저장소 |[Azure Data Lake Store에 대한 진단 로그에 액세스](../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs |[Azure Event Hubs 진단 로그](../event-hubs/event-hubs-diagnostic-logs.md) |
| 키 자격 증명 모음 |[Azure Key Vault 로깅](../key-vault/key-vault-logging.md) |
| 부하 분산 장치 |[Azure Load Balancer에 대한 Log analytics](../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Logic Apps B2B 사용자 지정 추적 스키마](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| 네트워크 보안 그룹 |[NSG(네트워크 보안 그룹)에 대한 로그 분석](../virtual-network/virtual-network-nsg-manage-log.md) |
| Recovery Services | 스키마를 사용할 수 없음|
| 검색 |[검색 트래픽 분석 설정 및 사용](../search/search-traffic-analytics.md) |
| 서버 관리 | 스키마를 사용할 수 없음 |
| Service Bus |[Azure Service Bus 진단 로그](../service-bus-messaging/service-bus-diagnostic-logs.md) |
| Stream Analytics |[작업 진단 로그](../stream-analytics/stream-analytics-job-diagnostic-logs.md) |

## <a name="supported-log-categories-per-resource-type"></a>각 리소스 유형별 지원되는 로그 범주
|리소스 종류|Category|범주 표시 이름|
|---|---|---|
|Microsoft.ApiManagement/service|GatewayLogs|ApiManagement 게이트웨이 관련 로그|
|Microsoft.Automation/automationAccounts|JobLogs|작업 로그|
|Microsoft.Automation/automationAccounts|JobStreams|작업 스트림|
|Microsoft.Automation/automationAccounts|DscNodeStatus|디스크 노드 상태|
|Microsoft.Batch/batchAccounts|ServiceLog|서비스 로그|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|끝점의 메트릭(예: 대역폭, 송신 등)을 가져옵니다.|
|Microsoft.CustomerInsights/hubs|AuditEvents|AuditEvents|
|Microsoft.DataLakeAnalytics/accounts|감사|감사 로그|
|Microsoft.DataLakeAnalytics/accounts|요청|요청 로그|
|Microsoft.DataLakeStore/accounts|감사|감사 로그|
|Microsoft.DataLakeStore/accounts|요청|요청 로그|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.EventHub/namespaces|ArchiveLogs|보관 로그|
|Microsoft.EventHub/namespaces|OperationalLogs|작업 로그|
|Microsoft.EventHub/namespaces|AutoScaleLogs|자동 크기 조정 로그|
|Microsoft.KeyVault/vaults|AuditEvent|감사 로그|
|Microsoft.Logic/workflows|WorkflowRuntime|워크플로 런타임 진단 이벤트|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|통합 계정 이벤트 추적|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|네트워크 보안 그룹 이벤트|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|네트워크 보안 그룹 규칙 카운터|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|부하 분산 장치 경고 이벤트|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|부하 분산 장치 프로브 상태|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Application Gateway 액세스 로그|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Application Gateway 성능 로그|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Application Gateway 방화벽 로그|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Azure Backup 보고 데이터|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Azure Site Recovery 작업|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Azure Site Recovery 이벤트|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Azure Site Recovery 복제된 항목|
|Microsoft.Search/searchServices|OperationLogs|작업 로그|
|Microsoft.ServiceBus/namespaces|OperationalLogs|작업 로그|
|Microsoft.StreamAnalytics/streamingjobs|실행|실행|
|Microsoft.StreamAnalytics/streamingjobs|작성|작성|

## <a name="next-steps"></a>다음 단계

* [**Event Hubs**로 리소스 진단 로그 스트림](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Azure Monitor REST API를 사용하여 리소스 진단 설정 변경](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Azure Storage에서 Log Analytics를 사용하여 로그 분석](../log-analytics/log-analytics-azure-storage.md)

