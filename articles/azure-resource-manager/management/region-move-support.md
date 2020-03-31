---
title: 지역 간 Azure 리소스 이동 지원
description: Azure 리전에서 이동할 수 있는 Azure 리소스 형식을 나열합니다.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 01/20/2020
ms.author: raynew
ms.openlocfilehash: 9bc7dc66ccf3049ac878f7871c816e5ade1afde5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760711"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>지역 간 Azure 리소스 이동 지원

이 문서에서는 Azure 리소스 유형이 다른 Azure 지역으로 이동하기 위해 지원되는지 여부를 확인합니다. 

리소스 공급자 네임스페이스로 이동합니다.
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [마이크로소프트.앱구성](#microsoftappconfiguration)
> - [Microsoft.AppService](#microsoftappservice)
> - [마이크로소프트.권한 부여](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [마이크로소프트.Azure데이터](#microsoftazuredata)
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
> - [마이크로소프트.클래식네트워크](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [마이크로소프트.컴퓨트](#microsoftcompute)
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [마이크로소프트.사용자 지정 공급자](#microsoftcustomproviders)
> - [마이크로소프트.데이터박스](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
> - [마이크로소프트.데이터레이크애널리틱스](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [마이크로소프트.데이터 쉐어](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [마이크로소프트.디바이스](#microsoftdevices)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [마이크로소프트.엔터프라이즈 지식그래프](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [마이크로소프트.헬스케어아피](#microsofthealthcareapis)
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
> - [마이크로소프트.마이크로 서비스4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [마이크로소프트 네트워크](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [마이크로소프트.오퍼랙타인사이트](#microsoftoperationalinsights)
> - [마이크로소프트.오퍼레이션매니지먼트](#microsoftoperationsmanagement)
> - [마이크로소프트.피어링](#microsoftpeering)
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
> - [마이크로소프트.검색](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [마이크로소프트 Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [마이크로소프트.스토리지캐시](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [마이크로소프트 토큰](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [마이크로소프트.비주얼 스튜디오](#microsoftvisualstudio)
> - [마이크로소프트.VM웨어클라우드심플](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [마이크로소프트.윈도우버추얼데스크톱](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- | 
> | domainservices | 예 | 
> | 도메인 서비스 / 복제 세트 | 예 | 

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | tenants | 예 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 작업 규칙 | 예 | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | servers | 예 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 서비스 |  yes | 

## <a name="microsoftappconfiguration"></a>마이크로소프트.앱구성

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 구성 저장소 | 예 | 

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | apiapps | 예 | 
> | appidentities | 예 | 
> | gateways | 예 | 


## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | policyassignments | 예 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | automationaccounts | 예 | 
> | 자동화 계정 / 구성 | 예 | 
> | 자동화 계정 / 런북 | 예 | 



## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | b2cdirectories | 예 | 

## <a name="microsoftazuredata"></a>마이크로소프트.Azure데이터

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | sqlserver 등록 | 예 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | registrations | 예 | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | batchaccounts | 예 |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | clusters | 예 | 
> | fileservers | 예 | 
> | jobs | 예 | 
> | workspaces | 예 | 

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
> | blockchainmembers | 예 |
> | 전문가 | 예 | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | blueprintassignments | 예 | 

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


## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | cdnwebapplication방화벽정책 | 예 |
> | 프로필 | 예 | 
> | 프로파일 / 끝점 | 예 | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | certificateorders | 예 | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | domainnames | 예 |  
> | virtualmachines | 예 | 



## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | networksecuritygroups | 예 |
> | reservedips | 예 | 
> | virtualnetworks | 예 | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | storageaccounts | yes |  


## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 예 | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | availabilitysets | 예 | 
> | 디스크 암호화 세트 | 예 | 
> | disks | 예 | 
> | galleries | 예 | 
> | 갤러리 / 이미지 | 예 | 
> | 갤러리 / 이미지 / 버전 | 예 | 
> | 호스트 그룹 | 예 | 
> | 호스트 그룹 / 호스트 | 예 | 
> | images | 예 | 
> | proximityplacementgroups | 예 | 
> | restorepointcollections | 예 | 
> | sharedvmimages | 예 | 
> | 공유vmimages / 버전 | 예 | 
> | 스냅샷 | 예 | 
> | virtualmachines | yes | 
> | 가상 머신/ 확장 | 예 | 
> | virtualmachinescalesets | 예 | 

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | containergroups | 예 | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | containergroups | 예 | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | registries | 예 |  
> | 레지스트리 / 빌드 작업 | 예 |  
> | 레지스트리 / 복제 | 예 | 
> | 레지스트리 / 작업 | 예 |  
> | 레지스트리 / 웹 후크 | 예 | 

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
> | 커넥터 | 예 |  

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | hubs | 예 |  

## <a name="microsoftcustomproviders"></a>마이크로소프트.사용자 지정 공급자

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 리소스 공급자 | 예 | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | jobs | 예 | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
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
> | 데이터 카탈로그 | 예 | 

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
> | 서비스 / 프로젝트 | 예 | 
> | slots | 예 | 

## <a name="microsoftdatashare"></a>마이크로소프트.데이터 쉐어

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 예 | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | servers | 예 |  

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | servers | 예 |  

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | servergroups | 예 | 
> | servers | 예 |  
> | 서버v2 | 예 | 

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | artifactsources | 예 | 
> | rollouts | 예 |  
> | servicetopologies | 예 | 
> | 서비스토폴로지/ 서비스 | 예 |  
> | 서비스토폴로지 / 서비스 / 서비스 단위 | 예 | 
> | 단계 | 예 | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | elasticpools | 예 | 
> | 탄성 풀 / iothubtenants | 예 | 
> | iothubs | yes | 
> | provisioningservices | 예 | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | controllers | 예 | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | labcenters | 예 | 
> | labs | 예 | 
> | 실험실 / 환경 | 예 |  
> | 실험실 / 서비스 러너 | 예 | 
> | 랩 / 가상 머신 | 예 |  
> | schedules | 예 |  

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | databaseaccounts | 예 | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | domains | 예 | 

## <a name="microsoftenterpriseknowledgegraph"></a>마이크로소프트.엔터프라이즈 지식그래프

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | services | 예 |  

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | domains | 예 |  
> | topics | 예 | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | clusters | 예 |  
> | 네임스페이스 | 예 | 

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 예 | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | hanainstances | 예 | 
> | 삽 모니터 | 예 |  

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | clusters | 예 | 

## <a name="microsofthealthcareapis"></a>마이크로소프트.헬스케어아피

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | services | 예 |  

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 기계 | 예 | 

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | datamanagers |  예 | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | jobs |  예 | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 예 | 
> | actiongroups |  예 | 
> | activitylogalerts | 예 | 
> | alertrules |  예 | 
> | autoscalesettings |  예 | 
> | components |  예 |  
> | guestdiagnosticsettings | 예 | 
> | metricalerts | 예 | 
> | notificationgroups | 예 | 
> | notificationrules | 예 | 
> | scheduledqueryrules |  예 | 
> | webtests |  예 | 
> | workbooks |  예 |  


## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | iotapps |  예 |  

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | checknameavailability |  예 |  
> | graph |  예 | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | hsmpools | 예 | 
> | vaults |  예 | 


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

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 예 | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 예 | 

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | hostingenvironments | 예 | 
> | integrationaccounts |  예 |  
> | integrationserviceenvironments | 예 | 
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
> | 계정 / 작업 영역 | 예 | 
> | 계정 / 작업 영역 / 프로젝트 | 예 | 
> | teamaccounts | 예 | 
> | 팀 계정 / 작업 영역 | 예 | 
> | 팀 계정 / 작업 영역 / 프로젝트 | 예 | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 예 | 

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | hostingaccounts | 예 | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | workspaces | 예 | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | userassignedidentities | 예 | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 |  예 |  

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | classicdevservices | 예 | 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | mediaservices |  예 | 
> | 미디어 서비스 / 라이브 이벤트 |  예 | 
> | 미디어 서비스 / 스트리밍 엔드포인트 |  예 | 

## <a name="microsoftmicroservices4spring"></a>마이크로소프트.마이크로 서비스4Spring

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 앱 클러스터 | 예 | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | assessmentprojects | 예 | 
> | migrateprojects | 예 | 
> | projects | 예 | 

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | netappaccounts | 예 | 
> | 넷앱계정 / 용량 풀 | 예 | 
> | 넷앱계정 / 용량 풀 / 볼륨 | 예 | 
> | 넷앱계정 / 용량 풀 / 볼륨 / 마운트 타겟 | 예 | 
> | 넷앱계정 / 용량 풀 / 볼륨 / 스냅 샷 | 예 | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | applicationgateways | 예 | 
> | 응용 프로그램 게이트웨이 웹 응용 프로그램 방화벽 정책 | 예 | 
> | applicationsecuritygroups |  예 |  
> | azurefirewalls |  예 |  
> | bastionhosts | 예 | 
> | connections |  예 | 
> | ddoscustompolicies |  예 | 
> | ddosprotectionplans | 예 | 
> | dnszones |  예 | 
> | expressroutecircuits | 예 | 
> | expressroutecrossconnections | 예 | 
> | expressroutegateways | 예 | 
> | expressrouteports | 예 | 
> | frontdoors | 예 | 
> | frontdoorwebapplicationfirewallpolicies | 예 | 
> | loadbalancers | 예 - 기본 SKU<br>아니오 - 표준 SKU | 예 - 기본 SKU<br> - 예 표준 SKU |
> | localnetworkgateways |  예 | 
> | natgateways |  예 | 
> | networkintentpolicies |  예 | 
> | networkinterfaces | yes | 
> | networkprofiles | 예 | 
> | networksecuritygroups | yes | 
> | networkwatchers |  예 |  
> | 네트워크 감시자 / 연결 모니터 |  예 | 
> | 네트워크 감시자 / 렌즈 |  예 | 
> | 네트워크 감시자 / 핑메시 |  예 | 
> | p2svpngateways | 예 | 
> | 프라이빗 스존 |  예 |  
> | 프라이빗스존 / 가상네트워크링크 |  예 |  
> | 프라이빗 엔드포인트 | 예 | 
> | privatelinkservices | 예 | 
> | publicipaddresses | 예 - 기본 SKU<br>아니오 - 표준 SKU | 예 - 기본 SKU<br>아니오 - 표준 SKU |
> | publicipprefixes | 예 | 
> | routefilters | 예 | 
> | routetables |  예 | 
> | serviceendpointpolicies |  예 | 
> | trafficmanagerprofiles |  예 | 
> | virtualhubs | 예 | 
> | virtualnetworkgateways |  예 |  
> | virtualnetworks |  예 | 
> | virtualnetworktaps | 예 | 
> | virtualwans | 예 | 
> | vpn게이트웨이(가상 WAN) | 예 | 
> | VPN 사이트(가상 WAN) | 예 | 
> | webapplicationfirewallpolicies |  예 | 


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 네임스페이스 |  예 | 
> | 네임스페이스 / 알림 허브 |  예 |  

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | workspaces |  예 | 



## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | managementconfigurations |  예 | 
> | 뷰 |  예 | 

## <a name="microsoftpeering"></a>마이크로소프트.피어링

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 피어링 | 예 | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | dashboards | 예 | 

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | rootresources | 예 | 

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

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 계정 | 예 | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | vaults | 아니요. [사이트 복구를](https://docs.microsoft.com/azure/site-recovery/move-vaults-across-regions) 위해 볼트 를 비활성화하고 다시 만들기  | 


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

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 애플리케이션 |  예 | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | flows |  예 |  
> | jobcollections |  예 | 

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | searchservices |  예 | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | iot보안솔루션 |  예 | 
> | 플레이북 구성 | 예 | 

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

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 애플리케이션 | 예 | 
> | clusters |  예 | 
> | 클러스터 / 응용 프로그램 | 예 | 
> | containergroups | 예 | 
> | containergroupsets | 예 | 
> | edgeclusters | 예 | 
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

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | signalr |  예 |  

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | appliancedefinitions | 예 | 
> | appliances | 예 | 
> | applicationdefinitions | 예 | 
> | 애플리케이션 | 예 | 
> | jitrequests | 예 | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 인스턴스 풀 | 예 | 
> | managedinstances | yes | 
> | 관리되는 인스턴스 / 데이터베이스 | yes | 
> | servers | yes | 
> | 서버 / 데이터베이스 | yes | 
> | 서버 / 탄성 풀 | yes | 
> | virtualclusters | yes | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  예 |  
> | sqlvirtualmachines |  예 |  

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | dwvm | 예 | 

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | storageaccounts | yes | 

## <a name="microsoftstoragecache"></a>마이크로소프트.스토리지캐시

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 캐시 | 예 | 

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
> | streamingjobs |  예 |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | environments | 예 | 
> | 환경 / 이벤트 소스 | 예 | 
> | 인스턴스 | 예 | 
> | 인스턴스/ 환경 | 예 | 
> | 인스턴스 / 환경 / 이벤트 소스 | 예 | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | providerregistrations | 예 | 
> | 리소스 | 예 | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | environments |  예 | 
> | 환경 / 이벤트 소스 |  예 |  
> | 환경 / 참조 데이터 집합 |  예 | 

## <a name="microsofttoken"></a>마이크로소프트 토큰

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
> | 계정 / 확장 |  예 | 
> | 계정 / 프로젝트 |  예 | 



## <a name="microsoftvmwarecloudsimple"></a>마이크로소프트.VM웨어클라우드심플

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 전용 클라우드 노드 | 예 | 
> | 전용 클라우드 서비스 | 예 | 
> | virtualmachines | 예 | 

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | certificates | 예 | 
> | connectiongateways |  예 |  
> | connections |  예 |  
> | customapis |  예 | 
> | hostingenvironments | 예 | 
> | serverfarms |  예 |  
> | sites |  예 | 
> | 사이트 / 프리미어 애드온 |  예 |  
> | 사이트 / 슬롯 |  예 |  


## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | deviceservices | 예 | 

## <a name="microsoftwindowsvirtualdesktop"></a>마이크로소프트.윈도우버추얼데스크톱

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 영역 이동 | 
> | ------------- | ----------- |
> | 응용 프로그램 그룹 | 예 | 
> | 호스트 풀 | 예 | 
> | workspaces | 예 | 

## <a name="third-party-services"></a>타사 서비스

타사 서비스는 현재 이동 작업을 지원하지 않습니다.
