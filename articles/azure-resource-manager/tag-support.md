---
title: 리소스에 대한 Azure Resource Manager 태그 지원
description: 태그를 지원하는 Azure 리소스 종류를 보여 줍니다. 모든 Azure 서비스에 대한 세부 정보를 제공합니다.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 11/20/2018
ms.author: tomfitz
ms.openlocfilehash: 580955d3c6fd7a33c152e49e601d8078eb169a22
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53409669"
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
| services | 아니요 | 
| addsservices | 아니요 | 
| 구성 | 아니요 | 
| agents | 아니요 | 
| aadsupportcases | 아니요 | 
| reports | 아니요 | 
| servicehealthmetrics | 아니요 | 
| 로그 | 아니요 | 
| anonymousapiusers | 아니요 | 

## <a name="analysis-services"></a>Analysis Services
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| servers | yes | 

## <a name="api-hubs"></a>API 허브
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| apiManagementAccounts | 아니요 | 
| apiManagementAccounts/connectionProviders | 아니요 | 
| apiManagementAccounts/connections | 아니요 | 
| apiManagementAccounts/connectionAcls | 아니요 | 
| apiManagementAccounts/connectionProviderAcls | 아니요 | 
| apiManagementAccounts/apis | 아니요 | 

## <a name="api-management"></a>API Management
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| 서비스 | yes | 

## <a name="automation"></a>Automation
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| automationAccounts | yes | 
| automationAccounts/runbooks | yes | 
| automationAccounts/configurations | yes | 
| automationAccounts/webhooks | 아니요 | 
| automationAccounts/softwareUpdateConfigurations | 아니요 | 
| automationAccounts/jobs | 아니요 | 

## <a name="batch"></a>Batch
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| batchAccounts | yes | 

## <a name="bing-maps"></a>Bing 지도
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| mapApis | yes | 

## <a name="biztalk-services"></a>Biztalk Services
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| BizTalk | yes | 

## <a name="cache"></a>캐시
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| Redis | yes | 

## <a name="cdn"></a>CDN
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| 프로필 | yes | 
| profiles/endpoints | yes | 
| profiles/endpoints/origins | 아니요 | 
| profiles/endpoints/customdomains | 아니요 | 
| validateProbe | 아니요 | 
| edgenodes | 아니요 | 

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
| virtualNetworks/virtualNetworkPeerings | 아니요 | 
| virtualNetworks/remoteVirtualNetworkPeeringProxies | 아니요 | 

## <a name="classic-storage"></a>클래식 스토리지
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| storageAccounts/services | 아니요 | 
| storageAccounts/services/diagnosticSettings | 아니요 | 

## <a name="compute"></a>컴퓨팅
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| availabilitySets | yes | 
| virtualMachines | yes | 
| virtualMachines/extensions | yes | 
| virtualMachineScaleSets | yes | 
| virtualMachineScaleSets/extensions | 아니요 | 
| virtualMachineScaleSets/virtualMachines | 아니요 | 
| virtualMachineScaleSets/networkInterfaces | 아니요 | 
| virtualMachineScaleSets/virtualMachines/networkInterfaces | 아니요 | 
| virtualMachineScaleSets/publicIPAddresses | 아니요 | 
| restorePointCollections | yes | 
| restorePointCollections/restorePoints | 아니요 | 
| virtualMachines/diagnosticSettings | 아니요 | 
| virtualMachines/metricDefinitions | 아니요 | 
| sharedVMImages | yes | 
| sharedVMImages/versions | yes | 
| disks | yes | 
| snapshots | yes | 
| images | yes | 

## <a name="container"></a>컨테이너
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| containerGroups | yes | 

## <a name="container-instance"></a>Container Instances
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| containerGroups | yes | 
| serviceAssociationLinks | 아니요 | 

## <a name="container-service"></a>컨테이너 서비스
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| containerServices | yes | 

## <a name="cortana-analytics"></a>Cortana 분석
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| 계정 | yes | 

## <a name="cosmos-db"></a>Cosmos DB
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| databaseAccounts | yes | 
| databaseAccountNames | 아니요 | 

## <a name="cost-management"></a>Cost Management
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| 커넥터 | yes | 

## <a name="data-box-edge"></a>Data Box Edge
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| DataBoxEdgeDevices | yes | 

## <a name="data-catalog"></a>Data Catalog
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| catalogs | yes | 

## <a name="data-connect"></a>데이터 연결
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| connectionManagers | yes | 

## <a name="data-factory"></a>Data Factory
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| dataFactories | yes | 
| factories | yes | 
| factories/integrationRuntimes | 아니요 | 
| dataFactories/diagnosticSettings | 아니요 | 
| dataFactories/metricDefinitions | 아니요 | 
| dataFactorySchema | 아니요 | 

## <a name="devices"></a>디바이스
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| IotHubs | yes | 
| IotHubs/eventGridFilters | 아니요 | 
| ProvisioningServices | yes | 

## <a name="devspaces"></a>Devspaces
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| controllers | yes | 

## <a name="devtest-lab"></a>DevTest Lab
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| labs | yes | 
| schedules | yes | 
| labs/virtualMachines | yes | 
| labs/serviceRunners | yes | 

## <a name="dynamics-lcs"></a>Dynamics LCS
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| lcsprojects | 아니요 | 
| lcsprojects/connectors | 아니요 | 
| lcsprojects/clouddeployments | 아니요 | 

## <a name="event-grid"></a>Event Grid
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| eventSubscriptions | 아니요 | 
| topics | yes | 
| domains | yes | 
| domains/topics | 아니요 | 
| topicTypes | 아니요 | 
| extensionTopics | 아니요 | 

## <a name="event-hub"></a>이벤트 허브
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| namespaces | yes | 
| clusters | yes | 

## <a name="hana-on-azure"></a>Azure의 HANA
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| hanaInstances | yes | 

## <a name="hdinsight"></a>HDInsight
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| clusters | yes | 
| clusters/applications | 아니요 | 

## <a name="import-export"></a>가져오기 및 내보내기
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| jobs | yes | 

## <a name="insights"></a>자세한 정보
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| components | yes | 
| components/query | 아니요 | 
| components/metrics | 아니요 | 
| components/events | 아니요 | 
| webtests | yes | 
| 쿼리 | 아니요 | 
| scheduledqueryrules | yes | 
| components/pricingPlans | 아니요 | 
| migrateToNewPricingModel | 아니요 | 
| rollbackToLegacyPricingModel | 아니요 | 
| automatedExportSettings | 아니요 | 
| workbooks | yes | 
| myWorkbooks | 아니요 | 
| 로그 | 아니요 | 

## <a name="key-vault"></a>Key Vault
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| vaults | yes | 
| vaults/secrets | 아니요 | 
| vaults/accessPolicies | 아니요 | 
| deletedVaults | 아니요 | 

## <a name="log-analytics"></a>Log Analytics
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| 로그 | 아니요 | 

## <a name="logic"></a>논리
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| workflows | yes | 
| integrationAccounts | yes | 

## <a name="machine-learning-services"></a>Machine Learning Services
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| workspaces | yes | 
| workspaces/computes | 아니요 | 

## <a name="managed-identity"></a>관리 ID
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| Identities | 아니요 | 
| userAssignedIdentities | yes | 

## <a name="mariadb"></a>MariaDB
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| servers | yes | 
| servers/recoverableServers | 아니요 | 
| servers/virtualNetworkRules | 아니요 | 

## <a name="marketplace-apps"></a>Marketplace 앱
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| classicDevServices | yes | 

## <a name="marketplace-ordering"></a>Marketplace 주문
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| agreements | 아니요 | 
| offertypes | 아니요 | 

## <a name="media"></a>미디어
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| mediaservices | yes | 
| mediaservices/assets | 아니요 | 
| mediaservices/contentKeyPolicies | 아니요 | 
| mediaservices/streamingLocators | 아니요 | 
| mediaservices/streamingPolicies | 아니요 | 
| mediaservices/eventGridFilters | 아니요 | 
| mediaservices/transforms | 아니요 | 
| mediaservices/transforms/jobs | 아니요 | 
| mediaservices/streamingEndpoints | yes | 
| mediaservices/liveEvents | yes | 
| mediaservices/liveEvents/liveOutputs | 아니요 | 
| mediaservices/streamingEndpointOperations | 아니요 | 
| mediaservices/liveEventOperations | 아니요 | 
| mediaservices/liveOutputOperations | 아니요 | 
| mediaservices/assets/assetFilters | 아니요 | 
| mediaservices/accountFilters | 아니요 | 

## <a name="mysql"></a>MySQL
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| servers | yes | 
| servers/recoverableServers | 아니요 | 
| servers/virtualNetworkRules | 아니요 | 

## <a name="network"></a>네트워크
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| virtualNetworks | yes | 
| publicIPAddresses | yes | 
| networkInterfaces | yes | 
| interfaceEndpoints | yes | 
| loadBalancers | yes | 
| networkSecurityGroups | yes | 
| applicationSecurityGroups | yes | 
| serviceEndpointPolicies | yes | 
| networkIntentPolicies | yes | 
| routeTables | yes | 
| publicIPPrefixes | yes | 
| networkWatchers | yes | 
| networkWatchers/connectionMonitors | yes | 
| networkWatchers/lenses | yes | 
| networkWatchers/pingMeshes | yes | 
| virtualNetworkGateways | yes | 
| localNetworkGateways | yes | 
| connections | yes | 
| applicationGateways | yes | 
| expressRouteCircuits | yes | 
| routeFilters | yes | 
| virtualWans | yes | 
| vpnSites | yes | 
| virtualHubs | yes | 
| vpnGateways | yes | 
| azureFirewalls | yes | 
| virtualNetworkTaps | yes | 
| privateLinkServices | yes | 
| ddosProtectionPlans | yes | 
| networkProfiles | yes | 
| frontdoors | yes | 
| frontdoorWebApplicationFirewallPolicies | yes | 
| webApplicationFirewallPolicies | yes | 

## <a name="notification-hubs"></a>Notification Hubs
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| namespaces | yes | 
| namespaces/notificationHubs | yes | 

## <a name="portal"></a>포털
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| dashboards | yes | 

## <a name="portal-sdk"></a>포털 SDK
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| rootResources | yes | 

## <a name="postgresql"></a>PostgreSQL
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| servers | yes | 
| servers/recoverableServers | 아니요 | 
| servers/virtualNetworkRules | 아니요 | 
| servers/topQueryStatistics | 아니요 | 
| servers/queryTexts | 아니요 | 
| servers/waitStatistics | 아니요 | 
| servers/advisors | 아니요 | 

## <a name="power-bi"></a>Power BI
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| workspaceCollections | yes | 

## <a name="recovery-services"></a>Recovery Services
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| vaults | yes | 
| backupProtectedItems | 아니요 | 

## <a name="relay"></a>릴레이
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| namespaces | yes | 

## <a name="resources"></a>리소스
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| resourceGroups | yes | 
| subscriptions/resourceGroups | yes | 

## <a name="scheduler"></a>Scheduler
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| jobcollections | yes | 
| flows | yes | 

## <a name="search"></a>검색
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| searchServices | yes | 
| resourceHealthMetadata | 아니요 | 

## <a name="security"></a>보안
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| dataCollectionAgents | 아니요 | 

## <a name="service-bus"></a>Service Bus
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| namespaces | yes | 
| namespaces/eventgridfilters | 아니요 | 

## <a name="service-fabric"></a>Service Fabric
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| clusters | yes | 
| clusters/applications | 아니요 | 

## <a name="service-fabric-mesh"></a>Service Fabric Mesh
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| 응용 프로그램 | yes | 
| networks | yes | 
| volumes | yes | 

## <a name="signalr-service"></a>SignalR Service
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| SignalR | yes | 

## <a name="site-recovery"></a>Site Recovery
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| SiteRecoveryVault | yes | 

## <a name="solutions"></a>솔루션
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| 응용 프로그램 | yes | 
| applicationDefinitions | yes | 
| jitRequests | yes | 

## <a name="sql-virtual-machine"></a>SQL 가상 머신
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| DWVM | yes | 

## <a name="storage"></a>Storage
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| storageAccounts | yes | 
| storageAccounts/blobServices | 아니요 | 
| storageAccounts/tableServices | 아니요 | 
| storageAccounts/queueServices | 아니요 | 
| storageAccounts/fileServices | 아니요 | 
| storageAccounts/services | 아니요 | 
| storageAccounts/services/metricDefinitions | 아니요 | 

## <a name="storage-sync"></a>스토리지 동기화
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| storageSyncServices | yes | 
| storageSyncServices/syncGroups | 아니요 | 
| storageSyncServices/syncGroups/cloudEndpoints | 아니요 | 
| storageSyncServices/syncGroups/serverEndpoints | 아니요 | 
| storageSyncServices/registeredServers | 아니요 | 
| storageSyncServices/workflows | 아니요 | 

## <a name="storsimple"></a>Storsimple
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| managers | yes | 

## <a name="stream-analytics"></a>Stream Analytics
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| streamingjobs | yes | 
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
| 계정 | yes | 
| account/project | yes | 
| account/extension | yes | 
| 계정 | yes | 
| account/project | yes | 
| account/extension | yes | 

## <a name="web"></a>웹
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| sites/instances | 아니요 | 
| sites/slots/instances | 아니요 | 
| sites/instances/extensions | 아니요 | 
| sites/slots/instances/extensions | 아니요 | 
| publishingUsers | 아니요 | 
| validate | 아니요 | 
| sourceControls | 아니요 | 
| sites/hostNameBindings | 아니요 | 
| sites/domainOwnershipIdentifiers | 아니요 | 
| sites/slots/hostNameBindings | 아니요 | 
| certificates | yes | 
| serverFarms | yes | 
| serverFarms/workers | 아니요 | 
| sites | yes | 
| sites/slots | yes | 
| sites/metrics | 아니요 | 
| sites/slots/metrics | 아니요 | 
| sites/premieraddons | yes | 
| hostingEnvironments | yes | 
| hostingEnvironments/multiRolePools | 아니요 | 
| hostingEnvironments/workerPools | 아니요 | 
| hostingEnvironments/metrics | 아니요 | 
| functions | 아니요 | 
| deletedSites | 아니요 | 
| apiManagementAccounts | 아니요 | 
| apiManagementAccounts/connections | 아니요 | 
| apiManagementAccounts/connectionAcls | 아니요 | 
| apiManagementAccounts/apis/connections/connectionAcls | 아니요 | 
| apiManagementAccounts/apis/connectionAcls | 아니요 | 
| apiManagementAccounts/apiAcls | 아니요 | 
| apiManagementAccounts/apis/apiAcls | 아니요 | 
| apiManagementAccounts/apis | 아니요 | 
| apiManagementAccounts/apis/localizedDefinitions | 아니요 | 
| apiManagementAccounts/apis/connections | 아니요 | 
| connections | yes | 
| customApis | yes | 
| connectionGateways | yes | 
| billingMeters | 아니요 | 
| verifyHostingEnvironmentVnet | 아니요 | 

## <a name="xrm"></a>XRM
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| organizations | 아니요 | 

## <a name="next-steps"></a>다음 단계
리소스에 태그를 적용하는 방법을 알아보려면 [태그를 사용하여 Azure 리소스 구성](resource-group-using-tags.md)을 참조하세요.
