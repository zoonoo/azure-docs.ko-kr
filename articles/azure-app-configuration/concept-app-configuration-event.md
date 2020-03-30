---
title: Azure 앱 구성 키-값 이벤트에 대한 반응
description: Azure 이벤트 그리드를 사용하여 앱 구성 이벤트를 구독합니다.
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 02/20/2020
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: a4f61d147ba1abf73ada6360b8d0d965d8e063a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523801"
---
# <a name="reacting-to-azure-app-configuration-events"></a>Azure 앱 구성 이벤트에 대한 반응

Azure 앱 구성 이벤트를 사용하면 응용 프로그램이 키-값의 변경에 반응할 수 있습니다. 이 작업은 복잡한 코드 나 비용이 많이 들고 비효율적인 폴링 서비스가 필요없이 수행됩니다. 대신 이벤트는 Azure [Event Grid를](https://azure.microsoft.com/services/event-grid/) 통해 [Azure Functions,](https://azure.microsoft.com/services/functions/) [Azure 논리 앱](https://azure.microsoft.com/services/logic-apps/)또는 사용자 지정 http 리스너와 같은 구독자에게 푸시됩니다. 비판적으로, 당신은 당신이 사용하는 것에 대해서만 지불합니다.

Azure 앱 구성 이벤트는 Azure Event Grid로 전송되며, 이 이벤트는 풍부한 재시도 정책과 배달 못한 배달을 통해 응용 프로그램에 안정적인 배달 서비스를 제공합니다. 자세한 내용은 [이벤트 그리드 메시지 배달 및 다시 시도를](https://docs.microsoft.com/azure/event-grid/delivery-and-retry)참조하세요.

일반적인 앱 구성 이벤트 시나리오에는 응용 프로그램 구성 새로 고침, 배포 트리거 또는 구성 지향 워크플로가 포함됩니다. 변경 빈도가 낮더라도 즉각적인 대응이 필요한 시나리오에서는 이벤트 기반 아키텍처가 특히 효율적일 수 있습니다.

Azure 앱 [구성 이벤트 라우팅을 사용자 지정 웹 끝점인 CLI로](./howto-app-configuration-event.md) 이동하여 간단한 예제를 살펴보겠습니다. 

![Event Grid 모델](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>사용 가능한 Azure 앱 구성 이벤트
Event Grid는 [이벤트 구독](../event-grid/concepts.md#event-subscriptions)을 사용하여 이벤트 메시지를 구독자에게 라우팅합니다. Azure 앱 구성 이벤트 구독에는 두 가지 유형의 이벤트가 포함될 수 있습니다.  

> |이벤트 이름|설명|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|키-값을 만들거나 대체할 때 발생합니다.|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|키-값이 삭제될 때 발생|

## <a name="event-schema"></a>이벤트 스키마
Azure 앱 구성 이벤트에는 데이터 변경 사항에 응답하는 데 필요한 모든 정보가 포함됩니다. eventType 속성이 "Microsoft.AppConfiguration"로 시작하기 때문에 앱 구성 이벤트를 식별할 수 있습니다. Event Grid 이벤트 속성 사용에 대한 추가 정보는 [Event Grid 이벤트 스키마](../event-grid/event-schema.md)에 설명되어 있습니다.  

> |속성|Type|Description|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |토픽|문자열|이벤트를 내하는 앱 구성의 전체 Azure 리소스 관리자 ID입니다.|
> |subject|문자열|이벤트의 주체인 키 값의 URI입니다.|
> |eventTime|문자열|이벤트가 생성된 날짜/시간(ISO 8601 형식)입니다.|
> |eventType|문자열|"마이크로소프트.AppConfiguration.KeyValue수정" 또는 "마이크로소프트.AppConfiguration.KeyValueDeleted".|
> |Id|문자열|이 이벤트의 고유 식별자입니다.|
> |dataVersion|문자열|데이터 개체의 스키마 버전입니다.|
> |metadataVersion|문자열|최상위 속성의 스키마 버전입니다.|
> |데이터|object|Azure 앱 구성 특정 이벤트 데이터 컬렉션|
> |데이터.키|문자열|수정또는 삭제된 키-값의 키입니다.|
> |데이터.라벨|문자열|수정 또는 삭제된 키 값의 레이블(있는 경우)입니다.|
> |데이터.etag|문자열|새 `KeyValueModified` 키 값의 etag에 대 한 입니다. 삭제된 키 값의 etag에 해당합니다. `KeyValueDeleted`|

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

자세한 내용은 [Azure 앱 구성 이벤트 스키마를](../event-grid/event-schema-app-configuration.md)참조하십시오.

## <a name="practices-for-consuming-events"></a>이벤트 사용에 관한 지침
앱 구성 이벤트를 처리하는 응용 프로그램은 다음 권장 방법을 따라야 합니다.
> [!div class="checklist"]
> * 이벤트를 동일한 이벤트 처리기로 라우팅하도록 여러 구독을 구성할 수 있으므로 이벤트가 특정 소스에서 온 것이라고 가정하지 마십시오. 대신 메시지 항목을 확인하여 이벤트를 보내는 앱 구성 인스턴스가 있는지 확인합니다.
> * eventType을 확인하고 수신하는 모든 이벤트가 예상되는 형식이라고 가정하지 않습니다.
> * etag 필드를 사용하여 개체에 대한 정보가 여전히 최신 상태인지 확인합니다.  
> * 시퀀서 필드를 사용하여 특정 개체의 이벤트 순서를 이해합니다.
> * 제목 필드를 사용하여 수정된 키 값에 액세스합니다.


## <a name="next-steps"></a>다음 단계

이벤트 그리드에 대해 자세히 알아보고 Azure 앱 구성 이벤트를 사용해 보십시오.

- [Event Grid 정보](../event-grid/overview.md)
- [Azure 앱 구성 이벤트를 사용자 지정 웹 끝점으로 라우팅](./howto-app-configuration-event.md)