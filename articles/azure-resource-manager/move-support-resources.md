---
title: Azure 리소스 종류별 이동 작업 지원
description: 새 리소스 그룹이나 구독으로 이동할 수 있는 Azure 리소스 종류를 소개합니다.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 09/06/2019
ms.author: tomfitz
ms.openlocfilehash: 9dc86fe7876a050fe67ec73a665be6141a4b98da
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/08/2019
ms.locfileid: "70801356"
---
# <a name="move-operation-support-for-resources"></a>리소스에 대한 이동 작업 지원
이 문서에서는 Azure 리소스 종류가 이동 작업을 지원하는지 여부에 대한 정보를 제공합니다. 또한 리소스를 이동할 때 고려해 야 할 특수 조건에 대 한 정보를 제공 합니다.

리소스 공급자 네임 스페이스로 이동 합니다.
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Backup](#microsoftbackup)
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
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
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
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [microsoft.dns](#microsoftdns)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
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
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | domainservices | 아니요 | 아니요 |
> | domainservices/replicasets | 아니요 | 아니요 |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | tenants | 아니요 | 아니요 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | actionrules | 예 | 예 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | 서버 | 예 | 예 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | 서비스 | 예 | 예 |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | configurationstores | 예 | 예 |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | apiapps | 아니요 | 아니요 |
> | appidentities | 아니요 | 아니요 |
> | gateways | 아니요 | 아니요 |

> [!IMPORTANT]
> [App Service 이동 가이드](./move-limitations/app-service-move-limitations.md)를 참조 하세요.

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | policyassignments | 아니요 | 아니요 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | automationaccounts | 예 | 예 |
> | automationaccounts/구성 | 예 | 예 |
> | automationaccounts/runbook | 예 | 예 |

> [!IMPORTANT]
> Runbook은 Automation 계정과 동일한 리소스 그룹에 있어야 합니다.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | 예 | 예 |

## <a name="microsoftazuredata"></a>Microsoft AzureData

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | sqlserverregistrations | 아니요 | 아니요 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | registrations | 예 | 예 |

## <a name="microsoftbackup"></a>Microsoft.Backup

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | backupvault | 아니요 | 아니요 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | batchaccounts | 예 | 예 |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | clusters | 아니요 | 아니요 |
> | fileservers | 아니요 | 아니요 |
> | 작업 | 아니요 | 아니요 |
> | 작업 영역 | 아니요 | 아니요 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | mapapis | 아니요 | 아니요 |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | biztalk | 예 | 예 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | 예 | 예 |
> | 감시자 | 아니요 | 아니요 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | 아니요 | 아니요 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | botservices | 예 | 예 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | redis | 예 | 예 |

> [!IMPORTANT]
> Azure Cache for Redis 인스턴스가 가상 네트워크를 사용 하 여 구성 된 경우 인스턴스를 다른 구독으로 이동할 수 없습니다. [네트워킹 이동 제한 사항](./move-limitations/networking-move-limitations.md)을 참조 하세요.

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallpolicies | 아니요 | 아니요 |
> | profiles | 예 | 예 |
> | 프로필/끝점 | 예 | 예 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | certificateorders | 예 | 예 |

> [!IMPORTANT]
> [App Service 이동 가이드](./move-limitations/app-service-move-limitations.md)를 참조 하세요.

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | domainnames | 예 | 아니요 |
> | virtualmachines | 예 | 아니요 |

> [!IMPORTANT]
> [클래식 배포 이동 지침](./move-limitations/classic-model-move-limitations.md)을 참조 하세요. 클래식 배포 리소스는 해당 시나리오와 관련 된 작업으로 구독 간에 이동할 수 있습니다.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | 아니요 | 아니요 |
> | reservedips | 아니요 | 아니요 |
> | virtualnetworks | 아니요 | 아니요 |

> [!IMPORTANT]
> [클래식 배포 이동 지침](./move-limitations/classic-model-move-limitations.md)을 참조 하세요. 클래식 배포 리소스는 해당 시나리오와 관련 된 작업으로 구독 간에 이동할 수 있습니다.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | storageaccounts | 예 | 아니요 |

> [!IMPORTANT]
> [클래식 배포 이동 지침](./move-limitations/classic-model-move-limitations.md)을 참조 하세요. 클래식 배포 리소스는 해당 시나리오와 관련 된 작업으로 구독 간에 이동할 수 있습니다.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | availabilitysets | 예 | 예 |
> | disk; 집합 | 아니요 | 아니요 |
> | 디스크 | 예 | 예 |
> | galleries | 아니요 | 아니요 |
> | 갤러리/이미지 | 아니요 | 아니요 |
> | 갤러리/이미지/버전 | 아니요 | 아니요 |
> | hostgroups | 아니요 | 아니요 |
> | 호스트 그룹/호스트 | 아니요 | 아니요 |
> | 이미지 | 예 | 예 |
> | proximityplacementgroups | 아니요 | 아니요 |
> | restorepointcollections | 아니요 | 아니요 |
> | sharedvmimages | 아니요 | 아니요 |
> | sharedvmimages/버전 | 아니요 | 아니요 |
> | 스냅숏 | 예 | 예 |
> | virtualmachines | 예 | 예 |
> | virtualmachines/확장 | 예 | 예 |
> | virtualmachinescalesets | 예 | 예 |

> [!IMPORTANT]
> [Virtual Machines 이동 가이드](./move-limitations/virtual-machines-move-limitations.md)를 참조 하세요.

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | containergroups | 아니요 | 아니요 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | containergroups | 아니요 | 아니요 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | registries | 예 | 예 |
> | 레지스트리/buildtasks | 예 | 예 |
> | 레지스트리/복제 | 예 | 예 |
> | 레지스트리/작업 | 예 | 예 |
> | 레지스트리/웹 후크 | 예 | 예 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | containerservices | 아니요 | 아니요 |
> | managedclusters | 아니요 | 아니요 |
> | openshiftmanagedclusters | 아니요 | 아니요 |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | 애플리케이션 | 예 | 예 |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | 계정 | 아니요 | 아니요 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | 커넥터 | 예 | 예 |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | hubs | 예 | 예 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | 작업 | 아니요 | 아니요 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | 아니요 | 아니요 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | 작업 영역 | 아니요 | 아니요 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | catalogs | 예 | 예 |
> | datacatalogs | 아니요 | 아니요 |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | 아니요 | 아니요 |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | 패키지 | 아니요 | 아니요 |
> | 플랜 | 아니요 | 아니요 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | datafactories | 예 | 예 |
> | factories | 예 | 예 |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | 아니요 | 아니요 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | services | 아니요 | 아니요 |
> | 서비스/프로젝트 | 아니요 | 아니요 |
> | slots | 아니요 | 아니요 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | 서버 | 예 | 예 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | 서버 | 예 | 예 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | servergroups | 아니요 | 아니요 |
> | 서버 | 예 | 예 |
> | serversv2 | 예 | 예 |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | artifactsources | 예 | 예 |
> | rollouts | 예 | 예 |
> | servicetopologies | 예 | 예 |
> | servicetopologies/서비스 | 예 | 예 |
> | servicetopologies/서비스/serviceunits | 예 | 예 |
> | 단계 | 예 | 예 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | elasticpools | 아니요 | 아니요 |
> | elasticpools / iothubtenants | 아니요 | 아니요 |
> | iothubs | 예 | 예 |
> | provisioningservices | 예 | 예 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | controllers | 예 | 예 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | labcenters | 아니요 | 아니요 |
> | labs | 예 | 아니요 |
> | 랩/환경 | 예 | 예 |
> | labs/servicerunners | 예 | 예 |
> | labs/virtualmachines | 예 | 아니요 |
> | schedules | 예 | 예 |

## <a name="microsoftdns"></a>microsoft.dns

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | dnszones | 아니요 | 아니요 |
> | dnszones/a | 아니요 | 아니요 |
> | dnszones/aaaa | 아니요 | 아니요 |
> | dnszones/cname | 아니요 | 아니요 |
> | dnszones/mx | 아니요 | 아니요 |
> | dnszones/ptr | 아니요 | 아니요 |
> | dnszones/srv | 아니요 | 아니요 |
> | dnszones/txt | 아니요 | 아니요 |
> | trafficmanagerprofiles | 아니요 | 아니요 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | databaseaccounts | 예 | 예 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | domains | 예 | 예 |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | services | 예 | 예 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | domains | 예 | 예 |
> | topics | 예 | 예 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | clusters | 예 | 예 |
> | namespaces | 예 | 예 |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | 계정 | 아니요 | 아니요 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | hanainstances | 아니요 | 아니요 |
> | sapmonitors | 예 | 예 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | clusters | 예 | 예 |

> [!IMPORTANT]
> 새 구독 또는 리소스 그룹에 HDInsight 클러스터를 이동할 수 있습니다. 그러나 HDInsight 클러스터에 연결된 네트워킹 리소스(예: Virtual Network, NIC 또는 부하 분산 장치)는 구독 간에 이동할 수 없습니다. 또한 클러스터에 대한 가상 머신에 연결된 NIC를 새 리소스 그룹으로 이동할 수 없습니다.
>
> HDInsight 클러스터를 새 구독으로 이동할 때 먼저 다른 리소스(예: 스토리지 계정)를 이동합니다. 그런 다음 자체적으로 HDInsight 클러스터를 이동합니다.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | services | 예 | 예 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | 머신 | 아니요 | 아니요 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | datamanagers | 예 | 예 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | 작업 | 예 | 예 |

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | 계정 | 아니요 | 아니요 |
> | actiongroups | 예 | 예 |
> | activitylogalerts | 아니요 | 아니요 |
> | alertrules | 예 | 예 |
> | autoscalesettings | 예 | 예 |
> | components | 예 | 예 |
> | guestdiagnosticsettings | 아니요 | 아니요 |
> | metricalerts | 아니요 | 아니요 |
> | notificationgroups | 아니요 | 아니요 |
> | notificationrules | 아니요 | 아니요 |
> | scheduledqueryrules | 예 | 예 |
> | webtests | 예 | 예 |
> | workbooks | 예 | 예 |

> [!IMPORTANT]
> 새 구독으로의 이동이 [구독 할당량](../azure-subscription-service-limits.md#azure-monitor-limits)을 초과 하지 않는지 확인 합니다.

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | iotapps | 예 | 예 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 예 | 예 |
> | 그래프 | 예 | 예 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | hsmpools | 아니요 | 아니요 |
> | vaults | 예 | 예 |

> [!IMPORTANT]
> 디스크 암호화에 사용 되는 키 자격 증명 모음은 동일한 구독의 리소스 그룹 또는 구독 간에 이동할 수 없습니다.

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | clusters | 예 | 예 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | labaccounts | 아니요 | 아니요 |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | 계정 | 아니요 | 아니요 |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | 계정 | 아니요 | 아니요 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | 아니요 | 아니요 |
> | integrationaccounts | 예 | 예 |
> | integrationserviceenvironments | 아니요 | 아니요 |
> | isolatedenvironments | 아니요 | 아니요 |
> | workflows | 예 | 예 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | commitmentplans | 예 | 예 |
> | webservices | 예 | 아니요 |
> | 작업 영역 | 예 | 예 |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | 예 | 예 |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | 계정 | 아니요 | 아니요 |
> | 계정/작업 영역 | 아니요 | 아니요 |
> | 계정/작업 영역/프로젝트 | 아니요 | 아니요 |
> | teamaccounts | 아니요 | 아니요 |
> | teamaccounts/작업 영역 | 아니요 | 아니요 |
> | teamaccounts/workspace/projects | 아니요 | 아니요 |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | 계정 | 아니요 | 아니요 |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | hostingaccounts | 아니요 | 아니요 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | 작업 영역 | 아니요 | 아니요 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | userassignedidentities | 아니요 | 아니요 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | classicdevservices | 아니요 | 아니요 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | mediaservices | 예 | 예 |
> | windowsazure.mediaservices/liveevents | 예 | 예 |
> | windowsazure.mediaservices/streamingendpoints | 예 | 예 |

## <a name="microsoftmicroservices4spring"></a>Microservices4Spring

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | appclusters | 아니요 | 아니요 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | 아니요 | 아니요 |
> | migrateprojects | 아니요 | 아니요 |
> | projects | 아니요 | 아니요 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | netappaccounts | 아니요 | 아니요 |
> | netappaccounts/capacitypools | 아니요 | 아니요 |
> | netappaccounts/capacitypools/볼륨 | 아니요 | 아니요 |
> | netappaccounts/capacitypools/볼륨/mounttargets | 아니요 | 아니요 |
> | netappaccounts/capacitypools/볼륨/스냅숏 | 아니요 | 아니요 |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | applicationgateways | 아니요 | 아니요 |
> | applicationgatewaywebapplicationfirewallpolicies | 아니요 | 아니요 |
> | applicationsecuritygroups | 예 | 예 |
> | azurefirewalls | 예 | 예 |
> | bastionhosts | 아니요 | 아니요 |
> | 연결 | 예 | 예 |
> | ddoscustompolicies | 예 | 예 |
> | ddosprotectionplans | 아니요 | 아니요 |
> | dnszones | 예 | 예 |
> | expressroutecircuits | 아니요 | 아니요 |
> | expressroutecrossconnections | 아니요 | 아니요 |
> | expressroutegateways | 아니요 | 아니요 |
> | expressrouteports | 아니요 | 아니요 |
> | frontdoors | 아니요 | 아니요 |
> | frontdoorwebapplicationfirewallpolicies | 아니요 | 아니요 |
> | loadbalancers | 예-기본 SKU<br>안 함-표준 SKU | 예-기본 SKU<br>안 함-표준 SKU |
> | localnetworkgateways | 예 | 예 |
> | natgateways | 예 | 예 |
> | networkintentpolicies | 예 | 예 |
> | networkinterfaces | 예 | 예 |
> | networkprofiles | 아니요 | 아니요 |
> | networksecuritygroups | 예 | 예 |
> | networkwatchers | 예 | 예 |
> | networkwatchers/connectionmonitors | 예 | 예 |
> | networkwatchers/lenses | 예 | 예 |
> | networkwatchers/ | 예 | 예 |
> | p2svpngateways | 아니요 | 아니요 |
> | privatednszones | 예 | 예 |
> | privatednszones / virtualnetworklinks | 예 | 예 |
> | privateendpoints | 아니요 | 아니요 |
> | privatelinkservices | 아니요 | 아니요 |
> | publicipaddresses | 예-기본 SKU<br>안 함-표준 SKU | 예-기본 SKU<br>안 함-표준 SKU |
> | publicipprefixes | 예 | 예 |
> | routefilters | 아니요 | 아니요 |
> | routetables | 예 | 예 |
> | securegateways | 예 | 예 |
> | serviceendpointpolicies | 예 | 예 |
> | trafficmanagerprofiles | 예 | 예 |
> | virtualhubs | 아니요 | 아니요 |
> | virtualnetworkgateways | 예 | 예 |
> | virtualnetworks | 예 | 예 |
> | virtualnetworktaps | 아니요 | 아니요 |
> | virtualwans | 아니요 | 아니요 |
> | vpngateways (가상 WAN) | 아니요 | 아니요 |
> | vpnsites (가상 WAN) | 아니요 | 아니요 |
> | webapplicationfirewallpolicies | 예 | 예 |

> [!IMPORTANT]
> [네트워킹 이동 지침](./move-limitations/networking-move-limitations.md)을 참조 하세요.

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | namespaces | 예 | 예 |
> | 네임 스페이스/notificationhubs | 예 | 예 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | 작업 영역 | 예 | 예 |

> [!IMPORTANT]
> 새 구독으로의 이동이 [구독 할당량](../azure-subscription-service-limits.md#azure-monitor-limits)을 초과 하지 않는지 확인 합니다.

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | managementconfigurations | 예 | 예 |
> | 솔루션 | 예 | 예 |
> | 보기 | 예 | 예 |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | 피어링 | 아니요 | 아니요 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | dashboards | 예 | 예 |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | rootresources | 아니요 | 아니요 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | workspacecollections | 예 | 예 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | capacities | 예 | 예 |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | 계정 | 아니요 | 아니요 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | vaults | 예 | 예 |

> [!IMPORTANT]
> [Recovery Services 이동 가이드](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)를 참조 하세요.

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | namespaces | 예 | 예 |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | 쿼리 | 예 | 예 |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | 애플리케이션 | 예 | 아니요 |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | 흐름 | 예 | 예 |
> | jobcollections | 예 | 예 |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | searchservices | 예 | 예 |

> [!IMPORTANT]
> 한 번의 작업으로 다른 지역에 있는 여러 검색 리소스를 이동할 수 없습니다. 대신 별도 작업으로 이동합니다.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | iotsecuritysolutions | 예 | 예 |
> | playbookconfigurations | 아니요 | 아니요 |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | gateways | 아니요 | 아니요 |
> | nodes | 아니요 | 아니요 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | namespaces | 예 | 예 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | 애플리케이션 | 아니요 | 아니요 |
> | clusters | 예 | 예 |
> | 클러스터/응용 프로그램 | 아니요 | 아니요 |
> | containergroups | 아니요 | 아니요 |
> | containergroupsets | 아니요 | 아니요 |
> | edgeclusters | 아니요 | 아니요 |
> | 네트워크 | 아니요 | 아니요 |
> | secretstores | 아니요 | 아니요 |
> | 볼륨 | 아니요 | 아니요 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | 애플리케이션 | 예 | 예 |
> | containergroups | 아니요 | 아니요 |
> | gateways | 예 | 예 |
> | 네트워크 | 예 | 예 |
> | 비밀 | 예 | 예 |
> | 볼륨 | 예 | 예 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | signalr | 예 | 예 |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | siterecoveryvault | 아니요 | 아니요 |

> [!IMPORTANT]
> [Recovery Services 이동 가이드](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)를 참조 하세요.

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | appliancedefinitions | 아니요 | 아니요 |
> | appliances | 아니요 | 아니요 |
> | applicationdefinitions | 아니요 | 아니요 |
> | 애플리케이션 | 아니요 | 아니요 |
> | jitrequests | 아니요 | 아니요 |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | instancepools | 아니요 | 아니요 |
> | managedinstances | 아니요 | 아니요 |
> | managedinstances/데이터베이스 | 아니요 | 아니요 |
> | 서버 | 예 | 예 |
> | 서버/데이터베이스 | 예 | 예 |
> | servers/elasticpools | 예 | 예 |
> | virtualclusters | 예 | 예 |

> [!IMPORTANT]
> 데이터베이스와 서버는 동일한 리소스 그룹에 있어야 합니다. SQL Server를 이동하면 모든 해당 데이터베이스도 함께 이동합니다. 이 동작은 Azure SQL Database 및 Azure SQL Data Warehouse 데이터베이스에 적용됩니다.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | 예 | 예 |
> | sqlvirtualmachines | 예 | 예 |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | dwvm | 아니요 | 아니요 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | storageaccounts | 예 | 예 |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | 캐시 | 아니요 | 아니요 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | 예 | 예 |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | 아니요 | 아니요 |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | 아니요 | 아니요 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | managers | 아니요 | 아니요 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | streamingjobs | 예 | 예 |

> [!IMPORTANT]
> Stream Analytics 작업은 실행 중 상태일 때 이동할 수 없습니다.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | environments | 아니요 | 아니요 |
> | 환경/s s o 원본 | 아니요 | 아니요 |
> | 인스턴스 | 아니요 | 아니요 |
> | 인스턴스/환경 | 아니요 | 아니요 |
> | 인스턴스/환경/s s o 원본 | 아니요 | 아니요 |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | providerregistrations | 아니요 | 아니요 |
> | 리소스 | 아니요 | 아니요 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | environments | 예 | 예 |
> | 환경/s s o 원본 | 예 | 예 |
> | environment/referencedatasets | 예 | 예 |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | 정보가 | 아니요 | 아니요 |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | imagetemplates | 아니요 | 아니요 |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |
> | 계정/확장 | 예 | 예 |
> | 계정/프로젝트 | 예 | 예 |

> [!IMPORTANT]
> Azure DevOps에 대 한 구독을 변경 하려면 [청구에 사용 되는 azure 구독 변경](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)을 참조 하세요.

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | 예 | 예 |
> | dedicatedcloudservices | 예 | 예 |
> | virtualmachines | 예 | 예 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | 인증서 | 아니요 | 예 |
> | connectiongateways | 예 | 예 |
> | 연결 | 예 | 예 |
> | customapis | 예 | 예 |
> | hostingenvironments | 아니요 | 아니요 |
> | serverfarms | 예 | 예 |
> | 사이트 | 예 | 예 |
> | 사이트/premieraddons | 예 | 예 |
> | 사이트/슬롯 | 예 | 예 |

> [!IMPORTANT]
> [App Service 이동 가이드](./move-limitations/app-service-move-limitations.md)를 참조 하세요.

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | deviceservices | 아니요 | 아니요 |

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 리소스 그룹 | 구독 |
> | ------------- | ----------- | ---------- |
> | applicationgroups | 아니요 | 아니요 |
> | hostpools | 아니요 | 아니요 |
> | 작업 영역 | 아니요 | 아니요 |

## <a name="third-party-services"></a>타사 서비스

타사 서비스는 현재 이동 작업을 지원하지 않습니다.

## <a name="next-steps"></a>다음 단계
리소스를 이동하는 명령은 [새 리소스 그룹 또는 구독으로 리소스 이동](resource-group-move-resources.md)을 참조하세요.

쉼표로 구분된 값 파일과 동일한 데이터를 가져오려면 [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv) 파일을 다운로드합니다.
