---
title: Azure 리소스 종류별 이동 작업 지원
description: 새 리소스 그룹이나 구독으로 이동할 수 있는 Azure 리소스 종류를 소개합니다.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 10/24/2019
ms.author: tomfitz
ms.openlocfilehash: ba594a2bc0f0cb50eb515a24255a3f9ad56a10a3
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931885"
---
# <a name="move-operation-support-for-resources"></a>리소스에 대한 이동 작업 지원
이 문서에서는 Azure 리소스 종류가 이동 작업을 지원하는지 여부에 대한 정보를 제공합니다. 또한 리소스를 이동할 때 고려해 야 할 특수 조건에 대 한 정보를 제공 합니다.

리소스 공급자 네임 스페이스로 이동 합니다.
> [!div class="op_single_selector"]
> - [Microsoft AAD](#microsoftaad)
> - [aadiam](#microsoftaadiam)
> - [AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.analysisservices.sharepoint.integration.dll](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft AppConfiguration](#microsoftappconfiguration)
> - [AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft AzureData](#microsoftazuredata)
> - [Microsoft AzureStack](#microsoftazurestack)
> - [Microsoft Batch](#microsoftbatch)
> - [Microsoft BatchAI](#microsoftbatchai)
> - [Microsoft. BingMaps](#microsoftbingmaps)
> - [BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft Blockchain](#microsoftblockchain)
> - [Microsoft 청사진](#microsoftblueprint)
> - [BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Cdn](#microsoftcdn)
> - [CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.classiccompute](#microsoftclassiccompute)
> - [Microsoft.classicnetwork](#microsoftclassicnetwork)
> - [ClassicStorage](#microsoftclassicstorage)
> - [Cognitiveservices account](#microsoftcognitiveservices)
> - [Microsoft Compute](#microsoftcompute)
> - [Microsoft 컨테이너](#microsoftcontainer)
> - [ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft ContentModerator](#microsoftcontentmoderator)
> - [CortanaAnalytics](#microsoftcortanaanalytics)
> - [CostManagement](#microsoftcostmanagement)
> - [Microsoft CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft CustomProviders](#microsoftcustomproviders)
> - [DataBox](#microsoftdatabox)
> - [DataBoxEdge](#microsoftdataboxedge)
> - [Databricks](#microsoftdatabricks)
> - [Microsoft.datacatalog](#microsoftdatacatalog)
> - [DataConnect](#microsoftdataconnect)
> - [DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [DataLake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.datamigration](#microsoftdatamigration)
> - [DataShare](#microsoftdatashare)
> - [DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft DBforMySQL](#microsoftdbformysql)
> - [DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft DomainRegistration](#microsoftdomainregistration)
> - [EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Genomics](#microsoftgenomics)
> - [HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [HealthcareApis](#microsofthealthcareapis)
> - [HybridCompute](#microsofthybridcompute)
> - [HybridData](#microsofthybriddata)
> - [Microsoft ImportExport](#microsoftimportexport)
> - [microsoft insights](#microsoftinsights)
> - [Microsoft. IoTCentral](#microsoftiotcentral)
> - [Microsoft. IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft. Kusto](#microsoftkusto)
> - [Microsoft. 서비스](#microsoftlabservices)
> - [LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [MachineLearning](#microsoftmachinelearning)
> - [MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.machinelearningexperimentation](#microsoftmachinelearningexperimentation)
> - [MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.managedidentity](#microsoftmanagedidentity)
> - [Microsoft Maps](#microsoftmaps)
> - [MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft 마이그레이션](#microsoftmigrate)
> - [Microsoft NetApp](#microsoftnetapp)
> - [Microsoft 네트워크](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.operationsmanagement](#microsoftoperationsmanagement)
> - [Microsoft 피어 링](#microsoftpeering)
> - [Microsoft 포털](#microsoftportal)
> - [PortalSdk](#microsoftportalsdk)
> - [Microsoft PowerBI](#microsoftpowerbi)
> - [Microsoft. PowerBIDedicated](#microsoftpowerbidedicated)
> - [ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft Relay](#microsoftrelay)
> - [Microsoft ResourceGraph](#microsoftresourcegraph)
> - [Microsoft SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [ServiceFabricMesh](#microsoftservicefabricmesh)
> - [SignalRService](#microsoftsignalrservice)
> - [Microsoft 솔루션](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft. SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [StorageCache](#microsoftstoragecache)
> - [Microsoft.storagesync](#microsoftstoragesync)
> - [Microsoft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft StorSimple](#microsoftstorsimple)
> - [Microsoft StreamAnalytics](#microsoftstreamanalytics)
> - [StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft TerraformOSS](#microsoftterraformoss)
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft 토큰](#microsofttoken)
> - [Microsoft VirtualMachineImages](#microsoftvirtualmachineimages)
> - [visualstudio](#microsoftvisualstudio)
> - [VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft WindowsIoT](#microsoftwindowsiot)
> - [Microsoft WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domainservices | 아닙니다. | 아닙니다. |
> | domainservices/replicasets | 아닙니다. | 아닙니다. |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | tenants | 아닙니다. | 아닙니다. |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | actionrules | yes | yes |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 서버 | yes | yes |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 서비스 | yes | yes |

## <a name="microsoftappconfiguration"></a>Microsoft AppConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | configurationstores | yes | yes |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | apiapps | 아닙니다. | 아닙니다. |
> | appidentities | 아닙니다. | 아닙니다. |
> | gateways | 아닙니다. | 아닙니다. |

> [!IMPORTANT]
> [App Service 이동 가이드](./move-limitations/app-service-move-limitations.md)를 참조 하세요.

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | policyassignments | 아닙니다. | 아닙니다. |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | automationaccounts | yes | yes |
> | automationaccounts/구성 | yes | yes |
> | automationaccounts/runbook | yes | yes |

> [!IMPORTANT]
> Runbook은 Automation 계정과 동일한 리소스 그룹에 있어야 합니다.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | yes | yes |

## <a name="microsoftazuredata"></a>Microsoft AzureData

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | sqlserverregistrations | 아닙니다. | 아닙니다. |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | registrations | yes | yes |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | batchaccounts | yes | yes |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | 아닙니다. | 아닙니다. |
> | fileservers | 아닙니다. | 아닙니다. |
> | jobs | 아닙니다. | 아닙니다. |
> | workspaces | 아닙니다. | 아닙니다. |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | mapapis | 아닙니다. | 아닙니다. |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | BizTalk | yes | yes |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | yes | yes |
> | 감시자 | 아닙니다. | 아닙니다. |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | 아닙니다. | 아닙니다. |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | botservices | yes | yes |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | redis | yes | yes |

> [!IMPORTANT]
> Azure Cache for Redis 인스턴스가 가상 네트워크를 사용 하 여 구성 된 경우 인스턴스를 다른 구독으로 이동할 수 없습니다. [네트워킹 이동 제한 사항](./move-limitations/networking-move-limitations.md)을 참조 하세요.

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallpolicies | 아닙니다. | 아닙니다. |
> | 프로필 | yes | yes |
> | 프로필/끝점 | yes | yes |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | certificateorders | yes | yes |

> [!IMPORTANT]
> [App Service 이동 가이드](./move-limitations/app-service-move-limitations.md)를 참조 하세요.

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domainnames | yes | 아닙니다. |
> | virtualmachines | yes | 아닙니다. |

> [!IMPORTANT]
> [클래식 배포 이동 지침](./move-limitations/classic-model-move-limitations.md)을 참조 하세요. 클래식 배포 리소스는 해당 시나리오와 관련 된 작업으로 구독 간에 이동할 수 있습니다.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | 아닙니다. | 아닙니다. |
> | reservedips | 아닙니다. | 아닙니다. |
> | virtualnetworks | 아닙니다. | 아닙니다. |

> [!IMPORTANT]
> [클래식 배포 이동 지침](./move-limitations/classic-model-move-limitations.md)을 참조 하세요. 클래식 배포 리소스는 해당 시나리오와 관련 된 작업으로 구독 간에 이동할 수 있습니다.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storageaccounts | yes | 아닙니다. |

> [!IMPORTANT]
> [클래식 배포 이동 지침](./move-limitations/classic-model-move-limitations.md)을 참조 하세요. 클래식 배포 리소스는 해당 시나리오와 관련 된 작업으로 구독 간에 이동할 수 있습니다.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | yes | yes |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availabilitysets | yes | yes |
> | disk; 집합 | 아닙니다. | 아닙니다. |
> | 디스크 | yes | yes |
> | galleries | 아닙니다. | 아닙니다. |
> | 갤러리/이미지 | 아닙니다. | 아닙니다. |
> | 갤러리/이미지/버전 | 아닙니다. | 아닙니다. |
> | 호스트 그룹 | 아닙니다. | 아닙니다. |
> | 호스트 그룹/호스트 | 아닙니다. | 아닙니다. |
> | images | yes | yes |
> | proximityplacementgroups | 아닙니다. | 아닙니다. |
> | restorepointcollections | 아닙니다. | 아닙니다. |
> | sharedvmimages | 아닙니다. | 아닙니다. |
> | sharedvmimages/버전 | 아닙니다. | 아닙니다. |
> | 스냅샷 | yes | yes |
> | virtualmachines | yes | yes |
> | virtualmachines/확장 | yes | yes |
> | virtualmachinescalesets | yes | yes |

> [!IMPORTANT]
> [Virtual Machines 이동 가이드](./move-limitations/virtual-machines-move-limitations.md)를 참조 하세요.

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | 아닙니다. | 아닙니다. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | 아닙니다. | 아닙니다. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | registries | yes | yes |
> | 레지스트리/buildtasks | yes | yes |
> | 레지스트리/복제 | yes | yes |
> | 레지스트리/작업 | yes | yes |
> | 레지스트리/웹 후크 | yes | yes |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containerservices | 아닙니다. | 아닙니다. |
> | managedclusters | 아닙니다. | 아닙니다. |
> | openshiftmanagedclusters | 아닙니다. | 아닙니다. |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 애플리케이션 | yes | yes |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아닙니다. | 아닙니다. |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 커넥터 | yes | yes |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hubs | yes | yes |

## <a name="microsoftcustomproviders"></a>Microsoft CustomProviders

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | resourceproviders | yes | yes |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | 아닙니다. | 아닙니다. |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | 아닙니다. | 아닙니다. |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | 아닙니다. | 아닙니다. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | catalogs | yes | yes |
> | datacatalogs | 아닙니다. | 아닙니다. |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | 아닙니다. | 아닙니다. |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 패키지 | 아닙니다. | 아닙니다. |
> | 플랜 | 아닙니다. | 아닙니다. |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datafactories | yes | yes |
> | factories | yes | yes |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | 아닙니다. | 아닙니다. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | yes | yes |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | yes | yes |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | 아닙니다. | 아닙니다. |
> | 서비스/프로젝트 | 아닙니다. | 아닙니다. |
> | slots | 아닙니다. | 아닙니다. |

## <a name="microsoftdatashare"></a>DataShare

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아닙니다. | 아닙니다. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 서버 | yes | yes |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 서버 | yes | yes |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | servergroups | 아닙니다. | 아닙니다. |
> | 서버 | yes | yes |
> | serversv2 | yes | yes |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | artifactsources | yes | yes |
> | rollouts | yes | yes |
> | servicetopologies | yes | yes |
> | servicetopologies/서비스 | yes | yes |
> | servicetopologies/서비스/serviceunits | yes | yes |
> | 단계 | yes | yes |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | elasticpools | 아닙니다. | 아닙니다. |
> | elasticpools / iothubtenants | 아닙니다. | 아닙니다. |
> | iothubs | yes | yes |
> | provisioningservices | yes | yes |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | controllers | yes | yes |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | labcenters | 아닙니다. | 아닙니다. |
> | labs | yes | 아닙니다. |
> | 랩/환경 | yes | yes |
> | labs/servicerunners | yes | yes |
> | labs/virtualmachines | yes | 아닙니다. |
> | schedules | yes | yes |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | databaseaccounts | yes | yes |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domains | yes | yes |

## <a name="microsoftenterpriseknowledgegraph"></a>EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | yes | yes |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domains | yes | yes |
> | topics | yes | yes |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | yes | yes |
> | namespaces | yes | yes |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아닙니다. | 아닙니다. |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hanainstances | 아닙니다. | 아닙니다. |
> | sapmonitors | yes | yes |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | yes | yes |

> [!IMPORTANT]
> 새 구독 또는 리소스 그룹에 HDInsight 클러스터를 이동할 수 있습니다. 그러나 HDInsight 클러스터에 연결된 네트워킹 리소스(예: Virtual Network, NIC 또는 부하 분산 장치)는 구독 간에 이동할 수 없습니다. 또한 클러스터에 대한 가상 머신에 연결된 NIC를 새 리소스 그룹으로 이동할 수 없습니다.
>
> HDInsight 클러스터를 새 구독으로 이동할 때 먼저 다른 리소스(예: 스토리지 계정)를 이동합니다. 그런 다음 자체적으로 HDInsight 클러스터를 이동합니다.

## <a name="microsofthealthcareapis"></a>HealthcareApis

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | yes | yes |

## <a name="microsofthybridcompute"></a>HybridCompute

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 머신의 | 아닙니다. | 아닙니다. |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datamanagers | yes | yes |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | yes | yes |

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아닙니다. | 아닙니다. |
> | actiongroups | yes | yes |
> | activitylogalerts | 아닙니다. | 아닙니다. |
> | alertrules | yes | yes |
> | autoscalesettings | yes | yes |
> | components | yes | yes |
> | guestdiagnosticsettings | 아닙니다. | 아닙니다. |
> | metricalerts | 아닙니다. | 아닙니다. |
> | notificationgroups | 아닙니다. | 아닙니다. |
> | notificationrules | 아닙니다. | 아닙니다. |
> | scheduledqueryrules | yes | yes |
> | webtests | yes | yes |
> | workbooks | yes | yes |

> [!IMPORTANT]
> 새 구독으로의 이동이 [구독 할당량](../azure-subscription-service-limits.md#azure-monitor-limits)을 초과 하지 않는지 확인 합니다.

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | iotapps | yes | yes |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | yes | yes |
> | graph | yes | yes |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hsmpools | 아닙니다. | 아닙니다. |
> | vaults | yes | yes |

> [!IMPORTANT]
> 디스크 암호화에 사용 되는 키 자격 증명 모음은 동일한 구독의 리소스 그룹 또는 구독 간에 이동할 수 없습니다.

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | yes | yes |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | labaccounts | 아닙니다. | 아닙니다. |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아닙니다. | 아닙니다. |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아닙니다. | 아닙니다. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | 아닙니다. | 아닙니다. |
> | integrationaccounts | yes | yes |
> | integrationserviceenvironments | 아닙니다. | 아닙니다. |
> | isolatedenvironments | 아닙니다. | 아닙니다. |
> | workflows | yes | yes |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | commitmentplans | yes | yes |
> | webservices | yes | 아닙니다. |
> | workspaces | yes | yes |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | yes | yes |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아닙니다. | 아닙니다. |
> | 계정/작업 영역 | 아닙니다. | 아닙니다. |
> | 계정/작업 영역/프로젝트 | 아닙니다. | 아닙니다. |
> | teamaccounts | 아닙니다. | 아닙니다. |
> | teamaccounts/작업 영역 | 아닙니다. | 아닙니다. |
> | teamaccounts/workspace/projects | 아닙니다. | 아닙니다. |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아닙니다. | 아닙니다. |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingaccounts | 아닙니다. | 아닙니다. |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | 아닙니다. | 아닙니다. |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | userassignedidentities | 아닙니다. | 아닙니다. |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | yes | yes |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | classicdevservices | 아닙니다. | 아닙니다. |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | mediaservices | yes | yes |
> | windowsazure.mediaservices/liveevents | yes | yes |
> | windowsazure.mediaservices/streamingendpoints | yes | yes |

## <a name="microsoftmicroservices4spring"></a>Microservices4Spring

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | appclusters | 아닙니다. | 아닙니다. |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | 아닙니다. | 아닙니다. |
> | migrateprojects | 아닙니다. | 아닙니다. |
> | projects | 아닙니다. | 아닙니다. |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | netappaccounts | 아닙니다. | 아닙니다. |
> | netappaccounts/capacitypools | 아닙니다. | 아닙니다. |
> | netappaccounts/capacitypools/볼륨 | 아닙니다. | 아닙니다. |
> | netappaccounts/capacitypools/볼륨/mounttargets | 아닙니다. | 아닙니다. |
> | netappaccounts/capacitypools/볼륨/스냅숏 | 아닙니다. | 아닙니다. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgateways | 아닙니다. | 아닙니다. |
> | applicationgatewaywebapplicationfirewallpolicies | 아닙니다. | 아닙니다. |
> | applicationsecuritygroups | yes | yes |
> | azurefirewalls | yes | yes |
> | bastionhosts | 아닙니다. | 아닙니다. |
> | connections | yes | yes |
> | ddoscustompolicies | yes | yes |
> | ddosprotectionplans | 아닙니다. | 아닙니다. |
> | dnszones | yes | yes |
> | expressroutecircuits | 아닙니다. | 아닙니다. |
> | expressroutecrossconnections | 아닙니다. | 아닙니다. |
> | expressroutegateways | 아닙니다. | 아닙니다. |
> | expressrouteports | 아닙니다. | 아닙니다. |
> | frontdoors | 아닙니다. | 아닙니다. |
> | frontdoorwebapplicationfirewallpolicies | 아닙니다. | 아닙니다. |
> | loadbalancers | 예-기본 SKU<br>안 함-표준 SKU | 예-기본 SKU<br>안 함-표준 SKU |
> | localnetworkgateways | yes | yes |
> | natgateways | yes | yes |
> | networkintentpolicies | yes | yes |
> | networkinterfaces | yes | yes |
> | networkprofiles | 아닙니다. | 아닙니다. |
> | networksecuritygroups | yes | yes |
> | networkwatchers | yes | yes |
> | networkwatchers/connectionmonitors | yes | yes |
> | networkwatchers/lenses | yes | yes |
> | networkwatchers/ | yes | yes |
> | p2svpngateways | 아닙니다. | 아닙니다. |
> | privatednszones | yes | yes |
> | privatednszones / virtualnetworklinks | yes | yes |
> | privateendpoints | 아닙니다. | 아닙니다. |
> | privatelinkservices | 아닙니다. | 아닙니다. |
> | publicipaddresses | 예-기본 SKU<br>안 함-표준 SKU | 예-기본 SKU<br>안 함-표준 SKU |
> | publicipprefixes | yes | yes |
> | routefilters | 아닙니다. | 아닙니다. |
> | routetables | yes | yes |
> | securegateways | yes | yes |
> | serviceendpointpolicies | yes | yes |
> | trafficmanagerprofiles | yes | yes |
> | virtualhubs | 아닙니다. | 아닙니다. |
> | virtualnetworkgateways | yes | yes |
> | virtualnetworks | yes | yes |
> | virtualnetworktaps | 아닙니다. | 아닙니다. |
> | virtualwans | 아닙니다. | 아닙니다. |
> | vpngateways (가상 WAN) | 아닙니다. | 아닙니다. |
> | vpnsites (가상 WAN) | 아닙니다. | 아닙니다. |
> | webapplicationfirewallpolicies | yes | yes |

> [!IMPORTANT]
> [네트워킹 이동 지침](./move-limitations/networking-move-limitations.md)을 참조 하세요.

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | namespaces | yes | yes |
> | 네임 스페이스/notificationhubs | yes | yes |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | yes | yes |

> [!IMPORTANT]
> 새 구독으로의 이동이 [구독 할당량](../azure-subscription-service-limits.md#azure-monitor-limits)을 초과 하지 않는지 확인 합니다.

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managementconfigurations | yes | yes |
> | solutions | yes | yes |
> | 뷰 | yes | yes |

## <a name="microsoftpeering"></a>Microsoft 피어 링

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 피어 링 | 아닙니다. | 아닙니다. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dashboards | yes | yes |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | rootresources | 아닙니다. | 아닙니다. |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspacecollections | yes | yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | capacities | yes | yes |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아닙니다. | 아닙니다. |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | vaults | yes | yes |

> [!IMPORTANT]
> [Recovery Services 이동 가이드](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)를 참조 하세요.

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | namespaces | yes | yes |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 쿼리 | yes | yes |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 애플리케이션 | yes | 아닙니다. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | flows | yes | yes |
> | jobcollections | yes | yes |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | searchservices | yes | yes |

> [!IMPORTANT]
> 한 번의 작업으로 다른 지역에 있는 여러 검색 리소스를 이동할 수 없습니다. 대신 별도 작업으로 이동합니다.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | iotsecuritysolutions | yes | yes |
> | playbookconfigurations | 아닙니다. | 아닙니다. |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | gateways | 아닙니다. | 아닙니다. |
> | 노드 | 아닙니다. | 아닙니다. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | namespaces | yes | yes |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 애플리케이션 | 아닙니다. | 아닙니다. |
> | clusters | yes | yes |
> | 클러스터/응용 프로그램 | 아닙니다. | 아닙니다. |
> | containergroups | 아닙니다. | 아닙니다. |
> | containergroupsets | 아닙니다. | 아닙니다. |
> | edgeclusters | 아닙니다. | 아닙니다. |
> | networks | 아닙니다. | 아닙니다. |
> | secretstores | 아닙니다. | 아닙니다. |
> | volumes | 아닙니다. | 아닙니다. |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 애플리케이션 | yes | yes |
> | containergroups | 아닙니다. | 아닙니다. |
> | gateways | yes | yes |
> | networks | yes | yes |
> | secrets | yes | yes |
> | volumes | yes | yes |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | signalr | yes | yes |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | appliancedefinitions | 아닙니다. | 아닙니다. |
> | appliances | 아닙니다. | 아닙니다. |
> | applicationdefinitions | 아닙니다. | 아닙니다. |
> | 애플리케이션 | 아닙니다. | 아닙니다. |
> | jitrequests | 아닙니다. | 아닙니다. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | instancepools | 아닙니다. | 아닙니다. |
> | managedinstances | 아닙니다. | 아닙니다. |
> | managedinstances/데이터베이스 | 아닙니다. | 아닙니다. |
> | 서버 | yes | yes |
> | 서버/데이터베이스 | yes | yes |
> | servers/elasticpools | yes | yes |
> | virtualclusters | yes | yes |

> [!IMPORTANT]
> 데이터베이스와 서버는 동일한 리소스 그룹에 있어야 합니다. SQL Server를 이동하면 모든 해당 데이터베이스도 함께 이동합니다. 이 동작은 Azure SQL Database 및 Azure SQL Data Warehouse 데이터베이스에 적용됩니다.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | yes | yes |
> | sqlvirtualmachines | yes | yes |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dwvm | 아닙니다. | 아닙니다. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storageaccounts | yes | yes |

## <a name="microsoftstoragecache"></a>StorageCache

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 캐시 | 아닙니다. | 아닙니다. |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | yes | yes |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | 아닙니다. | 아닙니다. |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | 아닙니다. | 아닙니다. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managers | 아닙니다. | 아닙니다. |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | streamingjobs | yes | yes |

> [!IMPORTANT]
> Stream Analytics 작업은 실행 중 상태일 때 이동할 수 없습니다.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | 아닙니다. | 아닙니다. |
> | 환경/s s o 원본 | 아닙니다. | 아닙니다. |
> | 인스턴스 | 아닙니다. | 아닙니다. |
> | 인스턴스/환경 | 아닙니다. | 아닙니다. |
> | 인스턴스/환경/s s o 원본 | 아닙니다. | 아닙니다. |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | providerregistrations | 아닙니다. | 아닙니다. |
> | 리소스 | 아닙니다. | 아닙니다. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | yes | yes |
> | 환경/s s o 원본 | yes | yes |
> | environment/referencedatasets | yes | yes |

## <a name="microsofttoken"></a>Microsoft 토큰

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 정보가 | 아닙니다. | 아닙니다. |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | imagetemplates | 아닙니다. | 아닙니다. |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | yes | yes |
> | 계정/확장 | yes | yes |
> | 계정/프로젝트 | yes | yes |

> [!IMPORTANT]
> Azure DevOps에 대 한 구독을 변경 하려면 [청구에 사용 되는 azure 구독 변경](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)을 참조 하세요.

## <a name="microsoftvmwarecloudsimple"></a>VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | 아닙니다. | 아닙니다. |
> | dedicatedcloudservices | 아닙니다. | 아닙니다. |
> | virtualmachines | 아닙니다. | 아닙니다. |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | certificates | 아닙니다. | yes |
> | connectiongateways | yes | yes |
> | connections | yes | yes |
> | customapis | yes | yes |
> | hostingenvironments | 아닙니다. | 아닙니다. |
> | serverfarms | yes | yes |
> | sites | yes | yes |
> | 사이트/premieraddons | yes | yes |
> | 사이트/슬롯 | yes | yes |

> [!IMPORTANT]
> [App Service 이동 가이드](./move-limitations/app-service-move-limitations.md)를 참조 하세요.

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | deviceservices | 아닙니다. | 아닙니다. |

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | 리소스 종류 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgroups | 아닙니다. | 아닙니다. |
> | hostpools | 아닙니다. | 아닙니다. |
> | workspaces | 아닙니다. | 아닙니다. |

## <a name="third-party-services"></a>타사 서비스

타사 서비스는 현재 이동 작업을 지원하지 않습니다.

## <a name="next-steps"></a>다음 단계
리소스를 이동하는 명령은 [새 리소스 그룹 또는 구독으로 리소스 이동](resource-group-move-resources.md)을 참조하세요.

쉼표로 구분된 값 파일과 동일한 데이터를 가져오려면 [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv) 파일을 다운로드합니다.
