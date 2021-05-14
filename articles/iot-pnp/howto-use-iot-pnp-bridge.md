---
title: Linux 또는 Windows에서 실행되는 IoT 플러그 앤 플레이 브리지 샘플을 IoT 허브에 연결하는 방법 | Microsoft Docs
description: Linux 또는 Windows에서 IoT 허브에 연결하는 IoT 플러그 앤 플레이 브리지를 빌드하고 실행합니다. Azure IoT 탐색기 도구를 사용하여 디바이스에서 허브로 전송된 정보를 봅니다.
author: usivagna
ms.author: ugans
ms.date: 12/11/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 28feaf266e2d5bed14077a686e6eb936e430a695
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106066758"
---
# <a name="how-to-connect-an--iot-plug-and-play-bridge-sample-running-on-linux-or-windows-to-iot-hub"></a>Linux 또는 Windows에서 실행되는 IoT 플러그 앤 플레이 브리지 샘플을 IoT Hub에 연결하는 방법

이 문서에서는 IoT 플러그 앤 플레이 브리지의 샘플 환경 어댑터를 빌드하고 이를 IoT 허브에 연결하며 Azure IoT 탐색기 도구를 사용하여 전송되는 원격 분석을 확인하는 방법을 보여 줍니다. IoT 플러그 앤 플레이 브리지는 C로 작성되었으며 C용 Azure IoT 디바이스 SDK가 포함되어 있습니다. 이 자습서를 마치면 IoT 플러그 앤 플레이 브리지를 실행할 수 있고 Azure IoT 탐색기에서 원격 분석 보고서를 확인할 수 있습니다.

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-explorer-telemetry.png" alt-text="Iot 플러그 앤 플레이 브리지에서 보고된 원격 분석(습도, 온도) 표가 있는 Azure IoT 탐색기를 보여 주는 스크린샷":::

## <a name="prerequisites"></a>사전 요구 사항

Windows 또는 Linux의 문서에서 샘플을 실행할 수 있습니다. 이 방법 가이드의 셸 명령은 ‘`\`’ 경로 구분 기호에 대한 Windows 규칙을 따릅니다. Linux에서 계속 실행 중인 경우 ‘`/`’에 대해 이러한 구분 기호를 교환해야 합니다.

### <a name="azure-iot-explorer"></a>Azure IoT 탐색기

이 문서의 두 번째 부분에서 샘플 디바이스와 상호 작용하려면 **Azure IoT 탐색기** 도구를 사용합니다. 운영 체제용 [최신 릴리스의 Azure IoT 탐색기를 다운로드하여 설치](./howto-use-iot-explorer.md)합니다.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

다음 명령을 실행하여 허브에 대한 ‘IoT Hub 연결 문자열’을 가져옵니다. 이 연결 문자열을 기록해 두고 이 문서 뒷부분에 사용합니다.

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

다음 명령을 실행하여 허브에 추가한 디바이스에 대한 _디바이스 연결 문자열_ 을 가져옵니다. 이 연결 문자열을 기록해 두고 이 문서 뒷부분에 사용합니다.

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

## <a name="download-and-run-the-bridge"></a>브리지 다운로드 및 실행

이 문서에는 브리지를 실행하는 두 가지 옵션이 있습니다. 다음을 할 수 있습니다.

- 이 섹션에 설명된 대로 미리 작성된 실행 파일을 다운로드하여 실행합니다.
- 다음 섹션에 설명된 대로 소스 코드를 다운로드한 다음 [브리지를 빌드하고 실행](#build-and-run-the-bridge)합니다.

브리지를 다운로드하여 실행하려면:

1. IoT 플러그 앤 플레이 [릴리스 페이지](https://github.com/Azure/iot-plug-and-play-bridge/releases)로 이동합니다.
1. 운영 체제에 맞게 미리 빌드된 실행 파일인 Windows용 **pnpbridge_bin.exe** 또는 Linux용 **pnpbridge_bin** 을 다운로드합니다.
1. 환경 센서 샘플에 대한 샘플 [config.json](https://raw.githubusercontent.com/Azure/iot-plug-and-play-bridge/master/pnpbridge/src/adapters/samples/environmental_sensor/config.json) 구성 파일을 다운로드합니다. 구성 파일이 실행 파일과 동일한 폴더에 있는지 확인합니다.
1. *config.json* 파일을 편집합니다.

    - 이전에 기록해 둔 _디바이스 연결 문자열_ 인 `connection-string` 값을 추가합니다.
    - _디바이스 연결 문자열_ 에서 공유 액세스 키 값인 `symmetric_key` 값을 추가합니다.
    - `root_interface_model_id` 값을 `dtmi:com:example:PnpBridgeEnvironmentalSensor;1`로 바꿉니다.

    이제 *config.json* 파일의 첫 번째 섹션은 다음 조각과 같습니다.

    ```json
    {
      "$schema": "../../../pnpbridge/src/pnpbridge_config_schema.json",
      "pnp_bridge_connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "HostName=youriothub.azure-devices.net;DeviceId=yourdevice;SharedAccessKey=TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8=",
        "root_interface_model_id": "dtmi:com:example:PnpBridgeEnvironmentalSensor;1",
        "auth_parameters": {
            "auth_type": "symmetric_key",
            "symmetric_key": "TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8="
        },
    ```

1. 명령줄 환경에서 실행 파일을 실행합니다. 브리지는 다음과 같은 출력을 생성합니다.

    ```output
    c:\temp\temp-bridge>dir
     Volume in drive C is OSDisk
     Volume Serial Number is 38F7-DA4A
    
     Directory of c:\temp\temp-bridge
    
    10/12/2020  12:24    <DIR>          .
    10/12/2020  12:24    <DIR>          ..
    08/12/2020  15:26             1,216 config.json
    10/12/2020  12:21         3,617,280 pnpbridge_bin.exe
                   2 File(s)      3,618,496 bytes
                   2 Dir(s)  12,999,147,520 bytes free
    
    c:\temp\temp-bridge>pnpbridge_bin.exe
    Info:
     -- Press Ctrl+C to stop PnpBridge
    
    Info: Using default configuration location
    Info: Starting Azure PnpBridge
    Info: Pnp Bridge is running as am IoT egde device.
    Info: Pnp Bridge creation succeeded.
    Info: Connection_type is [connection_string]
    Info: Tracing is disabled
    Info: WARNING: SharedAccessKey is included in connection string. Ignoring symmetric_key in config file.
    Info: IoT Edge Device configuration initialized successfully
    Info: Building Pnp Bridge Adapter Manager, Adapters & Components
    Info: Adapter with identity environment-sensor-sample-pnp-adapter does not have any associated global parameters. Proceeding with adapter creation.
    Info: Pnp Adapter with adapter ID environment-sensor-sample-pnp-adapter has been created.
    Info: Pnp Adapter Manager created successfully.
    Info: Pnp components created successfully.
    Info: Pnp components built in model successfully.
    Info: Connected to Azure IoT Hub
    Info: Environmental Sensor: Starting Pnp Component
    Info: IoTHub client call to _SendReportedState succeeded
    Info: Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=state, propertyValue=true
    Info: Pnp components started successfully.
    ```

## <a name="build-and-run-the-bridge"></a>브리지 빌드 및 실행

실행 파일을 직접 빌드하려면 소스 코드와 빌드 스크립트를 다운로드하면 됩니다.

선택한 폴더에서 명령 프롬프트를 엽니다. 다음 명령을 실행하여 [IoT 플러그 앤 플레이 브리지](https://github.com/Azure/iot-plug-and-play-bridge) GitHub 리포지토리를 다음 위치에 복제합니다.

```cmd
git clone https://github.com/Azure/iot-plug-and-play-bridge.git
```

리포지토리를 복제한 후 하위 모듈을 업데이트합니다. 하위 모듈에는 C용 Azure IoT SDK가 포함됩니다.

```cmd
cd iot-plug-and-play-bridge
git submodule update --init --recursive
```

이 작업을 완료하는 데 몇 분 정도가 걸립니다.

> [!TIP]
> Git Clone 하위 모듈 업데이트 실패와 관련된 문제가 발생하면 이는 Windows 파일 경로에서 알려진 문제입니다. 다음 명령을 실행하여 문제를 해결할 수 있습니다. `git config --system core.longpaths true`

브리지를 빌드하기 위한 필수 조건은 운영 체제에 따라 다릅니다.

### <a name="windows"></a>Windows

Windows에서 IoT 플러그 앤 플레이 브리지를 빌드하려면 다음 소프트웨어를 설치합니다.

* [Visual Studio(Community, Professional 또는 Enterprise)](https://visualstudio.microsoft.com/downloads/) - Visual Studio를 [설치](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019)할 때 **C++를 사용한 데스크톱 개발** 워크로드를 포함해야 합니다.
* [Git](https://git-scm.com/download/)
* [CMake](https://cmake.org/download/)

### <a name="linux"></a>Linux

이 문서에서는 Ubuntu Linux를 사용한다고 가정합니다. 이 문서의 단계는 Ubuntu 18.04를 사용하여 테스트했습니다.

Linux에서 IoT 플러그 앤 플레이 브리지를 빌드하려면 **GCC**, **Git**, **cmake** 와 `apt-get` 명령을 사용하여 필요한 모든 종속성을 설치합니다.

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

`cmake` 버전이 **2.8.12** 보다 크고, **GCC** 버전이 **4.4.7** 보다 큰지 확인합니다.

```sh
cmake --version
gcc --version
```

### <a name="build-the-iot-plug-and-play-bridge"></a>IoT 플러그 앤 플레이 브리지 빌드

리포지토리 디렉터리의 *pnpbridge* 폴더로 이동합니다.

Windows의 경우 [Visual Studio에 대한 개발자 명령 프롬프트](/dotnet/framework/tools/developer-command-prompt-for-vs)에서 다음을 실행합니다.

```cmd
cd scripts\windows
build.cmd
```

마찬가지로 Linux에서도 다음을 실행합니다.

```bash
cd scripts/linux
./setup.sh
./build.sh
```

>[!TIP]
>Windows에서 Visual Studio 2019의 cmake 명령으로 생성된 솔루션을 열 수 있습니다. cmake 디렉터리의 *azure_iot_pnp_bridge.sln* 프로젝트 파일을 열고 *pnpbridge_bin* 프로젝트를 솔루션의 시작 프로젝트로 설정합니다. 이제 Visual Studio에서 샘플을 빌드하고 디버그 모드에서 실행할 수 있습니다.

### <a name="edit-the-configuration-file"></a>구성 파일 편집

구성 파일에 대한 자세한 내용은 [IoT 플러그 앤 플레이 브리지 개념 설명서](concepts-iot-pnp-bridge.md)에서 확인할 수 있습니다.

텍스트 편집기에서 *iot-plug-and-play-bridge\pnpbridge\src\adapters\samples\environmental_sensor\config.json* 파일을 엽니다.

- 이전에 기록해 둔 _디바이스 연결 문자열_ 인 `connection-string` 값을 추가합니다.
- _디바이스 연결 문자열_ 에서 공유 액세스 키 값인 `symmetric_key` 값을 추가합니다.
- `root_interface_model_id` 값을 `dtmi:com:example:PnpBridgeEnvironmentalSensor;1`로 바꿉니다.

이제 *config.json* 파일의 첫 번째 섹션은 다음 조각과 같습니다.

```json
{
  "$schema": "../../../pnpbridge/src/pnpbridge_config_schema.json",
  "pnp_bridge_connection_parameters": {
    "connection_type" : "connection_string",
    "connection_string" : "HostName=youriothub.azure-devices.net;DeviceId=yourdevice;SharedAccessKey=TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8=",
    "root_interface_model_id": "dtmi:com:example:PnpBridgeEnvironmentalSensor;1",
    "auth_parameters": {
        "auth_type": "symmetric_key",
        "symmetric_key": "TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8="
    },
```

### <a name="run-the-iot-plug-and-play-bridge"></a>IoT 플러그 앤 플레이 브리지 실행

IoT 플러그 앤 플레이 브리지 환경 센서 샘플을 시작합니다. 매개 변수는 이전 섹션에서 편집한 `config.json` 파일의 경로입니다.

```cmd
REM Windows
cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console
Debug\pnpbridge_bin.exe ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
```

브리지는 다음과 같은 출력을 생성합니다.

```output
c:\temp>cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console

c:\temp\iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console>Debug\pnpbridge_bin.exe ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
Info:
 -- Press Ctrl+C to stop PnpBridge

Info: Using configuration from specified file path: ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
Info: Starting Azure PnpBridge
Info: Pnp Bridge is running as am IoT egde device.
Info: Pnp Bridge creation succeeded.
Info: Connection_type is [connection_string]
Info: Tracing is disabled
Info: WARNING: SharedAccessKey is included in connection string. Ignoring symmetric_key in config file.
Info: IoT Edge Device configuration initialized successfully
Info: Building Pnp Bridge Adapter Manager, Adapters & Components
Info: Adapter with identity environment-sensor-sample-pnp-adapter does not have any associated global parameters. Proceeding with adapter creation.
Info: Pnp Adapter with adapter ID environment-sensor-sample-pnp-adapter has been created.
Info: Pnp Adapter Manager created successfully.
Info: Pnp components created successfully.
Info: Pnp components built in model successfully.
Info: Connected to Azure IoT Hub
Info: Environmental Sensor: Starting Pnp Component
Info: IoTHub client call to _SendReportedState succeeded
Info: Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=state, propertyValue=true
Info: Pnp components started successfully.
Info: IoTHub client call to _SendEventAsync succeeded
Info: PnpBridge_PnpBridgeStateTelemetryCallback called, result=0, telemetry=PnpBridge configuration complete
Info: Processing property update for the device or module twin
Info: Environmental Sensor Adapter:: Successfully delivered telemetry message for <environmentalSensor>
```

다음 명령을 사용하여 Linux에서 브리지를 실행합니다.

```bash
cd iot-plug-and-play-bridge/pnpbridge/cmake/pnpbridge_x86/src/pnpbridge/samples/console
./pnpbridge_bin ../../../../../../src/adapters/samples/environmental_sensor/config.json
```

## <a name="download-the-model-files"></a>모델 파일 다운로드

나중에 Azure IoT 탐색기를 사용하여 IoT 허브에 연결할 때 디바이스를 볼 수 있습니다. Azure IoT 탐색기에는 디바이스에서 보내는 **모델 ID** 와 일치하는 모델 파일의 로컬 복사본이 필요합니다. 모델 파일을 통해 IoT Explorer에서 디바이스가 구현하는 원격 분석, 속성, 명령을 표시할 수 있습니다.

Azure IoT 탐색기용 모델을 다운로드하려면 다음을 수행합니다.

1. *모델* 이라는 폴더를 로컬 머신에 만듭니다.
1. 이전 단계에서 만든 ‘모델’ 폴더에 [EnvironmentalSensor.json](https://raw.githubusercontent.com/Azure/iot-plug-and-play-bridge/master/pnpbridge/docs/schemas/EnvironmentalSensor.json)을 저장합니다.
1. 텍스트 편집기에서 이 모델 파일을 열면 모델이 `dtmi:com:example:PnpBridgeEnvironmentalSensor;1`을 모델 ID로 구성 요소를 정의하는 것을 볼 수 있습니다. 이는 *config.json* 파일에서 사용한 모델 ID와 동일합니다.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Azure IoT 탐색기를 사용하여 코드의 유효성을 검사합니다.

브리지가 시작되면 Azure IoT 탐색기 도구를 사용하여 브리지가 작동하는지 확인합니다. `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` 모델에 정의된 원격 분석, 속성, 명령을 볼 수 있습니다.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>다음 단계

이 문서에서는 IoT 플러그 앤 플레이 디바이스를 IoT 허브에 연결하는 방법을 알아보았습니다. IoT 플러그 앤 플레이 디바이스와 상호 작용하는 솔루션을 빌드하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [IoT 플러그 앤 플레이 브리지란?](./concepts-iot-pnp-bridge.md)
* [IoT 플러그 앤 플레이 브리지 빌드, 배포, 확장](howto-build-deploy-extend-pnp-bridge.md)
* [GitHub의 IoT 플러그 앤 플레이 브리지](https://github.com/Azure/iot-plug-and-play-bridge)
