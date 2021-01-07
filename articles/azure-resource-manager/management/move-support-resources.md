---
title: 리소스 종류별 이동 작업 지원
description: 새 리소스 그룹이나 구독으로 이동할 수 있는 Azure 리소스 종류를 소개합니다.
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 72cc2bd1b391acd2065cc4b775c50af94510a840
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/28/2020
ms.locfileid: "97795904"
---
# <a name="move-operation-support-for-resources"></a>리소스에 대한 이동 작업 지원

이 문서에서는 Azure 리소스 종류가 이동 작업을 지원하는지 여부에 대한 정보를 제공합니다. 또한 리소스를 이동할 때 고려해야 할 특수 조건에 대한 정보도 제공합니다.

> [!IMPORTANT]
> 대부분의 경우 자식 리소스를 부모 리소스와 독립적으로 이동할 수 없습니다. 자식 리소스의 형식에는 리소스 형식이 `<resource-provider-namespace>/<parent-resource>/<child-resource>` 있습니다. 예를 들어 `Microsoft.ServiceBus/namespaces/queues` 는의 자식 리소스입니다 `Microsoft.ServiceBus/namespaces` . 부모 리소스를 이동 하면 자식 리소스가 자동으로 함께 이동 됩니다. 이 문서에 자식 리소스가 표시 되지 않으면 부모 리소스와 함께 이동 하는 것으로 간주할 수 있습니다. 부모 리소스가 이동을 지원 하지 않는 경우 자식 리소스를 이동할 수 없습니다.

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
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
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
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domainservices | 아니오 | 아니오 |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | diagnosticsettings | 아니오 | 아니오 |
> | diagnosticsettingscategories | 아니오 | 아니오 |
> | privatelinkforazuread | 예 | 예 |
> | tenants | 예 | 예 |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | supportproviders | 아니오 | 아니오 |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | aadsupportcases | 아니오 | 아니오 |
> | addsservices | 아니오 | 아니오 |
> | agents | 아니오 | 아니오 |
> | anonymousapiusers | 아니오 | 아니오 |
> | 구성 | 아니오 | 아니오 |
> | 로그 | 아니오 | 아니오 |
> | reports | 아니오 | 아니오 |
> | servicehealthmetrics | 아니오 | 아니오 |
> | services | 아니오 | 아니오 |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 구성 | 아니오 | 아니오 |
> | generaterecommendations | 아니오 | 아니오 |
> | metadata | 아니오 | 아니오 |
> | 동영상 추천 기능 | 아니오 | 아니오 |
> | suppressions | 아니오 | 아니오 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | actionrules | 예 | 예 |
> | 경고 | 아니오 | 아니오 |
> | alertslist | 아니오 | 아니오 |
> | alertsmetadata | 아니오 | 아니오 |
> | alertssummary | 아니오 | 아니오 |
> | alertssummarylist | 아니오 | 아니오 |
> | smartdetectoralertrules | 예 | 예 |
> | smartgroups | 아니오 | 아니오 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | servers | 예 | 예 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!IMPORTANT]
> 소비 SKU로 설정된 API Management 서비스는 이동할 수 없습니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | reportfeedback | 아니오 | 아니오 |
> | 서비스 | 예 | 예 |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | configurationstores | 예 | 예 |
> | configurationstores / eventgridfilters | 아니오 | 아니오 |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | spring | 예 | 예 |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!IMPORTANT]
> [App Service 이동 지침](./move-limitations/app-service-move-limitations.md)을 참조하세요.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | apiapps | 아니오 | 아니오 |
> | appidentities | 아니오 | 아니오 |
> | gateways | 아니오 | 아니요 |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | attestationproviders | 예 | 예 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | classicadministrators | 아니오 | 아니오 |
> | dataaliases | 아니오 | 아니오 |
> | denyassignments | 아니오 | 아니오 |
> | elevateaccess | 아니오 | 아니오 |
> | findorphanroleassignments | 아니오 | 아니오 |
> | locks | 아니오 | 아니오 |
> | 권한 | 아니오 | 아니오 |
> | policyassignments | 아니오 | 아니오 |
> | policydefinitions | 아니오 | 아니오 |
> | policysetdefinitions | 아니오 | 아니오 |
> | privatelinkassociations | 아니오 | 아니오 |
> | resourcemanagementprivatelinks | 아니요 | 아니오 |
> | roleassignments | 아니오 | 아니오 |
> | roleassignmentsusagemetrics | 아니오 | 아니오 |
> | roledefinitions | 아니오 | 아니오 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!IMPORTANT]
> Runbook은 Automation 계정과 동일한 리소스 그룹에 있어야 합니다.
>
> 자세한 내용은 [Azure Automation 계정을 다른 구독으로 이동](../../automation/how-to/move-account.md?toc=/azure/azure-resource-manager/toc.json)을 참조 하세요.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | automationaccounts | 예 | 예 |
> | automationaccounts / configurations | 예 | 예 |
> | automationaccounts / runbooks | 예 | 예 |

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | privateclouds | 예 | 예 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | 예 | 예 |
> | b2ctenants | 아니오 | 아니오 |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datacontrollers | 아니오 | 아니오 |
> | hybriddatamanagers | 아니오 | 아니오 |
> | postgresinstances | 아니오 | 아니오 |
> | sqlinstances | 아니오 | 아니오 |
> | sqlmanagedinstances | 아니오 | 아니오 |
> | sqlserverinstances | 아니오 | 아니오 |
> | sqlserverregistrations | 예 | 예 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | cloudmanifestfiles | 아니오 | 아니오 |
> | registrations | 예 | 예 |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | 아니오 | 아니오 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | batchaccounts | 예 | 예 |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | billingaccounts | 아니오 | 아니오 |
> | billingperiods | 아니오 | 아니오 |
> | billingpermissions | 아니오 | 아니오 |
> | billingproperty | 아니오 | 아니오 |
> | billingroleassignments | 아니오 | 아니오 |
> | billingroledefinitions | 아니오 | 아니오 |
> | departments | 아니오 | 아니오 |
> | enrollmentaccounts | 아니오 | 아니오 |
> | invoices | 아니오 | 아니오 |
> | transfers | 아니오 | 아니오 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | mapapis | 아니오 | 아니오 |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | biztalk | 아니오 | 아니오 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | 아니오 | 아니오 |
> | cordamembers | 아니오 | 아니오 |
> | watchers | 아니오 | 아니오 |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | tokenservices | 아니오 | 아니오 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | 아니오 | 아니오 |
> | blueprints | 아니오 | 아니오 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | botservices | 예 | 예 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!IMPORTANT]
> Azure Cache for Redis 인스턴스가 가상 네트워크를 사용하여 구성되면 이 인스턴스를 다른 구독으로 이동할 수 없습니다. [네트워킹 이동 제한](./move-limitations/networking-move-limitations.md)을 참조하세요.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | redis | 예 | 예 |
> | redisenterprise | 아니오 | 아니오 |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | appliedreservations | 아니오 | 아니오 |
> | calculateexchange | 아니오 | 아니오 |
> | calculateprice | 아니오 | 아니오 |
> | calculatepurchaseprice | 아니오 | 아니오 |
> | catalogs | 아니오 | 아니오 |
> | commercialreservationorders | 아니오 | 아니오 |
> | 교환 | 아니오 | 아니오 |
> | reservationorders | 아니오 | 아니오 |
> | reservations | 아니오 | 아니오 |
> | 리소스 | 아니오 | 아니오 |
> | validatereservationorder | 아니오 | 아니오 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallmanagedrulesets | 아니오 | 아니오 |
> | cdnwebapplicationfirewallpolicies | 예 | 예 |
> | edgenodes | 아니오 | 아니오 |
> | 프로필 | 예 | 예 |
> | profiles / endpoints | 예 | 예 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!IMPORTANT]
> [App Service 이동 지침](./move-limitations/app-service-move-limitations.md)을 참조하세요.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | certificateorders | 예 | 예 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!IMPORTANT]
> [클래식 배포 이동 지침](./move-limitations/classic-model-move-limitations.md)을 참조하세요. 클래식 배포 리소스는 해당 시나리오와 관련된 작업을 통해 구독 간에 이동할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | capabilities | 아니오 | 아니오 |
> | domainnames | 예 | 아니오 |
> | quotas | 아니오 | 아니오 |
> | resourcetypes | 아니오 | 아니오 |
> | validatesubscriptionmoveavailability | 아니오 | 아니오 |
> | virtualmachines | 예 | 예 |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!IMPORTANT]
> [클래식 배포 이동 지침](./move-limitations/classic-model-move-limitations.md)을 참조하세요. 클래식 배포 리소스는 해당 시나리오와 관련된 작업을 통해 구독 간에 이동할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | classicinfrastructureresources | 아니오 | 아니오 |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!IMPORTANT]
> [클래식 배포 이동 지침](./move-limitations/classic-model-move-limitations.md)을 참조하세요. 클래식 배포 리소스는 해당 시나리오와 관련된 작업을 통해 구독 간에 이동할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | capabilities | 아니오 | 아니오 |
> | expressroutecrossconnections | 아니오 | 아니오 |
> | expressroutecrossconnections / peerings | 아니오 | 아니오 |
> | gatewaysupporteddevices | 아니오 | 아니오 |
> | networksecuritygroups | 아니오 | 아니오 |
> | quotas | 아니오 | 아니오 |
> | reservedips | 아니오 | 아니오 |
> | virtualnetworks | 아니오 | 아니오 |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!IMPORTANT]
> [클래식 배포 이동 지침](./move-limitations/classic-model-move-limitations.md)을 참조하세요. 클래식 배포 리소스는 해당 시나리오와 관련된 작업을 통해 구독 간에 이동할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | disks | 아니오 | 아니오 |
> | images | 아니오 | 아니오 |
> | osimages | 아니오 | 아니오 |
> | osplatformimages | 아니오 | 아니오 |
> | publicimages | 아니오 | 아니오 |
> | quotas | 아니오 | 아니오 |
> | storageaccounts | 예 | 아니오 |
> | vmimages | 아니오 | 아니오 |

## <a name="microsoftclassicsubscription"></a>Microsoft.ClassicSubscription

> [!IMPORTANT]
> [클래식 배포 이동 지침](./move-limitations/classic-model-move-limitations.md)을 참조하세요. 클래식 배포 리소스는 해당 시나리오와 관련된 작업을 통해 구독 간에 이동할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 작업 | 아니오 | 아니오 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | ratecard | 아니오 | 아니오 |
> | usageaggregates | 아니오 | 아니오 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!IMPORTANT]
> [Virtual Machines 이동 지침](./move-limitations/virtual-machines-move-limitations.md)을 참조하세요.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availabilitysets | 예 | 예 |
> | diskaccesses | 아니오 | 아니오 |
> | diskencryptionsets | 아니오 | 아니오 |
> | disks | 예 | 예 |
> | galleries | 아니오 | 아니오 |
> | galleries / images | 아니오 | 아니오 |
> | galleries / images / versions | 아니오 | 아니오 |
> | hostgroups | 아니오 | 아니오 |
> | hostgroups / hosts | 아니오 | 아니오 |
> | images | 예 | 예 |
> | proximityplacementgroups | 예 | 예 |
> | restorepointcollections | 아니오 | 아니오 |
> | restorepointcollections / restorepoints | 아니오 | 아니오 |
> | sharedvmextensions | 아니오 | 아니오 |
> | sharedvmimages | 아니오 | 아니오 |
> | sharedvmimages / versions | 아니오 | 아니오 |
> | 스냅샷 | 예 | 예 |
> | sshpublickeys | 아니오 | 아니오 |
> | virtualmachines | 예 | 예 |
> | virtualmachines / extensions | 예 | 예 |
> | virtualmachinescalesets | 예 | 예 |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | aggregatedcost | 아니오 | 아니오 |
> | balances | 아니오 | 아니오 |
> | budgets | 아니오 | 아니오 |
> | charges | 아니오 | 아니오 |
> | costtags | 아니오 | 아니오 |
> | credits | 아니오 | 아니오 |
> | events | 아니오 | 아니오 |
> | forecasts | 아니오 | 아니오 |
> | lots | 아니오 | 아니오 |
> | marketplaces | 아니오 | 아니오 |
> | pricesheets | 아니오 | 아니오 |
> | products | 아니오 | 아니오 |
> | reservationdetails | 아니오 | 아니오 |
> | reservationrecommendationdetails | 아니오 | 아니오 |
> | reservationrecommendations | 아니오 | 아니오 |
> | reservationsummaries | 아니오 | 아니오 |
> | reservationtransactions | 아니오 | 아니오 |
> | tags | 아니오 | 아니오 |
> | tenants | 아니오 | 아니오 |
> | terms | 아니오 | 아니오 |
> | usagedetails | 아니오 | 아니오 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | 아니오 | 아니오 |
> | serviceassociationlinks | 아니오 | 아니오 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | registries | 예 | 예 |
> | registries / agentpools | 예 | 예 |
> | registries / buildtasks | 예 | 예 |
> | registries / replications | 예 | 예 |
> | registries / tasks | 예 | 예 |
> | registries / webhooks | 예 | 예 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containerservices | 아니오 | 아니오 |
> | managedclusters | 아니오 | 아니오 |
> | openshiftmanagedclusters | 아니오 | 아니오 |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 애플리케이션 | 아니오 | 아니오 |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아니오 | 아니오 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 경고 | 아니오 | 아니오 |
> | billingaccounts | 아니오 | 아니오 |
> | budgets | 아니오 | 아니오 |
> | cloudconnectors | 아니오 | 아니오 |
> | 커넥터 | 예 | 예 |
> | departments | 아니오 | 아니오 |
> | dimensions | 아니오 | 아니오 |
> | enrollmentaccounts | 아니오 | 아니오 |
> | exports | 아니오 | 아니오 |
> | externalbillingaccounts | 아니오 | 아니오 |
> | 아니오측 | 아니오 | 아니오 |
> | Query | 아니오 | 아니오 |
> | register | 아니오 | 아니오 |
> | reportconfigs | 아니오 | 아니오 |
> | reports | 아니오 | 아니오 |
> | 설정 | 아니오 | 아니오 |
> | showbackrules | 아니오 | 아니오 |
> | 뷰 | 아니오 | 아니오 |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hubs | 아니오 | 아니오 |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | requests | 아니오 | 아니오 |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | associations | 아니오 | 아니오 |
> | resourceproviders | 예 | 예 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | 아니오 | 아니오 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availableskus | 아니오 | 아니오 |
> | databoxedgedevices | 예 | 예 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | 아니오 | 아니오 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | catalogs | 예 | 예 |
> | datacatalogs | 아니오 | 아니오 |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | 아니오 | 아니오 |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 패키지 | 아니오 | 아니오 |
> | 플랜 | 아니오 | 아니오 |

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
> | datalakeaccounts | 아니오 | 아니오 |

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
> | services | 아니오 | 아니오 |
> | services / projects | 아니오 | 아니오 |
> | slots | 아니오 | 아니오 |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | backupvaults | 아니오 | 아니오 |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

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
> | flexibleServers | 예 | 예 |
> | servers | 예 | 예 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | flexibleServers | 예 | 예 |
> | servergroups | 아니오 | 아니오 |
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
> | servicetopologies / services | 예 | 예 |
> | servicetopologies / services / serviceunits | 예 | 예 |
> | 단계 | 예 | 예 |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgroups | 예 | 예 |
> | hostpools | 예 | 예 |
> | workspaces | 예 | 예 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | elasticpools | 아니오 | 아니오 |
> | elasticpools / iothubtenants | 아니오 | 아니오 |
> | iothubs | 예 | 예 |
> | provisioningservices | 예 | 예 |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | pipelines | 예 | 예 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | controllers | 예 | 예 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | labcenters | 아니오 | 아니오 |
> | labs | 예 | 아니오 |
> | labs / environments | 예 | 예 |
> | labs / servicerunners | 예 | 예 |
> | labs / virtualmachines | 예 | 아니오 |
> | schedules | 예 | 예 |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | 아니오 | 아니오 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | databaseaccountnames | 아니오 | 아니오 |
> | databaseaccounts | 예 | 예 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domains | 예 | 예 |
> | generatessorequest | 아니오 | 아니오 |
> | topleveldomains | 아니오 | 아니오 |
> | validatedomainregistrationinformation | 아니오 | 아니오 |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | 예 | 예 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domains | 예 | 예 |
> | eventsubscriptions | 아니요 - 독립적으로 이동할 수는 없지만, 구독된 리소스와 함께 자동으로 이동할 수 있습니다. | 아니요 - 독립적으로 이동할 수는 없지만, 구독된 리소스와 함께 자동으로 이동할 수 있습니다. |
> | extensiontopics | 아니오 | 아니오 |
> | partnernamespaces | 예 | 예 |
> | partnerregistrations | 아니오 | 아니오 |
> | partnertopics | 예 | 예 |
> | systemtopics | 예 | 예 |
> | topics | 예 | 예 |
> | topictypes | 아니오 | 아니오 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | 예 | 예 |
> | 네임스페이스 | 예 | 예 |
> | sku | 아니오 | 아니오 |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | experimentworkspaces | 아니오 | 아니오 |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 네임스페이스 | 예 | 예 |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | featureproviders | 아니오 | 아니오 |
> | 기능 | 아니오 | 아니오 |
> | providers | 아니오 | 아니오 |
> | subscriptionfeatureregistrations | 아니오 | 아니오 |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아니오 | 아니오 |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | automanagedaccounts | 아니오 | 아니오 |
> | automanagedvmconfigurationprofiles | 아니오 | 아니오 |
> | guestconfigurationassignments | 아니오 | 아니오 |
> | software | 아니오 | 아니오 |
> | softwareupdateprofile | 아니오 | 아니오 |
> | softwareupdates | 아니오 | 아니오 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hanainstances | 아니오 | 아니오 |
> | sapmonitors | 아니요 | 아니오 |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dedicatedhsms | 아니오 | 아니오 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!IMPORTANT]
> 새 구독 또는 리소스 그룹에 HDInsight 클러스터를 이동할 수 있습니다. 그러나 HDInsight 클러스터에 연결된 네트워킹 리소스(예: Virtual Network, NIC 또는 부하 분산 장치)는 구독 간에 이동할 수 없습니다. 또한 클러스터에 대한 가상 머신에 연결된 NIC를 새 리소스 그룹으로 이동할 수 없습니다.
>
> HDInsight 클러스터를 새 구독으로 이동할 때 먼저 다른 리소스(예: 스토리지 계정)를 이동합니다. 그런 다음 자체적으로 HDInsight 클러스터를 이동합니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | 예 | 예 |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | 예 | 예 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | machines | 예 | 예 |
> | machines / extensions | 예 | 예 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datamanagers | 예 | 예 |

## <a name="microsofthybridnetwork"></a>HybridNetwork

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | devices | 아니오 | 아니오 |
> | vnfs | 아니오 | 아니오 |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | components | 아니오 | 아니오 |
> | networkscopes | 아니오 | 아니오 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | 예 | 예 |

## <a name="microsoftinsights"></a>microsoft.insights

> [!IMPORTANT]
> 새 구독으로 이동하는 경우 [구독 할당량](azure-subscription-service-limits.md#azure-monitor-limits)을 초과하지 않아야 합니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | actiongroups | 예 | 예 |
> | activitylogalerts | 아니오 | 아니오 |
> | alertrules | 예 | 예 |
> | autoscalesettings | 예 | 예 |
> | baseline | 아니오 | 아니오 |
> | components | 예 | 예 |
> | datacollectionrules | 아니오 | 아니오 |
> | diagnosticsettings | 아니오 | 아니오 |
> | diagnosticsettingscategories | 아니오 | 아니오 |
> | eventcategories | 아니오 | 아니오 |
> | eventtypes | 아니오 | 아니오 |
> | extendeddiagnosticsettings | 아니오 | 아니오 |
> | guestdiagnosticsettings | 아니오 | 아니오 |
> | listmigrationdate | 아니오 | 아니오 |
> | logdefinitions | 아니오 | 아니오 |
> | logprofiles | 아니오 | 아니오 |
> | 로그 | 아니오 | 아니오 |
> | metricalerts | 아니오 | 아니오 |
> | metricbaselines | 아니오 | 아니오 |
> | metricbatch | 아니오 | 아니오 |
> | metricdefinitions | 아니오 | 아니오 |
> | metricnamespaces | 아니오 | 아니오 |
> | 메트릭 | 아니오 | 아니오 |
> | migratealertrules | 아니오 | 아니오 |
> | migratetonewpricingmodel | 아니오 | 아니오 |
> | myworkbooks | 아니오 | 아니오 |
> | notificationgroups | 아니오 | 아니오 |
> | privatelinkscopes | 아니오 | 아니오 |
> | rollbacktolegacypricingmodel | 아니오 | 아니오 |
> | scheduledqueryrules | 예 | 예 |
> | 토폴로지 | 아니오 | 아니오 |
> | 트랜잭션 | 아니오 | 아니오 |
> | vminsightsonboardingstatuses | 아니오 | 아니오 |
> | webtests | 예 | 예 |
> | 웹 테스트/gettestresultfile | 아니요 | 아니요 |
> | workbooks | 예 | 예 |
> | workbooktemplates | 예 | 예 |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | apptemplates | 아니오 | 아니오 |
> | iotapps | 예 | 예 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | graph | 예 | 예 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!IMPORTANT]
> 디스크 암호화에 사용되는 Key Vault는 동일한 구독의 리소스 그룹으로 또는 구독 간에 이동할 수 없습니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | deletedvaults | 아니오 | 아니오 |
> | hsmpools | 아니오 | 아니요 |
> | managedhsms | 아니오 | 아니오 |
> | vaults | 예 | 예 |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | connectedclusters | 예 | 예 |
> | registeredsubscriptions | 아니오 | 아니오 |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | sourcecontrolconfigurations | 아니오 | 아니오 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | 예 | 예 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | labaccounts | 아니오 | 아니오 |
> | users | 아니오 | 아니오 |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아니오 | 아니오 |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아니오 | 아니오 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | 아니오 | 아니오 |
> | integrationaccounts | 예 | 예 |
> | integrationserviceenvironments | 예 | 아니오 |
> | integrationserviceenvironments / managedapis | 예 | 아니오 |
> | isolatedenvironments | 아니오 | 아니오 |
> | workflows | 예 | 예 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | commitmentplans | 아니오 | 아니오 |
> | webservices | 예 | 아니오 |
> | workspaces | 예 | 예 |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | 아니오 | 아니오 |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아니오 | 아니오 |
> | teamaccounts | 아니오 | 아니오 |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아니오 | 아니오 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | 아니오 | 아니오 |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | configurationassignments | 아니오 | 아니오 |
> | maintenanceconfigurations | 예 | 예 |
> | updates | 아니오 | 아니오 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | ID | 아니오 | 아니오 |
> | userassignedidentities | 아니오 | 아니오 |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managednetworks | 아니오 | 아니오 |
> | managednetworks / managednetworkgroups | 아니오 | 아니오 |
> | managednetworks / managednetworkpeeringpolicies | 아니오 | 아니오 |
> | 알림 | 아니오 | 아니오 |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | marketplaceregistrationdefinitions | 아니오 | 아니오 |
> | registrationassignments | 아니오 | 아니오 |
> | registrationdefinitions | 아니오 | 아니오 |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | getentities | 아니오 | 아니오 |
> | managementgroups | 아니오 | 아니오 |
> | managementgroups / settings | 아니오 | 아니오 |
> | 리소스 | 아니오 | 아니오 |
> | starttenantbackfill | 아니오 | 아니오 |
> | tenantbackfillstatus | 아니오 | 아니오 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |
> | accounts / privateatlases | 예 | 예 |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | offers | 아니오 | 아니오 |
> | offertypes | 아니오 | 아니오 |
> | privategalleryitems | 아니오 | 아니오 |
> | privatestoreclient | 아니오 | 아니오 |
> | privatestores | 아니오 | 아니오 |
> | products | 아니오 | 아니오 |
> | publishers | 아니오 | 아니오 |
> | register | 아니오 | 아니오 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | classicdevservices | 아니오 | 아니오 |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | agreements | 아니오 | 아니오 |
> | offertypes | 아니오 | 아니오 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | mediaservices | 예 | 예 |
> | mediaservices / liveevents | 예 | 예 |
> | mediaservices / streamingendpoints | 예 | 예 |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | appclusters | 아니오 | 아니오 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | 아니오 | 아니오 |
> | migrateprojects | 아니오 | 아니오 |
> | movecollections | 아니오 | 아니오 |
> | projects | 아니오 | 아니오 |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | 아니오 | 아니오 |
> | objectunderstandingaccounts | 아니오 | 아니오 |
> | remoterenderingaccounts | 예 | 예 |
> | spatialanchorsaccounts | 예 | 예 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | netappaccounts | 아니오 | 아니오 |
> | netappaccounts / capacitypools | 아니오 | 아니오 |
> | netappaccounts / capacitypools / volumes | 아니오 | 아니오 |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!IMPORTANT]
> [네트워킹 이동 지침](./move-limitations/networking-move-limitations.md)을 참조하세요.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgateways | 아니오 | 아니오 |
> | applicationgatewaywebapplicationfirewallpolicies | 아니오 | 아니오 |
> | applicationsecuritygroups | 예 | 예 |
> | azurefirewalls | 아니오 | 아니오 |
> | bastionhosts | 아니오 | 아니오 |
> | bgpservicecommunities | 아니오 | 아니오 |
> | connections | 예 | 예 |
> | ddoscustompolicies | 예 | 예 |
> | ddosprotectionplans | 아니오 | 아니오 |
> | dnszones | 예 | 예 |
> | expressroutecircuits | 아니오 | 아니오 |
> | expressroutegateways | 아니오 | 아니오 |
> | expressrouteserviceproviders | 아니오 | 아니오 |
> | firewallpolicies | 예 | 예 |
> | frontdoors | 아니오 | 아니요 |
> | ipallocations | 예 | 예 |
> | ipgroups | 예 | 예 |
> | loadbalancers | 예 - 기본 SKU<br> 아니오-표준 SKU | 예 - 기본 SKU<br>아니요 - 표준 SKU |
> | localnetworkgateways | 예 | 예 |
> | natgateways | 아니오 | 아니오 |
> | networkexperimentprofiles | 아니오 | 아니오 |
> | networkintentpolicies | 예 | 예 |
> | networkinterfaces | 예 | 예 |
> | networkprofiles | 아니오 | 아니오 |
> | networksecuritygroups | 예 | 예 |
> | networkwatchers | 아니오 | 아니오 |
> | networkwatchers / connectionmonitors | 예 | 아니오 |
> | networkwatchers / flowlogs | 예 | 아니오 |
> | networkwatchers / pingmeshes | 예 | 아니오 |
> | p2svpngateways | 아니오 | 아니오 |
> | privatednszones | 예 | 예 |
> | privatednszones / virtualnetworklinks | 예 | 예 |
> | privatednszonesinternal | 아니오 | 아니오 |
> | privateendpointredirectmaps | 아니오 | 아니오 |
> | privateendpoints | 아니요 | 아니요 |
> | privatelinkservices | 아니오 | 아니오 |
> | publicipaddresses | 예 - 기본 SKU<br>예-표준 SKU | 예 - 기본 SKU<br>아니요 - 표준 SKU |
> | publicipprefixes | 예 | 예 |
> | routefilters | 아니오 | 아니오 |
> | routetables | 예 | 예 |
> | securitypartnerproviders | 예 | 예 |
> | serviceendpointpolicies | 예 | 예 |
> | trafficmanagergeographichierarchies | 아니오 | 아니오 |
> | trafficmanagerprofiles | 예 | 예 |
> | trafficmanagerprofiles / heatmaps | 아니오 | 아니오 |
> | trafficmanagerusermetricskeys | 아니오 | 아니오 |
> | virtualhubs | 아니오 | 아니오 |
> | virtualnetworkgateways | 예 | 예 |
> | virtualnetworks | 예 | 예 |
> | virtualnetworktaps | 아니오 | 아니오 |
> | virtualrouters | 예 | 예 |
> | virtualwans | 아니오 | 아니오 |
> | vpngateways(Virtual WAN) | 아니오 | 아니오 |
> | vpnserverconfigurations | 아니오 | 아니오 |
> | vpnsites(Virtual WAN) | 아니오 | 아니요 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 네임스페이스 | 예 | 예 |
> | namespaces / notificationhubs | 예 | 예 |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | osnamespaces | 예 | 예 |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hypervsites | 아니오 | 아니요 |
> | importsites | 아니요 | 아니오 |
> | serversites | 아니요 | 아니요 |
> | vmwaresites | 아니오 | 아니오 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!IMPORTANT]
> 새 구독으로 이동하는 경우 [구독 할당량](azure-subscription-service-limits.md#azure-monitor-limits)을 초과하지 않아야 합니다.
>
> 연결된 자동화 계정이 있는 작업 영역은 이동할 수 없습니다. 이동 작업을 시작하기 전에 자동화 계정의 연결을 해제해야 합니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | 아니오 | 아니오 |
> | deletedworkspaces | 아니오 | 아니오 |
> | linktargets | 아니오 | 아니오 |
> | storageinsightconfigs | 아니오 | 아니오 |
> | workspaces | 예 | 예 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managementassociations | 아니오 | 아니오 |
> | managementconfigurations | 예 | 예 |
> | solutions | 예 | 예 |
> | 뷰 | 예 | 예 |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | legacypeerings | 아니오 | 아니오 |
> | peerasns | 아니오 | 아니오 |
> | peeringlocations | 아니오 | 아니오 |
> | peerings | 아니오 | 아니오 |
> | peeringservicecountries | 아니오 | 아니오 |
> | peeringservicelocations | 아니오 | 아니오 |
> | peeringserviceproviders | 아니오 | 아니오 |
> | peeringservices | 아니오 | 아니오 |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | policyevents | 아니오 | 아니오 |
> | policystates | 아니오 | 아니오 |
> | policytrackedresources | 아니오 | 아니오 |
> | remediations | 아니오 | 아니오 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | consoles | 아니오 | 아니오 |
> | dashboards | 예 | 예 |
> | usersettings | 아니오 | 아니오 |

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

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아니오 | 아니오 |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availableaccounts | 아니오 | 아니오 |
> | providerregistrations | 아니오 | 아니오 |
> | rollouts | 아니오 | 아니오 |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | 아니오 | 아니오 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!IMPORTANT]
> [Recovery Services 이동 지침](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)을 참조하세요.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | replicationeligibilityresults | 아니오 | 아니오 |
> | vaults | 예 | 예 |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | openshiftclusters | 아니오 | 아니오 |

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
> | resourcechangedetails | 아니오 | 아니오 |
> | resourcechanges | 아니오 | 아니오 |
> | 리소스 | 아니오 | 아니오 |
> | resourceshistory | 아니오 | 아니오 |
> | subscriptionsstatus | 아니오 | 아니오 |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | childresources | 아니오 | 아니오 |
> | emergingissues | 아니오 | 아니오 |
> | events | 아니오 | 아니오 |
> | metadata | 아니오 | 아니오 |
> | 알림 | 아니오 | 아니오 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 배포 | 아니오 | 아니오 |
> | deploymentscripts | 아니오 | 아니오 |
> | deploymentscripts / logs | 아니오 | 아니오 |
> | 링크 | 아니오 | 아니오 |
> | providers | 아니오 | 아니오 |
> | resourcegroups | 아니오 | 아니오 |
> | 리소스 | 아니오 | 아니오 |
> | 구독 | 아니오 | 아니오 |
> | tags | 아니오 | 아니오 |
> | templatespecs | 아니오 | 아니오 |
> | templatespecs / versions | 아니오 | 아니오 |
> | tenants | 아니오 | 아니오 |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 애플리케이션 | 예 | 아니오 |
> | saasresources | 아니오 | 아니오 |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!IMPORTANT]
> 다른 지역에 있는 여러 Search 리소스를 한 번의 작업으로 이동할 수 없습니다. 대신 별도 작업으로 이동합니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | resourcehealthmetadata | 아니오 | 아니오 |
> | searchservices | 예 | 예 |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | 아니오 | 아니오 |
> | advancedthreatprotectionsettings | 아니오 | 아니오 |
> | 경고 | 아니오 | 아니오 |
> | allowedconnections | 아니오 | 아니오 |
> | applicationwhitelistings | 아니오 | 아니오 |
> | assessmentmetadata | 아니오 | 아니오 |
> | assessments | 아니오 | 아니오 |
> | autodismissalertsrules | 아니오 | 아니오 |
> | automations | 예 | 예 |
> | autoprovisioningsettings | 아니오 | 아니오 |
> | complianceresults | 아니오 | 아니오 |
> | compliances | 아니오 | 아니오 |
> | datacollectionagents | 아니오 | 아니오 |
> | devicesecuritygroups | 아니오 | 아니오 |
> | discoveredsecuritysolutions | 아니오 | 아니오 |
> | externalsecuritysolutions | 아니오 | 아니오 |
> | informationprotectionpolicies | 아니오 | 아니오 |
> | iotsecuritysolutions | 예 | 예 |
> | iotsecuritysolutions / analyticsmodels | 아니오 | 아니오 |
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | 아니오 | 아니오 |
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | 아니오 | 아니오 |
> | jitnetworkaccesspolicies | 아니오 | 아니오 |
> | 정책 | 아니오 | 아니오 |
> | pricings | 아니오 | 아니오 |
> | regulatorycompliancestandards | 아니오 | 아니오 |
> | regulatorycompliancestandards / regulatorycompliancecontrols | 아니오 | 아니오 |
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | 아니오 | 아니오 |
> | securitycontacts | 아니오 | 아니오 |
> | securitysolutions | 아니오 | 아니오 |
> | securitysolutionsreferencedata | 아니오 | 아니오 |
> | securitystatuses | 아니오 | 아니오 |
> | securitystatusessummaries | 아니오 | 아니오 |
> | servervulnerabilityassessments | 아니오 | 아니오 |
> | 설정 | 아니오 | 아니오 |
> | subassessments | 아니오 | 아니오 |
> | tasks | 아니오 | 아니오 |
> | topologies | 아니오 | 아니오 |
> | workspacesettings | 아니오 | 아니오 |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | aggregations | 아니오 | 아니오 |
> | alertrules | 아니오 | 아니오 |
> | alertruletemplates | 아니오 | 아니오 |
> | automationrules | 아니오 | 아니오 |
> | bookmarks | 아니오 | 아니오 |
> | cases | 아니오 | 아니오 |
> | dataconnectors | 아니오 | 아니오 |
> | 엔터티 | 아니오 | 아니오 |
> | entityqueries | 아니오 | 아니오 |
> | incidents | 아니오 | 아니오 |
> | officeconsents | 아니오 | 아니오 |
> | 설정 | 아니오 | 아니오 |
> | threatintelligence | 아니오 | 아니오 |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | consoleservices | 아니오 | 아니오 |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | gateways | 아니오 | 아니오 |
> | nodes | 아니오 | 아니오 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 네임스페이스 | 예 | 예 |
> | premiummessagingregions | 아니오 | 아니오 |
> | sku | 아니오 | 아니오 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 애플리케이션 | 아니오 | 아니오 |
> | clusters | 예 | 예 |
> | containergroups | 아니오 | 아니오 |
> | containergroupsets | 아니오 | 아니오 |
> | edgeclusters | 아니오 | 아니오 |
> | managedclusters | 아니오 | 아니오 |
> | networks | 아니오 | 아니오 |
> | secretstores | 아니오 | 아니오 |
> | volumes | 아니오 | 아니오 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 애플리케이션 | 예 | 예 |
> | containergroups | 아니오 | 아니오 |
> | gateways | 예 | 예 |
> | networks | 예 | 예 |
> | secrets | 예 | 예 |
> | volumes | 예 | 예 |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | rollouts | 아니오 | 아니오 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | signalr | 예 | 예 |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hybridusebenefits | 아니오 | 아니오 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | 아니오 | 아니오 |
> | 애플리케이션 | 아니오 | 아니오 |
> | jitrequests | 아니오 | 아니오 |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!IMPORTANT]
> 데이터베이스와 서버는 동일한 리소스 그룹에 있어야 합니다. SQL Server를 이동하면 모든 해당 데이터베이스도 함께 이동합니다. 이 동작은 Azure SQL Database 및 Azure Synapse Analytics 데이터베이스에 적용 됩니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | instancepools | 아니오 | 아니오 |
> | 위치 | 예 | 예 |
> | managedinstances | 아니오 | 아니오 |
> | servers | 예 | 예 |
> | servers / databases | 예 | 예 |
> | servers / databases / backuplongtermretentionpolicies | 예 | 예 |
> | servers / elasticpools | 예 | 예 |
> | servers / jobaccounts | 예 | 예 |
> | servers / jobagents | 예 | 예 |
> | virtualclusters | 예 | 예 |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | 예 | 예 |
> | sqlvirtualmachines | 예 | 예 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storageaccounts | 예 | 예 |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | caches | 아니오 | 아니오 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | 예 | 예 |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | 아니오 | 아니오 |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | 아니오 | 아니오 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managers | 아니오 | 아니오 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!IMPORTANT]
> 실행 중 상태인 Stream Analytics 작업은 이동할 수 없습니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | 아니오 | 아니오 |
> | streamingjobs | 예 | 예 |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | 아니오 | 아니오 |
> | 인스턴스 | 아니오 | 아니오 |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 구독 | 아니오 | 아니오 |

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | 아니오 | 아니오 |
> | supporttickets | 아니오 | 아니오 |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | 예 | 아니요 |
> | workspaces / bigdatapools | 예 | 예 |
> | workspaces / sqlpools | 예 | 예 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | 예 | 예 |
> | environments / eventsources | 예 | 예 |
> | environments / referencedatasets | 예 | 예 |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | stores | 예 | 예 |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | imagetemplates | 아니오 | 아니오 |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!IMPORTANT]
> Azure DevOps 구독을 변경하려면 [청구에 사용되는 Azure 구독 변경](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)을 참조하세요.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | 아니오 | 아니오 |
> | account / extension | 아니오 | 아니오 |
> | account / project | 아니오 | 아니요 |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | arczones | 아니오 | 아니오 |
> | resourcepools | 아니오 | 아니오 |
> | vcenters | 아니오 | 아니오 |
> | virtualmachines | 아니오 | 아니오 |
> | virtualmachinetemplates | 아니오 | 아니오 |
> | virtualnetworks | 아니오 | 아니오 |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | 아니오 | 아니오 |
> | dedicatedcloudservices | 아니오 | 아니오 |
> | virtualmachines | 아니오 | 아니오 |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | devices | 아니오 | 아니오 |
> | vnfs | 아니오 | 아니오 |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아니오 | 아니오 |
> | 플랜 | 아니오 | 아니오 |
> | registeredsubscriptions | 아니오 | 아니오 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!IMPORTANT]
> [App Service 이동 지침](./move-limitations/app-service-move-limitations.md)을 참조하세요.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availablestacks | 아니오 | 아니오 |
> | billingmeters | 아니오 | 아니오 |
> | certificates | 아니오 | 예 |
> | connectiongateways | 예 | 예 |
> | connections | 예 | 예 |
> | customapis | 예 | 예 |
> | deletedsites | 아니오 | 아니오 |
> | deploymentlocations | 아니오 | 아니오 |
> | georegions | 아니오 | 아니오 |
> | hostingenvironments | 아니오 | 아니오 |
> | kubeenvironments | 예 | 예 |
> | publishingusers | 아니오 | 아니오 |
> | 동영상 추천 기능 | 아니오 | 아니오 |
> | resourcehealthmetadata | 아니오 | 아니오 |
> | runtimes | 아니오 | 아니오 |
> | serverfarms | 예 | 예 |
> | serverfarms / eventgridfilters | 아니오 | 아니오 |
> | sites | 예 | 예 |
> | sites / premieraddons | 예 | 예 |
> | sites / slots | 예 | 예 |
> | sourcecontrols | 아니오 | 아니오 |
> | staticsites | 아니오 | 아니요 |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | multipleactivationkeys | 아니오 | 아니오 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | deviceservices | 아니오 | 아니오 |

## <a name="microsoftworkloadbuilder"></a>WorkloadBuilder

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 작업 | 아니오 | 아니오 |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | components | 아니오 | 아니오 |
> | componentssummary | 아니오 | 아니오 |
> | monitorinstances | 아니오 | 아니오 |
> | monitorinstancessummary | 아니오 | 아니오 |
> | monitors | 아니오 | 아니오 |

## <a name="third-party-services"></a>타사 서비스

타사 서비스는 현재 이동 작업을 지원하지 않습니다.

## <a name="next-steps"></a>다음 단계

리소스를 이동하는 명령은 [새 리소스 그룹 또는 구독으로 리소스 이동](move-resource-group-and-subscription.md)을 참조하세요.

쉼표로 구분된 값 파일과 동일한 데이터를 가져오려면 [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv) 파일을 다운로드합니다.
