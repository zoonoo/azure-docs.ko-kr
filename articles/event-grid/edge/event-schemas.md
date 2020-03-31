---
title: 이벤트 스키마 — Azure 이벤트 그리드 IoT 에지 | 마이크로 소프트 문서
description: IoT 에지의 이벤트 그리드의 이벤트 스키마입니다.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ba261aeedf6574f69d3c05f8fd005c912dcc59d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73242474"
---
# <a name="event-schemas"></a>이벤트 스키마

이벤트 그리드 모듈은 JSON 형식으로 이벤트를 수락하고 제공합니다. 현재 이벤트 그리드에서 지원하는 세 가지 스키마가 있습니다.

* **이벤트그리드스키마**
* **커스텀 스키마**
* **클라우드이벤트스키마**

토픽을 만드는 동안 게시자가 준수해야 하는 스키마를 구성할 수 있습니다. 지정되지 않은 경우 기본값은 **EventGridSchema**. 예상 스키마를 준수하지 않는 이벤트는 거부됩니다.

구독자는 이벤트를 전달할 스키마를 구성할 수도 있습니다. 지정되지 않은 경우 기본값은 토픽의 스키마입니다.
현재 구독자 배달 스키마는 토픽의 입력 스키마와 일치해야 합니다. 

## <a name="eventgrid-schema"></a>이벤트그리드 스키마

EventGrid 스키마는 게시 엔터티가 준수해야 하는 필수 속성 집합으로 구성됩니다. 각 게시자는 최상위 필드를 채워야 합니다.

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

### <a name="eventgrid-schema-properties"></a>EventGrid 스키마 속성

모든 이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 속성 | Type | 필수 | 설명 |
| -------- | ---- | ----------- |-----------
| 토픽 | 문자열 | 예 | 게시된 주제와 일치해야 합니다. 이벤트 그리드는 지정되지 않은 경우 게시된 토픽의 이름으로 채웁니다. |
| subject | 문자열 | yes | 게시자가 정의한 이벤트 주체에 대한 경로입니다. |
| eventType | 문자열 | yes | 이 이벤트 소스에 대한 이벤트 유형(예: BlobCreated). |
| eventTime | 문자열 | yes | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| ID | 문자열 | 예 | 이벤트에 대한 고유 식별자입니다. |
| 데이터 | object | 예 | 게시 엔터티와 관련된 이벤트 데이터를 캡처하는 데 사용됩니다. |
| dataVersion | 문자열 | yes | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| metadataVersion | 문자열 | 예 | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |

### <a name="example--eventgrid-schema-event"></a>예 — 이벤트그리드 스키마 이벤트

```json
[
  {
    "id": "1807",
    "eventType": "recordInserted",
    "subject": "myapp/vehicles/motorcycles",
    "eventTime": "2017-08-10T21:03:07+00:00",
    "data": {
      "make": "Ducati",
      "model": "Monster"
    },
    "dataVersion": "1.0"
  }
]
```

## <a name="customevent-schema"></a>사용자 지정 이벤트 스키마

사용자 지정 스키마에는 EventGrid 스키마와 같이 적용된 필수 속성이 없습니다. 게시 엔터티는 이벤트 스키마를 완전히 제어할 수 있습니다. 최대 유연성을 제공하고 이벤트 기반 시스템이 이미 준비되어 있고 기존 이벤트를 재사용하거나 특정 스키마에 연결하지 않으려는 시나리오를 사용할 수 있습니다.

### <a name="custom-schema-properties"></a>사용자 지정 스키마 속성

필수 속성이 없습니다. 페이로드를 결정하는 것은 게시 엔터티의 결정입니다.

### <a name="example--custom-schema-event"></a>예 — 사용자 지정 스키마 이벤트

```json
[
  {
    "eventdata": {
      "make": "Ducati",
      "model": "Monster"
    }
  }
]
```

## <a name="cloudevent-schema"></a>CloudEvent 스키마

위의 스키마 외에도 이벤트 그리드는 [기본적으로 CloudEvents JSON 스키마의](https://github.com/cloudevents/spec/blob/master/json-format.md)이벤트를 지원합니다. CloudEvents는 이벤트 데이터를 설명하는 공개 사양입니다. 이벤트를 게시하고 소비하기 위한 공통 이벤트 스키마를 제공하여 상호 운용성을 단순화합니다. [그것은 CNCF의](https://www.cncf.io/) 일부이며 현재 사용 가능한 버전은 1.0-rc1입니다.

### <a name="cloudevent-schema-properties"></a>CloudEvent 스키마 속성

필수 봉투 속성의 [CloudEvents 사양을](https://github.com/cloudevents/spec/blob/master/json-format.md#3-envelope) 참조하십시오.

### <a name="example--cloud-event"></a>예 — 클라우드 이벤트
```json
[{
       "id": "1807",
       "type": "recordInserted",
       "source": "myapp/vehicles/motorcycles",
       "time": "2017-08-10T21:03:07+00:00",
       "datacontenttype": "application/json",
       "data": {
            "make": "Ducati",
            "model": "Monster"
        },
        "dataVersion": "1.0",
        "specVersion": "1.0-rc1"
}]
```
