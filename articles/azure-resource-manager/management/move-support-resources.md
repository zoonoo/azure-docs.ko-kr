---
title: 리소스 종류별 이동 작업 지원
description: 새 리소스 그룹이나 구독으로 이동할 수 있는 Azure 리소스 종류를 소개합니다.
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 16197210326d73284a4a83edc7876e4faddded86
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87079509"
---
# <a name="move-operation-support-for-resources"></a>리소스에 대한 이동 작업 지원

이 문서에서는 Azure 리소스 종류가 이동 작업을 지원하는지 여부에 대한 정보를 제공합니다. 또한 리소스를 이동할 때 고려해야 할 특수 조건에 대한 정보도 제공합니다.

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
> - [Microsoft.ChangeAnalysis](#microsoftchangeanalysis)
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
> - [Microsoft.CostManagementExports](#microsoftcostmanagementexports)
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
> - [Microsoft PowerPlatform](#microsoftpowerplatform)
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
> | domainservices | 아니요 | 아니요 |
> | domainservices / oucontainer | 아니요 | 아니요 |
> | 위치 | 예 | 아니요 |
> | locations / operationresults | 예 | 아니요 |
> | 작업 | 예 | 아니요 |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | diagnosticsettings | 아니요 | 아니요 |
> | diagnosticsettingscategories | 아니요 | 아니요 |
> | 작업 | 예 | 아니요 |
> | privatelinkforazuread | 예 | 예 |
> | tenants | 예 | 예 |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | operationresults | 예 | 아니요 |
> | 작업 | 예 | 아니요 |
> | supportproviders | 아니요 | 아니요 |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | aadsupportcases | 예 | 아니요 |
> | addsservices | 예 | 아니요 |
> | agents | 예 | 아니요 |
> | anonymousapiusers | 예 | 아니요 |
> | 구성 | 예 | 아니요 |
> | 로그 | 예 | 아니요 |
> | 작업 | 예 | 아니요 |
> | reports | 예 | 아니요 |
> | servicehealthmetrics | 예 | 아니요 |
> | services | 아니요 | 아니요 |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 구성 | 예 | 아니요 |
> | generaterecommendations | 예 | 아니요 |
> | metadata | 예 | 아니요 |
> | 작업 | 예 | 아니요 |
> | 동영상 추천 기능 | 예 | 아니요 |
> | suppressions | 아니요 | 아니요 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | actionrules | 예 | 예 |
> | 경고 | 예 | 아니요 |
> | alertslist | 예 | 아니요 |
> | alertsmetadata | 예 | 아니요 |
> | alertssummary | 예 | 아니요 |
> | alertssummarylist | 예 | 아니요 |
> | 작업 | 아니요 | 아니요 |
> | smartdetectoralertrules | 예 | 예 |
> | smartgroups | 아니요 | 아니요 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 위치 | 예 | 아니요 |
> | locations / checknameavailability | 예 | 아니요 |
> | locations / operationresults | 예 | 아니요 |
> | locations / operationstatuses | 예 | 아니요 |
> | 작업 | 아니요 | 아니요 |
> | servers | 예 | 예 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!IMPORTANT]
> 소비 SKU로 설정된 API Management 서비스는 이동할 수 없습니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checkfeedbackrequired | 아니요 | 예 |
> | checknameavailability | 아니요 | 예 |
> | checkservicenameavailability | 아니요 | 예 |
> | 작업 | 아니요 | 아니요 |
> | reportfeedback | 아니요 | 예 |
> | 서비스 | 예 | 예 |
> | validateservicename | 아니요 | 아니요 |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 아니요 | 아니요 |
> | configurationstores | 예 | 예 |
> | configurationstores / eventgridfilters | 예 | 아니요 |
> | 위치 | 예 | 아니요 |
> | locations / operationsstatus | 예 | 아니요 |
> | 작업 | 아니요 | 아니요 |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 위치 | 예 | 아니요 |
> | locations / checknameavailability | 예 | 아니요 |
> | locations / operationresults | 예 | 아니요 |
> | locations / operationstatus | 예 | 아니요 |
> | 작업 | 아니요 | 아니요 |
> | spring | 예 | 예 |
> | 스프링/앱 | 예 | 예 |
> | 스프링/앱/배포 | 예 | 예 |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!IMPORTANT]
> [App Service 이동 지침](./move-limitations/app-service-move-limitations.md)을 참조하세요.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | apiapps | 아니요 | 아니요 |
> | appidentities | 아니요 | 아니요 |
> | gateways | 예 | 예 |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | attestationproviders | 예 | 예 |
> | 작업 | 예 | 예 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checkaccess | 예 | 아니요 |
> | classicadministrators | 예 | 아니요 |
> | dataaliases | 예 | 아니요 |
> | denyassignments | 예 | 아니요 |
> | elevateaccess | 예 | 아니요 |
> | findorphanroleassignments | 예 | 아니요 |
> | locks | 예 | 아니요 |
> | 작업 | 예 | 아니요 |
> | operationstatus | 예 | 아니요 |
> | 권한 | 예 | 아니요 |
> | policyassignments | 예 | 아니요 |
> | policydefinitions | 예 | 아니요 |
> | policysetdefinitions | 예 | 아니요 |
> | privatelinkassociations | 아니요 | 아니요 |
> | provideroperations | 아니요 | 아니요 |
> | resourcemanagementprivatelinks | 아니요 | 아니요 |
> | roleassignments | 예 | 예 |
> | roleassignmentsusagemetrics | 예 | 예 |
> | roledefinitions | 예 | 예 |

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
> | automationaccounts / jobs | 예 | 예 |
> | automationaccounts / privateendpointconnectionproxies | 아니요 | 아니요 |
> | automationaccounts / privateendpointconnections | 아니요 | 아니요 |
> | automationaccounts / privatelinkresources | 예 | 아니요 |
> | automationaccounts / runbooks | 예 | 예 |
> | automationaccounts / softwareupdateconfigurations | 예 | 아니요 |
> | automationaccounts / webhooks | 아니요 | 아니요 |
> | 작업 | 예 | 아니요 |

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 위치 | 아니요 | 아니요 |
> | locations / checkquotaavailability | 예 | 아니요 |
> | 위치/checktrialavailability | 예 | 아니요 |
> | 작업 | 예 | 아니요 |
> | privateclouds | 예 | 예 |
> | privateclouds / clusters | 예 | 예 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | 예 | 예 |
> | b2ctenants | 예 | 아니요 |
> | checknameavailability | 예 | 아니요 |
> | 작업 | 예 | 아니요 |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datacontrollers | 아니요 | 예 |
> | hybriddatamanagers | 아니요 | 아니요 |
> | 작업 | 아니요 | 아니요 |
> | postgresinstances | 예 | 아니요 |
> | sqlinstances | 예 | 아니요 |
> | sqlmanagedinstances | 아니요 | 아니요 |
> | sqlserverinstances | 예 | 아니요 |
> | sqlserverregistrations | 예 | 예 |
> | sqlserverregistrations / sqlservers | 아니요 | 아니요 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | cloudmanifestfiles | 예 | 아니요 |
> | 작업 | 아니요 | 아니요 |
> | registrations | 예 | 예 |
> | registrations / customersubscriptions | 예 | 예 |
> | registrations / products | 예 | 예 |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | 예 | 아니요 |
> | 작업 | 예 | 아니요 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | batchaccounts | 예 | 예 |
> | 위치 | 예 | 아니요 |
> | locations / accountoperationresults | 예 | 아니요 |
> | locations / checknameavailability | 예 | 아니요 |
> | locations / quotas | 예 | 아니요 |
> | 작업 | 예 | 아니요 |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | billingaccounts | 아니요 | 예 |
> | billingaccounts / agreements | 아니요 | 아니요 |
> | billingaccounts / billingpermissions | 아니요 | 아니요 |
> | billingaccounts / billingprofiles | 예 | 아니요 |
> | billingaccounts / billingprofiles / availablebalance | 예 | 아니요 |
> | billingaccounts / billingprofiles / billingpermissions | 예 | 아니요 |
> | billingaccounts / billingprofiles / billingroleassignments | 예 | 아니요 |
> | billingaccounts / billingprofiles / billingroledefinitions | 예 | 아니요 |
> | billingaccounts / billingprofiles / billingsubscriptions | 예 | 아니요 |
> | billingaccounts / billingprofiles / createbillingroleassignment | 예 | 아니요 |
> | billingaccounts / billingprofiles / customers | 예 | 아니요 |
> | billingaccounts / billingprofiles / instructions | 예 | 아니요 |
> | billingaccounts / billingprofiles / invoices | 예 | 아니요 |
> | billingaccounts / billingprofiles / invoices / pricesheet | 예 | 아니요 |
> | billingaccounts / billingprofiles / invoices / transactions | 예 | 아니요 |
> | billingaccounts / billingprofiles / invoicesections | 예 | 아니요 |
> | billingaccounts / billingprofiles / invoicesections / billingpermissions | 예 | 아니요 |
> | billingaccounts / billingprofiles / invoicesections / billingroleassignments | 예 | 아니요 |
> | billingaccounts / billingprofiles / invoicesections / billingroledefinitions | 예 | 아니요 |
> | billingaccounts / billingprofiles / invoicesections / billingsubscriptions | 예 | 아니요 |
> | billingaccounts / billingprofiles / invoicesections / createbillingroleassignment | 예 | 아니요 |
> | billingaccounts / billingprofiles / invoicesections / initiatetransfer | 예 | 아니요 |
> | billingaccounts / billingprofiles / invoicesections / products | 예 | 아니요 |
> | billingaccounts / billingprofiles / invoicesections / products / transfer | 예 | 아니요 |
> | billingaccounts / billingprofiles / invoicesections / products / updateautorenew | 예 | 아니요 |
> | billingaccounts / billingprofiles / invoicesections / transactions | 예 | 아니요 |
> | billingaccounts / billingprofiles / invoicesections / transfers | 예 | 아니요 |
> | billingaccounts / billingprofiles / patchoperations | 예 | 아니요 |
> | billingaccounts / billingprofiles / paymentmethods | 예 | 아니요 |
> | billingaccounts / billingprofiles / policies | 예 | 아니요 |
> | billingaccounts / billingprofiles / pricesheet | 예 | 아니요 |
> | billingaccounts / billingprofiles / pricesheetdownloadoperations | 예 | 아니요 |
> | billingaccounts / billingprofiles / products | 예 | 아니요 |
> | billingaccounts / billingprofiles / transactions | 예 | 아니요 |
> | billingaccounts / billingroleassignments | 예 | 아니요 |
> | billingaccounts / billingroledefinitions | 예 | 아니요 |
> | billingaccounts / billingsubscriptions | 예 | 아니요 |
> | billingaccounts / billingsubscriptions / invoices | 예 | 아니요 |
> | billingaccounts / createbillingroleassignment | 예 | 아니요 |
> | billingaccounts / createinvoicesectionoperations | 예 | 아니요 |
> | billingaccounts / customers | 예 | 아니요 |
> | billingaccounts / customers / billingpermissions | 예 | 아니요 |
> | billingaccounts / customers / billingsubscriptions | 예 | 아니요 |
> | billingaccounts / customers / initiatetransfer | 예 | 아니요 |
> | billingaccounts / customers / policies | 예 | 아니요 |
> | billingaccounts / customers / products | 예 | 아니요 |
> | billingaccounts / customers / transactions | 예 | 아니요 |
> | billingaccounts / customers / transfers | 예 | 아니요 |
> | billingaccounts / departments | 예 | 아니요 |
> | billingaccounts / enrollmentaccounts | 예 | 아니요 |
> | billingaccounts / invoices | 예 | 아니요 |
> | billingaccounts / invoicesections | 예 | 아니요 |
> | billingaccounts / invoicesections / billingsubscriptionmoveoperations | 예 | 아니요 |
> | billingaccounts / invoicesections / billingsubscriptions | 예 | 아니요 |
> | billingaccounts / invoicesections / billingsubscriptions / transfer | 예 | 아니요 |
> | billingaccounts / invoicesections / elevate | 예 | 아니요 |
> | billingaccounts / invoicesections / initiatetransfer | 예 | 아니요 |
> | billingaccounts / invoicesections / patchoperations | 예 | 아니요 |
> | billingaccounts / invoicesections / productmoveoperations | 예 | 아니요 |
> | billingaccounts / invoicesections / products | 예 | 아니요 |
> | billingaccounts / invoicesections / products / transfer | 예 | 아니요 |
> | billingaccounts / invoicesections / products / updateautorenew | 예 | 아니요 |
> | billingaccounts / invoicesections / producttransfersresults | 예 | 아니요 |
> | billingaccounts / invoicesections / transactions | 예 | 아니요 |
> | billingaccounts / invoicesections / transfers | 예 | 아니요 |
> | billingaccounts / lineofcredit | 예 | 아니요 |
> | billingaccounts / listinvoicesectionswithcreatesubscriptionpermission | 예 | 아니요 |
> | billingaccounts / operationresults | 예 | 아니요 |
> | billingaccounts / patchoperations | 예 | 아니요 |
> | billingaccounts / paymentmethods | 예 | 아니요 |
> | billingaccounts / products | 예 | 아니요 |
> | billingaccounts / transactions | 예 | 아니요 |
> | billingperiods | 예 | 아니요 |
> | billingpermissions | 예 | 아니요 |
> | billingproperty | 예 | 아니요 |
> | billingroleassignments | 예 | 아니요 |
> | billingroledefinitions | 예 | 예 |
> | createbillingroleassignment | 예 | 아니요 |
> | departments | 예 | 아니요 |
> | enrollmentaccounts | 예 | 아니요 |
> | invoices | 예 | 아니요 |
> | operationresults | 예 | 아니요 |
> | 작업 | 예 | 아니요 |
> | operationstatus | 예 | 아니요 |
> | transfers | 예 | 아니요 |
> | transfers / accepttransfer | 예 | 아니요 |
> | transfers / declinetransfer | 예 | 아니요 |
> | transfers / operationstatus | 예 | 아니요 |
> | transfers / validatetransfer | 예 | 아니요 |
> | validateaddress | 예 | 아니요 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | listcommunicationpreference | 아니요 | 예 |
> | mapapis | 아니요 | 아니요 |
> | 작업 | 아니요 | 아니요 |
> | updatecommunicationpreference | 예 | 아니요 |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | biztalk | 아니요 | 예 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | 아니요 | 예 |
> | cordamembers | 아니요 | 아니요 |
> | 위치 | 아니요 | 아니요 |
> | locations / blockchainmemberoperationresults | 예 | 아니요 |
> | locations / checknameavailability | 예 | 아니요 |
> | locations / listconsortiums | 예 | 아니요 |
> | locations / watcheroperationresults | 예 | 아니요 |
> | 작업 | 예 | 아니요 |
> | watchers | 예 | 아니요 |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 작업 | 아니요 | 예 |
> | tokenservices | 아니요 | 아니요 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | 아니요 | 예 |
> | blueprintassignments / assignmentoperations | 아니요 | 아니요 |
> | blueprintassignments / operations | 아니요 | 아니요 |
> | blueprints | 예 | 아니요 |
> | blueprints / artifacts | 예 | 아니요 |
> | blueprints / versions | 예 | 아니요 |
> | blueprints / versions / artifacts | 예 | 아니요 |
> | 작업 | 예 | 아니요 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | botservices | 예 | 예 |
> | botservices / channels | 아니요 | 아니요 |
> | botservices / connections | 아니요 | 아니요 |
> | checknameavailability | 아니요 | 아니요 |
> | listauthserviceproviders | 예 | 아니요 |
> | 작업 | 예 | 아니요 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!IMPORTANT]
> Azure Cache for Redis 인스턴스가 가상 네트워크를 사용하여 구성되면 이 인스턴스를 다른 구독으로 이동할 수 없습니다. [네트워킹 이동 제한](./move-limitations/networking-move-limitations.md)을 참조하세요.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 예 | 아니요 |
> | 위치 | 아니요 | 아니요 |
> | locations / operationresults | 예 | 예 |
> | locations / operationsstatus | 예 | 예 |
> | 작업 | 예 | 예 |
> | redis | 예 | 예 |
> | redis/eventgridfilters | 예 | 예 |
> | redis / privatelinkresources | 아니요 | 아니요 |
> | redisenterprise | 예 | 예 |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | appliedreservations | 예 | 아니요 |
> | calculateexchange | 아니요 | 아니요 |
> | calculateprice | 아니요 | 아니요 |
> | calculatepurchaseprice | 예 | 아니요 |
> | catalogs | 예 | 아니요 |
> | checkoffers | 예 | 아니요 |
> | checkpurchasestatus | 예 | 아니요 |
> | checkscopes | 예 | 아니요 |
> | commercialreservationorders | 예 | 아니요 |
> | 교환 | 예 | 아니요 |
> | listbenefits | 예 | 아니요 |
> | 작업 | 예 | 아니요 |
> | placepurchaseorder | 예 | 아니요 |
> | reservationorders | 예 | 아니요 |
> | reservationorders / availablescopes | 예 | 아니요 |
> | reservationorders / calculaterefund | 예 | 아니요 |
> | reservationorders / merge | 예 | 아니요 |
> | reservationorders / reservations | 예 | 아니요 |
> | reservationorders / reservations / availablescopes | 예 | 아니요 |
> | reservationorders / reservations / revisions | 예 | 아니요 |
> | reservationorders / return | 예 | 아니요 |
> | reservationorders / split | 예 | 아니요 |
> | reservationorders / swap | 예 | 아니요 |
> | reservations | 예 | 아니요 |
> | 리소스 | 예 | 아니요 |
> | validatereservationorder | 예 | 아니요 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallmanagedrulesets | 아니요 | 예 |
> | cdnwebapplicationfirewallpolicies | 예 | 예 |
> | checknameavailability | 아니요 | 아니요 |
> | checkresourceusage | 예 | 아니요 |
> | edgenodes | 아니요 | 아니요 |
> | operationresults | 예 | 아니요 |
> | operationresults / profileresults | 예 | 아니요 |
> | operationresults / profileresults / endpointresults | 예 | 아니요 |
> | operationresults / profileresults / endpointresults / customdomainresults | 예 | 아니요 |
> | operationresults / profileresults / endpointresults / origingroupresults | 예 | 아니요 |
> | operationresults / profileresults / endpointresults / originresults | 예 | 아니요 |
> | 작업 | 예 | 아니요 |
> | 프로필 | 예 | 예 |
> | profiles / endpoints | 예 | 예 |
> | profiles / endpoints / customdomains | 예 | 아니요 |
> | profiles / endpoints / origingroups | 아니요 | 아니요 |
> | profiles / endpoints / origins | 아니요 | 아니요 |
> | validateprobe | 예 | 아니요 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!IMPORTANT]
> [App Service 이동 지침](./move-limitations/app-service-move-limitations.md)을 참조하세요.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | certificateorders | 예 | 예 |
> | certificateorders / certificates | 아니요 | 아니요 |
> | 작업 | 아니요 | 아니요 |
> | validatecertificateregistrationinformation | 아니요 | 예 |

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 작업 | 예 | 예 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!IMPORTANT]
> [클래식 배포 이동 지침](./move-limitations/classic-model-move-limitations.md)을 참조하세요. 클래식 배포 리소스는 해당 시나리오와 관련된 작업을 통해 구독 간에 이동할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | capabilities | 예 | 아니요 |
> | checkdomainnameavailability | 아니요 | 아니요 |
> | domainnames | 예 | 예 |
> | domainnames / capabilities | 예 | 예 |
> | domainnames / internalloadbalancers | 아니요 | 예 |
> | domainnames / servicecertificates | 예 | 예 |
> | domainnames / slots | 예 | 예 |
> | domainnames / slots / roles | 예 | 예 |
> | domainnames / slots / roles / metricdefinitions | 예 | 예 |
> | domainnames / slots / roles / metrics | 예 | 예 |
> | movesubscriptionresources | 예 | 예 |
> | operatingsystemfamilies | 예 | 예 |
> | operatingsystems | 예 | 예 |
> | 작업 | 예 | 예 |
> | operationstatuses | 예 | 예 |
> | quotas | 예 | 예 |
> | resourcetypes | 예 | 예 |
> | validatesubscriptionmoveavailability | 예 | 예 |
> | virtualmachines | 예 | 예 |
> | virtualmachines / diagnosticsettings | 예 | 예 |
> | virtualmachines / metricdefinitions | 아니요 | 예 |
> | virtualmachines / metrics | 예 | 예 |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!IMPORTANT]
> [클래식 배포 이동 지침](./move-limitations/classic-model-move-limitations.md)을 참조하세요. 클래식 배포 리소스는 해당 시나리오와 관련된 작업을 통해 구독 간에 이동할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | classicinfrastructureresources | 아니요 | 아니요 |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!IMPORTANT]
> [클래식 배포 이동 지침](./move-limitations/classic-model-move-limitations.md)을 참조하세요. 클래식 배포 리소스는 해당 시나리오와 관련된 작업을 통해 구독 간에 이동할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | capabilities | 아니요 | 아니요 |
> | expressroutecrossconnections | 아니요 | 아니요 |
> | expressroutecrossconnections / peerings | 예 | 예 |
> | gatewaysupporteddevices | 예 | 예 |
> | networksecuritygroups | 예 | 예 |
> | 작업 | 예 | 예 |
> | quotas | 예 | 예 |
> | reservedips | 예 | 예 |
> | virtualnetworks | 예 | 예 |
> | virtualnetworks / remotevirtualnetworkpeeringproxies | 예 | 예 |
> | virtualnetworks / virtualnetworkpeerings | 예 | 예 |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!IMPORTANT]
> [클래식 배포 이동 지침](./move-limitations/classic-model-move-limitations.md)을 참조하세요. 클래식 배포 리소스는 해당 시나리오와 관련된 작업을 통해 구독 간에 이동할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | capabilities | 아니요 | 아니요 |
> | checkstorageaccountavailability | 아니요 | 아니요 |
> | disks | 예 | 예 |
> | images | 예 | 예 |
> | 작업 | 예 | 예 |
> | osimages | 예 | 예 |
> | osplatformimages | 예 | 예 |
> | publicimages | 예 | 예 |
> | quotas | 예 | 예 |
> | storageaccounts | 예 | 예 |
> | storageaccounts / blobservices | 예 | 예 |
> | storageaccounts / fileservices | 아니요 | 예 |
> | storageaccounts / metricdefinitions | 예 | 예 |
> | storageaccounts / metrics | 예 | 예 |
> | storageaccounts / queueservices | 예 | 예 |
> | storageaccounts / services | 예 | 예 |
> | storageaccounts / services / diagnosticsettings | 예 | 예 |
> | storageaccounts / services / metricdefinitions | 예 | 예 |
> | storageaccounts / services / metrics | 예 | 예 |
> | storageaccounts / tableservices | 예 | 예 |
> | storageaccounts / vmimages | 예 | 예 |
> | vmimages | 예 | 예 |

## <a name="microsoftclassicsubscription"></a>Microsoft.ClassicSubscription

> [!IMPORTANT]
> [클래식 배포 이동 지침](./move-limitations/classic-model-move-limitations.md)을 참조하세요. 클래식 배포 리소스는 해당 시나리오와 관련된 작업을 통해 구독 간에 이동할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 작업 | 아니요 | 아니요 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |
> | checkdomainavailability | 아니요 | 아니요 |
> | 위치 | 아니요 | 아니요 |
> | locations / checkskuavailability | 예 | 아니요 |
> | locations / deletevirtualnetworkorsubnets | 아니요 | 아니요 |
> | locations / operationresults | 아니요 | 아니요 |
> | 작업 | 예 | 예 |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 작업 | 예 | 아니요 |
> | ratecard | 예 | 아니요 |
> | usageaggregates | 예 | 아니요 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!IMPORTANT]
> [Virtual Machines 이동 지침](./move-limitations/virtual-machines-move-limitations.md)을 참조하세요.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availabilitysets | 예 | 예 |
> | diskaccesses | 아니요 | 예 |
> | diskencryptionsets | 아니요 | 예 |
> | disks | 예 | 예 |
> | galleries | 아니요 | 아니요 |
> | galleries / images | 아니요 | 아니요 |
> | galleries / images / versions | 예 | 아니요 |
> | hostgroups | 예 | 아니요 |
> | hostgroups / hosts | 아니요 | 아니요 |
> | images | 예 | 예 |
> | 위치 | 예 | 아니요 |
> | locations / artifactpublishers | 예 | 아니요 |
> | locations / capsoperations | 예 | 아니요 |
> | locations / diskoperations | 예 | 아니요 |
> | locations / loganalytics | 아니요 | 아니요 |
> | locations / operations | 예 | 아니요 |
> | locations / publishers | 예 | 아니요 |
> | locations / runcommands | 예 | 아니요 |
> | locations / usages | 예 | 아니요 |
> | locations / virtualmachines | 예 | 아니요 |
> | locations / vmsizes | 예 | 아니요 |
> | locations / vsmoperations | 예 | 아니요 |
> | 작업 | 예 | 아니요 |
> | proximityplacementgroups | 예 | 예 |
> | restorepointcollections | 예 | 아니요 |
> | restorepointcollections / restorepoints | 예 | 아니요 |
> | sharedvmextensions | 예 | 아니요 |
> | sharedvmimages | 예 | 아니요 |
> | sharedvmimages / versions | 아니요 | 아니요 |
> | 스냅샷 | 예 | 예 |
> | sshpublickeys | 예 | 아니요 |
> | virtualmachines | 예 | 예 |
> | virtualmachines / extensions | 예 | 예 |
> | virtualmachines / metricdefinitions | 예 | 아니요 |
> | virtualmachines / runcommands | 아니요 | 아니요 |
> | virtualmachinescalesets | 예 | 예 |
> | virtualmachinescalesets / extensions | 아니요 | 아니요 |
> | virtualmachinescalesets / networkinterfaces | 아니요 | 아니요 |
> | virtualmachinescalesets / publicipaddresses | 예 | 아니요 |
> | virtualmachinescalesets / virtualmachines | 예 | 아니요 |
> | virtualmachinescalesets / virtualmachines / networkinterfaces | 아니요 | 아니요 |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | aggregatedcost | 아니요 | 예 |
> | balances | 아니요 | 예 |
> | budgets | 아니요 | 예 |
> | charges | 아니요 | 아니요 |
> | costtags | 아니요 | 아니요 |
> | credits | 예 | 예 |
> | events | 예 | 예 |
> | forecasts | 예 | 예 |
> | lots | 예 | 예 |
> | marketplaces | 예 | 예 |
> | operationresults | 예 | 예 |
> | 작업 | 예 | 예 |
> | operationstatus | 예 | 예 |
> | pricesheets | 예 | 예 |
> | products | 예 | 예 |
> | reservationdetails | 예 | 예 |
> | reservationrecommendationdetails | 예 | 예 |
> | reservationrecommendations | 예 | 예 |
> | reservationsummaries | 예 | 예 |
> | reservationtransactions | 예 | 예 |
> | tags | 예 | 예 |
> | tenants | 예 | 예 |
> | terms | 예 | 예 |
> | usagedetails | 예 | 예 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | 예 | 아니요 |
> | 위치 | 예 | 아니요 |
> | locations / cachedimages | 예 | 아니요 |
> | locations / capabilities | 예 | 아니요 |
> | locations / deletevirtualnetworkorsubnets | 아니요 | 아니요 |
> | locations / operations | 예 | 예 |
> | locations / usages | 아니요 | 아니요 |
> | 작업 | 아니요 | 예 |
> | serviceassociationlinks | 아니요 | 예 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 예 | 예 |
> | 위치 | 예 | 예 |
> | locations / authorize | 예 | 예 |
> | locations / deletevirtualnetworkorsubnets | 예 | 예 |
> | locations / operationresults | 예 | 예 |
> | locations / setupauth | 예 | 예 |
> | 작업 | 아니요 | 아니요 |
> | registries | 예 | 예 |
> | registries / agentpools | 예 | 예 |
> | registries / agentpools / listqueuestatus | 아니요 | 예 |
> | registries / builds | 아니요 | 예 |
> | registries / builds / cancel | 아니요 | 예 |
> | registries / builds / getloglink | 아니요 | 예 |
> | registries / buildtasks | 예 | 예 |
> | registries / buildtasks / listsourcerepositoryproperties | 아니요 | 아니요 |
> | registries / buildtasks / steps | 아니요 | 아니요 |
> | registries / buildtasks / steps / listbuildarguments | 아니요 | 예 |
> | registries / eventgridfilters | 아니요 | 예 |
> | registries / exportpipelines | 아니요 | 예 |
> | registries / generatecredentials | 아니요 | 예 |
> | registries / getbuildsourceuploadurl | 아니요 | 아니요 |
> | registries / getcredentials | 아니요 | 예 |
> | registries / importimage | 아니요 | 예 |
> | registries / importpipelines | 아니요 | 예 |
> | registries / listbuildsourceuploadurl | 아니요 | 예 |
> | registries / listcredentials | 아니요 | 예 |
> | registries / listpolicies | 아니요 | 예 |
> | registries / listusages | 아니요 | 예 |
> | registries / pipelineruns | 아니요 | 예 |
> | registries / privateendpointconnectionproxies | 아니요 | 예 |
> | registries / privateendpointconnectionproxies / validate | 아니요 | 예 |
> | registries / privateendpointconnections | 아니요 | 예 |
> | registries / privatelinkresources | 아니요 | 예 |
> | registries / queuebuild | 아니요 | 예 |
> | registries / regeneratecredential | 아니요 | 예 |
> | registries / regeneratecredentials | 아니요 | 예 |
> | registries / replications | 예 | 예 |
> | registries / runs | 아니요 | 예 |
> | registries / runs / cancel | 아니요 | 예 |
> | registries / runs / listlogsasurl | 아니요 | 예 |
> | registries / schedulerun | 아니요 | 예 |
> | registries / scopemaps | 아니요 | 예 |
> | registries / taskruns | 아니요 | 예 |
> | registries / taskruns / listdetails | 아니요 | 아니요 |
> | registries / tasks | 예 | 예 |
> | registries / tasks / listdetails | 아니요 | 예 |
> | registries / tokens | 아니요 | 예 |
> | registries / updatepolicies | 아니요 | 예 |
> | registries / webhooks | 예 | 예 |
> | registries / webhooks / getcallbackconfig | 아니요 | 예 |
> | registries / webhooks / listevents | 아니요 | 예 |
> | registries / webhooks / ping | 아니요 | 아니요 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containerservices | 예 | 예 |
> | 위치 | 예 | 예 |
> | locations / openshiftclusters | 아니요 | 아니요 |
> | locations / operationresults | 예 | 예 |
> | locations / operations | 예 | 예 |
> | locations / orchestrators | 예 | 예 |
> | managedclusters | 아니요 | 아니요 |
> | openshiftmanagedclusters | 아니요 | 예 |
> | 작업 | 아니요 | 예 |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 애플리케이션 | 예 | 예 |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 아니요 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 경고 | 아니요 | 예 |
> | billingaccounts | 아니요 | 아니요 |
> | budgets | 아니요 | 아니요 |
> | cloudconnectors | 예 | 아니요 |
> | 커넥터 | 예 | 예 |
> | departments | 예 | 예 |
> | dimensions | 아니요 | 아니요 |
> | enrollmentaccounts | 아니요 | 예 |
> | exports | 아니요 | 예 |
> | externalbillingaccounts | 아니요 | 예 |
> | externalbillingaccounts / alerts | 아니요 | 예 |
> | externalbillingaccounts / dimensions | 아니요 | 아니요 |
> | externalbillingaccounts / forecast | 아니요 | 예 |
> | externalbillingaccounts / query | 아니요 | 예 |
> | externalsubscriptions | 아니요 | 예 |
> | externalsubscriptions / alerts | 아니요 | 예 |
> | externalsubscriptions / dimensions | 아니요 | 예 |
> | externalsubscriptions / forecast | 아니요 | 예 |
> | externalsubscriptions / query | 아니요 | 예 |
> | 예측 | 아니요 | 예 |
> | 작업 | 아니요 | 예 |
> | Query | 아니요 | 예 |
> | register | 아니요 | 예 |
> | reportconfigs | 아니요 | 예 |
> | reports | 아니요 | 예 |
> | 설정 | 아니요 | 예 |
> | showbackrules | 아니요 | 예 |
> | 뷰 | 아니요 | 예 |

## <a name="microsoftcostmanagementexports"></a>Microsoft.CostManagementExports

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 작업 | 예 | 예 |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hubs | 예 | 아니요 |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 작업 | 아니요 | 예 |
> | requests | 아니요 | 아니요 |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | associations | 아니요 | 아니요 |
> | 위치 | 아니요 | 아니요 |
> | locations / operationstatuses | 예 | 아니요 |
> | 작업 | 아니요 | 아니요 |
> | resourceproviders | 예 | 예 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | 아니요 | 아니요 |
> | 위치 | 예 | 예 |
> | locations / availableskus | 예 | 예 |
> | locations / checknameavailability | 예 | 예 |
> | locations / operationresults | 예 | 예 |
> | locations / regionconfiguration | 아니요 | 아니요 |
> | locations / validateaddress | 아니요 | 아니요 |
> | locations / validateinputs | 아니요 | 예 |
> | 작업 | 아니요 | 예 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availableskus | 예 | 예 |
> | databoxedgedevices | 예 | 예 |
> | databoxedgedevices / checknameavailability | 예 | 예 |
> | 작업 | 예 | 예 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 위치 | 예 | 아니요 |
> | locations / getnetworkpolicies | 아니요 | 아니요 |
> | locations / operationstatuses | 예 | 예 |
> | 작업 | 예 | 예 |
> | workspaces | 예 | 예 |
> | workspaces / dbworkspaces | 아니요 | 아니요 |
> | workspaces / virtualnetworkpeerings | 아니요 | 예 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | catalogs | 예 | 예 |
> | checknameavailability | 예 | 예 |
> | datacatalogs | 예 | 예 |
> | 위치 | 예 | 예 |
> | locations / jobs | 예 | 예 |
> | locations / operationresults | 아니요 | 아니요 |
> | 작업 | 아니요 | 아니요 |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | 예 | 예 |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 패키지 | 예 | 아니요 |
> | 플랜 | 예 | 아니요 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checkazuredatafactorynameavailability | 아니요 | 아니요 |
> | checkdatafactorynameavailability | 예 | 아니요 |
> | datafactories | 예 | 예 |
> | datafactories / diagnosticsettings | 예 | 예 |
> | datafactories / metricdefinitions | 예 | 예 |
> | datafactoryschema | 아니요 | 아니요 |
> | factories | 예 | 예 |
> | factories / integrationruntimes | 아니요 | 예 |
> | 위치 | 아니요 | 아니요 |
> | locations / configurefactoryrepo | 아니요 | 예 |
> | locations / getfeaturevalue | 아니요 | 예 |
> | 작업 | 아니요 | 예 |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | 예 | 예 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |
> | accounts / datalakestoreaccounts | 예 | 아니요 |
> | accounts / storageaccounts | 예 | 아니요 |
> | accounts / storageaccounts / containers | 아니요 | 아니요 |
> | accounts / storageaccounts / containers / listsastokens | 예 | 예 |
> | 위치 | 아니요 | 아니요 |
> | locations / capability | 아니요 | 아니요 |
> | locations / checknameavailability | 아니요 | 예 |
> | locations / operationresults | 아니요 | 예 |
> | locations / usages | 아니요 | 예 |
> | 작업 | 아니요 | 예 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |
> | accounts / eventgridfilters | 예 | 예 |
> | accounts / firewallrules | 예 | 예 |
> | 위치 | 예 | 예 |
> | locations / capability | 예 | 예 |
> | locations / checknameavailability | 아니요 | 아니요 |
> | locations / deletevirtualnetworkorsubnets | 아니요 | 아니요 |
> | locations / operationresults | 아니요 | 예 |
> | locations / usages | 아니요 | 예 |
> | 작업 | 아니요 | 예 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 위치 | 예 | 예 |
> | locations / checknameavailability | 예 | 예 |
> | locations / operationresults | 예 | 예 |
> | locations / operationstatuses | 예 | 예 |
> | 작업 | 예 | 예 |
> | services | 아니요 | 아니요 |
> | services / projects | 아니요 | 예 |
> | slots | 아니요 | 예 |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | backupvaults | 예 | 예 |
> | 위치 | 예 | 예 |
> | 작업 | 예 | 예 |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |
> | accounts / shares | 아니요 | 아니요 |
> | accounts / shares / datasets | 예 | 예 |
> | accounts / shares / invitations | 예 | 예 |
> | accounts / shares / providersharesubscriptions | 예 | 예 |
> | accounts / shares / synchronizationsettings | 아니요 | 아니요 |
> | accounts / sharesubscriptions | 아니요 | 아니요 |
> | accounts / sharesubscriptions / consumersourcedatasets | 아니요 | 예 |
> | accounts / sharesubscriptions / datasetmappings | 아니요 | 예 |
> | accounts / sharesubscriptions / triggers | 아니요 | 예 |
> | listinvitations | 아니요 | 예 |
> | 위치 | 아니요 | 예 |
> | locations / consumerinvitations | 아니요 | 예 |
> | locations / operationresults | 아니요 | 예 |
> | locations / rejectinvitation | 아니요 | 예 |
> | 작업 | 아니요 | 예 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 예 | 예 |
> | 위치 | 예 | 예 |
> | locations / azureasyncoperation | 예 | 예 |
> | locations / operationresults | 예 | 예 |
> | locations / performancetiers | 예 | 예 |
> | locations / privateendpointconnectionazureasyncoperation | 아니요 | 아니요 |
> | locations / privateendpointconnectionoperationresults | 아니요 | 예 |
> | locations / privateendpointconnectionproxyazureasyncoperation | 아니요 | 예 |
> | locations / privateendpointconnectionproxyoperationresults | 아니요 | 예 |
> | locations / recommendedactionsessionsazureasyncoperation | 아니요 | 예 |
> | locations / recommendedactionsessionsoperationresults | 아니요 | 예 |
> | locations / securityalertpoliciesazureasyncoperation | 아니요 | 예 |
> | locations / securityalertpoliciesoperationresults | 아니요 | 예 |
> | locations / serverkeyazureasyncoperation | 아니요 | 예 |
> | locations / serverkeyoperationresults | 아니요 | 예 |
> | 작업 | 아니요 | 예 |
> | servers | 예 | 예 |
> | servers / advisors | 아니요 | 예 |
> | servers / privateendpointconnectionproxies | 아니요 | 예 |
> | servers / privateendpointconnections | 아니요 | 예 |
> | servers / privatelinkresources | 아니요 | 예 |
> | servers / querytexts | 아니요 | 예 |
> | servers / recoverableservers | 아니요 | 아니요 |
> | servers / topquerystatistics | 아니요 | 예 |
> | servers / virtualnetworkrules | 아니요 | 예 |
> | servers / waitstatistics | 아니요 | 예 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 예 | 예 |
> | 위치 | 예 | 예 |
> | locations / administratorazureasyncoperation | 예 | 예 |
> | locations / administratoroperationresults | 예 | 예 |
> | locations / azureasyncoperation | 예 | 예 |
> | locations / operationresults | 아니요 | 아니요 |
> | locations / performancetiers | 아니요 | 예 |
> | locations / privateendpointconnectionazureasyncoperation | 아니요 | 예 |
> | locations / privateendpointconnectionoperationresults | 아니요 | 예 |
> | locations / privateendpointconnectionproxyazureasyncoperation | 아니요 | 예 |
> | locations / privateendpointconnectionproxyoperationresults | 아니요 | 예 |
> | locations / recommendedactionsessionsazureasyncoperation | 아니요 | 예 |
> | locations / recommendedactionsessionsoperationresults | 아니요 | 예 |
> | locations / securityalertpoliciesazureasyncoperation | 아니요 | 예 |
> | locations / securityalertpoliciesoperationresults | 아니요 | 예 |
> | locations / serverkeyazureasyncoperation | 아니요 | 예 |
> | locations / serverkeyoperationresults | 아니요 | 예 |
> | 작업 | 아니요 | 예 |
> | servers | 예 | 예 |
> | servers / advisors | 아니요 | 예 |
> | servers / keys | 아니요 | 예 |
> | servers / privateendpointconnectionproxies | 아니요 | 예 |
> | servers / privateendpointconnections | 아니요 | 예 |
> | servers / privatelinkresources | 아니요 | 예 |
> | servers / querytexts | 아니요 | 아니요 |
> | servers / recoverableservers | 아니요 | 예 |
> | servers / topquerystatistics | 아니요 | 예 |
> | servers / virtualnetworkrules | 아니요 | 예 |
> | servers / waitstatistics | 아니요 | 예 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 예 | 예 |
> | 위치 | 예 | 예 |
> | locations / administratorazureasyncoperation | 예 | 예 |
> | locations / administratoroperationresults | 예 | 예 |
> | locations / azureasyncoperation | 예 | 예 |
> | locations / operationresults | 아니요 | 아니요 |
> | locations / performancetiers | 아니요 | 예 |
> | locations / privateendpointconnectionazureasyncoperation | 아니요 | 예 |
> | locations / privateendpointconnectionoperationresults | 아니요 | 예 |
> | locations / privateendpointconnectionproxyazureasyncoperation | 아니요 | 예 |
> | locations / privateendpointconnectionproxyoperationresults | 아니요 | 예 |
> | locations / recommendedactionsessionsazureasyncoperation | 아니요 | 예 |
> | locations / recommendedactionsessionsoperationresults | 아니요 | 예 |
> | locations / securityalertpoliciesazureasyncoperation | 아니요 | 예 |
> | locations / securityalertpoliciesoperationresults | 아니요 | 예 |
> | locations / serverkeyazureasyncoperation | 아니요 | 예 |
> | locations / serverkeyoperationresults | 아니요 | 예 |
> | 작업 | 아니요 | 예 |
> | servergroups | 아니요 | 예 |
> | servers | 예 | 예 |
> | servers / advisors | 아니요 | 예 |
> | servers / keys | 아니요 | 예 |
> | servers / privateendpointconnectionproxies | 아니요 | 예 |
> | servers / privateendpointconnections | 아니요 | 예 |
> | servers / privatelinkresources | 아니요 | 예 |
> | servers / querytexts | 아니요 | 아니요 |
> | servers / recoverableservers | 아니요 | 예 |
> | servers / topquerystatistics | 아니요 | 예 |
> | servers / virtualnetworkrules | 아니요 | 예 |
> | servers / waitstatistics | 아니요 | 예 |
> | serversv2 | 예 | 예 |
> | singleservers | 예 | 예 |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | artifactsources | 예 | 예 |
> | operationresults | 예 | 예 |
> | 작업 | 예 | 예 |
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
> | applicationgroups / applications | 아니요 | 아니요 |
> | applicationgroups / desktops | 아니요 | 아니요 |
> | applicationgroups / startmenuitems | 아니요 | 아니요 |
> | hostpools | 예 | 예 |
> | hostpools / sessionhosts | 아니요 | 아니요 |
> | hostpools / sessionhosts / usersessions | 아니요 | 아니요 |
> | hostpools / usersessions | 아니요 | 예 |
> | 작업 | 아니요 | 예 |
> | workspaces | 예 | 예 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 예 | 예 |
> | checkprovisioningservicenameavailability | 예 | 예 |
> | elasticpools | 예 | 예 |
> | elasticpools / iothubtenants | 예 | 예 |
> | iothubs | 예 | 예 |
> | iothubs / eventgridfilters | 아니요 | 아니요 |
> | iothubs/securitysettings | 아니요 | 예 |
> | operationresults | 아니요 | 예 |
> | 작업 | 아니요 | 예 |
> | provisioningservices | 예 | 예 |
> | usages | 아니요 | 아니요 |

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
> | controllers / listconnectiondetails | 예 | 아니요 |
> | 위치 | 아니요 | 아니요 |
> | locations / checkcontainerhostmapping | 아니요 | 아니요 |
> | locations / operationresults | 아니요 | 아니요 |
> | 작업 | 아니요 | 아니요 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | labcenters | 예 | 예 |
> | labs | 예 | 예 |
> | labs / environments | 예 | 예 |
> | labs / servicerunners | 예 | 예 |
> | labs / virtualmachines | 예 | 아니요 |
> | 위치 | 아니요 | 예 |
> | locations / operations | 아니요 | 아니요 |
> | 작업 | 아니요 | 아니요 |
> | schedules | 예 | 예 |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | 예 | 예 |
> | digitaltwinsinstances/operationresults | 예 | 예 |
> | 위치 | 예 | 예 |
> | 작업 | 아니요 | 아니요 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | databaseaccountnames | 예 | 예 |
> | databaseaccounts | 예 | 예 |
> | 위치 | 예 | 예 |
> | locations / deletevirtualnetworkorsubnets | 아니요 | 아니요 |
> | locations / operationresults | 아니요 | 예 |
> | locations / operationsstatus | 아니요 | 아니요 |
> | operationresults | 아니요 | 예 |
> | 작업 | 아니요 | 예 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checkdomainavailability | 예 | 예 |
> | domains | 예 | 예 |
> | domains / domainownershipidentifiers | 예 | 예 |
> | generatessorequest | 아니요 | 아니요 |
> | listdomainrecommendations | 아니요 | 예 |
> | 작업 | 아니요 | 아니요 |
> | topleveldomains | 아니요 | 예 |
> | validatedomainregistrationinformation | 아니요 | 예 |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 위치 | 예 | 예 |
> | locations / operationresults | 예 | 예 |
> | 작업 | 예 | 예 |
> | services | 예 | 예 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domains | 예 | 예 |
> | domains / topics | 예 | 예 |
> | eventsubscriptions | 아니요 - 독립적으로 이동할 수는 없지만, 구독된 리소스와 함께 자동으로 이동할 수 있습니다. | 아니요 - 독립적으로 이동할 수는 없지만, 구독된 리소스와 함께 자동으로 이동할 수 있습니다. |
> | extensiontopics | 아니요 | 아니요 |
> | 위치 | 아니요 | 아니요 |
> | locations / eventsubscriptions | 아니요 | 아니요 |
> | locations / operationresults | 아니요 | 예 |
> | locations / operationsstatus | 아니요 | 아니요 |
> | locations / topictypes | 아니요 | 예 |
> | operationresults | 아니요 | 예 |
> | 작업 | 아니요 | 예 |
> | operationsstatus | 아니요 | 예 |
> | partnernamespaces | 예 | 예 |
> | partnernamespaces / eventchannels | 아니요 | 예 |
> | partnerregistrations | 아니요 | 예 |
> | partnertopics | 예 | 예 |
> | partnertopics / eventsubscriptions | 아니요 | 예 |
> | systemtopics | 예 | 예 |
> | systemtopics / eventsubscriptions | 아니요 | 예 |
> | topics | 예 | 예 |
> | topictypes | 아니요 | 아니요 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availableclusterregions | 아니요 | 아니요 |
> | checknameavailability | 예 | 예 |
> | checknamespaceavailability | 아니요 | 아니요 |
> | clusters | 예 | 예 |
> | 위치 | 아니요 | 아니요 |
> | locations / deletevirtualnetworkorsubnets | 아니요 | 예 |
> | 네임스페이스 | 예 | 예 |
> | namespaces / authorizationrules | 아니요 | 예 |
> | namespaces / disasterrecoveryconfigs | 아니요 | 아니요 |
> | namespaces / disasterrecoveryconfigs / checknameavailability | 아니요 | 예 |
> | namespaces / eventhubs | 아니요 | 예 |
> | namespaces / eventhubs / authorizationrules | 아니요 | 아니요 |
> | namespaces / eventhubs / consumergroups | 아니요 | 예 |
> | namespaces / networkrulesets | 아니요 | 예 |
> | 작업 | 아니요 | 예 |
> | sku | 아니요 | 예 |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | experimentworkspaces | 예 | 예 |
> | 위치 | 예 | 예 |
> | locations / operations | 예 | 예 |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 네임스페이스 | 예 | 예 |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | featureproviders | 예 | 아니요 |
> | 기능 | 예 | 아니요 |
> | 작업 | 아니요 | 아니요 |
> | providers | 아니요 | 아니요 |
> | subscriptionfeatureregistrations | 아니요 | 아니요 |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | automanagedaccounts | 예 | 아니요 |
> | automanagedvmconfigurationprofiles | 예 | 아니요 |
> | guestconfigurationassignments | 아니요 | 아니요 |
> | 작업 | 예 | 예 |
> | software | 아니요 | 아니요 |
> | softwareupdateprofile | 아니요 | 예 |
> | softwareupdates | 아니요 | 예 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hanainstances | 예 | 예 |
> | 위치 | 예 | 예 |
> | locations / operations | 예 | 예 |
> | locations / operationsstatus | 예 | 예 |
> | 작업 | 아니요 | 아니요 |
> | sapmonitors | 예 | 예 |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dedicatedhsms | 예 | 예 |
> | 위치 | 예 | 예 |
> | 작업 | 예 | 예 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!IMPORTANT]
> 새 구독 또는 리소스 그룹에 HDInsight 클러스터를 이동할 수 있습니다. 그러나 HDInsight 클러스터에 연결된 네트워킹 리소스(예: Virtual Network, NIC 또는 부하 분산 장치)는 구독 간에 이동할 수 없습니다. 또한 클러스터에 대한 가상 머신에 연결된 NIC를 새 리소스 그룹으로 이동할 수 없습니다.
>
> HDInsight 클러스터를 새 구독으로 이동할 때 먼저 다른 리소스(예: 스토리지 계정)를 이동합니다. 그런 다음 자체적으로 HDInsight 클러스터를 이동합니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | 예 | 예 |
> | clusters / applications | 아니요 | 예 |
> | clusters / operationresults | 아니요 | 아니요 |
> | 위치 | 아니요 | 아니요 |
> | locations / azureasyncoperations | 아니요 | 아니요 |
> | locations / billingspecs | 예 | 아니요 |
> | locations / capabilities | 예 | 아니요 |
> | locations / operationresults | 예 | 아니요 |
> | locations / usages | 예 | 아니요 |
> | locations / validatecreaterequest | 예 | 아니요 |
> | 작업 | 예 | 아니요 |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 아니요 | 예 |
> | 위치 | 아니요 | 예 |
> | locations / operationresults | 아니요 | 아니요 |
> | 작업 | 아니요 | 아니요 |
> | services | 예 | 예 |
> | services / privateendpointconnections | 아니요 | 예 |
> | services / privatelinkresources | 아니요 | 예 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 위치 | 예 | 예 |
> | locations / operationresults | 아니요 | 아니요 |
> | locations / operationstatus | 예 | 예 |
> | machines | 예 | 예 |
> | machines / extensions | 예 | 예 |
> | 작업 | 아니요 | 아니요 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datamanagers | 예 | 예 |
> | 작업 | 아니요 | 아니요 |

## <a name="microsofthybridnetwork"></a>HybridNetwork

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | devices | 예 | 예 |
> | 위치 | 아니요 | 아니요 |
> | locations / operationstatuses | 아니요 | 예 |
> | 작업 | 아니요 | 예 |
> | vnfs | 아니요 | 예 |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | components | 아니요 | 아니요 |
> | 위치 | 아니요 | 아니요 |
> | networkscopes | 아니요 | 예 |
> | 작업 | 아니요 | 예 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | 예 | 예 |
> | 위치 | 아니요 | 아니요 |
> | locations / operationresults | 아니요 | 예 |
> | 작업 | 아니요 | 아니요 |

## <a name="microsoftinsights"></a>microsoft.insights

> [!IMPORTANT]
> 새 구독으로 이동하는 경우 [구독 할당량](azure-subscription-service-limits.md#azure-monitor-limits)을 초과하지 않아야 합니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | actiongroups | 예 | 예 |
> | activitylogalerts | 예 | 아니요 |
> | alertrules | 예 | 예 |
> | autoscalesettings | 예 | 예 |
> | baseline | 예 | 아니요 |
> | calculatebaseline | 예 | 아니요 |
> | components | 예 | 예 |
> | components / events | 예 | 아니요 |
> | components / linkedstorageaccounts | 예 | 아니요 |
> | 구성 요소/메타 데이터 | 예 | 아니요 |
> | components / metrics | 예 | 아니요 |
> | components / pricingplans | 예 | 아니요 |
> | components / query | 예 | 아니요 |
> | datacollectionrules | 예 | 아니요 |
> | diagnosticsettings | 예 | 아니요 |
> | diagnosticsettingscategories | 예 | 아니요 |
> | eventcategories | 예 | 아니요 |
> | eventtypes | 예 | 아니요 |
> | extendeddiagnosticsettings | 예 | 아니요 |
> | guestdiagnosticsettings | 예 | 아니요 |
> | listmigrationdate | 예 | 아니요 |
> | 위치 | 예 | 아니요 |
> | locations / operationresults | 예 | 아니요 |
> | logdefinitions | 예 | 아니요 |
> | logprofiles | 예 | 아니요 |
> | 로그 | 예 | 아니요 |
> | metricalerts | 예 | 아니요 |
> | metricbaselines | 예 | 아니요 |
> | metricbatch | 예 | 아니요 |
> | metricdefinitions | 예 | 아니요 |
> | metricnamespaces | 예 | 아니요 |
> | 메트릭 | 예 | 아니요 |
> | migratealertrules | 예 | 아니요 |
> | migratetonewpricingmodel | 예 | 아니요 |
> | myworkbooks | 예 | 아니요 |
> | notificationgroups | 예 | 아니요 |
> | 작업 | 예 | 아니요 |
> | privatelinkscopeoperationstatuses | 예 | 아니요 |
> | privatelinkscopes | 아니요 | 아니요 |
> | privatelinkscopes / privateendpointconnectionproxies | 예 | 아니요 |
> | privatelinkscopes / privateendpointconnections | 예 | 아니요 |
> | privatelinkscopes / scopedresources | 예 | 아니요 |
> | rollbacktolegacypricingmodel | 아니요 | 아니요 |
> | scheduledqueryrules | 예 | 예 |
> | 토폴로지 | 아니요 | 아니요 |
> | 트랜잭션 | 아니요 | 아니요 |
> | vminsightsonboardingstatuses | 아니요 | 예 |
> | webtests | 예 | 예 |
> | 웹 테스트/gettestresultfile | 아니요 | 예 |
> | workbooks | 예 | 예 |
> | workbooktemplates | 예 | 예 |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | apptemplates | 아니요 | 아니요 |
> | checknameavailability | 아니요 | 아니요 |
> | checksubdomainavailability | 아니요 | 예 |
> | iotapps | 예 | 예 |
> | 작업 | 아니요 | 아니요 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 예 | 예 |
> | graph | 예 | 예 |
> | 작업 | 아니요 | 예 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!IMPORTANT]
> 디스크 암호화에 사용되는 Key Vault는 동일한 구독의 리소스 그룹으로 또는 구독 간에 이동할 수 없습니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 예 | 아니요 |
> | deletedvaults | 예 | 아니요 |
> | hsmpools | 아니요 | 아니요 |
> | 위치 | 예 | 아니요 |
> | locations / deletedvaults | 예 | 아니요 |
> | locations / deletevirtualnetworkorsubnets | 예 | 아니요 |
> | locations / operationresults | 아니요 | 아니요 |
> | managedhsms | 아니요 | 아니요 |
> | 작업 | 아니요 | 예 |
> | vaults | 예 | 예 |
> | vaults / accesspolicies | 아니요 | 예 |
> | vaults / eventgridfilters | 아니요 | 예 |
> | vaults / secrets | 아니요 | 아니요 |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | connectedclusters | 예 | 예 |
> | 위치 | 아니요 | 아니요 |
> | locations / operationstatuses | 아니요 | 아니요 |
> | 작업 | 예 | 아니요 |
> | registeredsubscriptions | 예 | 아니요 |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | sourcecontrolconfigurations | 아니요 | 예 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | 예 | 예 |
> | clusters / attacheddatabaseconfigurations | 예 | 예 |
> | clusters / databases | 예 | 예 |
> | clusters / databases / dataconnections | 예 | 예 |
> | clusters / databases / eventhubconnections | 아니요 | 아니요 |
> | clusters / databases / principalassignments | 아니요 | 예 |
> | clusters / principalassignments | 아니요 | 예 |
> | 위치 | 아니요 | 예 |
> | locations / checknameavailability | 아니요 | 예 |
> | locations / operationresults | 아니요 | 예 |
> | 작업 | 아니요 | 아니요 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | labaccounts | 아니요 | 아니요 |
> | 위치 | 아니요 | 아니요 |
> | locations / operations | 예 | 아니요 |
> | 작업 | 아니요 | 아니요 |
> | users | 예 | 예 |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 아니요 |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아니요 | 예 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | 예 | 예 |
> | integrationaccounts | 예 | 예 |
> | integrationserviceenvironments | 예 | 예 |
> | integrationserviceenvironments / managedapis | 예 | 예 |
> | isolatedenvironments | 아니요 | 예 |
> | 위치 | 아니요 | 예 |
> | locations / workflows | 아니요 | 예 |
> | 작업 | 아니요 | 예 |
> | workflows | 예 | 예 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | commitmentplans | 아니요 | 아니요 |
> | 위치 | 아니요 | 예 |
> | locations / operations | 아니요 | 아니요 |
> | locations / operationsstatus | 아니요 | 아니요 |
> | 작업 | 예 | 아니요 |
> | webservices | 예 | 아니요 |
> | workspaces | 예 | 예 |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | 아니요 | 예 |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아니요 | 예 |
> | accounts / workspaces | 아니요 | 아니요 |
> | accounts / workspaces / projects | 아니요 | 아니요 |
> | teamaccounts | 예 | 아니요 |
> | teamaccounts / workspaces | 아니요 | 아니요 |
> | teamaccounts / workspaces / projects | 예 | 예 |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 아니요 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 위치 | 아니요 | 예 |
> | locations / computeoperationsstatus | 아니요 | 예 |
> | locations / quotas | 아니요 | 예 |
> | locations / updatequotas | 아니요 | 예 |
> | locations / usages | 아니요 | 예 |
> | locations / vmsizes | 아니요 | 예 |
> | locations / workspaceoperationsstatus | 아니요 | 아니요 |
> | 작업 | 아니요 | 아니요 |
> | workspaces | 예 | 아니요 |
> | workspaces / computes | 예 | 아니요 |
> | workspaces / eventgridfilters | 아니요 | 아니요 |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applyupdates | 아니요 | 아니요 |
> | configurationassignments | 예 | 아니요 |
> | maintenanceconfigurations | 예 | 예 |
> | updates | 예 | 아니요 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | ID | 예 | 아니요 |
> | 작업 | 예 | 아니요 |
> | userassignedidentities | 예 | 아니요 |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managednetworks | 아니요 | 아니요 |
> | managednetworks / managednetworkgroups | 예 | 아니요 |
> | managednetworks / managednetworkpeeringpolicies | 예 | 아니요 |
> | 알림 | 예 | 아니요 |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | marketplaceregistrationdefinitions | 아니요 | 아니요 |
> | 작업 | 아니요 | 아니요 |
> | operationstatuses | 예 | 아니요 |
> | registrationassignments | 예 | 아니요 |
> | registrationdefinitions | 예 | 아니요 |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 아니요 | 예 |
> | getentities | 아니요 | 예 |
> | managementgroups | 아니요 | 예 |
> | managementgroups / settings | 아니요 | 예 |
> | operationresults | 아니요 | 예 |
> | operationresults / asyncoperation | 아니요 | 아니요 |
> | 작업 | 아니요 | 아니요 |
> | 리소스 | 아니요 | 아니요 |
> | starttenantbackfill | 예 | 아니요 |
> | tenantbackfillstatus | 아니요 | 아니요 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |
> | accounts / eventgridfilters | 예 | 아니요 |
> | accounts / privateatlases | 예 | 예 |
> | 작업 | 예 | 아니요 |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | listavailableoffers | 아니요 | 예 |
> | offers | 아니요 | 예 |
> | offertypes | 아니요 | 예 |
> | offertypes / publishers | 아니요 | 예 |
> | offertypes / publishers / offers | 아니요 | 예 |
> | offertypes / publishers / offers / plans | 아니요 | 예 |
> | offertypes / publishers / offers / plans / agreements | 아니요 | 예 |
> | offertypes / publishers / offers / plans / configs | 아니요 | 예 |
> | offertypes / publishers / offers / plans / configs / importimage | 아니요 | 예 |
> | 작업 | 아니요 | 예 |
> | privategalleryitems | 아니요 | 예 |
> | privatestoreclient | 아니요 | 예 |
> | privatestores | 아니요 | 예 |
> | privatestores / offers | 아니요 | 예 |
> | products | 아니요 | 아니요 |
> | publishers | 아니요 | 아니요 |
> | publishers / offers | 예 | 아니요 |
> | publishers / offers / amendments | 예 | 아니요 |
> | register | 예 | 아니요 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | classicdevservices | 아니요 | 아니요 |
> | listcommunicationpreference | 예 | 아니요 |
> | 작업 | 예 | 아니요 |
> | updatecommunicationpreference | 예 | 아니요 |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | agreements | 예 | 아니요 |
> | offertypes | 예 | 아니요 |
> | 작업 | 예 | 아니요 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 아니요 | 예 |
> | 위치 | 아니요 | 예 |
> | locations / checknameavailability | 아니요 | 예 |
> | mediaservices | 예 | 예 |
> | mediaservices / accountfilters | 아니요 | 예 |
> | mediaservices / assets | 아니요 | 아니요 |
> | mediaservices / assets / assetfilters | 아니요 | 예 |
> | mediaservices / contentkeypolicies | 아니요 | 예 |
> | mediaservices / eventgridfilters | 아니요 | 예 |
> | mediaservices / liveeventoperations | 아니요 | 아니요 |
> | mediaservices / liveevents | 예 | 예 |
> | mediaservices / liveevents / liveoutputs | 아니요 | 예 |
> | mediaservices / liveoutputoperations | 아니요 | 예 |
> | mediaservices / streamingendpointoperations | 아니요 | 예 |
> | mediaservices / streamingendpoints | 예 | 예 |
> | mediaservices / streaminglocators | 아니요 | 아니요 |
> | mediaservices / streamingpolicies | 아니요 | 아니요 |
> | mediaservices / transforms | 예 | 아니요 |
> | mediaservices / transforms / jobs | 아니요 | 아니요 |
> | 작업 | 아니요 | 아니요 |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | appclusters | 예 | 아니요 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | 아니요 | 예 |
> | 위치 | 아니요 | 예 |
> | locations / assessmentoptions | 아니요 | 예 |
> | locations / checknameavailability | 아니요 | 아니요 |
> | migrateprojects | 아니요 | 아니요 |
> | movecollections | 예 | 아니요 |
> | 작업 | 예 | 아니요 |
> | projects | 예 | 아니요 |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | 아니요 | 아니요 |
> | 위치 | 아니요 | 아니요 |
> | locations / checknameavailability | 아니요 | 아니요 |
> | objectunderstandingaccounts | 아니요 | 아니요 |
> | 작업 | 예 | 아니요 |
> | remoterenderingaccounts | 예 | 예 |
> | spatialanchorsaccounts | 예 | 예 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | netappaccounts | 아니요 | 예 |
> | netappaccounts / backuppolicies | 아니요 | 예 |
> | netappaccounts / capacitypools | 아니요 | 아니요 |
> | netappaccounts / capacitypools / volumes | 아니요 | 아니요 |
> | netappaccounts / capacitypools / volumes / mounttargets | 예 | 아니요 |
> | netappaccounts / capacitypools / volumes / snapshots | 예 | 아니요 |
> | 작업 | 예 | 아니요 |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!IMPORTANT]
> [네트워킹 이동 지침](./move-limitations/networking-move-limitations.md)을 참조하세요.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgatewayavailablerequestheaders | 예 | 예 |
> | applicationgatewayavailableresponseheaders | 예 | 예 |
> | applicationgatewayavailableservervariables | 아니요 | 아니요 |
> | applicationgatewayavailablessloptions | 예 | 예 |
> | applicationgatewayavailablewafrulesets | 예 | 예 |
> | applicationgateways | 예 | 예 |
> | applicationgatewaywebapplicationfirewallpolicies | 예 | 예 |
> | applicationsecuritygroups | 예 | 예 |
> | azurefirewallfqdntags | 예 | 예 |
> | azurefirewalls | 아니요 | 아니요 |
> | bastionhosts | 아니요 | 아니요 |
> | bgpservicecommunities | 예 | 예 |
> | checkfrontdoornameavailability | 예 | 예 |
> | checktrafficmanagernameavailability | 예 | 예 |
> | connections | 예 | 예 |
> | ddoscustompolicies | 예 | 예 |
> | ddosprotectionplans | 예 | 예 |
> | dnsoperationresults | 예 | 예 |
> | dnsoperationstatuses | 예 | 예 |
> | dnszones | 예 | 예 |
> | dnszones / a | 예 | 예 |
> | dnszones / aaaa | 예 | 예 |
> | dnszones / all | 예 | 예 |
> | dnszones / caa | 예 | 예 |
> | dnszones / cname | 예 | 예 |
> | dnszones / mx | 예 | 예 |
> | dnszones / ns | 예 | 예 |
> | dnszones / ptr | 예 | 예 |
> | dnszones / recordsets | 예 | 예 |
> | dnszones / soa | 예 | 예 |
> | dnszones / srv | 아니요 | 아니요 |
> | dnszones / txt | 예 | 예 |
> | expressroutecircuits | 예 | 예 |
> | expressroutegateways | 예 | 예 |
> | expressrouteserviceproviders | 예 | 예 |
> | firewallpolicies | 예 | 예 |
> | frontdooroperationresults | 예 | 예 |
> | frontdoors | 아니요 | 아니요 |
> | frontdoors / frontendendpoints | 아니요 | 아니요 |
> | frontdoorwebapplicationfirewallmanagedrulesets | 아니요 | 아니요 |
> | frontdoorwebapplicationfirewallpolicies | 아니요 | 아니요 |
> | getdnsresourcereference | 예 | 아니요 |
> | internalnotify | 예 | 아니요 |
> | ipallocations | 예 | 예 |
> | ipgroups | 예 | 예 |
> | loadbalancers | 예 - 기본 SKU<br>아니요 - 표준 SKU | 예 - 기본 SKU<br>아니요 - 표준 SKU |
> | localnetworkgateways | 예 | 예 |
> | 위치 | 예 | 예 |
> | locations / autoapprovedprivatelinkservices | 예 | 예 |
> | locations / availabledelegations | 예 | 예 |
> | locations / availableprivateendpointtypes | 예 | 예 |
> | locations / availableservicealiases | 예 | 예 |
> | locations / baremetaltenants | 예 | 예 |
> | locations / batchnotifyprivateendpointsforresourcemove | 예 | 예 |
> | locations / batchvalidateprivateendpointsforresourcemove | 예 | 예 |
> | locations / checkacceleratednetworkingsupport | 예 | 예 |
> | locations / checkdnsnameavailability | 예 | 예 |
> | locations / checkprivatelinkservicevisibility | 예 | 예 |
> | locations / commitinternalazurenetworkmanagerconfiguration | 예 | 예 |
> | locations / effectiveresourceownership | 예 | 예 |
> | locations / nfvoperationresults | 예 | 예 |
> | locations / nfvoperations | 예 | 예 |
> | locations / operationresults | 예 | 예 |
> | locations / operations | 아니요 | 아니요 |
> | locations / servicetags | 예 | 예 |
> | locations / setresourceownership | 아니요 | 아니요 |
> | locations / supportedvirtualmachinesizes | 아니요 | 아니요 |
> | locations / usages | 예 | 예 |
> | locations / validateresourceownership | 아니요 | 아니요 |
> | locations / virtualnetworkavailableendpointservices | 아니요 | 예 |
> | natgateways | 아니요 | 예 |
> | networkexperimentprofiles | 아니요 | 예 |
> | networkintentpolicies | 예 | 예 |
> | networkinterfaces | 예 | 예 |
> | networkprofiles | 예 | 예 |
> | networksecuritygroups | 예 | 예 |
> | networkwatchers | 예 | 예 |
> | networkwatchers / connectionmonitors | 예 | 예 |
> | networkwatchers / flowlogs | 예 | 예 |
> | networkwatchers / pingmeshes | 예 | 예 |
> | 작업 | 예 | 예 |
> | p2svpngateways | 예 | 예 |
> | privatednsoperationresults | 예 | 예 |
> | privatednsoperationstatuses | 예 | 예 |
> | privatednszones | 예 | 예 |
> | privatednszones / a | 예 | 예 |
> | privatednszones / aaaa | 예 | 예 |
> | privatednszones / all | 아니요 | 아니요 |
> | privatednszones / cname | 예 | 예 |
> | privatednszones / mx | 예 | 예 |
> | privatednszones / ptr | 아니요 | 아니요 |
> | privatednszones / soa | 예 | 예 |
> | privatednszones / srv | 아니요 | 아니요 |
> | privatednszones / txt | 아니요 | 아니요 |
> | privatednszones / virtualnetworklinks | 예 | 예 |
> | privatednszonesinternal | 예 | 아니요 |
> | privateendpointredirectmaps | 아니요 | 아니요 |
> | privateendpoints | 예 | 예 |
> | privatelinkservices | 아니요 | 아니요 |
> | publicipaddresses | 예 - 기본 SKU<br>아니요 - 표준 SKU | 예 - 기본 SKU<br>아니요 - 표준 SKU |
> | publicipprefixes | 예 | 예 |
> | routefilters | 예 | 예 |
> | routetables | 예 | 예 |
> | securitypartnerproviders | 예 | 예 |
> | serviceendpointpolicies | 예 | 예 |
> | trafficmanagergeographichierarchies | 예 | 예 |
> | trafficmanagerprofiles | 예 | 예 |
> | trafficmanagerprofiles / heatmaps | 예 | 예 |
> | trafficmanagerusermetricskeys | 예 | 예 |
> | virtualhubs | 예 | 예 |
> | virtualnetworkgateways | 예 | 예 |
> | virtualnetworks | 예 | 예 |
> | virtualnetworktaps | 아니요 | 아니요 |
> | virtualrouters | 예 | 예 |
> | virtualwans | 예 | 아니요 |
> | vpngateways(Virtual WAN) | 아니요 | 아니요 |
> | vpnserverconfigurations | 아니요 | 아니요 |
> | vpnsites(Virtual WAN) | 예 | 아니요 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 아니요 | 아니요 |
> | checknamespaceavailability | 아니요 | 아니요 |
> | 네임스페이스 | 예 | 예 |
> | namespaces / notificationhubs | 예 | 예 |
> | operationresults | 아니요 | 아니요 |
> | 작업 | 예 | 예 |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | osnamespaces | 예 | 예 |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hypervsites | 예 | 아니요 |
> | importsites | 예 | 아니요 |
> | 작업 | 예 | 아니요 |
> | serversites | 예 | 아니요 |
> | vmwaresites | 예 | 아니요 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!IMPORTANT]
> 새 구독으로 이동하는 경우 [구독 할당량](azure-subscription-service-limits.md#azure-monitor-limits)을 초과하지 않아야 합니다.
>
> 연결된 자동화 계정이 있는 작업 영역은 이동할 수 없습니다. 이동 작업을 시작하기 전에 자동화 계정의 연결을 해제해야 합니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | 아니요 | 예 |
> | deletedworkspaces | 아니요 | 예 |
> | linktargets | 아니요 | 예 |
> | 위치 | 아니요 | 예 |
> | locations / operationstatuses | 아니요 | 예 |
> | 작업 | 아니요 | 예 |
> | storageinsightconfigs | 아니요 | 아니요 |
> | workspaces | 예 | 예 |
> | workspaces / datasources | 아니요 | 아니요 |
> | workspaces / linkedservices | 예 | 아니요 |
> | workspaces / linkedstorageaccounts | 아니요 | 아니요 |
> | workspaces / metadata | 예 | 아니요 |
> | workspaces / query | 아니요 | 아니요 |
> | workspaces / scopedprivatelinkproxies | 아니요 | 아니요 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managementassociations | 예 | 아니요 |
> | managementconfigurations | 예 | 예 |
> | 작업 | 예 | 아니요 |
> | solutions | 예 | 예 |
> | 뷰 | 예 | 예 |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checkserviceprovideravailability | 아니요 | 예 |
> | legacypeerings | 아니요 | 예 |
> | 작업 | 아니요 | 예 |
> | peerasns | 아니요 | 아니요 |
> | peeringlocations | 아니요 | 아니요 |
> | peerings | 예 | 아니요 |
> | peeringservicecountries | 예 | 아니요 |
> | peeringservicelocations | 예 | 아니요 |
> | peeringserviceproviders | 예 | 아니요 |
> | peeringservices | 예 | 아니요 |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | asyncoperationresults | 아니요 | 아니요 |
> | 작업 | 예 | 아니요 |
> | policyevents | 아니요 | 아니요 |
> | policystates | 예 | 아니요 |
> | policytrackedresources | 예 | 아니요 |
> | remediations | 예 | 아니요 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | consoles | 아니요 | 아니요 |
> | dashboards | 예 | 예 |
> | 위치 | 예 | 아니요 |
> | locations / consoles | 아니요 | 아니요 |
> | locations / usersettings | 예 | 예 |
> | 작업 | 예 | 예 |
> | usersettings | 아니요 | 아니요 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 위치 | 아니요 | 아니요 |
> | locations / checknameavailability | 예 | 예 |
> | workspacecollections | 예 | 예 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | capacities | 예 | 예 |
> | 위치 | 예 | 아니요 |
> | locations / checknameavailability | 아니요 | 아니요 |
> | locations / operationresults | 예 | 예 |
> | locations / operationstatuses | 예 | 예 |
> | 작업 | 예 | 예 |

## <a name="microsoftpowerplatform"></a>Microsoft PowerPlatform

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 작업 | 예 | 예 |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |
> | checknameavailability | 예 | 예 |
> | 작업 | 예 | 예 |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availableaccounts | 아니요 | 아니요 |
> | providerregistrations | 예 | 예 |
> | providerregistrations / resourcetyperegistrations | 예 | 예 |
> | rollouts | 예 | 예 |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 위치 | 아니요 | 아니요 |
> | locations / operationstatuses | 예 | 예 |
> | 작업 | 예 | 예 |
> | workspaces | 예 | 예 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!IMPORTANT]
> [Recovery Services 이동 지침](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)을 참조하세요.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | backupprotecteditems | 아니요 | 예 |
> | 위치 | 아니요 | 예 |
> | locations / allocatedstamp | 아니요 | 예 |
> | locations / allocatestamp | 아니요 | 예 |
> | locations / backupaadproperties | 아니요 | 예 |
> | locations / backupcrossregionrestore | 아니요 | 예 |
> | locations / backupcrrjob | 아니요 | 예 |
> | locations / backupcrrjobs | 아니요 | 예 |
> | locations / backupcrroperationresults | 아니요 | 예 |
> | locations / backupcrroperationsstatus | 아니요 | 예 |
> | locations / backupprevalidateprotection | 아니요 | 예 |
> | locations / backupstatus | 아니요 | 아니요 |
> | locations / backupvalidatefeatures | 아니요 | 아니요 |
> | locations / checknameavailability | 아니요 | 아니요 |
> | 작업 | 예 | 예 |
> | replicationeligibilityresults | 예 | 예 |
> | vaults | 예 | 예 |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 위치 | 예 | 아니요 |
> | locations / operationresults | 아니요 | 아니요 |
> | locations / operationsstatus | 예 | 예 |
> | openshiftclusters | 아니요 | 아니요 |
> | 작업 | 예 | 예 |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 예 | 아니요 |
> | 네임스페이스 | 예 | 예 |
> | namespaces / authorizationrules | 예 | 아니요 |
> | namespaces / hybridconnections | 예 | 아니요 |
> | namespaces / hybridconnections / authorizationrules | 예 | 아니요 |
> | namespaces / privateendpointconnections | 아니요 | 아니요 |
> | namespaces / wcfrelays | 예 | 예 |
> | namespaces / wcfrelays / authorizationrules | 아니요 | 아니요 |
> | 작업 | 예 | 예 |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 작업 | 예 | 아니요 |
> | 쿼리 | 예 | 예 |
> | resourcechangedetails | 예 | 아니요 |
> | resourcechanges | 아니요 | 아니요 |
> | 리소스 | 예 | 예 |
> | resourceshistory | 예 | 예 |
> | subscriptionsstatus | 예 | 예 |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availabilitystatuses | 예 | 아니요 |
> | childavailabilitystatuses | 예 | 아니요 |
> | childresources | 예 | 아니요 |
> | emergingissues | 예 | 아니요 |
> | events | 아니요 | 아니요 |
> | metadata | 예 | 예 |
> | 알림 | 예 | 예 |
> | 작업 | 예 | 예 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | calculatetemplatehash | 예 | 아니요 |
> | checkpolicycompliance | 예 | 아니요 |
> | checkresourcename | 예 | 아니요 |
> | 배포 | 예 | 아니요 |
> | deployments / operations | 예 | 아니요 |
> | deploymentscripts | 예 | 아니요 |
> | deploymentscripts / logs | 예 | 아니요 |
> | 링크 | 예 | 아니요 |
> | 위치 | 예 | 아니요 |
> | locations / deploymentscriptoperationresults | 예 | 아니요 |
> | notifyresourcejobs | 예 | 아니요 |
> | operationresults | 예 | 아니요 |
> | 작업 | 예 | 아니요 |
> | providers | 예 | 아니요 |
> | resourcegroups | 예 | 아니요 |
> | 리소스 | 예 | 아니요 |
> | 구독 | 예 | 아니요 |
> | subscriptions / locations | 예 | 아니요 |
> | subscriptions / operationresults | 예 | 아니요 |
> | subscriptions / providers | 예 | 아니요 |
> | subscriptions / resourcegroups | 예 | 아니요 |
> | subscriptions / resourcegroups / resources | 예 | 아니요 |
> | subscriptions / resources | 예 | 아니요 |
> | subscriptions / tagnames | 예 | 아니요 |
> | subscriptions / tagnames / tagvalues | 아니요 | 아니요 |
> | tags | 아니요 | 예 |
> | templatespecs | 예 | 예 |
> | templatespecs / versions | 예 | 예 |
> | tenants | 예 | 예 |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 애플리케이션 | 예 | 아니요 |
> | checkmoderneligibility | 아니요 | 아니요 |
> | checknameavailability | 예 | 예 |
> | operationresults | 예 | 예 |
> | 작업 | 예 | 예 |
> | saasresources | 예 | 예 |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!IMPORTANT]
> 다른 지역에 있는 여러 Search 리소스를 한 번의 작업으로 이동할 수 없습니다. 대신 별도 작업으로 이동합니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 아니요 | 아니요 |
> | checkservicenameavailability | 예 | 예 |
> | 작업 | 예 | 예 |
> | resourcehealthmetadata | 예 | 예 |
> | searchservices | 예 | 예 |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | 예 | 아니요 |
> | advancedthreatprotectionsettings | 예 | 아니요 |
> | 경고 | 예 | 아니요 |
> | allowedconnections | 아니요 | 아니요 |
> | applicationwhitelistings | 예 | 아니요 |
> | assessmentmetadata | 예 | 아니요 |
> | assessments | 예 | 아니요 |
> | autodismissalertsrules | 예 | 아니요 |
> | automations | 예 | 예 |
> | autoprovisioningsettings | 예 | 아니요 |
> | complianceresults | 예 | 아니요 |
> | compliances | 예 | 아니요 |
> | datacollectionagents | 아니요 | 아니요 |
> | devicesecuritygroups | 예 | 아니요 |
> | discoveredsecuritysolutions | 예 | 아니요 |
> | externalsecuritysolutions | 예 | 아니요 |
> | informationprotectionpolicies | 예 | 아니요 |
> | iotsecuritysolutions | 예 | 예 |
> | iotsecuritysolutions / analyticsmodels | 예 | 아니요 |
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | 예 | 아니요 |
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | 예 | 아니요 |
> | jitnetworkaccesspolicies | 예 | 아니요 |
> | 위치 | 예 | 아니요 |
> | locations / alerts | 예 | 아니요 |
> | locations / allowedconnections | 예 | 아니요 |
> | locations / applicationwhitelistings | 예 | 아니요 |
> | locations / discoveredsecuritysolutions | 예 | 아니요 |
> | locations / externalsecuritysolutions | 예 | 아니요 |
> | locations / jitnetworkaccesspolicies | 예 | 아니요 |
> | locations / securitysolutions | 예 | 아니요 |
> | locations / securitysolutionsreferencedata | 예 | 아니요 |
> | locations / tasks | 예 | 아니요 |
> | locations / topologies | 예 | 아니요 |
> | 작업 | 예 | 아니요 |
> | 정책 | 예 | 아니요 |
> | pricings | 예 | 아니요 |
> | regulatorycompliancestandards | 예 | 아니요 |
> | regulatorycompliancestandards / regulatorycompliancecontrols | 예 | 아니요 |
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | 예 | 아니요 |
> | securitycontacts | 예 | 아니요 |
> | securitysolutions | 예 | 아니요 |
> | securitysolutionsreferencedata | 예 | 아니요 |
> | securitystatuses | 예 | 아니요 |
> | securitystatusessummaries | 예 | 아니요 |
> | servervulnerabilityassessments | 예 | 아니요 |
> | 설정 | 아니요 | 아니요 |
> | subassessments | 예 | 예 |
> | tasks | 예 | 예 |
> | topologies | 예 | 예 |
> | workspacesettings | 예 | 예 |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | aggregations | 예 | 아니요 |
> | alertrules | 예 | 아니요 |
> | alertruletemplates | 예 | 아니요 |
> | automationrules | 예 | 아니요 |
> | bookmarks | 예 | 아니요 |
> | cases | 예 | 아니요 |
> | dataconnectors | 예 | 아니요 |
> | dataconnectorscheckrequirements | 예 | 아니요 |
> | 엔터티 | 예 | 아니요 |
> | entityqueries | 아니요 | 아니요 |
> | incidents | 예 | 예 |
> | officeconsents | 예 | 예 |
> | 작업 | 예 | 예 |
> | 설정 | 예 | 예 |
> | threatintelligence | 아니요 | 아니요 |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | consoleservices | 예 | 예 |
> | 위치 | 아니요 | 아니요 |
> | locations / consoleservices | 아니요 | 예 |
> | 작업 | 아니요 | 예 |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | gateways | 예 | 예 |
> | nodes | 아니요 | 아니요 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 예 | 아니요 |
> | checknamespaceavailability | 예 | 아니요 |
> | 위치 | 예 | 아니요 |
> | locations / deletevirtualnetworkorsubnets | 예 | 아니요 |
> | 네임스페이스 | 예 | 예 |
> | namespaces / authorizationrules | 예 | 아니요 |
> | namespaces / disasterrecoveryconfigs | 예 | 아니요 |
> | namespaces / disasterrecoveryconfigs / checknameavailability | 예 | 아니요 |
> | namespaces / eventgridfilters | 예 | 아니요 |
> | namespaces / networkrulesets | 예 | 아니요 |
> | namespaces / queues | 예 | 아니요 |
> | namespaces / queues / authorizationrules | 예 | 아니요 |
> | namespaces / topics | 예 | 아니요 |
> | namespaces / topics / authorizationrules | 아니요 | 아니요 |
> | namespaces / topics / subscriptions | 예 | 예 |
> | namespaces / topics / subscriptions / rules | 아니요 | 아니요 |
> | 작업 | 예 | 예 |
> | premiummessagingregions | 예 | 예 |
> | sku | 예 | 예 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 애플리케이션 | 예 | 아니요 |
> | clusters | 예 | 예 |
> | clusters / applications | 예 | 아니요 |
> | containergroups | 예 | 아니요 |
> | containergroupsets | 예 | 아니요 |
> | edgeclusters | 예 | 아니요 |
> | 위치 | 예 | 아니요 |
> | locations / clusterversions | 예 | 아니요 |
> | locations / environments | 예 | 아니요 |
> | locations / operationresults | 예 | 아니요 |
> | locations / operations | 아니요 | 아니요 |
> | managedclusters | 아니요 | 아니요 |
> | networks | 예 | 예 |
> | 작업 | 아니요 | 아니요 |
> | secretstores | 예 | 예 |
> | volumes | 예 | 예 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 애플리케이션 | 예 | 예 |
> | containergroups | 예 | 아니요 |
> | gateways | 예 | 예 |
> | 위치 | 아니요 | 아니요 |
> | locations / applicationoperations | 예 | 아니요 |
> | locations / gatewayoperations | 아니요 | 아니요 |
> | locations / networkoperations | 아니요 | 아니요 |
> | locations / secretoperations | 아니요 | 아니요 |
> | locations / volumeoperations | 예 | 예 |
> | networks | 예 | 예 |
> | 작업 | 예 | 예 |
> | secrets | 예 | 예 |
> | volumes | 예 | 예 |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | rollouts | 예 | 예 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 위치 | 예 | 아니요 |
> | locations / checknameavailability | 예 | 아니요 |
> | locations / operationresults | 아니요 | 아니요 |
> | locations / operationstatuses | 예 | 아니요 |
> | locations / usages | 아니요 | 아니요 |
> | 작업 | 예 | 예 |
> | signalr | 예 | 예 |
> | signalr / eventgridfilters | 아니요 | 아니요 |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hybridusebenefits | 예 | 예 |
> | 작업 | 예 | 예 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | 예 | 아니요 |
> | 애플리케이션 | 예 | 아니요 |
> | jitrequests | 아니요 | 아니요 |
> | 위치 | 예 | 예 |
> | locations / operationstatuses | 예 | 예 |
> | 작업 | 아니요 | 아니요 |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!IMPORTANT]
> 데이터베이스와 서버는 동일한 리소스 그룹에 있어야 합니다. SQL Server를 이동하면 모든 해당 데이터베이스도 함께 이동합니다. 이 동작은 Azure SQL Database 및 Azure Synapse Analytics 데이터베이스에 적용 됩니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 예 | 아니요 |
> | instancepools | 예 | 아니요 |
> | 위치 | 예 | 예 |
> | locations / administratorazureasyncoperation | 예 | 아니요 |
> | locations / administratoroperationresults | 예 | 아니요 |
> | locations / auditingsettingsazureasyncoperation | 예 | 아니요 |
> | locations / auditingsettingsoperationresults | 예 | 아니요 |
> | locations / capabilities | 예 | 아니요 |
> | locations / databaseazureasyncoperation | 예 | 아니요 |
> | locations / databaseoperationresults | 예 | 아니요 |
> | locations / databaserestoreazureasyncoperation | 예 | 아니요 |
> | locations / deletevirtualnetworkorsubnets | 예 | 아니요 |
> | locations / deletevirtualnetworkorsubnetsazureasyncoperation | 예 | 아니요 |
> | locations / deletevirtualnetworkorsubnetsoperationresults | 예 | 아니요 |
> | locations / dnsaliasasyncoperation | 예 | 아니요 |
> | locations / dnsaliasoperationresults | 예 | 아니요 |
> | locations / elasticpoolazureasyncoperation | 예 | 아니요 |
> | locations / elasticpooloperationresults | 예 | 아니요 |
> | locations / encryptionprotectorazureasyncoperation | 예 | 아니요 |
> | locations / encryptionprotectoroperationresults | 예 | 아니요 |
> | locations / extendedauditingsettingsazureasyncoperation | 예 | 아니요 |
> | locations / extendedauditingsettingsoperationresults | 예 | 아니요 |
> | locations / failovergroupazureasyncoperation | 예 | 아니요 |
> | locations / failovergroupoperationresults | 예 | 아니요 |
> | locations / firewallrulesazureasyncoperation | 예 | 아니요 |
> | locations / firewallrulesoperationresults | 예 | 아니요 |
> | locations / instancefailovergroupazureasyncoperation | 예 | 아니요 |
> | locations / instancefailovergroupoperationresults | 예 | 아니요 |
> | locations / instancefailovergroups | 예 | 아니요 |
> | locations / instancepoolazureasyncoperation | 예 | 아니요 |
> | locations / instancepooloperationresults | 예 | 아니요 |
> | locations / jobagentazureasyncoperation | 예 | 아니요 |
> | locations / jobagentoperationresults | 예 | 아니요 |
> | locations / longtermretentionbackupazureasyncoperation | 예 | 아니요 |
> | locations / longtermretentionbackupoperationresults | 예 | 아니요 |
> | locations / longtermretentionbackups | 예 | 아니요 |
> | locations / longtermretentionmanagedinstancebackupazureasyncoperation | 예 | 아니요 |
> | locations / longtermretentionmanagedinstancebackupoperationresults | 예 | 아니요 |
> | locations / longtermretentionmanagedinstancebackups | 예 | 아니요 |
> | locations / longtermretentionmanagedinstances | 예 | 아니요 |
> | locations / longtermretentionpolicyazureasyncoperation | 예 | 아니요 |
> | locations / longtermretentionpolicyoperationresults | 예 | 아니요 |
> | locations / longtermretentionservers | 예 | 아니요 |
> | locations / manageddatabaseazureasyncoperation | 예 | 아니요 |
> | locations / manageddatabasecompleterestoreazureasyncoperation | 예 | 아니요 |
> | locations / manageddatabasecompleterestoreoperationresults | 예 | 예 |
> | locations / manageddatabaseoperationresults | 예 | 아니요 |
> | locations / manageddatabaserestoreazureasyncoperation | 예 | 아니요 |
> | locations / manageddatabaserestoreoperationresults | 예 | 아니요 |
> | locations / managedinstanceazureasyncoperation | 예 | 아니요 |
> | locations / managedinstanceencryptionprotectorazureasyncoperation | 예 | 아니요 |
> | locations / managedinstanceencryptionprotectoroperationresults | 예 | 아니요 |
> | locations / managedinstancekeyazureasyncoperation | 예 | 아니요 |
> | locations / managedinstancekeyoperationresults | 예 | 아니요 |
> | locations / managedinstancelongtermretentionpolicyazureasyncoperation | 예 | 아니요 |
> | locations / managedinstancelongtermretentionpolicyoperationresults | 예 | 아니요 |
> | locations / managedinstanceoperationresults | 예 | 아니요 |
> | locations / managedinstancetdecertazureasyncoperation | 예 | 아니요 |
> | locations / managedinstancetdecertoperationresults | 예 | 아니요 |
> | locations / managedserversecurityalertpoliciesazureasyncoperation | 예 | 아니요 |
> | locations / managedserversecurityalertpoliciesoperationresults | 예 | 아니요 |
> | locations / managedshorttermretentionpolicyazureasyncoperation | 예 | 아니요 |
> | locations / managedshorttermretentionpolicyoperationresults | 예 | 아니요 |
> | locations / notifyazureasyncoperation | 예 | 아니요 |
> | locations / privateendpointconnectionazureasyncoperation | 예 | 아니요 |
> | locations / privateendpointconnectionoperationresults | 예 | 아니요 |
> | locations / privateendpointconnectionproxyazureasyncoperation | 예 | 아니요 |
> | locations / privateendpointconnectionproxyoperationresults | 예 | 아니요 |
> | locations / securityalertpoliciesazureasyncoperation | 예 | 아니요 |
> | locations / securityalertpoliciesoperationresults | 예 | 아니요 |
> | locations / serveradministratorazureasyncoperation | 예 | 아니요 |
> | locations / serveradministratoroperationresults | 예 | 아니요 |
> | locations / serverazureasyncoperation | 예 | 아니요 |
> | locations / serverkeyazureasyncoperation | 예 | 아니요 |
> | locations / serverkeyoperationresults | 예 | 아니요 |
> | locations / serveroperationresults | 예 | 아니요 |
> | locations / shorttermretentionpolicyazureasyncoperation | 예 | 아니요 |
> | locations / shorttermretentionpolicyoperationresults | 예 | 아니요 |
> | locations / syncagentoperationresults | 예 | 아니요 |
> | locations / syncdatabaseids | 예 | 아니요 |
> | locations / syncgroupoperationresults | 예 | 아니요 |
> | locations / syncmemberoperationresults | 예 | 아니요 |
> | locations / tdecertazureasyncoperation | 예 | 아니요 |
> | locations / tdecertoperationresults | 예 | 아니요 |
> | locations / usages | 예 | 아니요 |
> | locations / virtualclusterazureasyncoperation | 예 | 아니요 |
> | locations / virtualclusteroperationresults | 예 | 아니요 |
> | locations / virtualnetworkrulesazureasyncoperation | 예 | 아니요 |
> | locations / virtualnetworkrulesoperationresults | 예 | 아니요 |
> | locations / vulnerabilityassessmentscanazureasyncoperation | 예 | 아니요 |
> | locations / vulnerabilityassessmentscanoperationresults | 예 | 아니요 |
> | managedinstances | 예 | 아니요 |
> | managedinstances / administrators | 예 | 아니요 |
> | managedinstances / databases | 예 | 아니요 |
> | managedinstances / databases / backuplongtermretentionpolicies | 예 | 아니요 |
> | managedinstances / databases / vulnerabilityassessments | 예 | 예 |
> | managedinstances / metricdefinitions | 예 | 아니요 |
> | managedinstances / metrics | 예 | 아니요 |
> | managedinstances / recoverabledatabases | 아니요 | 아니요 |
> | managedinstances / tdecertificates | 예 | 아니요 |
> | managedinstances / vulnerabilityassessments | 예 | 아니요 |
> | 작업 | 예 | 아니요 |
> | servers | 예 | 예 |
> | servers / administratoroperationresults | 예 | 아니요 |
> | servers / administrators | 예 | 아니요 |
> | servers / advisors | 예 | 아니요 |
> | servers / aggregateddatabasemetrics | 예 | 아니요 |
> | servers / auditingpolicies | 예 | 아니요 |
> | servers / auditingsettings | 아니요 | 아니요 |
> | servers / automatictuning | 예 | 아니요 |
> | servers / communicationlinks | 예 | 아니요 |
> | servers / connectionpolicies | 예 | 아니요 |
> | servers / databases | 예 | 예 |
> | servers / databases / advisors | 예 | 아니요 |
> | servers / databases / auditingpolicies | 예 | 아니요 |
> | servers / databases / auditingsettings | 예 | 아니요 |
> | servers / databases / auditrecords | 예 | 아니요 |
> | servers / databases / automatictuning | 예 | 아니요 |
> | servers / databases / backuplongtermretentionpolicies | 예 | 아니요 |
> | servers / databases / backupshorttermretentionpolicies | 예 | 아니요 |
> | servers / databases / connectionpolicies | 예 | 아니요 |
> | servers / databases / datamaskingpolicies | 예 | 아니요 |
> | servers / databases / datamaskingpolicies / rules | 예 | 아니요 |
> | servers / databases / extensions | 예 | 아니요 |
> | servers / databases / geobackuppolicies | 예 | 아니요 |
> | servers / databases / metricdefinitions | 예 | 아니요 |
> | servers / databases / metrics | 예 | 아니요 |
> | servers / databases / recommendedsensitivitylabels | 예 | 아니요 |
> | servers / databases / securityalertpolicies | 예 | 아니요 |
> | servers / databases / syncgroups | 예 | 아니요 |
> | servers / databases / syncgroups / syncmembers | 예 | 아니요 |
> | servers / databases / topqueries | 예 | 아니요 |
> | servers / databases / topqueries / querytext | 예 | 아니요 |
> | servers / databases / transparentdataencryption | 예 | 아니요 |
> | servers / databases / vulnerabilityassessment | 예 | 아니요 |
> | servers / databases / vulnerabilityassessments | 예 | 아니요 |
> | servers / databases / vulnerabilityassessmentscans | 예 | 아니요 |
> | servers / databases / vulnerabilityassessmentsettings | 예 | 아니요 |
> | servers / databases / workloadgroups | 예 | 아니요 |
> | servers / databasesecuritypolicies | 아니요 | 아니요 |
> | servers / disasterrecoveryconfiguration | 예 | 아니요 |
> | servers / dnsaliases | 예 | 아니요 |
> | servers / elasticpoolestimates | 예 | 아니요 |
> | servers / elasticpools | 예 | 예 |
> | servers / elasticpools / advisors | 예 | 아니요 |
> | servers / elasticpools / metricdefinitions | 예 | 아니요 |
> | servers / elasticpools / metrics | 예 | 아니요 |
> | servers / encryptionprotector | 예 | 아니요 |
> | servers / extendedauditingsettings | 아니요 | 아니요 |
> | servers / failovergroups | 아니요 | 아니요 |
> | servers / import | 예 | 아니요 |
> | servers / importexportoperationresults | 예 | 아니요 |
> | servers / jobaccounts | 예 | 예 |
> | servers / jobagents | 예 | 예 |
> | servers / jobagents / jobs | 예 | 아니요 |
> | servers / jobagents / jobs / executions | 예 | 아니요 |
> | servers / jobagents / jobs / steps | 예 | 아니요 |
> | servers / keys | 예 | 아니요 |
> | servers / operationresults | 예 | 아니요 |
> | servers / recommendedelasticpools | 예 | 아니요 |
> | servers / recoverabledatabases | 예 | 아니요 |
> | servers / restorabledroppeddatabases | 예 | 아니요 |
> | servers / securityalertpolicies | 예 | 아니요 |
> | servers / serviceobjectives | 예 | 아니요 |
> | servers / syncagents | 예 | 아니요 |
> | servers / tdecertificates | 아니요 | 아니요 |
> | servers / usages | 아니요 | 아니요 |
> | servers / virtualnetworkrules | 아니요 | 아니요 |
> | servers / vulnerabilityassessments | 예 | 예 |
> | virtualclusters | 예 | 예 |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 위치 | 예 | 아니요 |
> | locations / availabilitygrouplisteneroperationresults | 예 | 아니요 |
> | locations / operationtypes | 예 | 아니요 |
> | locations / sqlvirtualmachinegroupoperationresults | 아니요 | 아니요 |
> | locations / sqlvirtualmachineoperationresults | 예 | 아니요 |
> | 작업 | 아니요 | 아니요 |
> | sqlvirtualmachinegroups | 예 | 예 |
> | sqlvirtualmachinegroups / availabilitygrouplisteners | 예 | 예 |
> | sqlvirtualmachines | 예 | 예 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 예 | 예 |
> | 위치 | 예 | 예 |
> | locations / asyncoperations | 예 | 예 |
> | locations / checknameavailability | 예 | 예 |
> | locations / deletevirtualnetworkorsubnets | 예 | 예 |
> | locations / usages | 예 | 예 |
> | 작업 | 아니요 | 아니요 |
> | storageaccounts | 예 | 예 |
> | storageaccounts / blobservices | 예 | 예 |
> | storageaccounts / fileservices | 예 | 예 |
> | storageaccounts / listaccountsas | 예 | 예 |
> | storageaccounts / listservicesas | 예 | 예 |
> | storageaccounts / queueservices | 예 | 예 |
> | storageaccounts / services | 예 | 예 |
> | storageaccounts / services / metricdefinitions | 예 | 예 |
> | storageaccounts / tableservices | 예 | 예 |
> | usages | 아니요 | 아니요 |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | caches | 아니요 | 아니요 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 위치 | 예 | 예 |
> | locations / checknameavailability | 예 | 예 |
> | locations / operationresults | 예 | 예 |
> | locations / operations | 아니요 | 아니요 |
> | locations / workflows | 예 | 예 |
> | 작업 | 예 | 예 |
> | storagesyncservices | 예 | 예 |
> | storagesyncservices / registeredservers | 예 | 예 |
> | storagesyncservices / syncgroups | 예 | 예 |
> | storagesyncservices / syncgroups / cloudendpoints | 아니요 | 아니요 |
> | storagesyncservices / syncgroups / serverendpoints | 아니요 | 예 |
> | storagesyncservices / workflows | 아니요 | 아니요 |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | 아니요 | 아니요 |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | 아니요 | 예 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managers | 아니요 | 예 |
> | 작업 | 아니요 | 예 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!IMPORTANT]
> 실행 중 상태인 Stream Analytics 작업은 이동할 수 없습니다.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | 예 | 아니요 |
> | 위치 | 아니요 | 아니요 |
> | locations / quotas | 아니요 | 아니요 |
> | 작업 | 아니요 | 예 |
> | streamingjobs | 예 | 예 |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | 예 | 예 |
> | environments / eventsources | 예 | 예 |
> | 인스턴스 | 아니요 | 아니요 |
> | instances / environments | 아니요 | 예 |
> | instances / environments / eventsources | 아니요 | 예 |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | cancel | 예 | 예 |
> | createsubscription | 예 | 예 |
> | enable | 예 | 예 |
> | operationresults | 예 | 예 |
> | 작업 | 예 | 예 |
> | 이름 바꾸기 | 예 | 예 |
> | subscriptiondefinitions | 아니요 | 아니요 |
> | subscriptionoperations | 아니요 | 예 |
> | 구독 | 아니요 | 예 |

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 예 | 예 |
> | operationresults | 예 | 예 |
> | 작업 | 예 | 예 |
> | operationsstatus | 예 | 예 |
> | services | 아니요 | 아니요 |
> | services / problemclassifications | 아니요 | 예 |
> | supporttickets | 아니요 | 예 |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 아니요 | 아니요 |
> | 작업 | 예 | 예 |
> | workspaces | 예 | 예 |
> | workspaces / bigdatapools | 예 | 예 |
> | workspaces / operationresults | 아니요 | 아니요 |
> | workspaces / operationstatuses | 아니요 | 예 |
> | workspaces / sqlpools | 예 | 예 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | 예 | 예 |
> | environments / accesspolicies | 아니요 | 예 |
> | environments / eventsources | 예 | 예 |
> | environments / referencedatasets | 예 | 예 |
> | 작업 | 아니요 | 예 |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | stores | 예 | 예 |
> | stores / accesspolicies | 아니요 | 예 |
> | stores / services | 아니요 | 예 |
> | stores / services / tokens | 아니요 | 예 |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | imagetemplates | 아니요 | 예 |
> | imagetemplates/runoutputs | 아니요 | 예 |
> | 위치 | 아니요 | 아니요 |
> | locations / operations | 아니요 | 아니요 |
> | 작업 | 예 | 아니요 |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!IMPORTANT]
> Azure DevOps 구독을 변경하려면 [청구에 사용되는 Azure 구독 변경](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)을 참조하세요.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | 예 | 예 |
> | account / extension | 예 | 예 |
> | account / project | 아니요 | 아니요 |
> | checknameavailability | 아니요 | 아니요 |
> | 작업 | 예 | 아니요 |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | arczones | 아니요 | 예 |
> | 위치 | 아니요 | 예 |
> | locations / operationstatuses | 아니요 | 예 |
> | 작업 | 아니요 | 예 |
> | resourcepools | 아니요 | 예 |
> | vcenters | 아니요 | 예 |
> | virtualmachines | 아니요 | 아니요 |
> | virtualmachinetemplates | 아니요 | 아니요 |
> | virtualnetworks | 예 | 아니요 |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | 아니요 | 예 |
> | dedicatedcloudservices | 아니요 | 예 |
> | 위치 | 아니요 | 예 |
> | locations / availabilities | 아니요 | 예 |
> | locations / operationresults | 아니요 | 예 |
> | locations / privateclouds | 아니요 | 예 |
> | locations / privateclouds / resourcepools | 아니요 | 예 |
> | locations / privateclouds / virtualmachinetemplates | 아니요 | 예 |
> | locations / privateclouds / virtualnetworks | 아니요 | 예 |
> | locations / usages | 아니요 | 아니요 |
> | 작업 | 아니요 | 아니요 |
> | virtualmachines | 예 | 아니요 |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | devices | 아니요 | 예 |
> | 위치 | 아니요 | 예 |
> | locations / operationstatuses | 아니요 | 아니요 |
> | 작업 | 아니요 | 아니요 |
> | vnfs | 예 | 아니요 |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아니요 | 아니요 |
> | 작업 | 아니요 | 아니요 |
> | 플랜 | 예 | 아니요 |
> | registeredsubscriptions | 예 | 아니요 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!IMPORTANT]
> [App Service 이동 지침](./move-limitations/app-service-move-limitations.md)을 참조하세요.

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availablestacks | 아니요 | 아니요 |
> | billingmeters | 아니요 | 아니요 |
> | certificates | 아니요 | 예 |
> | checknameavailability | 예 | 예 |
> | connectiongateways | 예 | 예 |
> | connections | 예 | 예 |
> | customapis | 예 | 예 |
> | deletedsites | 예 | 예 |
> | deploymentlocations | 예 | 예 |
> | georegions | 예 | 예 |
> | hostingenvironments | 예 | 예 |
> | hostingenvironments / eventgridfilters | 예 | 예 |
> | hostingenvironments / multirolepools | 예 | 예 |
> | hostingenvironments / workerpools | 아니요 | 아니요 |
> | ishostingenvironmentnameavailable | 예 | 예 |
> | ishostnameavailable | 예 | 예 |
> | isusernameavailable | 예 | 예 |
> | kubeenvironments | 예 | 예 |
> | listsitesassignedtohostname | 예 | 예 |
> | 위치 | 예 | 예 |
> | locations / apioperations | 예 | 예 |
> | locations / connectiongatewayinstallations | 예 | 예 |
> | locations / deletedsites | 예 | 예 |
> | locations / deletevirtualnetworkorsubnets | 예 | 예 |
> | locations / extractapidefinitionfromwsdl | 예 | 예 |
> | locations / getnetworkpolicies | 예 | 예 |
> | locations / listwsdlinterfaces | 예 | 예 |
> | locations / managedapis | 예 | 예 |
> | locations / operationresults | 예 | 예 |
> | locations / operations | 예 | 예 |
> | locations / runtimes | 예 | 예 |
> | 작업 | 예 | 예 |
> | publishingusers | 아니요 | 아니요 |
> | 동영상 추천 기능 | 예 | 예 |
> | resourcehealthmetadata | 아니요 | 아니요 |
> | runtimes | 예 | 예 |
> | serverfarms | 예 | 예 |
> | serverfarms / eventgridfilters | 예 | 예 |
> | sites | 예 | 예 |
> | sites / eventgridfilters | 아니요 | 아니요 |
> | sites / hostnamebindings | 예 | 예 |
> | sites / networkconfig | 예 | 예 |
> | sites / premieraddons | 예 | 예 |
> | sites / slots | 예 | 예 |
> | sites / slots / eventgridfilters | 예 | 예 |
> | sites / slots / hostnamebindings | 예 | 예 |
> | sites / slots / networkconfig | 예 | 예 |
> | sourcecontrols | 아니요 | 아니요 |
> | staticsites | 아니요 | 아니요 |
> | validate | 예 | 아니요 |
> | verifyhostingenvironmentvnet | 예 | 아니요 |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 위치 | 아니요 | 아니요 |
> | locations / operationstatuses | 아니요 | 아니요 |
> | multipleactivationkeys | 예 | 아니요 |
> | 작업 | 예 | 아니요 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | deviceservices | 예 | 아니요 |
> | 작업 | 예 | 아니요 |

## <a name="microsoftworkloadbuilder"></a>WorkloadBuilder

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 위치 | 아니요 | 예 |
> | locations / operationstatuses | 아니요 | 예 |
> | 작업 | 아니요 | 예 |
> | 작업 | 아니요 | 아니요 |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | components | 아니요 | 아니요 |
> | componentssummary | 아니요 | 아니요 |
> | monitorinstances | 아니요 | 아니요 |
> | monitorinstancessummary | 아니요 | 아니요 |
> | monitors | 아니요 | 아니요 |
> | notificationsettings | 아니요 | 아니요 |
> | 작업 | 아니요 | 아니요 |

## <a name="third-party-services"></a>타사 서비스

타사 서비스는 현재 이동 작업을 지원하지 않습니다.

## <a name="next-steps"></a>다음 단계
리소스를 이동하는 명령은 [새 리소스 그룹 또는 구독으로 리소스 이동](move-resource-group-and-subscription.md)을 참조하세요.

쉼표로 구분된 값 파일과 동일한 데이터를 가져오려면 [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv) 파일을 다운로드합니다.
