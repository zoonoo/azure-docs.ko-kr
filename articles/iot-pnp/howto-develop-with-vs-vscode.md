---
title: Visual Studio 및 Visual Studio Code를 사용 하 여 IoT 플러그 앤 플레이 미리 보기 장치 빌드 | Microsoft Docs
description: Visual Studio 및 Visual Studio Code를 사용 하 여 IoT 플러그 앤 플레이 장치 모델을 신속 하 게 작성 하 고 장치 코드를 구현할 수 있습니다.
author: liydu
ms.author: liydu
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: b4325aa6f379dc0b281d06cb593c28448698c71b
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531329"
---
# <a name="use-visual-studio-and-visual-studio-code-to-build-iot-plug-and-play-devices"></a>Visual Studio 및 Visual Studio Code를 사용 하 여 IoT 플러그 앤 플레이 장치 빌드

Visual Studio Code 용 Azure IoT 도구는 DCM (장치 기능 모델) 및 인터페이스를 작성 하 고, 모델 리포지토리에 게시 하 고, 기본 C 코드를 생성 하 여 장치 응용 프로그램을 구현 하는 통합 환경을 제공 합니다.

이 아티클에서는 다음을 수행하는 방법을 보여줍니다.

- 장치 코드 및 응용 프로그램 프로젝트를 생성 합니다.
- 장치 프로젝트에서 생성 된 코드를 사용 합니다.
- 기본 코드를 다시 생성 하 여 반복 합니다.

VS Code를 사용 하 여 IoT 장치를 개발 하는 방법에 대 한 자세한 내용은 [https://github.com/microsoft/vscode-iot-workbench](https://github.com/microsoft/vscode-iot-workbench)을 참조 하세요.

## <a name="prerequisites"></a>필수 조건

[Visual Studio Code](https://code.visualstudio.com/)를 설치합니다.

VS Code에 확장 팩을 설치 하려면 다음 단계를 따르십시오.

1. VS Code에서 **확장** 탭을 선택 합니다.
1. Marketplace에서 **Azure IoT 도구** 를 검색 하 고 설치 합니다.

## <a name="generate-device-code-and-project"></a>장치 코드 및 프로젝트 생성

VS Code에서 **Ctrl + Shift + P** 를 사용 하 여 명령 팔레트를 열고 **IoT 플러그 앤 플레이**를 입력 한 다음 **장치 코드 스텁 생성** 을 선택 하 여 기본 코드와 프로젝트 형식을 구성 합니다. 다음 목록에서는 각 단계에 대해 자세히 설명 합니다.

- **코드를 생성 하는 데 사용 되는 DCM 파일**입니다. 기본 장치 코드를 생성 하려면 구현 하는 DCM 및 인터페이스 파일이 포함 된 폴더를 엽니다. 코드 생성기가 DCM에서 필요로 하는 인터페이스를 찾을 수 없는 경우 필요에 따라 모델 리포지토리에서 다운로드 합니다.

- **장치 코드 언어**입니다. 현재 코드 생성기는 ANSI C만 지원 합니다.

- **프로젝트 이름**입니다. 프로젝트 이름은 생성 된 코드 및 기타 프로젝트 파일의 폴더 이름으로 사용 됩니다. 폴더는 기본적으로 DCM 파일 옆에 배치 됩니다. DCM을 업데이트 하 고 장치 코드를 다시 생성할 때마다 생성 된 코드 폴더를 수동으로 복사할 필요가 없도록 하려면 DCM 파일을 프로젝트 폴더와 동일한 폴더에 보관 합니다.

- **Azure IoT에 연결 하는 방법**입니다. 생성 된 파일에는 Azure IoT Hub에 연결 하도록 장치를 구성 하는 코드도 포함 되어 있습니다. [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) 에 직접 연결 하거나 [장치 프로 비전 서비스](https://docs.microsoft.com/azure/iot-dps)를 사용 하도록 선택할 수 있습니다.

    - **Via IoT Hub 장치 연결 문자열**: IoT Hub에 직접 연결 하기 위해 장치 응용 프로그램에 대 한 장치 연결 문자열을 지정 합니다.
    - **Dps 대칭 키를 통해**: IoT Hub에 연결 하거나 DPS를 사용 하 여 IoT Central 하는 데 필요한 장치 응용 프로그램의 **id 범위**, **대칭 키** 및 **장치 id** 를 지정 합니다.

- **프로젝트 형식**입니다. 또한 코드 생성기는 CMake 또는 Arduino 프로젝트를 생성 합니다. 현재 지원 되는 프로젝트 형식은 다음과 같습니다.

    - **Windows의 Cmake 프로젝트**: Windows에서 [cmake](https://cmake.org/) 를 빌드 시스템으로 사용 하는 장치 프로젝트의 경우 이 옵션은 C 코드와 동일한 폴더에 장치 SDK 구성을 사용 하 여 `CMakeLists.txt`를 생성 합니다.
    - **Linux의 Cmake 프로젝트**: Linux에서 [cmake](https://cmake.org/) 를 빌드 시스템으로 사용 하는 장치 프로젝트용입니다. 이 옵션은 C 코드와 동일한 폴더에 장치 SDK 구성을 사용 하 여 `CMakeLists.txt`를 생성 합니다.
    - **MXChip Iot devkit 프로젝트**: [MXChip iot devkit](https://aka.ms/iot-devkit) 장치에서 실행 되는 장치 프로젝트를 위한 것입니다. 이 옵션은 VS Code 또는 Arduino IDE에서 IoT DevKit 장치를 빌드하고 실행 하는 [데 사용할](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) 수 있는 Arduino 프로젝트를 생성 합니다.

- **장치 SDK 유형**입니다. CMake 프로젝트 형식을 선택 하는 경우 생성 된 코드에 Azure IoT C 장치 SDK를 `CMakeLists.txt`포함 하는 방법을 구성 하는 단계는 다음과 같습니다.

    - **소스 코드**사용: 생성 된 코드는 [장치 SDK 소스 코드](https://github.com/Azure/azure-iot-sdk-c) 를 사용 하 여에 포함 하 고 함께 빌드합니다. 장치 SDK 소스 코드를 사용자 지정한 경우에 권장 됩니다.
    - **Vcpkg**사용: 생성 된 코드는 [장치 SDK Vcpkg](https://github.com/microsoft/vcpkg/tree/master/ports/azure-iot-sdk-c) 를 사용 하 여에 포함 하 고 함께 빌드합니다. 이는 Windows, Linux 또는 macOS를 실행 하는 장치에 권장 되는 방법입니다.

    > [!NOTE]
    > Azure IoT C 장치 SDK Vcpkg에 대 한 macOS 지원이 진행 중입니다.

코드 생성기는 로컬 폴더에 있는 DCM 및 인터페이스 파일을 사용 하려고 합니다. 인터페이스 파일이 로컬 폴더에 없는 경우 코드 생성기는 공용 모델 리포지토리 또는 회사 모델 리포지토리에서 해당 파일을 찾습니다. 공용 [인터페이스 파일](./concepts-common-interfaces.md) 은 공용 모델 리포지토리에 저장 됩니다.

코드 생성이 완료 되 면 확장은 코드를 사용 하 여 새 VS Code 창을 엽니다. **기본 .c**와 같은 생성 된 파일을 열면 IntelliSense에서 c SDK 원본 파일을 열 수 없다고 보고 하는 것을 알 수 있습니다. 올바른 IntelliSense 및 코드 탐색을 사용 하도록 설정 하려면 다음 단계를 사용 하 여 C SDK 원본을 포함 합니다.

1. VS Code에서 **Ctrl + Shift + P** 를 사용 하 여 명령 팔레트를 열고를 입력 하 고 **CC++/: Edit 구성 (json)** 을 선택 하 여 **c_cpp_properties json** 파일을 엽니다.

1. `includePath` 섹션에서 장치 SDK의 경로를 추가 합니다.

    ```json
    "includePath": [
        "${workspaceFolder}/**",
        "{path_of_device_c_sdk}/**"
    ]
    ```

1. 파일을 저장합니다.

## <a name="use-the-generated-code"></a>생성 된 코드 사용

다음 지침에서는 생성 된 코드를 다양 한 개발 컴퓨터 플랫폼의 고유한 장치 프로젝트에 사용 하는 방법을 설명 합니다. 이 지침에서는 생성 된 코드와 함께 IoT Hub 장치 연결 문자열을 사용 하 고 있다고 가정 합니다.

### <a name="linux"></a>Linux

Ubuntu 또는 Debian와 같은 Linux 환경에서 CMake를 사용 하 여 장치 C SDK Vcpkg와 함께 장치 코드를 빌드하려면 다음을 수행 합니다.

1. 터미널 응용 프로그램을 엽니다.

1. `apt-get` 명령을 사용 하 여 **GCC**, **Git**, `cmake`및 모든 종속성을 설치 합니다.

    ```bash
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    `cmake` 버전이 **2.8.12**보다 크고, **GCC** 버전이 **4.4.7**보다 큰지 확인합니다.

    ```bash
    cmake --version
    gcc --version
    ```

1. Vcpkg 설치:

    ```bash
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    ./bootstrap-vcpkg.sh
    ```

    그런 다음 사용자 수준 [통합](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)을 연결 하려면 다음을 실행 합니다.

    ```bash
    ./vcpkg integrate install
    ```

1. 다음과 같이 Azure IoT C 디바이스 SDK Vcpkg를 설치합니다.

    ```bash
    ./vcpkg install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

1. 생성 된 코드 스텁을 포함 하는 폴더에 `cmake` 하위 디렉터리를 만들고 해당 폴더로 이동 합니다.

    ```bash
    mkdir cmake
    cd cmake
    ```

1. CMake를 사용 하 여 장치 SDK 및 생성 된 코드 스텁을 빌드하려면 다음 명령을 실행 합니다.

    ```bash
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}/scripts/buildsystems/vcpkg.cmake"

    cmake --build .
    ```

1. 빌드가 성공적으로 완료 되 면 IoT Hub 장치 연결 문자열을 매개 변수로 지정 하 여 응용 프로그램을 실행 합니다.

    ```bash
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

### <a name="windows"></a>Windows

명령줄에서 CMake 및 Visual Studio C/C++ 컴파일러를 사용 하 여 Windows의 장치 C SDK와 함께 장치 코드를 빌드하려면 [IoT 플러그 앤 플레이 빠른](./quickstart-create-pnp-device-windows.md)시작을 참조 하세요. 다음 단계에서는 Visual Studio에서 CMake 프로젝트로 장치 C SDK Vcpkg와 함께 장치 코드를 빌드하는 방법을 보여 줍니다.

1. [빠른](https://docs.microsoft.com/azure/iot-pnp/quickstart-create-pnp-device-windows#prepare-the-development-environment) 시작의 단계에 따라 Vcpkg를 통해 C 용 Azure IOT 장치 SDK를 설치 합니다.

1. [Visual Studio 2019 (Community, Professional 또는 Enterprise)](https://visualstudio.microsoft.com/downloads/) 설치- **NuGet 패키지 관리자** 구성 요소와 워크 로드를 **사용한 C++ 데스크톱 개발** 을 포함 해야 합니다.

1. Visual Studio를 열고 **파일 > 열기 > cmake ...** 를 선택 하 여 폴더에 생성 된 코드를 포함 하는 `CMakeLists.txt`를 엽니다.

1. **일반** 도구 모음에서 **구성** 드롭다운을 찾습니다. **구성 관리** 를 선택 하 여 프로젝트에 대 한 cmake 설정을 추가 합니다.

    ![구성 관리](media/howto-develop-with-vs-vscode/vs-manage-config.png)

1. **Cmake 설정**에서 새 구성을 추가 하 고 **x86-Debug** 를 대상으로 선택 합니다.

1. **Cmake 명령 인수**에서 다음 줄을 추가 합니다.

    ```txt
    -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    ```

1. 파일을 저장합니다.

1. **구성** 드롭다운에서 **x86-Debug** 로 전환 합니다. CMake에서 캐시를 생성 하는 데 몇 초 정도 걸립니다. 출력 창을 확인 하 여 진행률을 확인 합니다.

    ![CMake 출력](media/howto-develop-with-vs-vscode/vs-cmake-output.png)

1. **솔루션 탐색기**에서 루트 폴더의 `CMakeLists.txt`를 마우스 오른쪽 단추로 클릭 하 고 상황에 맞는 메뉴에서 **빌드** 를 선택 하 여 장치 SDK를 사용 하 여 생성 된 코드 스텁을 작성 합니다.

1. 빌드가 성공적으로 완료 되 면 명령 프롬프트에서 IoT Hub 장치 연결 문자열을 매개 변수로 지정 하 여 응용 프로그램을 실행 합니다.

    ```cmd
    .\out\build\x86-Debug\{generated_code_project_name}.exe "[IoT Hub device connection string]"
    ```

> [!TIP]
> Visual Studio에서 Cmake를 사용 하는 방법에 대해 자세히 알아보려면 [Cmake 프로젝트 빌드](https://docs.microsoft.com/cpp/build/cmake-projects-in-visual-studio?view=vs-2019#building-cmake-projects) 를 참조 하세요.

### <a name="macos"></a>macOS

다음 단계는 CMake를 사용 하 여 macOS에서 장치 C SDK 소스 코드와 함께 장치 코드를 빌드하는 방법을 보여 줍니다.

1. 터미널 응용 프로그램을 엽니다.

1. [Homebrew](https://homebrew.sh) 를 사용 하 여 모든 종속성을 설치 합니다.

    ```bash
    brew update
    brew install git cmake pkgconfig openssl ossp-uuid
    ```

1. [Csversion](https://cmake.org/) **2.8.12**이상 인지 확인 합니다.

    ```bash
    cmake --version
    ```

1. 최신 버전의 [패치](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#upgrade-curl-on-mac-os) 를 제공 합니다.

1. 생성 된 코드가 포함 된 폴더에서 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 리포지토리를 복제 합니다.

    ```bash
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    이 작업을 완료하는 데 몇 분 정도가 걸립니다.

1. 생성 된 코드가 포함 된 폴더 아래에 `cmake` 이라는 폴더를 만들고 해당 폴더로 이동 합니다.

    ```bash
    mkdir cmake
    cd cmake
    ```

1. CMake를 사용 하 여 장치 SDK 및 생성 된 코드 스텁을 빌드하려면 다음 명령을 실행 합니다.

    ```bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DOPENSSL_ROOT_DIR:PATH=/usr/local/opt/openssl ..
    cmake --build .
    ```

1. 빌드가 성공적으로 완료 되 면 IoT Hub 장치 연결 문자열을 매개 변수로 지정 하 여 응용 프로그램을 실행 합니다.

    ```bash
    cd {generated_code_folder_name}/cmake/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

## <a name="iterate-by-regenerating-the-skeleton-code"></a>기본 코드를 다시 생성 하 여 반복

DCM 또는 인터페이스 파일을 업데이트 하는 경우 코드 생성기에서 코드를 다시 생성할 수 있습니다. DCM 파일에서 장치 코드를 이미 생성 했다고 가정 하 고 코드를 다시 생성 합니다.

1. DCM 파일이 들어 있는 폴더를 열어 놓은 상태에서, **Ctrl+Shift+P**를 사용하여 명령 팔레트를 열고, **IoT 플러그 앤 플레이**를 입력하고, **디바이스 코드 스텁 생성**을 선택합니다.

1. 업데이트 한 DCM 파일을 선택 합니다.

1. **{Project name}에 대 한 코드 다시 생성을**선택 합니다.

1. 코드 생성기는 구성 된 이전 설정을 사용 하 고 코드를 다시 생성 합니다. 그러나 `main.c` 및 `{project_name}_impl.c`와 같은 사용자 코드를 포함할 수 있는 파일은 덮어쓰지 않습니다.

> [!NOTE]
> 인터페이스 파일에서 URN id를 업데이트 하면 새 인터페이스로 처리 됩니다. 코드를 다시 생성 하면 코드 생성기에서 인터페이스에 대 한 코드를 생성 하지만 `{project_name}_impl.c` 파일의 원래 코드는 덮어쓰지 않습니다.

## <a name="problems-and-feedback"></a>문제 및 피드백

Azure IoT Tools는 GitHub에서 오픈 소스 프로젝트입니다. 모든 문제 및 기능 요청에 대해 [GitHub에서 문제를 만들](https://github.com/microsoft/vscode-azure-iot-tools/issues/new)수 있습니다.

## <a name="next-steps"></a>다음 단계

이 방법 문서에서는 Visual Studio 및 Visual Studio Code를 사용 하 여 장치 응용 프로그램을 구현 하는 기본 C 코드를 생성 하는 방법에 대해 알아보았습니다. 제안 된 다음 단계는 [Azure IoT 탐색기 도구를 설치 하 고 사용](./howto-install-iot-explorer.md) 하는 방법을 배우는 것입니다.
