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
ms.date: 06/09/2017
ms.author: andbuc
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: e7eb2931993daf3f0aecbd4a43d27ebd5adc10b0
ms.contentlocale: ko-kr
ms.lasthandoff: 06/17/2017


---

# <a name="use-azure-iot-edge-to-send-device-to-cloud-messages-with-a-simulated-device-windows"></a>Azure IoT Edge를 사용하여 시뮬레이션된 장치(Windows)에서 장치-클라우드 메시지 보내기

[!INCLUDE [iot-hub-iot-edge-simulated-selector](../../includes/iot-hub-iot-edge-simulated-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-windows](../../includes/iot-hub-iot-edge-install-build-windows.md)]

## <a name="how-to-run-the-sample"></a>샘플을 실행하는 방법

**build.cmd** 스크립트는 **iot-edge** 리포지토리의 로컬 복사본에 있는 **build** 폴더에 해당 출력을 생성합니다. 이 출력에는 이 샘플에서 사용된 네 개의 IoT Edge 모듈이 포함됩니다.

빌드 스크립트는 다음을 배치합니다.

* **build\\modules\\logger\\Debug** 폴더에 **logger.dll**.
* **build\\modules\\iothub\\Debug** 폴더에 **iothub.dll**.
* **build\\modules\\identitymap\\Debug** 폴더에 **identity\_map.dll**.
* **build\\modules\\simulated\_device\\Debug** 폴더에 **simulated\_device.dll**.

다음 JSON 설정 파일에 표시된 대로 **module path** 값에 이러한 경로를 사용합니다.

simulated\_device\_cloud\_upload\_sample 프로세스는 JSON 구성 파일에 대한 경로를 명령줄 인수 형태로 사용합니다. 다음 예제 JSON 파일은 **samples\\simulated\_device\_cloud\_upload\_sample\\src\\simulated\_device\_cloud\_upload\_sample\_win.json**의 SDK 리포지토리에서 제공됩니다. 이 구성 파일은 빌드 스크립트를 수정하여 IoT Edge 모듈이나 샘플 실행 파일을 기본 위치가 아닌 위치에 배치한 경우 외에는 그대로 작동합니다.

> [!NOTE]
> 모듈 경로는 simulated\_device\_cloud\_upload\_sample.exe가 있는 디렉터리에 대한 상대 경로입니다. 샘플 JSON 구성 파일은 현재 작업 디렉터리에서 작성 중인 ‘deviceCloudUploadGatewaylog.log’를 기본값으로 사용합니다.

텍스트 편집기에서 **iot-edge** 리포지토리의 로컬 복사본에 있는 **samples\\simulated\_device\_cloud\_upload\_sample\\src\\simulated\_device\_cloud\_upload\_win.json** 파일을 엽니다. 이 파일은 샘플 게이트웨이에서 IoT Edge 모듈을 구성합니다.

* **IoTHub** 모듈이 IoT Hub에 연결됩니다. IoT Hub에 데이터를 보내도록 이를 구성합니다. 특히 **IoTHubName** 값을 IoT Hub의 이름으로 설정하고, **IoTHubSuffix** 값을 **azure-devices.net**으로 설정합니다. **HTTP**, **AMQP** 또는 **MQTT** 중 하나에 **Transport** 값을 설정합니다. 현재 **HTTP**만 모든 장치 메시지에 대한 하나의 TCP 연결을 공유합니다. 값을 **AMQP** 또는 **MQTT**로 설정하는 경우 게이트웨이는 각 장치에 대해 IoT Hub에 대한 별도의 TCP 연결을 유지합니다.
* **mapping** 모듈은 시뮬레이션된 장치의 MAC 주소를 IoT Hub 장치 ID에 매핑합니다. **deviceId** 값이 IoT Hub에 추가한 두 장치의 ID와 일치하는지, 그리고 **deviceKey** 값에 두 장치의 키가 포함되어 있는지 확인합니다.
* **BLE1** 및 **BLE2** 모듈은 시뮬레이션된 장치입니다. 해당 모듈 MAC 주소가 **mapping** 모듈의 주소와 어떻게 일치되는지 확인합니다.
* **Logger** 모듈은 게이트웨이 활동을 파일에 로깅합니다.
* 다음 예제에 나온 **module path** 값은 simulated\_device\_cloud\_upload\_sample.exe가 있는 디렉터리에 대한 상대 경로입니다.
* JSON 파일의 맨 아래에 있는 **링크** 배열은 **BLE1** 및 **BLE2** 모듈을 **매핑** 모듈에 연결하고 **매핑** 모듈을 **IoTHub** 모듈에 연결합니다. 또한 **로거** 모듈이 모든 메시지를 기록하는지 확인합니다.

```json
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

1. 명령 프롬프트에서 **iot-edge** 리포지토리의 로컬 복사본에 있는 **build** 폴더로 이동합니다.
2. 다음 명령 실행:
   
    ```cmd
    samples\simulated_device_cloud_upload\Debug\simulated_device_cloud_upload_sample.exe ..\samples\simulated_device_cloud_upload\src\simulated_device_cloud_upload_win.json
    ```
3. [장치탐색기][lnk-device-explorer] 또는 [iothub-explorer][lnk-iothub-explorer] 도구를 사용하여 IoT Hub가 게이트웨이에서 수신하는 메시지를 모니터링할 수 있습니다. 예를 들어 iothub-explorer를 사용하면 다음 명령으로 장치-클라우드 메시지를 모니터링할 수 있습니다.

    ```cmd
    iothub-explorer monitor-events --login "HostName={Your iot hub name}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={Your IoT Hub key}"
    ```

## <a name="next-steps"></a>다음 단계

IoT Edge와 코드 예제 실험에 대해 더욱 심도 있게 이해하려면 다음 개발자 자습서 및 리소스를 참고하세요.

* [IoT Edge를 사용하여 물리적 장치에서 장치-클라우드 메시지 보내기][lnk-physical-device]
* [Azure IoT Edge][lnk-iot-edge]

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [IoT Hub 개발자 가이드][lnk-devguide]
* [처음부터 IoT 솔루션 보안 유지][lnk-securing]

<!-- Links -->
[lnk-iot-edge]: https://github.com/Azure/iot-edge/
[lnk-physical-device]: iot-hub-iot-edge-physical-device.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer
[lnk-iothub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md
