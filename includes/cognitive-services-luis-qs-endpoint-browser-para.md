---
title: 포함 파일
description: 포함 파일
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: e507a7c45e286473abe9b9e4365e80fb29eba2a4
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47044023"
---
LUIS 예측 엔드포인트가 반환하는 값을 이해하려면 웹 브라우저에서 예측 결과를 확인하세요. 공용 앱을 쿼리하려면 자체 키와 앱 ID가 필요합니다. 1단계에서 공용 IoT 앱 ID `df67dcdb-c37d-46af-88e1-8b97951ca1c2`가 URL의 일부로 제공됩니다.

**GET** 엔드포인트 요청의 URL 형식은 다음과 같습니다.

```JSON
https://<region>.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<YOUR-KEY>&q=<user-utterance>
```

1. 공용 IoT 앱의 엔드포인트는 `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=<YOUR_KEY>&q=turn on the bedroom light` 형식입니다.

    URL을 복사하고 키를 `<YOUR_KEY>` 값으로 바꿉니다.

2. 브라우저 창에 URL을 붙여넣고 Enter 키를 누릅니다. LUIS에서 `HomeAutomation.TurnOn` 의도(최고 의도)와 `bedroom` 값이 있는 `HomeAutomation.Room` 엔터티를 검색한다고 나타내는 JSON 결과가 브라우저에 표시됩니다.

    ```JSON
    {
      "query": "turn on the bedroom light",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOn",
        "score": 0.809439957
      },
      "entities": [
        {
          "entity": "bedroom",
          "type": "HomeAutomation.Room",
          "startIndex": 12,
          "endIndex": 18,
          "score": 0.8065475
        }
      ]
    }
    ```

3. URL에서 `q=` 매개 변수 값을 `turn off the living room light`로 변경하고 Enter 키를 누릅니다. 이제 결과에는 LUIS에서 `HomeAutomation.TurnOff` 의도(최고 의도) 및 `living room` 값이 있는 `HomeAutomation.Room` 엔터티를 검색했다고 표시됩니다. 

    ```JSON
    {
      "query": "turn off the living room light",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOff",
        "score": 0.984057844
      },
      "entities": [
        {
          "entity": "living room",
          "type": "HomeAutomation.Room",
          "startIndex": 13,
          "endIndex": 23,
          "score": 0.9619945
        }
      ]
    }
    ```
