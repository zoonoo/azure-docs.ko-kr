---
title: 서비스 개발자 가이드-IoT 플러그 앤 플레이 | Microsoft Docs
description: 서비스 개발자를 위한 IoT 플러그 앤 플레이 설명
author: dominicbetts
ms.author: dobett
ms.date: 09/24/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: f082e4d4c6c71e460842f80a5aa17130b6a41279
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91614226"
---
# <a name="iot-plug-and-play-service-developer-guide"></a>IoT 플러그 앤 플레이 서비스 개발자 가이드

IoT 플러그 앤 플레이를 사용 하 여 Azure IoT 응용 프로그램에 기능을 보급 하는 스마트 장치를 빌드할 수 있습니다. IoT 플러그 앤 플레이 장치는 고객이 IoT 플러그 앤 플레이 지원 응용 프로그램에 연결할 때 수동 구성이 필요 하지 않습니다.

IoT 플러그 앤 플레이를 사용 하면 IoT hub로 모델 ID를 발표 한 장치를 사용할 수 있습니다. 예를 들어 장치의 속성 및 명령에 직접 액세스할 수 있습니다.

Iot hub에 연결 된 IoT 플러그 앤 플레이 장치를 사용 하려면 iot 서비스 Sdk 또는 IoT Hub REST API 중 하나를 사용 합니다.

## <a name="service-sdks"></a>서비스 SDK

솔루션에서 Azure IoT 서비스 Sdk를 사용 하 여 장치 및 모듈과 상호 작용 합니다. 예를 들어, 서비스 Sdk를 사용 하 여 쌍 속성을 읽고 업데이트 하 고 명령을 호출할 수 있습니다. 지원 되는 언어에는 c #, Java, Node.js 및 Python이 포함 됩니다.

서비스 Sdk를 사용 하면 데스크톱 또는 웹 응용 프로그램과 같은 솔루션에서 장치 정보에 액세스할 수 있습니다. 서비스 Sdk에는 모델 ID를 검색 하는 데 사용할 수 있는 두 가지 네임 스페이스와 개체 모델이 포함 되어 있습니다.

- Iot Hub 서비스 클라이언트입니다. 이 서비스는 모델 ID를 장치 쌍 속성으로 노출 합니다.

- Digital Twins 서비스 클라이언트. 새 Digital Twins API는 디지털 Twins 정의 언어 모델을 정의 하는 구성 요소, 속성 및 명령과 같은 개략적인 구문에서 작동 합니다. 디지털 쌍 Api를 사용 하면 솔루션 빌더가 IoT 플러그 앤 플레이 솔루션을 쉽게 만들 수 있습니다.

| 플랫폼 | IoT Hub 서비스 클라이언트 | 디지털 쌍 서비스 클라이언트 |
| -------- | ---------------------- | ---------------------------- |
| .NET     | [설명서](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.shared.twin.modelid?view=azure-dotnet#Microsoft_Azure_Devices_Shared_Twin_ModelId&preserve-view=true) <br/> [샘플](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples)| [샘플](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/DigitalTwinClientSamples) |
| Java     | [설명서](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.devicetwindevice?view=azure-java-stable&preserve-view=true) <br/> [샘플](https://github.com/Azure/azure-iot-sdk-java/blob/master/service/iot-service-samples/pnp-service-sample)| [샘플](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/digitaltwin-service-samples) |
| Node.js  | [설명서](https://docs.microsoft.com/javascript/api/azure-iothub/twin?view=azure-node-latest&preserve-view=true) <br/> [샘플](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/twin.js)| [설명서](https://docs.microsoft.com/javascript/api/azure-iot-digitaltwins-service/?view=azure-node-latest&preserve-view=true) <br/> [샘플](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/get_digital_twin.js) |
| Python   | [설명서](https://docs.microsoft.com/python/api/azure-iot-hub/azure.iot.hub.iothubregistrymanager?view=azure-python&preserve-view=true) <br/> [샘플](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/iothub_registry_manager_method_sample.py)| [설명서](https://docs.microsoft.com/python/api/azure-iot-hub/azure.iot.hub.iothubdigitaltwinmanager?view=azure-python&preserve-view=true) <br/> [샘플](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/get_digital_twin_sample.py) |

## <a name="rest-api"></a>REST API

다음 예에서는 IoT Hub REST API를 사용 하 여 연결 된 IoT 플러그 앤 플레이 장치와 상호 작용 합니다. API의 현재 버전은 `2020-09-30` 입니다. `?api-version=2020-09-30`REST PI 호출에 추가 합니다.

> [!NOTE]
> 모듈 쌍은 현재 API에서 지원 되지 않습니다 `digitalTwins` .

자동 온도 조절기 장치를 호출 하는 경우 `t-123` REST API get 호출을 사용 하 여 장치에서 구현 된 모든 인터페이스의 모든 속성을 가져옵니다.

```REST
GET /digitalTwins/t-123
```

이 호출에는 `$metadata.$model` 장치에서 발표 한 모델 ID를 포함 하는 Json 속성이 포함 됩니다.

모든 인터페이스의 모든 속성은 REST API 템플릿을 사용 하 여 액세스할 수 있습니다 `GET /DigitalTwin/{device-id}` `{device-id}` . 여기서은 장치에 대 한 식별자입니다.

```REST
GET /digitalTwins/{device-id}
```

IoT 플러그 앤 플레이 장치 명령을 직접 호출할 수 있습니다. `Thermostat`장치의 구성 요소에 명령이 있는 경우 `t-123` `restart` REST API POST 호출을 사용 하 여 호출할 수 있습니다.

```REST
POST /digitalTwins/t-123/components/Thermostat/commands/restart
```

보다 일반적으로이 REST API 템플릿을 통해 명령을 호출할 수 있습니다.

- `device-id`: 장치의 식별자입니다.
- `component-name`: 장치 기능 모델의 implements 섹션에 있는 인터페이스의 이름입니다.
- `command-name`: 명령의 이름입니다.

```REST
/digitalTwins/{device-id}/components/{component-name}/commands/{command-name}
```

## <a name="next-steps"></a>다음 단계

이제 장치 모델링에 대해 알아보았습니다. 몇 가지 추가 리소스는 다음과 같습니다.

- [DTDL(디지털 쌍 정의 언어)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C 디바이스 SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
- [모델 구성 요소](./concepts-components.md)
- [DTDL authoring tools 설치 및 사용](howto-use-dtdl-authoring-tools.md)
