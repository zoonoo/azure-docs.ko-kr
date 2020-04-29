---
title: 지역 간 Azure 리소스 이동에 대 한 지원
description: Azure 지역 간에 이동할 수 있는 Azure 리소스 종류를 나열 합니다.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 01/20/2020
ms.author: raynew
ms.openlocfilehash: 9bc7dc66ccf3049ac878f7871c816e5ade1afde5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76760711"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>지역 간 Azure 리소스 이동에 대 한 지원

이 문서에서는 Azure 리소스 종류가 다른 Azure 지역으로 이동 하는 데 지원 되는지 여부를 확인 합니다. 

리소스 공급자 네임 스페이스로 이동 합니다.
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft 인증](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.classicnetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft Compute](#microsoftcompute)
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft CustomProviders](#microsoftcustomproviders)
> - [DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
> - [DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft. 장치](#microsoftdevices)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft 네트워크](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.operationsmanagement](#microsoftoperationsmanagement)
> - [Microsoft 피어 링](#microsoftpeering)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft 검색](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft .Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft 토큰](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [visualstudio](#microsoftvisualstudio)
> - [VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | domainservices | 아니요 | 
> | domainservices/replicasets | 아니요 | 

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | tenants | 아니요 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | actionrules | 아니요 | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | servers | 아니요 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 서비스 |  예 | 

## <a name="microsoftappconfiguration"></a>Microsoft AppConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | configurationstores | 아니요 | 

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | apiapps | 아니요 | 
> | appidentities | 아니요 | 
> | gateways | 아니요 | 


## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | policyassignments | 아니요 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | automationaccounts | 아니요 | 
> | automationaccounts/구성 | 아니요 | 
> | automationaccounts/runbook | 아니요 | 



## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | b2cdirectories | 아니요 | 

## <a name="microsoftazuredata"></a>Microsoft AzureData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | sqlserverregistrations | 아니요 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | registrations | 아니요 | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | batchaccounts | 아니요 |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | clusters | 아니요 | 
> | fileservers | 아니요 | 
> | jobs | 아니요 | 
> | workspaces | 아니요 | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | mapapis | 아니요 | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | biztalk | 아니요 | 

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | blockchainmembers | 아니요 |
> | 감시자 | 아니요 | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | blueprintassignments | 아니요 | 

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | botservices | 아니요 | 

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | redis | 아니요 | 


## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | 아니요 |
> | profiles | 아니요 | 
> | 프로필/끝점 | 아니요 | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | certificateorders | 아니요 | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | domainnames | 아니요 |  
> | virtualmachines | 아니요 | 



## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | networksecuritygroups | 아니요 |
> | reservedips | 아니요 | 
> | virtualnetworks | 아니요 | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | storageaccounts | 예 |  


## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 아니요 | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | availabilitysets | 아니요 | 
> | disk; 집합 | 아니요 | 
> | disks | 아니요 | 
> | galleries | 아니요 | 
> | 갤러리/이미지 | 아니요 | 
> | 갤러리/이미지/버전 | 아니요 | 
> | 호스트 그룹 | 아니요 | 
> | 호스트 그룹/호스트 | 아니요 | 
> | images | 아니요 | 
> | proximityplacementgroups | 아니요 | 
> | restorepointcollections | 아니요 | 
> | sharedvmimages | 아니요 | 
> | sharedvmimages/버전 | 아니요 | 
> | 스냅샷 | 아니요 | 
> | virtualmachines | 예 | 
> | virtualmachines/확장 | 아니요 | 
> | virtualmachinescalesets | 아니요 | 

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | containergroups | 아니요 | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | containergroups | 아니요 | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | registries | 아니요 |  
> | 레지스트리/buildtasks | 아니요 |  
> | 레지스트리/복제 | 아니요 | 
> | 레지스트리/작업 | 아니요 |  
> | 레지스트리/웹 후크 | 아니요 | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | containerservices | 아니요 | 
> | managedclusters | 아니요 | 
> | openshiftmanagedclusters | 아니요 | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 애플리케이션 | 아니요 | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 아니요 | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 커넥터 | 아니요 |  

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | hubs | 아니요 |  

## <a name="microsoftcustomproviders"></a>Microsoft CustomProviders

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | resourceproviders | 아니요 | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | jobs | 아니요 | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | databoxedgedevices | 아니요 | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | workspaces | 아니요 | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | catalogs | 아니요 | 
> | datacatalogs | 아니요 | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | connectionmanagers | 아니요 | 

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 패키지 | 아니요 | 
> | 플랜 | 아니요 | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | datafactories | 아니요 | 
> | factories | 아니요 |  

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | datalakeaccounts | 아니요 | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 아니요 | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 아니요 | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | services | 아니요 | 
> | 서비스/프로젝트 | 아니요 | 
> | slots | 아니요 | 

## <a name="microsoftdatashare"></a>DataShare

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 아니요 | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | servers | 아니요 |  

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | servers | 아니요 |  

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | servergroups | 아니요 | 
> | servers | 아니요 |  
> | serversv2 | 아니요 | 

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | artifactsources | 아니요 | 
> | rollouts | 아니요 |  
> | servicetopologies | 아니요 | 
> | servicetopologies/서비스 | 아니요 |  
> | servicetopologies/서비스/serviceunits | 아니요 | 
> | 단계 | 아니요 | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | elasticpools | 아니요 | 
> | elasticpools / iothubtenants | 아니요 | 
> | iothubs | 예 | 
> | provisioningservices | 아니요 | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | controllers | 아니요 | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | labcenters | 아니요 | 
> | labs | 아니요 | 
> | 랩/환경 | 아니요 |  
> | labs/servicerunners | 아니요 | 
> | labs/virtualmachines | 아니요 |  
> | schedules | 아니요 |  

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | databaseaccounts | 아니요 | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | domains | 아니요 | 

## <a name="microsoftenterpriseknowledgegraph"></a>EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | services | 아니요 |  

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | domains | 아니요 |  
> | topics | 아니요 | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | clusters | 아니요 |  
> | 네임스페이스 | 아니요 | 

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 아니요 | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | hanainstances | 아니요 | 
> | sapmonitors | 아니요 |  

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | clusters | 아니요 | 

## <a name="microsofthealthcareapis"></a>HealthcareApis

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | services | 아니요 |  

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 머신의 | 아니요 | 

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | datamanagers |  아니요 | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | jobs |  아니요 | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 아니요 | 
> | actiongroups |  아니요 | 
> | activitylogalerts | 아니요 | 
> | alertrules |  아니요 | 
> | autoscalesettings |  아니요 | 
> | components |  아니요 |  
> | guestdiagnosticsettings | 아니요 | 
> | metricalerts | 아니요 | 
> | notificationgroups | 아니요 | 
> | notificationrules | 아니요 | 
> | scheduledqueryrules |  아니요 | 
> | webtests |  아니요 | 
> | workbooks |  아니요 |  


## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | iotapps |  아니요 |  

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | checknameavailability |  아니요 |  
> | graph |  아니요 | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | hsmpools | 아니요 | 
> | vaults |  아니요 | 


## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | clusters |  아니요 |  

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | labaccounts | 아니요 | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 아니요 | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 아니요 | 

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | hostingenvironments | 아니요 | 
> | integrationaccounts |  아니요 |  
> | integrationserviceenvironments | 아니요 | 
> | isolatedenvironments | 아니요 | 
> | workflows |  아니요 |  

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | commitmentplans |  아니요 | 
> | webservices |  아니요 | 
> | workspaces |  아니요 | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | operationalizationclusters |  아니요 | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 아니요 | 
> | 계정/작업 영역 | 아니요 | 
> | 계정/작업 영역/프로젝트 | 아니요 | 
> | teamaccounts | 아니요 | 
> | teamaccounts/작업 영역 | 아니요 | 
> | teamaccounts/workspace/projects | 아니요 | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 아니요 | 

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | hostingaccounts | 아니요 | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | workspaces | 아니요 | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | userassignedidentities | 아니요 | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 |  아니요 |  

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | classicdevservices | 아니요 | 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | mediaservices |  아니요 | 
> | windowsazure.mediaservices/liveevents |  아니요 | 
> | windowsazure.mediaservices/streamingendpoints |  아니요 | 

## <a name="microsoftmicroservices4spring"></a>Microservices4Spring

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | appclusters | 아니요 | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | assessmentprojects | 아니요 | 
> | migrateprojects | 아니요 | 
> | projects | 아니요 | 

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | netappaccounts | 아니요 | 
> | netappaccounts/capacitypools | 아니요 | 
> | netappaccounts/capacitypools/볼륨 | 아니요 | 
> | netappaccounts/capacitypools/볼륨/mounttargets | 아니요 | 
> | netappaccounts/capacitypools/볼륨/스냅숏 | 아니요 | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | applicationgateways | 아니요 | 
> | applicationgatewaywebapplicationfirewallpolicies | 아니요 | 
> | applicationsecuritygroups |  아니요 |  
> | azurefirewalls |  아니요 |  
> | bastionhosts | 아니요 | 
> | connections |  아니요 | 
> | ddoscustompolicies |  아니요 | 
> | ddosprotectionplans | 아니요 | 
> | dnszones |  아니요 | 
> | expressroutecircuits | 아니요 | 
> | expressroutecrossconnections | 아니요 | 
> | expressroutegateways | 아니요 | 
> | expressrouteports | 아니요 | 
> | frontdoors | 아니요 | 
> | frontdoorwebapplicationfirewallpolicies | 아니요 | 
> | loadbalancers | 예-기본 SKU<br>안 함-표준 SKU | 예-기본 SKU<br> -예 표준 SKU |
> | localnetworkgateways |  아니요 | 
> | natgateways |  아니요 | 
> | networkintentpolicies |  아니요 | 
> | networkinterfaces | 예 | 
> | networkprofiles | 아니요 | 
> | networksecuritygroups | 예 | 
> | networkwatchers |  아니요 |  
> | networkwatchers/connectionmonitors |  아니요 | 
> | networkwatchers/lenses |  아니요 | 
> | networkwatchers/ |  아니요 | 
> | p2svpngateways | 아니요 | 
> | privatednszones |  아니요 |  
> | privatednszones / virtualnetworklinks |  아니요 |  
> | privateendpoints | 아니요 | 
> | privatelinkservices | 아니요 | 
> | publicipaddresses | 예-기본 SKU<br>안 함-표준 SKU | 예-기본 SKU<br>안 함-표준 SKU |
> | publicipprefixes | 아니요 | 
> | routefilters | 아니요 | 
> | routetables |  아니요 | 
> | serviceendpointpolicies |  아니요 | 
> | trafficmanagerprofiles |  아니요 | 
> | virtualhubs | 아니요 | 
> | virtualnetworkgateways |  아니요 |  
> | virtualnetworks |  아니요 | 
> | virtualnetworktaps | 아니요 | 
> | virtualwans | 아니요 | 
> | vpngateways (가상 WAN) | 아니요 | 
> | vpnsites (가상 WAN) | 아니요 | 
> | webapplicationfirewallpolicies |  아니요 | 


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 네임스페이스 |  아니요 | 
> | 네임 스페이스/notificationhubs |  아니요 |  

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | workspaces |  아니요 | 



## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | managementconfigurations |  아니요 | 
> | 뷰 |  아니요 | 

## <a name="microsoftpeering"></a>Microsoft 피어 링

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 피어 링 | 아니요 | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | dashboards | 아니요 | 

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | rootresources | 아니요 | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | workspacecollections |  아니요 | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | capacities |  아니요 | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 아니요 | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | vaults | 아니요. [자격 증명 모음을 사용 하지 않도록 설정 하 고 Site Recovery 다시 만들기](https://docs.microsoft.com/azure/site-recovery/move-vaults-across-regions)  | 


## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 네임스페이스 |  아니요 | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 쿼리 |  아니요 |  

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 애플리케이션 |  아니요 | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | flows |  아니요 |  
> | jobcollections |  아니요 | 

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | searchservices |  아니요 | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | iotsecuritysolutions |  아니요 | 
> | playbookconfigurations | 아니요 | 

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | gateways | 아니요 | 
> | nodes | 아니요 | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 네임스페이스 |  아니요 | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 애플리케이션 | 아니요 | 
> | clusters |  아니요 | 
> | 클러스터/응용 프로그램 | 아니요 | 
> | containergroups | 아니요 | 
> | containergroupsets | 아니요 | 
> | edgeclusters | 아니요 | 
> | networks | 아니요 | 
> | secretstores | 아니요 | 
> | volumes | 아니요 | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 애플리케이션 |  아니요 | 
> | containergroups | 아니요 | 
> | gateways |  아니요 | 
> | networks |  아니요 | 
> | secrets |  아니요 | 
> | volumes |  아니요 |  

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | signalr |  아니요 |  

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | appliancedefinitions | 아니요 | 
> | appliances | 아니요 | 
> | applicationdefinitions | 아니요 | 
> | 애플리케이션 | 아니요 | 
> | jitrequests | 아니요 | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | instancepools | 아니요 | 
> | managedinstances | 예 | 
> | managedinstances/데이터베이스 | 예 | 
> | servers | 예 | 
> | 서버/데이터베이스 | 예 | 
> | servers/elasticpools | 예 | 
> | virtualclusters | 예 | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  아니요 |  
> | sqlvirtualmachines |  아니요 |  

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | dwvm | 아니요 | 

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | storageaccounts | 예 | 

## <a name="microsoftstoragecache"></a>StorageCache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 캐시 | 아니요 | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | storagesyncservices |  아니요 | 

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | storagesyncservices | 아니요 | 

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | storagesyncservices | 아니요 | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | managers | 아니요 | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | streamingjobs |  아니요 |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | environments | 아니요 | 
> | 환경/s s o 원본 | 아니요 | 
> | 인스턴스 | 아니요 | 
> | 인스턴스/환경 | 아니요 | 
> | 인스턴스/환경/s s o 원본 | 아니요 | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | providerregistrations | 아니요 | 
> | 리소스 | 아니요 | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | environments |  아니요 | 
> | 환경/s s o 원본 |  아니요 |  
> | environment/referencedatasets |  아니요 | 

## <a name="microsofttoken"></a>Microsoft 토큰

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | stores | 아니요 | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | imagetemplates | 아니요 | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | account |  아니요 | 
> | 계정/확장 |  아니요 | 
> | 계정/프로젝트 |  아니요 | 



## <a name="microsoftvmwarecloudsimple"></a>VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | 아니요 | 
> | dedicatedcloudservices | 아니요 | 
> | virtualmachines | 아니요 | 

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | certificates | 아니요 | 
> | connectiongateways |  아니요 |  
> | connections |  아니요 |  
> | customapis |  아니요 | 
> | hostingenvironments | 아니요 | 
> | serverfarms |  아니요 |  
> | sites |  아니요 | 
> | 사이트/premieraddons |  아니요 |  
> | 사이트/슬롯 |  아니요 |  


## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | deviceservices | 아니요 | 

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | applicationgroups | 아니요 | 
> | hostpools | 아니요 | 
> | workspaces | 아니요 | 

## <a name="third-party-services"></a>타사 서비스

타사 서비스는 현재 이동 작업을 지원하지 않습니다.
