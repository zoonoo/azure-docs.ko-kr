---
title: IoT 플러그 앤 플레이 미리 보기 디바이스 만들기(Windows) | Microsoft Docs
description: 디바이스 기능 모델을 사용하여 디바이스 코드를 만듭니다. 그런 다음, 디바이스 코드를 실행하고 디바이스가 IoT Hub에 연결하는지 확인합니다.
author: miagdp
ms.author: miag
ms.date: 12/30/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: b447a0328de210caeb23a1beb91c532853c180bc
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75550471"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-windows"></a>빠른 시작: 디바이스 기능 모델을 사용하여 IoT 플러그 앤 플레이 미리 보기 디바이스 만들기(Windows)

[!INCLUDE [iot-pnp-quickstarts-1-selector.md](../../includes/iot-pnp-quickstarts-1-selector.md)]

_DCM(디바이스 기능 모델)_ 은 IoT 플러그 앤 플레이 디바이스의 기능을 설명합니다. DCM은 종종 제품 SKU와 연결됩니다. DCM에 정의된 기능은 재사용 가능한 인터페이스로 구성됩니다. DCM에서 기본 디바이스 코드를 생성할 수 있습니다. 이 빠른 시작에서는 Windows에서 VS Code를 사용하여 DCM을 사용하는 IoT 플러그 앤 플레이 디바이스를 만드는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작을 완료하려면 로컬 머신에 다음 소프트웨어를 설치해야 합니다.

* **C++ 빌드 도구** 및 **NuGet 패키지 관리자 구성 요소** 워크로드를 사용하여 [Visual Studio용 도구를 빌드](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16)합니다. 또는 동일한 워크로드가 설치된 [Visual Studio(Community, Professional 또는 Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 또는 2015가 이미 있는 경우
* [Git](https://git-scm.com/download/)
* [CMake](https://cmake.org/download/)
* [Visual Studio Code](https://code.visualstudio.com/)

### <a name="install-azure-iot-tools"></a>Azure IoT Tools 설치

[VS Code용 Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 확장을 설치하려면 다음 단계를 수행합니다.

1. VS Code에서 **확장** 탭을 선택합니다.
1. **Azure IoT Tools**를 검색합니다.
1. **설치**를 선택합니다.

### <a name="install-the-azure-iot-explorer"></a>Azure IoT 탐색기 설치

최신 업데이트에 대한 "자산"에서 .msi 파일을 선택하여 도구의 [리포지토리](https://github.com/Azure/azure-iot-explorer/releases) 페이지에서 최신 버전의 **Azure IoT 탐색기**를 다운로드하고 설치합니다.

### <a name="get-the-connection-string-for-your-company-model-repository"></a>회사 모델 리포지토리의 연결 문자열 가져오기

Microsoft 회사 또는 학교 계정으로 로그인하거나 Microsoft 파트너 ID로 로그인할 때(갖고 있는 경우) [IoT용 Azure Certified 포털](https://preview.catalog.azureiotsolutions.com)에서 _회사 모델 리포지토리 연결 문자열_을 찾을 수 있습니다. 로그인한 후에는 **회사 리포지토리**를 선택한 다음, **연결 문자열**을 선택합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

다음 명령을 실행하여 허브의 _IoT 허브 연결 문자열_을 가져옵니다(나중에 사용하기 위해 참고).

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>개발 환경 준비

이 빠른 시작에서는 [Vcpkg](https://github.com/microsoft/vcpkg) 라이브러리 관리자를 사용하여 개발 환경에 Azure IoT C 디바이스 SDK를 설치합니다.

1. 명령 프롬프트를 엽니다. 다음 명령을 실행하여 Vcpkg를 설치합니다.

    ```cmd
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    그런 다음, 사용자 전체 [통합](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)을 연결하려면 다음을 실행합니다(참고: 처음 사용할 때 관리자가 필요함).

    ```cmd
    .\vcpkg.exe integrate install
    ```

1. 다음과 같이 Azure IoT C 디바이스 SDK Vcpkg를 설치합니다.

    ```cmd
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="author-your-model"></a>모델 작성

이 빠른 시작에서는 기존 샘플 디바이스 기능 모델 및 연결된 인터페이스를 사용합니다.

1. 로컬 드라이브에 `pnp_app` 디렉터리를 만듭니다. 이 폴더는 디바이스 모델 파일 및 디바이스 코드 스텁에 사용됩니다.

1. [디바이스 기능 모델 및 인터페이스 샘플 파일](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/SampleDevice.capabilitymodel.json) 및 [인터페이스 샘플](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/EnvironmentalSensor.interface.json)을 다운로드하고 파일을 `pnp_app` 폴더에 저장합니다.

    > [!TIP]
    > GitHub에서 파일을 다운로드하려면 파일을 탐색하고, **Raw**를 마우스 오른쪽 단추로 클릭한 다음, **다른 이름으로 링크 저장**을 선택합니다.

1. VS Code에서 `pnp_app` 폴더를 엽니다. 다음과 같이 Intellisense를 사용하여 파일을 볼 수 있습니다.

    ![디바이스 기능 모델](media/quickstart-create-pnp-device/dcm.png)

1. 다운로드한 파일에서 `@id` 및 `schema` 필드의 `<YOUR_COMPANY_NAME_HERE>`를 고유한 값으로 바꿉니다. a-z, A-Z, 0-9 및 밑줄 문자만 사용합니다. 자세한 내용은 [디지털 쌍 식별자 형식](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format)을 참조하세요.

## <a name="generate-the-c-code-stub"></a>C 코드 스텁 생성

이제 DCM 및 관련 인터페이스가 있으므로, 모델을 구현하는 디바이스 코드를 생성할 수 있습니다. VS Code에서 C 코드 스텁을 생성하는 방법은 다음과 같습니다.

1. VS Code에서 `pnp_app` 폴더를 열고, **Ctrl+Shift+P**를 사용하여 명령 팔레트를 열고, **IoT 플러그 앤 플레이**를 입력하고, **디바이스 코드 스텁 생성**을 선택합니다.

    > [!NOTE]
    > IoT 플러그 앤 플레이 CodeGen CLI를 처음 사용하는 경우 자동으로 다운로드하고 설치하는 데 몇 초 정도 걸립니다.

1. 디바이스 코드 스텁을 생성하는 데 사용할 **SampleDevice.capabilitymodel.json** 파일을 선택합니다.

1. 프로젝트 이름 **sample_device**를 입력합니다. 이것은 디바이스 애플리케이션의 이름입니다.

1. 언어로 **ANSI C**를 선택합니다.

1. 연결 방법으로 **IoT Hub 디바이스 연결 문자열을 통해**를 선택합니다.

1. 프로젝트 템플릿으로 **Windows의 CMake 프로젝트**를 선택합니다.

1. 디바이스 SDK를 포함하는 방법으로 **Via Vcpkg**를 선택합니다.

1. **sample_device**라는 새 폴더가 DCM 파일이 있는 동일한 위치에 만들어지며, 그 안에는 생성된 디바이스 코드 스텁 파일이 있습니다. VS Code는 이 내용을 표시하기 위해 새 창을 엽니다.
    ![디바이스 코드](media/quickstart-create-pnp-device/device-code.png)

## <a name="build-and-run-the-code"></a>코드 빌드 및 실행

디바이스 SDK 소스 코드를 사용하여 생성된 디바이스 코드 스텁을 빌드합니다. 빌드하는 애플리케이션은 IoT Hub에 연결하는 디바이스를 시뮬레이션합니다. 이 애플리케이션은 원격 분석 데이터 및 속성을 보내고 명령을 수신합니다.

1. `sample_device` 폴더에 `cmake` 하위 디렉터리를 만들고 해당 폴더로 이동합니다.

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. 다음 명령을 실행하여 생성된 코드 스텁을 빌드합니다(자리 표시자를 Vcpkg 리포지토리의 디렉터리로 바꿈).

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build .
    ```
    
    > [!NOTE]
    > Visual Studio 2017 또는 2015를 사용하는 경우 사용 중인 빌드 도구에 따라 CMake 생성기를 지정해야 합니다.
    >```cmd
    ># Either
    >cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    ># or
    >cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    >```

    > [!NOTE]
    > cmake에서 C++ 컴파일러를 찾을 수 없는 경우 이전 명령을 실행할 때 빌드 오류가 발생합니다. 이 경우에는 [Visual Studio 명령 프롬프트](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)에서 이 명령을 실행합니다.

1. 빌드가 성공적으로 완료되면 애플리케이션을 실행하고 IoT Hub 디바이스 연결 문자열을 매개 변수로 전달합니다.

    ```cmd\sh
    .\Debug\sample_device.exe "<YourDeviceConnectionString>"
    ```

1. 디바이스 애플리케이션이 IoT Hub로 데이터를 보내기 시작합니다.

    ![실행 중인 디바이스 앱](media/quickstart-create-pnp-device/device-app-running.png)

## <a name="validate-the-code"></a>코드 유효성 검사

### <a name="publish-device-model-files-to-model-repository"></a>디바이스 모델 파일을 모델 리포지토리에 게시

**Azure IoT 탐색기**를 사용하여 디바이스 코드의 유효성을 검사하려면 파일을 모델 리포지토리에 게시해야 합니다.

1. VS Code에서 `pnp_app` 폴더를 열어 두고 **Ctrl+Shift+P**를 사용하여 명령 팔레트를 연 다음, **IoT 플러그 앤 플레이: 모델 리포지토리에 파일 제출**을 입력하고 선택합니다.

1. `SampleDevice.capabilitymodel.json` 및 `EnvironmentalSensor.interface.json` 파일을 선택합니다.

1. 회사 모델 리포지토리 연결 문자열을 입력합니다.

    > [!NOTE]
    > 연결 문자열은 리포지토리에 처음 연결할 때만 필요합니다.

1. VS Code 출력 창과 알림에서 파일이 성공적으로 게시되었는지 확인할 수 있습니다.

    > [!NOTE]
    > 디바이스 모델 파일을 게시할 때 오류가 발생하는 경우 **IoT 플러그 앤 플레이: 모델 리포지토리 로그아웃** 명령을 사용하여 로그아웃하고 단계를 다시 진행해볼 수 있습니다.

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Azure IoT 탐색기를 사용하여 코드의 유효성을 검사합니다.

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. 회사 리포지토리를 추가하려면 **설정**, **+ 모듈 정의 원본 새로 만들기**, **회사 리포지토리**를 차례로 선택합니다. 회사 모델 리포지토리 연결 문자열을 추가하고 **저장 수 연결**을 선택합니다.

1. **디바이스** 개요 페이지로 돌아가서 이전에 만든 디바이스 ID를 찾습니다. 디바이스 애플리케이션이 명령 프롬프트에서 계속 실행 중인 상태에서 Azure IoT 탐색기에 디바이스의 **연결 상태**가 _연결됨_으로 보고되고 있는지 확인합니다(그렇지 않은 경우 연결될 때까지 **새로 고침**을 누릅니다). 디바이스를 선택하여 자세한 내용을 봅니다.

1. ID가 **urn:<YOUR_INTERFACE_NAME>:EnvironmentalSensor:1**인 인터페이스를 확장하여 IoT 플러그 앤 플레이 기본 형식인 속성, 명령 및 원격 분석을 살펴봅니다. 표시되는 인터페이스 이름은 모델을 작성할 때 입력한 이름입니다.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 DCM을 사용하여 IoT 플러그 앤 플레이 디바이스를 만드는 방법을 알아보았습니다.

DCM에 대한 자세한 내용과 나만의 모델을 만드는 방법에 대한 자세한 내용을 보려면 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [자습서: Visual Studio Code를 사용하여 디바이스 기능 모델 만들기 및 테스트](tutorial-pnp-visual-studio-code.md)
