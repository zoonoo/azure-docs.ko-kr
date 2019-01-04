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
ms.openlocfilehash: 253cfd7ddeb04a12a3609ab5e14a37365015a568
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790880"
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
| servers | 예 | 예 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | --------------- | ----------- |
| 서비스 | 예 | 예 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | --------------- | ----------- |
| policyassignments | 아니요 | 아니요 |

## <a name="microsoftautomation"></a>Microsoft.Automation
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| automationaccounts | 예 | 예 |
| automationaccounts/configurations | 예 | 예 |
| automationaccounts/runbooks | 예 | 예 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| b2cdirectories | 예 | 예 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| registrations | 예 | 예 |

## <a name="microsoftbackup"></a>Microsoft.Backup
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| backupvault | 아니요 | 아니요 |

## <a name="microsoftbatch"></a>Microsoft.Batch
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| batchaccounts | 예 | 예 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| mapapis | 아니요 | 아니요 |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| biztalk | 예 | 예 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| blueprintassignments | 아니요 | 아니요 |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| botservices | 예 | 예 |

## <a name="microsoftcache"></a>Microsoft.Cache
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| redis | 예 | 예 |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| 프로필 | 예 | 예 |
| profiles/endpoints | 예 | 예 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| certificateorders | 예 | 예 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| domainnames | 예 | 아니요 |
| virtualmachines | 예 | 아니요 |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| networksecuritygroups | 아니요 | 아니요 |
| reservedips | 아니요 | 아니요 |
| virtualnetworks | 아니요 | 아니요 |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| storageaccounts | 예 | 아니요 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| 계정 | 예 | 예 |

## <a name="microsoftcompute"></a>Microsoft.Compute
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| availabilitysets | 예 | 예 |
| disks | 예 | 예 |
| galleries | 아니요 | 아니요 |
| galleries/images | 아니요 | 아니요 |
| galleries/images/versions | 아니요 | 아니요 |
| images | 예 | 예 |
| restorepointcollections | 아니요 | 아니요 |
| sharedvmimages | 아니요 | 아니요 |
| sharedvmimages/versions | 아니요 | 아니요 |
| snapshots | 예 | 예 |
| virtualmachines | 예 | 예 |
| virtualmachines/extensions | 예 | 예 |
| virtualmachinescalesets | 예 | 예 |

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
| registries | 예 | 예 |
| registries/buildtasks | 예 | 예 |
| registries/replications | 아니요 | 아니요 |
| registries/tasks | 예 | 예 |
| registries/webhooks | 예 | 예 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| containerservices | 아니요 | 아니요 |
| managedclusters | 아니요 | 아니요 |
| openshiftmanagedclusters | 아니요 | 아니요 |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| 애플리케이션 | 예 | 예 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| 커넥터 | 예 | 예 |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| hubs | 예 | 예 |

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
| catalogs | 예 | 예 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| datafactories | 예 | 예 |
| factories | 예 | 예 |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| datalakeaccounts | 아니요 | 아니요 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| 계정 | 예 | 예 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| 계정 | 예 | 예 |

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
| servers | 예 | 예 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| servergroups | 아니요 | 아니요 |
| servers | 예 | 예 |

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
| iothubs | 예 | 예 |
| provisioningservices | 예 | 예 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| labcenters | 아니요 | 아니요 |
| labs | 예 | 아니요 |
| labs/servicerunners | 예 | 예 |
| labs/virtualmachines | 예 | 아니요 |
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
| databaseaccounts | 예 | 예 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| domains | 예 | 예 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| topics | 예 | 예 |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| clusters | 예 | 예 |
| namespaces | 예 | 예 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| hanainstances | 예 | 예 |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| dedicatedhsms | 아니요 | 아니요 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| clusters | 예 | 예 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| jobs | 예 | 예 |

## <a name="microsoftinsights"></a>microsoft.insights
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| actiongroups | 예 | 예 |
| activitylogalerts | 아니요 | 아니요 |
| alertrules | 예 | 예 |
| autoscalesettings | 예 | 예 |
| components | 예 | 예 |
| metricalerts | 아니요 | 아니요 |
| scheduledqueryrules | 예 | 예 |
| webtests | 예 | 예 |
| workbooks | 예 | 예 |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| iotapps | 예 | 예 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| vaults | 예 | 예 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| labaccounts | 예 | 예 |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| 계정 | 예 | 예 |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| 계정 | 예 | 예 |

## <a name="microsoftlogic"></a>Microsoft.Logic
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| integrationaccounts | 예 | 예 |
| workflows | 예 | 예 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| commitmentplans | 예 | 예 |
| webservices | 예 | 아니요 |
| workspaces | 예 | 예 |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| operationalizationclusters | 예 | 예 |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| 계정 | 예 | 예 |
| accounts/workspaces | 예 | 예 |
| accounts/workspaces/projects | 예 | 예 |
| teamaccounts | 예 | 예 |
| teamaccounts/workspaces | 예 | 예 |
| teamaccounts/workspaces/projects | 예 | 예 |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| 계정 | 예 | 예 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| workspaces | 예 | 예 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| userassignedidentities | 예 | 예 |

## <a name="microsoftmaps"></a>Microsoft.Maps
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| 계정 | 예 | 예 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| classicdevservices | 아니요 | 아니요 |

## <a name="microsoftmedia"></a>Microsoft.Media
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| mediaservices | 예 | 예 |
| mediaservices/liveevents | 예 | 예 |
| mediaservices/streamingendpoints | 예 | 예 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| projects | 아니요 | 아니요 |

## <a name="microsoftnetwork"></a>Microsoft.Network
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| applicationgateways | 아니요 | 아니요 |
| applicationsecuritygroups | 예 | 예 |
| azurefirewalls | 아니요 | 아니요 |
| connections | 예 | 예 |
| ddosprotectionplans | 아니요 | 아니요 |
| dnszones | 예 | 예 |
| expressroutecircuits | 아니요 | 아니요 |
| expressroutecrossconnections | 아니요 | 아니요 |
| expressroutegateways | 아니요 | 아니요 |
| expressrouteports | 아니요 | 아니요 |
| frontdoors | 예 | 예 |
| frontdoorwebapplicationfirewallpolicies | 예 | 예 |
| interfaceendpoints | 아니요 | 아니요 |
| loadbalancers | 예 | 예 |
| localnetworkgateways | 예 | 예 |
| networkintentpolicies | 예 | 예 |
| networkinterfaces | 예 | 예 |
| networkprofiles | 아니요 | 아니요 |
| networksecuritygroups | 예 | 예 |
| networkwatchers | 예 | 예 |
| networkwatchers/connectionmonitors | 예 | 예 |
| networkwatchers/lenses | 예 | 예 |
| networkwatchers/pingmeshes | 예 | 예 |
| publicipaddresses | 예 | 예 |
| publicipprefixes | 예 | 예 |
| routefilters | 아니요 | 아니요 |
| routetables | 예 | 예 |
| serviceendpointpolicies | 예 | 예 |
| trafficmanagerprofiles | 예 | 예 |
| virtualhubs | 예 | 예 |
| virtualnetworkgateways | 예 | 예 |
| virtualnetworks | 예 | 예 |
| virtualnetworktaps | 아니요 | 아니요 |
| virtualwans | 예 | 예 |
| vpngateways | 예 | 예 |
| vpnsites | 예 | 예 |
| webapplicationfirewallpolicies | 예 | 예 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| namespaces | 예 | 예 |
| namespaces/notificationhubs | 예 | 예 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| workspaces | 예 | 예 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| managementconfigurations | 예 | 예 |
| solutions | 예 | 예 |
| 뷰 | 예 | 예 |

## <a name="microsoftportal"></a>Microsoft.Portal
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| dashboards | 예 | 예 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| workspacecollections | 예 | 예 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| capacities | 예 | 예 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| vaults | 예 | 예 |

## <a name="microsoftrelay"></a>Microsoft.Relay
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| namespaces | 예 | 예 |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| 애플리케이션 | 예 | 아니요 |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| flows | 예 | 예 |
| jobcollections | 예 | 예 |

## <a name="microsoftsearch"></a>Microsoft.Search
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| searchservices | 예 | 예 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| namespaces | 예 | 예 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| clusters | 예 | 예 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| 애플리케이션 | 예 | 예 |
| networks | 예 | 예 |
| volumes | 예 | 예 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| signalr | 예 | 예 |

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
| managedinstances | 예 | 예 |
| managedinstances/databases | 예 | 예 |
| servers | 예 | 예 |
| servers/databases | 예 | 예 |
| servers/elasticpools | 예 | 예 |
| virtualclusters | 예 | 예 |

## <a name="microsoftstorage"></a>Microsoft.Storage
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| storageaccounts | 예 | 예 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| storagesyncservices | 예 | 예 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| managers | 아니요 | 아니요 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| streamingjobs | 예 | 예 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| environments | 예 | 예 |
| environments/eventsources | 예 | 예 |
| environments/referencedatasets | 예 | 예 |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| 계정 | 예 | 예 |
| account/extension | 예 | 예 |
| account/project | 예 | 예 |

## <a name="microsoftweb"></a>Microsoft.Web
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| certificates | 아니요 | 예 |
| classicmobileservices | 아니요 | 아니요 |
| connectiongateways | 예 | 예 |
| connections | 예 | 예 |
| customapis | 예 | 예 |
| hostingenvironments | 아니요 | 아니요 |
| serverfarms | 예 | 예 |
| sites | 예 | 예 |
| sites/premieraddons | 예 | 예 |
| sites/slots | 예 | 예 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| 리소스 종류 | 리소스 그룹 | 구독 |
| ------------- | -------------- | ------------ |
| deviceservices | 예 | 예 |


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
