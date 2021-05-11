---
title: Azure Service Bus 진단 로그 | Microsoft Docs
description: 이 문서에서는 Azure Service Bus에 사용할 수 있는 모든 작업 및 진단 로그에 대한 개요를 제공합니다.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: f2672d61285025eadb8f742ca2518297107595ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100585271"
---
# <a name="enable-diagnostics-logs-for-service-bus"></a>Service Bus에 대한 진단 로그 사용

Azure Service Bus의 네임스페이스 사용을 시작하는 경우 네임스페이스를 만들고, 삭제하고, 액세스하는 방법과 시기를 모니터링할 수 있습니다. 이 문서에서는 이용 가능한 모든 작업 및 진단 로그의 개요를 제공합니다.

Azure Service Bus는 현재 Azure Service Bus 네임스페이스에서 수행되는 *관리 작업* 을 캡처하는 활동 및 작업 로그를 지원합니다. 특히 이러한 로그는 큐 생성, 사용된 리소스 및 작업 상태를 비롯한 작업 형식을 캡처합니다.

## <a name="operational-logs-schema"></a>작업 로그 스키마

모든 로그는 다음의 두 위치에서 JSON(JavaScript Object Notation) 형식으로 저장됩니다.

- **AzureActivity**: Azure Portal 또는 Azure Resource Manager 템플릿 배포를 통해 네임스페이스에 대해 수행하는 작업 및 활동의 로그를 표시합니다.
- **AzureDiagnostics**: API 또는 언어 SDK 관리 클라이언트를 통해 네임스페이스에 대해 수행하는 작업 및 활동의 로그를 표시합니다.

작업 로그 JSON 문자열에는 다음 표에 나열된 요소가 포함되어 있습니다.

| 이름 | Description |
| ------- | ------- |
| ActivityId | 지정된 작업을 식별하는 데 사용되는 내부 ID |
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

## <a name="events-and-operations-captured-in-operational-logs"></a>작업 로그에 캡처된 이벤트 및 작업

작업 로그는 Azure Service Bus 네임스페이스에서 수행되는 모든 관리 작업을 캡처합니다. 데이터 작업은 Azure Service Bus에서 수행되는 대용량 데이터 작업으로 인해 캡처되지 않습니다.

> [!NOTE]
> 데이터 작업을 보다 효율적으로 추적할 수 있도록 클라이언트 사이드 트레이싱을 사용하는 것이 좋습니다.

다음 관리 작업은 작업 로그에 캡처됩니다. 

| 범위 | 작업(Operation)|
|-------| -------- |
| 네임스페이스 | <ul> <li> 네임스페이스 만들기</li> <li> 네임스페이스 업데이트 </li> <li> 네임스페이스 삭제 </li> <li> 네임스페이스 SharedAccess 정책 업데이트 </li> </ul> | 
| 큐 | <ul> <li> 큐 만들기</li> <li> 큐 업데이트</li> <li> 큐 삭제 </li> <li> AutoDelete 큐 삭제 </li> </ul> | 
| 항목 | <ul> <li> 토픽 만들기 </li> <li> 토픽 업데이트 </li> <li> 토픽 삭제 </li> <li> AutoDelete 토픽 삭제 </li> </ul> |
| Subscription | <ul> <li> 구독 만들기 </li> <li> 구독 업데이트 </li> <li> 구독 삭제 </li> <li> AutoDelete 구독 삭제 </li> </ul> |

> [!NOTE]
> 현재 *읽기* 작업은 작업 로그에서 추적되지 않습니다.

## <a name="enable-operational-logs"></a>작업 로그 사용

작업 로그는 기본적으로 사용되지 않습니다. 진단 로그를 사용하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 Azure Service Bus 네임스페이스로 이동한 다음, **모니터링** 에서 **진단 설정** 을 선택합니다.

   ![“진단 설정” 링크](./media/service-bus-diagnostic-logs/image1.png)

1. **진단 설정** 창에서 **진단 설정 추가** 를 선택합니다.  

   !["진단 설정 추가" 링크](./media/service-bus-diagnostic-logs/image2.png)

1. 다음 단계를 수행하여 진단 설정을 구성합니다.

   a. **이름** 상자에 진단 설정의 이름을 입력합니다.  

   b. 진단 로그에 다음 세 가지 대상 중 하나를 선택합니다.  
   - **스토리지 계정에 보관** 을 선택하는 경우 진단 로그가 저장될 스토리지 계정을 구성해야 합니다.  
   - **이벤트 허브에 스트리밍** 을 선택하는 경우 진단 로그를 스트림할 이벤트 허브를 구성해야 합니다.
   - **Log Analytics에 보내기** 를 선택하는 경우 진단이 전송될 Log Analytics 인스턴스를 지정해야 합니다.  

   다. **OperationalLogs** 확인란을 선택합니다.

    ![“진단 설정” 창](./media/service-bus-diagnostic-logs/image3.png)

1. **저장** 을 선택합니다.

새 설정은 약 10분 후에 적용됩니다. 구성된 보관 대상의 **진단 로그** 창에 로그가 표시됩니다.

진단 구성 설정에 대한 자세한 내용은 [Azure 진단 로그 개요](../azure-monitor/essentials/platform-logs-overview.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Service Bus에 대한 자세한 정보는 다음을 참조하세요.

* [Service Bus 소개](service-bus-messaging-overview.md)
* [Service Bus 시작](service-bus-dotnet-get-started-with-queues.md)