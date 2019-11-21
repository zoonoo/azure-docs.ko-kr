---
title: 전체 모드 삭제
description: 리소스 종류가 Azure Resource Manager 템플릿에서 전체 모드 삭제를 처리하는 방법을 보여줍니다.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 075dd5071d6c59c6cc73fff8d51a495b012665dd
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232683"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>완료 모드 배포를 위한 Azure 리소스의 삭제

이 문서에서는 완료 모드로 배포된 템플릿에 없는 경우 리소스 종류가 삭제를 처리하는 방법을 설명합니다.

The resource types marked with **Yes** are deleted when the type isn't in the template deployed with complete mode.

The resource types marked with **No** aren't automatically deleted when not in the template; however, they're deleted if the parent resource is deleted. 동작에 대한 전체 설명은 [Azure Resource Manager 배포 모드](deployment-modes.md)를 참조하세요.

If you deploy to [more than one resource group in a template](resource-manager-cross-resource-group-deployment.md), resources in the resource group specified in the deployment operation are eligible to be deleted. Resources in the secondary resource groups aren't deleted.

Jump to a resource provider namespace:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.Azconfig](#microsoftazconfig)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
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
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
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
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
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
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
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
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.RemoteApp](#microsoftremoteapp)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | DomainServices | yes |
> | DomainServices / oucontainer | 아닙니다. |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | supportProviders | 아닙니다. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | aadsupportcases | 아닙니다. |
> | addsservices | 아닙니다. |
> | agents | 아닙니다. |
> | anonymousapiusers | 아닙니다. |
> | 구성 | 아닙니다. |
> | 로그 | 아닙니다. |
> | reports | 아닙니다. |
> | servicehealthmetrics | 아닙니다. |
> | services | 아닙니다. |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 구성 | 아닙니다. |
> | generateRecommendations | 아닙니다. |
> | metadata | 아닙니다. |
> | 동영상 추천 기능 | 아닙니다. |
> | suppressions | 아닙니다. |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | actionRules | yes |
> | 경고 | 아닙니다. |
> | alertsList | 아닙니다. |
> | alertsMetaData | 아닙니다. |
> | alertsSummary | 아닙니다. |
> | alertsSummaryList | 아닙니다. |
> | feedback | 아닙니다. |
> | smartDetectorAlertRules | yes |
> | smartDetectorRuntimeEnvironments | 아닙니다. |
> | smartGroups | 아닙니다. |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 서버 | yes |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | reportFeedback | 아닙니다. |
> | 서비스 | yes |
> | validateServiceName | 아닙니다. |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | configurationStores | yes |
> | configurationStores / eventGridFilters | 아닙니다. |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | Spring | yes |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | attestationProviders | 아닙니다. |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | classicAdministrators | 아닙니다. |
> | dataAliases | 아닙니다. |
> | denyAssignments | 아닙니다. |
> | elevateAccess | 아닙니다. |
> | findOrphanRoleAssignments | 아닙니다. |
> | locks | 아닙니다. |
> | 권한 | 아닙니다. |
> | policyAssignments | 아닙니다. |
> | policyDefinitions | 아닙니다. |
> | policySetDefinitions | 아닙니다. |
> | providerOperations | 아닙니다. |
> | roleAssignments | 아닙니다. |
> | roleDefinitions | 아닙니다. |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | automationAccounts | yes |
> | automationAccounts / configurations | yes |
> | automationAccounts / jobs | 아닙니다. |
> | automationAccounts / runbooks | yes |
> | automationAccounts / softwareUpdateConfigurations | 아닙니다. |
> | automationAccounts / webhooks | 아닙니다. |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | configurationStores | yes |
> | configurationStores / eventGridFilters | 아닙니다. |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | environments | 아닙니다. |
> | environments / accounts | 아닙니다. |
> | environments / accounts / namespaces | 아닙니다. |
> | environments / accounts / namespaces / configurations | 아닙니다. |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | b2cDirectories | yes |
> | b2ctenants | 아닙니다. |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | hybridDataManagers | yes |
> | postgresInstances | yes |
> | sqlBigDataClusters | yes |
> | sqlInstances | yes |
> | sqlServerRegistrations | yes |
> | sqlServerRegistrations / sqlServers | 아닙니다. |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | registrations | yes |
> | registrations / customerSubscriptions | 아닙니다. |
> | registrations / products | 아닙니다. |
> | verificationKeys | 아닙니다. |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | batchAccounts | yes |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | billingAccounts | 아닙니다. |
> | billingAccounts / agreements | 아닙니다. |
> | billingAccounts / billingPermissions | 아닙니다. |
> | billingAccounts / billingProfiles | 아닙니다. |
> | billingAccounts / billingProfiles / billingPermissions | 아닙니다. |
> | billingAccounts / billingProfiles / billingRoleAssignments | 아닙니다. |
> | billingAccounts / billingProfiles / billingRoleDefinitions | 아닙니다. |
> | billingAccounts / billingProfiles / billingSubscriptions | 아닙니다. |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | 아닙니다. |
> | billingAccounts / billingProfiles / customers | 아닙니다. |
> | billingAccounts / billingProfiles / invoices | 아닙니다. |
> | billingAccounts / billingProfiles / invoices / pricesheet | 아닙니다. |
> | billingAccounts / billingProfiles / invoiceSections | 아닙니다. |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | 아닙니다. |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | 아닙니다. |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | 아닙니다. |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | 아닙니다. |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | 아닙니다. |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | 아닙니다. |
> | billingAccounts / billingProfiles / invoiceSections / products | 아닙니다. |
> | billingAccounts / billingProfiles / invoiceSections / products / transfer | 아닙니다. |
> | billingAccounts / billingProfiles / invoiceSections / products / updateAutoRenew | 아닙니다. |
> | billingAccounts / billingProfiles / invoiceSections / transactions | 아닙니다. |
> | billingAccounts / billingProfiles / invoiceSections / transfers | 아닙니다. |
> | billingAccounts / BillingProfiles / patchOperations | 아닙니다. |
> | billingAccounts / billingProfiles / paymentMethods | 아닙니다. |
> | billingAccounts / billingProfiles / policies | 아닙니다. |
> | billingAccounts / billingProfiles / pricesheet | 아닙니다. |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | 아닙니다. |
> | billingAccounts / billingProfiles / products | 아닙니다. |
> | billingAccounts / billingProfiles / transactions | 아닙니다. |
> | billingAccounts / billingRoleAssignments | 아닙니다. |
> | billingAccounts / billingRoleDefinitions | 아닙니다. |
> | billingAccounts / billingSubscriptions | 아닙니다. |
> | billingAccounts / billingSubscriptions / invoices | 아닙니다. |
> | billingAccounts / createBillingRoleAssignment | 아닙니다. |
> | billingAccounts / createInvoiceSectionOperations | 아닙니다. |
> | billingAccounts / customers | 아닙니다. |
> | billingAccounts / customers / billingPermissions | 아닙니다. |
> | billingAccounts / customers / billingSubscriptions | 아닙니다. |
> | billingAccounts / customers / initiateTransfer | 아닙니다. |
> | billingAccounts / customers / policies | 아닙니다. |
> | billingAccounts / customers / products | 아닙니다. |
> | billingAccounts / customers / transactions | 아닙니다. |
> | billingAccounts / customers / transfers | 아닙니다. |
> | billingAccounts / departments | 아닙니다. |
> | billingAccounts / enrollmentAccounts | 아닙니다. |
> | billingAccounts / invoices | 아닙니다. |
> | billingAccounts / invoiceSections | 아닙니다. |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | 아닙니다. |
> | billingAccounts / invoiceSections / billingSubscriptions | 아닙니다. |
> | billingAccounts / invoiceSections / billingSubscriptions / transfer | 아닙니다. |
> | billingAccounts / invoiceSections / elevate | 아닙니다. |
> | billingAccounts / invoiceSections / initiateTransfer | 아닙니다. |
> | billingAccounts / invoiceSections / patchOperations | 아닙니다. |
> | billingAccounts / invoiceSections / productMoveOperations | 아닙니다. |
> | billingAccounts / invoiceSections / products | 아닙니다. |
> | billingAccounts / invoiceSections / products / transfer | 아닙니다. |
> | billingAccounts / invoiceSections / products / updateAutoRenew | 아닙니다. |
> | billingAccounts / invoiceSections / transactions | 아닙니다. |
> | billingAccounts / invoiceSections / transfers | 아닙니다. |
> | billingAccounts / lineOfCredit | 아닙니다. |
> | billingAccounts / patchOperations | 아닙니다. |
> | billingAccounts / paymentMethods | 아닙니다. |
> | billingAccounts / products | 아닙니다. |
> | billingAccounts / transactions | 아닙니다. |
> | billingPeriods | 아닙니다. |
> | billingPermissions | 아닙니다. |
> | billingProperty | 아닙니다. |
> | billingRoleAssignments | 아닙니다. |
> | billingRoleDefinitions | 아닙니다. |
> | createBillingRoleAssignment | 아닙니다. |
> | departments | 아닙니다. |
> | enrollmentAccounts | 아닙니다. |
> | invoices | 아닙니다. |
> | transfers | 아닙니다. |
> | transfers / acceptTransfer | 아닙니다. |
> | transfers / declineTransfer | 아닙니다. |
> | transfers / operationStatus | 아닙니다. |
> | transfers / validateTransfer | 아닙니다. |
> | validateAddress | 아닙니다. |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | mapApis | yes |
> | updateCommunicationPreference | 아닙니다. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | blockchainMembers | yes |
> | cordaMembers | yes |
> | 감시자 | yes |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | blueprintAssignments | 아닙니다. |
> | blueprintAssignments / assignmentOperations | 아닙니다. |
> | blueprintAssignments / operations | 아닙니다. |
> | blueprints | 아닙니다. |
> | blueprints / artifacts | 아닙니다. |
> | blueprints / versions | 아닙니다. |
> | blueprints / versions / artifacts | 아닙니다. |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | botServices | yes |
> | botServices / channels | 아닙니다. |
> | botServices / connections | 아닙니다. |
> | 언어 | 아닙니다. |
> | 템플릿 | 아닙니다. |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | Redis | yes |
> | RedisConfigDefinition | 아닙니다. |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | appliedReservations | 아닙니다. |
> | calculateExchange | 아닙니다. |
> | calculatePrice | 아닙니다. |
> | calculatePurchasePrice | 아닙니다. |
> | catalogs | 아닙니다. |
> | commercialReservationOrders | 아닙니다. |
> | exchange | 아닙니다. |
> | placePurchaseOrder | 아닙니다. |
> | reservationOrders | 아닙니다. |
> | reservationOrders / calculateRefund | 아닙니다. |
> | reservationOrders / merge | 아닙니다. |
> | reservationOrders / reservations | 아닙니다. |
> | reservationOrders / reservations / revisions | 아닙니다. |
> | reservationOrders / return | 아닙니다. |
> | reservationOrders / split | 아닙니다. |
> | reservationOrders / swap | 아닙니다. |
> | reservations | 아닙니다. |
> | 리소스 | 아닙니다. |
> | validateReservationOrder | 아닙니다. |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | 아닙니다. |
> | CdnWebApplicationFirewallPolicies | yes |
> | edgenodes | 아닙니다. |
> | 프로필 | yes |
> | profiles / endpoints | yes |
> | profiles / endpoints / customdomains | 아닙니다. |
> | profiles / endpoints / origins | 아닙니다. |
> | validateProbe | 아닙니다. |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | certificateOrders | yes |
> | certificateOrders / certificates | 아닙니다. |
> | validateCertificateRegistrationInformation | 아닙니다. |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | capabilities | 아닙니다. |
> | domainNames | yes |
> | domainNames / capabilities | 아닙니다. |
> | domainNames / internalLoadBalancers | 아닙니다. |
> | domainNames / serviceCertificates | 아닙니다. |
> | domainNames / slots | 아닙니다. |
> | domainNames / slots / roles | 아닙니다. |
> | domainNames / slots / roles / metricDefinitions | 아닙니다. |
> | domainNames / slots / roles / metrics | 아닙니다. |
> | moveSubscriptionResources | 아닙니다. |
> | operatingSystemFamilies | 아닙니다. |
> | operatingSystems | 아닙니다. |
> | quotas | 아닙니다. |
> | resourceTypes | 아닙니다. |
> | validateSubscriptionMoveAvailability | 아닙니다. |
> | virtualMachines | yes |
> | virtualMachines / diagnosticSettings | 아닙니다. |
> | virtualMachines / metricDefinitions | 아닙니다. |
> | virtualMachines / metrics | 아닙니다. |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | classicInfrastructureResources | 아닙니다. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | capabilities | 아닙니다. |
> | expressRouteCrossConnections | 아닙니다. |
> | expressRouteCrossConnections / peerings | 아닙니다. |
> | gatewaySupportedDevices | 아닙니다. |
> | networkSecurityGroups | yes |
> | quotas | 아닙니다. |
> | reservedIps | yes |
> | virtualNetworks | yes |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | 아닙니다. |
> | virtualNetworks / virtualNetworkPeerings | 아닙니다. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | capabilities | 아닙니다. |
> | 디스크 | 아닙니다. |
> | images | 아닙니다. |
> | osImages | 아닙니다. |
> | osPlatformImages | 아닙니다. |
> | publicImages | 아닙니다. |
> | quotas | 아닙니다. |
> | storageAccounts | yes |
> | storageAccounts / blobServices | 아닙니다. |
> | storageAccounts / fileServices | 아닙니다. |
> | storageAccounts / metricDefinitions | 아닙니다. |
> | storageAccounts / metrics | 아닙니다. |
> | storageAccounts / queueServices | 아닙니다. |
> | storageAccounts / services | 아닙니다. |
> | storageAccounts / services / diagnosticSettings | 아닙니다. |
> | storageAccounts / services / metricDefinitions | 아닙니다. |
> | storageAccounts / services / metrics | 아닙니다. |
> | storageAccounts / tableServices | 아닙니다. |
> | storageAccounts / vmImages | 아닙니다. |
> | vmImages | 아닙니다. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | yes |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | RateCard | 아닙니다. |
> | UsageAggregates | 아닙니다. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | availabilitySets | yes |
> | diskEncryptionSets | yes |
> | 디스크 | yes |
> | galleries | yes |
> | galleries / applications | 아닙니다. |
> | galleries / applications / versions | 아닙니다. |
> | galleries / images | 아닙니다. |
> | galleries / images / versions | 아닙니다. |
> | hostGroups | yes |
> | hostGroups / hosts | yes |
> | images | yes |
> | proximityPlacementGroups | yes |
> | restorePointCollections | yes |
> | restorePointCollections / restorePoints | 아닙니다. |
> | sharedVMImages | yes |
> | sharedVMImages / versions | 아닙니다. |
> | 스냅샷 | yes |
> | virtualMachines | yes |
> | virtualMachines / extensions | yes |
> | virtualMachines / metricDefinitions | 아닙니다. |
> | virtualMachineScaleSets | yes |
> | virtualMachineScaleSets / extensions | 아닙니다. |
> | virtualMachineScaleSets / networkInterfaces | 아닙니다. |
> | virtualMachineScaleSets / publicIPAddresses | 아닙니다. |
> | virtualMachineScaleSets / virtualMachines | 아닙니다. |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | 아닙니다. |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | AggregatedCost | 아닙니다. |
> | 잔액 | 아닙니다. |
> | 예산 | 아닙니다. |
> | Charges | 아닙니다. |
> | CostTags | 아닙니다. |
> | credits | 아닙니다. |
> | events | 아닙니다. |
> | 예측 | 아닙니다. |
> | lots | 아닙니다. |
> | Marketplace | 아닙니다. |
> | Pricesheets | 아닙니다. |
> | products | 아닙니다. |
> | ReservationDetails | 아닙니다. |
> | ReservationRecommendations | 아닙니다. |
> | ReservationSummaries | 아닙니다. |
> | ReservationTransactions | 아닙니다. |
> | 태그 | 아닙니다. |
> | tenants | 아닙니다. |
> | 용어 | 아닙니다. |
> | UsageDetails | 아닙니다. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | containerGroups | yes |
> | serviceAssociationLinks | 아닙니다. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | registries | yes |
> | registries / builds | 아닙니다. |
> | registries / builds / cancel | 아닙니다. |
> | registries / builds / getLogLink | 아닙니다. |
> | registries / buildTasks | yes |
> | registries / buildTasks / steps | 아닙니다. |
> | registries / eventGridFilters | 아닙니다. |
> | registries / generateCredentials | 아닙니다. |
> | registries / getBuildSourceUploadUrl | 아닙니다. |
> | registries / GetCredentials | 아닙니다. |
> | registries / importImage | 아닙니다. |
> | registries / queueBuild | 아닙니다. |
> | registries / regenerateCredential | 아닙니다. |
> | registries / regenerateCredentials | 아닙니다. |
> | registries / replications | yes |
> | registries / runs | 아닙니다. |
> | registries / runs / cancel | 아닙니다. |
> | registries / scheduleRun | 아닙니다. |
> | registries / scopeMaps | 아닙니다. |
> | registries / taskRuns | yes |
> | registries / tasks | yes |
> | registries / tokens | 아닙니다. |
> | registries / updatePolicies | 아닙니다. |
> | registries / webhooks | yes |
> | registries / webhooks / getCallbackConfig | 아닙니다. |
> | registries / webhooks / ping | 아닙니다. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | containerServices | yes |
> | managedClusters | yes |
> | openShiftManagedClusters | yes |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | yes |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 경고 | 아닙니다. |
> | BillingAccounts | 아닙니다. |
> | 예산 | 아닙니다. |
> | CloudConnectors | 아닙니다. |
> | 커넥터 | yes |
> | Departments | 아닙니다. |
> | 차원 | 아닙니다. |
> | EnrollmentAccounts | 아닙니다. |
> | 내보내기 | 아닙니다. |
> | ExternalBillingAccounts | 아닙니다. |
> | ExternalBillingAccounts / Alerts | 아닙니다. |
> | ExternalBillingAccounts / Dimensions | 아닙니다. |
> | ExternalBillingAccounts / Forecast | 아닙니다. |
> | ExternalBillingAccounts / Query | 아닙니다. |
> | ExternalSubscriptions | 아닙니다. |
> | ExternalSubscriptions / Alerts | 아닙니다. |
> | ExternalSubscriptions / Dimensions | 아닙니다. |
> | ExternalSubscriptions / Forecast | 아닙니다. |
> | ExternalSubscriptions / Query | 아닙니다. |
> | Forecast | 아닙니다. |
> | 쿼리 | 아닙니다. |
> | register | 아닙니다. |
> | Reportconfigs | 아닙니다. |
> | 보고서 | 아닙니다. |
> | 설정 | 아닙니다. |
> | showbackRules | 아닙니다. |
> | 뷰 | 아닙니다. |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | requests | 아닙니다. |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | associations | 아닙니다. |
> | resourceProviders | yes |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | jobs | yes |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | yes |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | workspaces | yes |
> | workspaces / virtualNetworkPeerings | 아닙니다. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | catalogs | yes |
> | datacatalogs | yes |
> | datacatalogs / datasources | 아닙니다. |
> | datacatalogs / datasources / scans | 아닙니다. |
> | datacatalogs / datasources / scans / datasets | 아닙니다. |
> | datacatalogs / datasources / scans / triggers | 아닙니다. |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | dataFactories | yes |
> | dataFactories / diagnosticSettings | 아닙니다. |
> | dataFactories / metricDefinitions | 아닙니다. |
> | dataFactorySchema | 아닙니다. |
> | factories | yes |
> | factories / integrationRuntimes | 아닙니다. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | yes |
> | accounts / dataLakeStoreAccounts | 아닙니다. |
> | accounts / storageAccounts | 아닙니다. |
> | accounts / storageAccounts / containers | 아닙니다. |
> | accounts / transferAnalyticsUnits | 아닙니다. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | yes |
> | accounts / eventGridFilters | 아닙니다. |
> | accounts / firewallRules | 아닙니다. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | services | yes |
> | services / projects | yes |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | yes |
> | accounts / shares | 아닙니다. |
> | accounts / shares / datasets | 아닙니다. |
> | accounts / shares / invitations | 아닙니다. |
> | accounts / shares / providersharesubscriptions | 아닙니다. |
> | accounts / shares / synchronizationSettings | 아닙니다. |
> | accounts / sharesubscriptions | 아닙니다. |
> | accounts / sharesubscriptions / consumerSourceDataSets | 아닙니다. |
> | accounts / sharesubscriptions / datasetmappings | 아닙니다. |
> | accounts / sharesubscriptions / triggers | 아닙니다. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 서버 | yes |
> | servers / advisors | 아닙니다. |
> | servers / privateEndpointConnectionProxies | 아닙니다. |
> | servers / privateEndpointConnections | 아닙니다. |
> | servers / privateLinkResources | 아닙니다. |
> | servers / queryTexts | 아닙니다. |
> | servers / recoverableServers | 아닙니다. |
> | servers / topQueryStatistics | 아닙니다. |
> | servers / virtualNetworkRules | 아닙니다. |
> | servers / waitStatistics | 아닙니다. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 서버 | yes |
> | servers / advisors | 아닙니다. |
> | servers / privateEndpointConnectionProxies | 아닙니다. |
> | servers / privateEndpointConnections | 아닙니다. |
> | servers / privateLinkResources | 아닙니다. |
> | servers / queryTexts | 아닙니다. |
> | servers / recoverableServers | 아닙니다. |
> | servers / topQueryStatistics | 아닙니다. |
> | servers / virtualNetworkRules | 아닙니다. |
> | servers / waitStatistics | 아닙니다. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | serverGroups | yes |
> | 서버 | yes |
> | servers / advisors | 아닙니다. |
> | servers / keys | 아닙니다. |
> | servers / privateEndpointConnectionProxies | 아닙니다. |
> | servers / privateEndpointConnections | 아닙니다. |
> | servers / privateLinkResources | 아닙니다. |
> | servers / queryTexts | 아닙니다. |
> | servers / recoverableServers | 아닙니다. |
> | servers / topQueryStatistics | 아닙니다. |
> | servers / virtualNetworkRules | 아닙니다. |
> | servers / waitStatistics | 아닙니다. |
> | serversv2 | yes |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | artifactSources | yes |
> | rollouts | yes |
> | serviceTopologies | yes |
> | serviceTopologies / services | yes |
> | serviceTopologies / services / serviceUnits | yes |
> | 단계 | yes |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | applicationgroups | yes |
> | applicationgroups / applications | 아닙니다. |
> | applicationgroups / desktops | 아닙니다. |
> | applicationgroups / startmenuitems | 아닙니다. |
> | hostpools | yes |
> | hostpools / sessionhosts | 아닙니다. |
> | hostpools / sessionhosts / usersessions | 아닙니다. |
> | hostpools / usersessions | 아닙니다. |
> | workspaces | yes |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | ElasticPools | yes |
> | ElasticPools / IotHubTenants | yes |
> | IotHubs | yes |
> | IotHubs / eventGridFilters | 아닙니다. |
> | ProvisioningServices | yes |
> | usages | 아닙니다. |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | pipelines | yes |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | controllers | yes |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | labcenters | yes |
> | labs | yes |
> | labs / environments | yes |
> | labs / serviceRunners | yes |
> | labs / virtualMachines | yes |
> | schedules | yes |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | databaseAccountNames | 아닙니다. |
> | databaseAccounts | yes |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | domains | yes |
> | domains / domainOwnershipIdentifiers | 아닙니다. |
> | generateSsoRequest | 아닙니다. |
> | topLevelDomains | 아닙니다. |
> | validateDomainRegistrationInformation | 아닙니다. |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | lcsprojects | 아닙니다. |
> | lcsprojects / clouddeployments | 아닙니다. |
> | lcsprojects / connectors | 아닙니다. |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | services | yes |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | domains | yes |
> | domains / topics | 아닙니다. |
> | eventSubscriptions | 아닙니다. |
> | extensionTopics | 아닙니다. |
> | topics | yes |
> | topicTypes | 아닙니다. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | clusters | yes |
> | namespaces | yes |
> | namespaces / authorizationrules | 아닙니다. |
> | namespaces / disasterrecoveryconfigs | 아닙니다. |
> | namespaces / eventhubs | 아닙니다. |
> | namespaces / eventhubs / authorizationrules | 아닙니다. |
> | namespaces / eventhubs / consumergroups | 아닙니다. |
> | namespaces / networkrulesets | 아닙니다. |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | SaaS 앱 개발 | 아닙니다. |
> | providers | 아닙니다. |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | enroll | 아닙니다. |
> | galleryitems | 아닙니다. |
> | generateartifactaccessuri | 아닙니다. |
> | myareas | 아닙니다. |
> | myareas / areas | 아닙니다. |
> | myareas / areas / areas | 아닙니다. |
> | myareas / areas / areas / galleryitems | 아닙니다. |
> | myareas / areas / galleryitems | 아닙니다. |
> | myareas / galleryitems | 아닙니다. |
> | register | 아닙니다. |
> | 리소스 | 아닙니다. |
> | retrieveresourcesbyid | 아닙니다. |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | yes |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | configurationProfileAssignments | 아닙니다. |
> | guestConfigurationAssignments | 아닙니다. |
> | software | 아닙니다. |
> | softwareUpdateProfile | 아닙니다. |
> | softwareUpdates | 아닙니다. |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | hanaInstances | yes |
> | sapMonitors | yes |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | dedicatedHSMs | yes |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | clusters | yes |
> | clusters / applications | 아닙니다. |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | services | yes |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | machines | yes |
> | machines / extensions | yes |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | dataManagers | yes |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | components | yes |
> | networkScopes | yes |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | jobs | yes |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | diagnosticSettings | 아닙니다. |
> | diagnosticSettingsCategories | 아닙니다. |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | appTemplates | 아닙니다. |
> | IoTApps | yes |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 그래프 | yes |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | deletedVaults | 아닙니다. |
> | hsmPools | yes |
> | vaults | yes |
> | vaults / accessPolicies | 아닙니다. |
> | vaults / eventGridFilters | 아닙니다. |
> | vaults / secrets | 아닙니다. |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | clusters | yes |
> | clusters / attacheddatabaseconfigurations | 아닙니다. |
> | clusters / databases | 아닙니다. |
> | clusters / databases / dataconnections | 아닙니다. |
> | clusters / databases / eventhubconnections | 아닙니다. |
> | clusters / sharedidentities | 아닙니다. |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | labaccounts | yes |
> | users | 아닙니다. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | hostingEnvironments | yes |
> | integrationAccounts | yes |
> | integrationServiceEnvironments | yes |
> | integrationServiceEnvironments / managedApis | yes |
> | isolatedEnvironments | yes |
> | workflows | yes |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | commitmentPlans | yes |
> | webServices | yes |
> | 작업 영역 | yes |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | workspaces | yes |
> | workspaces / computes | 아닙니다. |
> | workspaces / eventGridFilters | 아닙니다. |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | Identities | 아닙니다. |
> | userAssignedIdentities | yes |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | 아닙니다. |
> | registrationAssignments | 아닙니다. |
> | registrationDefinitions | 아닙니다. |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | getEntities | 아닙니다. |
> | managementGroups | 아닙니다. |
> | 리소스 | 아닙니다. |
> | startTenantBackfill | 아닙니다. |
> | tenantBackfillStatus | 아닙니다. |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | yes |
> | accounts / eventGridFilters | 아닙니다. |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | offers | 아닙니다. |
> | offerTypes | 아닙니다. |
> | offerTypes / publishers | 아닙니다. |
> | offerTypes / publishers / offers | 아닙니다. |
> | offerTypes / publishers / offers / plans | 아닙니다. |
> | offerTypes / publishers / offers / plans / agreements | 아닙니다. |
> | offerTypes / publishers / offers / plans / configs | 아닙니다. |
> | offerTypes / publishers / offers / plans / configs / importImage | 아닙니다. |
> | privategalleryitems | 아닙니다. |
> | products | 아닙니다. |
> | publishers | 아닙니다. |
> | publishers / offers | 아닙니다. |
> | publishers / offers / amendments | 아닙니다. |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | classicDevServices | yes |
> | updateCommunicationPreference | 아닙니다. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | agreements | 아닙니다. |
> | offertypes | 아닙니다. |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | mediaservices | yes |
> | mediaservices / accountFilters | 아닙니다. |
> | mediaservices / assets | 아닙니다. |
> | mediaservices / assets / assetFilters | 아닙니다. |
> | mediaservices / contentKeyPolicies | 아닙니다. |
> | mediaservices / eventGridFilters | 아닙니다. |
> | mediaservices / liveEventOperations | 아닙니다. |
> | mediaservices / liveEvents | yes |
> | mediaservices / liveEvents / liveOutputs | 아닙니다. |
> | mediaservices / liveOutputOperations | 아닙니다. |
> | mediaservices / mediaGraphs | 아닙니다. |
> | mediaservices / streamingEndpointOperations | 아닙니다. |
> | mediaservices / streamingEndpoints | yes |
> | mediaservices / streamingLocators | 아닙니다. |
> | mediaservices / streamingPolicies | 아닙니다. |
> | mediaservices / transforms | 아닙니다. |
> | mediaservices / transforms / jobs | 아닙니다. |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | appClusters | yes |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | assessmentProjects | yes |
> | migrateprojects | yes |
> | projects | yes |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | yes |
> | objectUnderstandingAccounts | yes |
> | remoteRenderingAccounts | yes |
> | spatialAnchorsAccounts | yes |
> | surfaceReconstructionAccounts | yes |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | netAppAccounts | yes |
> | netAppAccounts / capacityPools | yes |
> | netAppAccounts / capacityPools / volumes | yes |
> | netAppAccounts / capacityPools / volumes / mountTargets | yes |
> | netAppAccounts / capacityPools / volumes / snapshots | yes |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | applicationGateways | yes |
> | applicationGatewayWebApplicationFirewallPolicies | yes |
> | applicationSecurityGroups | yes |
> | azureFirewallFqdnTags | 아닙니다. |
> | azureFirewalls | yes |
> | bastionHosts | yes |
> | bgpServiceCommunities | 아닙니다. |
> | connections | yes |
> | ddosCustomPolicies | yes |
> | ddosProtectionPlans | yes |
> | dnsOperationStatuses | 아닙니다. |
> | dnszones | yes |
> | dnszones / A | 아닙니다. |
> | dnszones / AAAA | 아닙니다. |
> | dnszones / all | 아닙니다. |
> | dnszones / CAA | 아닙니다. |
> | dnszones / CNAME | 아닙니다. |
> | dnszones / MX | 아닙니다. |
> | dnszones / NS | 아닙니다. |
> | dnszones / PTR | 아닙니다. |
> | dnszones / recordsets | 아닙니다. |
> | dnszones / SOA | 아닙니다. |
> | dnszones / SRV | 아닙니다. |
> | dnszones / TXT | 아닙니다. |
> | expressRouteCircuits | yes |
> | expressRouteCrossConnections | yes |
> | expressRouteGateways | yes |
> | expressRoutePorts | yes |
> | expressRouteServiceProviders | 아닙니다. |
> | firewallPolicies | yes |
> | frontdoors | yes |
> | frontdoorWebApplicationFirewallManagedRuleSets | 아닙니다. |
> | frontdoorWebApplicationFirewallPolicies | yes |
> | getDnsResourceReference | 아닙니다. |
> | internalNotify | 아닙니다. |
> | loadBalancers | yes |
> | localNetworkGateways | yes |
> | natGateways | yes |
> | networkIntentPolicies | yes |
> | networkInterfaces | yes |
> | networkProfiles | yes |
> | networkSecurityGroups | yes |
> | networkWatchers | yes |
> | networkWatchers / connectionMonitors | yes |
> | networkWatchers / lenses | yes |
> | networkWatchers / pingMeshes | yes |
> | p2sVpnGateways | yes |
> | privateDnsOperationStatuses | 아닙니다. |
> | privateDnsZones | yes |
> | privateDnsZones / A | 아닙니다. |
> | privateDnsZones / AAAA | 아닙니다. |
> | privateDnsZones / all | 아닙니다. |
> | privateDnsZones / CNAME | 아닙니다. |
> | privateDnsZones / MX | 아닙니다. |
> | privateDnsZones / PTR | 아닙니다. |
> | privateDnsZones / SOA | 아닙니다. |
> | privateDnsZones / SRV | 아닙니다. |
> | privateDnsZones / TXT | 아닙니다. |
> | privateDnsZones / virtualNetworkLinks | yes |
> | privateEndpoints | yes |
> | privateLinkServices | yes |
> | publicIPAddresses | yes |
> | publicIPPrefixes | yes |
> | routeFilters | yes |
> | routeTables | yes |
> | serviceEndpointPolicies | yes |
> | trafficManagerGeographicHierarchies | 아닙니다. |
> | trafficmanagerprofiles | yes |
> | trafficmanagerprofiles / heatMaps | 아닙니다. |
> | trafficManagerUserMetricsKeys | 아닙니다. |
> | virtualHubs | yes |
> | virtualNetworkGateways | yes |
> | virtualNetworks | yes |
> | virtualNetworkTaps | yes |
> | virtualWans | yes |
> | vpnGateways | yes |
> | vpnSites | yes |
> | webApplicationFirewallPolicies | yes |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | namespaces | yes |
> | namespaces / notificationHubs | yes |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | osNamespaces | yes |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | HyperVSites | yes |
> | ImportSites | yes |
> | ServerSites | yes |
> | VMwareSites | yes |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | clusters | yes |
> | devices | 아닙니다. |
> | linkTargets | 아닙니다. |
> | storageInsightConfigs | 아닙니다. |
> | workspaces | yes |
> | workspaces / dataSources | 아닙니다. |
> | workspaces / linkedServices | 아닙니다. |
> | workspaces / query | 아닙니다. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | managementassociations | 아닙니다. |
> | managementconfigurations | yes |
> | solutions | yes |
> | 뷰 | yes |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | legacyPeerings | 아닙니다. |
> | peerAsns | 아닙니다. |
> | peerings | yes |
> | peeringServiceProviders | 아닙니다. |
> | peeringServices | yes |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | policyEvents | 아닙니다. |
> | policyMetadata | 아닙니다. |
> | policyStates | 아닙니다. |
> | policyTrackedResources | 아닙니다. |
> | remediations | 아닙니다. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | consoles | 아닙니다. |
> | dashboards | yes |
> | userSettings | 아닙니다. |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | workspaceCollections | yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | capacities | yes |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | backupProtectedItems | 아닙니다. |
> | vaults | yes |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | namespaces | yes |
> | namespaces / authorizationrules | 아닙니다. |
> | namespaces / hybridconnections | 아닙니다. |
> | namespaces / hybridconnections / authorizationrules | 아닙니다. |
> | namespaces / wcfrelays | 아닙니다. |
> | namespaces / wcfrelays / authorizationrules | 아닙니다. |

## <a name="microsoftremoteapp"></a>Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 계정 | 아닙니다. |
> | collections | yes |
> | collections / applications | 아닙니다. |
> | collections / securityprincipals | 아닙니다. |
> | templateImages | 아닙니다. |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 쿼리 | yes |
> | resourceChangeDetails | 아닙니다. |
> | resourceChanges | 아닙니다. |
> | 리소스 | 아닙니다. |
> | resourcesHistory | 아닙니다. |
> | subscriptionsStatus | 아닙니다. |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | availabilityStatuses | 아닙니다. |
> | childAvailabilityStatuses | 아닙니다. |
> | childResources | 아닙니다. |
> | events | 아닙니다. |
> | impactedResources | 아닙니다. |
> | metadata | 아닙니다. |
> | 알림 | 아닙니다. |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 배포 | 아닙니다. |
> | deployments / operations | 아닙니다. |
> | deploymentScripts | yes |
> | deploymentScripts / logs | 아닙니다. |
> | links | 아닙니다. |
> | notifyResourceJobs | 아닙니다. |
> | providers | 아닙니다. |
> | resourceGroups | 아닙니다. |
> | 리소스 | 아닙니다. |
> | 구독 | 아닙니다. |
> | subscriptions / providers | 아닙니다. |
> | subscriptions / resources | 아닙니다. |
> | subscriptions / tagnames | 아닙니다. |
> | subscriptions / tagNames / tagValues | 아닙니다. |
> | tenants | 아닙니다. |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 애플리케이션 | yes |
> | saasresources | 아닙니다. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | jobcollections | yes |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | resourceHealthMetadata | 아닙니다. |
> | searchServices | yes |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | 아닙니다. |
> | advancedThreatProtectionSettings | 아닙니다. |
> | 경고 | 아닙니다. |
> | allowedConnections | 아닙니다. |
> | applicationWhitelistings | 아닙니다. |
> | assessmentMetadata | 아닙니다. |
> | assessments | 아닙니다. |
> | autoDismissAlertsRules | 아닙니다. |
> | automations | yes |
> | AutoProvisioningSettings | 아닙니다. |
> | Compliances | 아닙니다. |
> | dataCollectionAgents | 아닙니다. |
> | deviceSecurityGroups | 아닙니다. |
> | discoveredSecuritySolutions | 아닙니다. |
> | externalSecuritySolutions | 아닙니다. |
> | InformationProtectionPolicies | 아닙니다. |
> | iotSecuritySolutions | yes |
> | iotSecuritySolutions / analyticsModels | 아닙니다. |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | 아닙니다. |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | 아닙니다. |
> | jitNetworkAccessPolicies | 아닙니다. |
> | networkData | 아닙니다. |
> | 정책 | 아닙니다. |
> | pricings | 아닙니다. |
> | regulatoryComplianceStandards | 아닙니다. |
> | regulatoryComplianceStandards / regulatoryComplianceControls | 아닙니다. |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | 아닙니다. |
> | securityContacts | 아닙니다. |
> | securitySolutions | 아닙니다. |
> | securitySolutionsReferenceData | 아닙니다. |
> | securityStatuses | 아닙니다. |
> | securityStatusesSummaries | 아닙니다. |
> | serverVulnerabilityAssessments | 아닙니다. |
> | 설정 | 아닙니다. |
> | subAssessments | 아닙니다. |
> | 태스크 | 아닙니다. |
> | topologies | 아닙니다. |
> | workspaceSettings | 아닙니다. |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | diagnosticSettings | 아닙니다. |
> | diagnosticSettingsCategories | 아닙니다. |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | aggregations | 아닙니다. |
> | alertRules | 아닙니다. |
> | alertRuleTemplates | 아닙니다. |
> | bookmarks | 아닙니다. |
> | cases | 아닙니다. |
> | dataConnectors | 아닙니다. |
> | 엔터티 | 아닙니다. |
> | entityQueries | 아닙니다. |
> | officeConsents | 아닙니다. |
> | 설정 | 아닙니다. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | namespaces | yes |
> | namespaces / authorizationrules | 아닙니다. |
> | namespaces / disasterrecoveryconfigs | 아닙니다. |
> | namespaces / eventgridfilters | 아닙니다. |
> | namespaces / networkrulesets | 아닙니다. |
> | namespaces / queues | 아닙니다. |
> | namespaces / queues / authorizationrules | 아닙니다. |
> | namespaces / topics | 아닙니다. |
> | namespaces / topics / authorizationrules | 아닙니다. |
> | namespaces / topics / subscriptions | 아닙니다. |
> | namespaces / topics / subscriptions / rules | 아닙니다. |
> | premiumMessagingRegions | 아닙니다. |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 애플리케이션 | yes |
> | clusters | yes |
> | clusters / applications | 아닙니다. |
> | containerGroups | yes |
> | containerGroupSets | yes |
> | edgeclusters | yes |
> | edgeclusters / applications | 아닙니다. |
> | networks | yes |
> | secretstores | yes |
> | secretstores / certificates | 아닙니다. |
> | secretstores / secrets | 아닙니다. |
> | volumes | yes |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | 애플리케이션 | yes |
> | containerGroups | yes |
> | gateways | yes |
> | networks | yes |
> | secrets | yes |
> | volumes | yes |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | providerRegistrations | 아닙니다. |
> | providerRegistrations / resourceTypeRegistrations | 아닙니다. |
> | rollouts | yes |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | SignalR | yes |
> | SignalR / eventGridFilters | 아닙니다. |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | SiteRecoveryVault | yes |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | hybridUseBenefits | 아닙니다. |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | applicationDefinitions | yes |
> | 애플리케이션 | yes |
> | jitRequests | yes |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | managedInstances | yes |
> | managedInstances / databases | yes |
> | managedInstances / databases / backupShortTermRetentionPolicies | 아닙니다. |
> | managedInstances / databases / schemas / tables / columns / sensitivityLabels | 아닙니다. |
> | managedInstances / databases / vulnerabilityAssessments | 아닙니다. |
> | managedInstances / databases / vulnerabilityAssessments / rules / baselines | 아닙니다. |
> | managedInstances / encryptionProtector | 아닙니다. |
> | managedInstances / keys | 아닙니다. |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | 아닙니다. |
> | managedInstances / vulnerabilityAssessments | 아닙니다. |
> | 서버 | yes |
> | servers / administrators | 아닙니다. |
> | servers / communicationLinks | 아닙니다. |
> | servers / databases | yes |
> | servers / encryptionProtector | 아닙니다. |
> | servers / firewallRules | 아닙니다. |
> | servers / keys | 아닙니다. |
> | servers / restorableDroppedDatabases | 아닙니다. |
> | servers / serviceobjectives | 아닙니다. |
> | servers / tdeCertificates | 아닙니다. |
> | virtualClusters | 아닙니다. |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | yes |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | 아닙니다. |
> | SqlVirtualMachines | yes |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | storageAccounts | yes |
> | storageAccounts / blobServices | 아닙니다. |
> | storageAccounts / fileServices | 아닙니다. |
> | storageAccounts / queueServices | 아닙니다. |
> | storageAccounts / services | 아닙니다. |
> | storageAccounts / services / metricDefinitions | 아닙니다. |
> | storageAccounts / tableServices | 아닙니다. |
> | usages | 아닙니다. |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | caches | yes |
> | caches / storageTargets | 아닙니다. |
> | usageModels | 아닙니다. |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | replicationGroups | 아닙니다. |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | storageSyncServices | yes |
> | storageSyncServices / registeredServers | 아닙니다. |
> | storageSyncServices / syncGroups | 아닙니다. |
> | storageSyncServices / syncGroups / cloudEndpoints | 아닙니다. |
> | storageSyncServices / syncGroups / serverEndpoints | 아닙니다. |
> | storageSyncServices / workflows | 아닙니다. |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | storageSyncServices | yes |
> | storageSyncServices / registeredServers | 아닙니다. |
> | storageSyncServices / syncGroups | 아닙니다. |
> | storageSyncServices / syncGroups / cloudEndpoints | 아닙니다. |
> | storageSyncServices / syncGroups / serverEndpoints | 아닙니다. |
> | storageSyncServices / workflows | 아닙니다. |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | storageSyncServices | yes |
> | storageSyncServices / registeredServers | 아닙니다. |
> | storageSyncServices / syncGroups | 아닙니다. |
> | storageSyncServices / syncGroups / cloudEndpoints | 아닙니다. |
> | storageSyncServices / syncGroups / serverEndpoints | 아닙니다. |
> | storageSyncServices / workflows | 아닙니다. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | managers | yes |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | streamingjobs | yes |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | cancel | 아닙니다. |
> | CreateSubscription | 아닙니다. |
> | enable | 아닙니다. |
> | rename | 아닙니다. |
> | SubscriptionDefinitions | 아닙니다. |
> | SubscriptionOperations | 아닙니다. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | environments | yes |
> | environments / accessPolicies | 아닙니다. |
> | environments / eventsources | yes |
> | environments / referenceDataSets | yes |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | dedicatedCloudNodes | yes |
> | dedicatedCloudServices | yes |
> | virtualMachines | yes |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | apiManagementAccounts | 아닙니다. |
> | apiManagementAccounts / apiAcls | 아닙니다. |
> | apiManagementAccounts / apis | 아닙니다. |
> | apiManagementAccounts / apis / apiAcls | 아닙니다. |
> | apiManagementAccounts / apis / connectionAcls | 아닙니다. |
> | apiManagementAccounts / apis / connections | 아닙니다. |
> | apiManagementAccounts / apis / connections / connectionAcls | 아닙니다. |
> | apiManagementAccounts / apis / localizedDefinitions | 아닙니다. |
> | apiManagementAccounts / connectionAcls | 아닙니다. |
> | apiManagementAccounts / connections | 아닙니다. |
> | billingMeters | 아닙니다. |
> | certificates | yes |
> | connectionGateways | yes |
> | connections | yes |
> | customApis | yes |
> | deletedSites | 아닙니다. |
> | Functions | 아닙니다. |
> | hostingEnvironments | yes |
> | hostingEnvironments / multiRolePools | 아닙니다. |
> | hostingEnvironments / workerPools | 아닙니다. |
> | publishingUsers | 아닙니다. |
> | 동영상 추천 기능 | 아닙니다. |
> | resourceHealthMetadata | 아닙니다. |
> | runtimes | 아닙니다. |
> | serverFarms | yes |
> | serverFarms / eventGridFilters | 아닙니다. |
> | sites | yes |
> | sites/config  | 아닙니다. |
> | sites / eventGridFilters | 아닙니다. |
> | sites / hostNameBindings | 아닙니다. |
> | sites / networkConfig | 아닙니다. |
> | sites / premieraddons | yes |
> | sites / slots | yes |
> | sites / slots / eventGridFilters | 아닙니다. |
> | sites / slots / hostNameBindings | 아닙니다. |
> | sites / slots / networkConfig | 아닙니다. |
> | sourceControls | 아닙니다. |
> | validate | 아닙니다. |
> | verifyHostingEnvironmentVnet | 아닙니다. |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | diagnosticSettings | 아닙니다. |
> | diagnosticSettingsCategories | 아닙니다. |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | DeviceServices | yes |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 리소스 종류 | 전체 모드 삭제 |
> | ------------- | ----------- |
> | components | 아닙니다. |
> | componentsSummary | 아닙니다. |
> | monitorInstances | 아닙니다. |
> | monitorInstancesSummary | 아닙니다. |
> | monitors | 아닙니다. |
> | notificationSettings | 아닙니다. |

## <a name="next-steps"></a>다음 단계

쉼표로 구분된 값의 파일과 동일한 데이터를 가져오려면 [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv)를 다운로드합니다.
