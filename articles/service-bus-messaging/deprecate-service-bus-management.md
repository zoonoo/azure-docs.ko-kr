---
title: Azure 메시징 서비스-리소스 관리자에 Service Manager
description: 이 문서에서는 사용 되지 않는 Azure Service Manager REST API & powershell cmdlet을 리소스 관리자 REST API & PowerShell cmdlet에 매핑하는 방법을 설명 합니다.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 65dab975ba7c5e4bdcf654088e7a42c2d92d7dee
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87059638"
---
# <a name="deprecation-of-azure-service-manager-support-for-azure-service-bus-relay-and-event-hubs"></a>Azure Service Bus, 릴레이 및 Event Hubs에 대 한 Azure Service Manager 지원 중단

차세대 클라우드 인프라 stack 리소스 관리자 "클래식" Azure 서비스 관리 모델 (클래식 배포 모델)을 완전히 대체 합니다. 따라서 클래식 배포 모델 REST Api와 Service Bus, 릴레이 및 Event Hubs에 대 한 지원은 2021 년 11 월 1 일에 사용 중지 됩니다. 이 사용 중단은 [Microsoft 기술 커뮤니티 공지](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Deprecating-Service-Management-support-for-Azure-Service-Bus/ba-p/370909)에서 처음 발표 되었지만 최근 발표 시간에서 2 년 동안 중단 기간을 연장 하기로 결정 했습니다. 쉽게 식별할 수 있도록 이러한 Api `management.core.windows.net` 의 URI는입니다. 사용 되지 않는 Api 및 현재 사용 해야 하는 Azure Resource Manager API 버전 목록은 다음 표를 참조 하세요.

Service Bus, 릴레이 및 Event Hubs를 계속 사용 하려면 2021 년 10 월 31 일까 지 리소스 관리자로 이동 합니다. 이전 Api를 사용 하는 모든 고객에 게는 리소스 그룹화, 태그, 간소화 된 배포 및 관리 프로세스 및 RBAC (역할 기반 액세스 제어)를 사용 하는 세분화 된 액세스 제어를 포함 하는 리소스 관리자의 추가 이점을 활용 하기 위해 곧 이전 Api를 사용 하는 것이 좋습니다.

Vs Azure Service Manager Azure Resource Manager에 대 한 자세한 내용은 [TechNet 블로그](https://blogs.technet.microsoft.com/meamcs/2016/12/22/difference-between-azure-service-manager-and-azure-resource-manager/)를 참조 하세요.

Azure Service Bus, 릴레이 및 Event Hubs에 대 한 Service Manager 및 리소스 관리자 Api에 대 한 자세한 내용은 REST API 설명서를 참조 하세요.

- [Azure Service Bus](/rest/api/servicebus/)
- [Azure Event Hubs](/rest/api/eventhub/)
- [Azure Relay](/rest/api/relay/)

## <a name="service-manager-rest-api---resource-manager-rest-api"></a>Service Manager REST API-리소스 관리자 REST API

| Service Manager Api (사용 되지 않음) | 리소스 관리자-Service Bus API | 리소스 관리자-이벤트 허브 API | 리소스 관리자-릴레이 API |
| --------------- | ----------------- | ----------------- | ----------------- | 
| **네임 스페이스-GetNamespaceAsync** <br/>[네임 스페이스 가져오기 Service Bus](/rest/api/servicebus/get-namespace)<br/>[이벤트 허브 가져오기 네임 스페이스](/rest/api/eventhub/get-event-hub)<br/>[Relay 네임 스페이스 가져오기](/rest/api/servicebus/get-relays)<br/> ```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [get](/rest/api/servicebus/namespaces/get) | [get](/rest/api/eventhub/2017-04-01/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **ConnectionDetails-GetConnectionDetails**<br/>Service Bus/이벤트 허브/릴레이 GetConnectionDetals<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/ConnectionDetails``` | [listkeys](/rest/api/servicebus/namespaces/listkeys) | [listkeys](/rest/api/eventhub/2017-04-01/authorization%20rules%20-%20event%20hubs/listkeys) | [listkeys](/rest/api/relay/namespaces/listkeys) |
| **토픽-Get토픽 Async**<br/>Service Bus<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/topics? $skip={skip}&$top={top}``` | [list](/rest/api/servicebus/topics/listbynamespace) | &nbsp; | &nbsp; | 
| **큐-GetQueueAsync** <br/>Service Bus<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/queues/{queueName}``` | [get](/rest/api/servicebus/queues/get) | &nbsp; | &nbsp; | 
| **릴레이-GetRelaysAsync**<br/>[릴레이 가져오기](/rest/api/servicebus/get-relays)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/relays? $skip={skip}&$top={top}```| &nbsp; | &nbsp; | [list](/rest/api/relay/wcfrelays/listbynamespace) | 
| **NamespaceAuthorizationRules-GetNamespaceAuthorizationRuleAsync**<br/>Service Bus/이벤트 허브/릴레이 GetNamespaceAuthRule<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/authorizationrules?``` | [getauthorizationrule](/rest/api/servicebus/namespaces/getauthorizationrule) | [getauthorizationrule](/rest/api/eventhub/2017-04-01/authorization%20rules%20-%20namespaces/getauthorizationrule) | [getauthorizationrule](/rest/api/relay/namespaces/getauthorizationrule) |
| **네임 스페이스-DeleteNamespaceAsync**<br/>[네임 스페이스 삭제 Service Bus](/rest/api/servicebus/delete-namespace)<br/>[네임 스페이스 삭제 Event Hubs](/rest/api/eventhub/delete-event-hub)<br/>[릴레이 네임 스페이스 삭제](/rest/api/servicebus/delete-namespace)<br/> ```DELETE  https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [delete](/rest/api/servicebus/namespaces/delete) | [delete](/rest/api/eventhub/2017-04-01/namespaces/delete) | [delete](/rest/api/relay/namespaces/delete) | 
| **MessagingSKUPlan-Getplan Async**<br/>Service Bus/이벤트 허브/릴레이 네임 스페이스 가져오기<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/MessagingPlan``` | [get](/rest/api/servicebus/namespaces/get) | [get](/rest/api/eventhub/2017-04-01/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **MessagingSKUPlan-Updateplan Async**<br/>Service Bus/이벤트 허브/릴레이 네임 스페이스 가져오기<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/MessagingPlan``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/2017-04-01/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) |
| **NamespaceAuthorizationRules-UpdateNamespaceAuthorizationRuleAsync**<br/>Service Bus/이벤트 허브/릴레이 네임 스페이스 가져오기<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdateauthorizationrule](/rest/api/eventhub/2017-04-01/authorization%20rules%20-%20event%20hubs/createorupdateauthorizationrule) | [createorupdateauthorizationrule](/rest/api/relay/namespaces/createorupdateauthorizationrule) | 
| **NamespaceAuthorizationRules-CreateNamespaceAuthorizationRuleAsync**<br/> 
Service Bus/이벤트 허브/릴레이<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` |[createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdateauthorizationrule](/rest/api/eventhub/2017-04-01/authorization%20rules%20-%20event%20hubs/createorupdateauthorizationrule) | [createorupdateauthorizationrule](/rest/api/relay/namespaces/createorupdateauthorizationrule) |
| **NamespaceProperties-GetNamespacePropertiesAsync**<br/>[네임 스페이스 가져오기 Service Bus](/rest/api/servicebus/get-namespace)<br/>[네임 스페이스 가져오기 Event Hubs](/rest/api/eventhub/get-event-hub)<br/>[Relay 네임 스페이스 가져오기](/rest/api/servicebus/get-relays)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [get](/rest/api/servicebus/namespaces/get) | [get](/rest/api/eventhub/2017-04-01/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **지역 코드-GetRegionCodesAsync**<br/>Service Bus/EventHub/Relay 네임 스페이스 가져오기<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [listbysku](/rest/api/servicebus/regions/listbysku) | [listbysku](/rest/api/eventhub/2017-04-01/regions/listbysku) | &nbsp; | 
| **NamespaceProperties-UpdateNamespacePropertyAsync**<br/>Service Bus/EventHub/Relay<br/>```GET    https://management.core.windows.net/{subscription ID}/services/ServiceBus/Regions/``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/2017-04-01/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) |
| **EventHubsCrud-ListEventHubsAsync**<br/>[목록 Event Hubs](/rest/api/eventhub/list-event-hubs)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/eventhubs?$skip={skip}&$top={top}``` | &nbsp; | [list](/rest/api/servicebus/eventhubs/listbynamespace) | &nbsp; | 
| **EventHubsCrud-GetEventHubAsync**<br/>[Event Hubs 가져오기](/rest/api/eventhub/get-event-hub)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/eventhubs/{eventHubPath}``` | &nbsp; | [get](/rest/api/eventhub/get-event-hub) | &nbsp; | 
| **NamespaceAuthorizationRules-DeleteNamespaceAuthorizationRuleAsync**<br/>Service Bus/이벤트 허브/릴레이<br/>```DELETE https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` | [deleteauthorizationrule](/rest/api/servicebus/namespaces/deleteauthorizationrule) | [deleteauthorizationrule](/rest/api/eventhub/2017-04-01/authorization%20rules%20-%20namespaces/deleteauthorizationrule) | [deleteauthorizationrule](/rest/api/relay/namespaces/deleteauthorizationrule) |
| **NamespaceAuthorizationRules-GetNamespaceAuthorizationRulesAsync**<br/>Service Bus/EventHub/Relay<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules``` | [listauthorizationrules](/rest/api/servicebus/namespaces/listauthorizationrules) | [listauthorizationrules](/rest/api/eventhub/2017-04-01/authorization%20rules%20-%20namespaces/listauthorizationrules) | [listauthorizationrules](/rest/api/relay/namespaces/listauthorizationrules) |
| **NamespaceAvailability-IsNamespaceAvailable**<br/>[Service Bus 네임 스페이스 가용성](/rest/api/servicebus/check-namespace-availability)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/CheckNamespaceAvailability/?namespace=<namespaceValue>``` | [checknameavailability](/rest/api/servicebus/namespaces/checknameavailability) | [checknameavailability](/rest/api/eventhub/2017-04-01/check%20name%20availability%20-%20namespaces/checknameavailability) | [checknameavailability](/rest/api/relay/namespaces/checknameavailability) |
| **네임 스페이스-CreateOrUpdateNamespaceAsync**<br/>Service Bus/이벤트 허브/릴레이<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/2017-04-01/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) | 
| **토픽-GetTopicAsync**<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/topics/{topicPath}``` | [get](/rest/api/servicebus/topics/get) | &nbsp; | &nbsp; |

## <a name="service-manager-powershell---resource-manager-powershell"></a>Service Manager PowerShell-리소스 관리자 PowerShell
| Service Manager PowerShell 명령 (사용 되지 않음) | 새 리소스 관리자 명령 | 최신 리소스 관리자 명령 |
| ----- | ----- | ----- | 
| [Get-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure.service/get-azuresbauthorizationrule?view=azuresmps-4.0.0) | [Get-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/get-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/get-azservicebusauthorizationrule?view=azps-1.6.0) |
| [Get-AzureSBLocation](/powershell/module/servicemanagement/azure.service/get-azuresblocation?view=azuresmps-4.0.0) | [AzureRmServiceBusGeoDRConfiguration](/powershell/module/azurerm.servicebus/get-azurermservicebusgeodrconfiguration?view=azurermps-6.13.0) | [AzServiceBusGeoDRConfiguration](/powershell/module/az.servicebus/get-azservicebusgeodrconfiguration?view=azps-1.6.0) |
| [Get-AzureSBNamespace](/powershell/module/servicemanagement/azure.service/get-azuresbnamespace?view=azuresmps-4.0.0) | [Get-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespace?view=azurermps-6.13.0) | [AzServiceBusNamespace](/powershell/module/az.servicebus/get-azservicebusnamespace?view=azps-1.6.0) |
| [New-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure.service/new-azuresbauthorizationrule?view=azuresmps-4.0.0) | [New-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/new-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/new-azservicebusauthorizationrule?view=azps-1.6.0) |
| [New-AzureSBNamespace](/powershell/module/servicemanagement/azure.service/new-azuresbnamespace?view=azuresmps-4.0.0) | [New-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespace?view=azurermps-6.13.0) | [AzServiceBusNamespace](/powershell/module/az.servicebus/new-azservicebusnamespace?view=azps-1.6.0) |
| [AzureRmRelayAuthorizationRule](/powershell/module/azurerm.relay/remove-azurermrelayauthorizationrule?view=azurermps-6.13.0) | [Remove-AzureRmEventHubAuthorizationRule](/powershell/module/azurerm.eventhub/remove-azurermeventhubauthorizationrule?view=azurermps-6.13.0) | [AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/remove-azservicebusauthorizationrule?view=azps-1.6.0) |
| [Remove-AzureSBNamespace](/powershell/module/servicemanagement/azure.service/remove-azuresbnamespace?view=azuresmps-4.0.0) | [Remove-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespace?view=azurermps-6.13.0) | [AzServiceBusNamespace](/powershell/module/az.servicebus/remove-azservicebusnamespace?view=azps-1.6.0) |
| [Set-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure.service/set-azuresbauthorizationrule?view=azuresmps-4.0.0) | [Set-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/set-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/set-azservicebusauthorizationrule?view=azps-1.6.0) |

## <a name="next-steps"></a>다음 단계
다음 설명서를 참조 하세요. 

- 최신 REST API 설명서
    - [Azure Service Bus](/rest/api/servicebus/)
    - [Azure Event Hubs](/rest/api/eventhub/)
    - [Azure Relay](/rest/api/relay/)
- 최신 PowerShell 설명서
    - [Azure Service Bus](/powershell/module/azurerm.servicebus/?view=azurermps-6.13.0#service_bus)
    - [Azure Event Hubs](/powershell/module/azurerm.eventhub/?view=azurermps-6.13.0#event_hub)
    - [Azure Event Grid](/powershell/module/azurerm.eventgrid/?view=azurermps-6.13.0#event_grid)
