---
author: baanders
description: Azure Digital Twins 경로 메트릭 옵션에 대한 포함 파일
ms.service: digital-twins
ms.topic: include
ms.date: 12/04/2020
ms.author: baanders
ms.openlocfilehash: d93f484e318c10489eb1db3e9c65c6e0c7479c90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98859857"
---
| 필터 이름 | 설명 | 텍스트 스키마 필터링 | 지원되는 값 | 
| --- | --- | --- | --- |
| True / False | 필터링 없이 경로를 만들거나, 이벤트를 보내지 않도록 경로를 비활성화할 수 있습니다. | `<true/false>` | `true` = 경로가 필터링 없이 활성화됨 <br> `false` = 경로가 비활성화됨 |
| 형식 | 디지털 트윈 인스턴스를 통해 흐르는 [이벤트 유형](../articles/digital-twins/concepts-route-events.md#types-of-event-messages) | `type = '<eventType>'` | 가능한 이벤트 유형 값은 다음과 같습니다. <br>`Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| 원본 | Azure Digital Twins 인스턴스의 이름 | `source = '<hostname>'`| 가능한 호스트 이름 값은 다음과 같습니다. <br> **알림의 경우**: `<yourDigitalTwinInstance>.api.<yourRegion>.digitaltwins.azure.net` <br> **원격 분석에 경우**: `<yourDigitalTwinInstance>.api.<yourRegion>.digitaltwins.azure.net/<twinId>`|
| 제목 | 위의 이벤트 원본의 컨텍스트에 있는 이벤트 설명 | `subject = '<subject>'` | 가능한 주체 값은 다음과 같습니다. <br>**알림의 경우**: 주체는 `<twinid>`입니다. <br> 또는 여러 부분이나 ID로 고유하게 식별되는 주체에 대한 URI 형식:<br>`<twinid>/relationships/<relationshipid>`<br> **원격 분석의 경우**: 주체는 `comp1.comp2`와 같은 구성 요소 경로(트윈 구성 요소에서 원격 분석을 내보내는 경우)입니다. 구성 요소에서 원격 분석을 내보내지 않으면 해당 주체 필드가 비어 있습니다. |
| 데이터 스키마 | DTDL 모델 ID | `dataschema = '<model-dtmi-ID>'` | **원격 분석의 경우**: 데이터 스키마는 원격 분석을 내보내는 트윈 또는 구성 요소의 모델 ID입니다. 예를 들어 `dtmi:example:com:floor4;2` <br>**알림(만들기/삭제)의 경우**: 데이터 스카마는 `$body.$metadata.$model`의 알림 본문에서 액세스할 수 있습니다. <br>**알림(업데이트)의 경우**: 데이터 스카마는 `$body.modelId`의 알림 본문에서 액세스할 수 있습니다.|
| 내용 유형 | 데이터 값의 콘텐츠 형식 | `datacontenttype = '<contentType>'` | 콘텐츠 형식은 `application/json`입니다. |
| 사양 버전 | 사용 중인 이벤트 스키마의 버전 | `specversion = '<version>'` | 버전은 `1.0`이어야 합니다. 이는 CloudEvents 스키마 버전 1.0을 나타냅니다. |
| 알림 본문 | 알림의 `data` 필드에 있는 모든 속성 참조 | `$body.<property>` | 알림 예제는 [*방법: 이벤트 데이터 이해*](../articles/digital-twins/how-to-interpret-event-data.md)를 참조하세요. `data` 필드의 모든 속성은 `$body`를 사용하여 참조할 수 있습니다.

위의 데이터에 대한 참조로 반환되는 값으로 지원되는 데이터 형식은 다음과 같습니다.

| 데이터 형식 | 예제 |
|-|-|-|
|**String**| `STARTS_WITH($body.$metadata.$model, 'dtmi:example:com:floor')` <br> `CONTAINS(subject, '<twinID>')`|
|**Integer**|`$body.errorCode > 200`|
|**double**|`$body.temperature <= 5.5`|
|**Bool**|`$body.poweredOn = true`|
|**Null**|`$body.prop != null`|

경로 필터를 정의할 때 지원되는 연산자는 다음과 같습니다.

|패밀리|연산자|예제|
|-|-|-|
|논리|AND, OR, ( )|`(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')`|
|비교|<, <=, >, >=, =, !=|`$body.temperature <= 5.5`

경로 필터를 정의할 때 지원되는 함수는 다음과 같습니다.

|함수|설명|예제|
|--|--|--|
|STARTS_WITH(x,y)|값 `x`가 문자열 `y`로 시작하면 true를 반환합니다.|`STARTS_WITH($body.$metadata.$model, 'dtmi:example:com:floor')`|
|ENDS_WITH(x, y) | 값 `x`가 문자열 `y`로 끝나면 true를 반환합니다.|`ENDS_WITH($body.$metadata.$model, 'floor;1')`|
|CONTAINS(x,y)| 값 `x`에 문자열 `y`가 포함된 경우 true를 반환합니다.|`CONTAINS(subject, '<twinID>')`|

필터를 구현하거나 업데이트하는 경우 변경 내용이 데이터 파이프라인에 반영되는 데 몇 분 정도 걸릴 수 있습니다.