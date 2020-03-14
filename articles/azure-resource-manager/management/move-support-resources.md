---
title: 리소스 유형별 이동 작업 지원
description: 새 리소스 그룹이나 구독으로 이동할 수 있는 Azure 리소스 종류를 소개합니다.
ms.topic: conceptual
ms.date: 02/26/2020
ms.openlocfilehash: 8ab194ad240e4f3e0994314ef9ade3bc7159cf81
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79273931"
---
# <a name="move-operation-support-for-resources"></a>리소스에 대한 이동 작업 지원
이 문서에서는 Azure 리소스 종류가 이동 작업을 지원하는지 여부에 대한 정보를 제공합니다. 또한 리소스를 이동할 때 고려해 야 할 특수 조건에 대 한 정보를 제공 합니다.

리소스 공급자 네임 스페이스로 이동 합니다.
> [!div class="op_single_selector"]
> - [Microsoft AAD](#microsoftaad)
> - [aadiam](#microsoftaadiam)
> - [Microsoft Advisor](#microsoftadvisor)
> - [AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.analysisservices.sharepoint.integration.dll](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft AppConfiguration](#microsoftappconfiguration)
> - [Microsoft AppPlatform](#microsoftappplatform)
> - [AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft AzureData](#microsoftazuredata)
> - [Microsoft AzureStack](#microsoftazurestack)
> - [Microsoft Batch](#microsoftbatch)
> - [Microsoft BatchAI](#microsoftbatchai)
> - [Microsoft 청구](#microsoftbilling)
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
> - [Microsoft 소비량](#microsoftconsumption)
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
> - [Microsoft DataProtection](#microsoftdataprotection)
> - [DataShare](#microsoftdatashare)
> - [DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft DBforMySQL](#microsoftdbformysql)
> - [DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft DevOps](#microsoftdevops)
> - [Microsoft DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft DomainRegistration](#microsoftdomainregistration)
> - [EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [HybridData](#microsofthybriddata)
> - [Microsoft ImportExport](#microsoftimportexport)
> - [microsoft insights](#microsoftinsights)
> - [Microsoft. IoTCentral](#microsoftiotcentral)
> - [Microsoft. IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Kubernetes](#microsoftkubernetes)
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
> - [Microsoft ManagedServices](#microsoftmanagedservices)
> - [Microsoft Maps](#microsoftmaps)
> - [MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft 마이그레이션](#microsoftmigrate)
> - [Microsoft NetApp](#microsoftnetapp)
> - [Microsoft 네트워크](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft ObjectStore](#microsoftobjectstore)
> - [OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.operationsmanagement](#microsoftoperationsmanagement)
> - [Microsoft 피어 링](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft 포털](#microsoftportal)
> - [PortalSdk](#microsoftportalsdk)
> - [Microsoft PowerBI](#microsoftpowerbi)
> - [Microsoft. PowerBIDedicated](#microsoftpowerbidedicated)
> - [ProjectBabylon](#microsoftprojectbabylon)
> - [ProjectOxford](#microsoftprojectoxford)
> - [Microsoft ProviderHub](#microsoftproviderhub)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft Relay](#microsoftrelay)
> - [Microsoft ResourceGraph](#microsoftresourcegraph)
> - [Microsoft ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft SaaS](#microsoftsaas)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft 서비스](#microsoftservices)
> - [SignalRService](#microsoftsignalrservice)
> - [SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft 솔루션](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft. SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.storagesync](#microsoftstoragesync)
> - [Microsoft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft StorSimple](#microsoftstorsimple)
> - [Microsoft StreamAnalytics](#microsoftstreamanalytics)
> - [StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft 구독](#microsoftsubscription)
> - [microsoft.support](#microsoftsupport)
> - [Microsoft TerraformOSS](#microsoftterraformoss)
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft 토큰](#microsofttoken)
> - [visualstudio](#microsoftvisualstudio)
> - [VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft WindowsIoT](#microsoftwindowsiot)
> - [WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domainservices | 예 | 예 |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | tenants | 예 | 예 |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 구성 | 예 | 예 |
> | 동영상 추천 기능 | 예 | 예 |
> | suppressions | 예 | 예 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | actionrules | yes | yes |
> | 경고 | 예 | 예 |
> | alertssummary | 예 | 예 |
> | smartdetectoralertrules | yes | yes |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | servers | yes | yes |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 서비스 | yes | yes |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | configurationstores | yes | yes |

## <a name="microsoftappplatform"></a>Microsoft AppPlatform

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 스프링 | yes | yes |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | apiapps | 예 | 예 |
> | appidentities | 예 | 예 |
> | gateways | 예 | 예 |

> [!IMPORTANT]
> [App Service 이동 가이드](./move-limitations/app-service-move-limitations.md)를 참조 하세요.

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checkaccess | 예 | 예 |
> | denyassignments | 예 | 예 |
> | findorphanroleassignments | 예 | 예 |
> | locks | 예 | 예 |
> | 권한 | 예 | 예 |
> | policyassignments | 예 | 예 |
> | policydefinitions | 예 | 예 |
> | policysetdefinitions | 예 | 예 |
> | roleassignments | 예 | 예 |
> | roleassignmentsusagemetrics | 예 | 예 |
> | roledefinitions | 예 | 예 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | automationaccounts | yes | yes |
> | automationaccounts/구성 | yes | yes |
> | automationaccounts/runbook | yes | yes |

> [!IMPORTANT]
> Runbook은 Automation 계정과 동일한 리소스 그룹에 있어야 합니다.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | yes | yes |

## <a name="microsoftazuredata"></a>Microsoft AzureData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hybriddatamanagers | 예 | 예 |
> | postgresinstances | 예 | 예 |
> | sqlbigdataclusters | 예 | 예 |
> | sqlinstances | 예 | 예 |
> | sqlserverregistrations | yes | yes |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | registrations | yes | yes |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | batchaccounts | yes | yes |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | 예 | 예 |
> | fileservers | 예 | 예 |
> | jobs | 예 | 예 |
> | workspaces | 예 | 예 |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | billingperiods | 예 | 예 |
> | billingpermissions | 예 | 예 |
> | billingroleassignments | 예 | 예 |
> | billingroledefinitions | 예 | 예 |
> | createbillingroleassignment | 예 | 예 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | mapapis | 예 | 예 |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | biztalk | 예 | 예 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | 예 | 예 |
> | 감시자 | 예 | 예 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | 예 | 예 |
> | blueprints | 예 | 예 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | botservices | yes | yes |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | redis | yes | yes |

> [!IMPORTANT]
> Azure Cache for Redis 인스턴스가 가상 네트워크를 사용 하 여 구성 된 경우 인스턴스를 다른 구독으로 이동할 수 없습니다. [네트워킹 이동 제한 사항](./move-limitations/networking-move-limitations.md)을 참조 하세요.

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallpolicies | yes | yes |
> | 프로필 | yes | yes |
> | 프로필/끝점 | yes | yes |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | certificateorders | yes | yes |

> [!IMPORTANT]
> [App Service 이동 가이드](./move-limitations/app-service-move-limitations.md)를 참조 하세요.

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domainnames | yes | 예 |
> | virtualmachines | yes | 예 |

> [!IMPORTANT]
> [클래식 배포 이동 지침](./move-limitations/classic-model-move-limitations.md)을 참조 하세요. 클래식 배포 리소스는 해당 시나리오와 관련 된 작업으로 구독 간에 이동할 수 있습니다.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | 예 | 예 |
> | reservedips | 예 | 예 |
> | virtualnetworks | 예 | 예 |

> [!IMPORTANT]
> [클래식 배포 이동 지침](./move-limitations/classic-model-move-limitations.md)을 참조 하세요. 클래식 배포 리소스는 해당 시나리오와 관련 된 작업으로 구독 간에 이동할 수 있습니다.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storageaccounts | yes | 예 |

> [!IMPORTANT]
> [클래식 배포 이동 지침](./move-limitations/classic-model-move-limitations.md)을 참조 하세요. 클래식 배포 리소스는 해당 시나리오와 관련 된 작업으로 구독 간에 이동할 수 있습니다.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | yes | yes |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availabilitysets | yes | yes |
> | disk; 집합 | 예 | 예 |
> | disks | yes | yes |
> | galleries | 예 | 예 |
> | 갤러리/이미지 | 예 | 예 |
> | 갤러리/이미지/버전 | 예 | 예 |
> | hostgroups | 예 | 예 |
> | 호스트 그룹/호스트 | 예 | 예 |
> | images | yes | yes |
> | proximityplacementgroups | 예 | 예 |
> | restorepointcollections | 예 | 예 |
> | sharedvmimages | 예 | 예 |
> | sharedvmimages/버전 | 예 | 예 |
> | 스냅샷 | yes | yes |
> | virtualmachines | yes | yes |
> | virtualmachines/확장 | yes | yes |
> | virtualmachinescalesets | yes | yes |

> [!IMPORTANT]
> [Virtual Machines 이동 가이드](./move-limitations/virtual-machines-move-limitations.md)를 참조 하세요.

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | aggregatedcost | 예 | 예 |
> | 잔고 | 예 | 예 |
> | 예산의 | 예 | 예 |
> | 요금 | 예 | 예 |
> | costtags | 예 | 예 |
> | credits | 예 | 예 |
> | events | 예 | 예 |
> | 예측 | 예 | 예 |
> | lots | 예 | 예 |
> | 마켓플레이스 | 예 | 예 |
> | operationresults | 예 | 예 |
> | operationstatus | 예 | 예 |
> | pricesheets | 예 | 예 |
> | products | 예 | 예 |
> | reservationdetails | 예 | 예 |
> | reservationrecommendations | 예 | 예 |
> | reservationsummaries | 예 | 예 |
> | reservationtransactions | 예 | 예 |
> | tags | 예 | 예 |
> | tenants | 예 | 예 |
> | terms | 예 | 예 |
> | 사용량 세부 정보 | 예 | 예 |

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | 예 | 예 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | 예 | 예 |
> | serviceassociationlinks | 예 | 예 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | registries | yes | yes |
> | 레지스트리/buildtasks | yes | yes |
> | 레지스트리/복제 | yes | yes |
> | 레지스트리/taskruns | yes | yes |
> | 레지스트리/작업 | yes | yes |
> | 레지스트리/웹 후크 | yes | yes |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containerservices | 예 | 예 |
> | managedclusters | 예 | 예 |
> | openshiftmanagedclusters | 예 | 예 |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 애플리케이션 | 예 | 예 |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 경고 | 예 | 예 |
> | 예산의 | 예 | 예 |
> | 커넥터 | yes | yes |
> | 차원 | 예 | 예 |
> | 내보내기 | 예 | 예 |
> | externalsubscriptions | 예 | 예 |
> | 예측할 | 예 | 예 |
> | Query | 예 | 예 |
> | reportconfigs | 예 | 예 |
> | reports | 예 | 예 |
> | showbackrules | 예 | 예 |
> | 뷰 | 예 | 예 |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hubs | 예 | 예 |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 연결 | 예 | 예 |
> | resourceproviders | yes | yes |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | 예 | 예 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | 예 | 예 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | 예 | 예 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | catalogs | yes | yes |
> | datacatalogs | 예 | 예 |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | 예 | 예 |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 패키지 | 예 | 예 |
> | 플랜 | 예 | 예 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datafactories | yes | yes |
> | factories | yes | yes |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | 예 | 예 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | yes | yes |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | yes | yes |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | 예 | 예 |
> | 서비스/프로젝트 | 예 | 예 |
> | slots | 예 | 예 |

## <a name="microsoftdataprotection"></a>Microsoft DataProtection

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | backupvaults | 예 | 예 |

## <a name="microsoftdatashare"></a>DataShare

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | yes | yes |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | servers | yes | yes |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | servers | yes | yes |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | servergroups | 예 | 예 |
> | servers | yes | yes |
> | serversv2 | yes | yes |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | artifactsources | yes | yes |
> | rollouts | yes | yes |
> | servicetopologies | yes | yes |
> | servicetopologies/서비스 | yes | yes |
> | servicetopologies/서비스/serviceunits | yes | yes |
> | 단계 | yes | yes |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | elasticpools | 예 | 예 |
> | elasticpools / iothubtenants | 예 | 예 |
> | iothubs | yes | yes |
> | provisioningservices | yes | yes |

## <a name="microsoftdevops"></a>Microsoft DevOps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 파이프라인 | yes | yes |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | controllers | yes | yes |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | labcenters | 예 | 예 |
> | labs | yes | 예 |
> | 랩/환경 | yes | yes |
> | labs/servicerunners | yes | yes |
> | labs/virtualmachines | yes | 예 |
> | schedules | yes | yes |

## <a name="microsoftdigitaltwins"></a>DigitalTwins

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | 예 | 예 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | databaseaccounts | yes | yes |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domains | yes | yes |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | yes | yes |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domains | yes | yes |
> | eventSubscriptions | 아니요-독립적으로 이동할 수 없지만 구독 된 리소스와 함께 자동으로 이동 됩니다. | 아니요-독립적으로 이동할 수 없지만 구독 된 리소스와 함께 자동으로 이동 됩니다. |
> | eventsubscriptions | 아니요-독립적으로 이동할 수 없지만 구독 된 리소스와 함께 자동으로 이동 됩니다. | 아니요-독립적으로 이동할 수 없지만 구독 된 리소스와 함께 자동으로 이동 됩니다. |
> | extensiontopics | 예 | 예 |
> | topics | yes | yes |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | yes | yes |
> | 네임스페이스 | yes | yes |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | guestconfigurationassignments | 예 | 예 |
> | software | 예 | 예 |
> | softwareupdateprofile | 예 | 예 |
> | 업데이트 | 예 | 예 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hanainstances | 예 | 예 |
> | sapmonitors | yes | yes |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | yes | yes |

> [!IMPORTANT]
> 새 구독 또는 리소스 그룹에 HDInsight 클러스터를 이동할 수 있습니다. 그러나 HDInsight 클러스터에 연결된 네트워킹 리소스(예: Virtual Network, NIC 또는 부하 분산 장치)는 구독 간에 이동할 수 없습니다. 또한 클러스터에 대한 가상 머신에 연결된 NIC를 새 리소스 그룹으로 이동할 수 없습니다.
>
> HDInsight 클러스터를 새 구독으로 이동할 때 먼저 다른 리소스(예: 스토리지 계정)를 이동합니다. 그런 다음 자체적으로 HDInsight 클러스터를 이동합니다.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | yes | yes |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 머신의 | yes | yes |
> | 컴퓨터/확장 | 예 | 예 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datamanagers | yes | yes |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | yes | yes |

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | actiongroups | yes | yes |
> | activitylogalerts | 예 | 예 |
> | alertrules | yes | yes |
> | autoscalesettings | yes | yes |
> | baseline | 예 | 예 |
> | calculatebaseline | 예 | 예 |
> | components | yes | yes |
> | diagnosticsettings | 예 | 예 |
> | diagnosticsettingscategories | 예 | 예 |
> | eventtypes | 예 | 예 |
> | extendeddiagnosticsettings | 예 | 예 |
> | logdefinitions | 예 | 예 |
> | 로그 | 예 | 예 |
> | metricalerts | 예 | 예 |
> | metricbaselines | 예 | 예 |
> | metricdefinitions | 예 | 예 |
> | metricnamespaces | 예 | 예 |
> | 메트릭 | 예 | 예 |
> | myworkbooks 문서 | 예 | 예 |
> | privatelinkscopes | yes | yes |
> | scheduledqueryrules | yes | yes |
> | 토폴로지 | 예 | 예 |
> | 트랜잭션 | 예 | 예 |
> | vminsightsonboardingstatuses | 예 | 예 |
> | webtests | yes | yes |
> | workbooks | yes | yes |
> | 통합 문서의 템플릿 | yes | yes |

> [!IMPORTANT]
> 새 구독으로의 이동이 [구독 할당량](azure-subscription-service-limits.md#azure-monitor-limits)을 초과 하지 않는지 확인 합니다.

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | iotapps | yes | yes |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | yes | yes |
> | graph | yes | yes |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | vaults | yes | yes |

> [!IMPORTANT]
> 디스크 암호화에 사용 되는 키 자격 증명 모음은 동일한 구독의 리소스 그룹 또는 구독 간에 이동할 수 없습니다.

## <a name="microsoftkubernetes"></a>Kubernetes

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | connectedclusters | 예 | 예 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | yes | yes |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | labaccounts | 예 | 예 |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | 예 | 예 |
> | integrationaccounts | yes | yes |
> | integrationserviceenvironments | yes | 예 |
> | integrationserviceenvironments/연결은 | yes | 예 |
> | isolatedenvironments | 예 | 예 |
> | workflows | yes | yes |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | commitmentplans | yes | yes |
> | webservices | yes | 예 |
> | workspaces | yes | yes |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | 예 | 예 |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |
> | 계정/작업 영역 | 예 | 예 |
> | 계정/작업 영역/프로젝트 | 예 | 예 |
> | teamaccounts | 예 | 예 |
> | teamaccounts/작업 영역 | 예 | 예 |
> | teamaccounts/workspace/projects | 예 | 예 |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingaccounts | 예 | 예 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | 예 | 예 |
> | 작업 영역/계산 | 예 | 예 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | ID | 예 | 예 |
> | userassignedidentities | 예 | 예 |

## <a name="microsoftmanagedservices"></a>Microsoft ManagedServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | registrationassignments | 예 | 예 |
> | registrationdefinitions | 예 | 예 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | yes | yes |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | classicdevservices | 예 | 예 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | mediaservices | yes | yes |
> | windowsazure.mediaservices/liveevents | yes | yes |
> | windowsazure.mediaservices/streamingendpoints | yes | yes |

## <a name="microsoftmicroservices4spring"></a>Microservices4Spring

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | appclusters | 예 | 예 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | yes | yes |
> | migrateprojects | yes | yes |
> | projects | 예 | 예 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | netappaccounts | 예 | 예 |
> | netappaccounts/backuppolicies | 예 | 예 |
> | netappaccounts/capacitypools | 예 | 예 |
> | netappaccounts/capacitypools/볼륨 | 예 | 예 |
> | netappaccounts/capacitypools/볼륨/mounttargets | 예 | 예 |
> | netappaccounts/capacitypools/볼륨/스냅숏 | 예 | 예 |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgateways | 예 | 예 |
> | applicationgatewaywebapplicationfirewallpolicies | 예 | 예 |
> | applicationsecuritygroups | yes | yes |
> | azurefirewalls | yes | yes |
> | bastionhosts | 예 | 예 |
> | connections | yes | yes |
> | ddoscustompolicies | yes | yes |
> | ddosprotectionplans | 예 | 예 |
> | dnszones | yes | yes |
> | expressroutecircuits | 예 | 예 |
> | expressroutegateways | 예 | 예 |
> | firewallpolicies | yes | yes |
> | frontdoors | 예 | 예 |
> | frontdoorwebapplicationfirewallpolicies | 예 | 예 |
> | ipgroups | yes | yes |
> | loadbalancers | 예-기본 SKU<br>안 함-표준 SKU | 예-기본 SKU<br>안 함-표준 SKU |
> | localnetworkgateways | yes | yes |
> | networkexperimentprofiles | yes | yes |
> | networkintentpolicies | yes | yes |
> | networkinterfaces | yes | yes |
> | networkprofiles | 예 | 예 |
> | networksecuritygroups | yes | yes |
> | networkwatchers | yes | 예 |
> | networkwatchers/connectionmonitors | yes | 예 |
> | networkwatchers/flowlogs | yes | 예 |
> | networkwatchers/lenses | yes | 예 |
> | networkwatchers/ | yes | 예 |
> | p2svpngateways | 예 | 예 |
> | privatednszones | yes | yes |
> | privatednszones / virtualnetworklinks | yes | yes |
> | privateendpointredirectmaps | 예 | 예 |
> | privateendpoints | 예 | 예 |
> | privatelinkservices | 예 | 예 |
> | publicipaddresses | 예-기본 SKU<br>안 함-표준 SKU | 예-기본 SKU<br>안 함-표준 SKU |
> | publicipprefixes | yes | yes |
> | routefilters | 예 | 예 |
> | routetables | yes | yes |
> | serviceendpointpolicies | yes | yes |
> | trafficmanagerprofiles | yes | yes |
> | virtualhubs | 예 | 예 |
> | virtualnetworkgateways | yes | yes |
> | virtualnetworks | yes | yes |
> | virtualnetworktaps | 예 | 예 |
> | virtualrouters | yes | yes |
> | virtualwans | 예 | 예 |
> | vpngateways (가상 WAN) | 예 | 예 |
> | vpnserverconfigurations | 예 | 예 |
> | vpnsites (가상 WAN) | 예 | 예 |
> | webapplicationfirewallpolicies | yes | yes |

> [!IMPORTANT]
> [네트워킹 이동 지침](./move-limitations/networking-move-limitations.md)을 참조 하세요.

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 네임스페이스 | yes | yes |
> | 네임 스페이스/notificationhubs | yes | yes |

## <a name="microsoftobjectstore"></a>Microsoft ObjectStore

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | osnamespaces 스페이스 | yes | yes |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storageinsightconfigs | 예 | 예 |
> | workspaces | yes | yes |

> [!IMPORTANT]
> 새 구독으로의 이동이 [구독 할당량](azure-subscription-service-limits.md#azure-monitor-limits)을 초과 하지 않는지 확인 합니다.

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managementassociations | 예 | 예 |
> | managementconfigurations | yes | yes |
> | solutions | yes | yes |
> | 뷰 | yes | yes |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 피어 링 | yes | yes |
> | peeringservices | 예 | 예 |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | policyevents | 예 | 예 |
> | policystates | 예 | 예 |
> | policytrackedresources | 예 | 예 |
> | remediations | 예 | 예 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dashboards | yes | yes |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | rootresources | 예 | 예 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspacecollections | yes | yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | capacities | yes | yes |

## <a name="microsoftprojectbabylon"></a>ProjectBabylon

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |

## <a name="microsoftproviderhub"></a>Microsoft ProviderHub

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | rollouts | 예 | 예 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | backupprotecteditems | 예 | 예 |
> | replicationeligibilityresults | 예 | 예 |
> | vaults | yes | yes |

> [!IMPORTANT]
> [Recovery Services 이동 가이드](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)를 참조 하세요.

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 네임스페이스 | yes | yes |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 쿼리 | yes | yes |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availabilitystatuses | 예 | 예 |
> | childavailabilitystatuses | 예 | 예 |
> | childresources | 예 | 예 |
> | events | 예 | 예 |
> | 알림 | 예 | 예 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | deploymentscripts | 예 | 예 |
> | 링크 | 예 | 예 |
> | tags | 예 | 예 |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 애플리케이션 | yes | 예 |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | searchservices | yes | yes |

> [!IMPORTANT]
> 한 번의 작업으로 다른 지역에 있는 여러 검색 리소스를 이동할 수 없습니다. 대신 별도 작업으로 이동합니다.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | 예 | 예 |
> | advancedthreatprotectionsettings | 예 | 예 |
> | assessmentmetadata | 예 | 예 |
> | 인사 | 예 | 예 |
> | 자동화 | yes | yes |
> | complianceresults | 예 | 예 |
> | 규격 | 예 | 예 |
> | datacollectionagents | 예 | 예 |
> | devicesecuritygroups | 예 | 예 |
> | informationprotectionpolicies | 예 | 예 |
> | iotsecuritysolutions | yes | yes |
> | servervulnerabilityassessments | 예 | 예 |

## <a name="microsoftsecurityinsights"></a>Microsoft SecurityInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | aggregations | 예 | 예 |
> | alertrules | 예 | 예 |
> | alertruletemplates | 예 | 예 |
> | 책갈피 | 예 | 예 |
> | cases | 예 | 예 |
> | dataconnectors | 예 | 예 |
> | dataconnectorscheckrequirements | 예 | 예 |
> | 엔터티 | 예 | 예 |
> | entityqueries | 예 | 예 |
> | 사고 | 예 | 예 |
> | officeconsents | 예 | 예 |
> | 설정 | 예 | 예 |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | gateways | 예 | 예 |
> | nodes | 예 | 예 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 네임스페이스 | yes | yes |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 애플리케이션 | 예 | 예 |
> | clusters | yes | yes |
> | 클러스터/응용 프로그램 | 예 | 예 |
> | containergroups | 예 | 예 |
> | containergroupsets | 예 | 예 |
> | edgeclusters | 예 | 예 |
> | managedclusters | 예 | 예 |
> | networks | 예 | 예 |
> | secretstores | 예 | 예 |
> | volumes | 예 | 예 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 애플리케이션 | yes | yes |
> | gateways | yes | yes |
> | networks | yes | yes |
> | secrets | yes | yes |
> | volumes | yes | yes |

## <a name="microsoftservices"></a>Microsoft 서비스

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | rollouts | 예 | 예 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | signalr | yes | yes |

## <a name="microsoftsoftwareplan"></a>SoftwarePlan

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hybridusebenefits | 예 | 예 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | 예 | 예 |
> | 애플리케이션 | 예 | 예 |
> | jitrequests | 예 | 예 |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | instancepools | 예 | 예 |
> | managedinstances | 예 | 예 |
> | managedinstances/데이터베이스 | 예 | 예 |
> | servers | yes | yes |
> | 서버/데이터베이스 | yes | yes |
> | servers/elasticpools | yes | yes |
> | 서버/jobaccounts | yes | yes |
> | 서버/jobagents | yes | yes |
> | virtualclusters | yes | yes |

> [!IMPORTANT]
> 데이터베이스와 서버는 동일한 리소스 그룹에 있어야 합니다. SQL Server를 이동하면 모든 해당 데이터베이스도 함께 이동합니다. 이 동작은 Azure SQL Database 및 Azure SQL Data Warehouse 데이터베이스에 적용됩니다.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | yes | yes |
> | sqlvirtualmachines | yes | yes |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dwvm | 예 | 예 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storageaccounts | yes | yes |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | yes | yes |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | 예 | 예 |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | 예 | 예 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managers | 예 | 예 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | streamingjobs | yes | yes |

> [!IMPORTANT]
> Stream Analytics 작업은 실행 중 상태일 때 이동할 수 없습니다.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | 예 | 예 |
> | 환경/s s o 원본 | 예 | 예 |
> | 인스턴스 | 예 | 예 |
> | 인스턴스/환경 | 예 | 예 |
> | 인스턴스/환경/s s o 원본 | 예 | 예 |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | createsubscription | 예 | 예 |

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | supporttickets | 예 | 예 |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | providerregistrations | 예 | 예 |
> | 리소스 | 예 | 예 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | yes | yes |
> | 환경/s s o 원본 | yes | yes |
> | environment/referencedatasets | yes | yes |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 저장소 | yes | yes |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | 예 | 예 |
> | 계정/확장 | yes | yes |
> | 계정/프로젝트 | yes | yes |

> [!IMPORTANT]
> Azure DevOps에 대 한 구독을 변경 하려면 [청구에 사용 되는 azure 구독 변경](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)을 참조 하세요.

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | 예 | 예 |
> | dedicatedcloudservices | 예 | 예 |
> | virtualmachines | 예 | 예 |

## <a name="microsoftvsonline"></a>Microsoft VSOnline

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | yes | yes |
> | 플랜 | yes | yes |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | certificates | 예 | yes |
> | connectiongateways | yes | yes |
> | connections | yes | yes |
> | customapis | yes | yes |
> | hostingenvironments | 예 | 예 |
> | serverfarms | yes | yes |
> | sites | yes | yes |
> | 사이트/premieraddons | yes | yes |
> | 사이트/슬롯 | yes | yes |
> | staticsites | 예 | 예 |

> [!IMPORTANT]
> [App Service 이동 가이드](./move-limitations/app-service-move-limitations.md)를 참조 하세요.

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | deviceservices | 예 | 예 |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | components | 예 | 예 |
> | monitorinstances | 예 | 예 |
> | monitors | 예 | 예 |
> | notificationsettings | 예 | 예 |

## <a name="third-party-services"></a>타사 서비스

타사 서비스는 현재 이동 작업을 지원하지 않습니다.

## <a name="next-steps"></a>다음 단계
리소스를 이동하는 명령은 [새 리소스 그룹 또는 구독으로 리소스 이동](move-resource-group-and-subscription.md)을 참조하세요.

쉼표로 구분된 값 파일과 동일한 데이터를 가져오려면 [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv) 파일을 다운로드합니다.
