---
title: Azure 앱 구성을 이벤트 그리드 원본으로
description: 이 문서에서는 Azure 앱 구성을 이벤트 그리드 이벤트 원본으로 사용하는 방법을 설명합니다. 스키마와 자습서 및 방법 문서에 대한 링크를 제공합니다.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: babanisa
ms.openlocfilehash: adb548ef8531698a2cb075fbc742bb20a02a434b
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393423"
---
# <a name="azure-app-configuration-as-an-event-grid-source"></a>이벤트 그리드 소스로 Azure 앱 구성
이 문서에서는 Azure 앱 구성 이벤트에 대한 속성 및 스키마를 제공합니다. 이벤트 스키마에 대한 소개는 [Azure Event Grid 이벤트 스키마](event-schema.md)를 참조하세요. 또한 Azure 앱 구성을 이벤트 소스로 사용할 수 있는 빠른 시작 및 자습서 목록도 제공합니다.

## <a name="event-grid-event-schema"></a>Event Grid 이벤트 스키마

### <a name="available-event-types"></a>사용할 수 있는 이벤트 유형

Azure 앱 구성은 다음 이벤트 유형을 내보며 다음을 처리합니다.

| 이벤트 유형 | Description |
| ---------- | ----------- |
| 마이크로소프트.앱 구성.키밸류수정 | 키-값을 만들거나 대체할 때 발생합니다. |
| 마이크로소프트.앱 구성.키밸류삭제 | 키-값이 삭제될 때 발생합니다. |

### <a name="example-event"></a>예제 이벤트

다음 예제에서는 키 값 수정 된 이벤트의 스키마를 보여 주면 됩니다. 

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

키 값 삭제된 이벤트의 스키마는 비슷합니다. 

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
  "eventType": "Microsoft.AppConfiguration.KeyValueDeleted",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```
 
### <a name="event-properties"></a>이벤트 속성

이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 속성 | Type | Description |
| -------- | ---- | ----------- |
| 토픽 | 문자열 | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| subject | 문자열 | 게시자가 정의한 이벤트 주체에 대한 경로입니다. |
| eventType | 문자열 | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| eventTime | 문자열 | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| ID | 문자열 | 이벤트에 대한 고유 식별자입니다. |
| 데이터 | object | 앱 구성 이벤트 데이터입니다. |
| dataVersion | 문자열 | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| metadataVersion | 문자열 | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |

데이터 개체의 속성은 다음과 같습니다.

| 속성 | Type | Description |
| -------- | ---- | ----------- |
| key | 문자열 | 수정또는 삭제된 키-값의 키입니다. |
| label | 문자열 | 수정 또는 삭제된 키 값의 레이블(있는 경우)입니다. |
| etag | 문자열 | 새 `KeyValueModified` 키 값의 etag에 대 한 입니다. 삭제된 키 값의 etag에 해당합니다. `KeyValueDeleted` |

## <a name="tutorials-and-how-tos"></a>자습서 및 방법

|제목 | Description |
|---------|---------|
| [이벤트 그리드를 사용하여 Azure 앱 구성 이벤트에 반응](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Azure 앱 구성을 이벤트 그리드와 통합하는 개요입니다. |
| [빠른 시작: Azure CLI를 사용하여 사용자 지정 웹 끝점으로 Azure 앱 구성 이벤트를 라우팅합니다.](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Azure CLI를 사용하여 Azure 앱 구성 이벤트를 WebHook으로 보내는 방법을 보여 주며 |

## <a name="next-steps"></a>다음 단계

* Azure Event Grid에 대한 소개는 [Event Grid란?](overview.md)을 참조하세요.
* Azure Event Grid 구독을 만드는 방법에 대한 자세한 내용은 [Event Grid 구독 스키마](subscription-creation-schema.md)를 참조하세요.
* Azure 앱 구성 이벤트 작업에 대한 소개는 [Azure 앱 구성 이벤트 인 Azure CLI](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json)를 참조합니다. 