---
title: IoT 플러그 앤 플레이 미리 보기 샘플 디바이스 코드를 IoT Hub에 연결(Linux) | Microsoft Docs
description: Linux에서 IoT Hub에 연결하는 IoT 플러그 앤 플레이 미리 보기 샘플 디바이스 코드를 빌드하고 실행합니다. Azure CLI를 사용하여 디바이스에서 허브로 전송된 정보를 봅니다.
author: dominicbetts
ms.author: dobett
ms.date: 12/23/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 8134c0a97f6350cfa2cf616695c5990618455393
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531271"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-linux-to-iot-hub-c-linux"></a>빠른 시작: Linux에서 실행되는 샘플 IoT 플러그 앤 플레이 미리 보기 디바이스 애플리케이션을 IoT Hub(C Linux)에 연결

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

이 빠른 시작에서는 Linux에서 샘플 IoT 플러그 앤 플레이 디바이스 애플리케이션을 빌드하고, IoT Hub에 연결하고, Azure CLI를 사용하여 디바이스에서 허브로 전송된 정보를 확인하는 방법을 보여 줍니다. 샘플 애플리케이션은 C로 작성되었으며 C용 Azure IoT 디바이스 SDK에 포함되어 있습니다. 솔루션 개발자는 디바이스 코드를 볼 필요 없이 Azure CLI를 사용하여 IoT 플러그 앤 플레이 디바이스의 기능을 이해할 수 있습니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작에서는 Ubuntu Linux를 사용하고 있다고 가정합니다. 이 자습서의 단계는 Ubuntu 18.04를 사용하여 테스트했습니다.

이 빠른 시작을 완료하려면 로컬 Linux 머신에 다음 소프트웨어를 설치해야 합니다.

`apt-get` 명령을 사용하여 **GCC**, **Git**, **cmake** 및 모든 종속성을 설치합니다.

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

`cmake` 버전이 **2.8.12**보다 크고, **GCC** 버전이 **4.4.7**보다 큰지 확인합니다.

```sh
cmake --version
gcc --version
```

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

## <a name="prepare-the-development-environment"></a>개발 환경 준비

이 빠른 시작에서는 Azure IoT Hub Device C SDK를 복제하고 빌드하는 데 사용할 수 있는 개발 환경을 준비합니다.

선택한 디렉터리에서 명령 프롬프트를 엽니다. 다음 명령을 실행하여 [Azure IoT C SDK 및 라이브러리](https://github.com/Azure/azure-iot-sdk-c) GitHub 리포지토리를 이 위치에 복제합니다.

```bash
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

이 작업을 완료하는 데 몇 분 정도가 걸립니다.

## <a name="build-the-code"></a>코드 빌드

디바이스 SDK를 사용하여 포함된 샘플 코드를 빌드합니다. 빌드하는 애플리케이션은 IoT Hub에 연결하는 디바이스를 시뮬레이션합니다. 이 애플리케이션은 원격 분석 데이터 및 속성을 보내고 명령을 수신합니다.

1. 디바이스 SDK 루트 폴더에 `cmake` 하위 디렉터리를 만들고 해당 폴더로 이동합니다.

    ```bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. 다음 명령을 실행하여 디바이스 SDK 및 생성된 코드 스텁을 빌드합니다.

    ```bash
    cmake ..
    cmake --build .
    ```

## <a name="update-your-model-repository"></a>모델 리포지토리 업데이트

샘플을 실행하기 전에 디바이스 기능 모델 및 인터페이스 정의를 회사 모델 리포지토리에 추가합니다.

1. Microsoft 회사 또는 학교 계정이나 Microsoft 파트너 ID(있는 경우)를 사용하여 [IoT용 Azure Certified](https://preview.catalog.azureiotsolutions.com) 포털에 로그인합니다.

1. **회사 리포지토리**를 선택한 다음, **기능 모델**을 선택합니다.

1. **새로 만들기**를 선택한 다음, **업로드**를 선택합니다.

1. 디바이스 SDK 루트 폴더의 `digitaltwin_client/samples` 폴더에서 `SampleDevice.capabilitymodel.json` 파일을 선택합니다. **열기**를 선택하고 **저장**을 선택하여 리포지토리에 모델 파일을 업로드합니다.

1. **회사 리포지토리**를 선택한 다음, **인터페이스**를 선택합니다.

1. **새로 만들기**를 선택한 다음, **업로드**를 선택합니다.

1. 디바이스 SDK 루트 폴더의 `digitaltwin_client/samples/digitaltwin_sample_environmental_sensor` 폴더에서 `EnvironmentalSensor.interface.json` 파일을 선택합니다. **열기**를 선택하고 **저장**을 선택하여 리포지토리에 인터페이스 파일을 업로드합니다.

1. **회사 리포지토리**를 선택한 다음, **연결 문자열**을 선택합니다. 이 빠른 시작 뒷부분에서 사용할 때 _첫 번째 회사 모델 리포지토리 연결 문자열_을 기록해 둡니다.

## <a name="run-the-device-sample"></a>디바이스 샘플 실행

SDK에서 샘플 애플리케이션을 실행하여 IoT Hub에 원격 분석을 전송하는 IoT 플러그 앤 플레이 디바이스를 시뮬레이트합니다. 샘플 애플리케이션을 실행하려면 다음을 수행합니다.

1. 다음과 같이 `cmake` 폴더에서 실행 파일이 있는 폴더로 이동합니다.

    ```bash
    cd digitaltwin_client/samples/digitaltwin_sample_device
    ```

1. 다음과 같이 실행 파일을 실행합니다.

    ```bash
    ./digitaltwin_sample_device "<YourDeviceConnectionString>"
    ```

이제 디바이스가 명령 및 속성 업데이트를 받을 준비가 되었으며, 허브로 원격 분석 데이터를 보내기 시작했습니다. 다음 단계를 완료하는 동안 샘플을 계속 실행하세요.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>Azure IoT CLI를 사용하여 코드의 유효성을 검사합니다.

디바이스 클라이언트 샘플이 시작된 후 Azure CLI에서 작동하는지 확인합니다.

다음 명령을 사용하여 샘플 디바이스가 보내는 원격 분석을 볼 수 있습니다. 출력에 원격 분석이 표시되기까지 1~ 2분 정도 기다려야 할 수 있습니다.

```azurecli-interactive
az iot dt monitor-events --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

다음 명령을 사용하여 디바이스에서 보낸 속성을 볼 수 있습니다.

```azurecli-interactive
az iot dt list-properties --hub-name <YourIoTHubName> --device-id <YourDeviceID> --interface sensor --source private --repo-login "<YourCompanyModelRepositoryConnectionString>"
```
[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 IoT 플러그 앤 플레이 디바이스를 IoT Hub에 연결하는 방법을 알아보았습니다. IoT 플러그 앤 플레이 디바이스와 상호 작용하는 솔루션을 빌드하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [방법: 디바이스에 연결 및 상호 작용](howto-develop-solution.md)
