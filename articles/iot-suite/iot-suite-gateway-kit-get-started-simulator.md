---
title: "Intel NUC를 사용하여 Azure IoT Suite에 게이트웨이 연결 | Microsoft Docs"
description: "미리 구성된 원격 모니터링 솔루션 및 Microsoft IoT 상용 게이트웨이 키트를 사용합니다. Azure IoT Edge 게이트웨이를 사용하여 원격 모니터링 솔루션에 연결하고, 시뮬레이션된 원격 분석을 클라우드로 전송하고, 솔루션 대시보드에서 호출된 메서드에 응답합니다."
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
ms.date: 07/25/2017
ms.author: dobett
ms.openlocfilehash: 9ed57d3c23e2adbd42c054f33c8ed46e3d6c9792
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="connect-your-azure-iot-edge-gateway-to-the-remote-monitoring-preconfigured-solution-and-send-simulated-telemetry"></a>미리 구성된 원격 모니터링 솔루션에 Azure IoT Edge 게이트웨이 연결 및 시뮬레이션된 원격 분석 전송

[!INCLUDE [iot-suite-gateway-kit-selector](../../includes/iot-suite-gateway-kit-selector.md)]

이 자습서에서는 Azure IoT Edge를 사용하여 미리 구성된 원격 모니터링 솔루션으로 전송할 온도 및 습도 데이터를 시뮬레이션하는 방법을 보여 줍니다. 이 자습서에서는 다음을 사용하여 작업을 수행합니다.

- 샘플 게이트웨이를 구현하는 Azure IoT Edge
- 클라우드 기반 백 엔드로 미리 구성된 IoT Suite 원격 모니터링 솔루션

## <a name="overview"></a>개요

이 자습서에서는 다음 단계를 완료합니다.

- Azure 구독에서 미리 구성된 원격 모니터링 솔루션의 인스턴스를 배포합니다. 이 단계에서는 여러 Azure 서비스를 자동으로 배포하고 구성합니다.
- 사용자 컴퓨터 및 원격 모니터링 솔루션과 통신하도록 Intel NUC 게이트웨이 장치를 설정합니다.
- 솔루션 대시보드에서 볼 수 있는 시뮬레이션된 원격 분석을 보내도록 IoT Edge 게이트웨이를 구성합니다.

[!INCLUDE [iot-suite-gateway-kit-prerequisites](../../includes/iot-suite-gateway-kit-prerequisites.md)]

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

> [!WARNING]
> 원격 모니터링 솔루션은 Azure 구독에서 Azure 서비스 집합을 프로비전합니다. 배포는 실제 엔터프라이즈 아키텍처를 반영합니다. 불필요한 Azure 사용료가 부과되지 않도록 하려면 배포가 완료된 후 azureiotsuite.com에서 미리 구성된 솔루션 인스턴스를 삭제합니다. 미리 구성된 솔루션이 다시 필요하면 쉽게 다시 만들 수 있습니다. 원격 모니터링 솔루션이 실행되는 동안 소비를 감소하는 방법에 대한 자세한 내용은 [데모 목적으로 미리 구성된 Azure IoT Suite 솔루션 구성][lnk-demo-config]을 참조하세요.

[!INCLUDE [iot-suite-gateway-kit-view-solution](../../includes/iot-suite-gateway-kit-view-solution.md)]

**device02**와 같은 장치 ID를 사용하여 두 번째 장치를 추가하기 위해 이전 단계를 반복합니다. 샘플은 게이트웨이의 시뮬레이션된 두 개의 장치에서 원격 모니터링 솔루션으로 데이터를 보냅니다.

[!INCLUDE [iot-suite-gateway-kit-prepare-nuc-connectivity](../../includes/iot-suite-gateway-kit-prepare-nuc-connectivity.md)]

[!INCLUDE [iot-suite-gateway-kit-prepare-nuc-software](../../includes/iot-suite-gateway-kit-prepare-nuc-software.md)]

## <a name="build-the-custom-iot-edge-module"></a>사용자 지정 IoT Edge 모듈 빌드

게이트웨이가 원격 모니터링 솔루션에 메시지를 보낼 수 있도록 사용자 지정 IoT Edge 모듈을 작성할 수 있습니다. 게이트웨이 및 IoT Edge 모듈을 구성하는 방법에 대한 자세한 내용은 [Azure IoT Edge 개념][lnk-gateway-concepts]을 참조하세요.

다음 명령을 사용하여 GitHub에서 사용자 지정 IoT Edge 모듈의 소스 코드를 다운로드합니다.

```bash
cd ~
git clone https://github.com/Azure-Samples/iot-remote-monitoring-c-intel-nuc-gateway-getting-started.git
```

다음 명령을 사용하여 사용자 지정 IoT Edge 모듈을 빌드합니다.

```bash
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/simulator
chmod u+x build.sh
sed -i -e 's/\r$//' build.sh
./build.sh
```

빌드 스크립트는 빌드 폴더에 사용자 지정 IoT Edge 모듈인 libsimulator.so를 배치합니다.

## <a name="configure-and-run-the-iot-edge-gateway"></a>IoT Edge 게이트웨이 구성 및 실행

이제 원격 모니터링 대시보드에 시뮬레이션된 원격 분석을 보내도록 IoT Edge 게이트웨이를 구성할 수 있습니다. 게이트웨이 및 IoT Edge 모듈을 구성하는 방법에 대한 자세한 내용은 [Azure IoT Edge 개념][lnk-gateway-concepts]을 참조하세요.

> [!TIP]
> 이 자습서에서는 Intel NUC에서 표준 `vi` 텍스트 편집기를 사용합니다. 이전에 `vi`를 사용하지 않은 경우 [Unix - vi 편집기 자습서][lnk-vi-tutorial]와 같은 입문용 자습서를 완료하여 이 편집기에 익숙해져야 합니다. 또는 `smart install nano -y` 명령을 사용하여 보다 친숙한 [nano](https://www.nano-editor.org/) 편집기를 설치할 수도 있습니다.

다음 명령을 사용하여 **vi** 편집기에서 샘플 구성 파일을 엽니다.

```bash
vi ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/simulator/remote_monitoring.json
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
    "macAddress": "AA:BB:CC:DD:EE:FF",
    "deviceId": "<<Azure IoT Hub Device ID>>",
    "deviceKey": "<<Azure IoT Hub Device Key>>"
  },
  {
    "macAddress": "AA:BB:CC:DD:EE:FF",
    "deviceId": "<<Azure IoT Hub Device ID>>",
    "deviceKey": "<<Azure IoT Hub Device Key>>"
  }
]
```

**deviceID** 및 **deviceKey** 자리 표시자를 원격 모니터링 솔루션에서 이전에 만든 두 개의 장치에 대한 ID 및 키로 바꿉니다.

변경 내용을 저장합니다.

이제 다음 명령을 사용하여 IoT Edge 게이트웨이를 실행할 수 있습니다.

```bash
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/simulator
/usr/share/azureiotgatewaysdk/samples/simulated_device_cloud_upload/simulated_device_cloud_upload remote_monitoring.json
```

게이트웨이는 Intel NUC에서 시작하고 원격 모니터링 솔루션에 시뮬레이션된 원격 분석을 보냅니다.

![IoT Edge 게이트웨이는 시뮬레이션된 원격 분석을 생성합니다.][img-simulated telemetry]

**Ctrl+C**를 눌러 언제든지 프로그램을 종료합니다.

## <a name="view-the-telemetry"></a>원격 분석 보기

이제 IoT Edge 게이트웨이는 시뮬레이션된 원격 분석을 원격 모니터링 솔루션으로 전송합니다. 솔루션 대시보드에서 원격 분석을 볼 수 있습니다.

- 솔루션 대시보드로 이동합니다.
- **장치 보기** 드롭다운에서 게이트웨이에 구성한 두 개의 장치 중 하나를 선택합니다.
- 게이트웨이 장치의 원격 분석은 대시보드에 표시됩니다.

![시뮬레이션된 게이트웨이 장치에서 원격 분석 표시][img-telemetry-display]

> [!WARNING]
> Azure 계정에서 원격 모니터링 솔루션을 실행하는 경우 실행하는 동안 요금이 청구됩니다. 원격 모니터링 솔루션이 실행되는 동안 소비를 감소하는 방법에 대한 자세한 내용은 [데모 목적으로 미리 구성된 Azure IoT Suite 솔루션 구성][lnk-demo-config]을 참조하세요. 사용을 마친 경우 Azure 계정에서 미리 구성된 솔루션을 삭제합니다.

## <a name="next-steps"></a>다음 단계

Azure IoT에 대한 추가 샘플 및 설명서를 보려면 [Azure IoT 개발자 센터](https://azure.microsoft.com/develop/iot/)를 방문하세요.

[img-simulated telemetry]: ./media/iot-suite-gateway-kit-get-started-simulator/appoutput.png

[img-telemetry-display]: ./media/iot-suite-gateway-kit-get-started-simulator/telemetry.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md

[lnk-vi-tutorial]: http://www.tutorialspoint.com/unix/unix-vi-editor.htm
[lnk-gateway-concepts]: https://docs.microsoft.com/azure/iot-hub/iot-hub-linux-iot-edge-get-started