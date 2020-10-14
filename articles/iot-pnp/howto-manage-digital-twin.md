---
title: IoT 플러그 앤 플레이 디지털 쌍을 관리 하는 방법
description: 디지털 쌍 Api를 사용 하 여 IoT 플러그 앤 플레이 장치를 관리 하는 방법
author: prashmo
ms.author: prashmo
ms.date: 07/20/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5f1c52b764634f8086763aca67dfc32b507d2edd
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92042850"
---
# <a name="manage-iot-plug-and-play-digital-twins"></a>IoT 플러그 앤 플레이 디지털 쌍 관리

IoT 플러그 앤 플레이는 디지털 쌍 **가져오기** 및 디지털 쌍 **업데이트** 작업을 지원 하 여 디지털 쌍을 관리 합니다. [REST api](/rest/api/iothub/service/digitaltwin) 또는 [서비스 sdk](libraries-sdks.md)중 하나를 사용할 수 있습니다.

문서를 작성할 당시 디지털 쌍 API 버전은 `2020-09-30` 입니다.

## <a name="update-a-digital-twin"></a>디지털 쌍 업데이트

IoT 플러그 앤 플레이 장치는 [DTDL (디지털 Twins 정의 언어 v2)](https://github.com/Azure/opendigitaltwins-dtdl)로 설명 된 모델을 구현 합니다. 솔루션 개발자는 디지털 쌍 **업데이트 API** 를 사용 하 여 구성 요소의 상태와 디지털 쌍의 속성을 업데이트할 수 있습니다.

이 문서에서 예로 사용 된 IoT 플러그 앤 플레이 장치는 [자동 온도 조절기](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) 구성 요소를 사용 하 여 [온도 컨트롤러 모델](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) 을 구현 합니다.

다음 코드 조각에서는 JSON 개체로 형식이 지정 된 **Get digital** 쌍 요청에 대 한 응답을 보여 줍니다. 디지털 쌍 형식에 대해 자세히 알아보려면 [IoT 플러그 앤 플레이 디지털 쌍 이해](./concepts-digital-twin.md#digital-twin-json-format)를 참조 하세요.

```json
{
    "$dtId": "sample-device",
    "serialNumber": "alwinexlepaho8329",
    "thermostat1": {
        "maxTempSinceLastReboot": 25.3,
        "targetTemperature": 20.4,
        "$metadata": {
            "targetTemperature": {
                "desiredValue": 20.4,
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

Digital 쌍를 사용 하면 [JSON 패치](http://jsonpatch.com/)를 사용 하 여 전체 구성 요소나 속성을 업데이트할 수 있습니다.

예를 들어 다음과 같이 속성을 업데이트할 수 있습니다 `targetTemperature` .

```json
[
    {
        "op": "add",
        "path": "/thermostat1/targetTemperature",
        "value": 21.4
    }
]
```

이전 업데이트는 다음 코드 조각과 같이 해당 구성 요소 수준에서 원하는 속성 값을 설정 합니다 `$metadata` . IoT Hub는 속성의 원하는 버전을 업데이트 합니다.

```json
"thermostat1": {
    "targetTemperature": 20.4,
    "$metadata": {
        "targetTemperature": {
            "desiredValue": 21.4,
            "desiredVersion": 5,
            "ackVersion": 4,
            "ackCode": 200,
            "ackDescription": "Successfully executed patch",
            "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
        }
    }
}
```

### <a name="add-replace-or-remove-a-component"></a>구성 요소 추가, 바꾸기 또는 제거

구성 요소 수준 작업을 수행 하려면 값에 빈 개체 `$metadata` 표식이 필요 합니다.

구성 요소 추가 또는 바꾸기 작업은 제공 된 모든 속성의 원하는 값을 설정 합니다. 또한 업데이트와 함께 제공 되지 않은 쓰기 가능한 속성에 대해 원하는 값을 지웁니다.

구성 요소를 제거 하면 제공 되는 모든 쓰기 가능한 속성 값이 지워집니다. 장치는 궁극적으로이 제거를 동기화 하 고 개별 속성에 대 한 보고를 중지 합니다. 그런 다음 디지털 쌍에서 구성 요소가 제거 됩니다.

다음 JSON Patch 샘플에서는 구성 요소를 추가, 교체 또는 제거 하는 방법을 보여 줍니다.

```json
[
    {
        "op": "add",
        "path": "/thermostat1",
        "value": {
            "targetTemperature": 21.4,
            "anotherWritableProperty": 42,
            "$metadata": {}
        }
    },
    {
        "op": "replace",
        "path": "/thermostat1",
        "value": {
            "targetTemperature": 21.4,
            "$metadata": {}
        }
    },
    {
        "op": "remove",
        "path": "/thermostat2"
    }
]
```

### <a name="add-replace-or-remove-a-property"></a>속성 추가, 바꾸기 또는 제거

추가 또는 바꾸기 작업은 속성의 원하는 값을 설정 합니다. 장치는 `ack` 코드, 버전 및 설명과 함께 상태를 동기화 하 고 값 업데이트를 보고할 수 있습니다.

속성을 제거 하면 속성 값이 설정 된 경우 해당 값이 지워집니다. 그러면 장치는이 속성에 대 한 보고를 중지할 수 있으며 구성 요소에서 제거 됩니다. 이 속성이 구성 요소의 마지막 속성 인 경우에도 구성 요소가 제거 됩니다.

다음 JSON Patch 샘플에서는 구성 요소 내에서 속성을 추가, 대체 또는 제거 하는 방법을 보여 줍니다.

```json
[
    {
        "op": "add",
        "path": "/thermostat1/targetTemperature",
        "value": 21.4
    },
    {
        "op": "replace",
        "path": "/thermostat1/anotherWritableProperty",
        "value": 42
    },
    {
        "op": "remove",
        "path": "/thermostat2/targetTemperature",
    }
]
```

### <a name="rules-for-setting-the-desired-value-of-a-digital-twin-property"></a>Digital 쌍 속성의 원하는 값을 설정 하기 위한 규칙

**이름**

구성 요소 또는 속성의 이름은 유효한 DTDL v2 이름 이어야 합니다.

허용 되는 문자는 a-z, a-z, 0-9 (첫 번째 문자가 아님) 및 밑줄 (첫 번째 또는 마지막 문자가 아님)입니다.

이름은 1-64 자까지 가능 합니다.

**속성 값**

값은 유효한 [Dtdl V2 속성](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#property)이어야 합니다.

모든 기본 형식이 지원 됩니다. 복합 형식, 열거형, 맵 및 개체가 지원 됩니다. 자세히 알아보려면 [Dtdl V2 스키마](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#schemas)를 참조 하세요.

속성은 배열이 있는 배열 또는 복합 스키마를 지원 하지 않습니다.

복합 개체에 대해 5 수준의 최대 깊이가 지원 됩니다.

복합 개체 내의 모든 필드 이름은 유효한 DTDL v2 이름 이어야 합니다.

모든 지도 키는 유효한 DTDL v2 이름 이어야 합니다.

## <a name="troubleshoot-update-digital-twin-api-errors"></a>디지털 쌍 업데이트 API 오류 문제 해결

디지털 쌍 API는 다음과 같은 일반 오류 메시지를 throw 합니다.

`ErrorCode:ArgumentInvalid;'{propertyName}' exists within the device twin and is not digital twin conformant property. Please refer to aka.ms/dtpatch to update this to be conformant.`

이 오류가 표시 되 면 업데이트 패치가 [디지털 쌍 속성의 원하는 값을 설정](#rules-for-setting-the-desired-value-of-a-digital-twin-property) 하는 규칙을 따르는지 확인 합니다.

구성 요소를 업데이트할 때 [빈 개체 $metadata 표식이](#add-replace-or-remove-a-component) 설정 되어 있는지 확인 합니다.

장치의 보고 된 값이 [IoT 플러그 및 재생 규칙](./concepts-convention.md#writable-properties)을 따르지 않는 경우 업데이트가 실패할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 디지털 쌍에 대해 알아보았습니다. 몇 가지 추가 리소스는 다음과 같습니다.

- [솔루션에서 디바이스와 상호 작용](quickstart-service-node.md)
- [IoT 디지털 쌍 REST API](/rest/api/iothub/service/digitaltwin)
- [Azure IoT 탐색기](howto-use-iot-explorer.md)