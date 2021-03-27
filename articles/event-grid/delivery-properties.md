---
title: Azure Event Grid-전달 된 이벤트에 대 한 사용자 지정 헤더 설정
description: 전달 된 이벤트에 대 한 사용자 지정 헤더 (또는 배달 속성)를 설정 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: e2eff6b0d1dc78f0d558bb8e4e1ad79c62c52657
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630531"
---
# <a name="delivery-with-custom-headers"></a>사용자 지정 헤더를 사용한 배달
이벤트 구독을 사용 하면 전달 된 이벤트에 포함 된 http 헤더를 설정할 수 있습니다. 이 기능을 사용 하 여 대상에 필요한 사용자 지정 헤더를 설정할 수 있습니다. 이벤트 구독을 만들 때 최대 10 개의 헤더를 설정할 수 있습니다. 각 헤더 값은 4096 (4K) 바이트 보다 크지 않아야 합니다.

다음 대상에 전달 되는 이벤트에 사용자 지정 헤더를 설정할 수 있습니다.

- Webhook
- Azure Service Bus 토픽 및 큐
- Azure Event Hubs
- 릴레이 하이브리드 연결

Azure Portal에서 이벤트 구독을 만들 때 **배달 속성** 탭을 사용 하 여 사용자 지정 http 헤더를 설정할 수 있습니다. 이 페이지에서는 고정 및 동적 헤더 값을 설정할 수 있습니다.

## <a name="setting-static-header-values"></a>정적 헤더 값 설정
고정 값을 사용 하 여 헤더를 설정 하려면 헤더의 이름과 해당 필드에 해당 값을 제공 합니다.

:::image type="content" source="./media/delivery-properties/static-header-property.png" alt-text="배달 속성-정적":::

중요 한 데이터를 제공할 때 확인 **이 비밀 인지** 확인 하는 것이 좋습니다. 중요 한 데이터는 Azure Portal에 표시 되지 않습니다. 

## <a name="setting-dynamic-header-values"></a>동적 헤더 값 설정
들어오는 이벤트의 속성을 기반으로 헤더의 값을 설정할 수 있습니다. JsonPath 구문을 사용 하 여 들어오는 이벤트의 속성 값을 참조 하 여 나가는 요청에서 헤더에 대 한 값으로 사용 합니다. 예를 들어 이벤트 데이터에서 들어오는 이벤트 속성 **시스템** 의 값을 사용 하 여 **Channel** 이라는 헤더의 값을 설정 하려면 다음과 같은 방법으로 이벤트 구독을 구성 합니다.

:::image type="content" source="./media/delivery-properties/dynamic-header-property.png" alt-text="배달 속성-동적":::

## <a name="examples"></a>예제
이 섹션에서는 배달 속성을 사용 하는 몇 가지 예를 제공 합니다.

### <a name="setting-the-authorization-header-with-a-bearer-token-non-normative-example"></a>전달자 토큰을 사용 하 여 인증 헤더 설정 (표준 이외의 예제)

Webhook 처리기를 사용 하 여 요청을 식별 하는 인증 헤더에 값을 설정 합니다. [Azure Active Directory로 Webhook를 보호](secure-webhook-delivery.md)하지 않는 경우 권한 부여 헤더를 설정할 수 있습니다.

| 헤더 이름   | 헤더 형식 | 헤더 값 |
| :--           | :--         | :--            |
|`Authorization` | 정적 | `BEARER SlAV32hkKG...`|

이제 나가는 요청은 이벤트 구독에 설정 된 헤더를 포함 해야 합니다.

```console
GET /home.html HTTP/1.1

Host: acme.com

User-Agent: <user-agent goes here>

Authorization: BEARER SlAV32hkKG...
```

> [!NOTE]
> 대상이 Webhook 인 경우 권한 부여 헤더를 정의 하는 것이 적절 한 옵션입니다. [리소스 id](/rest/api/eventgrid/eventsubscriptions/createorupdate#azurefunctioneventsubscriptiondestination), Service Bus, Event Hubs 및 하이브리드 연결를 구독 하는 함수에는 사용 하면 안 됩니다. 이러한 대상은 Event Grid에서 사용할 때 자체 인증 체계를 지원 하기 때문입니다.

### <a name="service-bus-example"></a>Service Bus 예제
Azure Service Bus에서는 단일 메시지를 보낼 때 [BROKERPROPERTIES HTTP 헤더](/rest/api/servicebus/message-headers-and-properties#message-headers) 를 사용 하 여 메시지 속성을 정의할 수 있습니다. 헤더의 값을 `BrokerProperties` JSON 형식으로 제공 해야 합니다. 예를 들어 Service Bus 단일 메시지를 보낼 때 메시지 속성을 설정 해야 하는 경우 다음과 같은 방법으로 헤더를 설정 합니다.

| 헤더 이름 | 헤더 형식 | 헤더 값 |
| :-- | :-- | :-- |
|`BrokerProperties` | 정적     | `BrokerProperties:  { "MessageId": "{701332E1-B37B-4D29-AA0A-E367906C206E}", "TimeToLive" : 90}` |


### <a name="event-hubs-example"></a>Event Hubs 예제

이벤트 허브 내의 특정 파티션에 이벤트를 게시 해야 하는 경우 이벤트 구독에서 [BROKERPROPERTIES HTTP 헤더](/rest/api/eventhub/event-hubs-runtime-rest#common-headers) 를 정의 하 여 대상 이벤트 허브 파티션을 식별 하는 파티션 키를 지정 합니다.

| 헤더 이름 | 헤더 형식 | 헤더 값                                  |
| :-- | :-- | :-- |
|`BrokerProperties` | 정적 | `BrokerProperties: {"PartitionKey": "0000000000-0000-0000-0000-000000000000000"}`  |


### <a name="configure-time-to-live-on-outgoing-events-to-azure-storage-queues"></a>보내는 이벤트에 대 한 ttl (time-to-live)을 Azure Storage 큐로 구성
Azure Storage 큐 대상의 경우 나가는 메시지가 Azure Storage 큐에 배달 된 후에만 사용할 수 있는 ttl을 구성할 수 있습니다. 제공 된 시간이 없으면 메시지의 기본 ttl (time-to-live)은 7 일입니다. 이벤트를 만료 되지 않도록 설정할 수도 있습니다.

:::image type="content" source="./media/delivery-properties/delivery-properties-storage-queue.png" alt-text="배달 속성-저장소 큐":::

## <a name="next-steps"></a>다음 단계
이벤트 배달에 대 한 자세한 내용은 다음 문서를 참조 하세요.

- [배달 및 다시 시도](delivery-and-retry.md)
- [웹후크 이벤트 전달](webhook-event-delivery.md)
- [이벤트 필터링](event-filtering.md)
