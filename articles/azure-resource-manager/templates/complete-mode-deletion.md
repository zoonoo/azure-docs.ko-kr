---
title: 전체 모드 삭제
description: 리소스 종류가 Azure Resource Manager 템플릿에서 전체 모드 삭제를 처리하는 방법을 보여줍니다.
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: e288d3af080a96616026e94ac7be352a7684c181
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327666"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>완료 모드 배포를 위한 Azure 리소스의 삭제

이 문서에서는 완료 모드로 배포된 템플릿에 없는 경우 리소스 종류가 삭제를 처리하는 방법을 설명합니다.

**예** 로 표시 된 리소스 유형은 형식이 전체 모드로 배포 된 템플릿에 없는 경우 삭제 됩니다.

로 표시 된 리소스 종류는 템플릿에 없을 때 자동으로 삭제 **되지 않습니다.** 그러나 부모 리소스를 삭제 하는 경우 삭제 됩니다. 동작에 대한 전체 설명은 [Azure Resource Manager 배포 모드](deployment-modes.md)를 참조하세요.

[템플릿에서 둘 이상의 리소스 그룹](cross-scope-deployment.md)에 배포 하는 경우 배포 작업에 지정 된 리소스 그룹의 리소스를 삭제할 수 있습니다. 보조 리소스 그룹의 리소스는 삭제 되지 않습니다.

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
> | DomainServices | 예 |
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
> | advisorScore | 예 |
> | 구성 | 예 |
> | generateRecommendations | 예 |
> | metadata | 예 |
> | 동영상 추천 기능 | 예 |
> | suppressions | 예 |

## <a name="microsoftagfoodplatform"></a>AgFoodPlatform

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | farmBeats | 예 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | actionRules | 예 |
> | 경고 | 예 |
> | alertsList | 예 |
> | alertsMetaData | 예 |
> | alertsSummary | 예 |
> | alertsSummaryList | 예 |
> | smartDetectorAlertRules | 예 |
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
> | validateServiceName | 예 |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | configurationStores | 예 |
> | configurationStores/eventGridFilters | 예 |
> | configurationStores/keyValues | 예 |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | Spring | 예 |
> | 스프링/앱 | 예 |
> | 스프링/앱/배포 | 예 |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | attestationProviders | 예 |
> | defaultProviders | 예 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | accessReviewScheduleDefinitions | 예 |
> | accessReviewScheduleSettings | 예 |
> | classicAdministrators | 예 |
> | dataAliases | 예 |
> | denyAssignments | 예 |
> | elevateAccess | 예 |
> | findOrphanRoleAssignments | 예 |
> | locks | 예 |
> | 권한 | 예 |
> | policyAssignments | 예 |
> | policyDefinitions | 예 |
> | policyExemptions | 예 |
> | policySetDefinitions | 예 |
> | privateLinkAssociations | 예 |
> | providerOperations | 예 |
> | resourceManagementPrivateLinks | 예 |
> | roleAssignments | 예 |
> | roleAssignmentsUsageMetrics | 예 |
> | roleDefinitions | 예 |

## <a name="microsoftautomanage"></a>Microsoft Automanage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |
> | configurationprofil | 예 |
> | configurationProfilePreferences 설정 | 예 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | automationAccounts | 예 |
> | automationAccounts/구성 | 예 |
> | automationAccounts/job | 예 |
> | automationAccounts/privateEndpointConnectionProxies | 예 |
> | automationAccounts/privateEndpointConnections | 예 |
> | automationAccounts/privateLinkResources | 예 |
> | automationAccounts / runbooks | 예 |
> | automationAccounts/softwareUpdateConfigurations | 예 |
> | automationAccounts / webhooks | 예 |

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | privateClouds | 예 |
> | privateClouds/권한 부여 | 예 |
> | privateClouds/클러스터 | 예 |
> | privateClouds / globalReachConnections | 예 |
> | privateClouds / hcxEnterpriseSites | 예 |
> | privateClouds / workloadNetworks | 예 |
> | privateClouds/workloadNetworks/dhcpConfigurations | 예 |
> | privateClouds/workloadNetworks/게이트웨이 | 예 |
> | privateClouds / workloadNetworks / portMirroringProfiles | 예 |
> | privateClouds/workloadNetworks/세그먼트 | 예 |
> | privateClouds/workloadNetworks/virtualMachines | 예 |
> | privateClouds/workloadNetworks/vmGroups | 예 |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | environments | 예 |
> | 환경/계정 | 예 |
> | 환경/계정/네임 스페이스 | 예 |
> | 환경/계정/네임 스페이스/구성 | 예 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | b2cDirectories | 예 |
> | b2ctenants | 예 |
> | guestUsages | 예 |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | dataControllers | 예 |
> | postgresInstances | 예 |
> | sqlManagedInstances | 예 |
> | sqlServerInstances | 예 |
> | sqlServerRegistrations | 예 |
> | sqlServerRegistrations/sqlServers | 예 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | cloudManifestFiles | 예 |
> | edgeSubscriptions | 예 |
> | linkedSubscriptions | 예 |
> | registrations | 예 |
> | 등록/customerSubscriptions | 예 |
> | registrations / products | 예 |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | clusters | 예 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | batchAccounts | 예 |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | billingAccounts | 예 |
> | billingAccounts/규약 | 예 |
> | billingAccounts / billingPermissions | 예 |
> | billingAccounts / billingProfiles | 예 |
> | billingAccounts / billingProfiles / billingPermissions | 예 |
> | billingAccounts / billingProfiles / billingRoleAssignments | 예 |
> | billingAccounts / billingProfiles / billingRoleDefinitions | 예 |
> | billingAccounts / billingProfiles / billingSubscriptions | 예 |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | 예 |
> | billingAccounts/billingProfiles/고객 | 예 |
> | billingAccounts/billingProfiles/지침 | 예 |
> | billingAccounts/billingProfiles/송장 | 예 |
> | billingAccounts/billingProfiles/청구서/가격표 | 예 |
> | billingAccounts/billingProfiles/송장/트랜잭션 | 예 |
> | billingAccounts / billingProfiles / invoiceSections | 예 |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | 예 |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | 예 |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | 예 |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | 예 |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | 예 |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | 예 |
> | billingAccounts/billingProfiles/invoiceSections/products | 예 |
> | billingAccounts/billingProfiles/invoiceSections/products/transfer | 예 |
> | billingAccounts/billingProfiles/invoiceSections/products/updateAutoRenew | 예 |
> | billingAccounts/billingProfiles/invoiceSections/트랜잭션 | 예 |
> | billingAccounts/billingProfiles/invoiceSections/transfer | 예 |
> | billingAccounts / BillingProfiles / patchOperations | 예 |
> | billingAccounts / billingProfiles / paymentMethods | 예 |
> | billingAccounts/billingProfiles/정책 | 예 |
> | billingAccounts/billingProfiles/가격표 | 예 |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | 예 |
> | billingAccounts/billingProfiles/제품 | 예 |
> | billingAccounts/billingProfiles/예약 | 예 |
> | billingAccounts/billingProfiles/트랜잭션 | 예 |
> | billingAccounts / billingProfiles / validateDetachPaymentMethodEligibility | 예 |
> | billingAccounts / billingRoleAssignments | 예 |
> | billingAccounts / billingRoleDefinitions | 예 |
> | billingAccounts / billingSubscriptions | 예 |
> | billingAccounts/billingSubscriptions/송장 | 예 |
> | billingAccounts / createBillingRoleAssignment | 예 |
> | billingAccounts / createInvoiceSectionOperations | 예 |
> | billingAccounts/고객 | 예 |
> | billingAccounts/customers/billingPermissions | 예 |
> | billingAccounts/customers/billingSubscriptions | 예 |
> | billingAccounts/customers/initiateTransfer | 예 |
> | billingAccounts/고객/정책 | 예 |
> | billingAccounts/고객/제품 | 예 |
> | billingAccounts/고객/트랜잭션 | 예 |
> | billingAccounts/고객/전송 | 예 |
> | billingAccounts/부서 | 예 |
> | billingAccounts/부서/billingPermissions | 예 |
> | billingAccounts/부서/billingRoleAssignments | 예 |
> | billingAccounts/부서/billingRoleDefinitions | 예 |
> | billingAccounts / enrollmentAccounts | 예 |
> | billingAccounts / enrollmentAccounts / billingPermissions | 예 |
> | billingAccounts / enrollmentAccounts / billingRoleAssignments | 예 |
> | billingAccounts / enrollmentAccounts / billingRoleDefinitions | 예 |
> | billingAccounts/송장 | 예 |
> | billingAccounts/송장/트랜잭션 | 예 |
> | billingAccounts / invoiceSections | 예 |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | 예 |
> | billingAccounts / invoiceSections / billingSubscriptions | 예 |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | 예 |
> | billingAccounts/invoiceSections/상승 | 예 |
> | billingAccounts / invoiceSections / initiateTransfer | 예 |
> | billingAccounts / invoiceSections / patchOperations | 예 |
> | billingAccounts/invoiceSections/제품 Moveoperations | 예 |
> | billingAccounts/invoiceSections/제품 | 예 |
> | billingAccounts/invoiceSections/제품/이전 | 예 |
> | billingAccounts/invoiceSections/products/updateAutoRenew | 예 |
> | billingAccounts/invoiceSections/트랜잭션 | 예 |
> | billingAccounts/invoiceSections/전송 | 예 |
> | billingAccounts/lineOfCredit | 예 |
> | billingAccounts / patchOperations | 예 |
> | billingAccounts / paymentMethods | 예 |
> | billingAccounts/제품 | 예 |
> | billingAccounts/예약 | 예 |
> | billingAccounts/트랜잭션 | 예 |
> | billingPeriods | 예 |
> | billingPermissions | 예 |
> | billingProperty | 예 |
> | billingRoleAssignments | 예 |
> | billingRoleDefinitions | 예 |
> | createBillingRoleAssignment | 예 |
> | departments | 예 |
> | enrollmentAccounts | 예 |
> | invoices | 예 |
> | transfers | 예 |
> | 전송/acceptTransfer | 예 |
> | 전송/declineTransfer | 예 |
> | 전송/operationStatus | 예 |
> | 전송/validateTransfer | 예 |
> | validateAddress | 예 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | mapApis | 예 |
> | updateCommunicationPreference | 예 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | blockchainMembers | 예 |
> | cordaMembers | 예 |
> | watchers | 예 |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | TokenServices | 예 |
> | TokenServices/BlockchainNetworks | 예 |
> | TokenServices/그룹 | 예 |
> | TokenServices/그룹/계정 | 예 |
> | TokenServices/TokenTemplates | 예 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | blueprintAssignments | 예 |
> | blueprintAssignments / assignmentOperations | 예 |
> | blueprintAssignments/작업 | 예 |
> | blueprints | 예 |
> | blueprints / artifacts | 예 |
> | blueprints / versions | 예 |
> | blueprints / versions / artifacts | 예 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | botServices | 예 |
> | botServices / channels | 예 |
> | botServices/연결 | 예 |
> | 언어 | 예 |
> | 템플릿 | 예 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | Redis | 예 |
> | Redis/EventGridFilters | 예 |
> | Redis/privateEndpointConnectionProxies | 예 |
> | Redis/privateEndpointConnectionProxies/validate | 예 |
> | Redis/privateEndpointConnections | 예 |
> | Redis/privateLinkResources | 예 |
> | redisEnterprise | 예 |
> | RedisEnterprise/privateEndpointConnectionProxies | 예 |
> | RedisEnterprise/privateEndpointConnectionProxies/validate | 예 |
> | RedisEnterprise/privateEndpointConnections | 예 |
> | RedisEnterprise/privateLinkResources | 예 |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | appliedReservations | 예 |
> | autoQuotaIncrease | 예 |
> | calculateExchange | 예 |
> | calculatePrice | 예 |
> | calculatePurchasePrice | 예 |
> | catalogs | 예 |
> | commercialReservationOrders | 예 |
> | 교환 | 예 |
> | ownReservations | 예 |
> | placePurchaseOrder | 예 |
> | reservationOrders | 예 |
> | reservationOrders / calculateRefund | 예 |
> | reservationOrders/병합 | 예 |
> | reservationOrders/예약 | 예 |
> | reservationOrders/예약/수정 버전 | 예 |
> | reservationOrders/반환 | 예 |
> | reservationOrders/분할 | 예 |
> | reservationOrders/교환 | 예 |
> | reservations | 예 |
> | resourceProviders | 예 |
> | 리소스 | 예 |
> | validateReservationOrder | 예 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | 예 |
> | CdnWebApplicationFirewallPolicies | 예 |
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
> | certificateOrders | 예 |
> | certificateOrders/certificate | 예 |
> | validateCertificateRegistrationInformation | 예 |

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | profile | 예 |
> | resourceChanges | 예 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | capabilities | 예 |
> | domainNames | 예 |
> | domainNames/기능 | 예 |
> | domainNames/internalLoadBalancers 장치 | 예 |
> | domainNames/serviceCertificates | 예 |
> | domainNames/슬롯 | 예 |
> | domainNames/슬롯/역할 | 예 |
> | domainNames/슬롯/역할/metricDefinitions | 예 |
> | domainNames/슬롯/역할/메트릭 | 예 |
> | moveSubscriptionResources | 예 |
> | operatingSystemFamilies | 예 |
> | operatingSystems | 예 |
> | quotas | 예 |
> | resourceTypes | 예 |
> | validateSubscriptionMoveAvailability | 예 |
> | virtualMachines | 예 |
> | virtualMachines/diagnosticSettings | 예 |
> | virtualMachines/metricDefinitions | 예 |
> | virtualMachines/메트릭 | 예 |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | classicInfrastructureResources | 예 |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | capabilities | 예 |
> | expressRouteCrossConnections | 예 |
> | expressRouteCrossConnections/피어 링 | 예 |
> | gatewaySupportedDevices | 예 |
> | networkSecurityGroups | 예 |
> | quotas | 예 |
> | reservedIps | 예 |
> | virtualNetworks | 예 |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | 예 |
> | virtualNetworks / virtualNetworkPeerings | 예 |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | capabilities | 예 |
> | disks | 예 |
> | images | 예 |
> | osImages | 예 |
> | osPlatformImages | 예 |
> | publicImages | 예 |
> | quotas | 예 |
> | storageAccounts | 예 |
> | storageAccounts / blobServices | 예 |
> | storageAccounts / fileServices | 예 |
> | storageAccounts/metricDefinitions | 예 |
> | storageAccounts/메트릭 | 예 |
> | storageAccounts/queueServices | 예 |
> | storageAccounts/서비스 | 예 |
> | storageAccounts/services/diagnosticSettings | 예 |
> | storageAccounts/services/metricDefinitions | 예 |
> | storageAccounts/서비스/메트릭 | 예 |
> | storageAccounts/tableServices | 예 |
> | storageAccounts/vmImages | 예 |
> | vmImages | 예 |

## <a name="microsoftcodespaces"></a>Microsoft Codespaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 플랜 | 예 |
> | registeredSubscriptions | 예 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |
> | 계정/privateEndpointConnectionProxies | 예 |
> | 계정/privateEndpointConnections | 예 |
> | 계정/privateLinkResources | 예 |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | RateCard | 예 |
> | UsageAggregates | 예 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | availabilitySets | 예 |
> | cloudServices | 예 |
> | cloudServices/networkInterfaces | 예 |
> | cloudServices/publicIPAddresses | 예 |
> | cloudServices/roleInstances | 예 |
> | cloudServices/roleInstances/networkInterfaces | 예 |
> | cloudServices/역할 | 예 |
> | diskAccesses | 예 |
> | diskEncryptionSets | 예 |
> | disks | 예 |
> | galleries | 예 |
> | galleries / applications | 예 |
> | 갤러리/응용 프로그램/버전 | 예 |
> | galleries / images | 예 |
> | galleries / images / versions | 예 |
> | 호스트 그룹 | 예 |
> | 호스트 그룹/호스트 | 예 |
> | images | 예 |
> | proximityPlacementGroups | 예 |
> | restorePointCollections | 예 |
> | restorePointCollections / restorePoints | 예 |
> | sharedVMExtensions | 예 |
> | sharedVMExtensions/버전 | 예 |
> | sharedVMImages | 예 |
> | sharedVMImages/버전 | 예 |
> | 스냅샷 | 예 |
> | sshPublicKeys | 예 |
> | virtualMachines | 예 |
> | virtualMachines/확장 | 예 |
> | virtualMachines/metricDefinitions | 예 |
> | virtualMachines/runCommands | 예 |
> | virtualMachineScaleSets | 예 |
> | virtualMachineScaleSets/확장 | 예 |
> | virtualMachineScaleSets/networkInterfaces | 예 |
> | virtualMachineScaleSets/publicIPAddresses | 예 |
> | virtualMachineScaleSets/virtualMachines | 예 |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | 예 |

## <a name="microsoftconnectedcache"></a>ConnectedCache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | CacheNodes | 예 |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | AggregatedCost | 예 |
> | 잔액 | 예 |
> | 예산 | 예 |
> | Charges | 예 |
> | CostTags | 예 |
> | credits | 예 |
> | events | 예 |
> | 예측 | 예 |
> | lots | 예 |
> | Marketplace | 예 |
> | Pricesheets | 예 |
> | products | 예 |
> | ReservationDetails | 예 |
> | ReservationRecommendationDetails | 예 |
> | ReservationRecommendations | 예 |
> | ReservationSummaries | 예 |
> | ReservationTransactions | 아니요 |
> | 태그 | 예 |
> | tenants | 예 |
> | 용어 | 예 |
> | UsageDetails | 예 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | containerGroups | 예 |
> | serviceAssociationLinks | 예 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | registries | 예 |
> | 레지스트리/agentPools | 예 |
> | registries / builds | 예 |
> | registries / builds / cancel | 예 |
> | 레지스트리/빌드/getLogLink | 예 |
> | registries / buildTasks | 예 |
> | 레지스트리/buildTasks/단계 | 예 |
> | 레지스트리/eventGridFilters | 예 |
> | 레지스트리/exportPipelines | 예 |
> | 레지스트리/generateCredentials | 예 |
> | 레지스트리/getBuildSourceUploadUrl | 예 |
> | 레지스트리/GetCredentials | 예 |
> | 레지스트리/importImage | 예 |
> | 레지스트리/importPipelines | 예 |
> | 레지스트리/pipelineRuns | 예 |
> | 레지스트리/privateEndpointConnectionProxies | 예 |
> | 레지스트리/privateEndpointConnectionProxies/유효성 검사 | 예 |
> | 레지스트리/privateEndpointConnections | 예 |
> | 레지스트리/privateLinkResources | 예 |
> | 레지스트리/queueBuild | 예 |
> | 레지스트리/regenerateCredential | 예 |
> | 레지스트리/regenerateCredentials | 예 |
> | registries / replications | 예 |
> | registries / runs | 예 |
> | registries / runs / cancel | 예 |
> | 레지스트리/scheduleRun | 예 |
> | registries / scopeMaps | 예 |
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
> | containerServices | 예 |
> | managedClusters | 예 |
> | openShiftManagedClusters | 예 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 경고 | 예 |
> | BillingAccounts | 예 |
> | 예산 | 예 |
> | CloudConnectors | 예 |
> | 커넥터 | 예 |
> | costAllocationRules | 예 |
> | Departments | 예 |
> | 차원 | 예 |
> | EnrollmentAccounts | 예 |
> | 내보내기 | 예 |
> | ExternalBillingAccounts | 예 |
> | ExternalBillingAccounts/경고 | 예 |
> | ExternalBillingAccounts/차원 | 예 |
> | ExternalBillingAccounts/예측 | 예 |
> | ExternalBillingAccounts/쿼리 | 예 |
> | ExternalSubscriptions | 예 |
> | ExternalSubscriptions/Alerts | 예 |
> | ExternalSubscriptions/차원 | 예 |
> | ExternalSubscriptions/예측 | 예 |
> | ExternalSubscriptions/Query | 예 |
> | 예측 | 예 |
> | 자세한 정보 | 예 |
> | 쿼리 | 예 |
> | register | 예 |
> | Reportconfigs | 예 |
> | 보고서 | 예 |
> | 설정 | 예 |
> | showbackRules | 예 |
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
> | resourceProviders | 예 |

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
> | DataBoxEdgeDevices | 예 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | workspaces | 예 |
> | workspace/dbWorkspaces | 예 |
> | 작업 영역/storageEncryption | 예 |
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
> | dataFactories | 예 |
> | dataFactories/diagnosticSettings | 예 |
> | dataFactories/metricDefinitions | 예 |
> | dataFactorySchema | 예 |
> | factories | 예 |
> | factories / integrationRuntimes | 예 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |
> | accounts / dataLakeStoreAccounts | 예 |
> | accounts / storageAccounts | 예 |
> | 계정/storageAccounts/컨테이너 | 예 |
> | 계정/transferAnalyticsUnits | 예 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |
> | 계정/eventGridFilters | 예 |
> | accounts / firewallRules | 예 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | services | 예 |
> | services / projects | 예 |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | BackupVaults | 예 |
> | ResourceOperationGateKeepers 키퍼 | 예 |

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
> | servers/privateEndpointConnectionProxies | 예 |
> | servers/privateEndpointConnections | 예 |
> | servers/privateLinkResources | 예 |
> | 서버/queryTexts | 예 |
> | servers/recoverableServers | 예 |
> | 서버/시작 | 예 |
> | 서버/중지 | 예 |
> | servers/topQueryStatistics | 예 |
> | servers / virtualNetworkRules | 예 |
> | 서버/waitStatistics | 예 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | flexibleServers | 예 |
> | servers | 예 |
> | servers / advisors | 예 |
> | servers / keys | 예 |
> | servers/privateEndpointConnectionProxies | 예 |
> | servers/privateEndpointConnections | 예 |
> | servers/privateLinkResources | 예 |
> | 서버/queryTexts | 예 |
> | servers/recoverableServers | 예 |
> | 서버/시작 | 예 |
> | 서버/중지 | 예 |
> | servers/topQueryStatistics | 예 |
> | 서버/업그레이드 | 예 |
> | servers / virtualNetworkRules | 예 |
> | 서버/waitStatistics | 예 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | flexibleServers | 예 |
> | serverGroups | 예 |
> | servers | 예 |
> | servers / advisors | 예 |
> | servers / keys | 예 |
> | servers/privateEndpointConnectionProxies | 예 |
> | servers/privateEndpointConnections | 예 |
> | servers/privateLinkResources | 예 |
> | 서버/queryTexts | 예 |
> | servers/recoverableServers | 예 |
> | servers/topQueryStatistics | 예 |
> | servers / virtualNetworkRules | 예 |
> | 서버/waitStatistics | 예 |
> | serversv2 | 예 |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | artifactSources | 예 |
> | rollouts | 예 |
> | serviceTopologies | 예 |
> | serviceTopologies/서비스 | 예 |
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
> | hostpools / sessionhosts | 예 |
> | hostpools / sessionhosts / usersessions | 예 |
> | hostpools / usersessions | 예 |
> | workspaces | 예 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | ElasticPools | 예 |
> | ElasticPools / IotHubTenants | 예 |
> | ElasticPools/IotHubTenants/securitySettings | 예 |
> | IotHubs | 예 |
> | IotHubs/eventGridFilters | 예 |
> | IotHubs/securitySettings | 예 |
> | ProvisioningServices | 예 |
> | usages | 예 |

## <a name="microsoftdeviceupdate"></a>Microsoft DeviceUpdate

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |
> | 계정/인스턴스 | 예 |

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
> | labcenters | 예 |
> | labs | 예 |
> | labs / environments | 예 |
> | labs/serviceRunners | 예 |
> | labs/virtualMachines | 예 |
> | schedules | 예 |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | digitalTwinsInstances | 예 |
> | digitalTwinsInstances/끝점 | 예 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | databaseAccountNames | 예 |
> | databaseAccounts | 예 |
> | restorableDatabaseAccounts | 예 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | domains | 예 |
> | 도메인/도메인 소유자의 식별자 | 예 |
> | generateSsoRequest | 예 |
> | topLevelDomains | 예 |
> | validateDomainRegistrationInformation | 예 |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | lcsprojects | 예 |
> | lcsprojects/clouddeployments | 예 |
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
> | eventSubscriptions | 예 |
> | extensionTopics | 예 |
> | 네임 스페이스 | 예 |
> | 네임 스페이스/a s p 채널 | 예 |
> | 파트너 등록 | 예 |
> | 항목 항목 | 예 |
> | 항목 토픽/eventSubscriptions | 예 |
> | systemTopics | 예 |
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
> | experimentWorkspaces | 예 |

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
> | subscriptionFeatureRegistrations | 예 |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | enroll | 예 |
> | galleryitems | 예 |
> | generateartifactaccessuri | 예 |
> | myareas | 예 |
> | myareas/areas | 예 |
> | myareas/areas/areas | 예 |
> | myareas/areas/areas/galleryitems | 예 |
> | myareas/areas/galleryitems | 예 |
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
> | autoManagedAccounts | 예 |
> | autoManagedVmConfigurationProfiles | 예 |
> | configurationprofil | 예 |
> | guestConfigurationAssignments | 예 |
> | software | 예 |
> | softwareUpdateProfile | 예 |
> | 업데이트 | 예 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | hanaInstances | 예 |
> | sapMonitors | 예 |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | dedicatedHSMs | 예 |

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
> | 서비스/iomtconnectors | 예 |
> | 서비스/iomtconnectors/연결 | 예 |
> | services/iomtconnectors/매핑 | 예 |
> | 서비스/privateEndpointConnectionProxies | 예 |
> | 서비스/privateEndpointConnections | 예 |
> | 서비스/privateLinkResources | 예 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | machines | 예 |
> | machines/assessPatches | 예 |
> | machines / extensions | 예 |
> | 컴퓨터/installPatches | 예 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | dataManagers | 예 |

## <a name="microsofthybridnetwork"></a>HybridNetwork

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 디바이스 | 예 |
> | registeredSubscriptions | 예 |
> | 판매 | 예 |
> | 공급 업체/vendorskus | 예 |
> | 공급 업체/vendorskus/previewSubscriptions | 예 |
> | virtualnetworkfunctions | 예 |
> | virtualnetworkfunctionvendors | 예 |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | components | 예 |
> | networkScopes | 예 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | jobs | 예 |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | diagnosticSettings | 예 |
> | diagnosticSettingsCategories | 예 |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | appTemplates | 예 |
> | IoTApps | 예 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 그래프 | 예 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | deletedVaults | 예 |
> | hsmPools | 예 |
> | managedHSMs | 예 |
> | vaults | 예 |
> | 자격 증명 모음/Accesspolicy | 예 |
> | 자격 증명 모음/eventGridFilters | 예 |
> | 자격 증명 모음/키 | 예 |
> | 자격 증명 모음/키/버전 | 예 |
> | vaults / secrets | 예 |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | connectedClusters | 예 |
> | registeredSubscriptions | 예 |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 확장 | 예 |
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
> | labaccounts | 예 |
> | users | 예 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | hostingEnvironments | 예 |
> | integrationAccounts | 예 |
> | integrationServiceEnvironments | 예 |
> | integrationServiceEnvironments / managedApis | 예 |
> | isolatedEnvironments | 예 |
> | workflows | 예 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | commitmentPlans | 예 |
> | webServices | 예 |
> | 작업 영역 | 예 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | workspaces | 예 |
> | 작업 영역/codeJobs | 예 |
> | workspaces / computes | 예 |
> | workspace/eventGridFilters | 예 |
> | 작업 영역/inferenceEndpoints | 예 |
> | 작업 영역/inferenceEndpoints/배포 | 예 |
> | 작업 영역/Linkedservices.json 및 datasets.json | 예 |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | applyUpdates | 예 |
> | configurationAssignments | 예 |
> | maintenanceConfigurations | 예 |
> | publicMaintenanceConfigurations | 예 |
> | updates | 예 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | Identities | 예 |
> | userAssignedIdentities | 예 |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | managedNetworks | 예 |
> | managedNetworks/managedNetworkGroups | 예 |
> | managedNetworks/Managednetworkpeering정책만 | 예 |
> | 알림 | 예 |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | 예 |
> | registrationAssignments | 예 |
> | registrationDefinitions | 예 |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | getEntities | 예 |
> | managementGroups | 예 |
> | managementGroups/settings | 예 |
> | 리소스 | 예 |
> | startTenantBackfill | 예 |
> | tenantBackfillStatus | 예 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |
> | 계정/eventGridFilters | 예 |
> | 계정/privateAtlases | 예 |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | macc | 예 |
> | offers | 예 |
> | offerTypes | 예 |
> | offerTypes/게시자 | 예 |
> | offerTypes/게시자/제안 | 예 |
> | offerTypes/게시자/제품/계획 | 예 |
> | offerTypes/게시자/제품/계획/규약 | 예 |
> | offerTypes/게시자/제품/계획/configs | 예 |
> | offerTypes/publishers/제품/계획/configs/importImage | 예 |
> | privategalleryitems | 예 |
> | privateStoreClient | 예 |
> | privateStores | 예 |
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
> | classicDevServices | 예 |
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
> | windowsazure.mediaservices/asset/assetFilters | 예 |
> | windowsazure.mediaservices/Contentkeypolicy | 예 |
> | windowsazure.mediaservices/eventGridFilters | 예 |
> | windowsazure.mediaservices/liveEventOperations | 예 |
> | mediaservices / liveEvents | 예 |
> | mediaservices / liveEvents / liveOutputs | 예 |
> | windowsazure.mediaservices/liveOutputOperations | 예 |
> | windowsazure.mediaservices/mediaGraphs | 예 |
> | windowsazure.mediaservices/privateEndpointConnectionOperations | 예 |
> | windowsazure.mediaservices/privateEndpointConnectionProxies | 예 |
> | windowsazure.mediaservices/privateEndpointConnections | 예 |
> | windowsazure.mediaservices/streamingEndpointOperations | 예 |
> | mediaservices / streamingEndpoints | 예 |
> | windowsazure.mediaservices/streamingLocators | 예 |
> | windowsazure.mediaservices/streamingPolicies | 예 |
> | mediaservices / transforms | 예 |
> | mediaservices / transforms / jobs | 예 |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | appClusters | 예 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | assessmentProjects | 예 |
> | migrateprojects | 예 |
> | moveCollections | 예 |
> | projects | 예 |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | 예 |
> | objectUnderstandingAccounts | 예 |
> | remoteRenderingAccounts | 예 |
> | spatialAnchorsAccounts | 예 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | netAppAccounts | 예 |
> | netAppAccounts/accountBackups | 예 |
> | netAppAccounts/capacityPools | 예 |
> | netAppAccounts/capacityPools/볼륨 | 예 |
> | netAppAccounts/capacityPools/볼륨/스냅숏 | 예 |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | applicationGateways | 예 |
> | applicationGatewayWebApplicationFirewallPolicies | 예 |
> | applicationSecurityGroups | 예 |
> | azureFirewallFqdnTags | 예 |
> | azureFirewalls | 예 |
> | bastionHosts | 예 |
> | bgpServiceCommunities | 예 |
> | connections | 예 |
> | ddosCustomPolicies | 예 |
> | ddosProtectionPlans | 예 |
> | dnsOperationStatuses | 예 |
> | dnszones | 예 |
> | dnszones/A | 예 |
> | dnszones/AAAA | 예 |
> | dnszones / all | 예 |
> | dnszones/CAA | 예 |
> | dnszones/CNAME | 예 |
> | dnszones/MX | 예 |
> | dnszones/NS | 예 |
> | dnszones/PTR | 예 |
> | dnszones / recordsets | 예 |
> | dnszones/SOA | 예 |
> | dnszones/SRV | 예 |
> | dnszones/TXT | 예 |
> | expressRouteCircuits | 예 |
> | expressRouteCrossConnections | 예 |
> | expressRouteGateways | 예 |
> | expressRoutePorts | 예 |
> | expressRouteServiceProviders | 예 |
> | firewallPolicies | 예 |
> | frontdoors | 예 |
> | frontdoorWebApplicationFirewallManagedRuleSets | 예 |
> | frontdoorWebApplicationFirewallPolicies | 예 |
> | getDnsResourceReference | 예 |
> | internalNotify | 예 |
> | ipGroups | 예 |
> | loadBalancers | 예 |
> | localNetworkGateways | 예 |
> | natGateways | 예 |
> | networkIntentPolicies | 예 |
> | networkInterfaces | 예 |
> | networkProfiles | 예 |
> | networkSecurityGroups | 예 |
> | networkWatchers | 예 |
> | networkWatchers/connectionMonitors | 예 |
> | networkWatchers/flowLogs | 예 |
> | networkWatchers/lenses | 예 |
> | networkWatchers/ | 예 |
> | p2sVpnGateways | 예 |
> | privateDnsOperationStatuses | 예 |
> | privateDnsZones | 예 |
> | privateDnsZones/A | 예 |
> | privateDnsZones/AAAA | 예 |
> | privateDnsZones/모두 | 예 |
> | privateDnsZones/CNAME | 예 |
> | privateDnsZones/MX | 예 |
> | privateDnsZones/PTR | 예 |
> | privateDnsZones/SOA | 예 |
> | privateDnsZones/SRV | 예 |
> | privateDnsZones/TXT | 예 |
> | privateDnsZones / virtualNetworkLinks | 예 |
> | privateEndpoints | 예 |
> | privateLinkServices | 예 |
> | publicIPAddresses | 예 |
> | publicIPPrefixes | 예 |
> | routeFilters | 예 |
> | routeTables | 예 |
> | serviceEndpointPolicies | 예 |
> | trafficManagerGeographicHierarchies | 예 |
> | trafficmanagerprofiles | 예 |
> | trafficmanagerprofiles/열 지도 | 예 |
> | trafficManagerUserMetricsKeys | 예 |
> | virtualHubs | 예 |
> | virtualNetworkGateways | 예 |
> | virtualNetworks | 예 |
> | virtualNetworks/서브넷 | 예 |
> | virtualNetworkTaps | 예 |
> | virtualWans | 예 |
> | vpnGateways | 예 |
> | vpnSites | 예 |
> | webApplicationFirewallPolicies | 예 |

## <a name="microsoftnotebooks"></a>Microsoft. 전자 필기장

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | NotebookProxies | 예 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 네임스페이스 | 예 |
> | namespaces / notificationHubs | 예 |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | osNamespaces 스페이스 | 예 |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | HyperVSites | 예 |
> | ImportSites | 예 |
> | MasterSites | 예 |
> | ServerSites | 예 |
> | VMwareSites | 예 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | clusters | 예 |
> | deletedWorkspaces | 예 |
> | linkTargets | 예 |
> | storageInsightConfigs | 예 |
> | workspaces | 예 |
> | 작업 영역/데이터 내보내기 | 예 |
> | 작업 영역/데이터 원본 | 예 |
> | 작업 영역/Linkedservices.json 및 datasets.json | 예 |
> | 작업 영역/linkedStorageAccounts | 예 |
> | workspaces / metadata | 예 |
> | workspaces / query | 예 |
> | 작업 영역/scopedPrivateLinkProxies | 예 |

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
> | legacyPeerings | 예 |
> | peerAsns | 예 |
> | peerings | 예 |
> | peeringServiceCountries | 예 |
> | peeringServiceProviders | 예 |
> | peeringServices | 예 |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 증명 | 예 |
> | policyEvents | 예 |
> | policyMetadata | 예 |
> | policyStates | 예 |
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
> | privateLinkServicesForPowerBI | 예 |
> | tenants | 예 |
> | 테 넌 트/작업 영역 | 예 |
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
> | providerRegistrations | 예 |
> | providerRegistrations/defaultRollouts | 예 |
> | providerRegistrations resourceTypeRegistrations | 예 |
> | rollouts | 예 |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 작업 영역 | 예 |

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
> | OpenShiftClusters | 예 |

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
> | resourceChangeDetails | 예 |
> | resourceChanges | 예 |
> | 리소스 | 예 |
> | 자원 기록 | 예 |
> | subscriptionsStatus | 예 |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | availabilityStatuses | 예 |
> | childAvailabilityStatuses | 예 |
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
> | deploymentScripts | 예 |
> | deploymentScripts/logs | 예 |
> | 링크 | 예 |
> | notifyResourceJobs | 예 |
> | providers | 예 |
> | resourceGroups | 예 |
> | 구독 | 예 |
> | templateSpecs | 예 |
> | templateSpecs/버전 | 예 |
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
> | 클라우드 | 예 |
> | VirtualMachines | 예 |
> | VirtualMachineTemplates | 예 |
> | VirtualNetworks | 예 |
> | vmmservers | 예 |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | resourceHealthMetadata | 예 |
> | searchServices | 예 |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | 예 |
> | advancedThreatProtectionSettings | 예 |
> | 경고 | 예 |
> | alertsSuppressionRules | 예 |
> | allowedConnections | 예 |
> | applicationWhitelistings | 예 |
> | assessmentMetadata | 예 |
> | assessments | 예 |
> | autoDismissAlertsRules | 예 |
> | automations | 예 |
> | AutoProvisioningSettings | 예 |
> | Compliances | 예 |
> | 커넥터 | 예 |
> | dataCollectionAgents | 예 |
> | deviceSecurityGroups | 예 |
> | discoveredSecuritySolutions | 예 |
> | externalSecuritySolutions | 예 |
> | InformationProtectionPolicies | 예 |
> | iotDefenderSettings | 예 |
> | iotSecuritySolutions | 예 |
> | IanalyticsModels Securitysolutions/ | 예 |
> | iotSecuritySolutions/analyticsModels/aggregatedAlerts | 예 |
> | iotSecuritySolutions/analyticsModels/aggregatedRecommendations | 예 |
> | iotSecuritySolutions/I이상 경고 | 예 |
> | iotSecuritySolutions/Iotsecuritysolutions | 예 |
> | iotSecuritySolutions/I이상 권장 사항 | 예 |
> | IiotRecommendationTypes Securitysolutions/ | 예 |
> | I이상 센서 | 예 |
> | jitNetworkAccessPolicies | 예 |
> | jitPolicies | 예 |
> | 정책 | 예 |
> | pricings | 예 |
> | regulatoryComplianceStandards | 예 |
> | regulatoryComplianceStandards / regulatoryComplianceControls | 예 |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | 예 |
> | secureScoreControlDefinitions | 예 |
> | secureScoreControls | 예 |
> | secureScores | 예 |
> | secureScores / secureScoreControls | 예 |
> | securityContacts | 예 |
> | securitySolutions | 예 |
> | securitySolutionsReferenceData | 예 |
> | securityStatuses | 예 |
> | securityStatusesSummaries | 예 |
> | serverVulnerabilityAssessments | 예 |
> | 설정 | 예 |
> | sqlVulnerabilityAssessments | 예 |
> | 하위 평가 | 예 |
> | tasks | 예 |
> | topologies | 예 |
> | workspaceSettings | 예 |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | diagnosticSettings | 예 |
> | diagnosticSettingsCategories | 예 |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | aggregations | 예 |
> | alertRules | 예 |
> | alertRuleTemplates | 예 |
> | automationRules | 예 |
> | bookmarks | 예 |
> | cases | 예 |
> | dataConnectors | 예 |
> | dataConnectorsCheckRequirements | 예 |
> | 엔터티 | 예 |
> | entityQueries | 예 |
> | incidents | 예 |
> | officeConsents | 예 |
> | 설정 | 예 |
> | threatIntelligence | 예 |
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
> | containerGroups | 예 |
> | containerGroupSets | 예 |
> | edgeclusters | 예 |
> | edgeclusters/응용 프로그램 | 예 |
> | managedclusters | 예 |
> | managedclusters/nodetypes | 예 |
> | networks | 예 |
> | secretstores | 예 |
> | secretstores/인증서 | 예 |
> | secretstores/비밀 | 예 |
> | volumes | 예 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 애플리케이션 | 예 |
> | containerGroups | 예 |
> | gateways | 예 |
> | networks | 예 |
> | secrets | 예 |
> | volumes | 예 |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | providerRegistrations | 예 |
> | providerRegistrations resourceTypeRegistrations | 예 |
> | rollouts | 예 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | SignalR | 예 |
> | SignalR/eventGridFilters | 예 |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | hybridUseBenefits | 예 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | applicationDefinitions | 예 |
> | 애플리케이션 | 예 |
> | jitRequests | 예 |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | managedInstances | 예 |
> | managedInstances/데이터베이스 | 예 |
> | managedInstances/databases/backupShortTermRetentionPolicies | 예 |
> | managedInstances/데이터베이스/스키마/테이블/열/sensitivityLabels | 예 |
> | managedInstances/databases/vulnerabilityAssessments | 예 |
> | managedInstances/databases/vulnerabilityAssessments/rules/기준선 | 예 |
> | managedInstances/가 나 보호기 | 예 |
> | managedInstances/키 | 예 |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | 예 |
> | managedInstances/vulnerabilityAssessments | 예 |
> | servers | 예 |
> | servers / administrators | 예 |
> | servers/communicationLinks | 예 |
> | servers / databases | 예 |
> | 서버/서버 보호기 | 예 |
> | servers / firewallRules | 예 |
> | servers / keys | 예 |
> | servers/restorableDroppedDatabases | 예 |
> | servers / serviceobjectives | 예 |
> | servers/tdeCertificates | 예 |
> | virtualClusters | 예 |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | 예 |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | 예 |
> | SqlVirtualMachines | 예 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | deletedAccounts | 예 |
> | storageAccounts | 예 |
> | storageAccounts / blobServices | 예 |
> | storageAccounts / fileServices | 예 |
> | storageAccounts/queueServices | 예 |
> | storageAccounts/서비스 | 예 |
> | storageAccounts/services/metricDefinitions | 예 |
> | storageAccounts/tableServices | 예 |
> | usages | 예 |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | caches | 예 |
> | 캐시/storageTargets 가져오기 | 예 |
> | usageModels | 예 |

## <a name="microsoftstoragereplication"></a>StorageReplication

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | replicationGroups | 예 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | storageSyncServices | 예 |
> | storageSyncServices/registeredServers | 예 |
> | storageSyncServices / syncGroups | 예 |
> | storageSyncServices/syncGroups/cloudEndpoints | 예 |
> | storageSyncServices/syncGroups/serverEndpoints | 예 |
> | storageSyncServices/워크플로 | 예 |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | storageSyncServices | 예 |
> | storageSyncServices/registeredServers | 예 |
> | storageSyncServices / syncGroups | 예 |
> | storageSyncServices/syncGroups/cloudEndpoints | 예 |
> | storageSyncServices/syncGroups/serverEndpoints | 예 |
> | storageSyncServices/워크플로 | 예 |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | storageSyncServices | 예 |
> | storageSyncServices/registeredServers | 예 |
> | storageSyncServices / syncGroups | 예 |
> | storageSyncServices/syncGroups/cloudEndpoints | 예 |
> | storageSyncServices/syncGroups/serverEndpoints | 예 |
> | storageSyncServices/워크플로 | 예 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | managers | 예 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | clusters | 예 |
> | streamingjobs | 예 |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | acceptChangeTenant | 예 |
> | 별칭 | 예 |
> | cancel | 예 |
> | Changeten앤틸리스 요청 | 예 |
> | Changeten앤틸리스 상태 | 예 |
> | CreateSubscription | 예 |
> | enable | 예 |
> | 이름 바꾸기 | 예 |
> | SubscriptionDefinitions | 예 |
> | SubscriptionOperations | 예 |
> | 구독 | 예 |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | privateLinkHubs | 예 |
> | workspaces | 예 |
> | 작업 영역/bigDataPools | 예 |
> | 작업 영역/작업 상태 | 예 |
> | 작업 영역/a s 데이터베이스 | 예 |
> | workspace/sqlPools | 예 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | environments | 예 |
> | environments / accessPolicies | 예 |
> | environments / eventsources | 예 |
> | environments / referenceDataSets | 예 |

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
> | imageTemplates | 예 |
> | imageTemplates/runOutputs | 예 |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | ArcZones | 예 |
> | ResourcePools | 예 |
> | Vcenter | 예 |
> | VirtualMachines | 예 |
> | VirtualMachineTemplates | 예 |
> | VirtualNetworks | 예 |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | dedicatedCloudNodes | 예 |
> | dedicatedCloudServices | 예 |
> | virtualMachines | 예 |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 디바이스 | 예 |
> | registeredSubscriptions | 예 |
> | 판매 | 예 |
> | 공급 업체/sku | 예 |
> | 공급 업체/vnfs | 예 |
> | virtualNetworkFunctionSkus | 예 |
> | vnfs | 예 |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 예 |
> | 플랜 | 예 |
> | registeredSubscriptions | 예 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | apiManagementAccounts | 예 |
> | Apimanagementaccount/apiAcls | 예 |
> | Apimanagementaccount/api | 예 |
> | Apimanagementaccount/api/apiAcls | 예 |
> | Apimanagementaccount/api/connectionAcls | 예 |
> | Apimanagementaccount/api/연결 | 예 |
> | Apimanagementaccount/api/connections/connectionAcls | 예 |
> | Apimanagementaccount/api/localizedDefinitions | 예 |
> | Apimanagementaccount/connectionAcls | 예 |
> | Apimanagementaccount/연결 | 예 |
> | billingMeters | 예 |
> | certificates | 예 |
> | connectionGateways | 예 |
> | connections | 예 |
> | customApis | 예 |
> | deletedSites | 예 |
> | hostingEnvironments | 예 |
> | hostingEnvironments/eventGridFilters | 예 |
> | hostingEnvironments/multiRolePools | 예 |
> | hostingEnvironments/이상 풀 | 예 |
> | kubeEnvironments | 예 |
> | publishingUsers | 예 |
> | 동영상 추천 기능 | 예 |
> | resourceHealthMetadata | 예 |
> | runtimes | 예 |
> | serverFarms | 예 |
> | serverFarms/eventGridFilters | 예 |
> | sites | 예 |
> | 사이트/구성  | 예 |
> | sites/eventGridFilters | 예 |
> | 사이트/hostNameBindings | 예 |
> | 사이트/네트워크 구성 | 예 |
> | sites / premieraddons | 예 |
> | sites / slots | 예 |
> | 사이트/슬롯/eventGridFilters 필터 | 예 |
> | 사이트/슬롯/hostNameBindings | 예 |
> | 사이트/슬롯/네트워크 구성 | 예 |
> | sourceControls | 예 |
> | staticSites | 예 |
> | validate | 예 |
> | verifyHostingEnvironmentVnet | 예 |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | diagnosticSettings | 예 |
> | diagnosticSettingsCategories | 예 |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | multipleActivationKeys | 예 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | DeviceServices | 예 |

## <a name="microsoftworkloadbuilder"></a>WorkloadBuilder

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 작업 | 예 |
> | 워크 로드/인스턴스 | 예 |
> | 워크 로드/버전 | 예 |
> | 워크 로드/버전/아티팩트 | 예 |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | components | 예 |
> | componentsSummary | 예 |
> | monitorInstances | 예 |
> | monitorInstancesSummary | 예 |
> | monitors | 예 |
> | notificationSettings | 예 |

## <a name="next-steps"></a>다음 단계

쉼표로 구분된 값의 파일과 동일한 데이터를 가져오려면 [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv)를 다운로드합니다.
