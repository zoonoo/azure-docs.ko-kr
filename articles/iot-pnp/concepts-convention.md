---
title: IoT 플러그 앤 플레이 규칙 | Microsoft Docs
description: IoT 플러그 앤 플레이에서 원격 분석 및 속성을 보낼 때 사용할 장치를 필요로 하는 규칙에 대 한 설명 및 명령과 속성 업데이트를 처리 합니다.
author: rido-min
ms.author: rmpablos
ms.date: 07/10/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 8b5492a737b733f486455507a8a813b5d583d453
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91573840"
---
# <a name="iot-plug-and-play-conventions"></a>IoT 플러그 앤 플레이 규칙

IoT 플러그 앤 플레이 장치는 IoT hub와 메시지를 교환할 때 규칙 집합을 따라야 합니다. IoT 플러그 앤 플레이 장치는 MQTT 프로토콜을 사용 하 여 IoT Hub와 통신 합니다.

장치는 [모듈](../iot-hub/iot-hub-devguide-module-twins.md)을 포함 하거나 IoT Edge 런타임에 호스트 되는 [IoT Edge 모듈](../iot-edge/about-iot-edge.md) 에서 구현 될 수 있습니다.

IoT 플러그 앤 플레이 장치에서 [DTDL (디지털 Twins Language v2)](https://github.com/Azure/opendigitaltwins-dtdl) _모델_을 사용 하 여 구현 하는 원격 분석, 속성 및 명령을 설명 합니다. 이 문서에서 참조 하는 모델에는 두 가지 유형이 있습니다.

- **구성 요소가** 없습니다. 구성 요소가 없는 모델입니다. 모델은 main 인터페이스의 내용 섹션에서 원격 분석, 속성 및 명령을 최상위 속성으로 선언 합니다. Azure IoT 탐색기 도구에서이 모델은 단일 _기본 구성 요소로_표시 됩니다.
- **여러 구성 요소** -두 개 이상의 인터페이스로 구성 된 모델입니다. 원격 분석, 속성 및 명령을 사용 하 여 _기본 구성 요소로_표시 되는 주 인터페이스입니다. 추가 원격 분석, 속성 및 명령을 사용 하 여 구성 요소로 선언 된 하나 이상의 인터페이스입니다.

자세한 내용은 [모델의 IoT 플러그 앤 플레이 구성 요소](concepts-components.md)를 참조 하세요.

## <a name="identify-the-model"></a>모델 식별

구현 하는 모델을 알리기 위해 IoT 플러그 앤 플레이 장치 또는 모듈에는 필드에를 추가 하 여 MQTT 연결 패킷에 모델 ID가 포함 됩니다 `model-id` `USERNAME` .

장치 또는 모듈에서 구현 하는 모델을 식별 하기 위해 서비스는 다음에서 모델 ID를 가져올 수 있습니다.

- 장치 쌍 `modelId` 필드입니다.
- 디지털 쌍 `$metadata.$model` 필드입니다.
- 디지털 쌍 변경 알림입니다.

## <a name="telemetry"></a>원격 분석

구성 요소 장치에서 전송 된 원격 분석에는 추가 메타 데이터가 필요 하지 않습니다. 시스템이 속성을 추가 합니다 `dt-dataschema` .

여러 구성 요소 장치에서 보낸 원격 분석은 `$.sub` 메시지 속성으로 추가 해야 합니다. 시스템에서 및 속성을 추가 합니다 `dt-subject` `dt-dataschema` .

## <a name="read-only-properties"></a>읽기 전용 속성

### <a name="sample-no-component-read-only-property"></a>샘플 구성 요소 읽기 전용 속성 없음

장치 또는 모듈은 DTDL v2 규칙을 따르는 유효한 JSON을 보낼 수 있습니다.

DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:example: Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "temperature",
      "schema": "double"
    }
  ]
}
```

샘플 보고 된 속성 페이로드:

```json
"reported" :
{
  "temperature" : 21.3
}
```

### <a name="sample-multiple-components-read-only-property"></a>샘플 여러 구성 요소 읽기 전용 속성

요소가 구성 요소를 참조 함을 나타내려면 장치 또는 모듈에서 표식을 추가 해야 합니다 `{"__t": "c"}` .

DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "contents": [
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1"
    }
  ]
}

{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "temperature",
      "schema": "double"
    }
  ]
}
```

샘플 보고 된 속성 페이로드:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "temperature": 21.3
  }
}
```

## <a name="writable-properties"></a>쓰기 가능 속성

장치 또는 모듈에서 보고 된 속성을 보내 속성을 받았는지 확인 해야 합니다. 보고 된 속성에는 다음이 포함 되어야 합니다.

- `value` -속성의 실제 값 (일반적으로 받은 값 이지만 장치는 다른 값을 보고 하도록 결정할 수 있음).
- `ac` -HTTP 상태 코드를 사용 하는 승인 코드입니다.
- `av` -desired 속성의를 참조 하는 승인 버전입니다 `$version` . 원하는 속성 JSON 페이로드에서이 값을 찾을 수 있습니다.
- `ad` -선택적 승인 설명입니다.

장치가 시작 되 면 장치 쌍을 요청 하 고 쓰기 가능한 속성 업데이트를 확인 해야 합니다. 장치가 오프 라인 상태인 동안 쓰기 가능한 속성의 버전이 증가 하는 경우 장치가 업데이트를 받았는지 확인 하기 위해 보고 된 속성 응답을 보내야 합니다.

장치를 처음 시작 하는 경우 허브에서 초기 desired 속성을 받지 못하는 경우 보고 된 속성에 대 한 초기 값을 보낼 수 있습니다. 이 경우 장치는를로 설정 해야 `av` 합니다 `1` . 예를 들면 다음과 같습니다.

```json
"reported": {
  "targetTemperature": {
    "value": 20.0,
    "ac": 200,
    "av": 1,
    "ad": "initialize"
  }
}
```

장치는 보고 된 속성을 사용 하 여 허브에 다른 정보를 제공할 수 있습니다. 예를 들어 장치는 다음과 같은 일련의 진행 중인 메시지를 사용 하 여 응답할 수 있습니다.

```json
"reported": {
  "targetTemperature": {
    "value": 35.0,
    "ac": 202,
    "av": 3,
    "ad": "In-progress - reporting current temperature"
  }
}
```

장치가 대상 온도에 도달 하면 다음 메시지를 보냅니다.

```json
"reported": {
  "targetTemperature": {
    "value": 20.0,
    "ac": 200,
    "av": 3,
    "ad": "Reached target temperature"
  }
}
```

장치에서 다음과 같은 오류를 보고할 수 있습니다.

```json
"reported": {
  "targetTemperature": {
    "value": 120.0,
    "ac": 500,
    "av": 3,
    "ad": "Target temperature out of range. Valid range is 10 to 99."
  }
}
```

### <a name="sample-no-component-writable-property"></a>샘플 구성 요소 쓰기 가능 속성 없음

장치가 단일 페이로드에 보고 된 여러 속성을 수신 하는 경우 보고 된 속성 응답을 여러 페이로드를 통해 보낼 수 있습니다.

장치 또는 모듈은 DTDL v2 규칙을 따르는 유효한 JSON을 보낼 수 있습니다.

DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:example: Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "targetTemperature",
      "schema": "double",
      "writable": true
    }
  ]
}
```

샘플 desired 속성 페이로드:

```json
"desired" :
{
  "targetTemperature" : 21.3,
  "targetHumidity" : 80
},
"$version" : 3
```

샘플 보고 된 속성 첫 번째 페이로드:

```json
"reported": {
  "targetTemperature": {
    "value": 21.3,
    "ac": 200,
    "av": 3,
    "ad": "complete"
  }
}
```

샘플 보고 된 속성 두 번째 페이로드:

```json
"reported": {
  "targetHumidity": {
    "value": 80,
    "ac": 200,
    "av": 3,
    "ad": "complete"
  }
}
```

### <a name="sample-multiple-components-writable-property"></a>예제 여러 구성 요소 쓰기 가능 속성

요소가 구성 요소를 참조 함을 나타내려면 장치 또는 모듈에서 표식을 추가 해야 합니다 `{"__t": "c"}` .

마커는 구성 요소에 정의 된 속성에 대 한 업데이트에 대해서만 전송 됩니다. 기본 구성 요소에 정의 된 속성에 대 한 업데이트에는 마커가 포함 되지 않습니다. [샘플 구성 요소 쓰기 가능 속성 속성](#sample-no-component-writable-property) 을 참조 하세요.

장치가 단일 페이로드에 보고 된 여러 속성을 수신 하는 경우 보고 된 속성 응답을 여러 페이로드를 통해 보낼 수 있습니다.

장치 또는 모듈은 보고 된 속성을 보내 속성을 받았는지 확인 해야 합니다.

DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "contents": [
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1"
    }
  ]
}

{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "targetTemperature",
      "schema": "double",
      "writable": true
    }
  ]
}
```

샘플 desired 속성 페이로드:

```json
"desired": {
  "thermostat1": {
    "__t": "c",
    "targetTemperature": 21.3,
    "targetHumidity": 80
  }
},
"$version" : 3
```

샘플 보고 된 속성 첫 번째 페이로드:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "targetTemperature": {
      "value": 23,
      "ac": 200,
      "av": 3,
      "ad": "complete"
    }
  }
}
```

샘플 보고 된 속성 두 번째 페이로드:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "targetHumidity": {
      "value": 80,
      "ac": 200,
      "av": 3,
      "ad": "complete"
    }
  }
}
```

## <a name="commands"></a>명령

접두사 없이 명령 이름을 사용 하는 구성 요소 인터페이스가 없습니다.

장치 또는 모듈에서 여러 구성 요소 인터페이스는 다음과 같은 형식으로 명령 이름을 사용 `componentName*commandName` 합니다.

## <a name="next-steps"></a>다음 단계

이제 IoT 플러그 앤 플레이 규칙에 대해 알아보았습니다. 몇 가지 추가 리소스는 다음과 같습니다.

- [DTDL(디지털 쌍 정의 언어)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C 디바이스 SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
- [모델 구성 요소](./concepts-components.md)
