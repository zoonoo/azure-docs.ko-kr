---
title: IoT 플러그 앤 플레이 디지털 트윈 이해
description: IoT 플러그 앤 플레이에서 디지털 쌍을 사용 하는 방법 이해
author: prashmo
ms.author: prashmo
ms.date: 07/17/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: f13230c7bd88a9c3cf043fc1881a34f6b7ce6fe7
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95495324"
---
# <a name="understand-iot-plug-and-play-digital-twins"></a>IoT 플러그 앤 플레이 디지털 트윈 이해

IoT 플러그 앤 플레이 장치는 [DTDL (디지털 Twins 정의 언어)](https://github.com/Azure/opendigitaltwins-dtdl) 스키마에서 설명 하는 모델을 구현 합니다. 모델은 특정 장치에 포함 될 수 있는 구성 요소, 속성, 명령 및 원격 분석 메시지 집합을 설명 합니다. IoT 플러그 앤 플레이 장치가 IoT hub에 처음 연결할 때 장치 쌍과 디지털 쌍이 초기화 됩니다.

IoT 플러그 앤 플레이는 DTDL 버전 2를 사용 합니다. 이 버전에 대 한 자세한 내용은 GitHub의 [DTDL (디지털 Twins 정의 언어)-버전 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) 사양을 참조 하십시오.

DTDL은 IoT 플러그 앤 플레이에만 국한 되지 않습니다. [Azure Digital Twins](../digital-twins/overview.md)와 같은 다른 IoT 서비스는이를 사용 하 여 건물 및 에너지 네트워크와 같은 전체 환경을 나타냅니다. 자세히 알아보려면 [Azure Digital Twins의 쌍 모델 이해](../digital-twins/concepts-models.md)를 참조 하세요.

이 문서에서는 구성 요소 및 속성이 장치 쌍의 *원하는* 및 *보고* 된 섹션에 어떻게 표시 되는지 설명 합니다. 또한 이러한 개념이 해당 디지털 트윈에 매핑되는 방식을 설명합니다.

이 문서의 IoT 플러그 앤 플레이 장치는 [자동 온도 조절기](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) 구성 요소를 사용 하 여 [온도 컨트롤러 모델](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) 을 구현 합니다.

## <a name="device-twins-and-digital-twins"></a>장치 쌍 및 디지털 쌍

장치 쌍은 메타 데이터, 구성 및 조건을 포함 하 여 장치 상태 정보를 저장 하는 JSON 문서입니다. 자세히 알아보려면 [IoT Hub에서 장치 쌍 이해 및 사용](../iot-hub/iot-hub-devguide-device-twins.md)을 참조 하세요. 장치 및 솔루션 빌더는 모두 동일한 장치 쌍 Api 및 Sdk 집합을 사용 하 여 IoT 플러그 앤 플레이 규칙을 사용 하 여 장치 및 솔루션을 구현할 수 있습니다.

디지털 쌍 Api는 구성 요소, 속성, 명령 등의 DTDL (디지털 쌍 정의 언어)에서 상위 수준 구문에 대해 작동 합니다. 디지털 쌍 Api를 사용 하면 솔루션 빌더가 IoT 플러그 앤 플레이 솔루션을 쉽게 만들 수 있습니다.

장치 쌍에서 쓰기 가능한 속성의 상태는 원하는 및 보고 된 섹션에서 분할 됩니다. 모든 읽기 전용 속성은 보고 된 섹션 내에서 사용할 수 있습니다.

디지털 쌍에는 속성의 현재 상태와 원하는 상태를 보여 주는 통합 뷰가 있습니다. 지정 된 속성의 동기화 상태는 해당 하는 기본 구성 요소 섹션에 저장 됩니다 `$metadata` .

### <a name="digital-twin-json-format"></a>디지털 쌍 JSON 형식

JSON 개체로 표시 되는 디지털 쌍에는 다음 필드가 포함 됩니다.

| 필드 이름 | Description |
| --- | --- |
| `$dtId` | 장치 디지털 쌍의 ID를 나타내는 사용자 제공 문자열입니다. |
| `{propertyName}` | JSON의 속성 값 |
| `$metadata.$model` | 필드 이 디지털 쌍의 특징을 나타내는 모델 인터페이스의 ID입니다. |
| `$metadata.{propertyName}.desiredValue` | [쓰기 가능한 속성에만 해당] 지정 된 속성의 원하는 값입니다. |
| `$metadata.{propertyName}.desiredVersion` | [쓰기 가능한 속성에만 해당] IoT Hub에서 유지 관리 되는 원하는 값의 버전입니다.|
| `$metadata.{propertyName}.ackVersion` | [필수, 쓰기 가능한 속성에만 해당] 디지털 쌍을 구현 하는 장치에서 승인 하는 버전은 원하는 버전과 같거나 커야 합니다. |
| `$metadata.{propertyName}.ackCode` | [필수, 쓰기 가능한 속성에만 해당] `ack` 디지털 쌍을 구현 하는 장치 앱에서 반환 된 코드 |
| `$metadata.{propertyName}.ackDescription` | [선택 사항, 쓰기 가능한 속성만 해당] `ack` 디지털 쌍을 구현 하는 장치 앱에서 반환 하는 설명입니다. |
| `$metadata.{propertyName}.lastUpdateTime` | IoT Hub은 장치의 마지막 속성 업데이트에 대 한 타임 스탬프를 유지 관리 합니다. 타임 스탬프는 UTC로 ISO8601 형식으로 인코딩됩니다. YYYY-MM-DDTHH: MM: SS. mmmZ |
| `{componentName}` | 구성 요소의 속성 값과 메타 데이터를 포함 하는 JSON 개체입니다. |
| `{componentName}.{propertyName}` | JSON의 구성 요소 속성 값 |
| `{componentName}.$metadata` | 구성 요소에 대 한 메타 데이터 정보입니다. |

#### <a name="device-twin-sample"></a>장치 쌍 샘플

다음 코드 조각에서는 JSON 개체로 형식이 지정 된 IoT 플러그 앤 플레이 장치 쌍을 보여 줍니다.

```json
{
    "deviceId": "sample-device",
    "modelId": "dtmi:com:example:TemperatureController;1",
    "version": 15,
    "properties": {
        "desired": {
            "thermostat1": {
                "__t": "c",
                "targetTemperature": 21.8
            },
            "$metadata": {...},
            "$version": 4
        },
        "reported": {
            "serialNumber": "alwinexlepaho8329",
            "thermostat1": {
                "maxTempSinceLastReboot": 25.3,
                "__t": "c",
                "targetTemperature": {
                    "value": 21.8,
                    "ac": 200,
                    "ad": "Successfully executed patch",
                }
            },
            "$metadata": {...},
            "$version": 11
        }
    }
}
```

#### <a name="digital-twin-sample"></a>디지털 쌍 샘플

다음 코드 조각에서는 JSON 개체로 형식이 지정 된 디지털 쌍을 보여 줍니다.

```json
{
    "$dtId": "sample-device",
    "serialNumber": "alwinexlepaho8329",
    "thermostat1": {
        "maxTempSinceLastReboot": 25.3,
        "targetTemperature": 21.8,
        "$metadata": {
            "targetTemperature": {
                "desiredValue": 21.8,
                "desiredVersion": 4,
                "ackVersion": 4,
                "ackCode": 200,
                "ackDescription": "Successfully executed patch",
                "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
            },
            "maxTempSinceLastReboot": {
                "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
            }
        }
    },
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

### <a name="properties"></a>속성

속성은 엔터티의 상태를 나타내는 데이터 필드입니다 (예: 많은 개체 지향 프로그래밍 언어의 속성).

#### <a name="read-only-property"></a>읽기 전용 속성

스키마:

```json
{
    "@type": "Property",
    "name": "serialNumber",
    "displayName": "Serial Number",
    "description": "Serial number of the device.",
    "schema": "string"
}
```

이 예제에서 `alwinexlepaho8329` 는 장치에서 보고 하는 읽기 전용 속성의 현재 값입니다 `serialNumber` .
다음 코드 조각은 속성의 side-by-side JSON 표현을 보여 줍니다 `serialNumber` .

:::row:::
   :::column span="":::
      **디바이스 쌍**

```json
"properties": {
    "reported": {
        "serialNumber": "alwinexlepaho8329"
    }
}
```

   :::column-end:::
   :::column span="":::
      **디지털 쌍**

```json
"serialNumber": "alwinexlepaho8329"
```

   :::column-end:::
:::row-end:::

#### <a name="writable-property"></a>쓰기 가능 속성

기본 구성 요소에 다음과 같은 쓰기 가능 속성도 있다고 가정해 보겠습니다.

```json
{
    "@type": "Property",
    "name": "fanSpeed",
    "displayName": "Fan Speed",
    "writable": true,
    "schema": "double"
}
```

:::row:::
   :::column span="":::
      **디바이스 쌍**

```json
{
    "properties": {
        "desired": {
            "fanSpeed": 2.0,
        },
        "reported": {
            "fanSpeed": {
                "value": 3.0,
                "ac": 200,
                "av": 1,
                "ad": "Successfully executed patch version 1"
            }
        }
    },
}
```

   :::column-end:::
   :::column span="":::
      **디지털 쌍**

```json
{
    "fanSpeed": 3.0,
    "$metadata": {
        "fanSpeed": {
            "desiredValue": 2.0,
            "desiredVersion": 2,
            "ackVersion": 1,
            "ackCode": 200,
            "ackDescription": "Successfully executed patch version 1",
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

   :::column-end:::
:::row-end:::

이 예제에서 `3.0` 는 장치에서 보고 하는 속성의 현재 값입니다 `fanSpeed` . `2.0` 는 솔루션에 의해 설정 되는 원하는 값입니다. 루트 수준 속성의 원하는 값 및 동기화 상태는 디지털 쌍의 루트 수준 내에서 설정 됩니다 `$metadata` . 장치가 온라인 상태가 되 면이 업데이트를 적용 하 고 업데이트 된 값을 다시 보고할 수 있습니다.

### <a name="components"></a>구성 요소

구성 요소를 사용 하 여 모델 인터페이스를 다른 인터페이스의 어셈블리로 빌드할 수 있습니다.
모델으로 정의 된 [자동 온도 조절기](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) 인터페이스를 고려 합니다.
이 인터페이스는 이제 [온도 컨트롤러 모델](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)을 정의할 때 구성 요소 thermostat1 (및 다른 구성 요소 thermostat2)로 통합 될 수 있습니다.

장치 쌍에서 구성 요소는 표식으로 식별 됩니다 `{ "__t": "c"}` . 디지털 쌍에서가 있으면 `$metadata` 구성 요소가 표시 됩니다.

이 예제에서 `thermostat1` 은 두 개의 속성을 포함 하는 구성 요소입니다.

- `maxTempSinceLastReboot` 읽기 전용 속성이입니다.
- `targetTemperature` 는 장치에서 성공적으로 동기화 된 쓰기 가능한 속성입니다. 이러한 속성의 원하는 값과 동기화 상태는 구성 요소에 `$metadata` 있습니다.

다음 코드 조각은 구성 요소의 side-by-side JSON 표현을 보여 줍니다 `thermostat1` .

:::row:::
   :::column span="":::
      **디바이스 쌍**

```json
"properties": {
    "desired": {
        "thermostat1": {
            "__t": "c",
            "targetTemperature": 21.8
        },
        "$metadata": {
        },
        "$version": 4
    },
    "reported": {
        "thermostat1": {
            "maxTempSinceLastReboot": 25.3,
            "__t": "c",
            "targetTemperature": {
                "value": 21.8,
                "ac": 200,
                "ad": "Successfully executed patch",
                "av": 4
            }
        },
        "$metadata": {
        },
        "$version": 11
    }
}
```

   :::column-end:::
   :::column span="":::
      **디지털 쌍**

```json
"thermostat1": {
    "maxTempSinceLastReboot": 25.3,
    "targetTemperature": 21.8,
    "$metadata": {
        "targetTemperature": {
            "desiredValue": 21.8,
            "desiredVersion": 4,
            "ackVersion": 4,
            "ackCode": 200,
            "ackDescription": "Successfully executed patch",
            "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
        },
        "maxTempSinceLastReboot": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

   :::column-end:::
:::row-end:::

## <a name="digital-twin-apis"></a>디지털 쌍 Api

Azure Digital Twins는 디지털 쌍 **가져오기**, 디지털 쌍 **업데이트**, **구성 요소 호출 명령** 및 **invoke 명령을** 사용 하 여 장치 디지털 쌍을 관리 합니다. [REST api](/rest/api/iothub/service/digitaltwin) 를 직접 사용 하거나 [서비스 SDK](../iot-pnp/libraries-sdks.md)를 통해 사용할 수 있습니다.

## <a name="digital-twin-change-events"></a>디지털 쌍 변경 이벤트

디지털 트윈 변경 이벤트를 사용하도록 설정하면 현재 또는 원하는 구성 요소나 속성 값이 변경될 때마다 이벤트가 트리거됩니다. 디지털 쌍 변경 이벤트는 [JSON 패치](http://jsonpatch.com/) 형식으로 생성 됩니다. 쌍 변경 이벤트를 사용 하도록 설정 하면 장치 쌍 형식으로 해당 이벤트가 생성 됩니다.

장치 및 디지털 쌍 이벤트에 대해 라우팅을 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [IoT Hub 메시지 라우팅을 사용 하 여 여러 끝점으로 장치-클라우드 메시지 보내기를](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)참조 하세요. 메시지 형식을 이해 하려면 [IoT Hub 메시지 만들기 및 읽기](../iot-hub/iot-hub-devguide-messages-construct.md)를 참조 하세요.

예를 들어 다음 디지털 쌍 변경 이벤트는 `targetTemperature` 이 솔루션에 의해 설정 될 때 트리거됩니다.

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/17/2020 6:11:04 AM
iothub-message-source:digitalTwinChangeEvents
correlation-id:275d463fa034
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature",
    "value": {
      "desiredValue": 21.8,
      "desiredVersion": 4
    }
  }
]
```

다음 디지털 쌍 변경 이벤트는 장치에서 위의 원하는 변경 내용이 적용 되었음을 보고할 때 트리거됩니다.

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/17/2020 6:11:05 AM
iothub-message-source:digitalTwinChangeEvents
correlation-id:275d464a2c80
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackCode",
    "value": 200
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackDescription",
    "value": "Successfully executed patch"
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackVersion",
    "value": 4
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/lastUpdateTime",
    "value": "2020-07-17T06:11:04.9309159Z"
  },
  {
    "op": "add",
    "path": "/thermostat1/targetTemperature",
    "value": 21.8
  }
]
```

> [!NOTE]
> 장치와 디지털 쌍 변경 알림에서 모두 설정 되 면 쌍 변경 알림 메시지가 두 배가 됩니다.

## <a name="next-steps"></a>다음 단계

이제 디지털 쌍에 대해 알아보았습니다. 몇 가지 추가 리소스는 다음과 같습니다.

- [IoT 플러그 앤 플레이 디지털 쌍 Api를 사용 하는 방법](howto-manage-digital-twin.md)
- [솔루션에서 디바이스와 상호 작용](quickstart-service.md)
- [IoT 디지털 쌍 REST API](/rest/api/iothub/service/digitaltwin)
- [Azure IoT 탐색기](howto-use-iot-explorer.md)