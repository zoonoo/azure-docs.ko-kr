---
title: 진단 로그 설정 - Azure Event Hub | Microsoft Docs
description: Azure에서 이벤트 허브의 활동 로그 및 진단 로그를 설정하는 방법을 배웁니다.
keywords: ''
documentationcenter: ''
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.custom: seodec18
ms.date: 04/28/2020
ms.author: shvija
ms.openlocfilehash: 3010ee7b996c9d3e96082edeb9447c960da321bd
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509787"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Azure 이벤트 허브에 대한 진단 로그 설정

Azure Event Hubs에 대해 다음 두 가지 유형의 로그를 볼 수 있습니다.

* **[활동 로그](../azure-monitor/platform/platform-logs-overview.md)**: 이러한 로그에는 작업에 대해 수행 된 작업에 대 한 정보가 포함 됩니다. 로그는 항상 켜져 있습니다. Azure Portal에서 이벤트 허브 네임 스페이스의 왼쪽 창에서 **활동 로그** 를 선택 하 여 활동 로그 항목을 볼 수 있습니다. 예: "네임 스페이스 만들기 또는 업데이트", "이벤트 허브 만들기 또는 업데이트"

    ![Event Hubs 네임 스페이스에 대 한 활동 로그](./media/event-hubs-diagnostic-logs/activity-log.png)
* **[진단 로그](../azure-monitor/platform/platform-logs-overview.md)**: 작업에서 발생하는 모든 상황을 보다 잘 이해할 수 있도록 진단 로그를 구성할 수 있습니다. 진단 로그는 업데이트 및 작업이 실행 중일 때 발생하는 활동을 비롯하여 작업이 만들어질 때부터 삭제될 때까지의 모든 활동을 포함합니다.

    다음 섹션에서는 Event Hubs 네임 스페이스에 대 한 진단 로그를 사용 하도록 설정 하는 방법을 보여 줍니다.

## <a name="enable-diagnostic-logs"></a>진단 로그 활성화
진단 로그는 기본적으로 해제되어 있습니다. 진단 로그를 활성화하려면 다음 단계를 수행합니다.

1.  [Azure Portal](https://portal.azure.com)에서 Event Hubs 네임 스페이스로 이동 합니다. 
2. 왼쪽 창의 **모니터링** 아래에서 **진단 설정** 을 선택 하 고 **+ 진단 설정 추가**를 선택 합니다. 

    ![진단 설정 페이지-진단 설정 추가](./media/event-hubs-diagnostic-logs/diagnostic-settings-page.png)
4. **범주 세부 정보** 섹션에서 사용 하도록 설정할 **진단 로그 유형을** 선택 합니다. 이러한 범주에 대 한 자세한 내용은이 문서의 뒷부분에서 찾을 수 있습니다. 
5. **대상 세부 정보** 섹션에서 원하는 보관 대상 (대상)을 설정 합니다. 예를 들어 저장소 계정, 이벤트 허브 또는 Log Analytics 작업 영역입니다.

    ![진단 설정 추가 페이지](./media/event-hubs-diagnostic-logs/aDD-diagnostic-settings-page.png)
6.  진단 설정을 저장 하려면 도구 모음에서 **저장** 을 선택 합니다.

    새 설정은 약 10분 후에 적용됩니다. 그런 다음 구성된 보관 대상의 **진단 로그** 창에 로그가 나타납니다.

    진단 구성에 대한 자세한 내용은 [Azure 진단 로그 개요](../azure-monitor/platform/platform-logs-overview.md)를 참조하세요.

## <a name="diagnostic-logs-categories"></a>진단 로그 범주

Event Hubs는 다음 범주에 대 한 진단 로그를 캡처합니다.

- **보관 로그:** Event Hubs 보관, 특히 보관 오류와 관련된 로그입니다.
- **작업 로그:** Event Hubs 작업 중에 발생하는 정보, 특히 이벤트 허브 만들기, 사용된 리소스 및 작업 상태와 같은 작업 유형입니다.
- **자동 크기 조정 로그**: Event Hubs 네임 스페이스에서 수행 되는 자동 크기 조정 작업에 대 한 정보입니다. 
- **Kafka 코디네이터 로그** -Event Hubs와 관련 된 kafka 코디네이터 작업에 대 한 정보입니다. 
- **Kafka 사용자 로그**: Event Hubs와 관련 된 kafka 사용자 작업에 대 한 정보입니다. 
- **Event Hubs 가상 네트워크 (VNet) 연결 이벤트**: Event Hubs 가상 네트워크 연결 이벤트에 대 한 정보입니다. 
- **고객 관리 키 사용자 로그**: 고객이 관리 하는 키와 관련 된 작업에 대 한 정보입니다. 


    모든 로그는 JSON(JavaScript Object Notation) 형식으로 저장됩니다. 각 항목에는 다음 섹션에 설명된 형식을 사용하는 문자열 필드가 있습니다.

## <a name="archive-logs-schema"></a>보관 로그 스키마

보관 로그 JSON 문자열에는 다음 표에 나열된 요소가 포함되어 있습니다.

속성 | Description
------- | -------
TaskName | 실패한 작업에 대한 설명입니다.
ActivityId | 추적에 사용되는 내부 ID입니다.
trackingId | 추적에 사용되는 내부 ID입니다.
resourceId | Azure Resource Manager 리소스 ID입니다.
eventHub | 이벤트 허브 전체 이름(네임스페이스 이름 포함)입니다.
partitionId | 데이터가 기록되는 이벤트 허브 파티션입니다.
archiveStep | ArchiveFlushWriter
startTime | 실패 시작 시간입니다.
failures | 실패가 발생한 횟수입니다.
durationInSeconds | 실패 기간입니다.
message | 오류 메시지입니다.
category | ArchiveLogs

다음 코드는 보관 로그 JSON 문자열에 대한 예입니다.

```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "21b89a0b-8095-471a-9db8-d151d74ecf26",
   "trackingId": "21b89a0b-8095-471a-9db8-d151d74ecf26_B7",
   "resourceId": "/SUBSCRIPTIONS/854D368F-1828-428F-8F3C-F2AFFA9B2F7D/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "eventHub": "fbettati-opera-eventhub:eventhub:eh123~32766",
   "partitionId": "1",
   "archiveStep": "ArchiveFlushWriter",
   "startTime": "9/22/2016 5:11:21 AM",
   "failures": 3,
   "durationInSeconds": 360,
   "message": "Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (404) Not Found. ---> System.Net.WebException: The remote server returned an error: (404) Not Found.\r\n   at Microsoft.WindowsAzure.Storage.Shared.Protocol.HttpResponseParsers.ProcessExpectedStatusCodeNoException[T](HttpStatusCode expectedStatusCode, HttpStatusCode actualStatusCode, T retVal, StorageCommandBase`1 cmd, Exception ex)\r\n   at Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob.<PutBlockImpl>b__3e(RESTCommand`1 cmd, HttpWebResponse resp, Exception ex, OperationContext ctx)\r\n   at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndGetResponse[T](IAsyncResult getResponseResult)\r\n   --- End of inner exception stack trace ---\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.StorageAsyncResult`1.End()\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.AsyncExtensions.<>c__DisplayClass4.<CreateCallbackVoid>b__3(IAsyncResult ar)\r\n--- End of stack trace from previous location where exception was thrown ---\r\n   at System.",
   "category": "ArchiveLogs"
}
```

## <a name="operational-logs-schema"></a>작업 로그 스키마

작업 로그 JSON 문자열에는 다음 표에 나열된 요소가 포함되어 있습니다.

속성 | Description
------- | -------
ActivityId | 추적 목적에 사용되는 내부 ID입니다.
EventName | 작업 이름입니다.  
resourceId | Azure Resource Manager 리소스 ID입니다.
SubscriptionId | 구독 ID가 표시됩니다.
EventTimeString | 작업 시간입니다.
EventProperties | 작업 속성입니다.
상태 | 작업 상태입니다.
Caller | 작업 호출자(Azure Portal 또는 관리 클라이언트)입니다.
category | OperationalLogs

다음 코드는 작업 로그 JSON 문자열에 대한 예입니다.

```json
Example:
{
   "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
   "EventName": "Create EventHub",
   "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/SHOEBOXEHNS-CY4001",
   "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
   "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
   "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
   "Status": "Succeeded",
   "Caller": "ServiceBus Client",
   "category": "OperationalLogs"
}
```

## <a name="autoscale-logs-schema"></a>자동 크기 조정 로그 스키마
자동 크기 조정 로그 JSON에는 다음 표에 나열 된 요소가 포함 되어 있습니다.

| 속성 | Description |
| ---- | ----------- | 
| trackingId | 추적 목적으로 사용 되는 내부 ID |
| resourceId | Azure 구독 ID 및 네임 스페이스 이름을 포함 하는 내부 ID |
| message | 자동 팽창 동작에 대 한 세부 정보를 제공 하는 정보 메시지입니다. 이 메시지에는 지정 된 네임 스페이스에 대 한 이전 및 현재 처리량 단위 값과 TU의 확장을 트리거한 항목이 포함 되어 있습니다. |

## <a name="kafka-coordinator-logs-schema"></a>Kafka 코디네이터 로그 스키마
Kafka 코디네이터 로그 JSON에는 다음 표에 나열 된 요소가 포함 되어 있습니다.

| 속성 | Description |
| ---- | ----------- | 
| requestId | 추적 목적으로 사용 되는 요청 ID |
| resourceId | Azure 구독 ID 및 네임 스페이스 이름을 포함 하는 내부 ID |
| operationName | 그룹 조정을 수행 하는 동안 수행 되는 작업의 이름입니다. |
| clientId | 클라이언트 ID |
| namespaceName | 네임스페이스 이름 | 
| subscriptionId | Azure 구독 ID |
| message | 소비자 그룹 조정을 수행 하는 동안 수행 된 작업에 대 한 세부 정보를 제공 하는 정보 메시지입니다. |

## <a name="kafka-user-error-logs-schema"></a>Kafka 사용자 오류 로그 스키마
Kafka 사용자 오류 로그 JSON에는 다음 표에 나열 된 요소가 포함 되어 있습니다.

| 속성 | Description |
| ---- | ----------- |
| trackingId | 추적에 사용 되는 추적 ID입니다. |
| namespaceName | 네임스페이스 이름 |
| eventhub | 이벤트 허브 이름 |
| partitionId | Partition ID |
| groupId | 그룹 ID |
| clientid | 클라이언트 ID |
| resourceId | Azure 구독 ID 및 네임 스페이스 이름을 포함 하는 내부 ID |
| message | 오류에 대 한 세부 정보를 제공 하는 정보 메시지 |

## <a name="event-hubs-virtual-network-connection-event-schema"></a>Event Hubs 가상 네트워크 연결 이벤트 스키마

VNet (가상 네트워크) 연결 이벤트 JSON에는 다음 표에 나열 된 요소가 포함 되어 Event Hubs.

| 속성 | Description |
| ---  | ----------- | 
| subscriptionId | Azure 구독 ID |
| namespaceName | 네임스페이스 이름 |
| ipAddress | Event Hubs 서비스에 연결 하는 클라이언트의 IP 주소 |
| action | 연결 요청을 평가할 때 Event Hubs 서비스에서 수행 하는 작업입니다. 지원 되는 작업은 **Acceptconnection** 및 **RejectConnection**입니다. |
| reason | 작업이 완료 된 이유를 제공 합니다. |
| 개수 | 지정 된 동작의 발생 횟수 |
| resourceId | 구독 ID 및 네임 스페이스 이름을 포함 하는 내부 리소스 ID입니다. |

## <a name="customer-managed-key-user-logs"></a>고객 관리 키 사용자 로그
고객 관리 키 사용자 로그 JSON에는 다음 표에 나열 된 요소가 포함 되어 있습니다.

| 속성 | Description |
| ---- | ----------- | 
| category | 메시지의 범주 유형입니다. 다음 값 중 하나입니다. **오류** 및 **정보** |
| resourceId | Azure 구독 ID 및 네임 스페이스 이름을 포함 하는 내부 리소스 ID |
| keyVault | Key Vault 리소스의 이름입니다. |
| key | Key Vault 키의 이름입니다. |
| 버전 | Key Vault 키의 버전 |
| operation(작업) | 요청을 처리 하기 위해 수행 된 작업의 이름입니다. |
| code | 상태 코드 |
| message | 메시지-오류 또는 정보 메시지에 대 한 세부 정보를 제공 합니다. |



## <a name="next-steps"></a>다음 단계
- [Event Hubs 소개](event-hubs-what-is-event-hubs.md)
- [Event Hubs API 개요](event-hubs-api-overview.md)
- Event Hubs 시작
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
