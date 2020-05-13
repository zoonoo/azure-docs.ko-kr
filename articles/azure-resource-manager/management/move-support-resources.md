---
title: 리소스 유형별 이동 작업 지원
description: 새 리소스 그룹이나 구독으로 이동할 수 있는 Azure 리소스 종류를 소개합니다.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 81d545066ea6bcc1d3e2eecd884671324155d796
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124689"
---
# <a name="move-operation-support-for-resources"></a>리소스에 대한 이동 작업 지원
이 문서에서는 Azure 리소스 종류가 이동 작업을 지원하는지 여부에 대한 정보를 제공합니다. 또한 리소스를 이동할 때 고려해 야 할 특수 조건에 대 한 정보를 제공 합니다.

리소스 공급자 네임 스페이스로 이동 합니다.
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft AppConfiguration](#microsoftappconfiguration)
> - [Microsoft AppPlatform](#microsoftappplatform)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft 인증](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.classicnetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Cognition](#microsoftcognition)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
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
> - [Microsoft DataProtection](#microsoftdataprotection)
> - [DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft. 장치](#microsoftdevices)
> - [Microsoft DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft. 실험](#microsoftexperimentation)
> - [Microsoft.](#microsoftfalcon)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft 히드라](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Kubernetes](#microsoftkubernetes)
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
> - [Microsoft. 유지 관리](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft ManagedNetwork](#microsoftmanagednetwork)
> - [Microsoft ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft 네트워크](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft ObjectStore](#microsoftobjectstore)
> - [OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.operationsmanagement](#microsoftoperationsmanagement)
> - [Microsoft 피어 링](#microsoftpeering)
> - [Microsoft PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft ProviderHub](#microsoftproviderhub)
> - [Microsoft 양자](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft. RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft 검색](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft 서비스](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [SoftwarePlan](#microsoftsoftwareplan)
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
> - [Microsoft.Subscription](#microsoftsubscription)
> - [microsoft 지원](#microsoftsupport)
> - [Synapse](#microsoftsynapse)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft 토큰](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [visualstudio](#microsoftvisualstudio)
> - [VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft VnfManager](#microsoftvnfmanager)
> - [Microsoft VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domainservices | 아니요 | 아니요 |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | tenants | 아니요 | 아니요 |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 구성 | 아니요 | 아니요 |
> | 권장 사항 | 아니요 | 아니요 |
> | suppressions | 아니요 | 아니요 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | actionrules | 예 | 예 |
> | 경고 | 아니요 | 아니요 |
> | alertssummary | 아니요 | 아니요 |
> | smartdetectoralertrules | 예 | 예 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | servers | 예 | 예 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 서비스 | 예 | 예 |

> [!IMPORTANT]
> 소비 SKU로 설정 된 API Management 서비스는 이동할 수 없습니다.

## <a name="microsoftappconfiguration"></a>Microsoft AppConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | configurationstores | 예 | 예 |

## <a name="microsoftappplatform"></a>Microsoft AppPlatform

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 스프링 | 예 | 예 |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | apiapps | 아니요 | 아니요 |
> | appidentities | 아니요 | 아니요 |
> | gateways | 아니요 | 아니요 |

> [!IMPORTANT]
> [App Service 이동 가이드](./move-limitations/app-service-move-limitations.md)를 참조 하세요.

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checkaccess | 아니요 | 아니요 |
> | denyassignments | 아니요 | 아니요 |
> | findorphanroleassignments | 아니요 | 아니요 |
> | locks | 아니요 | 아니요 |
> | 권한 | 아니요 | 아니요 |
> | policyassignments | 아니요 | 아니요 |
> | policydefinitions | 아니요 | 아니요 |
> | policysetdefinitions | 아니요 | 아니요 |
> | roleassignments | 아니요 | 아니요 |
> | roleassignmentsusagemetrics | 아니요 | 아니요 |
> | roledefinitions | 아니요 | 아니요 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | automationaccounts | 예 | 예 |
> | automationaccounts/구성 | 예 | 예 |
> | automationaccounts/runbook | 예 | 예 |

> [!IMPORTANT]
> Runbook은 Automation 계정과 동일한 리소스 그룹에 있어야 합니다.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | 예 | 예 |

## <a name="microsoftazuredata"></a>Microsoft AzureData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hybriddatamanagers | 아니요 | 아니요 |
> | postgresinstances | 아니요 | 아니요 |
> | sqlinstances | 아니요 | 아니요 |
> | sqlserverregistrations | 예 | 예 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | registrations | 예 | 예 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | batchaccounts | 예 | 예 |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | 아니요 | 아니요 |
> | fileservers | 아니요 | 아니요 |
> | jobs | 아니요 | 아니요 |
> | workspaces | 아니요 | 아니요 |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | billingperiods | 아니요 | 아니요 |
> | billingpermissions | 아니요 | 아니요 |
> | billingroleassignments | 아니요 | 아니요 |
> | billingroledefinitions | 아니요 | 아니요 |
> | createbillingroleassignment | 아니요 | 아니요 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | mapapis | 아니요 | 아니요 |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | biztalk | 아니요 | 아니요 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | 아니요 | 아니요 |
> | cordamembers | 아니요 | 아니요 |
> | 감시자 | 아니요 | 아니요 |

## <a name="microsoftblockchaintokens"></a>BlockchainTokens

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | tokenservices | 아니요 | 아니요 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | 아니요 | 아니요 |
> | blueprints | 아니요 | 아니요 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | botservices | 예 | 예 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | redis | 예 | 예 |

> [!IMPORTANT]
> Azure Cache for Redis 인스턴스가 가상 네트워크를 사용 하 여 구성 된 경우 인스턴스를 다른 구독으로 이동할 수 없습니다. [네트워킹 이동 제한 사항](./move-limitations/networking-move-limitations.md)을 참조 하세요.

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallpolicies | 예 | 예 |
> | profiles | 예 | 예 |
> | 프로필/끝점 | 예 | 예 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | certificateorders | 예 | 예 |

> [!IMPORTANT]
> [App Service 이동 가이드](./move-limitations/app-service-move-limitations.md)를 참조 하세요.

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domainnames | 예 | 아니요 |
> | virtualmachines | 예 | 아니요 |

> [!IMPORTANT]
> [클래식 배포 이동 지침](./move-limitations/classic-model-move-limitations.md)을 참조 하세요. 클래식 배포 리소스는 해당 시나리오와 관련 된 작업으로 구독 간에 이동할 수 있습니다.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | 아니요 | 아니요 |
> | reservedips | 아니요 | 아니요 |
> | virtualnetworks | 아니요 | 아니요 |

> [!IMPORTANT]
> [클래식 배포 이동 지침](./move-limitations/classic-model-move-limitations.md)을 참조 하세요. 클래식 배포 리소스는 해당 시나리오와 관련 된 작업으로 구독 간에 이동할 수 있습니다.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storageaccounts | 예 | 아니요 |

> [!IMPORTANT]
> [클래식 배포 이동 지침](./move-limitations/classic-model-move-limitations.md)을 참조 하세요. 클래식 배포 리소스는 해당 시나리오와 관련 된 작업으로 구독 간에 이동할 수 있습니다.

## <a name="microsoftcognition"></a>Cognition

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | syntheticsaccounts | 아니요 | 아니요 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availabilitysets | 예 | 예 |
> | disk; 집합 | 아니요 | 아니요 |
> | disks | 예 | 예 |
> | galleries | 아니요 | 아니요 |
> | 갤러리/이미지 | 아니요 | 아니요 |
> | 갤러리/이미지/버전 | 아니요 | 아니요 |
> | 호스트 그룹 | 아니요 | 아니요 |
> | 호스트 그룹/호스트 | 아니요 | 아니요 |
> | images | 예 | 예 |
> | proximityplacementgroups | 예 | 예 |
> | restorepointcollections | 아니요 | 아니요 |
> | sharedvmextensions | 아니요 | 아니요 |
> | sharedvmimages | 아니요 | 아니요 |
> | sharedvmimages/버전 | 아니요 | 아니요 |
> | 스냅샷 | 예 | 예 |
> | sshpublickeys | 아니요 | 아니요 |
> | virtualmachines | 예 | 예 |
> | virtualmachines/확장 | 예 | 예 |
> | virtualmachinescalesets | 예 | 예 |

> [!IMPORTANT]
> [Virtual Machines 이동 가이드](./move-limitations/virtual-machines-move-limitations.md)를 참조 하세요.

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | aggregatedcost | 아니요 | 아니요 |
> | 잔고 | 아니요 | 아니요 |
> | 예산의 | 아니요 | 아니요 |
> | 요금 | 아니요 | 아니요 |
> | costtags | 아니요 | 아니요 |
> | credits | 아니요 | 아니요 |
> | events | 아니요 | 아니요 |
> | 예측 | 아니요 | 아니요 |
> | lots | 아니요 | 아니요 |
> | 마켓플레이스 | 아니요 | 아니요 |
> | operationresults | 아니요 | 아니요 |
> | operationstatus | 아니요 | 아니요 |
> | pricesheets | 아니요 | 아니요 |
> | products | 아니요 | 아니요 |
> | reservationdetails | 아니요 | 아니요 |
> | reservationrecommendations | 아니요 | 아니요 |
> | reservationsummaries | 아니요 | 아니요 |
> | reservationtransactions | 아니요 | 아니요 |
> | tags | 아니요 | 아니요 |
> | tenants | 아니요 | 아니요 |
> | terms | 아니요 | 아니요 |
> | 사용량 세부 정보 | 아니요 | 아니요 |

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | 아니요 | 아니요 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | 아니요 | 아니요 |
> | serviceassociationlinks | 아니요 | 아니요 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | registries | 예 | 예 |
> | 레지스트리/agentpools | 아니요 | 아니요 |
> | 레지스트리/buildtasks | 예 | 예 |
> | 레지스트리/복제 | 예 | 예 |
> | 레지스트리/taskruns | 예 | 예 |
> | 레지스트리/작업 | 예 | 예 |
> | 레지스트리/웹 후크 | 예 | 예 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containerservices | 아니요 | 아니요 |
> | managedclusters | 아니요 | 아니요 |
> | openshiftmanagedclusters | 아니요 | 아니요 |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 애플리케이션 | 아니요 | 아니요 |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아니요 | 아니요 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 경고 | 아니요 | 아니요 |
> | 예산의 | 아니요 | 아니요 |
> | 커넥터 | 예 | 예 |
> | 차원 | 아니요 | 아니요 |
> | 내보내기 | 아니요 | 아니요 |
> | externalsubscriptions | 아니요 | 아니요 |
> | forecast | 아니요 | 아니요 |
> | Query | 아니요 | 아니요 |
> | reportconfigs | 아니요 | 아니요 |
> | reports | 아니요 | 아니요 |
> | showbackrules | 아니요 | 아니요 |
> | 뷰 | 아니요 | 아니요 |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hubs | 아니요 | 아니요 |

## <a name="microsoftcustomproviders"></a>Microsoft CustomProviders

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 연결 | 아니요 | 아니요 |
> | resourceproviders | 예 | 예 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | 아니요 | 아니요 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | 아니요 | 아니요 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | 아니요 | 아니요 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | catalogs | 예 | 예 |
> | datacatalogs | 아니요 | 아니요 |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | 아니요 | 아니요 |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 패키지 | 아니요 | 아니요 |
> | 플랜 | 아니요 | 아니요 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datafactories | 예 | 예 |
> | factories | 예 | 예 |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | 아니요 | 아니요 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | 아니요 | 아니요 |
> | 서비스/프로젝트 | 아니요 | 아니요 |
> | slots | 아니요 | 아니요 |

## <a name="microsoftdataprotection"></a>Microsoft DataProtection

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | backupvaults | 아니요 | 아니요 |

## <a name="microsoftdatashare"></a>DataShare

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | servers | 예 | 예 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | servers | 예 | 예 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | servergroups | 아니요 | 아니요 |
> | servers | 예 | 예 |
> | serversv2 | 예 | 예 |
> | singleservers | 예 | 예 |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | artifactsources | 예 | 예 |
> | rollouts | 예 | 예 |
> | servicetopologies | 예 | 예 |
> | servicetopologies/서비스 | 예 | 예 |
> | servicetopologies/서비스/serviceunits | 예 | 예 |
> | 단계 | 예 | 예 |

## <a name="microsoftdesktopvirtualization"></a>Microsoft DesktopVirtualization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgroups | 아니요 | 아니요 |
> | hostpools | 아니요 | 아니요 |
> | workspaces | 아니요 | 아니요 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | elasticpools | 아니요 | 아니요 |
> | elasticpools / iothubtenants | 아니요 | 아니요 |
> | iothubs | 예 | 예 |
> | provisioningservices | 예 | 예 |

## <a name="microsoftdevops"></a>Microsoft DevOps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 파이프라인 | 예 | 예 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | controllers | 예 | 예 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | labcenters | 아니요 | 아니요 |
> | labs | 예 | 아니요 |
> | 랩/환경 | 예 | 예 |
> | labs/servicerunners | 예 | 예 |
> | labs/virtualmachines | 예 | 아니요 |
> | schedules | 예 | 예 |

## <a name="microsoftdigitaltwins"></a>DigitalTwins

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | 아니요 | 아니요 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | databaseaccounts | 예 | 예 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domains | 예 | 예 |

## <a name="microsoftenterpriseknowledgegraph"></a>EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | 예 | 예 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domains | 예 | 예 |
> | eventSubscriptions | 아니요-독립적으로 이동할 수 없지만 구독 된 리소스와 함께 자동으로 이동 됩니다. | 아니요-독립적으로 이동할 수 없지만 구독 된 리소스와 함께 자동으로 이동 됩니다. |
> | eventsubscriptions | 아니요-독립적으로 이동할 수 없지만 구독 된 리소스와 함께 자동으로 이동 됩니다. | 아니요-독립적으로 이동할 수 없지만 구독 된 리소스와 함께 자동으로 이동 됩니다. |
> | extensiontopics | 아니요 | 아니요 |
> | 네임 스페이스 | 예 | 예 |
> | 파트너 등록 | 아니요 | 아니요 |
> | 항목 항목 | 예 | 예 |
> | systemtopics | 예 | 예 |
> | topics | 예 | 예 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | 예 | 예 |
> | 네임스페이스 | 예 | 예 |

## <a name="microsoftexperimentation"></a>Microsoft. 실험

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | experimentworkspaces | 아니요 | 아니요 |

## <a name="microsoftfalcon"></a>Microsoft.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 네임스페이스 | 예 | 예 |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아니요 | 아니요 |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | automanagedaccounts | 아니요 | 아니요 |
> | automanagedvmconfigurationprofiles | 아니요 | 아니요 |
> | guestconfigurationassignments | 아니요 | 아니요 |
> | software | 아니요 | 아니요 |
> | softwareupdateprofile | 아니요 | 아니요 |
> | 업데이트 | 아니요 | 아니요 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hanainstances | 아니요 | 아니요 |
> | sapmonitors | 예 | 예 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | 예 | 예 |

> [!IMPORTANT]
> 새 구독 또는 리소스 그룹에 HDInsight 클러스터를 이동할 수 있습니다. 그러나 HDInsight 클러스터에 연결된 네트워킹 리소스(예: Virtual Network, NIC 또는 부하 분산 장치)는 구독 간에 이동할 수 없습니다. 또한 클러스터에 대한 가상 머신에 연결된 NIC를 새 리소스 그룹으로 이동할 수 없습니다.
>
> HDInsight 클러스터를 새 구독으로 이동할 때 먼저 다른 리소스(예: 스토리지 계정)를 이동합니다. 그런 다음 자체적으로 HDInsight 클러스터를 이동합니다.

## <a name="microsofthealthcareapis"></a>HealthcareApis

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | 예 | 예 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 머신의 | 예 | 예 |
> | 컴퓨터/확장 | 아니요 | 아니요 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datamanagers | 예 | 예 |

## <a name="microsofthydra"></a>Microsoft 히드라

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | components | 아니요 | 아니요 |
> | networkscopes | 아니요 | 아니요 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | 예 | 예 |

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | actiongroups | 예 | 예 |
> | activitylogalerts | 아니요 | 아니요 |
> | alertrules | 예 | 예 |
> | autoscalesettings | 예 | 예 |
> | baseline | 아니요 | 아니요 |
> | calculatebaseline | 아니요 | 아니요 |
> | components | 예 | 예 |
> | datacollectionrules | 아니요 | 아니요 |
> | diagnosticsettings | 아니요 | 아니요 |
> | diagnosticsettingscategories | 아니요 | 아니요 |
> | eventtypes | 아니요 | 아니요 |
> | extendeddiagnosticsettings | 아니요 | 아니요 |
> | guestdiagnosticsettings | 아니요 | 아니요 |
> | logdefinitions | 아니요 | 아니요 |
> | 로그 | 아니요 | 아니요 |
> | metricalerts | 아니요 | 아니요 |
> | metricbaselines | 아니요 | 아니요 |
> | metricdefinitions | 아니요 | 아니요 |
> | metricnamespaces | 아니요 | 아니요 |
> | 메트릭 | 아니요 | 아니요 |
> | myworkbooks 문서 | 아니요 | 아니요 |
> | notificationgroups | 아니요 | 아니요 |
> | privatelinkscopes | 예 | 예 |
> | scheduledqueryrules | 예 | 예 |
> | 토폴로지 | 아니요 | 아니요 |
> | 트랜잭션 | 아니요 | 아니요 |
> | vminsightsonboardingstatuses | 아니요 | 아니요 |
> | webtests | 예 | 예 |
> | workbooks | 예 | 예 |
> | 통합 문서의 템플릿 | 예 | 예 |

> [!IMPORTANT]
> 새 구독으로의 이동이 [구독 할당량](azure-subscription-service-limits.md#azure-monitor-limits)을 초과 하지 않는지 확인 합니다.

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | iotapps | 예 | 예 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 예 | 예 |
> | graph | 예 | 예 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hsmpools | 아니요 | 아니요 |
> | vaults | 예 | 예 |

> [!IMPORTANT]
> 디스크 암호화에 사용 되는 키 자격 증명 모음은 동일한 구독의 리소스 그룹 또는 구독 간에 이동할 수 없습니다.

## <a name="microsoftkubernetes"></a>Kubernetes

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | connectedclusters | 아니요 | 아니요 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | 예 | 예 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | labaccounts | 아니요 | 아니요 |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아니요 | 아니요 |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아니요 | 아니요 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | 아니요 | 아니요 |
> | integrationaccounts | 예 | 예 |
> | integrationserviceenvironments | 예 | 아니요 |
> | integrationserviceenvironments/연결은 | 예 | 아니요 |
> | isolatedenvironments | 아니요 | 아니요 |
> | workflows | 예 | 예 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | commitmentplans | 아니요 | 아니요 |
> | webservices | 예 | 아니요 |
> | workspaces | 예 | 예 |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | 아니요 | 아니요 |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아니요 | 아니요 |
> | 계정/작업 영역 | 아니요 | 아니요 |
> | 계정/작업 영역/프로젝트 | 아니요 | 아니요 |
> | teamaccounts | 아니요 | 아니요 |
> | teamaccounts/작업 영역 | 아니요 | 아니요 |
> | teamaccounts/workspace/projects | 아니요 | 아니요 |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아니요 | 아니요 |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingaccounts | 아니요 | 아니요 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | 아니요 | 아니요 |
> | 작업 영역/계산 | 아니요 | 아니요 |

## <a name="microsoftmaintenance"></a>Microsoft. 유지 관리

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applyupdates | 아니요 | 아니요 |
> | configurationassignments | 아니요 | 아니요 |
> | maintenanceconfigurations | 예 | 예 |
> | 업데이트 | 아니요 | 아니요 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | ID | 아니요 | 아니요 |
> | userassignedidentities | 아니요 | 아니요 |

## <a name="microsoftmanagednetwork"></a>Microsoft ManagedNetwork

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managednetworks | 아니요 | 아니요 |
> | managednetworks/managednetworkgroups | 아니요 | 아니요 |
> | managednetworks/managednetworkpeering정책만 | 아니요 | 예 |
> | 알림 | 아니요 | 아니요 |

## <a name="microsoftmanagedservices"></a>Microsoft ManagedServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | registrationassignments | 아니요 | 아니요 |
> | registrationdefinitions | 아니요 | 아니요 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |
> | 계정/privateatlases | 예 | 예 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | classicdevservices | 아니요 | 아니요 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | mediaservices | 예 | 예 |
> | windowsazure.mediaservices/liveevents | 예 | 예 |
> | windowsazure.mediaservices/streamingendpoints | 예 | 예 |

## <a name="microsoftmicroservices4spring"></a>Microservices4Spring

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | appclusters | 아니요 | 아니요 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | 예 | 예 |
> | migrateprojects | 예 | 예 |
> | movecollections | 아니요 | 아니요 |
> | projects | 아니요 | 아니요 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | netappaccounts | 아니요 | 아니요 |
> | netappaccounts/backuppolicies | 아니요 | 아니요 |
> | netappaccounts/capacitypools | 아니요 | 아니요 |
> | netappaccounts/capacitypools/볼륨 | 아니요 | 아니요 |
> | netappaccounts/capacitypools/볼륨/mounttargets | 아니요 | 아니요 |
> | netappaccounts/capacitypools/볼륨/스냅숏 | 아니요 | 아니요 |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgateways | 아니요 | 아니요 |
> | applicationgatewaywebapplicationfirewallpolicies | 아니요 | 아니요 |
> | applicationsecuritygroups | 예 | 예 |
> | azurefirewalls | 예 | 예 |
> | bastionhosts | 아니요 | 아니요 |
> | connections | 예 | 예 |
> | ddoscustompolicies | 예 | 예 |
> | ddosprotectionplans | 아니요 | 아니요 |
> | dnszones | 예 | 예 |
> | expressroutecircuits | 아니요 | 아니요 |
> | expressroutegateways | 아니요 | 아니요 |
> | firewallpolicies | 예 | 예 |
> | frontdoors | 아니요 | 아니요 |
> | frontdoorwebapplicationfirewallpolicies | 아니요 | 아니요 |
> | ipgroups | 예 | 예 |
> | loadbalancers | 예-기본 SKU<br>안 함-표준 SKU | 예-기본 SKU<br>안 함-표준 SKU |
> | localnetworkgateways | 예 | 예 |
> | natgateways | 예 | 예 |
> | networkexperimentprofiles | 예 | 예 |
> | networkintentpolicies | 예 | 예 |
> | networkinterfaces | 예 | 예 |
> | networkprofiles | 아니요 | 아니요 |
> | networksecuritygroups | 예 | 예 |
> | networkwatchers | 예 | 아니요 |
> | networkwatchers/connectionmonitors | 예 | 아니요 |
> | networkwatchers/flowlogs | 예 | 아니요 |
> | networkwatchers/ | 예 | 아니요 |
> | p2svpngateways | 아니요 | 아니요 |
> | privatednszones | 예 | 예 |
> | privatednszones / virtualnetworklinks | 예 | 예 |
> | privateendpointredirectmaps | 아니요 | 아니요 |
> | privateendpoints | 예 | 예 |
> | privatelinkservices | 아니요 | 아니요 |
> | publicipaddresses | 예-기본 SKU<br>안 함-표준 SKU | 예-기본 SKU<br>안 함-표준 SKU |
> | publicipprefixes | 예 | 예 |
> | routefilters | 아니요 | 아니요 |
> | routetables | 예 | 예 |
> | serviceendpointpolicies | 예 | 예 |
> | trafficmanagerprofiles | 예 | 예 |
> | virtualhubs | 아니요 | 아니요 |
> | virtualnetworkgateways | 예 | 예 |
> | virtualnetworks | 예 | 예 |
> | virtualnetworktaps | 아니요 | 아니요 |
> | virtualrouters | 예 | 예 |
> | virtualwans | 아니요 | 아니요 |
> | vpngateways (가상 WAN) | 아니요 | 아니요 |
> | vpnserverconfigurations | 아니요 | 아니요 |
> | vpnsites (가상 WAN) | 아니요 | 아니요 |
> | webapplicationfirewallpolicies | 예 | 예 |

> [!IMPORTANT]
> [네트워킹 이동 지침](./move-limitations/networking-move-limitations.md)을 참조 하세요.

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 네임스페이스 | 예 | 예 |
> | 네임 스페이스/notificationhubs | 예 | 예 |

## <a name="microsoftobjectstore"></a>Microsoft ObjectStore

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | osnamespaces 스페이스 | 예 | 예 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | 아니요 | 아니요 |
> | storageinsightconfigs | 아니요 | 아니요 |
> | workspaces | 예 | 예 |

> [!IMPORTANT]
> 새 구독으로 이동 하는 것이 [구독 할당량](azure-subscription-service-limits.md#azure-monitor-limits)을 초과 하지 않는지 확인 합니다.
> 
> 연결 된 automation 계정이 있는 작업 영역은 이동할 수 없습니다. 이동 작업을 시작 하기 전에 automation 계정의 연결을 해제 해야 합니다.   

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managementassociations | 아니요 | 아니요 |
> | managementconfigurations | 예 | 예 |
> | solutions | 예 | 예 |
> | 뷰 | 예 | 예 |

## <a name="microsoftpeering"></a>Microsoft 피어 링

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 피어 링 | 예 | 예 |
> | peeringservices | 아니요 | 아니요 |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | policyevents | 아니요 | 아니요 |
> | policystates | 아니요 | 아니요 |
> | policytrackedresources | 아니요 | 아니요 |
> | remediations | 아니요 | 아니요 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dashboards | 예 | 예 |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | rootresources | 아니요 | 아니요 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspacecollections | 예 | 예 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | capacities | 예 | 예 |

## <a name="microsoftprojectbabylon"></a>ProjectBabylon

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아니요 | 아니요 |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아니요 | 아니요 |

## <a name="microsoftproviderhub"></a>Microsoft ProviderHub

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | rollouts | 아니요 | 아니요 |

## <a name="microsoftquantum"></a>Microsoft 양자

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | 아니요 | 아니요 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | backupprotecteditems | 아니요 | 아니요 |
> | replicationeligibilityresults | 아니요 | 아니요 |
> | vaults | 예 | 예 |

> [!IMPORTANT]
> [Recovery Services 이동 가이드](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)를 참조 하세요.

## <a name="microsoftredhatopenshift"></a>Microsoft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | openshiftclusters | 아니요 | 아니요 |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 네임스페이스 | 예 | 예 |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 쿼리 | 예 | 예 |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availabilitystatuses | 아니요 | 아니요 |
> | childavailabilitystatuses | 아니요 | 아니요 |
> | childresources | 아니요 | 아니요 |
> | events | 아니요 | 아니요 |
> | 알림 | 아니요 | 아니요 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | deploymentscripts | 아니요 | 아니요 |
> | 링크 | 아니요 | 아니요 |
> | tags | 아니요 | 아니요 |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 애플리케이션 | 예 | 아니요 |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | searchservices | 예 | 예 |

> [!IMPORTANT]
> 한 번의 작업으로 다른 지역에 있는 여러 검색 리소스를 이동할 수 없습니다. 대신 별도 작업으로 이동합니다.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | 아니요 | 아니요 |
> | advancedthreatprotectionsettings | 아니요 | 아니요 |
> | assessmentmetadata | 아니요 | 아니요 |
> | 평가 | 아니요 | 아니요 |
> | 자동화 | 예 | 예 |
> | complianceresults | 아니요 | 아니요 |
> | 규격 | 아니요 | 아니요 |
> | datacollectionagents | 아니요 | 아니요 |
> | devicesecuritygroups | 아니요 | 아니요 |
> | informationprotectionpolicies | 아니요 | 아니요 |
> | iotsecuritysolutions | 예 | 예 |
> | servervulnerabilityassessments | 아니요 | 아니요 |

## <a name="microsoftsecurityinsights"></a>Microsoft SecurityInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | aggregations | 아니요 | 아니요 |
> | alertrules | 아니요 | 아니요 |
> | alertruletemplates | 아니요 | 아니요 |
> | 책갈피 | 아니요 | 아니요 |
> | cases | 아니요 | 아니요 |
> | dataconnectors | 아니요 | 아니요 |
> | dataconnectorscheckrequirements | 아니요 | 아니요 |
> | 엔터티 | 아니요 | 아니요 |
> | entityqueries | 아니요 | 아니요 |
> | 사고 | 아니요 | 아니요 |
> | officeconsents | 아니요 | 아니요 |
> | 설정 | 아니요 | 아니요 |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | gateways | 아니요 | 아니요 |
> | nodes | 아니요 | 아니요 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 네임스페이스 | 예 | 예 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 애플리케이션 | 아니요 | 아니요 |
> | clusters | 예 | 예 |
> | 클러스터/응용 프로그램 | 아니요 | 아니요 |
> | containergroups | 아니요 | 아니요 |
> | containergroupsets | 아니요 | 아니요 |
> | edgeclusters | 아니요 | 아니요 |
> | managedclusters | 아니요 | 아니요 |
> | networks | 아니요 | 아니요 |
> | secretstores | 아니요 | 아니요 |
> | volumes | 아니요 | 아니요 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 애플리케이션 | 예 | 예 |
> | containergroups | 아니요 | 아니요 |
> | gateways | 예 | 예 |
> | networks | 예 | 예 |
> | secrets | 예 | 예 |
> | volumes | 예 | 예 |

## <a name="microsoftservices"></a>Microsoft 서비스

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | rollouts | 아니요 | 아니요 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | signalr | 예 | 예 |

## <a name="microsoftsoftwareplan"></a>SoftwarePlan

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hybridusebenefits | 아니요 | 아니요 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | 아니요 | 아니요 |
> | 애플리케이션 | 아니요 | 아니요 |
> | jitrequests | 아니요 | 아니요 |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | instancepools | 아니요 | 아니요 |
> | 위치 | 예 | 예 |
> | managedinstances | 아니요 | 아니요 |
> | managedinstances/데이터베이스 | 아니요 | 아니요 |
> | servers | 예 | 예 |
> | 서버/데이터베이스 | 예 | 예 |
> | servers/elasticpools | 예 | 예 |
> | 서버/jobaccounts | 예 | 예 |
> | 서버/jobagents | 예 | 예 |
> | virtualclusters | 예 | 예 |

> [!IMPORTANT]
> 데이터베이스와 서버는 동일한 리소스 그룹에 있어야 합니다. SQL Server를 이동하면 모든 해당 데이터베이스도 함께 이동합니다. 이 동작은 Azure SQL Database 및 Azure SQL Data Warehouse 데이터베이스에 적용됩니다.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | 예 | 예 |
> | sqlvirtualmachines | 예 | 예 |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dwvm | 아니요 | 아니요 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storageaccounts | 예 | 예 |

## <a name="microsoftstoragecache"></a>StorageCache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 캐시 | 아니요 | 아니요 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | 예 | 예 |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | 아니요 | 아니요 |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | 아니요 | 아니요 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managers | 아니요 | 아니요 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | streamingjobs | 예 | 예 |

> [!IMPORTANT]
> Stream Analytics 작업은 실행 중 상태일 때 이동할 수 없습니다.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | 아니요 | 아니요 |
> | 환경/s s o 원본 | 아니요 | 아니요 |
> | 인스턴스 | 아니요 | 아니요 |
> | 인스턴스/환경 | 아니요 | 아니요 |
> | 인스턴스/환경/s s o 원본 | 아니요 | 아니요 |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | createsubscription | 아니요 | 아니요 |

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | supporttickets | 아니요 | 아니요 |

## <a name="microsoftsynapse"></a>Synapse

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | 아니요 | 아니요 |
> | 작업 영역/bigdatapools | 아니요 | 아니요 |
> | workspace/sqlpools | 아니요 | 아니요 |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | providerregistrations | 아니요 | 아니요 |
> | 리소스 | 아니요 | 아니요 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | 예 | 예 |
> | 환경/s s o 원본 | 예 | 예 |
> | environment/referencedatasets | 예 | 예 |

## <a name="microsofttoken"></a>Microsoft 토큰

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | stores | 예 | 예 |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | imagetemplates | 아니요 | 아니요 |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | 아니요 | 아니요 |
> | 계정/확장 | 아니요 | 아니요 |
> | 계정/프로젝트 | 아니요 | 아니요 |

> [!IMPORTANT]
> Azure DevOps에 대 한 구독을 변경 하려면 [청구에 사용 되는 azure 구독 변경](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)을 참조 하세요.

## <a name="microsoftvmwarecloudsimple"></a>VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | 아니요 | 아니요 |
> | dedicatedcloudservices | 아니요 | 아니요 |
> | virtualmachines | 아니요 | 아니요 |

## <a name="microsoftvnfmanager"></a>Microsoft VnfManager

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 디바이스 | 아니요 | 아니요 |
> | vnfs | 아니요 | 아니요 |

## <a name="microsoftvsonline"></a>Microsoft VSOnline

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아니요 | 아니요 |
> | 플랜 | 아니요 | 아니요 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | certificates | 예 | 예 |
> | connectiongateways | 예 | 예 |
> | connections | 예 | 예 |
> | customapis | 예 | 예 |
> | hostingenvironments | 아니요 | 아니요 |
> | kubeenvironments | 예 | 예 |
> | serverfarms | 예 | 예 |
> | sites | 예 | 예 |
> | 사이트/premieraddons | 예 | 예 |
> | 사이트/슬롯 | 예 | 예 |
> | staticsites | 아니요 | 아니요 |

> [!IMPORTANT]
> [App Service 이동 가이드](./move-limitations/app-service-move-limitations.md)를 참조 하세요.

## <a name="microsoftwindowsesu"></a>Microsoft WindowsESU

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | multipleactivationkeys | 아니요 | 아니요 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | deviceservices | 아니요 | 아니요 |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | components | 아니요 | 아니요 |
> | monitorinstances | 아니요 | 아니요 |
> | monitors | 아니요 | 아니요 |
> | notificationsettings | 아니요 | 아니요 |

## <a name="third-party-services"></a>타사 서비스

타사 서비스는 현재 이동 작업을 지원하지 않습니다.

## <a name="next-steps"></a>다음 단계
리소스를 이동하는 명령은 [새 리소스 그룹 또는 구독으로 리소스 이동](move-resource-group-and-subscription.md)을 참조하세요.

쉼표로 구분된 값 파일과 동일한 데이터를 가져오려면 [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv) 파일을 다운로드합니다.
