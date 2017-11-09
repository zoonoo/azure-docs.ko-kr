---
title: "Intel NUC를 사용하여 Azure IoT Suite에 게이트웨이 연결 | Microsoft Docs"
description: "미리 구성된 원격 모니터링 솔루션 및 Microsoft IoT 상용 게이트웨이 키트를 사용합니다. Azure IoT Edge 게이트웨이를 사용하여 SensorTag 장치를 원격 모니터링 솔루션에 연결하고, 원격 분석을 클라우드로 전송하고, 솔루션 대시보드에서 호출된 메서드에 응답합니다."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 85b9ed0000c8c5ff2c7d6cc8fa4a051e0b27d6c2
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2017
---
# <a name="connect-your-azure-iot-edge-gateway-to-the-remote-monitoring-preconfigured-solution-and-send-telemetry-from-a-sensortag"></a>미리 구성된 원격 모니터링 솔루션에 Azure IoT Edge 게이트웨이 연결 및 SensorTag의 원격 분석 전송

[!INCLUDE [iot-suite-v1-gateway-kit-selector](../../includes/iot-suite-v1-gateway-kit-selector.md)]

이 자습서에서는 Azure IoT Edge를 사용하여 SensorTag 장치에서 미리 구성된 원격 모니터링 솔루션으로 온도 및 습도 데이터를 보내는 방법을 보여줍니다. SensorTag은 Bluetooth를 사용하여 Intel NUC 게이트웨이에 연결합니다. 이 자습서에서는 다음을 사용하여 작업을 수행합니다.

- 샘플 게이트웨이를 구현하는 Azure IoT Edge
- 클라우드 기반 백 엔드로 미리 구성된 IoT Suite 원격 모니터링 솔루션

## <a name="overview"></a>개요

이 자습서에서는 다음 단계를 완료합니다.

- Azure 구독에서 미리 구성된 원격 모니터링 솔루션의 인스턴스를 배포합니다. 이 단계에서는 여러 Azure 서비스를 자동으로 배포하고 구성합니다.
- 사용자 컴퓨터 및 원격 모니터링 솔루션과 통신하도록 Intel NUC 게이트웨이 장치를 설정합니다.
- SensorTag 장치에서 원격 분석을 수신하고 원격 모니터링 대시보드에 보내도록 Intel NUC 게이트웨이를 설정합니다.

[!INCLUDE [iot-suite-v1-gateway-kit-prerequisites](../../includes/iot-suite-v1-gateway-kit-prerequisites.md)]

[Texas Instruments BLE SensorTag][lnk-sensortag] 이 자습서는 SensorTag 장치에서 Bluetooth를 통한 원격 분석 데이터를 검색합니다.

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> 원격 모니터링 솔루션은 Azure 구독에서 Azure 서비스 집합을 프로비전합니다. 배포는 실제 엔터프라이즈 아키텍처를 반영합니다. 불필요한 Azure 사용료가 부과되지 않도록 하려면 배포가 완료된 후 azureiotsuite.com에서 미리 구성된 솔루션 인스턴스를 삭제합니다. 미리 구성된 솔루션이 다시 필요하면 쉽게 다시 만들 수 있습니다. 원격 모니터링 솔루션이 실행되는 동안 소비를 감소하는 방법에 대한 자세한 내용은 [데모 목적으로 미리 구성된 Azure IoT Suite 솔루션 구성][lnk-demo-config]을 참조하세요.

[!INCLUDE [iot-suite-v1-gateway-kit-view-solution](../../includes/iot-suite-v1-gateway-kit-view-solution.md)]

[!INCLUDE [iot-suite-v1-gateway-kit-prepare-nuc-connectivity](../../includes/iot-suite-v1-gateway-kit-prepare-nuc-connectivity.md)]

## <a name="configure-bluetooth-connectivity"></a>Bluetooth 연결 구성

원격 분석을 연결하고 보내기 위해 SensorTag 장치를 사용하도록 Intel NUC에서 Bluetooth를 구성합니다.

### <a name="find-the-mac-address-of-the-sensortag"></a>SensorTag의 MAC 주소 찾기

1. Intel NUC의 셸에서 다음 명령을 실행하여 Bluetooth 서비스의 차단을 해제합니다.

    ```bash
    sudo rfkill unblock bluetooth
    ```

1. 다음 명령을 실행하여 Intel NUC에서 Bluetooth 서비스를 시작하고 Bluetooth 셸을 입력합니다.

    ```bash
    sudo systemctl start bluetooth
    bluetoothctl
    ```

1. 다음 명령을 실행하여 Bluetooth 컨트롤러의 전원을 켭니다.

    ```bash
    power on
    ```

    컨트롤러가 켜지면 **전원 변경 성공**이라는 메시지가 표시됩니다.

1. 다음 명령을 실행하여 주변의 Bluetooth 장치를 스캔합니다.

    ```bash
    scan on
    ```

1. 검색할 수 있도록 SensorTag에서 전원 단추를 누릅니다. 녹색 LED가 깜박입니다.

1. 컨트롤러가 SensorTag을 발견했다는 메시지가 셸에서 표시되면 장치의 MAC 주소를 기록해 둡니다. MAC 주소는 **A0:E6:F8:B5:F6:00**과 같이 표시됩니다. 게이트웨이를 구성할 때 자습서의 뒷부분에 나오는 MAC 주소가 필요합니다.

1. Bluetooth 스캔을 끄려면 다음 명령을 실행합니다.

    ```bash
    scan off
    ```

1. 다음 명령을 실행하여 SensorTag 장치에 연결할 수 있는지 확인합니다.

    ```bash
    connect <SensorTag MAC address>
    ```

    성공적으로 연결된 경우 셸에서는 **연결 성공**이라는 메시지를 표시하고 SensorTag 장치에 대한 정보를 인쇄합니다. 연결할 수 없는 경우 SensorTag가 켜져 있는지 확인합니다.

1. 이제 다음 명령을 실행하여 SensorTag와의 연결을 끊고 Bluetooth 셸을 종료할 수 있습니다.

    ```bash
    disconnect
    exit
    ```

[!INCLUDE [iot-suite-v1-gateway-kit-prepare-nuc-software](../../includes/iot-suite-v1-gateway-kit-prepare-nuc-software.md)]

## <a name="build-the-custom-iot-edge-module"></a>사용자 지정 IoT Edge 모듈 빌드

게이트웨이가 원격 모니터링 솔루션에 메시지를 보낼 수 있도록 사용자 지정 IoT Edge 모듈을 작성할 수 있습니다. 게이트웨이 및 IoT Edge 모듈을 구성하는 방법에 대한 자세한 내용은 [Azure IoT Edge 개념][lnk-gateway-concepts]을 참조하세요.

다음 명령을 사용하여 GitHub에서 사용자 지정 IoT Edge 모듈의 소스 코드를 다운로드합니다.

```bash
cd ~
git clone https://github.com/Azure-Samples/iot-remote-monitoring-c-intel-nuc-gateway-getting-started.git
```

다음 명령을 사용하여 사용자 지정 IoT Edge 모듈을 빌드합니다.

```bash
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/basic
chmod u+x build.sh
sed -i -e 's/\r$//' build.sh
./build.sh
```

빌드 스크립트는 빌드 폴더에 사용자 지정 IoT Edge 모듈인 libsensor2remotemonitoring.so를 배치합니다.

## <a name="configure-and-run-the-iot-edge-gateway"></a>IoT Edge 게이트웨이 구성 및 실행

이제 SensorTag 장치에서 원격 모니터링 대시보드로 원격 분석을 보내도록 IoT Edge 게이트웨이를 구성할 수 있습니다. 게이트웨이 및 IoT Edge 모듈을 구성하는 방법에 대한 자세한 내용은 [Azure IoT Edge 개념][lnk-gateway-concepts]을 참조하세요.

> [!TIP]
> 이 자습서에서는 Intel NUC에서 표준 `vi` 텍스트 편집기를 사용합니다. 이전에 `vi`를 사용하지 않은 경우 [Unix - vi 편집기 자습서][lnk-vi-tutorial]와 같은 입문용 자습서를 완료하여 이 편집기에 익숙해져야 합니다. 또는 `smart install nano -y` 명령을 사용하여 보다 친숙한 [nano](https://www.nano-editor.org/) 편집기를 설치할 수도 있습니다.

다음 명령을 사용하여 **vi** 편집기에서 샘플 구성 파일을 엽니다.

```bash
vi ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/basic/remote_monitoring.json
```

IoT Hub 모듈에 대한 구성에서 다음 줄을 찾습니다.

```json
"args": {
  "IoTHubName": "<<Azure IoT Hub Name>>",
  "IoTHubSuffix": "<<Azure IoT Hub Suffix>>",
  "Transport": "http"
}
```

자리 표시자 값을 이 자습서를 시작할 때 만들어 저장한 IoT Hub 정보로 바꿉니다. IoTHubName의 값은 **yourrmsolution37e08**이고 IoTSuffix의 값은 대개 **azure-devices.net**입니다.

매핑 모듈에 대한 구성에서 다음 줄을 찾습니다.

```json
args": [
  {
    "macAddress": "<<AA:BB:CC:DD:EE:FF>>",
    "deviceId": "<<Azure IoT Hub Device ID>>",
    "deviceKey": "<<Azure IoT Hub Device Key>>"
  }
]
```

**macAddress** 자리 표시자를 이전에 기록한 SensorTag의 MAC 주소로 바꿉니다. **deviceID** 및 **deviceKey** 자리 표시자를 원격 모니터링 솔루션에서 이전에 만든 두 개의 장치에 대한 ID 및 키로 바꿉니다.

SensorTag 모듈에 대한 구성에서 다음 줄을 찾습니다.

```json
"args": {
  "controller_index": 0,
  "device_mac_address": "<<AA:BB:CC:DD:EE:FF>>",
  ...
}
```

**device\_mac\_address** 자리 표시자를 이전에 기록한 SensorTag의 MAC 주소로 바꿉니다.

변경 내용을 저장합니다.

이제 다음 명령을 사용하여 게이트웨이를 실행할 수 있습니다.

```bash
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/basic
/usr/share/azureiotgatewaysdk/samples/ble_gateway/ble_gateway remote_monitoring.json
```

IoT Edge 게이트웨이는 Intel NUC에서 시작하고 SensorTag에서 원격 모니터링 솔루션으로 원격 분석을 보냅니다.

![IoT Edge 게이트웨이는 SensorTag에서 원격 분석을 전송합니다.][img-telemetry]

**Ctrl+C**를 눌러 언제든지 프로그램을 종료합니다.

## <a name="view-the-telemetry"></a>원격 분석 보기

이제 게이트웨이는 SensorTag 장치에서 원격 모니터링 솔루션으로 원격 분석을 보냅니다. 솔루션 대시보드에서 원격 분석을 볼 수 있습니다. 또한 솔루션 대시보드에서 게이트웨이를 통해 SensorTag 장치에 명령을 보낼 수 있습니다.

- 솔루션 대시보드로 이동합니다.
- **장치 보기** 드롭다운에서 SensorTag를 나타내는 게이트웨이에 구성된 장치를 선택합니다.
- SensorTag 장치의 원격 분석은 대시보드에 표시됩니다.

![SensorTag 장치의 원격 분석 표시][img-telemetry-display]

> [!WARNING]
> Azure 계정에서 원격 모니터링 솔루션을 실행하는 경우 실행하는 동안 요금이 청구됩니다. 원격 모니터링 솔루션이 실행되는 동안 소비를 감소하는 방법에 대한 자세한 내용은 [데모 목적으로 미리 구성된 Azure IoT Suite 솔루션 구성][lnk-demo-config]을 참조하세요. 사용을 마친 경우 Azure 계정에서 미리 구성된 솔루션을 삭제합니다.


## <a name="next-steps"></a>다음 단계

Azure IoT에 대한 추가 샘플 및 설명서를 보려면 [Azure IoT 개발자 센터](https://azure.microsoft.com/develop/iot/)를 방문하세요.

[img-telemetry]: ./media/iot-suite-v1-gateway-kit-get-started-sensortag/appoutput.png


[img-telemetry-display]: ./media/iot-suite-v1-gateway-kit-get-started-sensortag/telemetry.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
[lnk-vi-tutorial]: http://www.tutorialspoint.com/unix/unix-vi-editor.htm
[lnk-sensortag]: http://www.ti.com/ww/en/wireless_connectivity/sensortag/
[lnk-gateway-concepts]: https://docs.microsoft.com/azure/iot-hub/iot-hub-linux-iot-edge-get-started