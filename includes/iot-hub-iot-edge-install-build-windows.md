## <a name="install-the-prerequisites"></a>필수 구성 요소 설치

1. [Visual Studio 2015 또는 2017](https://www.visualstudio.com)을 설치합니다. 라이선스 요구 사항을 충족하는 경우 무료 Community Edition을 사용할 수 있습니다. Visual C++ 및 NuGet Package Manager를 포함해야 합니다.

1. [git](http://www.git-scm.com)을 설치하고 명령줄에서 git.exe를 실행할 수 있는지 확인합니다.

1. [CMake](https://cmake.org/download/)를 설치하고 명령줄에서 cmake.exe을 실행할 수 있는지 확인합니다. CMake 버전 3.7.2 이상을 사용하는 것이 좋습니다. Windows에서 **.msi** 설치 관리자를 사용하는 것이 가장 쉬운 방법입니다. 설치 관리자가 메시지를 표시하면 적어도 현재 사용자에 대한 경로에 CMake를 추가합니다.

1. [Python 2.7](https://www.python.org/downloads/release/python-27)을 설치합니다. **제어판 -> 시스템 -> 고급 시스템 설정 -> 환경 변수**에서 `PATH` 환경 변수에 Python을 추가해야 합니다.

1. 명령 프롬프트에서 다음 명령을 실행하여 로컬 컴퓨터에 Azure IoT Edge GitHub 리포지토리를 복제합니다.

    ```cmd
    git clone https://github.com/Azure/iot-edge.git
    ```

## <a name="how-to-build-the-sample"></a>샘플을 빌드하는 방법

이제 로컬 컴퓨터에 IoT Edge 런타임과 샘플을 빌드할 수 있습니다.

1. **VS 2015용 개발자 명령 프롬프트** 또는 **VS 2017용 개발자 명령 프롬프트** 명령 프롬프트를 엽니다.

1. **iot-edge** 리포지토리의 로컬 복사본에 있는 루트 폴더로 이동합니다.

1. 빌드 스크립트를 다음과 같이 실행합니다.

    ```cmd
    tools\build.cmd --disable-native-remote-modules
    ```

이 스크립트는 Visual Studio 솔루션 파일을 만들고 솔루션을 빌드합니다. Visual Studio 솔루션은 **iot-edge** 리포지토리의 로컬 복사본에 있는 **build** 폴더에서 찾을 수 있습니다. 단위 테스트를 빌드하고 실행하려는 경우 `--run-unittests` 매개 변수를 추가합니다. 종단간 테스트를 빌드하고 실행하려는 경우 `--run-e2e-tests`를 추가합니다.

> [!NOTE]
> **build.cmd** 스크립트를 실행할 때마다 **iot-edge** 리포지토리의 로컬 복사본 루트 폴더에서 **build** 폴더를 삭제한 다음 다시 만듭니다.