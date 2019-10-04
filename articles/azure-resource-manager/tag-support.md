---
title: 리소스에 대한 Azure Resource Manager 태그 지원
description: 태그를 지원하는 Azure 리소스 종류를 보여 줍니다. 모든 Azure 서비스에 대한 세부 정보를 제공합니다.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: tomfitz
ms.openlocfilehash: 06028224379bd42e1e0ea21b684ce443abd5992a
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71937009"
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
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
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
> - [GuestConfiguration](#microsoftguestconfiguration)
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
> | DomainServices | 예 |예 |
> | DomainServices/oucontainer | 아니요 |아니요 |
> | DomainServices/ReplicaSets | 예 |예 |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | supportProviders | 아니요 |아니요 |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | 아니요 |아니요 |
> | addsservices | 아니요 |아니요 |
> | agents | 아니요 |아니요 |
> | anonymousapiusers | 아니요 |아니요 |
> | 구성 | 아니요 |아니요 |
> | logs | 아니요 |아니요 |
> | reports | 아니요 |아니요 |
> | servicehealthmetrics | 아니요 |아니요 |
> | 서비스 | 아니요 |아니요 |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 구성 | 아니요 |아니요 |
> | generateRecommendations | 아니요 |아니요 |
> | 메타데이터 | 아니요 |아니요 |
> | 권장 사항 | 아니요 |아니요 |
> | suppressions | 아니요 |아니요 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | actionRules | 예 |예 |
> | 경고 | 아니요 |아니요 |
> | alertsList | 아니요 |아니요 |
> | alertsMetaData | 아니요 |아니요 |
> | alertsSummary | 아니요 |아니요 |
> | alertsSummaryList | 아니요 |아니요 |
> | 있으신가요 | 아니요 |아니요 |
> | smartDetectorAlertRules | 예 |예 |
> | smartDetectorRuntimeEnvironments | 아니요 |아니요 |
> | smartGroups | 아니요 |아니요 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 서버 | 예 |예 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | reportFeedback | 아니요 |아니요 |
> | 서비스 | 예 |예 |
> | validateServiceName | 아니요 |아니요 |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | configurationStores | 예 |예 |
> | configurationStores/eventGridFilters | 아니요 |아니요 |

## <a name="microsoftappplatform"></a>Microsoft AppPlatform

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 봄 | 예 |예 |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | attestationProviders | 아니요 |아니요 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | classicAdministrators | 아니요 |아니요 |
> | dataAliases | 아니요 |아니요 |
> | denyAssignments | 아니요 |아니요 |
> | elevateAccess | 아니요 |아니요 |
> | locks | 아니요 |아니요 |
> | 사용 권한 | 아니요 |아니요 |
> | policyAssignments | 아니요 |아니요 |
> | policyDefinitions | 아니요 |아니요 |
> | policySetDefinitions | 아니요 |아니요 |
> | providerOperations | 아니요 |아니요 |
> | roleAssignments | 아니요 |아니요 |
> | roleDefinitions | 아니요 |아니요 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | automationAccounts | 예 |예 |
> | automationAccounts/configurations | 예 |예 |
> | automationAccounts/jobs | 아니요 |아니요 |
> | automationAccounts/runbooks | 예 |예 |
> | automationAccounts/softwareUpdateConfigurations | 아니요 |아니요 |
> | automationAccounts/webhooks | 아니요 |아니요 |

## <a name="microsoftazconfig"></a>Azconfig

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | configurationStores | 예 |예 |
> | configurationStores/eventGridFilters | 아니요 |아니요 |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | environments | 아니요 |아니요 |
> | environments/accounts | 아니요 |아니요 |
> | environments/accounts/namespaces | 아니요 |아니요 |
> | environments/accounts/namespaces/configurations | 아니요 |아니요 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | 예 |아니요 |
> | b2ctenants | 아니요 |아니요 |

## <a name="microsoftazuredata"></a>Microsoft AzureData

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | hybridDataManagers | 예 |예 |
> | postgresInstances | 예 |예 |
> | sqlBigDataClusters | 예 |예 |
> | sqlInstances | 예 |예 |
> | sqlServerRegistrations | 예 |예 |
> | sqlServerRegistrations/sqlServers | 아니요 |아니요 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | registrations | 예 |예 |
> | registrations/customerSubscriptions | 아니요 |아니요 |
> | registrations/products | 아니요 |아니요 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | batchAccounts | 예 |예 |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | billingAccounts | 아니요 |아니요 |
> | billingAccounts/규약 | 아니요 |아니요 |
> | billingAccounts/billingPermissions | 아니요 |아니요 |
> | billingAccounts/billingProfiles | 아니요 |아니요 |
> | billingAccounts/billingProfiles/billingPermissions | 아니요 |아니요 |
> | billingAccounts/billingProfiles/billingRoleAssignments | 아니요 |아니요 |
> | billingAccounts/billingProfiles/billingRoleDefinitions | 아니요 |아니요 |
> | billingAccounts/billingProfiles/billingSubscriptions | 아니요 |아니요 |
> | billingAccounts/billingProfiles/createBillingRoleAssignment | 아니요 |아니요 |
> | billingAccounts/billingProfiles/고객 | 아니요 |아니요 |
> | billingAccounts/billingProfiles/invoices | 아니요 |아니요 |
> | billingAccounts/billingProfiles/invoices/pricesheet | 아니요 |아니요 |
> | billingAccounts/billingProfiles/invoiceSections | 아니요 |아니요 |
> | billingAccounts/billingProfiles/invoiceSections/billingPermissions | 아니요 |아니요 |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleAssignments | 아니요 |아니요 |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleDefinitions | 아니요 |아니요 |
> | billingAccounts/billingProfiles/invoiceSections/billingSubscriptions | 아니요 |아니요 |
> | billingAccounts/billingProfiles/invoiceSections/createBillingRoleAssignment | 아니요 |아니요 |
> | billingAccounts/billingProfiles/invoiceSections/initiateTransfer | 아니요 |아니요 |
> | billingAccounts/billingProfiles/invoiceSections/products | 아니요 |아니요 |
> | billingAccounts/billingProfiles/invoiceSections/products/transfer | 아니요 |아니요 |
> | billingAccounts/billingProfiles/invoiceSections/products/updateAutoRenew | 아니요 |아니요 |
> | billingAccounts/billingProfiles/invoiceSections/트랜잭션 | 아니요 |아니요 |
> | billingAccounts/billingProfiles/invoiceSections/transfer | 아니요 |아니요 |
> | billingAccounts/BillingProfiles/patchOperations | 아니요 |아니요 |
> | billingAccounts/billingProfiles/paymentMethods | 아니요 |아니요 |
> | billingAccounts/billingProfiles/policies | 아니요 |아니요 |
> | billingAccounts/billingProfiles/pricesheet | 아니요 |아니요 |
> | billingAccounts/billingProfiles/pricesheetDownloadOperations | 아니요 |아니요 |
> | billingAccounts/billingProfiles/products | 아니요 |아니요 |
> | billingAccounts/billingProfiles/transactions | 아니요 |아니요 |
> | billingAccounts/billingRoleAssignments | 아니요 |아니요 |
> | billingAccounts/billingRoleDefinitions | 아니요 |아니요 |
> | billingAccounts/billingSubscriptions | 아니요 |아니요 |
> | billingAccounts/createBillingRoleAssignment | 아니요 |아니요 |
> | billingAccounts/createInvoiceSectionOperations | 아니요 |아니요 |
> | billingAccounts/고객 | 아니요 |아니요 |
> | billingAccounts/customers/billingSubscriptions | 아니요 |아니요 |
> | billingAccounts/customers/initiateTransfer | 아니요 |아니요 |
> | billingAccounts/고객/정책 | 아니요 |아니요 |
> | billingAccounts/고객/제품 | 아니요 |아니요 |
> | billingAccounts/고객/트랜잭션 | 아니요 |아니요 |
> | billingAccounts/고객/전송 | 아니요 |아니요 |
> | billingAccounts/departments | 아니요 |아니요 |
> | billingAccounts/enrollmentAccounts | 아니요 |아니요 |
> | billingAccounts/invoices | 아니요 |아니요 |
> | billingAccounts/invoiceSections | 아니요 |아니요 |
> | billingAccounts/invoiceSections/billingSubscriptionMoveOperations | 아니요 |아니요 |
> | billingAccounts/invoiceSections/billingSubscriptions | 아니요 |아니요 |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | 아니요 |아니요 |
> | billingAccounts/invoiceSections/상승 | 아니요 |아니요 |
> | billingAccounts/invoiceSections/initiateTransfer | 아니요 |아니요 |
> | billingAccounts/invoiceSections/patchOperations | 아니요 |아니요 |
> | billingAccounts/invoiceSections/제품 Moveoperations | 아니요 |아니요 |
> | billingAccounts/invoiceSections/products | 아니요 |아니요 |
> | billingAccounts/invoiceSections/제품/이전 | 아니요 |아니요 |
> | billingAccounts/invoiceSections/products/updateAutoRenew | 아니요 |아니요 |
> | billingAccounts/invoiceSections/트랜잭션 | 아니요 |아니요 |
> | billingAccounts/invoiceSections/transfers | 아니요 |아니요 |
> | billingAccounts/lineOfCredit | 아니요 |아니요 |
> | billingAccounts/patchOperations | 아니요 |아니요 |
> | billingAccounts/paymentMethods | 아니요 |아니요 |
> | billingAccounts/products | 아니요 |아니요 |
> | billingAccounts/transactions | 아니요 |아니요 |
> | billingPeriods | 아니요 |아니요 |
> | BillingPermissions | 아니요 |아니요 |
> | billingProperty | 아니요 |아니요 |
> | BillingRoleAssignments | 아니요 |아니요 |
> | BillingRoleDefinitions | 아니요 |아니요 |
> | CreateBillingRoleAssignment | 아니요 |아니요 |
> | departments | 아니요 |아니요 |
> | enrollmentAccounts | 아니요 |아니요 |
> | invoices | 아니요 |아니요 |
> | transfers | 아니요 |아니요 |
> | transfers/acceptTransfer | 아니요 |아니요 |
> | transfers/declineTransfer | 아니요 |아니요 |
> | transfers/operationStatus | 아니요 |아니요 |
> | 전송/validateTransfer | 아니요 |아니요 |
> | validateAddress | 아니요 |아니요 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | mapApis | 예 |예 |
> | updateCommunicationPreference | 아니요 |아니요 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | 예 |예 |
> | 감시자 | 예 |예 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | 아니요 |아니요 |
> | blueprintAssignments/assignmentOperations | 아니요 |아니요 |
> | blueprintAssignments/operations | 아니요 |아니요 |
> | blueprints | 아니요 |아니요 |
> | blueprints/artifacts | 아니요 |아니요 |
> | blueprints/versions | 아니요 |아니요 |
> | blueprints/versions/artifacts | 아니요 |아니요 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | botServices | 예 |예 |
> | botServices/channels | 아니요 |아니요 |
> | botServices/connections | 아니요 |아니요 |
> | languages | 아니요 |아니요 |
> | 템플릿 | 아니요 |아니요 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | Redis | 예 |예 |
> | RedisConfigDefinition | 아니요 |아니요 |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | appliedReservations | 아니요 |아니요 |
> | calculateExchange | 아니요 |아니요 |
> | calculatePrice | 아니요 |아니요 |
> | calculatePurchasePrice | 아니요 |아니요 |
> | catalogs | 아니요 |아니요 |
> | commercialReservationOrders | 아니요 |아니요 |
> | 교환 | 아니요 |아니요 |
> | placePurchaseOrder | 아니요 |아니요 |
> | reservationOrders | 아니요 |아니요 |
> | reservationOrders/calculateRefund | 아니요 |아니요 |
> | reservationOrders/merge | 아니요 |아니요 |
> | reservationOrders/reservations | 아니요 |아니요 |
> | reservationOrders/reservations/revisions | 아니요 |아니요 |
> | reservationOrders/return | 아니요 |아니요 |
> | reservationOrders/split | 아니요 |아니요 |
> | reservationOrders/swap | 아니요 |아니요 |
> | reservations | 아니요 |아니요 |
> | 리소스 | 아니요 |아니요 |
> | validateReservationOrder | 아니요 |아니요 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | 아니요 |아니요 |
> | CdnWebApplicationFirewallPolicies | 예 |예 |
> | edgenodes | 아니요 |아니요 |
> | profiles | 예 |예 |
> | profiles/endpoints | 예 |예 |
> | profiles/endpoints/customdomains | 아니요 |아니요 |
> | profiles/endpoints/origins | 아니요 |아니요 |
> | validateProbe | 아니요 |아니요 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | certificateOrders | 예 |예 |
> | certificateOrders/certificates | 아니요 |아니요 |
> | validateCertificateRegistrationInformation | 아니요 |아니요 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | capabilities | 아니요 |아니요 |
> | domainNames | 아니요 |아니요 |
> | domainNames/capabilities | 아니요 |아니요 |
> | domainNames/internalLoadBalancers | 아니요 |아니요 |
> | domainNames/serviceCertificates | 아니요 |아니요 |
> | domainNames/slots | 아니요 |아니요 |
> | domainNames/slots/roles | 아니요 |아니요 |
> | domainNames/슬롯/역할/metricDefinitions | 아니요 |아니요 |
> | domainNames/슬롯/역할/메트릭 | 아니요 |아니요 |
> | moveSubscriptionResources | 아니요 |아니요 |
> | operatingSystemFamilies | 아니요 |아니요 |
> | operatingSystems | 아니요 |아니요 |
> | quotas | 아니요 |아니요 |
> | resourceTypes | 아니요 |아니요 |
> | validateSubscriptionMoveAvailability | 아니요 |아니요 |
> | virtualMachines | 아니요 |아니요 |
> | virtualMachines/diagnosticSettings | 아니요 |아니요 |
> | virtualMachines/metricDefinitions | 아니요 |아니요 |
> | virtualMachines/metrics | 아니요 |아니요 |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | 아니요 |아니요 |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | capabilities | 아니요 |아니요 |
> | expressRouteCrossConnections | 아니요 |아니요 |
> | expressRouteCrossConnections/peerings | 아니요 |아니요 |
> | gatewaySupportedDevices | 아니요 |아니요 |
> | networkSecurityGroups | 아니요 |아니요 |
> | quotas | 아니요 |아니요 |
> | reservedIps | 아니요 |아니요 |
> | virtualNetworks | 아니요 |아니요 |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | 아니요 |아니요 |
> | virtualNetworks/virtualNetworkPeerings | 아니요 |아니요 |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | capabilities | 아니요 |아니요 |
> | 디스크 | 아니요 |아니요 |
> | 이미지 | 아니요 |아니요 |
> | osImages | 아니요 |아니요 |
> | osPlatformImages | 아니요 |아니요 |
> | publicImages | 아니요 |아니요 |
> | quotas | 아니요 |아니요 |
> | storageAccounts | 아니요 |아니요 |
> | storageAccounts/blobServices | 아니요 |아니요 |
> | storageAccounts/fileServices | 아니요 |아니요 |
> | storageAccounts/metricDefinitions | 아니요 |아니요 |
> | storageAccounts/메트릭 | 아니요 |아니요 |
> | storageAccounts/queueServices | 아니요 |아니요 |
> | storageAccounts/services | 아니요 |아니요 |
> | storageAccounts/services/diagnosticSettings | 아니요 |아니요 |
> | storageAccounts/services/metricDefinitions | 아니요 |아니요 |
> | storageAccounts/서비스/메트릭 | 아니요 |아니요 |
> | storageAccounts/tableServices | 아니요 |아니요 |
> | storageAccounts/vmImages | 아니요 |아니요 |
> | vmImages | 아니요 |아니요 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 |예 |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | RateCard | 아니요 |아니요 |
> | UsageAggregates | 아니요 |아니요 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | availabilitySets | 예 |예 |
> | Disk; 집합 | 예 |예 |
> | 디스크 | 예 |예 |
> | galleries | 예 |예 |
> | 갤러리/응용 프로그램 | 아니요 |아니요 |
> | 갤러리/응용 프로그램/버전 | 아니요 |아니요 |
> | galleries/images | 아니요 |아니요 |
> | galleries/images/versions | 아니요 |아니요 |
> | 호스트 그룹 | 예 |예 |
> | 호스트 그룹/호스트 | 예 |예 |
> | 이미지 | 예 |예 |
> | proximityPlacementGroups | 예 |예 |
> | restorePointCollections | 예 |예 |
> | restorePointCollections/restorePoints | 아니요 |아니요 |
> | sharedVMImages | 예 |예 |
> | sharedVMImages/versions | 아니요 |아니요 |
> | 스냅샷 | 예 |예 |
> | virtualMachines | 예 |예 |
> | virtualMachines/extensions | 예 |예 |
> | virtualMachines/metricDefinitions | 아니요 |아니요 |
> | virtualMachineScaleSets | 예 |예 |
> | virtualMachineScaleSets/extensions | 아니요 |아니요 |
> | virtualMachineScaleSets/networkInterfaces | 아니요 |아니요 |
> | virtualMachineScaleSets/publicIPAddresses | 아니요 |아니요 |
> | virtualMachineScaleSets/virtualMachines | 아니요 |아니요 |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | 아니요 |아니요 |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | 아니요 |아니요 |
> | 잔액 | 아니요 |아니요 |
> | 예산 | 아니요 |아니요 |
> | Charges | 아니요 |아니요 |
> | CostTags | 아니요 |아니요 |
> | credits | 아니요 |아니요 |
> | 이벤트 | 아니요 |아니요 |
> | 예측 | 아니요 |아니요 |
> | lots | 아니요 |아니요 |
> | Marketplace | 아니요 |아니요 |
> | Pricesheets | 아니요 |아니요 |
> | products | 아니요 |아니요 |
> | ReservationDetails | 아니요 |아니요 |
> | ReservationRecommendations | 아니요 |아니요 |
> | ReservationSummaries | 아니요 |아니요 |
> | ReservationTransactions | 아니요 |아니요 |
> | Tags | 아니요 |아니요 |
> | tenants | 아니요 |아니요 |
> | 용어 | 아니요 |아니요 |
> | UsageDetails | 아니요 |아니요 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | containerGroups | 예 |예 |
> | serviceAssociationLinks | 아니요 |아니요 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | registries | 예 |예 |
> | registries/builds | 아니요 |아니요 |
> | registries/builds/cancel | 아니요 |아니요 |
> | registries/builds/getLogLink | 아니요 |아니요 |
> | registries/buildTasks | 예 |예 |
> | registries/buildTasks/steps | 아니요 |아니요 |
> | registries/eventGridFilters | 아니요 |아니요 |
> | registries/getBuildSourceUploadUrl | 아니요 |아니요 |
> | registries/GetCredentials | 아니요 |아니요 |
> | registries/importImage | 아니요 |아니요 |
> | registries/queueBuild | 아니요 |아니요 |
> | registries/regenerateCredential | 아니요 |아니요 |
> | registries/regenerateCredentials | 아니요 |아니요 |
> | registries/replications | 예 |예 |
> | registries/runs | 아니요 |아니요 |
> | registries/runs/cancel | 아니요 |아니요 |
> | registries/scheduleRun | 아니요 |아니요 |
> | registries/tasks | 예 |예 |
> | registries/updatePolicies | 아니요 |아니요 |
> | registries/webhooks | 예 |예 |
> | registries/webhooks/getCallbackConfig | 아니요 |아니요 |
> | registries/webhooks/ping | 아니요 |아니요 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | containerServices | 예 |예 |
> | managedClusters | 예 |예 |
> | openShiftManagedClusters | 예 |예 |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 애플리케이션 | 예 |예 |
> | updateCommunicationPreference | 아니요 |아니요 |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 |예 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | , | 아니요 |아니요 |
> | BillingAccounts | 아니요 |아니요 |
> | 예산 | 아니요 |아니요 |
> | CloudConnectors | 아니요 |아니요 |
> | 커넥터 | 예 |예 |
> | Departments | 아니요 |아니요 |
> | 차원 | 아니요 |아니요 |
> | EnrollmentAccounts | 아니요 |아니요 |
> | 내보내기 | 아니요 |아니요 |
> | ExternalBillingAccounts | 아니요 |아니요 |
> | ExternalBillingAccounts/경고 | 아니요 |아니요 |
> | ExternalBillingAccounts/차원 | 아니요 |아니요 |
> | ExternalBillingAccounts/예측 | 아니요 |아니요 |
> | ExternalBillingAccounts/쿼리 | 아니요 |아니요 |
> | ExternalSubscriptions | 아니요 |아니요 |
> | ExternalSubscriptions/Alerts | 아니요 |아니요 |
> | ExternalSubscriptions/차원 | 아니요 |아니요 |
> | ExternalSubscriptions/예측 | 아니요 |아니요 |
> | ExternalSubscriptions/Query | 아니요 |아니요 |
> | 예측 | 아니요 |아니요 |
> | query | 아니요 |아니요 |
> | register | 아니요 |아니요 |
> | Reportconfigs | 아니요 |아니요 |
> | 보고서 | 아니요 |아니요 |
> | 설정 | 아니요 |아니요 |
> | showbackRules | 아니요 |아니요 |
> | 뷰 | 아니요 |아니요 |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 요청 | 아니요 |아니요 |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 사이의 | 아니요 |아니요 |
> | resourceProviders | 예 |예 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 작업 | 예 |예 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | 예 |예 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 작업 영역 | 예 |아니요 |
> | workspaces/virtualNetworkPeerings | 아니요 |아니요 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | catalogs | 예 |예 |
> | datacatalogs | 예 |예 |
> | datacatalogs/데이터 원본 | 아니요 |아니요 |
> | datacatalogs/데이터 원본/검색 | 아니요 |아니요 |
> | datacatalogs/데이터 원본/검색/데이터 집합 | 아니요 |아니요 |
> | datacatalogs/데이터 원본/검색/트리거 | 아니요 |아니요 |
> | datacatalogs/scantargets | 아니요 |아니요 |
> | datacatalogs/scantargets/데이터 집합 | 아니요 |아니요 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | dataFactories | 예 |아니요 |
> | dataFactories/diagnosticSettings | 아니요 |아니요 |
> | dataFactories/metricDefinitions | 아니요 |아니요 |
> | dataFactorySchema | 아니요 |아니요 |
> | factories | 예 |아니요 |
> | factories/integrationRuntimes | 아니요 |아니요 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 |예 |
> | accounts/dataLakeStoreAccounts | 아니요 |아니요 |
> | accounts/storageAccounts | 아니요 |아니요 |
> | accounts/storageAccounts/containers | 아니요 |아니요 |
> | 계정/transferAnalyticsUnits | 아니요 |아니요 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 |예 |
> | accounts/eventGridFilters | 아니요 |아니요 |
> | accounts/firewallRules | 아니요 |아니요 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 서비스 | 아니요 |아니요 |
> | services/projects | 아니요 |아니요 |

## <a name="microsoftdatashare"></a>DataShare

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 |예 |
> | 계정/공유 | 아니요 |아니요 |
> | 계정/공유/데이터 집합 | 아니요 |아니요 |
> | 계정/공유/초대 | 아니요 |아니요 |
> | 계정/공유/providersharesubscriptions | 아니요 |아니요 |
> | 계정/공유/synchronizationSettings | 아니요 |아니요 |
> | 계정/sharesubscriptions | 아니요 |아니요 |
> | 계정/sharesubscriptions/consumerSourceDataSets | 아니요 |아니요 |
> | 계정/sharesubscriptions/datasetmappings | 아니요 |아니요 |
> | 계정/sharesubscriptions/트리거 | 아니요 |아니요 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 서버 | 예 |예 |
> | servers/advisors | 아니요 |아니요 |
> | servers/queryTexts | 아니요 |아니요 |
> | servers/recoverableServers | 아니요 |아니요 |
> | servers/topQueryStatistics | 아니요 |아니요 |
> | servers/virtualNetworkRules | 아니요 |아니요 |
> | servers/waitStatistics | 아니요 |아니요 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 서버 | 예 |예 |
> | servers/advisors | 아니요 |아니요 |
> | servers/queryTexts | 아니요 |아니요 |
> | servers/recoverableServers | 아니요 |아니요 |
> | servers/topQueryStatistics | 아니요 |아니요 |
> | servers/virtualNetworkRules | 아니요 |아니요 |
> | servers/waitStatistics | 아니요 |아니요 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | serverGroups | 예 |예 |
> | 서버 | 예 |예 |
> | servers/advisors | 아니요 |아니요 |
> | servers/privateEndpointConnectionProxies | 아니요 |아니요 |
> | servers/privateEndpointConnections | 아니요 |아니요 |
> | servers/privateLinkResources | 아니요 |아니요 |
> | servers/queryTexts | 아니요 |아니요 |
> | servers/recoverableServers | 아니요 |아니요 |
> | servers/topQueryStatistics | 아니요 |아니요 |
> | servers/virtualNetworkRules | 아니요 |아니요 |
> | servers/waitStatistics | 아니요 |아니요 |
> | serversv2 | 예 |예 |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | artifactSources | 예 |예 |
> | rollouts | 예 |예 |
> | serviceTopologies | 예 |예 |
> | serviceTopologies/서비스 | 예 |예 |
> | serviceTopologies/서비스/serviceUnits | 예 |예 |
> | 단계 | 예 |예 |

## <a name="microsoftdesktopvirtualization"></a>Microsoft DesktopVirtualization

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | applicationgroups | 예 |예 |
> | applicationgroups/응용 프로그램 | 아니요 |아니요 |
> | applicationgroups/startmenuitems | 아니요 |아니요 |
> | hostpools | 예 |예 |
> | hostpools/sessionhosts | 아니요 |아니요 |
> | hostpools/sessionhosts/usersessions | 아니요 |아니요 |
> | hostpools/usersessions | 아니요 |아니요 |
> | 작업 영역 | 예 |예 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | elasticPools | 예 |예 |
> | ElasticPools/IotHubTenants | 예 |예 |
> | IotHubs | 예 |예 |
> | IotHubs/eventGridFilters | 아니요 |아니요 |
> | ProvisioningServices | 예 |예 |
> | usages | 아니요 |아니요 |

## <a name="microsoftdevops"></a>Microsoft DevOps

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 파이프라인 | 예 |예 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | controllers | 예 |예 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | labcenters | 예 |예 |
> | labs | 예 |예 |
> | 랩/환경 | 예 |예 |
> | labs/serviceRunners | 예 |예 |
> | labs/virtualMachines | 예 |예 |
> | schedules | 예 |예 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | 아니요 |아니요 |
> | databaseAccounts | 예 |예 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | domains | 예 |예 |
> | domains/domainOwnershipIdentifiers | 아니요 |아니요 |
> | generateSsoRequest | 아니요 |아니요 |
> | topLevelDomains | 아니요 |아니요 |
> | validateDomainRegistrationInformation | 아니요 |아니요 |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | lcsprojects | 아니요 |아니요 |
> | lcsprojects/clouddeployments | 아니요 |아니요 |
> | lcsprojects/connectors | 아니요 |아니요 |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 서비스 | 예 |예 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | domains | 예 |예 |
> | domains/topics | 아니요 |아니요 |
> | eventSubscriptions | 아니요 |아니요 |
> | extensionTopics | 아니요 |아니요 |
> | topics | 예 |예 |
> | topicTypes | 아니요 |아니요 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | clusters | 예 |예 |
> | 네임스페이스 | 예 |예 |
> | namespaces/authorizationrules | 아니요 |아니요 |
> | namespaces/disasterrecoveryconfigs | 아니요 |아니요 |
> | namespaces/eventhubs | 아니요 |아니요 |
> | namespaces/eventhubs/authorizationrules | 아니요 |아니요 |
> | namespaces/eventhubs/consumergroups | 아니요 |아니요 |
> | 네임 스페이스/네트워크 규칙 집합 | 아니요 |아니요 |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 기능 | 아니요 |아니요 |
> | providers | 아니요 |아니요 |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 등록(enroll) | 아니요 |아니요 |
> | galleryitems | 아니요 |아니요 |
> | generateartifactaccessuri | 아니요 |아니요 |
> | myareas | 아니요 |아니요 |
> | myareas/areas | 아니요 |아니요 |
> | myareas/areas/areas | 아니요 |아니요 |
> | myareas/areas/areas/galleryitems | 아니요 |아니요 |
> | myareas/areas/galleryitems | 아니요 |아니요 |
> | myareas/galleryitems | 아니요 |아니요 |
> | register | 아니요 |아니요 |
> | 리소스 | 아니요 |아니요 |
> | retrieveresourcesbyid | 아니요 |아니요 |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 |예 |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | autoManagedVmConfigurationProfiles | 예 |예 |
> | guestConfigurationAssignments | 아니요 |아니요 |
> | software | 아니요 |아니요 |
> | softwareUpdateProfile | 아니요 |아니요 |
> | 업데이트 | 아니요 |아니요 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | hanaInstances | 예 |예 |
> | sapMonitors | 예 |예 |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | dedicatedHSMs | 예 |예 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | clusters | 예 |예 |
> | clusters/applications | 아니요 |아니요 |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 서비스 | 예 |예 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 머신 | 예 |예 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | dataManagers | 예 |예 |

## <a name="microsofthydra"></a>Microsoft 히드라

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | components | 예 |예 |
> | networkScopes | 예 |예 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 작업 | 예 |예 |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 아니요 |아니요 |
> | diagnosticSettingsCategories | 아니요 |아니요 |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | appTemplates | 아니요 |아니요 |
> | IoTApps | 예 |예 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 그래프 | 예 |예 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | deletedVaults | 아니요 |아니요 |
> | hsmPools | 예 |예 |
> | vaults | 예 |예 |
> | vaults/accessPolicies | 아니요 |아니요 |
> | 자격 증명 모음/eventGridFilters | 아니요 |아니요 |
> | vaults/secrets | 아니요 |아니요 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | clusters | 예 |예 |
> | 클러스터/attacheddatabaseconfigurations | 아니요 |아니요 |
> | clusters/databases | 아니요 |아니요 |
> | clusters/databases/dataconnections | 아니요 |아니요 |
> | clusters/databases/eventhubconnections | 아니요 |아니요 |
> | 클러스터/sharedidentities | 아니요 |아니요 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | labaccounts | 예 |예 |
> | 사용자 | 아니요 |아니요 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | 예 |예 |
> | integrationAccounts | 예 |예 |
> | integrationServiceEnvironments | 예 |예 |
> | integrationServiceEnvironments/managedApis | 예 |예 |
> | isolatedEnvironments | 예 |예 |
> | workflows | 예 |예 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | 예 |예 |
> | webServices | 예 |예 |
> | 작업 영역 | 예 |예 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 작업 영역 | 예 |예 |
> | workspaces/computes | 아니요 |아니요 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | ID | 아니요 |아니요 |
> | userAssignedIdentities | 예 |예 |

## <a name="microsoftmanagedservices"></a>Microsoft ManagedServices

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | 아니요 |아니요 |
> | registrationAssignments | 아니요 |아니요 |
> | registrationDefinitions | 아니요 |아니요 |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | getEntities | 아니요 |아니요 |
> | managementGroups | 아니요 |아니요 |
> | 리소스 | 아니요 |아니요 |
> | startTenantBackfill | 아니요 |아니요 |
> | tenantBackfillStatus | 아니요 |아니요 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 예 |예 |
> | accounts/eventGridFilters | 아니요 |아니요 |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 제안 | 아니요 |아니요 |
> | offerTypes | 아니요 |아니요 |
> | offerTypes/publishers | 아니요 |아니요 |
> | offerTypes/publishers/offers | 아니요 |아니요 |
> | offerTypes/publishers/offers/plans | 아니요 |아니요 |
> | offerTypes/publishers/offers/plans/agreements | 아니요 |아니요 |
> | offerTypes/publishers/offers/plans/configs | 아니요 |아니요 |
> | offerTypes/publishers/offers/plans/configs/importImage | 아니요 |아니요 |
> | privategalleryitems | 아니요 |아니요 |
> | products | 아니요 |아니요 |
> | 게시자 | 아니요 |아니요 |
> | 게시자/제안 | 아니요 |아니요 |
> | 게시자/제품/개정 | 아니요 |아니요 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | classicDevServices | 예 |예 |
> | updateCommunicationPreference | 아니요 |아니요 |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | agreements | 아니요 |아니요 |
> | offertypes | 아니요 |아니요 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | mediaservices | 예 |예 |
> | mediaservices/accountFilters | 아니요 |아니요 |
> | mediaservices/assets | 아니요 |아니요 |
> | mediaservices/assets/assetFilters | 아니요 |아니요 |
> | mediaservices/contentKeyPolicies | 아니요 |아니요 |
> | mediaservices/eventGridFilters | 아니요 |아니요 |
> | mediaservices/liveEventOperations | 아니요 |아니요 |
> | mediaservices/liveEvents | 예 |예 |
> | mediaservices/liveEvents/liveOutputs | 아니요 |아니요 |
> | mediaservices/liveOutputOperations | 아니요 |아니요 |
> | mediaservices/streamingEndpointOperations | 아니요 |아니요 |
> | mediaservices/streamingEndpoints | 예 |예 |
> | mediaservices/streamingLocators | 아니요 |아니요 |
> | mediaservices/streamingPolicies | 아니요 |아니요 |
> | mediaservices/transforms | 아니요 |아니요 |
> | mediaservices/transforms/jobs | 아니요 |아니요 |

## <a name="microsoftmicroservices4spring"></a>Microservices4Spring

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | appClusters | 예 |예 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | 예 |예 |
> | migrateprojects | 예 |예 |
> | projects | 예 |예 |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | 예 |예 |
> | objectUnderstandingAccounts | 예 |예 |
> | remoteRenderingAccounts | 예 |예 |
> | spatialAnchorsAccounts | 예 |예 |
> | surfaceReconstructionAccounts | 예 |예 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | 예 |예 |
> | netAppAccounts/backupPolicies | 예 |예 |
> | netAppAccounts/capacityPools | 예 |예 |
> | netAppAccounts/capacityPools/볼륨 | 예 |예 |
> | netAppAccounts/capacityPools/볼륨/백업 | 아니요 |아니요 |
> | netAppAccounts/capacityPools/볼륨/mountTargets | 예 |예 |
> | netAppAccounts/capacityPools/볼륨/스냅숏 | 예 |예 |
> | netAppAccounts/자격 증명 모음 | 아니요 |아니요 |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | applicationGateways | 예 |예 |
> | applicationGatewayWebApplicationFirewallPolicies | 예 |예 |
> | applicationSecurityGroups | 예 |예 |
> | azureFirewallFqdnTags | 아니요 |아니요 |
> | azureFirewalls | 예 |예 |
> | bastionHosts | 예 |예 |
> | bgpServiceCommunities | 아니요 |아니요 |
> | 연결 | 예 |예 |
> | ddosCustomPolicies | 예 |예 |
> | ddosProtectionPlans | 예 |예 |
> | dnsOperationStatuses | 아니요 |아니요 |
> | dnszones | 예 |예 |
> | dnszones/A | 아니요 |아니요 |
> | dnszones/AAAA | 아니요 |아니요 |
> | dnszones/all | 아니요 |아니요 |
> | dnszones/CAA | 아니요 |아니요 |
> | dnszones/CNAME | 아니요 |아니요 |
> | dnszones/MX | 아니요 |아니요 |
> | dnszones/NS | 아니요 |아니요 |
> | dnszones/PTR | 아니요 |아니요 |
> | dnszones/recordsets | 아니요 |아니요 |
> | dnszones/SOA | 아니요 |아니요 |
> | dnszones/SRV | 아니요 |아니요 |
> | dnszones/TXT | 아니요 |아니요 |
> | expressRouteCircuits | 예 |예 |
> | expressRouteCrossConnections | 예 |예 |
> | expressRouteGateways | 예 |예 |
> | expressRoutePorts | 예 |예 |
> | expressRouteServiceProviders | 아니요 |아니요 |
> | firewallPolicies | 예 |예 |
> | frontdoors | 예, 제한 ( [아래 참고](#frontdoor)참조) |예 |
> | frontdoorWebApplicationFirewallManagedRuleSets | 예, 제한 ( [아래 참고](#frontdoor)참조) |아니요 |
> | frontdoorWebApplicationFirewallPolicies | 예, 제한 ( [아래 참고](#frontdoor)참조) |예 |
> | getDnsResourceReference | 아니요 |아니요 |
> | internalNotify | 아니요 |아니요 |
> | loadBalancers | 예 |아니요 |
> | localNetworkGateways | 예 |예 |
> | natGateways | 예 |예 |
> | networkIntentPolicies | 예 |예 |
> | networkInterfaces | 예 |예 |
> | networkProfiles | 예 |예 |
> | networkSecurityGroups | 예 |예 |
> | networkWatchers | 예 |아니요 |
> | networkWatchers/connectionMonitors | 예 |아니요 |
> | networkWatchers/lenses | 예 |아니요 |
> | networkWatchers/pingMeshes | 예 |아니요 |
> | p2sVpnGateways | 예 |예 |
> | privateDnsOperationStatuses | 아니요 |아니요 |
> | privateDnsZones | 예 |예 |
> | privateDnsZones/A | 아니요 |아니요 |
> | privateDnsZones/AAAA | 아니요 |아니요 |
> | privateDnsZones/모두 | 아니요 |아니요 |
> | privateDnsZones/CNAME | 아니요 |아니요 |
> | privateDnsZones/MX | 아니요 |아니요 |
> | privateDnsZones/PTR | 아니요 |아니요 |
> | privateDnsZones/SOA | 아니요 |아니요 |
> | privateDnsZones/SRV | 아니요 |아니요 |
> | privateDnsZones/TXT | 아니요 |아니요 |
> | privateDnsZones/virtualNetworkLinks | 예 |예 |
> | privateEndpoints | 예 |예 |
> | privateLinkServices | 예 |예 |
> | publicIPAddresses | 예 |예 |
> | publicIPPrefixes | 예 |예 |
> | routeFilters | 예 |예 |
> | routeTables | 예 |예 |
> | secureGateways | 예 |예 |
> | serviceEndpointPolicies | 예 |예 |
> | trafficManagerGeographicHierarchies | 아니요 |아니요 |
> | trafficmanagerprofiles | 예 |예 |
> | trafficmanagerprofiles/heatMaps | 아니요 |아니요 |
> | trafficManagerUserMetricsKeys | 아니요 |아니요 |
> | virtualHubs | 예 |예 |
> | virtualNetworkGateways | 예 |예 |
> | virtualNetworks | 예 |예 |
> | virtualNetworkTaps | 예 |예 |
> | virtualWans | 예 |예 |
> | vpnGateways | 예 |아니요 |
> | vpnSites | 예 |예 |
> | webApplicationFirewallPolicies | 예 |예 |

<a id="frontdoor" />

> [!NOTE]
> Azure Front 도어 서비스의 경우 리소스를 만들 때 태그를 적용할 수 있지만, 태그를 업데이트 하거나 추가 하는 기능은 현재 지원 되지 않습니다.


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 네임스페이스 | 예 |아니요 |
> | namespaces/notificationHubs | 예 |아니요 |

## <a name="microsoftobjectstore"></a>Microsoft ObjectStore

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | osNamespaces 스페이스 | 예 |예 |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | HyperVSites | 예 |예 |
> | ImportSites | 예 |예 |
> | ServerSites | 예 |예 |
> | VMwareSites | 예 |예 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | clusters | 예 |예 |
> | 디바이스 | 아니요 |아니요 |
> | linkTargets | 아니요 |아니요 |
> | storageInsightConfigs | 아니요 |아니요 |
> | 작업 영역 | 예 |예 |
> | workspaces/dataSources | 아니요 |아니요 |
> | workspaces/linkedServices | 아니요 |아니요 |
> | workspaces/query | 아니요 |아니요 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | managementassociations | 아니요 |아니요 |
> | managementconfigurations | 예 |예 |
> | 솔루션 | 예 |예 |
> | 보기 | 예 |예 |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | 아니요 |아니요 |
> | peerAsns | 아니요 |아니요 |
> | 피어링 | 예 |예 |
> | peeringServiceProviders | 아니요 |아니요 |
> | peeringServices | 예 |예 |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | policyEvents | 아니요 |아니요 |
> | policyStates | 아니요 |아니요 |
> | policyTrackedResources | 아니요 |아니요 |
> | remediations | 아니요 |아니요 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | consoles | 아니요 |아니요 |
> | dashboards | 예 |예 |
> | userSettings | 아니요 |아니요 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | workspaceCollections | 예 |예 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | capacities | 예 |예 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | 아니요 |아니요 |
> | vaults | 예 |예 |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 네임스페이스 | 예 |예 |
> | namespaces/authorizationrules | 아니요 |아니요 |
> | namespaces/hybridconnections | 아니요 |아니요 |
> | namespaces/hybridconnections/authorizationrules | 아니요 |아니요 |
> | namespaces/wcfrelays | 아니요 |아니요 |
> | namespaces/wcfrelays/authorizationrules | 아니요 |아니요 |

## <a name="microsoftremoteapp"></a>Microsoft RemoteApp

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 계정 | 아니요 |아니요 |
> | 수집이 | 예 |예 |
> | 컬렉션/응용 프로그램 | 아니요 |아니요 |
> | collections/securityprincipals | 아니요 |아니요 |
> | 템플릿 이미지 | 아니요 |아니요 |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 쿼리 | 예 |예 |
> | resourceChangeDetails | 아니요 |아니요 |
> | resourceChanges | 아니요 |아니요 |
> | 리소스 | 아니요 |아니요 |
> | 자원 기록 | 아니요 |아니요 |
> | subscriptionsStatus | 아니요 |아니요 |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | 아니요 |아니요 |
> | childAvailabilityStatuses | 아니요 |아니요 |
> | childResources | 아니요 |아니요 |
> | 이벤트 | 아니요 |아니요 |
> | impactedResources | 아니요 |아니요 |
> | 메타데이터 | 아니요 |아니요 |
> | 알림 | 아니요 |아니요 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 배포 | 예 |아니요 |
> | 배포/작업 | 아니요 |아니요 |
> | 링크 | 아니요 |아니요 |
> | notifyResourceJobs | 아니요 |아니요 |
> | providers | 아니요 |아니요 |
> | resourceGroups | 예 |아니요 |
> | 리소스 | 아니요 |아니요 |
> | 구독 | 아니요 |아니요 |
> | subscriptions/providers | 아니요 |아니요 |
> | subscriptions/resourceGroups | 아니요 |아니요 |
> | subscriptions/resourcegroups/resources | 아니요 |아니요 |
> | subscriptions/resources | 아니요 |아니요 |
> | subscriptions/tagnames | 아니요 |아니요 |
> | subscriptions/tagNames/tagValues | 아니요 |아니요 |
> | tenants | 아니요 |아니요 |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 애플리케이션 | 예 |예 |
> | saasresources | 아니요 |아니요 |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | jobcollections | 예 |예 |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | 아니요 |아니요 |
> | searchServices | 예 |예 |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | 아니요 |아니요 |
> | advancedThreatProtectionSettings | 아니요 |아니요 |
> | 경고 | 아니요 |아니요 |
> | allowedConnections | 아니요 |아니요 |
> | applicationWhitelistings | 아니요 |아니요 |
> | assessmentMetadata | 아니요 |아니요 |
> | 인사 | 아니요 |아니요 |
> | AutoProvisioningSettings | 아니요 |아니요 |
> | Compliances | 아니요 |아니요 |
> | dataCollectionAgents | 아니요 |아니요 |
> | deviceSecurityGroups | 아니요 |아니요 |
> | discoveredSecuritySolutions | 아니요 |아니요 |
> | externalSecuritySolutions | 아니요 |아니요 |
> | InformationProtectionPolicies | 아니요 |아니요 |
> | iotSecuritySolutions | 예 |예 |
> | IanalyticsModels Securitysolutions/ | 아니요 |아니요 |
> | iotSecuritySolutions/analyticsModels/aggregatedAlerts | 아니요 |아니요 |
> | iotSecuritySolutions/analyticsModels/aggregatedRecommendations | 아니요 |아니요 |
> | jitNetworkAccessPolicies | 아니요 |아니요 |
> | Networkdata.xml | 아니요 |아니요 |
> | playbookConfigurations | 예 |예 |
> | 정책 | 아니요 |아니요 |
> | pricings | 아니요 |아니요 |
> | regulatoryComplianceStandards | 아니요 |아니요 |
> | regulatoryComplianceStandards/regulatoryComplianceControls | 아니요 |아니요 |
> | regulatoryComplianceStandards/regulatoryComplianceControls/regulatoryComplianceAssessments | 아니요 |아니요 |
> | securityContacts | 아니요 |아니요 |
> | securitySolutions | 아니요 |아니요 |
> | securitySolutionsReferenceData | 아니요 |아니요 |
> | securityStatuses | 아니요 |아니요 |
> | securityStatusesSummaries | 아니요 |아니요 |
> | serverVulnerabilityAssessments | 아니요 |아니요 |
> | 설정 | 아니요 |아니요 |
> | 하위 평가 | 아니요 |아니요 |
> | 태스크 | 아니요 |아니요 |
> | topologies | 아니요 |아니요 |
> | workspaceSettings | 아니요 |아니요 |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 아니요 |아니요 |
> | diagnosticSettingsCategories | 아니요 |아니요 |

## <a name="microsoftsecurityinsights"></a>Microsoft SecurityInsights

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 집계 | 아니요 |아니요 |
> | alertRules | 아니요 |아니요 |
> | alertRuleTemplates | 아니요 |아니요 |
> | 내부의 | 아니요 |아니요 |
> | cases | 아니요 |아니요 |
> | dataConnectors | 아니요 |아니요 |
> | 엔터티 | 아니요 |아니요 |
> | entityQueries | 아니요 |아니요 |
> | officeConsents | 아니요 |아니요 |
> | 설정 | 아니요 |아니요 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 네임스페이스 | 예 |아니요 |
> | namespaces/authorizationrules | 아니요 |아니요 |
> | namespaces/disasterrecoveryconfigs | 아니요 |아니요 |
> | namespaces/eventgridfilters | 아니요 |아니요 |
> | 네임 스페이스/네트워크 규칙 집합 | 아니요 |아니요 |
> | namespaces/queues | 아니요 |아니요 |
> | namespaces/queues/authorizationrules | 아니요 |아니요 |
> | namespaces/topics | 아니요 |아니요 |
> | namespaces/topics/authorizationrules | 아니요 |아니요 |
> | namespaces/topics/subscriptions | 아니요 |아니요 |
> | namespaces/topics/subscriptions/rules | 아니요 |아니요 |
> | premiumMessagingRegions | 아니요 |아니요 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 애플리케이션 | 예 |예 |
> | clusters | 예 |예 |
> | clusters/applications | 아니요 |아니요 |
> | containerGroups | 예 |예 |
> | containerGroupSets | 예 |예 |
> | edgeclusters | 예 |예 |
> | edgeclusters/응용 프로그램 | 아니요 |아니요 |
> | 네트워크 | 예 |예 |
> | secretstores | 예 |예 |
> | secretstores/인증서 | 아니요 |아니요 |
> | secretstores/비밀 | 아니요 |아니요 |
> | 볼륨 | 예 |예 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 애플리케이션 | 예 |예 |
> | containerGroups | 예 |예 |
> | gateways | 예 |예 |
> | 네트워크 | 예 |예 |
> | 비밀 | 예 |예 |
> | 볼륨 | 예 |예 |

## <a name="microsoftservices"></a>Microsoft 서비스

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | 아니요 |아니요 |
> | providerRegistrations resourceTypeRegistrations | 아니요 |아니요 |
> | rollouts | 예 |예 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | SignalR | 예 |예 |
> | SignalR/eventGridFilters | 아니요 |아니요 |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | 예 |예 |

## <a name="microsoftsoftwareplan"></a>SoftwarePlan

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | 아니요 |아니요 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | 예 |예 |
> | 애플리케이션 | 예 |예 |
> | jitRequests | 예 |예 |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | managedInstances | 예 | 예 |
> | managedInstances/databases | 예 ( [아래 참고](#sqlnote)참조) | 예 |
> | managedInstances/databases/backupShortTermRetentionPolicies | 아니요 | 아니요 |
> | managedInstances/databases/schemas/tables/columns/sensitivityLabels | 아니요 | 아니요 |
> | managedInstances/databases/vulnerabilityAssessments | 아니요 | 아니요 |
> | managedInstances/databases/vulnerabilityAssessments/rules/baselines | 아니요 | 아니요 |
> | managedInstances/encryptionProtector | 아니요 | 아니요 |
> | managedInstances/keys | 아니요 | 아니요 |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | 아니요 | 아니요 |
> | managedInstances/vulnerabilityAssessments | 아니요 | 아니요 |
> | 서버 | 예 | 예 |
> | servers/administrators | 아니요 | 아니요 |
> | servers/communicationLinks | 아니요 | 아니요 |
> | servers/databases | 예 ( [아래 참고](#sqlnote)참조) | 예 |
> | servers/encryptionProtector | 아니요 | 아니요 |
> | servers/firewallRules | 아니요 | 아니요 |
> | servers/keys | 아니요 | 아니요 |
> | servers/restorableDroppedDatabases | 아니요 | 아니요 |
> | servers/serviceobjectives | 아니요 | 아니요 |
> | servers/tdeCertificates | 아니요 | 아니요 |
> | virtualClusters | 아니요 | 아니요 |

<a id="sqlnote" />

> [!NOTE]
> master 데이터베이스는 태그를 지원하지 않지만, Azure SQL Data Warehouse 데이터베이스를 포함한 다른 데이터베이스는 태그를 지원합니다. Azure SQL Data Warehouse 데이터베이스가 활성(일시 중지 아님) 상태여야 합니다.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | 예 |예 |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | 아니요 |아니요 |
> | SqlVirtualMachines | 예 |예 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | storageAccounts | 예 |예 |
> | storageAccounts/blobServices | 아니요 |아니요 |
> | storageAccounts/fileServices | 아니요 |아니요 |
> | storageAccounts/queueServices | 아니요 |아니요 |
> | storageAccounts/services | 아니요 |아니요 |
> | storageAccounts/services/metricDefinitions | 아니요 |아니요 |
> | storageAccounts/tableServices | 아니요 |아니요 |
> | usages | 아니요 |아니요 |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 캐시 | 예 |예 |
> | 캐시/storageTargets 가져오기 | 아니요 |아니요 |
> | usageModels | 아니요 |아니요 |

## <a name="microsoftstoragereplication"></a>StorageReplication

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | replicationGroups | 아니요 |아니요 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | 예 |예 |
> | storageSyncServices/registeredServers | 아니요 |아니요 |
> | storageSyncServices/syncGroups | 아니요 |아니요 |
> | storageSyncServices/syncGroups/cloudEndpoints | 아니요 |아니요 |
> | storageSyncServices/syncGroups/serverEndpoints | 아니요 |아니요 |
> | storageSyncServices/workflows | 아니요 |아니요 |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | 예 |예 |
> | storageSyncServices/registeredServers | 아니요 |아니요 |
> | storageSyncServices/syncGroups | 아니요 |아니요 |
> | storageSyncServices/syncGroups/cloudEndpoints | 아니요 |아니요 |
> | storageSyncServices/syncGroups/serverEndpoints | 아니요 |아니요 |
> | storageSyncServices/workflows | 아니요 |아니요 |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | 예 |예 |
> | storageSyncServices/registeredServers | 아니요 |아니요 |
> | storageSyncServices/syncGroups | 아니요 |아니요 |
> | storageSyncServices/syncGroups/cloudEndpoints | 아니요 |아니요 |
> | storageSyncServices/syncGroups/serverEndpoints | 아니요 |아니요 |
> | storageSyncServices/workflows | 아니요 |아니요 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | managers | 예 |예 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | streamingjobs | 예(아래 참고를 참조) |예 |

> [!NOTE]
> streamingjobs를 실행 중일 때 태그를 추가할 수 없습니다. 태그를 추가하려면 리소스를 중지합니다.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | 취소 | 아니요 |아니요 |
> | CreateSubscription | 아니요 |아니요 |
> | 사용 | 아니요 |아니요 |
> | 이름 바꾸기 | 아니요 |아니요 |
> | SubscriptionDefinitions | 아니요 |아니요 |
> | SubscriptionOperations | 아니요 |아니요 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | environments | 예 |아니요 |
> | environments/accessPolicies | 아니요 |아니요 |
> | environments/eventsources | 예 |아니요 |
> | environments/referenceDataSets | 예 |아니요 |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | 예 |예 |
> | dedicatedCloudServices | 예 |예 |
> | virtualMachines | 예 |예 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | 아니요 |아니요 |
> | apiManagementAccounts/apiAcls | 아니요 |아니요 |
> | apiManagementAccounts/apis | 아니요 |아니요 |
> | apiManagementAccounts/apis/apiAcls | 아니요 |아니요 |
> | apiManagementAccounts/apis/connectionAcls | 아니요 |아니요 |
> | apiManagementAccounts/apis/connections | 아니요 |아니요 |
> | apiManagementAccounts/apis/connections/connectionAcls | 아니요 |아니요 |
> | apiManagementAccounts/apis/localizedDefinitions | 아니요 |아니요 |
> | apiManagementAccounts/connectionAcls | 아니요 |아니요 |
> | apiManagementAccounts/connections | 아니요 |아니요 |
> | billingMeters | 아니요 |아니요 |
> | 인증서 | 예 |예 |
> | connectionGateways | 예 |예 |
> | 연결 | 예 |예 |
> | customApis | 예 |예 |
> | deletedSites | 아니요 |아니요 |
> | functions | 아니요 |아니요 |
> | hostingEnvironments | 예 |예 |
> | hostingEnvironments/multiRolePools | 아니요 |아니요 |
> | hostingEnvironments/workerPools | 아니요 |아니요 |
> | publishingUsers | 아니요 |아니요 |
> | 권장 사항 | 아니요 |아니요 |
> | resourceHealthMetadata | 아니요 |아니요 |
> | runtimes | 아니요 |아니요 |
> | serverFarms | 예 |예 |
> | serverFarms/eventGridFilters | 아니요 |아니요 |
> | 사이트 | 예 |예 |
> | 사이트/구성  | 아니요 | 아니요 |
> | sites/eventGridFilters | 아니요 |아니요 |
> | sites/hostNameBindings | 아니요 |아니요 |
> | 사이트/네트워크 구성 | 아니요 |아니요 |
> | sites/premieraddons | 예 |예 |
> | sites/slots | 예 |예 |
> | 사이트/슬롯/eventGridFilters 필터 | 아니요 |아니요 |
> | sites/slots/hostNameBindings | 아니요 |아니요 |
> | 사이트/슬롯/네트워크 구성 | 아니요 |아니요 |
> | sourceControls | 아니요 |아니요 |
> | validate | 아니요 |아니요 |
> | verifyHostingEnvironmentVnet | 아니요 |아니요 |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 아니요 |아니요 |
> | diagnosticSettingsCategories | 아니요 |아니요 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | DeviceServices | 예 |예 |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 리소스 형식 | 태그 지원 | 비용 보고서의 태그 |
> | ------------- | ----------- | ----------- |
> | components | 아니요 |아니요 |
> | componentsSummary | 아니요 |아니요 |
> | monitorInstances | 아니요 |아니요 |
> | monitorInstancesSummary | 아니요 |아니요 |
> | monitors | 아니요 |아니요 |
> | notificationSettings | 아니요 |아니요 |

## <a name="next-steps"></a>다음 단계

리소스에 태그를 적용하는 방법을 알아보려면 [태그를 사용하여 Azure 리소스 구성](resource-group-using-tags.md)을 참조하세요.
