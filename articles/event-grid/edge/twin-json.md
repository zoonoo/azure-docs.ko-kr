---
title: 모듈 쌍-Azure Event Grid IoT Edge | Microsoft Docs
description: 모듈 쌍을 통한 구성.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 5c23b9ef280a4a4e3458d279ecf060d2e3d50295
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "72992146"
---
# <a name="module-twin-json-schema"></a>모듈 쌍 JSON 스키마

IoT Edge Event Grid는 IoT Edge 에코 시스템과 통합 되며 모듈 쌍을 통해 토픽 및 구독 만들기를 지원 합니다. 또한 모듈 쌍의 보고 된 속성에 대 한 모든 토픽 및 이벤트 구독의 현재 상태를 보고 합니다.

> [!WARNING]
> IoT Edge 에코 시스템의 제한 사항 때문에 다음 json 예제의 모든 배열 요소가 json 문자열로 인코딩되어 있습니다. `EventSubscription.Filter.EventTypes`다음 예제에서는 및 키를 참조 `EventSubscription.Filter.AdvancedFilters` 하세요.

## <a name="desired-properties-json"></a>Desired 속성 JSON

* 토픽 섹션에서 각 키-값 쌍의 값에는 `Topic.Properties` 항목을 만들 때 API에서에 사용 되는 것과 동일한 JSON 스키마가 있습니다.
* **Eventsubscriptions** 섹션에서 각 키-값 쌍의 값에는 `EventSubscription.Properties` 항목을 만들 때 API에서에 사용 되는 것과 동일한 json 스키마가 있습니다.
* 토픽을 삭제 하려면 desired 속성에서 해당 값을로 설정 `null` 합니다.
* Desired 속성을 통해 이벤트 구독을 삭제할 수 없습니다.

```json
{
    "topics": {
        "twinTopic1": {},
        "twinTopic2": {
            "inputSchema": "customEventSchema"
        }
    },
    "eventSubscriptions": {
        "twinTopic1WebhookSub": {
            "topic": "twinTopic1",
            "retryPolicy": {
                "eventExpiryInMinutes": 120,
                "maxDeliveryAttempts": 30
            },
            "destination": {
                "endpointType": "WebHook",
                "properties": {
                    "endpointUrl": "https://localhost:4438"
                }
            },
            "filter": {
                "subjectBeginsWith": "^",
                "subjectEndsWith": "$",
                "isSubjectCaseSensitive": false,
                "includedEventTypes": "[\"et1\",\"et2\"]",
                "advancedFilters": "[{\"value\":true,\"operatorType\":\"BoolEquals\",\"key\":\"data.b\"},{\"values\":[\"\\\"\",\"c\"],    \"operatorType\":\"StringContains\",\"key\":\"data.s\"}]"
            }
        },
        "twinTopic2EdgeHubSub": {
            "topic": "twinTopic2",
            "deliveryPolicy": {
                "approxBatchSizeInBytes": 200000,
                "maxEventsPerBatch": 25
            },
            "destination": {
                "endpointType": "EdgeHub",
                "properties": {
                    "outputName": "twinTopic2EdgeHubSub"
                }
            },
            "filter": {
                "advancedFilters": "[{\"value\":true,\"operatorType\":\"BoolEquals\",\"key\":\"dAt\\\"A.a\"},{\"values\":[\"\\\"\",    \"c\"],\"operatorType\":\"StringContains\",\"key\":\"dAt\\\"A.a\"}]"
            }
        }
    }
}
```

## <a name="reported-properties-json"></a>보고 된 속성 JSON

모듈 쌍의 보고 된 속성 섹션에는 다음 정보가 포함 됩니다.

* 모듈의 저장소에 존재 하는 토픽 및 구독 집합
* 원하는 토픽/이벤트 구독을 만들 때 발생 하는 모든 오류
* 모든 부팅 오류 (예: desired 속성 JSON 구문 분석 실패)

```json
{
    "topics": {
        "twinTopic1": {},
        "twinTopic2": {
            "inputSchema": "customEventSchema"
        }
    },
    "eventSubscriptions": {
        "twinTopic1WebhookSub": {
            "topic": "twinTopic1",
            "retryPolicy": {
                "eventExpiryInMinutes": 120,
                "maxDeliveryAttempts": 30
            },
            "destination": {
                "endpointType": "WebHook",
                "properties": {
                    "endpointUrl": "https://localhost:4438"
                }
            },
            "filter": {
                "subjectBeginsWith": "^",
                "subjectEndsWith": "$",
                "isSubjectCaseSensitive": false,
                "includedEventTypes": "[\"et1\",\"et2\"]",
                "advancedFilters": "[{\"value\":true,\"operatorType\":\"BoolEquals\",\"key\":\"data.b\"},{\"values\":[\"\\\"\",\"c\"],    \"operatorType\":\"StringContains\",\"key\":\"data.s\"}]"
            }
        },
        "twinTopic2EdgeHubSub": {
            "topic": "twinTopic2",
            "deliveryPolicy": {
                "approxBatchSizeInBytes": 200000,
                "maxEventsPerBatch": 25
            },
            "destination": {
                "endpointType": "EdgeHub",
                "properties": {
                    "outputName": "twinTopic2EdgeHubSub"
                }
            },
            "filter": {
                "advancedFilters": "[{\"value\":true,\"operatorType\":\"BoolEquals\",\"key\":\"dAt\\\"A.a\"},{\"values\":[\"\\\"\",    \"c\"],\"operatorType\":\"StringContains\",\"key\":\"dAt\\\"A.a\"}]"
            }
        }
    },
    "errors": {
        "bootupMessage": "",
        "bootupException": "",
        "topics": {},
        "eventSubscriptions": {
            "twinTopic1EventGridUserTopicSub": "HttpStatusCode='BadRequest' ErrorCode='InvalidDestination' Message='EventSubscription.Properties.Destination failed validation. Reason: EndpointUrl must target the /api/events API of Azure Event Grid in the cloud..'"
        }
    }
}
```
