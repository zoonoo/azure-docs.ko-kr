---
title: IoT 플러그 앤 플레이 장치 모델 이해 | Microsoft Docs
description: IoT 플러그 앤 플레이 장치에 대 한 DTDL (디지털 Twins 정의 언어) 모델링 언어를 이해 합니다. 이 문서에서는 기본 및 복잡 한 데이터 형식, 구성 요소 및 상속을 사용 하는 패턴 및 의미 체계 형식을 설명 합니다. 이 문서에서는 모델 제작을 위한 장치 쌍 모델 식별자 및 도구 지원을 선택 하는 방법에 대 한 지침을 제공 합니다.
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2021
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: a3389408b0942875aa7d760f1c514b995e381f9c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609478"
---
# <a name="iot-plug-and-play-modeling-guide"></a>IoT 플러그 앤 플레이 모델링 가이드

IoT 플러그 앤 플레이의 핵심은 IoT 플러그 앤 플레이 사용 응용 프로그램에 대 한 장치의 기능을 설명 하는 장치 _모델_ 입니다. 이 모델은 다음을 정의 하는 인터페이스 집합으로 구성 됩니다.

- 속성 - 디바이스 또는 다른 엔터티의 읽기 전용 또는 쓰기 가능 상태를 나타냅니다. 예를 들어 디바이스 일련 번호는 읽기 전용 속성일 수 있으며, 자동 온도 조절기의 목표 온도는 쓰기 가능 속성일 수 있습니다.
- 장치가 내보낸 데이터를 정의 하는 _원격 분석_ 필드, 데이터가 센서 판독값의 일반 스트림 인지, 가끔 오류 또는 정보 메시지 인지 여부입니다.
- 디바이스에서 수행할 수 있는 함수 또는 작업을 설명하는 _명령_. 예를 들어 명령으로 게이트웨이를 다시 부팅하거나 원격 카메라를 사용하여 사진을 찍을 수 있습니다.

IoT 플러그 앤 플레이 장치 모델을 사용 하는 방법에 대해 자세히 알아보려면 [iot 플러그 앤 플레이 장치 개발자 가이드](concepts-developer-guide-device.md) 및 [iot 플러그 앤 플레이 서비스 개발자 가이드](concepts-developer-guide-service.md)를 참조 하세요.

모델을 정의 하려면 DTDL (디지털 Twins 정의 언어)를 사용 합니다. DTDL은 [json-LD](https://json-ld.org/)이라는 json variant를 사용 합니다. 다음 코드 조각에서는 자동 온도 조절기 장치에 대 한 모델을 보여 줍니다.

- 에는 고유한 모델 ID가 `dtmi:com:example:Thermostat;1` 있습니다.
- 온도 원격 분석을 보냅니다.
- 대상 온도를 설정 하는 쓰기 가능한 속성이 있습니다.
- 에는 마지막 다시 부팅 이후의 최대 온도를 보고 하는 읽기 전용 속성이 있습니다.
- 일정 기간 동안 최대, 최소 및 평균 온도를 요청 하는 명령에 응답 합니다.

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "targetTemperature",
      "schema": "double",
      "displayName": "Target Temperature",
      "description": "Allows to remotely specify the desired target temperature.",
      "unit": "degreeCelsius",
      "writable": true
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "maxTempSinceLastReboot",
      "schema": "double",
      "unit": "degreeCelsius",
      "displayName": "Max temperature since last reboot.",
      "description": "Returns the max temperature since last device reboot."
    },
    {
      "@type": "Command",
      "name": "getMaxMinReport",
      "displayName": "Get Max-Min report.",
      "description": "This command returns the max, min and average temperature from the specified time to the current time.",
      "request": {
        "name": "since",
        "displayName": "Since",
        "description": "Period to return the max-min report.",
        "schema": "dateTime"
      },
      "response": {
        "name": "tempReport",
        "displayName": "Temperature Report",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "name": "maxTemp",
              "displayName": "Max temperature",
              "schema": "double"
            },
            {
              "name": "minTemp",
              "displayName": "Min temperature",
              "schema": "double"
            },
            {
              "name": "avgTemp",
              "displayName": "Average Temperature",
              "schema": "double"
            },
            {
              "name": "startTime",
              "displayName": "Start Time",
              "schema": "dateTime"
            },
            {
              "name": "endTime",
              "displayName": "End Time",
              "schema": "dateTime"
            }
          ]
        }
      }
    }
  ]
}
```

자동 온도 조절기 모델에는 단일 인터페이스가 있습니다. 이 문서의 뒷부분에 나오는 예제에서는 구성 요소 및 상속을 사용 하는 좀 더 복잡 한 모델을 보여 줍니다.

이 문서에서는 사용자 고유의 모델을 디자인 하 고 작성 하는 방법을 설명 하 고 데이터 형식, 모델 구조 및 도구와 같은 주제에 대해 설명 합니다.

자세한 내용은 [디지털 Twins 정의 언어 v2](https://github.com/Azure/opendigitaltwins-dtdl) 사양을 참조 하세요.

## <a name="model-structure"></a>모델 구조

속성, 원격 분석 및 명령은 인터페이스로 그룹화 됩니다. 이 섹션에서는 구성 요소 및 상속을 사용 하 여 간단한 모델 및 복잡 한 모델을 설명 하는 인터페이스를 사용 하는 방법을 설명 합니다.

### <a name="model-ids"></a>모델 Id

모든 인터페이스에는 고유한 DTMI (디지털 쌍 모델 식별자)가 있습니다. 복합 모델은 DTMIs를 사용 하 여 구성 요소를 식별 합니다. 응용 프로그램은 장치가 전송 하는 DTMIs를 사용 하 여 리포지토리에서 모델 정의를 찾을 수 있습니다.

DTMIs는 [IoT 플러그 앤 플레이 모델 리포지토리에](https://github.com/Azure/iot-plugandplay-models)필요한 명명 규칙을 따라야 합니다.

- DTMI 접두사는 `dtmi:` 입니다.
- DTMI 접미사는와 같은 모델의 버전 번호입니다 `;2` .
- DTMI의 본문은 모델이 저장 된 모델 리포지토리의 폴더와 파일에 매핑됩니다. 버전 번호는 파일 이름의 일부입니다.

예를 들어 DTMI로 식별 된 모델은 `dtmi:com:Example:Thermostat;2` 파일 *의 dtmi/com/example/thermostat-2.js* 에 저장 됩니다.

다음 코드 조각은 고유한 DTMI를 사용 하는 인터페이스 정의에 대 한 개요를 보여 줍니다.

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;2",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    ...
  ]
}
```

### <a name="no-components"></a>구성 요소 없음

이전에 표시 된 자동 온도 조절기 같은 단순 모델은 포함 된 구성 요소 또는 종속 된 구성 요소를 사용 하지 않습니다. 원격 분석, 속성 및 명령은 `contents` 인터페이스의 노드에 정의 되어 있습니다.

다음 예에서는 구성 요소를 사용 하지 않는 간단한 모델의 일부를 보여 줍니다.

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
...
```

Azure IoT 탐색기 및 IoT Central 장치 템플릿 디자이너와 같은 도구는 _기본 구성 요소로_ 자동 온도 조절기 같은 독립 실행형 인터페이스에 레이블을 만듭니다.

다음 스크린샷은 Azure IoT 탐색기 도구에 모델을 표시 하는 방법을 보여 줍니다.

:::image type="content" source="media/concepts-modeling-guide/default-component.png" alt-text="Azure IoT 탐색기의 기본 구성 요소":::

다음 스크린샷은 IoT Central 장치 템플릿 디자이너에서 모델이 기본 구성 요소로 표시 되는 방법을 보여 줍니다. 모델의 DTMI를 보려면 **Id 보기** 를 선택 합니다.

:::image type="content" source="media/concepts-modeling-guide/iot-central-model.png" alt-text="IoT Central designer에서 자동 온도 조절기 모델을 보여 주는 스크린샷":::

모델 ID는 다음 스크린샷에 표시 된 것 처럼 장치 쌍 속성에 저장 됩니다.

:::image type="content" source="media/concepts-modeling-guide/twin-model-id.png" alt-text="디지털 쌍 속성의 모델 ID":::

구성 요소가 없는 DTDL 모델은 단일 원격 분석, 속성 및 명령 집합을 사용 하는 장치 또는 IoT Edge 모듈에 유용 하 게 사용할 수 있습니다. 구성 요소를 사용 하지 않는 모델을 사용 하면 기존 장치 또는 모듈을 IoT 플러그 앤 플레이 장치 또는 모듈로 쉽게 마이그레이션할 수 있습니다. 구성 요소를 정의할 필요 없이 실제 장치 또는 모듈을 설명 하는 DTDL 모델을 만듭니다.

> [!TIP]
> 모듈은 장치 [모듈](../iot-hub/iot-hub-devguide-module-twins.md) 이거나 [IoT Edge 모듈인](../iot-edge/about-iot-edge.md)수 있습니다.

### <a name="reuse"></a>재사용

인터페이스 정의를 다시 사용 하는 방법에는 두 가지가 있습니다. 모델의 여러 구성 요소를 사용 하 여 다른 인터페이스 정의를 참조 합니다. 상속을 사용 하 여 기존 인터페이스 정의를 확장 합니다.

### <a name="multiple-components"></a>여러 구성 요소

구성 요소를 사용 하 여 다른 인터페이스의 어셈블리로 모델 인터페이스를 빌드할 수 있습니다.

예를 들어 [자동 온도 조절기](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) 인터페이스는 모델로 정의 됩니다. [온도 컨트롤러 모델](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)을 정의할 때이 인터페이스를 하나 이상의 구성 요소로 통합할 수 있습니다. 다음 예제에서는 이러한 구성 요소를 및 라고 `thermostat1` `thermostat2` 합니다.

여러 구성 요소를 포함 하는 DTDL 모델에는 두 개 이상의 구성 요소 섹션이 있습니다. 각 섹션은 `@type` 다음 코드 조각과 같이로 설정 되 `Component` 고 명시적으로 스키마를 참조 합니다.

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "description": "Device with two thermostats and remote reboot.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "DataSize"
      ],
      "name": "workingSet",
      "displayName": "Working Set",
      "description": "Current working set of the device memory in KiB.",
      "schema": "double",
      "unit": "kibibyte"
    },
    {
      "@type": "Property",
      "name": "serialNumber",
      "displayName": "Serial Number",
      "description": "Serial number of the device.",
      "schema": "string"
    },
    {
      "@type": "Command",
      "name": "reboot",
      "displayName": "Reboot",
      "description": "Reboots the device after waiting the number of seconds specified.",
      "request": {
        "name": "delay",
        "displayName": "Delay",
        "description": "Number of seconds to wait before rebooting the device.",
        "schema": "integer"
      }
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1",
      "displayName": "Thermostat One",
      "description": "Thermostat One of Two."
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat2",
      "displayName": "Thermostat Two",
      "description": "Thermostat Two of Two."
    },
    {
      "@type": "Component",
      "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
  ]
}
```

이 모델에는 내용 섹션 (두 구성 요소 및 구성 요소)에 정의 된 세 가지 구성 요소가 있습니다 `Thermostat` `DeviceInformation` . 내용 섹션에는 속성, 원격 분석 및 명령 정의도 포함 되어 있습니다.

다음 스크린샷은이 모델이 IoT Central 표시 되는 방법을 보여 줍니다. 온도 컨트롤러의 속성, 원격 분석 및 명령 정의는 최상위 수준의 **기본 구성 요소** 에 표시 됩니다. 각 자동 온도 조절기에 대 한 속성, 원격 분석 및 명령 정의는 구성 요소 정의에 표시 됩니다.

:::image type="content" source="media/concepts-modeling-guide/temperature-controller.png" alt-text="IoT Central의 온도 컨트롤러 장치 템플릿을 보여 주는 스크린샷":::

:::image type="content" source="media/concepts-modeling-guide/temperature-controller-components.png" alt-text="IoT Central 온도 컨트롤러 장치 템플릿의 자동 온도 조절기 구성 요소를 보여 주는 스크린샷":::

구성 요소와 상호 작용 하는 장치 코드를 작성 하는 방법을 알아보려면 [IoT 플러그 앤 플레이 장치 개발자 가이드](concepts-developer-guide-device.md)를 참조 하세요.

장치에서 구성 요소를 포함 하는 서비스 코드를 작성 하는 방법을 알아보려면 [IoT 플러그 앤 플레이 서비스 개발자 가이드](concepts-developer-guide-service.md)를 참조 하세요.

### <a name="inheritance"></a>상속

상속을 통해 기본 인터페이스의 기능을 다시 사용 하 여 인터페이스의 기능을 확장할 수 있습니다. 예를 들어 여러 장치 모델은 일련 번호와 같은 일반적인 기능을 공유할 수 있습니다.

:::image type="content" source="media/concepts-modeling-guide/inheritance.png" alt-text="기본 인터페이스의 기능을 공유 하는 자동 온도 조절기 및 Flow 컨트롤러를 보여 주는 장치 모델 상속의 예입니다." border="false":::

다음 코드 조각에서는 키워드를 사용 하 여 `extends` 이전 다이어그램에 표시 된 상속 관계를 정의 하는 DTML 모델을 보여 줍니다.

```json
[
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:Thermostat;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double",
        "unit": "degreeCelsius"
      },
      {
        "@type": "Property",
        "name": "targetTemperature",
        "schema": "double",
        "unit": "degreeCelsius",
        "writable": true
      }
    ],
    "extends": [
      "dtmi:com:example:baseDevice;1"
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:baseDevice;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Property",
        "name": "SerialNumber",
        "schema": "double",
        "writable": false
      }
    ]
  }
]
```

다음 스크린샷은 IoT Central 장치 템플릿 환경에서이 모델을 보여 줍니다.

:::image type="content" source="media/concepts-modeling-guide/iot-central-inheritance.png" alt-text="IoT Central의 인터페이스 상속을 보여 주는 스크린샷":::

장치 또는 서비스 측 코드를 작성 하는 경우 코드는 상속 된 인터페이스를 처리 하기 위해 특별 한 작업을 수행할 필요가 없습니다. 이 섹션에 표시 된 예제에서 장치 코드는 일련 번호를 자동 온도 조절기 인터페이스의 일부인 것으로 보고 합니다.

### <a name="tips"></a>팁

모델을 만들 때 구성 요소와 상속을 결합할 수 있습니다. 다음 다이어그램에서는 인터페이스에서 상속 하는 모델을 보여 줍니다 `thermostat` `baseDevice` . 인터페이스에는 `baseDevice` 다른 인터페이스에서 상속 되는 구성 요소가 있습니다.

:::image type="content" source="media/concepts-modeling-guide/inheritance-components.png" alt-text="구성 요소와 상속을 모두 사용 하는 모델을 보여 주는 다이어그램" border="false":::

다음 코드 조각에서는 및 키워드를 사용 하 여 `extends` `component` 이전 다이어그램에 표시 된 상속 관계와 구성 요소 사용을 정의 하는 dtml 모델을 보여 줍니다.

```json
[
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:Thermostat;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double",
        "unit": "degreeCelsius"
      },
      {
        "@type": "Property",
        "name": "targetTemperature",
        "schema": "double",
        "unit": "degreeCelsius",
        "writable": true
      }
    ],
    "extends": [
      "dtmi:com:example:baseDevice;1"
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:baseDevice;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Property",
        "name": "SerialNumber",
        "schema": "double",
        "writable": false
      },
      {
        "@type" : "Component",
        "schema": "dtmi:com:example:baseComponent;1",
        "name": "baseComponent"
      }
    ]
  }
]
```

## <a name="data-types"></a>데이터 형식

데이터 형식을 사용 하 여 원격 분석, 속성 및 명령 매개 변수를 정의할 수 있습니다. 데이터 형식은 기본 형식이 나 복잡할 수 있습니다. 복합 데이터 형식은 기본 형식 또는 다른 복합 형식을 사용 합니다. 복합 형식의 최대 깊이는 5 수준입니다.

### <a name="primitive-types"></a>기본 형식

다음 표에서는 사용할 수 있는 기본 형식 집합을 보여 줍니다.

| 기본 형식 | 설명 |
| --- | --- |
| `boolean` | 부울 값 |
| `date` | [RFC 3339의 섹션 5.6](https://tools.ietf.org/html/rfc3339#section-5.6) 에 정의 된 전체 날짜 |
| `dateTime` | [RFC 3339](https://tools.ietf.org/html/rfc3339) 에 정의 된 날짜-시간 |
| `double` | IEEE 8 바이트 부동 소수점 |
| `duration` | ISO 8601 형식의 기간 |
| `float` | IEEE 4 바이트 부동 소수점 |
| `integer` | 부호 있는 4 바이트 정수입니다. |
| `long` | 부호 있는 8 바이트 정수입니다. |
| `string` | UTF8 문자열 |
| `time` | [RFC 3339의 5.6 절](https://tools.ietf.org/html/rfc3339#section-5.6) 에 정의 된 전체 시간 |

다음 코드 조각에서는 필드의 형식을 사용 하는 원격 분석 정의 예제를 보여 줍니다 `double` `schema` .

```json
{
  "@type": "Telemetry",
  "name": "temperature",
  "displayName": "Temperature",
  "schema": "double"
}
```

### <a name="complex-datatypes"></a>복합 데이터 형식

복합 데이터 형식은 *배열*, *열거형*, *맵*, *개체* 또는 지리 공간적 형식 중 하나입니다.

#### <a name="arrays"></a>배열

배열은 모든 요소가 동일한 유형인 인덱싱 가능한 데이터 형식입니다. 요소 형식은 기본 형식 또는 복합 형식일 수 있습니다.

다음 코드 조각에서는 필드의 형식을 사용 하는 원격 분석 정의 예제를 보여 줍니다 `Array` `schema` . 배열의 요소는 부울입니다.

```json
{
  "@type": "Telemetry",
  "name": "ledState",
  "schema": {
    "@type": "Array",
    "elementSchema": "boolean"
  }
}
```

#### <a name="enumerations"></a>열거형

열거형은 값에 매핑되는 명명 된 레이블 집합을 사용 하는 형식을 설명 합니다. 값은 정수 또는 문자열일 수 있지만 레이블은 항상 문자열입니다.

다음 코드 조각에서는 필드의 형식을 사용 하는 원격 분석 정의 예제를 보여 줍니다 `Enum` `schema` . 열거형의 값은 정수입니다.

```json
{
  "@type": "Telemetry",
  "name": "state",
  "schema": {
    "@type": "Enum",
    "valueSchema": "integer",
    "enumValues": [
      {
        "name": "offline",
        "displayName": "Offline",
        "enumValue": 1
      },
      {
        "name": "online",
        "displayName": "Online",
        "enumValue": 2
      }
    ]
  }
}
```

#### <a name="maps"></a>Maps

맵은 키-값 쌍이 있는 형식으로, 값이 모두 같은 형식입니다. 맵의 키는 문자열 이어야 합니다. Map의 값은 다른 복합 형식을 포함 한 모든 형식일 수 있습니다.

다음 코드 조각에서는 필드의 형식을 사용 하는 예제 속성 정의를 보여 줍니다 `Map` `schema` . 맵의 값은 문자열입니다.

```json
{
  "@type": "Property",
  "name": "modules",
  "writable": true,
  "schema": {
    "@type": "Map",
    "mapKey": {
      "name": "moduleName",
      "schema": "string"
    },
    "mapValue": {
      "name": "moduleState",
      "schema": "string"
    }
  }
}
```

### <a name="objects"></a>개체

개체 형식은 명명 된 필드로 구성 됩니다. 개체 맵의 필드 형식은 기본 형식 또는 복합 형식일 수 있습니다.

다음 코드 조각에서는 필드의 형식을 사용 하는 원격 분석 정의 예제를 보여 줍니다 `Object` `schema` . 개체의 필드는 `dateTime` , `duration` 및 `string` 형식입니다.

```json
{
  "@type": "Telemetry",
  "name": "monitor",
  "schema": {
    "@type": "Object",
    "fields": [
      {
        "name": "start",
        "schema": "dateTime"
      },
      {
        "name": "interval",
        "schema": "duration"
      },
      {
        "name": "status",
        "schema": "string"
      }
    ]
  }
}
```

#### <a name="geospatial-types"></a>지리 공간적 형식

Dtdl은 [GeoJSON](https://geojson.org/)를 기반으로 지리적 데이터 구조,,,, 및를 모델링 하는 지리 공간적 유형 집합을 제공 `point` `multiPoint` `lineString` `multiLineString` `polygon` `multiPolygon` 합니다. 이러한 형식은 배열, 개체 및 열거형의 미리 정의 된 중첩 구조입니다.

다음 코드 조각에서는 필드의 형식을 사용 하는 원격 분석 정의 예제를 보여 줍니다 `point` `schema` .

```json
{
  "@type": "Telemetry",
  "name": "location",
  "schema": "point"
}
```

지리 공간적 형식은 배열 기반 이므로 현재 속성 정의에서 사용할 수 없습니다.

## <a name="semantic-types"></a>의미 유형

속성 또는 원격 분석 정의의 데이터 형식은 장치가 서비스와 교환 하는 데이터의 형식을 지정 합니다. 의미 체계 형식은 응용 프로그램에서 값을 처리 하거나 표시 하는 방법을 결정 하는 데 사용할 수 있는 원격 분석 및 속성에 대 한 정보를 제공 합니다. 각 의미 체계 형식에는 하나 이상의 관련 단위가 있습니다. 예를 들어 섭씨와 화씨는 온도 의미 유형의 단위입니다. IoT Central 대시보드와 분석에서는 의미 체계 유형 정보를 사용 하 여 원격 분석 또는 속성 값과 표시 단위를 그리는 방법을 결정할 수 있습니다. 모델 파서를 사용 하 여 의미 체계 형식을 읽는 방법을 알아보려면 [디지털 쌍 모델 파서 이해](concepts-model-parser.md)를 참조 하세요.

다음 코드 조각에서는 의미 체계 형식 정보를 포함 하는 원격 분석 정의 예제를 보여 줍니다. 의미 체계 형식이 `Temperature` 배열에 추가 되 `@type` 고 `unit` 값은 `degreeCelsius` 의미 체계 형식에 대 한 유효한 단위 중 하나입니다.

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

## <a name="localization"></a>지역화

IoT Central와 같은 응용 프로그램은 모델의 정보를 사용 하 여 IoT 플러그 앤 플레이 장치와 교환 되는 데이터에 대 한 UI를 동적으로 작성 합니다. 예를 들어 대시보드의 타일은 원격 분석, 속성 및 명령에 대 한 이름 및 설명을 표시할 수 있습니다.

`description` `displayName` 모델의 선택적 필드와 필드에는 UI에서 사용 하기 위한 문자열이 포함 됩니다. 이러한 필드는 응용 프로그램이 지역화 된 UI를 렌더링 하는 데 사용할 수 있는 지역화 된 문자열을 포함할 수 있습니다.

다음 코드 조각은 지역화 된 문자열을 포함 하는 예제 온도 원격 분석 정의를 보여 줍니다.

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "description": {
    "en": "Temperature in degrees Celsius.",
    "it": "Temperatura in gradi Celsius."
  },
  "displayName": {
    "en": "Temperature",
    "it": "Temperatura"
  },
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

지역화 된 문자열 추가는 선택 사항입니다. 다음 예제에는 단일 기본 언어만 있습니다.

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "description": "Temperature in degrees Celsius.",
  "displayName": "Temperature",
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

## <a name="lifecycle-and-tools"></a>수명 주기 및 도구

장치 모델에 대 한 4 가지 수명 주기 단계는 제작, 게시, 사용 및 버전 관리입니다.

### <a name="author"></a>작성자

DTML 장치 모델은 텍스트 편집기에서 만들 수 있는 JSON 문서입니다. 그러나 IoT Central에서 장치 템플릿 GUI 환경을 사용 하 여 DTML 모델을 만들 수 있습니다. IoT Central에서 다음을 수행할 수 있습니다.

- 속성, 원격 분석 및 명령을 정의 하는 인터페이스를 만듭니다.
- 구성 요소를 사용 하 여 여러 인터페이스를 함께 어셈블합니다.
- 인터페이스 간에 상속 관계를 정의 합니다.
- DTML 모델 파일을 가져오고 내보냅니다.

자세히 알아보려면 [Azure IoT Central 응용 프로그램에서 새 IoT 장치 유형 정의](../iot-central/core/howto-set-up-template.md)를 참조 하세요.

[Visual Studio Code 용 Dtdl 편집기](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) 는 모델 제작 환경을 보다 세부적으로 제어 하기 위해 구문 유효성 검사 및 자동 완성 기능을 제공 하는 텍스트 기반 편집 환경을 제공 합니다.

### <a name="publish"></a>게시

DTML 모델을 공유할 수 있고 검색 가능 하 게 만들려면 장치 모델 리포지토리에 게시 합니다.

공용 리포지토리에서 모델을 게시 하기 전에 도구를 사용 `dmr-client` 하 여 모델의 유효성을 검사할 수 있습니다.

자세히 알아보려면 [장치 모델 리포지토리](concepts-model-repository.md)를 참조 하세요.

### <a name="use"></a>Windows Server Update Services와 함께

IoT Central와 같은 응용 프로그램은 장치 모델을 사용 합니다. IoT Central에서 모델은 장치의 기능을 설명 하는 장치 템플릿의 일부입니다. IoT Central는 장치 템플릿을 사용 하 여 대시보드 및 분석을 비롯 한 장치에 대 한 UI를 동적으로 빌드합니다.

사용자 지정 솔루션은 [디지털 쌍 모델 파서](concepts-model-parser.md) 를 사용 하 여 모델을 구현 하는 장치의 기능을 이해할 수 있습니다. 자세히 알아보려면 [iot 솔루션에서 iot 플러그 앤 플레이 모델 사용](concepts-model-discovery.md)을 참조 하세요.

### <a name="version"></a>버전

모델을 사용 하는 장치 및 서버 쪽 솔루션이 계속 작동 하도록 하기 위해 게시 된 모델은 변경할 수 없습니다.

DTMI에는 여러 버전의 모델을 만드는 데 사용할 수 있는 버전 번호가 포함 되어 있습니다. 장치 및 서버 쪽 솔루션은 사용 하도록 디자인 된 특정 버전을 사용할 수 있습니다.

IoT Central는 장치 모델에 대 한 더 많은 버전 관리 규칙을 구현 합니다. IoT Central에서 장치 템플릿과 해당 모델의 버전을 지정할 경우 이전 버전에서 이후 버전으로 장치를 마이그레이션할 수 있습니다. 그러나 마이그레이션된 장치는 펌웨어 업그레이드 없이는 새로운 기능을 사용할 수 없습니다. 자세히 알아보려면 [새 장치 템플릿 버전 만들기](../iot-central/core/howto-version-device-template.md)를 참조 하세요.

## <a name="limits-and-constraints"></a>제한 및 제약 조건

다음 목록에는 모델에 대 한 몇 가지 주요 제약 조건 및 제한이 요약 되어 있습니다.

- 현재는 배열, 지도 및 개체의 최대 깊이가 5 수준입니다.
- 속성 정의에는 배열을 사용할 수 없습니다.
- 인터페이스를 10 개 수준으로 확장할 수 있습니다.
- 인터페이스는 둘 이상의 다른 인터페이스를 확장할 수 있습니다.
- 구성 요소는 다른 구성 요소를 포함할 수 없습니다.

## <a name="next-steps"></a>다음 단계

이제 장치 모델링에 대해 알아보았습니다. 몇 가지 추가 리소스는 다음과 같습니다.

- [DTDL authoring tools 설치 및 사용](howto-use-dtdl-authoring-tools.md)
- [디지털 Twins 정의 언어 v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [모델 리포지토리](./concepts-model-repository.md)
