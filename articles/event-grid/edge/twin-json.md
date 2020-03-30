---
title: 모듈 트윈 - Azure 이벤트 그리드 IoT 에지 | 마이크로 소프트 문서
description: 모듈 트윈을 통한 구성.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 5c23b9ef280a4a4e3458d279ecf060d2e3d50295
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72992146"
---
# <a name="module-twin-json-schema"></a>모듈 트윈 JSON 스키마

IoT Edge의 이벤트 그리드는 IoT Edge 에코와 통합되며 모듈 트윈을 통해 주제 및 구독 생성을 지원합니다. 또한 모듈 트윈에서 보고된 속성에 대한 모든 토픽 및 이벤트 구독의 현재 상태를 보고합니다.

> [!WARNING]
> IoT Edge 에코시스템의 제한사항으로 인해 다음 json 예제의 모든 배열 요소가 json 문자열로 인코딩되었습니다. 다음 `EventSubscription.Filter.EventTypes` `EventSubscription.Filter.AdvancedFilters` 예제에서 참조 및 키를 참조하십시오.

## <a name="desired-properties-json"></a>원하는 속성 JSON

* 토픽 섹션의 각 키-값 쌍의 값은 토픽을 만들 때 `Topic.Properties` API에 사용되는 JSON 스키마와 정확히 동일합니다.
* **EventSubscriptions** 섹션의 각 키-값 쌍의 값에는 토픽을 만들 때 API에 `EventSubscription.Properties` 사용되는 것과 정확히 동일한 json 스키마가 있습니다.
* 토픽을 삭제하려면 해당 `null` 값을 원하는 속성으로 설정합니다.
* 원하는 속성을 통해 이벤트 구독을 삭제하는 것은 지원되지 않습니다.

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

## <a name="reported-properties-json"></a>보고된 속성 JSON

모듈 쌍의 보고된 속성 섹션에는 다음 정보가 포함됩니다.

* 모듈 저장소에 있는 토픽 및 구독 집합
* 원하는 토픽/이벤트 구독을 만들 때 발생하는 모든 오류
* 부팅 오류(예: 원하는 속성 JSON 구문 분석 실패)

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
