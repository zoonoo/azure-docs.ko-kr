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
ms.openlocfilehash: 0fb5da965a9b13667b8a128e83a5a4cd2c2b28d7
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691852"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Azure 이벤트 허브에 대한 진단 로그 설정

Azure Event Hubs에 대해 다음 두 가지 유형의 로그를 볼 수 있습니다.

* **[활동 로그](../azure-monitor/platform/platform-logs-overview.md)**: 이러한 로그에는 작업에 대해 수행 된 작업에 대 한 정보가 포함 됩니다. 로그는 항상 켜져 있습니다. Azure Portal에서 이벤트 허브 네임 스페이스의 왼쪽 창에서 **활동 로그** 를 선택 하 여 활동 로그 항목을 볼 수 있습니다. 예: "네임 스페이스 만들기 또는 업데이트", "이벤트 허브 만들기 또는 업데이트"

    ![Event Hubs 네임 스페이스에 대 한 활동 로그](./media/event-hubs-diagnostic-logs/activity-log.png)
* **[진단 로그](../azure-monitor/platform/platform-logs-overview.md)**: 진단 로그는 API를 사용 하거나 언어 SDK의 관리 클라이언트를 통해 네임 스페이스에 대해 수행 되는 작업 및 작업에 대 한 다양 한 정보를 제공 합니다. 
    
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

| 범주 | Description | 
| -------- | ----------- | 
| 보관 로그 | 캡처 작업과 관련 된 로그 [Event Hubs 캡처](event-hubs-capture-overview.md) 작업에 대 한 정보를 캡처합니다. |
| 작업 로그 | Azure Event Hubs 네임 스페이스에서 수행 되는 모든 관리 작업을 캡처합니다. Azure Event Hubs에서 수행 되는 대량의 데이터 작업으로 인해 데이터 작업이 캡처되지 않습니다. |
| 로그 자동 크기 조정 | Event Hubs 네임 스페이스에 대해 수행 되는 자동 확장 작업을 캡처합니다. |
| Kafka 코디네이터 로그 | Event Hubs와 관련 된 Kafka 코디네이터 작업을 캡처합니다. |
| Kafka 사용자 오류 로그 | Event Hubs에서 호출 되는 Kafka Api에 대 한 정보를 캡처합니다. |
| VNet (가상 네트워크) 연결 이벤트 Event Hubs | Event Hubs으로 트래픽을 전송 하는 IP 주소 및 가상 네트워크에 대 한 정보를 캡처합니다. |
| 고객 관리 키 사용자 로그 | 고객이 관리 하는 키와 관련 된 작업을 캡처합니다. |


모든 로그는 JSON(JavaScript Object Notation) 형식으로 저장됩니다. 각 항목에는 다음 섹션에 설명된 형식을 사용하는 문자열 필드가 있습니다.

## <a name="archive-logs-schema"></a>보관 로그 스키마

보관 로그 JSON 문자열에는 다음 표에 나열된 요소가 포함되어 있습니다.

Name | Description
------- | -------
TaskName | 실패한 작업에 대한 설명
ActivityId | 추적에 사용되는 내부 ID
trackingId | 추적에 사용되는 내부 ID
resourceId | Azure Resource Manager 리소스 ID
eventHub | 이벤트 허브 전체 이름(네임스페이스 이름 포함)
partitionId | 기록 되는 이벤트 허브 파티션
archiveStep | 가능한 값: ArchiveFlushWriter, DestinationInit
startTime | 실패 시작 시간
failures | 오류가 발생 한 횟수
durationInSeconds | 실패 기간
message | 오류 메시지
category | ArchiveLogs

다음 코드는 보관 로그 JSON 문자열에 대한 예입니다.

```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "000000000-0000-0000-0000-0000000000000",
   "trackingId": "0000000-0000-0000-0000-00000000000000000",
   "resourceId": "/SUBSCRIPTIONS/000000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs Namespace Name>",
   "eventHub": "<Event Hub full name>",
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

Name | Description
------- | -------
ActivityId | 추적 목적으로 사용 되는 내부 ID |
EventName | 작업 이름 |
resourceId | Azure Resource Manager 리소스 ID |
SubscriptionId | 구독 ID |
EventTimeString | 작업 시간 |
EventProperties | 작업 속성 |
상태 | 작업 상태 |
Caller | 작업 호출자(Azure Portal 또는 관리 클라이언트) |
범주 | OperationalLogs |

다음 코드는 작업 로그 JSON 문자열에 대한 예입니다.

```json
Example:
{
   "ActivityId": "00000000-0000-0000-0000-00000000000000",
   "EventName": "Create EventHub",
   "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs namespace name>",
   "SubscriptionId": "000000000-0000-0000-0000-000000000000",
   "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
   "EventProperties": "{\"SubscriptionId\":\"0000000000-0000-0000-0000-000000000000\",\"Namespace\":\"<Namespace Name>\",\"Via\":\"https://<Namespace Name>.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
   "Status": "Succeeded",
   "Caller": "ServiceBus Client",
   "category": "OperationalLogs"
}
```

## <a name="autoscale-logs-schema"></a>자동 크기 조정 로그 스키마
자동 크기 조정 로그 JSON에는 다음 표에 나열 된 요소가 포함 되어 있습니다.

| Name | Description |
| ---- | ----------- | 
| TrackingId | 추적 목적으로 사용 되는 내부 ID |
| ResourceId | Azure Resource Manager 리소스 ID입니다. |
| 메시지 | 자동 팽창 동작에 대 한 세부 정보를 제공 하는 정보 메시지입니다. 이 메시지에는 지정 된 네임 스페이스에 대 한 이전 및 현재 처리량 단위 값과 TU의 확장을 트리거한 항목이 포함 되어 있습니다. |

## <a name="kafka-coordinator-logs-schema"></a>Kafka 코디네이터 로그 스키마
Kafka 코디네이터 로그 JSON에는 다음 표에 나열 된 요소가 포함 되어 있습니다.

| Name | Description |
| ---- | ----------- | 
| RequestId | 추적 목적으로 사용 되는 요청 ID |
| ResourceId | Azure Resource Manager 리소스 ID |
| 작업(Operation) | 그룹 조정을 수행 하는 동안 수행 되는 작업의 이름입니다. |
| clientid | 클라이언트 ID |
| NamespaceName | 네임스페이스 이름 | 
| SubscriptionId | Azure 구독 ID |
| 메시지 | 그룹 조정 중 수행 된 작업에 대 한 세부 정보를 제공 하는 정보 또는 경고 메시지입니다. |

### <a name="example"></a>예

```json
{
    "RequestId": "FE01001A89E30B020000000304620E2A_KafkaExampleConsumer#0",
    "Operation": "Join.Start",
    "ClientId": "KafkaExampleConsumer#0",
    "Message": "Start join group for new member namespace-name:c:$default:I:KafkaExampleConsumer#0-cc40856f7f3c4607915a571efe994e82, current group size: 0, API version: 2, session timeout: 10000ms, rebalance timeout: 300000ms.",
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "KafkaCoordinatorLogs"
}
```

## <a name="kafka-user-error-logs-schema"></a>Kafka 사용자 오류 로그 스키마
Kafka 사용자 오류 로그 JSON에는 다음 표에 나열 된 요소가 포함 되어 있습니다.

| Name | Description |
| ---- | ----------- |
| TrackingId | 추적에 사용 되는 추적 ID입니다. |
| NamespaceName | 네임스페이스 이름 |
| Eventhub | 이벤트 허브 이름 |
| PartitionId | Partition ID |
| GroupId | 그룹 ID |
| clientid | 클라이언트 ID |
| ResourceId | Azure Resource Manager 리소스 ID입니다. |
| 메시지 | 오류에 대 한 세부 정보를 제공 하는 정보 메시지 |

## <a name="event-hubs-virtual-network-connection-event-schema"></a>Event Hubs 가상 네트워크 연결 이벤트 스키마

VNet (가상 네트워크) 연결 이벤트 JSON에는 다음 표에 나열 된 요소가 포함 되어 Event Hubs.

| Name | Description |
| ---  | ----------- | 
| SubscriptionId | Azure 구독 ID |
| NamespaceName | 네임스페이스 이름 |
| IPAddress | Event Hubs 서비스에 연결 하는 클라이언트의 IP 주소 |
| 작업 | 연결 요청을 평가할 때 Event Hubs 서비스에서 수행 하는 작업입니다. 지원 되는 작업은 **연결 허용** 및 **연결 거부**입니다. |
| 이유 | 작업이 완료 된 이유를 제공 합니다. |
| 개수 | 지정 된 동작의 발생 횟수 |
| ResourceId | Azure Resource Manager 리소스 ID입니다. |

### <a name="example"></a>예

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

## <a name="customer-managed-key-user-logs"></a>고객 관리 키 사용자 로그
고객 관리 키 사용자 로그 JSON에는 다음 표에 나열 된 요소가 포함 되어 있습니다.

| Name | Description |
| ---- | ----------- | 
| 범주 | 메시지의 범주 유형입니다. 다음 값 중 하나입니다. **오류** 및 **정보** |
| ResourceId | Azure 구독 ID 및 네임 스페이스 이름을 포함 하는 내부 리소스 ID |
| KeyVault | Key Vault 리소스의 이름입니다. |
| Key | Key Vault 키의 이름입니다. |
| 버전 | Key Vault 키의 버전 |
| 작업(Operation) | 요청을 처리 하기 위해 수행 된 작업의 이름입니다. |
| 코드 | 상태 코드 |
| 메시지 | 메시지-오류 또는 정보 메시지에 대 한 세부 정보를 제공 합니다. |



## <a name="next-steps"></a>다음 단계
- [Event Hubs 소개](event-hubs-what-is-event-hubs.md)
- [Event Hubs API 개요](event-hubs-api-overview.md)
- Event Hubs 시작
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
