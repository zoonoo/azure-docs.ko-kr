---
title: IoT 플러그 앤 플레이 디지털 트윈 관리 방법
description: 디지털 트윈 API를 사용한 IoT 플러그 앤 플레이 디바이스 관리 방법
author: prashmo
ms.author: prashmo
ms.date: 12/17/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: e68003878dc0e9275461100a59e0f45486c2978f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739875"
---
# <a name="manage-iot-plug-and-play-digital-twins"></a>IoT 플러그 앤 플레이 디지털 트윈 관리하기

디지털 트윈을 관리하기 위해 IoT 플러그 앤 플레이는 **디지털 트윈 가져오기** 및 **디지털 트윈 업데이트하기** 를 지원합니다. [REST API](/rest/api/iothub/service/digitaltwin)나 [서비스 SDK](libraries-sdks.md) 중 하나를 사용할 수 있습니다.

문서 작성 당시의 디지털 트윈 API 버전은 `2020-09-30`입니다.

## <a name="update-a-digital-twin"></a>디지털 쌍 업데이트

IoT 플러그 앤 플레이 디바이스는 [DTDL(Digital Twins 정의 언어) v2](https://github.com/Azure/opendigitaltwins-dtdl)에서 설명한 모델을 구현합니다. 솔루션 개발자는 **디지털 트윈 API 업데이트** 를 통해 디지털 트윈의 구성 요소 상태와 속성을 업데이트할 수 있습니다.

본 문서에서 예로 사용된 IoT 플러그 앤 플레이 디바이스는 [온도 조절기](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) 구성 요소를 사용해 [온도 컨트롤러 모델](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)을 구현합니다.

다음 코드 조각은 JSON 개체로 형식이 지정된 **디지털 트윈 가져오기** 요청에 대한 응답을 보여 줍니다. 디지털 트윈 형식에 대한 자세한 내용은 [IoT 플러그 앤 플레이 디지털 트윈 이해하기](./concepts-digital-twin.md#digital-twin-example)를 참조하세요.

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

디지털 트윈을 이용하면 [JSON 패치](http://jsonpatch.com/)를 통해 전체 구성 요소나 속성을 업데이트할 수 있습니다.

예를 들어, `targetTemperature` 속성을 다음과 같이 업데이트할 수 있습니다.

```json
[
    {
        "op": "add",
        "path": "/thermostat1/targetTemperature",
        "value": 21.4
    }
]
```

이전 업데이트는 해당 구성 요소 수준인 `$metadata`의 속성에 대해 원하는 값을 다음 코드 조각처럼 설정합니다. IoT Hub는 원하는 속성 버전을 다음과 같이 업데이트합니다.

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

### <a name="add-replace-or-remove-a-component"></a>구성 요소 추가, 교체 또는 제거하기

구성 요소 수준 작업에는 동일 값 내에 빈 개체 `$metadata` 마커가 필요합니다.

구성 요소 추가나 교체 작업은 제공된 모든 속성에 대해 원하는 값을 설정합니다. 또한 쓰기 가능하지만 업데이트를 통해 제공되지 않는 속성에 대해 원하는 값은 삭제합니다.

구성 요소 제거는 현재 쓰기 가능한 모든 속성의 원하는 값을 삭제합니다. 결국, 디바이스는 이러한 제거 작업에 동기화하여 개별 속성에 대한 보고를 중지합니다. 그런 다음에는 디지털 트윈에서 해당 구성 요소가 제거됩니다.

다음의 JSON 패치 샘플에서는 구성 요소를 추가하거나 교체 또는 제거하는 방법을 보여 줍니다.

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

### <a name="add-replace-or-remove-a-property"></a>속성을 추가, 교체 또는 제거하기

추가 작업이나 교체 작업은 속성에 대해 원하는 값을 설정합니다. 디바이스는 상태를 동기화하고 `ack` 코드, 버전, 설명과 값 업데이트를 함께 보고할 수 있습니다.

속성을 제거하면 속성에 대해 원하는 값이 설정된 경우 이 값이 지워집니다. 그렇게 되면 디바이스가 해당 속성을 보고하는 것을 중지할 수 있어 구성 요소에서 해당 속성이 제거됩니다. 해당 속성이 구성 요소의 마지막 속성이라면 해당 구성 요소까지 제거됩니다.

다음의 JSON 패치 샘플은 동일한 구성 요소 내에서 속성을 추가, 교체 또는 제거하는 방법을 보여 줍니다.

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

### <a name="rules-for-setting-the-desired-value-of-a-digital-twin-property"></a>디지털 트윈 속성에 원하는 값을 설정하는 규칙

**이름**

구성 요소나 속성의 이름은 반드시 유효한 DTDL v2 이름이어야 합니다.

소문자 및 대문자 a에서 z까지와 숫자 0~9(첫 번째 자리는 제외) 및 밑줄(첫 번째와 마지막 자리는 제외)을 사용할 수 있습니다.

1~64글자로 된 이름을 사용할 수 있습니다.

**속성 값**

해당 값은 유효한 [DTDL v2 속성](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#property)이어야 합니다.

기본 형식을 모두 지원합니다. 복합 형식, 열거형, 맵, 개체를 지원합니다. 자세한 내용은 [DTDL v2 스키마](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#schemas)를 참조하세요.

속성은 배열이나 배열이 있는 복합 스키마를 지원하지 않습니다.

복합 개체는 최대 5단계까지 지원합니다.

복합 개체 내의 필드 이름은 모두 유효한 DTDL v2 이름이어야 합니다.

지도 키는 모두 유효한 DTDL v2 이름이어야 합니다.

## <a name="troubleshoot-update-digital-twin-api-errors"></a>디지털 트윈 API 업데이트 오류 문제 해결하기

디지털 트윈 API는 다음과 같은 일반 오류 메시지를 표시합니다.

`ErrorCode:ArgumentInvalid;'{propertyName}' exists within the device twin and is not digital twin conformant property. Please refer to aka.ms/dtpatch to update this to be conformant.`

이와 같은 오류가 표시되면 업데이트 패치가 [디지털 트윈 속성의 원하는 값 설정 규칙](#rules-for-setting-the-desired-value-of-a-digital-twin-property)을 따르는지 확인합니다.

구성 요소를 업데이트하는 경우, [빈 개체 $metadata 마커](#add-replace-or-remove-a-component)가 설정되어 있는지 확인합니다.

디바이스의 보고된 값이 [IoT 플러그 앤 플레이 규칙](./concepts-convention.md#writable-properties)을 따르지 않는 경우, 업데이트가 실패할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 디지털 트윈에 대해 알아보았으므로 다음과 같은 추가 리소스가 있습니다.

- [솔루션에서 디바이스와 상호 작용](quickstart-service.md)
- [IoT 디지털 트윈 REST API](/rest/api/iothub/service/digitaltwin)
- [Azure IoT 탐색기](howto-use-iot-explorer.md)
