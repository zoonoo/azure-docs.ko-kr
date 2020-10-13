---
author: baanders
description: Azure Digital Twins 경로 필터 옵션에 대 한 파일 포함
ms.service: digital-twins
ms.topic: include
ms.date: 8/3/2020
ms.author: baanders
ms.openlocfilehash: a1098088a38b23ec1074434e5424e261e60bcd55
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91779171"
---
| 필터 이름 | 설명 | 필터 텍스트 스키마 | 지원되는 값 | 
| --- | --- | --- | --- |
| True/False | 필터링 없이 경로를 만들거나, 이벤트를 보내지 않도록 경로를 사용 하지 않도록 설정할 수 있습니다. | `<true/false>` | `true` = 필터링 없이 경로를 사용할 수 있습니다. <br> `false` = 경로를 사용할 수 없음 |
| 유형 | 디지털 쌍 인스턴스를 통해 흐르는 [이벤트의 유형입니다](../articles/digital-twins/concepts-route-events.md#types-of-event-messages) . | `type = '<eventType>'` | 가능한 이벤트 유형 값은 다음과 같습니다. <br>`Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| 원본 | Azure Digital Twins 인스턴스의 이름 | `source = '<hostname>'`| 가능한 호스트 이름 값은 다음과 같습니다. <br> **알림**: `<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net` <br> **원격 분석의 경우**: `<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net/digitaltwins/<twinId>`|
| 주체 | 위의 이벤트 원본에 대 한 컨텍스트의 이벤트 설명입니다. | `subject = '<subject>'` | 가능한 주제 값은 다음과 같습니다. <br>**알림의 경우**: 제목은입니다. `<twinid>` <br> 또는 여러 부분이 나 Id로 고유 하 게 식별 되는 주제에 대 한 URI 형식:<br>`<twinid>/relationships/<relationshipid>`<br> **원격 분석의**경우: 주체는와 같은 구성 요소 경로입니다 (쌍으로 원격 분석을 내보내는 경우) `comp1.comp2` . 구성 요소에서 원격 분석을 내보내지 않는 경우에는 해당 제목 필드가 비어 있습니다. |
| 데이터 스키마 | DTDL 모델 ID | `dataschema = '<model-dtmi-ID>'` | **원격 분석의 경우**: 데이터 스키마는 쌍의 모델 ID 또는 원격 분석을 내보내는 구성 요소입니다. 예, `dtmi:example:com:floor4;2` <br>**알림**:의 알림 본문에서 데이터 스키마에 액세스할 수 있습니다. `$body.$metadata.$model`|
| 내용 유형 | 데이터 값의 콘텐츠 형식 | `datacontenttype = '<contentType>'` | 콘텐츠 형식은입니다. `application/json` |
| 사양 버전 | 사용 중인 이벤트 스키마의 버전 | `specversion = '<version>'` | 버전은 이어야 합니다 `1.0` . 이는 CloudEvents 스키마 버전 1.0을 나타냅니다. |
| 알림 본문 | 알림 필드의 모든 속성을 참조 합니다. `data` | `$body.<property>` | 알림 예제 [*는 방법: 이벤트 데이터 이해를*](https://docs.microsoft.com/azure/digital-twins/how-to-interpret-event-data) 참조 하세요. 필드의 모든 속성은를 `data` 사용 하 여 참조할 수 있습니다. `$body`

위의 데이터에 대 한 참조로 반환 되는 값으로 지원 되는 데이터 형식은 다음과 같습니다.

| 데이터 형식 | 예제 |
|-|-|-|
|**String**| `STARTS_WITH($body.$metadate.$model, 'dtmi:example:com:floor')` <br> `CONTAINS(subject, '<twinID>')`|
|**정수**|`$body.errorCode > 200`|
|**double**|`$body.temperature <= 5.5`|
|**Bool**|`$body.poweredOn = true`|
|**Null**|`$body.prop != null`|

경로 필터를 정의할 때 지원 되는 연산자는 다음과 같습니다.

|제품군|연산자|예제|
|-|-|-|
|논리|AND, OR, ()|`(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')`|
|비교|<, <=, >, >=, =,! =|`$body.temperature <= 5.5`

경로 필터를 정의할 때 지원 되는 함수는 다음과 같습니다.

|함수|설명|예제|
|--|--|--|
|STARTS_WITH (x, y)|값이 문자열로 시작 하면 true를 반환 합니다 `x` `y` .|`STARTS_WITH($body.$metadata.$model, 'dtmi:example:com:floor')`|
|ENDS_WITH (x, y) | 값이 문자열로 끝나면 true를 반환 합니다 `x` `y` .|`ENDS_WITH($body.$metadata.$model, 'floor;1')`|
|CONTAINS(x,y)| 값에 문자열이 포함 된 경우 true를 반환 합니다 `x` `y` .|`CONTAINS(subject, '<twinID>')`|

필터를 구현 하거나 업데이트 하는 경우 변경 내용이 데이터 파이프라인에 반영 되는 데 몇 분 정도 걸릴 수 있습니다.
