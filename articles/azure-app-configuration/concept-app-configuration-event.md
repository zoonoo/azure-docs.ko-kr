---
title: Azure App Configuration 키-값 이벤트에 대응
description: Azure Event Grid를 사용하여 App Configuration 이벤트를 구독하면 복잡한 코드 없이 애플리케이션이 키-값의 변경에 대응할 수 있습니다.
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 02/20/2020
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: 12a62bbd06cf9adf3b5978bb061e1d014599b44c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99550745"
---
# <a name="reacting-to-azure-app-configuration-events"></a>Azure App Configuration 이벤트에 대응

Azure App Configuration 이벤트를 사용하면 애플리케이션이 키-값의 변경에 대응할 수 있습니다. 복잡한 코드나 비용이 많이 들고 비효율적인 폴링 서비스가 없어도 가능합니다. 대신 이벤트는 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)를 통해 [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)와 같은 구독자 또는 사용자 지정 HTTP 수신기로도 푸시됩니다. 중요한 것은 사용한 양만큼만 요금을 지불한다는 것입니다.

Azure App Configuration 이벤트는 다양한 다시 시도 정책 및 배달 못한 편지를 통해 애플리케이션에 신뢰할 수 있는 배달 서비스를 제공하는 Azure Event Grid 서비스에 전송됩니다. 자세한 내용은 [Event Grid 메시지 배달 및 다시 시도](../event-grid/delivery-and-retry.md)를 참조하세요.

일반적인 App Configuration 이벤트 시나리오에는 애플리케이션 구성 새로 고침, 배포 트리거 또는 모든 구성 지향 워크플로가 포함됩니다. 변경 빈도가 낮더라도 즉각적인 대응이 필요한 시나리오에서는 이벤트 기반 아키텍처가 특히 효율적일 수 있습니다.

빠른 예제를 보려면 [데이터 변경 알림에 Event Grid 사용](./howto-app-configuration-event.md)을 살펴보세요. 

![Event Grid 모델](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>사용 가능한 Azure App Configuration 이벤트
Event Grid는 [이벤트 구독](../event-grid/concepts.md#event-subscriptions)을 사용하여 이벤트 메시지를 구독자에게 라우팅합니다. Azure App Configuration 이벤트 구독에는 다음 두 가지 유형의 이벤트가 포함될 수 있습니다.  

> |이벤트 이름|설명|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|키-값이 만들어지거나 바뀔 때 발생합니다.|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|키-값이 삭제될 때 발생합니다.|

## <a name="event-schema"></a>이벤트 스키마
Azure App Configuration 이벤트에는 데이터 변경에 대응하는 데 필요한 모든 정보가 포함되어 있습니다. eventType 속성이 ‘Microsoft AppConfiguration’으로 시작하므로 App Configuration 이벤트를 식별할 수 있습니다. Event Grid 이벤트 속성 사용에 대한 추가 정보는 [Event Grid 이벤트 스키마](../event-grid/event-schema.md)에 설명되어 있습니다.  

> |속성|유형|Description|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |토픽|문자열|이벤트를 내보내는 App Configuration 이벤트의 전체 Azure Resource Manager ID입니다.|
> |subject|문자열|이벤트의 주체인 키-값의 URI입니다.|
> |eventTime|문자열|이벤트가 생성된 날짜/시간입니다(ISO 8601 형식).|
> |eventType|문자열|‘Microsoft.AppConfiguration.KeyValueModified’ 또는 ‘Microsoft.AppConfiguration.KeyValueDeleted’입니다.|
> |Id|문자열|이 이벤트의 고유 식별자입니다.|
> |dataVersion|문자열|데이터 개체의 스키마 버전입니다.|
> |metadataVersion|문자열|최상위 속성의 스키마 버전입니다.|
> |데이터|object|Azure App Configuration 관련 이벤트 데이터의 컬렉션입니다.|
> |data.key|문자열|수정되거나 삭제된 키-값의 키입니다.|
> |data.label|문자열|수정되거나 삭제된 키-값의 레이블(있는 경우)입니다.|
> |data.etag|문자열|`KeyValueModified`의 경우 새 키-값의 etag입니다. `KeyValueDeleted`의 경우 삭제된 키-값의 etag입니다.|

다음은 KeyValueModified 이벤트의 예입니다.
```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]

```

자세한 내용은 [Azure App Configuration 이벤트 스키마](../event-grid/event-schema-app-configuration.md)를 참조하세요.

## <a name="practices-for-consuming-events"></a>이벤트 사용에 관한 지침
App Configuration 이벤트를 처리하는 애플리케이션은 다음과 같은 권장 사항을 따라야 합니다.
> [!div class="checklist"]
> * 이벤트를 동일한 이벤트 처리기로 라우팅하도록 여러 구독을 구성할 수 있습니다. 따라서 이벤트가 특정 원본에서 제공되었다고 가정하지 마세요. 대신 메시지의 토픽을 확인하여 App Configuration 인스턴스가 이벤트를 전송하는지 확인합니다.
> * eventType을 확인하고 수신하는 모든 이벤트가 예상하는 형식일 것이라고 가정하지 마세요.
> * 개체에 관한 정보가 여전히 최신 상태인지 알아보려면 etag 필드를 사용합니다.  
> * sequencer 필드를 사용하여 특정 개체에 대한 이벤트 순서를 파악합니다.
> * subject 필드를 사용하여 수정된 키-값에 액세스합니다.


## <a name="next-steps"></a>다음 단계

Event Grid에 대해 자세히 알아보고 Azure App Configuration 이벤트를 사용해 보세요.

- [Event Grid 정보](../event-grid/overview.md)
- [데이터 변경 알림에 Event Grid 사용](./howto-app-configuration-event.md)