---
title: 모듈 쌍 - Azure Event Grid IoT Edge | Microsoft Docs
description: 모듈 쌍을 통한 구성
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.subservice: iot-edge
ms.date: 05/10/2021
ms.topic: article
ms.openlocfilehash: 27e5194b86ea5f76a346412c3239dac111c07d80
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110379786"
---
# <a name="module-twin-json-schema-azure-event-grid"></a>모듈 쌍 JSON 스키마(Azure Event Grid)

IoT Edge의 Event Grid는 IoT Edge 에코시스템과 통합되며 모듈 쌍을 통해 토픽 및 구독 만들기를 지원합니다. 또한 모듈 쌍의 reported 속성에 대한 모든 토픽 및 이벤트 구독의 현재 상태를 보고합니다.

> [!WARNING]
> IoT Edge 에코시스템의 제한 사항 때문에 다음 json 예제의 모든 배열 요소가 json 문자열로 인코딩되어 있습니다. 다음 예제에서는 `EventSubscription.Filter.EventTypes` 및 `EventSubscription.Filter.AdvancedFilters` 키를 참조하세요.

## <a name="desired-properties-json"></a>desired 속성 JSON

* 토픽 섹션의 각 키-값 쌍 값에는 토픽을 만들 때 API의 `Topic.Properties`에서 사용되는 것과 동일한 JSON 스키마가 적용됩니다.
* **EventSubscriptions** 섹션의 각 키-값 쌍 값에는 토픽을 만들 때 API의 `EventSubscription.Properties`에서 사용되는 것과 정확히 동일한 JSON 스키마가 적용됩니다.
* 토픽을 삭제하려면 desired 속성에서 해당 값을 `null`로 설정합니다.
* desired 속성을 통해 이벤트 구독을 삭제할 수는 없습니다.

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

## <a name="reported-properties-json"></a>reported 속성 JSON

모듈 쌍의 reported 속성 섹션에는 다음 정보가 포함됩니다.

* 모듈의 저장소에 존재하는 토픽 및 구독 세트
* 원하는 토픽/이벤트 구독을 만들 때 발생하는 오류
* 모든 부팅 오류(예: desired 속성 JSON 구문 분석 실패)

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
