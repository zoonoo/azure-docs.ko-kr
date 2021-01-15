---
title: Azure Monitor 리소스 로그에서 지원 되는 서비스 및 범주
description: Azure 리소스 로그에 대해 지원 되는 서비스 및 이벤트 스키마를 이해 하는 Azure Monitor에 대 한 참조입니다.
ms.subservice: logs
ms.topic: reference
ms.date: 12/09/2020
ms.openlocfilehash: aeac069b4e9382867664a82af62e29e72da7585e
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2021
ms.locfileid: "98232250"
---
# <a name="supported-categories-for-azure-resource-logs"></a>Azure 리소스 로그에 대해 지원 되는 범주

> [!NOTE]
> 이전에는 리소스 로그를 진단 로그 라고 했습니다. 이 이름은 2019 년 10 월에 변경 되었으며 Azure 리소스를 포함 하도록 이동 Azure Monitor 이동 했습니다.

[Azure Monitor 리소스 로그](./platform-logs-overview.md) 는 해당 서비스 또는 리소스의 작업을 설명 하는 Azure 서비스에서 내보낸 로그입니다. Azure Monitor를 통해 제공 되는 모든 리소스 로그는 일반적인 최상위 스키마를 공유 하며 각 서비스는 고유한 이벤트의 고유한 속성을 내보낼 수 있는 유연성을 제공 합니다.

리소스 종류(`resourceId` 속성에 제공) 및 `category`가 조합되어 스키마를 고유하게 식별합니다. 서비스 관련 필드를 포함 하는 모든 리소스 로그에 대 한 공통 스키마가 있으며, 다른 로그 범주에 대해 추가 됩니다. 자세한 내용은 [Azure 리소스 로그의 공통 및 서비스별 스키마]() 를 참조 하세요.


## <a name="costs"></a>비용

Log Analytics, Azure Storage 및/또는 이벤트 허브에 데이터를 보내고 저장 하는 것과 관련 된 비용이 있습니다. 이러한 위치에 데이터를 보관 하 고 유지 하기 위해 비용을 지불할 수 있습니다.  리소스 로그는 이러한 위치에 보낼 수 있는 데이터의 한 가지 유형입니다. [리소스 로그의 일부 범주](https://azure.microsoft.com/pricing/details/monitor/) 를 이러한 위치로 내보내기 위한 추가 비용이 있으며, 다른 일부는 내보내기 비용이 없습니다. 내보내기 비용 세부 정보는 아래 표에 나열 되어 있습니다.

## <a name="supported-log-categories-per-resource-type"></a>각 리소스 유형별 지원되는 로그 범주

다음은 각 리소스 종류에 사용할 수 있는 로그의 유형 목록입니다. 

일부 범주는 특정 유형의 리소스에 대해서만 지원 될 수 있습니다. 리소스가 누락 된 것으로 생각 되는 경우 리소스 관련 설명서를 참조 하세요. 예를 들어, 모든 유형의 데이터베이스에는 Microsoft .Sql/servers/databases 범주를 사용할 수 없습니다. 자세한 내용은 [SQL Database 진단 로깅에 대 한 정보](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md)를 참조 하세요. 

그래도 문제가 없는 경우이 문서의 맨 아래에 있는 GitHub 주석을 열 수 있습니다.
## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|엔진|엔진|
|서비스|서비스|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|GatewayLogs|ApiManagement 게이트웨이 관련 로그|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|ApplicationConsole|응용 프로그램 콘솔|
|SystemLogs|시스템 로그|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|DscNodeStatus|디스크 노드 상태|
|JobLogs|작업 로그|
|JobStreams|작업 스트림|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|ServiceLog|서비스 로그|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/workspaces

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|BaiClusterEvent|BaiClusterEvent|
|BaiClusterNodeEvent|BaiClusterNodeEvent|
|BaiJobEvent|BaiJobEvent|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|BlockchainApplication|블록 체인 응용 프로그램|
|FabricOrderer|패브릭 Orderer|
|FabricPeer|패브릭 피어|
|Proxy (프록시)|Proxy (프록시)|


## <a name="microsoftblockchaincordamembers"></a>Microsoft Blockchain/cordaMembers

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|BlockchainApplication|블록 체인 응용 프로그램|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft Cdn/cdnwebapplicationfirewallpolicies

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|WebApplicationFirewallLogs|웹 응용 프로그램 방화벽 로그|


## <a name="microsoftcdnprofiles"></a>Microsoft.Cdn/profiles

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|AzureCdnAccessLog|Azure Cdn 액세스 로그|


## <a name="microsoftcdnprofilesendpoints"></a>Microsoft.Cdn/profiles/endpoints

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|CoreAnalytics|엔드포인트의 메트릭(예: 대역폭, 송신 등)을 가져옵니다.|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Microsoft.ClassicNetwork/networksecuritygroups

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|네트워크 보안 그룹 규칙 흐름 이벤트|네트워크 보안 그룹 규칙 흐름 이벤트|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|감사|감사 로그|
|RequestResponse|요청 및 응답 로그|
|추적|추적 로그|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|ContainerRegistryLoginEvents|로그인 이벤트|
|ContainerRegistryRepositoryEvents|RepositoryEvent 로그|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|cluster-autoscaler|Kubernetes 클러스터 자동 크기 조정기|
|kube-apiserver|Kubernetes API 서버|
|kube-감사|Kubernetes 감사|
|kube-controller-manager|Kubernetes 컨트롤러 관리자|
|kube-scheduler|Kubernetes 스케줄러|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft CustomProviders/resourceproviders

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|AuditLogs|MiniRP 호출에 대 한 감사 로그|


## <a name="microsoftdatabricksworkspaces"></a>Databricks/작업 영역

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|계정|Databricks 계정|
|clusters|Databricks 클러스터|
|dbfs|Databricks 파일 시스템|
|instancePools|인스턴스 풀|
|jobs|Databricks 작업|
|Notebook|Databricks Notebook|
|secrets|Databricks 비밀|
|sqlPermissions|Databricks SQLPermissions|
|ssh|Databricks SSH|
|작업 영역|Databricks 작업 영역|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|ActivityRuns|파이프라인 작업 실행 로그|
|PipelineRuns|파이프라인 실행 로그|
|TriggerRuns|트리거 실행 로그|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|감사|감사 로그|
|요청|요청 로그|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/accounts

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|ReceivedShareSnapshots|받은 공유 스냅숏|
|SentShareSnapshots|전송 공유 스냅숏|
|공유|공유|
|ShareSubscriptions|구독 공유|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|MySqlAuditLogs|MariaDB 감사 로그|
|MySqlSlowLogs|MariaDB 서버 로그|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft.DBforMySQL/flexibleServers

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|MySqlAuditLogs|MySQL 감사 로그|
|MySqlSlowLogs|MySQL 저속 로그|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|MySqlAuditLogs|MySQL 감사 로그|
|MySqlSlowLogs|MySQL Server 로그|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft.DBforPostgreSQL/flexibleServers

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|PostgreSQLLogs|PostgreSQL 서버 로그|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|PostgreSQLLogs|PostgreSQL 서버 로그|
|QueryStoreRuntimeStatistics|PostgreSQL 쿼리 저장소 런타임 통계|
|QueryStoreWaitStatistics|PostgreSQL 쿼리 저장소 대기 통계|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|PostgreSQLLogs|PostgreSQL 서버 로그|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Microsoft DesktopVirtualization/applicationgroups

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|검사점|검사점|
|Error|Error|
|관리|관리|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Microsoft DesktopVirtualization/hostpools

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|검사점|검사점|
|연결|연결|
|Error|Error|
|HostRegistration|HostRegistration|
|관리|관리|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Microsoft DesktopVirtualization/작업 영역

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|검사점|검사점|
|Error|Error|
|피드|피드|
|관리|관리|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|C2DCommands|C2D 명령|
|C2DTwinOperations|C2D 쌍 작업|
|구성|구성|
|Connections|Connections|
|D2CTwinOperations|D2CTwinOperations|
|DeviceIdentityOperations|디바이스 ID 작업|
|DeviceStreams|장치 스트림 (미리 보기)|
|DeviceTelemetry|디바이스 원격 분석|
|DirectMethods|직접 메서드|
|DistributedTracing|분산 추적(미리 보기)|
|FileUploadOperations|파일 업로드 작업|
|JobsOperations|작업 연산|
|경로|경로|
|TwinQueries|쌍 쿼리|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|DeviceOperations|디바이스 작업|
|ServiceOperations|서비스 작업|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|CassandraRequests|CassandraRequests|
|ControlPlaneRequests|ControlPlaneRequests|
|DataPlaneRequests|DataPlaneRequests|
|GremlinRequests|GremlinRequests|
|MongoRequests|MongoRequests|
|PartitionKeyRUConsumption|PartitionKeyRUConsumption|
|PartitionKeyStatistics|PartitionKeyStatistics|
|QueryRuntimeStatistics|QueryRuntimeStatistics|


## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|DeliveryFailures|배달 실패 로그|
|오류가 발생 했습니다.|실패 로그 게시|


## <a name="microsofteventgridsystemtopics"></a>Microsoft EventGrid/systemTopics

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|DeliveryFailures|배달 실패 로그|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|DeliveryFailures|배달 실패 로그|
|오류가 발생 했습니다.|실패 로그 게시|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|ArchiveLogs|보관 로그|
|AutoScaleLogs|자동 크기 조정 로그|
|CustomerManagedKeyUserLogs|고객 관리 키 로그|
|EventHubVNetConnectionEvent|VNet/IP 필터링 연결 로그|
|KafkaCoordinatorLogs|Kafka 코디네이터 로그|
|KafkaUserErrorLogs|Kafka 사용자 오류 로그|
|OperationalLogs|작업 로그|


## <a name="microsofthealthcareapisservices"></a>Microsoft.HealthcareApis/services

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|AuditLogs|감사 로그|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|AutoscaleEvaluations|자동 크기 조정 평가|
|AutoscaleScaleActions|자동 크기 조정 크기 조정 작업|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|AppAvailabilityResults|가용성 결과|
|AppBrowserTimings|브라우저 타이밍|
|AppDependencies|종속성|
|AppEvents|이벤트|
|AppExceptions|예외|
|AppMetrics|메트릭|
|AppPageViews|페이지 보기|
|AppPerformanceCounters|성능 카운터|
|AppRequests|요청|
|AppSystemEvents|시스템 이벤트|
|AppTraces|추적|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|AuditEvent|감사 로그|


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|명령|명령|
|FailedIngestion|실패 한 수집 작업|
|IngestionBatching|수집 일괄 처리|
|쿼리|쿼리|
|SucceededIngestion|수집 작업 성공|
|TableDetails|테이블 세부 정보|
|TableUsageStatistics|테이블 사용 통계|


## <a name="microsoftlogicintegrationaccounts"></a>Microsoft.Logic/integrationAccounts

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|IntegrationAccountTrackingEvents|통합 계정 이벤트 추적|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|WorkflowRuntime|워크플로 런타임 진단 이벤트|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|AmlComputeClusterEvent|AmlComputeClusterEvent|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|
|AmlComputeJobEvent|AmlComputeJobEvent|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|KeyDeliveryRequests|키 배달 요청|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|ApplicationGatewayAccessLog|Application Gateway 액세스 로그|
|ApplicationGatewayFirewallLog|Application Gateway 방화벽 로그|
|ApplicationGatewayPerformanceLog|Application Gateway 성능 로그|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|AzureFirewallApplicationRule|Azure Firewall 애플리케이션 규칙|
|AzureFirewallNetworkRule|Azure Firewall 네트워크 규칙|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft.Network/bastionHosts

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|BastionAuditLogs|요새 감사 로그|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|PeeringRouteLog|피어링 경로 테이블 로그|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|FrontdoorAccessLog|Frontdoor 액세스 로그|
|FrontdoorWebApplicationFirewallLog|Frontdoor 웹 애플리케이션 방화벽 로그|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|LoadBalancerAlertEvent|부하 분산 장치 경고 이벤트|
|LoadBalancerProbeHealthStatus|부하 분산 장치 프로브 상태|


## <a name="microsoftnetworknetworksecuritygroups"></a>Microsoft.Network/networksecuritygroups

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|NetworkSecurityGroupEvent|네트워크 보안 그룹 이벤트|
|NetworkSecurityGroupFlowEvent|네트워크 보안 그룹 규칙 흐름 이벤트|
|NetworkSecurityGroupRuleCounter|네트워크 보안 그룹 규칙 카운터|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|DDoSMitigationFlowLogs|DDoS 완화 결정의 흐름 로그|
|DDoSMitigationReports|DDoS 완화의 보고서|
|DDoSProtectionNotifications|DDoS 보호 알림|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|ProbeHealthStatusEvents|Traffic Manager 프로브 상태 결과 이벤트|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|GatewayDiagnosticLog|게이트웨이 진단 로그|
|IKEDiagnosticLog|IKE 진단 로그|
|P2SDiagnosticLog|P2P 진단 로그|
|RouteDiagnosticLog|경로 진단 로그|
|TunnelDiagnosticLog|터널 진단 로그|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|VMProtectionAlerts|VM 보호 경고|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|엔진|엔진|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft.RecoveryServices/Vaults

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|AddonAzureBackupAlerts|추가 기능 Azure Backup 경고 데이터|
|AddonAzureBackupJobs|Addon Azure Backup 작업 데이터|
|AddonAzureBackupPolicy|추가 기능 Azure Backup 정책 데이터|
|AddonAzureBackupProtectedInstance|Addon Azure Backup 보호 된 인스턴스 데이터|
|AddonAzureBackupStorage|추가 기능 Azure Backup 저장소 데이터|
|AzureBackupReport|Azure Backup 보고 데이터|
|AzureSiteRecoveryEvents|Azure Site Recovery 이벤트|
|AzureSiteRecoveryJobs|Azure Site Recovery 작업|
|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery 보호된 디스크 데이터 변동|
|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery 복구 지점|
|AzureSiteRecoveryReplicatedItems|Azure Site Recovery 복제된 항목|
|AzureSiteRecoveryReplicationDataUploadRate|Azure Site Recovery 복제 데이터 업로드 속도|
|AzureSiteRecoveryReplicationStats|Azure Site Recovery 복제 통계|
|CoreAzureBackup|핵심 Azure Backup 데이터|


## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|HybridConnectionsEvent|HybridConnections 이벤트|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|OperationLogs|작업 로그|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|OperationalLogs|작업 로그|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|Alllogs.csv|Azure SignalR Service 로그.|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|DevOpsOperationsAudit|Devops 작업 감사 로그|
|ResourceUsageStats|리소스 사용량 통계|
|SQLSecurityAuditEvents|SQL 보안 감사 이벤트|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft.Sql/managedInstances/databases

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|오류|오류|
|QueryStoreRuntimeStatistics|쿼리 저장소 런타임 통계|
|QueryStoreWaitStatistics|쿼리 저장소 대기 통계|
|SQLInsights|SQL 정보|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|AutomaticTuning|자동 조정|
|블록|블록|
|DatabaseWaitStatistics|데이터베이스 대기 통계|
|교착 상태|교착 상태|
|DevOpsOperationsAudit|Devops 작업 감사 로그|
|DmsWorkers|DMS 작업자|
|오류|오류|
|ExecRequests|실행 요청|
|QueryStoreRuntimeStatistics|쿼리 저장소 런타임 통계|
|QueryStoreWaitStatistics|쿼리 저장소 대기 통계|
|RequestSteps|요청 단계|
|SQLInsights|SQL 정보|
|SqlRequests|SQL 요청|
|SQLSecurityAuditEvents|SQL 보안 감사 이벤트|
|시간 제한|시간 제한|
|대기|대기|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

내보내기 비용: [Azure Monitor 가격 책정 페이지](https://azure.microsoft.com/pricing/details/monitor/) 의 플랫폼 로그 섹션에 설명 된 대로 지불 합니다. 

|범주 |범주 표시 이름|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

내보내기 비용: [Azure Monitor 가격 책정 페이지](https://azure.microsoft.com/pricing/details/monitor/) 의 플랫폼 로그 섹션에 설명 된 대로 지불 합니다. 

|범주 |범주 표시 이름|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

내보내기 비용: [Azure Monitor 가격 책정 페이지](https://azure.microsoft.com/pricing/details/monitor/) 의 플랫폼 로그 섹션에 설명 된 대로 지불 합니다. 
 
|범주 |범주 표시 이름|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

내보내기 비용: [Azure Monitor 가격 책정 페이지](https://azure.microsoft.com/pricing/details/monitor/) 의 플랫폼 로그 섹션에 설명 된 대로 지불 합니다. 
 
|범주 |범주 표시 이름|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|작성|작성|
|실행|실행|


## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/workspaces

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|BuiltinSqlReqsEnded|기본 제공 Sql 풀 요청이 종료 되었습니다.|
|게이트웨이 Apirequests|Synapse Gateway Api 요청|
|SQLSecurityAuditEvents|SQL 보안 감사 이벤트|
|SynapseRbacOperations|Synapse RBAC 작업|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Synapse/작업 영역/bigDataPools

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|BigDataPoolAppsEnded|빅 데이터 풀 응용 프로그램이 종료 됨|


## <a name="microsoftsynapseworkspacessqlpools"></a>Synapse/workspaces/sqlPools

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|DmsWorkers|DMS 작업자|
|ExecRequests|실행 요청|
|RequestSteps|요청 단계|
|SqlRequests|SQL 요청|
|SQLSecurityAuditEvents|Sql 보안 감사 이벤트|
|대기|대기|


## <a name="microsoftwebhostingenvironments"></a>microsoft.web/hostingenvironments

내보내기 비용: 무료 

|범주 |범주 표시 이름|
|---|---|
|Appservice환경 Platformlogs|App Service Environment 플랫폼 로그|


## <a name="microsoftwebsites"></a>microsoft.web/sites

내보내기 비용: 무료 


|범주 |범주 표시 이름|
|---|---|
|AppServiceAppLogs|응용 프로그램 로그 App Service|
|AppServiceAuditLogs|감사 로그 액세스|
|AppServiceConsoleLogs|App Service 콘솔 로그|
|AppServiceFileAuditLogs|사이트 콘텐츠 변경 감사 로그|
|AppServiceHTTPLogs|HTTP 로그|
|FunctionAppLogs|함수 응용 프로그램 로그|


## <a name="microsoftwebsitesslots"></a>microsoft.web/sites/slots

내보내기 비용: 무료 


|범주 |범주 표시 이름|
|---|---|
|AppServiceAppLogs|응용 프로그램 로그 App Service|
|AppServiceAuditLogs|감사 로그 액세스|
|AppServiceConsoleLogs|App Service 콘솔 로그|
|AppServiceFileAuditLogs|사이트 콘텐츠 변경 감사 로그|
|AppServiceHTTPLogs|HTTP 로그|
|FunctionAppLogs|함수 응용 프로그램 로그|


## <a name="next-steps"></a>다음 단계

* [리소스 로그에 대해 자세히 알아보기](./platform-logs-overview.md)
* [**Event Hubs** 에 대 한 리소스 리소스 로그 스트림](./resource-logs.md#send-to-azure-event-hubs)
* [Azure Monitor REST API를 사용 하 여 리소스 로그 진단 설정 변경](/rest/api/monitor/diagnosticsettings)
* [Azure Storage에서 Log Analytics를 사용하여 로그 분석](./resource-logs.md#send-to-log-analytics-workspace)

