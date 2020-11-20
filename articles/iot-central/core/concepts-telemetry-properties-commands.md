---
title: Azure IoT Central에서 원격 분석, 속성 및 명령 페이로드 Microsoft Docs
description: Azure IoT Central 장치 템플릿을 사용 하면 장치에서를 구현 해야 하는 원격 분석, 속성 및 명령을 지정할 수 있습니다. 장치가 IoT Central 교환할 수 있는 데이터의 형식을 이해 합니다.
author: dominicbetts
ms.author: dobett
ms.date: 11/05/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 24fbe347aeb0b47ffd1ba694f761d909ff2950f8
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94989550"
---
# <a name="telemetry-property-and-command-payloads"></a>원격 분석, 속성 및 명령 페이로드

_이 문서는 디바이스 개발자에게 적용됩니다._

Azure IoT Central의 장치 템플릿은 다음을 정의 하는 청사진입니다.

* 장치가 IoT Central로 보내는 원격 분석입니다.
* 장치가 IoT Central와 동기화 하는 속성입니다.
* 장치에서 호출을 IoT Central 하는 명령입니다.

이 문서에서는 장치 개발자의 경우 장치 템플릿에서 정의 된 원격 분석, 속성 및 명령에 대해 장치에서 보내고 받는 JSON 페이로드를 설명 합니다.

이 문서에서는 가능한 모든 유형의 원격 분석, 속성 및 명령 페이로드를 설명 하지는 않지만이 예제에서는 모든 주요 유형을 설명 합니다.

각 예제에서는 장치에서 IoT Central 응용 프로그램과 상호 작용 하는 방법을 설명 하는 형식 및 예제 JSON 페이로드를 정의 하는 장치 모델의 조각을 보여 줍니다.

> [!NOTE]
> IoT Central는 유효한 모든 JSON을 허용 하지만 장치 모델의 정의와 일치 하는 경우에만 시각화에 사용할 수 있습니다. 정의와 일치 하지 않는 데이터를 내보낼 수 있습니다. [Azure에서 IoT 데이터를 대상으로 내보내기](howto-export-data.md)를 참조 하세요.

장치 모델을 정의 하는 JSON 파일은 [DTDL (디지털 쌍 정의 언어) v2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)를 사용 합니다.

사용 중인 이러한 페이로드 중 일부를 표시 하는 샘플 장치 코드는 [클라이언트 응용 프로그램을 만들어 azure IoT Central 응용 프로그램에 연결 (Node.js)](tutorial-connect-device-nodejs.md) 하 고 [클라이언트 응용 프로그램을 만들어 azure IoT Central 응용 프로그램 (Python) 자습서에 연결](tutorial-connect-device-python.md) 을 참조 하세요.

## <a name="view-raw-data"></a>원시 데이터 보기

IoT Central를 사용 하 여 장치가 응용 프로그램에 전송 하는 원시 데이터를 볼 수 있습니다. 이 보기는 장치에서 전송 된 페이로드의 문제를 해결 하는 데 유용 합니다. 장치가 전송 하는 원시 데이터를 보려면 다음을 수행 합니다.

1. **장치 페이지에서** 장치로 이동 합니다.

1. **원시 데이터** 탭을 선택 합니다.

    :::image type="content" source="media/concepts-telemetry-properties-commands/raw-data.png" alt-text="원시 데이터 보기":::

    이 보기에서 표시할 열을 선택하고 표시할 시간 범위를 설정할 수 있습니다. **모델링되지 않은 데이터** 열에는 디바이스 템플릿의 속성 또는 원격 분석 정의와 일치하지 않는 디바이스 데이터가 표시됩니다.

## <a name="telemetry"></a>원격 분석

### <a name="primitive-types"></a>기본 형식

이 섹션에서는 장치가 IoT Central 응용 프로그램으로 스트리밍하는 기본 원격 분석 형식의 예를 보여 줍니다.

장치 모델의 다음 코드 조각은 원격 분석 유형의 정의를 보여 줍니다 `boolean` .

```json
{
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

장치 모델의 다음 코드 조각은 원격 분석 유형의 정의를 보여 줍니다 `string` .

```json
{
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

장치 모델의 다음 코드 조각은 원격 분석 유형의 정의를 보여 줍니다 `integer` .

```json
{
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

장치 모델의 다음 코드 조각은 원격 분석 유형의 정의를 보여 줍니다 `double` .

```json
{
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

장치 모델의 다음 코드 조각은 원격 분석 유형의 정의를 보여 줍니다 `dateTime` .

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "DateTimeTelemetry"
  },
  "name": "DateTimeTelemetry",
  "schema": "dateTime"
}
```

장치 클라이언트는 다음 예제와 같이 JSON으로 원격 분석을 전송 해야 합니다 `DateTime` . 형식은 ISO 8061 형식 이어야 합니다.

```json
{ "DateTimeTelemetry": "2020-08-30T19:16:13.853Z" }
```

장치 모델의 다음 코드 조각은 원격 분석 유형의 정의를 보여 줍니다 `duration` .

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "DurationTelemetry"
  },
  "name": "DurationTelemetry",
  "schema": "duration"
}
```

장치 클라이언트는 다음 예와 같이 JSON으로 원격 분석을 전송 해야 합니다. 기간은 ISO 8601 형식 이어야 합니다.

```json
{ "DurationTelemetry": "PT10H24M6.169083011336625S" }
```

### <a name="complex-types"></a>복합 형식

이 섹션에서는 장치가 IoT Central 응용 프로그램으로 스트리밍하는 복잡 한 원격 분석 형식의 예를 보여 줍니다.

장치 모델의 다음 코드 조각은 원격 분석 유형의 정의를 보여 줍니다 `geopoint` .

```json
{
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

장치 모델의 다음 코드 조각은 원격 분석 유형의 정의를 보여 줍니다 `Enum` .

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "EnumTelemetry"
  },
  "name": "EnumTelemetry",
  "schema": {
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
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

장치 모델의 다음 코드 조각은 원격 분석 유형의 정의를 보여 줍니다 `Object` . 이 개체에는, 및 형식의 세 필드가 있습니다 `dateTime` `integer` `Enum` .

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "ObjectTelemetry"
  },
  "name": "ObjectTelemetry",
  "schema": {
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "displayName": {
          "en": "Property1"
        },
        "name": "Property1",
        "schema": "dateTime"
      },
      {
        "displayName": {
          "en": "Property2"
        },
        "name": "Property2",
        "schema": "integer"
      },
      {
        "displayName": {
          "en": "Property3"
        },
        "name": "Property3",
        "schema": {
          "@type": "Enum",
          "displayName": {
            "en": "Enum"
          },
          "valueSchema": "integer",
          "enumValues": [
            {
              "displayName": {
                "en": "Item1"
              },
              "enumValue": 0,
              "name": "Item1"
            },
            {
              "displayName": {
                "en": "Item2"
              },
              "enumValue": 1,
              "name": "Item2"
            },
            {
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

장치 클라이언트는 다음 예제와 같이 JSON으로 원격 분석을 전송 해야 합니다. `DateTime` 형식은 ISO 8061 규격 이어야 합니다. 에 사용할 수 있는 값은, 및 이며 `Property3` `0` `1` IoT Central에, 및로 표시 됩니다 `Item1` `Item2` `Item3` .

```json
{
  "ObjectTelemetry": {
      "Property1": "2020-09-09T03:36:46.195Z",
      "Property2": 37,
      "Property3": 2
  }
}
```

장치 모델의 다음 코드 조각은 원격 분석 유형의 정의를 보여 줍니다 `vector` .

```json
{
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

장치 모델의 다음 코드 조각은 이벤트 유형에 대 한 정의를 보여 줍니다 `integer` .

```json
{
  "@type": [
    "Telemetry",
    "Event"
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

장치 모델의 다음 코드 조각은 상태 형식의 정의를 보여 줍니다 `integer` .

```json
{
  "@type": [
    "Telemetry",
    "State"
  ],
  "displayName": {
    "en": "IntegerState"
  },
  "name": "IntegerState",
  "schema": {
    "@type": "Enum",
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Level1"
        },
        "enumValue": 1,
        "name": "Level1"
      },
      {
        "displayName": {
          "en": "Level2"
        },
        "enumValue": 2,
        "name": "Level2"
      },
      {
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

장치 모델의 다음 코드 조각은 속성 형식의 정의를 보여 줍니다 `boolean` .

```json
{
  "@type": "Property",
  "displayName": {
    "en": "BooleanProperty"
  },
  "name": "BooleanProperty",
  "schema": "boolean",
  "writable": false
}
```

장치 클라이언트는 다음 예제와 같은 JSON 페이로드를 장치 쌍의 보고 된 속성으로 전송 해야 합니다.

```json
{ "BooleanProperty": false }
```

장치 모델의 다음 코드 조각은 속성 형식의 정의를 보여 줍니다 `boolean` .

```json
{
  "@type": "Property",
  "displayName": {
    "en": "LongProperty"
  },
  "name": "LongProperty",
  "schema": "long",
  "writable": false
}
```

장치 클라이언트는 다음 예제와 같은 JSON 페이로드를 장치 쌍의 보고 된 속성으로 전송 해야 합니다.

```json
{ "LongProperty": 439 }
```

장치 모델의 다음 코드 조각은 속성 형식의 정의를 보여 줍니다 `date` .

```json
{
  "@type": "Property",
  "displayName": {
    "en": "DateProperty"
  },
  "name": "DateProperty",
  "schema": "date",
  "writable": false
}
```

장치 클라이언트는 다음 예제와 같은 JSON 페이로드를 장치 쌍에 보고 된 속성으로 전송 해야 합니다. `Date` 형식은 ISO 8061 규격 이어야 합니다.

```json
{ "DateProperty": "2020-05-17" }
```

장치 모델의 다음 코드 조각은 속성 형식의 정의를 보여 줍니다 `duration` .

```json
{
  "@type": "Property",
  "displayName": {
    "en": "DurationProperty"
  },
  "name": "DurationProperty",
  "schema": "duration",
  "writable": false
}
```

장치 클라이언트는 장치 쌍의 보고 된 속성으로 다음 예제와 같은 JSON 페이로드를 전송 해야 합니다. 기간은 ISO 8601 기간 규격 이어야 합니다.

```json
{ "DurationProperty": "PT10H24M6.169083011336625S" }
```

장치 모델의 다음 코드 조각은 속성 형식의 정의를 보여 줍니다 `float` .

```json
{
  "@type": "Property",
  "displayName": {
    "en": "FloatProperty"
  },
  "name": "FloatProperty",
  "schema": "float",
  "writable": false
}
```

장치 클라이언트는 다음 예제와 같은 JSON 페이로드를 장치 쌍의 보고 된 속성으로 전송 해야 합니다.

```json
{ "FloatProperty": 1.9 }
```

장치 모델의 다음 코드 조각은 속성 형식의 정의를 보여 줍니다 `string` .

```json
{
  "@type": "Property",
  "displayName": {
    "en": "StringProperty"
  },
  "name": "StringProperty",
  "schema": "string",
  "writable": false
}
```

장치 클라이언트는 다음 예제와 같은 JSON 페이로드를 장치 쌍의 보고 된 속성으로 전송 해야 합니다.

```json
{ "StringProperty": "A string value - could be a URL" }
```

### <a name="complex-types"></a>복합 형식

이 섹션에서는 장치가 IoT Central 응용 프로그램으로 보내는 복합 속성 형식의 예를 보여 줍니다.

장치 모델의 다음 코드 조각은 속성 형식의 정의를 보여 줍니다 `geopoint` .

```json
{
  "@type": "Property",
  "displayName": {
    "en": "GeopointProperty"
  },
  "name": "GeopointProperty",
  "schema": "geopoint",
  "writable": false
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

장치 모델의 다음 코드 조각은 속성 형식의 정의를 보여 줍니다 `Enum` .

```json
{
  "@type": "Property",
  "displayName": {
    "en": "EnumProperty"
  },
  "name": "EnumProperty",
  "writable": false,
  "schema": {
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
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

장치 모델의 다음 코드 조각은 속성 형식의 정의를 보여 줍니다 `Object` . 이 개체에는 및 형식의 두 필드가 있습니다 `string` `integer` .

```json
{
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
  "writable": false,
  "schema": {
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "displayName": {
          "en": "Field1"
        },
        "name": "Field1",
        "schema": "integer"
      },
      {
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

장치 모델의 다음 코드 조각은 속성 형식의 정의를 보여 줍니다 `vector` .

```json
{
  "@type": "Property",
  "displayName": {
    "en": "VectorProperty"
  },
  "name": "VectorProperty",
  "schema": "vector",
  "writable": false
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

`ac` 는 다음 표의 값을 사용 하는 숫자 필드입니다.

| 값 | 레이블 | Description |
| ----- | ----- | ----------- |
| `'ac': 200` | Completed | 속성 변경 작업을 성공적으로 완료 했습니다. |
| `'ac': 202`  디스크나 `'ac': 201` | Pending | 속성 변경 작업이 보류 중이거나 진행 중입니다. |
| `'ac': 4xx` | 오류 | 요청 된 속성 변경이 잘못 되었거나 오류가 발생 했습니다. |
| `'ac': 5xx` | 오류 | 장치에서 요청 된 변경을 처리 하는 동안 예기치 않은 오류가 발생 했습니다. |

`av` 장치에 전송 된 버전 번호입니다.

`ad` 는 옵션 문자열 설명입니다.

장치 모델의 다음 코드 조각은 쓰기 가능한 속성 형식의 정의를 보여 줍니다 `string` .

```json
{
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

장치 모델의 다음 코드 조각은 쓰기 가능한 속성 형식의 정의를 보여 줍니다 `Enum` .

```json
{
  "@type": "Property",
  "displayName": {
    "en": "EnumPropertyWritable"
  },
  "name": "EnumPropertyWritable",
  "writable": true,
  "schema": {
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
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

> [!NOTE]
> IoT Central 웹 UI에서 명령에 대 한 **오프 라인 상태인 경우 큐** 옵션을 선택할 수 있습니다. 이 설정은 장치 템플릿에서 모델 또는 인터페이스를 내보내는 경우에는 포함 되지 않습니다.

장치 모델의 다음 코드 조각은 매개 변수가 없고 장치에서 아무것도 반환 하지 않는 명령의 정의를 보여 줍니다.

```json
{
  "@type": "Command",
  "displayName": {
    "en": "CommandBasic"
  },
  "name": "CommandBasic"
}
```

장치는 요청에서 빈 페이로드를 받고 `200` 성공 여부를 나타내는 HTTP 응답 코드와 함께 응답에 빈 페이로드를 반환 해야 합니다.

장치 모델의 다음 코드 조각은 정수 매개 변수를 포함 하 고 장치에서 정수 값을 반환 해야 하는 명령의 정의를 보여 줍니다.

```json
{
  "@type": "Command",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": "integer"
  },
  "response": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": "integer"
  },
  "displayName": {
    "en": "CommandSimple"
  },
  "name": "CommandSimple"
}
```

장치는 요청 페이로드에서 정수 값을 받습니다. 장치는 `200` 성공 여부를 나타내는 HTTP 응답 코드를 사용 하 여 응답 페이로드로 정수 값을 반환 해야 합니다.

장치 모델의 다음 코드 조각은 개체 매개 변수를 포함 하 고 장치에서 개체를 반환 해야 하는 명령의 정의를 보여 줍니다. 이 예제에서는 두 개체 모두에 정수 및 문자열 필드가 있습니다.

```json
{
  "@type": "Command",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": {
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "displayName": {
            "en": "Field1"
          },
          "name": "Field1",
          "schema": "integer"
        },
        {
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
    "@type": "CommandPayload",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": {
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "displayName": {
            "en": "Field1"
          },
          "name": "Field1",
          "schema": "integer"
        },
        {
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
    "en": "CommandComplex"
  },
  "name": "CommandComplex"
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

### <a name="long-running-commands"></a>장기 실행 명령

장치 모델의 다음 코드 조각은 명령의 정의를 보여 줍니다. 명령에는 정수 매개 변수가 있으며 장치에서 정수 값을 반환 해야 합니다.

```json
{
  "@type": "Command",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": "integer"
  },
  "response": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": "integer"
  },
  "displayName": {
    "en": "LongRunningCommandSimple"
  },
  "name": "LongRunningCommandSimple"
}
```

장치는 요청 페이로드에서 정수 값을 받습니다. 장치에서이 명령을 처리 하는 데 시간이 필요한 경우 `202` 장치에서 처리 요청을 수락 했음을 나타내는 HTTP 응답 코드와 함께 빈 응답 페이로드를 반환 해야 합니다.

장치에서 요청 처리가 완료 되 면 다음 예제와 같이 IoT Central에 속성을 보내야 합니다. 속성 이름은 명령 이름과 동일 해야 합니다.

```json
{
  "LongRunningCommandSimple": {
    "value": 87
  }
}
```

## <a name="next-steps"></a>다음 단계

장치 개발자로 서 이제 장치 템플릿에 대해 알아보았습니다. 이제 제안 된 다음 단계는 [Azure IoT Central에](./concepts-get-connected.md) 대 한 연결을 읽어 IoT Central에 장치를 등록 하는 방법 및 장치 연결을 IoT Central 하는 방법에 대 한 자세한 내용을 확인 하는 것입니다.
