---
title: "IoT Gateway SDK를 사용하여 장치 시뮬레이션 | Microsoft Docs"
description: "Azure IoT Gateway SDK를 사용하여 시뮬레이션된 장치에서 원격 분석을 보내는 방법을 보여 주는 Linux를 사용한 Azure IoT Gateway SDK 연습입니다."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 11e7bf28-ee3d-48d6-a386-eb506c7a31cf
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2016
ms.author: andbuc
translationtype: Human Translation
ms.sourcegitcommit: 00746fa67292fa6858980e364c88921d60b29460
ms.openlocfilehash: a6a97202c8221680f13bd8b29fe8d616578629cd


---
# <a name="azure-iot-gateway-sdk--send-device-to-cloud-messages-with-a-simulated-device-using-linux"></a>Azure IoT Gateway SDK – Linux를 사용하는 시뮬레이션된 장치에서 장치-클라우드 메시지 보내기
[!INCLUDE [iot-hub-gateway-sdk-simulated-selector](../../includes/iot-hub-gateway-sdk-simulated-selector.md)]

## <a name="build-and-run-the-sample"></a>샘플 빌드 및 실행
시작하기 전에 다음을 수행해야 합니다.

* Linux에서 SDK를 사용할 수 있도록 [개발 환경을 설정][lnk-setupdevbox]합니다.
* Azure 구독에서 [IoT Hub를 만듭니다][lnk-create-hub]. 이 연습을 완료하려면 허브 이름이 필요합니다. 계정이 없는 경우 몇 분 만에 [무료 계정][lnk-free-trial]을 만들 수 있습니다.
* IoT Hub에 장치 두 개를 추가하고 해당 ID와 장치 키를 적어 둡니다. [장치 탐색기 또는 iothub-explorer][lnk-explorer-tools] 도구를 사용하여 이전 단계에서 만든 IoT Hub에 장치를 추가하고 해당 키를 검색할 수 있습니다.

샘플을 빌드하려면

1. 셸을 엽니다.
2. **azure-iot-gateway-sdk** 리포지토리의 로컬 복사본에서 루트 폴더로 이동합니다.
3. **tools/build.sh** 스크립트를 실행합니다. 이 스크립트는 **cmake** 유틸리티를 사용하여 **azure-iot-gateway-sdk** 리포지토리의 로컬 복사본 루트 폴더에 **build**라는 폴더를 만들고 메이크파일을 생성합니다. 그런 다음 솔루션을 빌드하고 테스트를 실행합니다.

> [!NOTE]
> **build.sh** 스크립트를 실행할 때마다 **azure-iot-gateway-sdk** 리포지토리의 로컬 복사본 루트 폴더에서 **build** 폴더가 삭제되었다가 다시 생성됩니다.
> 
> 

샘플을 실행하려면

텍스트 편집기에서 **azure-iot-gateway-sdk** 리포지토리의 로컬 복사본에 있는 **samples/simulated_device_cloud_upload/src/simulated_device_cloud_upload_lin.json** 파일을 엽니다. 이 파일은 샘플 게이트웨이의 모듈을 구성합니다.

* **IoTHub** 모듈이 IoT Hub에 연결됩니다. IoT Hub에 데이터를 보내도록 이를 구성해야 합니다. 특히 **IoTHubName** 값을 IoT Hub의 이름으로 설정하고, **IoTHubSuffix** 값을 **azure-devices.net**으로 설정합니다. "HTTP", "AMQP" 또는 "MQTT" 중 하나에 **Transport** 값을 설정합니다. 현재 "HTTP" 만이 모든 장치 메시지에 대한 하나의 TCP 연결을 공유한다는 점에 유의하세요. 값을 "AMQP" 또는 "MQTT"로 설정하는 경우 게이트웨이는 각 장치에 대해 IoT Hub에 대한 별도의 TCP 연결을 유지합니다.
* **mapping** 모듈은 시뮬레이션된 장치의 MAC 주소를 IoT Hub 장치 ID에 매핑합니다. **deviceId** 값이 IoT Hub에 추가한 두 장치의 ID와 일치하는지, 그리고 **deviceKey** 값에 두 장치의 키가 포함되어 있는지 확인합니다.
* **BLE1** 및 **BLE2** 모듈은 시뮬레이션된 장치입니다. 해당 MAC 주소가 **mapping** 모듈의 MAC 주소와 어떻게 일치되는지 확인합니다.
* **Logger** 모듈은 게이트웨이 활동을 파일에 로깅합니다.
* 아래 표시된 **module path** 값은 **azure-iot-gateway-sdk** 리포지토리의 로컬 복사본 루트 폴더에서 샘플을 실행하는 것으로 가정합니다.
* JSON 파일의 맨 아래에 있는 **링크** 배열은 **BLE1** 및 **BLE2** 모듈을 **매핑** 모듈에 연결하고 **매핑** 모듈을 **IoTHub** 모듈에 연결합니다. 또한 **로거** 모듈이 모든 메시지를 기록하는지 확인합니다.

```
{
    "modules": [
        {
            "name": "IotHub",
          "loader": {
            "name": "native",
            "entrypoint": {
              "module.path": "./modules/iothub/libiothub.so"
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
              "module.path": "./modules/identitymap/libidentity_map.so"
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
              "module.path": "./modules/simulated_device/libsimulated_device.so"
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
              "module.path": "./modules/simulated_device/libsimulated_device.so"
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
              "module.path": "./modules/logger/liblogger.so"
            }
            },
            "args": {
              "filename": "deviceCloudUploadGatewaylog.log"
            }
          }
    ],
    "links": [
        {
            "source": "*",
            "sink": "Logger"
        },
        {
            "source": "BLE1",
            "sink": "mapping"
        },
        {
            "source": "BLE2",
            "sink": "mapping"
        },
        {
            "source": "mapping",
            "sink": "IotHub"
        }
    ]
}
```

구성 파일에 적용한 변경 내용을 저장합니다.

샘플을 실행하려면

1. 셸에서 **azure-iot-gateway-sdk** 리포지토리의 로컬 복사본 루트 폴더로 이동합니다.
2. 다음 명령을 실행합니다.
   
    ```
    ./build/samples/simulated_device_cloud_upload/simulated_device_cloud_upload_sample ./samples/simulated_device_cloud_upload/src/simulated_device_cloud_upload_lin.json
    ```
3. [장치 탐색기 또는 iothub-explorer][lnk-explorer-tools] 도구를 사용하여 IoT Hub가 게이트웨이에서 수신하는 메시지를 모니터링할 수 있습니다.

## <a name="next-steps"></a>다음 단계
IoT Gateway SDK와 코드 예제 실험에 대해 더욱 심도 있게 이해하고 싶다면 다음 개발자 자습서 및 리소스를 참고하세요.

* [IoT Gateway SDK를 사용하여 물리적 장치에서 장치-클라우드 메시지 보내기][lnk-physical-device]
* [Azure IoT Gateway SDK][lnk-gateway-sdk]

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [개발자 가이드][lnk-devguide]
* [처음부터 IoT 솔루션 보안 유지][lnk-securing]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/

[lnk-physical-device]: iot-hub-gateway-sdk-physical-device.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-create-hub]: iot-hub-create-through-portal.md



<!--HONumber=Nov16_HO5-->


