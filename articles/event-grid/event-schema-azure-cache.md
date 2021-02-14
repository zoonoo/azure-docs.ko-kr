---
title: Event Grid 소스로 서의 Azure Cache for Redis
description: 을 사용 하 여 Redis 이벤트에 대해 Azure Cache에 제공 되는 속성에 대해 설명 Azure Event Grid
ms.topic: conceptual
ms.date: 02/11/2021
author: curib
ms.author: cauribeg
ms.openlocfilehash: 1a2995bc9ef40cd4eab320ce1bb4c5faf61e0e6e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371280"
---
# <a name="azure-cache-for-redis-as-an-event-grid-source"></a>Event Grid 소스로 서의 Azure Cache for Redis

이 문서에서는 Azure Cache for Redis 이벤트에 대 한 속성 및 스키마를 제공 합니다. 이벤트 스키마에 대한 소개는 [Azure Event Grid 이벤트 스키마](event-schema.md)를 참조하세요. 

## <a name="available-event-types"></a>사용할 수 있는 이벤트 유형
이러한 이벤트는 Redis REST Api 용 Azure Cache를 호출 하 여 클라이언트에서 내보내기, 가져오기 또는 크기를 조정 하는 경우에 트리거됩니다. 패칭 이벤트는 Redis update에 의해 트리거됩니다.

 |이벤트 이름 |Description|
 |----------|-----------|
 |**ExportRDBCompleted** |캐시 데이터를 내보낼 때 트리거됩니다. |
 |**ImportRDBCompleted** |캐시 데이터를 가져올 때 트리거됩니다. |
 |**PatchingCompleted** |패칭이 완료 되 면 트리거됩니다. |
 |**ScalingCompleted** |크기 조정이 완료 되 면 트리거됩니다. |

## <a name="example-event"></a>예제 이벤트
이벤트가 트리거될 때 Event Grid 서비스는 해당 이벤트에 대한 데이터를 구독 엔드포인트로 보냅니다. 이 섹션에는 각 Redis 용 Azure Cache 이벤트에 대 한 데이터가 어떻게 표시 되는지 예가 포함 되어 있습니다.

# <a name="event-grid-event-schema"></a>[Event Grid 이벤트 스키마](#tab/event-grid-event-schema)

### <a name="microsoftcachepatchingcompleted-event"></a>PatchingCompleted 이벤트

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.PatchingCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"PatchingCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"PatchingCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcacheimportrdbcompleted-event"></a>ImportRDBCompleted 이벤트

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ImportRDBCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ImportRDBCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ImportRDBCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcacheexportrdbcompleted-event"></a>ExportRDBCompleted 이벤트

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ExportRDBCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ExportRDBCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ExportRDBCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcachescalingcompleted"></a>ScalingCompleted

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ScalingCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ScalingCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ScalingCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

# <a name="cloud-event-schema"></a>[클라우드 이벤트 스키마](#tab/cloud-event-schema)


### <a name="microsoftcachepatchingcompleted-event"></a>PatchingCompleted 이벤트

```json
[{
    "id": "9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
    "type": "Microsoft.Cache.PatchingCompleted",
    "source": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
    "data": {
        "name": "PatchingCompleted",
        "timestamp": "2020-12-09T21:50:19.9995668+00:00",
        "status": "Succeeded"
    },
    "subject": "PatchingCompleted",
    "time": "2020-12-09T21:50:19.9995668+00:00",
    "specversion": "1.0"
}]
```

### <a name="microsoftcacheimportrdbcompleted-event"></a>ImportRDBCompleted 이벤트

```json
[{
    "id": "9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
    "type": "Microsoft.Cache.ImportRDBCompleted",
    "source": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
    "data": {
        "name": "ImportRDBCompleted",
        "timestamp": "2020-12-09T21:50:19.9995668+00:00",
        "status": "Succeeded"
    },
    "subject": "ImportRDBCompleted",
    "eventTime": "2020-12-09T21:50:19.9995668+00:00",
    "specversion": "1.0"
}]
```

### <a name="microsoftcacheexportrdbcompleted-event"></a>ExportRDBCompleted 이벤트

```json
[{
    "id": "9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
    "type": "Microsoft.Cache.ExportRDBCompleted",
    "source": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
    "data": {
        "name": "ExportRDBCompleted",
        "timestamp": "2020-12-09T21:50:19.9995668+00:00",
        "status": "Succeeded"
    },
    "subject": "ExportRDBCompleted",
    "time": "2020-12-09T21:50:19.9995668+00:00",
    "specversion": "1.0"
}]
```

### <a name="microsoftcachescalingcompleted"></a>ScalingCompleted

```json
[{
    "id": "9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
    "type": "Microsoft.Cache.ScalingCompleted",
    "source": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
    "data": {
        "name": "ScalingCompleted",
        "timestamp": "2020-12-09T21:50:19.9995668+00:00",
        "status": "Succeeded"
    },
    "subject": "ScalingCompleted",
    "time": "2020-12-09T21:50:19.9995668+00:00",
    "specversion": "1.0"
}]
```

---

## <a name="event-properties"></a>이벤트 속성

# <a name="event-grid-event-schema"></a>[Event Grid 이벤트 스키마](#tab/event-grid-event-schema)

이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 속성 | 형식 | Description |
| -------- | ---- | ----------- |
| `topic` | 문자열 | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| `subject` | 문자열 | 게시자가 정의한 이벤트 주체의 경로입니다. |
| `eventType` | 문자열 | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| `eventTime` | 문자열 | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| `id` | 문자열 | 이벤트에 대한 고유 식별자입니다. |
| `data` | object | Redis 이벤트 데이터에 대 한 Azure 캐시. |
| `dataVersion` | 문자열 | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| `metadataVersion` | 문자열 | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |


# <a name="cloud-event-schema"></a>[클라우드 이벤트 스키마](#tab/cloud-event-schema)


이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 속성 | 형식 | Description |
| -------- | ---- | ----------- |
| `source` | 문자열 | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| `subject` | 문자열 | 게시자가 정의한 이벤트 주체의 경로입니다. |
| `type` | 문자열 | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| `time` | 문자열 | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| `id` | 문자열 | 이벤트에 대한 고유 식별자입니다. |
| `data` | object | Redis 이벤트 데이터에 대 한 Azure 캐시. |
| `specversion` | 문자열 | CloudEvents 스키마 사양 버전입니다. |

---


데이터 개체의 속성은 다음과 같습니다.

| 속성 | 형식 | Description |
| -------- | ---- | ----------- |
| `timestamp` | 문자열 | 이벤트가 발생한 시간입니다. |
| `name` | 문자열 | 이벤트의 이름입니다. |
| `status` | 문자열 | 이벤트의 상태입니다. 실패 했거나 성공 했습니다. |

## <a name="quickstarts"></a>빠른 시작

Redis 이벤트에 대 한 Azure Cache를 시도 하려면 다음 빠른 시작 문서를 참조 하세요.

|이 도구를 사용 하려면 다음을 수행 합니다.    |이 문서를 참조 하세요. |
|--|-|
|Azure portal    |[빠른 시작: Azure Portal을 사용하여 Azure Cache for Redis 이벤트를 웹 엔드포인트로 라우팅](../azure-cache-for-redis/cache-event-grid-quickstart-portal.md)|
|PowerShell    |[빠른 시작: PowerShell을 사용하여 Azure Cache for Redis 이벤트를 웹 엔드포인트로 라우팅](../azure-cache-for-redis/cache-event-grid-quickstart-powershell.md)|
|Azure CLI    |[빠른 시작: Azure CLI를 사용하여 Azure Cache for Redis 이벤트를 웹 엔드포인트로 라우팅](../azure-cache-for-redis/cache-event-grid-quickstart-cli.md)|

## <a name="next-steps"></a>다음 단계

* Azure Event Grid에 대한 소개는 [Event Grid란?](overview.md)을 참조하세요.
* Azure Event Grid 구독을 만드는 방법에 대한 자세한 내용은 [Event Grid 구독 스키마](subscription-creation-schema.md)를 참조하세요.

