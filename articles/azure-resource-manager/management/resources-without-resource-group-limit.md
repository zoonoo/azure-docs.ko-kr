---
title: 800카운트 제한이 없는 리소스
description: 리소스 그룹에 800개 이상의 인스턴스를 가질 수 있는 Azure 리소스 형식을 나열합니다.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 8644bec1a68acebff18cf83d17acb014784dc964
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804778"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>리소스 그룹당 인스턴스 800개에 국한되지 않는 리소스

기본적으로 각 리소스 그룹에 리소스 유형의 인스턴스를 최대 800개까지 배포할 수 있습니다. 그러나 일부 리소스 유형은 800인스턴스 제한에서 제외됩니다. 이 문서에서는 리소스 그룹에 800개 이상의 인스턴스를 가질 수 있는 Azure 리소스 형식을 나열합니다. 다른 모든 리소스 유형은 800개의 인스턴스로 제한됩니다.

일부 리소스 유형의 경우 800개의 인스턴스 제한을 제거하려면 지원팀에 문의해야 합니다. 이러한 리소스 유형은 이 문서에서 설명합니다.


## <a name="microsoftautomation"></a>Microsoft.Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

* registrations
* registrations/customerSubscriptions
* registrations/products

## <a name="microsoftbotservice"></a>Microsoft.BotService

* botServices - 기본적으로 800개의 인스턴스로 제한됩니다. 지원에 문의하여 제한을 늘릴 수 있습니다.

## <a name="microsoftcompute"></a>Microsoft.Compute

* disks
* galleries
* galleries/images
* galleries/images/versions
* images
* 스냅샷
* virtualMachines

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

* containerGroups

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

* registries/buildTasks
* 레지스트리/빌드 작업/목록소스리포지토리속성
* registries/buildTasks/steps
* 레지스트리/빌드 작업/단계/목록빌드 인수
* registries/eventGridFilters
* registries/replications
* registries/tasks
* registries/webhooks

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

* servers

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* servers

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

* 서버 그룹
* servers
* 서버v2
* 단일 서버

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

* 일정 - 기본적으로 800개의 인스턴스로 제한됩니다. 지원에 문의하여 제한을 늘릴 수 있습니다.

## <a name="microsoftenterpriseknowledgegraph"></a>마이크로소프트.엔터프라이즈 지식그래프

* services

## <a name="microsofteventhub"></a>Microsoft.EventHub

* clusters
* 네임스페이스

## <a name="microsoftexperimentation"></a>마이크로소프트.실험

* 실험워크스페이스

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

* 자동 관리Vm구성 프로필
* 구성 프로필할당
* guestConfigurationAssignments
* software
* 소프트웨어업데이트프로필
* 소프트웨어 업데이트

## <a name="microsoftinsights"></a>microsoft.insights

* metricalerts

## <a name="microsoftlogic"></a>Microsoft.Logic

* integrationAccounts
* workflows

## <a name="microsoftnetapp"></a>Microsoft.NetApp

* 넷앱 어카운트
* 넷앱계정/용량풀
* netApp계정/용량풀/볼륨
* netApp계정/용량풀/볼륨/마운트대상
* netApp계정/용량풀/볼륨/스냅샷

## <a name="microsoftnetwork"></a>Microsoft.Network

* 응용 프로그램게이트웨이웹응용방화벽정책
* applicationSecurityGroups
* 요새호스트
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
* 프라이빗DnsZones
* 프라이빗Dns존/A
* 프라이빗들존/AAAA
* 프라이빗Dns존/CNAME
* 프라이빗센스존/MX
* 프라이빗Dns존/PTR
* 프라이빗DnsZones/SOA
* 프라이빗DnsZones/SRV
* 프라이빗디존/TXT
* 프라이빗DnsZones/모든
* 프라이빗DnsZones/가상네트워크링크
* 프라이빗 엔드포인트
* privateLinkServices
* publicIP주소 - 기본적으로 800개의 인스턴스로 제한됩니다. 지원에 문의하여 제한을 늘릴 수 있습니다.
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

* 작업 공간컬렉션 - 기본적으로 800개의 인스턴스로 제한됩니다. 지원에 문의하여 제한을 늘릴 수 있습니다.

## <a name="microsoftrelay"></a>Microsoft.Relay

* 네임스페이스

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

* jobcollections

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

* 네임스페이스

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

할당량 및 제한의 전체 목록은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건을](azure-subscription-service-limits.md)참조하십시오.
