---
title: IoT 플러그 앤 플레이 브리지 | Microsoft Docs
description: IoT 플러그 앤 플레이 브리지에 대해 알아보고, 이 브리지를 사용해 Windows 또는 Linux 게이트웨이에 연결된 기존 장치를 IoT 플러그 앤 플레이 장치로 연결하는 방법을 살펴봅니다.
author: usivagna
ms.author: ugans
ms.date: 1/20/2021
ms.topic: conceptual
ms.service: iot-develop
services: iot-develop
ms.custom: mvc
ms.openlocfilehash: 975b663be048d4438b14fce29e1a055bbd979643
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2021
ms.locfileid: "114406569"
---
# <a name="iot-plug-and-play-bridge"></a>IoT 플러그 앤 플레이 브리지

IoT 플러그 앤 플레이 브리지는 Windows 또는 Linux 게이트웨이에 연결된 기존 장치를 IoT 플러그 앤 플레이 장치로 연결하는 오픈 소스 응용 프로그램입니다. Windows 또는 Linux 컴퓨터에서 응용 프로그램을 설치 및 구성한 후 이를 사용하여 연결된 장치를 IoT Hub에 연결할 수 있습니다. 브리지를 사용해 연결된 장치에서 보내는 원격 분석에 IoT 플러그 앤 플레이 인터페이스를 매핑하고, 장치 속성으로 작업하며, 명령을 호출할 수 있습니다.

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-high-level.png" alt-text="왼쪽에는 IoT 플러그 앤 플레이 브리지가 포함된 Windows 또는 Linux PC에 몇 가지 기존 센서(유/무선 모두)가 연결되어 있습니다. 그런 다음 IoT 플러그 앤 플레이 브리지는 오른쪽에 있는 IoT Hub에 연결됩니다":::

IoT 플러그 앤 플레이 브리지는 Windows 10 또는 Linux를 실행하는 모든 IoT 장치, 산업용 PC, 서버 또는 게이트웨이에서 독립 실행형 실행 파일로 배포될 수 있습니다. 또한 응용 프로그램 코드로 컴파일될 수 있습니다. 간단한 구성 JSON 파일은 Azure에 공개해야 하는 연결된 장치/주변 장치를 IoT 플러그 앤 플레이 브리지에 알려줍니다.

## <a name="supported-protocols-and-sensors"></a>지원되는 프로토콜 및 센서

IoT 플러그 앤 플레이 브리지는 어댑터 설명서의 링크를 사용해 기본적으로 다음과 같은 유형의 주변 장치를 지원합니다.

|주변 장치|Windows|Linux|
|---------|---------|---------|
|[Bluetooth 센서 어댑터](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/bluetooth_sensor_adapter.md)는 검색된 BLE(Bluetooth Low Energy) 사용 센서를 연결합니다.       |예|아니요|
|[카메라 어댑터](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/camera_adapter.md)는 Windows 10 장치에서 카메라를 연결합니다.               |예|아니요|
|[Modbus 어댑터](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/modbus_adapters.md)는 Modbus 장치에서 센서를 연결합니다.              |예|예|
|[MQTT 어댑터](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/mqtt_adapter.md)는 MQTT broker를 사용하는 장치를 연결합니다.                  |예|예|
|[SerialPnP 어댑터](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/serialpnp/Readme.md)는 직렬 연결을 통해 통신하는 장치를 연결합니다.               |예|예|
|[Windows USB 주변 장치](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/coredevicehealth_adapter.md)는 어댑터 지원 장치 인터페이스 클래스 목록을 사용하여 특정 하드웨어 ID가 있는 장치를 연결합니다.  |Yes|해당 없음|

IoT 플러그 앤 플레이 브리지를 확장하여 추가 장치 프로토콜을 지원하는 방법을 알아보려면 [IoT 플러그 앤 플레이 브리지 확장](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/author_adapter.md)을 참조하세요. IoT 플러그 앤 플레이 브리지를 빌드하고 배포하는 방법을 알아보려면 [IoT 플러그 앤 플레이 브리지 빌드 및 배포](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/build_deploy.md)를 참조하세요.

## <a name="iot-plug-and-play-bridge-architecture"></a>IoT 플러그 앤 플레이 브리지 아키텍처

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-components.png" alt-text="왼쪽에는 IoT 플러그 앤 플레이 브리지가 포함된 Windows 또는 Linux PC에 연결된 다양한 주변 장치를 나타내는 여러 상자가 있습니다. 위쪽부터 구성 레이블이 지정된 상자가 브리지로 향합니다. 그런 다음 브리지는 다이어그램의 오른쪽에 있는 IoT 허브에 연결됩니다.":::

### <a name="iot-plug-and-play-bridge-adapters"></a>IoT 플러그 앤 플레이 브리지 어댑터

IoT 플러그 앤 플레이 브리지는 다양한 형식의 장치에 사용되는 IoT 플러그 앤 플레이 브리지 어댑터 세트를 지원합니다. *어댑터 매니페스트* 는 브리지에 어댑터를 정적으로 정의합니다.

브리지 어댑터 관리자는 매니페스트를 사용하여 어댑터 함수를 식별하고 호출합니다. 어댑터 관리자는 구성 파일에 나열된 인터페이스 구성 요소에 필요한 브리지 어댑터에서만 create 함수를 호출합니다. 각 IoT 플러그 앤 플레이 구성 요소에 대한 어댑터 인스턴스가 만들어집니다.

브리지 어댑터는 디지털 트윈 인터페이스 핸들을 만들어 가져옵니다. 어댑터는 이 핸들을 사용하여 장치 기능을 디지털 트윈에 바인딩합니다.

구성 파일의 정보를 사용하는 브리지 어댑터는 다음 기술을 이용하여 브리지를 통해 전체 장치에서 디지털 트윈 통신을 사용할 수 있도록 합니다.

- 통신 채널을 직접 설정합니다.
- 통신 채널을 사용할 수 있게 될 때까지 대기하는 장치 감시자를 만듭니다.

### <a name="configuration-file"></a>구성 파일

IoT 플러그 앤 플레이 브리지는 다음을 지정하는 JSON 기반 구성 파일을 사용합니다.

- IoT Hub 또는 IoT Central 응용 프로그램에 연결하는 방법: 옵션에는 연결 문자열, 인증 매개 변수 또는 DPS(장치 프로비전 서비스)가 포함됩니다.
- 브리지에서 사용하는 IoT 플러그 앤 플레이 기능 모델의 위치. 모델은 IoT 플러그 앤 플레이 장치의 기능을 정의하며, 정적이고 변경할 수 없습니다.
- IoT 플러그 앤 플레이 인터페이스 구성 요소 목록과 다음과 같은 각 구성 요소에 대한 정보:
- 인터페이스 ID 및 구성 요소 이름입니다.
- 구성 요소와 상호 작용하는 데 필요한 브리지 어댑터입니다.
- 브리지 어댑터가 장치와의 통신을 설정하는 데 필요한 장치 정보입니다. 예를 들면 하드웨어 ID 또는 어댑터, 인터페이스 또는 프로토콜에 대한 특정 정보가 있습니다.
- 어댑터가 비슷한 장치를 사용하는 여러 통신 유형을 지원하는 경우 선택적 브리지 어댑터 하위 유형 또는 인터페이스 구성. 이 예제에서는 Bluetooth 센서 구성 요소를 구성할 수 있는 방법을 보여줍니다.

    ```json
    {
      "_comment": "Component BLE sensor",
      "pnp_bridge_component_name": "blesensor1",
      "pnp_bridge_adapter_id": "bluetooth-sensor-pnp-adapter",
      "pnp_bridge_adapter_config": {
        "bluetooth_address": "267541100483311",
        "blesensor_identity" : "Blesensor1"
      }
    }
    ```

- 전역 브리지 어댑터 매개 변수의 선택적 목록입니다. 예를 들어 Bluetooth 센서 브리지 어댑터에는 지원되는 구성 사전이 있습니다. Bluetooth 센서 어댑터가 필요한 인터페이스 구성 요소는 `blesensor_identity`로 다음 구성 중 하나를 선택할 수 있습니다.

    ```json
    {
      "pnp_bridge_adapter_global_configs": {
        "bluetooth-sensor-pnp-adapter": {
          "Blesensor1" : {
            "company_id": "0x499",
            "endianness": "big",
            "telemetry_descriptor": [
              {
                "telemetry_name": "humidity",
                "data_parse_type": "uint8",
                "data_offset": 1,
                "conversion_bias": 0,
                "conversion_coefficient": 0.5
              },
              {
                "telemetry_name": "temperature",
                "data_parse_type": "int8",
                "data_offset": 2,
                "conversion_bias": 0,
                "conversion_coefficient": 1.0
              },
              {
                "telemetry_name": "pressure",
                "data_parse_type": "int16",
                "data_offset": 4,
                "conversion_bias": 0,
                "conversion_coefficient": 1.0
              },
              {
                "telemetry_name": "acceleration_x",
                "data_parse_type": "int16",
                "data_offset": 6,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              },
              {
                "telemetry_name": "acceleration_y",
                "data_parse_type": "int16",
                "data_offset": 8,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              },
              {
                "telemetry_name": "acceleration_z",
                "data_parse_type": "int16",
                "data_offset": 10,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              }
            ]
          }
        }
      }
    }
    ```

## <a name="download-iot-plug-and-play-bridge"></a>IoT 플러그 앤 플레이 브리지 다운로드

[IoT 플러그 앤 플레이 브리지 릴리스](https://github.com/Azure/iot-plug-and-play-bridge/releases) 에서 지원되는 어댑터를 사용해 미리 빌드된 버전의 브리지를 다운로드하고 최신 릴리스에 대한 자산 목록을 확장할 수 있습니다. 사용 중인 운영 체제에 맞는 최신 버전의 응용 프로그램을 다운로드합니다.

또한 [GitHub에서 IoT 플러그 앤 플레이 브리지](https://github.com/Azure/iot-plug-and-play-bridge)의 소스 코드를 다운로드하고 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 IoT 플러그 앤 플레이 브리지의 아키텍처에 대해 간략히 살펴보았으므로, 자세한 내용을 알아보려면 다음 단계를 진행합니다.

- [Linux 또는 Windows에서 실행되는 IoT 플러그 앤 플레이 브리지 샘플을 IoT Hub에 연결하는 방법](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/quick_start.md)
- [IoT 플러그 앤 플레이 브리지 빌드 및 배포](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/build_deploy.md)
- [IoT 플러그 앤 플레이 브리지 확장](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/author_adapter.md)
- [GitHub의 IoT 플러그 앤 플레이 브리지](https://github.com/Azure/iot-plug-and-play-bridge)
