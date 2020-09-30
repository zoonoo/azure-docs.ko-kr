---
title: Linux 또는 Windows에서 실행 되는 IoT 플러그 앤 플레이 bridge 샘플을 IoT hub에 연결 하는 방법 | Microsoft Docs
description: Iot hub에 연결 하는 Linux 또는 Windows에서 IoT 플러그 앤 플레이 브리지를 빌드하고 실행 합니다. Azure IoT 탐색기 도구를 사용하여 디바이스에서 허브로 전송된 정보를 봅니다.
author: usivagna
ms.author: ugans
ms.date: 09/22/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: b952c2663750c055ec6838bf66df31fec5a0d730
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91580415"
---
# <a name="how-to-connect-an--iot-plug-and-play-bridge-sample-running-on-linux-or-windows-to-iot-hub"></a>Linux 또는 Windows에서 실행 되는 IoT 플러그 앤 플레이 bridge 샘플을 IoT Hub에 연결 하는 방법

이 방법에서는 IoT 플러그 앤 플레이 bridge의 샘플 환경 어댑터를 빌드하고, IoT hub에 연결 하 고, Azure IoT 탐색기 도구를 사용 하 여 보내는 원격 분석을 보는 방법을 보여 줍니다. IoT 플러그 앤 플레이 브리지는 C로 작성 되며 C 용 Azure IoT 장치 SDK를 포함 합니다. 이 자습서를 마치면 IoT 플러그 앤 플레이 bridge를 실행 하 고 Azure IoT 탐색기에서 원격 분석 보고서를 볼 수 있습니다. :::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-explorer-telemetry.png" alt-text="iot 플러그 앤 플레이 bridge의 보고 된 원격 분석 (습도, 온도) 테이블을 포함 하는 Azure iot 탐색기를 보여 주는 스크린샷":::

## <a name="prerequisites"></a>필수 구성 요소

Linux 또는 Windows에서 이 빠른 시작을 실행할 수 있습니다. 이 방법 가이드의 셸 명령은 경로 구분 기호 ' '에 대 한 Windows 규칙 `\` 을 따릅니다. Linux를 따라 이동 하는 경우 ' '에 대해 이러한 구분 기호를 교환 해야 `/` 합니다.

필수 구성 요소는 운영 체제에 따라 다릅니다.

### <a name="linux"></a>Linux

이 빠른 시작에서는 Ubuntu Linux를 사용하고 있다고 가정합니다. 이 빠른 시작의 단계는 Ubuntu 18.04를 사용하여 테스트했습니다.

Linux에서 이 빠른 시작을 완료하려면 로컬 Linux 환경에 다음 소프트웨어를 설치해야 합니다.

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

Windows에서 이 빠른 시작을 완료하려면 로컬 Windows 환경에 다음 소프트웨어를 설치합니다.

* [Visual Studio(Community, Professional 또는 Enterprise)](https://visualstudio.microsoft.com/downloads/) - Visual Studio를 [설치](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2019&preserve-view=true)할 때 **C++를 사용한 데스크톱 개발** 워크로드를 포함해야 합니다.
* [Git](https://git-scm.com/download/)
* [CMake](https://cmake.org/download/)

### <a name="azure-iot-explorer"></a>Azure IoT 탐색기

이 빠른 시작의 두 번째 부분에서 샘플 디바이스와 상호 작용하려면 **Azure IoT 탐색기** 도구를 사용합니다. 운영 체제용 [최신 릴리스의 Azure IoT 탐색기를 다운로드하여 설치](./howto-use-iot-explorer.md)합니다.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

다음 명령을 실행 하 여 허브에 대 한 _IoT Hub 연결 문자열_ 을 가져옵니다. 이 연결 문자열을 기록해 두었다가 이 빠른 시작의 뒷부분에서 사용합니다.

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Azure IoT 탐색기 도구를 사용하여 IoT 허브 연결 문자열을 찾을 수도 있습니다.

다음 명령을 실행하여 허브에 추가한 디바이스에 대한 _디바이스 연결 문자열_을 가져옵니다. 이 연결 문자열을 기록해 두었다가 이 빠른 시작의 뒷부분에서 사용합니다.

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

## <a name="view-the-model"></a>모델 보기

이후 단계에서 Azure IoT 탐색기를 사용 하 여 IoT hub에 연결할 때 장치를 확인 합니다. Azure IoT 탐색기에는 장치에서 보내는 **모델 ID** 와 일치 하는 모델 파일의 로컬 복사본이 필요 합니다. 모델 파일을 통해 IoT 탐색기는 장치에서 구현 하는 원격 분석, 속성 및 명령을 표시할 수 있습니다.

아래 단계에서 코드를 다운로드 하면 폴더 아래에 샘플 모델 파일이 포함 됩니다 `pnpbridge/docs/schema` . Azure IoT 탐색기를 준비 하려면:

1. *모델*이라는 폴더를 로컬 머신에 만듭니다.
1. [EnvironmentalSensor.js](https://aka.ms/iot-pnp-bridge-env-model) 를 보고 JSON 파일을 *모델* 폴더에 저장 합니다.
1. [RootBridgeSampleDevice.js를](https://aka.ms/iot-pnp-bridge-root-model) 보고 JSON 파일을 *모델* 폴더에 저장 합니다.

## <a name="download-the-code"></a>코드 다운로드

선택한 디렉터리에서 명령 프롬프트를 엽니다. 다음 명령을 실행 하 여 [IoT 플러그 앤 플레이 bridge](https://aka.ms/iotplugandplaybridge) GitHub 리포지토리를이 위치에 복제 합니다.

```cmd
git clone https://github.com/Azure/AzurePnPBridgePreview.git
```

IoT 플러그 앤 플레이 브리지 리포지토리를 컴퓨터에 복제 한 후 관리 cmd 프롬프트를 열고 복제 된 리포지토리의 디렉터리로 이동 합니다.

```cmd
cd pnpbridge
git submodule update --init --recursive
```

이 작업을 완료하는 데 몇 분 정도가 걸립니다.

>[!NOTE]
> Git 복제 하위 모듈 업데이트 실패와 관련 된 문제가 발생 하는 경우이 문제는 Windows 파일 경로 및 git의 알려진 문제입니다 .를 참조 하십시오 https://github.com/msysgit/git/pull/110 . 다음 명령을 실행 하 여 문제를 해결할 수 있습니다. `git config --system core.longpaths true`

## <a name="setting-up-the-configuration-json"></a>구성 JSON 설정

IoT 플러그 앤 플레이 브리지 리포지토리를 컴퓨터에 복제 한 후에 `pnpbridge/docs/schema` [구성 JSON](https://aka.ms/iot-pnp-bridge-env-config) 또는 `config.json` 브리지의 환경적 센서 샘플을 찾을 수 있는 복제 된 리포지토리의 디렉터리로 이동 합니다. 구성 파일에 대 한 자세한 내용은 [IoT 플러그 앤 플레이 bridge 개념 문서](concepts-iot-pnp-bridge.md)에서 확인할 수 있습니다.

필드의 경우 `root-_interface_model_id` 장치에 대 한 모델을 식별 하는 IoT 플러그 앤 플레이 모델 ID를 복사 해야 합니다. 이 예제에서는 `dtmi:com:example:SampleDevice;1`입니다. '에 있는 파일의 **pnp_bridge_parameters** 노드 아래에서 다음 매개 변수를 수정 합니다 `config.json` .

  연결 문자열 사용 (참고: symmetric_key은 연결 문자열의 SAS 키와 일치 해야 함):

  ```JSON
    {
      "connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "[CONNECTION STRING]",
        "root_interface_model_id": "[To fill in]",
        "auth_parameters" : {
          "auth_type" : "symmetric_key",
          "symmetric_key" : "[SYMMETRIC KEY]"
        }
      }
    }
  }
  ```

 파일을 채운 후에는 `config.json` 다음과 유사 합니다.

   ```JSON
    {
      "connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "[CONNECTION STRING]",
        "root_interface_model_id": "dtmi:com:example:SampleDevice;1",
        "auth_parameters" : {
          "auth_type" : "symmetric_key",
          "symmetric_key" : "[SYMMETRIC KEY]"
        }
      }
    }
  }
```

 브리지를 작성 한 후에는이를 `config.json` 브리지와 동일한 디렉터리에 저장 하거나 실행 될 때 경로를 지정 해야 합니다.

## <a name="build-the-iot-plug-and-play-bridge"></a>IoT 플러그 앤 플레이 브리지 빌드

리포지토리 디렉터리의 *pnpbridge* 폴더로 이동 합니다.

Windows의 경우 [Visual Studio에 대 한 개발자 명령 프롬프트](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)에서 다음을 실행 합니다.

```cmd
cd scripts\windows
build.cmd
```

Linux의 경우 유사 하 게 다음을 실행 합니다.

```bash
cd scripts/linux
./setup.sh
./build.sh
```

>[!TIP]
>Windows에서 Visual Studio 2019의 ctocommand에 의해 생성 된 솔루션을 열 수 있습니다. Cmake 디렉터리에서 *azure_iot_pnp_bridge .sln* 프로젝트 파일을 열고 *pnpbridge_bin* 프로젝트를 솔루션의 시작 프로젝트로 설정 합니다. 이제 Visual Studio에서 샘플을 빌드하고 디버그 모드에서 실행할 수 있습니다.

## <a name="start-the-iot-plug-and-play-bridge"></a>IoT 플러그 앤 플레이 브리지 시작

 *Pnpbridge* 폴더로 이동 하 고 명령 프롬프트에서 다음을 실행 하 여 환경 센서에 대 한 IoT 플러그 앤 플레이 bridge 샘플을 시작 합니다.

```bash
 cd cmake/pnpbridge_x86/src/adaptors/samples/environmental_sensor/
./pnpbridge_environmentalsensor

```

```cmd
REM Windows
cd cmake\pnpbridge_x86\src\adaptors\samples\environmental_sensor
Debug\pnpbridge_environmentalsensor.exe
```

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Azure IoT 탐색기를 사용하여 코드의 유효성을 검사합니다.

디바이스 클라이언트 샘플이 시작된 후 Azure IoT 탐색기 도구를 사용하여 작동하는지 확인합니다.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 IoT 플러그 앤 플레이 디바이스를 IoT Hub에 연결하는 방법을 알아보았습니다. IoT 플러그 앤 플레이 디바이스와 상호 작용하는 솔루션을 빌드하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [IoT 플러그 앤 플레이 브리지 란?](./concepts-iot-pnp-bridge.md)
* [IoT 플러그 앤 플레이 브리지에 대 한 GitHub 개발자 참조를 참조 하세요.](https://aka.ms/iot-pnp-bridge-dev-doc)
* [GitHub의 IoT 플러그 앤 플레이 브리지](https://aka.ms/iotplugandplaybridge)
