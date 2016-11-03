<properties
    pageTitle="Gateway SDK를 사용하여 장치 시뮬레이션 | Microsoft Azure"
    description="Azure IoT Hub Gateway SDK를 사용하여 시뮬레이션된 장치에서 원격 분석을 보내는 방법을 보여 주는 Windows용 Azure IoT Hub Gateway SDK 연습입니다."
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
     ms.date="08/29/2016"
     ms.author="andbuc"/>



# <a name="iot-gateway-sdk-beta-send-devicetocloud-messages-with-a-simulated-device-using-windows"></a>IoT Gateway SDK(베타) – Windows를 사용하는 시뮬레이션된 장치에서 장치-클라우드 메시지 보내기

[AZURE.INCLUDE [iot-hub-gateway-sdk-simulated-selector](../../includes/iot-hub-gateway-sdk-simulated-selector.md)]

## <a name="build-and-run-the-sample"></a>샘플 빌드 및 실행

시작하기 전에 다음을 수행해야 합니다.

- Windows에서 SDK를 사용할 수 있도록 [개발 환경 설정][lnk-setupdevbox]
- Azure 구독에서 [IoT Hub를 만듭니다][lnk-create-hub]. 이 연습을 완료하려면 허브 이름이 필요합니다. Azure 구독이 아직 없는 경우 [무료 계정][lnk-free-trial]을 얻을 수 있습니다.
- IoT Hub에 장치 두 개를 추가하고 해당 ID와 장치 키를 적어 둡니다. [장치 탐색기 또는 iothub-explorer][lnk-explorer-tools] 도구를 사용하여 이전 단계에서 만든 IoT Hub에 장치를 추가하고 해당 키를 검색할 수 있습니다.

샘플을 빌드하려면

1. **VS2015용 개발자 명령 프롬프트** 를 엽니다.
2. **azure-iot-gateway-sdk** 리포지토리의 로컬 복사본에서 루트 폴더로 이동합니다.
3. **tools\\build.cmd** 스크립트를 실행합니다. 이 스크립트는 Visual Studio 솔루션 파일을 만들고, 솔루션을 빌드하고, 테스트를 실행합니다. Visual Studio 솔루션은 **azure-iot-gateway-sdk** 리포지토리의 로컬 복사본에 있는 **build** 폴더에서 찾을 수 있습니다.

샘플을 실행하려면

텍스트 편집기에서 **azure-iot-gateway-sdk** 리포지토리의 로컬 복사본에 있는 **samples\\simulated_device_cloud_upload\\src\\simulated_device_cloud_upload_win.json** 파일을 엽니다. 이 파일은 샘플 게이트웨이의 모듈을 구성합니다.

- **IoTHub** 모듈이 IoT Hub에 연결됩니다. IoT Hub에 데이터를 보내도록 이를 구성해야 합니다. 특히 **IoTHubName** 값을 IoT Hub의 이름으로 설정하고, **IoTHubSuffix** 값을 **azure-devices.net**으로 설정합니다. "HTTP", "AMQP" 또는 "MQTT" 중 하나에 **Transport** 값을 설정합니다. 현재 "HTTP" 만이 모든 장치 메시지에 대한 하나의 TCP 연결을 공유한다는 점에 유의하세요. 값을 "AMQP" 또는 "MQTT"로 설정하는 경우 게이트웨이는 각 장치에 대해 IoT Hub에 대한 별도의 TCP 연결을 유지합니다.
- **mapping** 모듈은 시뮬레이션된 장치의 MAC 주소를 IoT Hub 장치 ID에 매핑합니다. **deviceId** 값이 IoT Hub에 추가한 두 장치의 ID와 일치하는지, 그리고 **deviceKey** 값에 두 장치의 키가 포함되어 있는지 확인합니다.
- **BLE1** 및 **BLE2** 모듈은 시뮬레이션된 장치입니다. 해당 MAC 주소가 **mapping** 모듈의 MAC 주소와 어떻게 일치되는지 확인합니다.
- **Logger** 모듈은 게이트웨이 활동을 파일에 로깅합니다.
- 아래에 표시된 **module path** 값은 Gateway SDK 리포지토리를 **C:** 드라이브의 루트에 복제한 것으로 가정합니다. 다른 위치에 다운로드한 경우 해당 위치에 맞게 **module path** 값을 조정해야 합니다.
- JSON 파일의 맨 아래에 있는 **링크** 배열은 **BLE1** 및 **BLE2** 모듈을 **매핑** 모듈에 연결하고 **매핑** 모듈을 **IoTHub** 모듈에 연결합니다. 또한 **로거** 모듈이 모든 메시지를 기록하는지 확인합니다.

```
{
    "modules" :
    [ 
        {
            "module name" : "IoTHub",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\iothub\\Debug\\iothub_hl.dll",
            "args" : 
            {
                "IoTHubName" : "{Your IoT hub name}",
                "IoTHubSuffix" : "azure-devices.net",
                "Transport": "HTTP"
            }
        },
        {
            "module name" : "mapping",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\identitymap\\Debug\\identity_map_hl.dll",
            "args" : 
            [
                {
                    "macAddress" : "01-01-01-01-01-01",
                    "deviceId"   : "{Device ID 1}",
                    "deviceKey"  : "{Device key 1}"
                },
                {
                    "macAddress" : "02-02-02-02-02-02",
                    "deviceId"   : "{Device ID 2}",
                    "deviceKey"  : "{Device key 2}"
                }
            ]
        },
        {
            "module name":"BLE1",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\simulated_device\\Debug\\simulated_device_hl.dll",
            "args":
            {
                "macAddress" : "01-01-01-01-01-01"
            }
        },
        {
            "module name":"BLE2",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\simulated_device\\Debug\\simulated_device_hl.dll",
            "args":
            {
                "macAddress" : "02-02-02-02-02-02"
            }
        },
        {
            "module name":"Logger",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\logger\\Debug\\logger_hl.dll",
            "args":
            {
                "filename":"C:\\azure-iot-gateway-sdk\\deviceCloudUploadGatewaylog.log"
            }
        }
    ],
    "links" : [
        { "source" : "*", "sink" : "Logger" },
        { "source" : "BLE1", "sink" : "mapping" },
        { "source" : "BLE2", "sink" : "mapping" },
        { "source" : "mapping", "sink" : "IoTHub" }
    ]
}
```

구성 파일에 적용한 변경 내용을 저장합니다.

샘플을 실행하려면

1. 명령 프롬프트에서 **azure-iot-gateway-sdk** 리포지토리의 로컬 복사본 루트 폴더로 이동합니다.
2. 다음 명령을 실행합니다.
  
    ```
    build\samples\simulated_device_cloud_upload\Debug\simulated_device_cloud_upload_sample.exe samples\simulated_device_cloud_upload\src\simulated_device_cloud_upload_win.json
    ```

3. [장치 탐색기 또는 iothub-explorer][lnk-explorer-tools] 도구를 사용하여 IoT Hub가 게이트웨이에서 수신하는 메시지를 모니터링할 수 있습니다.


## <a name="next-steps"></a>다음 단계

게이트웨이 SDK와 코드 예제 실험에 대해 더욱 심도 있게 이해하고 싶다면 다음 개발자 자습서 및 리소스를 참고하세요.

- [Gateway SDK를 사용하여 실제 장치에서 장치-클라우드 메시지 보내기][lnk-physical-device]
- [Azure IoT 게이트웨이 SDK][lnk-gateway-sdk]

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

- [개발자 가이드][lnk-devguide]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/

[lnk-physical-device]: iot-hub-gateway-sdk-physical-device.md

[lnk-devguide]: ./iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md 


<!--HONumber=Oct16_HO2-->


