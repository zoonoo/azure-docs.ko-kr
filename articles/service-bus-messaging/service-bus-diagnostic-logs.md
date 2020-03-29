---
title: Azure 서비스 버스 진단 로그 | 마이크로 소프트 문서
description: 이 문서에서는 Azure Service Bus에서 사용할 수 있는 모든 운영 및 진단 로그에 대한 개요를 제공합니다.
keywords: ''
documentationcenter: .net
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: c8eba538a7015648611e6054ce85b381dcfc9105
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76761001"
---
# <a name="enable-diagnostics-logs-for-service-bus"></a>서비스 버스에 대한 진단 로그 사용

Azure Service Bus 네임스페이스사용을 시작할 때 네임스페이스를 만들거나 삭제하거나 액세스하는 방법과 시기를 모니터링할 수 있습니다. 이 문서에서는 사용 가능한 모든 운영 및 진단 로그에 대한 개요를 제공합니다.

Azure Service Bus는 현재 Azure Service Bus 네임스페이스에서 수행되는 *관리 작업을* 캡처하는 활동 및 운영 로그를 지원합니다. 특히 이러한 로그는 큐 생성, 사용된 리소스 및 작업 상태를 비롯한 작업 형식을 캡처합니다.

## <a name="operational-logs-schema"></a>작업 로그 스키마

모든 로그는 다음과 같은 두 위치에서 자바스크립트 객체 표기술(JSON) 형식으로 저장됩니다.

- **AzureActivity**: Azure 포털또는 Azure 리소스 관리자 템플릿 배포를 통해 네임스페이스에 대해 수행되는 작업 및 작업의 로그를 표시합니다.
- **AzureDiagnostics**: API를 사용하거나 언어 SDK의 관리 클라이언트를 통해 네임스페이스에 대해 수행되는 작업 및 작업의 로그를 표시합니다.

운영 로그 JSON 문자열에는 다음 표에 나열된 요소가 포함됩니다.

| 이름 | 설명 |
| ------- | ------- |
| ActivityId | 지정된 활동을 식별하는 데 사용되는 내부 ID |
| EventName | 작업 이름 |
| ResourceId | Azure Resource Manager 리소스 ID |
| SubscriptionId | 구독 ID |
| EventTimeString | 작업 시간 |
| EventProperties | 작업 속성 |
| 상태 | 작업 상태 |
| Caller | 작업 호출자(Azure 포털 또는 관리 클라이언트) |
| Category | OperationalLogs |

작업 로그 JSON 문자열 예제는 다음과 같습니다.

```json
{
  "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
  "EventName": "Create Queue",
  "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SHOEBOXEHNS-CY4001",
  "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
  "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
  "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
  "Status": "Succeeded",
  "Caller": "ServiceBus Client",
  "category": "OperationalLogs"
}
```

## <a name="events-and-operations-captured-in-operational-logs"></a>운영 로그에 캡처된 이벤트 및 작업

운영 로그는 Azure Service Bus 네임스페이스에서 수행되는 모든 관리 작업을 캡처합니다. Azure Service Bus에서 수행되는 많은 양의 데이터 작업으로 인해 데이터 작업이 캡처되지 않습니다.

> [!NOTE]
> 데이터 작업을 더 잘 추적하려면 클라이언트 쪽 추적을 사용하는 것이 좋습니다.

다음 관리 작업은 운영 로그에 캡처됩니다. 

| Scope | 작업(Operation)|
|-------| -------- |
| 네임스페이스 | <ul> <li> 네임스페이스 만들기</li> <li> 네임스페이스 업데이트 </li> <li> 네임스페이스 삭제 </li>  </ul> | 
| 큐 | <ul> <li> 큐 만들기</li> <li> 큐 업데이트</li> <li> 큐 삭제 </li> </ul> | 
| 항목 | <ul> <li> 토픽 만들기 </li> <li> 토픽 업데이트 </li> <li> 토픽 삭제 </li> </ul> |
| Subscription | <ul> <li> 구독 만들기 </li> <li> 구독 업데이트 </li> <li> 구독 삭제 </li> </ul> |

> [!NOTE]
> 현재 *읽기* 작업은 운영 로그에서 추적되지 않습니다.

## <a name="enable-operational-logs"></a>운영 로그 사용

운영 로그는 기본적으로 비활성화됩니다. 진단 로그를 사용하려면 다음을 수행합니다.

1. Azure [포털에서](https://portal.azure.com)Azure Service Bus 네임스페이스로 이동한 다음 **모니터링에서** **진단 설정을 선택합니다.**

   !["진단 설정" 링크](./media/service-bus-diagnostic-logs/image1.png)

1. 진단 **설정** 창에서 진단 **추가 설정을**선택합니다.  

   !["진단 설정 추가" 링크](./media/service-bus-diagnostic-logs/image2.png)

1. 다음을 수행하여 진단 설정을 구성합니다.

   a. **이름** 상자에 진단 설정의 이름을 입력합니다.  

   b. 진단 로그에 대해 다음 세 가지 대상 중 하나를 선택합니다.  
   - **저장소 계정에 보관을**선택하는 경우 진단 로그가 저장될 저장소 계정을 구성해야 합니다.  
   - **이벤트 허브로 Stream을**선택하는 경우 진단 로그를 스트리밍할 이벤트 허브를 구성해야 합니다.
   - **로그 분석으로 보내기를**선택하는 경우 진단 프로그램이 전송될 로그 분석의 인스턴스를 지정해야 합니다.  

   다. 운영 **로그 확인란을 선택합니다.**

    !["진단 설정" 창](./media/service-bus-diagnostic-logs/image3.png)

1. **저장**을 선택합니다.

새 설정은 약 10분 만에 적용됩니다. 로그는 진단 **로그** 창에 구성된 보관 대상에 표시됩니다.

진단 설정 구성에 대한 자세한 내용은 [Azure 진단 로그 개요를](../azure-monitor/platform/diagnostic-logs-overview.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

서비스 버스에 대한 자세한 내용은 다음을 참조하십시오.

* [Service Bus 소개](service-bus-messaging-overview.md)
* [Service Bus 시작](service-bus-dotnet-get-started-with-queues.md)
