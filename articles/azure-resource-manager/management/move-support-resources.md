---
title: 리소스 종류별 이동 작업 지원
description: 새 리소스 그룹이나 구독으로 이동할 수 있는 Azure 리소스 종류를 소개합니다.
ms.topic: conceptual
ms.date: 07/06/2020
ms.openlocfilehash: 7f241e12200101e2f8f9efa7cf31e4483b2d4229
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86044535"
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
> - [MICROSOFT. APPSERVICE](#microsoftappservice)
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
> - [BIZTALKSERVICES](#microsoftbiztalkservices)
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
> - [ConnectedCache](#microsoftconnectedcache)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [MICROSOFT. CONTENTMODERATOR](#microsoftcontentmoderator)
> - [MICROSOFT. CORTANAANALYTICS](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CostManagementExports](#microsoftcostmanagementexports)
> - [MICROSOFT. CUSTOMERINSIGHTS](#microsoftcustomerinsights)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [MICROSOFT. DATACONNECT](#microsoftdataconnect)
> - [MICROSOFT. DATAEXCHANGE](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [MICROSOFT. DATALAKE](#microsoftdatalake)
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
> - [MICROSOFT. GENOMICS](#microsoftgenomics)
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
> - [MICROSOFT. LOCATIONBASEDSERVICES](#microsoftlocationbasedservices)
> - [MICROSOFT. LOCATIONSERVICES.LOG](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [MICROSOFT. MACHINELEARNINGCOMPUTE](#microsoftmachinelearningcompute)
> - [MICROSOFT. MICROSOFT.MACHINELEARNINGEXPERIMENTATION](#microsoftmachinelearningexperimentation)
> - [MICROSOFT. MACHINELEARNINGMODELMANAGEMENT](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Maintenance](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [MICROSOFT. MANAGEDNETWORK](#microsoftmanagednetwork)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [MICROSOFT. MICROSERVICES4SPRING](#microsoftmicroservices4spring)
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
> - [MICROSOFT. SERVERMANAGEMENT](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [MICROSOFT. 서비스](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [MICROSOFT. STORAGECACHE](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [MICROSOFT. STORAGESYNCDEV](#microsoftstoragesyncdev)
> - [MICROSOFT. STORAGESYNCINT](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [MICROSOFT. STREAMANALYTICSEXPLORER](#microsoftstreamanalyticsexplorer)
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
> | domainservices / oucontainer | 아니요 | 예 |
> | 위치 | 아니요 | 예 |
> | locations / operationresults | 아니요 | 예 |
> | 작업 | 아니요 | 예 |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | azureadmetrics | 아니요 | 아니요 |
> | diagnosticsettings | 아니요 | 예 |
> | diagnosticsettingscategories | 아니요 | 예 |
> | 작업 | 아니요 | 아니요 |
> | privatelinkforazuread | 아니요 | 아니요 |
> | tenants | 예 | 아니요 |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | operationresults | 아니요 | 아니요 |
> | 작업 | 아니요 | 아니요 |
> | supportproviders | 예 | 아니요 |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | aadsupportcases | 아니요 | 예 |
> | addsservices | 아니요 | 예 |
> | agents | 아니요 | 예 |
> | anonymousapiusers | 아니요 | 예 |
> | 구성 | 아니요 | 예 |
> | 로그 | 아니요 | 예 |
> | 작업 | 아니요 | 예 |
> | reports | 아니요 | 아니요 |
> | servicehealthmetrics | 아니요 | 아니요 |
> | services | 예 | 아니요 |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 구성 | 아니요 | 예 |
> | generaterecommendations | 아니요 | 예 |
> | metadata | 아니요 | 예 |
> | 작업 | 아니요 | 아니요 |
> | 동영상 추천 기능 | 아니요 | 아니요 |
> | suppressions | 아니요 | 아니요 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | actionrules | 아니요 | 예 |
> | 경고 | 아니요 | 예 |
> | alertslist | 아니요 | 예 |
> | alertsmetadata | 아니요 | 예 |
> | alertssummary | 아니요 | 아니요 |
> | alertssummarylist | 아니요 | 예 |
> | 작업 | 아니요 | 아니요 |
> | smartdetectoralertrules | 아니요 | 아니요 |
> | smartgroups | 예 | 아니요 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 위치 | 아니요 | 예 |
> | locations / checknameavailability | 아니요 | 예 |
> | locations / operationresults | 아니요 | 아니요 |
> | locations / operationstatuses | 아니요 | 아니요 |
> | 작업 | 아니요 | 아니요 |
> | servers | 예 | 아니요 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checkfeedbackrequired | 아니요 | 예 |
> | checknameavailability | 아니요 | 예 |
> | checkservicenameavailability | 아니요 | 아니요 |
> | 작업 | 아니요 | 예 |
> | reportfeedback | 아니요 | 아니요 |
> | 서비스 | 예 | 예 |
> | validateservicename | 예 | 예 |

> [!IMPORTANT]
> 소비 SKU로 설정된 API Management 서비스는 이동할 수 없습니다.

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 아니요 | 예 |
> | configurationstores | 아니요 | 예 |
> | configurationstores / eventgridfilters | 아니요 | 예 |
> | 위치 | 아니요 | 아니요 |
> | locations / operationsstatus | 아니요 | 아니요 |
> | 작업 | 예 | 아니요 |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 위치 | 아니요 | 예 |
> | locations / checknameavailability | 아니요 | 예 |
> | locations / operationresults | 아니요 | 아니요 |
> | locations / operationstatus | 아니요 | 아니요 |
> | 작업 | 아니요 | 아니요 |
> | spring | 예 | 아니요 |
> | 스프링/앱 | 예 | 아니요 |
> | 스프링/앱/배포 | 예 | 아니요 |

## <a name="microsoftappservice"></a>MICROSOFT. APPSERVICE

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | apiapps | 아니요 | 아니요 |
> | appidentities | 예 | 예 |
> | gateways | 아니요 | 아니요 |

> [!IMPORTANT]
> [App Service 이동 지침](./move-limitations/app-service-move-limitations.md)을 참조하세요.

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | attestationproviders | 예 | 아니요 |
> | defaultproviders | 예 | 아니요 |
> | 위치 | 예 | 아니요 |
> | 위치/defaultprovider | 예 | 아니요 |
> | 작업 | 예 | 아니요 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checkaccess | 아니요 | 예 |
> | classicadministrators | 아니요 | 예 |
> | dataaliases | 아니요 | 예 |
> | denyassignments | 아니요 | 예 |
> | elevateaccess | 아니요 | 예 |
> | findorphanroleassignments | 아니요 | 예 |
> | locks | 아니요 | 예 |
> | 작업 | 아니요 | 예 |
> | operationstatus | 아니요 | 아니요 |
> | 권한 | 아니요 | 아니요 |
> | policyassignments | 아니요 | 아니요 |
> | policydefinitions | 아니요 | 아니요 |
> | policysetdefinitions | 예 | 아니요 |
> | privatelinkassociations | 예 | 아니요 |
> | provideroperations | 예 | 아니요 |
> | resourcemanagementprivatelinks | 예 | 아니요 |
> | roleassignments | 아니요 | 아니요 |
> | roleassignmentsusagemetrics | 예 | 아니요 |
> | roledefinitions | 예 | 아니요 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | automationaccounts | 예 | 예 |
> | automationaccounts / configurations | 예 | 예 |
> | automationaccounts / jobs | 예 | 예 |
> | automationaccounts / privateendpointconnectionproxies | 예 | 예 |
> | automationaccounts / privateendpointconnections | 아니요 | 아니요 |
> | automationaccounts / privatelinkresources | 예 | 예 |
> | automationaccounts / runbooks | 아니요 | 아니요 |
> | automationaccounts / softwareupdateconfigurations | 아니요 | 아니요 |
> | automationaccounts / webhooks | 아니요 | 예 |
> | 작업 | 아니요 | 예 |

> [!IMPORTANT]
> Runbook은 Automation 계정과 동일한 리소스 그룹에 있어야 합니다.
>
> 자세한 내용은 [Azure Automation 계정을 다른 구독으로 이동](../../automation/how-to/move-account.md?toc=/azure/azure-resource-manager/toc.json)을 참조 하세요.

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 위치 | 아니요 | 아니요 |
> | locations / checkquotaavailability | 예 | 아니요 |
> | 위치/checktrialavailability | 예 | 아니요 |
> | 작업 | 예 | 아니요 |
> | privateclouds | 예 | 아니요 |
> | privateclouds / clusters | 예 | 아니요 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | 예 | 예 |
> | b2ctenants | 아니요 | 예 |
> | checknameavailability | 아니요 | 아니요 |
> | guestusages | 예 | 예 |
> | 작업 | 예 | 아니요 |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datacontrollers | 예 | 예 |
> | hybriddatamanagers | 예 | 예 |
> | 작업 | 아니요 | 아니요 |
> | postgresinstances | 예 | 예 |
> | sqlinstances | 예 | 예 |
> | sqlmanagedinstances | 예 | 예 |
> | sqlserverinstances | 예 | 예 |
> | sqlserverregistrations | 예 | 예 |
> | sqlserverregistrations / sqlservers | 예 | 예 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | cloudmanifestfiles | 예 | 아니요 |
> | edgesubscriptions | 예 | 아니요 |
> | 작업 | 예 | 아니요 |
> | registrations | 아니요 | 아니요 |
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
> | 위치 | 아니요 | 예 |
> | locations / accountoperationresults | 아니요 | 아니요 |
> | locations / checknameavailability | 아니요 | 아니요 |
> | locations / quotas | 예 | 아니요 |
> | 작업 | 예 | 아니요 |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | billingaccounts | 아니요 | 예 |
> | billingaccounts / agreements | 아니요 | 예 |
> | billingaccounts / billingpermissions | 아니요 | 예 |
> | billingaccounts / billingprofiles | 아니요 | 예 |
> | billingaccounts / billingprofiles / availablebalance | 아니요 | 예 |
> | billingaccounts / billingprofiles / billingpermissions | 아니요 | 예 |
> | billingaccounts / billingprofiles / billingroleassignments | 아니요 | 예 |
> | billingaccounts / billingprofiles / billingroledefinitions | 아니요 | 예 |
> | billingaccounts / billingprofiles / billingsubscriptions | 아니요 | 예 |
> | billingaccounts / billingprofiles / createbillingroleassignment | 아니요 | 예 |
> | billingaccounts / billingprofiles / customers | 아니요 | 예 |
> | billingaccounts / billingprofiles / instructions | 아니요 | 예 |
> | billingaccounts / billingprofiles / invoices | 아니요 | 예 |
> | billingaccounts / billingprofiles / invoices / pricesheet | 아니요 | 예 |
> | billingaccounts / billingprofiles / invoices / transactions | 아니요 | 예 |
> | billingaccounts / billingprofiles / invoicesections | 아니요 | 예 |
> | billingaccounts / billingprofiles / invoicesections / billingpermissions | 아니요 | 예 |
> | billingaccounts / billingprofiles / invoicesections / billingroleassignments | 아니요 | 예 |
> | billingaccounts / billingprofiles / invoicesections / billingroledefinitions | 아니요 | 예 |
> | billingaccounts / billingprofiles / invoicesections / billingsubscriptions | 아니요 | 예 |
> | billingaccounts / billingprofiles / invoicesections / createbillingroleassignment | 아니요 | 예 |
> | billingaccounts / billingprofiles / invoicesections / initiatetransfer | 아니요 | 예 |
> | billingaccounts / billingprofiles / invoicesections / products | 아니요 | 예 |
> | billingaccounts / billingprofiles / invoicesections / products / transfer | 아니요 | 예 |
> | billingaccounts / billingprofiles / invoicesections / products / updateautorenew | 아니요 | 예 |
> | billingaccounts / billingprofiles / invoicesections / transactions | 아니요 | 예 |
> | billingaccounts / billingprofiles / invoicesections / transfers | 아니요 | 예 |
> | billingaccounts / billingprofiles / patchoperations | 아니요 | 예 |
> | billingaccounts / billingprofiles / paymentmethods | 아니요 | 예 |
> | billingaccounts / billingprofiles / policies | 아니요 | 예 |
> | billingaccounts / billingprofiles / pricesheet | 아니요 | 예 |
> | billingaccounts / billingprofiles / pricesheetdownloadoperations | 아니요 | 예 |
> | billingaccounts / billingprofiles / products | 아니요 | 예 |
> | billingaccounts / billingprofiles / transactions | 아니요 | 예 |
> | billingaccounts / billingroleassignments | 아니요 | 예 |
> | billingaccounts / billingroledefinitions | 아니요 | 예 |
> | billingaccounts / billingsubscriptions | 아니요 | 예 |
> | billingaccounts / billingsubscriptions / invoices | 아니요 | 예 |
> | billingaccounts / createbillingroleassignment | 아니요 | 예 |
> | billingaccounts / createinvoicesectionoperations | 아니요 | 예 |
> | billingaccounts / customers | 아니요 | 예 |
> | billingaccounts / customers / billingpermissions | 아니요 | 예 |
> | billingaccounts / customers / billingsubscriptions | 아니요 | 예 |
> | billingaccounts / customers / initiatetransfer | 아니요 | 예 |
> | billingaccounts / customers / policies | 아니요 | 예 |
> | billingaccounts / customers / products | 아니요 | 예 |
> | billingaccounts / customers / transactions | 아니요 | 예 |
> | billingaccounts / customers / transfers | 아니요 | 예 |
> | billingaccounts / departments | 아니요 | 예 |
> | billingaccounts/부서/billingpermissions | 아니요 | 예 |
> | billingaccounts/부서/billingroleassignments | 아니요 | 예 |
> | billingaccounts/부서/billingroledefinitions | 아니요 | 예 |
> | billingaccounts / enrollmentaccounts | 아니요 | 예 |
> | billingaccounts / enrollmentaccounts / billingpermissions | 아니요 | 예 |
> | billingaccounts / enrollmentaccounts / billingroleassignments | 아니요 | 예 |
> | billingaccounts / enrollmentaccounts / billingroledefinitions | 아니요 | 예 |
> | billingaccounts / invoices | 아니요 | 예 |
> | billingaccounts/송장/트랜잭션 | 아니요 | 예 |
> | billingaccounts / invoicesections | 아니요 | 예 |
> | billingaccounts / invoicesections / billingsubscriptionmoveoperations | 아니요 | 예 |
> | billingaccounts / invoicesections / billingsubscriptions | 아니요 | 예 |
> | billingaccounts / invoicesections / billingsubscriptions / transfer | 아니요 | 예 |
> | billingaccounts / invoicesections / elevate | 아니요 | 예 |
> | billingaccounts / invoicesections / initiatetransfer | 아니요 | 예 |
> | billingaccounts / invoicesections / patchoperations | 아니요 | 예 |
> | billingaccounts / invoicesections / productmoveoperations | 아니요 | 예 |
> | billingaccounts / invoicesections / products | 아니요 | 예 |
> | billingaccounts / invoicesections / products / transfer | 예 | 예 |
> | billingaccounts / invoicesections / products / updateautorenew | 아니요 | 예 |
> | billingaccounts / invoicesections / producttransfersresults | 아니요 | 예 |
> | billingaccounts / invoicesections / transactions | 아니요 | 예 |
> | billingaccounts / invoicesections / transfers | 아니요 | 예 |
> | billingaccounts / lineofcredit | 아니요 | 예 |
> | billingaccounts / listinvoicesectionswithcreatesubscriptionpermission | 아니요 | 예 |
> | billingaccounts / operationresults | 아니요 | 예 |
> | billingaccounts / patchoperations | 아니요 | 예 |
> | billingaccounts / paymentmethods | 아니요 | 예 |
> | billingaccounts / products | 아니요 | 예 |
> | billingaccounts / transactions | 아니요 | 예 |
> | billingperiods | 아니요 | 예 |
> | billingpermissions | 아니요 | 예 |
> | billingproperty | 아니요 | 예 |
> | billingroleassignments | 아니요 | 예 |
> | billingroledefinitions | 아니요 | 예 |
> | createbillingroleassignment | 아니요 | 예 |
> | departments | 아니요 | 예 |
> | enrollmentaccounts | 아니요 | 아니요 |
> | invoices | 아니요 | 아니요 |
> | operationresults | 예 | 아니요 |
> | 작업 | 예 | 아니요 |
> | operationstatus | 예 | 아니요 |
> | transfers | 예 | 아니요 |
> | transfers / accepttransfer | 아니요 | 아니요 |
> | transfers / declinetransfer | 예 | 예 |
> | transfers / operationstatus | 아니요 | 아니요 |
> | transfers / validatetransfer | 아니요 | 예 |
> | validateaddress | 아니요 | 예 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | listcommunicationpreference | 예 | 예 |
> | mapapis | 예 | 예 |
> | 작업 | 예 | 예 |
> | updatecommunicationpreference | 예 | 예 |

## <a name="microsoftbiztalkservices"></a>BIZTALKSERVICES

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | biztalk | 예 | 아니요 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | 예 | 아니요 |
> | cordamembers | 예 | 아니요 |
> | 위치 | 아니요 | 아니요 |
> | locations / blockchainmemberoperationresults | 예 | 예 |
> | locations / checknameavailability | 예 | 예 |
> | locations / listconsortiums | 예 | 예 |
> | locations / watcheroperationresults | 예 | 예 |
> | 작업 | 예 | 예 |
> | watchers | 예 | 예 |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 작업 | 예 | 아니요 |
> | tokenservices | 아니요 | 아니요 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | 예 | 아니요 |
> | blueprintassignments / assignmentoperations | 예 | 아니요 |
> | blueprintassignments / operations | 예 | 아니요 |
> | blueprints | 예 | 아니요 |
> | blueprints / artifacts | 예 | 아니요 |
> | blueprints / versions | 아니요 | 아니요 |
> | blueprints / versions / artifacts | 예 | 예 |
> | 작업 | 예 | 예 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | botservices | 예 | 아니요 |
> | botservices / channels | 예 | 아니요 |
> | botservices / connections | 아니요 | 아니요 |
> | checknameavailability | 예 | 아니요 |
> | listauthserviceproviders | 예 | 아니요 |
> | 작업 | 예 | 아니요 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 아니요 | 예 |
> | 위치 | 아니요 | 예 |
> | locations / operationresults | 아니요 | 예 |
> | locations / operationsstatus | 아니요 | 예 |
> | 작업 | 아니요 | 예 |
> | redis | 예 | 예 |
> | redis/eventgridfilters | 아니요 | 예 |
> | redis / privatelinkresources | 아니요 | 예 |
> | redisenterprise | 예 | 예 |

> [!IMPORTANT]
> Azure Cache for Redis 인스턴스가 가상 네트워크를 사용하여 구성되면 이 인스턴스를 다른 구독으로 이동할 수 없습니다. [네트워킹 이동 제한](./move-limitations/networking-move-limitations.md)을 참조하세요.

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | appliedreservations | 아니요 | 예 |
> | calculateexchange | 아니요 | 예 |
> | calculateprice | 아니요 | 예 |
> | calculatepurchaseprice | 아니요 | 예 |
> | catalogs | 아니요 | 예 |
> | checkoffers | 아니요 | 예 |
> | checkpurchasestatus | 아니요 | 예 |
> | checkscopes | 아니요 | 예 |
> | commercialreservationorders | 아니요 | 예 |
> | 교환 | 아니요 | 예 |
> | listbenefits | 아니요 | 예 |
> | 작업 | 아니요 | 예 |
> | placepurchaseorder | 아니요 | 예 |
> | reservationorders | 아니요 | 예 |
> | reservationorders / availablescopes | 아니요 | 예 |
> | reservationorders / calculaterefund | 아니요 | 아니요 |
> | reservationorders / merge | 아니요 | 아니요 |
> | reservationorders / reservations | 예 | 아니요 |
> | reservationorders / reservations / availablescopes | 아니요 | 아니요 |
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
> | cdnwebapplicationfirewallpolicies | 아니요 | 아니요 |
> | checknameavailability | 아니요 | 아니요 |
> | checkresourceusage | 아니요 | 예 |
> | edgenodes | 아니요 | 예 |
> | operationresults | 아니요 | 예 |
> | operationresults / profileresults | 아니요 | 예 |
> | operationresults / profileresults / endpointresults | 아니요 | 아니요 |
> | operationresults / profileresults / endpointresults / customdomainresults | 아니요 | 아니요 |
> | operationresults / profileresults / endpointresults / origingroupresults | 아니요 | 아니요 |
> | operationresults / profileresults / endpointresults / originresults | 예 | 아니요 |
> | 작업 | 예 | 아니요 |
> | 프로필 | 예 | 예 |
> | profiles / endpoints | 아니요 | 아니요 |
> | profiles / endpoints / customdomains | 아니요 | 예 |
> | profiles / endpoints / origingroups | 아니요 | 아니요 |
> | profiles / endpoints / origins | 아니요 | 아니요 |
> | validateprobe | 예 | 아니요 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | certificateorders | 예 | 예 |
> | certificateorders / certificates | 아니요 | 예 |
> | 작업 | 아니요 | 예 |
> | validatecertificateregistrationinformation | 아니요 | 예 |

> [!IMPORTANT]
> [App Service 이동 지침](./move-limitations/app-service-move-limitations.md)을 참조하세요.

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 작업 | 예 | 아니요 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | capabilities | 아니요 | 예 |
> | checkdomainnameavailability | 아니요 | 예 |
> | domainnames | 아니요 | 예 |
> | domainnames / capabilities | 아니요 | 예 |
> | domainnames / internalloadbalancers | 아니요 | 예 |
> | domainnames / servicecertificates | 아니요 | 예 |
> | domainnames / slots | 아니요 | 예 |
> | domainnames / slots / roles | 아니요 | 아니요 |
> | domainnames / slots / roles / metricdefinitions | 아니요 | 예 |
> | domainnames / slots / roles / metrics | 아니요 | 예 |
> | movesubscriptionresources | 아니요 | 예 |
> | operatingsystemfamilies | 아니요 | 아니요 |
> | operatingsystems | 아니요 | 아니요 |
> | 작업 | 아니요 | 예 |
> | operationstatuses | 아니요 | 아니요 |
> | quotas | 아니요 | 아니요 |
> | resourcetypes | 아니요 | 예 |
> | validatesubscriptionmoveavailability | 아니요 | 예 |
> | virtualmachines | 아니요 | 예 |
> | virtualmachines / diagnosticsettings | 아니요 | 예 |
> | virtualmachines / metricdefinitions | 아니요 | 예 |
> | virtualmachines / metrics | 아니요 | 예 |

> [!IMPORTANT]
> [클래식 배포 이동 지침](./move-limitations/classic-model-move-limitations.md)을 참조하세요. 클래식 배포 리소스는 해당 시나리오와 관련된 작업을 통해 구독 간에 이동할 수 있습니다.

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | classicinfrastructureresources | 아니요 | 예 |

> [!IMPORTANT]
> [클래식 배포 이동 지침](./move-limitations/classic-model-move-limitations.md)을 참조하세요. 클래식 배포 리소스는 해당 시나리오와 관련된 작업을 통해 구독 간에 이동할 수 있습니다.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | capabilities | 아니요 | 아니요 |
> | expressroutecrossconnections | 아니요 | 아니요 |
> | expressroutecrossconnections / peerings | 아니요 | 예 |
> | gatewaysupporteddevices | 아니요 | 예 |
> | networksecuritygroups | 아니요 | 예 |
> | 작업 | 아니요 | 예 |
> | quotas | 아니요 | 예 |
> | reservedips | 아니요 | 예 |
> | virtualnetworks | 아니요 | 예 |
> | virtualnetworks / remotevirtualnetworkpeeringproxies | 아니요 | 예 |
> | virtualnetworks / virtualnetworkpeerings | 아니요 | 예 |

> [!IMPORTANT]
> [클래식 배포 이동 지침](./move-limitations/classic-model-move-limitations.md)을 참조하세요. 클래식 배포 리소스는 해당 시나리오와 관련된 작업을 통해 구독 간에 이동할 수 있습니다.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | capabilities | 아니요 | 예 |
> | checkstorageaccountavailability | 아니요 | 예 |
> | disks | 아니요 | 예 |
> | images | 아니요 | 예 |
> | 작업 | 아니요 | 예 |
> | osimages | 아니요 | 예 |
> | osplatformimages | 아니요 | 예 |
> | publicimages | 아니요 | 예 |
> | quotas | 아니요 | 예 |
> | storageaccounts | 아니요 | 예 |
> | storageaccounts / blobservices | 아니요 | 예 |
> | storageaccounts / fileservices | 아니요 | 아니요 |
> | storageaccounts / metricdefinitions | 아니요 | 아니요 |
> | storageaccounts / metrics | 아니요 | 예 |
> | storageaccounts / queueservices | 아니요 | 아니요 |
> | storageaccounts / services | 아니요 | 아니요 |
> | storageaccounts / services / diagnosticsettings | 아니요 | 예 |
> | storageaccounts / services / metricdefinitions | 아니요 | 아니요 |
> | storageaccounts / services / metrics | 아니요 | 아니요 |
> | storageaccounts / tableservices | 아니요 | 아니요 |
> | storageaccounts / vmimages | 예 | 아니요 |
> | vmimages | 예 | 아니요 |

> [!IMPORTANT]
> [클래식 배포 이동 지침](./move-limitations/classic-model-move-limitations.md)을 참조하세요. 클래식 배포 리소스는 해당 시나리오와 관련된 작업을 통해 구독 간에 이동할 수 있습니다.

## <a name="microsoftclassicsubscription"></a>Microsoft.ClassicSubscription

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 작업 | 예 | 아니요 |

> [!IMPORTANT]
> [클래식 배포 이동 지침](./move-limitations/classic-model-move-limitations.md)을 참조하세요. 클래식 배포 리소스는 해당 시나리오와 관련된 작업을 통해 구독 간에 이동할 수 있습니다.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |
> | checkdomainavailability | 예 | 예 |
> | 위치 | 예 | 예 |
> | locations / checkskuavailability | 아니요 | 아니요 |
> | locations / deletevirtualnetworkorsubnets | 아니요 | 아니요 |
> | locations / operationresults | 아니요 | 예 |
> | 작업 | 아니요 | 예 |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 작업 | 예 | 예 |
> | ratecard | 예 | 예 |
> | usageaggregates | 예 | 예 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availabilitysets | 예 | 예 |
> | cloudservices | 아니요 | 아니요 |
> | diskaccesses | 예 | 아니요 |
> | diskencryptionsets | 예 | 아니요 |
> | disks | 예 | 예 |
> | galleries | 예 | 아니요 |
> | galleries / images | 예 | 아니요 |
> | galleries / images / versions | 예 | 아니요 |
> | hostgroups | 예 | 아니요 |
> | hostgroups / hosts | 예 | 아니요 |
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
> | locations / vmsizes | 아니요 | 아니요 |
> | locations / vsmoperations | 예 | 아니요 |
> | 작업 | 아니요 | 아니요 |
> | proximityplacementgroups | 예 | 예 |
> | restorepointcollections | 예 | 아니요 |
> | restorepointcollections / restorepoints | 예 | 아니요 |
> | sharedvmextensions | 아니요 | 아니요 |
> | sharedvmimages | 예 | 아니요 |
> | sharedvmimages / versions | 예 | 아니요 |
> | 스냅샷 | 예 | 예 |
> | sshpublickeys | 예 | 아니요 |
> | swiftlets | 예 | 아니요 |
> | virtualmachines | 예 | 예 |
> | virtualmachines / extensions | 예 | 예 |
> | virtualmachines / metricdefinitions | 아니요 | 예 |
> | virtualmachines / runcommands | 예 | 예 |
> | virtualmachinescalesets | 예 | 예 |
> | virtualmachinescalesets / extensions | 아니요 | 예 |
> | virtualmachinescalesets / networkinterfaces | 아니요 | 예 |
> | virtualmachinescalesets / publicipaddresses | 아니요 | 예 |
> | virtualmachinescalesets / virtualmachines | 아니요 | 예 |
> | virtualmachinescalesets / virtualmachines / networkinterfaces | 아니요 | 예 |

> [!IMPORTANT]
> [Virtual Machines 이동 지침](./move-limitations/virtual-machines-move-limitations.md)을 참조하세요.

## <a name="microsoftconnectedcache"></a>ConnectedCache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | cachenodes | 예 | 아니요 |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | aggregatedcost | 아니요 | 예 |
> | balances | 아니요 | 예 |
> | budgets | 아니요 | 예 |
> | charges | 아니요 | 예 |
> | costtags | 아니요 | 예 |
> | credits | 아니요 | 예 |
> | events | 아니요 | 예 |
> | forecasts | 아니요 | 예 |
> | lots | 아니요 | 예 |
> | marketplaces | 아니요 | 예 |
> | operationresults | 아니요 | 예 |
> | 작업 | 아니요 | 아니요 |
> | operationstatus | 아니요 | 아니요 |
> | pricesheets | 예 | 아니요 |
> | products | 아니요 | 아니요 |
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
> | containergroups | 예 | 예 |
> | 위치 | 예 | 예 |
> | locations / cachedimages | 예 | 예 |
> | locations / capabilities | 예 | 예 |
> | locations / deletevirtualnetworkorsubnets | 예 | 예 |
> | locations / operations | 예 | 예 |
> | locations / usages | 예 | 예 |
> | 작업 | 아니요 | 아니요 |
> | serviceassociationlinks | 아니요 | 아니요 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 예 | 아니요 |
> | 위치 | 예 | 아니요 |
> | locations / authorize | 예 | 아니요 |
> | locations / deletevirtualnetworkorsubnets | 아니요 | 아니요 |
> | locations / operationresults | 예 | 아니요 |
> | locations / setupauth | 예 | 아니요 |
> | 작업 | 예 | 아니요 |
> | registries | 예 | 예 |
> | registries / agentpools | 예 | 아니요 |
> | registries / agentpools / listqueuestatus | 예 | 아니요 |
> | registries / builds | 예 | 아니요 |
> | registries / builds / cancel | 예 | 아니요 |
> | registries / builds / getloglink | 예 | 아니요 |
> | registries / buildtasks | 예 | 아니요 |
> | registries / buildtasks / listsourcerepositoryproperties | 예 | 아니요 |
> | registries / buildtasks / steps | 예 | 아니요 |
> | registries / buildtasks / steps / listbuildarguments | 예 | 아니요 |
> | registries / eventgridfilters | 예 | 아니요 |
> | registries / exportpipelines | 예 | 아니요 |
> | registries / generatecredentials | 예 | 아니요 |
> | registries / getbuildsourceuploadurl | 예 | 아니요 |
> | registries / getcredentials | 예 | 아니요 |
> | registries / importimage | 예 | 아니요 |
> | registries / importpipelines | 예 | 아니요 |
> | registries / listbuildsourceuploadurl | 예 | 아니요 |
> | registries / listcredentials | 예 | 아니요 |
> | registries / listpolicies | 아니요 | 아니요 |
> | registries / listusages | 예 | 아니요 |
> | registries / pipelineruns | 예 | 아니요 |
> | registries / privateendpointconnectionproxies | 예 | 아니요 |
> | registries / privateendpointconnectionproxies / validate | 예 | 아니요 |
> | registries / privateendpointconnections | 예 | 아니요 |
> | registries / privatelinkresources | 예 | 아니요 |
> | registries / queuebuild | 예 | 아니요 |
> | registries / regeneratecredential | 아니요 | 아니요 |
> | registries / regeneratecredentials | 예 | 아니요 |
> | registries / replications | 예 | 아니요 |
> | registries / runs | 예 | 아니요 |
> | registries / runs / cancel | 아니요 | 아니요 |
> | registries / runs / listlogsasurl | 예 | 아니요 |
> | registries / schedulerun | 예 | 아니요 |
> | registries / scopemaps | 예 | 아니요 |
> | registries / taskruns | 아니요 | 아니요 |
> | registries / taskruns / listdetails | 예 | 예 |
> | registries / tasks | 예 | 예 |
> | registries / tasks / listdetails | 예 | 예 |
> | registries / tokens | 예 | 예 |
> | registries / updatepolicies | 예 | 예 |
> | registries / webhooks | 예 | 예 |
> | registries / webhooks / getcallbackconfig | 예 | 예 |
> | registries / webhooks / listevents | 예 | 예 |
> | registries / webhooks / ping | 예 | 예 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containerservices | 예 | 예 |
> | 위치 | 아니요 | 아니요 |
> | locations / openshiftclusters | 아니요 | 예 |
> | locations / operationresults | 아니요 | 아니요 |
> | locations / operations | 아니요 | 아니요 |
> | locations / orchestrators | 예 | 아니요 |
> | managedclusters | 예 | 아니요 |
> | openshiftmanagedclusters | 예 | 아니요 |
> | 작업 | 예 | 아니요 |

## <a name="microsoftcontentmoderator"></a>MICROSOFT. CONTENTMODERATOR

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 애플리케이션 | 아니요 | 예 |

## <a name="microsoftcortanaanalytics"></a>MICROSOFT. CORTANAANALYTICS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 경고 | 예 | 아니요 |
> | billingaccounts | 예 | 아니요 |
> | budgets | 예 | 아니요 |
> | cloudconnectors | 예 | 아니요 |
> | 커넥터 | 예 | 아니요 |
> | costallocationrules | 예 | 아니요 |
> | departments | 예 | 아니요 |
> | dimensions | 예 | 아니요 |
> | enrollmentaccounts | 예 | 아니요 |
> | exports | 예 | 아니요 |
> | externalbillingaccounts | 예 | 아니요 |
> | externalbillingaccounts / alerts | 예 | 아니요 |
> | externalbillingaccounts / dimensions | 예 | 아니요 |
> | externalbillingaccounts / forecast | 예 | 아니요 |
> | externalbillingaccounts / query | 예 | 아니요 |
> | externalsubscriptions | 예 | 아니요 |
> | externalsubscriptions / alerts | 예 | 아니요 |
> | externalsubscriptions / dimensions | 예 | 아니요 |
> | externalsubscriptions / forecast | 아니요 | 아니요 |
> | externalsubscriptions / query | 예 | 예 |
> | 예측 | 아니요 | 아니요 |
> | 작업 | 아니요 | 예 |
> | Query | 아니요 | 아니요 |
> | register | 아니요 | 아니요 |
> | reportconfigs | 예 | 아니요 |
> | reports | 예 | 아니요 |
> | 설정 | 아니요 | 아니요 |
> | showbackrules | 예 | 예 |
> | 뷰 | 예 | 예 |

## <a name="microsoftcostmanagementexports"></a>Microsoft.CostManagementExports

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 작업 | 예 | 아니요 |

## <a name="microsoftcustomerinsights"></a>MICROSOFT. CUSTOMERINSIGHTS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hubs | 아니요 | 아니요 |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 작업 | 아니요 | 예 |
> | requests | 아니요 | 예 |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | associations | 예 | 예 |
> | 위치 | 예 | 예 |
> | locations / operationstatuses | 예 | 예 |
> | 작업 | 예 | 예 |
> | resourceproviders | 아니요 | 아니요 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | 예 | 예 |
> | 위치 | 예 | 예 |
> | locations / availableskus | 아니요 | 아니요 |
> | locations / checknameavailability | 아니요 | 예 |
> | locations / operationresults | 아니요 | 예 |
> | locations / regionconfiguration | 아니요 | 예 |
> | locations / validateaddress | 아니요 | 예 |
> | locations / validateinputs | 아니요 | 예 |
> | 작업 | 아니요 | 예 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availableskus | 아니요 | 아니요 |
> | databoxedgedevices | 예 | 예 |
> | databoxedgedevices / checknameavailability | 아니요 | 예 |
> | 작업 | 아니요 | 예 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 위치 | 예 | 예 |
> | locations / getnetworkpolicies | 예 | 예 |
> | locations / operationstatuses | 예 | 예 |
> | 작업 | 아니요 | 아니요 |
> | workspaces | 아니요 | 예 |
> | workspaces / dbworkspaces | 아니요 | 아니요 |
> | workspaces / virtualnetworkpeerings | 아니요 | 아니요 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | catalogs | 아니요 | 아니요 |
> | checknameavailability | 아니요 | 아니요 |
> | datacatalogs | 예 | 아니요 |
> | 위치 | 예 | 아니요 |
> | locations / jobs | 아니요 | 아니요 |
> | locations / operationresults | 예 | 아니요 |
> | 작업 | 예 | 아니요 |

## <a name="microsoftdataconnect"></a>MICROSOFT. DATACONNECT

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | 아니요 | 예 |

## <a name="microsoftdataexchange"></a>MICROSOFT. DATAEXCHANGE

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 패키지 | 예 | 예 |
> | 플랜 | 예 | 예 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checkazuredatafactorynameavailability | 아니요 | 아니요 |
> | checkdatafactorynameavailability | 예 | 예 |
> | datafactories | 아니요 | 아니요 |
> | datafactories / diagnosticsettings | 아니요 | 아니요 |
> | datafactories / metricdefinitions | 아니요 | 예 |
> | datafactoryschema | 아니요 | 예 |
> | factories | 예 | 예 |
> | factories / integrationruntimes | 아니요 | 예 |
> | 위치 | 아니요 | 예 |
> | locations / configurefactoryrepo | 아니요 | 예 |
> | locations / getfeaturevalue | 아니요 | 예 |
> | 작업 | 아니요 | 예 |

## <a name="microsoftdatalake"></a>MICROSOFT. DATALAKE

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | 예 | 예 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아니요 | 아니요 |
> | accounts / datalakestoreaccounts | 예 | 예 |
> | accounts / storageaccounts | 예 | 예 |
> | accounts / storageaccounts / containers | 예 | 예 |
> | accounts / storageaccounts / containers / listsastokens | 예 | 예 |
> | 위치 | 예 | 예 |
> | locations / capability | 예 | 예 |
> | locations / checknameavailability | 예 | 예 |
> | locations / operationresults | 예 | 예 |
> | locations / usages | 예 | 예 |
> | 작업 | 아니요 | 아니요 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |
> | accounts / eventgridfilters | 예 | 예 |
> | accounts / firewallrules | 예 | 예 |
> | 위치 | 예 | 예 |
> | locations / capability | 예 | 예 |
> | locations / checknameavailability | 예 | 예 |
> | locations / deletevirtualnetworkorsubnets | 예 | 예 |
> | locations / operationresults | 아니요 | 아니요 |
> | locations / usages | 아니요 | 예 |
> | 작업 | 아니요 | 예 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 위치 | 아니요 | 아니요 |
> | locations / checknameavailability | 아니요 | 아니요 |
> | locations / operationresults | 아니요 | 예 |
> | locations / operationstatuses | 아니요 | 예 |
> | 작업 | 아니요 | 예 |
> | services | 아니요 | 예 |
> | services / projects | 아니요 | 예 |
> | slots | 아니요 | 예 |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | backupvaults | 예 | 예 |
> | 위치 | 예 | 예 |
> | 작업 | 예 | 예 |
> | resourceoperationgatekeepers 키퍼 | 예 | 예 |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |
> | accounts / shares | 예 | 아니요 |
> | accounts / shares / datasets | 예 | 아니요 |
> | accounts / shares / invitations | 아니요 | 아니요 |
> | accounts / shares / providersharesubscriptions | 예 | 예 |
> | accounts / shares / synchronizationsettings | 예 | 예 |
> | accounts / sharesubscriptions | 예 | 예 |
> | accounts / sharesubscriptions / consumersourcedatasets | 예 | 예 |
> | accounts / sharesubscriptions / datasetmappings | 예 | 예 |
> | accounts / sharesubscriptions / triggers | 예 | 예 |
> | listinvitations | 예 | 예 |
> | 위치 | 예 | 예 |
> | locations / consumerinvitations | 예 | 예 |
> | locations / operationresults | 예 | 예 |
> | locations / rejectinvitation | 예 | 예 |
> | 작업 | 예 | 예 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 예 | 아니요 |
> | 위치 | 예 | 아니요 |
> | locations / azureasyncoperation | 예 | 아니요 |
> | locations / operationresults | 아니요 | 아니요 |
> | locations / performancetiers | 예 | 아니요 |
> | locations / privateendpointconnectionazureasyncoperation | 예 | 아니요 |
> | locations / privateendpointconnectionoperationresults | 예 | 아니요 |
> | locations / privateendpointconnectionproxyazureasyncoperation | 예 | 아니요 |
> | locations / privateendpointconnectionproxyoperationresults | 예 | 아니요 |
> | locations / recommendedactionsessionsazureasyncoperation | 예 | 아니요 |
> | locations / recommendedactionsessionsoperationresults | 예 | 아니요 |
> | locations / securityalertpoliciesazureasyncoperation | 예 | 아니요 |
> | locations / securityalertpoliciesoperationresults | 예 | 아니요 |
> | locations / serverkeyazureasyncoperation | 아니요 | 아니요 |
> | locations / serverkeyoperationresults | 예 | 예 |
> | 작업 | 예 | 예 |
> | servers | 예 | 예 |
> | servers / advisors | 예 | 예 |
> | servers / privateendpointconnectionproxies | 예 | 예 |
> | servers / privateendpointconnections | 예 | 예 |
> | servers / privatelinkresources | 예 | 예 |
> | servers / querytexts | 예 | 예 |
> | servers / recoverableservers | 예 | 예 |
> | servers / topquerystatistics | 예 | 예 |
> | servers / virtualnetworkrules | 예 | 예 |
> | servers / waitstatistics | 예 | 예 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 예 | 아니요 |
> | 위치 | 예 | 아니요 |
> | locations / administratorazureasyncoperation | 예 | 아니요 |
> | locations / administratoroperationresults | 예 | 아니요 |
> | locations / azureasyncoperation | 예 | 아니요 |
> | locations / operationresults | 아니요 | 아니요 |
> | locations / performancetiers | 예 | 아니요 |
> | locations / privateendpointconnectionazureasyncoperation | 예 | 아니요 |
> | locations / privateendpointconnectionoperationresults | 예 | 아니요 |
> | locations / privateendpointconnectionproxyazureasyncoperation | 예 | 아니요 |
> | locations / privateendpointconnectionproxyoperationresults | 예 | 아니요 |
> | locations / recommendedactionsessionsazureasyncoperation | 예 | 아니요 |
> | locations / recommendedactionsessionsoperationresults | 예 | 아니요 |
> | locations / securityalertpoliciesazureasyncoperation | 예 | 아니요 |
> | locations / securityalertpoliciesoperationresults | 예 | 아니요 |
> | locations / serverkeyazureasyncoperation | 예 | 아니요 |
> | locations / serverkeyoperationresults | 아니요 | 아니요 |
> | 작업 | 예 | 예 |
> | servers | 예 | 예 |
> | servers / advisors | 예 | 예 |
> | servers / keys | 예 | 예 |
> | servers / privateendpointconnectionproxies | 예 | 예 |
> | servers / privateendpointconnections | 예 | 예 |
> | servers / privatelinkresources | 예 | 예 |
> | servers / querytexts | 예 | 예 |
> | servers / recoverableservers | 예 | 예 |
> | servers / topquerystatistics | 예 | 예 |
> | servers / virtualnetworkrules | 예 | 예 |
> | servers / waitstatistics | 예 | 예 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 예 | 아니요 |
> | 위치 | 예 | 아니요 |
> | locations / administratorazureasyncoperation | 예 | 아니요 |
> | locations / administratoroperationresults | 예 | 아니요 |
> | locations / azureasyncoperation | 예 | 아니요 |
> | locations / operationresults | 예 | 아니요 |
> | locations / performancetiers | 아니요 | 아니요 |
> | locations / privateendpointconnectionazureasyncoperation | 예 | 아니요 |
> | locations / privateendpointconnectionoperationresults | 예 | 아니요 |
> | locations / privateendpointconnectionproxyazureasyncoperation | 예 | 아니요 |
> | locations / privateendpointconnectionproxyoperationresults | 예 | 아니요 |
> | locations / recommendedactionsessionsazureasyncoperation | 예 | 아니요 |
> | locations / recommendedactionsessionsoperationresults | 예 | 아니요 |
> | locations / securityalertpoliciesazureasyncoperation | 예 | 아니요 |
> | locations / securityalertpoliciesoperationresults | 예 | 아니요 |
> | locations / serverkeyazureasyncoperation | 예 | 아니요 |
> | locations / serverkeyoperationresults | 예 | 아니요 |
> | 작업 | 아니요 | 아니요 |
> | servergroups | 아니요 | 아니요 |
> | servers | 예 | 예 |
> | servers / advisors | 아니요 | 아니요 |
> | servers / keys | 예 | 예 |
> | servers / privateendpointconnectionproxies | 예 | 예 |
> | servers / privateendpointconnections | 아니요 | 아니요 |
> | servers / privatelinkresources | 아니요 | 아니요 |
> | servers / querytexts | 아니요 | 아니요 |
> | servers / recoverableservers | 아니요 | 아니요 |
> | servers / topquerystatistics | 아니요 | 아니요 |
> | servers / virtualnetworkrules | 아니요 | 아니요 |
> | servers / waitstatistics | 아니요 | 아니요 |
> | serversv2 | 예 | 예 |
> | singleservers | 예 | 예 |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | artifactsources | 아니요 | 아니요 |
> | operationresults | 예 | 예 |
> | 작업 | 예 | 예 |
> | rollouts | 예 | 예 |
> | servicetopologies | 예 | 예 |
> | servicetopologies / services | 아니요 | 아니요 |
> | servicetopologies / services / serviceunits | 아니요 | 아니요 |
> | 단계 | 아니요 | 예 |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgroups | 예 | 예 |
> | applicationgroups / applications | 예 | 예 |
> | applicationgroups / desktops | 아니요 | 아니요 |
> | applicationgroups / startmenuitems | 예 | 예 |
> | hostpools | 예 | 예 |
> | hostpools / sessionhosts | 예 | 예 |
> | hostpools / sessionhosts / usersessions | 아니요 | 아니요 |
> | hostpools / usersessions | 예 | 예 |
> | 작업 | 아니요 | 아니요 |
> | workspaces | 아니요 | 아니요 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 아니요 | 아니요 |
> | checkprovisioningservicenameavailability | 아니요 | 예 |
> | elasticpools | 예 | 예 |
> | elasticpools / iothubtenants | 예 | 예 |
> | iothubs | 예 | 예 |
> | iothubs / eventgridfilters | 아니요 | 예 |
> | iothubs/securitysettings | 아니요 | 아니요 |
> | operationresults | 아니요 | 아니요 |
> | 작업 | 예 | 아니요 |
> | provisioningservices | 예 | 예 |
> | usages | 아니요 | 아니요 |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | pipelines | 아니요 | 예 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | controllers | 예 | 예 |
> | controllers / listconnectiondetails | 아니요 | 아니요 |
> | 위치 | 아니요 | 아니요 |
> | locations / checkcontainerhostmapping | 아니요 | 예 |
> | locations / operationresults | 아니요 | 예 |
> | 작업 | 아니요 | 예 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | labcenters | 아니요 | 예 |
> | labs | 아니요 | 아니요 |
> | labs / environments | 아니요 | 예 |
> | labs / servicerunners | 아니요 | 예 |
> | labs / virtualmachines | 아니요 | 예 |
> | 위치 | 아니요 | 예 |
> | locations / operations | 아니요 | 예 |
> | 작업 | 아니요 | 예 |
> | schedules | 아니요 | 아니요 |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | 아니요 | 아니요 |
> | digitaltwinsinstances/끝점 | 아니요 | 예 |
> | digitaltwinsinstances/operationresults | 아니요 | 예 |
> | 위치 | 아니요 | 예 |
> | locations / checknameavailability | 아니요 | 예 |
> | 작업 | 아니요 | 예 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | databaseaccountnames | 아니요 | 아니요 |
> | databaseaccounts | 예 | 예 |
> | 위치 | 아니요 | 예 |
> | locations / deletevirtualnetworkorsubnets | 아니요 | 예 |
> | locations / operationresults | 아니요 | 아니요 |
> | locations / operationsstatus | 아니요 | 아니요 |
> | operationresults | 아니요 | 아니요 |
> | 작업 | 아니요 | 아니요 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checkdomainavailability | 아니요 | 아니요 |
> | domains | 예 | 예 |
> | domains / domainownershipidentifiers | 아니요 | 예 |
> | generatessorequest | 아니요 | 예 |
> | listdomainrecommendations | 아니요 | 예 |
> | 작업 | 아니요 | 예 |
> | topleveldomains | 아니요 | 예 |
> | validatedomainregistrationinformation | 아니요 | 예 |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 위치 | 예 | 예 |
> | locations / operationresults | 아니요 | 아니요 |
> | 작업 | 예 | 예 |
> | services | 예 | 예 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domains | 예 | 아니요 |
> | domains / topics | 아니요 | 아니요 |
> | eventsubscriptions | 아니요 - 독립적으로 이동할 수는 없지만, 구독된 리소스와 함께 자동으로 이동할 수 있습니다. | 아니요 - 독립적으로 이동할 수는 없지만, 구독된 리소스와 함께 자동으로 이동할 수 있습니다. |
> | extensiontopics | 아니요 | 아니요 |
> | 위치 | 예 | 아니요 |
> | locations / eventsubscriptions | 아니요 | 아니요 |
> | locations / operationresults | 예 | 예 |
> | locations / operationsstatus | 예 | 예 |
> | locations / topictypes | 예 | 예 |
> | operationresults | 아니요 | 아니요 |
> | 작업 | 예 | 예 |
> | operationsstatus | 예 | 예 |
> | partnernamespaces | 아니요 | 아니요 |
> | partnernamespaces / eventchannels | 예 | 예 |
> | partnerregistrations | 예 | 예 |
> | partnertopics | 예 | 예 |
> | partnertopics / eventsubscriptions | 예 | 예 |
> | systemtopics | 예 | 예 |
> | systemtopics / eventsubscriptions | 예 | 예 |
> | topics | 예 | 예 |
> | topictypes | 예 | 예 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availableclusterregions | 아니요 | 아니요 |
> | checknameavailability | 예 | 예 |
> | checknamespaceavailability | 예 | 예 |
> | clusters | 예 | 예 |
> | 위치 | 아니요 | 아니요 |
> | locations / deletevirtualnetworkorsubnets | 아니요 | 아니요 |
> | 네임스페이스 | 아니요 | 아니요 |
> | namespaces / authorizationrules | 아니요 | 아니요 |
> | namespaces / disasterrecoveryconfigs | 예 | 아니요 |
> | namespaces / disasterrecoveryconfigs / checknameavailability | 예 | 아니요 |
> | namespaces / eventhubs | 예 | 아니요 |
> | namespaces / eventhubs / authorizationrules | 예 | 아니요 |
> | namespaces / eventhubs / consumergroups | 예 | 아니요 |
> | namespaces / networkrulesets | 아니요 | 아니요 |
> | 작업 | 예 | 예 |
> | sku | 아니요 | 아니요 |

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
> | 네임스페이스 | 예 | 아니요 |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | featureproviders | 아니요 | 아니요 |
> | 기능 | 예 | 아니요 |
> | 작업 | 예 | 아니요 |
> | providers | 예 | 아니요 |
> | subscriptionfeatureregistrations | 예 | 아니요 |

## <a name="microsoftgenomics"></a>MICROSOFT. GENOMICS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아니요 | 아니요 |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | automanagedaccounts | 아니요 | 예 |
> | automanagedvmconfigurationprofiles | 아니요 | 예 |
> | guestconfigurationassignments | 아니요 | 아니요 |
> | 작업 | 아니요 | 아니요 |
> | software | 아니요 | 아니요 |
> | softwareupdateprofile | 예 | 아니요 |
> | softwareupdates | 예 | 아니요 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hanainstances | 아니요 | 예 |
> | 위치 | 아니요 | 예 |
> | locations / operations | 아니요 | 예 |
> | locations / operationsstatus | 아니요 | 예 |
> | 작업 | 아니요 | 예 |
> | sapmonitors | 아니요 | 예 |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dedicatedhsms | 아니요 | 아니요 |
> | 위치 | 아니요 | 아니요 |
> | 작업 | 예 | 예 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | 예 | 예 |
> | clusters / applications | 예 | 아니요 |
> | clusters / operationresults | 아니요 | 아니요 |
> | 위치 | 예 | 아니요 |
> | locations / azureasyncoperations | 예 | 아니요 |
> | locations / billingspecs | 아니요 | 아니요 |
> | locations / capabilities | 아니요 | 아니요 |
> | locations / operationresults | 예 | 예 |
> | locations / usages | 예 | 예 |
> | locations / validatecreaterequest | 아니요 | 아니요 |
> | 작업 | 아니요 | 아니요 |

> [!IMPORTANT]
> 새 구독 또는 리소스 그룹에 HDInsight 클러스터를 이동할 수 있습니다. 그러나 HDInsight 클러스터에 연결된 네트워킹 리소스(예: Virtual Network, NIC 또는 부하 분산 장치)는 구독 간에 이동할 수 없습니다. 또한 클러스터에 대한 가상 머신에 연결된 NIC를 새 리소스 그룹으로 이동할 수 없습니다.
>
> HDInsight 클러스터를 새 구독으로 이동할 때 먼저 다른 리소스(예: 스토리지 계정)를 이동합니다. 그런 다음 자체적으로 HDInsight 클러스터를 이동합니다.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 예 | 아니요 |
> | 위치 | 예 | 아니요 |
> | locations / operationresults | 예 | 아니요 |
> | 작업 | 예 | 아니요 |
> | services | 아니요 | 아니요 |
> | services / privateendpointconnections | 아니요 | 아니요 |
> | services / privatelinkresources | 예 | 예 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 위치 | 예 | 아니요 |
> | locations / operationresults | 아니요 | 아니요 |
> | locations / operationstatus | 아니요 | 아니요 |
> | machines | 예 | 예 |
> | machines / extensions | 예 | 예 |
> | 작업 | 아니요 | 아니요 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datamanagers | 예 | 아니요 |
> | 작업 | 아니요 | 아니요 |

## <a name="microsofthybridnetwork"></a>HybridNetwork

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | devices | 아니요 | 예 |
> | 위치 | 아니요 | 예 |
> | locations / operationstatuses | 아니요 | 예 |
> | 작업 | 아니요 | 예 |
> | vnfs | 아니요 | 예 |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | components | 예 | 예 |
> | 위치 | 예 | 예 |
> | networkscopes | 예 | 예 |
> | 작업 | 예 | 예 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | 예 | 예 |
> | 위치 | 예 | 아니요 |
> | locations / operationresults | 예 | 아니요 |
> | 작업 | 예 | 아니요 |

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | actiongroups | 아니요 | 예 |
> | activitylogalerts | 아니요 | 예 |
> | alertrules | 예 | 예 |
> | autoscalesettings | 예 | 예 |
> | baseline | 아니요 | 예 |
> | calculatebaseline | 아니요 | 예 |
> | components | 예 | 예 |
> | components / events | 아니요 | 예 |
> | components / linkedstorageaccounts | 아니요 | 예 |
> | 구성 요소/메타 데이터 | 아니요 | 예 |
> | components / metrics | 아니요 | 예 |
> | components / pricingplans | 아니요 | 예 |
> | components / query | 아니요 | 예 |
> | datacollectionrules | 아니요 | 예 |
> | diagnosticsettings | 아니요 | 예 |
> | diagnosticsettingscategories | 아니요 | 예 |
> | eventcategories | 아니요 | 아니요 |
> | eventtypes | 아니요 | 예 |
> | extendeddiagnosticsettings | 아니요 | 예 |
> | guestdiagnosticsettings | 아니요 | 예 |
> | listmigrationdate | 아니요 | 아니요 |
> | 위치 | 아니요 | 아니요 |
> | locations / operationresults | 아니요 | 아니요 |
> | logdefinitions | 아니요 | 아니요 |
> | logprofiles | 아니요 | 아니요 |
> | 로그 | 예 | 예 |
> | metricalerts | 예 | 예 |
> | metricbaselines | 예 | 예 |
> | metricbatch | 아니요 | 아니요 |
> | metricdefinitions | 예 | 예 |
> | metricnamespaces | 아니요 | 아니요 |
> | 메트릭 | 아니요 | 아니요 |
> | migratealertrules | 아니요 | 아니요 |
> | migratetonewpricingmodel | 아니요 | 예 |
> | myworkbooks | 아니요 | 아니요 |
> | notificationgroups | 아니요 | 아니요 |
> | 작업 | 예 | 아니요 |
> | privatelinkscopeoperationstatuses | 예 | 아니요 |
> | privatelinkscopes | 예 | 아니요 |
> | privatelinkscopes / privateendpointconnectionproxies | 예 | 아니요 |
> | privatelinkscopes / privateendpointconnections | 예 | 아니요 |
> | privatelinkscopes / scopedresources | 예 | 아니요 |
> | rollbacktolegacypricingmodel | 예 | 아니요 |
> | scheduledqueryrules | 예 | 예 |
> | 토폴로지 | 아니요 | 아니요 |
> | 트랜잭션 | 예 | 아니요 |
> | vminsightsonboardingstatuses | 예 | 아니요 |
> | webtests | 예 | 예 |
> | 웹 테스트/gettestresultfile | 아니요 | 아니요 |
> | workbooks | 아니요 | 아니요 |
> | workbooktemplates | 아니요 | 예 |

> [!IMPORTANT]
> 새 구독으로 이동하는 경우 [구독 할당량](azure-subscription-service-limits.md#azure-monitor-limits)을 초과하지 않아야 합니다.

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | apptemplates | 예 | 아니요 |
> | checknameavailability | 예 | 아니요 |
> | checksubdomainavailability | 아니요 | 아니요 |
> | iotapps | 예 | 예 |
> | 작업 | 아니요 | 아니요 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 예 | 예 |
> | graph | 예 | 예 |
> | 작업 | 예 | 예 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 예 | 아니요 |
> | deletedvaults | 예 | 아니요 |
> | hsmpools | 예 | 예 |
> | 위치 | 예 | 아니요 |
> | locations / deletedvaults | 예 | 아니요 |
> | locations / deletevirtualnetworkorsubnets | 예 | 아니요 |
> | locations / operationresults | 아니요 | 아니요 |
> | managedhsms | 예 | 예 |
> | 작업 | 예 | 예 |
> | vaults | 예 | 예 |
> | vaults / accesspolicies | 예 | 예 |
> | vaults / eventgridfilters | 예 | 예 |
> | vaults / secrets | 아니요 | 아니요 |

> [!IMPORTANT]
> 디스크 암호화에 사용되는 Key Vault는 동일한 구독의 리소스 그룹으로 또는 구독 간에 이동할 수 없습니다.

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | connectedclusters | 아니요 | 아니요 |
> | 위치 | 예 | 예 |
> | locations / operationstatuses | 아니요 | 아니요 |
> | 작업 | 아니요 | 예 |
> | registeredsubscriptions | 아니요 | 아니요 |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | sourcecontrolconfigurations | 아니요 | 예 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | 예 | 아니요 |
> | clusters / attacheddatabaseconfigurations | 예 | 아니요 |
> | clusters / databases | 예 | 아니요 |
> | clusters / databases / dataconnections | 아니요 | 아니요 |
> | clusters / databases / eventhubconnections | 아니요 | 아니요 |
> | clusters / databases / principalassignments | 예 | 예 |
> | clusters / principalassignments | 예 | 예 |
> | 위치 | 예 | 예 |
> | locations / checknameavailability | 예 | 예 |
> | locations / operationresults | 예 | 예 |
> | 작업 | 아니요 | 예 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | labaccounts | 아니요 | 아니요 |
> | 위치 | 예 | 예 |
> | locations / operations | 아니요 | 아니요 |
> | 작업 | 아니요 | 예 |
> | users | 아니요 | 예 |

## <a name="microsoftlocationbasedservices"></a>MICROSOFT. LOCATIONBASEDSERVICES

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |

## <a name="microsoftlocationservices"></a>MICROSOFT. LOCATIONSERVICES.LOG

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 아니요 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | 예 | 아니요 |
> | integrationaccounts | 예 | 예 |
> | integrationserviceenvironments | 예 | 예 |
> | integrationserviceenvironments / managedapis | 아니요 | 아니요 |
> | isolatedenvironments | 아니요 | 예 |
> | 위치 | 아니요 | 예 |
> | locations / workflows | 아니요 | 예 |
> | 작업 | 아니요 | 예 |
> | workflows | 예 | 예 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | commitmentplans | 예 | 예 |
> | 위치 | 예 | 예 |
> | locations / operations | 예 | 예 |
> | locations / operationsstatus | 예 | 예 |
> | 작업 | 예 | 예 |
> | webservices | 아니요 | 아니요 |
> | workspaces | 아니요 | 예 |

## <a name="microsoftmachinelearningcompute"></a>MICROSOFT. MACHINELEARNINGCOMPUTE

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | 아니요 | 아니요 |

## <a name="microsoftmachinelearningexperimentation"></a>MICROSOFT. MICROSOFT.MACHINELEARNINGEXPERIMENTATION

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아니요 | 아니요 |
> | accounts / workspaces | 예 | 예 |
> | accounts / workspaces / projects | 예 | 예 |
> | teamaccounts | 예 | 예 |
> | teamaccounts / workspaces | 아니요 | 아니요 |
> | teamaccounts / workspaces / projects | 아니요 | 예 |

## <a name="microsoftmachinelearningmodelmanagement"></a>MICROSOFT. MACHINELEARNINGMODELMANAGEMENT

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 위치 | 아니요 | 아니요 |
> | locations / computeoperationsstatus | 예 | 예 |
> | locations / quotas | 예 | 예 |
> | locations / updatequotas | 예 | 예 |
> | locations / usages | 예 | 예 |
> | locations / vmsizes | 예 | 예 |
> | locations / workspaceoperationsstatus | 아니요 | 아니요 |
> | 작업 | 아니요 | 예 |
> | workspaces | 아니요 | 예 |
> | workspaces / computes | 아니요 | 예 |
> | workspaces / eventgridfilters | 아니요 | 예 |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applyupdates | 예 | 예 |
> | configurationassignments | 예 | 예 |
> | maintenanceconfigurations | 예 | 예 |
> | updates | 예 | 예 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | ID | 아니요 | 아니요 |
> | 작업 | 아니요 | 아니요 |
> | userassignedidentities | 예 | 예 |

## <a name="microsoftmanagednetwork"></a>MICROSOFT. MANAGEDNETWORK

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managednetworks | 예 | 아니요 |
> | managednetworks / managednetworkgroups | 아니요 | 아니요 |
> | managednetworks / managednetworkpeeringpolicies | 예 | 예 |
> | 알림 | 예 | 예 |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | marketplaceregistrationdefinitions | 예 | 아니요 |
> | 작업 | 예 | 아니요 |
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
> | operationresults / asyncoperation | 아니요 | 예 |
> | 작업 | 아니요 | 예 |
> | 리소스 | 아니요 | 예 |
> | starttenantbackfill | 아니요 | 예 |
> | tenantbackfillstatus | 아니요 | 아니요 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 아니요 | 예 |
> | accounts / eventgridfilters | 아니요 | 예 |
> | accounts / privateatlases | 아니요 | 예 |
> | 작업 | 아니요 | 아니요 |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | listavailableoffers | 아니요 | 예 |
> | offers | 아니요 | 예 |
> | offertypes | 아니요 | 아니요 |
> | offertypes / publishers | 아니요 | 아니요 |
> | offertypes / publishers / offers | 예 | 아니요 |
> | offertypes / publishers / offers / plans | 예 | 아니요 |
> | offertypes / publishers / offers / plans / agreements | 예 | 아니요 |
> | offertypes / publishers / offers / plans / configs | 아니요 | 아니요 |
> | offertypes / publishers / offers / plans / configs / importimage | 예 | 아니요 |
> | 작업 | 예 | 아니요 |
> | privategalleryitems | 예 | 아니요 |
> | privatestoreclient | 예 | 아니요 |
> | privatestores | 예 | 아니요 |
> | privatestores / offers | 예 | 아니요 |
> | products | 아니요 | 아니요 |
> | publishers | 예 | 아니요 |
> | publishers / offers | 예 | 아니요 |
> | publishers / offers / amendments | 예 | 아니요 |
> | register | 아니요 | 아니요 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | classicdevservices | 아니요 | 예 |
> | listcommunicationpreference | 아니요 | 예 |
> | 작업 | 아니요 | 예 |
> | updatecommunicationpreference | 아니요 | 아니요 |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | agreements | 아니요 | 아니요 |
> | offertypes | 아니요 | 아니요 |
> | 작업 | 아니요 | 아니요 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 아니요 | 예 |
> | 위치 | 아니요 | 아니요 |
> | locations / checknameavailability | 아니요 | 예 |
> | mediaservices | 아니요 | 예 |
> | mediaservices / accountfilters | 아니요 | 예 |
> | mediaservices / assets | 아니요 | 아니요 |
> | mediaservices / assets / assetfilters | 아니요 | 아니요 |
> | mediaservices / contentkeypolicies | 예 | 아니요 |
> | mediaservices / eventgridfilters | 예 | 아니요 |
> | mediaservices / liveeventoperations | 예 | 아니요 |
> | mediaservices / liveevents | 예 | 아니요 |
> | mediaservices / liveevents / liveoutputs | 예 | 아니요 |
> | mediaservices / liveoutputoperations | 아니요 | 아니요 |
> | mediaservices / streamingendpointoperations | 아니요 | 아니요 |
> | mediaservices / streamingendpoints | 아니요 | 아니요 |
> | mediaservices / streaminglocators | 예 | 예 |
> | mediaservices / streamingpolicies | 예 | 예 |
> | mediaservices / transforms | 예 | 예 |
> | mediaservices / transforms / jobs | 예 | 예 |
> | 작업 | 예 | 예 |

## <a name="microsoftmicroservices4spring"></a>MICROSOFT. MICROSERVICES4SPRING

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | appclusters | 예 | 아니요 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | 예 | 아니요 |
> | 위치 | 예 | 아니요 |
> | locations / assessmentoptions | 예 | 아니요 |
> | locations / checknameavailability | 예 | 아니요 |
> | migrateprojects | 예 | 아니요 |
> | movecollections | 예 | 아니요 |
> | 작업 | 예 | 아니요 |
> | projects | 아니요 | 아니요 |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | 아니요 | 예 |
> | 위치 | 아니요 | 예 |
> | locations / checknameavailability | 아니요 | 예 |
> | objectunderstandingaccounts | 아니요 | 예 |
> | 작업 | 아니요 | 아니요 |
> | remoterenderingaccounts | 아니요 | 아니요 |
> | spatialanchorsaccounts | 아니요 | 예 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | netappaccounts | 예 | 예 |
> | netappaccounts / backuppolicies | 아니요 | 아니요 |
> | netappaccounts / capacitypools | 예 | 예 |
> | netappaccounts / capacitypools / volumes | 예 | 예 |
> | netappaccounts / capacitypools / volumes / mounttargets | 예 | 예 |
> | netappaccounts / capacitypools / volumes / snapshots | 예 | 예 |
> | 작업 | 예 | 예 |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgatewayavailablerequestheaders | 예 | 아니요 |
> | applicationgatewayavailableresponseheaders | 예 | 아니요 |
> | applicationgatewayavailableservervariables | 예 | 아니요 |
> | applicationgatewayavailablessloptions | 예 | 아니요 |
> | applicationgatewayavailablewafrulesets | 예 | 아니요 |
> | applicationgateways | 예 | 아니요 |
> | applicationgatewaywebapplicationfirewallpolicies | 예 | 아니요 |
> | applicationsecuritygroups | 예 | 아니요 |
> | azurefirewallfqdntags | 예 | 아니요 |
> | azurefirewalls | 아니요 | 아니요 |
> | bastionhosts | 예 | 아니요 |
> | bgpservicecommunities | 예 | 아니요 |
> | checkfrontdoornameavailability | 예 | 아니요 |
> | checktrafficmanagernameavailability | 예 | 아니요 |
> | connections | 예 | 아니요 |
> | ddoscustompolicies | 예 | 아니요 |
> | ddosprotectionplans | 아니요 | 아니요 |
> | dnsoperationresults | 아니요 | 아니요 |
> | dnsoperationstatuses | 아니요 | 아니요 |
> | dnszones | 아니요 | 예 |
> | dnszones / a | 아니요 | 예 |
> | dnszones / aaaa | 아니요 | 예 |
> | dnszones / all | 아니요 | 예 |
> | dnszones / caa | 아니요 | 예 |
> | dnszones / cname | 아니요 | 예 |
> | dnszones / mx | 아니요 | 예 |
> | dnszones / ns | 아니요 | 예 |
> | dnszones / ptr | 아니요 | 예 |
> | dnszones / recordsets | 아니요 | 예 |
> | dnszones / soa | 아니요 | 예 |
> | dnszones / srv | 아니요 | 예 |
> | dnszones / txt | 아니요 | 예 |
> | expressroutecircuits | 아니요 | 예 |
> | expressroutegateways | 아니요 | 예 |
> | expressrouteserviceproviders | 아니요 | 예 |
> | firewallpolicies | 아니요 | 예 |
> | frontdooroperationresults | 아니요 | 예 |
> | frontdoors | 아니요 | 예 |
> | frontdoors / frontendendpoints | 아니요 | 예 |
> | frontdoorwebapplicationfirewallmanagedrulesets | 아니요 | 예 |
> | frontdoorwebapplicationfirewallpolicies | 아니요 | 예 |
> | getdnsresourcereference | 아니요 | 예 |
> | internalnotify | 아니요 | 아니요 |
> | ipallocations | 아니요 | 예 |
> | ipgroups | 아니요 | 아니요 |
> | loadbalancers | 예 - 기본 SKU<br>아니요 - 표준 SKU | 예 - 기본 SKU<br>아니요 - 표준 SKU |
> | localnetworkgateways | 예 | 아니요 |
> | 위치 | 아니요 | 아니요 |
> | locations / autoapprovedprivatelinkservices | 예 | 아니요 |
> | locations / availabledelegations | 예 | 아니요 |
> | locations / availableprivateendpointtypes | 예 | 아니요 |
> | locations / availableservicealiases | 예 | 아니요 |
> | locations / baremetaltenants | 예 | 아니요 |
> | locations / batchnotifyprivateendpointsforresourcemove | 예 | 아니요 |
> | locations / batchvalidateprivateendpointsforresourcemove | 예 | 아니요 |
> | locations / checkacceleratednetworkingsupport | 예 | 아니요 |
> | locations / checkdnsnameavailability | 아니요 | 아니요 |
> | locations / checkprivatelinkservicevisibility | 예 | 아니요 |
> | locations / commitinternalazurenetworkmanagerconfiguration | 예 | 아니요 |
> | locations / effectiveresourceownership | 예 | 아니요 |
> | locations / nfvoperationresults | 예 | 아니요 |
> | locations / nfvoperations | 예 | 아니요 |
> | locations / operationresults | 예 | 아니요 |
> | locations / operations | 예 | 아니요 |
> | locations / servicetags | 예 | 아니요 |
> | locations / setresourceownership | 예 | 아니요 |
> | locations / supportedvirtualmachinesizes | 아니요 | 아니요 |
> | locations / usages | 예 | 아니요 |
> | locations / validateresourceownership | 예 | 아니요 |
> | locations / virtualnetworkavailableendpointservices | 아니요 | 아니요 |
> | natgateways | 예 | 아니요 |
> | networkexperimentprofiles | 아니요 | 아니요 |
> | networkintentpolicies | 아니요 | 아니요 |
> | networkinterfaces | 아니요 | 예 |
> | networkprofiles | 아니요 | 아니요 |
> | networksecuritygroups | 아니요 | 아니요 |
> | networkwatchers | 예 | 아니요 |
> | networkwatchers / connectionmonitors | 예 | 예 |
> | networkwatchers / flowlogs | 예 | 아니요 |
> | networkwatchers / pingmeshes | 예 | 예 |
> | 작업 | 아니요 | 예 |
> | p2svpngateways | 아니요 | 예 |
> | privatednsoperationresults | 아니요 | 아니요 |
> | privatednsoperationstatuses | 아니요 | 아니요 |
> | privatednszones | 아니요 | 예 |
> | privatednszones / a | 아니요 | 아니요 |
> | privatednszones / aaaa | 아니요 | 예 |
> | privatednszones / all | 아니요 | 예 |
> | privatednszones / cname | 아니요 | 예 |
> | privatednszones / mx | 아니요 | 예 |
> | privatednszones / ptr | 아니요 | 아니요 |
> | privatednszones / soa | 아니요 | 아니요 |
> | privatednszones / srv | 예 | 예 |
> | privatednszones / txt | 예 | 예 |
> | privatednszones / virtualnetworklinks | 아니요 | 아니요 |
> | privatednszonesinternal | 아니요 | 아니요 |
> | privateendpointredirectmaps | 예 | 예 |
> | privateendpoints | 예 | 예 |
> | privatelinkservices | 아니요 | 아니요 |
> | publicipaddresses | 예 - 기본 SKU<br>아니요 - 표준 SKU | 예 - 기본 SKU<br>아니요 - 표준 SKU |
> | publicipprefixes | 아니요 | 아니요 |
> | routefilters | 아니요 | 아니요 |
> | routetables | 아니요 | 아니요 |
> | securitypartnerproviders | 예 | 예 |
> | serviceendpointpolicies | 아니요 | 아니요 |
> | trafficmanagergeographichierarchies | 아니요 | 아니요 |
> | trafficmanagerprofiles | 아니요 | 아니요 |
> | trafficmanagerprofiles / heatmaps | 아니요 | 예 |
> | trafficmanagerusermetricskeys | 아니요 | 예 |
> | virtualhubs | 아니요 | 예 |
> | virtualnetworkgateways | 아니요 | 예 |
> | virtualnetworks | 아니요 | 예 |
> | virtualnetworktaps | 아니요 | 예 |
> | virtualrouters | 아니요 | 예 |
> | virtualwans | 아니요 | 아니요 |
> | vpngateways(Virtual WAN) | 아니요 | 예 |
> | vpnserverconfigurations | 아니요 | 예 |
> | vpnsites(Virtual WAN) | 아니요 | 예 |

> [!IMPORTANT]
> [네트워킹 이동 지침](./move-limitations/networking-move-limitations.md)을 참조하세요.

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 예 | 아니요 |
> | checknamespaceavailability | 예 | 아니요 |
> | 네임스페이스 | 아니요 | 아니요 |
> | namespaces / notificationhubs | 아니요 | 아니요 |
> | operationresults | 예 | 예 |
> | 작업 | 아니요 | 아니요 |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | osnamespaces | 아니요 | 아니요 |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hypervsites | 아니요 | 아니요 |
> | importsites | 예 | 아니요 |
> | 작업 | 예 | 아니요 |
> | serversites | 예 | 아니요 |
> | vmwaresites | 예 | 아니요 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | 예 | 예 |
> | deletedworkspaces | 아니요 | 예 |
> | linktargets | 아니요 | 예 |
> | 위치 | 아니요 | 예 |
> | locations / operationstatuses | 아니요 | 아니요 |
> | 작업 | 아니요 | 아니요 |
> | storageinsightconfigs | 예 | 아니요 |
> | workspaces | 예 | 예 |
> | workspaces / datasources | 예 | 아니요 |
> | workspaces / linkedservices | 예 | 아니요 |
> | workspaces / linkedstorageaccounts | 예 | 아니요 |
> | workspaces / metadata | 예 | 아니요 |
> | workspaces / query | 아니요 | 아니요 |
> | workspaces / scopedprivatelinkproxies | 예 | 예 |

> [!IMPORTANT]
> 새 구독으로 이동하는 경우 [구독 할당량](azure-subscription-service-limits.md#azure-monitor-limits)을 초과하지 않아야 합니다.
>
> 연결된 자동화 계정이 있는 작업 영역은 이동할 수 없습니다. 이동 작업을 시작하기 전에 자동화 계정의 연결을 해제해야 합니다.

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managementassociations | 예 | 아니요 |
> | managementconfigurations | 예 | 아니요 |
> | 작업 | 예 | 아니요 |
> | solutions | 예 | 아니요 |
> | 뷰 | 아니요 | 아니요 |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checkserviceprovideravailability | 아니요 | 아니요 |
> | legacypeerings | 예 | 예 |
> | 작업 | 예 | 예 |
> | peerasns | 아니요 | 아니요 |
> | peeringlocations | 아니요 | 아니요 |
> | peerings | 아니요 | 아니요 |
> | peeringservicecountries | 아니요 | 예 |
> | peeringservicelocations | 아니요 | 예 |
> | peeringserviceproviders | 아니요 | 예 |
> | peeringservices | 아니요 | 예 |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | asyncoperationresults | 아니요 | 아니요 |
> | 작업 | 아니요 | 예 |
> | policyevents | 아니요 | 예 |
> | policystates | 아니요 | 예 |
> | policytrackedresources | 아니요 | 아니요 |
> | remediations | 아니요 | 아니요 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | consoles | 아니요 | 아니요 |
> | dashboards | 예 | 아니요 |
> | 위치 | 예 | 아니요 |
> | locations / consoles | 예 | 아니요 |
> | locations / usersettings | 예 | 아니요 |
> | 작업 | 아니요 | 아니요 |
> | usersettings | 예 | 예 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 위치 | 예 | 아니요 |
> | locations / checknameavailability | 예 | 아니요 |
> | 작업 | 아니요 | 아니요 |
> | privatelinkservicesforpowerbi | 예 | 예 |
> | privatelinkservicesforpowerbi/operationresults | 예 | 예 |
> | tenants | 예 | 예 |
> | workspacecollections | 예 | 예 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | capacities | 예 | 예 |
> | 위치 | 예 | 아니요 |
> | locations / checknameavailability | 예 | 아니요 |
> | locations / operationresults | 예 | 아니요 |
> | locations / operationstatuses | 예 | 아니요 |
> | 작업 | 예 | 아니요 |

## <a name="microsoftpowerplatform"></a>Microsoft PowerPlatform

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 작업 | 아니요 | 예 |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |
> | checknameavailability | 아니요 | 아니요 |
> | 작업 | 아니요 | 아니요 |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availableaccounts | 아니요 | 예 |
> | providerregistrations | 아니요 | 예 |
> | providerregistrations/defaultrollouts | 아니요 | 예 |
> | providerregistrations / resourcetyperegistrations | 아니요 | 예 |
> | rollouts | 아니요 | 아니요 |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 위치 | 아니요 | 아니요 |
> | locations / operationstatuses | 아니요 | 예 |
> | 작업 | 아니요 | 예 |
> | workspaces | 아니요 | 예 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | backupprotecteditems | 예 | 예 |
> | 위치 | 예 | 예 |
> | locations / allocatedstamp | 예 | 예 |
> | locations / allocatestamp | 아니요 | 아니요 |
> | locations / backupaadproperties | 아니요 | 예 |
> | locations / backupcrossregionrestore | 아니요 | 아니요 |
> | locations / backupcrrjob | 아니요 | 예 |
> | locations / backupcrrjobs | 아니요 | 예 |
> | locations / backupcrroperationresults | 아니요 | 예 |
> | locations / backupcrroperationsstatus | 아니요 | 예 |
> | locations / backupprevalidateprotection | 아니요 | 예 |
> | locations / backupstatus | 아니요 | 아니요 |
> | locations / backupvalidatefeatures | 아니요 | 아니요 |
> | locations / checknameavailability | 예 | 아니요 |
> | 작업 | 예 | 아니요 |
> | replicationeligibilityresults | 예 | 아니요 |
> | vaults | 예 | 예 |

> [!IMPORTANT]
> [Recovery Services 이동 지침](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)을 참조하세요.

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 위치 | 예 | 예 |
> | locations / operationresults | 예 | 예 |
> | locations / operationsstatus | 아니요 | 아니요 |
> | openshiftclusters | 아니요 | 예 |
> | 작업 | 아니요 | 예 |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 예 | 예 |
> | 네임스페이스 | 예 | 예 |
> | namespaces / authorizationrules | 예 | 예 |
> | namespaces / hybridconnections | 예 | 예 |
> | namespaces / hybridconnections / authorizationrules | 예 | 예 |
> | namespaces / privateendpointconnections | 예 | 예 |
> | namespaces / wcfrelays | 예 | 예 |
> | namespaces / wcfrelays / authorizationrules | 예 | 예 |
> | 작업 | 예 | 예 |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 작업 | 예 | 아니요 |
> | 쿼리 | 예 | 예 |
> | resourcechangedetails | 예 | 아니요 |
> | resourcechanges | 예 | 아니요 |
> | 리소스 | 예 | 아니요 |
> | resourceshistory | 예 | 아니요 |
> | subscriptionsstatus | 예 | 아니요 |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availabilitystatuses | 아니요 | 예 |
> | childavailabilitystatuses | 아니요 | 예 |
> | childresources | 아니요 | 예 |
> | emergingissues | 아니요 | 예 |
> | events | 아니요 | 예 |
> | metadata | 아니요 | 예 |
> | 알림 | 아니요 | 아니요 |
> | 작업 | 아니요 | 아니요 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | calculatetemplatehash | 아니요 | 예 |
> | checkpolicycompliance | 아니요 | 예 |
> | checkresourcename | 아니요 | 예 |
> | 배포 | 아니요 | 예 |
> | deployments / operations | 아니요 | 아니요 |
> | deploymentscripts | 아니요 | 아니요 |
> | deploymentscripts / logs | 예 | 아니요 |
> | 링크 | 예 | 아니요 |
> | 위치 | 예 | 아니요 |
> | locations / deploymentscriptoperationresults | 예 | 아니요 |
> | notifyresourcejobs | 아니요 | 아니요 |
> | operationresults | 아니요 | 아니요 |
> | 작업 | 아니요 | 아니요 |
> | providers | 예 | 아니요 |
> | resourcegroups | 예 | 아니요 |
> | 리소스 | 예 | 아니요 |
> | 구독 | 예 | 아니요 |
> | subscriptions / locations | 예 | 아니요 |
> | subscriptions / operationresults | 예 | 아니요 |
> | subscriptions / providers | 예 | 아니요 |
> | subscriptions / resourcegroups | 예 | 아니요 |
> | subscriptions / resourcegroups / resources | 아니요 | 아니요 |
> | subscriptions / resources | 예 | 아니요 |
> | subscriptions / tagnames | 예 | 아니요 |
> | subscriptions / tagnames / tagvalues | 예 | 아니요 |
> | tags | 예 | 아니요 |
> | templatespecs | 예 | 아니요 |
> | templatespecs / versions | 예 | 아니요 |
> | tenants | 예 | 아니요 |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 애플리케이션 | 아니요 | 예 |
> | checkmoderneligibility | 아니요 | 예 |
> | checknameavailability | 아니요 | 예 |
> | operationresults | 아니요 | 예 |
> | 작업 | 아니요 | 예 |
> | saasresources | 아니요 | 예 |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 예 | 예 |
> | checkservicenameavailability | 예 | 예 |
> | 작업 | 예 | 예 |
> | resourcehealthmetadata | 예 | 예 |
> | searchservices | 예 | 예 |

> [!IMPORTANT]
> 다른 지역에 있는 여러 Search 리소스를 한 번의 작업으로 이동할 수 없습니다. 대신 별도 작업으로 이동합니다.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | 예 | 예 |
> | advancedthreatprotectionsettings | 예 | 예 |
> | 경고 | 예 | 예 |
> | alertssuppressionrules | 예 | 예 |
> | allowedconnections | 예 | 예 |
> | applicationwhitelistings | 예 | 예 |
> | assessmentmetadata | 예 | 예 |
> | assessments | 예 | 예 |
> | autodismissalertsrules | 예 | 예 |
> | automations | 예 | 예 |
> | autoprovisioningsettings | 예 | 예 |
> | complianceresults | 예 | 예 |
> | compliances | 예 | 예 |
> | datacollectionagents | 예 | 예 |
> | devicesecuritygroups | 예 | 예 |
> | discoveredsecuritysolutions | 예 | 예 |
> | externalsecuritysolutions | 예 | 예 |
> | informationprotectionpolicies | 예 | 예 |
> | iotsecuritysolutions | 아니요 | 아니요 |
> | iotsecuritysolutions / analyticsmodels | 아니요 | 예 |
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | 아니요 | 예 |
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | 아니요 | 예 |
> | iotsecuritysolutions/i이상 경고 | 아니요 | 예 |
> | iotsecuritysolutions/iotsecuritysolutions | 아니요 | 예 |
> | jitnetworkaccesspolicies | 아니요 | 예 |
> | 위치 | 아니요 | 예 |
> | locations / alerts | 아니요 | 예 |
> | locations / allowedconnections | 아니요 | 예 |
> | locations / applicationwhitelistings | 아니요 | 예 |
> | locations / discoveredsecuritysolutions | 아니요 | 예 |
> | locations / externalsecuritysolutions | 아니요 | 예 |
> | locations / jitnetworkaccesspolicies | 아니요 | 예 |
> | locations / securitysolutions | 아니요 | 예 |
> | locations / securitysolutionsreferencedata | 아니요 | 아니요 |
> | locations / tasks | 아니요 | 아니요 |
> | locations / topologies | 예 | 아니요 |
> | 작업 | 예 | 아니요 |
> | 정책 | 예 | 아니요 |
> | pricings | 예 | 아니요 |
> | regulatorycompliancestandards | 아니요 | 아니요 |
> | regulatorycompliancestandards / regulatorycompliancecontrols | 예 | 예 |
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | 예 | 예 |
> | securescorecontroldefinitions | 아니요 | 아니요 |
> | securescorecontrols | 아니요 | 예 |
> | securescores | 아니요 | 예 |
> | securescores / securescorecontrols | 아니요 | 예 |
> | securitycontacts | 아니요 | 예 |
> | securitysolutions | 아니요 | 아니요 |
> | securitysolutionsreferencedata | 아니요 | 예 |
> | securitystatuses | 아니요 | 예 |
> | securitystatusessummaries | 아니요 | 예 |
> | servervulnerabilityassessments | 아니요 | 예 |
> | 설정 | 아니요 | 예 |
> | subassessments | 아니요 | 예 |
> | tasks | 아니요 | 예 |
> | topologies | 아니요 | 예 |
> | workspacesettings | 아니요 | 예 |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | aggregations | 예 | 예 |
> | alertrules | 예 | 예 |
> | alertruletemplates | 예 | 예 |
> | automationrules | 예 | 예 |
> | bookmarks | 아니요 | 아니요 |
> | cases | 아니요 | 예 |
> | dataconnectors | 아니요 | 아니요 |
> | dataconnectorscheckrequirements | 아니요 | 예 |
> | 엔터티 | 아니요 | 예 |
> | entityqueries | 아니요 | 예 |
> | incidents | 아니요 | 예 |
> | officeconsents | 아니요 | 예 |
> | 작업 | 아니요 | 예 |
> | 설정 | 아니요 | 예 |
> | threatintelligence | 아니요 | 예 |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | consoleservices | 예 | 예 |
> | 위치 | 예 | 예 |
> | locations / consoleservices | 예 | 예 |
> | 작업 | 예 | 예 |

## <a name="microsoftservermanagement"></a>MICROSOFT. SERVERMANAGEMENT

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | gateways | 아니요 | 아니요 |
> | nodes | 아니요 | 아니요 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 아니요 | 아니요 |
> | checknamespaceavailability | 예 | 아니요 |
> | 위치 | 예 | 아니요 |
> | locations / deletevirtualnetworkorsubnets | 예 | 아니요 |
> | 네임스페이스 | 예 | 아니요 |
> | namespaces / authorizationrules | 예 | 아니요 |
> | namespaces / disasterrecoveryconfigs | 예 | 아니요 |
> | namespaces / disasterrecoveryconfigs / checknameavailability | 아니요 | 아니요 |
> | namespaces / eventgridfilters | 예 | 아니요 |
> | namespaces / networkrulesets | 아니요 | 아니요 |
> | namespaces / queues | 아니요 | 아니요 |
> | namespaces / queues / authorizationrules | 아니요 | 아니요 |
> | namespaces / topics | 예 | 예 |
> | namespaces / topics / authorizationrules | 예 | 예 |
> | namespaces / topics / subscriptions | 예 | 예 |
> | namespaces / topics / subscriptions / rules | 아니요 | 아니요 |
> | 작업 | 아니요 | 예 |
> | premiummessagingregions | 아니요 | 예 |
> | sku | 아니요 | 예 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 애플리케이션 | 예 | 예 |
> | clusters | 예 | 예 |
> | clusters / applications | 아니요 | 아니요 |
> | containergroups | 예 | 예 |
> | containergroupsets | 아니요 | 아니요 |
> | edgeclusters | 아니요 | 예 |
> | 위치 | 아니요 | 예 |
> | locations / clusterversions | 아니요 | 아니요 |
> | locations / environments | 아니요 | 아니요 |
> | locations / operationresults | 예 | 아니요 |
> | locations / operations | 예 | 아니요 |
> | managedclusters | 예 | 아니요 |
> | networks | 예 | 아니요 |
> | 작업 | 예 | 아니요 |
> | secretstores | 예 | 아니요 |
> | volumes | 아니요 | 아니요 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 애플리케이션 | 예 | 아니요 |
> | containergroups | 아니요 | 아니요 |
> | gateways | 예 | 아니요 |
> | 위치 | 예 | 아니요 |
> | locations / applicationoperations | 예 | 아니요 |
> | locations / gatewayoperations | 예 | 아니요 |
> | locations / networkoperations | 예 | 아니요 |
> | locations / secretoperations | 예 | 아니요 |
> | locations / volumeoperations | 예 | 아니요 |
> | networks | 예 | 아니요 |
> | 작업 | 예 | 아니요 |
> | secrets | 예 | 아니요 |
> | volumes | 예 | 아니요 |

## <a name="microsoftservices"></a>MICROSOFT. 서비스

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | rollouts | 아니요 | 예 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 위치 | 예 | 예 |
> | locations / checknameavailability | 예 | 예 |
> | locations / operationresults | 예 | 예 |
> | locations / operationstatuses | 예 | 예 |
> | locations / usages | 예 | 예 |
> | 작업 | 예 | 예 |
> | signalr | 예 | 예 |
> | signalr / eventgridfilters | 예 | 예 |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hybridusebenefits | 예 | 아니요 |
> | 작업 | 예 | 아니요 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | 아니요 | 예 |
> | 애플리케이션 | 아니요 | 예 |
> | jitrequests | 아니요 | 예 |
> | 위치 | 아니요 | 예 |
> | locations / operationstatuses | 아니요 | 예 |
> | 작업 | 아니요 | 예 |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 예 | 예 |
> | instancepools | 예 | 예 |
> | 위치 | 예 | 예 |
> | locations / administratorazureasyncoperation | 예 | 예 |
> | locations / administratoroperationresults | 예 | 예 |
> | locations / auditingsettingsazureasyncoperation | 예 | 예 |
> | locations / auditingsettingsoperationresults | 예 | 예 |
> | locations / capabilities | 예 | 예 |
> | locations / databaseazureasyncoperation | 예 | 예 |
> | locations / databaseoperationresults | 예 | 예 |
> | locations / databaserestoreazureasyncoperation | 예 | 예 |
> | locations / deletevirtualnetworkorsubnets | 예 | 예 |
> | locations / deletevirtualnetworkorsubnetsazureasyncoperation | 예 | 예 |
> | locations / deletevirtualnetworkorsubnetsoperationresults | 예 | 예 |
> | locations / dnsaliasasyncoperation | 예 | 예 |
> | locations / dnsaliasoperationresults | 예 | 예 |
> | locations / elasticpoolazureasyncoperation | 예 | 예 |
> | locations / elasticpooloperationresults | 예 | 예 |
> | locations / encryptionprotectorazureasyncoperation | 예 | 예 |
> | locations / encryptionprotectoroperationresults | 예 | 예 |
> | locations / extendedauditingsettingsazureasyncoperation | 예 | 예 |
> | locations / extendedauditingsettingsoperationresults | 예 | 예 |
> | locations / failovergroupazureasyncoperation | 예 | 예 |
> | locations / failovergroupoperationresults | 예 | 예 |
> | locations / firewallrulesazureasyncoperation | 예 | 예 |
> | locations / firewallrulesoperationresults | 예 | 예 |
> | locations / instancefailovergroupazureasyncoperation | 예 | 예 |
> | locations / instancefailovergroupoperationresults | 예 | 예 |
> | locations / instancefailovergroups | 예 | 예 |
> | locations / instancepoolazureasyncoperation | 예 | 예 |
> | locations / instancepooloperationresults | 예 | 예 |
> | locations / jobagentazureasyncoperation | 예 | 예 |
> | locations / jobagentoperationresults | 예 | 예 |
> | locations / longtermretentionbackupazureasyncoperation | 예 | 예 |
> | locations / longtermretentionbackupoperationresults | 예 | 예 |
> | locations / longtermretentionbackups | 예 | 예 |
> | locations / longtermretentionmanagedinstancebackupazureasyncoperation | 예 | 예 |
> | locations / longtermretentionmanagedinstancebackupoperationresults | 예 | 예 |
> | locations / longtermretentionmanagedinstancebackups | 예 | 예 |
> | locations / longtermretentionmanagedinstances | 예 | 예 |
> | locations / longtermretentionpolicyazureasyncoperation | 예 | 예 |
> | locations / longtermretentionpolicyoperationresults | 예 | 예 |
> | locations / longtermretentionservers | 예 | 예 |
> | locations / manageddatabaseazureasyncoperation | 예 | 예 |
> | locations / manageddatabasecompleterestoreazureasyncoperation | 예 | 예 |
> | locations / manageddatabasecompleterestoreoperationresults | 예 | 예 |
> | locations / manageddatabaseoperationresults | 예 | 예 |
> | locations / manageddatabaserestoreazureasyncoperation | 예 | 예 |
> | locations / manageddatabaserestoreoperationresults | 예 | 예 |
> | locations / managedinstanceazureasyncoperation | 예 | 예 |
> | locations / managedinstanceencryptionprotectorazureasyncoperation | 예 | 예 |
> | locations / managedinstanceencryptionprotectoroperationresults | 예 | 예 |
> | locations / managedinstancekeyazureasyncoperation | 예 | 예 |
> | locations / managedinstancekeyoperationresults | 예 | 예 |
> | locations / managedinstancelongtermretentionpolicyazureasyncoperation | 예 | 예 |
> | locations / managedinstancelongtermretentionpolicyoperationresults | 예 | 예 |
> | locations / managedinstanceoperationresults | 예 | 예 |
> | locations / managedinstancetdecertazureasyncoperation | 예 | 예 |
> | locations / managedinstancetdecertoperationresults | 예 | 예 |
> | locations / managedserversecurityalertpoliciesazureasyncoperation | 예 | 예 |
> | locations / managedserversecurityalertpoliciesoperationresults | 예 | 예 |
> | locations / managedshorttermretentionpolicyazureasyncoperation | 예 | 예 |
> | locations / managedshorttermretentionpolicyoperationresults | 예 | 예 |
> | locations / notifyazureasyncoperation | 예 | 예 |
> | locations / privateendpointconnectionazureasyncoperation | 예 | 예 |
> | locations / privateendpointconnectionoperationresults | 아니요 | 아니요 |
> | locations / privateendpointconnectionproxyazureasyncoperation | 예 | 예 |
> | locations / privateendpointconnectionproxyoperationresults | 예 | 예 |
> | locations / securityalertpoliciesazureasyncoperation | 예 | 예 |
> | locations / securityalertpoliciesoperationresults | 예 | 예 |
> | locations / serveradministratorazureasyncoperation | 예 | 예 |
> | locations / serveradministratoroperationresults | 예 | 예 |
> | locations / serverazureasyncoperation | 예 | 예 |
> | locations / serverkeyazureasyncoperation | 예 | 예 |
> | locations / serverkeyoperationresults | 예 | 예 |
> | locations / serveroperationresults | 아니요 | 아니요 |
> | locations / shorttermretentionpolicyazureasyncoperation | 예 | 예 |
> | locations / shorttermretentionpolicyoperationresults | 예 | 예 |
> | locations / syncagentoperationresults | 예 | 예 |
> | locations / syncdatabaseids | 예 | 예 |
> | locations / syncgroupoperationresults | 예 | 예 |
> | locations / syncmemberoperationresults | 예 | 예 |
> | locations / tdecertazureasyncoperation | 예 | 예 |
> | locations / tdecertoperationresults | 예 | 예 |
> | locations / usages | 예 | 예 |
> | locations / virtualclusterazureasyncoperation | 예 | 예 |
> | locations / virtualclusteroperationresults | 예 | 예 |
> | locations / virtualnetworkrulesazureasyncoperation | 예 | 예 |
> | locations / virtualnetworkrulesoperationresults | 예 | 예 |
> | locations / vulnerabilityassessmentscanazureasyncoperation | 예 | 예 |
> | locations / vulnerabilityassessmentscanoperationresults | 예 | 예 |
> | managedinstances | 예 | 예 |
> | managedinstances / administrators | 예 | 예 |
> | managedinstances / databases | 예 | 예 |
> | managedinstances / databases / backuplongtermretentionpolicies | 예 | 예 |
> | managedinstances / databases / vulnerabilityassessments | 예 | 예 |
> | managedinstances / metricdefinitions | 예 | 예 |
> | managedinstances / metrics | 예 | 예 |
> | managedinstances / recoverabledatabases | 예 | 예 |
> | managedinstances / tdecertificates | 예 | 예 |
> | managedinstances / vulnerabilityassessments | 예 | 예 |
> | 작업 | 예 | 예 |
> | servers | 예 | 예 |
> | servers / administratoroperationresults | 예 | 예 |
> | servers / administrators | 예 | 예 |
> | servers / advisors | 예 | 예 |
> | servers / aggregateddatabasemetrics | 아니요 | 아니요 |
> | servers / auditingsettings | 예 | 예 |
> | servers / automatictuning | 예 | 예 |
> | servers / communicationlinks | 예 | 예 |
> | servers / databases | 예 | 예 |
> | servers / databases / advisors | 예 | 예 |
> | servers / databases / auditingsettings | 예 | 예 |
> | servers / databases / auditrecords | 예 | 예 |
> | servers / databases / automatictuning | 예 | 예 |
> | servers / databases / backuplongtermretentionpolicies | 아니요 | 아니요 |
> | servers / databases / backupshorttermretentionpolicies | 아니요 | 아니요 |
> | servers / databases / datamaskingpolicies | 예 | 예 |
> | servers / databases / datamaskingpolicies / rules | 예 | 예 |
> | servers / databases / extensions | 예 | 예 |
> | servers / databases / geobackuppolicies | 예 | 예 |
> | servers / databases / metricdefinitions | 예 | 예 |
> | servers / databases / metrics | 예 | 예 |
> | servers / databases / recommendedsensitivitylabels | 예 | 예 |
> | servers / databases / securityalertpolicies | 예 | 예 |
> | servers / databases / syncgroups | 예 | 예 |
> | servers / databases / syncgroups / syncmembers | 예 | 예 |
> | servers / databases / topqueries | 예 | 예 |
> | servers / databases / topqueries / querytext | 예 | 예 |
> | servers / databases / transparentdataencryption | 예 | 예 |
> | servers / databases / vulnerabilityassessment | 예 | 예 |
> | servers / databases / vulnerabilityassessments | 예 | 예 |
> | servers / databases / vulnerabilityassessmentscans | 아니요 | 아니요 |
> | servers / databases / vulnerabilityassessmentsettings | 아니요 | 아니요 |
> | servers / databases / workloadgroups | 아니요 | 아니요 |
> | servers / databasesecuritypolicies | 아니요 | 예 |
> | servers / disasterrecoveryconfiguration | 아니요 | 예 |
> | servers / dnsaliases | 아니요 | 예 |
> | servers / elasticpoolestimates | 아니요 | 예 |
> | servers / elasticpools | 아니요 | 예 |
> | servers / elasticpools / advisors | 아니요 | 예 |
> | servers / elasticpools / metricdefinitions | 아니요 | 아니요 |
> | servers / elasticpools / metrics | 아니요 | 예 |
> | servers / encryptionprotector | 아니요 | 아니요 |
> | servers / extendedauditingsettings | 아니요 | 아니요 |
> | servers / failovergroups | 아니요 | 아니요 |
> | servers / import | 예 | 아니요 |
> | servers / importexportoperationresults | 아니요 | 아니요 |
> | servers / jobaccounts | 예 | 예 |
> | servers / jobagents | 예 | 예 |
> | servers / jobagents / jobs | 예 | 예 |
> | servers / jobagents / jobs / executions | 예 | 예 |
> | servers / jobagents / jobs / steps | 예 | 예 |
> | servers / keys | 예 | 예 |
> | servers / operationresults | 예 | 예 |
> | servers / recommendedelasticpools | 아니요 | 아니요 |
> | servers / recoverabledatabases | 예 | 예 |
> | servers / restorabledroppeddatabases | 예 | 예 |
> | servers / securityalertpolicies | 예 | 예 |
> | servers / serviceobjectives | 예 | 예 |
> | servers / syncagents | 예 | 예 |
> | servers / tdecertificates | 예 | 예 |
> | servers / usages | 예 | 예 |
> | servers / virtualnetworkrules | 예 | 예 |
> | servers / vulnerabilityassessments | 예 | 예 |
> | virtualclusters | 아니요 | 아니요 |

> [!IMPORTANT]
> 데이터베이스와 서버는 동일한 리소스 그룹에 있어야 합니다. SQL Server를 이동하면 모든 해당 데이터베이스도 함께 이동합니다. 이 동작은 Azure SQL Database 및 Azure SQL Data Warehouse 데이터베이스에 적용됩니다.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 위치 | 아니요 | 예 |
> | locations / availabilitygrouplisteneroperationresults | 아니요 | 예 |
> | locations / operationtypes | 아니요 | 예 |
> | locations / sqlvirtualmachinegroupoperationresults | 아니요 | 예 |
> | locations / sqlvirtualmachineoperationresults | 아니요 | 아니요 |
> | 작업 | 아니요 | 예 |
> | sqlvirtualmachinegroups | 예 | 예 |
> | sqlvirtualmachinegroups / availabilitygrouplisteners | 아니요 | 예 |
> | sqlvirtualmachines | 예 | 예 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 아니요 | 아니요 |
> | 위치 | 아니요 | 예 |
> | locations / asyncoperations | 아니요 | 아니요 |
> | locations / checknameavailability | 아니요 | 아니요 |
> | locations / deletevirtualnetworkorsubnets | 예 | 아니요 |
> | locations / usages | 아니요 | 아니요 |
> | 작업 | 예 | 예 |
> | storageaccounts | 예 | 예 |
> | storageaccounts / blobservices | 아니요 | 아니요 |
> | storageaccounts / fileservices | 아니요 | 예 |
> | storageaccounts / listaccountsas | 아니요 | 예 |
> | storageaccounts / listservicesas | 아니요 | 예 |
> | storageaccounts / queueservices | 아니요 | 예 |
> | storageaccounts / services | 아니요 | 아니요 |
> | storageaccounts / services / metricdefinitions | 아니요 | 아니요 |
> | storageaccounts / tableservices | 아니요 | 아니요 |
> | usages | 예 | 예 |

## <a name="microsoftstoragecache"></a>MICROSOFT. STORAGECACHE

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | caches | 예 | 아니요 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 위치 | 아니요 | 아니요 |
> | locations / checknameavailability | 아니요 | 아니요 |
> | locations / operationresults | 예 | 아니요 |
> | locations / operations | 예 | 아니요 |
> | locations / workflows | 예 | 아니요 |
> | 작업 | 예 | 아니요 |
> | storagesyncservices | 예 | 아니요 |
> | storagesyncservices / registeredservers | 예 | 아니요 |
> | storagesyncservices / syncgroups | 예 | 아니요 |
> | storagesyncservices / syncgroups / cloudendpoints | 예 | 아니요 |
> | storagesyncservices / syncgroups / serverendpoints | 예 | 아니요 |
> | storagesyncservices / workflows | 아니요 | 아니요 |

## <a name="microsoftstoragesyncdev"></a>MICROSOFT. STORAGESYNCDEV

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | 예 | 아니요 |

## <a name="microsoftstoragesyncint"></a>MICROSOFT. STORAGESYNCINT

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | 아니요 | 예 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managers | 예 | 예 |
> | 작업 | 아니요 | 아니요 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | 예 | 예 |
> | 위치 | 아니요 | 아니요 |
> | locations / quotas | 아니요 | 아니요 |
> | 작업 | 예 | 예 |
> | streamingjobs | 예 | 예 |

> [!IMPORTANT]
> 실행 중 상태인 Stream Analytics 작업은 이동할 수 없습니다.

## <a name="microsoftstreamanalyticsexplorer"></a>MICROSOFT. STREAMANALYTICSEXPLORER

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | 아니요 | 아니요 |
> | environments / eventsources | 예 | 아니요 |
> | 인스턴스 | 예 | 아니요 |
> | instances / environments | 아니요 | 아니요 |
> | instances / environments / eventsources | 아니요 | 아니요 |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | cancel | 아니요 | 아니요 |
> | createsubscription | 아니요 | 아니요 |
> | enable | 예 | 아니요 |
> | operationresults | 아니요 | 아니요 |
> | 작업 | 아니요 | 아니요 |
> | 이름 바꾸기 | 예 | 예 |
> | subscriptiondefinitions | 예 | 예 |
> | subscriptionoperations | 예 | 예 |
> | 구독 | 아니요 | 아니요 |

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 예 | 예 |
> | operationresults | 아니요 | 아니요 |
> | 작업 | 아니요 | 예 |
> | operationsstatus | 아니요 | 예 |
> | services | 아니요 | 예 |
> | services / problemclassifications | 아니요 | 예 |
> | supporttickets | 아니요 | 예 |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | 아니요 | 아니요 |
> | 작업 | 예 | 아니요 |
> | privatelinkhubs | 예 | 아니요 |
> | workspaces | 예 | 아니요 |
> | workspaces / bigdatapools | 예 | 아니요 |
> | workspaces / operationresults | 예 | 아니요 |
> | workspaces / operationstatuses | 예 | 아니요 |
> | workspaces / sqlpools | 예 | 아니요 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | 아니요 | 아니요 |
> | environments / accesspolicies | 아니요 | 아니요 |
> | environments / eventsources | 예 | 아니요 |
> | environments / referencedatasets | 예 | 아니요 |
> | 작업 | 예 | 아니요 |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | stores | 아니요 | 예 |
> | stores / accesspolicies | 아니요 | 예 |
> | stores / services | 아니요 | 예 |
> | stores / services / tokens | 아니요 | 예 |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | imagetemplates | 예 | 예 |
> | imagetemplates/runoutputs | 예 | 예 |
> | 위치 | 아니요 | 아니요 |
> | locations / operations | 아니요 | 예 |
> | 작업 | 아니요 | 예 |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | 예 | 예 |
> | account / extension | 예 | 예 |
> | account / project | 아니요 | 아니요 |
> | checknameavailability | 아니요 | 예 |
> | 작업 | 아니요 | 예 |

> [!IMPORTANT]
> Azure DevOps 구독을 변경하려면 [청구에 사용되는 Azure 구독 변경](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)을 참조하세요.

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | arczones | 아니요 | 아니요 |
> | 위치 | 예 | 예 |
> | locations / operationstatuses | 예 | 예 |
> | 작업 | 예 | 아니요 |
> | resourcepools | 예 | 예 |
> | vcenters | 아니요 | 아니요 |
> | virtualmachines | 아니요 | 아니요 |
> | virtualmachinetemplates | 아니요 | 아니요 |
> | virtualnetworks | 예 | 예 |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | 예 | 아니요 |
> | dedicatedcloudservices | 예 | 아니요 |
> | 위치 | 예 | 아니요 |
> | locations / availabilities | 예 | 아니요 |
> | locations / operationresults | 예 | 아니요 |
> | locations / privateclouds | 예 | 아니요 |
> | locations / privateclouds / resourcepools | 예 | 아니요 |
> | locations / privateclouds / virtualmachinetemplates | 예 | 아니요 |
> | locations / privateclouds / virtualnetworks | 아니요 | 아니요 |
> | locations / usages | 예 | 아니요 |
> | 작업 | 예 | 아니요 |
> | virtualmachines | 예 | 아니요 |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | devices | 아니요 | 예 |
> | 위치 | 아니요 | 예 |
> | locations / operationstatuses | 아니요 | 예 |
> | 작업 | 아니요 | 예 |
> | vnfs | 아니요 | 예 |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 계정 | 예 | 예 |
> | 작업 | 예 | 예 |
> | 플랜 | 예 | 예 |
> | registeredsubscriptions | 예 | 예 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availablestacks | 예 | 아니요 |
> | billingmeters | 예 | 아니요 |
> | certificates | 아니요 | 아니요 |
> | checknameavailability | 예 | 아니요 |
> | connectiongateways | 예 | 예 |
> | connections | 예 | 예 |
> | customapis | 예 | 예 |
> | deletedsites | 예 | 아니요 |
> | deploymentlocations | 아니요 | 아니요 |
> | georegions | 아니요 | 아니요 |
> | hostingenvironments | 예 | 아니요 |
> | hostingenvironments / eventgridfilters | 예 | 아니요 |
> | hostingenvironments / multirolepools | 예 | 아니요 |
> | hostingenvironments / workerpools | 예 | 아니요 |
> | ishostingenvironmentnameavailable | 예 | 아니요 |
> | ishostnameavailable | 예 | 아니요 |
> | isusernameavailable | 예 | 아니요 |
> | kubeenvironments | 아니요 | 아니요 |
> | listsitesassignedtohostname | 아니요 | 예 |
> | 위치 | 아니요 | 예 |
> | locations / apioperations | 아니요 | 예 |
> | locations / connectiongatewayinstallations | 아니요 | 예 |
> | locations / deletedsites | 아니요 | 아니요 |
> | locations / deletevirtualnetworkorsubnets | 아니요 | 아니요 |
> | locations / extractapidefinitionfromwsdl | 예 | 아니요 |
> | locations / getnetworkpolicies | 예 | 아니요 |
> | locations / listwsdlinterfaces | 아니요 | 아니요 |
> | locations / managedapis | 예 | 예 |
> | locations / operationresults | 예 | 예 |
> | locations / operations | 예 | 예 |
> | locations / runtimes | 예 | 예 |
> | 작업 | 예 | 예 |
> | publishingusers | 예 | 예 |
> | 동영상 추천 기능 | 예 | 예 |
> | resourcehealthmetadata | 아니요 | 아니요 |
> | runtimes | 아니요 | 아니요 |
> | serverfarms | 예 | 예 |
> | serverfarms / eventgridfilters | 아니요 | 아니요 |
> | sites | 예 | 예 |
> | sites / eventgridfilters | 아니요 | 아니요 |
> | sites / hostnamebindings | 아니요 | 아니요 |
> | sites / networkconfig | 아니요 | 아니요 |
> | sites / premieraddons | 아니요 | 아니요 |
> | sites / slots | 아니요 | 아니요 |
> | sites / slots / eventgridfilters | 아니요 | 아니요 |
> | sites / slots / hostnamebindings | 아니요 | 아니요 |
> | sites / slots / networkconfig | 아니요 | 아니요 |
> | sourcecontrols | 아니요 | 아니요 |
> | staticsites | 아니요 | 아니요 |
> | validate | 아니요 | 아니요 |
> | verifyhostingenvironmentvnet | 아니요 | 아니요 |

> [!IMPORTANT]
> [App Service 이동 지침](./move-limitations/app-service-move-limitations.md)을 참조하세요.

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 위치 | 아니요 | 아니요 |
> | locations / operationstatuses | 아니요 | 아니요 |
> | multipleactivationkeys | 아니요 | 아니요 |
> | 작업 | 아니요 | 아니요 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | deviceservices | 아니요 | 아니요 |
> | 작업 | 아니요 | 아니요 |

## <a name="microsoftworkloadbuilder"></a>WorkloadBuilder

> [!div class="mx-tableFixed"]
> | 리소스 유형 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | 위치 | 아니요 | 아니요 |
> | locations / operationstatuses | 아니요 | 아니요 |
> | 작업 | 아니요 | 아니요 |
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
