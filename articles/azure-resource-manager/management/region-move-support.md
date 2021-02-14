---
title: 지역 간 Azure 리소스 이동에 대 한 지원
description: Azure 지역 간에 이동할 수 있는 Azure 리소스 종류를 나열 합니다.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 08/25/2020
ms.author: raynew
ms.openlocfilehash: 18d4d84462d528b718d784ff6a16ecf990ed0d20
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100094018"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>지역 간 Azure 리소스 이동에 대 한 지원

이 문서에서는 Azure 리소스 종류가 다른 Azure 지역으로 이동 하는 데 지원 되는지 여부를 확인 합니다. 

리소스 공급자 네임스페이스로 이동합니다.
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.ClassicSubscription](#microsoftclassicsubscription)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataProtection](#microsoftdataprotection)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentation](#microsoftexperimentation)
> - [Microsoft.Falcon](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [HybridNetwork](#microsofthybridnetwork)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Maintenance](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedNetwork](#microsoftmanagednetwork)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.Purview](#microsoftpurview)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [microsoft.support](#microsoftsupport)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | domainservices | 예 | 


## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | diagnosticsettings | 예 |
> | diagnosticsettingscategories | 예 |
> | privatelinkforazuread | 예 |
> | tenants |  예 |

## <a name="microsoftaddons"></a>microsoft. Addons

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | supportproviders | 예 |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | aadsupportcases | 예 |
> | addsservices | 예 | 
> | agents | 예 | 
> | anonymousapiusers | 예 |
> | 구성 | 예 | 
> | 로그 | 예 | 
> | reports | 예 | 
> | servicehealthmetrics | 예 | 
> | services | 예 | 

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | 구성 | 예 | 
> | generaterecommendations | 예 |
> | metadata | 예 |
> | 동영상 추천 기능 | 예 |
> | suppressions | 예 | 

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | actionrules | 예 | 
> | 경고 | 예 | 
> | alertslist | 예 | 
> | alertsmetadata | 예 | 
> | alertssummary | 예 | 
> | alertssummarylist | 예 | 
> | smartdetectoralertrules | 예 | 
> | smartgroups | 예 | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | servers | 예 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | reportfeedback | 예 |
> | 서비스 |  예 (템플릿 사용) <br/><br/> [지역 간에 API Management 이동](../../api-management/api-management-howto-migrate.md)합니다. | 

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | configurationstores | 예 | 
> | configurationstores / eventgridfilters | 예 |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | spring | 예 | 

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | apiapps | 예 (템플릿 사용)<br/><br/> [App Service 앱을 다른 지역으로 이동](../../app-service/manage-move-across-regions.md) | 
> | appidentities | 예 | 
> | gateways | 예 | 

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | attestationproviders | 예 | 

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | classicadministrators | 예 | 
> | dataaliases | 예 | 
> | denyassignments | 예 | 
> | elevateaccess | 예 | 
> | findorphanroleassignments | 예 | 
> | locks | 예 | 
> | 권한 | 예 | 
> | policyassignments | 예 | 
> | policydefinitions | 예 | 
> | policysetdefinitions | 예 | 
> | privatelinkassociations | 예 | 
> | resourcemanagementprivatelinks | 예 | 
> | roleassignments | 예 | 
> | roleassignmentsusagemetrics | 예 | 
> | roledefinitions | 예 | 

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | automationaccounts | 예 (템플릿 사용) <br/><br/> [지역에서 복제 사용](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |  
> | automationaccounts / configurations | 예 | 
> | automationaccounts / runbooks | 예 | 

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | Subscription |
> | ------------- | ----------- | 
> | privateclouds | 예 | 


## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | b2cdirectories | 예 | 
> | b2ctenants | 예 | 

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | datacontrollers | 예 | 
> | hybriddatamanagers | 예 | 
> | postgresinstances | 예 | 
> | sqlinstances | 예 | 
> | sqlmanagedinstances | 예 |
> | sqlserverinstances | 예 | 
> | sqlserverregistrations | 예 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | cloudmanifestfiles | 예 |
> | registrations | 예 | 

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | clusters | 예 | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | batchaccounts |  Batch 계정은 한 지역에서 다른 지역으로 직접 이동할 수 없지만 템플릿을 사용 하 여 템플릿을 내보내고 수정한 다음 템플릿을 새 지역에 배포할 수 있습니다. <br/><br/> [지역 간 Batch 계정 이동](../../batch/best-practices.md#moving-batch-accounts-across-regions) 에 대 한 자세한 정보 |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | billingaccounts | 예 | 
> | billingperiods | 예 | 
> | billingpermissions | 예 | 
> | billingproperty | 예 | 
> | billingroleassignments | 예 | 
> | billingroledefinitions | 예 | 
> | departments | 예 | 
> | enrollmentaccounts | 예 | 
> | invoices | 예 | 
> | transfers | 예 | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | mapapis | 예 | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | biztalk | 예 | 

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | blockchainmembers | 예 <br/><br/> Blockchain 네트워크는 다른 지역에 노드를 포함할 수 없습니다. 
> | cordamembers | 예 |
> | watchers | 예 | 

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | tokenservices | 예 |


## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | blueprintassignments | 예 | 
> | blueprints | 예 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | botservices | 예 | 

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | redis | 예 | 
> | redisenterprise | 예 | 

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | appliedreservations | 예 | 
> | calculateexchange | 예 | 
> | calculateprice | 예 | 
> | calculatepurchaseprice | 예 | 
> | catalogs | 예 | 
> | commercialreservationorders | 예 | 
> | 교환 | 예 |
> | reservationorders | 예 | 
> | reservations | 예 | 
> | 리소스 | 예 | 
> | validatereservationorder | 예 | 

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | 예 |
> | edgenodes | 예
> | 프로필 | 예 | 
> | profiles / endpoints | 예 | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | certificateorders | 예 | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | capabilities | 예 | 
> | domainnames | 예 | 예 |
> | quotas | 예 | 
> | resourcetypes | 예 |
> | validatesubscriptionmoveavailability | 예 | 
> | virtualmachines | 예 

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | classicinfrastructureresources | 예 | 

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | capabilities | 예 | 
> | expressroutecrossconnections | 예 | 
> | expressroutecrossconnections / peerings | 예 | 
> | gatewaysupporteddevices | 예 | 
> | networksecuritygroups | 예 |
> | quotas | 예 |
> | reservedips | 예 | 
> | virtualnetworks | 예 | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | disks | 예 | 
> | images | 예 | 
> | osimages | 예 | 
> | osplatformimages | 예 | 
> | publicimages | 예 | 
> | quotas | 예 | 
> | storageaccounts | 예 |  
> | vmimages | 예 |

## <a name="microsoftclassicsubscription"></a>Microsoft.ClassicSubscription

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | 작업 | 예 | 

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 예 | 
> | Cognitive Search | 수동 단계에서 지원 됩니다.<br/><br/> [Azure Cognitive Search 서비스를 다른 지역으로 이동 하](../../search/search-howto-move-across-regions.md) 는 방법을 알아봅니다.

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | ratecard | 예 | 
> | usageaggregates | 예 | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | availabilitysets | 예 <br/><br/> [Azure 리소스](../../resource-mover/tutorial-move-region-virtual-machines.md) 이동 기를 사용 하 여 가용성 집합을 이동 합니다. | 
> | diskaccesses | 예 |
> | diskencryptionsets | 예 | 
> | disks | 예 <br/><br/> Azure [리소스](../../resource-mover/tutorial-move-region-virtual-machines.md) 이동 기를 사용 하 여 azure vm 및 관련 디스크를 이동 합니다. | 
> | galleries | 예 | 
> | galleries / images | 예 | 
> | galleries / images / versions | 예 | 
> | hostgroups | 예 | 
> | hostgroups / hosts | 예 | 
> | images | 예 | 
> | proximityplacementgroups | 예 | 
> | restorepointcollections | 예 | 
> | sharedvmimages | 예 | 
> | sharedvmimages / versions | 예 | 
> | 스냅샷 | 예 | 
> | sshpublickeys | 예 |
> | virtualmachines | 예 <br/><br/> Azure [리소스](../../resource-mover/tutorial-move-region-virtual-machines.md) 이동 기를 사용 하 여 azure vm을 이동 합니다. | 
> | virtualmachines / extensions | 예 | 
> | virtualmachinescalesets | 예 | 

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | aggregatedcost | 예 | 
> | balances | 예 | 
> | budgets | 예 | 
> | charges | 예 | 
> | costtags | 예 | 
> | credits | 예 | 
> | events | 예 | 
> | forecasts | 예 | 
> | lots | 예 | 
> | marketplaces | 예 | 
> | pricesheets | 예 | 
> | products | 예 | 
> | reservationdetails | 예 | 
> | reservationrecommendationdetails | 예 | 
> | reservationrecommendations | 예 | 
> | reservationsummaries | 예 | 
> | reservationtransactions | 예 | 
> | tags | 예 | 
> | tenants | 예 | 
> | terms | 예 | 
> | usagedetails | 예 | 


## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | containergroups | 예 | 
> | serviceassociationlinks | 예 |


## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | registries | 예 |  
> | registries / agentpools | 예 | 
> | registries / buildtasks | 예 |  
> | registries / replications | 예 | 
> | registries / tasks | 예 |  
> | registries / webhooks | 예 | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | containerservices | 예 |
> | managedclusters | 예 | 
> | openshiftmanagedclusters | 예 | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 애플리케이션 | 예 | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 예 | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 경고 | 예 | 
> | billingaccounts | 예 | 
> | budgets | 예 | 
> | cloudconnectors | 예 | 
> | 커넥터 | 예 | 
> | departments | 예 | 
> | dimensions | 예 | 
> | enrollmentaccounts | 예 | 
> | exports | 예 | 
> | externalbillingaccounts | 예 | 
> | 예측 | 예 | 
> | Query | 예 | 
> | register | 예 | 
> | reportconfigs | 예 | 
> | reports | 예 | 
> | 설정 | 예 | 
> | showbackrules | 예 | 
> | 뷰 | 예 | 

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | hubs | 예 |  

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | requests | 예 | 

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | associations | 예 |
> | resourceproviders | 예 | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | jobs | 예 | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | availableskus | 예 |
> | databoxedgedevices | 예 | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | workspaces | 예 | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | catalogs | 예 | 
> | datacatalogs | 예 | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | connectionmanagers | 예 | 

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 패키지 | 예 | 
> | 플랜 | 예 | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | datafactories | 예 | 
> | factories | 예 |  

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | datalakeaccounts | 예 | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 예 | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 예 | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | services | 예 | 
> | services / projects | 예 | 
> | slots | 예 | 

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | ---------- |
> | backupvaults | 예 | 

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 예 | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | servers | 지역 간 읽기 복제본을 사용 하 여 기존 서버를 이동할 수 있습니다. [자세히 알아보기](../../postgresql/howto-move-regions-portal.md).<br/><br/> 지역 중복 백업 저장소를 사용 하 여 서비스를 프로 비전 하는 경우 지역 복원을 사용 하 여 다른 지역에서 복원할 수 있습니다. [자세히 알아보기](../../mariadb/concepts-business-continuity.md#recover-from-an-azure-regional-data-center-outage).

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | servers | 지역 간 읽기 복제본을 사용 하 여 기존 서버를 이동할 수 있습니다. [자세히 알아보기](../../mysql/howto-move-regions-portal.md).

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | servergroups | 예 | 
> | servers | 지역 간 읽기 복제본을 사용 하 여 기존 서버를 이동할 수 있습니다. [자세한 정보](../../postgresql/howto-move-regions-portal.md).
> | serversv2 | 예 | 

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | artifactsources | 예 | 
> | rollouts | 예 |  
> | servicetopologies | 예 | 
> | servicetopologies / services | 예 |  
> | servicetopologies / services / serviceunits | 예 | 
> | 단계 | 예 | 


## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | applicationgroups | 예 | 
> | workspaces | 예 | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | elasticpools | 아니요. 리소스가 노출 되지 않습니다.
> | elasticpools / iothubtenants | 아니요. 리소스가 노출 되지 않습니다.
> | iothubs | 예. [자세한 정보](../../iot-hub/iot-hub-how-to-clone.md)
> | provisioningservices | 예 | 

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | controllers | 예 | 


## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | controllers | 예 | 
> | AKS 클러스터 | 예<br/><br/> 다른 지역으로 이동 하는 방법에 [대해 자세히 알아보세요](../../dev-spaces/faq.md#can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region) .

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | labcenters | 예 | 
> | labs | 예 | 
> | labs / environments | 예 |  
> | labs / servicerunners | 예 | 
> | labs / virtualmachines | 예 |  
> | schedules | 예 |  

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | digitaltwinsinstances | 예, 새 지역에서 리소스를 다시 만들어 합니다. [자세한 정보](../../digital-twins/how-to-move-regions.md) |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | databaseaccounts | 예 | 
> | databaseaccounts | 예 | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | domains | 예 | 
> | generatessorequest | 예 | 
> | topleveldomains | 예 | 
> | validatedomainregistrationinformation | 예 |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | services | 예 |  

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | domains | 예 | 
> | eventsubscriptions | 예 |
> | extensiontopics | 예 | 
> | partnernamespaces | 예 | 
> | partnerregistrations | 예 | 
> | partnertopics | 예 | 
> | systemtopics | 예 | 
> | topics | 예 | 
> | topictypes | 예 | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | clusters | 예 |  
> | 네임스페이스 | 예 (템플릿 사용)<br/><br/> [이벤트 허브 네임 스페이스를 다른 지역으로 이동](../../event-hubs/move-across-regions.md) | 
> | sku | 예 |  

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | experimentworkspaces | 예 | 

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | 네임스페이스 | 예 | 

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | featureproviders | 예 | 
> | 기능 | 예 | 
> | providers | 예 | 
> | subscriptionfeatureregistrations | 예 | 

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 예 | 

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | automanagedaccounts | 예 | 
> | automanagedvmconfigurationprofiles | 예 | 
> | guestconfigurationassignments | 예 | 
> | software | 예 | 
> | softwareupdateprofile | 예 | 
> | softwareupdates | 예 | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | hanainstances | 예 | 
> | sapmonitors | 예 |  

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | dedicatedhsms | 예 | 


## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | clusters | 예 | 

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | services | 예 |  

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | machines | 예 | 
> | machines / extensions | 예 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | datamanagers |  예 | 

## <a name="microsofthybridnetwork"></a>HybridNetwork

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | devices | 예 | 
> | vnfs | 예 | 

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | components | 예 | 
> | networkscopes | 예 | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | jobs |  예 | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 아니요. [자세히 알아보기](../../azure-monitor/faq.md#how-do-i-move-an-application-insights-resource-to-a-new-region).
> | actiongroups |  예 | 
> | activitylogalerts | 예 | 
> | alertrules |  예 | 
> | autoscalesettings |  예 | 
> | baseline | 예 |
> | components |  예 |  
> | datacollectionrules | 예 | 
> | diagnosticsettings | 예 | 
> | diagnosticsettingscategories | 예 | 
> | eventcategories | 예 | 
> | eventtypes | 예 | 
> | extendeddiagnosticsettings | 예 | |
> | guestdiagnosticsettings | 예 | 
> | listmigrationdate | 예 | 
> | logdefinitions | 예 | 
> | logprofiles | 예 | 
> | 로그 | 예 | 예 |
> | metricalerts | 예 | 
> | metricbaselines | 예 | 
> | metricbatch | 예 | 
> | metricdefinitions | 예 | 
> | metricnamespaces | 예 | 
> | 메트릭 | 예 | 
> | migratealertrules | 예 |
> | migratetonewpricingmodel | 예 | 
> | myworkbooks | 예 |
> | notificationgroups | 예 | 
> | privatelinkscopes | 예 |
> | rollbacktolegacypricingmodel | 예 |
> | scheduledqueryrules |  예 | 
> | 토폴로지 | 예 |
> | 트랜잭션 | 예 |
> | vminsightsonboardingstatuses | 예 |
> | webtests |  예 | 
> | 웹 테스트/gettestresultfile | 예 |
> | workbooks |  예 |  
> | workbooktemplates | 예 |


## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | apptemplates | 예 | 
> | iotapps | 예 | 



## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> |  iothub |  예 (허브 복제) <br/><br/> [IoT hub를 다른 지역에 복제](../../iot-hub/iot-hub-how-to-clone.md)

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 |
> | ------------- | ----------- | 
> | graph | 예 | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | deletedvaults | 예 |
> | hsmpools | 예 | 
> | managedhsms | 예 |
> | vaults |  예 | 

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | connectedclusters | 예 | 
> | registeredsubscriptions | 예 | 

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | sourcecontrolconfigurations | 예 | 

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | clusters |  예 |  

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | labaccounts | 예 | 
> | users | 예 | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 예 | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 아니요, 글로벌 서비스입니다.

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | hostingenvironments | 예 | 
> | integrationaccounts |  예 |  
> | integrationserviceenvironments | 예 | 
> | integrationserviceenvironments / managedapis | 예 |
> | isolatedenvironments | 예 | 
> | workflows |  예 |  

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | commitmentplans |  예 | 
> | webservices |  예 | 
> | workspaces |  예 | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | operationalizationclusters |  예 | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 예 | 
> | teamaccounts | 예 | 


## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 예 | 


## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | workspaces | 예 | 

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | configurationassignments | 예. [자세한 정보](../../virtual-machines/move-region-maintenance-configuration.md) | 
> | maintenanceconfigurations | 예. [자세한 정보](../../virtual-machines/move-region-maintenance-configuration-resources.md) |
> | updates | 예 | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | ID | 예 | 
> | userassignedidentities | 예 | 

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | managednetworks | 예 | 
> | managednetworks / managednetworkgroups | 예 |
> | managednetworks / managednetworkpeeringpolicies | 예 | 
> | 알림 | 예 | 

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | marketplaceregistrationdefinitions | 예 | 
> | registrationassignments | 예 |
> | registrationdefinitions | 예 | 

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | getentities | 예 | 
> | managementgroups | 예 | 
> | managementgroups / settings | 예 | 
> | 리소스 | 예 | 
> | starttenantbackfill | 예 | 
> | tenantbackfillstatus | 예 | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 |  아니요, Azure Maps는 지리 공간적 서비스입니다. 
> | accounts / privateatlases | 예

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | offers | 예 | 
> | offertypes | 예 | 
> | privategalleryitems | 예 | 
> | privatestoreclient | 예 | 
> | privatestores | 예 | 
> | products | 예 | 
> | publishers | 예 | 
> | register | 예 | 

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | classicdevservices | 예 | 

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | agreements | 예 | 
> | offertypes | 예 | 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | mediaservices |  예 | 
> | mediaservices / liveevents |  예 | 
> | mediaservices / streamingendpoints |  예 | 

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | appclusters | 예 | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | assessmentprojects | 예 | 
> | migrateprojects | 예 | 
> | movecollections | 예
> | projects | 예 | 

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | 예 | 
> | objectunderstandingaccounts | 예 | 
> | remoterenderingaccounts | 예 | 
> | spatialanchorsaccounts | 예 | 

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | netappaccounts | 예 | 
> | netappaccounts / capacitypools | 예 | 
> | netappaccounts / capacitypools / volumes | 예 | 
> | netappaccounts / capacitypools / volumes / mounttargets | 예 | 
> | netappaccounts / capacitypools / volumes / snapshots | 예 | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | applicationgateways | 예 |
> | applicationgatewaywebapplicationfirewallpolicies | 예 | 
> | applicationsecuritygroups |  예 |  
> | azurefirewalls |  예 |  
> | bastionhosts | 예 | 
> | bgpservicecommunities | 예 |
> | connections |  예 | 
> | ddoscustompolicies |  예 | 
> | ddosprotectionplans | 예 | 
> | dnszones |  예 | 
> | expressroutecircuits | 예 | 
> | expressroutegateways | 예 | 
> | expressrouteserviceproviders | 예 | 
> | firewallpolicies | 예 |
> | frontdoors | 예 | 
> | ipallocations | 예 |
> | ipgroups | 예 |
> | loadbalancers | 예 <br/><br/> [Azure 리소스](../../resource-mover/tutorial-move-region-virtual-machines.md) 이동 기를 사용 하 여 내부 및 외부 부하 분산 장치를 이동 합니다. |
> | localnetworkgateways |  예 | 
> | natgateways |  예 | 
> | networkexperimentprofiles | 예 |
> | networkintentpolicies |  예 | 
> | networkinterfaces | 예 <br/><br/> [Azure 리소스](../../resource-mover/tutorial-move-region-virtual-machines.md) 이동 기를 사용 하 여 nic를 이동 합니다. | 
> | networkprofiles | 예 | 
> | networksecuritygroups | 예 <br/><br/> [Azure 리소스](../../resource-mover/tutorial-move-region-virtual-machines.md) 이동 기를 사용 하 여 네트워크 보안 그룹 (ngss)을 이동 합니다. | 
> | networkwatchers |  예 |  
> | networkwatchers / connectionmonitors |  예 | 
> | networkwatchers / flowlogs |  예 | 
> | networkwatchers / pingmeshes |  예 | 
> | p2svpngateways | 예 | 
> | privatednszones |  예 |  
> | privatednszones / virtualnetworklinks | 예 |> | privatednszonesinternal | 예 |
> | privateendpointredirectmaps | 예 |
> | privateendpoints | 예 | 
> | privatelinkservices | 예 | 
> | publicipaddresses | 예<br/><br/> [Azure 리소스](../../resource-mover/tutorial-move-region-virtual-machines.md) 이동 기를 사용 하 여 공용 IP 주소를 이동 합니다. |
> | publicipprefixes | 예 | 
> | routefilters | 예 | 
> | routetables |  예 | 
> | securitypartnerproviders | 예 |
> | serviceendpointpolicies |  예 | 
> | trafficmanagergeographichierarchies | 예 | 
> | trafficmanagerprofiles |  예 | 
> | trafficmanagerusermetricskeys | 예 |
> | virtualhubs | 예 | 
> | virtualnetworkgateways |  예 |  
> | virtualnetworks |  예 | 
> | virtualnetworktaps | 예 | 
> | virtualwans | 예 | 
> | vpngateways(Virtual WAN) | 예 | 
> | vpnsites(Virtual WAN) | 예 | 
> | vpnsites(Virtual WAN) | 예 |


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 네임스페이스 |  예 | 
> | namespaces / notificationhubs |  예 |  

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | osnamespaces | 예 | 

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | hypervsites | 예 | 
> | importsites | 예 | 
> | serversites | 예 | 
> | vmwaresites | 예 | 

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | clusters | 예 | 
> | deletedworkspaces | 예 | 
> | linktargets | 예 | 
> | storageinsightconfigs | 예 |
> | workspaces | 예 |



## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | managementassociations | 예 |
> | managementconfigurations |  예 | 
> | solutions | 예 |
> | 뷰 |  예 | 

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | legacypeerings | 예 | 
> | peerasns | 예 | 
> | peeringlocations | 예 | 
> | peerings | 예 | 
> | peeringservicecountries | 예 | 
> | peeringservicelocations | 예 | 
> | peeringserviceproviders | 예 | 
> | peeringservices | 예 | 

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | policyevents | 예 | 
> | policystates | 예 | 
> | policytrackedresources | 예 | 
> | remediations | 예 | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | consoles | 예 |
> | dashboards | 예 | 
> | usersettings | 예 | 


## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | workspacecollections |  예 | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | capacities |  예 | 

## <a name="microsoftpurview"></a>Microsoft.Purview

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 예 | 

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | availableaccounts | 예 | 
> | providerregistrations | 예 | 
> | rollouts | 예 | 

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | workspaces | 예 | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | replicationeligibilityresults | 예 |
> | vaults | 아니요.<br/><br/> Azure 지역에서 Azure Backup에 대 한 Recovery Services 자격 증명 모음 이동은 지원 되지 않습니다.<br/><br/> Azure Site Recovery에 대 한 Recovery Services 자격 증명 모음에서 대상 지역에 [자격 증명 모음을 사용 하지 않도록 설정 하 고 다시 만들](../../site-recovery/move-vaults-across-regions.md) 수 있습니다. | 

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | openshiftclusters | 예 | 

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 네임스페이스 |  예 | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 쿼리 |  예 |  
> | resourcechangedetails | 예 | 
> | resourcechanges | 예 | 
> | 리소스 | 예 | 
> | resourceshistory | 예 | 
> | subscriptionsstatus | 예 | 

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | childresources | 예 | 
> | emergingissues | 예 | 
> | events | 예 | 
> | metadata | 예 | 
> | 알림 | 예 | 

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 |
> | ------------- | ----------- |
> | deploymentScripts |  예<br/><br/>[Microsoft .Resources 리소스를 새 지역으로 이동](microsoft-resources-move-regions.md) |
> | templateSpecs |  예<br/><br/>[Microsoft .Resources 리소스를 새 지역으로 이동](microsoft-resources-move-regions.md) |  

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 애플리케이션 |  예 | 
> | saasresources | 예 | 


## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | resourcehealthmetadata | 예 |
> | searchservices |  예 | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | adaptivenetworkhardenings | 예 | 
> | advancedthreatprotectionsettings | 예 | 
> | 경고 | 예 | 
> | allowedconnections | 예 | 
> | applicationwhitelistings | 예 | 
> | assessmentmetadata | 예 | 
> | assessments | 예 | 
> | autodismissalertsrules | 예 | 
> | automations | 예 | 
> | autoprovisioningsettings | 예 |
> | complianceresults | 예 | 
> | compliances | 예 | 
> | datacollectionagents | 예 | 
> | devicesecuritygroups | 예 | 
> | discoveredsecuritysolutions | 예 | 
> | externalsecuritysolutions | 예 | 
> | informationprotectionpolicies | 예 | 
> | iotsecuritysolutions | 예 | 
> | iotsecuritysolutions / analyticsmodels | 예 | 
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | 예 | 
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | 예 | 
> | jitnetworkaccesspolicies | 예 | 
> | 정책 | 예 | 
> | pricings | 예 | 
> | regulatorycompliancestandards | 예 | 
> | regulatorycompliancestandards / regulatorycompliancecontrols | 예 | 
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | 예 | 
> | securitycontacts | 예 | 
> | securitysolutions | 예 | 
> | securitysolutionsreferencedata | 예 | 
> | securitystatuses | 예 | 
> | securitystatusessummaries | 예 | 
> | servervulnerabilityassessments | 예 | 
> | 설정 | 예 | 
> | subassessments | 예 |
> | tasks | 예 | 
> | topologies | 예 | 
> | workspacesettings | 예 | 

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | aggregations | 예 | 
> | alertrules | 예 | 
> | alertruletemplates | 예 | 
> | automationrules | 예 |
> | cases | 예 | 
> | dataconnectors | 예 | 
> | 엔터티 | 예 | 
> | entityqueries | 예 |
> | incidents | 예 | 
> | officeconsents | 예 | 
> | 설정 | 예 | 
> | threatintelligence | 예 | 

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | consoleservices | 예 | 

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | gateways | 예 | 
> | nodes | 예 | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 네임스페이스 |  예 | 
> | premiummessagingregions | 예 | 
> | sku | 예 | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 애플리케이션 | 예 | 
> | clusters |  예 |  
> | containergroups | 예 | 
> | containergroupsets | 예 | 
> | edgeclusters | 예 | 
> | managedclusters | 예 |
> | networks | 예 | 
> | secretstores | 예 | 
> | volumes | 예 | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 애플리케이션 |  예 | 
> | containergroups | 예 | 
> | gateways |  예 | 
> | networks |  예 | 
> | secrets |  예 | 
> | volumes |  예 |  

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | rollouts | 예 | 

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | signalr |  예 |  

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | hybridusebenefits | 예 | 

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | appliancedefinitions | 예 | 
> | appliances | 예 | 
> | jitrequests | 예 | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | instancepools | 예 | 
> | 위치 | 예 |
> | managedinstances | 예 <br/><br/> 여러 지역에서 관리 되는 인스턴스를 이동 하는 방법에 [대해 자세히 알아보세요](../../azure-sql/database/move-resources-across-regions.md) . | 
> | managedinstances / databases | 예 | 
> | servers | 예 | 
> | servers / databases | 예 <br/><br/> 여러 지역에서 데이터베이스를 이동 하는 방법에 [대해 자세히 알아보세요](../../azure-sql/database/move-resources-across-regions.md) .<br/><br/> Azure 리소스 이동 기를 사용 하 여 Azure SQL database를 이동 하는 방법에 [대해 자세히 알아보세요](../../resource-mover/tutorial-move-region-sql.md) .  | 
> | servers / elasticpools | 예 <br/><br/> 여러 지역에서 탄력적 풀을 이동 하는 방법에 [대해 자세히 알아보세요](../../azure-sql/database/move-resources-across-regions.md) .<br/><br/> Azure 리소스 이동 기를 사용 하 여 Azure SQL 탄력적 풀을 이동 하는 방법에 [대해 자세히 알아보세요](../../resource-mover/tutorial-move-region-sql.md) .  | 
> | virtualclusters | 예 | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  예 |  
> | sqlvirtualmachines |  예 |  


## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | storageaccounts | 예<br/><br/> [Azure Storage 계정을 다른 지역으로 이동](../../storage/common/storage-account-move.md) | 

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | caches | 예 | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | storagesyncservices |  예 | 

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | storagesyncservices | 예 | 

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | storagesyncservices | 예 | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | managers | 예 | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | clusters | 예 |
> | streamingjobs |  예 |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | environments | 예 | 
> | 인스턴스 | 예 | 

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | 구독 | 예 | 

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | services | 예 | 
> | supporttickets | 예 | 

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | workspaces | 예 | 
> | workspaces / bigdatapools | 예 | 
> | workspaces / sqlpools | 예 | 


## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | environments |  예 | 
> | environments / eventsources |  예 |  
> | environments / referencedatasets |  예 | 

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | stores | 예 | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | imagetemplates | 예 | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | account |  예 | 
> | account / extension |  예 | 
> | account / project |  예 | 

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | arczones | 예 | 
> | resourcepools | 예 | 
> | vcenters | 예 | 
> | virtualmachines | 예 | 
> | virtualmachinetemplates | 예 | 
> | virtualnetworks | 예 | 

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | 예 | 
> | dedicatedcloudservices | 예 | 
> | virtualmachines | 예 | 

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | devices | 예 | 
> | vnfs | 예 | 

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | 계정 | 예 | 
> | 플랜 | 예 | 
> | registeredsubscriptions | 예 |


## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | availablestacks | 예 | 
> | billingmeters | 예 | 
> | certificates | 예 | 
> | connectiongateways |  예 |  
> | connections |  예 |  
> | customapis |  예 | 
> | deletedsites | 예 | 
> | deploymentlocations | 예 | 
> | georegions | 예 | 
> | hostingenvironments | 예 | 
> | kubeenvironments | 예 | 
> | publishingusers | 예 |
> | 동영상 추천 기능 | 예 | 
> | resourcehealthmetadata | 예 | 
> | runtimes | 예 | 
> | serverfarms | 예 |  
> | serverfarms / eventgridfilters | N
> | sites |  예 | 
> | sites / premieraddons |  예 |  
> | sites / slots |  예 |  
> | sourcecontrols | 예 |
> | staticsites | 예 | 

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | multipleactivationkeys | 예 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | deviceservices | 예 | 

## <a name="microsoftworkloadbuilder"></a>WorkloadBuilder

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | 작업 | 예 | 

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | components | 예 |
> | componentssummary | 예 | 
> | monitorinstances | 예 | 
> | monitorinstancessummary | 예 | 
> | monitors | 예 | 
## <a name="third-party-services"></a>타사 서비스

타사 서비스는 현재 이동 작업을 지원하지 않습니다.

## <a name="next-steps"></a>다음 단계

리소스 이동 서비스에 대해 자세히 [알아보세요](../../resource-mover/overview.md) .

