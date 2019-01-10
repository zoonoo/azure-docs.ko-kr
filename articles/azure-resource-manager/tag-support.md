---
title: 리소스에 대한 Azure Resource Manager 태그 지원
description: 태그를 지원하는 Azure 리소스 종류를 보여 줍니다. 모든 Azure 서비스에 대한 세부 정보를 제공합니다.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 01/02/2019
ms.author: tomfitz
ms.openlocfilehash: 50ea7a2446b5560bd208b2da128fa877068ce452
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/03/2019
ms.locfileid: "54000294"
---
# <a name="tag-support-for-azure-resources"></a>Azure 리소스에 대한 태그 지원
이 문서에서는 리소스 종류가 [태그 지정](resource-group-using-tags.md)을 지원하는지 여부를 설명합니다.

## <a name="aad-domain-services"></a>AAD 도메인 서비스
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| domains | 아니요 | 

## <a name="ad-hybrid-health-service"></a>AD 하이브리드 상태 관리 서비스
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| addsservices | 아니요 |
| aadsupportcases | 아니요 | 
| agents | 아니요 | 
| anonymousapiusers | 아니요 | 
| 구성 | 아니요 | 
| 로그 | 아니요 | 
| reports | 아니요 | 
| services | 아니요 | 
| servicehealthmetrics | 아니요 | 

## <a name="aks"></a>AKS
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| managedClusters | 예 | 

## <a name="analysis-services"></a>Analysis Services
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| servers | 예 | 

## <a name="api-hubs"></a>API 허브
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| apiManagementAccounts | 아니요 | 
| apiManagementAccounts/apis | 아니요 | 
| apiManagementAccounts/connectionAcls | 아니요 | 
| apiManagementAccounts/connectionProviders | 아니요 | 
| apiManagementAccounts/connectionProviderAcls | 아니요 | 
| apiManagementAccounts/connections | 아니요 | 

## <a name="api-management"></a>API Management
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| 서비스 | 예 | 

## <a name="automation"></a>Automation
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| automationAccounts | 예 | 
| automationAccounts/configurations | 예 | 
| automationAccounts/jobs | 아니요 | 
| automationAccounts/runbooks | 예 | 
| automationAccounts/softwareUpdateConfigurations | 아니요 | 
| automationAccounts/webhooks | 아니요 | 

## <a name="azure-database-for-mariadb"></a>Azure Database for MariaDB
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| servers | 예 | 
| servers/configurations | 아니요 |
| servers/databases | 아니요 |
| servers/firewallRules | 아니요 |
| servers/recoverableServers | 아니요 | 
| servers/securityAlertPolicies | 아니요 |
| servers/virtualNetworkRules | 아니요 | 

## <a name="azure-database-for-mysql"></a>Azure Database for MySQL
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| servers | 예 | 
| servers/configurations | 아니요 |
| servers/databases | 아니요 |
| servers/firewallRules | 아니요 |
| servers/recoverableServers | 아니요 | 
| servers/securityAlertPolicies | 아니요 |
| servers/virtualNetworkRules | 아니요 | 

## <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| servers | 예 | 
| servers/advisors | 아니요 | 
| servers/configurations | 아니요 |
| servers/databases | 아니요 |
| servers/firewallRules | 아니요 |
| servers/queryTexts | 아니요 | 
| servers/recoverableServers | 아니요 | 
| servers/securityAlertPolicies | 아니요 |
| servers/topQueryStatistics | 아니요 | 
| servers/virtualNetworkRules | 아니요 | 
| servers/waitStatistics | 아니요 | 

## <a name="batch"></a>Batch
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| batchAccounts | 예 | 

## <a name="bing-maps"></a>Bing 지도
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| mapApis | 예 | 

## <a name="biztalk-services"></a>Biztalk Services
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| BizTalk | 예 | 

## <a name="cache"></a>캐시
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| Redis | 예 | 

## <a name="cdn"></a>CDN
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| edgenodes | 아니요 | 
| 프로필 | 예 | 
| profiles/endpoints | 예 | 
| profiles/endpoints/customdomains | 아니요 | 
| profiles/endpoints/origins | 아니요 | 
| validateProbe | 아니요 | 

## <a name="classic-compute"></a>클래식 컴퓨팅
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| domainNames | 아니요 | 
| domainNames/slots | 아니요 | 
| domainNames/slots/roles | 아니요 | 
| virtualMachines | 아니요 | 
| virtualMachines/diagnosticSettings | 아니요 | 
| virtualMachines/metricDefinitions | 아니요 | 
| virtualMachines/metrics | 아니요 | 

## <a name="classic-infrastructure-migrate"></a>클래식 인프라 마이그레이션
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| classicInfrastructureResources | 아니요 | 

## <a name="classic-network"></a>클래식 네트워크
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| virtualNetworks | 아니요 | 
| virtualNetworks/remoteVirtualNetworkPeeringProxies | 아니요 | 
| virtualNetworks/virtualNetworkPeerings | 아니요 | 

## <a name="classic-storage"></a>클래식 스토리지
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| storageAccounts/services | 아니요 | 
| storageAccounts/services/diagnosticSettings | 아니요 | 

## <a name="compute"></a>컴퓨팅
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| availabilitySets | 예 | 
| disks | 예 | 
| images | 예 | 
| restorePointCollections | 예 | 
| restorePointCollections/restorePoints | 아니요 | 
| sharedVMImages | 예 | 
| sharedVMImages/versions | 예 | 
| snapshots | 예 | 
| virtualMachines | 예 | 
| virtualMachines/diagnosticSettings | 아니요 | 
| virtualMachines/extensions | 예 | 
| virtualMachines/metricDefinitions | 아니요 | 
| virtualMachineScaleSets | 예 | 
| virtualMachineScaleSets/extensions | 아니요 | 
| virtualMachineScaleSets/networkInterfaces | 아니요 | 
| virtualMachineScaleSets/publicIPAddresses | 아니요 | 
| virtualMachineScaleSets/virtualMachines | 아니요 | 
| virtualMachineScaleSets/virtualMachines/networkInterfaces | 아니요 | 

## <a name="container"></a>컨테이너
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| containerGroups | 예 | 

## <a name="container-instance"></a>Container Instances
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| containerGroups | 예 | 
| serviceAssociationLinks | 아니요 | 

## <a name="container-registry"></a>Container Registry
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| registries | 예 | 
| registries/replications | 예 |
| registries/tasks | 예 |
| registries/webhooks | 예 |

## <a name="container-service"></a>컨테이너 서비스
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| containerServices | 예 | 

## <a name="cortana-analytics"></a>Cortana 분석
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| 계정 | 예 | 

## <a name="cosmos-db"></a>Cosmos DB
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| databaseAccounts | 예 | 
| databaseAccountNames | 아니요 | 

## <a name="cost-management"></a>Cost Management
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| 커넥터 | 예 | 

## <a name="data-box"></a>Data Box
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| jobs | 예 | 

## <a name="data-box-edge"></a>Data Box Edge
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| DataBoxEdgeDevices | 예 | 

## <a name="data-catalog"></a>Data Catalog
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| catalogs | 예 | 

## <a name="data-connect"></a>데이터 연결
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| connectionManagers | 예 | 

## <a name="data-factory"></a>Data Factory
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| dataFactories | 예 | 
| dataFactories/diagnosticSettings | 아니요 | 
| dataFactories/metricDefinitions | 아니요 | 
| dataFactorySchema | 아니요 | 
| factories | 예 | 
| factories/integrationRuntimes | 아니요 | 

## <a name="devices"></a>디바이스
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| IotHubs | 예 | 
| IotHubs/eventGridFilters | 아니요 | 
| ProvisioningServices | 예 | 

## <a name="devspaces"></a>Devspaces
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| controllers | 예 | 

## <a name="devtest-lab"></a>DevTest Lab
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| labs | 예 | 
| labs/artifactsources | 예 |
| labs/costs | 예 |
| labs/customimages | 예 |
| labs/formulas | 예 |
| labs/notificationchannels | 예 |
| labs/policysets/policies | 예 |
| labs/schedules | 예 |
| labs/serviceRunners | 예 | 
| labs/users | 예 |
| labs/users/disks | 예 |
| labs/users/environments | 예 |
| labs/users/secrets | 예 |
| labs/users/servicefabrics | 예 |
| labs/users/servicefabrics/schedules | 예 |
| labs/virtualMachines | 예 | 
| labs/virtualmachines/schedules | 예 |
| labs/virtualnetworks | 예 |
| schedules | 예 | 

## <a name="dynamics-lcs"></a>Dynamics LCS
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| lcsprojects | 아니요 | 
| lcsprojects/connectors | 아니요 | 
| lcsprojects/clouddeployments | 아니요 | 

## <a name="event-grid"></a>Event Grid
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| domains | 예 | 
| domains/topics | 아니요 | 
| eventSubscriptions | 아니요 | 
| extensionTopics | 아니요 | 
| topics | 예 | 
| topicTypes | 아니요 | 

## <a name="event-hub"></a>이벤트 허브
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| clusters | 예 | 
| namespaces | 예 | 
| namespaces/AuthorizationRules | 아니요 |
| namespaces/disasterRecoveryConfigs | 아니요 |
| namespaces/eventhubs | 아니요 |
| namespaces/eventhubs/authorizationRules | 아니요 |
| namespaces/eventhubs/consumergroups | 아니요 |

## <a name="hana-on-azure"></a>Azure의 HANA
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| hanaInstances | 예 | 

## <a name="hdinsight"></a>HDInsight
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| clusters | 예 | 
| clusters/applications | 아니요 | 

## <a name="import-export"></a>가져오기 및 내보내기
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| jobs | 예 | 

## <a name="insights"></a>자세한 정보
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| actionGroups | 예 |
| activityLogAlerts | 예 |
| alertrules | 예 |
| automatedExportSettings | 아니요 | 
| components | 예 | 
| components/events | 아니요 | 
| components/metrics | 아니요 | 
| components/pricingPlans | 아니요 | 
| components/query | 아니요 | 
| 로그 | 아니요 | 
| metricAlerts | 예 |
| migrateToNewPricingModel | 아니요 | 
| myWorkbooks | 아니요 | 
| 쿼리 | 아니요 | 
| rollbackToLegacyPricingModel | 아니요 | 
| scheduledqueryrules | 예 | 
| webtests | 예 | 
| workbooks | 예 | 

## <a name="key-vault"></a>Key Vault
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| deletedVaults | 아니요 | 
| vaults | 예 | 
| vaults/accessPolicies | 아니요 | 
| vaults/secrets | 아니요 | 

## <a name="log-analytics"></a>Log Analytics
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| 로그 | 아니요 | 

## <a name="logic"></a>논리
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| integrationAccounts | 예 | 
| workflows | 예 | 

## <a name="machine-learning-services"></a>Machine Learning Services
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| workspaces | 예 | 
| workspaces/computes | 아니요 | 

## <a name="managed-identity"></a>관리 ID
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| Identities | 아니요 | 
| userAssignedIdentities | 예 | 

## <a name="marketplace-apps"></a>Marketplace 앱
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| classicDevServices | 예 | 

## <a name="marketplace-ordering"></a>Marketplace 주문
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| agreements | 아니요 | 
| offertypes | 아니요 | 

## <a name="media"></a>미디어
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| mediaservices | 예 | 
| mediaservices/accountFilters | 아니요 | 
| mediaservices/assets | 아니요 | 
| mediaservices/assets/assetFilters | 아니요 | 
| mediaservices/contentKeyPolicies | 아니요 | 
| mediaservices/eventGridFilters | 아니요 | 
| mediaservices/liveEventOperations | 아니요 | 
| mediaservices/liveEvents | 예 | 
| mediaservices/liveEvents/liveOutputs | 아니요 | 
| mediaservices/liveOutputOperations | 아니요 | 
| mediaservices/streamingEndpoints | 예 | 
| mediaservices/streamingEndpointOperations | 아니요 | 
| mediaservices/streamingLocators | 아니요 | 
| mediaservices/streamingPolicies | 아니요 | 
| mediaservices/transforms | 아니요 | 
| mediaservices/transforms/jobs | 아니요 | 

## <a name="network"></a>네트워크
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| applicationGateways | 예 | 
| applicationSecurityGroups | 예 | 
| azureFirewalls | 예 | 
| connections | 예 | 
| ddosProtectionPlans | 예 | 
| expressRouteCircuits | 예 | 
| frontdoors | 예 | 
| frontdoorWebApplicationFirewallPolicies | 예 | 
| interfaceEndpoints | 예 | 
| loadBalancers | 예 | 
| localNetworkGateways | 예 | 
| networkIntentPolicies | 예 | 
| networkInterfaces | 예 | 
| networkProfiles | 예 | 
| networkSecurityGroups | 예 | 
| networkWatchers | 예 | 
| networkWatchers/connectionMonitors | 예 | 
| networkWatchers/lenses | 예 | 
| networkWatchers/pingMeshes | 예 | 
| privateLinkServices | 예 | 
| publicIPAddresses | 예 | 
| publicIPPrefixes | 예 | 
| routeFilters | 예 | 
| routeTables | 예 | 
| serviceEndpointPolicies | 예 | 
| virtualHubs | 예 | 
| virtualNetworks | 예 | 
| virtualNetworkGateways | 예 | 
| virtualNetworkTaps | 예 | 
| virtualWans | 예 | 
| vpnGateways | 예 | 
| vpnSites | 예 | 
| webApplicationFirewallPolicies | 예 | 

## <a name="notification-hubs"></a>Notification Hubs
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| namespaces | 예 | 
| namespaces/notificationHubs | 예 | 

## <a name="operational-insights"></a>Operational Insights
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| workspaces | 예 |
| workspaces/dataSources | 예 |
| workspaces/linkedServices | 예 |
| workspaces/savedSearches | 아니요 |
| workspaces/storageInsightConfigs | 예 |

## <a name="operations-management"></a>운영 관리
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| solutions | 아니요 |

## <a name="portal"></a>포털
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| dashboards | 예 | 

## <a name="portal-sdk"></a>포털 SDK
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| rootResources | 예 | 

## <a name="power-bi"></a>Power BI
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| workspaceCollections | 예 | 

## <a name="recovery-services"></a>Recovery Services
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| backupProtectedItems | 아니요 | 
| vaults | 예 | 

## <a name="relay"></a>릴레이
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| namespaces | 예 | 

## <a name="resources"></a>리소스
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| resourceGroups | 예 | 
| subscriptions/resourceGroups | 예 | 

## <a name="scheduler"></a>Scheduler
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| jobcollections | 예 | 
| flows | 예 | 

## <a name="search"></a>검색
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| resourceHealthMetadata | 아니요 | 
| searchServices | 예 | 

## <a name="security"></a>보안
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| dataCollectionAgents | 아니요 | 

## <a name="service-bus"></a>Service Bus
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| namespaces | 예 | 
| namespaces/eventgridfilters | 아니요 | 

## <a name="service-fabric"></a>Service Fabric
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| clusters | 예 | 
| clusters/applications | 아니요 | 

## <a name="service-fabric-mesh"></a>Service Fabric Mesh
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| 애플리케이션 | 예 | 
| networks | 예 | 
| volumes | 예 | 

## <a name="signalr-service"></a>SignalR Service
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| SignalR | 예 | 

## <a name="site-recovery"></a>Site Recovery
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| SiteRecoveryVault | 예 | 

## <a name="solutions"></a>솔루션
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| 애플리케이션 | 예 | 
| applicationDefinitions | 예 | 
| jitRequests | 예 | 

## <a name="sql"></a>SQL
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| locations/instanceFailoverGroups | 아니요 |
| managedInstances | 예 |
| managedInstances/databases | 예 |
| managedInstances/databases/backupShortTermRetentionPolicies | 아니요 |
| managedInstances/databases/schemas/tables/columns/sensitivityLabels | 아니요 |
| managedInstances/databases/vulnerabilityAssessments | 아니요 |
| managedInstances/databases/vulnerabilityAssessments/rules/baselines | 아니요 |
| managedInstances/encryptionProtector | 아니요 |
| managedInstances/keys | 아니요 |
| managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | 아니요 |
| managedInstances/vulnerabilityAssessments | 아니요 |
| servers | 예 |
| servers/administrators | 아니요 |
| servers/advisors | 아니요 |
| servers/auditingSettings | 아니요 |
| servers/backupLongTermRetentionVaults | 아니요 |
| servers/communicationLinks | 아니요 |
| servers/connectionPolicies | 아니요 |
| servers/databases | 예 |
| servers/databases/advisors | 아니요 |
| servers/databases/auditingSettings | 아니요 |
| servers/databases/backupLongTermRetentionPolicies | 아니요 |
| servers/databases/backupShortTermRetentionPolicies | 아니요 |
| servers/databases/connectionPolicies | 아니요 |
| servers/databases/dataMaskingPolicies | 아니요 |
| servers/databases/dataMaskingPolicies/rules | 아니요 |
| servers/databases/extendedAuditingSettings | 아니요 |
| servers/databases/extensions | 아니요 |
| servers/databases/geoBackupPolicies | 아니요 |
| servers/databases/schemas/tables/columns/sensitivityLabels | 아니요 |
| servers/databases/securityAlertPolicies | 아니요 |
| servers/databases/syncGroups | 아니요 |
| servers/databases/syncGroups/syncMembers | 아니요 |
| servers/databases/transparentDataEncryption | 아니요 |
| servers/databases/vulnerabilityAssessments | 아니요 |
| servers/databases/vulnerabilityAssessments/rules/baselines | 아니요 |
| servers/disasterRecoveryConfiguration | 아니요 |
| servers/dnsAliases | 아니요 |
| servers/elasticPools | 예 |
| servers/encryptionProtector | 아니요 |
| servers/extendedAuditingSettings | 아니요 |
| servers/failoverGroups | 예 |
| servers/firewallRules | 아니요 |
| servers/jobAgents | 예 |
| servers/jobAgents/credentials | 아니요 |
| servers/jobAgents/jobs | 아니요 |
| servers/jobAgents/jobs/executions | 아니요 |
| servers/jobAgents/jobs/steps | 아니요 |
| servers/jobAgents/targetGroups | 아니요 |
| servers/keys | 아니요 |
| servers/securityAlertPolicies | 아니요 |
| servers/syncAgents | 아니요 |
| servers/virtualNetworkRules | 아니요 |
| servers/vulnerabilityAssessments | 아니요 |

## <a name="sql-virtual-machine"></a>SQL 가상 머신
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| DWVM | 예 | 

## <a name="storage"></a>Storage
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| storageAccounts | 예 | 
| storageAccounts/blobServices | 아니요 | 
| storageAccounts/fileServices | 아니요 | 
| storageAccounts/queueServices | 아니요 | 
| storageAccounts/services | 아니요 | 
| storageAccounts/services/metricDefinitions | 아니요 | 
| storageAccounts/tableServices | 아니요 | 

## <a name="storage-sync"></a>스토리지 동기화
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| storageSyncServices | 예 | 
| storageSyncServices/registeredServers | 아니요 | 
| storageSyncServices/syncGroups | 아니요 | 
| storageSyncServices/syncGroups/cloudEndpoints | 아니요 | 
| storageSyncServices/syncGroups/serverEndpoints | 아니요 | 
| storageSyncServices/workflows | 아니요 | 

## <a name="storsimple"></a>Storsimple
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| managers | 예 | 

## <a name="stream-analytics"></a>Stream Analytics
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| streamingjobs | 예 | 
| streamingjobs/diagnosticSettings | 아니요 | 
| streamingjobs/metricDefinitions | 아니요 | 

## <a name="subscription"></a>구독
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| SubscriptionDefinitions | 아니요 | 
| SubscriptionOperations | 아니요 | 

## <a name="support"></a>지원
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| supporttickets | 아니요 | 

## <a name="visual-studio"></a>Visual Studio
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| 계정 | 예 | 
| account/extension | 예 | 
| account/project | 예 | 

## <a name="web"></a>웹
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| apiManagementAccounts | 아니요 | 
| apiManagementAccounts/apiAcls | 아니요 | 
| apiManagementAccounts/apis | 아니요 | 
| apiManagementAccounts/apis/apiAcls | 아니요 | 
| apiManagementAccounts/apis/connectionAcls | 아니요 | 
| apiManagementAccounts/apis/connections | 아니요 | 
| apiManagementAccounts/apis/connections/connectionAcls | 아니요 | 
| apiManagementAccounts/apis/localizedDefinitions | 아니요 | 
| apiManagementAccounts/connectionAcls | 아니요 | 
| apiManagementAccounts/connections | 아니요 | 
| billingMeters | 아니요 | 
| certificates | 예 | 
| connectionGateways | 예 | 
| connections | 예 | 
| customApis | 예 | 
| deletedSites | 아니요 | 
| functions | 아니요 | 
| hostingEnvironments | 예 | 
| hostingEnvironments/metrics | 아니요 | 
| hostingEnvironments/multiRolePools | 아니요 | 
| hostingEnvironments/workerPools | 아니요 | 
| publishingUsers | 아니요 | 
| serverFarms | 예 | 
| serverFarms/workers | 아니요 | 
| sites | 예 | 
| sites/domainOwnershipIdentifiers | 아니요 | 
| sites/hostNameBindings | 아니요 | 
| sites/instances | 아니요 | 
| sites/instances/extensions | 아니요 | 
| sites/metrics | 아니요 | 
| sites/premieraddons | 예 | 
| sites/slots | 예 | 
| sites/slots/hostNameBindings | 아니요 | 
| sites/slots/instances | 아니요 | 
| sites/slots/instances/extensions | 아니요 | 
| sites/slots/metrics | 아니요 | 
| sourceControls | 아니요 | 
| validate | 아니요 | 
| verifyHostingEnvironmentVnet | 아니요 | 

## <a name="xrm"></a>XRM
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| organizations | 아니요 | 

## <a name="next-steps"></a>다음 단계
리소스에 태그를 적용하는 방법을 알아보려면 [태그를 사용하여 Azure 리소스 구성](resource-group-using-tags.md)을 참조하세요.
