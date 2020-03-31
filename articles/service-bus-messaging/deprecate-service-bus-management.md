---
title: Azure 메시징 서비스 - 서비스 관리자에서 리소스 관리자로
description: 이 문서에서는 더 이상 사용되지 & Azure 서비스 관리자 REST API를 리소스 관리자 REST API에 powerShell cmdlet& PowerShell cmdlet에 매핑합니다.
services: service-bus-messaging, event-hubs, event-grid
documentationcenter: na
author: spelluru
editor: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2020
ms.author: spelluru
ms.openlocfilehash: d263381667319b98a28ee6168e2de75c4041b58a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589910"
---
# <a name="deprecation-of-azure-service-manager-support-for-azure-service-bus-relay-and-event-hubs"></a>Azure 서비스 버스, 릴레이 및 이벤트 허브에 대한 Azure 서비스 관리자 지원 사용 중단

차세대 클라우드 인프라 스택인 리소스 관리자가 "클래식" Azure 서비스 관리 모델(클래식 배포 모델)을 완전히 대체하고 있습니다. 따라서 클래식 배포 모델 REST API 및 서비스 버스, 릴레이 및 이벤트 허브에 대한 지원은 2021년 11월 1일에 사용 중지됩니다. 이 사용 중단은 Microsoft [기술 커뮤니티 발표에서](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Deprecating-Service-Management-support-for-Azure-Service-Bus/ba-p/370909)처음 발표되었지만 최근에는 원래 발표 시점으로부터 2년 더 사용 중단 기간을 연장하기로 결정했습니다. 쉽게 식별할 수 있도록 `management.core.windows.net` 이러한 API는 URI에 있습니다. 더 이상 사용되지 않는 API 및 이제 사용해야 하는 Azure Resource Manager API 버전의 목록은 다음 표를 참조하십시오.

서비스 버스, 릴레이 및 이벤트 허브를 계속 사용하려면 2021년 10월 31일까지 리소스 관리자로 이동합니다. 이전 API를 사용하는 모든 고객은 리소스 그룹화, 태그, 간소화된 배포 및 관리 프로세스, 세분화된 액세스 등 Resource Manager의 추가 이점을 최대한 활용할 수 있도록 곧 전환할 것을 권장합니다. RBAC(역할 기반 액세스 제어)를 사용하여 제어합니다.

Azure 리소스 관리자 와 Azure 서비스 관리자에 대한 자세한 내용은 [TechNet 블로그](https://blogs.technet.microsoft.com/meamcs/2016/12/22/difference-between-azure-service-manager-and-azure-resource-manager/)를 참조하십시오.

Azure Service Bus, 릴레이 및 이벤트 허브에 대한 서비스 관리자 및 리소스 관리자 APIAPI에 대한 자세한 내용은 REST API 설명서를 참조하십시오.

- [Azure 서비스 버스](/rest/api/servicebus/)
- [Azure Event Hubs](/rest/api/eventhub/)
- [Azure Relay](/rest/api/relay/)

## <a name="service-manager-rest-api---resource-manager-rest-api"></a>서비스 관리자 REST API - 리소스 관리자 REST API

| 서비스 관리자 API(더 이상 사용되지 않습니다) | 리소스 관리자 - 서비스 버스 API | 리소스 관리자 - 이벤트 허브 API | 리소스 관리자 - 릴레이 API |
| --------------- | ----------------- | ----------------- | ----------------- | 
| **네임스페이스-겟네임스페이스Async** <br/>[서비스 버스 네임스페이스 받기](/rest/api/servicebus/get-namespace)<br/>[이벤트 허브 Get 네임스페이스](/rest/api/eventhub/get-event-hub)<br/>[네임스페이스 릴레이](/rest/api/servicebus/get-relays)<br/> ```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [get](/rest/api/servicebus/namespaces/get) | [get](/rest/api/eventhub/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **연결 세부 정보-가져오기 세부 정보**<br/>서비스 버스/이벤트 허브/릴레이 GetConnectionDetals<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/ConnectionDetails``` | [listkeys](/rest/api/servicebus/namespaces/listkeys) | [listkeys](/rest/api/eventhub/namespaces/listkeys) | [listkeys](/rest/api/relay/namespaces/listkeys) |
| **주제 - 겟토픽Async**<br/>Service Bus<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/topics? $skip={skip}&$top={top}``` | [목록](/rest/api/servicebus/topics/listbynamespace) | &nbsp; | &nbsp; | 
| **큐-겟큐Async** <br/>Service Bus<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/queues/{queueName}``` | [get](/rest/api/servicebus/queues/get) | &nbsp; | &nbsp; | 
| **릴레이-겟릴레이사싱크**<br/>[릴레이 받기](/rest/api/servicebus/get-relays)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/relays? $skip={skip}&$top={top}```| &nbsp; | &nbsp; | [목록](/rest/api/relay/wcfrelays/listbynamespace) | 
| **네임스페이스권한부여규칙-GetNamespace권한부여규칙Async**<br/>서비스 버스/이벤트 허브/릴레이 GetNamespaceAuthRule<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/authorizationrules?``` | [get권한 부여 규칙](/rest/api/servicebus/namespaces/getauthorizationrule) |[get권한 부여 규칙](/rest/api/eventhub/namespaces/getauthorizationrule) | [get권한 부여 규칙](/rest/api/relay/namespaces/getauthorizationrule) |
| **네임스페이스-삭제네임스페이스동기화**<br/>[서비스 버스 삭제 네임스페이스](/rest/api/servicebus/delete-namespace)<br/>[이벤트 허브 네임스페이스 삭제](/rest/api/eventhub/delete-event-hub)<br/>[네임스페이스 삭제 릴레이](/rest/api/servicebus/delete-namespace)<br/> ```DELETE  https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [삭제](/rest/api/servicebus/namespaces/delete) | [삭제](/rest/api/eventhub/namespaces/delete) | [삭제](/rest/api/relay/namespaces/delete) | 
| **메시징스쿠플랜-겟플라나싱크**<br/>서비스 버스/이벤트 허브/릴레이 네임스페이스 받기<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/MessagingPlan``` | [get](/rest/api/servicebus/namespaces/get) | [get](/rest/api/eventhub/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **메시징스쿠플랜-업데이트플랜Async**<br/>서비스 버스/이벤트 허브/릴레이 네임스페이스 받기<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/MessagingPlan``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) |
| **네임스페이스권한부여규칙-업데이트네임스페이스권한부여규칙Async**<br/>서비스 버스/이벤트 허브/릴레이 네임스페이스 받기<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdate 권한 부여 규칙](/rest/api/eventhub/namespaces/createorupdateauthorizationrule) | [createorupdate 권한 부여 규칙](/rest/api/relay/namespaces/createorupdateauthorizationrule) | 
| **네임스페이스권한부여규칙-만들기네임스페이스권한부여규칙Async**<br/> 
서비스 버스/이벤트 허브/릴레이<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` |[createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdate 권한 부여 규칙](/rest/api/eventhub/namespaces/createorupdateauthorizationrule) | [createorupdate 권한 부여 규칙](/rest/api/relay/namespaces/createorupdateauthorizationrule) |
| **네임스페이스프로퍼-겟네임스페이스속성**<br/>[서비스 버스 네임스페이스 받기](/rest/api/servicebus/get-namespace)<br/>[이벤트 허브는 네임스페이스를 가져옵니다.](/rest/api/eventhub/get-event-hub)<br/>[네임스페이스 릴레이](/rest/api/servicebus/get-relays)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [get](/rest/api/servicebus/namespaces/get) | [get](/rest/api/eventhub/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **지역 코드-GetregionCodesAsync**<br/>서비스 버스/이벤트 허브/릴레이 네임스페이스 받기<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [리스트비스쿠](/rest/api/servicebus/regions/listbysku) | [리스트비스쿠](/rest/api/eventhub/regions/listbysku) | &nbsp; | 
| **네임스페이스속성-업데이트네임스페이스속성**<br/>서비스 버스/이벤트 허브/릴레이<br/>```GET    https://management.core.windows.net/{subscription ID}/services/ServiceBus/Regions/``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) |
| **이벤트허브크루드-리스트이벤트허브Async**<br/>[이벤트 허브 목록](/rest/api/eventhub/list-event-hubs)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/eventhubs?$skip={skip}&$top={top}``` | &nbsp; | [목록](/rest/api/eventhub/eventhubs/listbynamespace) | &nbsp; | 
| **이벤트허브크루드-겟이벤트허브동기**<br/>[이벤트 허브 받기](/rest/api/eventhub/get-event-hub)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/eventhubs/{eventHubPath}``` | &nbsp; | [get](/rest/api/eventhub/eventhubs/get) | &nbsp; | 
| **네임스페이스권한부여규칙-삭제네임스페이스권한부여규칙Async**<br/>서비스 버스/이벤트 허브/릴레이<br/>```DELETE https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` | [삭제 권한 부여 규칙](/rest/api/servicebus/namespaces/deleteauthorizationrule) | [삭제 권한 부여 규칙](/rest/api/eventhub/namespaces/deleteauthorizationrule) | [삭제 권한 부여 규칙](/rest/api/relay/namespaces/deleteauthorizationrule) |
| **네임 스페이스권한부여규칙-GetNamespace권한부여규칙Async**<br/>서비스 버스/이벤트 허브/릴레이<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules``` | [목록 권한 부여 규칙](/rest/api/servicebus/namespaces/listauthorizationrules) | [목록 권한 부여 규칙](/rest/api/eventhub/namespaces/listauthorizationrules) | [목록 권한 부여 규칙](/rest/api/relay/namespaces/listauthorizationrules) |
| **네임스페이스가용성-IsName스페이스사용 가능**<br/>[서비스 버스 네임스페이스 가용성](/rest/api/servicebus/check-namespace-availability)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/CheckNamespaceAvailability/?namespace=<namespaceValue>``` | [checknameavailability](/rest/api/servicebus/namespaces/checknameavailability) | [checknameavailability](/rest/api/eventhub/namespaces/checknameavailability) | [checknameavailability](/rest/api/relay/namespaces/checknameavailability) |
| **네임스페이스-만들기또는Update네임스페이스동기화**<br/>서비스 버스/이벤트 허브/릴레이<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) | 
| **토픽-겟토피토픽Async**<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/topics/{topicPath}``` | [get](/rest/api/servicebus/topics/get) | &nbsp; | &nbsp; |

## <a name="service-manager-powershell---resource-manager-powershell"></a>서비스 관리자 PowerShell - 리소스 관리자 PowerShell
| 서비스 관리자 PowerShell 명령(더 이상 사용되지 않습니다) | 새 리소스 관리자 명령 | 최신 리소스 관리자 명령 |
| ----- | ----- | ----- | 
| [Get-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/get-azuresbauthorizationrule?view=azuresmps-4.0.0) | [Get-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/get-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [겟-아즈서비스버스권한부여룰](/powershell/module/az.servicebus/get-azservicebusauthorizationrule?view=azps-1.6.0) |
| [Get-AzureSBLocation](/powershell/module/servicemanagement/azure/get-azuresblocation?view=azuresmps-4.0.0) | [Get-AzureRmServiceBusGeoDR 구성](/powershell/module/azurerm.servicebus/get-azurermservicebusgeodrconfiguration?view=azurermps-6.13.0) | [Get-AzServiceBusGeoDR구성](/powershell/module/az.servicebus/get-azservicebusgeodrconfiguration?view=azps-1.6.0) |
| [Get-AzureSBNamespace](/powershell/module/servicemanagement/azure/get-azuresbnamespace?view=azuresmps-4.0.0) | [Get-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespace?view=azurermps-6.13.0) | [겟 아즈서비스버스네임스페이스](/powershell/module/az.servicebus/get-azservicebusnamespace?view=azps-1.6.0) |
| [New-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/new-azuresbauthorizationrule?view=azuresmps-4.0.0) | [New-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/new-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [뉴 아즈서비스버스권한부여룰](/powershell/module/az.servicebus/new-azservicebusauthorizationrule?view=azps-1.6.0) |
| [New-AzureSBNamespace](/powershell/module/servicemanagement/azure/new-azuresbnamespace?view=azuresmps-4.0.0) | [New-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespace?view=azurermps-6.13.0) | [뉴 아즈서비스버스네임스페이스](/powershell/module/az.servicebus/new-azservicebusnamespace?view=azps-1.6.0) |
| [제거-AzureRm릴레이권한 부여 규칙](/powershell/module/azurerm.relay/remove-azurermrelayauthorizationrule?view=azurermps-6.13.0) | [Remove-AzureRmEventHubAuthorizationRule](/powershell/module/azurerm.eventhub/remove-azurermeventhubauthorizationrule?view=azurermps-6.13.0) | [제거-아즈서비스버스 권한 부여규칙](/powershell/module/az.servicebus/remove-azservicebusauthorizationrule?view=azps-1.6.0) |
| [Remove-AzureSBNamespace](/powershell/module/servicemanagement/azure/remove-azuresbnamespace?view=azuresmps-4.0.0) | [Remove-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespace?view=azurermps-6.13.0) | [제거-아즈서비스버스네임스페이스](/powershell/module/az.servicebus/remove-azservicebusnamespace?view=azps-1.6.0) |
| [Set-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/set-azuresbauthorizationrule?view=azuresmps-4.0.0) | [Set-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/set-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [세트 아즈서비스버스권한부여규칙](/powershell/module/az.servicebus/set-azservicebusauthorizationrule?view=azps-1.6.0) |

## <a name="next-steps"></a>다음 단계
다음 설명서를 참조 하세요. 

- 최신 REST API 설명서
    - [Azure 서비스 버스](/rest/api/servicebus/)
    - [Azure Event Hubs](/rest/api/eventhub/)
    - [Azure Relay](/rest/api/relay/)
- 최신 PowerShell 문서
    - [Azure 서비스 버스](/powershell/module/azurerm.servicebus/?view=azurermps-6.13.0#service_bus)
    - [Azure Event Hubs](/powershell/module/azurerm.eventhub/?view=azurermps-6.13.0#event_hub)
    - [Azure Event Grid](/powershell/module/azurerm.eventgrid/?view=azurermps-6.13.0#event_grid)
