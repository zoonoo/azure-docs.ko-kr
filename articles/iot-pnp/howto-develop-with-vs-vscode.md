---
title: 비주얼 스튜디오 및 비주얼 스튜디오 코드를 사용하여 IoT 플러그 앤 플레이 미리 보기 장치 구축 | 마이크로 소프트 문서
description: Visual Studio 및 Visual Studio 코드를 사용하여 IoT 플러그 앤 플레이 장치 모델 작성 및 장치 코드 구현을 가속화합니다.
author: liydu
ms.author: liydu
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 91e7b1c0be9a38c3d79440f07d944d182980dc10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159237"
---
# <a name="use-visual-studio-and-visual-studio-code-to-build-iot-plug-and-play-devices"></a>비주얼 스튜디오 및 비주얼 스튜디오 코드를 사용하여 IoT 플러그 앤 플레이 디바이스 구축

Visual Studio 코드용 Azure IoT 도구는 DCM(장치 기능 모델) 및 인터페이스를 작성하고, 리포지토리모델에 게시하고, 장치 응용 프로그램을 구현하기 위한 스켈레톤 C 코드를 생성하는 통합 환경을 제공합니다.

이 아티클에서는 다음을 수행하는 방법을 보여줍니다.

- 장치 코드 및 응용 프로그램 프로젝트를 생성합니다.
- 장치 프로젝트에서 생성된 코드를 사용합니다.
- 스켈레톤 코드를 다시 생성하여 반복합니다.

VS 코드를 사용하여 IoT 장치를 개발하는 방법에 [https://github.com/microsoft/vscode-iot-workbench](https://github.com/microsoft/vscode-iot-workbench)대해 자세히 알아보려면 을 참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

[비주얼 스튜디오 코드를 설치합니다.](https://code.visualstudio.com/)

다음 단계를 사용하여 VS 코드에 확장 팩을 설치합니다.

1. VS 코드에서 **확장 탭을 선택합니다.**
1. 마켓플레이스에서 **Azure IoT 도구를** 검색하고 설치합니다.

## <a name="generate-device-code-and-project"></a>장치 코드 및 프로젝트 생성

VS 코드에서 **Ctrl+Shift+P를** 사용하여 명령 팔레트를 열고, **IoT 플러그 앤 플레이를 입력하고,** **장치 코드 스텁 생성을** 선택하여 스켈레톤 코드 및 프로젝트 유형을 구성합니다. 다음 목록에서는 각 단계에 대해 자세히 설명합니다.

- **코드를 생성하는 데 사용할 DCM 파일입니다.** 스켈레톤 장치 코드를 생성하려면 구현한 DCM 및 인터페이스 파일이 포함된 폴더를 엽니다. 코드 생성기가 DCM에 필요한 인터페이스를 찾을 수 없는 경우 필요에 따라 모델 리포지토리에서 다운로드합니다.

- **장치 코드 언어**. 현재 코드 생성기는 ANSI C만 지원합니다.

- **프로젝트 이름**. 프로젝트 이름은 생성된 코드 및 기타 프로젝트 파일의 폴더 이름으로 사용됩니다. 폴더는 기본적으로 DCM 파일 옆에 배치됩니다. DCM을 업데이트하고 장치 코드를 다시 생성할 때마다 생성된 코드 폴더를 수동으로 복사하지 않으려면 DCM 파일을 프로젝트 폴더와 동일한 폴더에 보관하십시오.

- **Azure IoT에 연결하는 방법**. 생성된 파일에는 Azure IoT Hub에 연결할 장치를 구성하는 코드도 포함되어 있습니다. [Azure IoT Hub에](https://docs.microsoft.com/azure/iot-hub) 직접 연결하거나 [장치 프로비저닝 서비스를](https://docs.microsoft.com/azure/iot-dps)사용하도록 선택할 수 있습니다.

    - **IoT Hub 장치 연결 문자열을 통해**: 장치 응용 프로그램이 IoT Hub에 직접 연결할 장치 연결 문자열을 지정합니다.
    - **DPS 대칭 키를 통해**: DPS를 사용하여 IoT Hub 또는 IoT Central에 연결하는 데 필요한 장치 응용 프로그램에 대한 **ID 범위,** **대칭 키** 및 장치 **ID를** 지정합니다.

- **프로젝트 유형**. 코드 생성기는 CMake 또는 Arduino 프로젝트도 생성합니다. 현재 지원되는 프로젝트 유형은 다음과 같습니다.

    - **Windows에서 CMake 프로젝트**: [Windows에서 CMake를](https://cmake.org/) 빌드 시스템으로 사용하는 장치 프로젝트용입니다. 이 옵션은 `CMakeLists.txt` C 코드와 동일한 폴더에 장치 SDK 구성을 생성합니다.
    - **리눅스에서 CMake 프로젝트**: 리눅스에서 빌드 시스템으로 [CMake를](https://cmake.org/) 사용하는 장치 프로젝트. 이 옵션은 `CMakeLists.txt` C 코드와 동일한 폴더에 장치 SDK 구성을 생성합니다.
    - **MXChip IoT DevKit 프로젝트**: [MXChip IoT DevKit](https://aka.ms/iot-devkit) 장치에서 실행되는 장치 프로젝트용. 이 옵션은 VS 코드 또는 Arduino [IDE에서](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) IoT DevKit 장치에서 빌드및 실행할 수 있는 Arduino 프로젝트를 생성합니다.

- **장치 SDK 유형**. CMake를 프로젝트 유형으로 선택하는 경우 생성된 코드가 `CMakeLists.txt`다음과 같은 Azure IoT C 장치 SDK를 포함하는 방법을 구성하는 단계입니다.

    - **소스 코드를 통해**: 생성 된 코드는 [장치 SDK 소스 코드에](https://github.com/Azure/azure-iot-sdk-c) 포함하고 함께 구축에 의존합니다. 장치 SDK 소스 코드를 사용자 지정한 경우에 권장됩니다.
    - **Vcpkg을 통해**: 생성 된 코드는 장치 [SDK Vcpkg에](https://github.com/microsoft/vcpkg/tree/master/ports/azure-iot-sdk-c) 포함하고 함께 구축에 의존한다. 이것은 윈도우, 리눅스 또는 macOS를 실행하는 장치에 대한 권장 방법입니다.

    > [!NOTE]
    > Azure IoT C 장치 SDK Vcpkg에 대한 macOS 지원이 진행 중입니다.

코드 생성기는 로컬 폴더에 있는 DCM 및 인터페이스 파일을 사용하려고 합니다. 인터페이스 파일이 로컬 폴더에 없는 경우 코드 생성기는 공용 모델 리포지토리 또는 회사 모델 리포지토리에서 인터페이스 파일을 찾습니다. [공통 인터페이스 파일은](./concepts-common-interfaces.md) 공용 모델 리포지토리에 저장됩니다.

코드 생성이 완료되면 확장은 코드가 있는 새 VS 코드 창을 엽니다. **main.c와**같은 생성된 파일을 열면 IntelliSense가 C SDK 소스 파일을 열 수 없다고 보고할 수 있습니다. 올바른 IntelliSense 및 코드 탐색을 사용하려면 다음 단계를 사용하여 C SDK 소스를 포함합니다.

1. VS 코드에서 **Ctrl+Shift+P를** 사용하여 명령 팔레트를 열고 **C/C++: 구성 편집(JSON)을** 선택하여 **c_cpp_properties.json** 파일을 엽니다.

1. `includePath` 섹션에서 장치 SDK의 경로를 추가합니다.

    ```json
    "includePath": [
        "${workspaceFolder}/**",
        "{path_of_device_c_sdk}/**"
    ]
    ```

1. 파일을 저장합니다.

## <a name="use-the-generated-code"></a>생성된 코드 사용

다음 지침은 다른 개발 기계 플랫폼에서 사용자 고유의 장치 프로젝트에서 생성된 코드를 사용하는 방법을 설명합니다. 이 지침은 생성된 코드가 포함된 IoT Hub 장치 연결 문자열을 사용하고 있다고 가정합니다.

### <a name="linux"></a>Linux

우분투 또는 데비안과 같은 리눅스 환경에서 CMake를 사용하여 장치 C SDK Vcpkg과 함께 장치 코드를 빌드하려면 다음을 수행합니다.

1. 터미널 응용 프로그램을 엽니다.

1. 명령을 사용하여 **GCC,** **Git**및 `cmake`모든 종속성을 설치합니다. `apt-get`

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

    그런 다음 사용자 전체 [통합을](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)연결하여 다음을 실행합니다.

    ```bash
    ./vcpkg integrate install
    ```

1. 다음과 같이 Azure IoT C 디바이스 SDK Vcpkg를 설치합니다.

    ```bash
    ./vcpkg install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

1. 폴더에 `cmake` 하위 디렉터리 만들기에는 생성된 코드 스텁이 포함되어 있으며 해당 폴더로 이동합니다.

    ```bash
    mkdir cmake
    cd cmake
    ```

1. 다음 명령을 실행하여 CMake를 사용하여 장치 SDK 및 생성된 코드 스텁을 빌드합니다.

    ```bash
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}/scripts/buildsystems/vcpkg.cmake"

    cmake --build .
    ```

1. 빌드가 성공하면 IoT Hub 장치 연결 문자열을 매개 변수로 지정하는 응용 프로그램을 실행합니다.

    ```bash
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

### <a name="windows"></a>Windows

명령줄에서 CMake 및 Visual Studio C/C++ 컴파일러를 사용하여 Windows의 장치 C SDK와 함께 장치 코드를 빌드하려면 [IoT 플러그 앤 플레이 퀵스타트를](./quickstart-create-pnp-device-windows.md)참조하십시오. 다음 단계는 Visual Studio의 CMake 프로젝트로 장치 C SDK Vcpkg과 함께 장치 코드를 빌드하는 방법을 보여 주며, 이 단계를 보여 준다.

1. [빠른 시작의](https://docs.microsoft.com/azure/iot-pnp/quickstart-create-pnp-device-windows#prepare-the-development-environment) 단계를 수행하여 Vcpkg을 통해 C용 Azure IoT 장치 SDK를 설치합니다.

1. [Visual Studio 2019(커뮤니티, 전문가 또는 엔터프라이즈)](https://visualstudio.microsoft.com/downloads/) 설치 - **NuGet 패키지 관리자** 구성 요소와 **C++** 워크로드가 있는 데스크톱 개발을 포함해야 합니다.

1. 시각적 스튜디오를 열고 파일 > 열기 > `CMakeLists.txt` **CMake를** 선택하여 생성된 코드가 포함된 폴더에서 엽니다.

1. **일반** 도구 모음에서 구성 드롭다운을 **찾습니다.** **구성 관리를** 선택하여 프로젝트에 대한 CMake 설정을 추가합니다.

    ![구성 관리](media/howto-develop-with-vs-vscode/vs-manage-config.png)

1. **CMake 설정에서**새 구성을 추가하고 **x86-디버그를** 대상으로 선택합니다.

1. **CMake 명령 인수에서**다음 줄을 추가합니다.

    ```txt
    -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    ```

1. 파일을 저장합니다.

1. 구성 드롭다운에서 **x86-디버그로** **전환합니다.** CMake에서 캐시를 생성하는 데 몇 초가 필요합니다. 출력 창을 보고 진행 률을 확인 합니다.

    ![CMake 출력](media/howto-develop-with-vs-vscode/vs-cmake-output.png)

1. 솔루션 **탐색기에서**루트 폴더의 `CMakeLists.txt` 마우스 오른쪽 단추를 클릭하고 컨텍스트 메뉴에서 **빌드를** 선택하여 장치 SDK를 사용하여 생성된 코드 스텁을 빌드합니다.

1. 빌드가 성공하면 명령 프롬프트에서 IoT Hub 장치 연결 문자열을 매개 변수로 지정하는 응용 프로그램을 실행합니다.

    ```cmd
    .\out\build\x86-Debug\{generated_code_project_name}.exe "[IoT Hub device connection string]"
    ```

> [!TIP]
> 비주얼 스튜디오에서 CMake 사용에 대해 자세히 알아보려면 [CMake 프로젝트 빌드를](https://docs.microsoft.com/cpp/build/cmake-projects-in-visual-studio?view=vs-2019#building-cmake-projects) 참조하십시오.

### <a name="macos"></a>macOS

다음 단계는 CMake를 사용하여 macOS의 장치 C SDK 소스 코드와 함께 장치 코드를 빌드하는 방법을 보여 주며 다음과 같은 단계를 보여 주며 있습니다.

1. 터미널 응용 프로그램을 엽니다.

1. [홈브루를](https://homebrew.sh) 사용하여 모든 종속성을 설치합니다.

    ```bash
    brew update
    brew install git cmake pkgconfig openssl ossp-uuid
    ```

1. [CMake가](https://cmake.org/) 적어도 버전 **2.8.12인지 확인합니다.**

    ```bash
    cmake --version
    ```

1. 최신 버전으로 [CURL](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#upgrade-curl-on-mac-os) 패치를 사용할 수 있습니다.

1. 생성된 코드가 포함된 폴더에서 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 리포지토리를 복제합니다.

    ```bash
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    이 작업을 완료하는 데 몇 분 정도가 걸립니다.

1. 생성된 코드가 `cmake` 포함된 폴더 아래에 라는 폴더를 만들고 해당 폴더로 이동합니다.

    ```bash
    mkdir cmake
    cd cmake
    ```

1. 다음 명령을 실행하여 CMake를 사용하여 장치 SDK 및 생성된 코드 스텁을 빌드합니다.

    ```bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DOPENSSL_ROOT_DIR:PATH=/usr/local/opt/openssl ..
    cmake --build .
    ```

1. 빌드가 성공하면 IoT Hub 장치 연결 문자열을 매개 변수로 지정하는 응용 프로그램을 실행합니다.

    ```bash
    cd {generated_code_folder_name}/cmake/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

## <a name="iterate-by-regenerating-the-skeleton-code"></a>스켈레톤 코드를 다시 생성하여 반복

DCM 또는 인터페이스 파일을 업데이트하는 경우 코드 생성기가 코드를 다시 생성할 수 있습니다. DCM 파일에서 장치 코드를 이미 생성했다고 가정하면 코드를 다시 생성합니다.

1. DCM 파일이 들어 있는 폴더를 열어 놓은 상태에서, **Ctrl+Shift+P**를 사용하여 명령 팔레트를 열고, **IoT 플러그 앤 플레이**를 입력하고, **디바이스 코드 스텁 생성**을 선택합니다.

1. 업데이트한 DCM 파일을 선택합니다.

1. **{프로젝트 이름}에 대한 코드 다시 생성을**선택합니다.

1. 코드 생성기는 구성한 이전 설정을 사용하고 코드를 다시 생성합니다. 그러나 와 같은 `main.c` 사용자 코드를 포함할 수 있는 파일을 `{project_name}_impl.c`덮어쓰지 는 않습니다.

> [!NOTE]
> 인터페이스 파일에서 URN ID를 업데이트하면 새 인터페이스로 처리됩니다. 코드를 다시 생성하면 코드 생성기는 인터페이스에 대한 코드를 생성하지만 `{project_name}_impl.c` 파일의 원래 코드를 덮어쓰지 않습니다.

## <a name="problems-and-feedback"></a>문제 및 피드백

Azure IoT 도구는 GitHub의 오픈 소스 프로젝트입니다. 모든 문제 및 기능 요청에 대 [한 GitHub에 문제를 만들](https://github.com/microsoft/vscode-azure-iot-tools/issues/new)수 있습니다.

## <a name="next-steps"></a>다음 단계

이 방법 문서에서는 Visual Studio 및 Visual Studio 코드를 사용하여 스켈레톤 C 코드를 생성하여 장치 응용 프로그램을 구현하는 방법을 배웠습니다. 다음 단계는 [Azure IoT 탐색기](./howto-install-iot-explorer.md) 도구를 설치하고 사용하는 방법을 알아보는 것입니다.
