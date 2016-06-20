<properties
	pageTitle="Gateway SDK를 사용하여 장치 시뮬레이션 | Microsoft Azure"
	description="Azure IoT Hub Gateway SDK를 사용하여 시뮬레이션된 장치에서 원격 분석을 보내는 방법을 보여 주는 Linux용 Azure IoT Hub Gateway SDK 연습입니다."
	services="iot-hub"
	documentationCenter=""
	authors="chipalost"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="04/20/2016"
     ms.author="cstreet"/>


# IoT Gateway SDK(베타) – Linux를 사용하는 시뮬레이션된 장치에서 장치-클라우드 메시지 보내기

[AZURE.INCLUDE [iot-hub-gateway-sdk-simulated-selector](../../includes/iot-hub-gateway-sdk-simulated-selector.md)]

## 샘플 빌드 및 실행

시작하기 전에 다음을 수행해야 합니다.

- Linux에서 SDK를 사용할 수 있도록 [개발 환경을 설정][lnk-setupdevbox]합니다.
- Azure 구독에서 [IoT Hub를 만듭니다][lnk-create-hub]. 이 연습을 완료하려면 허브 이름이 필요합니다. Azure 구독이 아직 없는 경우 [무료 계정][lnk-free-trial]을 얻을 수 있습니다.
- IoT Hub에 장치 두 개를 추가하고 해당 ID와 장치 키를 적어 둡니다. [장치 탐색기 또는 iothub-explorer][lnk-explorer-tools] 도구를 사용하여 이전 단계에서 만든 IoT Hub에 장치를 추가하고 해당 키를 검색할 수 있습니다.

샘플을 빌드하려면

1. 셸을 엽니다.
2. **azure-iot-gateway-sdk** 리포지토리의 로컬 복사본에서 루트 폴더로 이동합니다.
3. **tools/build.sh** 스크립트를 실행합니다. 이 스크립트는 **cmake** 유틸리티를 사용하여 **azure-iot-gateway-sdk** 리포지토리의 로컬 복사본 루트 폴더에 **build**라는 폴더를 만들고 메이크파일을 생성합니다. 그런 다음 솔루션을 빌드하고 테스트를 실행합니다.

> [AZURE.NOTE]  **build.sh** 스크립트를 실행할 때마다 **azure-iot-gateway-sdk** 리포지토리의 로컬 복사본 루트 폴더에서 **build** 폴더가 삭제되었다가 다시 생성됩니다.

샘플을 실행하려면

텍스트 편집기에서 **azure-iot-gateway-sdk** 리포지토리의 로컬 복사본에 있는 **samples/simulated\_device\_cloud\_upload/src/simulated\_device\_cloud\_upload\_lin.json** 파일을 엽니다. 이 파일은 샘플 게이트웨이의 모듈을 구성합니다.

- **IoTHub** 모듈이 IoT Hub에 연결됩니다. IoT Hub에 데이터를 보내도록 이를 구성해야 합니다. 특히 **IoTHubName** 값을 IoT Hub의 이름으로 설정하고, **IoTHubSuffix** 값을 **azure-devices.net**으로 설정합니다.
- **mapping** 모듈은 시뮬레이션된 장치의 MAC 주소를 IoT Hub 장치 ID에 매핑합니다. **deviceId** 값이 IoT Hub에 추가한 두 장치의 ID와 일치하는지, 그리고 **deviceKey** 값에 두 장치의 키가 포함되어 있는지 확인합니다.
- **BLE1** 및 **BLE2** 모듈은 시뮬레이션된 장치입니다. 해당 MAC 주소가 **mapping** 모듈의 MAC 주소와 어떻게 일치되는지 확인합니다.
- **Logger** 모듈은 게이트웨이 활동을 파일에 로깅합니다.
- 아래 표시된 **module path** 값은 **azure-iot-gateway-sdk** 리포지토리의 로컬 복사본 루트 폴더에서 샘플을 실행하는 것으로 가정합니다.

```
{
    "modules" :
    [ 
        {
            "module name" : "IoTHub",
            "module path" : "./build/modules/iothubhttp/libiothubhttp_hl.so",
            "args" : 
            {
                "IoTHubName" : "{Your IoT hub name}",
                "IoTHubSuffix" : "azure-devices.net"
            }
        },
        {
            "module name" : "mapping",
            "module path" : "./build/modules/identitymap/libidentitymap_hl.so",
            "args" : 
            [
                {
                    "macAddress" : "01-01-01-01-01-01",
                    "deviceId"   : "GW-ble1-demo",
                    "deviceKey"  : "{Device key}"
                },
                {
                    "macAddress" : "02-02-02-02-02-02",
                    "deviceId"   : "GW-ble2-demo",
                    "deviceKey"  : "{Device key}"
                }
            ]
        },
        {
            "module name":"BLE1",
            "module path" : "./build/modules/simulated_device/libsimulated_device_hl.so",
            "args":
            {
                "macAddress" : "01-01-01-01-01-01"
            }
        },
        {
            "module name":"BLE2",
            "module path" : "./build/modules/simulated_device/libsimulated_device_hl.so",
            "args":
            {
                "macAddress" : "02-02-02-02-02-02"
            }
        },
        {
            "module name":"Logger",
            "module path" : "./build/modules/logger/liblogger_hl.so",
            "args":
            {
                "filename":"./deviceCloudUploadGatewaylog.log"
            }
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

## 다음 단계

Gateway SDK 사용 방법에 대한 자세한 내용은 GitHub에서 [Azure IoT Gateway SDK][lnk-gateway-sdk]를 참조하세요.

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
[lnk-create-hub]: iot-hub-manage-through-portal.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/

<!---HONumber=AcomDC_0608_2016-->