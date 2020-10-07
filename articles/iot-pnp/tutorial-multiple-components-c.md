---
title: IoT 플러그 앤 플레이 샘플 C 디바이스 코드를 IoT Hub에 연결 | Microsoft Docs
description: 여러 구성 요소를 사용하고 IoT 허브에 연결하는 IoT 플러그 앤 플레이 샘플 C 디바이스 코드를 빌드하고 실행합니다. Azure IoT 탐색기 도구를 사용하여 디바이스에서 허브로 전송된 정보를 봅니다.
author: ericmitt
ms.author: ericmitt
ms.date: 07/22/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 1873d2acb96c0c94c7e0d678e450596c60ca51fb
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91575404"
---
# <a name="tutorial-connect-an-iot-plug-and-play-multiple-component-device-applications-running-on-linux-or-windows-to-iot-hub-c"></a>자습서: Linux 또는 Windows에서 실행되는 IoT 플러그 앤 플레이 다중 구성 요소 디바이스 애플리케이션을 IoT Hub에 연결(C)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

이 자습서에서는 구성 요소를 사용하여 샘플 IoT 플러그 앤 플레이 디바이스 애플리케이션을 빌드하고, IoT 허브에 연결하고, Azure IoT 탐색기 도구를 사용하여 허브로 전송된 정보를 확인하는 방법을 보여 줍니다. 샘플 애플리케이션은 C로 작성되었으며 C용 Azure IoT 디바이스 SDK에 포함되어 있습니다. 솔루션 빌더는 디바이스 코드를 볼 필요 없이 Azure IoT 탐색기 도구를 사용하여 IoT 플러그 앤 플레이 디바이스의 기능을 이해할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Linux 또는 Windows에서 이 자습서를 완료할 수 있습니다. 이 자습서의 셸 명령은 '`/`' 경로 구분 기호에 대한 Linux 규칙을 따릅니다. Windows에서 계속 실행 중인 경우 '`\`'에 대해 이러한 구분 기호를 교환해야 합니다.

필수 구성 요소는 운영 체제에 따라 다릅니다.

### <a name="linux"></a>Linux

이 자습서에서는 Ubuntu Linux를 사용하고 있다고 가정합니다. 이 자습서의 단계는 Ubuntu 18.04를 사용하여 테스트했습니다.

Linux에서 이 자습서를 완료하려면 로컬 Linux 환경에 다음 소프트웨어를 설치해야 합니다.

`apt-get` 명령을 사용하여 **GCC**, **Git**, **cmake** 및 필요한 모든 종속성을 설치합니다.

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

`cmake` 버전이 **2.8.12**보다 크고, **GCC** 버전이 **4.4.7**보다 큰지 확인합니다.

```sh
cmake --version
gcc --version
```

### <a name="windows"></a>Windows

Windows에서 이 자습서를 완료하려면 로컬 Windows 환경에 다음 소프트웨어를 설치합니다.

* [Visual Studio(Community, Professional 또는 Enterprise)](https://visualstudio.microsoft.com/downloads/) - Visual Studio를 [설치](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2019&preserve-view=true)할 때 **C++를 사용한 데스크톱 개발** 워크로드를 포함해야 합니다.
* [Git](https://git-scm.com/download/)
* [CMake](https://cmake.org/download/)

## <a name="download-the-code"></a>코드 다운로드

[빠른 시작: Linux 또는 Windows에서 실행되는 샘플 IoT 플러그 앤 플레이 디바이스 애플리케이션을 IoT Hub(C)에 연결](quickstart-connect-device-c.md)을 완료한 경우 이미 코드를 다운로드한 것입니다.

이 자습서에서는 Azure IoT Hub Device C SDK를 복제하고 빌드하는 데 사용할 수 있는 개발 환경을 준비합니다.

선택한 폴더에서 명령 프롬프트를 엽니다. 다음 명령을 실행하여 [Azure IoT C SDK 및 라이브러리](https://github.com/Azure/azure-iot-sdk-c) GitHub 리포지토리를 이 위치에 복제합니다.

```cmd/bash
git clone https://github.com/Azure/azure-iot-sdk-c.git
cd azure-iot-sdk-c
git submodule update --init
```

이 작업을 완료하는 데 몇 분 정도가 걸립니다.

## <a name="build-and-run-the-code"></a>코드 빌드 및 실행

Visual Studio를 사용하거나 명령줄에서 `cmake`를 사용하여 코드를 빌드하고 실행할 수 있습니다.

### <a name="use-visual-studio"></a>Visual Studio 사용

1. 복제된 리포지토리의 루트 폴더를 엽니다. 몇 초 후에 Visual Studio에서 **CMake** 지원은 프로젝트를 실행하고 디버그하는 데 필요한 모든 작업을 만듭니다.
1. Visual Studio가 준비되면 **솔루션 탐색기**에서 *iothub_client/samples/pnp/pnp_temperature_controller/* 샘플로 이동합니다.
1. *pnp_temperature_controller.c* 파일을 마우스 오른쪽 단추로 클릭하고 **디버그 구성 추가**를 선택합니다. **기본값**을 선택합니다.
1. Visual Studio에서 *launch.vs.json* 파일을 엽니다. 필요한 환경 변수를 설정하려면 다음 코드 조각에 나와 있는 것처럼 이 파일을 편집합니다. [IoT 플러그 앤 플레이 빠른 시작 및 자습서에 대한 환경 설정](set-up-environment.md)을 완료할 때 범위 ID 및 등록 기본 키를 적어 두었습니다.

    ```json
    {
      "version": "0.2.1",
      "defaults": {},
      "configurations": [
        {
          "type": "default",
          "project": "iothub_client\\samples\\pnp\\pnp_temperature_controller\\pnp_temperature_controller.c",
          "projectTarget": "",
          "name": "pnp_temperature_controller.c",
          "env": {
            "IOTHUB_DEVICE_SECURITY_TYPE": "DPS",
            "IOTHUB_DEVICE_DPS_ID_SCOPE": "<Your ID scope>",
            "IOTHUB_DEVICE_DPS_DEVICE_ID": "my-pnp-device",
            "IOTHUB_DEVICE_DPS_DEVICE_KEY": "<Your enrollment primary key>"
          }
        }
      ]
    }
    ```

1. *pnp_temperature_controller.c* 파일을 마우스 오른쪽 단추로 클릭하고 **시작 항목으로 설정**을 선택합니다.
1. Visual Studio에서 코드 실행을 추적하려면 *pnp_temperature_controller.c* 파일의 `main` 함수에 중단점을 추가합니다.
1. 이제 **디버그** 메뉴에서 샘플을 실행하고 디버그할 수 있습니다.

이제 디바이스가 명령 및 속성 업데이트를 받을 준비가 되었으며, 허브로 원격 분석 데이터를 보내기 시작했습니다. 다음 단계를 완료하는 동안 샘플을 계속 실행하세요.

### <a name="use-cmake-at-the-command-line"></a>명령줄에서 `cmake`를 사용합니다.

샘플을 빌드하려면

1. 복제된 디바이스 SDK의 루트 폴더에 _cmake_ 하위 폴더를 만들고 해당 폴더로 이동합니다.

    ```cmd/bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. SDK 및 샘플에 대한 프로젝트 파일을 생성하고 빌드하려면 다음 명령을 실행합니다.

    ```cmd/bash
    cmake ..
    cmake --build .
    ```

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

샘플 구성에 대해 자세히 알아보려면 [샘플 추가 정보](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/pnp/readme.md)를 참조하세요.

예제를 실행하려면

1. _cmake_ 폴더에서 실행 파일이 있는 폴더로 이동하여 실행합니다.

    ```bash
    # Bash
    cd iothub_client/samples/pnp/pnp_temperature_controller/
    ./pnp_temperature_controller
    ```

    ```cmd
    REM Windows
    cd iothub_client\samples\pnp\pnp_temperature_controller\Debug
    pnp_temperature_controller.exe
    ```

이제 디바이스가 명령 및 속성 업데이트를 받을 준비가 되었으며, 허브로 원격 분석 데이터를 보내기 시작했습니다. 다음 단계를 완료하는 동안 샘플을 계속 실행하세요.

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Azure IoT 탐색기를 사용하여 코드의 유효성을 검사합니다.

디바이스 클라이언트 샘플이 시작된 후 Azure IoT 탐색기 도구를 사용하여 작동하는지 확인합니다.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>코드 검토

이 샘플은 IoT 플러그 앤 플레이 온도 컨트롤러 디바이스를 구현합니다. 이 샘플에서는 [여러 구성 요소](concepts-components.md)를 사용하여 모델을 구현합니다. [온도 디바이스용 DTDL(Digital Twins 정의 언어) 모델 파일](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)은 디바이스에서 구현하는 원격 분석, 속성 및 명령을 정의합니다.

### <a name="iot-plug-and-play-helper-functions"></a>IoT 플러그 앤 플레이 도우미 함수

이 샘플의 경우 코드는 */common* 폴더의 일부 도우미 함수를 사용합니다.

*pnp_device_client_ll*에는 매개 변수로 포함된 `model-id`를 사용하여 IoT 플러그 앤 플레이에 대한 connect 메서드가 포함되어 있습니다. `PnP_CreateDeviceClientLLHandle`

*pnp_protocol*: IoT 플러그 앤 플레이 도우미 함수를 포함합니다.

* `PnP_CreateReportedProperty`
* `PnP_CreateReportedPropertyWithStatus`
* `PnP_ParseCommandName`
* `PnP_CreateTelemetryMessageHandle`
* `PnP_ProcessTwinData`
* `PnP_CopyPayloadToString`
* `PnP_CreateDeviceClientLLHandle_ViaDps`

이러한 도우미 함수는 고유한 프로젝트에서 사용할 수 있을 만큼 일반적입니다. 이 샘플은 모델의 각 구성 요소에 해당하는 세 개의 파일에서 이를 사용합니다.

* *pnp_deviceinfo_component*
* *pnp_temperature_controller*
* *pnp_thermostat_component*

예를 들어 *pnp_deviceinfo_component* 파일에서 `SendReportedPropertyForDeviceInformation` 함수는 두 도우미 함수를 사용합니다.

```c
if ((jsonToSend = PnP_CreateReportedProperty(componentName, propertyName, propertyValue)) == NULL)
{
    LogError("Unable to build reported property response for propertyName=%s, propertyValue=%s", propertyName, propertyValue);
}
else
{
    const char* jsonToSendStr = STRING_c_str(jsonToSend);
    size_t jsonToSendStrLen = strlen(jsonToSendStr);

    if ((iothubClientResult = IoTHubDeviceClient_LL_SendReportedState(deviceClientLL, (const unsigned char*)jsonToSendStr, jsonToSendStrLen, NULL, NULL)) != IOTHUB_CLIENT_OK)
    {
        LogError("Unable to send reported state for property=%s, error=%d", propertyName, iothubClientResult);
    }
    else
    {
        LogInfo("Sending device information property to IoTHub.  propertyName=%s, propertyValue=%s", propertyName, propertyValue);
    }
}
```

샘플의 각 구성 요소는 이 패턴을 따릅니다.

### <a name="code-flow"></a>코드 흐름

`main` 함수는 연결을 초기화하고 모델 ID를 보냅니다.

```c
deviceClient = CreateDeviceClientAndAllocateComponents();
```

이 코드는 `PnP_CreateDeviceClientLLHandle`을 사용하여 IoT 허브에 연결하고, `modelId`를 옵션으로 설정하고, 직접 메서드 및 디바이스 쌍 업데이트에 대한 디바이스 메서드와 디바이스 쌍 콜백 처리기를 설정합니다.

```c
g_pnpDeviceConfiguration.deviceMethodCallback = PnP_TempControlComponent_DeviceMethodCallback;
g_pnpDeviceConfiguration.deviceTwinCallback = PnP_TempControlComponent_DeviceTwinCallback;
g_pnpDeviceConfiguration.modelId = g_temperatureControllerModelId;
...

deviceClient = PnP_CreateDeviceClientLLHandle(&g_pnpDeviceConfiguration);
```

`&g_pnpDeviceConfiguration`에는 연결 정보도 포함되어 있습니다. 환경 변수 **IOTHUB_DEVICE_SECURITY_TYPE**은 샘플에서 연결 문자열 또는 디바이스 프로비저닝 서비스를 사용하여 IoT 허브에 연결하는지 여부를 결정합니다.

디바이스에서 모델 ID를 보내면 해당 디바이스는 IoT 플러그 앤 플레이 디바이스가 됩니다.

콜백 처리기가 준비되면 디바이스는 쌍 업데이트 및 직접 메서드 호출에 반응합니다.

* 디바이스 쌍 콜백의 경우 `PnP_TempControlComponent_DeviceTwinCallback`은 `PnP_ProcessTwinData` 함수를 호출하여 데이터를 처리합니다. `PnP_ProcessTwinData`는 *방문자 패턴*을 사용하여 JSON을 구문 분석한 다음, 각 요소에 대해 `PnP_TempControlComponent_ApplicationPropertyCallback`을 호출하여 각 속성을 방문합니다.

* 명령 콜백의 경우 `PnP_TempControlComponent_DeviceMethodCallback` 함수는 도우미 함수를 사용하여 명령 및 구성 요소 이름을 구문 분석합니다.

    ```c
    PnP_ParseCommandName(methodName, &componentName, &componentNameSize, &pnpCommandName);
    ```

    그런 다음, `PnP_TempControlComponent_DeviceMethodCallback` 함수는 구성 요소에 대해 명령을 호출합니다.

    ```c
    LogInfo("Received PnP command for component=%.*s, command=%s", (int)componentNameSize, componentName, pnpCommandName);
    if (strncmp((const char*)componentName, g_thermostatComponent1Name, g_thermostatComponent1Size) == 0)
    {
        result = PnP_ThermostatComponent_ProcessCommand(g_thermostatHandle1, pnpCommandName, rootValue, response, responseSize);
    }
    else if (strncmp((const char*)componentName, g_thermostatComponent2Name, g_thermostatComponent2Size) == 0)
    {
        result = PnP_ThermostatComponent_ProcessCommand(g_thermostatHandle2, pnpCommandName, rootValue, response, responseSize);
    }
    else
    {
        LogError("PnP component=%.*s is not supported by TemperatureController", (int)componentNameSize, componentName);
        result = PNP_STATUS_NOT_FOUND;
    }
    ```

`main` 함수는 IoT 허브로 전송되는 읽기 전용 속성을 초기화합니다.

```c
PnP_TempControlComponent_ReportSerialNumber_Property(deviceClient);
PnP_DeviceInfoComponent_Report_All_Properties(g_deviceInfoComponentName, deviceClient);
PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle1, deviceClient);
PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle2, deviceClient);
```

`main` 함수는 각 구성 요소에 대한 이벤트 및 원격 분석 데이터를 업데이트하는 루프를 입력합니다.

```c
while (true)
{
    PnP_TempControlComponent_SendWorkingSet(deviceClient);
    PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle1, deviceClient);
    PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle2, deviceClient);
}
```

`PnP_ThermostatComponent_SendTelemetry` 함수는 `PNP_THERMOSTAT_COMPONENT` 구조체를 사용하는 방법을 보여 줍니다. 샘플에서는 이 구조체를 사용하여 온도 컨트롤러의 두 자동 온도 조절기에 대한 정보를 저장합니다. 이 코드는 `PnP_CreateTelemetryMessageHandle` 함수를 사용하여 메시지를 준비하고 보냅니다.

```c
messageHandle = PnP_CreateTelemetryMessageHandle(pnpThermostatComponent->componentName, temperatureStringBuffer);
...
iothubResult = IoTHubDeviceClient_LL_SendEventAsync(deviceClientLL, messageHandle, NULL, NULL);
```

`main` 함수는 마지막으로 다른 구성 요소를 삭제하고 허브에 대한 연결을 닫습니다.

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 IoT 플러그 앤 플레이 디바이스를 구성 요소와 IoT 허브에 연결하는 방법을 알아보았습니다. IoT 플러그 앤 플레이 디바이스 모델에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [IoT 플러그 앤 플레이 모델링 개발자 가이드](concepts-developer-guide-device-csharp.md)
