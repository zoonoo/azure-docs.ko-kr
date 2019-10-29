---
title: 이벤트 스키마-Azure Event Grid IoT Edge | Microsoft Docs
description: IoT Edge에서 Event Grid 이벤트 스키마.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: c4dec98b66c5074acd6c2350a125c65e448e4878
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992380"
---
# <a name="event-schemas"></a>이벤트 스키마

Event Grid 모듈은 이벤트를 수신 하 고 JSON 형식으로 전달 합니다. 현재 Event Grid에서 지 원하는 두 개의 스키마가 있습니다.

* **EventGridSchema**
* **CustomSchema**
* **CloudEventSchema**

항목을 만드는 동안 게시자가 준수 해야 하는 스키마를 구성할 수 있습니다. 지정 하지 않으면 기본적으로 **Eventgridschema**로 설정 됩니다. 필요한 스키마를 따르지 않는 이벤트는 거부 됩니다.

구독자는 이벤트를 전달 하려는 스키마를 구성할 수도 있습니다. 지정 하지 않으면 기본값은 토픽의 스키마입니다.
현재 구독자 배달 스키마는 해당 항목의 입력 스키마와 일치 해야 합니다. 

## <a name="eventgrid-schema"></a>EventGrid 스키마

EventGrid 스키마는 게시 엔터티가 준수 해야 하는 필수 속성 집합으로 구성 됩니다. 각 게시자는 최상위 필드를 채워야 합니다.

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

모든 이벤트에는 다음과 같은 최상위 수준 데이터가 있습니다.

| 자산 | Type | 필수 | 설명 |
| -------- | ---- | ----------- |-----------
| 토픽 | 문자열 | 아닙니다. | 는 게시 된 항목과 일치 해야 합니다. Event Grid는 지정 되지 않은 경우 게시 된 항목의 이름으로 채웁니다. |
| 제목 | 문자열 | yes | 게시자가 정의한 이벤트 주체에 대한 경로입니다. |
| eventType | 문자열 | yes | 이 이벤트 원본에 대 한 이벤트 유형입니다 (예: BlobCreated). |
| eventTime | 문자열 | yes | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| ID | 문자열 | 아닙니다. | 이벤트에 대한 고유 식별자입니다. |
| 데이터 | object | 아닙니다. | 게시 엔터티와 관련 된 이벤트 데이터를 캡처하는 데 사용 됩니다. |
| dataVersion | 문자열 | yes | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| metadataVersion | 문자열 | 아닙니다. | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |

### <a name="example--eventgrid-schema-event"></a>예-EventGrid 스키마 이벤트

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

## <a name="customevent-schema"></a>CustomEvent 스키마

사용자 지정 스키마에는 EventGrid 스키마와 같이 적용 되는 필수 속성이 없습니다. 게시 엔터티는 이벤트 스키마를 완전히 제어할 수 있습니다. 최대 유연성을 제공 하 고 이벤트 기반 시스템이 이미 있는 경우 기존 이벤트를 다시 사용 하거나 특정 스키마에 연결 하지 않으려는 시나리오를 가능 하 게 합니다.

### <a name="custom-schema-properties"></a>사용자 지정 스키마 속성

필수 속성이 없습니다. 페이로드를 확인 하는 것은 게시 엔터티입니다.

### <a name="example--custom-schema-event"></a>예-사용자 지정 스키마 이벤트

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

위의 스키마 외에도 Event Grid는 기본적으로 [CLOUDEVENTS JSON 스키마](https://github.com/cloudevents/spec/blob/master/json-format.md)의 이벤트를 지원 합니다. CloudEvents는 이벤트 데이터를 설명 하는 공개 사양입니다. 이벤트를 게시 하 고 소비 하는 데 일반적인 이벤트 스키마를 제공 하 여 상호 운용성을 간소화 합니다. [Cncf](https://www.cncf.io/) 의 일부 이며 현재 사용할 수 있는 버전은 1.0-rc1입니다.

### <a name="cloudevent-schema-properties"></a>CloudEvent 스키마 속성

필수 봉투 (envelope) 속성에 대 한 [CloudEvents 사양](https://github.com/cloudevents/spec/blob/master/json-format.md#3-envelope) 을 참조 하세요.

### <a name="example--cloud-event"></a>예-클라우드 이벤트
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