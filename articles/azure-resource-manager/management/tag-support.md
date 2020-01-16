---
title: 리소스에 대 한 태그 지원
description: 태그를 지원하는 Azure 리소스 종류를 보여 줍니다. 모든 Azure 서비스에 대한 세부 정보를 제공합니다.
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 4eb163376f1a0b8d8194defed4445578975976f2
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982215"
---
# <a name="tag-support-for-azure-resources"></a>Azure 리소스에 대한 태그 지원
이 문서에서는 리소스 종류가 [태그](tag-resources.md)를 지원하는지 여부를 설명합니다. **태그 지원** 이라는 열은 리소스 형식에 태그에 대 한 속성이 있는지 여부를 나타냅니다. **비용 보고서에서 태그** 레이블이 지정 된 열은 리소스 종류가 태그를 비용 보고서에 전달 하는지 여부를 나타냅니다. [Cost Management 비용 분석](../../cost-management-billing/costs/quick-acm-cost-analysis.md#understanding-grouping-and-filtering-options) 및 [Azure 청구 송장 및 일간 사용 현황 데이터](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md)에서 태그를 기준으로 비용을 볼 수 있습니다.

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
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | DomainServices | 예 | 예 |
> | DomainServices/oucontainer | 아닙니다. | 아닙니다. |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | supportProviders | 아닙니다. | 아닙니다. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | 아닙니다. | 아닙니다. |
> | addsservices | 아닙니다. | 아닙니다. |
> | agents | 아닙니다. | 아닙니다. |
> | anonymousapiusers | 아닙니다. | 아닙니다. |
> | 구성 | 아닙니다. | 아닙니다. |
> | 로그 | 아닙니다. | 아닙니다. |
> | reports | 아닙니다. | 아닙니다. |
> | servicehealthmetrics | 아닙니다. | 아닙니다. |
> | services | 아닙니다. | 아닙니다. |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 구성 | 아닙니다. | 아닙니다. |
> | generateRecommendations | 아닙니다. | 아닙니다. |
> | metadata | 아닙니다. | 아닙니다. |
> | 동영상 추천 기능 | 아닙니다. | 아닙니다. |
> | suppressions | 아닙니다. | 아닙니다. |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | actionRules | 예 | 예 |
> | 경고 | 아닙니다. | 아닙니다. |
> | alertsList | 아닙니다. | 아닙니다. |
> | alertsMetaData | 아닙니다. | 아닙니다. |
> | alertsSummary | 아닙니다. | 아닙니다. |
> | alertsSummaryList | 아닙니다. | 아닙니다. |
> | 피드백 | 아닙니다. | 아닙니다. |
> | smartDetectorAlertRules | 예 | 예 |
> | smartDetectorRuntimeEnvironments | 아닙니다. | 아닙니다. |
> | smartGroups | 아닙니다. | 아닙니다. |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 서버 | 예 | 예 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | reportFeedback | 아닙니다. | 아닙니다. |
> | 서비스 | 예 | 예 |
> | validateServiceName | 아닙니다. | 아닙니다. |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | configurationStores | 예 | 예 |
> | configurationStores/eventGridFilters | 아닙니다. | 아닙니다. |

## <a name="microsoftappplatform"></a>Microsoft AppPlatform

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | Spring | 예 | 예 |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | attestationProviders | 아닙니다. | 아닙니다. |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | classicAdministrators | 아닙니다. | 아닙니다. |
> | dataAliases | 아닙니다. | 아닙니다. |
> | denyAssignments | 아닙니다. | 아닙니다. |
> | elevateAccess | 아닙니다. | 아닙니다. |
> | findOrphanRoleAssignments | 아닙니다. | 아닙니다. |
> | locks | 아닙니다. | 아닙니다. |
> | 권한 | 아닙니다. | 아닙니다. |
> | policyAssignments | 아닙니다. | 아닙니다. |
> | policyDefinitions | 아닙니다. | 아닙니다. |
> | policySetDefinitions | 아닙니다. | 아닙니다. |
> | providerOperations | 아닙니다. | 아닙니다. |
> | roleAssignments | 아닙니다. | 아닙니다. |
> | roleDefinitions | 아닙니다. | 아닙니다. |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | automationAccounts | 예 | 예 |
> | automationAccounts/구성 | 예 | 예 |
> | automationAccounts/job | 아닙니다. | 아닙니다. |
> | automationAccounts/runbook | 예 | 예 |
> | automationAccounts/softwareUpdateConfigurations | 아닙니다. | 아닙니다. |
> | automationAccounts/웹 후크 | 아닙니다. | 아닙니다. |

## <a name="microsoftazconfig"></a>Azconfig

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | configurationStores | 예 | 예 |
> | configurationStores/eventGridFilters | 아닙니다. | 아닙니다. |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | environments | 아닙니다. | 아닙니다. |
> | 환경/계정 | 아닙니다. | 아닙니다. |
> | 환경/계정/네임 스페이스 | 아닙니다. | 아닙니다. |
> | 환경/계정/네임 스페이스/구성 | 아닙니다. | 아닙니다. |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | 예 | 아닙니다. |
> | b2ctenants | 아닙니다. | 아닙니다. |

## <a name="microsoftazuredata"></a>Microsoft AzureData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | hybridDataManagers | 예 | 예 |
> | postgresInstances | 예 | 예 |
> | sqlBigDataClusters | 예 | 예 |
> | sqlInstances | 예 | 예 |
> | sqlServerRegistrations | 예 | 예 |
> | sqlServerRegistrations/sqlServers | 아닙니다. | 아닙니다. |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | registrations | 예 | 예 |
> | 등록/customerSubscriptions | 아닙니다. | 아닙니다. |
> | 등록/제품 | 아닙니다. | 아닙니다. |
> | verificationKeys | 아닙니다. | 아닙니다. |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | batchAccounts | 예 | 예 |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | billingAccounts | 아닙니다. | 아닙니다. |
> | billingAccounts/규약 | 아닙니다. | 아닙니다. |
> | billingAccounts / billingPermissions | 아닙니다. | 아닙니다. |
> | billingAccounts / billingProfiles | 아닙니다. | 아닙니다. |
> | billingAccounts / billingProfiles / billingPermissions | 아닙니다. | 아닙니다. |
> | billingAccounts / billingProfiles / billingRoleAssignments | 아닙니다. | 아닙니다. |
> | billingAccounts / billingProfiles / billingRoleDefinitions | 아닙니다. | 아닙니다. |
> | billingAccounts / billingProfiles / billingSubscriptions | 아닙니다. | 아닙니다. |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | 아닙니다. | 아닙니다. |
> | billingAccounts/billingProfiles/고객 | 아닙니다. | 아닙니다. |
> | billingAccounts/billingProfiles/송장 | 아닙니다. | 아닙니다. |
> | billingAccounts/billingProfiles/청구서/가격표 | 아닙니다. | 아닙니다. |
> | billingAccounts / billingProfiles / invoiceSections | 아닙니다. | 아닙니다. |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | 아닙니다. | 아닙니다. |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | 아닙니다. | 아닙니다. |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | 아닙니다. | 아닙니다. |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | 아닙니다. | 아닙니다. |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | 아닙니다. | 아닙니다. |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | 아닙니다. | 아닙니다. |
> | billingAccounts/billingProfiles/invoiceSections/products | 아닙니다. | 아닙니다. |
> | billingAccounts/billingProfiles/invoiceSections/products/transfer | 아닙니다. | 아닙니다. |
> | billingAccounts/billingProfiles/invoiceSections/products/updateAutoRenew | 아닙니다. | 아닙니다. |
> | billingAccounts/billingProfiles/invoiceSections/트랜잭션 | 아닙니다. | 아닙니다. |
> | billingAccounts/billingProfiles/invoiceSections/transfer | 아닙니다. | 아닙니다. |
> | billingAccounts / BillingProfiles / patchOperations | 아닙니다. | 아닙니다. |
> | billingAccounts / billingProfiles / paymentMethods | 아닙니다. | 아닙니다. |
> | billingAccounts/billingProfiles/정책 | 아닙니다. | 아닙니다. |
> | billingAccounts/billingProfiles/가격표 | 아닙니다. | 아닙니다. |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | 아닙니다. | 아닙니다. |
> | billingAccounts/billingProfiles/제품 | 아닙니다. | 아닙니다. |
> | billingAccounts/billingProfiles/트랜잭션 | 아닙니다. | 아닙니다. |
> | billingAccounts / billingRoleAssignments | 아닙니다. | 아닙니다. |
> | billingAccounts / billingRoleDefinitions | 아닙니다. | 아닙니다. |
> | billingAccounts / billingSubscriptions | 아닙니다. | 아닙니다. |
> | billingAccounts/billingSubscriptions/송장 | 아닙니다. | 아닙니다. |
> | billingAccounts / createBillingRoleAssignment | 아닙니다. | 아닙니다. |
> | billingAccounts / createInvoiceSectionOperations | 아닙니다. | 아닙니다. |
> | billingAccounts/고객 | 아닙니다. | 아닙니다. |
> | billingAccounts/customers/billingPermissions | 아닙니다. | 아닙니다. |
> | billingAccounts/customers/billingSubscriptions | 아닙니다. | 아닙니다. |
> | billingAccounts/customers/initiateTransfer | 아닙니다. | 아닙니다. |
> | billingAccounts/고객/정책 | 아닙니다. | 아닙니다. |
> | billingAccounts/고객/제품 | 아닙니다. | 아닙니다. |
> | billingAccounts/고객/트랜잭션 | 아닙니다. | 아닙니다. |
> | billingAccounts/고객/전송 | 아닙니다. | 아닙니다. |
> | billingAccounts/부서 | 아닙니다. | 아닙니다. |
> | billingAccounts / enrollmentAccounts | 아닙니다. | 아닙니다. |
> | billingAccounts/송장 | 아닙니다. | 아닙니다. |
> | billingAccounts / invoiceSections | 아닙니다. | 아닙니다. |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | 아닙니다. | 아닙니다. |
> | billingAccounts / invoiceSections / billingSubscriptions | 아닙니다. | 아닙니다. |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | 아닙니다. | 아닙니다. |
> | billingAccounts/invoiceSections/상승 | 아닙니다. | 아닙니다. |
> | billingAccounts / invoiceSections / initiateTransfer | 아닙니다. | 아닙니다. |
> | billingAccounts / invoiceSections / patchOperations | 아닙니다. | 아닙니다. |
> | billingAccounts/invoiceSections/제품 Moveoperations | 아닙니다. | 아닙니다. |
> | billingAccounts/invoiceSections/제품 | 아닙니다. | 아닙니다. |
> | billingAccounts/invoiceSections/제품/이전 | 아닙니다. | 아닙니다. |
> | billingAccounts/invoiceSections/products/updateAutoRenew | 아닙니다. | 아닙니다. |
> | billingAccounts/invoiceSections/트랜잭션 | 아닙니다. | 아닙니다. |
> | billingAccounts/invoiceSections/전송 | 아닙니다. | 아닙니다. |
> | billingAccounts/lineOfCredit | 아닙니다. | 아닙니다. |
> | billingAccounts / patchOperations | 아닙니다. | 아닙니다. |
> | billingAccounts / paymentMethods | 아닙니다. | 아닙니다. |
> | billingAccounts/제품 | 아닙니다. | 아닙니다. |
> | billingAccounts/트랜잭션 | 아닙니다. | 아닙니다. |
> | billingPeriods | 아닙니다. | 아닙니다. |
> | billingPermissions | 아닙니다. | 아닙니다. |
> | billingProperty | 아닙니다. | 아닙니다. |
> | billingRoleAssignments | 아닙니다. | 아닙니다. |
> | billingRoleDefinitions | 아닙니다. | 아닙니다. |
> | createBillingRoleAssignment | 아닙니다. | 아닙니다. |
> | departments | 아닙니다. | 아닙니다. |
> | enrollmentAccounts | 아닙니다. | 아닙니다. |
> | invoices | 아닙니다. | 아닙니다. |
> | transfers | 아닙니다. | 아닙니다. |
> | 전송/acceptTransfer | 아닙니다. | 아닙니다. |
> | 전송/declineTransfer | 아닙니다. | 아닙니다. |
> | 전송/operationStatus | 아닙니다. | 아닙니다. |
> | 전송/validateTransfer | 아닙니다. | 아닙니다. |
> | validateAddress | 아닙니다. | 아닙니다. |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | mapApis | 예 | 예 |
> | updateCommunicationPreference | 아닙니다. | 아닙니다. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | 예 | 예 |
> | cordaMembers | 예 | 예 |
> | 감시자 | 예 | 예 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | 아닙니다. | 아닙니다. |
> | blueprintAssignments / assignmentOperations | 아닙니다. | 아닙니다. |
> | blueprintAssignments/작업 | 아닙니다. | 아닙니다. |
> | blueprints | 아닙니다. | 아닙니다. |
> | 청사진/아티팩트 | 아닙니다. | 아닙니다. |
> | 청사진/버전 | 아닙니다. | 아닙니다. |
> | 청사진/버전/아티팩트 | 아닙니다. | 아닙니다. |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | botServices | 예 | 예 |
> | botServices/채널 | 아닙니다. | 아닙니다. |
> | botServices/연결 | 아닙니다. | 아닙니다. |
> | 언어 | 아닙니다. | 아닙니다. |
> | 템플릿 | 아닙니다. | 아닙니다. |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | Redis | 예 | 예 |
> | RedisConfigDefinition | 아닙니다. | 아닙니다. |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | appliedReservations | 아닙니다. | 아닙니다. |
> | calculateExchange | 아닙니다. | 아닙니다. |
> | calculatePrice | 아닙니다. | 아닙니다. |
> | calculatePurchasePrice | 아닙니다. | 아닙니다. |
> | catalogs | 아닙니다. | 아닙니다. |
> | commercialReservationOrders | 아닙니다. | 아닙니다. |
> | exchange | 아닙니다. | 아닙니다. |
> | placePurchaseOrder | 아닙니다. | 아닙니다. |
> | reservationOrders | 아닙니다. | 아닙니다. |
> | reservationOrders / calculateRefund | 아닙니다. | 아닙니다. |
> | reservationOrders/병합 | 아닙니다. | 아닙니다. |
> | reservationOrders/예약 | 아닙니다. | 아닙니다. |
> | reservationOrders/예약/수정 버전 | 아닙니다. | 아닙니다. |
> | reservationOrders/반환 | 아닙니다. | 아닙니다. |
> | reservationOrders/분할 | 아닙니다. | 아닙니다. |
> | reservationOrders/교환 | 아닙니다. | 아닙니다. |
> | reservations | 아닙니다. | 아닙니다. |
> | 리소스 | 아닙니다. | 아닙니다. |
> | validateReservationOrder | 아닙니다. | 아닙니다. |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | 아닙니다. | 아닙니다. |
> | CdnWebApplicationFirewallPolicies | 예 | 예 |
> | edgenodes | 아닙니다. | 아닙니다. |
> | 프로필 | 예 | 예 |
> | 프로필/끝점 | 예 | 예 |
> | 프로필/끝점/customdomains | 아닙니다. | 아닙니다. |
> | 프로필/끝점/원본 | 아닙니다. | 아닙니다. |
> | validateProbe | 아닙니다. | 아닙니다. |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | certificateOrders | 예 | 예 |
> | certificateOrders/certificate | 아닙니다. | 아닙니다. |
> | validateCertificateRegistrationInformation | 아닙니다. | 아닙니다. |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | capabilities | 아닙니다. | 아닙니다. |
> | domainNames | 아닙니다. | 아닙니다. |
> | domainNames/기능 | 아닙니다. | 아닙니다. |
> | domainNames/internalLoadBalancers 장치 | 아닙니다. | 아닙니다. |
> | domainNames/serviceCertificates | 아닙니다. | 아닙니다. |
> | domainNames/슬롯 | 아닙니다. | 아닙니다. |
> | domainNames/슬롯/역할 | 아닙니다. | 아닙니다. |
> | domainNames/슬롯/역할/metricDefinitions | 아닙니다. | 아닙니다. |
> | domainNames/슬롯/역할/메트릭 | 아닙니다. | 아닙니다. |
> | moveSubscriptionResources | 아닙니다. | 아닙니다. |
> | operatingSystemFamilies | 아닙니다. | 아닙니다. |
> | operatingSystems | 아닙니다. | 아닙니다. |
> | quotas | 아닙니다. | 아닙니다. |
> | resourceTypes | 아닙니다. | 아닙니다. |
> | validateSubscriptionMoveAvailability | 아닙니다. | 아닙니다. |
> | virtualMachines | 아닙니다. | 아닙니다. |
> | virtualMachines/diagnosticSettings | 아닙니다. | 아닙니다. |
> | virtualMachines/metricDefinitions | 아닙니다. | 아닙니다. |
> | virtualMachines/메트릭 | 아닙니다. | 아닙니다. |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | 아닙니다. | 아닙니다. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | capabilities | 아닙니다. | 아닙니다. |
> | expressRouteCrossConnections | 아닙니다. | 아닙니다. |
> | expressRouteCrossConnections/피어 링 | 아닙니다. | 아닙니다. |
> | gatewaySupportedDevices | 아닙니다. | 아닙니다. |
> | networkSecurityGroups | 아닙니다. | 아닙니다. |
> | quotas | 아닙니다. | 아닙니다. |
> | reservedIps | 아닙니다. | 아닙니다. |
> | virtualNetworks | 아닙니다. | 아닙니다. |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | 아닙니다. | 아닙니다. |
> | virtualNetworks/virtualNetworkPeerings | 아닙니다. | 아닙니다. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | capabilities | 아닙니다. | 아닙니다. |
> | disks | 아닙니다. | 아닙니다. |
> | images | 아닙니다. | 아닙니다. |
> | osImages | 아닙니다. | 아닙니다. |
> | osPlatformImages | 아닙니다. | 아닙니다. |
> | publicImages | 아닙니다. | 아닙니다. |
> | quotas | 아닙니다. | 아닙니다. |
> | storageAccounts | 아닙니다. | 아닙니다. |
> | storageAccounts/blobServices | 아닙니다. | 아닙니다. |
> | storageAccounts/fileServices | 아닙니다. | 아닙니다. |
> | storageAccounts/metricDefinitions | 아닙니다. | 아닙니다. |
> | storageAccounts/메트릭 | 아닙니다. | 아닙니다. |
> | storageAccounts/queueServices | 아닙니다. | 아닙니다. |
> | storageAccounts/서비스 | 아닙니다. | 아닙니다. |
> | storageAccounts/services/diagnosticSettings | 아닙니다. | 아닙니다. |
> | storageAccounts/services/metricDefinitions | 아닙니다. | 아닙니다. |
> | storageAccounts/서비스/메트릭 | 아닙니다. | 아닙니다. |
> | storageAccounts/tableServices | 아닙니다. | 아닙니다. |
> | storageAccounts/vmImages | 아닙니다. | 아닙니다. |
> | vmImages | 아닙니다. | 아닙니다. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | RateCard | 아닙니다. | 아닙니다. |
> | UsageAggregates | 아닙니다. | 아닙니다. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | availabilitySets | 예 | 예 |
> | Disk; 집합 | 예 | 예 |
> | disks | 예 | 예 |
> | galleries | 예 | 예 |
> | 갤러리/응용 프로그램 | 아닙니다. | 아닙니다. |
> | 갤러리/응용 프로그램/버전 | 아닙니다. | 아닙니다. |
> | 갤러리/이미지 | 아닙니다. | 아닙니다. |
> | 갤러리/이미지/버전 | 아닙니다. | 아닙니다. |
> | 호스트 그룹 | 예 | 예 |
> | 호스트 그룹/호스트 | 예 | 예 |
> | images | 예 | 예 |
> | proximityPlacementGroups | 예 | 예 |
> | restorePointCollections | 예 | 예 |
> | restorePointCollections / restorePoints | 아닙니다. | 아닙니다. |
> | sharedVMImages | 예 | 예 |
> | sharedVMImages/버전 | 아닙니다. | 아닙니다. |
> | 스냅샷 | 예 | 예 |
> | virtualMachines | 예 | 예 |
> | virtualMachines/확장 | 예 | 예 |
> | virtualMachines/metricDefinitions | 아닙니다. | 아닙니다. |
> | virtualMachineScaleSets | 예 | 예 |
> | virtualMachineScaleSets/확장 | 아닙니다. | 아닙니다. |
> | virtualMachineScaleSets/networkInterfaces | 아닙니다. | 아닙니다. |
> | virtualMachineScaleSets/publicIPAddresses | 아닙니다. | 아닙니다. |
> | virtualMachineScaleSets/virtualMachines | 아닙니다. | 아닙니다. |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | 아닙니다. | 아닙니다. |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | 아닙니다. | 아닙니다. |
> | 잔액 | 아닙니다. | 아닙니다. |
> | 예산 | 아닙니다. | 아닙니다. |
> | Charges | 아닙니다. | 아닙니다. |
> | CostTags | 아닙니다. | 아닙니다. |
> | credits | 아닙니다. | 아닙니다. |
> | events | 아닙니다. | 아닙니다. |
> | 예측 | 아닙니다. | 아닙니다. |
> | lots | 아닙니다. | 아닙니다. |
> | Marketplace | 아닙니다. | 아닙니다. |
> | Pricesheets | 아닙니다. | 아닙니다. |
> | products | 아닙니다. | 아닙니다. |
> | ReservationDetails | 아닙니다. | 아닙니다. |
> | ReservationRecommendations | 아닙니다. | 아닙니다. |
> | ReservationSummaries | 아닙니다. | 아닙니다. |
> | ReservationTransactions | 아닙니다. | 아닙니다. |
> | 태그 | 아닙니다. | 아닙니다. |
> | tenants | 아닙니다. | 아닙니다. |
> | 용어 | 아닙니다. | 아닙니다. |
> | UsageDetails | 아닙니다. | 아닙니다. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | containerGroups | 예 | 예 |
> | serviceAssociationLinks | 아닙니다. | 아닙니다. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | registries | 예 | 예 |
> | 레지스트리/빌드 | 아닙니다. | 아닙니다. |
> | 레지스트리/빌드/취소 | 아닙니다. | 아닙니다. |
> | 레지스트리/빌드/getLogLink | 아닙니다. | 아닙니다. |
> | 레지스트리/buildTasks | 예 | 예 |
> | 레지스트리/buildTasks/단계 | 아닙니다. | 아닙니다. |
> | 레지스트리/eventGridFilters | 아닙니다. | 아닙니다. |
> | 레지스트리/generateCredentials | 아닙니다. | 아닙니다. |
> | 레지스트리/getBuildSourceUploadUrl | 아닙니다. | 아닙니다. |
> | 레지스트리/GetCredentials | 아닙니다. | 아닙니다. |
> | 레지스트리/importImage | 아닙니다. | 아닙니다. |
> | 레지스트리/queueBuild | 아닙니다. | 아닙니다. |
> | 레지스트리/regenerateCredential | 아닙니다. | 아닙니다. |
> | 레지스트리/regenerateCredentials | 아닙니다. | 아닙니다. |
> | 레지스트리/복제 | 예 | 예 |
> | 레지스트리/실행 | 아닙니다. | 아닙니다. |
> | 레지스트리/실행/취소 | 아닙니다. | 아닙니다. |
> | 레지스트리/scheduleRun | 아닙니다. | 아닙니다. |
> | 레지스트리/scopeMaps | 아닙니다. | 아닙니다. |
> | 레지스트리/taskRuns | 예 | 예 |
> | 레지스트리/작업 | 예 | 예 |
> | 레지스트리/토큰 | 아닙니다. | 아닙니다. |
> | 레지스트리/updatePolicies | 아닙니다. | 아닙니다. |
> | 레지스트리/웹 후크 | 예 | 예 |
> | registry/웹 후크/getcallbackconfig | 아닙니다. | 아닙니다. |
> | registry/웹 후크/ping | 아닙니다. | 아닙니다. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | containerServices | 예 | 예 |
> | managedClusters | 예 | 예 |
> | openShiftManagedClusters | 예 | 예 |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 경고 | 아닙니다. | 아닙니다. |
> | BillingAccounts | 아닙니다. | 아닙니다. |
> | 예산 | 아닙니다. | 아닙니다. |
> | CloudConnectors | 아닙니다. | 아닙니다. |
> | 커넥터 | 예 | 예 |
> | Departments | 아닙니다. | 아닙니다. |
> | 차원 | 아닙니다. | 아닙니다. |
> | EnrollmentAccounts | 아닙니다. | 아닙니다. |
> | 내보내기 | 아닙니다. | 아닙니다. |
> | ExternalBillingAccounts | 아닙니다. | 아닙니다. |
> | ExternalBillingAccounts/경고 | 아닙니다. | 아닙니다. |
> | ExternalBillingAccounts/차원 | 아닙니다. | 아닙니다. |
> | ExternalBillingAccounts/예측 | 아닙니다. | 아닙니다. |
> | ExternalBillingAccounts/쿼리 | 아닙니다. | 아닙니다. |
> | ExternalSubscriptions | 아닙니다. | 아닙니다. |
> | ExternalSubscriptions/Alerts | 아닙니다. | 아닙니다. |
> | ExternalSubscriptions/차원 | 아닙니다. | 아닙니다. |
> | ExternalSubscriptions/예측 | 아닙니다. | 아닙니다. |
> | ExternalSubscriptions/Query | 아닙니다. | 아닙니다. |
> | Forecast | 아닙니다. | 아닙니다. |
> | 쿼리 | 아닙니다. | 아닙니다. |
> | register | 아닙니다. | 아닙니다. |
> | Reportconfigs | 아닙니다. | 아닙니다. |
> | 보고서 | 아닙니다. | 아닙니다. |
> | 설정 | 아닙니다. | 아닙니다. |
> | showbackRules | 아닙니다. | 아닙니다. |
> | 뷰 | 아닙니다. | 아닙니다. |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | requests | 아닙니다. | 아닙니다. |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 연결 | 아닙니다. | 아닙니다. |
> | resourceProviders | 예 | 예 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | jobs | 예 | 예 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | 예 | 예 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | workspaces | 예 | 아닙니다. |
> | 작업 영역/virtualNetworkPeerings | 아닙니다. | 아닙니다. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | catalogs | 예 | 예 |
> | datacatalogs | 예 | 예 |
> | datacatalogs/데이터 원본 | 아닙니다. | 아닙니다. |
> | datacatalogs/데이터 원본/검색 | 아닙니다. | 아닙니다. |
> | datacatalogs/데이터 원본/검색/데이터 집합 | 아닙니다. | 아닙니다. |
> | datacatalogs/데이터 원본/검색/트리거 | 아닙니다. | 아닙니다. |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | dataFactories | 예 | 아닙니다. |
> | dataFactories/diagnosticSettings | 아닙니다. | 아닙니다. |
> | dataFactories/metricDefinitions | 아닙니다. | 아닙니다. |
> | dataFactorySchema | 아닙니다. | 아닙니다. |
> | factories | 예 | 아닙니다. |
> | 팩터리/integrationRuntimes | 아닙니다. | 아닙니다. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |
> | 계정/dataLakeStoreAccounts | 아닙니다. | 아닙니다. |
> | 계정/storageAccounts | 아닙니다. | 아닙니다. |
> | 계정/storageAccounts/컨테이너 | 아닙니다. | 아닙니다. |
> | 계정/transferAnalyticsUnits | 아닙니다. | 아닙니다. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |
> | 계정/eventGridFilters | 아닙니다. | 아닙니다. |
> | 계정/firewallRules | 아닙니다. | 아닙니다. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | services | 아닙니다. | 아닙니다. |
> | 서비스/프로젝트 | 아닙니다. | 아닙니다. |

## <a name="microsoftdatashare"></a>DataShare

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |
> | 계정/공유 | 아닙니다. | 아닙니다. |
> | 계정/공유/데이터 집합 | 아닙니다. | 아닙니다. |
> | 계정/공유/초대 | 아닙니다. | 아닙니다. |
> | 계정/공유/providersharesubscriptions | 아닙니다. | 아닙니다. |
> | 계정/공유/synchronizationSettings | 아닙니다. | 아닙니다. |
> | 계정/sharesubscriptions | 아닙니다. | 아닙니다. |
> | 계정/sharesubscriptions/consumerSourceDataSets | 아닙니다. | 아닙니다. |
> | 계정/sharesubscriptions/datasetmappings | 아닙니다. | 아닙니다. |
> | 계정/sharesubscriptions/트리거 | 아닙니다. | 아닙니다. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 서버 | 예 | 예 |
> | 서버/관리자 | 아닙니다. | 아닙니다. |
> | servers/privateEndpointConnectionProxies | 아닙니다. | 아닙니다. |
> | servers/privateEndpointConnections | 아닙니다. | 아닙니다. |
> | servers/privateLinkResources | 아닙니다. | 아닙니다. |
> | 서버/queryTexts | 아닙니다. | 아닙니다. |
> | servers/recoverableServers | 아닙니다. | 아닙니다. |
> | servers/topQueryStatistics | 아닙니다. | 아닙니다. |
> | servers/virtualNetworkRules | 아닙니다. | 아닙니다. |
> | 서버/waitStatistics | 아닙니다. | 아닙니다. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 서버 | 예 | 예 |
> | 서버/관리자 | 아닙니다. | 아닙니다. |
> | servers/privateEndpointConnectionProxies | 아닙니다. | 아닙니다. |
> | servers/privateEndpointConnections | 아닙니다. | 아닙니다. |
> | servers/privateLinkResources | 아닙니다. | 아닙니다. |
> | 서버/queryTexts | 아닙니다. | 아닙니다. |
> | servers/recoverableServers | 아닙니다. | 아닙니다. |
> | servers/topQueryStatistics | 아닙니다. | 아닙니다. |
> | servers/virtualNetworkRules | 아닙니다. | 아닙니다. |
> | 서버/waitStatistics | 아닙니다. | 아닙니다. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | serverGroups | 예 | 예 |
> | 서버 | 예 | 예 |
> | 서버/관리자 | 아닙니다. | 아닙니다. |
> | 서버/키 | 아닙니다. | 아닙니다. |
> | servers/privateEndpointConnectionProxies | 아닙니다. | 아닙니다. |
> | servers/privateEndpointConnections | 아닙니다. | 아닙니다. |
> | servers/privateLinkResources | 아닙니다. | 아닙니다. |
> | 서버/queryTexts | 아닙니다. | 아닙니다. |
> | servers/recoverableServers | 아닙니다. | 아닙니다. |
> | servers/topQueryStatistics | 아닙니다. | 아닙니다. |
> | servers/virtualNetworkRules | 아닙니다. | 아닙니다. |
> | 서버/waitStatistics | 아닙니다. | 아닙니다. |
> | serversv2 | 예 | 예 |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | artifactSources | 예 | 예 |
> | rollouts | 예 | 예 |
> | serviceTopologies | 예 | 예 |
> | serviceTopologies/서비스 | 예 | 예 |
> | serviceTopologies/서비스/serviceUnits | 예 | 예 |
> | 단계 | 예 | 예 |

## <a name="microsoftdesktopvirtualization"></a>Microsoft DesktopVirtualization

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | applicationgroups | 예 | 예 |
> | applicationgroups/응용 프로그램 | 아닙니다. | 아닙니다. |
> | applicationgroups/데스크톱 | 아닙니다. | 아닙니다. |
> | applicationgroups/startmenuitems | 아닙니다. | 아닙니다. |
> | hostpools | 예 | 예 |
> | hostpools/sessionhosts | 아닙니다. | 아닙니다. |
> | hostpools/sessionhosts/usersessions | 아닙니다. | 아닙니다. |
> | hostpools/usersessions | 아닙니다. | 아닙니다. |
> | workspaces | 예 | 예 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | ElasticPools | 예 | 예 |
> | ElasticPools / IotHubTenants | 예 | 예 |
> | IotHubs | 예 | 예 |
> | IotHubs/eventGridFilters | 아닙니다. | 아닙니다. |
> | ProvisioningServices | 예 | 예 |
> | usages | 아닙니다. | 아닙니다. |

## <a name="microsoftdevops"></a>Microsoft DevOps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 파이프라인 | 예 | 예 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | controllers | 예 | 예 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | labcenters | 예 | 예 |
> | labs | 예 | 예 |
> | 랩/환경 | 예 | 예 |
> | labs/serviceRunners | 예 | 예 |
> | labs/virtualMachines | 예 | 예 |
> | schedules | 예 | 예 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | 아닙니다. | 아닙니다. |
> | databaseAccounts | 예 | 예 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | domains | 예 | 예 |
> | 도메인/도메인 소유자의 식별자 | 아닙니다. | 아닙니다. |
> | generateSsoRequest | 아닙니다. | 아닙니다. |
> | topLevelDomains | 아닙니다. | 아닙니다. |
> | validateDomainRegistrationInformation | 아닙니다. | 아닙니다. |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | lcsprojects | 아닙니다. | 아닙니다. |
> | lcsprojects/clouddeployments | 아닙니다. | 아닙니다. |
> | lcsprojects/커넥터 | 아닙니다. | 아닙니다. |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | services | 예 | 예 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | domains | 예 | 예 |
> | 도메인/항목 | 아닙니다. | 아닙니다. |
> | eventSubscriptions | 아닙니다. | 아닙니다. |
> | extensionTopics | 아닙니다. | 아닙니다. |
> | topics | 예 | 예 |
> | topicTypes | 아닙니다. | 아닙니다. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | clusters | 예 | 예 |
> | 네임스페이스 | 예 | 예 |
> | 네임 스페이스/authorizationrules | 아닙니다. | 아닙니다. |
> | 네임 스페이스/disasterrecoveryconfigs | 아닙니다. | 아닙니다. |
> | 네임 스페이스/eventhubs | 아닙니다. | 아닙니다. |
> | 네임 스페이스/eventhubs/authorizationrules | 아닙니다. | 아닙니다. |
> | 네임 스페이스/eventhubs/consumergroups | 아닙니다. | 아닙니다. |
> | 네임 스페이스/네트워크 규칙 집합 | 아닙니다. | 아닙니다. |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | SaaS 앱 개발 | 아닙니다. | 아닙니다. |
> | providers | 아닙니다. | 아닙니다. |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | enroll | 아닙니다. | 아닙니다. |
> | galleryitems | 아닙니다. | 아닙니다. |
> | generateartifactaccessuri | 아닙니다. | 아닙니다. |
> | myareas | 아닙니다. | 아닙니다. |
> | myareas/areas | 아닙니다. | 아닙니다. |
> | myareas/areas/areas | 아닙니다. | 아닙니다. |
> | myareas/areas/areas/galleryitems | 아닙니다. | 아닙니다. |
> | myareas/areas/galleryitems | 아닙니다. | 아닙니다. |
> | myareas/galleryitems | 아닙니다. | 아닙니다. |
> | register | 아닙니다. | 아닙니다. |
> | 리소스 | 아닙니다. | 아닙니다. |
> | retrieveresourcesbyid | 아닙니다. | 아닙니다. |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | configurationprofil | 아닙니다. | 아닙니다. |
> | guestConfigurationAssignments | 아닙니다. | 아닙니다. |
> | software | 아닙니다. | 아닙니다. |
> | softwareUpdateProfile | 아닙니다. | 아닙니다. |
> | 업데이트 | 아닙니다. | 아닙니다. |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | hanaInstances | 예 | 예 |
> | sapMonitors | 예 | 예 |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | dedicatedHSMs | 예 | 예 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | clusters | 예 | 예 |
> | 클러스터/응용 프로그램 | 아닙니다. | 아닙니다. |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | services | 예 | 예 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 머신의 | 예 | 예 |
> | 컴퓨터/확장 | 예 | 예 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | dataManagers | 예 | 예 |

## <a name="microsofthydra"></a>Microsoft 히드라

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | components | 예 | 예 |
> | networkScopes | 예 | 예 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | jobs | 예 | 예 |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 아닙니다. | 아닙니다. |
> | diagnosticSettingsCategories | 아닙니다. | 아닙니다. |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | appTemplates | 아닙니다. | 아닙니다. |
> | IoTApps | 예 | 예 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 그래프 | 예 | 예 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | deletedVaults | 아닙니다. | 아닙니다. |
> | hsmPools | 예 | 예 |
> | vaults | 예 | 예 |
> | 자격 증명 모음/Accesspolicy | 아닙니다. | 아닙니다. |
> | 자격 증명 모음/eventGridFilters | 아닙니다. | 아닙니다. |
> | 자격 증명 모음/비밀 | 아닙니다. | 아닙니다. |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | clusters | 예 | 예 |
> | 클러스터/attacheddatabaseconfigurations | 아닙니다. | 아닙니다. |
> | 클러스터/데이터베이스 | 아닙니다. | 아닙니다. |
> | 클러스터/데이터베이스/dataconnections | 아닙니다. | 아닙니다. |
> | 클러스터/데이터베이스/eventhubconnections | 아닙니다. | 아닙니다. |
> | 클러스터/sharedidentities | 아닙니다. | 아닙니다. |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | labaccounts | 예 | 예 |
> | 사용자 | 아닙니다. | 아닙니다. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | 예 | 예 |
> | integrationAccounts | 예 | 예 |
> | integrationServiceEnvironments | 예 | 예 |
> | integrationServiceEnvironments/managedApis | 예 | 예 |
> | isolatedEnvironments | 예 | 예 |
> | workflows | 예 | 예 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | 예 | 예 |
> | webServices | 예 | 예 |
> | 작업 영역 | 예 | 예 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | workspaces | 예 | 예 |
> | 작업 영역/계산 | 아닙니다. | 아닙니다. |
> | workspace/eventGridFilters | 아닙니다. | 아닙니다. |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | Identities | 아닙니다. | 아닙니다. |
> | userAssignedIdentities | 예 | 예 |

## <a name="microsoftmanagedservices"></a>Microsoft ManagedServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | 아닙니다. | 아닙니다. |
> | registrationAssignments | 아닙니다. | 아닙니다. |
> | registrationDefinitions | 아닙니다. | 아닙니다. |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | getEntities | 아닙니다. | 아닙니다. |
> | managementGroups | 아닙니다. | 아닙니다. |
> | 리소스 | 아닙니다. | 아닙니다. |
> | startTenantBackfill | 아닙니다. | 아닙니다. |
> | tenantBackfillStatus | 아닙니다. | 아닙니다. |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 | 예 |
> | 계정/eventGridFilters | 아닙니다. | 아닙니다. |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | offers | 아닙니다. | 아닙니다. |
> | offerTypes | 아닙니다. | 아닙니다. |
> | offerTypes/게시자 | 아닙니다. | 아닙니다. |
> | offerTypes/게시자/제안 | 아닙니다. | 아닙니다. |
> | offerTypes/게시자/제품/계획 | 아닙니다. | 아닙니다. |
> | offerTypes/게시자/제품/계획/규약 | 아닙니다. | 아닙니다. |
> | offerTypes/게시자/제품/계획/configs | 아닙니다. | 아닙니다. |
> | offerTypes/publishers/제품/계획/configs/importImage | 아닙니다. | 아닙니다. |
> | privategalleryitems | 아닙니다. | 아닙니다. |
> | products | 아닙니다. | 아닙니다. |
> | 게시자 | 아닙니다. | 아닙니다. |
> | 게시자/제안 | 아닙니다. | 아닙니다. |
> | 게시자/제품/개정 | 아닙니다. | 아닙니다. |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | classicDevServices | 예 | 예 |
> | updateCommunicationPreference | 아닙니다. | 아닙니다. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | agreements | 아닙니다. | 아닙니다. |
> | offertypes | 아닙니다. | 아닙니다. |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | mediaservices | 예 | 예 |
> | windowsazure.mediaservices/accountFilters | 아닙니다. | 아닙니다. |
> | windowsazure.mediaservices/자산 | 아닙니다. | 아닙니다. |
> | windowsazure.mediaservices/asset/assetFilters | 아닙니다. | 아닙니다. |
> | windowsazure.mediaservices/Contentkeypolicy | 아닙니다. | 아닙니다. |
> | windowsazure.mediaservices/eventGridFilters | 아닙니다. | 아닙니다. |
> | windowsazure.mediaservices/liveEventOperations | 아닙니다. | 아닙니다. |
> | windowsazure.mediaservices/liveEvents | 예 | 예 |
> | windowsazure.mediaservices/liveEvents/Liveoutput | 아닙니다. | 아닙니다. |
> | windowsazure.mediaservices/liveOutputOperations | 아닙니다. | 아닙니다. |
> | windowsazure.mediaservices/mediaGraphs | 아닙니다. | 아닙니다. |
> | windowsazure.mediaservices/streamingEndpointOperations | 아닙니다. | 아닙니다. |
> | windowsazure.mediaservices/streamingEndpoints | 예 | 예 |
> | windowsazure.mediaservices/streamingLocators | 아닙니다. | 아닙니다. |
> | windowsazure.mediaservices/streamingPolicies | 아닙니다. | 아닙니다. |
> | windowsazure.mediaservices/변환 | 아닙니다. | 아닙니다. |
> | windowsazure.mediaservices/변환/작업 | 아닙니다. | 아닙니다. |

## <a name="microsoftmicroservices4spring"></a>Microservices4Spring

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | appClusters | 예 | 예 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | 예 | 예 |
> | migrateprojects | 예 | 예 |
> | projects | 예 | 예 |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | 예 | 예 |
> | objectUnderstandingAccounts | 예 | 예 |
> | remoteRenderingAccounts | 예 | 예 |
> | spatialAnchorsAccounts | 예 | 예 |
> | surfaceReconstructionAccounts | 예 | 예 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | 예 | 아닙니다. |
> | netAppAccounts/capacityPools | 예 | 아닙니다. |
> | netAppAccounts/capacityPools/볼륨 | 예 | 아닙니다. |
> | netAppAccounts/capacityPools/볼륨/mountTargets | 예 | 아닙니다. |
> | netAppAccounts/capacityPools/볼륨/스냅숏 | 예 | 아닙니다. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | applicationGateways | 예 | 예 |
> | applicationGatewayWebApplicationFirewallPolicies | 예 | 예 |
> | applicationSecurityGroups | 예 | 예 |
> | azureFirewallFqdnTags | 아닙니다. | 아닙니다. |
> | azureFirewalls | 예 | 아닙니다. |
> | bastionHosts | 예 | 예 |
> | bgpServiceCommunities | 아닙니다. | 아닙니다. |
> | connections | 예 | 예 |
> | ddosCustomPolicies | 예 | 예 |
> | ddosProtectionPlans | 예 | 예 |
> | dnsOperationStatuses | 아닙니다. | 아닙니다. |
> | dnszones | 예 | 예 |
> | dnszones/A | 아닙니다. | 아닙니다. |
> | dnszones/AAAA | 아닙니다. | 아닙니다. |
> | dnszones/모두 | 아닙니다. | 아닙니다. |
> | dnszones/CAA | 아닙니다. | 아닙니다. |
> | dnszones/CNAME | 아닙니다. | 아닙니다. |
> | dnszones/MX | 아닙니다. | 아닙니다. |
> | dnszones/NS | 아닙니다. | 아닙니다. |
> | dnszones/PTR | 아닙니다. | 아닙니다. |
> | dnszones/레코드 집합 | 아닙니다. | 아닙니다. |
> | dnszones/SOA | 아닙니다. | 아닙니다. |
> | dnszones/SRV | 아닙니다. | 아닙니다. |
> | dnszones/TXT | 아닙니다. | 아닙니다. |
> | expressRouteCircuits | 예 | 예 |
> | expressRouteCrossConnections | 예 | 예 |
> | expressRouteGateways | 예 | 예 |
> | expressRoutePorts | 예 | 예 |
> | expressRouteServiceProviders | 아닙니다. | 아닙니다. |
> | firewallPolicies | 예 | 예 |
> | frontdoors | 예, 제한 ( [아래 참고](#frontdoor)참조) | 예 |
> | frontdoorWebApplicationFirewallManagedRuleSets | 예, 제한 ( [아래 참고](#frontdoor)참조) | 아닙니다. |
> | frontdoorWebApplicationFirewallPolicies | 예, 제한 ( [아래 참고](#frontdoor)참조) | 예 |
> | getDnsResourceReference | 아닙니다. | 아닙니다. |
> | internalNotify | 아닙니다. | 아닙니다. |
> | loadBalancers | 예 | 아닙니다. |
> | localNetworkGateways | 예 | 예 |
> | natGateways | 예 | 예 |
> | networkIntentPolicies | 예 | 예 |
> | networkInterfaces | 예 | 예 |
> | networkProfiles | 예 | 예 |
> | networkSecurityGroups | 예 | 예 |
> | networkWatchers | 예 | 아닙니다. |
> | networkWatchers/connectionMonitors | 예 | 아닙니다. |
> | networkWatchers/lenses | 예 | 아닙니다. |
> | networkWatchers/ | 예 | 아닙니다. |
> | p2sVpnGateways | 예 | 예 |
> | privateDnsOperationStatuses | 아닙니다. | 아닙니다. |
> | privateDnsZones | 예 | 예 |
> | privateDnsZones/A | 아닙니다. | 아닙니다. |
> | privateDnsZones/AAAA | 아닙니다. | 아닙니다. |
> | privateDnsZones/모두 | 아닙니다. | 아닙니다. |
> | privateDnsZones/CNAME | 아닙니다. | 아닙니다. |
> | privateDnsZones/MX | 아닙니다. | 아닙니다. |
> | privateDnsZones/PTR | 아닙니다. | 아닙니다. |
> | privateDnsZones/SOA | 아닙니다. | 아닙니다. |
> | privateDnsZones/SRV | 아닙니다. | 아닙니다. |
> | privateDnsZones/TXT | 아닙니다. | 아닙니다. |
> | privateDnsZones / virtualNetworkLinks | 예 | 예 |
> | privateEndpoints | 예 | 예 |
> | privateLinkServices | 예 | 예 |
> | publicIPAddresses | 예 | 예 |
> | publicIPPrefixes | 예 | 예 |
> | routeFilters | 예 | 예 |
> | routeTables | 예 | 예 |
> | serviceEndpointPolicies | 예 | 예 |
> | trafficManagerGeographicHierarchies | 아닙니다. | 아닙니다. |
> | trafficmanagerprofiles | 예 | 예 |
> | trafficmanagerprofiles/heatMaps | 아닙니다. | 아닙니다. |
> | trafficManagerUserMetricsKeys | 아닙니다. | 아닙니다. |
> | virtualHubs | 예 | 예 |
> | virtualNetworkGateways | 예 | 예 |
> | virtualNetworks | 예 | 예 |
> | virtualNetworkTaps | 예 | 예 |
> | virtualWans | 예 | 예 |
> | vpnGateways | 예 | 아닙니다. |
> | vpnSites | 예 | 예 |
> | webApplicationFirewallPolicies | 예 | 예 |

<a id="frontdoor" />

> [!NOTE]
> Azure Front 도어 서비스의 경우 리소스를 만들 때 태그를 적용할 수 있지만, 태그를 업데이트 하거나 추가 하는 기능은 현재 지원 되지 않습니다.


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 네임스페이스 | 예 | 아닙니다. |
> | 네임 스페이스/notificationHubs | 예 | 아닙니다. |

## <a name="microsoftobjectstore"></a>Microsoft ObjectStore

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | osNamespaces 스페이스 | 예 | 예 |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | HyperVSites | 예 | 예 |
> | ImportSites | 예 | 예 |
> | ServerSites | 예 | 예 |
> | VMwareSites | 예 | 예 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | clusters | 예 | 예 |
> | devices | 아닙니다. | 아닙니다. |
> | linkTargets | 아닙니다. | 아닙니다. |
> | storageInsightConfigs | 아닙니다. | 아닙니다. |
> | workspaces | 예 | 예 |
> | 작업 영역/데이터 원본 | 아닙니다. | 아닙니다. |
> | 작업 영역/Linkedservices.json 및 datasets.json | 아닙니다. | 아닙니다. |
> | 작업 영역/쿼리 | 아닙니다. | 아닙니다. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | managementassociations | 아닙니다. | 아닙니다. |
> | managementconfigurations | 예 | 예 |
> | solutions | 예 | 예 |
> | 뷰 | 예 | 예 |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | 아닙니다. | 아닙니다. |
> | peerAsns | 아닙니다. | 아닙니다. |
> | 피어 링 | 예 | 예 |
> | peeringServiceProviders | 아닙니다. | 아닙니다. |
> | peeringServices | 예 | 예 |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | policyEvents | 아닙니다. | 아닙니다. |
> | policyMetadata | 아닙니다. | 아닙니다. |
> | policyStates | 아닙니다. | 아닙니다. |
> | policyTrackedResources | 아닙니다. | 아닙니다. |
> | remediations | 아닙니다. | 아닙니다. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | consoles | 아닙니다. | 아닙니다. |
> | dashboards | 예 | 예 |
> | userSettings | 아닙니다. | 아닙니다. |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | workspaceCollections | 예 | 예 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | capacities | 예 | 예 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | 아닙니다. | 아닙니다. |
> | vaults | 예 | 예 |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 네임스페이스 | 예 | 예 |
> | 네임 스페이스/authorizationrules | 아닙니다. | 아닙니다. |
> | 네임 스페이스/hybridconnections | 아닙니다. | 아닙니다. |
> | 네임 스페이스/hybridconnections/authorizationrules | 아닙니다. | 아닙니다. |
> | 네임 스페이스/wcfrelays | 아닙니다. | 아닙니다. |
> | 네임 스페이스/wcfrelays/authorizationrules | 아닙니다. | 아닙니다. |

## <a name="microsoftremoteapp"></a>Microsoft RemoteApp

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 아닙니다. | 아닙니다. |
> | 컬렉션 | 예 | 예 |
> | 컬렉션/응용 프로그램 | 아닙니다. | 아닙니다. |
> | collections/securityprincipals | 아닙니다. | 아닙니다. |
> | 템플릿 이미지 | 아닙니다. | 아닙니다. |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 쿼리 | 예 | 예 |
> | resourceChangeDetails | 아닙니다. | 아닙니다. |
> | resourceChanges | 아닙니다. | 아닙니다. |
> | 리소스 | 아닙니다. | 아닙니다. |
> | 자원 기록 | 아닙니다. | 아닙니다. |
> | subscriptionsStatus | 아닙니다. | 아닙니다. |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | 아닙니다. | 아닙니다. |
> | childAvailabilityStatuses | 아닙니다. | 아닙니다. |
> | childResources | 아닙니다. | 아닙니다. |
> | events | 아닙니다. | 아닙니다. |
> | impactedResources | 아닙니다. | 아닙니다. |
> | metadata | 아닙니다. | 아닙니다. |
> | 알림 | 아닙니다. | 아닙니다. |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 배포 | 예 | 아닙니다. |
> | 배포/작업 | 아닙니다. | 아닙니다. |
> | deploymentScripts | 예 | 예 |
> | deploymentScripts/logs | 아닙니다. | 아닙니다. |
> | 링크 | 아닙니다. | 아닙니다. |
> | notifyResourceJobs | 아닙니다. | 아닙니다. |
> | providers | 아닙니다. | 아닙니다. |
> | resourceGroups | 예 | 아닙니다. |
> | 구독 | 아닙니다. | 아닙니다. |
> | tenants | 아닙니다. | 아닙니다. |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 애플리케이션 | 예 | 예 |
> | saasresources | 아닙니다. | 아닙니다. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | jobcollections | 예 | 예 |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | 아닙니다. | 아닙니다. |
> | searchServices | 예 | 예 |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | 아닙니다. | 아닙니다. |
> | advancedThreatProtectionSettings | 아닙니다. | 아닙니다. |
> | 경고 | 아닙니다. | 아닙니다. |
> | allowedConnections | 아닙니다. | 아닙니다. |
> | applicationWhitelistings | 아닙니다. | 아닙니다. |
> | assessmentMetadata | 아닙니다. | 아닙니다. |
> | 평가 | 아닙니다. | 아닙니다. |
> | autoDismissAlertsRules | 아닙니다. | 아닙니다. |
> | 자동화 | 예 | 예 |
> | AutoProvisioningSettings | 아닙니다. | 아닙니다. |
> | Compliances | 아닙니다. | 아닙니다. |
> | dataCollectionAgents | 아닙니다. | 아닙니다. |
> | deviceSecurityGroups | 아닙니다. | 아닙니다. |
> | discoveredSecuritySolutions | 아닙니다. | 아닙니다. |
> | externalSecuritySolutions | 아닙니다. | 아닙니다. |
> | InformationProtectionPolicies | 아닙니다. | 아닙니다. |
> | iotSecuritySolutions | 예 | 예 |
> | IanalyticsModels Securitysolutions/ | 아닙니다. | 아닙니다. |
> | iotSecuritySolutions/analyticsModels/aggregatedAlerts | 아닙니다. | 아닙니다. |
> | iotSecuritySolutions/analyticsModels/aggregatedRecommendations | 아닙니다. | 아닙니다. |
> | jitNetworkAccessPolicies | 아닙니다. | 아닙니다. |
> | Networkdata.xml | 아닙니다. | 아닙니다. |
> | 정책 | 아닙니다. | 아닙니다. |
> | pricings | 아닙니다. | 아닙니다. |
> | regulatoryComplianceStandards | 아닙니다. | 아닙니다. |
> | regulatoryComplianceStandards / regulatoryComplianceControls | 아닙니다. | 아닙니다. |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | 아닙니다. | 아닙니다. |
> | securityContacts | 아닙니다. | 아닙니다. |
> | securitySolutions | 아닙니다. | 아닙니다. |
> | securitySolutionsReferenceData | 아닙니다. | 아닙니다. |
> | securityStatuses | 아닙니다. | 아닙니다. |
> | securityStatusesSummaries | 아닙니다. | 아닙니다. |
> | serverVulnerabilityAssessments | 아닙니다. | 아닙니다. |
> | 설정 | 아닙니다. | 아닙니다. |
> | 하위 평가 | 아닙니다. | 아닙니다. |
> | tasks | 아닙니다. | 아닙니다. |
> | topologies | 아닙니다. | 아닙니다. |
> | workspaceSettings | 아닙니다. | 아닙니다. |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 아닙니다. | 아닙니다. |
> | diagnosticSettingsCategories | 아닙니다. | 아닙니다. |

## <a name="microsoftsecurityinsights"></a>Microsoft SecurityInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | aggregations | 아닙니다. | 아닙니다. |
> | alertRules | 아닙니다. | 아닙니다. |
> | alertRuleTemplates | 아닙니다. | 아닙니다. |
> | 책갈피 | 아닙니다. | 아닙니다. |
> | cases | 아닙니다. | 아닙니다. |
> | dataConnectors | 아닙니다. | 아닙니다. |
> | 엔터티 | 아닙니다. | 아닙니다. |
> | entityQueries | 아닙니다. | 아닙니다. |
> | officeConsents | 아닙니다. | 아닙니다. |
> | 설정 | 아닙니다. | 아닙니다. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 네임스페이스 | 예 | 아닙니다. |
> | 네임 스페이스/authorizationrules | 아닙니다. | 아닙니다. |
> | 네임 스페이스/disasterrecoveryconfigs | 아닙니다. | 아닙니다. |
> | 네임 스페이스/eventgridfilters | 아닙니다. | 아닙니다. |
> | 네임 스페이스/네트워크 규칙 집합 | 아닙니다. | 아닙니다. |
> | 네임 스페이스/큐 | 아닙니다. | 아닙니다. |
> | 네임 스페이스/큐/authorizationrules | 아닙니다. | 아닙니다. |
> | 네임 스페이스/항목 | 아닙니다. | 아닙니다. |
> | 네임 스페이스/토픽/authorizationrules | 아닙니다. | 아닙니다. |
> | 네임 스페이스/토픽/구독 | 아닙니다. | 아닙니다. |
> | 네임 스페이스/항목/구독/규칙 | 아닙니다. | 아닙니다. |
> | premiumMessagingRegions | 아닙니다. | 아닙니다. |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 애플리케이션 | 예 | 예 |
> | clusters | 예 | 예 |
> | 클러스터/응용 프로그램 | 아닙니다. | 아닙니다. |
> | containerGroups | 예 | 예 |
> | containerGroupSets | 예 | 예 |
> | edgeclusters | 예 | 예 |
> | edgeclusters/응용 프로그램 | 아닙니다. | 아닙니다. |
> | networks | 예 | 예 |
> | secretstores | 예 | 예 |
> | secretstores/인증서 | 아닙니다. | 아닙니다. |
> | secretstores/비밀 | 아닙니다. | 아닙니다. |
> | volumes | 예 | 예 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 애플리케이션 | 예 | 예 |
> | containerGroups | 예 | 예 |
> | gateways | 예 | 예 |
> | networks | 예 | 예 |
> | secrets | 예 | 예 |
> | volumes | 예 | 예 |

## <a name="microsoftservices"></a>Microsoft 서비스

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | 아닙니다. | 아닙니다. |
> | providerRegistrations resourceTypeRegistrations | 아닙니다. | 아닙니다. |
> | rollouts | 예 | 예 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | SignalR | 예 | 예 |
> | SignalR/eventGridFilters | 아닙니다. | 아닙니다. |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | 예 | 예 |

## <a name="microsoftsoftwareplan"></a>SoftwarePlan

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | 아닙니다. | 아닙니다. |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | 예 | 예 |
> | 애플리케이션 | 예 | 예 |
> | jitRequests | 예 | 예 |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | managedInstances | 예 | 예 |
> | managedInstances/데이터베이스 | 아닙니다. | 아닙니다. |
> | managedInstances/databases/backupShortTermRetentionPolicies | 아닙니다. | 아닙니다. |
> | managedInstances/데이터베이스/스키마/테이블/열/sensitivityLabels | 아닙니다. | 아닙니다. |
> | managedInstances/databases/vulnerabilityAssessments | 아닙니다. | 아닙니다. |
> | managedInstances/databases/vulnerabilityAssessments/rules/기준선 | 아닙니다. | 아닙니다. |
> | managedInstances/가 나 보호기 | 아닙니다. | 아닙니다. |
> | managedInstances/키 | 아닙니다. | 아닙니다. |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | 아닙니다. | 아닙니다. |
> | managedInstances/vulnerabilityAssessments | 아닙니다. | 아닙니다. |
> | 서버 | 예 | 예 |
> | 서버/관리자 | 아닙니다. | 아닙니다. |
> | servers/communicationLinks | 아닙니다. | 아닙니다. |
> | 서버/데이터베이스 | 예 ( [아래 참고](#sqlnote)참조) | 예 |
> | 서버/서버 보호기 | 아닙니다. | 아닙니다. |
> | servers/firewallRules | 아닙니다. | 아닙니다. |
> | 서버/키 | 아닙니다. | 아닙니다. |
> | servers/restorableDroppedDatabases | 아닙니다. | 아닙니다. |
> | servers/serviceobjectives | 아닙니다. | 아닙니다. |
> | servers/tdeCertificates | 아닙니다. | 아닙니다. |
> | virtualClusters | 아닙니다. | 아닙니다. |

<a id="sqlnote" />

> [!NOTE]
> master 데이터베이스는 태그를 지원하지 않지만, Azure SQL Data Warehouse 데이터베이스를 포함한 다른 데이터베이스는 태그를 지원합니다. Azure SQL Data Warehouse 데이터베이스가 활성(일시 중지 아님) 상태여야 합니다.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | 예 | 예 |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | 아닙니다. | 아닙니다. |
> | SqlVirtualMachines | 예 | 예 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | storageAccounts | 예 | 예 |
> | storageAccounts/blobServices | 아닙니다. | 아닙니다. |
> | storageAccounts/fileServices | 아닙니다. | 아닙니다. |
> | storageAccounts/queueServices | 아닙니다. | 아닙니다. |
> | storageAccounts/서비스 | 아닙니다. | 아닙니다. |
> | storageAccounts/services/metricDefinitions | 아닙니다. | 아닙니다. |
> | storageAccounts/tableServices | 아닙니다. | 아닙니다. |
> | usages | 아닙니다. | 아닙니다. |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 캐시 | 예 | 예 |
> | 캐시/storageTargets 가져오기 | 아닙니다. | 아닙니다. |
> | usageModels | 아닙니다. | 아닙니다. |

## <a name="microsoftstoragereplication"></a>StorageReplication

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | replicationGroups | 아닙니다. | 아닙니다. |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | 예 | 예 |
> | storageSyncServices/registeredServers | 아닙니다. | 아닙니다. |
> | storageSyncServices/syncGroups | 아닙니다. | 아닙니다. |
> | storageSyncServices/syncGroups/cloudEndpoints | 아닙니다. | 아닙니다. |
> | storageSyncServices/syncGroups/serverEndpoints | 아닙니다. | 아닙니다. |
> | storageSyncServices/워크플로 | 아닙니다. | 아닙니다. |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | 예 | 예 |
> | storageSyncServices/registeredServers | 아닙니다. | 아닙니다. |
> | storageSyncServices/syncGroups | 아닙니다. | 아닙니다. |
> | storageSyncServices/syncGroups/cloudEndpoints | 아닙니다. | 아닙니다. |
> | storageSyncServices/syncGroups/serverEndpoints | 아닙니다. | 아닙니다. |
> | storageSyncServices/워크플로 | 아닙니다. | 아닙니다. |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | 예 | 예 |
> | storageSyncServices/registeredServers | 아닙니다. | 아닙니다. |
> | storageSyncServices/syncGroups | 아닙니다. | 아닙니다. |
> | storageSyncServices/syncGroups/cloudEndpoints | 아닙니다. | 아닙니다. |
> | storageSyncServices/syncGroups/serverEndpoints | 아닙니다. | 아닙니다. |
> | storageSyncServices/워크플로 | 아닙니다. | 아닙니다. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | managers | 예 | 예 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | streamingjobs | 예(아래 참고를 참조) | 예 |

> [!NOTE]
> streamingjobs를 실행 중일 때 태그를 추가할 수 없습니다. 태그를 추가하려면 리소스를 중지합니다.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | cancel | 아닙니다. | 아닙니다. |
> | CreateSubscription | 아닙니다. | 아닙니다. |
> | enable | 아닙니다. | 아닙니다. |
> | 이름 바꾸기 | 아닙니다. | 아닙니다. |
> | SubscriptionDefinitions | 아닙니다. | 아닙니다. |
> | SubscriptionOperations | 아닙니다. | 아닙니다. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | environments | 예 | 아닙니다. |
> | 환경/Accesspolicy | 아닙니다. | 아닙니다. |
> | 환경/s s o 원본 | 예 | 아닙니다. |
> | environment/referenceDataSets | 예 | 아닙니다. |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | 예 | 예 |
> | dedicatedCloudServices | 예 | 예 |
> | virtualMachines | 예 | 예 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | 아닙니다. | 아닙니다. |
> | Apimanagementaccount/apiAcls | 아닙니다. | 아닙니다. |
> | Apimanagementaccount/api | 아닙니다. | 아닙니다. |
> | Apimanagementaccount/api/apiAcls | 아닙니다. | 아닙니다. |
> | Apimanagementaccount/api/connectionAcls | 아닙니다. | 아닙니다. |
> | Apimanagementaccount/api/연결 | 아닙니다. | 아닙니다. |
> | Apimanagementaccount/api/connections/connectionAcls | 아닙니다. | 아닙니다. |
> | Apimanagementaccount/api/localizedDefinitions | 아닙니다. | 아닙니다. |
> | Apimanagementaccount/connectionAcls | 아닙니다. | 아닙니다. |
> | Apimanagementaccount/연결 | 아닙니다. | 아닙니다. |
> | billingMeters | 아닙니다. | 아닙니다. |
> | certificates | 예 | 예 |
> | connectionGateways | 예 | 예 |
> | connections | 예 | 예 |
> | customApis | 예 | 예 |
> | deletedSites | 아닙니다. | 아닙니다. |
> | Functions | 아닙니다. | 아닙니다. |
> | hostingEnvironments | 예 | 예 |
> | hostingEnvironments/multiRolePools | 아닙니다. | 아닙니다. |
> | hostingEnvironments/이상 풀 | 아닙니다. | 아닙니다. |
> | publishingUsers | 아닙니다. | 아닙니다. |
> | 동영상 추천 기능 | 아닙니다. | 아닙니다. |
> | resourceHealthMetadata | 아닙니다. | 아닙니다. |
> | runtimes | 아닙니다. | 아닙니다. |
> | serverFarms | 예 | 예 |
> | serverFarms/eventGridFilters | 아닙니다. | 아닙니다. |
> | sites | 예 | 예 |
> | 사이트/구성  | 아닙니다. | 아닙니다. |
> | sites/eventGridFilters | 아닙니다. | 아닙니다. |
> | 사이트/hostNameBindings | 아닙니다. | 아닙니다. |
> | 사이트/네트워크 구성 | 아닙니다. | 아닙니다. |
> | 사이트/premieraddons | 예 | 예 |
> | 사이트/슬롯 | 예 | 예 |
> | 사이트/슬롯/eventGridFilters 필터 | 아닙니다. | 아닙니다. |
> | 사이트/슬롯/hostNameBindings | 아닙니다. | 아닙니다. |
> | 사이트/슬롯/네트워크 구성 | 아닙니다. | 아닙니다. |
> | sourceControls | 아닙니다. | 아닙니다. |
> | validate | 아닙니다. | 아닙니다. |
> | verifyHostingEnvironmentVnet | 아닙니다. | 아닙니다. |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 아닙니다. | 아닙니다. |
> | diagnosticSettingsCategories | 아닙니다. | 아닙니다. |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | DeviceServices | 예 | 예 |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 리소스 유형 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | components | 아닙니다. | 아닙니다. |
> | componentsSummary | 아닙니다. | 아닙니다. |
> | monitorInstances | 아닙니다. | 아닙니다. |
> | monitorInstancesSummary | 아닙니다. | 아닙니다. |
> | monitors | 아닙니다. | 아닙니다. |
> | notificationSettings | 아닙니다. | 아닙니다. |

## <a name="next-steps"></a>다음 단계

리소스에 태그를 적용하는 방법을 알아보려면 [태그를 사용하여 Azure 리소스 구성](tag-resources.md)을 참조하세요.
