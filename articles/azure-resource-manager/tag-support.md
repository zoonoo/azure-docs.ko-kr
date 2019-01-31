---
title: 리소스에 대한 Azure Resource Manager 태그 지원
description: 태그를 지원하는 Azure 리소스 종류를 보여 줍니다. 모든 Azure 서비스에 대한 세부 정보를 제공합니다.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 1/24/2019
ms.author: tomfitz
ms.openlocfilehash: fc085a94e2412ff21b09755102f79636a0b0d3b5
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54884948"
---
# <a name="tag-support-for-azure-resources"></a>Azure 리소스에 대한 태그 지원
이 문서에서는 리소스 종류가 [태그](resource-group-using-tags.md)를 지원하는지 여부를 설명합니다.

## <a name="microsoftaad"></a>Microsoft.AAD
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| DomainServices | 예 | 
| DomainServices/oucontainer | 아니요 | 

## <a name="microsoftaadiam"></a>microsoft.aadiam
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| diagnosticSettings | 아니요 | 
| diagnosticSettingsCategories | 아니요 | 

## <a name="microsoftaddons"></a>Microsoft.Addons
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| supportProviders | 아니요 | 

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| aadsupportcases | 아니요 | 
| addsservices | 아니요 | 
| agents | 아니요 | 
| anonymousapiusers | 아니요 | 
| 구성 | 아니요 | 
| 로그 | 아니요 | 
| reports | 아니요 | 
| services | 아니요 | 

## <a name="microsoftadvisor"></a>Microsoft.Advisor
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| 구성 | 아니요 | 
| generateRecommendations | 아니요 | 
| 동영상 추천 기능 | 아니요 | 
| suppressions | 아니요 | 

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| actionRules | 아니요 | 
| 경고 | 아니요 | 
| alertsList | 아니요 | 
| alertsSummary | 아니요 | 
| alertsSummaryList | 아니요 | 
| smartDetectorAlertRules | 아니요 | 
| smartDetectorRuntimeEnvironments | 아니요 | 
| smartGroups | 아니요 | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| servers | 예 | 

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| reportFeedback | 아니요 | 
| 서비스 | 예 | 
| validateServiceName | 아니요 | 

## <a name="microsoftattestation"></a>Microsoft.Attestation
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| attestationProviders | 아니요 | 

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| classicAdministrators | 아니요 | 
| denyAssignments | 아니요 | 
| elevateAccess | 아니요 | 
| locks | 아니요 | 
| 권한 | 아니요 | 
| policyAssignments | 아니요 | 
| policyDefinitions | 아니요 | 
| policySetDefinitions | 아니요 | 
| providerOperations | 아니요 | 
| roleAssignments | 아니요 | 
| roleDefinitions | 아니요 | 

## <a name="microsoftautomation"></a>Microsoft.Automation
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| automationAccounts | 예 | 
| automationAccounts/configurations | 예 | 
| automationAccounts/jobs | 아니요 | 
| automationAccounts/runbooks | 예 | 
| automationAccounts/softwareUpdateConfigurations | 아니요 | 
| automationAccounts/webhooks | 아니요 | 

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| environments | 아니요 | 
| environments/accounts | 아니요 | 
| environments/accounts/namespaces | 아니요 | 
| environments/accounts/namespaces/configurations | 아니요 | 

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| b2cDirectories | 예 | 

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| registrations | 예 | 
| registrations/customerSubscriptions | 아니요 | 
| registrations/products | 아니요 | 

## <a name="microsoftbatch"></a>Microsoft.Batch
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| batchAccounts | 예 | 

## <a name="microsoftbilling"></a>Microsoft.Billing
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| billingAccounts | 아니요 | 
| billingAccounts/billingProfiles | 아니요 | 
| billingAccounts/billingProfiles/billingSubscriptions | 아니요 | 
| billingAccounts/billingProfiles/invoices | 아니요 | 
| billingAccounts/billingProfiles/invoices/pricesheet | 아니요 | 
| billingAccounts/billingProfiles/operationStatus | 아니요 | 
| billingAccounts/billingProfiles/paymentMethods | 아니요 | 
| billingAccounts/billingProfiles/policies | 아니요 | 
| billingAccounts/billingProfiles/pricesheet | 아니요 | 
| billingAccounts/billingProfiles/products | 아니요 | 
| billingAccounts/billingProfiles/transactions | 아니요 | 
| billingAccounts/billingSubscriptions | 아니요 | 
| billingAccounts/departments | 아니요 | 
| billingAccounts/eligibleOffers | 아니요 | 
| billingAccounts/enrollmentAccounts | 아니요 | 
| billingAccounts/invoices | 아니요 | 
| billingAccounts/invoiceSections | 아니요 | 
| billingAccounts/invoiceSections/billingSubscriptions | 아니요 | 
| billingAccounts/invoiceSections/billingSubscriptions/transfer | 아니요 | 
| billingAccounts/invoiceSections/importRequests | 아니요 | 
| billingAccounts/invoiceSections/initiateImportRequest | 아니요 | 
| billingAccounts/invoiceSections/initiateTransfer | 아니요 | 
| billingAccounts/invoiceSections/operationStatus | 아니요 | 
| billingAccounts/invoiceSections/products | 아니요 | 
| billingAccounts/invoiceSections/transfers | 아니요 | 
| billingAccounts/products | 아니요 | 
| billingAccounts/projects | 아니요 | 
| billingAccounts/projects/billingSubscriptions | 아니요 | 
| billingAccounts/projects/importRequests | 아니요 | 
| billingAccounts/projects/initiateImportRequest | 아니요 | 
| billingAccounts/projects/operationStatus | 아니요 | 
| billingAccounts/projects/products | 아니요 | 
| billingAccounts/transactions | 아니요 | 
| billingPeriods | 아니요 | 
| BillingPermissions | 아니요 | 
| billingProperty | 아니요 | 
| BillingRoleAssignments | 아니요 | 
| BillingRoleDefinitions | 아니요 | 
| CreateBillingRoleAssignment | 아니요 | 
| departments | 아니요 | 
| enrollmentAccounts | 아니요 | 
| importRequests | 아니요 | 
| importRequests/acceptImportRequest | 아니요 | 
| importRequests/declineImportRequest | 아니요 | 
| invoices | 아니요 | 
| transfers | 아니요 | 
| transfers/acceptTransfer | 아니요 | 
| transfers/declineTransfer | 아니요 | 
| transfers/operationStatus | 아니요 | 
| usagePlans | 아니요 | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| mapApis | 예 | 
| updateCommunicationPreference | 아니요 | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| BizTalk | 예 | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| blueprintAssignments | 아니요 | 
| blueprintAssignments/assignmentOperations | 아니요 | 
| blueprintAssignments/operations | 아니요 | 
| blueprints | 아니요 | 
| blueprints/artifacts | 아니요 | 
| blueprints/versions | 아니요 | 
| blueprints/versions/artifacts | 아니요 | 

## <a name="microsoftbotservice"></a>Microsoft.BotService
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| botServices | 예 | 
| botServices/channels | 아니요 | 
| botServices/connections | 아니요 | 

## <a name="microsoftcache"></a>Microsoft.Cache
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| Redis | 예 | 
| RedisConfigDefinition | 아니요 | 

## <a name="microsoftcapacity"></a>Microsoft.Capacity
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| appliedReservations | 아니요 | 
| calculatePrice | 아니요 | 
| catalogs | 아니요 | 
| commercialReservationOrders | 아니요 | 
| reservationOrders | 아니요 | 
| reservationOrders/calculateRefund | 아니요 | 
| reservationOrders/merge | 아니요 | 
| reservationOrders/reservations | 아니요 | 
| reservationOrders/reservations/revisions | 아니요 | 
| reservationOrders/return | 아니요 | 
| reservationOrders/split | 아니요 | 
| reservationOrders/swap | 아니요 | 
| reservations | 아니요 | 
| 리소스 | 아니요 | 
| validateReservationOrder | 아니요 | 

## <a name="microsoftcdn"></a>Microsoft.Cdn
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| edgenodes | 아니요 | 
| 프로필 | 예 | 
| profiles/endpoints | 예 | 
| profiles/endpoints/customdomains | 아니요 | 
| profiles/endpoints/origins | 아니요 | 
| validateProbe | 아니요 | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| certificateOrders | 예 | 
| certificateOrders/certificates | 아니요 | 
| validateCertificateRegistrationInformation | 아니요 | 

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| capabilities | 아니요 | 
| domainNames | 아니요 | 
| domainNames/capabilities | 아니요 | 
| domainNames/internalLoadBalancers | 아니요 | 
| domainNames/serviceCertificates | 아니요 | 
| domainNames/slots | 아니요 | 
| domainNames/slots/roles | 아니요 | 
| moveSubscriptionResources | 아니요 | 
| operatingSystemFamilies | 아니요 | 
| operatingSystems | 아니요 | 
| quotas | 아니요 | 
| resourceTypes | 아니요 | 
| validateSubscriptionMoveAvailability | 아니요 | 
| virtualMachines | 아니요 | 
| virtualMachines/diagnosticSettings | 아니요 | 

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| classicInfrastructureResources | 아니요 | 

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| capabilities | 아니요 | 
| expressRouteCrossConnections | 아니요 | 
| expressRouteCrossConnections/peerings | 아니요 | 
| gatewaySupportedDevices | 아니요 | 
| networkSecurityGroups | 아니요 | 
| quotas | 아니요 | 
| reservedIps | 아니요 | 
| virtualNetworks | 아니요 | 
| virtualNetworks/remoteVirtualNetworkPeeringProxies | 아니요 | 
| virtualNetworks/virtualNetworkPeerings | 아니요 | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| capabilities | 아니요 | 
| disks | 아니요 | 
| images | 아니요 | 
| osImages | 아니요 | 
| osPlatformImages | 아니요 | 
| publicImages | 아니요 | 
| quotas | 아니요 | 
| storageAccounts | 아니요 | 
| storageAccounts/services | 아니요 | 
| storageAccounts/services/diagnosticSettings | 아니요 | 
| storageAccounts/vmImages | 아니요 | 
| vmImages | 아니요 | 

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| 계정 | 예 | 

## <a name="microsoftcommerce"></a>Microsoft.Commerce
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| RateCard | 아니요 | 
| UsageAggregates | 아니요 | 

## <a name="microsoftcompute"></a>Microsoft.Compute
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
| virtualMachineScaleSets | 예 | 
| virtualMachineScaleSets/extensions | 아니요 | 
| virtualMachineScaleSets/networkInterfaces | 아니요 | 
| virtualMachineScaleSets/publicIPAddresses | 아니요 | 
| virtualMachineScaleSets/virtualMachines | 아니요 | 
| virtualMachineScaleSets/virtualMachines/networkInterfaces | 아니요 | 

## <a name="microsoftconsumption"></a>Microsoft.Consumption
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| AggregatedCost | 아니요 | 
| 잔액 | 아니요 | 
| 예산 | 아니요 | 
| Charges | 아니요 | 
| CostTags | 아니요 | 
| credits | 아니요 | 
| events | 아니요 | 
| 예측 | 아니요 | 
| lots | 아니요 | 
| Marketplace | 아니요 | 
| Pricesheets | 아니요 | 
| products | 아니요 | 
| ReservationDetails | 아니요 | 
| ReservationRecommendations | 아니요 | 
| ReservationSummaries | 아니요 | 
| ReservationTransactions | 아니요 | 
| 태그들 | 아니요 | 
| 용어 | 아니요 | 
| UsageDetails | 아니요 | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| containerGroups | 예 | 
| serviceAssociationLinks | 아니요 | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| registries | 예 | 
| registries/builds | 아니요 | 
| registries/builds/cancel | 아니요 | 
| registries/builds/getLogLink | 아니요 | 
| registries/buildTasks | 예 | 
| registries/buildTasks/steps | 아니요 | 
| registries/eventGridFilters | 아니요 | 
| registries/getBuildSourceUploadUrl | 아니요 | 
| registries/GetCredentials | 아니요 | 
| registries/importImage | 아니요 | 
| registries/queueBuild | 아니요 | 
| registries/regenerateCredential | 아니요 | 
| registries/regenerateCredentials | 아니요 | 
| registries/replications | 예 | 
| registries/runs | 아니요 | 
| registries/runs/cancel | 아니요 | 
| registries/scheduleRun | 아니요 | 
| registries/tasks | 예 | 
| registries/updatePolicies | 아니요 | 
| registries/webhooks | 예 | 
| registries/webhooks/getCallbackConfig | 아니요 | 
| registries/webhooks/ping | 아니요 | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| containerServices | 예 | 
| managedClusters | 예 | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| 애플리케이션 | 예 | 
| updateCommunicationPreference | 아니요 | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| 계정 | 예 | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| 경고 | 아니요 | 
| BillingAccounts | 아니요 | 
| 커넥터 | 예 | 
| Departments | 아니요 | 
| 차원 | 아니요 | 
| EnrollmentAccounts | 아니요 | 
| 쿼리 | 아니요 | 
| register | 아니요 | 
| Reportconfigs | 아니요 | 
| 보고서 | 아니요 | 

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| hubs | 예 | 
| hubs/authorizationPolicies | 아니요 | 
| hubs/connectors | 아니요 | 
| hubs/connectors/mappings | 아니요 | 
| hubs/interactions | 아니요 | 
| hubs/kpi | 아니요 | 
| hubs/links | 아니요 | 
| hubs/profiles | 아니요 | 
| hubs/roleAssignments | 아니요 | 
| hubs/roles | 아니요 | 
| hubs/suggestTypeSchema | 아니요 | 
| hubs/views | 아니요 | 
| hubs/widgetTypes | 아니요 | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| jobs | 예 | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| DataBoxEdgeDevices | 예 | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| workspaces | 예 | 
| workspaces/virtualNetworkPeerings | 아니요 | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| catalogs | 예 | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| connectionManagers | 예 | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| dataFactories | 예 | 
| dataFactories/diagnosticSettings | 아니요 | 
| dataFactorySchema | 아니요 | 
| factories | 예 | 
| factories/integrationRuntimes | 아니요 | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| 계정 | 예 | 
| accounts/dataLakeStoreAccounts | 아니요 | 
| accounts/storageAccounts | 아니요 | 
| accounts/storageAccounts/containers | 아니요 | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| 계정 | 예 | 
| accounts/eventGridFilters | 아니요 | 
| accounts/firewallRules | 아니요 | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| services | 예 | 
| services/projects | 예 | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| servers | 예 | 
| servers/recoverableServers | 아니요 | 
| servers/virtualNetworkRules | 아니요 | 

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| servers | 예 | 
| servers/recoverableServers | 아니요 | 
| servers/virtualNetworkRules | 아니요 | 

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| servers | 예 | 
| servers/advisors | 아니요 | 
| servers/queryTexts | 아니요 | 
| servers/recoverableServers | 아니요 | 
| servers/topQueryStatistics | 아니요 | 
| servers/virtualNetworkRules | 아니요 | 
| servers/waitStatistics | 아니요 | 

## <a name="microsoftdevices"></a>Microsoft.Devices
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| IotHubs | 예 | 
| IotHubs/eventGridFilters | 아니요 | 
| ProvisioningServices | 예 | 
| usages | 아니요 | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| controllers | 예 | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| labs | 예 | 
| labs/serviceRunners | 예 | 
| labs/virtualMachines | 예 | 
| schedules | 예 | 

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| databaseAccountNames | 아니요 | 
| databaseAccounts | 예 | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| domains | 예 | 
| domains/domainOwnershipIdentifiers | 아니요 | 
| generateSsoRequest | 아니요 | 
| topLevelDomains | 아니요 | 
| validateDomainRegistrationInformation | 아니요 | 

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| lcsprojects | 아니요 | 
| lcsprojects/clouddeployments | 아니요 | 
| lcsprojects/connectors | 아니요 | 

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| domains | 예 | 
| domains/topics | 아니요 | 
| eventSubscriptions | 아니요 | 
| extensionTopics | 아니요 | 
| topics | 예 | 
| topicTypes | 아니요 | 

## <a name="microsofteventhub"></a>Microsoft.EventHub
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| clusters | 예 | 
| namespaces | 예 | 
| namespaces/authorizationrules | 아니요 | 
| namespaces/disasterrecoveryconfigs | 아니요 | 
| namespaces/eventhubs | 아니요 | 
| namespaces/eventhubs/authorizationrules | 아니요 | 
| namespaces/eventhubs/consumergroups | 아니요 | 

## <a name="microsoftfeatures"></a>Microsoft.Features
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| 기능 | 아니요 | 
| providers | 아니요 | 

## <a name="microsoftgallery"></a>Microsoft.Gallery
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| enroll | 아니요 | 
| galleryitems | 아니요 | 
| generateartifactaccessuri | 아니요 | 
| myareas | 아니요 | 
| myareas/areas | 아니요 | 
| myareas/areas/areas | 아니요 | 
| myareas/areas/areas/galleryitems | 아니요 | 
| myareas/areas/galleryitems | 아니요 | 
| myareas/galleryitems | 아니요 | 
| register | 아니요 | 
| 리소스 | 아니요 | 
| retrieveresourcesbyid | 아니요 | 

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| guestConfigurationAssignments | 아니요 | 
| software | 아니요 | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| hanaInstances | 예 | 

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| clusters | 예 | 
| clusters/applications | 아니요 | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| jobs | 예 | 

## <a name="microsoftinformationprotection"></a>Microsoft.InformationProtection
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| labelGroups | 아니요 | 
| labelGroups/labels | 아니요 | 
| labelGroups/labels/conditions | 아니요 | 
| labelGroups/labels/subLabels | 아니요 | 
| labelGroups/labels/subLabels/conditions | 아니요 | 

## <a name="microsoftinsights"></a>microsoft.insights
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| actiongroups | 예 | 
| activityLogAlerts | 예 | 
| alertrules | 예 | 
| automatedExportSettings | 아니요 | 
| autoscalesettings | 예 | 
| baseline | 아니요 | 
| calculatebaseline | 아니요 | 
| components | 예 | 
| components/events | 아니요 | 
| components/pricingPlans | 아니요 | 
| components/query | 아니요 | 
| diagnosticSettings | 아니요 | 
| diagnosticSettingsCategories | 아니요 | 
| eventCategories | 아니요 | 
| eventtypes | 아니요 | 
| extendedDiagnosticSettings | 아니요 | 
| logDefinitions | 아니요 | 
| logprofiles | 아니요 | 
| 로그 | 아니요 | 
| migrateToNewPricingModel | 아니요 | 
| myWorkbooks | 아니요 | 
| 쿼리 | 아니요 | 
| rollbackToLegacyPricingModel | 아니요 | 
| scheduledqueryrules | 예 | 
| vmInsightsOnboardingStatuses | 아니요 | 
| webtests | 예 | 
| workbooks | 예 | 

## <a name="microsoftintune"></a>Microsoft.Intune
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| diagnosticSettings | 아니요 | 
| diagnosticSettingsCategories | 아니요 | 

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| IoTApps | 예 | 

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| 그래프 | 예 | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| deletedVaults | 아니요 | 
| vaults | 예 | 
| vaults/accessPolicies | 아니요 | 
| vaults/secrets | 아니요 | 

## <a name="microsoftkusto"></a>Microsoft.Kusto
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| clusters | 예 | 
| clusters/databases | 아니요 | 
| clusters/databases/dataconnections | 아니요 | 
| clusters/databases/eventhubconnections | 아니요 | 

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| labaccounts | 예 | 
| users | 아니요 | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| 계정 | 예 | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| 계정 | 예 | 

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| 로그 | 아니요 | 

## <a name="microsoftlogic"></a>Microsoft.Logic
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| integrationAccounts | 예 | 
| workflows | 예 | 

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| commitmentPlans | 예 | 
| webServices | 예 | 
| 작업 영역 | 예 | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| 계정 | 예 | 
| accounts/workspaces | 예 | 
| accounts/workspaces/projects | 예 | 
| teamAccounts | 예 | 
| teamAccounts/workspaces | 예 | 
| teamAccounts/workspaces/projects | 예 | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| 계정 | 예 | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| workspaces | 예 | 
| workspaces/computes | 아니요 | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| Identities | 아니요 | 
| userAssignedIdentities | 예 | 

## <a name="microsoftmanagement"></a>Microsoft.Management
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| getEntities | 아니요 | 
| managementGroups | 아니요 | 
| 리소스 | 아니요 | 
| startTenantBackfill | 아니요 | 
| tenantBackfillStatus | 아니요 | 

## <a name="microsoftmaps"></a>Microsoft.Maps
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| 계정 | 예 | 
| accounts/eventGridFilters | 아니요 | 

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| offers | 아니요 | 
| offerTypes | 아니요 | 
| offerTypes/publishers | 아니요 | 
| offerTypes/publishers/offers | 아니요 | 
| offerTypes/publishers/offers/plans | 아니요 | 
| offerTypes/publishers/offers/plans/agreements | 아니요 | 
| offerTypes/publishers/offers/plans/configs | 아니요 | 
| offerTypes/publishers/offers/plans/configs/importImage | 아니요 | 
| privategalleryitems | 아니요 | 
| products | 아니요 | 

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| classicDevServices | 예 | 
| updateCommunicationPreference | 아니요 | 

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| agreements | 아니요 | 
| offertypes | 아니요 | 

## <a name="microsoftmedia"></a>Microsoft.Media
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
| mediaservices/streamingEndpointOperations | 아니요 | 
| mediaservices/streamingEndpoints | 예 | 
| mediaservices/streamingLocators | 아니요 | 
| mediaservices/streamingPolicies | 아니요 | 
| mediaservices/transforms | 아니요 | 
| mediaservices/transforms/jobs | 아니요 | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| projects | 예 | 

## <a name="microsoftnetwork"></a>Microsoft.Network
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| applicationGateways | 예 | 
| applicationSecurityGroups | 예 | 
| azureFirewallFqdnTags | 아니요 | 
| azureFirewalls | 예 | 
| bgpServiceCommunities | 아니요 | 
| connections | 예 | 
| ddosCustomPolicies | 예 | 
| ddosProtectionPlans | 예 | 
| dnsOperationStatuses | 아니요 | 
| dnszones | 예 | 
| dnszones/A | 아니요 | 
| dnszones/AAAA | 아니요 | 
| dnszones/all | 아니요 | 
| dnszones/CAA | 아니요 | 
| dnszones/CNAME | 아니요 | 
| dnszones/MX | 아니요 | 
| dnszones/NS | 아니요 | 
| dnszones/PTR | 아니요 | 
| dnszones/recordsets | 아니요 | 
| dnszones/SOA | 아니요 | 
| dnszones/SRV | 아니요 | 
| dnszones/TXT | 아니요 | 
| expressRouteCircuits | 예 | 
| expressRouteServiceProviders | 아니요 | 
| frontdoors | 예 | 
| frontdoorWebApplicationFirewallPolicies | 예 | 
| getDnsResourceReference | 아니요 | 
| interfaceEndpoints | 예 | 
| internalNotify | 아니요 | 
| loadBalancers | 예 | 
| localNetworkGateways | 예 | 
| natGateways | 예 | 
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
| trafficManagerGeographicHierarchies | 아니요 | 
| trafficmanagerprofiles | 예 | 
| trafficmanagerprofiles/heatMaps | 아니요 | 
| virtualHubs | 예 | 
| virtualNetworkGateways | 예 | 
| virtualNetworks | 예 | 
| virtualNetworkTaps | 예 | 
| virtualWans | 예 | 
| vpnGateways | 예 | 
| vpnSites | 예 | 
| webApplicationFirewallPolicies | 예 | 

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| namespaces | 예 | 
| namespaces/notificationHubs | 예 | 

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| devices | 아니요 | 
| linkTargets | 아니요 | 
| storageInsightConfigs | 아니요 | 
| workspaces | 예 | 
| workspaces/dataSources | 아니요 | 
| workspaces/linkedServices | 아니요 | 
| workspaces/query | 아니요 | 

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| managementassociations | 아니요 | 
| managementconfigurations | 예 | 
| solutions | 예 | 
| 뷰 | 예 | 

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| policyEvents | 아니요 | 
| policyStates | 아니요 | 
| policyTrackedResources | 아니요 | 
| remediations | 아니요 | 

## <a name="microsoftportal"></a>Microsoft.Portal
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| consoles | 아니요 | 
| dashboards | 예 | 
| userSettings | 아니요 | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| workspaceCollections | 예 | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| capacities | 예 | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| 계정 | 예 | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| backupProtectedItems | 아니요 | 
| vaults | 예 | 

## <a name="microsoftrelay"></a>Microsoft.Relay
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| namespaces | 예 | 
| namespaces/authorizationrules | 아니요 | 
| namespaces/hybridconnections | 아니요 | 
| namespaces/hybridconnections/authorizationrules | 아니요 | 
| namespaces/wcfrelays | 아니요 | 
| namespaces/wcfrelays/authorizationrules | 아니요 | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| 리소스 | 아니요 | 
| subscriptionsStatus | 아니요 | 

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| availabilityStatuses | 아니요 | 
| childAvailabilityStatuses | 아니요 | 
| childResources | 아니요 | 
| events | 아니요 | 
| impactedResources | 아니요 | 
| 알림 | 아니요 | 

## <a name="microsoftresources"></a>Microsoft.Resources
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| 배포 | 아니요 | 
| 배포/작업 | 아니요 | 
| links | 아니요 | 
| notifyResourceJobs | 아니요 | 
| providers | 아니요 | 
| resourceGroups | 아니요 | 
| 리소스 | 아니요 | 
| 구독 | 아니요 | 
| subscriptions/providers | 아니요 | 
| subscriptions/resourceGroups | 아니요 | 
| subscriptions/resourcegroups/resources | 아니요 | 
| subscriptions/resources | 아니요 | 
| subscriptions/tagnames | 아니요 | 
| subscriptions/tagNames/tagValues | 아니요 | 
| tenants | 아니요 | 

## <a name="microsoftsaas"></a>Microsoft.SaaS
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| 애플리케이션 | 예 | 
| saasresources | 아니요 | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| flows | 예 | 
| jobcollections | 예 | 

## <a name="microsoftsearch"></a>Microsoft.Search
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| resourceHealthMetadata | 아니요 | 
| searchServices | 예 | 

## <a name="microsoftsecurity"></a>Microsoft.Security
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| advancedThreatProtectionSettings | 아니요 | 
| 경고 | 아니요 | 
| allowedConnections | 아니요 | 
| appliances | 아니요 | 
| applicationWhitelistings | 아니요 | 
| AutoProvisioningSettings | 아니요 | 
| Compliances | 아니요 | 
| dataCollectionAgents | 아니요 | 
| discoveredSecuritySolutions | 아니요 | 
| externalSecuritySolutions | 아니요 | 
| InformationProtectionPolicies | 아니요 | 
| jitNetworkAccessPolicies | 아니요 | 
| monitoring | 아니요 | 
| monitoring/antimalware | 아니요 | 
| monitoring/baseline | 아니요 | 
| monitoring/patch | 아니요 | 
| 정책 | 아니요 | 
| pricings | 아니요 | 
| securityContacts | 아니요 | 
| securitySolutions | 아니요 | 
| securitySolutionsReferenceData | 아니요 | 
| securityStatus | 아니요 | 
| securityStatus/endpoints | 아니요 | 
| securityStatus/subnets | 아니요 | 
| securityStatus/virtualMachines | 아니요 | 
| securityStatuses | 아니요 | 
| securityStatusesSummaries | 아니요 | 
| 설정 | 아니요 | 
| 태스크 | 아니요 | 
| topologies | 아니요 | 
| workspaceSettings | 아니요 | 

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| diagnosticSettings | 아니요 | 
| diagnosticSettingsCategories | 아니요 | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| namespaces | 예 | 
| namespaces/authorizationrules | 아니요 | 
| namespaces/disasterrecoveryconfigs | 아니요 | 
| namespaces/eventgridfilters | 아니요 | 
| namespaces/queues | 아니요 | 
| namespaces/queues/authorizationrules | 아니요 | 
| namespaces/topics | 아니요 | 
| namespaces/topics/authorizationrules | 아니요 | 
| namespaces/topics/subscriptions | 아니요 | 
| namespaces/topics/subscriptions/rules | 아니요 | 
| premiumMessagingRegions | 아니요 | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| clusters | 예 | 
| clusters/applications | 아니요 | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| 애플리케이션 | 예 | 
| gateways | 예 | 
| networks | 예 | 
| secrets | 예 | 
| volumes | 예 | 

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| SignalR | 예 | 

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| applianceDefinitions | 예 | 
| appliances | 예 | 
| applicationDefinitions | 예 | 
| 애플리케이션 | 예 | 
| jitRequests | 예 | 

## <a name="microsoftsql"></a>Microsoft.SQL
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
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
| servers/communicationLinks | 아니요 | 
| servers/databases | 예 | 
| servers/encryptionProtector | 아니요 | 
| servers/keys | 아니요 | 
| servers/restorableDroppedDatabases | 아니요 | 
| servers/serviceobjectives | 아니요 | 
| servers/tdeCertificates | 아니요 | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| SqlVirtualMachineGroups | 예 | 
| SqlVirtualMachineGroups/AvailabilityGroupListeners | 아니요 | 
| SqlVirtualMachines | 예 | 

## <a name="microsoftstorage"></a>Microsoft.Storage
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| storageAccounts | 예 | 
| storageAccounts/blobServices | 아니요 | 
| storageAccounts/fileServices | 아니요 | 
| storageAccounts/queueServices | 아니요 | 
| storageAccounts/services | 아니요 | 
| storageAccounts/tableServices | 아니요 | 
| usages | 아니요 | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| storageSyncServices | 예 | 
| storageSyncServices/registeredServers | 아니요 | 
| storageSyncServices/syncGroups | 아니요 | 
| storageSyncServices/syncGroups/cloudEndpoints | 아니요 | 
| storageSyncServices/syncGroups/serverEndpoints | 아니요 | 
| storageSyncServices/workflows | 아니요 | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| managers | 예 | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| streamingjobs | 예 | 
| streamingjobs/diagnosticSettings | 아니요 | 

## <a name="microsoftsubscription"></a>Microsoft.Subscription
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| CreateSubscription | 아니요 | 
| SubscriptionDefinitions | 아니요 | 
| SubscriptionOperations | 아니요 | 

## <a name="microsoftsupport"></a>microsoft.support
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| supporttickets | 아니요 | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| providerRegistrations | 예 | 
| 리소스 | 예 | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| environments | 예 | 
| environments/accessPolicies | 아니요 | 
| environments/eventsources | 예 | 
| environments/referenceDataSets | 예 | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| 계정 | 예 | 
| account/extension | 예 | 
| account/project | 예 | 

## <a name="microsoftweb"></a>Microsoft.Web
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
| hostingEnvironments/multiRolePools | 아니요 | 
| hostingEnvironments/multiRolePools/instances | 아니요 | 
| hostingEnvironments/workerPools | 아니요 | 
| hostingEnvironments/workerPools/instances | 아니요 | 
| publishingUsers | 아니요 | 
| 동영상 추천 기능 | 아니요 | 
| resourceHealthMetadata | 아니요 | 
| runtimes | 아니요 | 
| serverFarms | 예 | 
| serverFarms/workers | 아니요 | 
| sites | 예 | 
| sites/domainOwnershipIdentifiers | 아니요 | 
| sites/hostNameBindings | 아니요 | 
| sites/instances | 아니요 | 
| sites/instances/extensions | 아니요 | 
| sites/premieraddons | 예 | 
| sites/recommendations | 아니요 | 
| sites/resourceHealthMetadata | 아니요 | 
| sites/slots | 예 | 
| sites/slots/hostNameBindings | 아니요 | 
| sites/slots/instances | 아니요 | 
| sites/slots/instances/extensions | 아니요 | 
| sourceControls | 아니요 | 
| validate | 아니요 | 
| verifyHostingEnvironmentVnet | 아니요 | 

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| diagnosticSettings | 아니요 | 
| diagnosticSettingsCategories | 아니요 | 

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| DeviceServices | 예 | 

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor
| 리소스 종류 | 태그 지원 |
| ------------- | ----------- |
| components | 아니요 | 
| componentsSummary | 아니요 | 
| monitorInstances | 아니요 | 
| monitorInstancesSummary | 아니요 | 
| monitors | 아니요 | 
| notificationSettings | 아니요 | 

## <a name="next-steps"></a>다음 단계
리소스에 태그를 적용하는 방법을 알아보려면 [태그를 사용하여 Azure 리소스 구성](resource-group-using-tags.md)을 참조하세요.
