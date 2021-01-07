---
title: IoT 플러그 앤 플레이 모델의 구성 요소 이해 | Microsoft Docs
description: 구성 요소를 사용 하지 않는 구성 요소 및 모델을 사용 하는 IoT 플러그 앤 플레이 DTDL 모델 간의 차이점을 이해 합니다.
author: ericmitt
ms.author: ericmitt
ms.date: 07/07/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 7d33811dc056baa3d205d7d86c1bdd7bff74ae54
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92330005"
---
# <a name="iot-plug-and-play-components-in-models"></a>모델의 IoT 플러그 앤 플레이 구성 요소

IoT 플러그 앤 플레이 규칙에서 디바이스는 IoT Hub에 연결할 때 DTDL(디지털 트윈 정의 언어) 모델 ID를 제공하는 경우 IoT 플러그 앤 플레이 디바이스입니다.

다음 코드 조각에서 몇 가지 모델 ID 예를 보여 줍니다.

```json
 "@id": "dtmi:com:example:TemperatureController;1"
 "@id": "dtmi:com:example:Thermostat;1",
```

## <a name="no-components"></a>구성 요소 없음

단순 모델은 포함 된 구성 요소 또는 종속 된 구성 요소를 사용 하지 않습니다. 여기에는 원격 분석, 속성 및 명령을 정의 하기 위한 헤더 정보 및 내용 섹션이 포함 되어 있습니다.

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

모델은 구성 요소를 명시적으로 정의 하지 않지만 모든 원격 분석, 속성 및 명령 정의를 포함 하는 단일 _기본 구성 요소가_있는 것 처럼 동작 합니다.

다음 스크린샷은 Azure IoT 탐색기 도구에 모델을 표시 하는 방법을 보여 줍니다.

:::image type="content" source="media/concepts-components/default-component.png" alt-text="Azure IoT 탐색기의 기본 구성 요소":::

모델 ID는 다음 스크린샷에 표시 된 것 처럼 장치 쌍 속성에 저장 됩니다.

:::image type="content" source="media/concepts-components/twin-model-id.png" alt-text="Azure IoT 탐색기의 기본 구성 요소":::

구성 요소가 없는 DTDL 모델은 단일 원격 분석, 속성 및 명령 집합을 사용 하는 장치 또는 IoT Edge 모듈에 유용 하 게 사용할 수 있습니다. 구성 요소를 사용 하지 않는 모델을 사용 하면 기존 장치 또는 모듈을 IoT 플러그 앤 플레이 장치 또는 모듈로 쉽게 마이그레이션할 수 있습니다. 구성 요소를 정의할 필요 없이 실제 장치 또는 모듈을 설명 하는 DTDL 모델을 만듭니다.

> [!TIP]
> 모듈은 장치 [모듈](../iot-hub/iot-hub-devguide-module-twins.md) 이거나 [IoT Edge 모듈인](../iot-edge/about-iot-edge.md)수 있습니다.

## <a name="multiple-components"></a>여러 구성 요소

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
...
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
...
```

이 모델에는 내용 섹션 (두 구성 요소 및 구성 요소)에 정의 된 세 가지 구성 요소가 있습니다 `Thermostat` `DeviceInformation` . 기본 구성 요소도 있습니다.

## <a name="next-steps"></a>다음 단계

이제 모델 구성 요소에 대해 알아보았습니다. 몇 가지 추가 리소스는 다음과 같습니다.

- [DTDL authoring tools 설치 및 사용](howto-use-dtdl-authoring-tools.md)
- [디지털 Twins 정의 언어 v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [모델 리포지토리](./concepts-model-repository.md)
