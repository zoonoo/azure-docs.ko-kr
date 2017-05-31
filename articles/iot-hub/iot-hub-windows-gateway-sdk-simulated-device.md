---
title: "Azure IoT Edge를 사용하여 장치 시뮬레이션(Windows) | Microsoft Docs"
description: "Windows에서 Azure IoT Edge를 사용하여 Azure IoT Edge 게이트웨이를 통해 원격 분석을 IoT Hub에 전송하는 시뮬레이션된 장치를 만드는 방법입니다."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 6a2aeda0-696a-4732-90e1-595d2e2fadc6
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/29/2017
ms.author: andbuc
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 0ea8483e19ddec447642a33c24c9ecdd9937ea11
ms.contentlocale: ko-kr
ms.lasthandoff: 05/16/2017


---
# <a name="use-azure-iot-edge-to-send-device-to-cloud-messages-with-a-simulated-device-windows"></a>Azure IoT Edge를 사용하여 시뮬레이션된 장치(Windows)에서 장치-클라우드 메시지 보내기
[!INCLUDE [iot-hub-gateway-sdk-simulated-selector](../../includes/iot-hub-gateway-sdk-simulated-selector.md)]

## <a name="build-and-run-the-sample"></a>샘플 빌드 및 실행
시작하기 전에 다음을 수행해야 합니다.

* Windows에서 SDK를 사용할 수 있도록 [개발 환경을 설정][lnk-setupdevbox]합니다.
* Azure 구독에서 [IoT Hub를 만듭니다][lnk-create-hub]. 이 연습을 완료하려면 허브 이름이 필요합니다. 계정이 없는 경우 몇 분 내에 [계정][lnk-free-trial]을 만들 수 있습니다.
* IoT Hub에 장치 두 개를 추가하고 해당 ID와 장치 키를 적어 둡니다. [장치 탐색기][lnk-device-explorer] 또는 [iothub-explorer][lnk-iothub-explorer] 도구를 사용하여 이전 단계에서 만든 IoT Hub에 장치를 추가하고 해당 키를 검색할 수 있습니다.

샘플을 빌드하려면

1. **VS 2015용 개발자 명령 프롬프트** 또는 **VS 2017용 개발자 명령 프롬프트** 명령 프롬프트를 엽니다.
2. **iot-edge** 리포지토리의 로컬 복사본에 있는 루트 폴더로 이동합니다.
3. **tools\\build.cmd** 스크립트를 실행합니다. 이 스크립트는 Visual Studio 솔루션 파일을 만들고 솔루션을 빌드합니다. Visual Studio 솔루션은 **iot-edge** 리포지토리의 로컬 복사본에 있는 **build** 폴더에서 찾을 수 있습니다. 스크립트에 추가 매개 변수를 제공하여 단위 테스트 및 종단간 테스트를 빌드하여 실행할 수 있습니다. 이러한 매개 변수는 각각 **--run-unittests** 및 **--run-e2e-tests**입니다.

샘플을 실행하려면

텍스트 편집기에서 **iot-edge** 리포지토리의 로컬 복사본에 있는 **samples\\simulated_device_cloud_upload\\src\\simulated_device_cloud_upload_win.json** 파일을 엽니다. 이 파일은 샘플 게이트웨이에서 IoT Edge 모듈을 구성합니다.

* **IoTHub** 모듈이 IoT Hub에 연결됩니다. IoT Hub에 데이터를 보내도록 이를 구성합니다. 특히 **IoTHubName** 값을 IoT Hub의 이름으로 설정하고, **IoTHubSuffix** 값을 **azure-devices.net**으로 설정합니다. "HTTP", "AMQP" 또는 "MQTT" 중 하나에 **Transport** 값을 설정합니다. 현재 "HTTP" 만이 모든 장치 메시지에 대한 하나의 TCP 연결을 공유합니다. 값을 "AMQP" 또는 "MQTT"로 설정하는 경우 게이트웨이는 각 장치에 대해 IoT Hub에 대한 별도의 TCP 연결을 유지합니다.
* **mapping** 모듈은 시뮬레이션된 장치의 MAC 주소를 IoT Hub 장치 ID에 매핑합니다. **deviceId** 값이 IoT Hub에 추가한 두 장치의 ID와 일치하는지, 그리고 **deviceKey** 값에 두 장치의 키가 포함되어 있는지 확인합니다.
* **BLE1** 및 **BLE2** 모듈은 시뮬레이션된 장치입니다. 해당 모듈 MAC 주소가 **mapping** 모듈의 주소와 어떻게 일치되는지 확인합니다.
* **Logger** 모듈은 게이트웨이 활동을 파일에 로깅합니다.
* 다음 예제에 표시된 **module path** 값은 IoT Edge 리포지토리를 **C:** 드라이브의 루트에 복제한 것으로 가정합니다. 다른 위치에 다운로드한 경우 해당 위치에 맞게 **module path** 값을 조정해야 합니다.
* JSON 파일의 맨 아래에 있는 **링크** 배열은 **BLE1** 및 **BLE2** 모듈을 **매핑** 모듈에 연결하고 **매핑** 모듈을 **IoTHub** 모듈에 연결합니다. 또한 **로거** 모듈이 모든 메시지를 기록하는지 확인합니다.

```
{
    "modules" :
    [
      {
        "name": "IotHub",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\iothub\\Debug\\iothub.dll"
          }
          },
          "args": {
            "IoTHubName": "<<insert here IoTHubName>>",
            "IoTHubSuffix": "<<insert here IoTHubSuffix>>",
            "Transport": "HTTP"
          }
        },
      {
        "name": "mapping",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\identitymap\\Debug\\identity_map.dll"
          }
          },
          "args": [
            {
              "macAddress": "01:01:01:01:01:01",
              "deviceId": "<<insert here deviceId>>",
              "deviceKey": "<<insert here deviceKey>>"
            },
            {
              "macAddress": "02:02:02:02:02:02",
              "deviceId": "<<insert here deviceId>>",
              "deviceKey": "<<insert here deviceKey>>"
            }
          ]
        },
      {
        "name": "BLE1",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\simulated_device\\Debug\\simulated_device.dll"
          }
          },
          "args": {
            "macAddress": "01:01:01:01:01:01"
          }
        },
      {
        "name": "BLE2",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\simulated_device\\Debug\\simulated_device.dll"
          }
          },
          "args": {
            "macAddress": "02:02:02:02:02:02"
          }
        },
      {
        "name": "Logger",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\logger\\Debug\\logger.dll"
          }
        },
        "args": {
          "filename": "deviceCloudUploadGatewaylog.log"
        }
      }
    ],
    "links" : [
        { "source" : "*", "sink" : "Logger" },
        { "source" : "BLE1", "sink" : "mapping" },
        { "source" : "BLE2", "sink" : "mapping" },
        { "source" : "mapping", "sink" : "IotHub" }
    ]
}
```

구성 파일에 적용한 변경 내용을 저장합니다.

샘플을 실행하려면

1. 명령 프롬프트에서 **iot-edge** 리포지토리의 로컬 복사본에 있는 루트 폴더로 이동합니다.
2. 다음 명령을 실행합니다.
   
    ```
    build\samples\simulated_device_cloud_upload\Debug\simulated_device_cloud_upload_sample.exe samples\simulated_device_cloud_upload\src\simulated_device_cloud_upload_win.json
    ```
3. [장치탐색기][lnk-device-explorer] 또는 [iothub-explorer][lnk-iothub-explorer] 도구를 사용하여 IoT Hub가 게이트웨이에서 수신하는 메시지를 모니터링할 수 있습니다.

## <a name="next-steps"></a>다음 단계
IoT Edge와 코드 예제 실험에 대해 더욱 심도 있게 이해하고 싶다면 다음 개발자 자습서 및 리소스를 참고하세요.

* [IoT Edge를 사용하여 물리적 장치에서 장치-클라우드 메시지 보내기][lnk-physical-device]
* [Azure IoT Edge][lnk-gateway-sdk]

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [IoT Hub 개발자 가이드][lnk-devguide]
* [처음부터 IoT 솔루션 보안 유지][lnk-securing]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/iot-edge/blob/master/doc/devbox_setup.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer
[lnk-iothub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md
[lnk-gateway-sdk]: https://github.com/Azure/iot-edge/

[lnk-physical-device]: iot-hub-gateway-sdk-physical-device.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md

