---
title: IoT 플러그 앤 플레이 브리지 | Microsoft Docs
description: IoT 플러그 앤 플레이 브리지 및이 브리지를 사용 하 여 Windows 또는 Linux 게이트웨이에 연결 된 기존 장치를 IoT 플러그 앤 플레이 장치로 연결 하는 방법에 대해 알아봅니다.
author: usivagna
ms.author: ugans
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 34af380d057ad47811e394da1e7a29198e102920
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672783"
---
# <a name="iot-plug-and-play-bridge"></a>IoT 플러그 앤 플레이 브리지

IoT 플러그 앤 플레이 브리지는 Windows 또는 Linux 게이트웨이에 연결 된 기존 장치를 IoT 플러그 앤 플레이 장치로 연결 하는 오픈 소스 응용 프로그램입니다. Windows 또는 Linux 컴퓨터에서 응용 프로그램을 설치 하 고 구성한 후에는이를 사용 하 여 연결 된 장치를 IoT hub에 연결할 수 있습니다. 브리지를 사용 하 여 연결 된 장치에서 전송 하는 원격 분석에 IoT 플러그 앤 플레이 인터페이스를 매핑하고, 장치 속성을 작업 하 고, 명령을 호출할 수 있습니다.

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-high-level.png" alt-text="왼쪽에는 IoT 플러그 앤 플레이 브리지가 포함 된 Windows 또는 Linux PC에 몇 가지 기존 센서 (유선 및 무선 모두)가 연결 되어 있습니다. IoT 플러그 앤 플레이 브리지는 오른쪽에 있는 IoT hub에 연결 합니다.":::

IoT 플러그 앤 플레이 브리지는 Windows 10 또는 Linux를 실행 하는 모든 IoT 장치, 산업용 PC, 서버 또는 게이트웨이에서 독립 실행형 실행 파일로 배포할 수 있습니다. 응용 프로그램 코드로 컴파일될 수도 있습니다. 간단한 구성 JSON 파일은 Azure에 노출 해야 하는 연결 된 장치/주변 장치를 IoT 플러그 앤 플레이 브리지에 알려 줍니다.

## <a name="supported-protocols-and-sensors"></a>지원 되는 프로토콜 및 센서

IoT 플러그 앤 플레이 브리지는 어댑터 설명서에 대 한 링크를 사용 하 여 기본적으로 다음과 같은 유형의 주변 장치를 지원 합니다.

|파악|Windows|Linux|
|---------|---------|---------|
|[Bluetooth 센서 어댑터가](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/bluetooth_sensor_adapter.md) 검색 된 Bluetooth 저 에너지 (여) 사용 센서를 연결 합니다.       |예|예|
|[카메라 어댑터](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/camera_adapter.md) 는 Windows 10 장치에서 카메라를 연결 합니다.               |예|예|
|[Modbus 어댑터](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/modbus_adapters.md) 는 modbus 장치에서 센서를 연결 합니다.              |예|예|
|[Mqtt 어댑터](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/mqtt_adapter.md) 는 mqtt broker를 사용 하는 장치를 연결 합니다.                  |예|예|
|[SerialPnP 어댑터](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/serialpnp/Readme.md) 는 직렬 연결을 통해 통신 하는 장치를 연결 합니다.               |예|예|
|[WINDOWS USB 주변](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/coredevicehealth_adapter.md) 장치는 어댑터 지원 장치 인터페이스 클래스 목록을 사용 하 여 특정 하드웨어 ID가 있는 장치를 연결 합니다.  |예|해당 사항 없음|

IoT 플러그 앤 플레이 브리지를 확장 하 여 추가 장치 프로토콜을 지 원하는 방법을 알아보려면 [iot 플러그 앤 플레이 브리지 빌드, 배포 및 확장](howto-build-deploy-extend-pnp-bridge.md)을 참조 하세요.

## <a name="iot-plug-and-play-bridge-architecture"></a>IoT 플러그 앤 플레이 브리지 아키텍처

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-components.png" alt-text="왼쪽에는 IoT 플러그 앤 플레이 브리지가 포함 된 Windows 또는 Linux PC에 연결 된 다양 한 주변 장치를 나타내는 몇 가지 상자가 있습니다. 위쪽부터 구성 레이블이 지정 된 상자가 브리지를 향합니다. 그런 다음 브리지는 다이어그램의 오른쪽에 있는 IoT hub에 연결 합니다.":::

### <a name="iot-plug-and-play-bridge-adapters"></a>IoT 플러그 앤 플레이 브리지 어댑터

IoT 플러그 앤 플레이 브리지는 다양 한 형식의 장치에 대 한 IoT 플러그 앤 플레이 브리지 어댑터 집합을 지원 합니다. *어댑터 매니페스트* 는 브리지에 어댑터를 정적으로 정의 합니다.

브리지 어댑터 관리자는 매니페스트를 사용 하 여 어댑터 함수를 식별 하 고 호출 합니다. 어댑터 관리자는 구성 파일에 나열 된 인터페이스 구성 요소에 필요한 브리지 어댑터 에서만 create 함수를 호출 합니다. 어댑터 인스턴스는 각 IoT 플러그 앤 플레이 구성 요소에 대해 만들어집니다.

브리지 어댑터가 디지털 쌍 인터페이스 핸들을 만들고 가져옵니다. 어댑터는이 핸들을 사용 하 여 장치 기능을 디지털 쌍에 바인딩합니다.

브리지 어댑터는 구성 파일의 정보를 사용 하 여 다음 기술을 사용 하 여 브리지를 통해 전체 장치에서 디지털 쌍 통신을 사용 하도록 설정 합니다.

- 통신 채널을 직접 설정 합니다.
- 통신 채널을 사용할 수 있을 때까지 대기 하는 장치 감시자를 만듭니다.

### <a name="configuration-file"></a>구성 파일

IoT 플러그 앤 플레이 브리지는 다음을 지정 하는 JSON 기반 구성 파일을 사용 합니다.

- IoT hub 또는 IoT Central 응용 프로그램에 연결 하는 방법: 옵션에는 연결 문자열, 인증 매개 변수 또는 DPS (장치 프로 비전 서비스)가 포함 됩니다.
- Bridge에서 사용 하는 IoT 플러그 앤 플레이 기능 모델의 위치입니다. 모델은 IoT 플러그 앤 플레이 장치의 기능을 정의 하며, 정적 이며 변경할 수 없습니다.
- IoT 플러그 앤 플레이 인터페이스 구성 요소 목록과 각 구성 요소에 대 한 다음 정보입니다.
- 인터페이스 ID 및 구성 요소 이름입니다.
- 브리지 어댑터는 구성 요소와 상호 작용 하는 데 필요 합니다.
- 브리지 어댑터가 장치와의 통신을 설정 하는 데 필요한 장치 정보입니다. 예를 들면 하드웨어 ID 또는 어댑터, 인터페이스 또는 프로토콜에 대 한 특정 정보가 있습니다.
- 어댑터가 비슷한 장치를 사용 하는 여러 통신 유형을 지 원하는 경우에는 선택적 브리지 어댑터 하위 유형 또는 인터페이스 구성입니다. 이 예제에서는 bluetooth 센서 구성 요소를 구성 하는 방법을 보여 줍니다.

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

- 전역 브리지 어댑터 매개 변수의 선택적 목록입니다. 예를 들어 bluetooth 센서 브리지 어댑터에는 지원 되는 구성 사전이 있습니다. Bluetooth 센서 어댑터가 필요한 인터페이스 구성 요소는 이러한 구성 중 하나를 다음과 같이 선택할 수 있습니다 `blesensor_identity` .

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

[IoT 플러그 앤 플레이 브리지 릴리스](https://github.com/Azure/iot-plug-and-play-bridge/releases) 에서 지원 되는 어댑터를 사용 하 여 브리지의 미리 작성 된 버전을 다운로드 하 고 최신 릴리스에 대 한 자산 목록을 확장할 수 있습니다. 운영 체제용 응용 프로그램의 최신 버전을 다운로드 합니다.

또한 [GitHub에서 IoT 플러그 앤 플레이 bridge](https://github.com/Azure/iot-plug-and-play-bridge)의 소스 코드를 다운로드 하 고 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

IoT 플러그 앤 플레이 브리지의 아키텍처에 대 한 개요를 만들었으므로 다음 단계는에 대 한 자세한 내용입니다.

- [IoT 플러그 앤 플레이 브리지를 사용 하는 방법](./howto-use-iot-pnp-bridge.md)
- [IoT 플러그 앤 플레이 브리지를 빌드, 배포 및 확장](howto-build-deploy-extend-pnp-bridge.md)
- [GitHub의 IoT 플러그 앤 플레이 브리지](https://github.com/Azure/iot-plug-and-play-bridge)
