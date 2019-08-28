---
title: IoT 플러그 앤 플레이 미리 보기 샘플 디바이스 코드를 IoT Hub에 연결 | Microsoft Docs
description: IoT Hub에 연결하는 IoT 플러그 앤 플레이 미리 보기 샘플 디바이스 코드를 빌드하고 실행합니다. Azure IoT 탐색기 도구를 사용하여 디바이스에서 허브로 전송된 정보를 봅니다.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: b233fcecfe80d1ce4464d2d02fdddb188f9265a7
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69881582"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub"></a>빠른 시작: 샘플 IoT 플러그 앤 플레이 미리 보기 디바이스 애플리케이션을 IoT Hub에 연결

이 빠른 시작에서는 샘플 IoT 플러그 앤 플레이 디바이스 애플리케이션을 빌드하고, IoT Hub에 연결하고, Azure IoT 탐색기 도구를 사용하여 디바이스에서 허브로 전송된 정보를 확인하는 방법을 보여줍니다. 샘플 애플리케이션은 C로 작성되었으며 C용 Azure IoT 디바이스 SDK에 포함되어 있습니다. 솔루션 개발자는 디바이스 코드를 볼 필요 없이 Azure IoT 탐색기 도구를 사용하여 IoT 플러그 앤 플레이 디바이스의 기능을 이해할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 로컬 머신에 다음 소프트웨어를 설치해야 합니다.

* [Visual Studio(Community, Professional 또는 Enterprise)](https://visualstudio.microsoft.com/downloads/) - Visual Studio를 설치할 때 **NuGet 패키지 관리자** 구성 요소와 **C++를 사용한 데스크톱 개발** 워크로드를 포함해야 합니다.
* [Git](https://git-scm.com/download/)
* [CMake](https://cmake.org/download/)

### <a name="install-the-azure-iot-explorer"></a>Azure IoT 탐색기 설치

[최신 릴리스](https://github.com/Azure/azure-iot-explorer/releases) 페이지에서 Azure IoT 탐색기 도구를 다운로드하여 설치합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>IoT Hub 준비

또한 이 빠른 시작을 완료하려면 Azure 구독의 Azure IoT Hub가 필요합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

다음과 같이 Azure CLI용 Microsoft Azure IoT 확장을 추가합니다.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

다음 명령을 실행하여 IoT Hub에 디바이스 ID를 만듭니다. **YourIoTHubName** 및 **YourDevice** 자리 표시자를 실제 이름으로 바꿉니다. IoT Hub가 없는 경우 [다음 지침에 따라 새로 만듭니다](../iot-hub/iot-hub-create-using-cli.md).

```azurecli-interactive
az iot hub device-identity create --hub-name [YourIoTHubName] --device-id [YourDevice]
```

다음 명령을 실행하여 방금 등록한 디바이스의 _디바이스 연결 문자열_을 가져옵니다.

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name [YourIoTHubName] --device-id [YourDevice] --output table
```

다음 명령을 실행하여 허브의 _IoT Hub 연결 문자열_을 가져옵니다.

```azurecli-interactive
az iot hub show-connection-string --hub-name [YourIoTHubName] --output table
```

## <a name="prepare-the-development-environment"></a>개발 환경 준비

### <a name="get-azure-iot-device-sdk-for-c"></a>C용 Azure IoT 디바이스 SDK 받기

이 빠른 시작에서는 Azure IoT C 디바이스 SDK를 복제하고 빌드하는 데 사용할 수 있는 개발 환경을 준비합니다.

명령 프롬프트를 엽니다. 다음 명령을 실행하여 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 리포지토리를 복제합니다.

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

이 작업을 완료하는 데 몇 분 정도가 걸립니다.

## <a name="build-the-code"></a>코드 빌드

빌드하는 애플리케이션은 IoT Hub에 연결하는 디바이스를 시뮬레이션합니다. 이 애플리케이션은 원격 분석 데이터 및 속성을 보내고 명령을 수신합니다.

1. 디바이스 SDK 루트 폴더에 `cmake` 하위 디렉터리를 만들고 해당 폴더로 이동합니다.

    ```cmd\sh
    cd <root folder>\azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. 다음 명령을 실행하여 디바이스 SDK 및 생성된 코드 스텁을 빌드합니다.

    ```cmd\sh
    cmake ..
    cmake --build . -- /m /p:Configuration=Release
    ```

    > [!NOTE]
    > cmake에서 C++ 컴파일러를 찾을 수 없는 경우 이전 명령을 실행할 때 빌드 오류가 발생합니다. 이 경우에는 [Visual Studio 명령 프롬프트](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)에서 이 명령을 실행합니다.

## <a name="run-the-device-sample"></a>디바이스 샘플 실행

IoT Hub 디바이스 연결 문자열을 매개 변수로 전달하여 애플리케이션을 실행합니다.

```cmd\sh
cd digitaltwin_client\samples\digitaltwin_sample_device\Release
copy ..\EnvironmentalSensor.interface.json .
digitaltwin_sample_device.exe "[IoT Hub device connection string]"
```

디바이스 애플리케이션이 IoT Hub로 데이터를 보내기 시작합니다.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Azure IoT 탐색기를 사용하여 코드의 유효성을 검사합니다.

1. Azure IoT 탐색기를 열면 **앱 구성** 페이지가 표시됩니다.

1. IoT Hub 연결 문자열을 입력하고 **연결**을 클릭합니다.

1. 연결되면 디바이스 개요 페이지가 표시됩니다.

1. 회사 리포지토리를 추가하려면 **설정**, **+ 새로 만들기**, **연결된 디바이스**를 차례로 선택합니다.

1. 디바이스 개요 페이지에서 이전에 만든 디바이스 ID를 찾아 선택하고 세부 정보를 살펴봅니다.

1. ID가 **urn:YOUR_COMPANY_NAME_HERE:EnvironmentalSensor:1**인 인터페이스를 확장하여 IoT 플러그 앤 플레이 기본 형식인 속성, 명령 및 원격 분석을 살펴봅니다.

1. **원격 분석** 페이지를 선택하여 디바이스에서 보내는 원격 분석 데이터를 살펴봅니다.

1. **속성(쓰기 불가능)** 페이지를 선택하여 디바이스에서 보고한 쓰기 불가능 속성을 살펴봅니다.

1. **속성(쓰기 가능)** 페이지를 선택하여 업데이트할 수 있는 쓰기 가능 속성을 살펴봅니다.

1. 속성 **이름**을 확장하고, 새 이름으로 업데이트하고, **쓰기 가능한 속성 업데이트**를 선택합니다. 

1. 새 이름이 **보고된 속성** 열에 표시되는 것을 보려면 페이지 맨 위에 있는 **새로 고침** 단추를 클릭합니다.

1. **명령** 페이지를 선택하여 디바이스에서 지원하는 모든 명령을 살펴봅니다.

1. **blink** 명령을 확장하고 깜박임 시간 간격을 새로 설정합니다. **명령 보내기**를 선택하여 디바이스에서 명령을 호출합니다.

1. 시뮬레이션된 디바이스로 이동하여 명령이 예상대로 실행되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 IoT 플러그 앤 플레이 디바이스를 IoT Hub에 연결하는 방법을 알아보았습니다. IoT 플러그 앤 플레이 디바이스와 상호 작용하는 솔루션을 빌드하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [방법: IoT 플러그 앤 플레이 미리 보기 디바이스에 연결하고 상호 작용](howto-develop-solution.md)
