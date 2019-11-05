---
title: Azure 리소스 로그에서 지원 되는 서비스 및 스키마
description: Azure 진단 로그에 대해 지원되는 서비스 및 이벤트 스키마에 대해 알아봅니다.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: reference
ms.date: 10/22/2019
author: rboucher
ms.author: robb
ms.openlocfilehash: 0031a0c96ecadbb3c7d3a479384bee92ba4d102c
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161979"
---
# <a name="supported-services-schemas-and-categories-for-azure-resource-logs"></a>Azure 리소스 로그에 대해 지원 되는 서비스, 스키마 및 범주

> [!NOTE]
> 이전에는 리소스 로그를 진단 로그 라고 했습니다.

[Azure Monitor 리소스 로그](../../azure-monitor/platform/resource-logs-overview.md) 는 해당 서비스 또는 리소스의 작업을 설명 하는 Azure 서비스에서 내보낸 로그입니다. Azure Monitor를 통해 제공 되는 모든 리소스 로그는 일반적인 최상위 스키마를 공유 하며 각 서비스는 고유한 이벤트의 고유한 속성을 내보낼 수 있는 유연성을 제공 합니다.

리소스 종류(`resourceId` 속성에 제공) 및 `category`가 조합되어 스키마를 고유하게 식별합니다. 이 문서에서는 리소스 로그의 최상위 스키마와 각 서비스에 대 한 schemata 링크를 설명 합니다.

## <a name="top-level-resource-logs-schema"></a>최상위 리소스 로그 스키마

| Name | 필수/선택 | 설명 |
|---|---|---|
| 실시간 | 필수 | 이벤트의 타임스탬프(UTC)입니다. |
| resourceId | 필수 | 이벤트를 내보낸 리소스의 리소스 ID입니다. 테넌트 서비스의 경우 /tenants/tenant-id/providers/provider-name의 형태입니다. |
| tenantId | 테넌트 로그에 필요 | 이 이벤트가 연결된 Active Directory 테넌트의 테넌트 ID입니다. 이 속성은 테넌트 수준 로그에만 사용되며 리소스 수준 로그에는 나타나지 않습니다. |
| operationName | 필수 | 이 이벤트가 나타내는 작업의 이름입니다. 이벤트가 RBAC 작업을 나타내는 경우, RBAC 작업 이름입니다(예: Microsoft.Storage/storageAccounts/blobServices/blobs/Read). 실제로 문서화된 리소스 관리자 작업은 아니지만, 일반적으로 리소스 관리자 작업 형태로 모델링됩니다(`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`). |
| operationVersion | 옵션 | operationName이 API를 사용하여 수행된 경우, 작업과 연결된 api-version입니다(예: `http://myservice.windowsazure.net/object?api-version=2016-06-01`). 이 작업에 해당하는 API가 없으면, 버전은 작업과 연결된 속성이 나중에 변경될 경우, 해당 작업의 버전을 나타냅니다. |
| 카테고리 | 필수 | 이벤트의 로그 범주입니다. 범주는 특정 리소스에 대해 로그를 사용하거나 사용하지 않도록 설정할 수 있는 세분성입니다. 이벤트의 속성 Blob에 표시되는 속성은 특정 로그 범주 및 리소스 종류 내에서 동일합니다. 일반적인 로그 범주는 “감사”, “작동”, “실행” 및 “요청”입니다. |
| resultType | 옵션 | 이벤트의 상태입니다. 일반적인 값으로 시작됨, 진행 중, 성공, 실패, 활성 및 확인됨이 있습니다. |
| resultSignature | 옵션 | 이벤트의 하위 상태입니다. 이 작업이 REST API 호출에 해당하는 경우, 해당 REST 호출의 HTTP 상태 코드입니다. |
| resultDescription | 옵션 | 이 작업에 대한 정적 텍스트 설명입니다(예: “스토리지 파일 가져오기”). |
| durationMS | 옵션 | 밀리초 단위의 작업 기간입니다. |
| callerIpAddress | 옵션 | 작업이 공개적으로 사용 가능한 IP 주소가 있는 엔터티에서 시작된 API 호출에 해당하는 경우, 호출자 IP 주소입니다. |
| CorrelationId | 옵션 | 관련 이벤트 집합을 그룹화하는 데 사용되는 GUID입니다. 일반적으로, 두 이벤트의 operationName이 같고 상태가 다른(예: “시작됨” 및 “성공”) 경우, 동일한 상관 관계 ID를 공유합니다. 이벤트 간의 다른 관계를 나타낼 수도 있습니다. |
| ID | 옵션 | 작업을 수행한 사용자 또는 애플리케이션의 ID를 설명하는 JSON Blob입니다. 일반적으로 활성 디렉터리의 클레임/JWT 토큰 및 권한 부여가 포함됩니다. |
| 수준 | 옵션 | 이벤트의 심각도 수준입니다. 정보, 경고, 오류 또는 위험 중 하나여야 합니다. |
| location | 옵션 | 이벤트를 내보내는 리소스의 지역입니다(예: “미국 동부” 또는 “프랑스 남부”). |
| properties | 옵션 | 이 특정 범주의 이벤트와 관련된 확장 속성입니다. 모든 사용자 지정/고유 속성은 스키마의 “파트 B”에 넣어야 합니다. |

## <a name="service-specific-schemas-for-resource-logs"></a>리소스 로그의 서비스별 스키마
리소스 진단 로그의 스키마는 리소스 및 로그 범주에 따라 달라집니다. 이 목록에는 사용 가능한 리소스 로그와 서비스 및 범주 관련 스키마 (사용 가능한 경우)에 대 한 링크를 만드는 모든 서비스가 표시 됩니다.

| 부여 | 스키마 및 문서 |
| --- | --- |
| Azure Active Directory | [개요](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), [감사 로그 스키마](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) 및 [로그인 스키마](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API Management | [API Management 리소스 로그](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Application Gateway |[Application Gateway에 대 한 로깅](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Azure Automation에 대한 Log Analytics](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure Batch 로깅](../../batch/batch-diagnostics.md) |
| Azure Database for MySQL | [Azure Database for MySQL 진단 로그](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Azure Database for PostgreSQL 로그](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Azure Data Explorer | [Azure 데이터 탐색기 로그](../../data-explorer/using-diagnostic-logs.md) |
| Cognitive Services | [Azure Cognitive Services에 대 한 로깅](../../cognitive-services/diagnostic-logging.md) |
| Content Delivery Network | [CDN에 대 한 Azure 로그](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Azure Cosmos DB 로깅](../../cosmos-db/logging.md) |
| Data Factory | [Azure Monitor를 사용하여 데이터 팩터리 모니터링](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Azure Data Lake Analytics에 대 한 로그 액세스](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Azure Data Lake Store에 대 한 로그 액세스](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs(영문) |[Azure Event Hubs 로그](../../event-hubs/event-hubs-diagnostic-logs.md) |
| Express 경로 | 스키마를 사용할 수 없음 |
| Azure Firewall | 스키마를 사용할 수 없음 |
| IoT Hub | [IoT Hub 작업](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Azure Key Vault 로깅](../../key-vault/key-vault-logging.md) |
| Kubernetes 서비스 |[Azure Kubernetes 로깅](../../aks/view-master-logs.md#log-event-schema) |
| 부하 분산 장치 |[Azure Load Balancer에 대한 Log analytics](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Logic Apps B2B 사용자 지정 추적 스키마](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| 네트워크 보안 그룹 |[NSG(네트워크 보안 그룹)에 대한 로그 분석](../../virtual-network/virtual-network-nsg-manage-log.md) |
| DDoS 보호 | [Azure DDoS Protection 표준 관리](../../virtual-network/manage-ddos-protection.md) |
| Power BI 전용 | [Azure의 Power BI Embedded에 대 한 로깅](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Azure Backup용 데이터 모델](../../backup/backup-azure-reports-data-model.md)|
| 검색 |[검색 트래픽 분석 설정 및 사용](../../search/search-traffic-analytics.md) |
| Service Bus |[Azure Service Bus 로그](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Azure SQL Database 로깅](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[작업 로그](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [Traffic Manager 로그 스키마](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Virtual Network | 스키마를 사용할 수 없음 |
| Virtual Network 게이트웨이 | 스키마를 사용할 수 없음 |

## <a name="supported-log-categories-per-resource-type"></a>각 리소스 유형별 지원되는 로그 범주
|리소스 종류|Category|범주 표시 이름|
|---|---|---|
|Microsoft AAD/domainServices|SystemSecurity|SystemSecurity|
|Microsoft AAD/domainServices|AccountManagement|AccountManagement|
|Microsoft AAD/domainServices|로그온 로그 오프|로그온 로그 오프|
|Microsoft AAD/domainServices|ObjectAccess|ObjectAccess|
|Microsoft AAD/domainServices|PolicyChange|PolicyChange|
|Microsoft AAD/domainServices|PrivilegeUse|PrivilegeUse|
|Microsoft AAD/domainServices|DetailTracking|DetailTracking|
|Microsoft AAD/domainServices|DirectoryServiceAccess|DirectoryServiceAccess|
|Microsoft AAD/domainServices|AccountLogon|AccountLogon|
|microsoft.aadiam/tenants|로그인|로그인|
|Microsoft.AnalysisServices/servers|엔진|엔진|
|Microsoft.AnalysisServices/servers|부여|부여|
|Microsoft.ApiManagement/service|GatewayLogs|ApiManagement 게이트웨이 관련 로그|
|Microsoft AppPlatform/스프링|ApplicationConsole|응용 프로그램 콘솔|
|Microsoft.Automation/automationAccounts|JobLogs|작업 로그|
|Microsoft.Automation/automationAccounts|JobStreams|작업 스트림|
|Microsoft.Automation/automationAccounts|DscNodeStatus|디스크 노드 상태|
|Microsoft.Batch/batchAccounts|ServiceLog|서비스 로그|
|Microsoft BatchAI/workspace|BaiClusterEvent|BaiClusterEvent|
|Microsoft BatchAI/workspace|BaiClusterNodeEvent|BaiClusterNodeEvent|
|Microsoft BatchAI/workspace|BaiJobEvent|BaiJobEvent|
|Microsoft Blockchain/blockchainMembers|BlockchainApplication|블록 체인 응용 프로그램|
|Microsoft Blockchain/blockchainMembers|Proxy|Proxy|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|엔드포인트의 메트릭(예: 대역폭, 송신 등)을 가져옵니다.|
|Microsoft.ClassicNetwork/networksecuritygroups|네트워크 보안 그룹 규칙 흐름 이벤트|네트워크 보안 그룹 규칙 흐름 이벤트|
|Microsoft.CognitiveServices/accounts|감사|감사 로그|
|Microsoft.CognitiveServices/accounts|RequestResponse|요청 및 응답 로그|
|Microsoft.ContainerRegistry/registries|ContainerRegistryRepositoryEvents|RepositoryEvent 로그|
|Microsoft.ContainerRegistry/registries|ContainerRegistryLoginEvents|로그인 이벤트|
|Microsoft.ContainerService/managedClusters|kube-apiserver|Kubernetes API 서버|
|Microsoft.ContainerService/managedClusters|kube-controller-manager|Kubernetes 컨트롤러 관리자|
|Microsoft.ContainerService/managedClusters|kube-scheduler|Kubernetes 스케줄러|
|Microsoft.ContainerService/managedClusters|kube-감사|Kubernetes 감사|
|Microsoft.ContainerService/managedClusters|cluster-autoscaler|Kubernetes 클러스터 자동 크기 조정기|
|Databricks/작업 영역|dbfs|Databricks 파일 시스템|
|Databricks/작업 영역|clusters|Databricks 클러스터|
|Databricks/작업 영역|계정|Databricks 계정|
|Databricks/작업 영역|jobs|Databricks 작업|
|Databricks/작업 영역|필기장이|Databricks Notebook|
|Databricks/작업 영역|ssh|Databricks SSH|
|Databricks/작업 영역|작업 영역|Databricks 작업 영역|
|Databricks/작업 영역|secrets|Databricks 비밀|
|Databricks/작업 영역|sqlPermissions|Databricks SQLPermissions|
|Databricks/작업 영역|instancePools|인스턴스 풀|
|Microsoft.datacatalog/datacatalogs|ScanStatusLogEvent|ScanStatus|
|Microsoft.DataFactory/factories|ActivityRuns|파이프라인 작업 실행 로그|
|Microsoft.DataFactory/factories|PipelineRuns|파이프라인 실행 로그|
|Microsoft.DataFactory/factories|TriggerRuns|트리거 실행 로그|
|Microsoft.DataLakeAnalytics/accounts|감사|감사 로그|
|Microsoft.DataLakeAnalytics/accounts|요청|요청 로그|
|Microsoft.DataLakeStore/accounts|감사|감사 로그|
|Microsoft.DataLakeStore/accounts|요청|요청 로그|
|DataShare/계정|공유|공유|
|DataShare/계정|ShareSubscriptions|구독 공유|
|DataShare/계정|SentShareSnapshots|전송 공유 스냅숏|
|DataShare/계정|ReceivedShareSnapshots|받은 공유 스냅숏|
|Microsoft.DBforMySQL/servers|MySqlSlowLogs|MySQL Server 로그|
|Microsoft.DBforMySQL/servers|MySqlAuditLogs|MySQL 감사 로그|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs|PostgreSQL 서버 로그|
|Microsoft.DBforPostgreSQL/servers|QueryStoreRuntimeStatistics|PostgreSQL 쿼리 저장소 런타임 통계|
|Microsoft.DBforPostgreSQL/servers|QueryStoreWaitStatistics|PostgreSQL 쿼리 저장소 대기 통계|
|DBforPostgreSQL/serversv2|PostgreSQLLogs|PostgreSQL 서버 로그|
|DBforPostgreSQL/serversv2|QueryStoreRuntimeStatistics|PostgreSQL 쿼리 저장소 런타임 통계|
|DBforPostgreSQL/serversv2|QueryStoreWaitStatistics|PostgreSQL 쿼리 저장소 대기 통계|
|Microsoft DesktopVirtualization/작업 영역|검사점|검사점|
|Microsoft DesktopVirtualization/작업 영역|오류|오류|
|Microsoft DesktopVirtualization/작업 영역|관리|관리|
|Microsoft DesktopVirtualization/작업 영역|피드|피드|
|Microsoft DesktopVirtualization/applicationGroups|검사점|검사점|
|Microsoft DesktopVirtualization/applicationGroups|오류|오류|
|Microsoft DesktopVirtualization/applicationGroups|관리|관리|
|Microsoft DesktopVirtualization/hostPools|검사점|검사점|
|Microsoft DesktopVirtualization/hostPools|오류|오류|
|Microsoft DesktopVirtualization/hostPools|관리|관리|
|Microsoft DesktopVirtualization/hostPools|연결|연결|
|Microsoft DesktopVirtualization/hostPools|HostRegistration|HostRegistration|
|Microsoft.Devices/IotHubs|연결|연결|
|Microsoft.Devices/IotHubs|DeviceTelemetry|디바이스 원격 분석|
|Microsoft.Devices/IotHubs|C2DCommands|C2D 명령|
|Microsoft.Devices/IotHubs|DeviceIdentityOperations|디바이스 ID 작업|
|Microsoft.Devices/IotHubs|FileUploadOperations|파일 업로드 작업|
|Microsoft.Devices/IotHubs|경로|경로|
|Microsoft.Devices/IotHubs|D2CTwinOperations|D2CTwinOperations|
|Microsoft.Devices/IotHubs|C2DTwinOperations|C2D 쌍 작업|
|Microsoft.Devices/IotHubs|TwinQueries|쌍 쿼리|
|Microsoft.Devices/IotHubs|JobsOperations|작업 연산|
|Microsoft.Devices/IotHubs|DirectMethods|직접 메서드|
|Microsoft.Devices/IotHubs|DistributedTracing|분산 추적(미리 보기)|
|Microsoft.Devices/IotHubs|구성|구성|
|Microsoft.Devices/IotHubs|DeviceStreams|장치 스트림 (미리 보기)|
|Microsoft.Devices/provisioningServices|DeviceOperations|디바이스 작업|
|Microsoft.Devices/provisioningServices|ServiceOperations|서비스 작동|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft.DocumentDB/databaseAccounts|파티션 파티션|파티션 파티션|
|Microsoft.DocumentDB/databaseAccounts|ControlPlaneRequests|ControlPlaneRequests|
|EnterpriseKnowledgeGraph/서비스|AuditEvent|AuditEvent 로그|
|EnterpriseKnowledgeGraph/서비스|DataIssue|DataIssue 로그|
|EnterpriseKnowledgeGraph/서비스|요청|구성 로그|
|Microsoft.EventHub/namespaces|ArchiveLogs|보관 로그|
|Microsoft.EventHub/namespaces|OperationalLogs|작업 로그|
|Microsoft.EventHub/namespaces|AutoScaleLogs|자동 크기 조정 로그|
|Microsoft.EventHub/namespaces|KafkaCoordinatorLogs|Kafka 코디네이터 로그|
|Microsoft.EventHub/namespaces|KafkaUserErrorLogs|Kafka 사용자 오류 로그|
|Microsoft.EventHub/namespaces|EventHubVNetConnectionEvent|VNet/IP 필터링 연결 로그|
|Microsoft.EventHub/namespaces|CustomerManagedKeyUserLogs|고객 관리 키 로그|
|HealthcareApis/서비스|AuditLogs|감사 로그|
|Microsoft.Insights/AutoscaleSettings|AutoscaleEvaluations|자동 크기 조정 평가|
|Microsoft.Insights/AutoscaleSettings|AutoscaleScaleActions|자동 크기 조정 크기 조정 작업|
|Microsoft.IoTSpaces/Graph|추적|추적|
|Microsoft.IoTSpaces/Graph|작동|작동|
|Microsoft.IoTSpaces/Graph|감사|감사|
|Microsoft.IoTSpaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Microsoft.IoTSpaces/Graph|수신|수신|
|Microsoft.IoTSpaces/Graph|송신|송신|
|Microsoft.KeyVault/vaults|AuditEvent|감사 로그|
|Microsoft.Kusto/Clusters|SucceededIngestion|수집 작업 성공|
|Microsoft.Kusto/Clusters|FailedIngestion|실패 한 수집 작업|
|Microsoft.Logic/workflows|WorkflowRuntime|워크플로 런타임 진단 이벤트|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|통합 계정 이벤트 추적|
|Microsoft.MachineLearningServices/workspaces|AmlComputeClusterEvent|AmlComputeClusterEvent|
|Microsoft.MachineLearningServices/workspaces|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|
|Microsoft.MachineLearningServices/workspaces|AmlComputeJobEvent|AmlComputeJobEvent|
|Microsoft. Media/windowsazure.mediaservices|KeyDeliveryRequests|키 배달 요청|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|네트워크 보안 그룹 이벤트|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|네트워크 보안 그룹 규칙 카운터|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupFlowEvent|네트워크 보안 그룹 규칙 흐름 이벤트|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|DDoS 보호 알림|
|Microsoft.Network/publicIPAddresses|DDoSMitigationFlowLogs|DDoS 완화 결정의 흐름 로그|
|Microsoft.Network/publicIPAddresses|DDoSMitigationReports|DDoS 완화의 보고서|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|VM 보호 경고|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Application Gateway 액세스 로그|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Application Gateway 성능 로그|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Application Gateway 방화벽 로그|
|Microsoft.Network/azurefirewalls|AzureFirewallApplicationRule|Azure Firewall 애플리케이션 규칙|
|Microsoft.Network/azurefirewalls|AzureFirewallNetworkRule|Azure Firewall 네트워크 규칙|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|게이트웨이 진단 로그|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|터널 진단 로그|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|경로 진단 로그|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|IKE 진단 로그|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|P2P 진단 로그|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|Traffic Manager 프로브 상태 결과 이벤트|
|Microsoft.Network/expressRouteCircuits|PeeringRouteLog|피어링 경로 테이블 로그|
|Microsoft. Network/vpnGateways|GatewayDiagnosticLog|게이트웨이 진단 로그|
|Microsoft. Network/vpnGateways|TunnelDiagnosticLog|터널 진단 로그|
|Microsoft. Network/vpnGateways|RouteDiagnosticLog|경로 진단 로그|
|Microsoft. Network/vpnGateways|IKEDiagnosticLog|IKE 진단 로그|
|Microsoft.Network/frontdoors|FrontdoorAccessLog|Frontdoor 액세스 로그|
|Microsoft.Network/frontdoors|FrontdoorWebApplicationFirewallLog|Frontdoor 웹 애플리케이션 방화벽 로그|
|Microsoft. Network/p2sVpnGateways|GatewayDiagnosticLog|게이트웨이 진단 로그|
|Microsoft. Network/p2sVpnGateways|IKEDiagnosticLog|IKE 진단 로그|
|Microsoft. Network/p2sVpnGateways|P2SDiagnosticLog|P2P 진단 로그|
|Microsoft. Network/bastionHosts|BastionAuditLogs|요새 감사 로그|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|부하 분산 장치 경고 이벤트|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|부하 분산 장치 프로브 상태|
|Microsoft.PowerBIDedicated/capacities|엔진|엔진|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Azure Backup 보고 데이터|
|Microsoft.RecoveryServices/Vaults|CoreAzureBackup|핵심 Azure Backup 데이터|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupJobs|Addon Azure Backup 작업 데이터|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupAlerts|추가 기능 Azure Backup 경고 데이터|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupPolicy|추가 기능 Azure Backup 정책 데이터|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupStorage|추가 기능 Azure Backup 저장소 데이터|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupProtectedInstance|Addon Azure Backup 보호 된 인스턴스 데이터|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Azure Site Recovery 작업|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Azure Site Recovery 이벤트|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Azure Site Recovery 복제된 항목|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Azure Site Recovery 복제 통계|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery 복구 지점|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationDataUploadRate|Azure Site Recovery 복제 데이터 업로드 속도|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery 보호된 디스크 데이터 변동|
|Microsoft.Search/searchServices|OperationLogs|작업 로그|
|Microsoft.ServiceBus/namespaces|OperationalLogs|작업 로그|
|Microsoft.Sql/servers/databases|SQLInsights|SQL 정보|
|Microsoft.Sql/servers/databases|AutomaticTuning|자동 조정|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatistics|쿼리 저장소 런타임 통계|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatistics|쿼리 저장소 대기 통계|
|Microsoft.Sql/servers/databases|오류|오류|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics|데이터베이스 대기 통계|
|Microsoft.Sql/servers/databases|시간 제한|시간 제한|
|Microsoft.Sql/servers/databases|블록|블록|
|Microsoft.Sql/servers/databases|교착 상태|교착 상태|
|Microsoft.Sql/servers/databases|감사|감사 로그|
|Microsoft.Sql/servers/databases|SQLSecurityAuditEvents|SQL 보안 감사 이벤트|
|Microsoft.Sql/servers/databases|DmsWorkers|DMS 작업자|
|Microsoft.Sql/servers/databases|ExecRequests|실행 요청|
|Microsoft.Sql/servers/databases|RequestSteps|요청 단계|
|Microsoft.Sql/servers/databases|SqlRequests|SQL 요청|
|Microsoft.Sql/servers/databases|대기|대기|
|Microsoft.Sql/managedInstances|ResourceUsageStats|리소스 사용량 통계|
|Microsoft.Sql/managedInstances|SQLSecurityAuditEvents|SQL 보안 감사 이벤트|
|Microsoft.Sql/managedInstances/databases|SQLInsights|SQL 정보|
|Microsoft.Sql/managedInstances/databases|QueryStoreRuntimeStatistics|쿼리 저장소 런타임 통계|
|Microsoft.Sql/managedInstances/databases|QueryStoreWaitStatistics|쿼리 저장소 대기 통계|
|Microsoft.Sql/managedInstances/databases|오류|오류|
|Microsoft.Storage/storageAccounts/tableServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/tableServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/tableServices|StorageDelete|StorageDelete|
|Microsoft.Storage/storageAccounts/blobServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/blobServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/blobServices|StorageDelete|StorageDelete|
|Microsoft.Storage/storageAccounts/fileServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/fileServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/fileServices|StorageDelete|StorageDelete|
|Microsoft.Storage/storageAccounts/queueServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/queueServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/queueServices|StorageDelete|StorageDelete|
|Microsoft.StreamAnalytics/streamingjobs|실행|실행|
|Microsoft.StreamAnalytics/streamingjobs|작성|작성|
|microsoft 웹/hostingenvironments|Appservice환경 Platformlogs|App Service Environment 플랫폼 로그|
|microsoft.web/sites|FunctionAppLogs|함수 응용 프로그램 로그|
|microsoft.web/sites|AppServiceHTTPLogs|HTTP 로그|
|microsoft.web/sites|AppServiceConsoleLogs|App Service 콘솔 로그|
|microsoft.web/sites|AppServiceAppLogs|응용 프로그램 로그 App Service|
|microsoft.web/sites|AppServiceFileAuditLogs|사이트 콘텐츠 변경 감사 로그|
|microsoft.web/sites|AppServiceAuditLogs|감사 로그 액세스|
|microsoft.web/sites/slots|FunctionAppLogs|함수 응용 프로그램 로그|
|microsoft.web/sites/slots|AppServiceHTTPLogs|HTTP 로그|
|microsoft.web/sites/slots|AppServiceConsoleLogs|콘솔 로그|
|microsoft.web/sites/slots|AppServiceAppLogs|애플리케이션 로그|
|microsoft.web/sites/slots|AppServiceFileAuditLogs|사이트 콘텐츠 변경 감사 로그|
|microsoft.web/sites/slots|AppServiceAuditLogs|감사 로그 액세스|

## <a name="next-steps"></a>다음 단계

* [리소스 로그에 대해 자세히 알아보기](../../azure-monitor/platform/resource-logs-overview.md)
* [**Event Hubs** 에 대 한 리소스 리소스 로그 스트림](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)
* [Azure Monitor REST API를 사용 하 여 리소스 로그 진단 설정 변경](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [Azure Storage에서 Log Analytics를 사용하여 로그 분석](../../azure-monitor/platform/collect-azure-metrics-logs.md)
