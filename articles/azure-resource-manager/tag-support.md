---
title: 리소스에 대 한 태그 지원
description: 태그를 지원하는 Azure 리소스 종류를 보여 줍니다. 모든 Azure 서비스에 대한 세부 정보를 제공합니다.
ms.topic: conceptual
ms.date: 11/20/2019
ms.openlocfilehash: b58591fe3bcf5b1f181669b1494b1c698c4968c0
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278770"
---
# <a name="tag-support-for-azure-resources"></a>Azure 리소스에 대한 태그 지원
이 문서에서는 리소스 종류가 [태그](resource-group-using-tags.md)를 지원하는지 여부를 설명합니다. **태그 지원** 이라는 열은 리소스 형식에 태그에 대 한 속성이 있는지 여부를 나타냅니다. **비용 보고서에서 태그** 레이블이 지정 된 열은 리소스 종류가 태그를 비용 보고서에 전달 하는지 여부를 나타냅니다.

쉼표로 구분된 값 파일과 동일한 데이터를 가져오려면 [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv) 파일을 다운로드합니다.

리소스 공급자 네임 스페이스로 이동 합니다.
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Addons](#microsoftaddons)
> - [ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft AppPlatform](#microsoftappplatform)
> - [Microsoft 증명](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Azconfig](#microsoftazconfig)
> - [Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft 청구](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft 용량](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft 상거래](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft 소비량](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft. CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft. 기능](#microsoftfeatures)
> - [Microsoft 갤러리](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft 히드라](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft ManagedServices](#microsoftmanagedservices)
> - [Microsoft 관리](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft ObjectStore](#microsoftobjectstore)
> - [Microsoft. OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft RemoteApp](#microsoftremoteapp)
> - [Microsoft ResourceGraph](#microsoftresourcegraph)
> - [Microsoft ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft 서비스](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [SoftwarePlan](#microsoftsoftwareplan)
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
> - [Microsoft 구독](#microsoftsubscription)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | DomainServices | 예 | 예 |
> | DomainServices/oucontainer | 아니오 | 아니오 |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | supportProviders | 아니오 | 아니오 |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | 아니오 | 아니오 |
> | addsservices | 아니오 | 아니오 |
> | agents | 아니오 | 아니오 |
> | anonymousapiusers | 아니오 | 아니오 |
> | 구성 | 아니오 | 아니오 |
> | logs | 아니오 | 아니오 |
> | reports | 아니오 | 아니오 |
> | servicehealthmetrics | 아니오 | 아니오 |
> | services | 아니오 | 아니오 |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 구성 | 아니오 | 아니오 |
> | generateRecommendations | 아니오 | 아니오 |
> | metadata | 아니오 | 아니오 |
> | 동영상 추천 기능 | 아니오 | 아니오 |
> | suppressions | 아니오 | 아니오 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | actionRules | 예 | 예 |
> | 경고 | 아니오 | 아니오 |
> | alertsList | 아니오 | 아니오 |
> | alertsMetaData | 아니오 | 아니오 |
> | alertsSummary | 아니오 | 아니오 |
> | alertsSummaryList | 아니오 | 아니오 |
> | 있으신가요 | 아니오 | 아니오 |
> | smartDetectorAlertRules | 예 | 예 |
> | smartDetectorRuntimeEnvironments | 아니오 | 아니오 |
> | smartGroups | 아니오 | 아니오 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | servers | 예 | 예 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | reportFeedback | 아니오 | 아니오 |
> | 서비스 | 예 | 예 |
> | validateServiceName | 아니오 | 아니오 |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | configurationStores | 예 | 예 |
> | configurationStores/eventGridFilters | 아니오 | 아니오 |

## <a name="microsoftappplatform"></a>Microsoft AppPlatform

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | Spring | 예 | 예 |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | attestationProviders | 아니오 | 아니오 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | classicAdministrators | 아니오 | 아니오 |
> | dataAliases | 아니오 | 아니오 |
> | denyAssignments | 아니오 | 아니오 |
> | elevateAccess | 아니오 | 아니오 |
> | findOrphanRoleAssignments | 아니오 | 아니오 |
> | locks | 아니오 | 아니오 |
> | 권한 | 아니오 | 아니오 |
> | policyAssignments | 아니오 | 아니오 |
> | policyDefinitions | 아니오 | 아니오 |
> | policySetDefinitions | 아니오 | 아니오 |
> | providerOperations | 아니오 | 아니오 |
> | roleAssignments | 아니오 | 아니오 |
> | roleDefinitions | 아니오 | 아니오 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | automationAccounts | 예 | 예 |
> | automationAccounts/구성 | 예 | 예 |
> | automationAccounts/job | 아니오 | 아니오 |
> | automationAccounts/runbook | 예 | 예 |
> | automationAccounts/softwareUpdateConfigurations | 아니오 | 아니오 |
> | automationAccounts/웹 후크 | 아니오 | 아니오 |

## <a name="microsoftazconfig"></a>Azconfig

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | configurationStores | 예 | 예 |
> | configurationStores/eventGridFilters | 아니오 | 아니오 |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | environments | 아니오 | 아니오 |
> | 환경/계정 | 아니오 | 아니오 |
> | 환경/계정/네임 스페이스 | 아니오 | 아니오 |
> | 환경/계정/네임 스페이스/구성 | 아니오 | 아니오 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | 예 | 아니오 |
> | b2ctenants | 아니오 | 아니오 |

## <a name="microsoftazuredata"></a>Microsoft AzureData

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | hybridDataManagers | 예 | 예 |
> | postgresInstances | 예 | 예 |
> | sqlBigDataClusters | 예 | 예 |
> | sqlInstances | 예 | 예 |
> | sqlServerRegistrations | 예 | 예 |
> | sqlServerRegistrations/sqlServers | 아니오 | 아니오 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | registrations | 예 | 예 |
> | 등록/customerSubscriptions | 아니오 | 아니오 |
> | 등록/제품 | 아니오 | 아니오 |
> | verificationKeys | 아니오 | 아니오 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | batchAccounts | 예 | 예 |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | billingAccounts | 아니오 | 아니오 |
> | billingAccounts/규약 | 아니오 | 아니오 |
> | billingAccounts / billingPermissions | 아니오 | 아니오 |
> | billingAccounts / billingProfiles | 아니오 | 아니오 |
> | billingAccounts / billingProfiles / billingPermissions | 아니오 | 아니오 |
> | billingAccounts / billingProfiles / billingRoleAssignments | 아니오 | 아니오 |
> | billingAccounts / billingProfiles / billingRoleDefinitions | 아니오 | 아니오 |
> | billingAccounts / billingProfiles / billingSubscriptions | 아니오 | 아니오 |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | 아니오 | 아니오 |
> | billingAccounts/billingProfiles/고객 | 아니오 | 아니오 |
> | billingAccounts/billingProfiles/송장 | 아니오 | 아니오 |
> | billingAccounts/billingProfiles/청구서/가격표 | 아니오 | 아니오 |
> | billingAccounts / billingProfiles / invoiceSections | 아니오 | 아니오 |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | 아니오 | 아니오 |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | 아니오 | 아니오 |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | 아니오 | 아니오 |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | 아니오 | 아니오 |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | 아니오 | 아니오 |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | 아니오 | 아니오 |
> | billingAccounts/billingProfiles/invoiceSections/products | 아니오 | 아니오 |
> | billingAccounts/billingProfiles/invoiceSections/products/transfer | 아니오 | 아니오 |
> | billingAccounts/billingProfiles/invoiceSections/products/updateAutoRenew | 아니오 | 아니오 |
> | billingAccounts/billingProfiles/invoiceSections/트랜잭션 | 아니오 | 아니오 |
> | billingAccounts/billingProfiles/invoiceSections/transfer | 아니오 | 아니오 |
> | billingAccounts / BillingProfiles / patchOperations | 아니오 | 아니오 |
> | billingAccounts / billingProfiles / paymentMethods | 아니오 | 아니오 |
> | billingAccounts/billingProfiles/정책 | 아니오 | 아니오 |
> | billingAccounts/billingProfiles/가격표 | 아니오 | 아니오 |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | 아니오 | 아니오 |
> | billingAccounts/billingProfiles/제품 | 아니오 | 아니오 |
> | billingAccounts/billingProfiles/트랜잭션 | 아니오 | 아니오 |
> | billingAccounts / billingRoleAssignments | 아니오 | 아니오 |
> | billingAccounts / billingRoleDefinitions | 아니오 | 아니오 |
> | billingAccounts / billingSubscriptions | 아니오 | 아니오 |
> | billingAccounts/billingSubscriptions/송장 | 아니오 | 아니오 |
> | billingAccounts / createBillingRoleAssignment | 아니오 | 아니오 |
> | billingAccounts / createInvoiceSectionOperations | 아니오 | 아니오 |
> | billingAccounts/고객 | 아니오 | 아니오 |
> | billingAccounts/customers/billingPermissions | 아니오 | 아니오 |
> | billingAccounts/customers/billingSubscriptions | 아니오 | 아니오 |
> | billingAccounts/customers/initiateTransfer | 아니오 | 아니오 |
> | billingAccounts/고객/정책 | 아니오 | 아니오 |
> | billingAccounts/고객/제품 | 아니오 | 아니오 |
> | billingAccounts/고객/트랜잭션 | 아니오 | 아니오 |
> | billingAccounts/고객/전송 | 아니오 | 아니오 |
> | billingAccounts/부서 | 아니오 | 아니오 |
> | billingAccounts / enrollmentAccounts | 아니오 | 아니오 |
> | billingAccounts/송장 | 아니오 | 아니오 |
> | billingAccounts / invoiceSections | 아니오 | 아니오 |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | 아니오 | 아니오 |
> | billingAccounts / invoiceSections / billingSubscriptions | 아니오 | 아니오 |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | 아니오 | 아니오 |
> | billingAccounts/invoiceSections/상승 | 아니오 | 아니오 |
> | billingAccounts / invoiceSections / initiateTransfer | 아니오 | 아니오 |
> | billingAccounts / invoiceSections / patchOperations | 아니오 | 아니오 |
> | billingAccounts/invoiceSections/제품 Moveoperations | 아니오 | 아니오 |
> | billingAccounts/invoiceSections/제품 | 아니오 | 아니오 |
> | billingAccounts/invoiceSections/제품/이전 | 아니오 | 아니오 |
> | billingAccounts/invoiceSections/products/updateAutoRenew | 아니오 | 아니오 |
> | billingAccounts/invoiceSections/트랜잭션 | 아니오 | 아니오 |
> | billingAccounts/invoiceSections/전송 | 아니오 | 아니오 |
> | billingAccounts/lineOfCredit | 아니오 | 아니오 |
> | billingAccounts / patchOperations | 아니오 | 아니오 |
> | billingAccounts / paymentMethods | 아니오 | 아니오 |
> | billingAccounts/제품 | 아니오 | 아니오 |
> | billingAccounts/트랜잭션 | 아니오 | 아니오 |
> | billingPeriods | 아니오 | 아니오 |
> | billingPermissions | 아니오 | 아니오 |
> | billingProperty | 아니오 | 아니오 |
> | billingRoleAssignments | 아니오 | 아니오 |
> | billingRoleDefinitions | 아니오 | 아니오 |
> | createBillingRoleAssignment | 아니오 | 아니오 |
> | departments | 아니오 | 아니오 |
> | enrollmentAccounts | 아니오 | 아니오 |
> | invoices | 아니오 | 아니오 |
> | transfers | 아니오 | 아니오 |
> | 전송/acceptTransfer | 아니오 | 아니오 |
> | 전송/declineTransfer | 아니오 | 아니오 |
> | 전송/operationStatus | 아니오 | 아니오 |
> | 전송/validateTransfer | 아니오 | 아니오 |
> | validateAddress | 아니오 | 아니오 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | mapApis | 예 | 예 |
> | updateCommunicationPreference | 아니오 | 아니오 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | 예 | 예 |
> | cordaMembers | 예 | 예 |
> | 감시자 | 예 | 예 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | 아니오 | 아니오 |
> | blueprintAssignments / assignmentOperations | 아니오 | 아니오 |
> | blueprintAssignments/작업 | 아니오 | 아니오 |
> | blueprints | 아니오 | 아니오 |
> | 청사진/아티팩트 | 아니오 | 아니오 |
> | 청사진/버전 | 아니오 | 아니오 |
> | 청사진/버전/아티팩트 | 아니오 | 아니오 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | botServices | 예 | 예 |
> | botServices/채널 | 아니오 | 아니오 |
> | botServices/연결 | 아니오 | 아니오 |
> | 언어 | 아니오 | 아니오 |
> | 템플릿 | 아니오 | 아니오 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | Redis | 예 | 예 |
> | RedisConfigDefinition | 아니오 | 아니오 |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | appliedReservations | 아니오 | 아니오 |
> | calculateExchange | 아니오 | 아니오 |
> | calculatePrice | 아니오 | 아니오 |
> | calculatePurchasePrice | 아니오 | 아니오 |
> | catalogs | 아니오 | 아니오 |
> | commercialReservationOrders | 아니오 | 아니오 |
> | 교환 | 아니오 | 아니오 |
> | placePurchaseOrder | 아니오 | 아니오 |
> | reservationOrders | 아니오 | 아니오 |
> | reservationOrders / calculateRefund | 아니오 | 아니오 |
> | reservationOrders/병합 | 아니오 | 아니오 |
> | reservationOrders/예약 | 아니오 | 아니오 |
> | reservationOrders/예약/수정 버전 | 아니오 | 아니오 |
> | reservationOrders/반환 | 아니오 | 아니오 |
> | reservationOrders/분할 | 아니오 | 아니오 |
> | reservationOrders/교환 | 아니오 | 아니오 |
> | reservations | 아니오 | 아니오 |
> | 리소스 | 아니오 | 아니오 |
> | validateReservationOrder | 아니오 | 아니오 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | 아니오 | 아니오 |
> | CdnWebApplicationFirewallPolicies | 예 | 예 |
> | edgenodes | 아니오 | 아니오 |
> | profiles | 예 | 예 |
> | 프로필/끝점 | 예 | 예 |
> | 프로필/끝점/customdomains | 아니오 | 아니오 |
> | 프로필/끝점/원본 | 아니오 | 아니오 |
> | validateProbe | 아니오 | 아니오 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | certificateOrders | 예 | 예 |
> | certificateOrders/certificate | 아니오 | 아니오 |
> | validateCertificateRegistrationInformation | 아니오 | 아니오 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | capabilities | 아니오 | 아니오 |
> | domainNames | 아니오 | 아니오 |
> | domainNames/기능 | 아니오 | 아니오 |
> | domainNames/internalLoadBalancers 장치 | 아니오 | 아니오 |
> | domainNames/serviceCertificates | 아니오 | 아니오 |
> | domainNames/슬롯 | 아니오 | 아니오 |
> | domainNames/슬롯/역할 | 아니오 | 아니오 |
> | domainNames/슬롯/역할/metricDefinitions | 아니오 | 아니오 |
> | domainNames/슬롯/역할/메트릭 | 아니오 | 아니오 |
> | moveSubscriptionResources | 아니오 | 아니오 |
> | operatingSystemFamilies | 아니오 | 아니오 |
> | operatingSystems | 아니오 | 아니오 |
> | quotas | 아니오 | 아니오 |
> | resourceTypes | 아니오 | 아니오 |
> | validateSubscriptionMoveAvailability | 아니오 | 아니오 |
> | virtualMachines | 아니오 | 아니오 |
> | virtualMachines/diagnosticSettings | 아니오 | 아니오 |
> | virtualMachines/metricDefinitions | 아니오 | 아니오 |
> | virtualMachines/메트릭 | 아니오 | 아니오 |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | 아니오 | 아니오 |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | capabilities | 아니오 | 아니오 |
> | expressRouteCrossConnections | 아니오 | 아니오 |
> | expressRouteCrossConnections/피어 링 | 아니오 | 아니오 |
> | gatewaySupportedDevices | 아니오 | 아니오 |
> | networkSecurityGroups | 아니오 | 아니오 |
> | quotas | 아니오 | 아니오 |
> | reservedIps | 아니오 | 아니오 |
> | virtualNetworks | 아니오 | 아니오 |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | 아니오 | 아니오 |
> | virtualNetworks/virtualNetworkPeerings | 아니오 | 아니오 |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | capabilities | 아니오 | 아니오 |
> | 디스크 | 아니오 | 아니오 |
> | images | 아니오 | 아니오 |
> | osImages | 아니오 | 아니오 |
> | osPlatformImages | 아니오 | 아니오 |
> | publicImages | 아니오 | 아니오 |
> | quotas | 아니오 | 아니오 |
> | storageAccounts | 아니오 | 아니오 |
> | storageAccounts/blobServices | 아니오 | 아니오 |
> | storageAccounts/fileServices | 아니오 | 아니오 |
> | storageAccounts/metricDefinitions | 아니오 | 아니오 |
> | storageAccounts/메트릭 | 아니오 | 아니오 |
> | storageAccounts/queueServices | 아니오 | 아니오 |
> | storageAccounts/서비스 | 아니오 | 아니오 |
> | storageAccounts/services/diagnosticSettings | 아니오 | 아니오 |
> | storageAccounts/services/metricDefinitions | 아니오 | 아니오 |
> | storageAccounts/서비스/메트릭 | 아니오 | 아니오 |
> | storageAccounts/tableServices | 아니오 | 아니오 |
> | storageAccounts/vmImages | 아니오 | 아니오 |
> | vmImages | 아니오 | 아니오 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | RateCard | 아니오 | 아니오 |
> | UsageAggregates | 아니오 | 아니오 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | availabilitySets | 예 | 예 |
> | Disk; 집합 | 예 | 예 |
> | 디스크 | 예 | 예 |
> | galleries | 예 | 예 |
> | 갤러리/응용 프로그램 | 아니오 | 아니오 |
> | 갤러리/응용 프로그램/버전 | 아니오 | 아니오 |
> | 갤러리/이미지 | 아니오 | 아니오 |
> | 갤러리/이미지/버전 | 아니오 | 아니오 |
> | 호스트 그룹 | 예 | 예 |
> | 호스트 그룹/호스트 | 예 | 예 |
> | images | 예 | 예 |
> | proximityPlacementGroups | 예 | 예 |
> | restorePointCollections | 예 | 예 |
> | restorePointCollections / restorePoints | 아니오 | 아니오 |
> | sharedVMImages | 예 | 예 |
> | sharedVMImages/버전 | 아니오 | 아니오 |
> | 스냅샷 | 예 | 예 |
> | virtualMachines | 예 | 예 |
> | virtualMachines/확장 | 예 | 예 |
> | virtualMachines/metricDefinitions | 아니오 | 아니오 |
> | virtualMachineScaleSets | 예 | 예 |
> | virtualMachineScaleSets/확장 | 아니오 | 아니오 |
> | virtualMachineScaleSets/networkInterfaces | 아니오 | 아니오 |
> | virtualMachineScaleSets/publicIPAddresses | 아니오 | 아니오 |
> | virtualMachineScaleSets/virtualMachines | 아니오 | 아니오 |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | 아니오 | 아니오 |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | 아니오 | 아니오 |
> | 잔액 | 아니오 | 아니오 |
> | 예산 | 아니오 | 아니오 |
> | Charges | 아니오 | 아니오 |
> | CostTags | 아니오 | 아니오 |
> | credits | 아니오 | 아니오 |
> | events | 아니오 | 아니오 |
> | 예측 | 아니오 | 아니오 |
> | lots | 아니오 | 아니오 |
> | Marketplace | 아니오 | 아니오 |
> | Pricesheets | 아니오 | 아니오 |
> | products | 아니오 | 아니오 |
> | ReservationDetails | 아니오 | 아니오 |
> | ReservationRecommendations | 아니오 | 아니오 |
> | ReservationSummaries | 아니오 | 아니오 |
> | ReservationTransactions | 아니오 | 아니오 |
> | 태그 | 아니오 | 아니오 |
> | tenants | 아니오 | 아니오 |
> | 용어 | 아니오 | 아니오 |
> | UsageDetails | 아니오 | 아니오 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | containerGroups | 예 | 예 |
> | serviceAssociationLinks | 아니오 | 아니오 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | registries | 예 | 예 |
> | 레지스트리/빌드 | 아니오 | 아니오 |
> | 레지스트리/빌드/취소 | 아니오 | 아니오 |
> | 레지스트리/빌드/getLogLink | 아니오 | 아니오 |
> | 레지스트리/buildTasks | 예 | 예 |
> | 레지스트리/buildTasks/단계 | 아니오 | 아니오 |
> | 레지스트리/eventGridFilters | 아니오 | 아니오 |
> | 레지스트리/generateCredentials | 아니오 | 아니오 |
> | 레지스트리/getBuildSourceUploadUrl | 아니오 | 아니오 |
> | 레지스트리/GetCredentials | 아니오 | 아니오 |
> | 레지스트리/importImage | 아니오 | 아니오 |
> | 레지스트리/queueBuild | 아니오 | 아니오 |
> | 레지스트리/regenerateCredential | 아니오 | 아니오 |
> | 레지스트리/regenerateCredentials | 아니오 | 아니오 |
> | 레지스트리/복제 | 예 | 예 |
> | 레지스트리/실행 | 아니오 | 아니오 |
> | 레지스트리/실행/취소 | 아니오 | 아니오 |
> | 레지스트리/scheduleRun | 아니오 | 아니오 |
> | 레지스트리/scopeMaps | 아니오 | 아니오 |
> | 레지스트리/taskRuns | 예 | 예 |
> | 레지스트리/작업 | 예 | 예 |
> | 레지스트리/토큰 | 아니오 | 아니오 |
> | 레지스트리/updatePolicies | 아니오 | 아니오 |
> | 레지스트리/웹 후크 | 예 | 예 |
> | registry/웹 후크/getcallbackconfig | 아니오 | 아니오 |
> | registry/웹 후크/ping | 아니오 | 아니오 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | containerServices | 예 | 예 |
> | managedClusters | 예 | 예 |
> | openShiftManagedClusters | 예 | 예 |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 경고 | 아니오 | 아니오 |
> | BillingAccounts | 아니오 | 아니오 |
> | 예산 | 아니오 | 아니오 |
> | CloudConnectors | 아니오 | 아니오 |
> | 커넥터 | 예 | 예 |
> | Departments | 아니오 | 아니오 |
> | 차원 | 아니오 | 아니오 |
> | EnrollmentAccounts | 아니오 | 아니오 |
> | 내보내기 | 아니오 | 아니오 |
> | ExternalBillingAccounts | 아니오 | 아니오 |
> | ExternalBillingAccounts/경고 | 아니오 | 아니오 |
> | ExternalBillingAccounts/차원 | 아니오 | 아니오 |
> | ExternalBillingAccounts/예측 | 아니오 | 아니오 |
> | ExternalBillingAccounts/쿼리 | 아니오 | 아니오 |
> | ExternalSubscriptions | 아니오 | 아니오 |
> | ExternalSubscriptions/Alerts | 아니오 | 아니오 |
> | ExternalSubscriptions/차원 | 아니오 | 아니오 |
> | ExternalSubscriptions/예측 | 아니오 | 아니오 |
> | ExternalSubscriptions/Query | 아니오 | 아니오 |
> | 예측할 | 아니오 | 아니오 |
> | query | 아니오 | 아니오 |
> | register | 아니오 | 아니오 |
> | Reportconfigs | 아니오 | 아니오 |
> | 보고서 | 아니오 | 아니오 |
> | 설정 | 아니오 | 아니오 |
> | showbackRules | 아니오 | 아니오 |
> | 뷰 | 아니오 | 아니오 |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | requests | 아니오 | 아니오 |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 사이의 | 아니오 | 아니오 |
> | resourceProviders | 예 | 예 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | jobs | 예 | 예 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | 예 | 예 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | workspaces | 예 | 아니오 |
> | 작업 영역/virtualNetworkPeerings | 아니오 | 아니오 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | catalogs | 예 | 예 |
> | datacatalogs | 예 | 예 |
> | datacatalogs/데이터 원본 | 아니오 | 아니오 |
> | datacatalogs/데이터 원본/검색 | 아니오 | 아니오 |
> | datacatalogs/데이터 원본/검색/데이터 집합 | 아니오 | 아니오 |
> | datacatalogs/데이터 원본/검색/트리거 | 아니오 | 아니오 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | dataFactories | 예 | 아니오 |
> | dataFactories/diagnosticSettings | 아니오 | 아니오 |
> | dataFactories/metricDefinitions | 아니오 | 아니오 |
> | dataFactorySchema | 아니오 | 아니오 |
> | factories | 예 | 아니오 |
> | 팩터리/integrationRuntimes | 아니오 | 아니오 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |
> | 계정/dataLakeStoreAccounts | 아니오 | 아니오 |
> | 계정/storageAccounts | 아니오 | 아니오 |
> | 계정/storageAccounts/컨테이너 | 아니오 | 아니오 |
> | 계정/transferAnalyticsUnits | 아니오 | 아니오 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |
> | 계정/eventGridFilters | 아니오 | 아니오 |
> | 계정/firewallRules | 아니오 | 아니오 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | services | 아니오 | 아니오 |
> | 서비스/프로젝트 | 아니오 | 아니오 |

## <a name="microsoftdatashare"></a>DataShare

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |
> | 계정/공유 | 아니오 | 아니오 |
> | 계정/공유/데이터 집합 | 아니오 | 아니오 |
> | 계정/공유/초대 | 아니오 | 아니오 |
> | 계정/공유/providersharesubscriptions | 아니오 | 아니오 |
> | 계정/공유/synchronizationSettings | 아니오 | 아니오 |
> | 계정/sharesubscriptions | 아니오 | 아니오 |
> | 계정/sharesubscriptions/consumerSourceDataSets | 아니오 | 아니오 |
> | 계정/sharesubscriptions/datasetmappings | 아니오 | 아니오 |
> | 계정/sharesubscriptions/트리거 | 아니오 | 아니오 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | servers | 예 | 예 |
> | 서버/관리자 | 아니오 | 아니오 |
> | servers/privateEndpointConnectionProxies | 아니오 | 아니오 |
> | servers/privateEndpointConnections | 아니오 | 아니오 |
> | servers/privateLinkResources | 아니오 | 아니오 |
> | 서버/queryTexts | 아니오 | 아니오 |
> | servers/recoverableServers | 아니오 | 아니오 |
> | servers/topQueryStatistics | 아니오 | 아니오 |
> | servers/virtualNetworkRules | 아니오 | 아니오 |
> | 서버/waitStatistics | 아니오 | 아니오 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | servers | 예 | 예 |
> | 서버/관리자 | 아니오 | 아니오 |
> | servers/privateEndpointConnectionProxies | 아니오 | 아니오 |
> | servers/privateEndpointConnections | 아니오 | 아니오 |
> | servers/privateLinkResources | 아니오 | 아니오 |
> | 서버/queryTexts | 아니오 | 아니오 |
> | servers/recoverableServers | 아니오 | 아니오 |
> | servers/topQueryStatistics | 아니오 | 아니오 |
> | servers/virtualNetworkRules | 아니오 | 아니오 |
> | 서버/waitStatistics | 아니오 | 아니오 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | serverGroups | 예 | 예 |
> | servers | 예 | 예 |
> | 서버/관리자 | 아니오 | 아니오 |
> | 서버/키 | 아니오 | 아니오 |
> | servers/privateEndpointConnectionProxies | 아니오 | 아니오 |
> | servers/privateEndpointConnections | 아니오 | 아니오 |
> | servers/privateLinkResources | 아니오 | 아니오 |
> | 서버/queryTexts | 아니오 | 아니오 |
> | servers/recoverableServers | 아니오 | 아니오 |
> | servers/topQueryStatistics | 아니오 | 아니오 |
> | servers/virtualNetworkRules | 아니오 | 아니오 |
> | 서버/waitStatistics | 아니오 | 아니오 |
> | serversv2 | 예 | 예 |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | artifactSources | 예 | 예 |
> | rollouts | 예 | 예 |
> | serviceTopologies | 예 | 예 |
> | serviceTopologies/서비스 | 예 | 예 |
> | serviceTopologies/서비스/serviceUnits | 예 | 예 |
> | 단계 | 예 | 예 |

## <a name="microsoftdesktopvirtualization"></a>Microsoft DesktopVirtualization

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | applicationgroups | 예 | 예 |
> | applicationgroups/응용 프로그램 | 아니오 | 아니오 |
> | applicationgroups/데스크톱 | 아니오 | 아니오 |
> | applicationgroups/startmenuitems | 아니오 | 아니오 |
> | hostpools | 예 | 예 |
> | hostpools/sessionhosts | 아니오 | 아니오 |
> | hostpools/sessionhosts/usersessions | 아니오 | 아니오 |
> | hostpools/usersessions | 아니오 | 아니오 |
> | workspaces | 예 | 예 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | ElasticPools | 예 | 예 |
> | ElasticPools / IotHubTenants | 예 | 예 |
> | IotHubs | 예 | 예 |
> | IotHubs/eventGridFilters | 아니오 | 아니오 |
> | ProvisioningServices | 예 | 예 |
> | usages | 아니오 | 아니오 |

## <a name="microsoftdevops"></a>Microsoft DevOps

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 파이프라인 | 예 | 예 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | controllers | 예 | 예 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | labcenters | 예 | 예 |
> | labs | 예 | 예 |
> | 랩/환경 | 예 | 예 |
> | labs/serviceRunners | 예 | 예 |
> | labs/virtualMachines | 예 | 예 |
> | schedules | 예 | 예 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | 아니오 | 아니오 |
> | databaseAccounts | 예 | 예 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | domains | 예 | 예 |
> | 도메인/도메인 소유자의 식별자 | 아니오 | 아니오 |
> | generateSsoRequest | 아니오 | 아니오 |
> | topLevelDomains | 아니오 | 아니오 |
> | validateDomainRegistrationInformation | 아니오 | 아니오 |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | lcsprojects | 아니오 | 아니오 |
> | lcsprojects/clouddeployments | 아니오 | 아니오 |
> | lcsprojects/커넥터 | 아니오 | 아니오 |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | services | 예 | 예 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | domains | 예 | 예 |
> | 도메인/항목 | 아니오 | 아니오 |
> | eventSubscriptions | 아니오 | 아니오 |
> | extensionTopics | 아니오 | 아니오 |
> | topics | 예 | 예 |
> | topicTypes | 아니오 | 아니오 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | clusters | 예 | 예 |
> | namespaces | 예 | 예 |
> | 네임 스페이스/authorizationrules | 아니오 | 아니오 |
> | 네임 스페이스/disasterrecoveryconfigs | 아니오 | 아니오 |
> | 네임 스페이스/eventhubs | 아니오 | 아니오 |
> | 네임 스페이스/eventhubs/authorizationrules | 아니오 | 아니오 |
> | 네임 스페이스/eventhubs/consumergroups | 아니오 | 아니오 |
> | 네임 스페이스/네트워크 규칙 집합 | 아니오 | 아니오 |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 기능 | 아니오 | 아니오 |
> | providers | 아니오 | 아니오 |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | enroll | 아니오 | 아니오 |
> | galleryitems | 아니오 | 아니오 |
> | generateartifactaccessuri | 아니오 | 아니오 |
> | myareas | 아니오 | 아니오 |
> | myareas/areas | 아니오 | 아니오 |
> | myareas/areas/areas | 아니오 | 아니오 |
> | myareas/areas/areas/galleryitems | 아니오 | 아니오 |
> | myareas/areas/galleryitems | 아니오 | 아니오 |
> | myareas/galleryitems | 아니오 | 아니오 |
> | register | 아니오 | 아니오 |
> | 리소스 | 아니오 | 아니오 |
> | retrieveresourcesbyid | 아니오 | 아니오 |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | configurationprofil | 아니오 | 아니오 |
> | guestConfigurationAssignments | 아니오 | 아니오 |
> | software | 아니오 | 아니오 |
> | softwareUpdateProfile | 아니오 | 아니오 |
> | 업데이트 | 아니오 | 아니오 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | hanaInstances | 예 | 예 |
> | sapMonitors | 예 | 예 |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | dedicatedHSMs | 예 | 예 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | clusters | 예 | 예 |
> | 클러스터/응용 프로그램 | 아니오 | 아니오 |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | services | 예 | 예 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 머신의 | 예 | 예 |
> | 컴퓨터/확장 | 예 | 예 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | dataManagers | 예 | 예 |

## <a name="microsofthydra"></a>Microsoft 히드라

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | components | 예 | 예 |
> | networkScopes | 예 | 예 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | jobs | 예 | 예 |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 아니오 | 아니오 |
> | diagnosticSettingsCategories | 아니오 | 아니오 |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | appTemplates | 아니오 | 아니오 |
> | IoTApps | 예 | 예 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 그래프 | 예 | 예 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | deletedVaults | 아니오 | 아니오 |
> | hsmPools | 예 | 예 |
> | vaults | 예 | 예 |
> | 자격 증명 모음/Accesspolicy | 아니오 | 아니오 |
> | 자격 증명 모음/eventGridFilters | 아니오 | 아니오 |
> | 자격 증명 모음/비밀 | 아니오 | 아니오 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | clusters | 예 | 예 |
> | 클러스터/attacheddatabaseconfigurations | 아니오 | 아니오 |
> | 클러스터/데이터베이스 | 아니오 | 아니오 |
> | 클러스터/데이터베이스/dataconnections | 아니오 | 아니오 |
> | 클러스터/데이터베이스/eventhubconnections | 아니오 | 아니오 |
> | 클러스터/sharedidentities | 아니오 | 아니오 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | labaccounts | 예 | 예 |
> | users | 아니오 | 아니오 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | 예 | 예 |
> | integrationAccounts | 예 | 예 |
> | integrationServiceEnvironments | 예 | 예 |
> | integrationServiceEnvironments/managedApis | 예 | 예 |
> | isolatedEnvironments | 예 | 예 |
> | workflows | 예 | 예 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | 예 | 예 |
> | webServices | 예 | 예 |
> | 작업 영역 | 예 | 예 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | workspaces | 예 | 예 |
> | 작업 영역/계산 | 아니오 | 아니오 |
> | workspace/eventGridFilters | 아니오 | 아니오 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | Identities | 아니오 | 아니오 |
> | userAssignedIdentities | 예 | 예 |

## <a name="microsoftmanagedservices"></a>Microsoft ManagedServices

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | 아니오 | 아니오 |
> | registrationAssignments | 아니오 | 아니오 |
> | registrationDefinitions | 아니오 | 아니오 |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | getEntities | 아니오 | 아니오 |
> | managementGroups | 아니오 | 아니오 |
> | 리소스 | 아니오 | 아니오 |
> | startTenantBackfill | 아니오 | 아니오 |
> | tenantBackfillStatus | 아니오 | 아니오 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |
> | 계정/eventGridFilters | 아니오 | 아니오 |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | offers | 아니오 | 아니오 |
> | offerTypes | 아니오 | 아니오 |
> | offerTypes/게시자 | 아니오 | 아니오 |
> | offerTypes/게시자/제안 | 아니오 | 아니오 |
> | offerTypes/게시자/제품/계획 | 아니오 | 아니오 |
> | offerTypes/게시자/제품/계획/규약 | 아니오 | 아니오 |
> | offerTypes/게시자/제품/계획/configs | 아니오 | 아니오 |
> | offerTypes/publishers/제품/계획/configs/importImage | 아니오 | 아니오 |
> | privategalleryitems | 아니오 | 아니오 |
> | products | 아니오 | 아니오 |
> | 게시자 | 아니오 | 아니오 |
> | 게시자/제안 | 아니오 | 아니오 |
> | 게시자/제품/개정 | 아니오 | 아니오 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | classicDevServices | 예 | 예 |
> | updateCommunicationPreference | 아니오 | 아니오 |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | agreements | 아니오 | 아니오 |
> | offertypes | 아니오 | 아니오 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | mediaservices | 예 | 예 |
> | windowsazure.mediaservices/accountFilters | 아니오 | 아니오 |
> | windowsazure.mediaservices/자산 | 아니오 | 아니오 |
> | windowsazure.mediaservices/asset/assetFilters | 아니오 | 아니오 |
> | windowsazure.mediaservices/Contentkeypolicy | 아니오 | 아니오 |
> | windowsazure.mediaservices/eventGridFilters | 아니오 | 아니오 |
> | windowsazure.mediaservices/liveEventOperations | 아니오 | 아니오 |
> | windowsazure.mediaservices/liveEvents | 예 | 예 |
> | windowsazure.mediaservices/liveEvents/Liveoutput | 아니오 | 아니오 |
> | windowsazure.mediaservices/liveOutputOperations | 아니오 | 아니오 |
> | windowsazure.mediaservices/mediaGraphs | 아니오 | 아니오 |
> | windowsazure.mediaservices/streamingEndpointOperations | 아니오 | 아니오 |
> | windowsazure.mediaservices/streamingEndpoints | 예 | 예 |
> | windowsazure.mediaservices/streamingLocators | 아니오 | 아니오 |
> | windowsazure.mediaservices/streamingPolicies | 아니오 | 아니오 |
> | windowsazure.mediaservices/변환 | 아니오 | 아니오 |
> | windowsazure.mediaservices/변환/작업 | 아니오 | 아니오 |

## <a name="microsoftmicroservices4spring"></a>Microservices4Spring

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | appClusters | 예 | 예 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | 예 | 예 |
> | migrateprojects | 예 | 예 |
> | projects | 예 | 예 |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | 예 | 예 |
> | objectUnderstandingAccounts | 예 | 예 |
> | remoteRenderingAccounts | 예 | 예 |
> | spatialAnchorsAccounts | 예 | 예 |
> | surfaceReconstructionAccounts | 예 | 예 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | 예 | 아니오 |
> | netAppAccounts/capacityPools | 예 | 아니오 |
> | netAppAccounts/capacityPools/볼륨 | 예 | 아니오 |
> | netAppAccounts/capacityPools/볼륨/mountTargets | 예 | 아니오 |
> | netAppAccounts/capacityPools/볼륨/스냅숏 | 예 | 아니오 |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | applicationGateways | 예 | 예 |
> | applicationGatewayWebApplicationFirewallPolicies | 예 | 예 |
> | applicationSecurityGroups | 예 | 예 |
> | azureFirewallFqdnTags | 아니오 | 아니오 |
> | azureFirewalls | 예 | 아니오 |
> | bastionHosts | 예 | 예 |
> | bgpServiceCommunities | 아니오 | 아니오 |
> | connections | 예 | 예 |
> | ddosCustomPolicies | 예 | 예 |
> | ddosProtectionPlans | 예 | 예 |
> | dnsOperationStatuses | 아니오 | 아니오 |
> | dnszones | 예 | 예 |
> | dnszones/A | 아니오 | 아니오 |
> | dnszones/AAAA | 아니오 | 아니오 |
> | dnszones/모두 | 아니오 | 아니오 |
> | dnszones/CAA | 아니오 | 아니오 |
> | dnszones/CNAME | 아니오 | 아니오 |
> | dnszones/MX | 아니오 | 아니오 |
> | dnszones/NS | 아니오 | 아니오 |
> | dnszones/PTR | 아니오 | 아니오 |
> | dnszones/레코드 집합 | 아니오 | 아니오 |
> | dnszones/SOA | 아니오 | 아니오 |
> | dnszones/SRV | 아니오 | 아니오 |
> | dnszones/TXT | 아니오 | 아니오 |
> | expressRouteCircuits | 예 | 예 |
> | expressRouteCrossConnections | 예 | 예 |
> | expressRouteGateways | 예 | 예 |
> | expressRoutePorts | 예 | 예 |
> | expressRouteServiceProviders | 아니오 | 아니오 |
> | firewallPolicies | 예 | 예 |
> | frontdoors | 예, 제한 ( [아래 참고](#frontdoor)참조) | 예 |
> | frontdoorWebApplicationFirewallManagedRuleSets | 예, 제한 ( [아래 참고](#frontdoor)참조) | 아니오 |
> | frontdoorWebApplicationFirewallPolicies | 예, 제한 ( [아래 참고](#frontdoor)참조) | 예 |
> | getDnsResourceReference | 아니오 | 아니오 |
> | internalNotify | 아니오 | 아니오 |
> | loadBalancers | 예 | 아니오 |
> | localNetworkGateways | 예 | 예 |
> | natGateways | 예 | 예 |
> | networkIntentPolicies | 예 | 예 |
> | networkInterfaces | 예 | 예 |
> | networkProfiles | 예 | 예 |
> | networkSecurityGroups | 예 | 예 |
> | networkWatchers | 예 | 아니오 |
> | networkWatchers/connectionMonitors | 예 | 아니오 |
> | networkWatchers/lenses | 예 | 아니오 |
> | networkWatchers/ | 예 | 아니오 |
> | p2sVpnGateways | 예 | 예 |
> | privateDnsOperationStatuses | 아니오 | 아니오 |
> | privateDnsZones | 예 | 예 |
> | privateDnsZones/A | 아니오 | 아니오 |
> | privateDnsZones/AAAA | 아니오 | 아니오 |
> | privateDnsZones/모두 | 아니오 | 아니오 |
> | privateDnsZones/CNAME | 아니오 | 아니오 |
> | privateDnsZones/MX | 아니오 | 아니오 |
> | privateDnsZones/PTR | 아니오 | 아니오 |
> | privateDnsZones/SOA | 아니오 | 아니오 |
> | privateDnsZones/SRV | 아니오 | 아니오 |
> | privateDnsZones/TXT | 아니오 | 아니오 |
> | privateDnsZones / virtualNetworkLinks | 예 | 예 |
> | privateEndpoints | 예 | 예 |
> | privateLinkServices | 예 | 예 |
> | publicIPAddresses | 예 | 예 |
> | publicIPPrefixes | 예 | 예 |
> | routeFilters | 예 | 예 |
> | routeTables | 예 | 예 |
> | serviceEndpointPolicies | 예 | 예 |
> | trafficManagerGeographicHierarchies | 아니오 | 아니오 |
> | trafficmanagerprofiles | 예 | 예 |
> | trafficmanagerprofiles/heatMaps | 아니오 | 아니오 |
> | trafficManagerUserMetricsKeys | 아니오 | 아니오 |
> | virtualHubs | 예 | 예 |
> | virtualNetworkGateways | 예 | 예 |
> | virtualNetworks | 예 | 예 |
> | virtualNetworkTaps | 예 | 예 |
> | virtualWans | 예 | 예 |
> | vpnGateways | 예 | 아니오 |
> | vpnSites | 예 | 예 |
> | webApplicationFirewallPolicies | 예 | 예 |

<a id="frontdoor" />

> [!NOTE]
> Azure Front 도어 서비스의 경우 리소스를 만들 때 태그를 적용할 수 있지만, 태그를 업데이트 하거나 추가 하는 기능은 현재 지원 되지 않습니다.


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | namespaces | 예 | 아니오 |
> | 네임 스페이스/notificationHubs | 예 | 아니오 |

## <a name="microsoftobjectstore"></a>Microsoft ObjectStore

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | osNamespaces 스페이스 | 예 | 예 |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | HyperVSites | 예 | 예 |
> | ImportSites | 예 | 예 |
> | ServerSites | 예 | 예 |
> | VMwareSites | 예 | 예 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | clusters | 예 | 예 |
> | devices | 아니오 | 아니오 |
> | linkTargets | 아니오 | 아니오 |
> | storageInsightConfigs | 아니오 | 아니오 |
> | workspaces | 예 | 예 |
> | 작업 영역/데이터 원본 | 아니오 | 아니오 |
> | 작업 영역/Linkedservices.json 및 datasets.json | 아니오 | 아니오 |
> | 작업 영역/쿼리 | 아니오 | 아니오 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | managementassociations | 아니오 | 아니오 |
> | managementconfigurations | 예 | 예 |
> | solutions | 예 | 예 |
> | 뷰 | 예 | 예 |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | 아니오 | 아니오 |
> | peerAsns | 아니오 | 아니오 |
> | 피어 링 | 예 | 예 |
> | peeringServiceProviders | 아니오 | 아니오 |
> | peeringServices | 예 | 예 |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | policyEvents | 아니오 | 아니오 |
> | policyMetadata | 아니오 | 아니오 |
> | policyStates | 아니오 | 아니오 |
> | policyTrackedResources | 아니오 | 아니오 |
> | remediations | 아니오 | 아니오 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | consoles | 아니오 | 아니오 |
> | dashboards | 예 | 예 |
> | userSettings | 아니오 | 아니오 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | workspaceCollections | 예 | 예 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | capacities | 예 | 예 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | 아니오 | 아니오 |
> | vaults | 예 | 예 |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | namespaces | 예 | 예 |
> | 네임 스페이스/authorizationrules | 아니오 | 아니오 |
> | 네임 스페이스/hybridconnections | 아니오 | 아니오 |
> | 네임 스페이스/hybridconnections/authorizationrules | 아니오 | 아니오 |
> | 네임 스페이스/wcfrelays | 아니오 | 아니오 |
> | 네임 스페이스/wcfrelays/authorizationrules | 아니오 | 아니오 |

## <a name="microsoftremoteapp"></a>Microsoft RemoteApp

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 아니오 | 아니오 |
> | 수집이 | 예 | 예 |
> | 컬렉션/응용 프로그램 | 아니오 | 아니오 |
> | collections/securityprincipals | 아니오 | 아니오 |
> | 템플릿 이미지 | 아니오 | 아니오 |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 쿼리 | 예 | 예 |
> | resourceChangeDetails | 아니오 | 아니오 |
> | resourceChanges | 아니오 | 아니오 |
> | 리소스 | 아니오 | 아니오 |
> | 자원 기록 | 아니오 | 아니오 |
> | subscriptionsStatus | 아니오 | 아니오 |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | 아니오 | 아니오 |
> | childAvailabilityStatuses | 아니오 | 아니오 |
> | childResources | 아니오 | 아니오 |
> | events | 아니오 | 아니오 |
> | impactedResources | 아니오 | 아니오 |
> | metadata | 아니오 | 아니오 |
> | 알림 | 아니오 | 아니오 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 배포 | 예 | 아니오 |
> | 배포/작업 | 아니오 | 아니오 |
> | deploymentScripts | 예 | 예 |
> | deploymentScripts/logs | 아니오 | 아니오 |
> | links | 아니오 | 아니오 |
> | notifyResourceJobs | 아니오 | 아니오 |
> | providers | 아니오 | 아니오 |
> | resourceGroups | 예 | 아니오 |
> | 구독 | 아니오 | 아니오 |
> | tenants | 아니오 | 아니오 |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 애플리케이션 | 예 | 예 |
> | saasresources | 아니오 | 아니오 |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | jobcollections | 예 | 예 |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | 아니오 | 아니오 |
> | searchServices | 예 | 예 |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | 아니오 | 아니오 |
> | advancedThreatProtectionSettings | 아니오 | 아니오 |
> | 경고 | 아니오 | 아니오 |
> | allowedConnections | 아니오 | 아니오 |
> | applicationWhitelistings | 아니오 | 아니오 |
> | assessmentMetadata | 아니오 | 아니오 |
> | 인사 | 아니오 | 아니오 |
> | autoDismissAlertsRules | 아니오 | 아니오 |
> | 자동화 | 예 | 예 |
> | AutoProvisioningSettings | 아니오 | 아니오 |
> | Compliances | 아니오 | 아니오 |
> | dataCollectionAgents | 아니오 | 아니오 |
> | deviceSecurityGroups | 아니오 | 아니오 |
> | discoveredSecuritySolutions | 아니오 | 아니오 |
> | externalSecuritySolutions | 아니오 | 아니오 |
> | InformationProtectionPolicies | 아니오 | 아니오 |
> | iotSecuritySolutions | 예 | 예 |
> | IanalyticsModels Securitysolutions/ | 아니오 | 아니오 |
> | iotSecuritySolutions/analyticsModels/aggregatedAlerts | 아니오 | 아니오 |
> | iotSecuritySolutions/analyticsModels/aggregatedRecommendations | 아니오 | 아니오 |
> | jitNetworkAccessPolicies | 아니오 | 아니오 |
> | Networkdata.xml | 아니오 | 아니오 |
> | 정책 | 아니오 | 아니오 |
> | pricings | 아니오 | 아니오 |
> | regulatoryComplianceStandards | 아니오 | 아니오 |
> | regulatoryComplianceStandards / regulatoryComplianceControls | 아니오 | 아니오 |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | 아니오 | 아니오 |
> | securityContacts | 아니오 | 아니오 |
> | securitySolutions | 아니오 | 아니오 |
> | securitySolutionsReferenceData | 아니오 | 아니오 |
> | securityStatuses | 아니오 | 아니오 |
> | securityStatusesSummaries | 아니오 | 아니오 |
> | serverVulnerabilityAssessments | 아니오 | 아니오 |
> | 설정 | 아니오 | 아니오 |
> | 하위 평가 | 아니오 | 아니오 |
> | 태스크 | 아니오 | 아니오 |
> | topologies | 아니오 | 아니오 |
> | workspaceSettings | 아니오 | 아니오 |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 아니오 | 아니오 |
> | diagnosticSettingsCategories | 아니오 | 아니오 |

## <a name="microsoftsecurityinsights"></a>Microsoft SecurityInsights

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 집계 | 아니오 | 아니오 |
> | alertRules | 아니오 | 아니오 |
> | alertRuleTemplates | 아니오 | 아니오 |
> | 내부의 | 아니오 | 아니오 |
> | cases | 아니오 | 아니오 |
> | dataConnectors | 아니오 | 아니오 |
> | 엔터티 | 아니오 | 아니오 |
> | entityQueries | 아니오 | 아니오 |
> | officeConsents | 아니오 | 아니오 |
> | 설정 | 아니오 | 아니오 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | namespaces | 예 | 아니오 |
> | 네임 스페이스/authorizationrules | 아니오 | 아니오 |
> | 네임 스페이스/disasterrecoveryconfigs | 아니오 | 아니오 |
> | 네임 스페이스/eventgridfilters | 아니오 | 아니오 |
> | 네임 스페이스/네트워크 규칙 집합 | 아니오 | 아니오 |
> | 네임 스페이스/큐 | 아니오 | 아니오 |
> | 네임 스페이스/큐/authorizationrules | 아니오 | 아니오 |
> | 네임 스페이스/항목 | 아니오 | 아니오 |
> | 네임 스페이스/토픽/authorizationrules | 아니오 | 아니오 |
> | 네임 스페이스/토픽/구독 | 아니오 | 아니오 |
> | 네임 스페이스/항목/구독/규칙 | 아니오 | 아니오 |
> | premiumMessagingRegions | 아니오 | 아니오 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 애플리케이션 | 예 | 예 |
> | clusters | 예 | 예 |
> | 클러스터/응용 프로그램 | 아니오 | 아니오 |
> | containerGroups | 예 | 예 |
> | containerGroupSets | 예 | 예 |
> | edgeclusters | 예 | 예 |
> | edgeclusters/응용 프로그램 | 아니오 | 아니오 |
> | networks | 예 | 예 |
> | secretstores | 예 | 예 |
> | secretstores/인증서 | 아니오 | 아니오 |
> | secretstores/비밀 | 아니오 | 아니오 |
> | volumes | 예 | 예 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 애플리케이션 | 예 | 예 |
> | containerGroups | 예 | 예 |
> | gateways | 예 | 예 |
> | networks | 예 | 예 |
> | secrets | 예 | 예 |
> | volumes | 예 | 예 |

## <a name="microsoftservices"></a>Microsoft 서비스

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | 아니오 | 아니오 |
> | providerRegistrations resourceTypeRegistrations | 아니오 | 아니오 |
> | rollouts | 예 | 예 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | SignalR | 예 | 예 |
> | SignalR/eventGridFilters | 아니오 | 아니오 |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | 예 | 예 |

## <a name="microsoftsoftwareplan"></a>SoftwarePlan

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | 아니오 | 아니오 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | 예 | 예 |
> | 애플리케이션 | 예 | 예 |
> | jitRequests | 예 | 예 |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | managedInstances | 예 | 예 |
> | managedInstances/데이터베이스 | 아니오 | 아니오 |
> | managedInstances/databases/backupShortTermRetentionPolicies | 아니오 | 아니오 |
> | managedInstances/데이터베이스/스키마/테이블/열/sensitivityLabels | 아니오 | 아니오 |
> | managedInstances/databases/vulnerabilityAssessments | 아니오 | 아니오 |
> | managedInstances/databases/vulnerabilityAssessments/rules/기준선 | 아니오 | 아니오 |
> | managedInstances/가 나 보호기 | 아니오 | 아니오 |
> | managedInstances/키 | 아니오 | 아니오 |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | 아니오 | 아니오 |
> | managedInstances/vulnerabilityAssessments | 아니오 | 아니오 |
> | servers | 예 | 예 |
> | 서버/관리자 | 아니오 | 아니오 |
> | servers/communicationLinks | 아니오 | 아니오 |
> | 서버/데이터베이스 | 예 ( [아래 참고](#sqlnote)참조) | 예 |
> | 서버/서버 보호기 | 아니오 | 아니오 |
> | servers/firewallRules | 아니오 | 아니오 |
> | 서버/키 | 아니오 | 아니오 |
> | servers/restorableDroppedDatabases | 아니오 | 아니오 |
> | servers/serviceobjectives | 아니오 | 아니오 |
> | servers/tdeCertificates | 아니오 | 아니오 |
> | virtualClusters | 아니오 | 아니오 |

<a id="sqlnote" />

> [!NOTE]
> master 데이터베이스는 태그를 지원하지 않지만, Azure SQL Data Warehouse 데이터베이스를 포함한 다른 데이터베이스는 태그를 지원합니다. Azure SQL Data Warehouse 데이터베이스가 활성(일시 중지 아님) 상태여야 합니다.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | 예 | 예 |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | 아니오 | 아니오 |
> | SqlVirtualMachines | 예 | 예 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | storageAccounts | 예 | 예 |
> | storageAccounts/blobServices | 아니오 | 아니오 |
> | storageAccounts/fileServices | 아니오 | 아니오 |
> | storageAccounts/queueServices | 아니오 | 아니오 |
> | storageAccounts/서비스 | 아니오 | 아니오 |
> | storageAccounts/services/metricDefinitions | 아니오 | 아니오 |
> | storageAccounts/tableServices | 아니오 | 아니오 |
> | usages | 아니오 | 아니오 |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 캐시 | 예 | 예 |
> | 캐시/storageTargets 가져오기 | 아니오 | 아니오 |
> | usageModels | 아니오 | 아니오 |

## <a name="microsoftstoragereplication"></a>StorageReplication

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | replicationGroups | 아니오 | 아니오 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | 예 | 예 |
> | storageSyncServices/registeredServers | 아니오 | 아니오 |
> | storageSyncServices/syncGroups | 아니오 | 아니오 |
> | storageSyncServices/syncGroups/cloudEndpoints | 아니오 | 아니오 |
> | storageSyncServices/syncGroups/serverEndpoints | 아니오 | 아니오 |
> | storageSyncServices/워크플로 | 아니오 | 아니오 |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | 예 | 예 |
> | storageSyncServices/registeredServers | 아니오 | 아니오 |
> | storageSyncServices/syncGroups | 아니오 | 아니오 |
> | storageSyncServices/syncGroups/cloudEndpoints | 아니오 | 아니오 |
> | storageSyncServices/syncGroups/serverEndpoints | 아니오 | 아니오 |
> | storageSyncServices/워크플로 | 아니오 | 아니오 |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | 예 | 예 |
> | storageSyncServices/registeredServers | 아니오 | 아니오 |
> | storageSyncServices/syncGroups | 아니오 | 아니오 |
> | storageSyncServices/syncGroups/cloudEndpoints | 아니오 | 아니오 |
> | storageSyncServices/syncGroups/serverEndpoints | 아니오 | 아니오 |
> | storageSyncServices/워크플로 | 아니오 | 아니오 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | managers | 예 | 예 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | streamingjobs | 예(아래 참고를 참조) | 예 |

> [!NOTE]
> streamingjobs를 실행 중일 때 태그를 추가할 수 없습니다. 태그를 추가하려면 리소스를 중지합니다.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 취소 | 아니오 | 아니오 |
> | CreateSubscription | 아니오 | 아니오 |
> | enable | 아니오 | 아니오 |
> | 이름 바꾸기 | 아니오 | 아니오 |
> | SubscriptionDefinitions | 아니오 | 아니오 |
> | SubscriptionOperations | 아니오 | 아니오 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | environments | 예 | 아니오 |
> | 환경/Accesspolicy | 아니오 | 아니오 |
> | 환경/s s o 원본 | 예 | 아니오 |
> | environment/referenceDataSets | 예 | 아니오 |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | 예 | 예 |
> | dedicatedCloudServices | 예 | 예 |
> | virtualMachines | 예 | 예 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | 아니오 | 아니오 |
> | Apimanagementaccount/apiAcls | 아니오 | 아니오 |
> | Apimanagementaccount/api | 아니오 | 아니오 |
> | Apimanagementaccount/api/apiAcls | 아니오 | 아니오 |
> | Apimanagementaccount/api/connectionAcls | 아니오 | 아니오 |
> | Apimanagementaccount/api/연결 | 아니오 | 아니오 |
> | Apimanagementaccount/api/connections/connectionAcls | 아니오 | 아니오 |
> | Apimanagementaccount/api/localizedDefinitions | 아니오 | 아니오 |
> | Apimanagementaccount/connectionAcls | 아니오 | 아니오 |
> | Apimanagementaccount/연결 | 아니오 | 아니오 |
> | billingMeters | 아니오 | 아니오 |
> | certificates | 예 | 예 |
> | connectionGateways | 예 | 예 |
> | connections | 예 | 예 |
> | customApis | 예 | 예 |
> | deletedSites | 아니오 | 아니오 |
> | functions | 아니오 | 아니오 |
> | hostingEnvironments | 예 | 예 |
> | hostingEnvironments/multiRolePools | 아니오 | 아니오 |
> | hostingEnvironments/이상 풀 | 아니오 | 아니오 |
> | publishingUsers | 아니오 | 아니오 |
> | 동영상 추천 기능 | 아니오 | 아니오 |
> | resourceHealthMetadata | 아니오 | 아니오 |
> | runtimes | 아니오 | 아니오 |
> | serverFarms | 예 | 예 |
> | serverFarms/eventGridFilters | 아니오 | 아니오 |
> | sites | 예 | 예 |
> | 사이트/구성  | 아니오 | 아니오 |
> | sites/eventGridFilters | 아니오 | 아니오 |
> | 사이트/hostNameBindings | 아니오 | 아니오 |
> | 사이트/네트워크 구성 | 아니오 | 아니오 |
> | 사이트/premieraddons | 예 | 예 |
> | 사이트/슬롯 | 예 | 예 |
> | 사이트/슬롯/eventGridFilters 필터 | 아니오 | 아니오 |
> | 사이트/슬롯/hostNameBindings | 아니오 | 아니오 |
> | 사이트/슬롯/네트워크 구성 | 아니오 | 아니오 |
> | sourceControls | 아니오 | 아니오 |
> | validate | 아니오 | 아니오 |
> | verifyHostingEnvironmentVnet | 아니오 | 아니오 |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 아니오 | 아니오 |
> | diagnosticSettingsCategories | 아니오 | 아니오 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | DeviceServices | 예 | 예 |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | components | 아니오 | 아니오 |
> | componentsSummary | 아니오 | 아니오 |
> | monitorInstances | 아니오 | 아니오 |
> | monitorInstancesSummary | 아니오 | 아니오 |
> | monitors | 아니오 | 아니오 |
> | notificationSettings | 아니오 | 아니오 |

## <a name="next-steps"></a>다음 단계

리소스에 태그를 적용하는 방법을 알아보려면 [태그를 사용하여 Azure 리소스 구성](resource-group-using-tags.md)을 참조하세요.
