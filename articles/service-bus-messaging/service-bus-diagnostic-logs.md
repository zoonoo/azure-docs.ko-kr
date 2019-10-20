---
title: Azure Service Bus 진단 로그 | Microsoft Docs
description: Azure에서 Service Bus에 대한 진단 로그를 설정하는 방법을 알아봅니다.
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
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 41e0bdc1f04c9491ebe939f46b59ae4eb2bc7ab6
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72592440"
---
# <a name="enable-diagnostic-logs-for-service-bus"></a>Service Bus에 대 한 진단 로그 사용

Azure Service Bus 네임 스페이스 사용을 시작 하는 경우 네임 스페이스를 생성, 삭제 또는 액세스 하는 방법과 시기를 모니터링할 수 있습니다. 이 문서에서는 사용 가능한 모든 운영/진단 로그에 대 한 개요를 제공 합니다.

Azure Service Bus 현재 Azure Service Bus 네임 스페이스에 대해 수행 되는 **관리 작업** 을 캡처하는 활동/작업 로그를 지원 합니다. 특히 이러한 로그는 큐 생성, 사용된 리소스 및 작업 상태를 비롯한 작업 형식을 캡처합니다.

## <a name="operational-logs-schema"></a>작업 로그 스키마

모든 로그는 아래 2 개 위치에서 JavaScript Object Notation (JSON) 형식으로 저장 됩니다.

- **Azureactivity** -포털에서 또는 Azure Resource Manager 템플릿 배포를 통해 네임 스페이스에 대해 수행 된 작업/작업의 로그를 표시 합니다.
- **Azurediagnostics** -API를 사용 하 여 네임 스페이스에 대해 수행 된 작업/작업의 로그 또는 언어 SDK의 관리 클라이언트를 통해 로그를 표시 합니다.

작업 로그 JSON 문자열에는 다음 표에 나열된 요소가 포함되어 있습니다.

| name | 설명 |
| ------- | ------- |
| ActivityId | 지정 된 작업을 식별 하는 데 사용 되는 내부 ID |
| EventName | 작업 이름 |
| ResourceId | Azure Resource Manager 리소스 ID |
| SubscriptionId | 구독 ID |
| EventTimeString | 작업 시간 |
| EventProperties | 작업 속성 |
| 상태 | 작업 상태 |
| Caller | 작업 호출자(Azure Portal 또는 관리 클라이언트) |
| 범주 | OperationalLogs |

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

## <a name="what-eventsoperations-are-captured-in-operational-logs"></a>작업 로그에 캡처되는 이벤트/작업은 무엇 인가요?

작업 로그는 Azure Service Bus 네임 스페이스에 대해 수행 되는 모든 관리 작업을 캡처합니다. Azure Service Bus에서 수행 되는 데이터 작업의 양이 많아 데이터 작업이 캡처되지 않습니다.

> [!NOTE]
> 데이터 작업을 효율적으로 추적 하기 위해 클라이언트 쪽 추적을 사용 하는 것이 좋습니다.

아래 관리 작업은 작업 로그에 캡처됩니다. 

| 범위 | 작업(Operation)|
|-------| -------- |
| 네임스페이스 | <ul> <li> 네임스페이스 만들기</li> <li> 네임 스페이스 업데이트 </li> <li> 네임 스페이스 삭제 </li>  </ul> | 
| Queue | <ul> <li> 큐 만들기</li> <li> 업데이트 큐</li> <li> 큐 삭제 </li> </ul> | 
| 주제 | <ul> <li> 항목 만들기 </li> <li> 업데이트 항목 </li> <li> 항목 삭제 </li> </ul> |
| Subscription | <ul> <li> 구독 만들기 </li> <li> 구독 업데이트 </li> <li> 구독 삭제 </li> </ul> |

> [!NOTE]
> 현재 **읽기** 작업은 작업 로그에서 추적 되지 않습니다.

## <a name="how-to-enable-operational-logs"></a>작업 로그를 사용 하도록 설정 하는 방법

작업 로그는 기본적으로 사용 되지 않습니다. 진단 로그를 활성화하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 Azure Service Bus 네임 스페이스로 이동 하 고 **모니터링**아래에서 **진단 설정**을 클릭 합니다.

   ![진단 로그에 대한 블레이드 탐](./media/service-bus-diagnostic-logs/image1.png)

2. 진단 설정 **추가** 를 클릭 하 여 진단 설정을 구성 합니다.  

   ![진단 로그 사용](./media/service-bus-diagnostic-logs/image2.png)

3. 진단 설정 구성
   1. 진단 설정을 식별할 **이름을** 입력 합니다.
   2. 진단에 대 한 대상을 선택 합니다.
      - **저장소 계정을**선택 하는 경우 진단이 저장 될 저장소 계정을 구성 해야 합니다.
      - **Event hubs**를 선택 하는 경우 진단 설정이 스트리밍되는 적절 한 이벤트 허브를 구성 해야 합니다.
      - **Log Analytics**선택 하는 경우 진단이 전송 될 Log Analytics 인스턴스를 지정 해야 합니다.
    3. **OperationalLogs**를 확인 합니다.

       ![진단 로그 상태 변경](./media/service-bus-diagnostic-logs/image3.png)

4. 페이지 맨 아래에 있는 **저장**을 참조하세요.


새 설정은 약 10분 후에 적용됩니다. 그런 다음 구성된 보관 대상의 **진단 로그** 블레이드에 로그가 나타납니다.

진단 구성에 대한 자세한 내용은 [Azure 진단 로그 개요](../azure-monitor/platform/diagnostic-logs-overview.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Service Bus에 대한 자세한 내용을 보려면 다음 링크를 참조하세요.

* [Service Bus 소개](service-bus-messaging-overview.md)
* [Service Bus 시작](service-bus-dotnet-get-started-with-queues.md)
