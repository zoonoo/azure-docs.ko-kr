---
title: 포함 파일
description: 포함 파일
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: 15047b9782ea86b91ba78e0d745c67892fdc8b76
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182520"
---
예시 발언을 추가하는 데 사용되는 `response` 배열은 `hasError` 속성을 가진 각 예시 발언의 성공 또는 실패를 나타냅니다. 다음과 같은 JSON 응답은 두 발언이 성공적으로 추가되었음을 보여줍니다. 

```json
    "response": [
        {
            "value": {
                "UtteranceText": "go to seattle today",
                "ExampleId": -5123383
            },
            "hasError": false
        },
        {
            "value": {
                "UtteranceText": "book a flight",
                "ExampleId": -169157
            },
            "hasError": false
        }
    ]
```

학습 요청이 성공한 결과는 다음 JSON과 같습니다.

```json
{
    "request": null,
    "response": {
        "statusId": 9,
        "status": "Queued"
    }
}
```

학습 요청이 성공한 상태의 결과는 다음 JSON과 같습니다. 각 modelID는 의도입니다. 각 의도에 모든 발언을 학습시켜서 의도에 속하는 발언은 물론 의도에 속하지 않는 발언까지 정확하기 식별하도록 해야 합니다. 

```JSON
[
    {
        "modelId": "0c694cf9-8c32-44b8-9ea0-3d30a7d901ca",
        "details": {
            "statusId": 3,
            "status": "InProgress",
            "exampleCount": 48
        }
    },
    {
        "modelId": "10e53836-ade4-494e-9531-3bd6a944c510",
        "details": {
            "statusId": 3,
            "status": "InProgress",
            "exampleCount": 48
        }
    },
    {
        "modelId": "21e48732-a512-4c33-b5ed-8ea629465269",
        "details": {
            "statusId": 3,
            "status": "InProgress",
            "exampleCount": 48
        }
    },
    {
        "modelId": "edee15b1-9999-45c2-bbab-591d3a643033",
        "details": {
            "statusId": 3,
            "status": "InProgress",
            "exampleCount": 48
        }
    },
    {
        "modelId": "aa78e06e-df81-4bb2-b2d9-a2fbb2f81c54",
        "details": {
            "statusId": 3,
            "status": "InProgress",
            "exampleCount": 48
        }
    },
    {
        "modelId": "e39bb7bd-b417-41a9-a24f-caf4c47fc62c",
        "details": {
            "statusId": 3,
            "status": "InProgress",
            "exampleCount": 48
        }
    },
    {
        "modelId": "3782eac7-db84-4d66-ba00-0598dffb48ee",
        "details": {
            "statusId": 3,
            "status": "InProgress",
            "exampleCount": 48
        }
    },
    {
        "modelId": "a941d926-cb0f-47a8-ab7e-deba4378b96f",
        "details": {
            "statusId": 3,
            "status": "InProgress",
            "exampleCount": 48
        }
    },
    {
        "modelId": "8137f40e-ce6d-40a5-881f-dfd46a05f7e0",
        "details": {
            "statusId": 3,
            "status": "InProgress",
            "exampleCount": 48
        }
    },
    {
        "modelId": "dc08f95a-58b4-4064-a210-03fe34f75a3c",
        "details": {
            "statusId": 3,
            "status": "InProgress",
            "exampleCount": 48
        }
    },
    {
        "modelId": "4fabdbed-5697-4562-8c7d-36e174efff2e",
        "details": {
            "statusId": 3,
            "status": "InProgress",
            "exampleCount": 48
        }
    }
]
```
