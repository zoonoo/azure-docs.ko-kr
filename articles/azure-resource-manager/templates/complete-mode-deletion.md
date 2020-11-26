---
title: 전체 모드 삭제
description: 리소스 종류가 Azure Resource Manager 템플릿에서 전체 모드 삭제를 처리하는 방법을 보여줍니다.
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: e0c67bfcda81ad128e0018c4ab37c4b0cbe680f0
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184028"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>완료 모드 배포를 위한 Azure 리소스의 삭제

이 문서에서는 완료 모드로 배포된 템플릿에 없는 경우 리소스 종류가 삭제를 처리하는 방법을 설명합니다.

**예** 로 표시 된 리소스 유형은 형식이 전체 모드로 배포 된 템플릿에 없는 경우 삭제 됩니다.

로 표시 된 리소스 종류는 템플릿에 없을 때 자동으로 삭제 **되지 않습니다.** 그러나 부모 리소스를 삭제 하는 경우 삭제 됩니다. 동작에 대한 전체 설명은 [Azure Resource Manager 배포 모드](deployment-modes.md)를 참조하세요.

[템플릿에서 둘 이상의 리소스 그룹](./deploy-to-resource-group.md)에 배포 하는 경우 배포 작업에 지정 된 리소스 그룹의 리소스를 삭제할 수 있습니다. 보조 리소스 그룹의 리소스는 삭제 되지 않습니다.

리소스는 리소스 공급자 네임 스페이스 별로 나열 됩니다. 리소스 공급자 네임 스페이스를 해당 Azure 서비스 이름과 일치 시키려면 [azure 서비스의 리소스 공급자](../management/azure-services-resource-providers.md)를 참조 하세요.

> [!NOTE]
> 템플릿을 전체 모드로 배포 하기 전에 항상 [가상 작업](template-deploy-what-if.md) 을 사용 합니다. 대상-생성, 삭제 또는 수정 되는 리소스를 표시 합니다. 리소스를 실수로 삭제 하지 않도록 하려면 가상-if를 사용 합니다.
리소스 공급자 네임스페이스로 이동합니다.
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [AgFoodPlatform](#microsoftagfoodplatform)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft Automanage](#microsoftautomanage)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [BareMetalInfrastructure](#microsoftbaremetalinfrastructure)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
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
> - [Microsoft Codespaces](#microsoftcodespaces)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [ConnectedCache](#microsoftconnectedcache)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [D365CustomerInsights](#microsoftd365customerinsights)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
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
> - [Microsoft DeviceUpdate](#microsoftdeviceupdate)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentation](#microsoftexperimentation)
> - [Microsoft.Falcon](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Gallery](#microsoftgallery)
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
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
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
> - [Microsoft. 전자 필기장](#microsoftnotebooks)
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
> - [Microsoft ScVmm](#microsoftscvmm)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [특이성](#microsoftsingularity)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft .SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | DomainServices | Yes |
> | DomainServices/oucontainer | 예 |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | supportProviders | 예 |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
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
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | advisorScore | No |
> | 구성 | 예 |
> | generateRecommendations | 예 |
> | metadata | 예 |
> | 동영상 추천 기능 | 예 |
> | suppressions | 예 |

## <a name="microsoftagfoodplatform"></a>AgFoodPlatform

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | farmBeats | Yes |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | actionRules | 예 |
> | 경고 | 예 |
> | alertsList | No |
> | alertsMetaData | No |
> | alertsSummary | No |
> | alertsSummaryList | No |
> | smartDetectorAlertRules | Yes |
> | smartGroups | 예 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | servers | 예 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | reportFeedback | 예 |
> | 서비스 | 예 |
> | validateServiceName | No |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | configurationStores | Yes |
> | configurationStores/eventGridFilters | No |
> | configurationStores/keyValues | 예 |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | Spring | Yes |
> | 스프링/앱 | No |
> | 스프링/앱/배포 | No |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | attestationProviders | Yes |
> | defaultProviders | 예 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | accessReviewScheduleDefinitions | No |
> | accessReviewScheduleSettings | No |
> | classicAdministrators | No |
> | dataAliases | No |
> | denyAssignments | No |
> | elevateAccess | No |
> | findOrphanRoleAssignments | 예 |
> | locks | 예 |
> | 권한 | 예 |
> | policyAssignments | No |
> | policyDefinitions | No |
> | policyExemptions | No |
> | policySetDefinitions | No |
> | privateLinkAssociations | No |
> | providerOperations | No |
> | resourceManagementPrivateLinks | Yes |
> | roleAssignments | No |
> | roleAssignmentsUsageMetrics | No |
> | roleDefinitions | No |

## <a name="microsoftautomanage"></a>Microsoft Automanage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |
> | configurationprofil | No |
> | configurationProfilePreferences 설정 | Yes |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | automationAccounts | Yes |
> | automationAccounts/구성 | Yes |
> | automationAccounts/job | No |
> | automationAccounts/privateEndpointConnectionProxies | No |
> | automationAccounts/privateEndpointConnections | No |
> | automationAccounts/privateLinkResources | No |
> | automationAccounts / runbooks | Yes |
> | automationAccounts/softwareUpdateConfigurations | No |
> | automationAccounts / webhooks | No |

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | privateClouds | Yes |
> | privateClouds/addons | No |
> | privateClouds/권한 부여 | No |
> | privateClouds/클러스터 | No |
> | privateClouds / globalReachConnections | No |
> | privateClouds / hcxEnterpriseSites | No |
> | privateClouds / workloadNetworks | No |
> | privateClouds/workloadNetworks/dhcpConfigurations | No |
> | privateClouds/workloadNetworks/게이트웨이 | No |
> | privateClouds / workloadNetworks / portMirroringProfiles | No |
> | privateClouds/workloadNetworks/세그먼트 | No |
> | privateClouds/workloadNetworks/virtualMachines | No |
> | privateClouds/workloadNetworks/vmGroups | No |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | environments | 예 |
> | 환경/계정 | No |
> | 환경/계정/네임 스페이스 | No |
> | 환경/계정/네임 스페이스/구성 | 예 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | b2cDirectories | 예 |
> | b2ctenants | 예 |
> | guestUsages | Yes |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | dataControllers | Yes |
> | postgresInstances | Yes |
> | sqlManagedInstances | Yes |
> | sqlServerInstances | Yes |
> | sqlServerRegistrations | Yes |
> | sqlServerRegistrations/sqlServers | 예 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | cloudManifestFiles | No |
> | edgeSubscriptions | Yes |
> | linkedSubscriptions | Yes |
> | registrations | 예 |
> | 등록/customerSubscriptions | 예 |
> | registrations / products | 예 |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | clusters | 예 |

## <a name="microsoftbaremetalinfrastructure"></a>BareMetalInfrastructure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | bareMetalInstances | Yes |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | batchAccounts | Yes |
> | batchAccounts / certificates | No |
> | batchAccounts / pools | 예 |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | billingAccounts | No |
> | billingAccounts/규약 | No |
> | billingAccounts / billingPermissions | No |
> | billingAccounts / billingProfiles | No |
> | billingAccounts / billingProfiles / billingPermissions | No |
> | billingAccounts / billingProfiles / billingRoleAssignments | No |
> | billingAccounts / billingProfiles / billingRoleDefinitions | No |
> | billingAccounts / billingProfiles / billingSubscriptions | No |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | No |
> | billingAccounts/billingProfiles/고객 | No |
> | billingAccounts/billingProfiles/지침 | No |
> | billingAccounts/billingProfiles/송장 | No |
> | billingAccounts/billingProfiles/청구서/가격표 | No |
> | billingAccounts/billingProfiles/송장/트랜잭션 | No |
> | billingAccounts / billingProfiles / invoiceSections | No |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | No |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | No |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | No |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | No |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | No |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | No |
> | billingAccounts/billingProfiles/invoiceSections/products | No |
> | billingAccounts/billingProfiles/invoiceSections/products/transfer | No |
> | billingAccounts/billingProfiles/invoiceSections/products/updateAutoRenew | No |
> | billingAccounts/billingProfiles/invoiceSections/트랜잭션 | No |
> | billingAccounts/billingProfiles/invoiceSections/transfer | No |
> | billingAccounts / BillingProfiles / patchOperations | No |
> | billingAccounts / billingProfiles / paymentMethods | No |
> | billingAccounts/billingProfiles/정책 | No |
> | billingAccounts/billingProfiles/가격표 | No |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | No |
> | billingAccounts/billingProfiles/제품 | No |
> | billingAccounts/billingProfiles/예약 | No |
> | billingAccounts/billingProfiles/트랜잭션 | No |
> | billingAccounts / billingProfiles / validateDetachPaymentMethodEligibility | No |
> | billingAccounts / billingRoleAssignments | No |
> | billingAccounts / billingRoleDefinitions | No |
> | billingAccounts / billingSubscriptions | No |
> | billingAccounts/billingSubscriptions/송장 | No |
> | billingAccounts / createBillingRoleAssignment | No |
> | billingAccounts / createInvoiceSectionOperations | No |
> | billingAccounts/고객 | No |
> | billingAccounts/customers/billingPermissions | No |
> | billingAccounts/customers/billingSubscriptions | No |
> | billingAccounts/customers/initiateTransfer | No |
> | billingAccounts/고객/정책 | No |
> | billingAccounts/고객/제품 | No |
> | billingAccounts/고객/트랜잭션 | No |
> | billingAccounts/고객/전송 | No |
> | billingAccounts/부서 | No |
> | billingAccounts/부서/billingPermissions | No |
> | billingAccounts/부서/billingRoleAssignments | No |
> | billingAccounts/부서/billingRoleDefinitions | No |
> | billingAccounts / enrollmentAccounts | No |
> | billingAccounts / enrollmentAccounts / billingPermissions | No |
> | billingAccounts / enrollmentAccounts / billingRoleAssignments | No |
> | billingAccounts / enrollmentAccounts / billingRoleDefinitions | No |
> | billingAccounts/송장 | No |
> | billingAccounts/송장/트랜잭션 | No |
> | billingAccounts / invoiceSections | No |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | No |
> | billingAccounts / invoiceSections / billingSubscriptions | No |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | No |
> | billingAccounts/invoiceSections/상승 | No |
> | billingAccounts / invoiceSections / initiateTransfer | No |
> | billingAccounts / invoiceSections / patchOperations | No |
> | billingAccounts/invoiceSections/제품 Moveoperations | No |
> | billingAccounts/invoiceSections/제품 | No |
> | billingAccounts/invoiceSections/제품/이전 | No |
> | billingAccounts/invoiceSections/products/updateAutoRenew | No |
> | billingAccounts/invoiceSections/트랜잭션 | No |
> | billingAccounts/invoiceSections/전송 | No |
> | billingAccounts/lineOfCredit | No |
> | billingAccounts / patchOperations | No |
> | billingAccounts / paymentMethods | No |
> | billingAccounts/제품 | No |
> | billingAccounts/예약 | No |
> | billingAccounts/트랜잭션 | No |
> | billingPeriods | No |
> | billingPermissions | No |
> | billingProperty | No |
> | billingRoleAssignments | No |
> | billingRoleDefinitions | No |
> | createBillingRoleAssignment | 예 |
> | departments | 예 |
> | enrollmentAccounts | 예 |
> | invoices | 예 |
> | transfers | 예 |
> | 전송/acceptTransfer | No |
> | 전송/declineTransfer | No |
> | 전송/operationStatus | No |
> | 전송/validateTransfer | No |
> | validateAddress | 예 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | mapApis | Yes |
> | updateCommunicationPreference | 예 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | blockchainMembers | Yes |
> | cordaMembers | Yes |
> | watchers | Yes |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | TokenServices | Yes |
> | TokenServices/BlockchainNetworks | No |
> | TokenServices/그룹 | No |
> | TokenServices/그룹/계정 | No |
> | TokenServices/TokenTemplates | 예 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | blueprintAssignments | No |
> | blueprintAssignments / assignmentOperations | No |
> | blueprintAssignments/작업 | 예 |
> | blueprints | 예 |
> | blueprints / artifacts | 예 |
> | blueprints / versions | 예 |
> | blueprints / versions / artifacts | 예 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | botServices | Yes |
> | botServices / channels | No |
> | botServices/연결 | No |
> | 언어 | No |
> | 템플릿 | 예 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | Redis | Yes |
> | Redis/EventGridFilters | No |
> | Redis/privateEndpointConnectionProxies | No |
> | Redis/privateEndpointConnectionProxies/validate | No |
> | Redis/privateEndpointConnections | No |
> | Redis/privateLinkResources | No |
> | redisEnterprise | Yes |
> | RedisEnterprise/privateEndpointConnectionProxies | No |
> | RedisEnterprise/privateEndpointConnectionProxies/validate | No |
> | RedisEnterprise/privateEndpointConnections | No |
> | RedisEnterprise/privateLinkResources | No |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | appliedReservations | No |
> | autoQuotaIncrease | No |
> | calculateExchange | No |
> | calculatePrice | No |
> | calculatePurchasePrice | 예 |
> | catalogs | 예 |
> | commercialReservationOrders | 예 |
> | 교환 | 예 |
> | ownReservations | No |
> | placePurchaseOrder | No |
> | reservationOrders | No |
> | reservationOrders / calculateRefund | No |
> | reservationOrders/병합 | No |
> | reservationOrders/예약 | No |
> | reservationOrders/예약/수정 버전 | No |
> | reservationOrders/반환 | No |
> | reservationOrders/분할 | No |
> | reservationOrders/교환 | 예 |
> | reservations | 예 |
> | resourceProviders | 예 |
> | 리소스 | 예 |
> | validateReservationOrder | 예 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | No |
> | CdnWebApplicationFirewallPolicies | Yes |
> | edgenodes | 예 |
> | 프로필 | 예 |
> | profiles / endpoints | 예 |
> | profiles / endpoints / customdomains | 예 |
> | profiles / endpoints / origingroups | 예 |
> | profiles / endpoints / origins | 예 |
> | validateProbe | 예 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | certificateOrders | Yes |
> | certificateOrders/certificate | No |
> | validateCertificateRegistrationInformation | No |

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | profile | No |
> | resourceChanges | 예 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | capabilities | 예 |
> | domainNames | Yes |
> | domainNames/기능 | No |
> | domainNames/internalLoadBalancers 장치 | No |
> | domainNames/serviceCertificates | No |
> | domainNames/슬롯 | No |
> | domainNames/슬롯/역할 | No |
> | domainNames/슬롯/역할/metricDefinitions | No |
> | domainNames/슬롯/역할/메트릭 | No |
> | moveSubscriptionResources | No |
> | operatingSystemFamilies | No |
> | operatingSystems | 예 |
> | quotas | 예 |
> | resourceTypes | No |
> | validateSubscriptionMoveAvailability | No |
> | virtualMachines | Yes |
> | virtualMachines/diagnosticSettings | No |
> | virtualMachines/metricDefinitions | No |
> | virtualMachines/메트릭 | No |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | classicInfrastructureResources | No |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | capabilities | 예 |
> | expressRouteCrossConnections | No |
> | expressRouteCrossConnections/피어 링 | No |
> | gatewaySupportedDevices | No |
> | networkSecurityGroups | Yes |
> | quotas | 예 |
> | reservedIps | Yes |
> | virtualNetworks | Yes |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | No |
> | virtualNetworks / virtualNetworkPeerings | No |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | capabilities | 예 |
> | disks | 예 |
> | images | 예 |
> | osImages | No |
> | osPlatformImages | No |
> | publicImages | 예 |
> | quotas | 예 |
> | storageAccounts | Yes |
> | storageAccounts / blobServices | No |
> | storageAccounts / fileServices | No |
> | storageAccounts/metricDefinitions | No |
> | storageAccounts/메트릭 | No |
> | storageAccounts/queueServices | No |
> | storageAccounts/서비스 | No |
> | storageAccounts/services/diagnosticSettings | No |
> | storageAccounts/services/metricDefinitions | No |
> | storageAccounts/서비스/메트릭 | No |
> | storageAccounts/tableServices | No |
> | storageAccounts/vmImages | No |
> | vmImages | No |

## <a name="microsoftcodespaces"></a>Microsoft Codespaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 플랜 | Yes |
> | registeredSubscriptions | 예 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |
> | 계정/privateEndpointConnectionProxies | No |
> | 계정/privateEndpointConnections | No |
> | 계정/privateLinkResources | 예 |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | RateCard | No |
> | UsageAggregates | 예 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | availabilitySets | Yes |
> | cloudServices | Yes |
> | cloudServices/networkInterfaces | No |
> | cloudServices/publicIPAddresses | No |
> | cloudServices/roleInstances | No |
> | cloudServices/roleInstances/networkInterfaces | No |
> | cloudServices/역할 | No |
> | diskAccesses | Yes |
> | diskEncryptionSets | Yes |
> | disks | 예 |
> | galleries | Yes |
> | galleries / applications | No |
> | 갤러리/응용 프로그램/버전 | 예 |
> | galleries / images | 예 |
> | galleries / images / versions | 예 |
> | 호스트 그룹 | Yes |
> | 호스트 그룹/호스트 | Yes |
> | images | 예 |
> | proximityPlacementGroups | Yes |
> | restorePointCollections | Yes |
> | restorePointCollections / restorePoints | No |
> | sharedVMExtensions | Yes |
> | sharedVMExtensions/버전 | No |
> | sharedVMImages | Yes |
> | sharedVMImages/버전 | 예 |
> | 스냅샷 | 예 |
> | sshPublicKeys | Yes |
> | virtualMachines | Yes |
> | virtualMachines/확장 | Yes |
> | virtualMachines/metricDefinitions | No |
> | virtualMachines/runCommands | Yes |
> | virtualMachineScaleSets | Yes |
> | virtualMachineScaleSets/확장 | No |
> | virtualMachineScaleSets/networkInterfaces | No |
> | virtualMachineScaleSets/publicIPAddresses | No |
> | virtualMachineScaleSets/virtualMachines | No |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | No |

## <a name="microsoftconnectedcache"></a>ConnectedCache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | CacheNodes | Yes |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | AggregatedCost | No |
> | 잔액 | No |
> | 예산 | No |
> | Charges | No |
> | CostTags | 예 |
> | credits | 예 |
> | events | 예 |
> | 예측 | 예 |
> | lots | 예 |
> | Marketplace | No |
> | Pricesheets | 예 |
> | products | 예 |
> | ReservationDetails | No |
> | ReservationRecommendationDetails | No |
> | ReservationRecommendations | No |
> | ReservationSummaries | No |
> | ReservationTransactions | 아니요 |
> | 태그 | 예 |
> | tenants | 예 |
> | 용어 | No |
> | UsageDetails | 예 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | containerGroups | Yes |
> | serviceAssociationLinks | 예 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | registries | 예 |
> | 레지스트리/agentPools | Yes |
> | registries / builds | 예 |
> | registries / builds / cancel | 예 |
> | 레지스트리/빌드/getLogLink | No |
> | registries / buildTasks | Yes |
> | 레지스트리/buildTasks/단계 | No |
> | 레지스트리/eventGridFilters | No |
> | 레지스트리/exportPipelines | No |
> | 레지스트리/generateCredentials | No |
> | 레지스트리/getBuildSourceUploadUrl | No |
> | 레지스트리/GetCredentials | No |
> | 레지스트리/importImage | No |
> | 레지스트리/importPipelines | No |
> | 레지스트리/pipelineRuns | No |
> | 레지스트리/privateEndpointConnectionProxies | No |
> | 레지스트리/privateEndpointConnectionProxies/유효성 검사 | No |
> | 레지스트리/privateEndpointConnections | No |
> | 레지스트리/privateLinkResources | No |
> | 레지스트리/queueBuild | No |
> | 레지스트리/regenerateCredential | No |
> | 레지스트리/regenerateCredentials | 예 |
> | registries / replications | 예 |
> | registries / runs | 예 |
> | registries / runs / cancel | 예 |
> | 레지스트리/scheduleRun | No |
> | registries / scopeMaps | No |
> | 레지스트리/taskRuns | 예 |
> | registries / tasks | 예 |
> | registries / tokens | 예 |
> | 레지스트리/updatePolicies | 예 |
> | registries / webhooks | 예 |
> | registry/웹 후크/getcallbackconfig | 예 |
> | registries / webhooks / ping | 예 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | containerServices | Yes |
> | managedClusters | Yes |
> | openShiftManagedClusters | Yes |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 경고 | No |
> | BillingAccounts | No |
> | 예산 | No |
> | CloudConnectors | No |
> | 커넥터 | Yes |
> | costAllocationRules | No |
> | Departments | No |
> | 차원 | No |
> | EnrollmentAccounts | No |
> | 내보내기 | No |
> | ExternalBillingAccounts | No |
> | ExternalBillingAccounts/경고 | No |
> | ExternalBillingAccounts/차원 | No |
> | ExternalBillingAccounts/예측 | No |
> | ExternalBillingAccounts/쿼리 | No |
> | ExternalSubscriptions | No |
> | ExternalSubscriptions/Alerts | No |
> | ExternalSubscriptions/차원 | No |
> | ExternalSubscriptions/예측 | No |
> | ExternalSubscriptions/Query | No |
> | 예측 | No |
> | 자세한 정보 | No |
> | 쿼리 | 예 |
> | register | 예 |
> | Reportconfigs | No |
> | 보고서 | No |
> | 설정 | No |
> | showbackRules | No |
> | 보기 | 예 |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | requests | 예 |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | associations | 예 |
> | resourceProviders | Yes |

## <a name="microsoftd365customerinsights"></a>D365CustomerInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 인스턴스 | 예 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | jobs | 예 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Yes |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | workspaces | 예 |
> | workspace/dbWorkspaces | No |
> | 작업 영역/virtualNetworkPeerings | 예 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | catalogs | 예 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | dataFactories | Yes |
> | dataFactories/diagnosticSettings | No |
> | dataFactories/metricDefinitions | No |
> | dataFactorySchema | 예 |
> | factories | 예 |
> | factories / integrationRuntimes | 예 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |
> | accounts / dataLakeStoreAccounts | No |
> | accounts / storageAccounts | No |
> | 계정/storageAccounts/컨테이너 | No |
> | 계정/transferAnalyticsUnits | 예 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |
> | 계정/eventGridFilters | No |
> | accounts / firewallRules | 예 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | services | 예 |
> | services / projects | Yes |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | BackupVaults | Yes |
> | ResourceOperationGateKeepers 키퍼 | Yes |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |
> | accounts / shares | 예 |
> | accounts / shares / datasets | 예 |
> | accounts / shares / invitations | 예 |
> | accounts / shares / providersharesubscriptions | 예 |
> | 계정/공유/synchronizationSettings | 예 |
> | accounts / sharesubscriptions | 예 |
> | 계정/sharesubscriptions/consumerSourceDataSets | 예 |
> | accounts / sharesubscriptions / datasetmappings | 예 |
> | accounts / sharesubscriptions / triggers | 예 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | servers | 예 |
> | servers / advisors | 예 |
> | servers / keys | 예 |
> | servers/privateEndpointConnectionProxies | No |
> | servers/privateEndpointConnections | No |
> | servers/privateLinkResources | No |
> | 서버/queryTexts | No |
> | servers/recoverableServers | No |
> | 서버/시작 | No |
> | 서버/중지 | No |
> | servers/topQueryStatistics | No |
> | servers / virtualNetworkRules | No |
> | 서버/waitStatistics | 예 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | flexibleServers | Yes |
> | servers | 예 |
> | servers / advisors | 예 |
> | servers / keys | 예 |
> | servers/privateEndpointConnectionProxies | No |
> | servers/privateEndpointConnections | No |
> | servers/privateLinkResources | No |
> | 서버/queryTexts | No |
> | servers/recoverableServers | No |
> | 서버/시작 | No |
> | 서버/중지 | No |
> | servers/topQueryStatistics | No |
> | 서버/업그레이드 | No |
> | servers / virtualNetworkRules | No |
> | 서버/waitStatistics | 예 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | flexibleServers | Yes |
> | serverGroups | Yes |
> | servers | 예 |
> | servers / advisors | 예 |
> | servers / keys | 예 |
> | servers/privateEndpointConnectionProxies | No |
> | servers/privateEndpointConnections | No |
> | servers/privateLinkResources | No |
> | 서버/queryTexts | No |
> | servers/recoverableServers | No |
> | servers/topQueryStatistics | No |
> | servers / virtualNetworkRules | No |
> | 서버/waitStatistics | 예 |
> | serversv2 | 예 |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | artifactSources | Yes |
> | rollouts | 예 |
> | serviceTopologies | Yes |
> | serviceTopologies/서비스 | Yes |
> | serviceTopologies/서비스/serviceUnits | 예 |
> | 단계 | 예 |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | applicationgroups | 예 |
> | applicationgroups / applications | 예 |
> | applicationgroups / desktops | 예 |
> | applicationgroups / startmenuitems | 예 |
> | hostpools | 예 |
> | hostpools/msixpackages | No |
> | hostpools / sessionhosts | 예 |
> | hostpools / sessionhosts / usersessions | 예 |
> | hostpools / usersessions | 예 |
> | workspaces | 예 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | ElasticPools | Yes |
> | ElasticPools / IotHubTenants | Yes |
> | ElasticPools/IotHubTenants/securitySettings | No |
> | IotHubs | Yes |
> | IotHubs/eventGridFilters | No |
> | IotHubs/securitySettings | No |
> | ProvisioningServices | 예 |
> | usages | 예 |

## <a name="microsoftdeviceupdate"></a>Microsoft DeviceUpdate

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |
> | 계정/인스턴스 | Yes |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | pipelines | 예 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | controllers | 예 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | labcenters | Yes |
> | labs | 예 |
> | labs / environments | 예 |
> | labs/serviceRunners | Yes |
> | labs/virtualMachines | Yes |
> | schedules | 예 |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | digitalTwinsInstances | Yes |
> | digitalTwinsInstances/끝점 | 예 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | databaseAccountNames | No |
> | databaseAccounts | Yes |
> | restorableDatabaseAccounts | 예 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | domains | 예 |
> | 도메인/도메인 소유자의 식별자 | No |
> | generateSsoRequest | No |
> | topLevelDomains | No |
> | validateDomainRegistrationInformation | No |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | lcsprojects | No |
> | lcsprojects/clouddeployments | No |
> | lcsprojects/커넥터 | 예 |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | services | 예 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | domains | 예 |
> | domains / topics | 예 |
> | eventSubscriptions | No |
> | extensionTopics | No |
> | 네임 스페이스 | Yes |
> | 네임 스페이스/a s p 채널 | No |
> | 파트너 등록 | Yes |
> | 항목 항목 | Yes |
> | 항목 토픽/eventSubscriptions | No |
> | systemTopics | Yes |
> | systemTopics/eventSubscriptions | 예 |
> | topics | 예 |
> | topicTypes | 예 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | clusters | 예 |
> | 네임스페이스 | 예 |
> | namespaces / authorizationrules | 예 |
> | namespaces / disasterrecoveryconfigs | 예 |
> | namespaces / eventhubs | 예 |
> | namespaces / eventhubs / authorizationrules | 예 |
> | namespaces / eventhubs / consumergroups | 예 |
> | namespaces / networkrulesets | 예 |
> | 네임 스페이스/privateEndpointConnections | 예 |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | experimentWorkspaces | Yes |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 네임스페이스 | 예 |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | featureProviders | 예 |
> | 기능 | 예 |
> | providers | 예 |
> | subscriptionFeatureRegistrations | No |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | enroll | No |
> | galleryitems | No |
> | generateartifactaccessuri | No |
> | myareas | No |
> | myareas/areas | No |
> | myareas/areas/areas | No |
> | myareas/areas/areas/galleryitems | No |
> | myareas/areas/galleryitems | No |
> | myareas/galleryitems | 예 |
> | register | 예 |
> | 리소스 | 예 |
> | retrieveresourcesbyid | 예 |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | autoManagedAccounts | Yes |
> | autoManagedVmConfigurationProfiles | Yes |
> | configurationprofil | No |
> | guestConfigurationAssignments | 예 |
> | software | 예 |
> | softwareUpdateProfile | No |
> | 업데이트 | 예 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | hanaInstances | Yes |
> | sapMonitors | 예 |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | dedicatedHSMs | Yes |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | clusters | 예 |
> | clusters / applications | 예 |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | services | 예 |
> | 서비스/iomtconnectors | No |
> | 서비스/iomtconnectors/연결 | No |
> | services/iomtconnectors/매핑 | No |
> | 서비스/privateEndpointConnectionProxies | No |
> | 서비스/privateEndpointConnections | No |
> | 서비스/privateLinkResources | 예 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | machines | 예 |
> | machines/assessPatches | No |
> | machines / extensions | Yes |
> | 컴퓨터/installPatches | 예 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | dataManagers | Yes |

## <a name="microsofthybridnetwork"></a>HybridNetwork

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 디바이스 | Yes |
> | networkFunctions | Yes |
> | networkFunctionVendors | No |
> | registeredSubscriptions | No |
> | 판매 | No |
> | 공급 업체/vendorSkus | No |
> | 공급 업체/vendorSkus/previewSubscriptions | No |
> | virtualNetworkFunctions | Yes |
> | virtualNetworkFunctionVendors | 예 |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | components | 예 |
> | networkScopes | Yes |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | jobs | 예 |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | diagnosticSettings | No |
> | diagnosticSettingsCategories | No |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | appTemplates | No |
> | IoTApps | Yes |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 그래프 | 예 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | deletedVaults | No |
> | hsmPools | Yes |
> | managedHSMs | Yes |
> | vaults | 예 |
> | 자격 증명 모음/Accesspolicy | No |
> | 자격 증명 모음/eventGridFilters | No |
> | 자격 증명 모음/키 | No |
> | 자격 증명 모음/키/버전 | 예 |
> | vaults / secrets | 예 |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | connectedClusters | Yes |
> | registeredSubscriptions | 예 |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 확장 | No |
> | sourceControlConfigurations | 예 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | clusters | 예 |
> | clusters / attacheddatabaseconfigurations | 예 |
> | clusters / databases | 예 |
> | clusters / databases / dataconnections | 예 |
> | clusters / databases / eventhubconnections | 예 |
> | clusters / databases / principalassignments | 예 |
> | 클러스터/dataconnections | 예 |
> | clusters / principalassignments | 예 |
> | 클러스터/sharedidentities | 예 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | labaccounts | Yes |
> | users | 예 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | hostingEnvironments | Yes |
> | integrationAccounts | Yes |
> | integrationServiceEnvironments | Yes |
> | integrationServiceEnvironments / managedApis | Yes |
> | isolatedEnvironments | Yes |
> | workflows | 예 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | commitmentPlans | Yes |
> | webServices | Yes |
> | 작업 영역 | Yes |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | workspaces | 예 |
> | 작업 영역/batchEndpoints | Yes |
> | 작업 영역/batchEndpoints/배포 | Yes |
> | 작업 영역/코드 | No |
> | 작업 영역/코드/버전 | 예 |
> | workspaces / computes | 예 |
> | 작업 영역/데이터 저장소 | No |
> | workspace/eventGridFilters | No |
> | 작업 영역/작업 | No |
> | 작업 영역/labelingJobs | No |
> | 작업 영역/Linkedservices.json 및 datasets.json | No |
> | 작업 영역/모델 | No |
> | 작업 영역/모델/버전 | No |
> | 작업 영역/onlineEndpoints | Yes |
> | 작업 영역/onlineEndpoints/배포 | Yes |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | applyUpdates | No |
> | configurationAssignments | No |
> | maintenanceConfigurations | Yes |
> | publicMaintenanceConfigurations | No |
> | updates | 예 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | Identities | No |
> | userAssignedIdentities | Yes |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | managedNetworks | Yes |
> | managedNetworks/managedNetworkGroups | Yes |
> | managedNetworks/Managednetworkpeering정책만 | Yes |
> | 알림 | Yes |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | No |
> | registrationAssignments | No |
> | registrationDefinitions | 예 |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | getEntities | No |
> | managementGroups | No |
> | managementGroups/settings | 예 |
> | 리소스 | 예 |
> | startTenantBackfill | No |
> | tenantBackfillStatus | 예 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |
> | 계정/eventGridFilters | No |
> | 계정/privateAtlases | Yes |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | macc | 예 |
> | offers | 예 |
> | offerTypes | No |
> | offerTypes/게시자 | No |
> | offerTypes/게시자/제안 | No |
> | offerTypes/게시자/제품/계획 | No |
> | offerTypes/게시자/제품/계획/규약 | No |
> | offerTypes/게시자/제품/계획/configs | No |
> | offerTypes/publishers/제품/계획/configs/importImage | 예 |
> | privategalleryitems | 예 |
> | privateStoreClient | No |
> | privateStores | No |
> | privateStores/제안 | 예 |
> | products | 예 |
> | publishers | 예 |
> | publishers / offers | 예 |
> | publishers / offers / amendments | 예 |
> | register | 예 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | classicDevServices | Yes |
> | updateCommunicationPreference | 예 |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | agreements | 예 |
> | offertypes | 예 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | mediaservices | 예 |
> | windowsazure.mediaservices/accountFilters | 예 |
> | mediaservices / assets | 예 |
> | windowsazure.mediaservices/asset/assetFilters | No |
> | windowsazure.mediaservices/Contentkeypolicy | No |
> | windowsazure.mediaservices/eventGridFilters | No |
> | windowsazure.mediaservices/liveEventOperations | No |
> | mediaservices / liveEvents | Yes |
> | mediaservices / liveEvents / liveOutputs | No |
> | windowsazure.mediaservices/liveOutputOperations | No |
> | windowsazure.mediaservices/mediaGraphs | No |
> | windowsazure.mediaservices/privateEndpointConnectionOperations | No |
> | windowsazure.mediaservices/privateEndpointConnectionProxies | No |
> | windowsazure.mediaservices/privateEndpointConnections | No |
> | windowsazure.mediaservices/streamingEndpointOperations | No |
> | mediaservices / streamingEndpoints | Yes |
> | windowsazure.mediaservices/streamingLocators | No |
> | windowsazure.mediaservices/streamingPolicies | 예 |
> | mediaservices / transforms | 예 |
> | mediaservices / transforms / jobs | 예 |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | appClusters | Yes |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | assessmentProjects | Yes |
> | migrateprojects | 예 |
> | moveCollections | Yes |
> | projects | Yes |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | Yes |
> | objectUnderstandingAccounts | Yes |
> | remoteRenderingAccounts | Yes |
> | spatialAnchorsAccounts | 예 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | netAppAccounts | Yes |
> | netAppAccounts/accountBackups | No |
> | netAppAccounts/capacityPools | Yes |
> | netAppAccounts/capacityPools/볼륨 | Yes |
> | netAppAccounts/capacityPools/볼륨/스냅숏 | 예 |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | applicationGateways | Yes |
> | applicationGatewayWebApplicationFirewallPolicies | Yes |
> | applicationSecurityGroups | Yes |
> | azureFirewallFqdnTags | No |
> | azureFirewalls | Yes |
> | bastionHosts | Yes |
> | bgpServiceCommunities | 예 |
> | connections | 예 |
> | ddosCustomPolicies | Yes |
> | ddosProtectionPlans | Yes |
> | dnsOperationStatuses | 예 |
> | dnszones | 예 |
> | dnszones/A | No |
> | dnszones/AAAA | 예 |
> | dnszones / all | 예 |
> | dnszones/CAA | No |
> | dnszones/CNAME | No |
> | dnszones/MX | No |
> | dnszones/NS | No |
> | dnszones/PTR | 예 |
> | dnszones / recordsets | 예 |
> | dnszones/SOA | No |
> | dnszones/SRV | No |
> | dnszones/TXT | No |
> | expressRouteCircuits | Yes |
> | expressRouteCrossConnections | Yes |
> | expressRouteGateways | Yes |
> | expressRoutePorts | Yes |
> | expressRouteServiceProviders | No |
> | firewallPolicies | Yes |
> | frontdoors | Yes |
> | frontdoorWebApplicationFirewallManagedRuleSets | No |
> | frontdoorWebApplicationFirewallPolicies | Yes |
> | getDnsResourceReference | No |
> | internalNotify | No |
> | ipGroups | Yes |
> | loadBalancers | Yes |
> | localNetworkGateways | Yes |
> | natGateways | Yes |
> | networkIntentPolicies | Yes |
> | networkInterfaces | Yes |
> | networkProfiles | Yes |
> | networkSecurityGroups | Yes |
> | networkWatchers | Yes |
> | networkWatchers/connectionMonitors | Yes |
> | networkWatchers/flowLogs | Yes |
> | networkWatchers/lenses | Yes |
> | networkWatchers/ | Yes |
> | p2sVpnGateways | Yes |
> | privateDnsOperationStatuses | No |
> | privateDnsZones | Yes |
> | privateDnsZones/A | No |
> | privateDnsZones/AAAA | No |
> | privateDnsZones/모두 | No |
> | privateDnsZones/CNAME | No |
> | privateDnsZones/MX | No |
> | privateDnsZones/PTR | No |
> | privateDnsZones/SOA | No |
> | privateDnsZones/SRV | No |
> | privateDnsZones/TXT | No |
> | privateDnsZones / virtualNetworkLinks | Yes |
> | privateEndpoints | Yes |
> | privateLinkServices | Yes |
> | publicIPAddresses | Yes |
> | publicIPPrefixes | Yes |
> | routeFilters | Yes |
> | routeTables | Yes |
> | serviceEndpointPolicies | Yes |
> | trafficManagerGeographicHierarchies | 예 |
> | trafficmanagerprofiles | 예 |
> | trafficmanagerprofiles/열 지도 | No |
> | trafficManagerUserMetricsKeys | No |
> | virtualHubs | Yes |
> | virtualNetworkGateways | Yes |
> | virtualNetworks | Yes |
> | virtualNetworks/서브넷 | No |
> | virtualNetworkTaps | Yes |
> | virtualWans | Yes |
> | vpnGateways | Yes |
> | vpnSites | Yes |
> | webApplicationFirewallPolicies | Yes |

## <a name="microsoftnotebooks"></a>Microsoft. 전자 필기장

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | NotebookProxies | No |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 네임스페이스 | 예 |
> | namespaces / notificationHubs | Yes |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | osNamespaces 스페이스 | 예 |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | HyperVSites | Yes |
> | ImportSites | Yes |
> | MasterSites | Yes |
> | ServerSites | Yes |
> | VMwareSites | 예 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | clusters | 예 |
> | deletedWorkspaces | No |
> | linkTargets | No |
> | storageInsightConfigs | 예 |
> | workspaces | 예 |
> | 작업 영역/데이터 내보내기 | No |
> | 작업 영역/데이터 원본 | No |
> | 작업 영역/Linkedservices.json 및 datasets.json | No |
> | 작업 영역/linkedStorageAccounts | 예 |
> | workspaces / metadata | 예 |
> | workspaces / query | 예 |
> | 작업 영역/scopedPrivateLinkProxies | No |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | managementassociations | 예 |
> | managementconfigurations | 예 |
> | solutions | 예 |
> | 뷰 | 예 |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | legacyPeerings | No |
> | peerAsns | 예 |
> | peerings | 예 |
> | peeringServiceCountries | No |
> | peeringServiceProviders | No |
> | peeringServices | Yes |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 증명 | No |
> | policyEvents | No |
> | policyMetadata | No |
> | policyStates | No |
> | policyTrackedResources | 예 |
> | remediations | 예 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | consoles | 예 |
> | dashboards | 예 |
> | userSettings | 예 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | privateLinkServicesForPowerBI | Yes |
> | tenants | Yes |
> | 테 넌 트/작업 영역 | No |
> | workspaceCollections | 예 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | capacities | 예 |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |
> | deletedAccounts | 예 |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | providerRegistrations | No |
> | providerRegistrations/defaultRollouts | No |
> | providerRegistrations resourceTypeRegistrations | 예 |
> | rollouts | 예 |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 작업 영역 | Yes |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | backupProtectedItems | 예 |
> | vaults | 예 |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | OpenShiftClusters | Yes |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 네임스페이스 | 예 |
> | namespaces / authorizationrules | 예 |
> | namespaces / hybridconnections | 예 |
> | namespaces / hybridconnections / authorizationrules | 예 |
> | 네임 스페이스/privateEndpointConnections | 예 |
> | namespaces / wcfrelays | 예 |
> | namespaces / wcfrelays / authorizationrules | 예 |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 쿼리 | 예 |
> | resourceChangeDetails | No |
> | resourceChanges | 예 |
> | 리소스 | 예 |
> | 자원 기록 | No |
> | subscriptionsStatus | 예 |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | availabilityStatuses | No |
> | childAvailabilityStatuses | No |
> | childResources | 예 |
> | emergingissues | 예 |
> | events | 예 |
> | impactedResources | 예 |
> | metadata | 예 |
> | 알림 | 예 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | calculateTemplateHash | 예 |
> | 배포 | 예 |
> | deployments / operations | 예 |
> | deploymentScripts | Yes |
> | deploymentScripts/logs | 예 |
> | 링크 | 예 |
> | notifyResourceJobs | 예 |
> | providers | 예 |
> | resourceGroups | 예 |
> | 구독 | 예 |
> | templateSpecs | Yes |
> | templateSpecs/버전 | Yes |
> | tenants | 예 |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 애플리케이션 | 예 |
> | saasresources | 예 |

## <a name="microsoftscvmm"></a>Microsoft ScVmm

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 클라우드 | Yes |
> | VirtualMachines | Yes |
> | VirtualMachineTemplates | Yes |
> | VirtualNetworks | Yes |
> | vmmservers | Yes |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | resourceHealthMetadata | No |
> | searchServices | Yes |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | No |
> | advancedThreatProtectionSettings | 예 |
> | 경고 | 예 |
> | alertsSuppressionRules | No |
> | allowedConnections | No |
> | applicationWhitelistings | No |
> | assessmentMetadata | 예 |
> | assessments | 예 |
> | autoDismissAlertsRules | 예 |
> | automations | 예 |
> | AutoProvisioningSettings | No |
> | Compliances | 예 |
> | 커넥터 | No |
> | dataCollectionAgents | No |
> | deviceSecurityGroups | No |
> | discoveredSecuritySolutions | No |
> | externalSecuritySolutions | No |
> | InformationProtectionPolicies | No |
> | iotDefenderSettings | No |
> | iotSecuritySolutions | Yes |
> | IanalyticsModels Securitysolutions/ | No |
> | iotSecuritySolutions/analyticsModels/aggregatedAlerts | No |
> | iotSecuritySolutions/analyticsModels/aggregatedRecommendations | No |
> | iotSecuritySolutions/I이상 경고 | No |
> | iotSecuritySolutions/Iotsecuritysolutions | No |
> | iotSecuritySolutions/I이상 권장 사항 | No |
> | IiotRecommendationTypes Securitysolutions/ | No |
> | I이상 센서 | No |
> | jitNetworkAccessPolicies | No |
> | jitPolicies | 예 |
> | 정책 | 예 |
> | pricings | 예 |
> | regulatoryComplianceStandards | No |
> | regulatoryComplianceStandards / regulatoryComplianceControls | No |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | No |
> | secureScoreControlDefinitions | No |
> | secureScoreControls | No |
> | secureScores | No |
> | secureScores / secureScoreControls | No |
> | securityContacts | No |
> | securitySolutions | No |
> | securitySolutionsReferenceData | No |
> | securityStatuses | No |
> | securityStatusesSummaries | No |
> | serverVulnerabilityAssessments | 예 |
> | 설정 | 예 |
> | sqlVulnerabilityAssessments | No |
> | 하위 평가 | 예 |
> | tasks | 예 |
> | topologies | 예 |
> | workspaceSettings | No |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | diagnosticSettings | No |
> | diagnosticSettingsCategories | 예 |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | aggregations | 예 |
> | alertRules | No |
> | alertRuleTemplates | No |
> | automationRules | 예 |
> | bookmarks | 예 |
> | cases | 예 |
> | dataConnectors | No |
> | dataConnectorsCheckRequirements | 예 |
> | 엔터티 | 예 |
> | entityQueries | 예 |
> | incidents | 예 |
> | officeConsents | 예 |
> | 설정 | 예 |
> | threatIntelligence | No |
> | watchlists | 예 |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | consoleServices | 예 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 네임스페이스 | 예 |
> | namespaces / authorizationrules | 예 |
> | namespaces / disasterrecoveryconfigs | 예 |
> | namespaces / eventgridfilters | 예 |
> | namespaces / networkrulesets | 예 |
> | 네임 스페이스/privateEndpointConnections | 예 |
> | namespaces / queues | 예 |
> | namespaces / queues / authorizationrules | 예 |
> | namespaces / topics | 예 |
> | namespaces / topics / authorizationrules | 예 |
> | namespaces / topics / subscriptions | 예 |
> | namespaces / topics / subscriptions / rules | 예 |
> | premiumMessagingRegions | 예 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 애플리케이션 | 예 |
> | clusters | 예 |
> | clusters / applications | 예 |
> | containerGroups | Yes |
> | containerGroupSets | Yes |
> | edgeclusters | Yes |
> | edgeclusters/응용 프로그램 | 예 |
> | managedclusters | Yes |
> | managedclusters/nodetypes | 예 |
> | networks | 예 |
> | secretstores | Yes |
> | secretstores/인증서 | No |
> | secretstores/비밀 | 예 |
> | volumes | 예 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 애플리케이션 | 예 |
> | containerGroups | Yes |
> | gateways | 예 |
> | networks | 예 |
> | secrets | 예 |
> | volumes | 예 |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | providerRegistrations | No |
> | providerRegistrations resourceTypeRegistrations | 예 |
> | rollouts | 예 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | SignalR | Yes |
> | SignalR/eventGridFilters | No |

## <a name="microsoftsingularity"></a>특이성

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |
> | 계정/accountQuotaPolicies | No |
> | 계정/그룹 정책 | No |
> | 계정/작업 | No |
> | 계정/storageContainers | 예 |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | hybridUseBenefits | 예 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | applicationDefinitions | Yes |
> | 애플리케이션 | 예 |
> | jitRequests | Yes |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | managedInstances | Yes |
> | managedInstances/데이터베이스 | Yes |
> | managedInstances/databases/backupShortTermRetentionPolicies | No |
> | managedInstances/데이터베이스/스키마/테이블/열/sensitivityLabels | No |
> | managedInstances/databases/vulnerabilityAssessments | No |
> | managedInstances/databases/vulnerabilityAssessments/rules/기준선 | No |
> | managedInstances/가 나 보호기 | No |
> | managedInstances/키 | No |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | No |
> | managedInstances/vulnerabilityAssessments | 예 |
> | servers | 예 |
> | servers / administrators | 예 |
> | servers/communicationLinks | 예 |
> | servers / databases | 예 |
> | 서버/서버 보호기 | No |
> | servers / firewallRules | 예 |
> | servers / keys | 예 |
> | servers/restorableDroppedDatabases | 예 |
> | servers / serviceobjectives | 예 |
> | servers/tdeCertificates | No |
> | virtualClusters | No |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Yes |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | No |
> | SqlVirtualMachines | Yes |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | deletedAccounts | No |
> | storageAccounts | Yes |
> | storageAccounts / blobServices | No |
> | storageAccounts / fileServices | No |
> | storageAccounts/queueServices | No |
> | storageAccounts/서비스 | No |
> | storageAccounts/services/metricDefinitions | No |
> | storageAccounts/tableServices | 예 |
> | usages | 예 |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | caches | Yes |
> | 캐시/storageTargets 가져오기 | No |
> | usageModels | No |

## <a name="microsoftstoragereplication"></a>StorageReplication

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | replicationGroups | 예 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | storageSyncServices | Yes |
> | storageSyncServices/registeredServers | No |
> | storageSyncServices / syncGroups | No |
> | storageSyncServices/syncGroups/cloudEndpoints | No |
> | storageSyncServices/syncGroups/serverEndpoints | No |
> | storageSyncServices/워크플로 | 예 |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | storageSyncServices | Yes |
> | storageSyncServices/registeredServers | No |
> | storageSyncServices / syncGroups | No |
> | storageSyncServices/syncGroups/cloudEndpoints | No |
> | storageSyncServices/syncGroups/serverEndpoints | No |
> | storageSyncServices/워크플로 | 예 |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | storageSyncServices | Yes |
> | storageSyncServices/registeredServers | No |
> | storageSyncServices / syncGroups | No |
> | storageSyncServices/syncGroups/cloudEndpoints | No |
> | storageSyncServices/syncGroups/serverEndpoints | No |
> | storageSyncServices/워크플로 | 예 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | managers | Yes |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | clusters | 예 |
> | 클러스터/privateEndpoints | 예 |
> | streamingjobs | 예 |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | acceptChangeTenant | No |
> | 별칭 | No |
> | cancel | 예 |
> | Changeten앤틸리스 요청 | No |
> | Changeten앤틸리스 상태 | No |
> | CreateSubscription | 예 |
> | enable | 예 |
> | 이름 바꾸기 | 예 |
> | SubscriptionDefinitions | No |
> | SubscriptionOperations | 예 |
> | 구독 | 예 |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | privateLinkHubs | Yes |
> | workspaces | 예 |
> | 작업 영역/bigDataPools | Yes |
> | 작업 영역/작업 상태 | No |
> | 작업 영역/a s 데이터베이스 | Yes |
> | workspace/sqlPools | Yes |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | environments | 예 |
> | environments / accessPolicies | 예 |
> | environments / eventsources | 예 |
> | environments / referenceDataSets | Yes |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | stores | 예 |
> | 매장/Accesspolicy | 예 |
> | stores / services | 예 |
> | stores / services / tokens | 예 |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | imageTemplates | Yes |
> | imageTemplates/runOutputs | No |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | ArcZones | Yes |
> | ResourcePools | Yes |
> | Vcenter | Yes |
> | VirtualMachines | Yes |
> | VirtualMachineTemplates | Yes |
> | VirtualNetworks | Yes |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | dedicatedCloudNodes | Yes |
> | dedicatedCloudServices | Yes |
> | virtualMachines | Yes |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 디바이스 | Yes |
> | registeredSubscriptions | No |
> | 판매 | No |
> | 공급 업체/sku | No |
> | 공급 업체/vnfs | No |
> | virtualNetworkFunctionSkus | 예 |
> | vnfs | Yes |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |
> | 플랜 | Yes |
> | registeredSubscriptions | 예 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | apiManagementAccounts | No |
> | Apimanagementaccount/apiAcls | No |
> | Apimanagementaccount/api | No |
> | Apimanagementaccount/api/apiAcls | No |
> | Apimanagementaccount/api/connectionAcls | No |
> | Apimanagementaccount/api/연결 | No |
> | Apimanagementaccount/api/connections/connectionAcls | No |
> | Apimanagementaccount/api/localizedDefinitions | No |
> | Apimanagementaccount/connectionAcls | No |
> | Apimanagementaccount/연결 | No |
> | billingMeters | 예 |
> | certificates | Yes |
> | connectionGateways | 예 |
> | connections | 예 |
> | customApis | Yes |
> | deletedSites | No |
> | hostingEnvironments | Yes |
> | hostingEnvironments/eventGridFilters | No |
> | hostingEnvironments/multiRolePools | No |
> | hostingEnvironments/이상 풀 | No |
> | kubeEnvironments | Yes |
> | publishingUsers | 예 |
> | 동영상 추천 기능 | 예 |
> | resourceHealthMetadata | 예 |
> | runtimes | 예 |
> | serverFarms | Yes |
> | serverFarms/eventGridFilters | No |
> | serverFarms/firstPartyApps | No |
> | serverFarms/firstPartyApps/keyVaultSettings | 예 |
> | sites | 예 |
> | 사이트/구성  | No |
> | sites/eventGridFilters | No |
> | 사이트/hostNameBindings | No |
> | 사이트/네트워크 구성 | 예 |
> | sites / premieraddons | 예 |
> | sites / slots | 예 |
> | 사이트/슬롯/eventGridFilters 필터 | No |
> | 사이트/슬롯/hostNameBindings | No |
> | 사이트/슬롯/네트워크 구성 | No |
> | sourceControls | No |
> | staticSites | Yes |
> | validate | 예 |
> | verifyHostingEnvironmentVnet | No |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | diagnosticSettings | No |
> | diagnosticSettingsCategories | No |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | multipleActivationKeys | Yes |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | DeviceServices | Yes |

## <a name="microsoftworkloadbuilder"></a>WorkloadBuilder

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 작업 | Yes |
> | 워크 로드/인스턴스 | No |
> | 워크 로드/버전 | No |
> | 워크 로드/버전/아티팩트 | 예 |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | components | 예 |
> | componentsSummary | No |
> | monitorInstances | No |
> | monitorInstancesSummary | 예 |
> | monitors | 예 |
> | notificationSettings | 예 |

## <a name="next-steps"></a>다음 단계

쉼표로 구분된 값의 파일과 동일한 데이터를 가져오려면 [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv)를 다운로드합니다.