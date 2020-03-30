---
title: 개발자 가이드 - IoT 플러그 앤 플레이 미리보기 | 마이크로 소프트 문서
description: IoT 플러그 앤 플레이 개발자를 위한 장치 모델링에 대한 설명
author: dominicbetts
ms.author: dobett
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5fda51e6d2f62b9cbef0fcac22d5bb2ea0df905b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605211"
---
# <a name="iot-plug-and-play-preview-modeling-developer-guide"></a>IoT 플러그 앤 플레이 미리 보기 모델링 개발자 가이드

IoT 플러그 앤 플레이 미리 보기를 사용하면 Azure IoT 응용 프로그램에 기능을 보급하는 장치를 빌드할 수 있습니다. IoT 플러그 앤 플레이 장치는 고객이 IoT 플러그 앤 플레이 지원 애플리케이션에 연결할 때 수동 구성이 필요하지 않습니다. IoT Central은 IoT 플러그 앤 플레이 지원 애플리케이션의 예입니다.

IoT 플러그 앤 플레이 장치를 빌드하려면 장치 설명을 만들어야 합니다. 설명은 DTDL(디지털 트윈 정의 언어)이라는 간단한 정의 언어로 수행됩니다.

## <a name="device-capability-model"></a>디바이스 기능 모델

DTDL을 사용하면 장치의 일부를 설명하는 _장치 기능 모델을_ 만들 수 있습니다. 일반적인 IoT 장치는 다음과 같은 구성으로 구성됩니다.

- 사용자 정의 부품, 이는 당신의 장치를 고유하게 하는 것들입니다.
- 모든 장치에 공통적인 표준 부품입니다.

이러한 부분을 장치 기능 모델의 _인터페이스라고_ 합니다. 인터페이스는 장치가 구현하는 각 부분의 세부 정보를 정의합니다.

다음 예제에서는 온도 조절 장치에 대한 장치 기능 모델을 보여 주며 있습니다.

```json
{
  "@id": "urn:example:Thermostat_T_1000:1",
  "@type": "CapabilityModel",
  "implements": [
    {
      "name": "thermostat",
      "schema": "urn:example:Thermostat:1"
    },
    {
      "name": "urn_azureiot_deviceManagement_DeviceInformation",
      "schema": "urn:azureiot:deviceManagement:DeviceInformation:1"
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

기능 모델에는 몇 가지 필수 필드가 있습니다.

- `@id`: 간단한 통일 자원 이름의 형태로 고유 ID.
- `@type`: 이 개체가 기능 모델임을 선언합니다.
- `@context`: 기능 모델에 사용되는 DTDL 버전을 지정합니다.
- `implements`: 장치가 구현하는 인터페이스를 나열합니다.

구현 섹션의 인터페이스 목록의 각 항목에는 다음이 있습니다.

- `name`: 인터페이스의 프로그래밍 이름입니다.
- `schema`: 기능 모델이 구현하는 인터페이스입니다.

기능 모델에 표시 이름 및 설명과 같은 세부 정보를 추가하는 데 사용할 수 있는 추가 선택적 필드가 있습니다. 기능 모델 내에서 선언된 인터페이스는 장치의 구성 요소로 생각할 수 있습니다. 공개 미리 보기의 경우 인터페이스 목록에 스키마당 항목이 하나만 있을 수 있습니다.

## <a name="interface"></a>인터페이스

DTDL을 사용하면 인터페이스를 사용하여 장치의 기능을 설명합니다. 인터페이스는 _속성,_ _원격 분석_및 장치 구현의 일부를 _명령합니다._

- `Properties`. 속성은 장치의 상태를 나타내는 데이터 필드입니다. 속성을 사용하여 냉각수 펌프의 온-오프 상태와 같은 장치의 내구성 상태를 나타냅니다. 속성은 장치의 펌웨어 버전과 같은 기본 장치 속성을 나타낼 수도 있습니다. 속성을 읽기 전용 또는 쓰기 용으로 선언할 수 있습니다.
- `Telemetry`. 원격 분석 필드는 센서의 측정값을 나타냅니다. 장치가 센서 측정을 할 때마다 센서 데이터가 포함된 원격 분석 이벤트를 보내야 합니다.
- `Commands`. 명령은 장치 사용자가 장치에서 실행할 수 있는 메서드를 나타냅니다. 예를 들어, 팬 켜거나 끄는 리셋 명령 또는 명령입니다.

다음 예제에서는 온도 조절 장치에 대한 인터페이스를 보여 줍니다.

```json
{
  "@id": "urn:example:Thermostat:1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Telemetry",
      "name": "temperature",
      "schema": "double"
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

인터페이스에는 몇 가지 필수 필드가 있습니다.

- `@id`: 간단한 통일 자원 이름의 형태로 고유 ID.
- `@type`: 이 개체가 인터페이스임을 선언합니다.
- `@context`: 인터페이스에 사용되는 DTDL 버전을 지정합니다.
- `contents`: 장치를 구성하는 속성, 원격 분석 및 명령을 나열합니다.

이 간단한 예제에서는 단일 원격 분석 필드만 있습니다. 최소 필드 설명에는 다음이 있습니다.

- `@type`: 기능 `Telemetry` `Property` `Command`유형을 지정합니다.
- `name`: 원격 분석 값의 이름을 제공합니다.
- `schema`: 원격 분석의 데이터 형식을 지정합니다. 이 값은 double, 정수, 부울 또는 문자열과 같은 기본 형식일 수 있습니다. 복잡한 개체 유형, 배열 및 맵도 지원됩니다.

표시 이름 및 설명과 같은 다른 선택적 필드를 사용하면 인터페이스 및 기능에 더 많은 세부 정보를 추가할 수 있습니다.

### <a name="properties"></a>속성

기본적으로 속성은 읽기 전용입니다. 읽기 전용 속성은 장치가 IoT 허브에 속성 값 업데이트를 보고한다는 것을 의미합니다. IoT 허브는 읽기 전용 속성의 값을 설정할 수 없습니다.

인터페이스에서 쓰기 가능한 것으로 속성을 표시할 수도 있습니다. 장치는 IoT 허브에서 쓰기 가능한 속성에 대한 업데이트를 받을 수 있을 뿐만 아니라 허브에 속성 값 업데이트를 보고할 수 있습니다.

속성 값을 설정하기 위해 장치를 연결할 필요가 없습니다. 업데이트된 값은 장치가 다음에 허브에 연결될 때 전송됩니다. 이 동작은 읽기 전용 및 쓰기 가능한 속성 모두에 적용됩니다.

장치에서 원격 분석을 전송하는 속성을 사용하지 마십시오. 예를 들어 readonly 속성과 `temperatureSetting=80` 같은 경우 장치 온도가 80으로 설정되어 장치가 이 온도에 도착하거나 유지하려고 한다는 의미여야 합니다.

쓰기 가능한 속성의 경우 장치 응용 프로그램은 속성 값을 수신하고 적용했는지 여부를 나타내기 위해 원하는 상태 코드, 버전 및 설명을 반환합니다.

### <a name="telemetry"></a>원격 분석

기본적으로 IoT Hub는 장치에서 이벤트 허브와 호환되는 [기본 제공 서비스 설정 끝점(메시지/이벤트)으로**messages/events**](../iot-hub/iot-hub-devguide-messages-read-builtin.md) 모든 원격 분석 메시지를 [라우팅합니다.](https://azure.microsoft.com/documentation/services/event-hubs/)

[IoT Hub의 사용자 지정 끝점 및 라우팅 규칙을](../iot-hub/iot-hub-devguide-messages-d2c.md) 사용하여 Blob 저장소 또는 기타 이벤트 허브와 같은 다른 대상으로 원격 분석을 보낼 수 있습니다. 라우팅 규칙은 메시지 속성을 사용하여 메시지를 선택합니다.

### <a name="commands"></a>명령

명령은 동기 또는 비동기입니다. 동기 명령은 기본적으로 30초 이내에 실행되어야 하며 명령이 도착하면 장치를 연결해야 합니다. 장치가 제 시간에 응답하거나 장치가 연결되지 않은 경우 명령이 실패합니다.

장기 실행 작업에비동기 명령을 사용합니다. 장치는 원격 분석 메시지를 사용하여 진행률 정보를 보냅니다. 이러한 진행률 메시지에는 다음과 같은 헤더 속성이 있습니다.

- `iothub-command-name`: 명령 이름(예: `UpdateFirmware`.
- `iothub-command-request-id`: 서버 측에서 생성된 요청 ID로 초기 호출시 장치로 전송됩니다.
- `iothub-interface-id`: 이 명령이 정의된 인터페이스의 ID(예: `urn:example:AssetTracker:1`).
 `iothub-interface-name`: 이 인터페이스의 인스턴스 이름(예: `myAssetTracker`) .
- `iothub-command-statuscode`: 예를 들어 `202`장치에서 반환된 상태 코드입니다.

## <a name="register-a-device"></a>디바이스 등록

IoT 플러그 앤 플레이를 사용하면 장치의 기능을 쉽게 광고할 수 있습니다. IoT 플러그 앤 플레이를 사용하면 장치가 IoT Hub에 연결되면 장치 기능 모델을 등록해야 합니다. 등록을 통해 고객은 장치의 IoT 플러그 앤 플레이 기능을 사용할 수 있습니다.

이 가이드에서는 C용 Azure IoT 장치 SDK를 사용하여 장치를 등록하는 방법을 보여 주실 수 있습니다.

장치가 구현하는 각 인터페이스에 대해 인터페이스를 만들고 구현에 연결해야 합니다.

C SDK를 사용하여 이전에 표시된 온도 조절기 인터페이스의 경우 온도 조절 기 인터페이스를 생성하고 구현에 연결합니다.

```c
DIGITALTWIN_INTERFACE_HANDLE thermostatInterfaceHandle;

DIGITALTWIN_CLIENT_RESULT result = DigitalTwin_InterfaceClient_Create(
    "thermostat",
    "urn:example:Thermostat:1",
    null, null,
    &thermostatInterfaceHandle);

result = DigitalTwin_Interface_SetCommandsCallbacks(
    thermostatInterfaceHandle,
    commandsCallbackTable);

result = DigitalTwin_Interface_SetPropertiesUpdatedCallbacks(
    thermostatInterfaceHandle,
    propertiesCallbackTable);

```

장치가 구현하는 각 인터페이스에 대해 이 코드를 반복합니다.

인터페이스를 만든 후 IoT 허브에 장치 기능 모델 및 인터페이스를 등록합니다.

```c
DIGITALTWIN_INTERFACE_CLIENT_HANDLE interfaces[2];
interfaces[0] = thermostatInterfaceHandle;
interfaces[1] = deviceInfoInterfaceHandle;

result = DigitalTwin_DeviceClient_RegisterInterfacesAsync(
    digitalTwinClientHandle, // The handle for the connection to Azure IoT
    "urn:example:Thermostat_T_1000:1",
    interfaces, 2,
    null, null);
```

## <a name="use-a-device"></a>장치 사용

IoT 플러그 앤 플레이를 사용하면 IoT 허브에 기능을 등록한 장치를 사용할 수 있습니다. 예를 들어 장치의 속성 및 명령에 직접 액세스할 수 있습니다.

IoT 허브에 연결된 IoT 플러그 앤 플레이 장치를 사용하려면 IoT Hub REST API 또는 IoT 언어 SDK 중 하나를 사용합니다. 다음 예제는 IoT Hub REST API를 사용합니다. API의 현재 버전은 `2019-07-01-preview`. REST `?api-version=2019-07-01-preview` PI 호출에 연결합니다.

온도 조절기의`fwVersion` `DeviceInformation` 인터페이스에서 펌웨어 버전 ()과 같은 장치 속성의 값을 얻으려면 디지털 트윈 REST API를 사용합니다.

온도 조절 장치가 호출되는 `t-123`경우 REST API GET 호출을 사용하여 장치에서 구현한 모든 인터페이스의 모든 속성을 가져옵니다.

```REST
GET /digitalTwins/t-123/interfaces
```

일반적으로 모든 인터페이스의 모든 `{device-id}` 속성은 장치의 식별자인 이 REST API 템플릿을 사용하여 액세스됩니다.

```REST
GET /digitalTwins/{device-id}/interfaces
```

와 같은 `deviceInformation`인터페이스 이름을 알고 있고 해당 특정 인터페이스에 대한 속성을 얻으려면 요청의 범위를 이름으로 특정 인터페이스로 지정합니다.

```REST
GET /digitalTwins/t-123/interfaces/deviceInformation
```

일반적으로 특정 인터페이스에 대한 속성은 장치의 식별자이며 `device-id` `{interface-name}` 인터페이스의 이름인 이 REST API 템플릿을 통해 액세스할 수 있습니다.

```REST
GET /digitalTwins/{device-id}/interfaces/{interface-name}
```

IoT 플러그 앤 플레이 장치 명령을 직접 호출할 수 있습니다. 장치의 `Thermostat` 인터페이스에 명령이 `restart` 있는 경우 REST API POST 호출을 사용하여 호출할 수 있습니다. `t-123`

```REST
POST /digitalTwins/t-123/interfaces/thermostat/commands/restart
```

일반적으로 이 REST API 템플릿을 통해 명령을 호출할 수 있습니다.

- `device-id`: 장치의 식별자입니다.
- `interface-name`: 장치 기능 모델의 구현 섹션의 인터페이스 이름입니다.
- `command-name`: 명령의 이름입니다.

```REST
/digitalTwins/{device-id}/interfaces/{interface-name}/commands/{command-name}
```

## <a name="next-steps"></a>다음 단계

장치 모델링에 대해 배웠으니 다음과 같은 몇 가지 추가 리소스가 있습니다.

- [디지털 트윈 정의 언어(DTDL)](https://aka.ms/DTDL)
- [C 장치 SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
