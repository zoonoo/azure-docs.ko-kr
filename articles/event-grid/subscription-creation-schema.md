---
title: "Azure Event Grid 구독 스키마"
description: "Azure Event Grid를 사용하여 이벤트에 대한 구독의 속성을 설명합니다."
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 08/17/2017
ms.author: babanisa
ms.translationtype: HT
ms.sourcegitcommit: 540180e7d6cd02dfa1f3cac8ccd343e965ded91b
ms.openlocfilehash: cce058e75ebf49f53d11e3b5a87ea4625994db0f
ms.contentlocale: ko-kr
ms.lasthandoff: 08/16/2017

---

# <a name="event-grid-subscription-schema"></a>Event Grid 구독 스키마

Event Grid 구독을 만들려면 이벤트 만들기 구독 작업에 요청을 보냅니다. 이때 다음 형식을 사용합니다.

```
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2017-06-15-preview
``` 

예를 들어 `examplegroup`이라는 리소스 그룹의 `examplestorage`라는 저장소 계정에 대한 이벤트 구독을 만들려면 다음 형식을 사용합니다.

```
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2017-06-15-preview
``` 

문서는 요청 본문에 대한 속성 및 스키마를 설명합니다.
 
## <a name="event-subscription-properties"></a>이벤트 구독 속성

| 속성 | 형식 | 설명 |
| -------- | ---- | ----------- |
| destination | object | 끝점을 정의하는 개체입니다. |
| filter | object | 이벤트 유형을 필터링하기 위한 선택적 필드입니다. |

### <a name="destination-object"></a>대상 개체

| 속성 | 형식 | 설명 |
| -------- | ---- | ----------- |
| endpointType | string | 구독(웹후크/HTTP, Event Hub 또는 큐)에 대한 끝점의 형식입니다. | 
| endpointUrl | string |  | 

### <a name="filter-object"></a>필터 개체

| 속성 | 형식 | 설명 |
| -------- | ---- | ----------- |
| includedEventTypes | array | 이벤트 메시지의 이벤트 유형이 이러한 이벤트 유형 이름 중 하나와 정확하게 일치할 때 일치합니다. 이벤트 이름이 이벤트 원본에 대해 등록된 이벤트 유형 이름과 일치하지 않는 경우 오류가 발생합니다. 기본값은 모든 이벤트 유형과 일치합니다. |
| subjectBeginsWith | string | 이벤트 메시지의 제목 필드에 대한 접두사-일치 필터입니다. 기본값 또는 빈 문자열은 모두 일치합니다. | 
| subjectEndsWith | string | 이벤트 메시지의 제목 필드에 대한 접미사-일치 필터입니다. 기본값 또는 빈 문자열은 모두 일치합니다. |
| subjectIsCaseSensitive | string | 필터에 대한 대/소문자 구분 일치를 제어합니다. |


## <a name="example-subscription-schema"></a>예제 구독 스키마

```json
{
  "properties": {
    "destination": {
      "endpointType": "webhook",
      "properties": {
          "endpointUrl": "https://example.azurewebsites.net/api/HttpTriggerCSharp1?code=VXbGWce53l48Mt8wuotr0GPmyJ/nDT4hgdFj9DpBiRt38qqnnm5OFg=="
      }
    },
    "filter": {
      "includedEventTypes": [ "blobCreated", "blobDeleted" ],
      "subjectBeginsWith": "blobServices/default/containers/mycontainer/log",
      "subjectEndsWith": ".jpg",
      "subjectIsCaseSensitive": "true"
    }
  }
}
```

## <a name="next-steps"></a>다음 단계

* Event Grid에 대한 소개는 [Event Grid란?](overview.md)을 참조하세요.
