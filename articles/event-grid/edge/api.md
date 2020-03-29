---
title: REST API - Azure 이벤트 그리드 IoT 에지 | 마이크로 소프트 문서
description: IoT 에지의 이벤트 그리드에서 REST API.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 19f86b1d8233e05844201e1095c1f79324955cd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841832"
---
# <a name="rest-api"></a>REST API
이 문서에서는 IoT Edge에서 Azure 이벤트 그리드의 나머지 API에 대해 설명합니다.

## <a name="common-api-behavior"></a>일반적인 API 동작

### <a name="base-url"></a>기준 URL
IoT Edge의 이벤트 그리드에는 HTTP(포트 5888) 및 HTTPS(포트 4438)를 통해 다음과 같은 API가 노출됩니다.

* HTTP의 기본 URL:http://eventgridmodule:5888
* HTTPS의 기본 URL:https://eventgridmodule:4438

### <a name="request-query-string"></a>쿼리 문자열 요청
모든 API 요청에는 다음 쿼리 문자열 매개 변수가 필요합니다.

```?api-version=2019-01-01-preview```

### <a name="request-content-type"></a>콘텐츠 형식 요청
모든 API 요청에는 **Content 유형**이 있어야 합니다.

**EventGridSchema** 또는 **CustomSchema의**경우 콘텐츠 유형값은 다음 값 중 하나일 수 있습니다.

```Content-Type: application/json```

```Content-Type: application/json; charset=utf-8```

구조화 모드에서 **CloudEventSchemaV1_0** 경우 콘텐츠 유형 값은 다음 값 중 하나일 수 있습니다.

```Content-Type: application/cloudevents+json```
    
```Content-Type: application/cloudevents+json; charset=utf-8```
    
```Content-Type: application/cloudevents-batch+json```
    
```Content-Type: application/cloudevents-batch+json; charset=utf-8```

이진 모드에서 **CloudEventSchemaV1_0** 경우 자세한 내용은 [설명서를](https://github.com/cloudevents/spec/blob/master/http-protocol-binding.md) 참조하십시오.

### <a name="error-response"></a>오류 응답
모든 API는 다음 페이로드를 통해 오류를 반환합니다.

```json
{
    "error":
    {
        "code": "<HTTP STATUS CODE>",
        "details": 
        {
            "code": "<Detailed Error Code>",
            "message": "..."
        }
    }
}
```

## <a name="manage-topics"></a>주제 관리

### <a name="put-topic-create--update"></a>주제 넣기(만들기/업데이트)

**요청**:``` PUT /topics/<topic_name>?api-version=2019-01-01-preview ```

**탑재하중**:

```json
    {
        "name": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "properties":
        {
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // optional
        }
    }
```

**응답**: HTTP 200

**탑재하중**:

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<get_request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // populated with EventGridSchema if not explicitly specified in PUT request
    }
}
```

### <a name="get-topic"></a>주제 받기

**요청**:``` GET /topics/<topic_name>?api-version=2019-01-01-preview ```

**응답**: HTTP 200

**탑재하중**:
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
    }
}
```

### <a name="get-all-topics"></a>모든 주제 보기

**요청**:``` GET /topics?api-version=2019-01-01-preview ```

**응답**: HTTP 200

**탑재하중**:
```json
[
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    },
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    }
]
```

### <a name="delete-topic"></a>항목 삭제

**요청**:``` DELETE /topics/<topic_name>?api-version=2019-01-01-preview ```

**응답**: HTTP 200, 빈 페이로드

## <a name="manage-event-subscriptions"></a>이벤트 구독 관리
이 섹션의 샘플은 을 사용합니다. `EndpointType=Webhook;` json `EndpointType=EdgeHub / EndpointType=EventGrid` 샘플은 다음 섹션에 있습니다. 

### <a name="put-event-subscription-create--update"></a>이벤트 구독 넣기(만들기/업데이트)

**요청**:``` PUT /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**탑재하중**:
```json
{
    "name": "<subscription_name>", // optional, inferred from URL. If specified must match URL subscription_name
    "properties":
    {
        "topicName": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0", // optional
        "retryPolicy":  //optional
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "persistencePolicy": "true",
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // optional
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

**응답**: HTTP 200

**탑재하중**:

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```


### <a name="get-event-subscription"></a>이벤트 구독 받기

**요청**:``` GET /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**응답**: HTTP 200

**탑재하중**:
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

### <a name="get-event-subscriptions"></a>이벤트 구독 받기

**요청**:``` GET /topics/<topic_name>/eventSubscriptions?api-version=2019-01-01-preview ```

**응답**: HTTP 200

**탑재하중**:
```json
[
    {
        // same event-subscription json as that returned from Get-EventSubscription above
    },
    {
    },
    ...
]
```

### <a name="delete-event-subscription"></a>이벤트 구독 삭제

**요청**:``` DELETE /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**응답**: HTTP 200, 페이로드 없음


## <a name="publish-events-api"></a>이벤트 API 게시

### <a name="send-batch-of-events-in-event-grid-schema"></a>이벤트 일괄 처리 보내기(이벤트 그리드 스키마)

**요청**:``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        "id": "<user-defined-event-id>",
        "topic": "<topic_name>",
        "subject": "",
        "eventType": "",
        "eventTime": ""
        "dataVersion": "",
        "metadataVersion": "1",
        "data": 
            ...
    }
]
```

**응답**: HTTP 200, 빈 페이로드


**페이로드 필드 설명**
- ```Id```필수입니다. 호출자별로 채워진 모든 문자열 값일 수 있습니다. Event Grid는 중복 검색을 수행하거나 이 필드의 의미 체계를 적용하지 않습니다.
- ```Topic```선택 사항이지만 지정된 경우 요청 URL의 topic_name 일치해야 합니다.
- ```Subject```필수이며, 모든 문자열 값이 될 수 있습니다.
- ```EventType```필수이며, 모든 문자열 값이 될 수 있습니다.
- ```EventTime```유효성이 검사되지 는 않지만 적절한 DateTime이어야 합니다.
- ```DataVersion```는 필수입니다.
- ```MetadataVersion```선택 사항입니다.```"1"```
- ```Data```선택 사항이며 JSON 토큰(숫자, 문자열, 부울, 배열, 개체)일 수 있습니다.

### <a name="send-batch-of-events-in-custom-schema"></a>이벤트 일괄 처리 보내기(사용자 지정 스키마)

**요청**:``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        ...
    }
]
```

**응답**: HTTP 200, 빈 페이로드


**페이로드 제한**
- 이벤트의 배열이어야 합니다.
- 각 배열 항목은 JSON 개체여야 합니다.
- 페이로드 크기 이외의 다른 제약 조건은 없습니다.

## <a name="examples"></a>예

### <a name="set-up-topic-with-eventgrid-schema"></a>EventGrid 스키마로 주제 설정
**이벤트그리드스키마에**이벤트를 게시해야 하는 토픽을 설정합니다.

```json
    {
        "name": "myeventgridtopic",
        "properties":
        {
            "inputSchema": "EventGridSchema"
        }
    }
```

### <a name="set-up-topic-with-custom-schema"></a>사용자 지정 스키마를 통해 주제 설정
에 게시해야 하는 항목을 설정합니다. `customschema`

```json
    {
        "name": "mycustomschematopic",
        "properties":
        {
            "inputSchema": "CustomSchema"
        }
    }
```

### <a name="set-up-topic-with-cloud-event-schema"></a>클라우드 이벤트 스키마로 주제 설정
에 게시해야 하는 항목을 설정합니다. `cloudeventschema`

```json
    {
        "name": "mycloudeventschematopic",
        "properties":
        {
            "inputSchema": "CloudEventSchemaV1_0"
        }
    }
```

### <a name="set-up-webhook-as-destination-events-to-be-delivered-in-eventgridschema"></a>Eventgridschema에서 배달할 이벤트, 이벤트로 WebHook 설정
이 대상 유형을 사용하여 HTTP 끝점을 호스팅하는 다른 모듈 또는 네트워크/인터넷의 HTTP 주소 지정 가능한 끝점으로 이벤트를 보냅니다.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "eventDeliverySchema": "eventgridschema",
            }
        }
    }
}
```

`endpointUrl` 특성에 대한 제약 조건:
- null이 아닌 이어야 합니다.
- 절대 URL이어야 합니다.
- eventGridModule 설정에서 outbound__webhook__httpsOnly true로 설정된 경우 HTTPS전용이어야 합니다.
- outbound__webhook__httpsOnly 거짓으로 설정하면 HTTP 또는 HTTPS가 될 수 있습니다.

`eventDeliverySchema` 속성에 대한 제약 조건:
- 구독 토픽의 입력 스키마와 일치해야 합니다.
- null일 수 있습니다. 기본적으로 토픽의 입력 스키마입니다.

### <a name="set-up-iot-edge-as-destination"></a>IoT 에지를 목적지로 설정

이 대상을 사용하여 IoT Edge Hub로 이벤트를 보내고 에지 허브의 라우팅/필터링/전달 하위 시스템의 대상이 됩니다.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EdgeHub",
            "properties":
            {
                "outputName": "<eventgridmodule_output_port_name>"
            }
        }
    }
}
```

### <a name="set-up-event-grid-cloud-as-destination"></a>이벤트 그리드 클라우드를 대상으로 설정

이 대상을 사용하여 Azure(Azure)의 이벤트 그리드에 이벤트를 보냅니다. 먼저 에지에서 이벤트 구독을 만들기 전에 이벤트를 보낼 클라우드에서 사용자 항목을 설정해야 합니다.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EventGrid",
            "properties":
            {
                "endpointUrl": "<eventgrid_user_topic_url>",
                "sasKey": "<user_topic_sas_key>",
                "topicName": "<new value to populate in forwarded EventGridEvent.Topic>" // if not specified, the Topic field on every event gets nulled out before being sent to Azure Event Grid
            }
        }
    }
}
```

엔드포인트Url:
- null이 아닌 이어야 합니다.
- 절대 URL이어야 합니다.
- 경로는 `/api/events` 요청 URL 경로에 정의되어야 합니다.
- 쿼리 문자열에 있어야 `api-version=2018-01-01` 합니다.
- EventGridModule 설정에서 outbound__eventgrid__httpsOnly true로 설정된 경우(기본적으로 true) HTTPS전용이어야 합니다.
- outbound__eventgrid__httpsOnly false로 설정된 경우 HTTP 또는 HTTPS일 수 있습니다.
- outbound__eventgrid__allowInvalidHostnames 기본적으로 false로 설정된 경우 다음 끝점 중 하나를 대상으로 해야 합니다.
   - `eventgrid.azure.net`
   - `eventgrid.azure.us`
   - `eventgrid.azure.cn`

사스키:
- null이 아닌 이어야 합니다.

주제 이름:
- 구독.EventDeliverySchema가 EventGridSchema로 설정된 경우 이 필드의 값은 클라우드의 이벤트 그리드로 전달되기 전에 모든 이벤트의 주제 필드에 전달됩니다.
- 구독.EventDeliverySchema가 CustomEventSchema로 설정된 경우 이 속성은 무시되고 사용자 지정 이벤트 페이로드는 수신된 그대로 전달됩니다.

## <a name="set-up-event-hubs-as-a-destination"></a>이벤트 허브를 대상으로 설정

이벤트 허브에 게시하려면 `endpointType` 다음을 `eventHub` 설정하고 다음을 제공합니다.

* connectionString: 공유 액세스 정책을 통해 생성된 타겟팅하는 특정 이벤트 허브에 대한 연결 문자열입니다.

    >[!NOTE]
    > 연결 문자열은 엔터티별이어야 합니다. 네임스페이스 연결 문자열을 사용하면 작동하지 않습니다. Azure Portal에서 게시할 특정 이벤트 허브로 이동하여 엔터티 별 연결 문자열을 생성하고 **공유 액세스 정책을** 클릭하여 새 엔터티 특정 연결 문자열을 생성할 수 있습니다.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "connectionString": "<your-event-hub-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-queues-as-a-destination"></a>서비스 버스 대기열을 대상으로 설정

서비스 버스 큐에 게시하려면 `endpointType` 다음을 `serviceBusQueue` 설정하고 다음을 제공합니다.

* connectionString: 공유 액세스 정책을 통해 생성된 대상으로 하는 특정 서비스 버스 큐에 대한 연결 문자열입니다.

    >[!NOTE]
    > 연결 문자열은 엔터티별이어야 합니다. 네임스페이스 연결 문자열을 사용하면 작동하지 않습니다. Azure Portal에서 게시할 특정 서비스 버스 큐로 이동하여 엔터티 별 연결 문자열을 생성하고 **공유 액세스 정책을** 클릭하여 새 엔터티 특정 connecection 문자열을 생성합니다.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "connectionString": "<your-service-bus-queue-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-topics-as-a-destination"></a>서비스 버스 주제를 대상으로 설정

서비스 버스 토픽에 게시하려면 `endpointType` `serviceBusTopic` 다음을 설정하고 다음을 제공합니다.

* connectionString: 공유 액세스 정책을 통해 생성된 대상으로 하는 특정 서비스 버스 토픽에 대한 연결 문자열입니다.

    >[!NOTE]
    > 연결 문자열은 엔터티별이어야 합니다. 네임스페이스 연결 문자열을 사용하면 작동하지 않습니다. Azure Portal에서 게시할 특정 Service Bus 토픽으로 이동하여 엔터티 별 연결 문자열을 생성하고 **공유 액세스 정책을** 클릭하여 새 엔터티 특정 connecection 문자열을 생성합니다.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "connectionString": "<your-service-bus-topic-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-storage-queues-as-a-destination"></a>저장소 큐를 대상으로 설정

저장소 큐에 게시하려면 `endpointType` 다음을 `storageQueue` 설정하고 다음을 제공합니다.

* queueName: 게시할 저장소 큐의 이름입니다.
* 연결문자열: 저장소 큐에 있는 저장소 계정에 대한 연결 문자열입니다.

    >[!NOTE]
    > Unline 이벤트 허브, 서비스 버스 큐 및 서비스 버스 항목, 저장소 큐에 사용되는 연결 문자열은 엔터티에 특정되지 않습니다. 대신 저장소 계정에 대한 연결 문자열을 사용해야 합니다.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "connectionString": "<your-storage-account-connection-string>"
              }
            }
          }
        }
    ```