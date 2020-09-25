---
title: 이벤트 처리
titleSuffix: An Azure Communication Services concept document
description: Azure Event Grid를 사용하여 Communication Service에서 발생하는 작업을 기반으로 프로세스를 트리거합니다.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 697e0f7031e55cd924352fe1e1fdbd480f8e411b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90945768"
---
# <a name="event-handling-in-azure-communication-services"></a>Azure Communication Services에서 이벤트 처리

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Azure Communication Services는 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)와 통합되어 안정적이고 확장 가능하며 안전한 방식으로 실시간 이벤트 알림을 제공합니다. 이 문서의 목적은 Communication Services 이벤트를 수신하도록 애플리케이션을 구성하는 데 도움을 주는 것입니다. 예를 들어, Communication Services 리소스와 연결된 전화 번호로 SMS 메시지를 받을 때마다 데이터베이스를 업데이트하고, 작업 항목을 만들고, 푸시 알림을 제공할 수 있습니다.

Azure Event Grid는 게시-구독 모델을 사용하는 완전 관리형 이벤트 라우팅 서비스입니다. Event Grid에는 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) 및 [Azure Logic Apps](https://docs.microsoft.com/azure/azure-functions/functions-overview)와 같은 Azure 서비스에 대한 지원이 기본적으로 제공됩니다. 웹후크를 사용하여 비 Azure 서비스에 이벤트 경고를 제공할 수 있습니다. Azure Event Grid가 지원하는 이벤트 처리기의 전체 목록은 [Azure Event Grid 소개](https://docs.microsoft.com/azure/event-grid/overview)를 참조하세요.

:::image type="content" source="https://docs.microsoft.com/azure/event-grid/media/overview/functional-model.png" alt-text="Azure Event Grid의 이벤트 모델을 보여 주는 다이어그램.":::

## <a name="events-types"></a>이벤트 유형

Event Grid는 [이벤트 구독](https://docs.microsoft.com/azure/event-grid/concepts#event-subscriptions)을 사용하여 이벤트 메시지를 구독자에게 라우팅합니다. 

Azure Communication Services에서 내보내는 이벤트 유형은 다음과 같습니다.

| 이벤트 유형                                                  | 설명                                                                                    |
| ----------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| Microsoft.Communication.SMSReceived                         | Communication Service와 연결된 전화 번호로 SMS를 받을 때 게시됩니다. |
| Microsoft.Communication.SMSDeliveryReportReceived           | Communication Service에서 보낸 SMS에 대해 배달 보고서를 받을 때 게시됩니다.     |
| Microsoft.Communication.ChatMessageReceived                 | 자신이 속한 채팅 스레드에서 사용자에 대한 메시지를 받을 때 게시됩니다.        |
| Microsoft.Communication.ChatMessageEdited                   | 자신이 속한 채팅 스레드에서 메시지가 편집될 때 게시됩니다.                |
| Microsoft.Communication.ChatMessageDeleted                  | 자신이 속한 채팅 스레드에서 메시지가 삭제될 때 게시됩니다.               |
| Microsoft.Communication.ChatThreadCreatedWithUser           | 채팅 스레드를 만들 때 사용자가 멤버로 추가되면 게시됩니다.           |
| Microsoft.Communication.ChatThreadWithUserDeleted           | 자신이 속한 채팅 스레드가 삭제되면 게시됩니다.                           |
| Microsoft.Communication.ChatThreadPropertiesUpdatedPerUser  | 자신이 속한 채팅 스레드의 속성이 업데이트되면 게시됩니다.              |
| Microsoft.Communication.ChatMemberAddedToThreadWithUser     | 채팅 스레드에 사용자가 멤버로 추가되면 게시됩니다.                                   |
| Microsoft.Communication.ChatMemberRemovedFromThreadWithUser | 채팅 스레드에서 사용자가 제거되면 게시됩니다.                                         |

Azure Portal 또는 Azure CLI를 사용하여 Communication Services 리소스에서 내보낸 이벤트를 구독할 수 있습니다. [Communication Services에서 SMS 이벤트를 처리하는 방법](../quickstarts/telephony-sms/handle-sms-events.md)을 확인하여 이벤트 처리를 시작합니다.

## <a name="event-subjects"></a>이벤트 제목

모든 Communication Services 이벤트의 `subject` 필드는 이벤트의 대상으로 지정된 사용자, 전화 번호 또는 엔터티를 식별합니다. 일반적인 접두사는 단순한 [Event Grid 필터링](https://docs.microsoft.com/azure/event-grid/event-filtering)을 허용하는 데 사용됩니다.

| 제목 접두사                              | Communication Service 엔터티 |
| ------------------------------------------- | ---------------------------- |
| `phonenumber/`                              | PSTN 전화 번호            |
| `user/`                                     | Communication Services 사용자  |
| `thread/`                                   | 채팅 스레드.                 |

다음 예제에서는 Communication Services 리소스에서 소유한 전체 555개 지역 전화 번호로 전송되는 모든 SMS 메시지 및 배달 보고서에 대한 필터를 보여 줍니다.

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Communication.SMSReceived",
    "Microsoft.Communication.SMSDeliveryReportReceived"
  ],
  "subjectBeginsWith": "phonenumber/1555",
}
```

## <a name="sample-event-responses"></a>샘플 이벤트 응답

이벤트가 트리거될 때 Event Grid 서비스는 해당 이벤트에 대한 데이터를 구독 엔드포인트로 보냅니다.

이 섹션에는 각 이벤트에 대한 데이터가 어떻게 표시되는지 예가 포함되어 있습니다.

### <a name="microsoftcommunicationsmsdeliveryreportreceived-event"></a>Microsoft.Communication.SMSDeliveryReportReceived event

```json
[{
  "id": "Outgoing_202009180022138813a09b-0cbf-4304-9b03-1546683bb910",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
  "subject": "/phonenumber/15555555555",
  "data": {
    "MessageId": "Outgoing_202009180022138813a09b-0cbf-4304-9b03-1546683bb910",
    "From": "15555555555",
    "To": "+15555555555",
    "DeliveryStatus": "Delivered",
    "DeliveryStatusDetails": "No error.",
    "ReceivedTimestamp": "2020-09-18T00:22:20.2855749Z",
    "DeliveryAttempts": [
      {
        "Timestamp": "2020-09-18T00:22:14.9315918Z",
        "SegmentsSucceeded": 1,
        "SegmentsFailed": 0
      }
    ]
  },
  "eventType": "Microsoft.Communication.SMSDeliveryReportReceived",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:22:20Z"
}]
```
### <a name="microsoftcommunicationsmsreceived-event"></a>Microsoft.Communication.SMSReceived event

```json
[{
  "id": "Incoming_20200918002745d29ebbea-3341-4466-9690-0a03af35228e",
  "topic": "/subscriptions/50ad1522-5c2c-4d9a-a6c8-67c11ecb75b8/resourcegroups/acse2e/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
  "subject": "/phonenumber/15555555555",
  "data": {
    "MessageId": "Incoming_20200918002745d29ebbea-3341-4466-9690-0a03af35228e",
    "From": "15555555555",
    "To": "15555555555",
    "Message": "Great to connect with ACS events ",
    "ReceivedTimestamp": "2020-09-18T00:27:45.32Z"
  },
  "eventType": "Microsoft.Communication.SMSReceived",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:27:47Z"
}]
```

### <a name="microsoftcommunicationchatmessagereceived-event"></a>Microsoft.Communication.ChatMessageReceived event

```json
[{
  "id": "c13afb5f-d975-4296-a8ef-348c8fc496ee",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/{thread-id}/sender/{id-of-message-sender}/recipient/{id-of-message-recipient}",
  "data": {
    "messageBody": "Welcome to Azure Communication Services",
    "messageId": "1600389507167",
    "senderId": "8:acs:fac4607d-d2d0-40e5-84df-6f32ebd1251a_00000005-3e0d-e5aa-0e04-343a0d00037c",
    "senderDisplayName": "John",
    "composeTime": "2020-09-18T00:38:27.167Z",
    "type": "Text",
    "version": 1600389507167,
    "recipientId": "8:acs:fac4607d-d2d0-40e5-84df-6f32ebd1251a_00000005-3e1a-3090-6a0b-343a0d000409",
    "transactionId": "WGW1YmwRzkupk0UI0QA9ZA.1.1.1.1.1797783722.1.9",
    "threadId": "19:46df844a4c064bfaa2b3b30e385d1018@thread.v2"
  },
  "eventType": "Microsoft.Communication.ChatMessageReceived",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:38:28.0946757Z"
}
]
```

### <a name="microsoftcommunicationchatmessageedited-event"></a>Microsoft.Communication.ChatMessageEdited event

```json
[{
  "id": "18247662-e94a-40cc-8d2f-f7357365309e",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2/sender/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe/recipient/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
  "data": {
    "editTime": "2020-09-18T00:48:47.361Z",
    "messageBody": "Let's Chat about new communication services.",
    "messageId": "1600390097873",
    "senderId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe",
    "senderDisplayName": "Bob(Admin)",
    "composeTime": "2020-09-18T00:48:17.873Z",
    "type": "Text",
    "version": 1600390127361,
    "recipientId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
    "transactionId": "bbopOa1JZEW5NDDFLgH1ZQ.2.1.2.1.1822032097.1.5",
    "threadId": "19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2"
  },
  "eventType": "Microsoft.Communication.ChatMessageEdited",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:48:48.037823Z"
}]
```

### <a name="microsoftcommunicationchatmessagedeleted-event"></a>Microsoft.Communication.ChatMessageDeleted event
```json
[{
  "id": "08034616-cf11-4fc2-b402-88963b93d083",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2/sender/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe/recipient/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
  "data": {
    "deleteTime": "2020-09-18T00:48:47.361Z",
    "messageId": "1600390099195",
    "senderId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe",
    "senderDisplayName": "Bob(Admin)",
    "composeTime": "2020-09-18T00:48:19.195Z",
    "type": "Text",
    "version": 1600390152154,
    "recipientId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
    "transactionId": "mAxUjeTsG06NpObXkFcjVQ.1.1.2.1.1823015063.1.5",
    "threadId": "19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2"
  },
  "eventType": "Microsoft.Communication.ChatMessageDeleted",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:49:12.6698791Z"
}]
```

### <a name="microsoftcommunicationchatthreadcreatedwithuser-event"></a>Microsoft.Communication.ChatThreadCreatedWithUser event 

```json
[{
  "id": "06c7c381-bb0a-4fff-aedd-919df1d52137",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/19:7bdf5504a23f41a79d1bd472dd40044a@thread.v2/createdBy/8:acs:73551687-f8c8-48a7-bf06-d8263f15b02a_00000005-3e5f-1bc6-f40f-343a0d0003fe/recipient/8:acs:73551687-f8c8-48a7-bf06-d8263f15b02a_00000005-3e5f-1bc6-f40f-343a0d0003f0",
  "data": {
    "createdBy": "8:acs:73551687-f8c8-48a7-bf06-d8263f15b02a_06014f-6001fc107f",
    "properties": {
      "topic": "Chat about new commuication services",
    },
    "members": [
      {
        "displayName": "Bob",
        "memberId": "8:acs:73551687-f8c8-48a7-bf06-d8263f15b02a_00000005-3e5f-1bc6-f40f-343a0d0003f0"
      },
      {
        "displayName": "John",
        "memberId": "8:acs:73551687-f8c8-48a7-bf06-d8263f15b02a_00000005-3e5f-1bc6-f40f-343a0d0003f1"
      }
    ],
    "createTime": "2020-09-17T22:06:09.988Z",
    "version": 1600380369988,
    "recipientId": "8:acs:73551687-f8c8-48a7-bf06-d8263f15b02a_00000005-3e5f-1bc6-f40f-343a0d0003f0",
    "transactionId": "9ZxrGXVXCkOTygd5iwsvAQ.1.1.1.1.1440874720.1.1",
    "threadId": "19:7bdf5504a23f41a79d1bd472dd40044a@thread.v2"
  },
  "eventType": "Microsoft.Communication.ChatThreadCreatedWithUser",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-17T22:06:10.3235137Z"
}]
```

### <a name="microsoftcommunicationchatthreadwithuserdeleted-event"></a>Microsoft.Communication.ChatThreadWithUserDeleted event

```json
[{
  "id": "7f4fa31b-e95e-428b-a6e8-53e2553620ad",
  "topic":"/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2/deletedBy/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe/recipient/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
  "data": {
    "deletedBy": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe",
    "deleteTime": "2020-09-18T00:49:26.3694459Z",
    "createTime": "2020-09-18T00:46:41.559Z",
    "version": 1600390071625,
    "recipientId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
    "transactionId": "MoZlSM2j7kSD2b5X8bjH7Q.1.1.2.1.1823539230.1.1",
    "threadId": "19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2"
  },
  "eventType": "Microsoft.Communication.ChatThreadWithUserDeleted",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:49:26.4269056Z"
}]
```

### <a name="microsoftcommunicationchatthreadpropertiesupdatedperuser-event"></a>Microsoft.Communication.ChatThreadPropertiesUpdatedPerUser event 

```json
[{
  "id": "47a66834-57d7-4f77-9c7d-676d45524982",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/19:a33a128babf04431b7fe8cbca82f4238@thread.v2/editedBy/8:acs:fac4607d-d2d0-40e5-84df-6f32ebd1251a_00000005-3e88-2b7f-ac00-343a0d0005a8/recipient/8:acs:fac4607d-d2d0-40e5-84df-6f32ebd1251a_00000005-3e88-15fa-ac00-343a0d0005a7",
  "data": {
    "editedBy": "8:acs:fac4607d-d2d0-40e5-84df-6f32ebd1251a_00000005-3e88-2b7f-ac00-343a0d0005a8",
    "editTime": "2020-09-18T00:40:38.4914428Z",
    "properties": {
      "topic": "Communication in Azure"
    },
    "createTime": "2020-09-18T00:39:02.541Z",
    "version": 1600389638481,
    "recipientId": "8:acs:fac4607d-d2d0-40e5-84df-6f32ebd1251a_00000005-3e88-15fa-ac00-343a0d0005a7",
    "transactionId": "+ah9tVwqNkCT6nUGCKIvAg.1.1.1.1.1802895561.1.1",
    "threadId": "19:a33a128babf04431b7fe8cbca82f4238@thread.v2"
  },
  "eventType": "Microsoft.Communication.ChatThreadPropertiesUpdatedPerUser",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:40:38.5804349Z"
}]
```

### <a name="microsoftcommunicationchatmemberaddedtothreadwithuser-event"></a>Microsoft.Communication.ChatMemberAddedToThreadWithUser event

```json
[{
  "id": "4abd2b49-d1a9-4fcc-9cd7-170fa5d96443",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2/memberAdded/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe/recipient/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
  "data": {
    "time": "2020-09-18T00:47:13.1867087Z",
    "addedBy": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f1",
    "memberAdded": {
      "displayName": "John Smith",
      "memberId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe"
    },
    "createTime": "2020-09-18T00:46:41.559Z",
    "version": 1600390033176,
    "recipientId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
    "transactionId": "pVIjw/pHEEKUOUJ2DAAl5A.1.1.1.1.1818361951.1.1",
    "threadId": "19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2"
  },
  "eventType": "Microsoft.Communication.ChatMemberAddedToThreadWithUser",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:47:13.2342692Z"
}]
```

### <a name="microsoftcommunicationchatmemberremovedfromthreadwithuser-event"></a>Microsoft.Communication.ChatMemberRemovedFromThreadWithUser event

```json
[{
  "id": "b3701976-1ea2-4d66-be68-4ec4fc1b4b96",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2/memberRemoved/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe/recipient/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
  "data": {
    "time": "2020-09-18T00:47:51.1461742Z",
    "removedBy": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f1",
    "memberRemoved": {
      "displayName": "John",
      "memberId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe"
    },
    "createTime": "2020-09-18T00:46:41.559Z",
    "version": 1600390071131,
    "recipientId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
    "transactionId": "G9Y+UbjVmEuxAG3O4bEyvw.1.1.1.1.1819803816.1.1",
    "threadId": "19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2"
  },
  "eventType": "Microsoft.Communication.ChatMemberRemovedFromThreadWithUser",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:47:51.2244511Z"
}]
```

## <a name="quickstarts-and-how-tos"></a>빠른 시작 및 방법

| 제목 | 설명 |
| --- | --- |
| [Communication Services에서 SMS 이벤트를 처리하는 방법](../quickstarts/telephony-sms/handle-sms-events.md) | 웹후크를 사용하여 Communication Services에서 받은 모든 SMS 이벤트를 처리합니다. |


## <a name="next-steps"></a>다음 단계

* Azure Event Grid에 대한 소개는 [Event Grid란?](https://docs.microsoft.com/azure/event-grid/overview)을 참조하세요.
* Azure Event Grid 개념에 대한 소개는 [Event Grid란?](https://docs.microsoft.com/azure/event-grid/concepts)을 참조하세요.
* Azure Event Grid 시스템 토픽에 대한 소개는 [Azure Event Grid의 시스템 토픽](https://docs.microsoft.com/azure/event-grid/system-topics)을 참조하세요.
