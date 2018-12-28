---
title: Azure 리소스 종류별 이동 작업 지원 | Microsoft Docs
description: 새 리소스 그룹이나 구독으로 이동할 수 있는 Azure 리소스 종류를 소개합니다.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/28/2018
ms.author: tomfitz
ms.openlocfilehash: 6f1869b83f46f97d0c54eb874a8879521a43b1e2
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53387066"
---
# <a name="move-operation-support-for-resources"></a>리소스에 대한 이동 작업 지원

이 문서에서는 Azure 리소스 종류가 이동 작업을 지원하는지 여부에 대한 정보를 제공합니다. 이동 작업을 지원하는 리소스 종류라도 해당 리소스를 이동할 수 없는 조건이 있을 수 있습니다. 이동 작업에 영향을 주는 조건에 대한 자세한 내용은 [새 리소스 그룹 또는 구독으로 리소스 이동](resource-group-move-resources.md)을 참조하세요.

## <a name="find-resource-provider-and-resource-type"></a>리소스 공급자 및 리소스 종류 찾기

리소스를 이동할 수 있는지 확인하려면 해당 리소스 공급자 및 리소스 종류를 찾아야 합니다.

PowerShell의 경우 다음을 사용합니다.

```azurepowershell-interactive
Get-AzureRmResource -ResourceGroupName demogroup | Select Name, ResourceType | Format-table
```

Azure CLI의 경우 

```azurecli-interactive
az resource list -g demogroup --query '[].{name:name, resourceType:type}' --output table
```

리소스 종류는 `<resource-provider>/<resource-type-name>` 형식으로 반환됩니다. 따라서 `Microsoft.OperationalInsights/workspaces` 값의 경우 리소스 공급자는 **Microsoft.OperationalInsights**이고 리소스 종류 이름은 **workspaces**입니다.

리소스 공급자 및 리소스 종류를 찾은 후에는 이 문서의 표를 참조하여 해당 리소스 종류가 이동 작업을 지원하는지 여부를 확인하세요.

## <a name="microsoftaad"></a>Microsoft.AAD

| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | --------------- | ----------- |
| domainservices | 아니요 | 아니요 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| servers | yes | yes |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | --------------- | ----------- |
| 서비스 | yes | yes |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | --------------- | ----------- |
| policyassignments | 아니요 | 아니요 |

## <a name="microsoftautomation"></a>Microsoft.Automation
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| automationaccounts | yes | yes |
| automationaccounts/configurations | yes | yes |
| automationaccounts/runbooks | yes | yes |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| b2cdirectories | yes | yes |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| registrations | yes | yes |

## <a name="microsoftbackup"></a>Microsoft.Backup
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| backupvault | 아니요 | 아니요 |

## <a name="microsoftbatch"></a>Microsoft.Batch
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| batchaccounts | yes | yes |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| mapapis | 아니요 | 아니요 |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| biztalk | yes | yes |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| blueprintassignments | 아니요 | 아니요 |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| botservices | yes | yes |

## <a name="microsoftcache"></a>Microsoft.Cache
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| redis | yes | yes |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| 프로필 | yes | yes |
| profiles/endpoints | yes | yes |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| certificateorders | yes | yes |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| domainnames | yes | 아니요 |
| virtualmachines | yes | 아니요 |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| networksecuritygroups | 아니요 | 아니요 |
| reservedips | 아니요 | 아니요 |
| virtualnetworks | 아니요 | 아니요 |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| storageaccounts | yes | 아니요 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| 계정 | yes | yes |

## <a name="microsoftcompute"></a>Microsoft.Compute
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| availabilitysets | yes | yes |
| disks | yes | yes |
| galleries | 아니요 | 아니요 |
| galleries/images | 아니요 | 아니요 |
| galleries/images/versions | 아니요 | 아니요 |
| images | yes | yes |
| restorepointcollections | 아니요 | 아니요 |
| sharedvmimages | 아니요 | 아니요 |
| sharedvmimages/versions | 아니요 | 아니요 |
| snapshots | yes | yes |
| virtualmachines | yes | yes |
| virtualmachines/extensions | yes | yes |
| virtualmachinescalesets | yes | yes |

## <a name="microsoftcontainer"></a>Microsoft.Container
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| containergroups | 아니요 | 아니요 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| containergroups | 아니요 | 아니요 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| registries | yes | yes |
| registries/buildtasks | yes | yes |
| registries/replications | 아니요 | 아니요 |
| registries/tasks | yes | yes |
| registries/webhooks | yes | yes |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| containerservices | 아니요 | 아니요 |
| managedclusters | 아니요 | 아니요 |
| openshiftmanagedclusters | 아니요 | 아니요 |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| 애플리케이션 | yes | yes |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| 커넥터 | yes | yes |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| hubs | yes | yes |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| jobs | 아니요 | 아니요 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| databoxedgedevices | 아니요 | 아니요 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| workspaces | 아니요 | 아니요 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| catalogs | yes | yes |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| datafactories | yes | yes |
| factories | yes | yes |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| datalakeaccounts | 아니요 | 아니요 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| 계정 | yes | yes |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| 계정 | yes | yes |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| services | 아니요 | 아니요 |
| services/projects | 아니요 | 아니요 |
| slots | 아니요 | 아니요 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| servers | 아니요 | 아니요 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| servers | yes | yes |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| servergroups | 아니요 | 아니요 |
| servers | yes | yes |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| artifactsources | 아니요 | 아니요 |
| rollouts | 아니요 | 아니요 |
| servicetopologies | 아니요 | 아니요 |
| servicetopologies/services | 아니요 | 아니요 |
| servicetopologies/services/serviceunits | 아니요 | 아니요 |

## <a name="microsoftdevices"></a>Microsoft.Devices
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| iothubs | yes | yes |
| provisioningservices | yes | yes |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| labcenters | 아니요 | 아니요 |
| labs | yes | 아니요 |
| labs/servicerunners | yes | yes |
| labs/virtualmachines | yes | 아니요 |
| schedules | 아니요 | 아니요 |

## <a name="microsoftdns"></a>microsoft.dns
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| dnszones | 아니요 | 아니요 |
| dnszones/a | 아니요 | 아니요 |
| dnszones/aaaa | 아니요 | 아니요 |
| dnszones/cname | 아니요 | 아니요 |
| dnszones/mx | 아니요 | 아니요 |
| dnszones/ptr | 아니요 | 아니요 |
| dnszones/srv | 아니요 | 아니요 |
| dnszones/txt | 아니요 | 아니요 |
| trafficmanagerprofiles | 아니요 | 아니요 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| databaseaccounts | yes | yes |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| domains | yes | yes |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| topics | yes | yes |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| clusters | yes | yes |
| namespaces | yes | yes |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| hanainstances | yes | yes |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| dedicatedhsms | 아니요 | 아니요 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| clusters | yes | yes |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| jobs | yes | yes |

## <a name="microsoftinsights"></a>microsoft.insights
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| actiongroups | yes | yes |
| activitylogalerts | 아니요 | 아니요 |
| alertrules | yes | yes |
| autoscalesettings | yes | yes |
| components | yes | yes |
| metricalerts | 아니요 | 아니요 |
| scheduledqueryrules | yes | yes |
| webtests | yes | yes |
| workbooks | yes | yes |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| iotapps | yes | yes |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| vaults | yes | yes |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| labaccounts | yes | yes |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| 계정 | yes | yes |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| 계정 | yes | yes |

## <a name="microsoftlogic"></a>Microsoft.Logic
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| integrationaccounts | yes | yes |
| workflows | yes | yes |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| commitmentplans | yes | yes |
| webservices | yes | 아니요 |
| workspaces | yes | yes |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| operationalizationclusters | yes | yes |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| 계정 | yes | yes |
| accounts/workspaces | yes | yes |
| accounts/workspaces/projects | yes | yes |
| teamaccounts | yes | yes |
| teamaccounts/workspaces | yes | yes |
| teamaccounts/workspaces/projects | yes | yes |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| 계정 | yes | yes |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| workspaces | yes | yes |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| userassignedidentities | yes | yes |

## <a name="microsoftmaps"></a>Microsoft.Maps
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| 계정 | yes | yes |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| classicdevservices | 아니요 | 아니요 |

## <a name="microsoftmedia"></a>Microsoft.Media
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| mediaservices | yes | yes |
| mediaservices/liveevents | yes | yes |
| mediaservices/streamingendpoints | yes | yes |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| projects | 아니요 | 아니요 |

## <a name="microsoftnetwork"></a>Microsoft.Network
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| applicationgateways | 아니요 | 아니요 |
| applicationsecuritygroups | yes | yes |
| azurefirewalls | yes | yes |
| connections | yes | yes |
| ddosprotectionplans | 아니요 | 아니요 |
| dnszones | yes | yes |
| expressroutecircuits | 아니요 | 아니요 |
| expressroutecrossconnections | 아니요 | 아니요 |
| expressroutegateways | 아니요 | 아니요 |
| expressrouteports | 아니요 | 아니요 |
| frontdoors | yes | yes |
| frontdoorwebapplicationfirewallpolicies | yes | yes |
| interfaceendpoints | 아니요 | 아니요 |
| loadbalancers | yes | yes |
| localnetworkgateways | yes | yes |
| networkintentpolicies | yes | yes |
| networkinterfaces | yes | yes |
| networkprofiles | 아니요 | 아니요 |
| networksecuritygroups | yes | yes |
| networkwatchers | yes | yes |
| networkwatchers/connectionmonitors | yes | yes |
| networkwatchers/lenses | yes | yes |
| networkwatchers/pingmeshes | yes | yes |
| publicipaddresses | yes | yes |
| publicipprefixes | yes | yes |
| routefilters | 아니요 | 아니요 |
| routetables | yes | yes |
| serviceendpointpolicies | yes | yes |
| trafficmanagerprofiles | yes | yes |
| virtualhubs | yes | yes |
| virtualnetworkgateways | yes | yes |
| virtualnetworks | yes | yes |
| virtualnetworktaps | 아니요 | 아니요 |
| virtualwans | yes | yes |
| vpngateways | yes | yes |
| vpnsites | yes | yes |
| webapplicationfirewallpolicies | yes | yes |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| namespaces | yes | yes |
| namespaces/notificationhubs | yes | yes |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| workspaces | yes | yes |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| managementconfigurations | yes | yes |
| solutions | yes | yes |
| 뷰 | yes | yes |

## <a name="microsoftportal"></a>Microsoft.Portal
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| dashboards | yes | yes |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| workspacecollections | yes | yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| capacities | yes | yes |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| vaults | yes | yes |

## <a name="microsoftrelay"></a>Microsoft.Relay
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| namespaces | yes | yes |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| 애플리케이션 | yes | 아니요 |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| flows | yes | yes |
| jobcollections | yes | yes |

## <a name="microsoftsearch"></a>Microsoft.Search
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| searchservices | yes | yes |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| namespaces | yes | yes |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| clusters | yes | yes |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| 애플리케이션 | yes | yes |
| networks | yes | yes |
| volumes | yes | yes |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| signalr | yes | yes |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| siterecoveryvault | 아니요 | 아니요 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| appliancedefinitions | 아니요 | 아니요 |
| appliances | 아니요 | 아니요 |
| applicationdefinitions | 아니요 | 아니요 |
| 애플리케이션 | 아니요 | 아니요 |

## <a name="microsoftsql"></a>Microsoft.Sql
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| managedinstances | yes | yes |
| managedinstances/databases | yes | yes |
| servers | yes | yes |
| servers/databases | yes | yes |
| servers/elasticpools | yes | yes |
| virtualclusters | yes | yes |

## <a name="microsoftstorage"></a>Microsoft.Storage
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| storageaccounts | yes | yes |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| storagesyncservices | yes | yes |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| managers | 아니요 | 아니요 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| streamingjobs | yes | yes |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| environments | yes | yes |
| environments/eventsources | yes | yes |
| environments/referencedatasets | yes | yes |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| 계정 | yes | yes |
| account/extension | yes | yes |
| account/project | yes | yes |

## <a name="microsoftweb"></a>Microsoft.Web
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| certificates | 아니요 | yes |
| classicmobileservices | 아니요 | 아니요 |
| connectiongateways | yes | yes |
| connections | yes | yes |
| customapis | yes | yes |
| hostingenvironments | 아니요 | 아니요 |
| serverfarms | yes | yes |
| sites | yes | yes |
| sites/premieraddons | yes | yes |
| sites/slots | yes | yes |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| deviceservices | yes | yes |


## <a name="third-party-services"></a>타사 서비스

타사 서비스는 현재 이동 작업을 지원하지 않습니다. 타사 리소스 공급자는 다음과 같습니다.

* 84codes.CloudAMQP
* AppDynamics.APM
* Aspera.Transfers
* Auth0.Cloud
* Citrix.Cloud
* Citrix.Services
* CloudSimple.PrivateCloudIaaS
* Cloudyn.Analytics
* Conexlink.MyCloudIT
* Crypteron.DataSecurity
* Dynatrace.DynatraceSaaS
* Dynatrace.Ruxit
* LiveArena.Broadcast
* Lombiq.DotNest
* Mailjet.Email
* Myget.PackageManagement
* NewRelic.APM
* nuubit.nextgencdn
* Paraleap.CloudMonix
* Pokitdok.Platform
* RavenHq.Db
* Raygun.CrashReporting
* RevAPM.MobileCDN
* Sendgrid.Email
* Sparkpost.Basic
* stackify.retrace
* SuccessBricks.ClearDB
* TrendMicro.DeepSecurity
* U2uconsult.TheIdentityHub


## <a name="next-steps"></a>다음 단계

* 리소스를 이동하는 명령은 [새 리소스 그룹 또는 구독으로 리소스 이동](resource-group-move-resources.md)을 참조하세요.
