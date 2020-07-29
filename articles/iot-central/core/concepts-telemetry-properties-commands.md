---
title: Azure IoT Central에서 원격 분석, 속성 및 명령 페이로드 Microsoft Docs
description: Azure IoT Central 장치 템플릿을 사용 하면 장치에서를 구현 해야 하는 원격 분석, 속성 및 명령을 지정할 수 있습니다. 장치가 IoT Central 교환할 수 있는 데이터의 형식을 이해 합니다.
author: dominicbetts
ms.author: dobett
ms.date: 06/12/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 554079ddec3332ced2817d18ea55ce1260d68817
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87291617"
---
# <a name="telemetry-property-and-command-payloads"></a>원격 분석, 속성 및 명령 페이로드

_이 문서는 디바이스 개발자에게 적용됩니다._

Azure IoT Central의 장치 템플릿은 다음을 정의 하는 청사진입니다.

* 장치가 IoT Central로 보내는 원격 분석입니다.
* 장치가 IoT Central와 동기화 하는 속성입니다.
* 장치에서 호출을 IoT Central 하는 명령입니다.

이 문서에서는 장치 개발자의 경우 장치 템플릿에서 정의 된 원격 분석, 속성 및 명령에 대해 장치에서 보내고 받는 JSON 페이로드를 설명 합니다.

이 문서에서는 가능한 모든 유형의 원격 분석, 속성 및 명령 페이로드를 설명 하지는 않지만이 예제에서는 모든 주요 유형을 설명 합니다.

각 예제에서는 장치에서 IoT Central 응용 프로그램과 상호 작용 하는 방법을 설명 하는 형식 및 예제 JSON 페이로드를 정의 하는 DCM (장치 기능 모델)의 조각을 보여 줍니다.

> [!NOTE]
> IoT Central는 유효한 모든 JSON을 허용 하지만 DCM의 정의와 일치 하는 경우 시각화에만 사용할 수 있습니다. 정의와 일치 하지 않는 데이터를 내보낼 수 있습니다. [Azure에서 IoT 데이터를 대상으로 내보내기](howto-export-data.md)를 참조 하세요.

DCM을 정의 하는 JSON 파일은 [DTDL (디지털 쌍 정의 언어) V1](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/dtdlv1.md)을 사용 합니다. 이 사양에는 속성 형식에 대 한 정의가 포함 됩니다 `@id` .

사용 중인 이러한 페이로드 중 일부를 표시 하는 샘플 장치 코드는 [클라이언트 응용 프로그램을 만들어 azure IoT Central 응용 프로그램에 연결 (Node.js)](tutorial-connect-device-nodejs.md) 하 고 [클라이언트 응용 프로그램을 만들어 azure IoT Central 응용 프로그램 (Python) 자습서에 연결](tutorial-connect-device-python.md) 을 참조 하세요.

## <a name="view-raw-data"></a>원시 데이터 보기

IoT Central를 사용 하 여 장치가 응용 프로그램에 전송 하는 원시 데이터를 볼 수 있습니다. 이 보기는 장치에서 전송 된 페이로드의 문제를 해결 하는 데 유용 합니다. 장치가 전송 하는 원시 데이터를 보려면 다음을 수행 합니다.

1. **장치 페이지에서** 장치로 이동 합니다.

1. **원시 데이터** 탭을 선택 합니다.

    :::image type="content" source="media/concepts-telemetry-properties-commands/raw-data.png" alt-text="원시 데이터 뷰":::

    이 뷰에서 표시할 열을 선택 하 고 표시할 시간 범위를 설정할 수 있습니다. 모델링 되지 않은 **데이터** 열은 장치 템플릿의 속성 또는 원격 분석 정의와 일치 하지 않는 장치의 데이터를 표시 합니다.

## <a name="telemetry"></a>원격 분석

### <a name="primitive-types"></a>기본 형식

이 섹션에서는 장치가 IoT Central 응용 프로그램으로 스트리밍하는 기본 원격 분석 형식의 예를 보여 줍니다.

DCM의 다음 코드 조각은 원격 분석 유형의 정의를 보여 줍니다 `boolean` .

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "BooleanTelemetry"
  },
  "name": "BooleanTelemetry",
  "schema": "boolean"
}
```

장치 클라이언트는 다음 예제와 같이 JSON으로 원격 분석을 전송 해야 합니다.

```json
{ "BooleanTelemetry": true }
```

DCM의 다음 코드 조각은 원격 분석 유형의 정의를 보여 줍니다 `string` .

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "StringTelemetry"
  },
  "name": "StringTelemetry",
  "schema": "string"
}
```

장치 클라이언트는 다음 예제와 같이 JSON으로 원격 분석을 전송 해야 합니다.

```json
{ "StringTelemetry": "A string value - could be a URL" }
```

DCM의 다음 코드 조각은 원격 분석 유형의 정의를 보여 줍니다 `integer` .

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "IntegerTelemetry"
  },
  "name": "IntegerTelemetry",
  "schema": "integer"
}

```

장치 클라이언트는 다음 예제와 같이 JSON으로 원격 분석을 전송 해야 합니다.

```json
{ "IntegerTelemetry": 23 }
```

DCM의 다음 코드 조각은 원격 분석 유형의 정의를 보여 줍니다 `double` .

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "DoubleTelemetry"
  },
  "name": "DoubleTelemetry",
  "schema": "double"
}
```

장치 클라이언트는 다음 예제와 같이 JSON으로 원격 분석을 전송 해야 합니다.

```json
{ "DoubleTelemetry": 56.78 }
```

DCM의 다음 코드 조각은 원격 분석 유형의 정의를 보여 줍니다 `dateTime` .

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "DateTimeTelemetry"
  },
  "name": "DateTimeTelemetry",
  "schema": "dateTime"
}
```

장치 클라이언트는 다음 예제와 같이 JSON으로 원격 분석을 전송 해야 합니다 `DateTime` . 형식은 ISO 8061 규격 이어야 합니다.

```json
{ "DateTimeTelemetry": "2020-08-30T19:16:13.853Z" }
```

DCM의 다음 코드 조각은 원격 분석 유형의 정의를 보여 줍니다 `duration` .

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "DurationTelemetry"
  },
  "name": "DurationTelemetry",
  "schema": "duration"
}
```

장치 클라이언트는 다음 예제와 같이 JSON으로 원격 분석을 전송 해야 합니다. 기간은 ISO 8601 기간 규격 이어야 합니다.

```json
{ "DurationTelemetry": "PT10H24M6.169083011336625S" }
```

### <a name="complex-types"></a>복합 형식

이 섹션에서는 장치가 IoT Central 응용 프로그램으로 스트리밍하는 복잡 한 원격 분석 형식의 예를 보여 줍니다.

DCM의 다음 코드 조각은 원격 분석 유형의 정의를 보여 줍니다 `geopoint` .

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "GeopointTelemetry"
  },
  "name": "GeopointTelemetry",
  "schema": "geopoint"
}
```

장치 클라이언트는 다음 예제와 같이 JSON으로 원격 분석을 전송 해야 합니다. IoT Central는 맵에 값을 pin으로 표시 합니다.

```json
{
  "GeopointTelemetry": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

DCM의 다음 코드 조각은 원격 분석 유형의 정의를 보여 줍니다 `Enum` .

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "EnumTelemetry"
  },
  "name": "EnumTelemetry",
  "schema": {
    "@id": "<element id>",
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

장치 클라이언트는 다음 예제와 같이 JSON으로 원격 분석을 전송 해야 합니다. 가능한 값은, 및 이며 IoT Central는, `0` `1` `2` 및로 표시 됩니다 `Item1` `Item2` `Item3` .

```json
{ "EnumTelemetry": 1 }
```

DCM의 다음 코드 조각은 원격 분석 형식에 대 한 정의를 보여 줍니다 `Object` . 이 개체에는, 및 형식의 세 필드가 있습니다 `dateTime` `integer` `Enum` .

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "ObjectTelemetry"
  },
  "name": "ObjectTelemetry",
  "schema": {
    "@id": "<element id>",
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Property1"
        },
        "name": "Property1",
        "schema": "dateTime"
      },
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Property2"
        },
        "name": "Property2",
        "schema": "integer"
      },
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Property3"
        },
        "name": "Property3",
        "schema": {
          "@id": "<element id>",
          "@type": "Enum",
          "displayName": {
            "en": "Enum"
          },
          "valueSchema": "integer",
          "enumValues": [
            {
              "@id": "<element id>",
              "@type": "EnumValue",
              "displayName": {
                "en": "Item1"
              },
              "enumValue": 0,
              "name": "Item1"
            },
            {
              "@id": "<element id>",
              "@type": "EnumValue",
              "displayName": {
                "en": "Item2"
              },
              "enumValue": 1,
              "name": "Item2"
            },
            {
              "@id": "<element id>",
              "@type": "EnumValue",
              "displayName": {
                "en": "Item3"
              },
              "enumValue": 2,
              "name": "Item3"
            }
          ]
        }
      }
    ]
  }
}
```

장치 클라이언트는 다음 예제와 같이 JSON으로 원격 분석을 전송 해야 합니다. `DateTime`형식은 ISO 8061 규격 이어야 합니다. 에 사용할 수 있는 값은, 및 이며 `Property3` `0` `1` IoT Central에, 및로 표시 됩니다 `Item1` `Item2` `Item3` .

```json
{
  "ObjectTelemetry": {
      "Property1": "2020-09-09T03:36:46.195Z",
      "Property2": 37,
      "Property3": 2
  }
}
```

DCM의 다음 코드 조각은 원격 분석 유형의 정의를 보여 줍니다 `vector` .

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "VectorTelemetry"
  },
  "name": "VectorTelemetry",
  "schema": "vector"
}
```

장치 클라이언트는 다음 예제와 같이 JSON으로 원격 분석을 전송 해야 합니다.

```json
{
  "VectorTelemetry": {
    "x": 74.72395045538597,
    "y": 74.72395045538597,
    "z": 74.72395045538597
  }
}
```

### <a name="event-and-state-types"></a>이벤트 및 상태 유형

이 섹션에서는 장치가 IoT Central 응용 프로그램으로 보내는 원격 분석 이벤트 및 상태의 예를 보여 줍니다.

DCM의 다음 코드 조각은 이벤트 유형 정의를 보여 줍니다 `integer` .

```json
{
  "@id": "<element id>",
  "@type": [
    "Telemetry",
    "SemanticType/Event"
  ],
  "displayName": {
    "en": "IntegerEvent"
  },
  "name": "IntegerEvent",
  "schema": "integer"
}
```

장치 클라이언트는 다음 예제와 같은 JSON으로 이벤트 데이터를 전송 해야 합니다.

```json
{ "IntegerEvent": 74 }
```

DCM의 다음 코드 조각은 상태 유형에 대 한 정의를 보여 줍니다 `integer` .

```json
{
  "@id": "<element id>",
  "@type": [
    "Telemetry",
    "SemanticType/State"
  ],
  "displayName": {
    "en": "IntegerState"
  },
  "name": "IntegerState",
  "schema": {
    "@id": "<element id>",
    "@type": "Enum",
    "valueSchema": "integer",
    "enumValues": [
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Level1"
        },
        "enumValue": 1,
        "name": "Level1"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Level2"
        },
        "enumValue": 2,
        "name": "Level2"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Level3"
        },
        "enumValue": 3,
        "name": "Level3"
      }
    ]
  }
}
```

장치 클라이언트는 다음 예제와 같은 JSON으로 상태를 전송 해야 합니다. 가능한 정수 상태 값은 `1` , `2` 또는입니다 `3` .

```json
{ "IntegerState": 2 }
```

## <a name="properties"></a>속성

> [!NOTE]
> 속성의 페이로드 형식은 07/14/2020 후에 생성 된 응용 프로그램에 적용 됩니다.

### <a name="primitive-types"></a>기본 형식

이 섹션에서는 장치가 IoT Central 응용 프로그램으로 전송 하는 기본 속성 형식의 예를 보여 줍니다.

DCM의 다음 코드 조각은 속성 형식 정의를 보여 줍니다 `boolean` .

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "BooleanProperty"
  },
  "name": "BooleanProperty",
  "schema": "boolean"
}
```

장치 클라이언트는 다음 예제와 같은 JSON 페이로드를 장치 쌍의 보고 된 속성으로 전송 해야 합니다.

```json
{ "BooleanProperty": false }
```

DCM의 다음 코드 조각은 속성 형식 정의를 보여 줍니다 `boolean` .

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "LongProperty"
  },
  "name": "LongProperty",
  "schema": "long"
}
```

장치 클라이언트는 다음 예제와 같은 JSON 페이로드를 장치 쌍의 보고 된 속성으로 전송 해야 합니다.

```json
{ "LongProperty": 439 }
```

DCM의 다음 코드 조각은 속성 형식 정의를 보여 줍니다 `date` .

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "DateProperty"
  },
  "name": "DateProperty",
  "schema": "date"
}
```

장치 클라이언트는 다음 예제와 같은 JSON 페이로드를 장치 쌍에 보고 된 속성으로 전송 해야 합니다. `Date`형식은 ISO 8061 규격 이어야 합니다.

```json
{ "DateProperty": "2020-05-17" }
```

DCM의 다음 코드 조각은 속성 형식 정의를 보여 줍니다 `duration` .

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "DurationProperty"
  },
  "name": "DurationProperty",
  "schema": "duration"
}
```

장치 클라이언트는 장치 쌍의 보고 된 속성으로 다음 예제와 같은 JSON 페이로드를 전송 해야 합니다. 기간은 ISO 8601 기간 규격 이어야 합니다.

```json
{ "DurationProperty": "PT10H24M6.169083011336625S" }
```

DCM의 다음 코드 조각은 속성 형식 정의를 보여 줍니다 `float` .

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "FloatProperty"
  },
  "name": "FloatProperty",
  "schema": "float"
}
```

장치 클라이언트는 다음 예제와 같은 JSON 페이로드를 장치 쌍의 보고 된 속성으로 전송 해야 합니다.

```json
{ "FloatProperty": 1.9 }
```

DCM의 다음 코드 조각은 속성 형식 정의를 보여 줍니다 `string` .

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "StringProperty"
  },
  "name": "StringProperty",
  "schema": "string"
}
```

장치 클라이언트는 다음 예제와 같은 JSON 페이로드를 장치 쌍의 보고 된 속성으로 전송 해야 합니다.

```json
{ "StringProperty": "A string value - could be a URL" }
```

### <a name="complex-types"></a>복합 형식

이 섹션에서는 장치가 IoT Central 응용 프로그램으로 보내는 복합 속성 형식의 예를 보여 줍니다.

DCM의 다음 코드 조각은 속성 형식 정의를 보여 줍니다 `geopoint` .

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "GeopointProperty"
  },
  "name": "GeopointProperty",
  "schema": "geopoint"
}
```

장치 클라이언트는 다음 예제와 같은 JSON 페이로드를 장치 쌍의 보고 된 속성으로 전송 해야 합니다.

```json
{
  "GeopointProperty": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

DCM의 다음 코드 조각은 속성 형식 정의를 보여 줍니다 `Enum` .

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "EnumProperty"
  },
  "name": "EnumProperty",
  "schema": {
    "@id": "<element id>",
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

장치 클라이언트는 다음 예제와 같은 JSON 페이로드를 장치 쌍에 보고 된 속성으로 전송 해야 합니다. 가능한 값은, 및 이며 IoT Central는, `0` `1` 및로 표시 됩니다 `Item1` `Item2` `Item3` .

```json
{ "EnumProperty": 1 }
```

DCM의 다음 코드 조각은 속성 형식의 정의를 보여 줍니다 `Object` . 이 개체에는 및 형식의 두 필드가 있습니다 `string` `integer` .

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
  "schema": {
    "@id": "<element id>",
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Field1"
        },
        "name": "Field1",
        "schema": "integer"
      },
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Field2"
        },
        "name": "Field2",
        "schema": "string"
      }
    ]
  }
}
```

장치 클라이언트는 다음 예제와 같은 JSON 페이로드를 장치 쌍의 보고 된 속성으로 전송 해야 합니다.

```json
{
  "ObjectProperty": {
    "Field1": 37,
    "Field2": "A string value"
  }
}
```

DCM의 다음 코드 조각은 속성 형식 정의를 보여 줍니다 `vector` .

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "VectorProperty"
  },
  "name": "VectorProperty",
  "schema": "vector"
}
```

장치 클라이언트는 다음 예제와 같은 JSON 페이로드를 장치 쌍의 보고 된 속성으로 전송 해야 합니다.

```json
{
  "VectorProperty": {
    "x": 74.72395045538597,
    "y": 74.72395045538597,
    "z": 74.72395045538597
  }
}
```

### <a name="writeable-property-types"></a>쓰기 가능한 속성 형식

이 섹션에서는 장치가 IoT Central 응용 프로그램에서 수신 하는 쓰기 가능한 속성 형식의 예를 보여 줍니다.

IoT Central 장치에서 쓰기 가능한 속성 업데이트에 대 한 응답을 예상 합니다. 응답 메시지에는 및 필드가 포함 되어야 합니다 `ac` `av` . `ad` 필드는 선택 사항입니다. 예제는 다음 코드 조각을 참조 하세요.

`ac`는 다음 표의 값을 사용 하는 숫자 필드입니다.

| 값 | 레이블 | Description |
| ----- | ----- | ----------- |
| `'ac': 200` | Completed | 속성 변경 작업을 성공적으로 완료 했습니다. |
| `'ac': 202`디스크나`'ac': 201` | Pending | 속성 변경 작업이 보류 중이거나 진행 중입니다. |
| `'ac': 4xx` | Error | 요청 된 속성 변경이 잘못 되었거나 오류가 발생 했습니다. |
| `'ac': 5xx` | Error | 장치에서 요청 된 변경을 처리 하는 동안 예기치 않은 오류가 발생 했습니다. |

`av`장치에 전송 된 버전 번호입니다.

`ad`는 옵션 문자열 설명입니다.

DCM의 다음 코드 조각은 쓰기 가능한 속성 형식의 정의를 보여 줍니다 `string` .

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "StringPropertyWritable"
  },
  "name": "StringPropertyWritable",
  "writable": true,
  "schema": "string"
}
```

장치는 IoT Central에서 다음 페이로드를 수신 합니다.

```json
{  
  "StringPropertyWritable": "A string from IoT Central", "$version": 7
}
```

장치는 업데이트를 처리 한 후 IoT Central에 다음 JSON 페이로드를 전송 해야 합니다. 이 메시지는 IoT Central에서 받은 원래 업데이트의 버전 번호를 포함 합니다. IoT Central이 메시지를 받으면 UI에서 속성을 **동기화** 된 것으로 표시 합니다.

```json
{
  "StringPropertyWritable": {
    "value": "A string from IoT Central",
    "ac": 200,
    "ad": "completed",
    "av": 7
  }
}
```

DCM의 다음 코드 조각은 쓰기 가능한 속성 형식의 정의를 보여 줍니다 `Enum` .

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "EnumPropertyWritable"
  },
  "name": "EnumPropertyWritable",
  "writable": true,
  "schema": {
    "@id": "<element id>",
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

장치는 IoT Central에서 다음 페이로드를 수신 합니다.

```json
{  
  "EnumPropertyWritable":  1 , "$version": 10
}
```

장치는 업데이트를 처리 한 후 IoT Central에 다음 JSON 페이로드를 전송 해야 합니다. 이 메시지는 IoT Central에서 받은 원래 업데이트의 버전 번호를 포함 합니다. IoT Central이 메시지를 받으면 UI에서 속성을 **동기화** 된 것으로 표시 합니다.

```json
{
  "EnumPropertyWritable": {
    "value": 1,
    "ac": 200,
    "ad": "completed",
    "av": 10
  }
}
```

## <a name="commands"></a>명령

### <a name="synchronous-command-types"></a>동기 명령 유형

DCM의 다음 코드 조각은 매개 변수가 없고 장치에서 아무것도 반환 하지 않는 동기 명령의 정의를 보여 줍니다.

```json
{
  "@id": "<element id>",
  "@type": "Command",
  "commandType": "synchronous",
  "durable": false,
  "displayName": {
    "en": "SynchronousCommandBasic"
  },
  "name": "SynchronousCommandBasic"
}
```

장치는 요청에서 빈 페이로드를 받고 `200` 성공 여부를 나타내는 HTTP 응답 코드와 함께 응답에 빈 페이로드를 반환 해야 합니다.

DCM의 다음 코드 조각은 정수 매개 변수를 포함 하 고 장치에서 정수 값을 반환 해야 하는 동기 명령의 정의를 보여 줍니다.

```json
{
  "@id": "<element id>",
  "@type": "Command",
  "commandType": "synchronous",
  "durable": false,
  "request": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": "integer"
  },
  "response": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": "integer"
  },
  "displayName": {
    "en": "SynchronousCommandSimple"
  },
  "name": "SynchronousCommandSimple"
}
```

장치는 요청 페이로드에서 정수 값을 받습니다. 장치는 `200` 성공 여부를 나타내는 HTTP 응답 코드를 사용 하 여 응답 페이로드로 정수 값을 반환 해야 합니다.

DCM의 다음 코드 조각은 개체 매개 변수를 포함 하며 장치에서 개체를 반환할 것으로 예상 하는 동기 명령의 정의를 보여 줍니다. 이 예제에서는 두 개체 모두에 정수 및 문자열 필드가 있습니다.

```json
{
  "@id": "<element id>",
  "@type": "Command",
  "commandType": "synchronous",
  "durable": false,
  "request": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": {
      "@id": "<element id>",
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "@id": "<element id>",
          "@type": "SchemaField",
          "displayName": {
            "en": "Field1"
          },
          "name": "Field1",
          "schema": "integer"
        },
        {
          "@id": "<element id>",
          "@type": "SchemaField",
          "displayName": {
            "en": "Field2"
          },
          "name": "Field2",
          "schema": "string"
        }
      ]
    }
  },
  "response": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": {
      "@id": "<element id>",
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "@id": "<element id>",
          "@type": "SchemaField",
          "displayName": {
            "en": "Field1"
          },
          "name": "Field1",
          "schema": "integer"
        },
        {
          "@id": "<element id>",
          "@type": "SchemaField",
          "displayName": {
            "en": "Field2"
          },
          "name": "Field2",
          "schema": "string"
        }
      ]
    }
  },
  "displayName": {
    "en": "SynchronousCommandComplex"
  },
  "name": "SynchronousCommandComplex"
}
```

다음 코드 조각은 장치에 전송 된 예제 요청 페이로드를 보여줍니다.

```json
{ "Field1": 56, "Field2": "A string value" }
```

다음 코드 조각은 장치에서 보낸 예제 응답 페이로드를 보여 줍니다. `200`HTTP 응답 코드를 사용 하 여 성공 여부를 표시 합니다.

```json
{ "Field1": 87, "Field2": "Another string value" }
```

### <a name="asynchronous-command-types"></a>비동기 명령 유형

DCM의 다음 코드 조각은 비동기 명령의 정의를 보여 줍니다. 명령에는 정수 매개 변수가 있으며 장치에서 정수 값을 반환 해야 합니다.

```json
{
  "@id": "<element id>",
  "@type": "Command",
  "commandType": "asynchronous",
  "durable": false,
  "request": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": "integer"
  },
  "response": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": "integer"
  },
  "displayName": {
    "en": "AsynchronousCommandSimple"
  },
  "name": "AsynchronousCommandSimple"
}
```

장치는 요청 페이로드에서 정수 값을 받습니다. 장치는 `202` 비동기 처리에 대 한 요청을 수락 했음을 나타내는 HTTP 응답 코드와 함께 빈 응답 페이로드를 반환 해야 합니다.

장치에서 요청 처리가 완료 되 면 다음 예제와 같이 IoT Central에 속성을 보내야 합니다. 속성 이름은 명령 이름과 동일 해야 합니다.

```json
{
  "AsynchronousCommandSimple": {
    "value": 87
  }
}
```

## <a name="next-steps"></a>다음 단계

장치 개발자로 서 이제 장치 템플릿에 대해 알아보았습니다. 이제 제안 된 다음 단계는 [Azure IoT Central에](./concepts-get-connected.md) 대 한 연결을 읽어 IoT Central에 장치를 등록 하는 방법 및 장치 연결을 IoT Central 하는 방법에 대 한 자세한 내용을 확인 하는 것입니다.
