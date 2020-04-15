---
title: Azure 리소스 로그 지원 되는 서비스 및 스키마
description: Azure 리소스 로그에 대해 지원되는 서비스 및 이벤트 스키마를 이해합니다.
ms.subservice: logs
ms.topic: reference
ms.date: 10/22/2019
ms.openlocfilehash: 8abd8767d9bb7e3c4336f6600b94f6b3f4ea48f1
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380513"
---
# <a name="supported-services-schemas-and-categories-for-azure-resource-logs"></a>Azure 리소스 로그에 대한 지원되는 서비스, 스키마 및 범주

> [!NOTE]
> 리소스 로그를 이전에는 진단 로그라고 했습니다.

[Azure Monitor 리소스 로그는](../../azure-monitor/platform/platform-logs-overview.md) 해당 서비스 또는 리소스의 작업을 설명하는 Azure 서비스에서 내보낸 로그입니다. Azure Monitor를 통해 사용할 수 있는 모든 리소스 로그는 공통 최상위 스키마를 공유하며 각 서비스에서 고유한 속성을 자체 이벤트에 대해 내림차순으로 내보를 내도록 합니다.

리소스 종류(`resourceId` 속성에 제공) 및 `category`가 조합되어 스키마를 고유하게 식별합니다. 이 문서에서는 리소스 로그에 대한 최상위 스키마와 각 서비스에 대한 스키마에 대한 링크를 설명합니다.

## <a name="top-level-resource-logs-schema"></a>최상위 리소스 로그 스키마

| 속성 | 필수/선택 | 설명 |
|---|---|---|
| time | 필수 | 이벤트의 타임스탬프(UTC)입니다. |
| resourceId | 필수 | 이벤트를 내보낸 리소스의 리소스 ID입니다. 테넌트 서비스의 경우 /tenants/tenant-id/providers/provider-name의 형태입니다. |
| tenantId | 테넌트 로그에 필요 | 이 이벤트가 연결된 Active Directory 테넌트의 테넌트 ID입니다. 이 속성은 테넌트 수준 로그에만 사용되며 리소스 수준 로그에는 나타나지 않습니다. |
| operationName | 필수 | 이 이벤트가 나타내는 작업의 이름입니다. 이벤트가 RBAC 작업을 나타내는 경우, RBAC 작업 이름입니다(예: Microsoft.Storage/storageAccounts/blobServices/blobs/Read). 실제로 문서화된 리소스 관리자 작업은 아니지만, 일반적으로 리소스 관리자 작업 형태로 모델링됩니다(`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`). |
| operationVersion | 옵션 | operationName이 API를 사용하여 수행된 경우, 작업과 연결된 api-version입니다(예: `http://myservice.windowsazure.net/object?api-version=2016-06-01`). 이 작업에 해당하는 API가 없으면, 버전은 작업과 연결된 속성이 나중에 변경될 경우, 해당 작업의 버전을 나타냅니다. |
| category | 필수 | 이벤트의 로그 범주입니다. 범주는 특정 리소스에 대해 로그를 사용하거나 사용하지 않도록 설정할 수 있는 세분성입니다. 이벤트의 속성 Blob에 표시되는 속성은 특정 로그 범주 및 리소스 종류 내에서 동일합니다. 일반적인 로그 범주는 "감사", "운영" "실행" 및 "요청"입니다. |
| resultType | 옵션 | 이벤트의 상태입니다. 일반적인 값으로 시작됨, 진행 중, 성공, 실패, 활성 및 확인됨이 있습니다. |
| resultSignature | 옵션 | 이벤트의 하위 상태입니다. 이 작업이 REST API 호출에 해당하는 경우, 해당 REST 호출의 HTTP 상태 코드입니다. |
| resultDescription | 옵션 | 이 작업에 대한 정적 텍스트 설명입니다(예: "스토리지 파일 가져옵니다." |
| durationMS | 옵션 | 밀리초 단위의 작업 기간입니다. |
| callerIpAddress | 옵션 | 작업이 공개적으로 사용 가능한 IP 주소가 있는 엔터티에서 시작된 API 호출에 해당하는 경우, 호출자 IP 주소입니다. |
| correlationId | 옵션 | 관련 이벤트 집합을 그룹화하는 데 사용되는 GUID입니다. 일반적으로, 두 이벤트의 operationName이 같고 상태가 다른(예: "시작됨" 및 "성공") 동일한 상관 관계 ID를 공유합니다. 이벤트 간의 다른 관계를 나타낼 수도 있습니다. |
| ID | 옵션 | 작업을 수행한 사용자 또는 애플리케이션의 ID를 설명하는 JSON Blob입니다. 일반적으로 활성 디렉터리의 클레임/JWT 토큰 및 권한 부여가 포함됩니다. |
| Level | 옵션 | 이벤트의 심각도 수준입니다. 정보, 경고, 오류 또는 위험 중 하나여야 합니다. |
| 위치 | 옵션 | 이벤트를 내보내는 리소스의 지역입니다(예: "미국 동부" 또는 "프랑스 남부" |
| properties | 옵션 | 이 특정 범주의 이벤트와 관련된 확장 속성입니다. 모든 사용자 지정/고유 속성은 스키마의 "파트 B" 내에 넣어야 합니다. |

## <a name="service-specific-schemas-for-resource-logs"></a>리소스 로그에 대한 서비스별 스키마
리소스 진단 로그의 스키마는 리소스 및 로그 범주에 따라 달라집니다. 이 목록에는 사용 가능한 리소스 로그 및 서비스 및 범주별 스키마에 대한 링크를 사용할 수 있는 모든 서비스가 표시됩니다.

| 서비스 | 스키마 및 문서 |
| --- | --- |
| Azure Active Directory | [개요,](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md) [감사 로그 스키마](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) 및 [로그인 스키마](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API Management | [API 관리 리소스 로그](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Application Gateway |[응용 프로그램 게이트웨이에 대한 로깅](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Azure 자동화를 위한 로그 분석](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure 일괄 처리 로깅](../../batch/batch-diagnostics.md) |
| Azure Database for MySQL | [Azure Database for MySQL 진단 로그](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [포스트그레SQL 로그에 대한 Azure 데이터베이스](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Azure Data Explorer | [Azure 데이터 탐색기 로그](/azure/data-explorer/using-diagnostic-logs) |
| Cognitive Services | [Azure 인지 서비스에 대한 로깅](../../cognitive-services/diagnostic-logging.md) |
| Container Registry | [Azure 컨테이너 레지스트리에 대한 로깅](../../container-registry/container-registry-diagnostics-audit-logs.md) |
| Content Delivery Network | [CDN용 Azure 로그](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Azure Cosmos DB 로깅](../../cosmos-db/logging.md) |
| Data Factory | [Azure 모니터를 사용하여 데이터 팩터리 모니터링](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Azure 데이터 레이크 분석을 위한 로그 액세스](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Azure 데이터 레이크 저장소에 대한 로그 액세스](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs |[Azure 이벤트 허브 로그](../../event-hubs/event-hubs-diagnostic-logs.md) |
| Express 경로 | 스키마를 사용할 수 없음 |
| Azure Firewall | 스키마를 사용할 수 없음 |
| IoT Hub | [IoT Hub 작업](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Azure 키 볼트 로깅](../../key-vault/key-vault-logging.md) |
| Kubernetes 서비스 |[Azure Kubernetes 로깅](../../aks/view-master-logs.md#log-event-schema) |
| Load Balancer |[Azure Load Balancer에 대한 Log analytics](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Logic Apps B2B 사용자 지정 추적 스키마](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| 네트워크 보안 그룹 |[NSG(네트워크 보안 그룹)에 대한 로그 분석](../../virtual-network/virtual-network-nsg-manage-log.md) |
| DDOS Protection | [Azure DDoS Protection 표준 관리](../../virtual-network/manage-ddos-protection.md) |
| Power BI 전용 | [Azure에 포함된 전원 BI에 대한 로깅](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Azure 백업을 위한 데이터 모델](../../backup/backup-azure-reports-data-model.md)|
| 검색 |[검색 트래픽 분석 설정 및 사용](../../search/search-traffic-analytics.md) |
| Service Bus |[Azure 서비스 버스 로그](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Azure SQL 데이터베이스 로깅](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[작업 로그](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [트래픽 관리자 로그 스키마](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Virtual Network | 스키마를 사용할 수 없음 |
| Virtual Network 게이트웨이 | 스키마를 사용할 수 없음 |

## <a name="supported-log-categories-per-resource-type"></a>각 리소스 유형별 지원되는 로그 범주

일부 범주는 특정 유형의 리소스에 대해서만 지원될 수 있습니다. 이것은 어떤 형태로 사용할 수있는 모든 목록입니다.  예를 들어 Microsoft.Sql/서버/데이터베이스 범주는 모든 유형의 데이터베이스에 사용할 수 없습니다. 자세한 내용은 [SQL Database 진단 로깅에 대한 정보를](../../sql-database/sql-database-metrics-diag-logging.md)참조하십시오. 

|리소스 종류|범주|범주 표시 이름|
|---|---|---|
|마이크로소프트.AAD/도메인서비스|시스템 보안|시스템 보안|
|마이크로소프트.AAD/도메인서비스|계정 관리|계정 관리|
|마이크로소프트.AAD/도메인서비스|로그온로그오프|로그온로그오프|
|마이크로소프트.AAD/도메인서비스|개체 액세스|개체 액세스|
|마이크로소프트.AAD/도메인서비스|정책 변경|정책 변경|
|마이크로소프트.AAD/도메인서비스|권한 사용|권한 사용|
|마이크로소프트.AAD/도메인서비스|세부 정보 추적|세부 정보 추적|
|마이크로소프트.AAD/도메인서비스|디렉토리 서비스 액세스|디렉토리 서비스 액세스|
|마이크로소프트.AAD/도메인서비스|계정 로그온|계정 로그온|
|microsoft.aadiam/tenants|로그인|로그인|
|Microsoft.AnalysisServices/servers|엔진|엔진|
|Microsoft.AnalysisServices/servers|서비스|서비스|
|Microsoft.ApiManagement/service|GatewayLogs|ApiManagement 게이트웨이 관련 로그|
|마이크로소프트.앱플랫폼/봄|응용 프로그램 콘솔|응용 프로그램 콘솔|
|Microsoft.Automation/automationAccounts|JobLogs|작업 로그|
|Microsoft.Automation/automationAccounts|JobStreams|작업 스트림|
|Microsoft.Automation/automationAccounts|DscNodeStatus|디스크 노드 상태|
|Microsoft.Batch/batchAccounts|ServiceLog|서비스 로그|
|마이크로소프트.배치AI/작업 공간|바이클러스터이벤트|바이클러스터이벤트|
|마이크로소프트.배치AI/작업 공간|바이클러스터노드이벤트|바이클러스터노드이벤트|
|마이크로소프트.배치AI/작업 공간|바이잡 이벤트|바이잡 이벤트|
|마이크로소프트.블록체인/블록체인 회원|블록체인 응용 프로그램|블록체인 애플리케이션|
|마이크로소프트.블록체인/블록체인 회원|Proxy|Proxy|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|엔드포인트의 메트릭(예: 대역폭, 송신 등)을 가져옵니다.|
|Microsoft.ClassicNetwork/networksecuritygroups|네트워크 보안 그룹 규칙 흐름 이벤트|네트워크 보안 그룹 규칙 흐름 이벤트|
|Microsoft.CognitiveServices/accounts|감사|감사 로그|
|Microsoft.CognitiveServices/accounts|RequestResponse|요청 및 응답 로그|
|Microsoft.ContainerRegistry/registries|컨테이너레지스트리리포지토리이벤트|리포지토리이벤트 로그(미리 보기)|
|Microsoft.ContainerRegistry/registries|컨테이너레지스트리로그인이벤트|로그인 이벤트(미리 보기)|
|Microsoft.ContainerService/managedClusters|kube-apiserver|Kubernetes API 서버|
|Microsoft.ContainerService/managedClusters|kube-controller-manager|Kubernetes 컨트롤러 관리자|
|Microsoft.ContainerService/managedClusters|kube-scheduler|Kubernetes 스케줄러|
|Microsoft.ContainerService/managedClusters|쿠베 감사|쿠베르네츠 감사|
|Microsoft.ContainerService/managedClusters|cluster-autoscaler|Kubernetes 클러스터 자동 크기 조정기|
|마이크로소프트.데이터브릭스/작업 공간|dbfs|Databricks 파일 시스템|
|마이크로소프트.데이터브릭스/작업 공간|clusters|데이터브릭 클러스터|
|마이크로소프트.데이터브릭스/작업 공간|계정|데이터브릭 계정|
|마이크로소프트.데이터브릭스/작업 공간|jobs|데이터브릭 채용 정보|
|마이크로소프트.데이터브릭스/작업 공간|Notebook|Databricks Notebook|
|마이크로소프트.데이터브릭스/작업 공간|ssh|데이터 브릭스 SSH|
|마이크로소프트.데이터브릭스/작업 공간|작업 영역|데이터 브릭 작업 영역|
|마이크로소프트.데이터브릭스/작업 공간|secrets|데이터브릭의 비밀|
|마이크로소프트.데이터브릭스/작업 공간|sql권한|데이터 브릭 SQL권한|
|마이크로소프트.데이터브릭스/작업 공간|인스턴스풀|인스턴스 풀|
|마이크로소프트.데이터 카탈로그/데이터 카탈로그|스캔 상태 로그이벤트|검색 상태|
|Microsoft.DataFactory/factories|ActivityRuns|파이프라인 작업 실행 로그|
|Microsoft.DataFactory/factories|PipelineRuns|파이프라인 실행 로그|
|Microsoft.DataFactory/factories|TriggerRuns|트리거 실행 로그|
|Microsoft.DataLakeAnalytics/accounts|감사|감사 로그|
|Microsoft.DataLakeAnalytics/accounts|요청|요청 로그|
|Microsoft.DataLakeStore/accounts|감사|감사 로그|
|Microsoft.DataLakeStore/accounts|요청|요청 로그|
|마이크로소프트.데이터 쉐어/계정|공유|공유|
|마이크로소프트.데이터 쉐어/계정|공유 구독|구독 공유|
|마이크로소프트.데이터 쉐어/계정|보낸 공유스냅샷|보낸 공유 스냅샷|
|마이크로소프트.데이터 쉐어/계정|수신된 공유스냅샷|받은 공유 스냅샷|
|Microsoft.DBforMySQL/servers|MySqlSlowLogs|MySQL Server 로그|
|Microsoft.DBforMySQL/servers|마이Sql감사로그|MySQL 감사 로그|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs|PostgreSQL 서버 로그|
|Microsoft.DBforPostgreSQL/servers|QueryStoreRuntimeStatistics|PostgreSQL 쿼리 저장소 런타임 통계|
|Microsoft.DBforPostgreSQL/servers|QueryStoreWaitStatistics|PostgreSQL 쿼리 저장소 대기 통계|
|마이크로소프트.DBforPostgreSQL/서버v2|PostgreSQLLogs|PostgreSQL 서버 로그|
|마이크로소프트.DBforPostgreSQL/서버v2|QueryStoreRuntimeStatistics|PostgreSQL 쿼리 저장소 런타임 통계|
|마이크로소프트.DBforPostgreSQL/서버v2|QueryStoreWaitStatistics|PostgreSQL 쿼리 저장소 대기 통계|
|마이크로소프트.데스크톱 가상화/작업 영역|검사점|검사점|
|마이크로소프트.데스크톱 가상화/작업 영역|Error|Error|
|마이크로소프트.데스크톱 가상화/작업 영역|관리|관리|
|마이크로소프트.데스크톱 가상화/작업 영역|피드|피드|
|마이크로소프트.데스크톱 가상화/응용 프로그램 그룹|검사점|검사점|
|마이크로소프트.데스크톱 가상화/응용 프로그램 그룹|Error|Error|
|마이크로소프트.데스크톱 가상화/응용 프로그램 그룹|관리|관리|
|마이크로소프트.데스크톱 가상화/호스트풀|검사점|검사점|
|마이크로소프트.데스크톱 가상화/호스트풀|Error|Error|
|마이크로소프트.데스크톱 가상화/호스트풀|관리|관리|
|마이크로소프트.데스크톱 가상화/호스트풀|연결|연결|
|마이크로소프트.데스크톱 가상화/호스트풀|호스트 등록|호스트 등록|
|Microsoft.Devices/IotHubs|Connections|Connections|
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
|Microsoft.Devices/IotHubs|디바이스스트림|장치 스트림(미리 보기)|
|Microsoft.Devices/provisioningServices|DeviceOperations|디바이스 작업|
|Microsoft.Devices/provisioningServices|ServiceOperations|서비스 작업|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft.DocumentDB/databaseAccounts|파티션키통계|파티션키통계|
|Microsoft.DocumentDB/databaseAccounts|컨트롤플레인요청|컨트롤플레인요청|
|마이크로소프트.엔터프라이즈 지식 그래프/서비스|AuditEvent|감사이벤트 로그|
|마이크로소프트.엔터프라이즈 지식 그래프/서비스|데이터 문제|데이터 문제 로그|
|마이크로소프트.엔터프라이즈 지식 그래프/서비스|요청|구성 로그|
|Microsoft.EventHub/namespaces|ArchiveLogs|보관 로그|
|Microsoft.EventHub/namespaces|OperationalLogs|작업 로그|
|Microsoft.EventHub/namespaces|AutoScaleLogs|자동 크기 조정 로그|
|Microsoft.EventHub/namespaces|카프카 코디네이터로그|카프카 코디네이터 로그|
|Microsoft.EventHub/namespaces|카프카유저에이오류로그|카프카 사용자 오류 로그|
|Microsoft.EventHub/namespaces|이벤트허브V넷커넥션이벤트|VNet/IP 필터링 연결 로그|
|Microsoft.EventHub/namespaces|고객 관리키유저로그|고객 관리 키 로그|
|마이크로소프트.헬스케어아피/서비스|AuditLogs|감사 로그|
|Microsoft.Insights/AutoscaleSettings|AutoscaleEvaluations|자동 크기 조정 평가|
|Microsoft.Insights/AutoscaleSettings|AutoscaleScaleActions|자동 크기 조정 크기 조정 작업|
|Microsoft.IoTSpaces/Graph|추적|추적|
|Microsoft.IoTSpaces/Graph|작동|작동|
|Microsoft.IoTSpaces/Graph|감사|감사|
|Microsoft.IoTSpaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Microsoft.IoTSpaces/Graph|수신|수신|
|Microsoft.IoTSpaces/Graph|송신|송신|
|Microsoft.KeyVault/vaults|AuditEvent|감사 로그|
|Microsoft.Kusto/Clusters|성공|성공적인 인제스트 작업|
|Microsoft.Kusto/Clusters|실패|인제 작업 실패|
|Microsoft.Logic/workflows|WorkflowRuntime|워크플로 런타임 진단 이벤트|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|통합 계정 이벤트 추적|
|Microsoft.MachineLearningServices/workspaces|AmlCompute클러스터이벤트|AmlCompute클러스터이벤트|
|Microsoft.MachineLearningServices/workspaces|AmlCompute클러스터노드이벤트|AmlCompute클러스터노드이벤트|
|Microsoft.MachineLearningServices/workspaces|아믈컴테스잡이벤트|아믈컴테스잡이벤트|
|마이크로소프트.미디어/미디어 서비스|키 배달 요청|주요 배달 요청|
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
|마이크로소프트.네트워크/vpnGateways|GatewayDiagnosticLog|게이트웨이 진단 로그|
|마이크로소프트.네트워크/vpnGateways|TunnelDiagnosticLog|터널 진단 로그|
|마이크로소프트.네트워크/vpnGateways|RouteDiagnosticLog|경로 진단 로그|
|마이크로소프트.네트워크/vpnGateways|IKEDiagnosticLog|IKE 진단 로그|
|Microsoft.Network/frontdoors|FrontdoorAccessLog|Frontdoor 액세스 로그|
|Microsoft.Network/frontdoors|FrontdoorWebApplicationFirewallLog|Frontdoor 웹 애플리케이션 방화벽 로그|
|마이크로소프트.네트워크/p2sVpn게이트웨이|GatewayDiagnosticLog|게이트웨이 진단 로그|
|마이크로소프트.네트워크/p2sVpn게이트웨이|IKEDiagnosticLog|IKE 진단 로그|
|마이크로소프트.네트워크/p2sVpn게이트웨이|P2SDiagnosticLog|P2P 진단 로그|
|마이크로소프트.네트워크/요새호스트|바스티온감사로그|요새 감사 로그|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|부하 분산 장치 경고 이벤트|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|부하 분산 장치 프로브 상태|
|Microsoft.PowerBIDedicated/capacities|엔진|엔진|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Azure Backup 보고 데이터|
|Microsoft.RecoveryServices/Vaults|코어Azure백업|핵심 Azure 백업 데이터|
|Microsoft.RecoveryServices/Vaults|애드온Azure백업작업|애드온 Azure 백업 작업 데이터|
|Microsoft.RecoveryServices/Vaults|애드온Azure백업경고|애드온 Azure 백업 경고 데이터|
|Microsoft.RecoveryServices/Vaults|애드온Azure백업정책|애드온 Azure 백업 정책 데이터|
|Microsoft.RecoveryServices/Vaults|애드온Azure백업스토리지|애드온 Azure 백업 저장소 데이터|
|Microsoft.RecoveryServices/Vaults|애드온Azure백업보호인스턴스|애드온 Azure 백업 보호 인스턴스 데이터|
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
|Microsoft.Storage/storageAccounts/tableServices|스토리지 읽기|스토리지 읽기|
|Microsoft.Storage/storageAccounts/tableServices|스토리지쓰기|스토리지쓰기|
|Microsoft.Storage/storageAccounts/tableServices|스토리지 삭제|스토리지 삭제|
|Microsoft.Storage/storageAccounts/blobServices|스토리지 읽기|스토리지 읽기|
|Microsoft.Storage/storageAccounts/blobServices|스토리지쓰기|스토리지쓰기|
|Microsoft.Storage/storageAccounts/blobServices|스토리지 삭제|스토리지 삭제|
|Microsoft.Storage/storageAccounts/fileServices|스토리지 읽기|스토리지 읽기|
|Microsoft.Storage/storageAccounts/fileServices|스토리지쓰기|스토리지쓰기|
|Microsoft.Storage/storageAccounts/fileServices|스토리지 삭제|스토리지 삭제|
|Microsoft.Storage/storageAccounts/queueServices|스토리지 읽기|스토리지 읽기|
|Microsoft.Storage/storageAccounts/queueServices|스토리지쓰기|스토리지쓰기|
|Microsoft.Storage/storageAccounts/queueServices|스토리지 삭제|스토리지 삭제|
|Microsoft.StreamAnalytics/streamingjobs|실행|실행|
|Microsoft.StreamAnalytics/streamingjobs|작성|작성|
|마이크로소프트.웹/호스팅 환경|앱 서비스환경플랫폼로그|앱 서비스 환경 플랫폼 로그|
|microsoft.web/sites|함수 앱로그|함수 응용 프로그램 로그|
|microsoft.web/sites|앱서비스HTTP로그|HTTP 로그|
|microsoft.web/sites|앱서비스콘솔로그|앱 서비스 콘솔 로그|
|microsoft.web/sites|앱 서비스 앱로그|앱 서비스 애플리케이션 로그|
|microsoft.web/sites|앱서비스파일감사로그|사이트 콘텐츠 변경 감사 로그|
|microsoft.web/sites|앱서비스감사로그|감사 로그 액세스|
|microsoft.web/sites/slots|함수 앱로그|함수 응용 프로그램 로그|
|microsoft.web/sites/slots|앱서비스HTTP로그|HTTP 로그|
|microsoft.web/sites/slots|앱서비스콘솔로그|콘솔 로그|
|microsoft.web/sites/slots|앱 서비스 앱로그|애플리케이션 로그 전송 사용|
|microsoft.web/sites/slots|앱서비스파일감사로그|사이트 콘텐츠 변경 감사 로그|
|microsoft.web/sites/slots|앱서비스감사로그|감사 로그 액세스|

## <a name="next-steps"></a>다음 단계

* [리소스 로그에 대해 자세히 알아보기](../../azure-monitor/platform/platform-logs-overview.md)
* [**이벤트 허브로** 리소스 리소스 로그 스트리밍](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)
* [Azure 모니터 REST API를 사용하여 리소스 로그 진단 설정 변경](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [Azure Storage에서 Log Analytics를 사용하여 로그 분석](../../azure-monitor/platform/collect-azure-metrics-logs.md)
