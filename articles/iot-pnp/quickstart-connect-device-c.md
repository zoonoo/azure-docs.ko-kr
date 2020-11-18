---
title: 빠른 시작 - IoT 플러그 앤 플레이 샘플 C 디바이스 코드를 Azure IoT Hub에 연결 | Microsoft Docs
description: 빠른 시작 - IoT 허브에 연결하는 Linux 또는 Windows에서 IoT 플러그 앤 플레이 샘플 디바이스 코드를 빌드하고 실행합니다. Azure IoT 탐색기 도구를 사용하여 디바이스에서 허브로 전송된 정보를 봅니다.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: d69eca10a3ee19919d7cd9e748486e30db95e749
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421671"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-device-application-running-on-linux-or-windows-to-iot-hub-c"></a>빠른 시작: Linux 또는 Windows에서 실행되는 샘플 IoT 플러그 앤 플레이 디바이스 애플리케이션을 IoT Hub에 연결(C)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

이 빠른 시작에서는 샘플 IoT 플러그 앤 플레이 디바이스 애플리케이션을 빌드하고, IoT 허브에 연결하고, Azure IoT 탐색기 도구를 사용하여 전송되는 원격 분석을 확인하는 방법을 보여줍니다. 샘플 애플리케이션은 C로 작성되었으며 C용 Azure IoT 디바이스 SDK에 포함되어 있습니다. 솔루션 빌더는 디바이스 코드를 볼 필요 없이 Azure IoT 탐색기 도구를 사용하여 IoT 플러그 앤 플레이 디바이스의 기능을 이해할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Linux 또는 Windows에서 이 빠른 시작을 실행할 수 있습니다. 이 빠른 시작의 셸 명령은 '`/`' 경로 구분 기호에 대한 Linux 규칙을 따릅니다. Windows에서 계속 실행 중인 경우 '`\`'에 대해 이러한 구분 기호를 교환해야 합니다.

필수 구성 요소는 운영 체제에 따라 다릅니다.

### <a name="linux"></a>Linux

이 빠른 시작에서는 Ubuntu Linux를 사용하고 있다고 가정합니다. 이 빠른 시작의 단계는 Ubuntu 18.04를 사용하여 테스트했습니다.

Linux에서 이 빠른 시작을 완료하려면 로컬 Linux 환경에 다음 소프트웨어를 설치해야 합니다.

`apt-get` 명령을 사용하여 **GCC**, **Git**, **cmake** 및 필요한 모든 종속성을 설치합니다.

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

`cmake` 버전이 **2.8.12** 보다 크고, **GCC** 버전이 **4.4.7** 보다 큰지 확인합니다.

```sh
cmake --version
gcc --version
```

### <a name="windows"></a>Windows

Windows에서 이 빠른 시작을 완료하려면 로컬 Windows 환경에 다음 소프트웨어를 설치합니다.

* [Visual Studio(Community, Professional 또는 Enterprise)](https://visualstudio.microsoft.com/downloads/) - Visual Studio를 [설치](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019)할 때 **C++를 사용한 데스크톱 개발** 워크로드를 포함해야 합니다.
* [Git](https://git-scm.com/download/)
* [CMake](https://cmake.org/download/)

## <a name="download-the-code"></a>코드 다운로드

이 빠른 시작에서는 Azure IoT Hub Device C SDK를 복제하고 빌드하는 데 사용할 수 있는 개발 환경을 준비합니다.

선택한 디렉터리에서 명령 프롬프트를 엽니다. 다음 명령을 실행하여 [Azure IoT C SDK 및 라이브러리](https://github.com/Azure/azure-iot-sdk-c) GitHub 리포지토리를 이 위치에 복제합니다.

```cmd\bash
git clone https://github.com/Azure/azure-iot-sdk-c.git
cd azure-iot-sdk-c
git submodule update --init
```

이 작업을 완료하는 데 몇 분 정도가 걸립니다.

## <a name="build-the-code"></a>코드 빌드

디바이스 SDK를 사용하여 포함된 샘플 코드를 빌드합니다.

1. 디바이스 SDK의 루트 폴더에 _cmake_ 하위 디렉터리를 만들고 해당 폴더로 이동합니다.

    ```cmd\bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. 다음 명령을 실행하여 SDK 및 샘플을 빌드합니다.

    ```cmd\bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key=ON -Drun_e2e_tests=OFF ..
    cmake --build .
    ```

> [!TIP]
> Windows에서 Visual Studio 2019의 `cmake` 명령으로 생성된 솔루션을 열 수 있습니다. _cmake_ 디렉터리에서 *azure_iot_sdks.sln* 프로젝트 파일을 열고 **pnp_simple_thermostat** 프로젝트를 솔루션의 시작 프로젝트로 설정합니다. 이제 Visual Studio에서 샘플을 빌드하고 디버그 모드에서 실행할 수 있습니다.

## <a name="run-the-device-sample"></a>디바이스 샘플 실행

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

샘플 구성에 대해 자세히 알아보려면 [샘플 추가 정보](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/pnp/readme.md)를 참조하세요.

IoT 허브에 원격 분석을 전송하는 IoT 플러그 앤 플레이 디바이스를 시뮬레이트하는 SDK에서 샘플 애플리케이션을 실행하려면 다음을 수행합니다.

_cmake_ 폴더에서 실행 파일이 있는 폴더로 이동하여 실행합니다.

```bash
# Bash
cd iothub_client/samples/pnp/pnp_simple_thermostat/
./pnp_simple_thermostat
```

```cmd
REM Windows
cd iothub_client\samples\pnp\pnp_simple_thermostat\Debug
.\pnp_simple_thermostat.exe
```

> [!TIP]
> Windows의 Visual Studio에서 코드 실행을 추적하려면 _pnp_simple_thermostat.c_ 파일의 `main` 함수에 중단점을 추가합니다.

이제 디바이스가 명령 및 속성 업데이트를 받을 준비가 되었으며, 허브로 원격 분석 데이터를 보내기 시작했습니다. 다음 단계를 완료하는 동안 샘플을 계속 실행하세요.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Azure IoT 탐색기를 사용하여 코드의 유효성을 검사합니다.

디바이스 클라이언트 샘플이 시작된 후 Azure IoT 탐색기 도구를 사용하여 작동하는지 확인합니다.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>코드 검토

이 샘플은 간단한 IoT 플러그 앤 플레이 온도 조절 디바이스를 구현합니다. 이 샘플에서 구현하는 모델은 IoT 플러그 앤 플레이 [구성 요소](concepts-components.md)를 사용하지 않습니다. [온도 조절 디바이스용 DTDL 모델 파일](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)은 디바이스에서 구현하는 원격 분석, 속성 및 명령을 정의합니다.

디바이스 코드는 표준 함수를 사용하여 IoT 허브에 연결합니다.

```c
deviceHandle = IoTHubDeviceClient_CreateFromConnectionString(connectionString, MQTT_Protocol)
```

디바이스는 연결 요청에서 구현하는 DTDL 모델의 모델 ID를 보냅니다. 모델 ID를 보내는 디바이스는 IoT 플러그 앤 플레이 디바이스입니다.

```c
static const char g_ModelId[] = "dtmi:com:example:Thermostat;1";

...

IoTHubDeviceClient_SetOption(deviceHandle, OPTION_MODEL_ID, modelId)
```

속성을 업데이트하고, 명령을 처리하고, 원격 분석을 보내는 코드는 IoT 플러그 앤 플레이 규칙을 사용하지 않는 디바이스의 코드와 동일합니다.

코드에서는 Parson 라이브러리를 사용하여 IoT 허브에서 전송된 페이로드의 JSON 개체를 구문 분석합니다.

```c
// JSON parser
#include "parson.h"
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 IoT 플러그 앤 플레이 디바이스를 IoT Hub에 연결하는 방법을 알아보았습니다. IoT 플러그 앤 플레이 디바이스와 상호 작용하는 솔루션을 빌드하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [방법: 디바이스에 연결 및 상호 작용](./quickstart-service-node.md)