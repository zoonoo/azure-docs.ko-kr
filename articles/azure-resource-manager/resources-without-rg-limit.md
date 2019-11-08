---
title: 800 수 제한이 없는 Azure 리소스
description: 리소스 그룹에 800 개 이상의 인스턴스를 포함할 수 있는 Azure 리소스 종류를 나열 합니다.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: tomfitz
ms.openlocfilehash: c08b80a841199ed2737f6fe8d7f2b76943c5b269
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795666"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>리소스 그룹당 800 인스턴스로 제한 되지 않는 리소스

기본적으로 각 리소스 그룹에 리소스 종류의 최대 800 개의 인스턴스를 배포할 수 있습니다. 그러나 일부 리소스 종류는 800 인스턴스 제한에서 제외 됩니다. 이 문서에서는 리소스 그룹에 800 개 이상의 인스턴스를 포함할 수 있는 Azure 리소스 종류를 나열 합니다. 다른 모든 리소스 유형은 800 인스턴스로 제한 됩니다.

일부 리소스 종류의 경우 지원 서비스에 문의 하 여 800 인스턴스 제한을 제거 해야 합니다. 이러한 리소스 종류는이 문서에 나와 있습니다.


## <a name="microsoftautomation"></a>Microsoft.Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

* registrations
* registrations/customerSubscriptions
* registrations/products

## <a name="microsoftbotservice"></a>Microsoft.BotService

* botServices-기본적으로 800 인스턴스로 제한 됩니다. 지원에 문의 하 여이 제한을 늘릴 수 있습니다.

## <a name="microsoftcompute"></a>Microsoft.Compute

* 디스크
* images
* 스냅샷
* virtualMachines

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

* containerGroups

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

* registries/buildTasks
* 레지스트리/buildTasks/listSourceRepositoryProperties
* registries/buildTasks/steps
* 레지스트리/buildTasks/단계/listBuildArguments
* registries/eventGridFilters
* registries/replications
* registries/tasks
* registries/webhooks

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

* servers

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* servers

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

* serverGroups
* servers
* serversv2

## <a name="microsoftenterpriseknowledgegraph"></a>EnterpriseKnowledgeGraph

* services

## <a name="microsofteventhub"></a>Microsoft.EventHub

* clusters
* namespaces

## <a name="microsoftexperimentation"></a>Microsoft. 실험

* experimentWorkspaces

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

* configurationprofil
* guestConfigurationAssignments
* software
* softwareUpdateProfile
* 업데이트

## <a name="microsoftlogic"></a>Microsoft.Logic

* integrationAccounts
* workflows

## <a name="microsoftnetapp"></a>Microsoft.NetApp

* netAppAccounts
* netAppAccounts/capacityPools
* netAppAccounts/capacityPools/볼륨
* netAppAccounts/capacityPools/볼륨/mountTargets
* netAppAccounts/capacityPools/볼륨/스냅숏

## <a name="microsoftnetwork"></a>Microsoft.Network

* applicationGatewayWebApplicationFirewallPolicies
* applicationSecurityGroups
* bastionHosts
* ddosProtectionPlans
* dnszones
* dnszones/A
* dnszones/AAAA
* dnszones/CAA
* dnszones/CNAME
* dnszones/MX
* dnszones/NS
* dnszones/PTR
* dnszones/SOA
* dnszones/SRV
* dnszones/TXT
* dnszones/all
* dnszones/recordsets
* networkIntentPolicies
* networkInterfaces
* privateDnsZones
* privateDnsZones/A
* privateDnsZones/AAAA
* privateDnsZones/CNAME
* privateDnsZones/MX
* privateDnsZones/PTR
* privateDnsZones/SOA
* privateDnsZones/SRV
* privateDnsZones/TXT
* privateDnsZones/모두
* privateDnsZones/virtualNetworkLinks
* privateEndpoints
* privateLinkServices
* publicIPAddresses-기본적으로 800 인스턴스로 제한 됩니다. 지원에 문의 하 여이 제한을 늘릴 수 있습니다.
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

* workspaceCollections-기본적으로 800 인스턴스로 제한 됩니다. 지원에 문의 하 여이 제한을 늘릴 수 있습니다.

## <a name="microsoftrelay"></a>Microsoft.Relay

* namespaces

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

* jobcollections

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

* namespaces

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

* 애플리케이션
* containerGroups
* gateways
* networks
* secrets
* volumes

## <a name="microsoftstorage"></a>Microsoft.Storage

* storageAccounts

## <a name="microsoftweb"></a>Microsoft.Web

* apiManagementAccounts/apis
* sites

## <a name="next-steps"></a>다음 단계

할당량 및 제한의 전체 목록은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)을 참조 하세요.
